---
title: Concepts & Configuration
description: Configure the Nuxeo PHP Client
review:
    comment: ''
    date: '2021-02-22'
    status: ok
labels:
    - php-client
tree_item_index: 200
section_parent: php
toc: true
---

## Concepts

Nuxeo PHP Client uses several libraries:
- [Guzzle](https://github.com/guzzle/guzzle/) to perform HTTP requests.
- [jms/serializer](https://github.com/schmittjoh/serializer) to serialize/deserialize requests/responses
- [Monolog](https://github.com/Seldaek/monolog) for logging

### NuxeoClient

NuxeoClient is the base client's class. It allows to get managers and to perform arbitrary HTTP request with client configuration.

Managers are created each time you get them, we're gonna see why in [configuration isolation](#configuration-isolation) section.

The server version is fetched from Nuxeo Server in a lazy way. Client will get this information by requesting `$URL/json/cmis`.

Current user is retrieved during `NuxeoClient#connect()`.

### Managers

Managers own a set of Nuxeo Server API, for example `UserManager` manager owns API to handle user and group.

### Public API and Service API

Typical usage of the Nuxeo PHP Client should be through its public API. It offers anything needed to interact with a Nuxeo Server with a developer-friendly Fluent API:

```php
use Nuxeo\Client\Objects\Document;

$document = $client->repository()
    ->schemas('dublincore') // only fetch dublincore properties
    ->enrichers('acls') // enrich Document with its ACLs
    ->fetchDocumentById('3dd87292-1345-4ad8-acb5-4e74c26bf893')
    ->setProperty('dc:title', 'Updated title')
    ->startWorkflow('validationWorkflow');
```

This public API strictly follows Semver recommendations and no breaking changes should happen except on Major releases, with depreciation warnings on previous releases.

Any internal logic of the Nuxeo PHP Client (serialization, HTTP internals but also both the `NuxeoEntity` and `AbstractConnectable` types) and within an SPI namespace and subject to breaking architectural changes. Extend the SPI with care.

### Objects

Every sent/received object in Nuxeo PHP Client extends the `NuxeoEntity` type.

The `NuxeoEntity` type holds the JSON converters and URL generation.

It extends the `AbstractConnectable` type that holds the interactions with the HTTP client.

### Marshalling

All serialization/deserialization are made by marshallers inside the `Nuxeo\Marshaller` namespace leveraging jms/serializer.

All the default marshallers are automatically setup within `NuxeoEntity` but you're free to add new marshallers for custom objects and override the default on your API calls:

```php
use Nuxeo\Client\Objects\Document;
use Acme\CustomDoc;

$document = $client->repository()
    ->fetchDocumentById('3dd87292-1345-4ad8-acb5-4e74c26bf893', null, CustomDoc::class);
```

## Configuration

Depending on configuration type, you can configure your clients at different moment:
- during use with `NuxeoClient`
- during use with managers - access specific manager configuration

NuxeoClient owns only common configuration.

Managers own common and specific configuration.

{{> anchor 'configuration-isolation'}}
### Configuration Isolation
The manager configuration is isolated from the client one.

This means that each configuration made on a manager belongs to this manager instance.
For instance:
```php
$repository1 = $client->repository()->schemas("*");
$repository2 = $client->repository()->schemas("dublincore");

// document containing all schemas
$doc1 = $repository1->fetchDocumentRoot();
// document containing only dublincore
$doc2 = $repository2->fetchDocumentRoot();
```

### Configuration options

| Method                                                           | Description                                                                            |
| ---------------------------------------------------------------- | -------------------------------------------------------------------------------------- |
| header(string $name, string $value, bool $append)                | Replace or append header of a given name with given value                              |
| enrichers(string $entityType, string $enrichers, bool $append)   | Replace or append enricher of given type with given value                              |
| enrichers(string $entityType, string[] $enrichers, bool $append) | Replace or append enricher of given type with given values                             |
| schemas(string $schemas, bool $append)                           | Replace or append schemas to fetch with given values, `*` is allowed to fetch them all |
| schemas(string[] $schemas, bool $append)                         | Replace schemas to fetch with given values, `*` is allowed to fetch them all           |
| voidOperation()                                                  | Set void operation header for next call (only usefull for Operations)                  |
| withAuthentication($authenticationInterceptor)   | Setup authentication, see [authentication]({{page page='authentication'}}) page                        |

#### Examples

```php
use Nuxeo\Client\NuxeoClient;
use Nuxeo\Client\Objects\Document;
use Nuxeo\Client\Objects\Repository;

$client = new NuxeoClient('http://localhost:8080/nuxeo');
$client
    ->schemas("dublincore")
    ->enrichers("thumbnail");

/** @var Repository $repository */
$repository = $client->repository()
    ->schemas('*') // fetch all properties instead of dublincore
    ->enrichers('document', 'acls', true); // enrich Entity with its ACLs in addition to its Thumbnail

/** @var Document $document */
$document = $repository->fetchDocumentById('3dd87292-1345-4ad8-acb5-4e74c26bf893');
```

<div class="row" data-equalizer data-equalize-on="medium">
<div class="column medium-6">
{{#> panel heading='Related sections in this documentation'}}

- [Nuxeo - Special HTTP Headers]({{page space='nxdoc' page='special-http-headers'}})

{{/panel}}
</div>
</div>
