+++
title = "One Simple Trick for Readable Python Integers"
date = "2023-12-10"
[taxonomies]
  tags = ["python"]
+++

Which integer is easier to read: 100000000 or 100,000,000?

If you are human like me, you likely agree that the latter is more inuitive due to the comma separator. Python syntax allows you to add an underscore (`_`) separator to integers to make them more readible.

```python
# Non-readable integer. I can't easily tell if this is 100 million or 1 billion.
i = 100000000
print(i)
# Output:
# 100000000

# Readable integer. It is immediately obvious this is 100 million.
i = 100_000_000
print(i)
# Output:
# 100000000
```

While the code above is much more readable, the print statement still outputs the integer in it's non-readable form. Read below to see what we can do about that.

## Formatting integers with f-strings

Python f-strings provide a way make printing or logging integers just as readable.

```python
i = 100_000_000
print(f"{i:,}")
# Output:
# 100,000,000

# You can also provide the underscore as a separator if that's what you'd prefer.
i = 100_000_000
print(f"{i:_}")
# Output:
# 100_000_000
```

For more on all you can do with Python f-strings check out this great article by [Martin Hienz, Python f-strings Are More Powerful Than You Might Think](https://martinheinz.dev/blog/70).
