+++
title = "Using Either to Handle Errors"
date = 2018-06-01T20:16:09-05:00
tags = [""]
categories = ["code", "spring", "rest-template"]
draft = false
+++

Dealing with errors isn’t always easy or fun. Sometimes we don’t expect it to
happen depending on the context we found ourselves.

Let’s say we read the following code:

```java
public List<Product> allProducts() {
  var response = restTemplate.getForEntity("//products", Product[].class);
  return response.getStatusCode().is2xxSuccessful()
    ? asList(response.getBody())
    : List.of();
}
```

It looks like it reaches out to a third-party service for a list of products and
if successful (i.e. `2xx`) it returns the products otherwise an empty list.

It turns out that is misleading. Even though `RestTemplate` allows us to check
if the response was successful, after a few days we got a `500` from the
upstream application causing an exception on our code, which puzzled us.

Looking into the incident, we found out that `RestTemplate` doesn’t treat `4xx` and
`5xx` responses as we expected. It actually throws exceptions for such cases.

After some team discussion, we got to the conclusion that we should be adequately
handling the `4xx` and `5xx` cases, and surfacing the proper responses to our
consumers depending on the error.

To make that an easy thing, we decided to introduce `Either` to our codebase via
the following DSL:

```java
public static <T> Either<AnError, T> clientRequest(Supplier<T> request) {
  try {
    return Either.right(request.get());
  } catch (HttpClientErrorException | HttpServerErrorException e) {
    return Either.left(new AnError(e.getStatusCode(), e.getMessage()));
  }
}
```

Given that, we started by updating our tests:

```java
@Test
public void returnsListOfProductsWhenSuccessful() {
  given(restTemplate.getForEntity("//products", Product[].class))
    .willReturn(new ResponseEntity<>(new Product[]{}, HttpStatus.OK));

  Either<AnError, List<Product>> possiblyAllProducts = this.products.allProducts();

  assertThat(possiblyAllProducts.isRight(), is(true));
  assertThat(possiblyAllProducts.get(), is(equalTo(List.of())));
}

@Test
public void returnsAnErrorWhen4xx() {
  given(restTemplate.getForEntity("//products", Product[].class))
    .willThrow(new HttpClientErrorException(HttpStatus.BAD_REQUEST, "ops"));

  Either<AnError, List<Product>> possiblyAllProducts = this.products.allProducts();

  assertThat(possiblyAllProducts.isLeft(), is(true));
  assertThat(
      possiblyAllProducts.getLeft(),
      is(equalTo(new AnError(HttpStatus.BAD_REQUEST, "400 ops"))));
}
```

Which lead to having the implementation wrapping the remote call with the
function created above:

```java
public Either<AnError, List<Product>> allProducts() {
  return clientRequest(() -> List.of(
    restTemplate
      .getForEntity("//products", Product[].class)
      .getBody()));
}
```

Now the client code is focused on the desired result, and we have the
flexibility to handle `4xx` and `5xx` easily.

Let's say a resource is consuming `ProductsClient`, and we get a `5xx` from the
upstream. In this case, we want to return a proper message to our consumers,
let's describe the behaviour we desire with a test:

```java
@Test
public void returnsBadGatewayWithDescriptionWhenUnsuccessful() throws Exception {
  given(products.allProducts()).willReturn(Either.left(
    new AnError(HttpStatus.INTERNAL_SERVER_ERROR, "I failed")));

  mockMvc.perform(get("/products"))
    .andExpect(status().isBadGateway())
    .andExpect(jsonPath(
      "$.errors[0].description",
      equalTo("Unable to fetch Products from upstream")));
}
```

In the test above we are requesting to our resource `/products` and
expecting it to return `Bad Gateway` with a custom message when we get a `5xx`
from the client.

To emulate the behaviour of the client class when something goes wrong, all we
need to do is return an `Either` with the value in the _left_.

To satisfy that, we need the following piece of code in our resource:

```java
@GetMapping
public ResponseEntity<?> allProducts() {
  return products
    .allProducts()
    .fold(this::toProperError, ResponseEntity::ok);
}

private ResponseEntity<ErrorsRepresentation> toProperError(AnError anError) {
  return ResponseEntity
    .status(HttpStatus.BAD_GATEWAY)
    .body(errorsWith("Unable to fetch Products from upstream"));
}
```

Ok, what happened on that method? We called `#allProducts()` in our client class,
and we are reducing the two possible values to one. That means the `#fold`
allows us to provide two mappers, one for the _left_ side (`#toProperError`) and
another to the _right_ side (`#ok`). It will check which of the values is
present, apply the mapper and then return it.

So, if the call was successful, it will apply `#ok` to the **list of products**,
exposing such list to our consumers and, if the request is unsuccessful, it will
**create a proper error** and return a `Bad Gateway` with a description of what happened.

Also, you will notice that for any error (or _left_) we get the same error
representation will be created, but that is the method we can check the
different errors we care about and enhance with the proper message and
HTTP codes.

To be able to leverage the power of either, I would recommend getting more
familiar with the concept if you aren't already, here are some references:

* [Starting with Haskell: Error Handling](https://www.schoolofhaskell.com/school/starting-with-haskell/basics-of-haskell/10_Error_Handling)
* [Exploring Either in Kotlin](https://kotlin.link/articles/Exploring-an-Either-Monad-in-Kotlin.html)

In this post, we are making use of a library called [vavr](http://www.vavr.io)
which offers an implementation of [Either](http://www.vavr.io/vavr-docs/#_either)
for Java.

The source code of the post is [here](https://github.com/rafasf/using-either-resttemplate).

