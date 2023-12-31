---
layout: default
title: Security
parent: AppDev Handbook
nav_order: 8
---

**Table of Contents**

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->

- [Security](#security)
  - [Dependabot Alerts](#dependabot-alerts)
    - [Known Issues](#known-issues)
  - [Application Security Testing (AST)](#application-security-testing-ast)
  - [Content Security Policy (CSP)](#content-security-policy-csp)
  - [Cross-Site Request Forgery (CSRF) Protection](#cross-site-request-forgery-csrf-protection)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

# Security

Vulnerability scanning and other security features are enabled by default by the UNITY platform.
The goal ist to make fixing security features as for app developers as easy as possible.
However, it is the application developers responsibility to fix vulnerabilities and security features in the appliation
code.

## Dependabot Alerts

Dependabot alerts are enabled for all repositories in the UNITY organisation.

### Known Issues

There is currently a known issue with the `@bmw-ds/components` dependency. There is
a [Malware in @bmw-ds/components ][False Malware Report @bmw-ds/components] alert, which can be safely dismissed as
"This alert is inaccurate or incorrect":

![This-alert-is-inaccurate-or-incorrect.png](..%2Fassets%2FThis-alert-is-inaccurate-or-incorrect.png)

[False Malware Report @bmw-ds/components]: https://teams.microsoft.com/l/message/19:14f4e3a00c0544e2b5a02dd28db3ea92@thread.skype/1656588739336?tenantId=ce849bab-cc1c-465b-b62e-18f07c9ac198&groupId=30b20eef-ffc7-4fa5-aed5-d2f15cfc324c&parentMessageId=1656588739336&teamName=Design%20System%20Community&channelName=Angular&createdTime=1656588739336&allowXTenantAccess=false

## Application Security Testing (AST)

UNITY does not provide any kind of AST out of the box. Apps are responsible for AST and fulfill all BMW requirements on
AST.
The [AST Navigator](https://ast-navigator.bmwgroup.net) is a good starting point.

## Content Security Policy (CSP)

> A strong CSP provides an effective second layer of protection against various types of vulnerabilities, especially
> XSS.
> Although CSP doesn't prevent web applications from containing vulnerabilities, it can make those vulnerabilities
> significantly more difficult for an attacker to exploit.

See [Defense in Depth](https://cheatsheetseries.owasp.org/cheatsheets/Content_Security_Policy_Cheat_Sheet.html#defense-in-depth).

The recommended `Content-Security-Policy`
is `frame-ancestors 'self'; form-action 'self'; object-src 'none'; default-src 'self';` which can be set like in the
following example:

```yaml
deployments:
  ui:
    headers:
      response:
        add:
          Content-Security-Policy: frame-ancestors 'self'; form-action 'self'; object-src 'none'; default-src 'self';
        remove:
          - Content-Security-Policy
```

However, when setting `default-src 'self'` pages generated by frameworks
such as [Swagger UI](https://swagger.io/tools/swagger-ui/) and [Angular](https://angular.io) will not work out of the
box, which is why the default CSP config does not include `default-src 'self`.
There are alternative secure approaches which are described in
the [Content Security Policy Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Content_Security_Policy_Cheat_Sheet.html#content-security-policy-cheat-sheet).
Setting [nonces](https://cheatsheetseries.owasp.org/cheatsheets/Content_Security_Policy_Cheat_Sheet.html#nonces)
or [hashes](https://cheatsheetseries.owasp.org/cheatsheets/Content_Security_Policy_Cheat_Sheet.html#hashes) must be
solved on the application side and is beyond the scope of this article.

## Cross-Site Request Forgery (CSRF) Protection

Application developers are responsible for protecting their apps against CSRF attacks.
There are various ways to do so, which are described in depth in
the [Cross-Site Request Forgery Prevention Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Cross-Site_Request_Forgery_Prevention_Cheat_Sheet.html).

Application developers should at least follow the guidelines below to protect a simple web application.

* Application state changing requests **must** be implemented as `POST`, `PUT`, `PATCH` or `DELETE` requests.
* A `GET` and `HEAD` requests **must not** lead to any state change on the application side (e.g. an `UPDATE` on a DB).
* All `POST` requests **must** require a non-empty message body (e.g. JSON) to transform them from
  a [simple request](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS#simple_requests) to a request
  requiring [CORS preflight requests](https://developer.mozilla.org/en-US/docs/Glossary/Preflight_request).
