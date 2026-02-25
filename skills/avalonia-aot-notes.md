# Skill: .NET Native AOT (Windows) 通用編譯注意事項與檢查清單

## 目的

當使用者執行「AOT 編譯檢查」、「Native AOT 疑難排解」或「產生 AOT 配置建議」時，應對比標準成功案例（如 `AOBMaker`）與修復案例（如 `UE5CEDumper`），提供精準的技術指導。核心目標是**消除 AOT 運行時產生的不可預期崩潰**。

## 執行步驟

### 1. 靜態程式碼與配置分析

優先讀取專案中與 AOT 行為直接相關的檔案：

* **專案檔 (`.csproj`)**：檢查屬性標籤是否符合 AOT 嚴格要求。
* **進入點 (`Program.cs`)**：檢查初始化階段是否有反射載入或平台衝突程式碼。
* **視窗與 UI 邏輯 (`App.axaml.cs` 或 `MainWindow.xaml.cs`)**：檢查資料繫結（Data Binding）與驗證邏輯。

### 2. 生成注意事項清單 (依重要程度排序)

#### A. 專案配置屬性 (`.csproj`)

* **`PublishAot=true`**：啟用 AOT 編譯的核心開關。
* **`BuiltInComInteropSupport=false`**：在 AOT 下必須關閉，否則編譯器無法處理 COM 相互操作。
* **`TrimMode` 選擇**：
* `partial`：預設較安全，僅修剪未引用的組件。
* `full`：體積最小但風險最高，**必須**搭配 `RdXmlFile` 或 `TrimmerRootAssembly`。

* **`StackTraceSupport`**：若需要除錯 AOT 崩潰，應設為 `true`，否則崩潰時不會有檔名與行號。
* **`NoWarn IL3053;IL2104`**：明確列出需要忽略的警告原因，避免編譯日誌過於雜亂。

#### B. 程式碼相容性 (Trimming & Reflection)

* **禁止 `dynamic` 關鍵字**：AOT 無法在運行時生成類型，使用 `dynamic` 必崩潰。
* **反射替代方案**：
* 禁止使用 `Type.GetType("ClassName")`。
* 應改用 `typeof(ClassName)` 或 Source Generators。

* **ViewLocator 修改**：
* 將「反射式自動尋找 View」改為「明確的 `switch` 或 `if-else` 分支」。

#### C. Windows 平台與 UI 特有問題 (Avalonia/WinUI)

* **Composition Mode 崩潰**：在 AOT 環境下，`Win32CompositionMode` 通常需設為 `RedirectionSurface` 以避開 AOT 不支援的 COM interop 渲染路徑。
* **日誌限制**：`LogToTrace()` 等依賴反射的偵錯工具應在 AOT 下停用，或改用強型別的 Log 系統。

#### D. 崩潰診斷技術

* **Step Logging**：引導使用者在 `Main` 函數的第一行開始插入 `Console.WriteLine`，定位到底是「載入 DLL 失敗」還是「靜態建構子崩潰」。
* **外部相依性檢索**：檢查輸出的 `publish` 資料夾是否缺少必要的 Native DLL（如 `libHarfBuzzShard.dll` 等）。

### 3. 輸出規範

輸出內容必須包含以下三大部分：

1. **診斷表格**：
| 典型錯誤現象 | 可能原因 | 修正方法 |
| :--- | :--- | :--- |
| 啟動後直接消失（無視窗） | COM Interop 或靜態建構子報錯 | 檢查 `BuiltInComInteropSupport` |
| Binding 失敗 | 反射被修剪（Trimmed） | 增加 `TrimmerRootAssembly` |
| 找不到特定 View | 反射式 ViewLocator 失敗 | 改用 Explicit ViewLocator |
2. **配置範本**：提供一個「萬用安全型」的 `.csproj` 配置片段。
3. **警告標籤說明**：解釋為何需要在程式碼中使用 `[UnconditionalSuppressMessage]`，以及如何標註 `[DynamicallyAccessedMembers]`。