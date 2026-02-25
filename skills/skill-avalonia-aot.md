# Skill: Avalonia Windows Native AOT 配置規範

## 1. .csproj 必備設定
- `PublishAot=true`
- `BuiltInComInteropSupport=false` (防止 COM 崩潰)
- `TrimMode=partial` (初次部署建議)
- `StackTraceSupport=true` (便於 AOT 除錯)

## 2. 程式碼修正規則
- **Program.cs**: 必須加入 `.With(new Win32PlatformOptions { CompositionMode = [Win32CompositionMode.RedirectionSurface] })`。
- **ViewLocator**: 禁止使用反射 (Reflection)，必須改用顯式 `switch` 或 `if-else` 返回 View。
- **Validation**: `App.axaml.cs` 需調用 `DisableAvaloniaDataAnnotationValidation()` 並標註 `[UnconditionalSuppressMessage]`。

## 3. 禁止項目 (AOT Crashers)
- 嚴禁使用 `dynamic` 關鍵字。
- 嚴禁使用 `LogToTrace()`。
- 嚴禁在 Hot Path 使用 `Activator.CreateInstance`。

## 4. 診斷 SOP
- 啟動即崩潰時，優先在 `Program.Main` 插入 `Console.WriteLine` 進行定位。
- 檢查輸出資料夾是否缺失外部 DLL、例如 `libHarfBuzzSharp.dll`、`av_libglesv2.dll`、`libSkiaSharp.dll`。