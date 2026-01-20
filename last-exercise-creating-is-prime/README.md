Exercise: Creating a PostgreSQL Patch - is_prime() Function
=============================================================

This is the final capstone exercise. You will create an `is_prime()`
builtin function directly in PostgreSQL core (not as an extension),
write comprehensive regression tests for it, and generate a patch file.

This is a complete exercise covering the full cycle: implementation in
C, function registration, SQL testing, and patch creation.

Steps:

1. Understand the PostgreSQL source structure. Start by exploring where
   builtin functions are implemented:

   - Look at `src/backend/utils/adt/int.c` for simple function examples
   - Check `src/include/catalog/pg_proc.dat` for function registration patterns
   - Review how existing integer/boolean functions are organized

   What naming conventions do they follow? How are functions registered?

2. Design the is_prime() function. Plan your implementation:

   - Input: integer (int4)
   - Output: boolean
   - Function name: `is_prime`
   - Algorithm: Handle edge cases (< 2 are not prime), use trial division
   - Follow PostgreSQL C conventions (PG_FUNCTION_INFO_V1, NULL handling, etc.)

   Where in the source tree should you add this function? Should you
   modify an existing file like `int.c` or create a new utility file?

3. Implement the is_prime() C function. Find an appropriate location
   and add your implementation:

   - Use proper PostgreSQL API (PG_GETARG_INT32, PG_RETURN_BOOL, etc.)
   - Include memory management using PostgreSQL allocators
   - Add appropriate comments and error handling
   - Follow the coding style of surrounding code

   Look at similar functions in `int.c` as templates.

4. Register the function in pg_proc.dat. Add an entry for your function:

   - Find the correct section and format (study existing int/bool functions)
   - Specify name, return type (bool), argument type (int4)
   - Link to your C function implementation

   What are the required fields? How do other similar functions look?

5. Write comprehensive regression tests. Create test files following Exercise 3:

   - Create `src/test/regress/sql/is_prime.sql` with test queries
   - Create `src/test/regress/expected/is_prime.out` with expected output
   - Test coverage should include:
     - Edge cases: 0, 1, 2, negative numbers
     - Couple of small and large values
   - Edit `src/test/regress/parallel_schedule` file.

   Review how tests are structured from Exercise 3.

6. Build and test your changes:

   ```
   cd /path/to/postgres/source
   ./configure
   make
   make check
   ```

   Ensure all tests pass, including your new regression tests.

7. Generate the patch file. Use git to create a clean patch:

   ```
   git add [your modified files]
   git commit -m "Commit message"

   git format-patch -1 HEAD
   ```

   Or use git diff to compare against a base:

   ```
   git diff origin/master > is_prime.patch
   ```

   What files should be included in the patch? Review it to ensure
   it contains only necessary changes.

