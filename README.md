# My python Snippets
This is my python snippet storage.


## Dictionary

### dict_path

Get a deep value from a dictionary tree.

Snippet
```python
import re

def dict_path(path: str, data, **kwargs):
    """
    Get deep value from dictionary tree
    :param path: str (e.g. config.labels.cz\\.my\\.test)
    :param data: dict tree
    :raise KeyError
    :return: Any - return value
    """
    delim = r'(?<!\\)\.' if 'delimiter' not in kwargs else kwargs['delimiter']
    try:
        dd = data
        for it in re.split(delim, path):
            dd = dd[it.replace('\\', '')]
        return dd
    except Exception as ex:
        if 'default' in kwargs:
            return kwargs['default']
        raise ex
```
Example
```pycon
data = {'A': 1, 'B': {'BA': 2, 'BB': {'BB.A': ['XXX','YYY']}}}

dict_path('B.BA', data)
>> 2


dict_path('B.BB.BB\\.A', data)
>> ['XXX','YYY']


dict_path('B.BB.C', data, default=None)
>> None


```

### dict_update

Update a dictionary tree.

Snippet:
```python
import re

def dict_update(data: dict, update: dict):
    """
    version: 1.0
    Update dictionary tree
    """
    for key, val in update.items():
        keys = re.split(r'(?<!\\)\.', key)
        attr = keys.pop().replace('\\', '')
        item = data
        while keys:
            _k = keys.pop(0)
            if _k not in item:
                item[_k] = {}
            item = item[_k]
        if not isinstance(val, dict) or attr not in item:
            if isinstance(val, list) and isinstance(item.get(attr, ''), list) \
                    and '...' in val:
                val.remove('...')
                item[attr] += val
            else:
                item[attr] = val
        else:
            dict_update(item[attr], val)
```

Example:
```python
data = {'A': 1, 'B': {'BA': 2, 'BB': {'BBA': ['XXX', 'YYY']}}}
dict_update(data, {'B.BA': 5)
print(data)
>> {'A': 1, 'B': {'BA': 5, 'BB': {'BBA': ['XXX', 'YYY']}}}


data = {'A': 1, 'B': {'BA': 2, 'BB': {'BBA': ['XXX','YYY']}}}
dict_update(data, {'B.BB': {'BBA': ['...', 'ZZZ']}})
print(data)
>> {'A': 1, 'B': {'BA': 2, 'BB': {'BBA': ['XXX', 'YYY', 'ZZZ']}}}

```
