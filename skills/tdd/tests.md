# Good and Bad Tests

## Good Tests

Prefer real collaborators and interfaces used by callers; choose focused or integration tests according to the behavior and risk.

```typescript
// GOOD: Tests observable behavior
test("user can checkout with valid cart", async () => {
  const cart = createCart();
  cart.add(product);
  const result = await checkout(cart, paymentMethod);
  expect(result.status).toBe("confirmed");
});
```

Characteristics:

- Tests behavior users/callers care about
- Uses a stable interface with a meaningful contract
- Survives internal refactors
- Describes WHAT, not HOW
- One logical assertion per test

## Bad Tests

**Implementation-detail tests**: Coupled to internal structure.

```typescript
// BAD: Tests implementation details
test("checkout calls paymentService.process", async () => {
  const mockPayment = jest.mock(paymentService);
  await checkout(cart, payment);
  expect(mockPayment.process).toHaveBeenCalledWith(cart.total);
});
```

Review these as signs of possible coupling, rather than automatic failures:

- Mocking internal collaborators
- Testing private methods
- Asserting on call counts/order that the contract does not require
- Test breaks when refactoring without behavior change
- Test name describes HOW not WHAT
- Inspecting storage details when persistence is not the behavior under test

An interaction assertion can be appropriate when the interaction is the observable contract, such as charging an external payment provider once with the correct amount. A stable internal algorithm interface can also be a useful focused test surface without testing private implementation structure.

```typescript
// Weak evidence for caller-visible retrievability: checks storage only
test("createUser saves to database", async () => {
  await createUser({ name: "Alice" });
  const row = await db.query("SELECT * FROM users WHERE name = ?", ["Alice"]);
  expect(row).toBeDefined();
});

// GOOD: Verifies through interface
test("createUser makes user retrievable", async () => {
  const user = await createUser({ name: "Alice" });
  const retrieved = await getUser(user.id);
  expect(retrieved.name).toBe("Alice");
});
```

For a persistence contract, direct database evidence can be necessary: verify a transaction rollback leaves no partial rows, for example. A read through the same cache or in-memory object may miss that defect. Choose evidence that can distinguish correct behavior from the relevant failure.

**Tautological tests**: Expected value restates the implementation, so the test passes by construction.

```typescript
// BAD: Expected value is recomputed the way the code computes it
test("calculateTotal sums line items", () => {
  const items = [{ price: 10 }, { price: 5 }];
  const expected = items.reduce((sum, i) => sum + i.price, 0);
  expect(calculateTotal(items)).toBe(expected);
});

// GOOD: Expected value is an independent, known literal
test("calculateTotal sums line items", () => {
  expect(calculateTotal([{ price: 10 }, { price: 5 }])).toBe(15);
});
```
