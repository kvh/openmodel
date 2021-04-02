![schemas](https://github.com/kvh/schemas/workflows/schemas-python/badge.svg)

# Semantic Schemas

# Common Schema

Semantic Schemas are a universal format for specifying the structure and semantics of data, record, and object types. Think supercharged "CREATE TABLE" statement or JSON spec. The goal of Semantic Schemas is to provide a universal library of common Schemas that tools, libraries, researchers, analysts, databases, and APIs can use to communicate data frictionlessly. Semantic Schemas are a data protocol for the next 70,000 years.

The Semantic Schema Repository has many common schemas like:

- Country
- Currency
- Date
- Transaction
- Customer
- Address
- PhoneNumber
- EndOfDayPrice

and popular third-party ones like:

- StripeCharge
- FredObservation
- ShopifyOrder
- WorldBankCountryIndicator
- MailchimpMember
- SalesforceCustomer

Semantic Schemas provide a single place to describe the properties of an abstract object, its attributes and their types, its relation to other objects, and provide documentation of the meaning and details of each. A basic Schema looks like this:

```yaml
name: Transaction
version: 0.1.0
description: |
  Represents any commercial transaction of an amount at a given time, optionally
  specifying the buyer, seller, currency, and item transacted.
immutable: true
unique_on:
  - id
fields:
  id: Text NotNull
  amount: Decimal(16,2) NotNull
  transacted_at: DateTime NotNull
  buyer_id: Text
  seller_id: Text
  item_id: Text
  currency_code: Text
  metadata: Json
relations:
  Currency:
    fields:
      code: currency_code
implementations:
  TimeSeries:
    time: transacted_at
    value: amount

documentation:
  schema: |
    A Transaction is meant to be the broadest, most base definition
    for all commercial transactions involving a buyer and a seller or a sender
    and receiver, whether that's an ecommerce order, a ACH transfer, or a real
    estate sale.
  fields:
    id: |
      Unique identifier for this transaction, required so that transactions can
      be deduped accurately. If data does not have a unique identifier, either
      create one, or use a more basic schema like `common.TimeSeries`.
```

Toml reads better imo, but isn't designed to handle nested dictionaries cleanly:

```toml
name = "Transaction"
version = 1
description = '''
  Represents a transaction of an amount at a given time, optionally
  specifying the buyer, seller, currency, and item transacted as well.
  '''
immutable = true
unique_on = ["id"]

[fields]
id = "Text NotNull"
  [fields.amount]
  type = "Decimal(16,2)"
  validators = ["NotNull"]
  description = "Amount of the transaction"
transacted_at = "DateTime NotNull"
buyer_id = "Text"
seller_id = "Text"

[relations.Currency.fields]
code = "currency_code"

[implementations.TimeSeries]
time = "transacted_at"
value = "amount"

[documentation]
schema = '''
    A Transaction is meant to be the broadest, most base definition
    for all commercial transactions involving a buyer and a seller or a sender
    and receiver, whether that's an ecommerce order, a ACH transfer, or a real
    estate sale.
'''

[documentation.fields]
id = '''
      Unique identifier for this transaction, required so that transactions can
      be deduped accurately. If data does not have a unique identifier, either
      create one, or use a more basic schema like `common.TimeSeries`.
'''
```

## Versioning

Schemas follow semantic versioning conventions, meaning that breaking (backwards
incompatible) changes require a major version bump, new backwards
compatible features require a minor version bump, and bug fixes can be a patch
version bump.

Examples of backwards **incompatible** changes requiring major version bump:

- Add a new NotNull field
- Change an existing field type to a more restrictive type (Float -> Integer)
- Rename a field
- Change unique fields
- Remove or change relations or implementations
- Make immutable

Examples of backwards **compatible** changes requiring minor version bump:

- Add a new nullable field
- Change an existing field type to a less restrictive type (Text -> LongText, Integer -> Decimal)
- Change the semantic meaning of a field or schema
- Add new relations or implementations

Examples of fixes requiring a patch version bump:

- Edit the documentation or description
- Fix typo or other bug
