---
title: Authentication
description: Handle authentication with Nuxeo PHP Client
review:
    comment: ''
    date: '2019-06-18'
    status: ok
labels:
    - php-client
tree_item_index: 100
section_parent: java
toc: true
---

Nuxeo PHP Client handles these authentication methods:
- basic authentication via `BasicAuthentication`
- portal SSO authentication via `PortalSSOAuthentication`
- token authentication via `TokenAuthentication`
- OAuth2 authentication via `OAuth2Authentication`

## Basic Authentication

The default authentication method is the basic authentication:

```php
use Nuxeo\Client\NuxeoClient;

new NuxeoClient('http://localhost:8080/nuxeo', 'Administrator', 'Administrator');
```

Whose equivalent is:

```php
use Nuxeo\Client\NuxeoClient;
use Nuxeo\Client\Auth\BasicAuthentication;

(new NuxeoClient())->withAuthentication(new BasicAuthentication("Administrator", "Administrator"));
```

## Portal SSO Authentication

Once Portal SSO authentication has been activated on your Nuxeo server you can use this method to connect the PHP client with:

```php
use Nuxeo\Client\NuxeoClient;
use Nuxeo\Client\Auth\PortalSSOAuthentication;

(new NuxeoClient())->withAuthentication(new PortalSSOAuthentication('nuxeo5secretkey', 'Administrator'));
```

## Token Authentication

Token authentication is enabled by default on Nuxeo server, to use it in Nuxeo PHP client:
```php
use Nuxeo\Client\NuxeoClient;
use Nuxeo\Client\Auth\TokenAuthentication;

(new NuxeoClient())->withAuthentication(new TokenAuthentication("nuxeoToken"));
```
Token acquisition is not implemented in PHP client and should be done by following this [documentation](https://github.com/nuxeo/nuxeo/tree/master/modules/platform/login/nuxeo-platform-login-token#implementation).

## OAuth2 Authentication

Oauth2 authentication is enabled by default on Nuxeo server but Nuxeo PHP client do not implement the login flow. You need to implement it on your own and use the access token with Nuxeo PHP client:

You can find a sample of login flow for a Symfon 4 application and the `league/oauth2-client` library under [integration/oauth2](https://github.com/nuxeo/nuxeo-php-client/tree/main/integration/oauth2)

```php
use Nuxeo\Client\NuxeoClient;
use Nuxeo\Client\Auth\OAuth2Authentication;

(new NuxeoClient())->withAuthentication(new OAuth2Authentication($accessToken));
```

## Implement Your Own

By design, Nuxeo PHP Client leverages `AuthenticationInterceptor` interface to handle authentication between client and server.

This is what expect the method: `NuxeoClient->withAuthentication(AuthenticationInterceptor $authenticationInterceptor)`.

You just need to implement one method which will be responsible to inject the appropriate header:

```php
use GuzzleHttp\Client;
use Nuxeo\Client\Request;
use Nuxeo\Client\Spi\Auth\AuthenticationInterceptor;

public class MyAuthInterceptor implements AuthenticationInterceptor {
    protected $token;
    
    public function __construct($token) {
      $this->token = $token;
    }
    
    public function proceed(Client $client, Request $request) {
        return $request->withHeader("MY-HEADER", $this->token);
    }
}
```

Then you can use it:

```php
(new NuxeoClient())->withAuthentication(new MyAuthInterceptor("nuxeoToken"));
```

<div class="row" data-equalizer data-equalize-on="medium"><div class="column medium-6">{{#> panel heading='Related sections in this documentation'}}

- [Nuxeo - REST Authentication]({{page space='nxdoc' page='request-authentication'}})
- [Nuxeo - Portal SSO Authentication]({{page space='nxdoc' page='using-sso-portals'}})

{{/panel}}</div></div>
