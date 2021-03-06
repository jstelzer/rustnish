# Rustnish

[![Build Status](https://travis-ci.org/klausi/rustnish.svg?branch=goal-05)](https://travis-ci.org/klausi/rustnish)

Experimental project to learn Rust. A reverse proxy.

https://klausi.github.io/rustnish/

## Goal 1: Just pipe HTTP requests through

Completed: yes

A webserver like Apache is listening on port 80. Write a reverse proxy service
that does nothing but forwarding HTTP requests to port 80. The service must
listen on port 9090. The service must not modify the HTTP response in any way.

## Goal 2: One integration test

Completed: yes

Write an integration test that confirms that the reverse proxy is working as
expected. The test should issue a real HTTP request and check that passing
through upstream responses works. Refactor the code to accept arbitrary port
numbers so that the tests can simulate a real backend without requiring root
access to bind on port 80.

## Goal 3: Convert Hyper server to Tokio

Completed: yes

A new version of the [Hyper library](https://hyper.rs/) has been released which
is based on the [Tokio library](https://tokio.rs/). Convert the existing code to
use that new version and provide one integration test case.

## Goal 4: Full integration tests

Completed: yes

Expand the integration tests to confirm that the reverse proxy is working as
expected. Add tests with broken HTTP requests to cover error handling of the
reverse proxy. All ```unwrap()``` calls in none test code should be removed and
covered by proper error handling.

## Goal 5: Continuous integration testing with Travis CI

Completed: yes

Enable [Travis CI](http://travis-ci.org/) so that the automated tests are run
after every Git push to the Rustnish repository. Enable
[Clippy](https://github.com/rust-lang-nursery/rust-clippy) that also checks for
Rust best practices.

## Goal 6: Add HTTP headers to requests/responses

Completed: yes

Add the following HTTP headers to requests/responses passed through Rustnish:

Headers for requests forwarded upstream:

* `X-Forwarded-For`: the originating IP address of the client connecting to the
  proxy. Append IP address if already set.
* `X-Forwarded-Port`: the originating port of the HTTP request (example: 443).
  Append port if already set.

Headers for responses returned downstream:

* `Via`: a code name for the Rustnish proxy, with the value rustnish-0.0.1.
  Append if already set.
* `Server`: will be added to the response ("rustnish") if the upstream server
  didn't add it first.

## Goal 7: Add integration test that watches for memory leaks

Completed: no, because impossible

Add an integration test that ensures that the proxy server is not leaking memory
(growing RAM usage without shrinking again). Use /proc information to compare
memory usage of the current process before and after the test.

**Update**: The test case was not stable enough, it had random test fails and
had to be removed. Watching and asserting /proc memory information is not
reliable.

## Goal 8: Make Tokio/Hyper server resilient

Completed: partially

There is a [known flaw](https://github.com/hyperium/hyper/issues/1358) in the
Tokio/Hyper libraries that stops server execution under high load. Implement a
workaround to make the proxy more resilient. Ensure that the server does not
stop under such an attack and still delivers valid responses after the attack.

**Update**: The test case was not stable enough, it had random test fails and
had to be removed. Simulating an attack with millions of requests was not
reliable and made different parts of the test case fail.

## Goal 9: Benchmark against Varnish

Completed: yes

Write benchmark code that compares runtime performance of Rustnish against
[Varnish](https://varnish-cache.org/). Use `cargo bench` to execute the benchmarks.

## Goal 10: Cache 20 upstream responses

Completed: yes

Create an in-memory store that caches GET HTTP responses for anonymous users.
* A response is considered cachable if the Cache-Control HTTP header contains
"public" and a "max-age" value.
* A request is considered anonymous if the Cookie HTTP header does not contain a key that starts with "SESS".
* The cache key is the full URL path (including query parameters).
* Cache entries should be kept for 1 minute.
* A maximum of 20 cache entries should be used.


## Goal 11: Build a memory constrained cache

Completed: no

Improve the in-memory store of cached responses. Make the cache be limited by
memory size and not by number of cache entries. Use a default of 256MB allowed
cache memory size.


## Possible future goals

* Add HTTP Age header
* Read proxy config from config file
* Read proxy config from Varnish syntax config file
* Support Vary HTTP header in responses
