# Introduction
## What is it?

Shaed is a web request proxy. Rather than proxying just at the HTTP level
(though it does that too), it can manipulate requests and proxy them to
other sites and services based on the input request.

For example, say you wanted to use a third party API in a Javascript-based
project and that API required a key in every request. To hide the key, you would
normally have to construct a proxy for that particular project. This gets
cumbersome if you have lots of projects. With Shaed you can set up a mapping
between a Shaed URL and the third party API, and tell Shaed to automatically
include the key parameter.

Shaed has 2 main components:

 - A **web-based administration panel** that allows you to log in and configure
   mappings and routes.
 - A **non-blocking request router** that performs the actual routing of
   requests.

## Screenshots

A picture is worth a thousand words.

<ul class="thumbnails">
  <li class="span3">
    <a class="thumbnail" href="img/screenshots/login.png"><img src="img/screenshots/login.png" alt="Login page" title="Login page" /></a>
  </li>

  <li class="span3">
    <a class="thumbnail" href="img/screenshots/mappings.png"><img src="img/screenshots/mappings.png" alt="List of mappings" title="List of mappings" /></a>
  </li>

  <li class="span3">
    <a class="thumbnail" href="img/screenshots/edit.png"><img src="img/screenshots/edit.png" alt="Editing a mapping" title="Editing a mapping" /></a>
  </li>

</ul>

## Features

 - Web-based administration with Google OpenID login.
 - Regex-based URL matching with the ability to interpolate match variables
   into target URLs.
 - Cache responses with a variable TTL.
 - Unlimited 'mappings'.
 - Merging of provided parameters with stored parameters (e.g. add an API key
   transparently).

Coming soon:

 - JSONP wrapper.
 - Header manipulation.
 - [Your feature](http://github.com/newsinternational/shaed).
