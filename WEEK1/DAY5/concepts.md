# Week 1, Day 5 — Connecting Python to Snowflake

**Estimated time:** ~6 hours total (concepts are the main focus today — see `tasks.md` for a lighter, end-of-week practice load)

---

## Why This Day Matters

This is the day everything from the week connects to the platform you already know. You've spent four days learning Python's core building blocks — variables, control flow, functions, data structures, files, and basic OOP. Today, you point all of that at Snowflake. From here on, "a Python script" and "a script that talks to your data warehouse" become the same thing.

---

## 1. Installing the Snowflake Connector

```bash
pip install snowflake-connector-python
```

If you also plan to use `pandas` with Snowflake (which you will, starting next week), install the optional pandas extra as well:
```bash
pip install "snowflake-connector-python[pandas]"
```

Add these to your `requirements.txt` (`pip freeze > requirements.txt`) so the environment stays reproducible, exactly like you practiced on Day 1.

---

## 2. Never Hardcode Credentials

Before writing any connection code: **credentials (account, username, password) should never be typed directly into a script.** If that script ever gets committed to Git or shared, your credentials go with it.

The standard, simple fix is a `.env` file plus the `python-dotenv` package:

```bash
pip install python-dotenv
```

**`.env`** (a plain text file in your project root — **never commit this to Git**):
```
SNOWFLAKE_ACCOUNT=your_account_identifier
SNOWFLAKE_USER=your_username
SNOWFLAKE_PASSWORD=your_password
SNOWFLAKE_WAREHOUSE=your_warehouse
SNOWFLAKE_DATABASE=your_database
SNOWFLAKE_SCHEMA=your_schema
```

**`.gitignore`** (add this line so `.env` never gets tracked by Git):
```
.env
```

**In your Python script:**
```python
import os
from dotenv import load_dotenv

load_dotenv()   # reads the .env file and loads values into environment variables

account = os.getenv("SNOWFLAKE_ACCOUNT")
user = os.getenv("SNOWFLAKE_USER")
password = os.getenv("SNOWFLAKE_PASSWORD")
```

This pattern — secrets in a `.env` file, loaded via `python-dotenv`, `.env` excluded from Git — is standard practice, not a "beginner shortcut." You'll use it for the rest of this course and, realistically, for the rest of your career.

---

## 3. Connecting to Snowflake

```python
import snowflake.connector
import os
from dotenv import load_dotenv

load_dotenv()

conn = snowflake.connector.connect(
    account=os.getenv("SNOWFLAKE_ACCOUNT"),
    user=os.getenv("SNOWFLAKE_USER"),
    password=os.getenv("SNOWFLAKE_PASSWORD"),
    warehouse=os.getenv("SNOWFLAKE_WAREHOUSE"),
    database=os.getenv("SNOWFLAKE_DATABASE"),
    schema=os.getenv("SNOWFLAKE_SCHEMA"),
)

print("Connected successfully!")
```

`conn` is now your live connection object — every query you run goes through it.

---

## 4. Running a Query and Reading Results

Snowflake's Python connector follows the standard Python DB-API pattern (the same shape used by most database libraries — the mental model transfers if you ever use MySQL, Postgres, etc. from Python):

```python
cursor = conn.cursor()

cursor.execute("SELECT id, name, age FROM my_table LIMIT 10")

rows = cursor.fetchall()   # returns a list of tuples, one tuple per row

for row in rows:
    print(row)   # e.g. (1, 'Alice', 29)

cursor.close()
conn.close()   # always close the connection when you're done
```

### Getting results as dictionaries instead of tuples
By default, each row comes back as a plain tuple — you access values by position (`row[0]`, `row[1]`), which mirrors what you already know from Day 3. If you'd rather work with column names (like a dictionary, or a list of dicts as practiced this week), use `DictCursor`:

```python
cursor = conn.cursor(snowflake.connector.DictCursor)
cursor.execute("SELECT id, name, age FROM my_table LIMIT 10")

rows = cursor.fetchall()   # now a list of dictionaries

for row in rows:
    print(row["name"], row["age"])
```

Notice this is exactly the "list of dictionaries" shape from Day 3 — the same pattern you practiced with your Grade Book. Snowflake data, once queried, becomes ordinary Python data structures you already know how to work with.

---

## 5. Always Use try/except/finally with Connections

Real-world connections can fail — bad credentials, network issues, warehouse not running. Wrap your connection logic the way you practiced on Day 2:

```python
conn = None
try:
    conn = snowflake.connector.connect(
        account=os.getenv("SNOWFLAKE_ACCOUNT"),
        user=os.getenv("SNOWFLAKE_USER"),
        password=os.getenv("SNOWFLAKE_PASSWORD"),
        warehouse=os.getenv("SNOWFLAKE_WAREHOUSE"),
        database=os.getenv("SNOWFLAKE_DATABASE"),
        schema=os.getenv("SNOWFLAKE_SCHEMA"),
    )
    cursor = conn.cursor(snowflake.connector.DictCursor)
    cursor.execute("SELECT CURRENT_VERSION()")
    print(cursor.fetchone())

except Exception as e:
    print(f"Something went wrong: {e}")

finally:
    if conn is not None:
        conn.close()
        print("Connection closed.")
```

This is the pattern you'll build on for the rest of the course — clean connect, query, handle failure gracefully, always close the connection.

---

## 6. A Reusable Connection Function

Rather than repeating the connection code in every script, wrap it in a function — combining today's Snowflake material with Day 2's functions and Day 4's modules:

**`snowflake_helpers.py`:**
```python
import snowflake.connector
import os
from dotenv import load_dotenv

load_dotenv()

def get_connection():
    return snowflake.connector.connect(
        account=os.getenv("SNOWFLAKE_ACCOUNT"),
        user=os.getenv("SNOWFLAKE_USER"),
        password=os.getenv("SNOWFLAKE_PASSWORD"),
        warehouse=os.getenv("SNOWFLAKE_WAREHOUSE"),
        database=os.getenv("SNOWFLAKE_DATABASE"),
        schema=os.getenv("SNOWFLAKE_SCHEMA"),
    )

def run_query(query):
    conn = get_connection()
    try:
        cursor = conn.cursor(snowflake.connector.DictCursor)
        cursor.execute(query)
        return cursor.fetchall()
    finally:
        conn.close()
```

**In another script:**
```python
from snowflake_helpers import run_query

results = run_query("SELECT * FROM my_table LIMIT 5")
for row in results:
    print(row)
```

This `snowflake_helpers.py` pattern is one you'll keep reusing — and extending — for the rest of the module.

---

## Key Takeaways for Today

1. Never hardcode credentials — use a `.env` file with `python-dotenv`, and make sure `.env` is in `.gitignore`.
2. `snowflake.connector.connect()` creates a connection; a cursor runs queries against it.
3. `DictCursor` returns rows as dictionaries — the same "list of dicts" shape you practiced all week.
4. Wrap connection/query logic in `try`/`except`/`finally` so failures are handled gracefully, and always close the connection.
5. Package your connection logic into a reusable function in its own module (`snowflake_helpers.py`) — this becomes the foundation for every remaining week of the course.

Move to `tasks.md` for a lighter end-of-week practice session.
