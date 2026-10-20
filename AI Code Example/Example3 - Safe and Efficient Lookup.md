# Example 3: Safe and Efficient Data Lookup

Efficiency is not only about doing work quickly. Reliable code also avoids unnecessary work, limits what it can affect, and handles unsafe or unexpected inputs deliberately.

Consider looking up a note by its numeric database ID. A primary-key lookup is efficient, and a parameterized query avoids treating user input as SQL code.

```python
def get_note(connection, note_id: int):
    if isinstance(note_id, bool) or not isinstance(note_id, int) or note_id <= 0:
        raise ValueError("note_id must be a positive integer")

    return connection.execute(
        "SELECT id, title, body FROM notes WHERE id = ?",
        (note_id,),
    ).fetchone()
```

The example assumes `notes.id` is a primary key and that the database connection is configured by trusted application code.

## 1. Chaos: Untrusted Possibilities

At the system boundary, an identifier may be missing, malformed, negative, or supplied in an unexpected type. The database may also contain no matching row.

Chaos here means the range of conditions the program may encounter. It is not an excuse to accept every input or to treat every unusual value as an attack.

## 2. Order: Explicit Rules and Safe Operations

The function applies a small set of rules before querying:

- accept only a positive integer, while explicitly rejecting Python's `bool` subtype of `int`
- pass the identifier as a SQL parameter, never concatenate it into the SQL statement
- select only the columns this caller needs
- let invalid input raise a clear error instead of silently turning it into a different request

The primary-key condition lets the database use its key lookup rather than scanning every row. Actual performance still depends on the schema, database, and workload.

## 3. Singularity: One Bounded Outcome

The function returns one matching row or `None`. Its purpose is narrow: retrieve one note by its identity. It does not expose a general-purpose SQL interface or return every record.

The result is focused, but the caller must still handle `None` and must apply authorization separately if users should only access certain notes. Input validation and parameterization do not replace access control.

## The Pattern

- **Chaos:** input and database state can vary.
- **Order:** validation and parameterized, primary-key access constrain the operation.
- **Singularity:** one record, or no record, is returned for one specific lookup.

This design is efficient in more than one sense: it limits the work and data requested, and it reduces risks from malformed input and SQL injection. The laws serve as a design lens; the safety properties come from the concrete validation, query, schema, and authorization choices.
