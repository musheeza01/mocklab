---
layout: docs
title: Getting Started
toc_rank: 10
redirect_from:
  - /docs/
  - /
description: Overview of the MockLab APIs.
---

This article is intended to help new users of MockLab get their first mock API up and running.

MockLab provides a number of ways to set up a mock API:

* Manually via the **web UI**
* **Swagger** or **OpenAPI** specification import
* **Record** traffic to and from another API
* **Import** an existing project from **WireMock**
* **Automate** via the REST API or WireMock


## Creating an account

Before you can proceed you'll need a MockLab login, so if you've not signed up already then [you can do so here](https://app.mocklab.io/login?for=signup){:target="{{site.data.misc.blank}}"}.


## Manual mock API configuration

Creating stubs manually in the web UI is often the simplest way forward if:

* The API you're mocking is quite simple (or you only need a small part of it)
* You're working from a specification document and you want to copy/paste examples into a running simulation
* You just want to quickly explore what MockLab can do

After signup an example mock API will be created for you, showcasing different MockLab features. You can edit this or add your own stubs to experiment with MockLab's capabilities.

If you'd like to start afresh and choose your own domain name, you can create a new mock API by clicking <img style="margin-bottom: 0.8rem; vertical-align: middle; border: none; height: 45px" src="/images/screenshots/new-mock-api-button.png" title="New mock API"/>

More details on stub creation can be found in the [Stubbing](/docs/stubbing/) and [Advanced stubbing](/docs/advanced-stubbing/) articles.

For a more in-depth look at how MockLab can be used with a manual approch, see [the exploratory testing tutorial](/docs/exploratory-testing-tutorial/).

## Swagger and OpenAPI import

If you already have a Swagger or OpenAPI specification, you can import it and MockLab will auto-generate a set of stubs.

Swaggerhub users can integrate with MockLab via a webhook, so that the mock API
will be updated each time a change is saved.

See [Swagger Import](/docs/swagger/) for more details.


## Record and playback

If you want to create a mock of an existing API which is accessible over the internet,
you can configure MockLab to proxy (forward) traffic to it and record requests as stubs.

See [Recording Stubs](/docs/recording-stubs/) for details.


## Importing from WireMock

MockLab uses WireMock as its underlying engine, so mock APIs created within WireMock
can be directly imported into MockLab (and vice versa).

This can be useful when you need to record APIs that are only accessible inside your
organisation or from a private network, or if you have existing projects utilising
WireMock that you'd like to host in the cloud.

See the article on [import and export](/docs/import-and-export/) for details on how to do this.


## Scripting and automation

Sometimes automation is the best approach to maintaining the configuration of your mock APIs.
Some reasons for this include:

* You need to create hundreds or even thousands of stubs, so manual editing is impractical.
* You're mocking a fast-changing API, and generating stubs programmatically makes it easier to stay in sync.
* Your team manages all configuration as code which can be kept in source control.

MockLab enables this approach by making all features available via its REST APIs.

The [provisioning API](/docs/provisioning/) supports the creation, querying and deletion of mock APIs.

The [mocking API](/docs/mocking/) supports configuration of an individual mock API, including stub create/update/delete, request log querying and verification and more.
It is 100% compatible with [WireMock's API](http://wiremock.org/docs/api/) and can therefore be used with any WireMock client library.

See [the article on automated testing with Java](/docs/automated-testing-with-java)
to see how the Java WireMock library can be used to work with a mock API programmatically.
