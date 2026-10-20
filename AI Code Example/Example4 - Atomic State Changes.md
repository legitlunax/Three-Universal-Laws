# Example 4: Atomic State Changes

A safe operation should not leave data half-updated if something goes wrong. A bank transfer is a clear example: subtracting money from one account and adding it to another must succeed as one unit, or not happen at all.

Use integer cents rather than floating-point dollars so the amount is exact.

```python
import sqlite3


def transfer(
    connection: sqlite3.Connection,
    source_id: int,
    destination_id: int,
    amount_cents: int,
) -> None:
    for name, value in (
        ("source_id", source_id),
        ("destination_id", destination_id),
        ("amount_cents", amount_cents),
    ):
        if isinstance(value, bool) or not isinstance(value, int) or value <= 0:
            raise ValueError(f"{name} must be a positive integer")

    if source_id == destination_id:
        raise ValueError("source and destination must be different accounts")

    with connection:
        debit = connection.execute(
            """
            UPDATE accounts
            SET balance_cents = balance_cents - ?
            WHERE id = ? AND balance_cents >= ?
            """,
            (amount_cents, source_id, amount_cents),
        )
        if debit.rowcount != 1:
            raise ValueError("source account not found or has insufficient funds")

        credit = connection.execute(
            """
            UPDATE accounts
            SET balance_cents = balance_cents + ?
            WHERE id = ?
            """,
            (amount_cents, destination_id),
        )
        if credit.rowcount != 1:
            raise ValueError("destination account not found")
```

This assumes `accounts.id` is unique and `balance_cents` is an integer column with a nonnegative constraint. The connection context manager commits when the block succeeds and rolls back when an exception escapes it. The caller should handle the documented `ValueError` cases.

## 1. Chaos: Inputs and Changing State

The operation faces different possible conditions: invalid IDs, a missing account, insufficient funds, or an unexpected database failure. Account state may also be changing as other operations run.

Chaos here is the range of real conditions the code must safely handle, not a reason to silently ignore errors.

## 2. Order: Validation and Transaction

The function validates its inputs and then performs two parameterized updates inside one database transaction. The debit checks the balance in the update itself, rather than reading a balance first and later making an unguarded subtraction.

If the credit fails, its exception causes the transaction to roll back the debit too. Both changes become durable together, or neither does.

## 3. Singularity: One Committed Outcome

The operation has one focused result: the transfer is committed, or it is not. It does not expose a partially completed transfer as a successful outcome.

## The Pattern

- **Chaos:** inputs, account state, and failures can vary.
- **Order:** validation, guarded updates, and a transaction constrain the operation.
- **Singularity:** one complete transfer—or a rollback.

The transaction provides atomicity, not every financial-system guarantee. Production systems also need suitable authorization, audit records, database configuration, concurrency design, and idempotency for safely retrying requests.
