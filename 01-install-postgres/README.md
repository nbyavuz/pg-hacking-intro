Exercise: Install PostgreSQL from source
========================================

Before diving into PostgreSQL extension development, we need to set up
a working PostgreSQL installation compiled from source. This gives us
access to development headers and allows us to understand how Postgres
is built.

This exercise walks through cloning the PostgreSQL repository, installing
dependencies, configuring, compiling, running tests, and creating a
database cluster ready for the following exercises.

Steps:

1. Clone the PostgreSQL repository from the official Git mirror:

   ```
   git clone https://github.com/postgres/postgres.git
   cd postgres
   ```

   You can also use the official git.postgresql.org repository:

   ```
   git clone https://git.postgresql.org/git/postgresql.git postgres
   ```

2. Install the required dependencies for building PostgreSQL from source.
   On Ubuntu/Debian systems, run:

   ```
   sudo apt-get update
   sudo apt-get install -y build-essential flex bison pkg-config \
        libreadline-dev zlib1g-dev libicu-dev
   ```

   These packages provide:
   - `build-essential`: GCC compiler and make tools
   - `flex` and `bison`: Parser generation tools
   - `pkg-config`: Helps find installed libraries and their compiler flags
   - `libreadline-dev`: Command-line editing and history in psql
   - `zlib1g-dev`: Compression library for data compression support
   - `libicu-dev`: ICU library for Unicode and internationalization support

3. Configure the build. First, create a directory for your installation
   (we'll use a local directory to avoid requiring root permissions):

   ```
   mkdir -p ~/pg-install
   ./configure --prefix=$HOME/pg-install --enable-debug --enable-cassert
   ```

   Key configure options explained:
   - `--prefix`: Where to install PostgreSQL
   - `--enable-debug`: Include debugging symbols (useful for development)
   - `--enable-cassert`: Enable assertion checks (catches bugs early)

   For more options, run `./configure --help`.

4. Compile PostgreSQL:

   ```
   make -j4
   ```

   After compilation completes, install it:

   ```
   make install
   ```

5. Run the regression tests to verify the build is working correctly:

   ```
   make check
   ```

   This runs the PostgreSQL test suite. All tests should pass. If some
   tests fail, check the `src/test/regress/regression.diffs` file for
   details.

   You can also run the tests in parallel for faster execution:

   ```
   make check -j4
   ```

6. Set up environment variables to use your new installation. Add these
   to your `~/.bashrc` or `~/.profile`:

   ```
   export PATH=$HOME/pg-install/bin:$PATH
   export PGDATA=$HOME/pg-data
   ```

   Then reload your shell configuration:

   ```
   source ~/.bashrc
   ```

7. Create a database cluster (this initializes the data directory):

   ```
   mkdir -p $PGDATA
   initdb -D $PGDATA
   ```

   You should see output confirming the cluster was created successfully.

8. Start the PostgreSQL server:

   ```
   pg_ctl -D $PGDATA -l $PGDATA/logfile start
   ```

   To check the server status:

   ```
   pg_ctl -D $PGDATA status
   ```

9. Create a database and connect to it:

   ```
   createdb mydb
   psql mydb
   ```

   Try running some SQL commands to verify everything works:

   ```
   SELECT version();
   \q
   ```

10. Useful commands for managing your PostgreSQL server:

    ```
    Stop the server:
    pg_ctl -D $PGDATA stop

    Restart the server:
    pg_ctl -D $PGDATA restart

    View server logs:
    tail -f $PGDATA/logfile
    ```

11. Starting from scratch if the database is broken:

    If something goes wrong with your database cluster and you want to
    start fresh, you can safely remove the data directory and reinitialize
    it. This will destroy all your data, so use with caution:

    ```
    Stop the server first:
    pg_ctl -D $PGDATA stop

    Remove the entire data directory:
    rm -rf $PGDATA

    Recreate the directory and initialize a fresh cluster:
    mkdir -p $PGDATA
    initdb -D $PGDATA

    Start the server again:
    pg_ctl -D $PGDATA -l $PGDATA/logfile start
    ```

    After reinitializing, you'll need to recreate your databases:

    ```
    createdb mydb
    ```

You now have a working PostgreSQL installation from source, ready for
the following exercises where we'll develop extensions!
