Beginning of python learning
- [SQLite](SQLite)
- [Threads](Threading.md)
- [Decorator](Decorator)
- [PIL](Pillow)
- [DateTime](DateTime)

---
- `sort`
```python
# To sort a an array containing complex objects
# we can use key parameter, it takes a function
# which will be called on each element before making
# comparison
a = [("rg", 23), ("rgd", 2), ("at", 24)]
a.sort(key=lambda elem: elem[1]) # sort the array by second elem of tuple
# >> a = [("rgd", 2), ("rg", 23), ("at",24)]
```
