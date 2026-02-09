
# 使用 Poetry 管理原始碼與模組

共用的程式碼 不用使用 virtual env 。
使用 poetry add 管理 。 
專案引用 共用的程式碼 須建立自己的 virtual env。

### 使用 poetry 建立專案

```bash
poetry new <project name>
```

### 共用的專案不須建立 virtual env

### 應用專案建立需 建立 virtual env

```bash
# 使用 install 建立 virtual env
poetry install
```

### 加入共用專案

```shell
 poetry add ../<shared project name> --editable
```



