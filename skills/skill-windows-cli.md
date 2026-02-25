# Skill: Windows CLI 穩健執行規範

## 核心原則
在 Windows 環境下執行指令時，嚴禁使用複雜的單行 `&&` 串接命令。

## 強制執行步驟
1. **建立批次檔**：凡涉及環境變數 (如 `vcvars64.bat`) 或多步驟指令，必須先將內容寫入 `%LOCALATTDATA%\temp\run.bat` (或專案根目錄暫存檔)。
2. **絕對路徑**：寫入與呼叫時，路徑一律使用 Windows 格式絕對路徑 (例如 `C:\Users\...`)。
3. **單一呼叫**：執行指令僅限使用 `cmd.exe /c "C:\path\to\your.bat"`。

## 負面約束
- 禁止在終端機直接使用 `cmd /c "call ... && cmake ..."`。
- 禁止使用 `/c/Program Files/` 等 Bash 風格路徑呼叫 Windows 原生程式。