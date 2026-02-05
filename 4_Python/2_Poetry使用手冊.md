# Poetry 使用手冊

> Python 現代化專案管理工具完全指南

## 目錄
- [什麼是 Poetry](#什麼是-poetry)
- [安裝 Poetry](#安裝-poetry)
- [快速開始](#快速開始)
- [基本命令](#基本命令)
- [依賴管理](#依賴管理)
- [虛擬環境管理](#虛擬環境管理)
- [專案配置](#專案配置)
- [打包與發布](#打包與發布)
- [進階功能](#進階功能)
- [實戰範例](#實戰範例)
- [常見問題](#常見問題)
- [最佳實踐](#最佳實踐)

---

## 什麼是 Poetry

Poetry 是一個 Python 依賴管理和打包工具,它整合了:
- 📦 依賴管理 (像 pip)
- 🔒 版本鎖定 (像 npm 的 package-lock.json)
- 🏗️ 專案建置與打包 (像 setuptools)
- 🚀 發布到 PyPI (像 twine)
- 🌐 虛擬環境管理 (像 venv)

### 為什麼選擇 Poetry?

| 傳統方式 | Poetry |
|---------|--------|
| 多個工具 (pip, venv, setuptools, twine) | 一個工具搞定 |
| 多個配置文件 | 單一 pyproject.toml |
| 手動解決依賴衝突 | 自動依賴解析 |
| requirements.txt 不完整 | poetry.lock 完整鎖定 |

---

## 安裝 Poetry

### 官方推薦方式 (跨平台)

```bash
# Linux, macOS, Windows (WSL)
curl -sSL https://install.python-poetry.org | python3 -

# 或使用 pip (不推薦,但可用)
pip install poetry
```

### 驗證安裝

```bash
poetry --version
# 輸出: Poetry (version 1.7.0)
```

### 配置 Poetry

```bash
# 讓虛擬環境在專案目錄內創建 (推薦)
poetry config virtualenvs.in-project true

# 查看所有配置
poetry config --list

# 查看配置文件位置
poetry config --list --local
```

---

## 快速開始

### 創建新專案

```bash
# 方式 1: 創建新專案 (推薦)
poetry new my-project

# 生成結構:
# my-project/
# ├── pyproject.toml       # 專案配置
# ├── README.md            # 說明文件
# ├── my_project/          # 源碼目錄
# │   └── __init__.py
# └── tests/               # 測試目錄
#     └── __init__.py
```

```bash
# 方式 2: 在現有專案中初始化
cd existing-project
poetry init

# 會互動式引導你填寫專案資訊
```

### 安裝依賴

```bash
cd my-project

# 安裝所有依賴 (根據 pyproject.toml)
poetry install

# 這會:
# 1. 創建虛擬環境
# 2. 安裝所有依賴
# 3. 生成 poetry.lock 文件
```

### 運行程式

```bash
# 在虛擬環境中執行 Python
poetry run python my_project/main.py

# 進入虛擬環境的 shell
poetry shell

# 在 shell 中就可以直接使用
python my_project/main.py
pytest
```

---

## 基本命令

### 專案管理

```bash
# 創建新專案
poetry new <project-name>

# 初始化現有專案
poetry init

# 安裝依賴
poetry install

# 更新依賴
poetry update

# 查看專案資訊
poetry show
```

### 虛擬環境

```bash
# 進入虛擬環境
poetry shell

# 退出虛擬環境
exit  # 或 deactivate

# 在虛擬環境中執行命令
poetry run <command>

# 查看虛擬環境資訊
poetry env info

# 查看虛擬環境路徑
poetry env info --path

# 移除虛擬環境
poetry env remove <env-name>
```

### 依賴操作

```bash
# 添加依賴
poetry add <package>

# 添加開發依賴
poetry add --group dev <package>

# 移除依賴
poetry remove <package>

# 列出依賴
poetry show

# 列出依賴樹狀結構
poetry show --tree

# 查看特定套件資訊
poetry show <package>
```

---

## 依賴管理

### 添加依賴

```bash
# 添加最新版本
poetry add requests

# 指定版本
poetry add requests@2.28.0

# 指定版本範圍
poetry add "requests>=2.28.0,<3.0.0"

# 使用簡寫符號
poetry add "requests^2.28.0"  # >=2.28.0 <3.0.0
poetry add "requests~2.28.0"  # >=2.28.0 <2.29.0

# 添加多個套件
poetry add requests flask fastapi
```

### 開發依賴 (Development Dependencies)

```bash
# 添加到 dev 群組
poetry add --group dev pytest
poetry add --group dev black flake8 mypy

# 添加到自定義群組
poetry add --group test pytest-cov
poetry add --group docs sphinx

# 安裝時排除某些群組
poetry install --without dev
poetry install --without dev,test

# 只安裝特定群組
poetry install --only dev
```

### 從其他來源安裝

```bash
# 從 Git 倉庫
poetry add git+https://github.com/user/repo.git

# 從特定分支
poetry add git+https://github.com/user/repo.git#branch-name

# 從本地路徑
poetry add ./path/to/local/package

# 以可編輯模式安裝 (類似 pip install -e)
poetry add --editable ./path/to/local/package

# 從私有 PyPI 源
poetry add --source my-source package-name
```

### 更新依賴

```bash
# 更新所有依賴到最新版本
poetry update

# 更新特定套件
poetry update requests

# 更新多個套件
poetry update requests flask

# 鎖定依賴版本 (不更新,只更新 lock 文件)
poetry lock

# 不更新鎖定文件,只檢查
poetry lock --check
```

---

## 虛擬環境管理

### 基本操作

```bash
# 查看當前虛擬環境資訊
poetry env info

# 輸出範例:
# Virtualenv
# Python:         3.11.0
# Implementation: CPython
# Path:           /path/to/project/.venv

# 列出所有虛擬環境
poetry env list

# 使用特定 Python 版本
poetry env use python3.11
poetry env use 3.11
poetry env use /usr/bin/python3.11
```

### 虛擬環境配置

```bash
# 在專案目錄內創建虛擬環境 (推薦)
poetry config virtualenvs.in-project true

# 在系統目錄創建虛擬環境
poetry config virtualenvs.in-project false

# 設置虛擬環境存放路徑
poetry config virtualenvs.path /path/to/venvs

# 查看虛擬環境配置
poetry config virtualenvs.in-project
```

### 管理多個 Python 版本

```bash
# 創建 Python 3.10 環境
poetry env use 3.10

# 創建 Python 3.11 環境
poetry env use 3.11

# 列出所有環境
poetry env list
# 輸出:
# my-project-3.10
# my-project-3.11 (Activated)

# 移除特定環境
poetry env remove 3.10

# 移除所有環境
poetry env remove --all
```

---

## 專案配置

### pyproject.toml 結構

```toml
[tool.poetry]
name = "my-project"                    # 專案名稱
version = "0.1.0"                      # 版本號
description = "專案描述"               # 簡短描述
authors = ["Your Name <you@example.com>"]  # 作者資訊
readme = "README.md"                   # README 文件
license = "MIT"                        # 授權
homepage = "https://example.com"       # 專案首頁
repository = "https://github.com/user/repo"  # 倉庫地址
keywords = ["keyword1", "keyword2"]    # 關鍵字
classifiers = [                        # PyPI 分類
    "Programming Language :: Python :: 3",
    "License :: OSI Approved :: MIT License",
]

# 生產環境依賴
[tool.poetry.dependencies]
python = "^3.8"                        # Python 版本要求
requests = "^2.28.0"
fastapi = "^0.104.0"
pydantic = "^2.0.0"

# 開發依賴
[tool.poetry.group.dev.dependencies]
pytest = "^7.0"
black = "^22.0"
flake8 = "^6.0"
mypy = "^1.0"

# 測試依賴
[tool.poetry.group.test.dependencies]
pytest-cov = "^4.0"
pytest-mock = "^3.10"

# 文件依賴
[tool.poetry.group.docs.dependencies]
sphinx = "^7.0"
sphinx-rtd-theme = "^1.3"

# 腳本定義
[tool.poetry.scripts]
my-script = "my_project.main:run"      # 命令行腳本

# 建置系統
[build-system]
requires = ["poetry-core"]
build-backend = "poetry.core.masonry.api"
```

### 版本號規則

Poetry 使用 [Semantic Versioning](https://semver.org/):

```toml
[tool.poetry.dependencies]
# 精確版本
requests = "2.28.0"           # 必須是 2.28.0

# 插入符號 (Caret) - 推薦
requests = "^2.28.0"          # >=2.28.0 <3.0.0

# 波浪符號 (Tilde)
requests = "~2.28.0"          # >=2.28.0 <2.29.0
requests = "~2.28"            # >=2.28.0 <2.29.0

# 通配符
requests = "2.*"              # >=2.0.0 <3.0.0
requests = "2.28.*"           # >=2.28.0 <2.29.0

# 比較運算符
requests = ">=2.28.0"         # 大於等於
requests = ">2.28.0,<3.0.0"  # 範圍

# 最新版本
requests = "*"                # 任意版本 (不推薦)
```

### 自定義腳本

```toml
[tool.poetry.scripts]
# 定義命令行工具
my-cli = "my_project.cli:main"
serve = "my_project.server:run"
```

使用:
```bash
poetry run my-cli --help
poetry run serve
```

---

## 打包與發布

### 打包專案

```bash
# 打包成 wheel 和 source distribution
poetry build

# 只打包 wheel
poetry build -f wheel

# 只打包 source distribution
poetry build -f sdist

# 打包結果在 dist/ 目錄:
# dist/
# ├── my_project-0.1.0-py3-none-any.whl
# └── my_project-0.1.0.tar.gz
```

### 發布到 PyPI

```bash
# 配置 PyPI 憑證
poetry config pypi-token.pypi <your-token>

# 或使用用戶名密碼
poetry config http-basic.pypi <username> <password>

# 發布 (會先打包)
poetry publish

# 或分開執行
poetry build
poetry publish

# 發布到測試 PyPI
poetry config repositories.testpypi https://test.pypi.org/legacy/
poetry publish -r testpypi

# 乾運行 (不實際發布)
poetry publish --dry-run
```

### 版本管理

```bash
# 手動修改版本
poetry version 0.2.0

# 使用規則升級版本
poetry version patch      # 0.1.0 -> 0.1.1
poetry version minor      # 0.1.0 -> 0.2.0
poetry version major      # 0.1.0 -> 1.0.0

# 預發布版本
poetry version prepatch   # 0.1.0 -> 0.1.1-alpha.0
poetry version preminor   # 0.1.0 -> 0.2.0-alpha.0
poetry version premajor   # 0.1.0 -> 1.0.0-alpha.0

# 查看當前版本
poetry version
```

---

## 進階功能

### 依賴群組管理

```toml
[tool.poetry.group.dev.dependencies]
pytest = "^7.0"

[tool.poetry.group.test.dependencies]
pytest-cov = "^4.0"

[tool.poetry.group.docs.dependencies]
sphinx = "^7.0"
```

```bash
# 安裝所有群組 (預設)
poetry install

# 排除某些群組
poetry install --without dev
poetry install --without dev,docs

# 只安裝主依賴 (不含任何群組)
poetry install --only main

# 只安裝特定群組
poetry install --only dev
poetry install --only dev,test

# 同時使用 with 和 without
poetry install --with docs --without test
```

### 多源配置

```toml
[[tool.poetry.source]]
name = "private"
url = "https://pypi.example.com/simple"
priority = "primary"

[[tool.poetry.source]]
name = "PyPI"
priority = "supplemental"
```

```bash
# 從特定源安裝
poetry add --source private my-private-package
```

### 插件系統

```bash
# 安裝插件
poetry self add poetry-plugin-export

# 使用插件功能
poetry export -f requirements.txt --output requirements.txt

# 列出已安裝插件
poetry self show plugins
```

### 使用 poetry.lock

```bash
# 根據 lock 文件精確安裝
poetry install

# 更新 lock 文件但不安裝
poetry lock

# 檢查 lock 文件是否需要更新
poetry lock --check

# 強制更新 lock 文件
poetry lock --no-update
```

---

## 實戰範例

### 範例 1: 創建 Web API 專案

```bash
# 1. 創建專案
poetry new fastapi-demo
cd fastapi-demo

# 2. 添加依賴
poetry add fastapi uvicorn pydantic
poetry add --group dev pytest httpx black

# 3. 安裝
poetry install

# 4. 創建主程式
cat > fastapi_demo/main.py << 'EOF'
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
def read_root():
    return {"Hello": "World"}

if __name__ == "__main__":
    import uvicorn
    uvicorn.run(app, host="0.0.0.0", port=8000)
EOF

# 5. 運行
poetry run python fastapi_demo/main.py
```

**pyproject.toml**:
```toml
[tool.poetry]
name = "fastapi-demo"
version = "0.1.0"
description = "FastAPI demo project"
authors = ["Your Name <you@example.com>"]

[tool.poetry.dependencies]
python = "^3.8"
fastapi = "^0.104.0"
uvicorn = "^0.24.0"
pydantic = "^2.0.0"

[tool.poetry.group.dev.dependencies]
pytest = "^7.0"
httpx = "^0.25.0"
black = "^22.0"

[tool.poetry.scripts]
serve = "fastapi_demo.main:main"
```

### 範例 2: 管理多個本地套件

```bash
# 目錄結構:
# workspace/
# ├── lib-base/
# │   ├── pyproject.toml
# │   └── src/
# │       └── nio/
# │           └── textreader.py
# └── main-app/
#     ├── pyproject.toml
#     └── src/

# 在 lib-base 目錄
cd workspace/lib-base
poetry init
# ... 填寫資訊

# 在 main-app 目錄
cd workspace/main-app
poetry init
poetry add ../lib-base  # 添加本地依賴
```

**main-app/pyproject.toml**:
```toml
[tool.poetry.dependencies]
python = "^3.8"
lib-base = {path = "../lib-base", develop = true}
```

### 範例 3: 設置 CI/CD

**.github/workflows/test.yml**:
```yaml
name: Tests

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Install Poetry
        run: pipx install poetry
      
      - name: Set up Python
        uses: actions/setup-python@v4
        with:
          python-version: '3.11'
          cache: 'poetry'
      
      - name: Install dependencies
        run: poetry install
      
      - name: Run tests
        run: poetry run pytest
      
      - name: Run linting
        run: poetry run black --check .
```

### 範例 4: Monorepo 結構

```bash
# 目錄結構:
# my-monorepo/
# ├── pyproject.toml          # 主配置
# ├── packages/
# │   ├── package-a/
# │   │   ├── pyproject.toml
# │   │   └── src/
# │   └── package-b/
# │       ├── pyproject.toml
# │       └── src/
# └── poetry.lock
```

**主 pyproject.toml**:
```toml
[tool.poetry]
name = "my-monorepo"
version = "0.1.0"

[tool.poetry.dependencies]
python = "^3.8"
package-a = {path = "packages/package-a", develop = true}
package-b = {path = "packages/package-b", develop = true}
```

---

## 常見問題

### Q1: Poetry 安裝後找不到命令

**解決方案**:
```bash
# 添加到 PATH
export PATH="$HOME/.local/bin:$PATH"

# 或使用完整路徑
~/.local/bin/poetry --version

# macOS 可能在
/Users/username/Library/Python/3.x/bin/poetry
```

### Q2: 虛擬環境在哪裡?

```bash
# 查看路徑
poetry env info --path

# 如果想要在專案目錄內
poetry config virtualenvs.in-project true
poetry install  # 重新安裝會在專案內創建 .venv
```

### Q3: 如何匯出 requirements.txt?

```bash
# 安裝插件
poetry self add poetry-plugin-export

# 匯出
poetry export -f requirements.txt --output requirements.txt

# 匯出包含開發依賴
poetry export -f requirements.txt --with dev --output requirements.txt

# 不包含 hash
poetry export -f requirements.txt --without-hashes --output requirements.txt
```

### Q4: 依賴衝突怎麼辦?

```bash
# 查看依賴樹找出衝突
poetry show --tree

# 嘗試更新所有依賴
poetry update

# 手動指定版本
poetry add "package>=1.0,<2.0"

# 清除快取重試
poetry cache clear pypi --all
poetry install
```

### Q5: 如何從 requirements.txt 遷移?

```bash
# 1. 初始化 Poetry
poetry init

# 2. 添加依賴 (一次一個或批量)
cat requirements.txt | grep -v "^#" | xargs poetry add

# 或者手動添加
poetry add $(cat requirements.txt | grep -v "^#" | tr '\n' ' ')

# 3. 開發依賴
cat requirements-dev.txt | grep -v "^#" | xargs poetry add --group dev
```

### Q6: Poetry 太慢怎麼辦?

```bash
# 使用國內鏡像 (中國地區)
poetry source add --priority=primary tsinghua https://pypi.tuna.tsinghua.edu.cn/simple/

# 或使用阿里雲鏡像
poetry source add --priority=primary aliyun https://mirrors.aliyun.com/pypi/simple/

# 關閉並行安裝限制
poetry config installer.max-workers 10

# 清除快取
poetry cache clear pypi --all
```

### Q7: 在 Docker 中使用 Poetry

```dockerfile
FROM python:3.11-slim

# 安裝 Poetry
RUN pip install poetry

# 配置 Poetry
ENV POETRY_NO_INTERACTION=1 \
    POETRY_VIRTUALENVS_IN_PROJECT=1 \
    POETRY_VIRTUALENVS_CREATE=1 \
    POETRY_CACHE_DIR=/tmp/poetry_cache

WORKDIR /app

# 只複製依賴文件
COPY pyproject.toml poetry.lock ./

# 安裝依賴
RUN poetry install --without dev --no-root && rm -rf $POETRY_CACHE_DIR

# 複製程式碼
COPY . .

# 安裝專案本身
RUN poetry install --without dev

CMD ["poetry", "run", "python", "-m", "my_project"]
```

---

## 最佳實踐

### 1. 版本控制

```bash
# .gitignore
__pycache__/
*.py[cod]
*$py.class
.venv/
dist/
build/
*.egg-info/

# 提交這些文件
pyproject.toml    # 必須
poetry.lock       # 必須 (確保環境一致)
```

### 2. 依賴管理原則

- ✅ 使用插入符號 `^` 指定版本範圍
- ✅ 定期更新依賴 `poetry update`
- ✅ 區分開發和生產依賴
- ✅ 鎖定關鍵依賴的版本
- ❌ 避免使用 `*` 通配符

### 3. 專案結構

```
my-project/
├── pyproject.toml
├── poetry.lock
├── README.md
├── .gitignore
├── src/
│   └── my_project/
│       ├── __init__.py
│       ├── main.py
│       └── utils.py
├── tests/
│   ├── __init__.py
│   └── test_main.py
└── docs/
    └── index.md
```

使用 `src` 佈局:
```toml
[tool.poetry]
packages = [{include = "my_project", from = "src"}]
```

### 4. 測試覆蓋

```bash
# 添加測試工具
poetry add --group dev pytest pytest-cov

# 運行測試
poetry run pytest

# 生成覆蓋報告
poetry run pytest --cov=my_project --cov-report=html
```

### 5. 程式碼品質

```bash
# 添加工具
poetry add --group dev black flake8 mypy isort

# 格式化
poetry run black .

# 檢查
poetry run flake8 .
poetry run mypy src/

# 排序 imports
poetry run isort .
```

### 6. Pre-commit 整合

```yaml
# .pre-commit-config.yaml
repos:
  - repo: https://github.com/psf/black
    rev: 23.0.0
    hooks:
      - id: black
  
  - repo: https://github.com/pycqa/flake8
    rev: 6.0.0
    hooks:
      - id: flake8
```

```bash
poetry add --group dev pre-commit
poetry run pre-commit install
```

### 7. 文件化

```toml
[tool.poetry.group.docs.dependencies]
sphinx = "^7.0"
sphinx-rtd-theme = "^1.3"
```

```bash
# 初始化文件
poetry run sphinx-quickstart docs

# 生成文件
poetry run sphinx-build docs docs/_build
```

### 8. 腳本管理

```toml
[tool.poetry.scripts]
# CLI 工具
my-cli = "my_project.cli:main"

# 開發腳本
dev = "my_project.dev:run_dev_server"
test = "pytest:main"
lint = "my_project.scripts:run_linters"
```

---

## 快速參考

### 常用命令速查

```bash
# 專案初始化
poetry new <name>          # 新專案
poetry init                # 現有專案

# 依賴管理
poetry add <pkg>           # 添加依賴
poetry add -G dev <pkg>    # 添加開發依賴
poetry remove <pkg>        # 移除依賴
poetry update              # 更新依賴
poetry install             # 安裝依賴

# 虛擬環境
poetry shell               # 進入環境
poetry run <cmd>           # 執行命令
poetry env info            # 環境資訊
poetry env list            # 列出環境

# 打包發布
poetry build               # 打包
poetry publish             # 發布
poetry version <rule>      # 版本管理

# 資訊查詢
poetry show                # 列出依賴
poetry show --tree         # 依賴樹
poetry check               # 檢查配置
```

### 配置速查

```bash
# 虛擬環境配置
poetry config virtualenvs.in-project true
poetry config virtualenvs.path <path>

# PyPI 配置
poetry config pypi-token.pypi <token>
poetry config repositories.<name> <url>

# 快取管理
poetry cache list
poetry cache clear <cache> --all
```

---

## 總結

Poetry 是現代 Python 專案的最佳選擇:

✅ **統一工具鏈**: 一個工具搞定所有事情  
✅ **依賴解析**: 自動處理版本衝突  
✅ **可重現性**: lock 文件確保環境一致  
✅ **現代標準**: 使用 pyproject.toml (PEP 518)  
✅ **活躍社群**: 持續更新和改進  

### 學習路徑

1. **初學者**: 學會創建專案、添加依賴、運行程式
2. **進階**: 掌握依賴群組、版本管理、虛擬環境配置
3. **專家**: 使用插件、Monorepo、CI/CD 整合

### 相關資源

- 📚 [官方文件](https://python-poetry.org/docs/)
- 🐙 [GitHub 倉庫](https://github.com/python-poetry/poetry)
- 💬 [Discord 社群](https://discord.com/invite/awxPgve)
- 📖 [PyPI](https://pypi.org/project/poetry/)

---

**Happy coding with Poetry! 🎉**

### 將虛擬路徑建立在 目錄下
``` bash
poetry config virtualenvs.in-project true
```
