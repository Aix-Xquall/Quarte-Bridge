# Quarter Bridge verL

開啟 [QuarterBridge_verL.PrjPcb](QuarterBridge_verL.PrjPcb)。本專案包含主原理圖、`Chassis_Bridge_verL.SchDoc` 機殼接地圖、PCB 與各自專用的 SchLib／PcbLib。

- J1：51 pin Micro-D，BR 直角穿孔公頭，設計型號 M83513/17-G02NP。
- J2：100 pin Micro-D，CBR 直角穿孔 PIN 配置，封裝 `QB69_CBR100_PIN_R1`。
- PCB：70 × 65.1 mm；板框 Y 範圍 −2.4～62.7 mm。
- J2 訊號腳群與通道電阻的對稱軸為 X＝35 mm；J2 元件原點／安裝孔中線為 X＝35.635 mm。
- 48 顆通道電阻已向 J1 方向上移 2 mm，配置於兩接頭之間；電阻焊墊中心 Y 範圍為 30.16～39.06 mm。
- 64 條訊號的中段主幹為連續直線；出口與避孔區允許非 45° 折角。八組通道的 J2 至電阻走線以 X＝35 mm 鏡射。
- 單數通道 TopLayer、雙數 BottomLayer；64 訊號網路全通，0 訊號過孔。

## 驗證與接線

本次整理後重新編譯並執行 ERC，結果為 0。最近原版原生 DRC 為既有 AGND_A 34、AGND_B 32 項未連線，共 66 項；其餘該次已執行規則為 0。接地尚未全部完成，不能視為已完成製板驗證。

J2 定義已經過鏡像排列調整。請依 [J1／J2 線束腳位表](connector-harness-pinout.csv) 與 [J2 每通道腳位表](J2-channel-pinout.csv) 接線，沿用各通道的 S+、S−、P+、PS+ 訊號名稱。

3D 模型內嵌於 PCB／PcbLib；更新封裝時使用本資料夾的 `QuarterBridge_verL.PcbLib`。目前模型沿用原設計，附件 G01／H02CP 未取代既有模型。

[版本比較](../docs/version-comparison.md)
