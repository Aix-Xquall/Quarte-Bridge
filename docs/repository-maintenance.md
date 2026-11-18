# Repository 維護方式

- 長期並存的接頭／板型放在 根目錄的 `<版本>/`，每版以自己的 `.PrjPcb` 開啟。
- 修改中的工作使用 branch；確認的交付或製板快照使用 tag／Release。
- 各版本先保留自己的 SchLib／PcbLib。新增或更改共用元件前，先確認對其他版本的影響。
- 元件符號及封裝模型使用同資料夾的相對檔名；避免把電腦的絕對路徑寫入有效模型連結。
- PCB 已嵌入封裝與模型。板內可能仍保留歷史來源資訊，該資訊不作為外部檔案收錄依據；舊的 SourceFootprintLibrary 綁定已取消。更新封裝時明確指定本版 PcbLib，不要直接套用其他版本的庫。
- 線束定義變動時同步更新該版本的接線表；verL 的 `connector-harness-pinout.csv` 包含 J1 與 J2。
- Altium 原生文件採二進位版控，不適合以文字方式自動合併。多人修改同一個 SchDoc／PcbDoc 前先協調。
- `.gitignore` 排除 Altium 自動生成狀態、歷史、備份及本機環境。Gerber、鑽孔、整板 STEP 等交付檔在確定版本後另外整理為 Release 附件。
- 本 repo 目前不使用 Git LFS；之後若加入大型模型，應在收錄前決定 LFS 規則。

本次整理保留原始工作資料與歷史備份，repository 中只收錄設計來源與必要文件。

本機工作與 Git 版控共用 `Quarter Bridge`，不再另設 repository 工作副本或 `hardware` 層。原始歷史目錄、工具與合併前備份保留在本機，由白名單 `.gitignore` 排除。
