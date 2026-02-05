
### 為何要有 Vector DataBase

#### 解決問題

#### 如何解決


### 什麼是Vector DataBase 

#### 誰提出

#### 歷史


### 安裝 Vector DataBase 

1. 安裝 PostgreSQL Vector 外掛 pgvector
2.  解壓縮安裝檔 。 將 \lib\vector.dll 放到 programe files\postgresql ..\lib\。 \share\ 放到 \share
3. 建立資料庫  DataBase VectorDB
4. 執行 

PostgreSQL : 

建立外掛
```sql
CREATE EXTENSION IF NOT EXISTS vector;
```
檢核外掛是否安裝成功
```sql
SELECT * FROM pg_available_extensions WHERE name = 'vector';
```


