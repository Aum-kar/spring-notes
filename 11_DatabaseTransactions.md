# Transactions & Dirty Checking

## What does `@Transactional` do?

`@Transactional` tells Spring to execute a method inside a database transaction.

When a method annotated with `@Transactional` starts:

* A transaction is opened.
* All database operations inside that method run within the same transaction.
* If everything succeeds → the transaction is committed.
* If a runtime exception occurs → the transaction is rolled back.

This ensures atomicity:

Either all changes succeed, or none of them are saved.

---

### When does SQL execute?

SQL execution does not always happen immediately when you modify an entity.

Inside a transaction:

1. When you call `repository.findById()`, Hibernate executes a SELECT query.
2. When you modify a managed entity (e.g., `product.setStockQuantity(...)`), Hibernate does not immediately execute UPDATE.
3. The UPDATE SQL is generated and executed during flush (usually just before commit).

So the typical sequence is:

* SELECT executes when fetching data.
* UPDATE/INSERT/DELETE executes during flush.
* Commit finalizes the transaction.

---

### Why `save()` is not needed for a managed entity?

When an entity is fetched using `findById()` inside a transaction, it becomes a managed entity.

Managed entity means:

* Hibernate tracks its state.
* Any changes to its fields are monitored.

This mechanism is called dirty checking.

If you modify a field:

```java
product.setStockQuantity(20);
```

Hibernate detects that the entity state has changed compared to the original snapshot.

During flush, Hibernate automatically generates the required UPDATE SQL.

Therefore, calling `save()` is not required for an already managed entity.

`save()` is mainly needed when:

* You are creating a new entity.
* You are working with a detached entity.

---

### What is flush?

Flush is the process where Hibernate synchronizes the in-memory state of managed entities with the database.

During flush:

* Hibernate compares current entity state with its original state.
* Detects changes (dirty checking).
* Generates SQL statements (INSERT, UPDATE, DELETE).
* Executes them against the database.

Flush does not end the transaction.

It only pushes changes to the database within the ongoing transaction.

Flush typically happens:

* Automatically before transaction commit.
* Before certain queries.
* Or manually if `entityManager.flush()` is called.

---

### What is commit?

Commit is the final step of a transaction.

When commit happens:

* All flushed SQL changes become permanent in the database.
* The transaction is closed.
* Database guarantees durability.

If an exception occurs before commit and rollback is triggered:

* All changes made during the transaction are undone.
* The database remains unchanged.

---

### Summary

* `@Transactional` creates a transaction boundary.
* SELECT runs when fetching data.
* Changes to managed entities are tracked automatically.
* Flush generates and executes SQL.
* Commit finalizes and permanently saves changes.
* Dirty checking removes the need to call `save()` for managed entities.
