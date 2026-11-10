# Test shapes that cannot fail

Open this file when you write a test, or when you review a test and doubt its assertion. `SKILL.md` states the rule. This file lists the five shapes that break the rule, the assertions to look for in each language, and the fix.

Ask two questions of a test. Would it still pass if the code under test did nothing, meaning it returned `null`, an empty list, or a default, or never ran? Does the assertion compare the result with a literal value that the test wrote down? Each shape below fails one of the two.

## The five shapes

### Weak or no assertion

The test asserts only that a value exists, that a collection has some size, or that nothing threw.

- Kotlin: `assertNotNull(result)`, `assertTrue(results.isNotEmpty())`, an `awaitItem()` whose value the test never reads.
- Swift: `XCTAssertNotNil(route)`, `XCTAssertTrue(routes.count > 0)`, `XCTAssertNoThrow(...)`.
- TypeScript: `toBeDefined()`, `toBeTruthy()`, `not.toThrow()`, `toBeGreaterThan(0)`.
- C#: `Assert.IsNotNull(response)`, `Assert.IsTrue(items.Count > 0)`.

### Call or absence only

The test asserts only that a collaborator was called, or that something is empty, null, or absent. An absence is also what you get when nothing ran. A call shows that the code reached a collaborator, not that the caller got the right result.

- Kotlin: `assertNull(error)`, `assertTrue(results.isEmpty())`, a check that a fake recorded one call.
- Swift: `XCTAssertNil(error)`, `XCTAssertTrue(routes.isEmpty)`.
- TypeScript: `toHaveBeenCalled()`, `not.toHaveBeenCalled()`, `toBeUndefined()`, `toEqual([])`, `toHaveLength(0)`.
- C#: `Assert.IsNull(error)`, `Assert.AreEqual(0, items.Count)`.

### Self-referential

The expected value comes from the code under test, so both sides change together.

- `assertEquals(format(price), format(price))`
- `expect(parsed.url).toBe(buildUrl(input))`

### Constant pin

The assertion restates a constant, a config default, or a row of a lookup table. The test fails only when someone edits the constant, so it blocks that edit and catches no defect.

- `assertEquals(30, Config.TIMEOUT_SECONDS)`
- `expect(LIMITS.maxResults).toBe(50)`

### Fixture asserts fixture

The assertion reads data that the test built, or a value that the setup computed. The code under test never runs inside the test body.

## The fix

Call the code under test inside the test body with one concrete input. Assert the literal output, or the state after the call. For example, with Turbine:

```kotlin
state.test {
	load(userId)

	assertEquals(LoadState.Loading, awaitItem())
	assertEquals(LoadState.Success(expectedUser), awaitItem())
}
```

Apply the same fix to each shape:

- For an absence, assert the presence for a second input in the same test.
- For a call, assert the payload that the fake received, or the state after the call.
- For a constant, test the code that reads the constant, with one input.
- When no such assertion exists, delete the test.

## Tests to keep

These tests can look like the shapes above, but they check real output:

- A dbt data test such as `unique`, `not_null`, or `relationships`. It runs against the rows that the model produced.
- A Roborazzi screenshot test. It compares the rendered screen with a recorded image.
