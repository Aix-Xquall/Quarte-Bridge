# Quarter Bridge RevK

開啟 [QuarterBridge_RevK.PrjPcb](QuarterBridge_RevK.PrjPcb)。本資料夾保留該版本自己的原理圖、PCB、SchLib 與 PcbLib。

- J1：設計採 ITT M24308/24-29F，50 pin、直角公頭 D-sub。
- J2：設計採 Positronic M24308/24-77F，78 pin、直角公頭 D-sub。
- PCB：84 × 55 mm；48 顆通道電阻，單數通道正面、雙數背面。
- 既有設計紀錄：64 訊號網路接通、0 訊號過孔；最近原版原生 DRC 紀錄為 0。
- 本次整理僅調整元件庫引用／來源綁定；原理圖已重新編譯並執行 ERC，結果為 0。
- 機構、疊構與線束仍須依該版設計狀態確認，板厚 1.6 mm 為原版的暫定設定。

3D 模型內嵌於 PCB／PcbLib。更新符號或封裝時使用本資料夾的 `QuarterBridge_RevK.SchLib`／`QuarterBridge_RevK.PcbLib`。

[版本比較](../docs/version-comparison.md)
