# Skill: 專業 C++ MSVC 與 CMake 編譯 (Windows)

## 目的
當使用者要求「build」、「compile」或「編譯」時，請嚴格遵守此流程，建立實體批次檔以避免命令列跳脫字元導致 CMD 卡死。**絕對不要**隨意添加會干擾專案 CMakeLists 的全域編譯參數。

## 執行步驟
1. **建立建置腳本**：在專案根目錄建立 `build_msvc.bat`，內容如下：
```bat
@echo off
setlocal enabledelayedexpansion

:: 1. 尋找 Visual Studio 路徑
for /f "usebackq tokens=*" %%i in (`"%ProgramFiles(x86)%\Microsoft Visual Studio\Installer\vswhere.exe" -latest -property installationPath`) do (
    set "VS_PATH=%%i"
)

if not exist "!VS_PATH!\VC\Auxiliary\Build\vcvars64.bat" (
    echo [ERROR] 找不到 vcvars64.bat，請檢查 Visual Studio 安裝。
    exit /b 1
)

:: 2. 載入環境變數並執行編譯
echo [INFO] 正在載入 MSVC 環境...
call "!VS_PATH!\VC\Auxiliary\Build\vcvars64.bat"

echo [INFO] 執行 CMake 設定...
cmake -S . -B build -G Ninja -DCMAKE_BUILD_TYPE=Release

echo [INFO] 開始編譯...
cmake --build build --config Release
```

2. **執行建置**：在終端機執行 .\build_msvc.bat。
3. **讀取結果**：分析輸出日誌，若有錯誤請協助排除。