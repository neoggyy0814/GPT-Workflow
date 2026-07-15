# 媒體規格（Media Specification） v2.0

> 本文件定義所有媒體內容之資料模型（Data Model）。
>
> Media Specification 不定義任何工作流程、任務判斷或 Mode。
>
> Media Specification 僅描述媒體內容應包含哪些資訊，供各 Mode 引用。
>
> 所有媒體相關 Mode 應直接引用本 Specification。

---

# 一、定位（Purpose）

Media Specification 用於建立完整媒體資料模型。

適用於：

- Image
- Video
- 未來其他媒體形式

例如：

- Audio
- 3D
- XR

本文件不限制任何媒體生成模型。

---

# 二、Constraints（限制）

定義不可違反之條件。

包含但不限於：

- 使用者核心需求
- 必須保持內容
- 必須修改內容
- 禁止修改內容
- 輸出用途
- 畫面比例
- 風格限制
- 品質限制
- 參考圖片
- 負面限制（Negative Constraints）

Constraints 為最高優先資料。

---

# 三、Identity（身份）

定義主體不可改變之辨識特徵。

依不同主體，

可包含但不限於：

## 人物（Character）

- 臉型
- 五官比例
- 年齡感
- 神韻
- 身材比例
- 膚色
- 髮型特徵
- 種族
- 角色設定

---

## 品牌（Brand）

- Logo
- 品牌識別
- 品牌色彩
- 視覺語言

---

## 建築（Architecture）

- 建築特色
- 外觀比例
- 結構特徵

---

## 車輛（Vehicle）

- 外型比例
- 品牌特徵
- 車身細節

---

## 生物（Creature）

- 種類
- 外觀特徵
- 身體比例
- 生物辨識特徵

---

Identity 應維持整體辨識度。

---

# 四、World（世界）

描述畫面中存在之內容。

## Subject（主體）

包含但不限於：

- 主體
- 主體細節
- 服裝
- 配件
- 外觀
- 動作
- 姿勢
- 表情
- 視線
- 手勢
- 人數
- 主從關係
- 互動

---

## Environment（環境）

包含但不限於：

- 地點
- 建築
- 室內／室外
- 地形
- 植物
- 水體
- 天空
- 前景
- 背景

---

## Time & Climate（時間與氣候）

包含但不限於：

- 時間
- 季節
- 天氣
- 氣候

---

## Narrative（情境）

包含但不限於：

- 情境
- 劇情
- 情緒
- 氛圍
- 畫面焦點
- 敘事
- 象徵元素

---

# 五、Visual（呈現）

描述世界如何被呈現。

## Composition（構圖）

包含但不限於：

- 構圖
- 景別
- 主體位置
- 留白
- 視覺重心
- 前景／中景／背景

---

## Camera（攝影機）

包含但不限於：

- 視角
- 鏡頭高度
- 焦段（Lens）
- 景深
- 對焦
- 透視

---

## Lighting（光線）

包含但不限於：

- 光源
- 光向
- 光質
- 光強
- 色溫
- 陰影
- 反射
- 體積光

---

## Color（色彩）

包含但不限於：

- 主色
- 配色
- 色調
- 飽和度
- 對比
- Color Grading

---

## Material（材質）

包含但不限於：

- 皮膚
- 毛髮
- 布料
- 金屬
- 木材
- 石材
- 水
- 玻璃
- 塑膠
- 陶瓷
- Texture

---

## Style（風格）

包含但不限於：

- 畫風
- 美學
- 攝影風格
- 渲染方式
- 藝術風格參考（若模型支援）

---

## Rendering（渲染）

包含但不限於：

- 細節程度
- 品質
- 解析度
- HDR
- Film Grain
- Bloom
- Motion Blur
- Micro-details

---

# 六、Modification（修改）

適用於：

- Edit
- Composite
- Restore
- Enhancement

定義：

- 保持內容
- 修改內容
- 禁止修改內容

Modification 僅描述修改範圍。

不描述修改流程。

---

# 七、Propagation（自然連動）

描述修改後應自然產生之合理影響。

例如：

- 光影
- 遮擋
- 布料
- 材質
- 表情肌肉
- 姿勢
- 反射
- 接觸面

Propagation 應符合自然一致性。

不得影響 Identity。

---

# 八、一致性優先順序（Consistency Priority）

所有媒體內容應遵循下列優先順序：

1. Constraints
2. Identity
3. Modification
4. World
5. Visual
6. Rendering

下層項目不得違反上層內容。

---

# 九、版本資訊（Version）

目前版本：

**v2.0**

較 v1.1 之變更：

- 原「Personal Workflow GPT Specification」更名為「Runtime」，共 1 處，對應該名稱確認即為 Runtime 舊稱。

本次調整僅為命名統一，不涉及任何資料模型內容變動。

本文件僅定義媒體資料模型。

不得包含：

- Workflow
- Requirement Analysis
- Task Classification
- 問答流程
- Mode 工作流程
- Prompt Generation

所有流程相關內容，

皆由：

- Runtime
- Knowledge Routing
- 各 Mode

分別負責。
