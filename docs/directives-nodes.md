---
id: directives-nodes
title: Nodes
---

Node directives can be placed on Object/Input Type nodes.

<br />
[**@group** directive](#group) Ad a namespace and/or middleware.<br /><br />
[**@interface** directive](#interface) Used for defining Interfaces.<br />
<br />

## @group

The `@group` directive can be used to set a namespace and/or middleware on a set of fields.

```graphql
type User @group(namespace: "App\\Http\\GraphQL\\Types") {
  email: String @field(resolver: "UserType@email")
}

type Mutation @group(middleware: ["api:auth"]) {
  createPost(title: String!): Post
}
```

## @interface

The `@interface` directive can be used when defining Interfaces in your schema. It requires a `resolver` argument so Lighthouse knows how to resolve the interface.

_A common use-case for interfaces with a Laravel project would be polymorphic relationships._

```graphql
interface Commentable
  @interface(
    resolver: "App\\Http\\GraphQL\\Interfaces\\Commentable@resolveType"
  ) {
  id: ID!
}

type Post implements Commentable {
  id: ID!
  # ...
}

type Video implements Commentable {
  id: ID!
  # ...
}

type Comment {
  # ...

  # resolves to either a Video or Post type
  commentable: Commentable
}
```

```php
class Commentable
{
    public function resolveType($value)
    {
        if ($value instanceof \App\Post) {
            return schema()->instance('Post');
        } else if ($value instanceof \App\Video) {
          return schema()->instance('Video');
        }

        return null;
    }
}
```
