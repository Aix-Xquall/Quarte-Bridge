# EDA-Agent MCP 安裝與使用

EDA-Agent MCP 是選用的 Altium 自動化工具。單純開啟、檢視或編輯本 repo 的電路，可直接使用 Altium Designer；需要 AI 透過 MCP 讀取或修改設計時，再設定以下環境。

上游：[salitronic/eda-agent](https://github.com/salitronic/eda-agent)。本文依本機已驗證的安裝及上游 README 整理；本機版本基準為 eda-agent 0.8.1、橋接腳本 2026.10.30.7、Altium Designer 23.3.1.30。新安裝取得的版本可能不同，Python 套件與 DelphiScript 應來自同一次安裝。

## 連線需要的三個部分

| 部分 | 執行位置 | 用途 |
|---|---|---|
| MCP 客戶端 | 使用者的 AI／MCP 應用程式 | 啟動 Python stdio server，呼叫工具 |
| eda-agent Python 程式 | Windows 本機 | 將工具呼叫轉成工作區中的橋接請求 |
| `StartMCPServer` | Altium Designer 內 | 執行請求並回傳結果 |

`Altium_API.PrjScr` 是控制工具的腳本專案；`RevK/*.PrjPcb`、`verL/*.PrjPcb` 才是電路專案，兩者用途不同。

## 已經有可用環境：日常啟動

本機既有 Python 環境、橋接腳本與 MCP 客戶端設定可以繼續使用。合併電路工作目錄不代表必須搬動 Python 環境或 IPC 工作區。

1. 啟動 Altium Designer，開啟本 repo 的電路專案：`RevK/QuarterBridge_RevK.PrjPcb` 或 `verL/QuarterBridge_verL.PrjPcb`。
2. 在 Altium 的 **File → Run Script…**，選擇已安裝的 `Altium_API.PrjScr`，展開 `Dispatcher.pas`，執行 **StartMCPServer**。
3. 在 MCP 客戶端連接既有的 eda-agent server。客戶端會以 stdio 啟動 Python 程式，這與 Altium 內的輪詢腳本是兩個不同程序。
4. 請 AI 先呼叫 `app_ping`、`app_list_documents`，並回報目標文件完整路徑；確認指向本 repo 下的正確版本後，再操作電路。

若本機根目錄已經有可用的 `Altium_API.PrjScr`，可以沿用；這些本機工具檔由 `.gitignore` 排除，其他人 clone repo 時不會取得。

## 新電腦：安裝 Python 與橋接腳本

需求：Windows、已安裝且可執行腳本的 Altium Designer、Python 3.11 以上、Git，以及支援本機 stdio MCP 的客戶端。以下命令在 PowerShell 執行，將工具放在本機 `%LOCALAPPDATA%\EDA-Agent`，與電路 repo 分開。安裝流程依[上游安裝說明](https://github.com/salitronic/eda-agent#installation)。

第一次安裝：

```powershell
py -3 --version

$edaRoot = Join-Path $env:LOCALAPPDATA 'EDA-Agent'
New-Item -ItemType Directory -Path $edaRoot -Force | Out-Null
git clone https://github.com/salitronic/eda-agent.git (Join-Path $edaRoot 'src')
py -3 -m venv (Join-Path $edaRoot '.venv')

$edaPython = Join-Path $edaRoot '.venv\Scripts\python.exe'
& $edaPython -m pip install -e (Join-Path $edaRoot 'src')

$env:EDA_AGENT_WORKSPACE = Join-Path $edaRoot 'workspace'
$env:EDA_AGENT_UPDATE_CHECK = '0'
$env:EDA_AGENT_DISABLE_DASHBOARD = '1'
$env:PYTHONUTF8 = '1'
$env:PYTHONIOENCODING = 'utf-8'

& $edaPython -m eda_agent install-scripts --dest (Join-Path $edaRoot 'altium-scripts')
& $edaPython -m eda_agent health
```

先確認第一行顯示 Python 3.11 以上；若沒有 `py`，改用已安裝的合適版本 `python`。若 `src` 已存在，先檢查是否為原有安裝，不要重複 clone 到同一個目錄。

這些 `$env:` 設定只作用於目前 PowerShell；MCP 客戶端還需設定相同的環境變數。

接著在 Altium 註冊腳本專案：

1. 進入 **Preferences → Scripting System → Global Projects**；不同 Altium 版本的 Preferences 入口可能不同。
2. 使用 **Install from file** 選取剛安裝的 `%LOCALAPPDATA%\EDA-Agent\altium-scripts\Altium_API.PrjScr`。
3. 使用 **File → Run Script… → Dispatcher.pas → StartMCPServer → Run** 啟動。

## 設定 MCP 客戶端

使用支援本機 stdio server 的設定欄位：

| 欄位 | 設定 |
|---|---|
| command | 該虛擬環境 `python.exe` 的完整路徑 |
| args | `-m`、`eda_agent`、`--no-dashboard` |
| `EDA_AGENT_WORKSPACE` | 與安裝／啟動橋接時一致的工作區完整路徑 |
| `EDA_AGENT_UPDATE_CHECK` | `0`，停用自動背景更新檢查 |
| `EDA_AGENT_DISABLE_DASHBOARD` | `1`，不啟動選用的網頁儀表板 |
| `PYTHONUTF8` | `1` |
| `PYTHONIOENCODING` | `utf-8` |

使用 TOML 設定的客戶端可參考 [mcp-config.example.toml](mcp-config.example.toml)。範例中的 `YOUR_USER` 必須換成自己的使用者目錄；`command` 與工作區均要填實際完整路徑，不要假設客戶端會展開 `%LOCALAPPDATA%` 或 PowerShell 變數。

若已有可用的 `altium_test` 或其他 server 名稱，可保留原名稱；名稱不影響電路專案。請將範例合併到客戶端的既有設定中，勿覆蓋整份設定。設定後重新連接該 MCP server。

stdio server 是由客戶端啟動並等待協定訊息的程序；在終端機直接啟動後持續等待，不代表安裝失敗。

## 連線檢查

新 PowerShell 視窗需重新設定變數。先在 Altium 啟動 `StartMCPServer`，再執行：

```powershell
$edaRoot = Join-Path $env:LOCALAPPDATA 'EDA-Agent'
$edaPython = Join-Path $edaRoot '.venv\Scripts\python.exe'
$env:EDA_AGENT_WORKSPACE = Join-Path $edaRoot 'workspace'
$env:PYTHONUTF8 = '1'
$env:PYTHONIOENCODING = 'utf-8'

& $edaPython -m eda_agent health
& $edaPython -m eda_agent doctor --json
```

`health` 檢查本機設定與腳本；`doctor` 另外檢查 Altium 程序、輪詢回應、版本與工具往返。實際 MCP 連線成功還應由客戶端呼叫 `app_ping` 驗證。

只有需要重新啟動橋接時，才執行：

```powershell
& $edaPython -m eda_agent relaunch --restart --prjscr (Join-Path $edaRoot 'altium-scripts\Altium_API.PrjScr')
```

此命令會重啟正在使用相同橋接的輪詢程序；若原本沿用其他位置的腳本，請指定原本那一份 `.PrjScr`。

## 使用本 repo

可直接向 AI 提出以下請求：

- 「開啟目前 repo 的 verL 專案，列出已開啟文件及完整路徑。」
- 「列出 J1、J2 的焊墊與對應網路，以及目前 Routing Rules。」
- 「執行原生 ERC／DRC，分別列出新增與既有問題。」
- 「修改前先備份；保持單數通道上層、雙數下層與零訊號過孔，完成後儲存並檢查連通。」

工具依文件類型分組：`sch_` 操作原理圖、`pcb_` 操作 PCB、`lib_` 操作元件庫、`proj_` 操作專案。每次操作前確認完整文件路徑，尤其同時開啟 RevK 與 verL 時。

工作完成後可請 AI 呼叫 `app_detach` 釋放 Altium 的腳本引擎。此工具可能儲存被工具修改過的文件；下一次控制前需再啟動 `StartMCPServer`。

verL 目前既有 66 項 AGND 未連線。MCP 連線正常、ERC 為 0，均不代表 PCB 已達製板完成狀態；設計狀態見[版本比較](version-comparison.md)。

## 常見問題

| 現象 | 檢查方式 |
|---|---|
| Altium 已啟動，但 `script_loaded=false` 或逾時 | 執行正確 `.PrjScr` 的 `StartMCPServer`，確認沒有阻擋中的對話框 |
| Python 程式正常，Altium 卻沒有收到命令 | 核對雙端工作區。標準腳本啟動時讀取 `C:\ProgramData\eda-agent\workspace-path.txt`；該指標內容應與客戶端 `EDA_AGENT_WORKSPACE` 一致 |
| 更換工作區後仍逾時 | 舊輪詢程序在啟動時已讀取舊路徑；先停止舊程序，再用同一工作區重新部署／啟動 |
| 中文路徑或輸出亂碼 | 使用 Python 完整路徑，設定 `PYTHONUTF8=1` 與 `PYTHONIOENCODING=utf-8` |
| 套件或腳本版本不一致 | 用同一個虛擬環境執行 `install-scripts` 與 MCP server，更新後重連客戶端及橋接 |
| Altium 顯示「Another script executing」或原生功能暫時無法使用 | 完成操作後 detach；若腳本已停在錯誤處，先在 Script IDE 按 Stop，再重新啟動 |
| 可讀取但無法修改或儲存 | 檢查橋接狀態視窗的編輯／儲存開關及文件存取權限 |
| 工具作用在另一個版本 | 先列出文件路徑，再明確指定並啟用目標 SchDoc／PcbDoc／元件庫 |

若改動 `EDA_AGENT_WORKSPACE`，也可能影響同一台機器上的其他 EDA-Agent 連線。保持一組明確的 Python、工作區及橋接腳本設定。

## 哪些檔案納入版控

本 repo 收錄本說明與不含私人路徑的設定範例。EDA-Agent 原始碼、虛擬環境、安裝出的 `.pas`／`.PrjScr`、IPC 工作區、日誌與使用者客戶端設定維持本機安裝，由 `.gitignore` 排除。

只有本電路專用且要重複使用的自動化腳本，才另行評估收錄。一般使用不需將 EDA-Agent 整套複製進本 repo。
