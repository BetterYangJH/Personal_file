对比对象id的hash值是否相同，所以修改\_\_hash\_\_和\__eq__可制定规则

```python
class A:
    def __init__(self,name,phone):
        self.name = name
        self.phone = phone
    def __hash__(self):
        # hash(obj)值依据不再是id，而是name和phone
        return hash(self.name,self.phone)
    def __eq__(self,other): # 运算符重载记得other
        if isinstance(other,A) and hash(self)==hash(other):
            return True
        else:
            return False
a1 = set(A(i) for i in list1)
a2 = set(A(i) for i in list2)
a1-a2 # 返回两个集合种name和phone都不同对象

# 简化：
@dataclass(unsafe_hash=True)
class A:
    # 比较字段
    name:str
    phone:str
    # 跳过不比较字段
    visited:str=field(hash=False,compare=False)
a1 = set(A(i) for i in list1)
a2 = set(A(i) for i in list2)
a1-a2 # 返回两个集合种name和phone都不同对象
```