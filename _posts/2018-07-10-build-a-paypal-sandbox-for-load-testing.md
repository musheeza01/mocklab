---
author: Tom Akehurst
author_pic: tom_akehurst.png
layout: single
title: Build a Paypal Sandbox for Load Testing in 10 Minutes
description: Build a mock of the Paypal API to allow end to end load testing of your e-commerce app
excerpt: Build a mock of the Paypal API to allow end to end load testing of your e-commerce app.
categories: ['MockLab', 'API Mocking', 'Performance Testing', 'Paypal']
comments: true
---

<div class="video">
<script src="https://fast.wistia.com/embed/medias/tz7u9ot0sj.jsonp" async></script><script src="https://fast.wistia.com/assets/external/E-v1.js" async></script><div class="wistia_responsive_padding" style="padding:56.25% 0 0 0;position:relative;"><div class="wistia_responsive_wrapper" style="height:100%;left:0;position:absolute;top:0;width:100%;"><div class="wistia_embed wistia_async_tz7u9ot0sj videoFoam=true" style="height:100%;position:relative;width:100%"><div class="wistia_swatch" style="height:100%;left:0;opacity:0;overflow:hidden;position:absolute;top:0;transition:opacity 200ms;width:100%;"><img src="https://fast.wistia.com/embed/medias/tz7u9ot0sj/swatch" style="filter:blur(5px);height:100%;object-fit:contain;width:100%;" alt="" onload="this.parentNode.style.opacity=1;" /></div></div></div></div>
</div>

Modern e-commerce websites and apps need to be fast and stable in order to avoid losing swathes of customers to competitors.

Achieving this requires regular performance testing of systems, but this is a problem for sites that accept payments with Paypal since
running high load against their sandbox is specifically prohibited by their terms of service.

So what can you do?

One option is to simply have your load test stop short of the final step of your checkout. This is less than ideal for (hopefully) obvious reasons.

Another option is to mock the part of the app's code that calls Paypal during load testing. This is an improvement, but still means critical bits of code
used in production won't be tested.

A better solution is to build a simulation of the Paypal API and connect your test environment to it.

MockLab is a tool specifically built for this purpose, and the rest of this article will focus on how you can use it to quickly construct a mock Paypal API
suitable for performance testing.

## Setup

To begin, you need to have logged into MockLab and created a new, empty mock API. You can [sign up here](https://app.mocklab.io/login?for=signup){:target="{{site.data.misc.blank}}"} if you haven't already.

Once you've created a new mock API, grab a copy of its base URL. You can find this in the title bar:

<img src="/images/screenshots/paypal-article/base-url.png" alt="Base URL" style="width: 70%"/>

You'll also need the client ID and client secret from your Paypal Sandbox account.

We've developed a simple demo app for this tutorial. If you'd like to download and run this you can find it at [https://github.com/mocklab/mocklab-demo-app](https://github.com/mocklab/mocklab-demo-app){:target="{{site.data.misc.blank}}"}

Once you've checked out the app, set the Paypal endpoint in `src/main/resources/application.properties` to your mock API's base URL:

<img src="/images/screenshots/paypal-article/app-config.png" alt="App configuration" style="width: 70%"/>

Then start the app:

[Linux/OSX]

```bash
export PAYPAL_CLIENT_ID=<your client ID>
export PAYPAL_CLIENT_SECRET=<your client secret>
 ./gradlew bootRun
```

[Windows]

```
set PAYPAL_CLIENT_ID=<your client ID>
set PAYPAL_CLIENT_SECRET=<your client secret>
gradlew bootRun
```

## Recording initial stubs

In MockLab, hit the Record button, set the target URL as Paypal's sandbox then start recording:

<img src="/images/screenshots/paypal-article/recorder.png" alt="Stub recorder" style="width: 70%; border: none"/>

Go to [http://localhost:9000/paypal](http://localhost:9000/paypal){:target="{{site.data.misc.blank}}"}, enter an amount and complete a Payment:

<img src="/images/screenshots/paypal-article/paypal-demo-app.png" alt="Paypal demo app" style="width: 70%; border: none"/>

Return to MockLab and stop recording. You should now see two stubs have been appeared in the list, one for the prepare payment step and one for execute payment.

If you wish you can test these with the internal request maker or an HTTP client like Postman:

<img src="/images/screenshots/paypal-article/test-request-1.png" alt="Test request in Postman" />

## Making performance ready

Recorded stubs are by default very specific. They will only be matched to an incoming request if the method, URL and body match exactly. In practice this
means they'll only work for the exact same payment ID and amount.

Additionally they will return the same response each time,
meaning IDs and dates you'd expect to be unique between requests will be the same. This causes problems if, for instance, the payment ID is saved
in a unique field by your app.

All of these issues can be resolved using MockLab's advanced matching and templated responses.

### Create payment

Open the create payment stub first (the one with URL `/v1/payments/payment`). The generated name is a bit ugly so you may wish to change this to something
more readable.

Next up, in the Advanced section you'll notice there's a body matcher for the exact JSON that was sent during recording. We don't need this, since we want to respond
to any create payment request, so delete this.

<img src="/images/screenshots/paypal-article/request-body.png" alt="Request body" style="width: 90%; border: none"/>

We want to ensure we're sending a unique payment ID in the response each time. To do this, we need to enable [response templating](/docs/response-templating/):

<img src="/images/screenshots/enable-response-templating-screenshot.png" alt="Enable response templating" style="width: 30%"/>

Then we need to generate a random ID for each response. We can do this by first assigning a variable by adding this line at the top of the response body:

{% raw %}
```
{{#assign 'paymentId'}}PAY-{{randomValue length=24 type='ALPHANUMERIC' uppercase=true}}{{/assign}}
```
{% endraw %}

and replacing instances of the payment ID with the variable we set e.g.

{% raw %}
```
"id": "{{paymentId}}"
```
{% endraw %}

We also may wish to set the `create_time` field to the current time, rather than it being fixed at the one captured at record time:

{% raw %}
```
"create_time": "{{now}}"
```
{% endraw %}

Putting it all together it should look something like this:

<img src="/images/screenshots/paypal-article/create-payment-body.png" alt="Templated create payment body" style="border: none"/>


### Execute payment

We need to give similar treatment to the execute payment stub, with a key difference: rather than generating the payment ID at random,
we want to extract it from the request URL.

You'll notice the request URL for the execute step is also very specific - it will only match the exact payment ID passed. We can fix this by
converting it to a regular expression (not that the URL match type has also changed to 'Path Regex'):

<img src="/images/screenshots/paypal-article/url-regex.png" alt="URL regular expression" style="border: none"/>

Next we'll give the response body the same treatment as with the create payment stub, but extracting the ID from the URL:

{% raw %}
```
{{#assign 'paymentId'}}{{request.path.[3]}}{{/assign}}
```
{% endraw %}

(Ensure you change substitute the `paymentId` variables `now` variables where appropriate as before)

## Adding delays for extra realism

Since stubbed responses tend to return faster than from real APIs, this can create misleading outcomes when performance testing. To remedy this,
you can add some artificial delay to your stubs:

<img src="/images/screenshots/paypal-article/random-delay.png" alt="Random delay" style="width: 70%"/>

## Validating your stubs

You may wish to quickly test your updated stubs before pressing them into service. Again, you can do this with a tool like Postman, or
with MockLab's own test request maker:

<img src="/images/screenshots/paypal-article/test-requester.png" alt="Test requester" />

## Load testing

Now your mock API is ready for use. You can try it out by running the JMeter from the demo project and opening `paypal-demo.jmx`.

You can start JMeter like this:

[Linux/OSX]

```
cd mocklab-demo-app
apache-jmeter-4.0/bin/jmeter.sh
```

[Windows]

```
cd mocklab-demo-app
apache-jmeter-4.0/bin/jmeter
```

<img src="/images/screenshots/paypal-article/jmeter.png" alt="JMeter" style="border:none" />

Then just hit play to start testing!


{% include mid-article-cta.html campaign="paypal-in-10-minutes" %}
