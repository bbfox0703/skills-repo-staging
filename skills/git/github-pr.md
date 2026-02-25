# Skill: 一鍵提交與發布 PR (Commit, Push & PR)

## 目的
當使用者要求「commit, push & PR」或類似的一條龍提交流程時，請自動執行從暫存變更到建立 GitHub Pull Request 的所有步驟。

## 執行先決條件
- 系統必須已安裝並登入 GitHub CLI (`gh`)。

## 執行步驟 (嚴格遵守順序)
1. **呼叫 Commit 技能**：先執行專案中或全域的 Git Commit 邏輯，分析 `git diff` 並產生符合 Conventional Commits 規範的提交，並請使用者確認。
2. **獲取分支資訊**：執行 `git branch --show-current` 確認目前分支名稱。如果是在 `main` 或 `master` 分支，請警告使用者並停止操作。
3. **推送到遠端 (Push)**：
   - 執行 `git push -u origin <目前分支名稱>`。
4. **草擬 PR 內容**：
   - **Title**: 直接使用剛才的 Commit 標題 (例如 `feat: add memory scanner`)。
   - **Body**: 根據 Commit 的變更內容，用繁體中文寫一段簡短的 PR 描述。包含：
     - 修改了什麼？
     - 為什麼要這樣改？
5. **建立 Pull Request**：
   - 詢問使用者：「即將使用以下標題與內容建立 PR，是否繼續？」
   - 獲得同意後，執行指令：`gh pr create --title "<Title>" --body "<Body>"`
6. **提供結果**：建立完成後，將 PR 的網址提供給使用者。