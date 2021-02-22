---
title: Advanced Usage
description: How to extend Nuxeo PHP Client
review:
    comment: ''
    date: '2021-02-22'
    status: ok
labels:
    - php-client
tree_item_index: 900
section_parent: php
toc: true
---

If you ever need to fetch a custom entity from Nuxeo as a PHP object, you can easily do it.

First, create your custom entity by extending `NuxeoEntity`:

```php
namespace Acme\Objects;

use Nuxeo\Client\Spi\Objects\NuxeoEntity;

class MyDocType extends NuxeoEntity {

  /**
   * @var string
   * @Serializer\Type("string")
   */
  private $customField;

  /**
   * Document constructor.
   * @param NuxeoClient $nuxeoClient
   */
  public function __construct($nuxeoClient = null) {
    parent::__construct('custom-json-object', $nuxeoClient);
  }

  /**
   * @return string
   */
  public function getCustomField() {
    return $this->customField;
  }

  /**
   * @param string $customField
   * @return self
   */
  public function setCustomField($customField) {
    $this->customField = $customField;
    return $this;
  }

}
```

Now we can call an operation returning this entity and get a proper PHP object:

```php
use Acme\MyDocType;

$document = $client->repository()
    ->fetchDocumentById('3dd87292-1345-4ad8-acb5-4e74c26bf893', null, MyDocType::class);
```

Note: it would be more interesting to extend `Document`, since your entity will probably inherit from the base Nuxeo document type.
