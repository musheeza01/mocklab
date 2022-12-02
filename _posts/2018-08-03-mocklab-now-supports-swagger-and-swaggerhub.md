---
author: Tom Akehurst
author_pic: tom_akehurst.png
layout: single
title: MockLab Now Supports Swagger and Swaggerhub
description: MockLab now supports import of Swagger documents and integration with Swaggerhub
excerpt: MockLab now supports import of Swagger documents and integration with Swaggerhub.
categories: ['MockLab', 'API Mocking', 'Open API Specification', 'Swagger', 'Swaggerhub']
comments: true
---

<img src="/images/screenshots/swagger-logo2.png" alt="Swagger logo" style="border:none"/>

[Swagger](https://swagger.io/){:target="{{site.data.misc.blank}}"} (or the OpenAPI Specification as it's now known) has become the de-facto standard
way to specify, document and test REST-like APIs.

Given Swagger's broad adoption and language/stack compatibility, being able to generate a
mock API from a Swagger specification offers a substantial productivity boost over coding or hand-crafting.
In addition it provides a way to ensure your mock API stays true to the real thing as it evolves.

This is why we've launched a first beta of Swagger import in MockLab. This new feature provides a number of ways
to generate and re-generate your mock API's stubs from a Swagger specification.

## How it works

The simplest way to get started is to paste your API spec into MockLab's web UI:

<img src="/images/screenshots/stub-toolbar-with-import3.png" alt="Stub toolbar" style="border: none;" />

<img src="/images/screenshots/import-stubs-dialog.png" alt="Stub toolbar" style="border-radius: 3px;" />

If you want to regularly push new specification versions via an automated process, you can do so by posting it
to MockLab's API. See [the documentation page](/docs/swagger/#automating-imports-via-the-api) for details.

On submission of a valid spec, MockLab will generate a collection of new stubs, for each combination of URL template, operation and content type.
If the spec contains response examples, these will be used, but if not the generator will fall back on the JSON schema, emitting random values that
conform to the schema constraints.


## Swaggerhub

<img src="/images/screenshots/swaggerhub-hero.png" alt="Swaggerhub hero" />

[Swaggerhub](https://swaggerhub.com) is a hosted product from Smartbear that supports authoring, collaboration and documentation of APIs.

A cool feature of Swaggerhub is its ability to call a webhook when a spec is saved or published. It's extremely simple to set up MockLab as
a receiver - just create a new webhook in the Integrations menu (<img src="/images/screenshots/swaggerhub-integrations-icon.png" alt="Swaggerhub integrations icon" style="border:none; width: 52px;" />) with a payload URL of `http://my-swagger-api.mocklab.io/__admin/imports/swaggerhub` (substituting `my-swagger-api` with your API's domain name):

<img src="/images/screenshots/swaggerhub-webhook-dialog.png" alt="Swaggerhub webhook dialog" />

Once you've added this, try saving your API then heading back to MockLab and refreshing your stubs.

As I mentioned at the start, this is a first beta release, and we intend to evolve and improve Swagger support based on the feedback from real use. If you love, hate or simply think this feature could be improved please drop us a message in the chat widget or
[by email](mailto:info@mocklab.io) and let us know!

{% include mid-article-cta.html campaign="swagger-and-swaggerhub" %}
