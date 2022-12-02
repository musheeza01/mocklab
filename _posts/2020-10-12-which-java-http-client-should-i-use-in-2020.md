---
author: Tom Akehurst
author_pic: tom_akehurst.png
layout: single
title: Which Java HTTP client should I use in 2020?
description: A comparison of all the major Java HTTP clients
excerpt: A review and comparison of all the major Java HTTP client libraries available in 2020.
categories: ['Java', 'HTTP']
comments: true
---

HTTP has become the dominant protocol for integration of networked programs, and consequently many (possibly most) Java projects need to be able to make HTTP calls to other systems.

As with many things in the Java ecosystem, a broad array of alternatives exist for achieving this, both via core libraries and open source.

This can make choosing the right HTTP client less than straightforward. This article aims to provide an overview of the major libraries, with a focus on the characteristics you’re likely to care about when making a selection for your project.

We’re only going to discuss clients that actually implement the HTTP protocol, so libraries such as Spring’s RestTemplate or Feign that act as higher-level wrappers will not be discussed (although we may look at these in a future article).

## Criteria

So what should we consider when choosing an HTTP client?

Obviously, each project’s requirements are somewhat unique, but there are several common factors that most teams would wish to consider:
* Features
* API style
* Quality and maturity
* Maintenance and community
* Operability, reliability and observability
* Security
* Performance

The last three of these are deep topics in their own right, and we’ll write more about these in future articles.

While some factors are somewhat qualitative e.g. documentation, the following features can easily be described in yes/no form and are summarised for each client in the table below.

**Synchronous vs. asynchronous API**<br>
Whether the client supports a synchronous (blocking) call style, asynchronous (non-blocking) or both.

We also indicate what style (or styles) of async API is presented, if any. In practice, this is one or more of futures, callbacks or reactive streams.

It's worth pointing out that although most clients with an asynchronous API only support one
style (futures or callbacks typically), there are a number of wrapper libraries
such as Square's [Retrofit](https://square.github.io/retrofit/){:target="{{site.data.misc.blank}}"} or Spring's [*WebClient*](https://www.baeldung.com/spring-5-webclient){:target="{{site.data.misc.blank}}"} that adapt these to other styles such as reactive streams.

**HTTP/2**<br>
Support for version 2 of the HTTP protocol.

**Forms**<br>
Support for posting of form data to the server.

Note: all clients can do this if you’re willing to build up the request from scratch yourself, so what we’re really talking about here is whether a specific API is provided.

**Multipart request and file upload**<br>
Support for sending multipart requests to the server.

As with forms, we’re interested in whether the client provides an API specifically for this.

**Cookies**<br>
Receives, stores, sends and allows manipulation of cookies.

**Authentication**<br>
Support for HTTP authentication protocols. The most common of these are:

* [HTTP Basic](https://en.wikipedia.org/wiki/Basic_access_authentication){:target="{{site.data.misc.blank}}"}
* [Digest](https://en.wikipedia.org/wiki/Digest_access_authentication){:target="{{site.data.misc.blank}}"}
* [NTLM](https://en.wikipedia.org/wiki/NT_LAN_Manager){:target="{{site.data.misc.blank}}"}
* [SPNEGO](https://en.wikipedia.org/wiki/SPNEGO){:target="{{site.data.misc.blank}}"}
* [Kerberos](https://en.wikipedia.org/wiki/Kerberos_(protocol)){:target="{{site.data.misc.blank}}"}

While all of the clients discussed can in principle support any authentication scheme by allowing setting of request headers and request parameters, only some offer explicit extension points into which authentication implementations can be plugged, and fewer still offer common schemes out of the box.

**Transparent content compression/decompression**<br>
Whether the client can perform [compression and decompression](https://en.wikipedia.org/wiki/HTTP_compression){:target="{{site.data.misc.blank}}"}
of content via (most commonly) [Deflate](){:target="{{site.data.misc.blank}}"},
[GZip](https://en.wikipedia.org/wiki/Gzip){:target="{{site.data.misc.blank}}"} or
[Brotli](https://en.wikipedia.org/wiki/Brotli){:target="{{site.data.misc.blank}}"}
without requiring the caller to explicitly perform the encode and decode steps.

At present GZip is by far the most common algorithm.

**Caching**<br>
Support for caching of HTTP responses according to the caching elements of the HTTP standard.

**Websockets**<br>
Support for the [Websockets](https://en.wikipedia.org/wiki/WebSocket){:target="{{site.data.misc.blank}}"} extension to the HTTP spec.

## Configuration options
The potential range of configuration options for an HTTP client is large, and some of the clients discussed here offer very fine-grained control over both high and low-level aspects of their behaviour.

However, there are a few settings that are likely to be of interest to most teams (at some stage):
* Connect timeout
* Socket read timeout
* Redirect policy
* Pooled connection limit - overall and per destination/route
* Idle connection timeout
* Proxy server address and authentication settings

A further characteristic to be aware of is that some clients (specifically the core Java ones) only expose certain parameters as system properties. This means that a) they can only be set globally per running JVM, and b) they are much harder to integrate with other configuration systems.

In general, being able to set configuration parameters programmatically, at the client instance level is preferable as this permits multiple clients to exist differing options and simpler integration into frameworks and apps with their own config arrangements. It also makes life much easier if you’re deploying into an environment such as a servlet container where you may not have full control over startup parameters.

{% include mid-article-cta.html campaign="java-http-clients" %}

## The clients
The HTTP clients we will examine are:

* Java’s [HttpURLConnection](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/net/HttpURLConnection.html){:target="{{site.data.misc.blank}}"} and [HttpsURLConnection](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/javax/net/ssl/HttpsURLConnection.html){:target="{{site.data.misc.blank}}"}
* The new [HttpClient](https://docs.oracle.com/en/java/javase/11/docs/api/java.net.http/java/net/http/HttpClient.html){:target="{{site.data.misc.blank}}"}, introduced in Java 11
* [Apache HTTPClient](https://hc.apache.org/httpcomponents-client-5.0.x/index.html){:target="{{site.data.misc.blank}}"}
* [OkHttpClient](https://square.github.io/okhttp/){:target="{{site.data.misc.blank}}"}
* [AsyncHttpClient](https://github.com/AsyncHttpClient/async-http-client){:target="{{site.data.misc.blank}}"}
* [Jetty HttpClient](https://www.eclipse.org/jetty/documentation/current/http-client.html){:target="{{site.data.misc.blank}}"}


Below is a summary of the salient information and features for each client:

|                                            | Java version compatibility (current version of client) | Original release date                   | Latest release (as of September 2020)                                                     | Sync/Async | Async API style(s)                  | HTTP/2                         | Forms | Multipart / file upload | Cookies | Authentication                         | Transparent content compression | Caching | Websockets |
|--------------------------------------------|--------------------------------------------------------|-----------------------------------------|------------------------------------------------------------------------------------------------------|------------|-------------------------------------|--------------------------------|-------|-------------------------|---------|----------------------------------------|---------------------------------|---------|------------|
| <img src="/images/http-clients/old-java-logo.png" style="border: none; height: 50px" alt="Old Java logo"  /><br> HttpURLConnection | 1.1+ (HTTP)<br>1.4+ (HTTPS)                              | 1997 (with JDK 1.1)<br>2002 (with JDK 1.4) | September 2020 (with Java SE 15)                                                                     | Sync only  | N/A                                 | No                             | No    | No                      | No      | None                                   | No                              | No      | No         |
| <img src="/images/http-clients/new-java-logo.png" style="border: none; height: 50px" alt="New Java logo"  /><br> Java HttpClient                          | 9+ (as incubator module) 11+ (GA)                      | September 2018 (with Java SE 11)        | September 2020 (with Java SE 15)                                                                     | Both       | Futures                             | Yes                            | No    | No                      | Yes     | Basic (not pre-emptive) Pluggable      | No                              | No      | Yes        |
| <img src="/images/http-clients/httpcomponents-logo.png" style="border: none; height: 40px" alt="Apache HTTP Components logo"  /><br> Apache HTTPClient                        | 7+                                                     | 2001                                    | September 2020                                                                                       | Both       | Futures                             | Currently only in the 5.1 beta | Yes   | Yes                     | Yes     | Basic<br>Digest<br>NTLM<br>SPNEGO<br>Kerberos<br>Pluggable | GZip Deflate                    | Yes     | No         |
| <img src="/images/http-clients/square-logo.png" style="border: none; height: 50px" alt="Square logo"  /><br> OkHttp                                   | 8+                                                     | 2013                                    | September 2020                                                                                       | Both       | Callbacks                           | Yes                            | Yes   | Yes                     | Yes     | Pluggable                              | GZip Deflate Brotli             | Yes     | Yes        |
| AsyncHttpClient                          | 8+                                                     | 2010                                    | April 2020 | Async only | Futures  Callbacks Reactive streams | No                             | Yes   | Yes                     | Yes     | None                                   | No                              | Yes     | Yes        |
| <img src="/images/http-clients/jetty-logo.png" style="border: none; height: 40px" alt="Jetty logo"  /><br> Jetty HttpClient                         | 8+                                                     | 2009                                    | July 2020                                                                                            | Both       | Callbacks                           | Yes                            | Yes   | Yes                     | Yes     | Basic<br>Digest<br>SPNEGO<br>Pluggable          | GZip                            | No      | Yes        |


The following sections describe each client in detail.

## HttpURLConnection / HttpsURLConnection
<img src="/images/http-clients/old-java-logo.png" style="border: none; height: 100px" alt="Old Java logo"  />

[*HttpURLConnection*](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/net/HttpURLConnection.html){:target="{{site.data.misc.blank}}"} is the oldest of the clients we’re comparing, and probably the oldest in the Java ecosystem, having been introduced way back in version 1.1 of the JDK. Its HTTPS supporting counterpart HttpsURLConnection was introduced in Java 1.4.

The main advantage of using these classes is that they’re guaranteed to be available, even in older Java versions, without the need to add any external dependencies.

Arguably, however, that’s where the benefits end. These classes suffer from pretty clunky API design, only offer a synchronous/blocking API, have no HTTP/2 support and lack additional features such as compression and form handling.

They also exhibit some pretty questionable (or buggy, depending on your view) [default behaviour](https://bugs.java.com/bugdatabase/view_bug.do?bug_id=6382788){:target="{{site.data.misc.blank}}"} which has been retained throughout the versions for the sake of backwards compatibility.

It’s worth noting that many “wrapper” clients such as Spring’s RestTemplate will use *Http(s)URLConnection* as their default underlying HTTP implementation, so you can often end up using it without realising. It’s always a good idea to check how your HTTP calls are being made under the hood!

### Configuration
*HttpURLConnection* supports the basic set of configuration options you’d expect, but not much more. The connection pool limit and keep-alive idle timeout are only available as system properties.

### Documentation
The official documentation is pretty much limited to just the [Javadoc page](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/net/HttpURLConnection.html){:target="{{site.data.misc.blank}}"}, however, a plethora of 3rd party blog articles exist and at the time of writing, there are over 30k Stackoverflow questions relating to *HttpURLConnection*.

### When to use
Use it if you’re not yet on Java 11+ and you’re unable to add any more 3rd party dependencies. Otherwise, avoid!


## Java 11+ HttpClient
<img src="/images/http-clients/new-java-logo.png" style="border: none; height: 100px" alt="New Java logo"  />

Java 9 introduced a brand new HTTP client as an incubator module, and this was then made generally available in Java 11.

The new client has a fluent, builder-driven API which is much more legible and easier to work with than *HttpURLConnection*. It supports both synchronous and asynchronous modes of operation, with the latter making use of Futures.

A major benefit over the older Java client is support for HTTP/2, which is now becoming increasingly prevalent and offers some significant performance improvements over HTTP/1.1.

The new client offers a pluggable authentication mechanism, only provides an implementation of non-preemptive HTTP Basic so if you need anything else you’ll need to implement it yourself.

Transparent compression is not supported, so this is also something you’ll need to handle yourself if you need it.

It does however provide support for cookies.

### Configuration
Available configuration options are very similar to those for *HttpURLConnection*, with the connection pool limit and keep-alive idle timeout also only settable via system properties.

Unlike the older client, the redirect policy can be set programmatically per client instance.

### Documentation
The official documentation for the new *HttpClient* is a little bit better than for *HttpURLConnection*, consisting of a [recipes document](https://openjdk.java.net/groups/net/httpclient/recipes.html){:target="{{site.data.misc.blank}}"} in addition to the [Javadoc](https://docs.oracle.com/en/java/javase/11/docs/api/java.net.http/java/net/http/HttpClient.html){:target="{{site.data.misc.blank}}"}.

However as the newest client in our list, inevitably less has been written about it in blogs and forums, so expect to work a little harder in the near future to find answers to your questions.

### When to use
Use it if you’re already on Java 11+ and are happy with limited documentation and configuration options.


## Apache HttpClient
<img src="/images/http-clients/httpcomponents-logo.png" style="border: none; height: 100px" alt="Apache HTTP Components logo"  />

The Apache HTTP client was the first widely adopted open source client to be released, arriving in its original form in 2002. Now on its 5th major version, it’s probably still the most commonly used client outside of Java’s core libraries.

The Apache client is rich in features and configuration options, although at the time of writing HTTP/2 is only supported in the 5.1 beta release.

### Community and maintenance
<img src="/images/http-clients/apache-contribution-graph.png" style="border: none" alt="Apache client contribution graph" />

As a top-level Apache project, *HTTPClient*’s future seems fairly well assured. Contributions continue to arrive at a steady rate, albeit one that’s a bit lower than the most actively maintained libraries in this article.

### Configuration
One of the Apache client’s strengths is its configurability. Many features of the client such as connection management and name resolution are abstracted and pluggable, so it’s possible to extensively configure them in code, or even supply your own implementations. This makes the Apache client a good bet if you have unusual or tricky requirements.

While it will accept parameters supplied via system properties, all configuration can be done at the client instance level.

One gotcha to watch out for with the Apache client is the low default connection pool limits - 5 per route and 25 overall. These defaults can result in significant bottlenecks in high throughput systems, so it’s always wise to review and raise these before subjecting your app to live traffic.

### Documentation
The Apache client’s documentation website is produced using the Maven site generator, so it isn’t the most modern reading experience. The content of the documentation is OK if not great, providing code samples for a variety of scenarios, but without much supporting narrative.

Luckily, given how widely used *HTTPClient* is and its maturity there’s plenty of help available on Stack Overflow and 3rd party blog sites such as [Baeldung](https://www.baeldung.com/httpclient-guide){:target="{{site.data.misc.blank}}"}.

### When to use
The Apache client is a strong choice when you need extreme flexibility in configuring and customising behaviour. Teams that place a lot of value on familiarity and breadth of adoption may also find it the most comfortable option.


## OkHttpClient
<img src="/images/http-clients/square-logo.png" style="border: none; height: 100px" alt="Square logo"  />

*OkHttpClient* is an open source library originally released in 2013 by [Square](https://square.github.io/){:target="{{site.data.misc.blank}}"}.

The main motivation behind OkHttp is a strong focus on production readiness - performance, reliability and security. It natively supports HTTP/2 and TLS1.3+ and has a number of fault tolerance features such as the ability to fail over between multiple IP addresses and recover from failed connection attempts. It also implements transparent content compression via Deflate, GZip and Brotli, being the only client that supports the last of these.

*OkHttpClient*’s defaults are thoughtfully chosen. Provided you keep to the latest version, you’ll get a fast, secure and reliable setup without needing to do much of your own configuration.

Since version 4 OkHttp has been written in Kotlin. Some teams see this as a downside as it means that the Kotlin standard library is pulled in as a transitive dependency (adding about 1.5MB at the time of writing), and also that debugging the client’s code is more difficult for developers not familiar with Kotlin.

### Community and maintenance
<img src="/images/http-clients/okhttp-contribution-graph.png" style="border: none" alt="OkHttp client contribution graph" />

*OkHttp* continues to be sponsored by Square and shows a healthy, regular contribution rate. It has nearly 40k stars on GitHub and has been contributed to by over 200 individuals.

### Configuration
Despite being strong on usable out-of-the-box defaults, *OkHttpClient* provides plenty of configuration options and extension points. Everything is configurable at the client instance level, so multiple clients can exist with different settings and it’s straightforward to integrate with your configuration system.

Another configuration option OkHttp provides not seen elsewhere is separate read and write timeouts.

### Documentation
The OkHttp [documentation site](https://square.github.io/okhttp/){:target="{{site.data.misc.blank}}"} is very good and nicely presented. It provides a decent level of detail for all of the major topics, a decent API reference and a search function that actually works.

### When to use
OkHttp is the client we’d recommend by default.

It has all of the features you’re likely to need, both initially and in the future, and will probably give you the fewest headaches when running in production. It has extensive options for configuration and customisation, but will usually do a perfectly good job with its default settings.


## AsyncHttpClient
*AsyncHttpClient* was originally released in 2010, building on top of [Netty](https://netty.io/){:target="{{site.data.misc.blank}}"} for fast, non-blocking I/O. Uniquely amongst our set of clients, it only presents an asynchronous API, however, it provides considerable flexibility here, supporting Futures, Callbacks and Reactive Streams API styles.

### Community and maintenance
<img src="/images/http-clients/asynchttpclient-contribution-graph.png" style="border: none" alt="AsyncHttpClient contribution graph" />

The project has mostly seen a decent level of contribution activity over the last few years. However, as of June 2020, the lead maintainer of *AsyncHttpClient* has stepped down and is looking for a successor so its future is currently somewhat uncertain.

### Configuration
*AsyncHttpClient* provides a wealth of configuration options, allowing very fine-grained tuning of timeouts, thread pools and other high and low-level parameters. All of these are all settable from when building a client instance.

### Documentation
Official documentation is limited to the README document on GitHub, which provides an adequate but brief overview of the major features and a path to getting started. It links to several blog posts which go into a bit more depth, but these are 9-10 years old so are of limited utility.

A search of Stack Overflow for “asynchttpclient” yields a little over 2k results, so while you may have some success finding answers there, there’s significantly less content than for most of the other clients discussed in this article.

### When to use
We’d hesitate to recommend *AsyncHttpClient* at this point in time due to the uncertainty around its ongoing maintenance.

However, if you’re comfortable with this risk and need a battle-tested, high performing non-blocking client then it’s still worth a look.


## Jetty HttpClient
<img src="/images/http-clients/jetty-logo.png" style="border: none; height: 80px" alt="Jetty logo"  />

The Jetty client has been an integral part of the Jetty project since 2009. It uses entirely non-blocking code under the hood and presents both synchronous and asynchronous APIs.

HTTP/2 is supported via the inclusion of an additional library.

### Community and maintenance
<img src="/images/http-clients/jetty-contribution-graph.png" style="border: none" alt="Jetty contribution graph" />
[Contribution graph for the Jetty project overall]

The Jetty project lives within the Eclipse foundation. It’s also backed by a commercial company - Webtide. Their staff (including Jetty’s originator) make regular contributions to the project and are responsive to bug reports and queries.

### Configuration
The Jetty client provides a pretty comprehensive set of configuration options. These include all of the obvious settings you’d want to tweak, plus access to lower-level tuning parameters such as the executor implementation, scheduler and byte buffer pools.

One notable absence is an overall connection pool limit parameter. However, it is possible to set a per-destination limit for pooled connections, which in practice is often the more useful parameter.

### Documentation
The [Jetty client documentation pages](https://www.eclipse.org/jetty/documentation/current/http-client.html){:target="{{site.data.misc.blank}}"} are reasonably good, with a decent “getting started” section and fairly detailed coverage of the major features. Plenty of code examples are provided, with supporting narrative.

### When to use
The Jetty client supports HTTP/2 and is very configurable, which makes it a good alternative to the OkHttp client if you’re not happy with the latter’s Kotlin dependencies.

It may also be the right choice if you’re using Jetty server already since they share a fair amount of common code and API design. This means that things like TLS configuration work very similarly across client and server.


## Conclusions
Hopefully, if you’ve made it this far, you’re now more informed about the range of HTTP clients available in the Java ecosystem and how to go about selecting one for your project.

We’d definitely recommend avoiding *HttpURLConnection* unless you really have no alternative, particularly now that the Java 11+ *HttpClient* is available.

If ultimate flexibility is what you need and you can do without HTTP/2 for the time being, the venerable Apache client may be the one to aim for. You’ll also benefit from its very widespread use and the abundance of information around the internet.

However, all things being equal Square’s *OkHttpClient* would be our recommendation for teams choosing a new client library. It’s feature-rich, highly configurable and works well in production out of the box.

## About MockLab

MockLab is a hosted API mocking tool built on WireMock by its creators. It helps
teams quickly simulate their external API dependencies so that they can ship faster and test more
thoroughly.

For more information, visit [https://get.mocklab.io](https://get.mocklab.io?utm_source=www.mocklab.io&utm_medium=blog&utm_campaign=http_clients_article-bottom_cta).
