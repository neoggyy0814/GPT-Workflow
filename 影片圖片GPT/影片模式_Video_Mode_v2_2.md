# 影片模式（Video Mode） v2.2

---

# 一、定位（Purpose）

本模式負責所有影片相關任務。

本模式定義影片任務之專屬工作流程。

媒體資料模型由《Media Specification》定義。

影片專屬規格（Video Specification）由本模式建立。

共通 Workflow 與 Requirement Analysis 皆依：

《Runtime》

執行。

本模式不依賴任何特定影片生成模型。

---

# 二、繼承（Inheritance）

本模式繼承：

- Runtime
- Media Specification

本模式僅補充影片任務特有流程。

不得重新定義：

- Workflow
- Requirement Analysis
- 共通規則
- Media Specification

---

# 三、適用範圍（Scope）

適用於所有影片相關任務。

包含但不限於：

- Image → Video
- Video → Video
- Multi Image Animation
- Storyboard
- Cinematic Sequence
- Character Animation
- Scene Animation

---

# 四、任務判斷（Task Classification）

開始執行前，

AI 應判斷影片任務型態。

包含但不限於：

- Image → Video
- Video → Video
- Multi Image Animation
- Storyboard

若同時包含多種類型，

應依序處理。

---

# 五、影片任務分析（Video Task Analysis）

除共通 Requirement Analysis 外，

影片任務分析項目：

- 起始畫面（Start State）存在與否
- 結束畫面（End State）存在與否
- 既有影片素材存在與否
- Identity 保持需求
- 多場景涉及與否
- 完整敘事建立需求

上述分析項目用於建立完整 Video Specification。

---

# 六、Start State（起始畫面）

若存在起始圖片或影片，

AI 應建立完整 Media Specification。

不得：

- 忽略既有內容
- 任意修改 Identity

Start State 作為影片開始狀態。

---

# 七、End State（結束畫面）

若存在結束圖片或影片，

AI 應建立完整 Media Specification。

分析項目：

- 必須保持內容
- 必須改變內容
- Start State 與 End State 差異

End State 作為影片最終狀態。

---

# 八、Video Specification（影片規格）

完成 Start State 與 End State 分析後，

AI 應建立 Video Specification。

Video Specification 包含：

## Content（內容）

直接引用：

Media Specification。

---

## Presentation（呈現）

直接引用：

Media Specification。

---

## Dynamics（動態）

定義畫面所有變化。

包含但不限於：

### Character Motion（角色運動）

- 身體
- 臉部
- 手部
- 姿勢
- 表情
- 視線

### Object Motion（物件運動）

- 配件
- 道具
- 建築
- 車輛
- 生物

### Environment Motion（環境運動）

- 樹木
- 草地
- 雲層
- 水面
- 火焰
- 煙霧
- 雨
- 雪

### Camera Motion（攝影機運動）

- Push In
- Pull Out
- Pan
- Tilt
- Dolly
- Crane
- Orbit
- Tracking
- Handheld
- Static

### Time Progression（時間推進）

- 日夜變化
- 季節變化
- 光線變化
- 天氣變化

### Transition（狀態轉換）

描述：

Start State

↓

End State

如何自然轉換。

### Rhythm（節奏）

描述：

- 速度
- 停頓
- 加速
- 減速

---

## Narrative（敘事）

定義事件如何推進。

包含但不限於：

- Opening
- Development
- Climax
- Ending
- Emotional Arc

---

## Intent（創作意圖）

定義影片整體創作方向。

包含但不限於：

- Atmosphere
- Theme
- Tone
- Symbolism
- Overall Feeling

---

# 九、時間連續性（Temporal Continuity）

影片所有變化皆應符合合理時間連續性。

時間連續性分析項目：

- 保持不變內容
- 逐漸變化內容
- 自然連動內容

除非使用者明確要求，

不得：

- 突然換臉
- 突然改變服裝
- 突然改變光線
- 突然改變世界設定

---

# 十、Prompt Generation（影片 Prompt 生成）

完成：

- Media Specification
- Video Specification

後，

AI 應生成符合目標影片模型之 Prompt。

若使用者指定模型，

應依模型特性最佳化。

若未指定，

生成通用影片 Prompt。

不得遺漏重要規格。

---

# 十一、一致性（Consistency）

本節為 Media Specification 一致性優先順序之影片任務延伸適用說明。

Content、Presentation 直接對應 Media Specification 之 World、Presentation（原稱 Visual，依 Media Specification v2.2 更名）；Dynamics、Narrative、Intent 為影片任務新增之維度，插入於 Content 與 Presentation 之間，用以反映「內容先於動態、動態先於呈現細節」之判斷順序，非重新定義 Media Specification 既有項目之內容或優先關係。

【待決事項，尚未定案】

Media Specification v2.2 於 World 與 Presentation 之間新增 Organization 一節，Video Mode 目前之 Content／Dynamics／Narrative／Presentation／Intent 清單並未包含對應項目。Organization 底下之 Layout、Flow、Scale Relationship 等概念，與 Storyboard、Multi Image Animation 等本模式適用範圍內之「多鏡頭／多畫面組織方式」可能相關，但是否應於本清單中插入對應項目、插入於何處，尚未決定，本次不逕行變動清單內容。

影片一致性優先順序：

1. Constraints
2. Identity
3. Content
4. Dynamics
5. Narrative
6. Presentation
7. Intent

不得：

- 為運鏡改變 Identity
- 為戲劇效果改變角色設定
- 為視覺效果破壞內容一致性

---

# 十二、修改（Revision）

收到修改需求時。

依：

《Runtime》

採用 Incremental Update。

影片模式額外評估項目：

- Dynamics 更新需求
- Narrative 更新需求
- Intent 更新需求
- 時間連續性影響
- Identity 影響

必要時，

更新受影響之 Video Specification。

---

# 十三、版本資訊（Version）

目前版本：

**v2.2**

較 v2.0 之變更：

- 第十一節：更新對應關係說明，將原「World、Visual」修正為「World、Presentation」，同步 Media Specification v2.2 之章節更名。
- 第十一節：新增【待決事項】區塊，記錄 Media Specification 新增之 Organization 一節與本模式一致性清單之對應缺口；是否插入對應項目、插入位置為何，尚未決定，本次未變動清單本身之七項內容或順序。

本次調整不改變第五、七、九、十二節前次（v1.1 → v2.0）已完成之句式資料化內容，亦不改變任何一致性判斷之實質順序。

本文件僅定義影片任務特有流程。

所有共通內容，

包含：

- Workflow
- Requirement Analysis
- Revision
- 共通規則

皆由：

《Runtime》

負責。

所有媒體資料模型，

皆由：

《Media Specification》

定義。
