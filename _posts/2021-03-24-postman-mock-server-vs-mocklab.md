---
author: Tom Akehurst
author_pic: tom_akehurst.png
layout: single
title: Postman Mock Server vs. MockLab
description: A comparison of Postman mock servers and MockLab for building mock APIs.
excerpt: Postman offers the ability to create mock mock servers, while mocking APIs is MockLab's main focus. This article compares their approaches and highlights their respective strengths.
categories: ['Postman', 'MockLab', 'API Mocking']
comments: true
---

Postman is indisputably the dominant HTTP testing tool on the market today, and
if you're involved in developing or testing APIs it's highly likely you've used it
(or know someone who does at least).

A feature of Postman is "mock servers", which allows mock APIs to be generated from
from Collections (Postman's main data model - sets of request and response examples). This
article compares Postman's capabilities and approach to API mocking with MockLab's,
examining the relative strengths and best use of each.

## Terminology

The concepts underlying Postman's and MockLab's mocking capabilities are very similar,
but some naming and terminology differs. The following table describes both
products' terms for each major concept:

| MockLab             | Postman           | Description                                                                        |
|---------------------|-------------------|------------------------------------------------------------------------------------|
| Mock API            | Mock Server       | A container for stubs/examples. Accessed via a unique domain name.                 |
| Stub                | Example           | Specification of a request to be matched, paired with the response to be returned. |
| Response Templating | Dynamic Responses | Responses that contain random values, variables or request data.                   |



## Getting up and running

With both products you need to sign-up/log into a user account in order to create a mock API/server.

In Postman you create a mock server either via the context menu for a collection, or from the Mock Servers tab:

<img src="/images/postman-vs-mocklab/postman-new-mock-server.png" style="border: none" alt="Postman new mock server" />

In MockLab you can create a new mock API via the "+ Mock API" button on the top bar:

<img src="/images/postman-vs-mocklab/mocklab-new-mock-api.png" style="border: none" alt="MockLab new mock API" />


### Base URLs and domain names

To use a mock server/API in either product, a unique base URL is needed.

Postman will generate a unique domain name for a mock server using a UUID for the
hostname e.g.

`https://aab7bb9b-4a59-4169-bd3a-697274f033f7.mock.pstmn.io`

MockLab will also generate a unique domain name, using a short hash for the hostname e.g.

`https://nd7dd.mocklab.io`

MockLab also permits you to optionally choose a hostname of your own, so you can
create a mock API with a base URL like

`https://acme-payments-api.mocklab.io`


## Creating stubs and examples

In Postman, examples in a collection are used directly to configure responses in
the mock server. Adding a new example to a request in a collection will automatically
add it to the mock server:

<img src="/images/postman-vs-mocklab/postman-new-example.png" style="border: none" alt="Postman new example" />


In MockLab, stubs are added directly from within a mock API's Stubs page:

<img src="/images/postman-vs-mocklab/mocklab-new-stub.png" style="border: none" alt="MockLab new stub" />


## Matching requests

An essential component of mocking is request matching. The mocking tool must
select the correct response for the incoming request based on a set of criteria.

This is often as simple as checking that the HTTP method and URL are exactly equal but
it's not uncommon to need to match headers, cookies or request body elements.
Sometimes it's also necessary to match more loosely e.g. when we want to return
a response regardless of the user ID passed in the URL path.

### URL and query

Postman supports a degree of request matching flexibility above simple method + URL
equality.

In your URL path you can specify a parameter name and this will be treated as a wildard, so any value will be accepted:

<img src="/images/postman-vs-mocklab/postman-path-parameter.png" alt="Postman path parameter" />

MockLab also supports loose matching of URL paths, but via the use of regular expressions
rather than named parameters:

<img src="/images/postman-vs-mocklab/mocklab-loose-url-matching.png" style="width: 80%" alt="MockLab loose URL matching" />

This gives some additional control over named parameters - you can use very loose
regex expressions that will match anything, or choose to constrain the range of valid
values to e.g. just numbers.


### Query parameters, headers and cookies

Postman doesn't use query parameters, headers or cookies in its matching process,
so a request sent with any combination of these values will always be matched provided
the method and URL are correct.

MockLab supports matching of all of these attributes. It also provides a variety
of predicates, so that it's possible to match loosely or precisely, depending on your needs.

For instance, you could match a request with a query parameter of `search`
exactly equalling `pyjamas`, an `Accept` header containing `json` and a cookie named
`session` that consists of lowercase alphanumerics only:

<img src="/images/postman-vs-mocklab/mocklab-request-attribute-matching.png" style="width: 80%" alt="MockLab query, header and cookie matching" />


### Request body

Matching the request body is also a requirement in many situations. For instance,
if you want to test a successful outcome when one variant of a request payload is
POSTed to the API and an error when a different variant is sent, you need to be able
to distinguish between request body contents.

By default, Postman will ignore the request body when matching a request. However,
you can make it include the request body, evaluating an exact match by including the
following header in the HTTP request:

```
x-mock-match-request-body: true
```

This is quite limiting for a couple of reasons:

1. It requires you to modify your application under test in order to make it send the header.
2. Exact matching on request bodies can fail when whitespace varies, or when volatile data such as transaction IDs are present.

MockLab can match the request body without the need to modify requests sent by
the app under test. It offers a set of predicates that support flexible
matching of input in JSON, XML or plain text.

For example, you could match a request with a JSON body of the correct general structure
and one field exactly specified using the `equalToJson` predicate:

<img src="/images/postman-vs-mocklab/mocklab-body-matching.png" style="width: 80%" alt="MockLab body matching" />

## Debugging

When testing a system with your mock APIs, sooner or later you're going to want to see
the details of requests that were made. Both products have a request log feature
for this purpose, with each presenting very similar information.

Postman:

<img src="/images/postman-vs-mocklab/postman-request-log.png" style="border: none" alt="Postman request log" />

MockLab:

<img src="/images/postman-vs-mocklab/mocklab-request-log.png" style="border: none" alt="MockLab request log" />

### Unmatched requests

A common issue when working with mock APIs is when a request made by your app under test isn't matched.

When Postman can't match a response to your request it issues a standard response:

```
{"error":{"name":"mockRequestNotFoundError","message":"To mock this request add an example response for the mock server to return.","header":"You need an example"}}
```

When MockLab can't match a request it will return a diff between the most similar stub
it could find and the request:

```
$ curl https://example.mocklab.io/v1/users -X PUT -d '{ "wrong": "stuff" }'

                                               Request was not matched
                                               =======================

-----------------------------------------------------------------------------------------------------------------------
| Closest stub                                             | Request                                                  |
-----------------------------------------------------------------------------------------------------------------------
                                                           |
JSON request body matching                                 |
                                                           |
POST                                                       | PUT                                                 <<<<< HTTP method does not match
[path] /v1/users                                           | /v1/users
                                                           |
{                                                          | {                                                   <<<<< Body does not match
  "id" : 1234,                                             |   "wrong" : "stuff"
  "name" : "${json-unit.any-string}",                      | }
  "isAdminUser" : "${json-unit.any-boolean}"               |
}                                                          |
                                                           |
-----------------------------------------------------------------------------------------------------------------------
```

## Importing from other formats

There are a number of widely used formats for describing HTTP interfaces and events.
If you're already working with one of these, being able to generate a mock API
automatically from it can be a big time-saver.

Postman can import from [OpenAPI](https://swagger.io/specification/){:target="{{site.data.misc.blank}}"},
[RAML](https://raml.org/){:target="{{site.data.misc.blank}}"},
[GraphQL](https://graphql.org/){:target="{{site.data.misc.blank}}"},
[curl](https://curl.se/){:target="{{site.data.misc.blank}}"} and
[WADL](https://en.wikipedia.org/wiki/Web_Application_Description_Language){:target="{{site.data.misc.blank}}"}:

<img src="/images/postman-vs-mocklab/postman-import.png" style="border: none" alt="Postman import" />


MockLab supports [OpenAPI](https://swagger.io/specification/){:target="{{site.data.misc.blank}}"},
[Swagger](https://swagger.io/specification/){:target="{{site.data.misc.blank}}"},
Postman collections and
[WireMock](http://wiremock.org){:target="{{site.data.misc.blank}}"} JSON:

<img src="/images/postman-vs-mocklab/mocklab-import.png" style="border: none" alt="MockLab import" />


### Customising the import

When importing from a specification format like OpenAPI (one that represents a contract
rather than just a list of request/response pairs) it's useful to be able to exert
some control over how your examples or stubs are generated.

Postman presents a number of options prior to executing the import that permit you
to choose things like whether response examples or schemas will be used to generate
the request and response parameters in your Postman examples:

<img src="/images/postman-vs-mocklab/postman-openapi-import-options.png" style="border: none" alt="Postman OpenAPI import options" />

MockLab takes a slightly different approach. Rather than offering a set of options
up-front, it understands a set of [extended attributes](https://swagger.io/docs/specification/openapi-extensions/){:target="{{site.data.misc.blank}}"}
that you can add to your OpenAPI spec. These allow you to set specific request
parameters in the generated stubs, rather than getting the randomly generated defaults.
In this way you can also control how randomly-generated values are produced when response bodies
are generated from schemas.

```yaml
schema:
  type: string
  x-faker: name.first_name
```

See the [Swagger documentation](https://www.mocklab.io/docs/swagger/#customising-the-import){:target="{{site.data.misc.blank}}"}
for details on using extended attributes.


## Dynamic responses

A basic stub or example will return a fixed response each time it is matched.
However, in some cases it's necessary to generate the response dynamically.

Reasons for this include:

* Copying data into the response from the request e.g a URL path parameter or element from the request body.
* Including random data such as names, dates, job titles, addresses etc.
* Generating values that must be calculated per request e.g. when a cryptographic signature must be included.

Postman supports a rich set of random value generators based on [Faker](https://www.npmjs.com/package/faker){:target="{{site.data.misc.blank}}"}.

You can include tokens like {% raw %} `{{$randomFirstName}}` {% endraw %} in an example's
response body and they will be substitued with a random value of the type specified
when the response is served. You can find the [full list of available tokens here](https://learning.postman.com/docs/writing-scripts/script-references/variables-list/){:target="{{site.data.misc.blank}}"}.

Postman also supports using wildcards as variables in response bodies, so for instance
if your example's URL is {% raw %} `/v1/users/{{user_id}}` {% endraw %} then you
can return the value of `user_id` in your response body by including {% raw %} `/v1/users/{{user_id}}` {% endraw %}.

<img src="/images/postman-vs-mocklab/postman-dynamic-variable.png" style="border: none" alt="Postman dynamic variable" />

MockLab supports full [Handlebars](https://handlebarsjs.com/){:target="{{site.data.misc.blank}}"}
template syntax in its response bodies and headers. This enables [conditional logic and looping](/docs/response-templating/conditional-logic-and-iteration/){:target="{{site.data.misc.blank}}"}, variables, and access to a library of functions via Handlebars helpers.

The model provided to the template
includes all [attributes of the incoming request](/docs/response-templating/basics/#the-data-model){:target="{{site.data.misc.blank}}"}, so it's possible to include
URL path elements, query parameters, header and the request body in your responses.

Additionally, there are a number of Handlebars helpers to extract and transform
[text](/docs/response-templating/string-helpers/){:target="{{site.data.misc.blank}}"}/[JSON](/docs/response-templating/json/){:target="{{site.data.misc.blank}}"}/[XML](/docs/response-templating/xml/){:target="{{site.data.misc.blank}}"},
manipulate [dates/times](/docs/response-templating/dates-and-times/){:target="{{site.data.misc.blank}}"}, generate [random strings](/docs/response-templating/random-values/){:target="{{site.data.misc.blank}}"}
(the full Faker set isn't supported yet) and generate properly constructed, signed [JSON Web Tokens (JWTs)](/docs/response-templating/jwt/){:target="{{site.data.misc.blank}}"}.

<img src="/images/postman-vs-mocklab/mocklab-response-templating.png" style="width: 80%;padding: 15px" alt="MockLab response templating" />


## Response delays

Mock servers often return responses faster than their real API counterparts.
This is great when you just want your functional test suite to run as fast as possible,
but if you're testing your app's UX with realistic timings, or want to check that
a timeout is configured correctly then you need to be able to add artificial delay
to your responses.

Postman permits you to specify a fixed delay at the mock server level.

<img src="/images/postman-vs-mocklab/postman-response-delay.png" alt="Postman response delay" />

MockLab supports [fixed, random and chunked delays](/docs/delays/){:target="{{site.data.misc.blank}}"} at the individual stub level.

<img src="/images/postman-vs-mocklab/mocklab-response-delay.png" style="width: 80%" alt="MockLab response delay" />


## Automation

In modern development and testing workflows it's common to want to automate the setup of
your environment and to be able to operate your tools programmatically. Both products provide APIs for these purposes.

[Postman's API](https://www.postman.com/postman/workspace/postman-public-workspace/folder/12959542-6c733d02-0bec-4bcb-bb36-59db8d941198?ctx=documentation){:target="{{site.data.misc.blank}}"}
supports create/update/delete of mock servers and collections,
allowing you to automatically configure the mock servers available to your environment
and the specific examples served by them.

<img src="/images/postman-vs-mocklab/postman-api.png" style="border:none;width:30%" alt="Postman API" />

[MockLab's Provisioning API](/docs/provisioning/){:target="{{site.data.misc.blank}}"}
allows mock APIs to be created and deleted.

Once created a mock API has its own [Mocking API](/docs/mocking/){:target="{{site.data.misc.blank}}"}
which supports create/update/delete of stubs, querying of the request log and control
of other features such as recording.

<img src="/images/postman-vs-mocklab/mocklab-api.png" style="border:none" alt="MockLab API" />


## Other features

MockLab provides a few extra capabilities that are not yet supported by Postman.

[Recording](/docs/recording-stubs/){:target="{{site.data.misc.blank}}"} - the ability to intercept HTTP traffic between your app (or a client tool)
and an API, converting it into a set of stubs.

[Proxying](/docs/proxying/){:target="{{site.data.misc.blank}}"} - conditionally forwarding requests to another API. This is useful
when you want to test against a real API, but mock the parts of it that haven't yet been implemented.
It's also a good way to reliably return otherwise hard-to-reproduce errors.

[Faults](/docs/simulating-faults/){:target="{{site.data.misc.blank}}"} - a MockLab stub can respond with a number of network faults including dropped
connections and garbage HTTP content.

**Performance testing** - Mock servers in Postman are rate limited, meaning they're not
practical for use in a load test. MockLab's Performance plans provide dedicated host
capacity, no rate limiting and a performance metrics dashboard.


## Conclusion

Postman mock servers are a great solution if you already have Postman in your
workflow, your request matching requirements are simple and you want a
straightforward solution that's tightly integrated with your existing setup.

MockLab may be the best choice if you need fine control over how requests are matched,
advanced response templating, recording, proxying, a comprehensive API or support for performance testing.

And remember, you can [import your Postman collections](https://www.mocklab.io/docs/import-export/postman/){:target="{{site.data.misc.blank}}"} into MockLab to get started.

<div class="mid-article-cta">
  <p class="mid-article-cta__text">
    If you've made it this far, why not give MockLab a try?
  </p>

  <p class="mid-article-cta__text">
    It's quick, simple and free to get started, and it has the flexibility to grow with your project's testing demands.
  </p>

  <a class="mid-article-cta__learn-more" href="https://get.mocklab.io/?utm_source=www.mocklab.io&utm_medium=blog&utm_campaign=postman-vs-mocklab-learn-more-cta" title="Learn more" >Learn more</a>

  <a class="mid-article-cta__get-started" href="https://app.mocklab.io/login?for=signup&utm_source=www.mocklab.io&utm_medium=blog&utm_campaign=postman-vs-mocklab-get-started-cta" title="Get started for free" target="{{site.data.misc.blank}}">Get started for free</a>
</div>
