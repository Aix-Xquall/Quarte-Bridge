# Quarte-Bridge

16 通道 Quarter Bridge 電路的 Altium Designer 設計來源。本機以 `Quarter Bridge` 同時作為工作目錄與 Git 根目錄，GitHub repository 名稱為 `Quarte-Bridge`。不同接頭／板型直接放在根目錄的 `RevK/`、`verL/`，各版本使用自己的元件庫。

```text
Quarter Bridge/
├─ README.md
├─ .gitignore
├─ .gitattributes
├─ RevK/
├─ verL/
└─ docs/
```

| 版本 | 專案入口 | 內容 |
|---|---|---|
| RevK | [QuarterBridge_RevK.PrjPcb](RevK/QuarterBridge_RevK.PrjPcb) | 50 pin／78 pin D-sub 接頭配置 |
| verL | [QuarterBridge_verL.PrjPcb](verL/QuarterBridge_verL.PrjPcb) | 51 pin BR／100 pin CBR Micro-D 接頭配置、獨立機殼接地電路圖 |

[版本比較與驗證狀態](docs/version-comparison.md) · [維護方式](docs/repository-maintenance.md) · [MCP 安裝與使用](docs/mcp-installation.md)

## 開啟專案

使用 Altium Designer 開啟所需版本的 `.PrjPcb`，保留同一資料夾內的 `.SchDoc`、`.PcbDoc`、`.SchLib`、`.PcbLib`。專案文件與元件模型使用同資料夾的相對檔名，各版本互不依賴。

PCB 與 PcbLib 已內嵌既有 3D 模型；本 repository 不另收錄參考用 STEP 或重複的整板 STEP。更新封裝時，請選擇該版本資料夾內的 PcbLib。

verL 的實際接線請使用 [線束腳位表](verL/connector-harness-pinout.csv) 與 [J2 通道腳位表](verL/J2-channel-pinout.csv)。兩版本的線束不能直接互換。

## 收錄範圍

收錄可編輯的 Altium 專案、各版本元件庫、必要接線表與簡要說明。歷史備份、暫存檔、自動輸出、MCP 腳本、Python 環境及大量驗證中間檔不納入版控。

目前是設計開發版本；各版的接地、機構、模型與驗證狀態見版本比較表。

## EDA-Agent MCP（選用）

若要使用 AI 控制 Altium，請參考 [MCP 安裝與使用](docs/mcp-installation.md) 及 [設定範例](docs/mcp-config.example.toml)。開啟電路專案本身不需要安裝 MCP。
