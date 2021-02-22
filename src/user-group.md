---
title: User & Group
description: Handle users & groups
review:
    comment: ''
    date: '2021-02-22'
    status: ok
labels:
    - php-client
tree_item_index: 500
section_parent: php
toc: true
---

This section is about handling users and groups within Nuxeo PHP Client.

Let's first retrieve user manager:
```php
$userManager = $client->userManager();
```

## Create

APIs below are available to create user/group:
- `createUser` which takes a user
- `createGroup` which takes a group

Let's first create a user:
```php
use Nuxeo\Client\Objects\User\User;

$user = (new User())
  ->setUsername('neo')
  ->setCompany('Nuxeo')
  ->setEmail('devnull@nuxeo.com')
  ->setFirstName('Thomas A.')
  ->setLastName('Anderson')
  ->setPassword('passw0d');
$userManager->createUser($user);
```

Now we want to create a new group:

```php
use Nuxeo\Client\Objects\User\Group;

$group = (new Group())
  ->setGroupName('myGroup')
  ->setGroupLabel('My Group');

Group group = new Group();
group.setGroupName("myGroup");
group.setGroupLabel("My Group");
group.setMemberUsers(Arrays.asList("john"));
group.setParentGroups(Arrays.asList("members"));
group = userManager.createGroup(group);
$userManager->createGroup($group);
```

We can now add Thomas to the group:

```php
$group->addUser($user->getUsername());
```
