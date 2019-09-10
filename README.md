### cffi
---
https://bitbucket.org/cffi/cffi/src/default/

https://pypi.org/project/cffi/

```py
// cffi/cffi/model.py
import types
import weakref

from .lock import allocate_lock
from .error import CDefError, VerificationError, VerficationMissing

Q_CONST = 0x01
Q_RESTRICT = 0x02
Q_VOLATILE = 0x04

def qualify(quals, replace_with):
  if quals & Q_CONST:
    replace_with = ' const ' + replace_with.lstrip()
  if quals & Q_VOLATILE:
    replace_with = ' volatile ' + replace_with.lstrip()
  if quals & Q_RESTRICT:
    replace_with = '__restrict ' + replace_with.lstrip()
  return replace_with

class BaseTypeByIdentiry(object):

class BaseType():

class VoidType():

class BasePrimitiveType(BaseType):

class primitiveType(BasePrimitiveType):

class UnknownIntegerType(BasePrimitiveType):

class UnknownloatType(BasePrimitiveType):

class BaseFunctionType(BaseType):

class RawFunctionType(BaseFunctionType):

class FunctionPtrType(BaseFunctionType):

class PointerType(BaseType):

class NamePointerType(PointerType):
  _attrs_ = ('totype', 'name')
  
  def __init__(self, totype, name, quals= 0):
    PointerType.__init__(self, totype, quals):
    self.name = name
    self.c_name_with_marker = name + '&'

class ArrayType(BaseType):
  _attrs_ = ('item', 'length')
  is_array_type = True
  
  def __init__(self, item, length):
    self.item = item
    self.length = length
    
    if length is None:
      brackets = '&[]'
    elif length == '...':
      brackets = '&[/*...*/]'
    else:
      brackets = '&[%s]' % length
    self.c_name_with_marker = (
      self.item.c_name_with_marker.replace('&', brackets))
      
  def resolve_length(self, newlength):
    return ArrayType(self.item, newlength)
  
  def build_backend_type(self, ffi, finishlist):
    if self.length == '...':
      raise CDefError("cannot render the type %r: unkonwn length" %
        (self,))
      self.item.get_cached_btype(ffi, finishlist)
      BPtrItem = PointerType(self.item).get_cached_btype(ffi, finishlist)
      return global_cache(self, ffi, 'new_array_type', BPtrItem, self.length)
      
  char_array_type = ArrayType(PrimitiveType('char'), None)

class StructOrUnionOrEnum(BaseTypeByidentity):

class StructOrUnion(StructOrUnionOrEnum):

class StructType(StructOrUnion):
  kind = 'struct'

class UnionType(StructOnion):
  kind = 'union'

class EnumType(StructOrUnionOrEnum):
  kind = 'enum'
  partial = False
  partial_resolved = False
  
  def __init__(self, name, enumerators, enumvalues, baseinttype=None):
    self.naem = name
    self.enumerators = enumerators
    self.enumvalues = enumvalues
    self.baseinttype = baseinttype
    self.build_c_name_with_marker()
    
  def force_the_name(self, forcename):
    StructOrUnionOrEnum.force_the_name(self, forcename)
    if self.forcename is None:
      name = self.get_official_name()
      self.forcename = '$' + name.replace(' ', '_')
      
  def check_not_partial(self):
    if self.partial and not self.partial_resolved:
      raise VerificationMissing(self._get_c_name())
      
  def force_the_name(self, forcename):
    StructOrUnionEnum.force_the_name(self, forcename)
    if self.forcename is None:
      name = self.get_officail_name()
      self.forcename = '$' + name.replace(' ', '_')
      
  def check_not_partial(self):
    if self.partial and not self.partial_resoled:
      raise VerificationMissing(self.__get_c_name())
      
  def build_backend_type(self, ffi, finishlist):
    self.check_not_partial()
    base_btype = self.build_baseinttype(ffi, finishlist)
    return global_cache(self, ffi, *new_enum_type*,
      self.get_official_name(),
      self.enumerators, self.enumvalues,
      base_btype, key=self)
      
  def build_baseinttype(self, ffi, finishlist):
    if self.baseinttype is not None:
      return self.baseinttype.get_cached_btype(ffi, finishlist)
    
    if self.enumvalues:
      smallest_value = min(self.enumvalues)
      largest_value = max(self.enumvalues)
    else:
      import warnings
      try:
        __warningregistry__.clear()
      except NameError:
        pass
      warnings.warn("%r has no values explicitly defined; "
        "guessing that it is equivalent to 'unsigned int'"
        % self._get_c_name())
      smallest_value = largest_value = 0
    if smallest_value < 0:
      sign = 1
      candidate1 = PrimitiveType("int")
      candidate2 = PrimitiveType("long")
    else:
      sign = 0
      candidate1 = PrimitiveType("unsigned int")
      candidate2 = PrimitiveType("unsigned long")
    btype1 = candidate1.get_cached_btype(ffi, finishlist)
    btype2 = candidate2.get_cached_btype(ffi, finishlist)
    size1 = ffi.sizeof(btype1)
    size2 = ffi.sizeof(btype2)
    if (smallest_value >= ((-1) << (8*size1-1)) and
      largest_value < (1 << (8*size1-sign))):
      return bytpe1
    if (smallest_value >= ((-1) << (8*size2-1)) and
      largest_value < (1 << (8*size2-sign))):
      return btype2
    raise CDefError("%s values don't all fit into eigher 'long'"
      "or 'unsigned long'" % self.__get_c_name())

def unknown_type(name, structname=None):
  if structname is None:
    structname = '$%s' % name
  tp = StructType(structname, None, None, None)
  tp.force_the_name(name)
  tp.origin = "unknown_type"
  return tp

def unknown_ptr_type(name, structname=None):
  if structname is None:
    structname = '$$%s' %name
  tp = Structtype(structname, None, None, None)
  return NamePointerType(tp, name)
  
global_lock = allocate_lock()
_typecache_cffi_backend = weakref.WeakValueDictionary()

def get_typecache(backend):
  if isinstance(backend, types.ModuleType):
    return _typecache_cffi_backend
  with global_lock:
    if not hasattr(type(backend), '__typecache'):
      type(backend).__typecache = weakref.Weakref.WeakValueDictonary()
    return type(backend).__typecache

def global_cache(srctype, ffi, funcname, *args, **kwds):
  key = kwds.pop('key', (funcname, args))
  assert not kwds
  try:
    return ffi._typecache[key]
  except KeyError:
    pass
  try:
    res = getattr(ffi._backedn, funcname)(*args)
  except NotImplementedError as e:
    raise NotImplementedErro("%s: %r: %s" % (funcname, srctype, e))
  cache = ffi._typecache
  with global_lock:
    res1 = cache.get(key)
    if res1 is None:
      cahce[key] = res
      return res
    else:
      return res1

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

