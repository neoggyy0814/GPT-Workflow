# 知識路由（Knowledge Routing） v1.1

---

# 一、定位（Purpose）

本文件負責管理 GPT 的知識路由（Knowledge Routing）。

收到任何任務後，

AI 應先判斷任務類型，

再決定：

- 啟用哪些工作模式（Modes）
- 引用哪些規格（Specifications）
- 啟動對應工作流程（Workflow）

Knowledge Routing 僅負責決定任務如何開始。

不負責：

- 工作流程
- Requirement Analysis
- Specification 建立
- 成果生成
- 修改流程

上述內容皆依《Personal Workflow GPT Specification》執行。

---

# 二、核心原則（Core Principles）

收到任何任務後，

AI 應依序完成：

Task Classification

↓

Knowledge Routing

↓

Workflow 啟動

之後交由對應 Mode 執行。

Knowledge Routing 不介入後續工作流程。

---

# 三、任務判斷（Task Classification）

AI 應先判斷目前任務屬於哪一種類型。

目前支援：

- 圖片任務（Image Task）
- 影片任務（Video Task）
- 通用專案（General Project）

未來可擴充：

- Programming
- Writing
- Planning

若同時符合多種類型，

AI 應判斷：

- Primary Task（主要任務）
- Secondary Task（次要任務）

並依序執行。

不得同時啟用互相衝突之主要 Mode。

---

# 四、模式路由（Mode Routing）

完成任務判斷後，

AI 應選擇最適合之 Mode。

例如：

Image Task

↓

Image Mode

Video Task

↓

Video Mode

General Project

↓

General Project Mode

若需多個 Mode，

應：

先完成主要 Mode，

再依需求切換至次要 Mode。

不得同時執行互相衝突之 Mode。

---

# 五、規格路由（Specification Routing）

Mode 確認後，

AI 應引用對應 Specification。

例如：

Image Mode

↓

Media Specification

Video Mode

↓

Media Specification

↓

Video Specification（由 Video Mode 定義）

General Project Mode

↓

Project Specification（未來可擴充）

若已有對應 Specification，

應直接引用。

不得重新定義既有 Specification。

---

# 六、工作流程啟動（Workflow Initialization）

完成 Mode 與 Specification 路由後，

AI 應啟動 Workflow。

Workflow 之執行方式、

Requirement Analysis、

Specification Building、

Output、

Revision

皆依：

《Personal Workflow GPT Specification》

執行。

Knowledge Routing 不重新定義 Workflow。

---

# 七、模式切換（Mode Switching）

若使用者指定 Mode，

應優先使用指定 Mode。

若未指定，

由 AI 自行判斷最適合之 Mode。

若任務進行中發現需要其他 Mode，

AI 可建議切換。

是否切換，

仍以使用者需求為優先。

不得無預警改變主要 Mode。

---

# 八、路由優先順序（Routing Priority）

所有任務依下列順序決定：

1. 使用者需求（User Intent）
2. Personal Workflow GPT Specification
3. Knowledge Routing
4. 工作模式（Mode）
5. 規格（Specification）

Knowledge Routing 不得：

- 修改 Workflow
- 修改 Specification
- 修改 Mode 定義

僅負責決定：

任務應交由哪個 Mode 執行。

---

# 九、例外處理（Exception Handling）

若使用者明確指定：

- Mode
- Specification
- 工作方式

且不與系統規則衝突，

AI 應優先遵循。

若指定內容不足以完成任務，

則交由 Workflow 進行後續處理。

Knowledge Routing 不處理需求成熟度判斷。

---

# 十、版本資訊（Version）

目前版本：

**v1.1**

本文件僅負責：

- Task Classification
- Mode Routing
- Specification Routing
- Workflow Initialization
- Mode Switching

不得包含：

- Workflow
- Requirement Analysis
- Specification 定義
- Mode 工作流程
- 成果生成

所有上述內容，

皆由對應文件負責，

以降低耦合、

避免重複、

提升可維護性。