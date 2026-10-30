# 規則式人力排班系統｜輪班條件、月曆與違規檢查

[English Version](README.en.md) · [案例研究 (Case Study)](docs/case-study.md) · [公開邊界說明 (Publication Boundary)](docs/publication-boundary.md)

在需要 24 小時連續運作的營運環境（如公共勤務、輪班製造或緊急應變單位）中，排班往往是一項高度複雜的工程難題。排班人員必須同時處理：

* **人員資格與偏好**：不同人員的特殊執勤資格、請假需求與偏好時段。
* **嚴格的法規與休假限制**：連續輪班天數上限、班次間隔的最短休息時間。
* **時段覆蓋率要求**：尖峰與離峰時段對特定專業人力的最低門檻。
* **人工介入與例外鎖定**：特定指定專案、法庭出庭或特定任務需要人工鎖定班次，且不得被自動排程打亂。

傳統人工或單純套表格的做法極易在規模擴大時出錯，且往往在人員已到班時才發現違規。本專案將這些複雜的營運規章轉化為結構化的**約束求解規則引擎**，以規則驅動自動生成基礎班表，並將違規與警告提升為第一類狀態，實現可追溯的自動化排班治理。

---

## 核心展示能力

| 能力維度 | 實作策略與實證 |
|---|---|
| **業務需求結構化** | 將複雜且模糊的人事規章轉化為明確的規則約束模型。 |
| **規則引擎架構設計** | 涵蓋人力覆蓋 (Coverage)、休息間隔 (Rest-period)、專業資格 (Qualification)、鎖定班次 (Locked-duty) 與組織政策規則。 |
| **人員狀態抽象模型** | 支援人員狀態生命週期、輪班限制與排休偏好建模。 |
| **月曆工作流與視覺化** | 支援月份總覽、每日班次明細、時段粒度檢視與班次交換候選推薦。 |
| **違規分級與可解釋性** | 將違規依嚴重度分為紅燈 (Violation)、黃燈 (Warning)、未完成 (Incomplete)，並提供歸因規則代碼。 |
| **自動化回歸驗證** | 底層私有完整工程實作累積 **56 項通過驗證的自動化測試案例**。 |

---

## 系統架構全景

```text
人員登記模組 (Personnel Registry)
  ├── 專業資格與在職狀態模型 (Qualification / Status)
  └── 請假與排班偏好模型 (Leave / Preferences)

排班核心引擎 (Scheduling Engine)
  ├── 月度工作區 (Monthly Workspace)
  ├── 規則驅動基礎排班 (Rule-based Generation)
  ├── 時段與班次模型 (Segment & Shift Model)
  └── 人工強制覆寫與鎖定班次 (Manual Override & Locked-duty)

約束校驗層 (Validation Layer)
  ├── 班次覆蓋率檢查 (Coverage Check)
  ├── 最短休息間隔檢查 (Rest-period Check)
  ├── 重複指派與衝突排查 (Duplicate Check)
  └── 違規 / 警告 / 缺工狀態歸因分類 (Classification)

月曆視圖與匯出 (Calendar View & Export)
  ├── 月度全景日曆與即時警告
  ├── 班次交換候選提示
  └── 報表匯出 (CSV / JSON / Excel XLSX)
```

---

## 重要架構取捨

1. **顯式約束模型 (Explicit Constraint Modeling) 優於隱含條件**：
   每個排班限制均抽象為具備獨立代碼 (Rule Code)、描述與嚴重等級的獨立物件。這讓系統具備極高的可審計性，任何被標記的班次皆能精確溯源至對應的法規或人事規章，且新增規則無需修改排程核心算法。
2. **時段切分模型 (Segment Model) 保障排班彈性**：
   不把輪班型態（如三班制、兩班制）寫死在系統底層，而是透過時間片段 (Segments) 進行動態覆蓋拼裝，適應未來不同的排班週期調整。

---

## 相關文件與發布邊界

* [完整案例研究 (docs/case-study.md)](docs/case-study.md)：公共輪班情境的規則推演歷程、設計決策與 56 項測試演進。
* [公開發布邊界 (docs/publication-boundary.md)](docs/publication-boundary.md)：本公開倉庫展示規則架構、匿名化場景與決策邏輯，真實人員個資與完整引擎原始碼保留於內部獨立開發。

---

## 授權 (License)

本專案採用 [MIT License](LICENSE) 授權。
