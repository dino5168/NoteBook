
### 使用 .env .env.local 產製 config.py 預設值為 app/config.py

```python
# 使用 .env 檔案自動生成 Pydantic Settings 類別的腳本   .env.local 優先於 .env 只產製型態
import os
import argparse
from dotenv import dotenv_values
from typing import List

def generate_settings_class(env_files: List[str], output_file: str = "config.py"):
    """
    根據多個 .env 檔案生成 Pydantic Settings 類別
    Args:
        env_files: .env 檔案列表
        output_file: 輸出的檔案名稱
    """
    # 1. 分別讀取各個 .env 檔案
    base_vars = {}  # .env 的變數（帶預設值）
    local_vars = set()  # .env.local 的變數名稱（只記錄key）
    all_vars = {}  # 所有變數用於型別判斷
    loaded_files = []
    for env_file in env_files:
        if os.path.exists(env_file):
            file_vars = dotenv_values(env_file)
            all_vars.update(file_vars)
            # 判斷是否為 .env.local 檔案
            if '.local' in env_file:
                local_vars.update(file_vars.keys())
            else:
                base_vars.update(file_vars)
            loaded_files.append(env_file)
            print(f"📄 已讀取: {env_file} ({len(file_vars)} 個變數)")
        else:
            print(f"⚠️  檔案不存在，跳過: {env_file}")
    if not base_vars and not local_vars:
        print("❌ 沒有找到任何環境變數，請檢查檔案路徑")
        return

    # 2. 生成 config.py 內容

    lines = [

        "from pydantic_settings import BaseSettings, SettingsConfigDict",

        "from typing import Optional\n",

        "class Settings(BaseSettings):",

        f'    model_config = SettingsConfigDict(env_file={loaded_files}, extra="ignore")\n'

    ]
    # 處理所有變數

    processed_keys = set()

    # 先處理 .env.local 的變數（只有型別，無預設值）

    for key in sorted(local_vars):

        value = all_vars.get(key, "")

        # 簡單的型別判斷

        if value.isdigit():

            v_type = "int"

        elif value.lower() in ["true", "false"]:

            v_type = "bool"

        else:

            v_type = "str"

        # 只有型別註解，沒有預設值

        lines.append(f"    {key.upper()}: {v_type}")

        processed_keys.add(key)

    # 再處理 .env 的變數（帶預設值）

    for key, value in sorted(base_vars.items()):

        # 跳過已經在 .env.local 中處理過的變數

        if key in processed_keys:

            continue

        # 簡單的型別判斷

        if value.isdigit():

            v_type = "int"

        elif value.lower() in ["true", "false"]:

            v_type = "bool"

        else:

            v_type = "str"

        # 生成屬性列（帶預設值）

        lines.append(f"    {key.upper()}: {v_type} = \"{value}\"")

  

    lines.append("\nsettings = Settings()")

  

    # 3. 寫入檔案

    with open(output_file, "w", encoding="utf-8") as f:

        f.write("\n".join(lines))

    total_vars = len(local_vars) + len(base_vars) - len(local_vars & set(base_vars.keys()))

    print(f"\n✅ {output_file} 已根據 {len(loaded_files)} 個檔案自動生成成功！")

    print(f"📋 總共 {total_vars} 個環境變數")

    print(f"   - .env.local 變數（無預設值）: {len(local_vars)} 個")

    print(f"   - .env 變數（有預設值）: {len(base_vars) - len(local_vars & set(base_vars.keys()))} 個")

  

if __name__ == "__main__":

    parser = argparse.ArgumentParser(description="根據 .env 檔案自動生成 Pydantic Settings 配置")

    parser.add_argument(

        'files',

        nargs='*',

        default=['.env', '.env.local'],

        help='.env 檔案列表（預設: .env）'

    )

    parser.add_argument(

        '-o', '--output',

        default='app/config.py',

        help='輸出檔案名稱（預設: config.py）'

    )

    args = parser.parse_args()

    generate_settings_class(args.files, args.output)
```
