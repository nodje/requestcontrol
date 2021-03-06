Request Control Rule
--------------------

Request Control Rule consists of *request pattern*, *request types* and *rule action*.

Requests that match a pattern of an active rule, will be intercepted taking the action of the rule.

### Pattern

Request pattern has three parts: *scheme*, *host* and *path*. Rule can include one to many request patterns.

#### Scheme

Scheme matches the protocol of the request URL. The following schemes are supported.

|              |                                    |
|--------------|------------------------------------|
| `http`       | Match a http scheme.               |
| `https`      | Match a https scheme.              |
| `http/https` | Match both http and https schemes. |

#### Host

Host matches the host of the request URL. It can be in one of the following forms.

|                   |                                                                                                             |                                                                                            |
|-------------------|-------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------|
| `www.example.com` | Match a complete host.                                                                                      |                                                                                            |
| `*.example.com`   | Match the given host and any of its subdomains.                                                             | Will match any subdomain of example.com e.g. ***www**.example.com*, ***good**.example.com* |
| `www.example.*`   | Match the given host and all of the listed top-level domains. (can be combined with the subdomain matching) | Write the top-level domains to the top-level domain name list (e.g. *com*, *org*).         |
| `*`               | Match any host.                                                                                             |                                                                                            |

#### Path

Path matches the request URL path. Path may subsequently contain any combination of "\*" wildcard and any of the characters that are allowed in URL path. The "\*" wildcard matches any portion of path and may appear more than once. Below is examples for using path patterns.

|             |                                                                   |
|-------------|-------------------------------------------------------------------|
| `*`         | Match any path.                                                   |
| `path/a/b/` | Match exact path "path/a/b/".                                     |
| `*b*`       | Match path that contains a component "b" somewhere in the middle. |
|             | Match an empty path.                                              |

### Types

Apply rule to only certain types of requests. A type indicates the requested resource. Rule can apply from one to many types, or any type. All possible types and their descriptions are listed below.

| Type           | Details                                                                                                                                                   |
|----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------|
| Document       | Indicates a DOM document at the top-level that is retrieved directly within a browser tab. (main frame)                                                   |
| Sub document   | Indicates a DOM document that is retrieved inside another DOM document. (sub frame)                                                                       |
| Stylesheet     | Indicates a stylesheet (for example, &lt;style&gt; elements).                                                                                             |
| Script         | Indicates an executable script (such as JavaScript).                                                                                                      |
| Image          | Indicates an image (for example, &lt;img&gt; elements).                                                                                                   |
| Object         | Indicates a generic object.                                                                                                                               |
| Plugin         | Indicates a request made by a plugin. (object\_subrequest)                                                                                                |
| XMLHttpRequest | Indicates an XMLHttpRequest.                                                                                                                              |
| XBL            | Indicates an XBL binding request.                                                                                                                         |
| XSLT           | Indicates a style sheet transformation.                                                                                                                   |
| Ping           | Indicates a ping triggered by a click on an &lt;a&gt; element using the ping attribute. Only in use if browser.send\_pings is enabled (default is false). |
| Beacon         | Indicates a [Beacon] request.                                                                                                                             |
| XML DTD        | Indicates a DTD loaded by an XML document.                                                                                                                |
| Font           | Indicates a font loaded via <span class="citation">@font-face</span> rule.                                                                                |
| Media          | Indicates a video or audio load.                                                                                                                          |
| WebSocket      | Indicates a [WebSocket] load.                                                                                                                             |
| CSP Report     | Indicates a [Content Security Policy] report.                                                                                                             |
| Imageset       | Indicates a request to load an &lt;img&gt; (with the srcset attribute) or &lt;picture&gt;.                                                                |
| Web Manifest   | Indicates a request to load a Web manifest.                                                                                                               |
| Other          | Indicates a request that is not classified as being any of the above types.                                                                               |

  [Beacon]: https://developer.mozilla.org/en-US/docs/Web/API/Beacon_API
  [WebSocket]: https://developer.mozilla.org/en-US/docs/Web/API/WebSockets_API
  [Content Security Policy]: https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP

### Action

There are four different rule actions.

|        |           |                                                                                                                                                                                                                                                                                        |
|--------|-----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ![][1] | Filter    | Any request that matches a filter rule will be filtered according the filter rule configuration. With URL redirection filtering the request is taken directly to the contained redirect URL. With URL parameters trimming the configured URL parameters will be removed from requests. |
| ![][2] | Block     | Any request that matches a block rule will be cancelled before it is made.                                                                                                                                                                                                             |
| ![][3] | Redirect  | Any request that matches a redirect rule will be redirected to the configured redirect URL.                                                                                                                                                                                            |
| ![][4] | Whitelist | Any requests that match a whitelist rule will be processed normally without taking any action of any other matched rules.                                                                                                                                                              |

  [1]: /icons/icon-filter@19.png
  [2]: /icons/icon-block@19.png
  [3]: /icons/icon-redirect@19.png
  [4]: /icons/icon-whitelist@19.png


Rule priorities
---------------

Rules have following priorities:

1.  Whitelist rule
2.  Block rule
3.  Redirect rule
4.  Filter rule

Whitelist rules have the highest priority and they revoke all other rules. Next are block rules and they revoke redirect and filter rules. Finally redirect rules will be applied before filter rules. If more than one redirect or filter rule matches a single request they will all be applied one by one.

Matching all URLs
-----------------

The request pattern can be set to a global pattern that matches all URLs under the supported schemes ("http" or "https"). The global request pattern is enabled by checking the Any URL button.

Trimming URL parameters
-----------------------

Filter rules supports URL query parameter trimming. URL parameters are commonly used in redirection tracking as a method to analyze the origin of traffic. Trimmed URL parameters are defined either as literal strings with support for "\*" wildcard or using regular expression patterns. Below is examples of parameter trimming.

|             |                                       |
|-------------|---------------------------------------|
| utm\_source | Trim any "utm\_source" param          |
| utm\_\*     | Trim any param starting with "utm\_"  |
| /\[0-9\]+/  | Trim any param containing only digits |

### Invert Trim

Keep only parameters that are defined in trimmed parameters list. All other parameters will be removed.

### Trim All

Remove all URL query parameters from filtered request.

Redirect using pattern capturing
--------------------------------

There are two ways for redirecting requests based on the original request. The first way is to use parameter expansion that includes writing the redirection URL but using some patterns from the original request. The second way is to use a single or multiple instructions to override parts of the original request (e.g. instruct requests to redirect to a different port).

Both methods can also be used together. Redirect instructions will get parsed and applied to the base URL before parameter expansion.

### Parameter expansion

    {parameter}

Access a named parameter of original request URL. Available named parameters are listed at the end of this section.

Parameter expansion supports the following string manipulation formats:

#### Substring extraction

    {parameter:offset:length}

Include only a part of the expanded parameter. Offset determines the starting position. It begins from 0 and can be a negative value counting from the end of the string.

#### Substring replacing

    {parameter/pattern/replacement}

Replace a matched substring in the extracted parameter. Pattern is written in regular expression.

#### Combining manipulation rules

    {parameter(manipulation1)|(manipulation2)|...|(manipulationN)}

All the string manipulation rules can be chained using a "|" pipe character. The output is the result of the manipulations chain.

#### Examples

|                                                              |                                                                                                                                       |
|--------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------|
| *https&#58;//{hostname}/some/new/path*                       | Uses the hostname of the original request.                                                                                            |
| *https&#58;//{hostname::-3&#124;/\\.co$/.com}/some/new/path* | Uses the hostname of the original request but manipulates its length by three cutting it from the end and replaces ".co" with ".com". |

### Redirect instructions

    [parameter=value]

Modify the original request. Available named parameters are listed at the end of this section.

#### Examples

|                                     |                                                             |
|-------------------------------------|-------------------------------------------------------------|
| \[port=8080\]                       | Redirects the original request to a port 8080.              |
| \[port=8080\]\[hostname=localhost\] | Redirects the original request to a port 8080 of localhost. |

### List of named parameters

Names of the supported parameters and their example outputs are listed in below table.

Example address used as input:

    https://www.example.com:8080/some/path?query=value#hash

| Name     | Output                                                    |
|----------|-----------------------------------------------------------|
| protocol | `https:`                                                  |
| hostname | `www.example.com`                                         |
| port     | `8080`                                                    |
| pathname | `/some/path`                                              |
| search   | `?query=value`                                            |
| hash     | `\#hash`                                                  |
| host     | `www.example.com:8080`                                    |
| origin   | `https://www.example.com:8080`                            |
| href     | `https://www.example.com:8080/some/path?query=value\#hash`|

This help page is build upon the material of the following MDN wiki documents and is licenced under [CC-BY-SA 2.5].

1.  [Match patterns] by [Mozilla Contributors][Match patterns history] is licensed under [CC-BY-SA 2.5].
2.  [webRequest.ResourceType] by [Mozilla Contributors][webRequest.ResourceType history] is licensed under [CC-BY-SA 2.5].
3.  [URL] by [Mozilla Contributors][URL history] is licensed under [CC-BY-SA 2.5].
4.  [nsIContentPolicy] by [Mozilla Contributors][nsIContentPolicy history] is licensed under [CC-BY-SA 2.5].

  [CC-BY-SA 2.5]: http://creativecommons.org/licenses/by-sa/2.5/
  [Match patterns]: https://developer.mozilla.org/en-US/Add-ons/WebExtensions/Match_patterns
  [Match patterns history]: https://developer.mozilla.org/en-US/Add-ons/WebExtensions/Match_patterns$history
  [webRequest.ResourceType]: https://developer.mozilla.org/en-US/Add-ons/WebExtensions/API/webRequest/ResourceType
  [webRequest.ResourceType history]: https://developer.mozilla.org/en-US/Add-ons/WebExtensions/API/webRequest/ResourceType$history
  [URL]: https://developer.mozilla.org/en-US/docs/Web/API/URL
  [URL history]: https://developer.mozilla.org/en-US/docs/Web/API/URL$history
  [nsIContentPolicy]: https://developer.mozilla.org/en-US/docs/Mozilla/Tech/XPCOM/Reference/Interface/nsIContentPolicy
  [nsIContentPolicy history]: https://developer.mozilla.org/en-US/docs/Mozilla/Tech/XPCOM/Reference/Interface/nsIContentPolicy$history
