# Helpdesk Schema Repair

This project implements the Helpdesk database schema using PostgreSQL based on the provided Entity Relationship Diagram (ERD).

## ERD-to-Schema Decisions

### Decision 1: UUID instead of SERIAL

ERD said: All entity IDs should use UUID as their primary key.

Schema decided: Used `UUID PRIMARY KEY DEFAULT gen_random_uuid()` for all six tables.

Reason: UUIDs provide unique identifiers without depending on sequential integer IDs.

### Decision 2: ON DELETE behaviour

ERD said: Relationships between entities must be maintained through foreign keys.

Schema decided: Used `RESTRICT`, `CASCADE`, and `SET NULL` depending on the relationship.

Reason: This protects important audit records, removes dependent records when appropriate, and preserves tickets when an assigned agent is deleted.

### Decision 3: Default values

ERD said: Created timestamps should record when a record was created, and ticket status should default to open.

Schema decided: Used `DEFAULT NOW()` for creation timestamps and `DEFAULT 'open'` for ticket status.

Reason: This automatically provides creation timestamps and ensures new tickets start with the correct status without requiring explicit values.

## Rejected Table Shape

### Shape: Tags stored as an array in tickets

What it was: The original schema stored tags in a `TEXT[]` column inside the tickets table.

Why rejected: The ERD represents tags as a separate entity with a many-to-many relationship between tickets and tags.

What would break: Storing tags as an array makes it difficult to maintain tag details, enforce foreign keys, prevent invalid references, and manage tags independently.

The corrected schema uses separate `tags` and `ticket_tags` tables to implement the relationship properly.