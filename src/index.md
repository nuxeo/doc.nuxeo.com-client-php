---
title: PHP Client SDK
description: Nuxeo Client SDK - PHP
review:
    comment: ''
    date: '2021-02-22'
    status: ok
labels:
    - php-client
is_overview: true
---

The Nuxeo PHP Client is a PHP library for Nuxeo REST API.

It is compatible with all Nuxeo versions as of LTS 2015.

## Getting Started

Follow this [tutorial]({{page space='nxdoc' page='setting-up-your-nuxeo-environment'}}) to have a running Nuxeo instance on your local machine.

### Library Import

Download the latest build [Nuxeo PHP Client main](https://github.com/nuxeo/nuxeo-php-client/archive/main.zip).

Download the latest stable [![GitHub release (latest SemVer)](https://img.shields.io/github/v/release/nuxeo/nuxeo-php-client)](https://github.com/nuxeo/nuxeo-php-client/releases)

Composer:

```json
  "require": {
    "nuxeo/nuxeo-php-client": "~2.1"
  }
```

### Create a Client

```php
use Nuxeo\Client\NuxeoClient;

$client = new NuxeoClient('http://localhost:8080/nuxeo', 'Administrator', 'Administrator')
    ->schemas('*'); // fetch all document schemas
```

Client is now ready to execute requests to Nuxeo Server.

### Fetch Your First Document

Below is how we can retrieve default domain from a stock Nuxeo Server:

```php
use Nuxeo\Client\Objects\Document;

Document $domain = $client->repository()->fetchDocumentByPath("/default-domain");
$title = $domain->getProperty("dc:title"); // should be equal to "Domain"
```

<div class="row" data-equalizer data-equalize-on="medium"><div class="column medium-6">{{#> panel heading='Related sections in this documentation'}}

- [Nuxeo - Getting Started]({{page space='nxdoc' page='getting-started'}})
- [Nuxeo - Setting up your environment]({{page space='nxdoc' page='setting-up-your-nuxeo-environment'}})

{{/panel}}</div></div>
