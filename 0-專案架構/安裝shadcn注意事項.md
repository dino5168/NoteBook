# 安裝 shadcn/ui 注意事項

## 問題描述

執行 `npx shadcn@latest init` 時，可能會遇到以下錯誤：

```
✖ Validating import alias.
No import alias found in your tsconfig.json file.
```

## 解決方案

### 1. 安裝必要的依賴

首先安裝 Node.js 的 TypeScript 型別定義：

```bash
npm install -D @types/node
```

**說明：**
- `-D` 是 `--save-dev` 的簡寫，表示安裝為開發依賴
- `@types/node` 提供 Node.js 的 TypeScript 型別定義
- 這讓你能在 TypeScript 中使用 `path` 等 Node.js 模組

### 2. 配置 vite.config.ts

在 `vite.config.ts` 中添加路徑別名配置：

```typescript
import { defineConfig } from 'vite'
import path from "path"
import react from '@vitejs/plugin-react'
import tailwindcss from '@tailwindcss/vite'

export default defineConfig({
  plugins: [react(), tailwindcss()],
  resolve: {
    alias: {
      "@": path.resolve(__dirname, "./src"),
    },
  },
})
```

### 3. 配置 tsconfig.json

在**根目錄**的 `tsconfig.json` 中添加以下配置：

```jsonc
{
  "files": [],
  "references": [
    { "path": "./tsconfig.app.json" },
    { "path": "./tsconfig.node.json" }
  ],
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@/*": ["./src/*"]
    }
  }
}
```

**重點：**
- `paths` 和 `baseUrl` 必須放在 `compilerOptions` 裡面
- `baseUrl` 設為 `"."`（當前目錄）
- `paths` 定義 `@/*` 對應到 `./src/*`

### 4. 配置 tsconfig.app.json

同時在 `tsconfig.app.json` 中也添加相同配置：

```jsonc
{
  "compilerOptions": {
    // ... 其他配置
    
    /* Path Alias */
    "baseUrl": ".",
    "paths": {
      "@/*": ["./src/*"]
    }
  },
  "include": ["src"]
}
```

### 5. 執行安裝

完成上述配置後，執行：

```bash
npx shadcn@latest init
```

應該會看到：

```
✔ Preflight checks.
✔ Verifying framework. Found Vite.
✔ Validating Tailwind CSS config.
✔ Validating import alias.
```

## 常見錯誤

### 錯誤 1：paths 配置位置錯誤

❌ **錯誤寫法：**
```json
{
  "files": [],
  "paths": {
    "@/*": ["./src/*"]
  }
}
```

✅ **正確寫法：**
```json
{
  "files": [],
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@/*": ["./src/*"]
    }
  }
}
```

### 錯誤 2：缺少 baseUrl

❌ **錯誤寫法：**
```json
{
  "compilerOptions": {
    "paths": {
      "@/*": ["./src/*"]
    }
  }
}
```

✅ **正確寫法：**
```json
{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@/*": ["./src/*"]
    }
  }
}
```

## 配置說明

### 什麼是 Path Alias（路徑別名）？

路徑別名讓你可以使用簡潔的導入路徑：

```typescript
// 使用別名（推薦）
import { Button } from "@/components/ui/button"

// 不使用別名
import { Button } from "../../components/ui/button"
```

### 為什麼需要同時配置兩個文件？

- **tsconfig.json** - shadcn 工具會檢查這個文件
- **tsconfig.app.json** - TypeScript 編譯器實際使用的配置
- 兩個都需要配置以確保工具和編譯器都能正確解析路徑

## 檢查配置的命令

```bash
# 查看所有 tsconfig 文件
ls tsconfig*.json

# 查看 tsconfig.json 內容
cat tsconfig.json

# 查看 tsconfig.app.json 內容
cat tsconfig.app.json

# Windows 用戶使用
type tsconfig.json
```

## 參考資源

- [shadcn/ui 官方安裝文檔](https://ui.shadcn.com/docs/installation/vite)
- [TypeScript Path Mapping](https://www.typescriptlang.org/docs/handbook/module-resolution.html#path-mapping)
