---
name: docs
description: Use when creating, moving, renaming or indexing any file under docs/, when deciding where a spec, plan, request, decision record, report, guide or manual belongs, or when a workflow tool (superpowers, oh-my-claudecode, etc.) is about to write a design or plan document. Defines the docs/ taxonomy, date-prefix rule, and index maintenance.
---

# docs/ 文件分類法

## 目錄

| 目錄 | 放什麼 | 日期前綴 | 典型來源 |
|---|---|---|---|
| `manuals/` | 給**終端使用者**的操作手冊（含截圖、docx、zip） | 否 | 人 |
| `guides/` | 給**開發／維運**的指南：部署、指令、測試計畫、狀態機 | 否 | 人、AI |
| `specs/` | 開發規格書：功能設計、架構決定的完整描述 | **是** | brainstorming、人 |
| `plans/` | 實作計畫：對應某份 spec 的步驟、任務拆解 | **是** | writing-plans、人 |
| `requests/` | 需求方原始文件：客戶反饋、修改需求、往來信件 | **是** | 人 |
| `decisions/` | 設計決策紀錄：為什麼這樣做、取捨、當時的限制 | **是** | 人、AI |
| `reports/` | 一次性分析／稽核報告 | **是** | AI |
| `archive/` | 過期文件，僅供追溯 | 保留原名 | — |

規則：**會持續改的文件不加日期**（manuals、guides），因為它代表現況；**有時間點意義的文件加日期**，排序即時間軸。

## 新文件放哪（決策表）

| 這份文件是… | 放 |
|---|---|
| 教使用者怎麼操作系統 | `manuals/` |
| 教開發者怎麼部署、跑指令、測試 | `guides/` |
| 描述一個功能要做成什麼樣、為什麼 | `specs/` |
| 列出實作步驟、任務清單 | `plans/` |
| 客戶或需求方給的、或寫給他們確認的 | `requests/` |
| 記錄某個技術選擇的理由與取捨 | `decisions/` |
| 一次性的資料分析、稽核、比對結果 | `reports/` |
| 已被取代但要留著追溯 | `archive/` |

「實作計畫」即使是需求方交來的，也放 `plans/`，看內容不看來源。

## 檔名

- 有日期者：`YYYY-MM-DD-<主題>.md`，日期取文件建立日或所述事件日
- 主題用 kebab-case 或中文，**不用底線、不用空格**；來源人名可作前綴：`2026-08-07-客戶a-修改需求.md`
- 不把日期寫在檔尾（`xxx_20260814.md` ✗）

## 工作流套件的輸出路徑

不論使用 superpowers、oh-my-claudecode 或其他套件，**規格寫 `docs/specs/`、計畫寫 `docs/plans/`**。superpowers 的 brainstorming / writing-plans 預設路徑是 `docs/superpowers/specs|plans`，其 SKILL.md 明寫「使用者偏好可覆寫」，本規範即為該偏好。oh-my-claudecode 的狀態寫在 `.omc/`，不影響 `docs/`。

## 索引

`docs/README.md` 是唯一索引：一張目錄表 + 每個目錄一張檔案表（檔名連結 + 一句說明）。**新增、搬移、刪除文件時必須同步更新**，同一筆 commit 提交。

## 搬移既有文件

- 用 `git mv` 保留歷史
- `grep -rn "<舊路徑>" docs CLAUDE.md README.md` 找出內部引用一併改
- commit：`docs(structure): ...`
