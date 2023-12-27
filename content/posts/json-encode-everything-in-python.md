+++
title = "JSON Encode Anything in Python"
date = "2023-12-25"
[taxonomies]
  tags = ["python"]
+++

Working with JSON is an ever present task.

While the built-in json module in Python handles serialization for ["standard" data types](https://docs.python.org/3/library/json.html#json.JSONEncoder) we quickly and regulary encounter common types that are not json serializable. (i.e. datetime, Decimal and more).

These "non-standard" types result in a TypeError. I often see programers looping through a series of if statements to handle these types. There is a better way! That way is extending the builtin JSONEncoder.

## Standard Python types

No issue here.

```python
import json

standard = {
    "int": 10,
    "string": "iamastring",
    "iterable": [1, 2],
}
print(json.dumps(standard))

# Output:
# {"int": 10, "string": "iamastring", "iterable": [1, 2]}
```

## O no! Non-standard types

We have an issue.

```python
from decimal import Decimal
from datetime import datetime

non_standard = {
    "datetime": datetime.utcnow(),
    "decimal": Decimal(10.5),
}
print(json.dumps(non_standard))

# Output:
# TypeError: Object of type datetime is not JSON serializable
```

## Solution

Extend the JSONEncoder class to make handling non-standard types quick, easy and carefree.

```python
from json import JSONEncoder

class CustomJsonEncoder(JSONEncoder):
    def default(self, obj):
        if isinstance(obj, datetime):
            return obj.isoformat()
        if isinstance(obj, Decimal):
            return float(obj)
        else:
            return JSONEncoder.default(self, obj)

print(json.dumps(non_standard, cls=CustomJsonEncoder))

# Output:
# {"datettime": "2023-12-25T17:38:32.872132", "decimal": 10.5}
```

Awesome! By specifying a CustomerJsonEncoder we can serialize without worry. This pattern can be extended to handle any other non-standard type we may desire.
