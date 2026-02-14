# JSON Encoding and Decoding

## Built-in JSON Encoders

The built-in json module encodes and decodes lists, dictionaires and basic types.
1. import json
2. dumps(obj) -> str - dumps an encodable json object to a string
3. loads(s: str) -> Dict[str, Any] - loads a dictionary or list from a json string

```python
import json

lst = [1, 2, 3]
json_lst = """[1, 2, 3]"""

assert json.dumps(lst) == json_lst
assert json.loads(json_lst) == lst

d = {1: "foo", 2: "bar", 3: [123, 234]}
json_dict = """{"1": "foo", "2": "bar", "3": [123, 234]}"""
assert json.dumps(d) == json_dict

decoded_dict = {"1": "foo", "2": "bar", "3": [123, 234]}
assert json.loads(json_dict) == decoded_dict
```

## Using Pydantic

For user defined types the json encoding and decoding doesn't work out of the box.
However, using the Pydantic base models it is possible to encode and decode
from json strings directly or from dictionaries.

Pydantic will do some data validation as well.

```python
import json
import pytest
from typing import *
from dataclasses import dataclass, asdict
from pydantic import BaseModel, ValidationError

@dataclass
class Car:
    name: str

class User(BaseModel):
    id: int
    name: str
    cars: List[Car]

bob = User(id=1, name="Bob", cars=[Car("Toyota"), Car("Ford")])

# To Json
bob_json = bob.model_dump_json()
expected_json = """{"id":1,"name":"Bob","cars":[{"name":"Toyota"},{"name":"Ford"}]}"""
assert bob_json == expected_json

# To dictionary
bob_dict = bob.model_dump()
assert bob_dict["name"] == "Bob"

# From Json
decoded_bob = User.model_validate_json(expected_json)
assert decoded_bob.name == "Bob"
assert decoded_bob.cars[0].name == "Toyota"

# From dictionary
decoded_bob = User.model_validate(bob_dict)
assert decoded_bob.name == "Bob"
assert decoded_bob.cars[0].name == "Toyota"

# Type safety example
bad_json = """{"id":1,"name":"Bob","cars":[{"name": 1},{"name":"Ford"}]}"""
with pytest.raises(ValidationError) as validation_error:
    User.model_validate_json(bad_json)

assert "Input should be a valid string" in str(validation_error)
```