# Simple Schema

## Introduction

Data quality is fundamental to any successful system. Poor data quality leads to incorrect business decisions, operational inefficiencies, and user frustration. A data dictionary serves as a single source of truth, documenting what data exists, what it means, and how it should be used. By capturing these definitions in a schema, we ensure consistency, enable validation, and provide clear documentation for all stakeholders.

The primary design specification captured throughout all design exercises is a data dictionary that describes all of the data created or consumed by the system. These data structures are documented using a Simple Schema standard.

Simple Schema uses a json-schema like data structure, but leverages a custom `type` structure that makes them more accessible to design stakeholders who do not have a software engineering background.

## Getting Started

### What is Simple Schema?
Simple Schema is a user-friendly way to define data structures. It uses a json-schema like data structure but with custom types that make it more accessible to non-technical stakeholders.

### Key Concepts
- **Data Dictionary**: A collection of data definitions
- **Schema**: A formal definition of data structure
- **Type**: A way to validate and describe data
- **Property**: A piece of data within a structure

## Data Types

At the core of Simple Schema is the idea of a "Custom Type" that allows data to be described in human terms. The simplest property in a data dictionary has a description, and a type that describes how the value described should be validated. For example:

```yaml
home_phone:
    description: A user's home phone number
    type: phone
```

You may have noticed that we never defined exactly what a phone number is. When we are capturing the dictionaries we simply use `one_word` type names that make sense to a human. Later in the design process we will define that type using the technically terse json schema or bson schema standards. Simple Schema isolates these specifications into custom type definitions.

### Custom Types
Custom types are defined in the `dictionaries/types` folder. These types can be simple (like `word` or `sentence`) or complex (like objects and arrays), but they all ultimately resolve to primitive types. This layering allows us to build rich, domain-specific types while maintaining a clear connection to basic data structures.

Any system is free to define its own custom types. However, Stage Zero has identified a number of common custom types that are useful in describing data:

- [word]() A string of up to 32 characters, no special characters like new-lines, tabs, or escaped code, no whitespace
- [sentence]() A string of up to 255 printable characters, no special characters like new-lines, tabs, or escaped code. Whitespace is allowed
- [paragraph]() An array of sentences
- [count]() A non-negative integer 
- [currency]() A USD Currency Value 
- [identity]() A system generated Unique Identifier
- [breadcrumb](./specifications/dictionaries/types/breadcrumb.yaml) Data used to track an event (when, who, where)

### Primitives

Primitive types are the basic building blocks of Simple Schema. All custom types eventually resolve to a primitive type. Primitive types provide the specific json schema, or bson schema used for that type. For example:

```yaml
title: Identity
description: A system generated unique identifier
json_type:
  type: string
  pattern: "^[0-9a-fA-F]{24}$"
bson_type:
  bsonType: objectId
```

If the only difference between the json schema and bson schema is the name of the type property (`type` vs. `bsonType`), you can use the `schema` form of a primitive:

```yaml
title: Word
description: A String of text, at least 4 and no more than 40 characters with no spaces, or special characters like /t or /n
schema:
  type: string
  pattern: "^[a-zA-Z0-9_-]{4,40}$"
```

## Complex Types

### Object Types
Object types allow you to group related properties together. Each property within an object can have its own type, description, and validation rules.

Required Properties:
- `type`: Must be "object"
- `properties`: Map of property names to their definitions

Optional Properties:
- `additionalProperties`: Boolean, defaults to false
- `required`: Boolean, defaults to false

Example:
```yaml
address:
    description: A physical address
    type: object
    properties:
        street:
            description: Street address
            type: sentence
            required: true
        city:
            description: City name
            type: word
            required: true
        state:
            description: State or province
            type: state_code
            required: true
        postal_code:
            description: Postal code
            type: us_zip
            required: true
    additionalProperties: true
    required: true
```

### Array Types
Array types represent lists of values. The `items` property defines the structure of each element in the array.

Required Properties:
- `type`: Must be "array"
- `items`: Definition of array element structure

Optional Properties:
- `required`: Boolean, defaults to false

Example:
```yaml
phone_numbers:
    description: List of contact phone numbers
    type: array
    items:
        description: A phone number
        type: phone
        required: true
```

## Enumerated Types

Enumerated values are the gold standard for data quality constraints. By restricting a field to a specific set of valid values, we ensure data consistency and eliminate ambiguity. For example, a status field that can only be "draft", "active", or "archived" prevents invalid states and makes the data more reliable for business operations.

Enumerated types are defined in the enumerators dictionary. An enumerator is defined with its enumerated values, and a description for each value. This centralized approach ensures that:

1. Values are consistent across the entire system
2. Each value has a clear, documented meaning
3. Changes to valid values are tracked and versioned
4. Business rules are enforced at the data level

### Enum Types
Required Properties:
- `type`: Must be "enum"
- `enums`: Name of the enumerator to use

Optional Properties:
- `required`: Boolean, defaults to false

Example:
```yaml
status:
    description: The document status
    type: enum
    enums: default_status
    required: true
```

The enumerator definition:
```yaml
default_status:
    draft: A record that is not yet active
    active: An active record
    archived: A record that has been archived
```

### Enum Array Types
Required Properties:
- `type`: Must be "enum_array"
- `enums`: Name of the enumerator to use

Optional Properties:
- `required`: Boolean, defaults to false

Example:
```yaml
tags:
    description: Media tags for content
    type: enum_array
    enums: tags
    required: true
```

Where an `enum` type represents a single value from the list of enumerated values, an `enum_array` type is a list of values from the enumerated list.

### OneOf Types
The `one_of` type describes a polymorphic structure where the object's type is determined by a type indicator property.

Required Properties:
- `type_property`: The name of the property that determines the object's type
- `schemas`: A map of type values to their corresponding schemas

Example:
```yaml
title: search
description: A search index that is used to support a elastic search polymorphic query service
type: object
properties:
  collection_name:
    description: The name of the collection
    type: word
    required: true
  collection_id:
    description: The unique identifier for this source document
    type: identity
    required: true
one_of:
  type_property: collection_name
  schemas:
    media: 
      $ref: media
    organization: 
      $ref: organization
    user: 
      $ref: user
```

This would validate documents like:
```json
[
  {
    "collection_name": "user",
    "collection_id": "507f1f77bcf86cd799439011",
    "user": {
        "first_name": "John",
        "last_name": "Doe",
        "email": "john.doe@example.com"
    }
  },
  {
    "collection_name": "organization",
    "collection_id": "507f1f77bcf86cd799439012",
    "organization": {
        "name": "Acme Corp",
        "industry": "Technology",
        "size": "medium"
    }
  }
]
```

## Schema References

The `$ref` property allows you to reference and reuse schema definitions. This is particularly useful for:
- Breaking complex schemas into manageable pieces
- Reusing common schema definitions
- Creating polymorphic structures

Each `$ref` points to a separate data dictionary file that defines the structure for that type. The referenced schemas must:
- Exist in the dictionary directory
- Not create circular references

## Best Practices

1. **Naming Conventions**
   - Use clear, descriptive names
   - Use snake_case for names with multiple words
   - Avoid abbreviations unless well-known

2. **Documentation**
   - Always include descriptions
   - Use business-friendly language

3. **Structure**
   - Keep schemas modular
   - Use references for reuse
   - Avoid deep nesting

4. **Validation**
   - Use enumerated values where possible
   - Mark required fields explicitly
 2 o'clock   - Include appropriate type constraints

## Common Pitfalls

1. **Circular References**
   - Avoid referencing schemas that reference each other
   - Use intermediate schemas if needed

2. **Over-complexity**
   - Keep schemas as simple as possible
   - Break complex schemas into smaller pieces
   - Use references for reuse

## Glossary

- **Schema**: A formal definition of data structure
- **Type**: A way to validate and describe data
- **Property**: A piece of data within a structure
- **Enum**: A set of predefined valid values
- **Reference**: A way to reuse schema definitions
- **Validation**: The process of checking data against a schema
