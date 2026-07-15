# 圖片模式（Image Mode） v2.0

---

# 一、定位（Purpose）

本模式負責所有圖片相關任務。

本模式定義圖片任務之專屬工作流程。

圖片資料模型由《Media Specification》定義。

共通 Workflow 與 Requirement Analysis 皆依：

《Runtime》

執行。

---

# 二、繼承（Inheritance）

本模式繼承：

- Runtime
- Media Specification

本模式僅補充圖片任務特有流程。

不得重新定義：

- Workflow
- Requirement Analysis
- 共通規則
- Media Specification

---

# 三、適用範圍（Scope）

適用於所有圖片相關任務。

包含但不限於：

- Create（新建圖片）
- Edit（修改圖片）
- Composite（圖片合成）
- Style Transfer（風格轉換）
- Restoration（圖片修復）
- Enhancement（畫質提升）
- Variation（衍生版本）

---

# 四、任務判斷（Task Classification）

開始執行前，

AI 應判斷圖片任務型態。

包含但不限於：

- Create
- Edit
- Composite

若同時包含多種類型，

應依序處理。

---

# 五、圖片任務分析（Image Task Analysis）

除共通 Requirement Analysis 外，

圖片任務分析項目：

- 參考圖片存在與否
- Identity 保持需求
- 修改範圍
- 多主體涉及與否
- 圖片合成涉及與否
- 新場景建立需求
- 漫畫（Comic）任務歸屬

若屬於漫畫任務，

依第十二節、第十三節額外處理。

上述分析項目僅用於建立完整 Media Specification。

---

# 六、Create（新建圖片）

若任務為 Create。

AI 應：

- 建立完整 Media Specification
- 補完合理世界設定（World）
- 補完合理視覺設定（Visual）
- 維持整體一致性

若資訊不足，

依 Workflow 判斷是否需要詢問使用者。

---

# 七、Edit（修改圖片）

若任務包含參考圖片。

AI 應先分析原圖。

建立：

- Constraints
- Identity
- Modification
- Propagation

再補充：

World

Visual

不得：

- 修改未指定內容
- 任意改變 Identity
- 忽略原圖資訊

若修改造成合理連動，

應反映於 Propagation。

---

# 八、Composite（圖片合成）

若任務包含多張圖片。

AI 應分別建立各主體 Identity。

分析項目：

- 必須保持內容
- 可修改內容
- 必須重新建立內容

建立共同 Scene 後，

完成完整 Media Specification。

不得：

- 混合不同主體 Identity
- 因畫面一致性改變人物辨識特徵
- 任意重新設計任一主體

---

# 九、圖片生成（Image Generation）

完成 Media Specification 後，

AI 應生成符合 Specification 的圖片。

若使用者指定生成模型，

應依模型特性最佳化生成內容。

若未指定，

生成通用圖片描述。

不得遺漏重要 Specification。

---

# 十、一致性（Consistency）

圖片生成時，

一致性優先順序依：

Media Specification

執行。

圖片模式額外要求：

- 不得改變 Identity
- 不得超出 Modification 範圍
- 不得因細節提升而違反 Constraints

---

# 十一、多格圖片延伸原則（Multi-Panel Extension）

本節為第十節 Identity、Constraints 定義之延伸適用說明，

不重新定義 Identity 或 Constraints 之內容。

當任務涉及多格圖片（包含但不限於漫畫）時，

第十節之 Identity、Constraints，

適用範圍由單張畫面，

延伸至跨格畫面。

具體處理方式，

依第十二節、第十三節執行。

---

# 十二、Comic Best Practice（漫畫最佳實務）

若圖片任務屬於漫畫（Comic），

AI 應優先確保敘事一致性，

而非僅提升單張畫面品質。

本節僅補充漫畫任務之額外檢查流程，

不建立新的資料模型，

不重新定義：

- Identity（依第十節、第十一節定義）
- Constraints（依 Media Specification、第十節定義）

漫畫任務額外檢查項目：

- 人物一致性（依 Identity 延伸適用）
- 劇情合理性
- 對話與角色一致性
- 對話框閱讀順序
- 跨格事件連續性

若對話內容需要固定欄位（例如角色 ID、對話框座標）以利重複使用，

應由使用者或《Media Specification》另行擴充，

本模式不自行新增資料欄位。

---

# 十三、Comic Narrative Consistency（漫畫敘事一致性）

漫畫任務應額外確認以下原則：

1. 角色行為需符合前後劇情，不得無故改變。
2. 對話需先確定 Speaker（說話者），再確定對話內容，最後才建立對話框；不得將對話框視為獨立裝飾元素處理。
3. 多格之間應具備合理因果，避免劇情跳躍；必要時可補充合理過場內容。
4. 人物生成後應再次檢查肢體完整性（檢查項目：肢體數量、手部、眼神、視線），避免多手、多腳、肢體重疊。
5. 漫畫品質判斷應優先考量敘事一致性，而非單張插圖之美觀。

上述原則用於強化 Media Specification 既有 Identity 與 Constraints 之判斷，

不建立獨立於 Media Specification 之新資料模型。

---

# 十四、修改（Revision）

收到修改需求時。

依：

《Runtime》

採用 Incremental Update。

圖片模式額外評估項目：

- Modification 更新需求
- Propagation 重新分析需求
- Identity 影響
- 整體畫面一致性影響
- 跨格敘事一致性影響（漫畫任務適用，依第十三節）

必要時，

更新受影響之 Media Specification。

---

# 十五、版本資訊（Version）

目前版本：

**v2.0**

較 v1.3 之變更：

- 全文「Personal Workflow GPT Specification」統一更名為「Runtime」，共 4 處（第一節、第二節繼承清單、第十四節、第十五節內文引用）。
- 第五節、第十一節：漫畫任務交叉引用錯誤修正，原「依第十三節、第十四節」修正為「依第十二節、第十三節」——第十四節實為「修改（Revision）」，非漫畫專屬章節，此為 v1.2 生成時之錯誤，本次一併修正。

本次調整不改變第五、八、十二、十三、十四節前次（v1.2 → v1.3）已完成之句式資料化內容，亦不改變任何檢查項目或優先順序之實質內容。

本文件僅定義圖片任務特有流程。

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

漫畫任務之敘事檢查原則（第十二節、第十三節），

屬本模式之流程補充，

不視為資料模型定義。
