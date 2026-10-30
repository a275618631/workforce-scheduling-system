# 規則式人力排班系統規格設計 (Workforce Scheduling System Specification)

[English Version](README.en.md) · [案例研究 (Case Study)](docs/case-study.md) · [公開邊界說明 (Publication Boundary)](docs/publication-boundary.md)

> **專案定位**：架構規格設計與業務約束模型案例研究。  
> 展示如何將連續運作單位（輪班生產、公共勤務與緊急應變）複雜的人事工時規章，抽象為可形式化驗證的約束求解規則模型。

---

## 1. 核心業務難題

在需要 24 小時連續運作的營運環境中，排班排程面臨以下多維度約束：
- **法規與休息限制**：連續執勤天數上限、兩班次間最短休息間隔（如至少間隔 11 小時）、每週強制例假。
- **時段人力覆蓋**：尖峰與離峰時段對特定專業資格人力的最低在崗門檻。
- **排班偏好與請假**：人員預排休假、輪班偏好與臨時缺勤補位。
- **人工例外鎖定**：特定指定專案或特殊勤務需人工鎖定班次，且不得被自動重排覆蓋。

---

## 2. 規則引擎約束全景模型

本專案將業務規章收斂為三層規則約束矩陣：

```text
人員登記模型 (Personnel Model)
  ├── 專業資格與在職狀態 (Qualification / Status)
  └── 請假與排休偏好 (Leave / Preferences)

排班核心規格 (Scheduling Core Model)
  ├── 月度工作區 (Monthly Workspace)
  ├── 時段與班次模型 (Segment & Shift Model)
  └── 人工強制覆寫與鎖定 (Manual Override & Locked-duty)

約束校驗矩陣 (Constraint Validation Matrix)
  ├── [硬性約束] 班次覆蓋率檢查 (Coverage Check)
  ├── [硬性約束] 最短休息間隔檢查 (Rest-period Check)
  ├── [硬性約束] 重複指派與衝突排查 (Conflict Check)
  └── [軟性約束] 偏好滿足度與公平輪調計算 (Fairness Scoring)
```

---

## 3. 違規分級與可解釋性

系統設計將班表狀態歸因為可解釋的診斷碼：
- **紅燈 (Violation)**：違反硬性約束（如間隔不足 11 小時、超時連續執勤），系統禁止發布並標明衝突人員與規則代號。
- **黃燈 (Warning)**：違反軟性約束（如未滿足偏好休假、輪替間隔不均），提供調度主管調整建議。
- **未完成 (Incomplete)**：人力覆蓋率不足，提示缺工時段與建議遞補名單。

---

## 4. 公開展示邊界說明
- **本公開倉庫定位**：專案架構規格設計、案例分析（Case Study）與約束模型展示。
- **實作代碼與測試**：底層 C#/Python 求解器實作與 56 項私有回歸測試保留於內部研發環境，公開版本不包含具體員工名冊與組織機密資料。
