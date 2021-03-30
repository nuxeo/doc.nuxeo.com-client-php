---
title: Automation
description: Run Operation and Automation Chain
review:
    comment: ''
    date: '2021-02-22'
    status: ok
labels:
    - php-client
tree_item_index: 800
section_parent: php
toc: true
---

This section is about how to run automation with Nuxeo PHP Client.

Operation manager is instantiated for each automation to run, this gives us high isolation for each request to automation.

Automation input can be anything you want, Blob, Blobs, Document, Documents or primitive type. To give a document reference as input to automation, you need to use `DocRef`.

Automation output is dynamic and leverage [marshaller]({{page page='configuration'}}#marshalling) capabilities in order to return proper PHP object.

## Document.Create

Let's create a file:
```php
$file = $client->operation("Document.Create")
    ->param("name", "file001")
    ->param("type", "File")
    ->param("properties", "dc:title=My File\ndc:description=A short description")
    ->input(new DocRef("/"))
    ->execute(Document::class);
```
## Document.Update

Now, update it:
```php
$file = $client->operation("Document.Update")
    ->param("properties", "dc:nature=card")
    ->input($file)
    ->execute(Document::class);
```

{{#> callout type='note' heading='Document as Input'}}
We can directly give the `Document` as input to automation but this serialize the whole document. This has impact on performance regarding usage of `DocRef`.
{{/callout}}

We can also do:
```php
$file = $client->operation("Document.Update")
    ->param("properties", "dc:nature=card")
    ->input(new DocRef("/file001"))
    ->execute(Document::class);
```

## Blob.Attach

Now that the document exists, we can attach a blob to our it:
```php
$client->operation("Blob.Attach")
    ->param("document", "/file001")
    ->param("xpath", "file:content")
    ->input(Blob::fromFile('/tmp/myFile', 'plain/text'))
    ->voidOperation(true) // allows to not download blob in response
    ->execute();
```

## Blob.Get

Let's download our blob:
```php
$client->operation("Blob.Get")
    ->param("xpath", "file:content")
    ->input(new DocRef("/file001"))
    ->execute(Blob::class);
```
