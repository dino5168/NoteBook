## Python Dunder 方法全清單 (核心分類)

### 1. 物件初始化與銷毀 (Life Cycle)

這是最基礎的，管理物件的誕生與消失。

- `__init__(self, ...)`：建構子，初始化實例。
    
- `__new__(cls, ...)`：真正的實體化方法（通常用於單例模式或繼承不可變型別）。
    
- `__del__(self)`：析構子，物件被垃圾回收時呼叫。
    

### 2. 物件的可視化與表示 (Representation)

這對調試（Debug）至關重要，類似 Java 的 `toString()`。

- **`__repr__(self)`**：開發者導向，應盡可能返回 `eval()` 能執行的字串。
    
- **`__str__(self)`**：使用者導向，`print()` 時呼叫。
    
- `__format__(self, format_spec)`：處理 `f-string` 或 `format()`。
    

### 3. 數值運算子 (Arithmetic Operators)

讓你的物件可以像數字一樣運算（類似 C# 運算子重載）。

- `__add__` (+), `__sub__` (-), `__mul__` (*), `__truediv__` (/)
    
- `__floordiv__` (//), `__mod__` (%), `__pow__` (**)
    
- **反向運算**：`__radd__`, `__rsub__`（當左邊不是你的物件時呼叫）。
    

### 4. 比較運算子 (Comparison Operators)

- `__eq__(self, other)`：等於 `==`。
    
- `__lt__` (<), `__le__` (<=), `__gt__` (>), `__ge__` (>=), `__ne__` (!=)。
    

### 5. 集合與序列協議 (Collection & Sequence)

**這是 Python 最強大的地方**。實作這些，你的物件就能像 `List` 或 `Dict`。

- **`__len__(self)`**：`len()` 呼叫，也影響 `if obj:` 的布林判斷。
    
- **`__getitem__(self, key)`**：實現 `obj[key]` 存取（含切片）。
    
- `__setitem__`, `__delitem__`：索引賦值與刪除。
    
- **`__contains__(self, item)`**：實現 `in` 運算子。
    

### 6. 迭代協議 (Iteration)

讓你的物件可以用於 `for x in obj:`。

- **`__iter__(self)`**：回傳一個迭代器。
    
- **`__next__(self)`**：迭代器的下一步。
    
- `__reversed__(self)`：`reversed()` 呼叫。
    

### 7. 可呼叫物件 (Callable)

- **`__call__(self, *args, **kwargs)`**：讓物件可以像函數一樣被執行 `obj()`。
    

### 8. 上下文管理 (Context Manager)

類似 Java `try-with-resources` 或 C# `using`。

- `__enter__(self)`：進入 `with` 區塊前的準備。
    
- `__exit__(self, ...)`：離開 `with` 區塊後的清理。
    

### 9. 屬性管理 (Attribute Access)

- `__getattr__(self, name)`：當找不到屬性時的後備機制。
    
- `__setattr__`, `__delattr__`：攔截屬性賦值。