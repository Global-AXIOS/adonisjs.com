---
permalink: guides/validator/rules
group: Validator
---

# Validation Rules
The validation rules allows you to validate the formatting and integrity of data. The validation rules are standalone can be applied on any schema type (with some exceptions).

1. First, you need to import the `rules` list.
  ```ts
  import { rules, schema } from '@ioc:Adonis/Core/Validator'
  ```
2. Next, you can define one or more rules on a given field.
  ```ts
  schema.create({
    email: schema.string({}, [
      rules.email(),
      rules.unique({ table: 'users', column: 'email' })
    ])
  })
  ```

## `rules.alpha`
Enforces the value to only have letters. Optionally, you can also allow `spaces`, `dash` and `underscore` characters.

[note]
Can only be used with the string data type. Otherwise, an exception is raised
[/note]

```ts
{
  username: schema.string({}, [
    rules.alpha(),
  ])
}

// or
rules.alpha({
  allow: ['space', 'underscore', 'dash']
})
```

## `rules.confirmed`
Enforce the field under validation is also confirmed using the `_confirmation` convention. You will mostly this rule for password confirmation.

```ts
{
  password: schema.string({}, [
    rules.confirmed()
  ])
}

// Valid data: { password: 'secret', password_confirmation: 'secret' }
```

## `rules.email`
Enforces the value to be properly formatted as an email. 

```ts
{
  email: schema.string({}, [
    rules.email()
  ])
}
```

You can also define the following options to control the validation behavior.

| Option | Default Value | Description |
|--------|----------------|-------------|
| allowIpDomain | `false` | Set it as `true` to allow IP addresses in the host part. |
| ignoreMaxLength | `false` | Set it as `true` to disable email address max length validation. |
| domainSpecificValidation | `false` | Set it as `true` to enable dis-allow certain syntactically valid email addresses that are rejected by **GMail** |
| sanitize | `false` | Not a validation option, but instead can be used to transform the local part of the email (before the @ symbol) to all lowercase. |

```ts
{
  email: schema.string({}, [
    rules.email({
      sanitize: true,
      ignoreMaxLength: true,
    })
  ])
}
```

## `rules.exists`
Enforces the value to exist in a database table.

[note]
The validation rule is added by `@adonisjs/lucid` package. So make sure it is [installed and configured](/guides/database/setup), before using this rule.
[/note]

```ts
{
  category_id: schema.number([
    rules.exists({ column: 'categories', column: 'id' })
  ])
}
```

Additionally, you can also defined `where` and `whereNot` constraints. For example: Limit the database query to a specific tenant id.

```ts
{
  category_id: schema.number([
    rules.exists({
      column: 'categories',
      column: 'id',
      where: { tenant_id: 1 },
    })
  ])
}
```

## `rules.ip`
Enforce the field under validation is a valid ip address. Optionally, you can also specify the `ipv` version.

```ts
{
  ip: schema.string({}, [
    rules.ip()
  ])
}

// or
rules.ip({
  version: '6',
})
```

## `rules.maxLength`
Limit the max length of a `string` or an `array`.

```ts
{
  username: schema.string({}, [
    rules.maxLength(40)
  ])
}
```

You can also apply the `maxLength` rule on an array.

```ts
{
  tags: schema
    .array([
      rules.maxLength(10)
    ])
    .members(schema.string())
}
```

## `rules.minLength`
Enforce minimum length on a string or an array.

```ts
{
  tags: schema.array([
    rules.minLength(1)
  ])
}
```

## `rules.unsigned`
Ensure, the value is an [unsigned integer](https://www.cs.utah.edu/~germain/PPS/Topics/unsigned_integer.html)

```ts
{
  age: schema.number([
    rules.unsigned()
  ])
}
```

## `rules.regex`
Define a custom regex to validate the value against. 

```ts
{
  username: schema.string([
    rules.regex(/^[a-zA-Z0-9]+$/)
  ])
}
```

## `rules.uuid`
Enforce the value of field under validation is a valid `uuid`. You can also optionally enforce a uuid version.

```ts
{
  id: schema.string([
    rules.uuid()
  ])
}

// Or enforce version
{
  id: schema.string([
    rules.uuid({ version: 4 })
  ])
}
```

## `rules.mobile`
Enforces the value to be properly formatted as a phone number. You can also define locales for country specific validation.

```ts
{
  mobile: schema.string([
    rules.mobile()
  ])
}
```

Validate against selected locales

```ts
{
  mobile: schema.string([
    rules.mobile({ locales: ['pt-BR', 'en-IN', 'en-US'] })
  ])
}
```

Also, you can enable **strict mode**, which will force the end user to specify the country code along with the `+` prefix.

```ts
{
  mobile: schema.string([
    rules.mobile({ strict: true })
  ])
}
```

## `rules.requiredIfExists`
Mark the current field as required, when **another field exists** and contains some value.

```ts
{
  password: schema.string.optional({}, [
    rules.requiredIfExists('username')
  ])
}
```

## `rules.requiredIfExistsAll`
Mark the current field as required, when **all of the other fields exists** and contains some value.

```ts
{
  password: schema.string.optional({}, [
    rules.requiredIfExistsAll(['username', 'email'])
  ])
}
```

## `rules.requiredIfExistsAny`
Mark the current field as required, when **any of the other fields exists** and contains some value.

```ts
{
  password: schema.string.optional({}, [
    rules.requiredIfExistsAny(['username', 'email'])
  ])
}
```

## `rules.requiredIfNotExists`
The opposite of [rules.requiredIfExists](#rulesrequiredifexists)

## `rules.requiredIfNotExistsAll`
The opposite of [rules.requiredIfExistsAll](#rulesrequiredifexistsall)

## `rules.requiredIfNotExistsAny`
The opposite of [rules.requiredIfExistsAny](#rulesrequiredifexistsany)

## `rules.requiredWhen`
Mark the current field as required, when the value of the other field matches a given criteria.

```ts
{
  address: schema.string.optional({}, [
    rules.requiredWhen('delivery_method', '=', 'shipping')
  ])
}
```

The `requiredWhen` rule support the following operators.

- `in`
- `notIn`
- `=`
- `!=`
- `>`
- `<`
- `>=`
- `<=`