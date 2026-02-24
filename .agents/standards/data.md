# Data Standards

## DTO or API Contract Standards

- Always specify explicit types for each field (string, number, boolean, array, object)
- Document each field with purpose and constraints (required, optional, allowed values)
- Avoid ambiguous field names; use descriptive, domain specific terms
- Use consistent naming and structure across all endpoints
- Prefer camelCase since most of this is going to be in JSON format
- Include versioning in the contract (e.g., `v1`, `v2`) for backward compatibility
- Use enums for fields with limited allowed values
- Validate input and output data against the contract (schema validation)
- Keep DTOs flat where possible; avoid deeply nested structures unless necessary
- Separate DTOs for request and response if they differ
- Include error objects in response contracts (standardised error format)
- Prefer ISO 8601 for date/time fields

## Entities or Domain Model Standards

- Always specify explicit types for each property
- Document each property with its purpose and constraints
- Use language specific naming conventions:
  - infrastructure: snake_case
  - python: snake_case
  - javascript: camelCase
  - go: camelCase
  - c#: PascalCase
  - Java: PascalCase
- Prefer immutability for value objects and entities where possible
- Validate entity invariants in constructors or factory methods
- Model relationships explicitly (one to one, one to many, many to many)
- Avoid exposing internal state directly; use methods for business logic
- Separate domain logic from persistence and infrastructure concerns
- Use enums for properties with limited allowed values
- Prefer composition over inheritance for shared behaviour
- Keep entities focused; avoid bloated models with unrelated responsibilities
- Use consistent naming and structure across all domain models

## Database Object Best Practices Standards

- Define primary keys for every table (prefer surrogate keys for simplicity)
- Use foreign keys to enforce relationships and referential integrity
- Apply NOT NULL constraints where possible
- Use unique constraints for fields that must be unique
- Use clear, descriptive table and column names (avoid abbreviations)
- Prefer snake_case for table and column names
- Use appropriate data types for each column (avoid generic types)
- Normalise data to reduce redundancy (up to 3rd normal form unless justified)
- Secure sensitive data (encrypt at rest, restrict access)
- Avoid storing passwords in plain text (use strong hashing)
- Add indexes for frequently queried columns (but avoid over indexing)
- Optimise for query performance (analyse query plans, avoid N+1 queries)
- Use migrations/versioning for schema changes
- Document each table and column with purpose and constraints
- Use default values for columns where appropriate
- Prefer explicit relationships over implicit (junction tables for many to many)
- Avoid storing calculated or derived data unless necessary for performance
