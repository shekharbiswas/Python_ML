# Week 1, Day 3 — Core Data Structures

**Estimated time:** 6 hours (concepts + hands-on practice, paired with `tasks.md`)

---

## Why This Day Matters

Almost all real data you'll work with — a Snowflake query result, a JSON API response, a CSV file — arrives in Python as some combination of lists and dictionaries. `pandas` (which you'll start Week 2) is really just a powerful wrapper around these same core structures. Understanding lists, tuples, dicts, and sets *before* pandas makes pandas make sense, instead of feeling like magic.

---

## 1. Lists — Ordered, Changeable Collections

```python
fruits = ["apple", "banana", "cherry"]
```

- **Ordered** — items keep their position.
- **Mutable** — you can change, add, or remove items after creation.
- **Allows duplicates.**

### Common operations
```python
fruits.append("date")          # add to the end
fruits.insert(1, "blueberry")   # insert at a specific position
fruits.remove("banana")         # remove by value
popped = fruits.pop()            # removes and returns the last item
fruits.sort()                    # sorts in place (alphabetical/numerical)
len(fruits)                      # number of items
"apple" in fruits                # True/False — membership check
```

### Indexing and slicing
```python
fruits[0]        # first item
fruits[-1]       # last item
fruits[1:3]      # items at index 1 and 2 (slice end is exclusive)
fruits[:2]       # first two items
fruits[::-1]     # reversed list
```

**Key mental model:** indexing starts at `0`, and slicing's end index is *exclusive* — `fruits[1:3]` gives you indices 1 and 2, not 3. This trips up nearly everyone at first; it's worth deliberately testing until it's automatic.

---

## 2. Tuples — Ordered, Unchangeable Collections

```python
coordinates = (10.5, 20.3)
```

- Same ordering behavior as lists, but **immutable** — once created, you can't change, add, or remove items.
- Used when you want to guarantee the data won't be accidentally modified — e.g., a fixed (latitude, longitude) pair, or returning multiple fixed values from a function (which you did on Day 2 with `get_min_max`).

```python
coordinates[0]     # 10.5 — indexing works like a list
# coordinates[0] = 5   # this would raise an error — tuples can't be modified
```

**When to choose a tuple over a list:** if the data represents a fixed collection that shouldn't change (a coordinate pair, RGB color values, a database row that's just been read). If you need to grow/shrink/modify the collection, use a list instead.

---

## 3. Dictionaries — Key-Value Pairs

```python
person = {
    "name": "Alice",
    "age": 29,
    "city": "Bangalore"
}
```

- Stores data as **key → value** pairs, not by position.
- Keys must be unique; values can be anything (including other dicts or lists).
- This is the structure that maps most directly onto real-world data — a row from a database, a JSON object from an API, a single record from Snowflake.

### Common operations
```python
person["name"]                  # "Alice" — access by key
person["email"] = "a@x.com"      # add a new key-value pair
person.get("phone", "N/A")       # safe access — returns "N/A" if "phone" doesn't exist
del person["city"]               # remove a key
"age" in person                  # True/False — checks KEYS, not values

person.keys()      # all keys
person.values()    # all values
person.items()     # all (key, value) pairs — useful for looping
```

### Looping through a dictionary
```python
for key, value in person.items():
    print(f"{key}: {value}")
```

**Why `.get()` matters:** `person["phone"]` throws a `KeyError` if the key doesn't exist. `person.get("phone", "N/A")` returns a safe default instead. When working with real, messy data (like rows from Snowflake where some fields might be missing), `.get()` is usually the safer choice.

---

## 4. Sets — Unordered Collections of Unique Items

```python
unique_ids = {101, 102, 103, 102}   # duplicates are automatically dropped
print(unique_ids)   # {101, 102, 103}
```

- **No duplicates allowed** — this is the entire point of a set.
- **Unordered** — don't rely on any particular item order.
- Extremely fast for membership checks (`x in my_set`) compared to a list, especially with large data.

### Common operations
```python
a = {1, 2, 3}
b = {2, 3, 4}

a | b     # union → {1, 2, 3, 4}
a & b     # intersection → {2, 3}
a - b     # difference → {1}
```

**Real-world use case:** deduplicating a list of IDs pulled from Snowflake (`unique_ids = set(id_list)`), or quickly checking whether a value exists in a large collection.

---

## 5. Choosing the Right Structure

| Need | Use |
|---|---|
| Ordered collection, may change | **List** |
| Ordered collection, must NOT change | **Tuple** |
| Key-value lookups (like a database record) | **Dictionary** |
| Unique items only, fast membership checks | **Set** |

This decision comes up constantly once you start working with real data — get comfortable making this call quickly.

---

## 6. List Comprehensions

A compact, Pythonic way to build a new list from an existing iterable, often replacing a multi-line `for` loop.

```python
# Traditional loop
squares = []
for n in range(10):
    squares.append(n ** 2)

# List comprehension — same result, one line
squares = [n ** 2 for n in range(10)]
```

### With a condition (filtering)
```python
evens = [n for n in range(20) if n % 2 == 0]
```

### With transformation and condition together
```python
names = ["alice", "bob", "carla"]
capitalized_long_names = [name.upper() for name in names if len(name) > 3]
# ['ALICE', 'CARLA']
```

**When to use a comprehension vs. a regular loop:** if the logic is simple (a single transformation, maybe one filter condition), a comprehension is cleaner. If there's complex logic, multiple conditions, or several steps, a regular `for` loop is more readable — don't force a comprehension just to be clever.

---

## 7. Nested Data Structures

Real data is rarely flat. A very common shape you'll see constantly — including from Snowflake query results — is **a list of dictionaries**, where each dictionary represents one row/record:

```python
users = [
    {"id": 1, "name": "Alice", "age": 29},
    {"id": 2, "name": "Bob", "age": 34},
    {"id": 3, "name": "Carla", "age": 41},
]

# Looping through and accessing nested values
for user in users:
    print(f"{user['name']} is {user['age']} years old")

# Extracting just the names using a list comprehension
names = [user["name"] for user in users]
```

This exact shape — list of dicts — is what you'll get back from many database queries and JSON APIs, and it's essentially what a `pandas` DataFrame is built from under the hood. Getting comfortable navigating this structure today will make Week 2 click much faster.

---

## Key Takeaways for Today

1. Lists = ordered + changeable. Tuples = ordered + unchangeable. Use each intentionally.
2. Dictionaries map keys to values — this is the shape of most real-world records.
3. Sets automatically remove duplicates and are fast for membership checks.
4. `.get()` on a dictionary is safer than direct key access when data might be missing a field.
5. List comprehensions are a clean shortcut for simple loop-and-filter logic — but don't force them when a regular loop is clearer.
6. A "list of dictionaries" is the most common real-world data shape you'll encounter — and it's exactly what pandas builds on next week.

Move to `tasks.md` now to put all of this into practice.
