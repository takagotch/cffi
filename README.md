### cffi
---
https://bitbucket.org/cffi/cffi/src/default/

https://pypi.org/project/cffi/

```py
// cffi/cffi/model.py


def pointer_cache(ffi, BType):
  return global_cache('?', ffi, 'new_pointer_type', BType)
  
def attach_exception_info(e, name):
  if e.args and type(e.args[0]) is str:
    e.args = ('%s: %s' % (name, e.args[0]),) + e.args[1:]
```

```
```

```
```

