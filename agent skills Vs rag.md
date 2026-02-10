  

介紹一下agent skills 的歷史。解決了甚麼問題?如何解決? 怎麼運作?

2月5日

# Agent Skills 的發展歷史與運作

## 起源與背景

Agent Skills 的概念源於 **大型語言模型（LLM）應用開發**中的一個核心挑戰：如何讓 AI 助手能夠可靠地執行特定領域的複雜任務。

### 時間線

**2022-2023 年初期**

- GPT-3/GPT-4 等模型展現強大能力，但在特定任務上表現不穩定
- 開發者發現通過精心設計的 prompt 可以顯著提升性能
- 但這些 prompt 難以管理、重用和迭代

**2023-2024 年**

- Function calling 和 tool use 成為主流
- 出現了將「專業知識」模組化的需求
- Skills 概念開始成形：將最佳實踐封裝成可重用的指令集

## 解決的核心問題

### 1. **不一致的輸出品質**

LLM 在相同任務上可能產生差異巨大的結果，特別是：

- 創建複雜文檔（Word、PowerPoint、Excel）
- 處理結構化數據
- 執行多步驟工作流程

### 2. **知識傳承困難**

- 通過試錯積累的最佳實踐難以保存
- 團隊間無法有效分享成功經驗
- 每次都要重新摸索如何讓 AI 正確完成任務

### 3. **上下文窗口限制**

- 將所有指令都放在系統提示中會消耗大量 tokens
- 需要動態加載相關指令

## 解決方案：Skills 架構

### 核心設計

```
/mnt/skills/
├── public/          # 平台提供的官方 skills
│   ├── docx/       # Word 文檔處理
│   ├── pptx/       # PowerPoint 處理
│   ├── xlsx/       # Excel 處理
│   └── pdf/        # PDF 處理
├── private/         # 組織內部 skills
└── user/           # 用戶自定義 skills
```

每個 skill 包含：

- **SKILL.md**：詳細的最佳實踐指南
- **description**：觸發條件說明
- **location**：文件路徑

### 運作機制

**1. 觸發階段**

```
用戶請求 → 分析任務類型 → 匹配相關 skills
```

例如：用戶說「創建一份季度報告的 PowerPoint」

- 系統識別關鍵詞：「PowerPoint」、「創建」
- 匹配到 `pptx` skill

**2. 載入階段**

python

````python
# Claude 首先使用 view 工具讀取 skill
view /mnt/skills/public/pptx/SKILL.md
```

**3. 執行階段**
- 按照 skill 中的指引執行任務
- 使用推薦的庫和方法
- 遵循格式和結構規範

**4. 輸出階段**
- 生成符合最佳實踐的高質量產出
- 確保一致性和專業性

## 技術優勢

### 動態知識注入
只在需要時載入相關 skill，節省上下文空間：
```
基礎系統提示: ~50K tokens
+ 相關 skill: ~5-10K tokens
────────────────────────────
總計: 遠小於全量指令
```

### 可組合性
複雜任務可以組合多個 skills：
```
任務：「分析 Excel 數據並製作 PowerPoint 報告」
→ 載入 xlsx skill + pptx skill
````

### 持續改進

- 無需重新訓練模型
- 通過更新 SKILL.md 即可優化
- 社區可以貢獻新 skills

## 實際案例

**創建 Word 文檔**

- **沒有 skill**：可能使用錯誤的庫、格式混亂、缺少目錄
- **使用 docx skill**：自動使用 python-docx、正確設置樣式、添加頁碼和目錄

**處理 PDF**

- **沒有 skill**：可能嘗試使用已棄用的 pypdf
- **使用 pdf skill**：使用推薦的 pypdf2/pdfplumber，正確處理不同場景

## 當前生態

**官方 Skills**（Anthropic 提供）

- 文檔處理（docx, pptx, xlsx, pdf）
- 前端設計
- 產品知識庫

**擴展方向**

- 用戶可以創建自定義 skills
- 組織可以建立內部知識庫
- 社區分享最佳實踐

## 未來發展

Agent Skills 代表了從「通用 AI」到「專家 AI」的演進路徑，通過模組化知識管理，讓 AI 助手能夠在特定領域達到專家級表現，同時保持系統的靈活性和可維護性。

與傳統的 Vector DB ,RAG 有何不同 ?

2月5日

# Agent Skills vs Vector DB/RAG：架構比較

這是個很好的問題！它們解決的問題有重疊，但方法和適用場景很不同。

## 核心差異對比

### **檢索機制**

**Vector DB + RAG**

```
用戶查詢 → embedding → 向量相似度搜索 → 檢索相關文檔片段
```

- 基於**語義相似度**
- 概率性匹配
- 可能檢索到不相關內容

**Agent Skills**

```
用戶查詢 → 規則匹配 + 關鍵詞觸發 → 載入完整 skill
```

- 基於**確定性規則**
- 精確匹配
- 全有或全無

### **內容組織方式**

**RAG**

```
知識庫
├── 文檔 A (切分成 chunks)
│   ├── chunk_1 (512 tokens)
│   ├── chunk_2 (512 tokens)
│   └── chunk_3 (512 tokens)
└── 文檔 B (切分成 chunks)
    └── ...
```

- 碎片化存儲
- 可能丟失上下文
- 需要重組信息

**Skills**

```
/mnt/skills/docx/SKILL.md (完整文檔)
├── 最佳實踐說明
├── 代碼示例
├── 常見陷阱
└── 完整工作流程
```

- 保持完整性
- 結構化組織
- 一次性載入全部相關指令

## 適用場景對比

### RAG 擅長的場景

**1. 大規模知識查詢**

```
場景：企業內部文檔搜索
數據：10,000+ 份文檔
查詢：「公司的差旅報銷政策是什麼？」

→ RAG 從大量文檔中找到相關段落
```

**2. 事實性問答**

```
場景：產品手冊查詢
數據：數百頁技術文檔
查詢：「X型號的最大承重是多少？」

→ 精確定位相關信息
```

**3. 動態內容**

```
場景：新聞資料庫
特點：內容頻繁更新
查詢：需要最新信息

→ 實時檢索最新內容
```

### Skills 擅長的場景

**1. 程序性知識（How-to）**

```
場景：創建 PowerPoint
需求：完整的工作流程
- 如何初始化庫
- 如何設置版式
- 如何添加圖表
- 如何處理錯誤

→ Skill 提供端到端指南
```

**2. 最佳實踐**

```
場景：代碼生成
需求：遵循特定規範
- 使用推薦的庫版本
- 避免已知陷阱
- 處理邊緣情況

→ Skill 封裝經驗知識
```

**3. 一致性要求高的任務**

```
場景：生成標準化報告
需求：每次輸出格式一致
- 固定的結構
- 統一的樣式
- 可預測的行為

→ Skill 確保一致性
```

## 技術架構差異

### RAG 架構複雜度

python

```python
# RAG 需要的基礎設施
1. Embedding 模型（OpenAI/Cohere/本地模型）
2. 向量數據庫（Pinecone/Weaviate/ChromaDB）
3. 文檔處理管道
   - 切分策略
   - 重疊設置
   - 元數據管理
4. 檢索策略
   - Top-K 選擇
   - 重排序
   - 混合搜索
```

**維護成本**

- 需要獨立服務
- 向量索引更新
- Embedding 費用
- 延遲考量（API 調用）

### Skills 架構簡潔度

python

```python
# Skills 需要的基礎設施
1. 文件系統（已有）
2. 簡單的匹配邏輯
3. Markdown 文件
```

**維護成本**

- 無需額外服務
- 直接編輯文件
- 零運行成本
- 極低延遲（本地文件）

## 性能對比

|維度|RAG|Skills|
|---|---|---|
|**延遲**|100-500ms (embedding + 檢索)|<10ms (文件讀取)|
|**準確度**|70-90% (取決於檢索質量)|~100% (規則觸發)|
|**Token 消耗**|可控 (top-k chunks)|較高 (完整文檔)|
|**擴展性**|優秀 (可存儲海量數據)|有限 (受限於可管理的 skills 數量)|

## 組合使用案例

實際上，它們可以**互補**：

### 混合架構示例

python

```python
# 場景：智能文檔處理助手

if task_type == "創建文檔":
    # 使用 Skill 獲取操作指南
    load_skill("docx")
    
if query == "公司有哪些類似案例？":
    # 使用 RAG 檢索歷史文檔
    results = vector_db.search(query)
    
# 結合兩者
skill_instructions + relevant_examples → 最佳輸出
```

### 實際場景

**客服機器人**

- **Skills**：處理標準化流程（退貨、換貨）
- **RAG**：查詢產品信息、政策細節

**代碼助手**

- **Skills**：提供編程最佳實踐
- **RAG**：檢索公司內部代碼庫示例

**研究助手**

- **Skills**：指導如何進行文獻綜述
- **RAG**：檢索相關論文內容

## 選擇建議

### 選擇 RAG 當：

✅ 知識庫很大（1000+ 文檔）  
✅ 內容頻繁變化  
✅ 需要靈活的語義搜索  
✅ 重點在信息檢索而非任務執行

### 選擇 Skills 當：

✅ 需要可預測、一致的行為  
✅ 封裝的是「如何做」而非「是什麼」  
✅ 知識相對穩定  
✅ 需要完整的上下文（不能切分）  
✅ 追求極低延遲和成本

### 兩者都用當：

✅ 系統需要同時處理知識查詢和任務執行  
✅ 希望結合結構化指令和非結構化知識

## 總結

**RAG** 是一個強大的**檢索系統**，適合從海量信息中找到相關內容。

**Skills** 是一個輕量的**知識管理系統**，適合封裝和傳遞專家級的操作指南。

它們不是競爭關係，而是在 AI 系統架構中扮演不同角色。選擇哪個取決於你的具體需求：是需要「找到信息」還是「正確執行任務」。

RAG 內容頻繁變化? 使用資料建立 Vector DB ? 然後 查詢 ? 為何可以應付內容頻繁變化?

2月5日

你問到了關鍵點！我需要澄清我之前的表述——RAG 並**不是天生就擅長處理頻繁變化**，而是**相對於 Skills 來說更容易更新**。讓我重新解釋：

## RAG 更新機制的真相

### 更新流程

python

````python
# 傳統 RAG 更新流程
新文檔 → 文檔處理 → 切分 chunks → 生成 embeddings → 寫入向量庫
```

這個過程**並不即時**，實際上有明顯的延遲：

**時間成本**
```
1. 文檔切分: 秒級
2. Embedding 生成: 
   - 1000 個 chunks × 50ms = 50 秒
   - 批量處理可能需要數分鐘
3. 向量庫索引更新: 秒到分鐘級
```

**實際例子**
```
場景：公司政策文檔更新
09:00 - HR 上傳新版差旅政策
09:05 - 文檔被處理並生成 embeddings
09:10 - 向量庫索引完成
09:15 - 用戶查詢才能看到新內容

→ 15分鐘延遲
```

## 為什麼說 RAG "適合"頻繁變化？

### 不是因為它快，而是因為它**可擴展**

**對比 1: RAG vs 重新訓練模型**
```
方案 A: 微調/重新訓練 LLM
- 準備訓練數據: 天級
- 訓練: 小時到天級
- 部署: 小時級
總計: 數天

方案 B: 更新 RAG
- 處理新文檔: 分鐘級
- 更新向量庫: 分鐘級
總計: <30 分鐘
````

**對比 2: RAG vs 硬編碼 Prompts**

python

```python
# 硬編碼方式
system_prompt = """
公司政策:
1. 差旅報銷上限 5000 元
2. 需提前申請
3. ... (500 行政策)
"""
# 問題: 每次政策變化都要修改代碼、重新部署

# RAG 方式
system_prompt = "查詢向量庫獲取最新政策"
# 優勢: 只需更新文檔庫，無需改代碼
```

## RAG 的增量更新能力

### 實際優勢在於**增量更新**

python

````python
# 場景：新聞資料庫
每天新增 1000 篇文章

# 傳統方式
def update_all():
    重新處理所有 100 萬篇文章  # 不可行！
    
# RAG 增量更新
def incremental_update():
    for new_article in today_articles:
        embedding = embed(new_article)
        vector_db.insert(embedding)  # 只處理新內容
```

**對比**
```
全量更新: 100 萬篇 × 50ms = 13.9 小時
增量更新: 1000 篇 × 50ms = 50 秒
````

## RAG 處理"變化"的局限性

### 1. **實時性問題**

python

```python
# 不適合的場景
場景: 股票價格查詢
更新頻率: 秒級
RAG 延遲: 分鐘級
→ 數據永遠是過期的！

# 更好的方案: 直接 API 調用
def get_stock_price(symbol):
    return api.get_real_time_price(symbol)
```

### 2. **更新成本**

python

```python
# 高頻更新的成本
更新頻率: 每小時
文檔數: 1000 個
Embedding 成本: $0.0001/1K tokens
每月成本: 1000 docs × 24 hours × 30 days × $0.0001 = $72

# 如果內容變化頻繁，成本會快速累積
```

### 3. **一致性問題**

python

````python
# 場景: 用戶在查詢期間，文檔被更新

10:00:00 - 用戶發起查詢
10:00:05 - RAG 檢索到舊版本 chunks
10:00:10 - 文檔被更新
10:00:15 - 用戶看到的是過期信息

# 向量庫更新不是事務性的
```

## 真正適合 RAG 的"變化"類型

### ✅ 適合：週期性、批次更新
```
例子 1: 企業知識庫
- 每週新增幾份文檔
- 每天夜間批次更新向量庫
- 用戶白天查詢到昨天的最新內容

例子 2: 技術文檔
- API 文檔每月更新幾次
- 更新後重建索引
- 對時效性要求不高
```

### ❌ 不適合：高頻、實時更新
```
反例 1: 社交媒體動態
- 每秒數千條新帖子
- Embedding 成本爆炸
- 延遲無法接受

反例 2: 傳感器數據
- 毫秒級數據流
- 不需要語義搜索
- 時序數據庫更合適
````

## RAG vs Skills 在"變化"上的對比

### RAG 更新

bash

```bash
# 更新過程
1. 編輯文檔 (人工)
2. 上傳到系統
3. 自動處理 pipeline
   ├── 解析
   ├── 切分
   ├── Embedding
   └── 索引
4. 幾分鐘後生效

# 優點: 自動化程度高
# 缺點: 有處理延遲
```

### Skills 更新

bash

```bash
# 更新過程
1. 編輯 SKILL.md (人工)
2. 保存文件
3. 立即生效

# 優點: 零延遲
# 缺點: 需要技術人員手動編輯
```

## 實際場景分析

### 場景 1: 產品文檔系統

python

```python
特點:
- 500 個產品
- 每個產品 50 頁文檔
- 每月更新 20 個產品

RAG 方案:
✅ 可擴展: 25,000 頁文檔
✅ 更新頻率: 可接受（每月）
✅ 自動化: 產品經理上傳即可

Skills 方案:
❌ 不可行: 無法為每個產品寫 skill
❌ 維護困難: 500 個 skill 難管理
```

### 場景 2: 編程最佳實踐

python

```python
特點:
- 10 種文檔格式
- 每種有固定的最佳實踐
- 每季度優化一次

Skills 方案:
✅ 結構化: 每個格式一個 skill
✅ 一致性: 確保每次都遵循最佳實踐
✅ 低延遲: 即時載入

RAG 方案:
❌ 碎片化: 最佳實踐可能被切分
❌ 不確定性: 可能檢索到不完整的指令
```

### 場景 3: 客服知識庫

python

```python
特點:
- 1000+ FAQ
- 每天新增/修改 10 條
- 需要快速反映最新政策

混合方案:
Skills: 標準流程（退貨、換貨步驟）
  ├── 固定不變
  └── 需要完整上下文
  
RAG: 具體政策內容
  ├── 頻繁更新
  └── 可以增量添加
```

## 結論修正

我之前說 RAG "適合內容頻繁變化" 需要加上限定條件：

**RAG 適合的"變化"**

- ✅ **規模變化**：內容數量持續增長
- ✅ **批次更新**：定期更新（天/周級別）
- ✅ **增量添加**：新增內容但舊內容穩定
- ✅ **可容忍延遲**：幾分鐘到幾小時的更新延遲

**RAG 不適合的"變化"**

- ❌ **實時變化**：秒級/分鐘級更新
- ❌ **全局修改**：整個知識庫結構性改變
- ❌ **強一致性**：要求數據絕對最新

**Skills 的優勢**

- ✅ **零延遲更新**：編輯即生效
- ✅ **版本控制友好**：Git 可追蹤
- ✅ **適合穩定知識**：最佳實踐、操作流程

所以更準確的說法是：**RAG 的可擴展性使它更容易處理大規模、增量式的內容更新，而不是真正的高頻實時變化**。

RAG 方式 system_prompt = "查詢向量庫獲取最新政策" # 優勢: 只需更新文檔庫，無需改代碼 ? 江心的文件轉成 Vector DB ?

2月5日

你抓到重點了！讓我詳細解釋 RAG 的完整工作流程，特別是**文件如何轉成 Vector DB**。

## RAG 完整流程圖

```
文檔更新 → 文檔處理 → 向量化 → 存儲 → 查詢 → 返回結果
```

## 第一步：文檔處理（Document Processing）

### 1. 文檔載入

python

```python
# 支持多種格式
from langchain.document_loaders import (
    PDFLoader,
    WordLoader,
    TextLoader,
    WebsiteLoader
)

# 載入文檔
loader = PDFLoader("公司差旅政策.pdf")
documents = loader.load()

# 原始文檔內容
"""
公司差旅政策
一、報銷標準
1. 國內差旅：每日住宿上限 800 元...
2. 國際差旅：每日住宿上限 1500 元...
二、申請流程
3. 提前 3 天提交申請...
"""
```

### 2. 文檔切分（Chunking）

**為什麼要切分？**

- LLM 上下文窗口有限
- Embedding 模型通常限制 512-8192 tokens
- 精確匹配：小片段更容易命中相關內容

python

```python
from langchain.text_splitter import RecursiveCharacterTextSplitter

# 切分策略
splitter = RecursiveCharacterTextSplitter(
    chunk_size=500,        # 每個 chunk 500 字符
    chunk_overlap=50,      # 重疊 50 字符（避免切斷語義）
    separators=["\n\n", "\n", "。", "，", " "]  # 優先在這些位置切分
)

chunks = splitter.split_documents(documents)
```

**切分結果示例**

python

```python
# Chunk 1
{
    "content": "公司差旅政策\n一、報銷標準\n1. 國內差旅：每日住宿上限 800 元，餐費上限 200 元。需提供正式發票。",
    "metadata": {
        "source": "公司差旅政策.pdf",
        "page": 1,
        "chunk_id": 0
    }
}

# Chunk 2 (注意有 50 字符重疊)
{
    "content": "需提供正式發票。2. 國際差旅：每日住宿上限 1500 元，餐費上限 500 元。需提前申請外匯。",
    "metadata": {
        "source": "公司差旅政策.pdf", 
        "page": 1,
        "chunk_id": 1
    }
}

# Chunk 3
{
    "content": "二、申請流程\n1. 提前 3 天在系統提交申請\n2. 部門主管審批\n3. 行政部核准後方可出行",
    "metadata": {
        "source": "公司差旅政策.pdf",
        "page": 2,
        "chunk_id": 2
    }
}
```

## 第二步：向量化（Embedding）

### 什麼是 Embedding？

將文字轉換成高維向量（數字陣列），語義相似的文字會有相似的向量。

python

```python
from openai import OpenAI

client = OpenAI(api_key="your-key")

# 對每個 chunk 生成 embedding
def embed_chunk(text):
    response = client.embeddings.create(
        model="text-embedding-3-small",  # 1536 維向量
        input=text
    )
    return response.data[0].embedding

# 實際執行
chunk_1_text = "公司差旅政策\n一、報銷標準\n1. 國內差旅：每日住宿上限 800 元..."
embedding_1 = embed_chunk(chunk_1_text)

print(len(embedding_1))  # 1536
print(embedding_1[:5])   # [0.0234, -0.0123, 0.0456, -0.0089, 0.0321]
```

**向量化結果**

python

```python
embeddings = []

for chunk in chunks:
    vector = embed_chunk(chunk["content"])
    embeddings.append({
        "id": chunk["chunk_id"],
        "vector": vector,           # [0.023, -0.012, ...] (1536 維)
        "text": chunk["content"],   # 原始文字
        "metadata": chunk["metadata"]
    })

# embeddings[0]
{
    "id": 0,
    "vector": [0.0234, -0.0123, 0.0456, ..., 0.0211],  # 1536 個數字
    "text": "公司差旅政策\n一、報銷標準...",
    "metadata": {"source": "公司差旅政策.pdf", "page": 1}
}
```

## 第三步：存入向量資料庫

### 向量資料庫選擇

python

```python
# 選項 1: Pinecone (雲端服務)
import pinecone

pinecone.init(api_key="your-key")
index = pinecone.Index("company-policies")

# 批量插入
index.upsert(vectors=[
    ("chunk_0", embedding_1, {"text": "...", "source": "..."}),
    ("chunk_1", embedding_2, {"text": "...", "source": "..."}),
    ("chunk_2", embedding_3, {"text": "...", "source": "..."})
])
```

python

````python
# 選項 2: ChromaDB (本地/開源)
import chromadb

client = chromadb.Client()
collection = client.create_collection("company_policies")

collection.add(
    ids=["chunk_0", "chunk_1", "chunk_2"],
    embeddings=[embedding_1, embedding_2, embedding_3],
    documents=[chunk_1_text, chunk_2_text, chunk_3_text],
    metadatas=[
        {"source": "差旅政策.pdf", "page": 1},
        {"source": "差旅政策.pdf", "page": 1},
        {"source": "差旅政策.pdf", "page": 2}
    ]
)
```

**向量資料庫內部結構**
```
Vector Database
│
├─ Index (快速搜尋用的資料結構，如 HNSW)
│
└─ Storage
    ├─ chunk_0
    │   ├─ vector: [0.023, -0.012, ..., 0.021]
    │   ├─ text: "公司差旅政策..."
    │   └─ metadata: {source, page}
    │
    ├─ chunk_1
    │   ├─ vector: [0.045, 0.032, ..., -0.018]
    │   ├─ text: "需提供正式發票..."
    │   └─ metadata: {source, page}
    │
    └─ chunk_2
        ├─ vector: [-0.012, 0.056, ..., 0.034]
        ├─ text: "二、申請流程..."
        └─ metadata: {source, page}
````

## 第四步：查詢流程

### 用戶查詢處理

python

```python
# 用戶問題
user_query = "國內出差住宿可以報銷多少錢？"

# 1. 將問題也轉成向量
query_vector = embed_chunk(user_query)
# query_vector = [0.0245, -0.0115, 0.0423, ..., 0.0198]

# 2. 在向量資料庫中搜尋最相似的 chunks
results = collection.query(
    query_embeddings=[query_vector],
    n_results=3  # 返回最相似的 3 個 chunks
)
```

### 向量相似度計算

python

```python
# 向量資料庫內部計算（餘弦相似度）
import numpy as np

def cosine_similarity(vec1, vec2):
    return np.dot(vec1, vec2) / (np.linalg.norm(vec1) * np.linalg.norm(vec2))

# 計算查詢向量與每個 chunk 的相似度
similarities = []
for chunk_id, chunk_vector in vector_db:
    similarity = cosine_similarity(query_vector, chunk_vector)
    similarities.append((chunk_id, similarity))

# 排序，取前 3 名
top_3 = sorted(similarities, key=lambda x: x[1], reverse=True)[:3]

"""
結果:
[
    ("chunk_0", 0.89),  # "公司差旅政策\n一、報銷標準\n1. 國內差旅：每日住宿上限 800 元..."
    ("chunk_1", 0.76),  # "需提供正式發票。2. 國際差旅：每日住宿上限 1500 元..."
    ("chunk_2", 0.45)   # "二、申請流程..."
]
"""
```

### 檢索結果

python

```python
# 向量資料庫返回的內容
retrieved_chunks = [
    {
        "text": "公司差旅政策\n一、報銷標準\n1. 國內差旅：每日住宿上限 800 元，餐費上限 200 元。需提供正式發票。",
        "score": 0.89,
        "metadata": {"source": "差旅政策.pdf", "page": 1}
    },
    {
        "text": "需提供正式發票。2. 國際差旅：每日住宿上限 1500 元，餐費上限 500 元。",
        "score": 0.76,
        "metadata": {"source": "差旅政策.pdf", "page": 1}
    },
    {
        "text": "二、申請流程\n1. 提前 3 天在系統提交申請",
        "score": 0.45,
        "metadata": {"source": "差旅政策.pdf", "page": 2}
    }
]
```

## 第五步：生成回答

### 構建 Prompt

python

```python
# 組合檢索結果和用戶問題
context = "\n\n".join([chunk["text"] for chunk in retrieved_chunks])

prompt = f"""
根據以下參考資料回答問題：

參考資料：
{context}

問題：{user_query}

請基於參考資料回答，如果參考資料中沒有相關信息，請明確說明。
"""

# 實際的 prompt 內容：
"""
根據以下參考資料回答問題：

參考資料：
公司差旅政策
一、報銷標準
1. 國內差旅：每日住宿上限 800 元，餐費上限 200 元。需提供正式發票。

需提供正式發票。2. 國際差旅：每日住宿上限 1500 元，餐費上限 500 元。

二、申請流程
1. 提前 3 天在系統提交申請

問題：國內出差住宿可以報銷多少錢？

請基於參考資料回答，如果參考資料中沒有相關信息，請明確說明。
"""
```

### LLM 生成答案

python

```python
from openai import OpenAI

client = OpenAI()

response = client.chat.completions.create(
    model="gpt-4",
    messages=[
        {"role": "system", "content": "你是公司政策助手"},
        {"role": "user", "content": prompt}
    ]
)

answer = response.choices[0].message.content
# "根據公司差旅政策，國內差旅每日住宿報銷上限為 800 元，需要提供正式發票。"
```

## 文檔更新時的處理

### 場景：政策更新

python

```python
# 舊政策文檔
舊內容 = "國內差旅：每日住宿上限 800 元"

# 新政策文檔（2024/02/01 更新）
新內容 = "國內差旅：每日住宿上限 1000 元"  # 漲價了！

# 更新流程
def update_policy(new_document):
    # 1. 刪除舊的 chunks
    old_chunks = collection.get(where={"source": "差旅政策.pdf"})
    collection.delete(ids=old_chunks["ids"])
    
    # 2. 處理新文檔
    new_chunks = splitter.split_text(new_document)
    
    # 3. 生成新的 embeddings
    new_embeddings = [embed_chunk(chunk) for chunk in new_chunks]
    
    # 4. 插入向量資料庫
    collection.add(
        ids=[f"chunk_{i}" for i in range(len(new_chunks))],
        embeddings=new_embeddings,
        documents=new_chunks,
        metadatas=[{"source": "差旅政策.pdf", "updated": "2024-02-01"}] * len(new_chunks)
    )
    
    print("✅ 向量資料庫已更新，用戶下次查詢會看到新政策")

# 執行更新
update_policy(新內容)
```

### 增量更新示例

python

````python
# 不是替換整個資料庫，而是增量添加
def add_new_document(document, source_name):
    """
    新增文檔而不影響現有內容
    """
    # 處理新文檔
    chunks = splitter.split_text(document)
    embeddings = [embed_chunk(chunk) for chunk in chunks]
    
    # 生成唯一 ID (避免衝突)
    import uuid
    ids = [f"{source_name}_{uuid.uuid4()}" for _ in chunks]
    
    # 添加到向量資料庫
    collection.add(
        ids=ids,
        embeddings=embeddings,
        documents=chunks,
        metadatas=[{"source": source_name}] * len(chunks)
    )

# 使用示例
add_new_document("新產品發布公告...", "產品公告_2024Q1.pdf")
# ✅ 舊文檔仍然存在
# ✅ 新文檔已添加
# ✅ 查詢時會搜尋所有文檔
```

## 完整的系統架構圖
```
┌─────────────────────────────────────────────────────┐
│                    文檔來源                          │
│   PDF, Word, 網頁, 資料庫, API...                    │
└──────────────────┬──────────────────────────────────┘
                   │
                   ▼
┌─────────────────────────────────────────────────────┐
│              文檔處理 Pipeline                        │
│  ├─ 載入文檔                                         │
│  ├─ 清理格式                                         │
│  ├─ 切分 chunks (500 tokens, overlap 50)            │
│  └─ 提取 metadata                                    │
└──────────────────┬──────────────────────────────────┘
                   │
                   ▼
┌─────────────────────────────────────────────────────┐
│           Embedding 服務                             │
│  OpenAI: text-embedding-3-small                     │
│  或 Cohere, HuggingFace, 本地模型                    │
└──────────────────┬──────────────────────────────────┘
                   │
                   ▼
┌─────────────────────────────────────────────────────┐
│            向量資料庫                                 │
│  Pinecone / ChromaDB / Weaviate / Milvus           │
│                                                      │
│  儲存:                                               │
│  - 向量 (1536 維)                                    │
│  - 原始文字                                          │
│  - Metadata                                          │
│                                                      │
│  索引: HNSW / IVF 用於快速搜尋                       │
└──────────────────┬──────────────────────────────────┘
                   │
                   ▼
       ┌───────────────────────┐
       │   用戶查詢              │
       └───────────┬───────────┘
                   │
                   ▼
       ┌───────────────────────┐
       │  查詢向量化             │
       └───────────┬───────────┘
                   │
                   ▼
       ┌───────────────────────┐
       │  向量相似度搜尋         │
       │  返回 Top-K chunks     │
       └───────────┬───────────┘
                   │
                   ▼
       ┌───────────────────────┐
       │  構建 Prompt           │
       │  (查詢 + 檢索結果)      │
       └───────────┬───────────┘
                   │
                   ▼
       ┌───────────────────────┐
       │  LLM 生成答案          │
       └───────────┬───────────┘
                   │
                   ▼
       ┌───────────────────────┐
       │  返回給用戶             │
       └───────────────────────┘
````

## 總結

**你的理解是對的！**核心流程就是：

1. **文檔** → 切分成小片段
2. **小片段** → 轉成向量（數字陣列）
3. **向量 + 原文** → 存入向量資料庫
4. **用戶問題** → 也轉成向量
5. **向量搜尋** → 找出最相似的片段
6. **片段 + 問題** → 組成 prompt 給 LLM
7. **LLM** → 基於檢索到的內容生成答案

**關鍵優勢：**

- 更新文檔時，只需重新處理該文檔並更新向量庫
- 無需修改代碼或 prompt
- 可以隨時增量添加新內容

**技術成本：**

- 需要 Embedding API（有費用）
- 需要向量資料庫（儲存和計算）
- 有處理延遲（分鐘級）

  

[Claude is AI and can make mistakes. Please double-check responses.](https://support.anthropic.com/en/articles/8525154-claude-is-providing-incorrect-or-misleading-responses-what-s-going-on)