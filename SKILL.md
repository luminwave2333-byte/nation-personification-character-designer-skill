---
name: nation-personification-character-designer
description: 研究、拆解并生成原创的国家拟人、文明拟人、地区拟人、民族/族群、朝代与历史文化角色设计。将历史、文化、国家特色、族群传统、服装结构、材料工艺、图形系统和现代身份转译为轮廓、服装建筑、纹样层级、色彩职责、头部锚点、道具与 AI 生图/视频连续性提示词。用于国拟、省拟、文明拟人、五常群像、角色设定稿拆解、原创重设计与 ComfyUI/Flux/Qwen Image/Wan/LTX 工作流。不得 1:1 复刻参考角色的独特发型、头饰、服装、纹样、道具与配色完整组合。
---

# Nation Personification Character Designer

默认语言：中文。

默认输出：中文设计说明 + 英文 AI 图像提示词 + 中文负面约束 + 结构化 JSON。

核心目标：**不靠大面积国旗和国家名字，也能通过轮廓、历史层、材料、工艺、色彩节奏、纹样层级与道具识别角色归属。**

## 0. 激活条件

用户出现以下任一意图时使用本 Skill：

- 设计国家拟人、省拟、城市拟、文明拟人、地区拟人、朝代拟人或历史文化角色。
- 分析角色设计稿的设计语言、服装逻辑、图腾层级、配色与动态件。
- 学习某参考图的设计理念并生成原创角色，但不复刻。
- 将历史、文化、民族/族群特色、国家特色融入角色。
- 设计五常或多国群像。
- 输出角色设定页、character design sheet、AI 提示词或视频连续性锚点。

只给对象名时不要追问，使用默认参数开始设计。

```yaml
mode: DESIGN
genre: contemporary_fantasy_nation_personification
presentation: premium_2d_anime_concept_sheet
sheet_ratio: 3:2_landscape
character_age_read: adult_20s
historical_weight: balanced
modernity_weight: medium
symbol_literalness: low_to_medium
text_density: low
video_safe: true
output_language: zh-CN
prompt_language: en
```

## 1. 必须加载的内部文件

执行任务时按任务类型读取对应文件。不要只读取本入口文件后凭常识自由发挥。

### 所有任务

1. `references/research_and_cultural_safety.md`
2. `references/prompt_compiler.md`

### 参考图拆解 / REDESIGN

继续读取：

3. `references/reference_deconstruction.md`

### 五常 / 多国群像 / CAST_SET

继续读取：

3. `references/five_nation_seed_library.md`
4. `schemas/cast_design.schema.json`

### 单角色设计 / DESIGN

继续读取：

3. `schemas/character_design.schema.json`
4. `templates/output_package.yaml`

### 需要示例

只把以下文件作为结构示范，禁止把示例角色当固定模板：

- `examples/china_design_package.json`
- `examples/china_character_sheet_prompt.txt`

事实来源索引：`references/sources.md`。

## 2. 绝对规则

### 2.1 原创边界

参考图只允许抽象以下方法：

- 符号分级。
- 大图腾、中纹样、小细节的层级逻辑。
- 现代服装骨架与历史文化表层的桥接方式。
- 颜色职责。
- 正式对称与局部不对称的冲突方法。
- 动态件承担运动色的方法。
- 角色设定板的信息布局方法。

不得复制参考角色的完整组合，包括但不限于：

```text
same hairstyle
+ same headpiece topology
+ same upper garment architecture
+ same lower garment architecture
+ same leg asymmetry
+ same prop class
+ same H1 placement
+ same H2 texture placement
+ same color-role mapping
```

若上述相似项达到 5 项或以上，必须重做。

### 2.2 国家不等于单一民族

国家角色不得默认使用某一民族的脸型、肤色或节庆服装代表全国。

优先顺序：

```text
state/civilizational symbols
→ architecture / industrial / material systems
→ historically sourced garment structure
→ craft
→ color roles
→ face styling
```

民族/族群角色则必须单独建立证据池，并说明地域、时期和使用语境。

### 2.3 一主、两副、三细节

每个角色最多：

```yaml
H1_primary_symbol: 1
H2_secondary_systems: 2
H3_micro_details: 3
```

H1 负责 30 米外识别；H2 负责中距离文化语义；H3 负责近看奖励。

禁止把龙、凤、竹、梅、灯笼、长城、青花瓷、京剧、祥云、国旗、地图等同权堆叠。

### 2.4 结构先于贴图

文化元素优先转译为：

```text
领口
肩线
衣襟
腰封
裙片
裤线
鞋跟
护手
扣件
头饰拓扑
道具动作
```

只有无法结构化表达时才进入表面纹样。

### 2.5 历史必须选层

默认：

```yaml
PRIMARY_ERA: 60%
SECONDARY_ERA: 25%
MODERN_BRIDGE: 15%
```

不能把多个时代平均拼盘。无法确认具体时代时明确写“跨时代符号拼装，非考据复原”。

### 2.6 色彩必须有职责

```yaml
BASE_COLOR: 35-60%
STRUCTURE_COLOR: 15-30%
ATTACK_COLOR: 5-15%
MOTION_COLOR: 2-8%
SKIN_METAL_ACCENT: supplement_only
```

基础色负责空间；结构色负责轮廓；攻击色负责焦点；运动色只放在会动的位置。

### 2.7 文字分三类

```text
SEMANTIC_TEXT  真正需要读懂
TEXTURE_TEXT   只承担纹理节奏
IDENTITY_TEXT  身份标签
```

AI 图像和视频中：SEMANTIC_TEXT 最多 1–2 处；TEXTURE_TEXT 改成宽笔痕、大字片段或抽象字形节奏；IDENTITY_TEXT 不得成为主要识别方式。

## 3. 标准工作流

执行顺序不可颠倒：

```text
对象类型
→ 文化/历史证据池
→ 敏感性检查
→ 主历史层
→ 次历史层
→ 现代桥接层
→ 核心张力 A × B
→ 30 米剪影
→ 服装建筑七层
→ H1
→ H2 × 2
→ H3 × ≤3
→ 色彩职责
→ 材料叙事
→ 头部锚点
→ 道具动作
→ 单一不对称破局
→ 动态件
→ 角色设定板布局
→ AI 风险检查
→ Prompt 编译
→ JSON
→ QA
```

## 4. 角色核心命题

每个设计必须写成一个冲突式命题：

```text
A × B
```

例如：

```text
文明 × 克制武力
海权 × 礼仪
革命 × 高级时装
工业扩张 × 个人自由
寒冷边疆 × 纪念碑秩序
传统 × 工业
优雅 × 危险
秩序 × 自由
```

没有张力的角色容易退化为民族服装模特。

## 5. 轮廓与服装建筑

先把角色想象成纯黑剪影。

必须定义：

```yaml
silhouette:
  primary_shape: I|A|X|H|T|S|O|column|inverted_triangle
  widest_point: head|shoulder|hip|skirt|cloak|weapon
  verticality: 0-100
  mass: light|medium|heavy
  motion_direction: ""
  long_range_identifiers: [3_to_5_items]
```

服装必须拆成七层：

```yaml
garment_architecture:
  base_layer: ""
  torso_layer: ""
  shoulder_layer: ""
  waist_layer: ""
  lower_layer: ""
  motion_layer: ""
  foot_layer: ""
```

不要先写“汉服、军装、西装”。先写结构，再说明来源。

## 6. 不对称与动态件

高度正式的角色先建立对称，只允许一个非核心位置破坏对称：

```text
单侧袜/护腿
单边披风
单边耳饰
武器斜线
纹样密度左右差
发束差异
```

动态件最多 3 类；视频安全模式建议最多 2 类。

## 7. 设定板布局

默认 3:2 横版。

```text
LEFT 32%   full-body character
CENTER 38% back garment + head close-up + H1 motif + prop
RIGHT 30%  lower garment + garment construction + legs/shoes + palette/materials
```

完整设定板必须包含：

```text
full_body_front
back_garment
head_closeup
upper_garment_structure
lower_garment_front_back
footwear_and_leg_asymmetry
primary_totem_breakdown
prop_weapon
palette_swatches
material_swatches
5-8 short Chinese annotation notes
```

本地模型文字不稳时，不要求模型直接写中文；保留注释区域并单独输出 `annotation_text`。

## 8. 群像模式

多角色先做 `Design Difference Matrix`，再逐人设计。

硬规则：

- 不允许所有角色都长发。
- 不允许所有角色都拿剑。
- 不允许所有角色都短裙。
- 不允许所有角色都胸口贴国旗。
- 不允许所有角色都金色刺绣。
- 每个角色至少一个独占轮廓锚点。
- 群像角色最宽点不能全部相同。
- 道具线方向不能全部相同。

五常研究种子使用 `references/five_nation_seed_library.md`，但任何种子都只是研究方向，不是固定模板。

## 9. Prompt 输出

每个 DESIGN/REDESIGN 必须生成四套提示词：

```text
PROMPT_A  FULL CHARACTER DESIGN SHEET
PROMPT_B  SINGLE CHARACTER KEY ART
PROMPT_C  VIDEO-SAFE CONTINUITY VERSION
PROMPT_D  DETAIL SHEET
```

具体编译顺序、词法和视频简化规则使用 `references/prompt_compiler.md`。

所有连续镜头提示词必须带：

```text
preserve the same identity, same facial structure, same hairstyle,
same headpiece, same clothing construction, same asymmetry, same footwear,
same prop and same color blocking; do not redesign the character
```

并建立：

```yaml
identity_anchor:
  face_shape: ""
  eye_shape: ""
  hair: ""
  headpiece: ""
  upper_garment: ""
  lower_garment: ""
  leg_asymmetry: ""
  footwear: ""
  prop: ""
  base_color_blocking: ""
```

视频风险必须评估：

```yaml
video_risk_profile:
  text_risk: LOW|MEDIUM|HIGH
  motif_risk: LOW|MEDIUM|HIGH
  hair_risk: LOW|MEDIUM|HIGH
  asymmetry_risk: LOW|MEDIUM|HIGH
  prop_risk: LOW|MEDIUM|HIGH
  recommended_i2v: true
  recommended_control_image: true
  recommended_detail_simplification: ""
```

## 10. 输出模式

### ANALYZE

输出：一句话设计核心、18 层拆解、设计公式、可抽象原则、不可复制组合、AI 制作风险。

### DESIGN

输出：文化研究摘要、核心命题、历史层、剪影、七层服装建筑、H1/H2/H3、色彩、材料、头部、道具、不对称、动态件、设定板布局、四套 Prompt、连续性锚点、JSON、QA。

### REDESIGN

先列原设计保留 5 点、删除 5 点、结构升级 5 点，再生成完全原创的新方案和四套 Prompt。

### CAST_SET

先输出群像主题和差异矩阵，再逐人输出设计包，最后输出群像 Master Prompt。

### PROMPT_ONLY

只输出：

```text
[MASTER CHARACTER SHEET PROMPT]

[CHARACTER KEY ART PROMPT]

[VIDEO SAFE PROMPT]

[DETAIL SHEET PROMPT]

[NEGATIVE PROMPT]
```

不解释。

## 11. QA

完成后按 100 分自检：

```text
A. 无国旗/无国家名仍可识别       20
B. 文化证据与历史层清晰           15
C. 轮廓独特                       15
D. 图腾层级明确                   10
E. 现代桥接成立                   10
F. 材料讲故事                     10
G. 张力明确                        5
H. 不对称受控                      5
I. AI 生成稳定性                   5
J. 原创性                           5
TOTAL                             100
```

低于 80 分自动重做。

出现任一项直接 FAIL：

- 大面积国旗成为主体设计。
- 国家名或国徽成为唯一识别器。
- 四个以上同等级大图腾。
- 多朝代无主次拼盘。
- 单一民族服装等同整个多民族国家。
- 用生理脸型或肤色代表国家制度。
- 直接复制参考角色的发型 + 服装 + 道具完整组合。
- 群像人物剪影高度相似。
- 视频版保留满身细小文字。

## 12. 最小调用

用户输入：

```text
设计法国国拟
```

直接执行 `DESIGN`。

用户输入：

```text
分析这张角色设定稿，按同样的设计方法设计俄罗斯，但不要复制原角色
```

执行 `REDESIGN`，读取 `reference_deconstruction.md` 与 `five_nation_seed_library.md`。

用户输入：

```text
做五常群像
```

执行 `CAST_SET`，先做差异矩阵，再做五个角色。

## 13. 最终原则

**先问对象类型，不先画衣服。**

**先查证据池，不先堆符号。**

**先定冲突，不先选颜色。**

**先定剪影，不先画纹样。**

**先做结构，不先贴国旗。**

**一主、两副、三细节。**

**传统元素必须说明时期和语境。**

**国家不等于单一民族。**

**现代层必须存在。**

**AI 视频版本必须主动降细节。**

**参考作品只学习设计方法，不复制其独特角色组合。**
