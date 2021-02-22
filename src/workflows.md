---
title: Workflows
description: Handle workflows
review:
    comment: ''
    date: '2019-06-18'
    status: ok
labels:
    - php-client
tree_item_index: 700
section_parent: php
toc: true
---

This section is about handling workflows and tasks within Nuxeo PHP Client.

Let's first retrieve the workflows manager:

```php
$workflowManager = $client->workflows();
```

## Model Retrieval

Let's retrieve all workflow models on Nuxeo Server:

```php
use Nuxeo\Client\Objects\Workflow\Workflows;

/** @var Workflows $workflows */
$workflows = $workflowManager->fetchModels();
```

Next examples will use the `SerialDocumentReview` workflow which is present on a bare Nuxeo Server:

## Start

Let's start serial workflow on a note:

```php
$note = $client->repository()->fetchDocumentByPath('/note');

// The next statements are equivalent
$workflow = $note->startWorkflow('SerialDocumentReview');
$workflow = $workflowManager->startWorkflowByNameWithDocId('SerialDocumentReview', $note->getUid());
```

## Fetch

Let's retrieve the tasks of a document:

```php
// The next statements are equivalent
$tasks = $note->fetchTasks();
$tasks = $workflowManager->fetchTasksByDocumentId($note->getUid());
```

## Update - Work with Task

We can add our review of a running workflow on a document:

```php
$tasks = $note->fetchTasks();
$completionRequest = (new TaskCompletionRequest($task))->setVariables([
      'comment' => 'LGTM',
      'participants' => ['user:Administrator'],
      'validationOrReview' => 'simpleReview'
    ]);

$tasks[0]->complete('approve', $completionRequest);
```
