---
layout: docs
title: Security
toc_rank: 48
description: MockLab's API security and how to work with it
---

MockLab supports separately configurable authentication on the stub serving
and admin interface for each mock API.


## Admin API security

Every mock API created has its own admin API, which is used for stubbing, verification, reset and a few other things.
This has the same base URL as your mock API, with a base path of `/__admin` e.g. `https://example.mocklab.io/__admin`.

By default this is secured using the API token that can be found in [https://app.mocklab.io/account/api](https://app.mocklab.io/account/api){:target="{{site.data.misc.blank}}"}.

The token can either be sent in an Authorization header e.g.

`Authorization:Token 1kj3h98f7sihjfsf`

or as a query parameter e.g.

`https://example.mocklab.io/__admin/mappings?apiToken=1kj3h98f7sihjfsf`.

It is recommended that you use the header approach where possible as this reduces the risk of the key appearing in log files an browser histories.

### Disabling admin security

Admin API security can be disabled from the Settings page for the API:

<img src="/images/screenshots/security-toggle.png" title="Security toggle" style="width: 50%"/>

> **note**
>
> Admin API security will be disabled by default for APIs created prior to the security feature being released.


## Mock API security

Mock APIs can optionally be configured to authenticate callers. At present this can
be via HTTP Basic authentication (username + password) or an arbitrary header match.

### HTTP Basic authentication

<img src="/images/screenshots/http-basic-auth.png" title="HTTP Basic authentication" style="width: 50%"/>

HTTP Basic is a widely supported part of the HTTP standard supporting username/password authentication.
An HTTP resource secured with HTTP Basic will result in a browser prompting the user
with a username/password dialogue box on their initial visit.

Alternatively, an API client can pre-emptively authenticate by sending a header of the form
`Authorization:Basic <base64 encoded username:password>`.


### Header match authentication

<img src="/images/screenshots/header-match-auth.png" title="Header match authentication" style="width: 50%"/>

MockLab can also authenticate requests based on a match expression against any header.
The match expression works in the same way as header matches in the stub creation form,
whereby you specify the header name, predicate and expected value.
