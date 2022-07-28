---
banner: ""
banner_icon: 
---

---
**Tags**:: #python
**Links**::[[Python]]
**Description**:: data types , comments , strings

---

### Data Types

| Data Type              | Examples                                  |
| ---------------------- | ----------------------------------------- |
| Integers               | `-2, -1, 0, 1, 2, 3, 4, 5`                |
| Floating-point numbers | `-1.25, -1.0, --0.5, 0.0, 0.5, 1.0, 1.25` |
| Strings                | `'a', 'aa', 'aaa', 'Hello!', '11 cats'`   |

---  

### String Concatenation and Replication

String concatenation:

```python
>>> 'Alice' 'Bob'

'AliceBob'
```

Note: Avoid `+` operator for string concatenation. Prefer string formatting.

String Replication:

```python
>>> 'Alice' * 5

'AliceAliceAliceAliceAlice'

```

---

### Comments
Inline comment:
```python
# This is a comment
```

Multiline comment:

```Python
# This is a

# multiline comment

```

Code with a comment:

```python
a = 1  # initialization
```

Please note the two spaces in front of the comment.

Function docstring:

```python
def foo():

    """

    This is a function docstring

    You can also use:

    ''' Function Docstring '''

    """
```