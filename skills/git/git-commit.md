# Skill: 專業 Git Commit 助理

## 目的
當使用者要求「commit」、「提交程式碼」或「產生 commit message」時，請嚴格遵循此技能的步驟，分析程式碼變更並產生符合規範的提交訊息。

## 執行步驟 (Claude 必須遵守的流程)
1. **檢查狀態與分支**：先在終端機執行 `git status` 了解檔案狀態，並執行 `git branch --show-current` 取得目前所在的分支名稱。
2. **分析分支意圖**：
   - 如果分支名稱包含 `feat/`, `feature/` 前綴，草擬的 Commit Type 應優先考慮 `feat`。
   - 如果分支名稱包含 `fix/`, `bugfix/` 前綴，應優先考慮 `fix`。
   - 如果分支名稱包含特定的任務編號（例如 `#12`, `ISSUE-45`），請將該編號放在 Commit 訊息的結尾，例如：`... (Resolves #12)`。
3. **讀取變更**：執行 `git diff --cached` (如果已經 add) 或 `git diff` (如果還沒 add) 來讀取實際的程式碼變動。
4. **分析與草擬**：根據變更內容，草擬一個符合「Conventional Commits (約定式提交)」規範的訊息。
5. **使用者確認**：將草擬的訊息呈現給使用者，並詢問：「是否使用此訊息進行提交？」
6. **執行提交**：使用者同意後，執行 `git commit -m "<標題>" -m "<詳細說明>"`。

## 提交訊息格式規範 (Conventional Commits)
必須包含 `<type>(<scope>): <subject>` 的標題，以及選擇性的 Body 說明。

* **Type 選項**：
  * `feat`: 新增功能 (例如：新增 C++ Hook 邏輯、新的 Lua API)
  * `fix`: 修復 Bug (例如：修正記憶體位址抓取錯誤)
  * `refactor`: 重構 (既不是新增功能，也不是修復 bug 的程式碼變動)
  * `docs`: 只有修改文件 (例如更新 README 或 API 說明)
  * `chore`: 建置過程或輔助工具的變動 (例如修改編譯腳本、更新 submodule)
  * `perf`: 提升效能的程式碼變動

* **Scope (選填)**：標示影響的範圍 (例如：`core`, `lua-engine`, `gui`, `docs`)。

* **Subject 規範**：
  * 使用英文撰寫，動詞以原形開頭 (例如：Use, Add, Change，而不是 Added 或 Adds)。
  * 不要超過 50 個字元。
  * 結尾不要加句號。

* **Body 規範 (如果變更很複雜)**：
  * 解釋「為什麼」要做這個變更，以及與先前邏輯的差異。
  * 可以使用繁體中文進行詳細說明，以利後續團隊或自己回顧。

## 範例
`feat(memory): add pattern scanning for dynamic offsets`

`新增了針對動態記憶體位移的特徵碼掃描功能。這解決了遊戲更新後硬編碼 (hardcoded) 位址失效的問題。`