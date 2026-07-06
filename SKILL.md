---
name: nation-personification-character-designer
version: 1.1.0
description: 研究、拆解并生成原创的国家拟人、文明拟人、地区拟人、民族/族群、朝代与历史文化角色设计；支持直接生图、自动拆解整图、角色拆解、物料拆解、道具拆解、透明背景抠图、批量资产包与视频连续性资产。将历史、文化、国家特色、族群传统、服装结构、材料工艺、图形系统和现代身份转译为轮廓、服装建筑、纹样层级、色彩职责、头部锚点、道具与 AI 生图/视频提示词。不得 1:1 复刻参考角色的独特发型、头饰、服装、纹样、道具与配色完整组合。
---

# Nation Personification Character Designer

默认语言：中文。

默认输出：中文设计说明 + 英文 AI 图像提示词 + 中文负面约束 + 结构化 JSON。

核心目标：**不靠大面积国旗和国家名字，也能通过轮廓、历史层、材料、工艺、色彩节奏、纹样层级与道具识别角色归属；设计完成后可以继续直接生产角色、物料、道具和透明背景资产。**

---

## 0. 强制启动协议：加载后第一时间显示功能菜单

### 0.1 首次响应规则

当本 Skill 被导入、加载、激活，或用户只说“使用这个 skill / 加载 skill / 开始”而没有明确任务时，**第一条响应必须先显示功能菜单**。

不要先长篇介绍 Skill。
不要先询问国家。
不要先输出设计理论。

固定启动菜单：

```text
🎨 Nation Personification Character Designer v1.1 已加载

请选择工作模式：

[1] 生图设计 IMAGE_GENERATE
    从历史/文化/国家特色开始设计角色，并直接生成角色图或设定稿

[2] 自动拆解整图 AUTO_DECOMPOSE
    自动识别图片中的角色、服装、物料、纹样、道具和可独立资产

[3] 角色拆解 CHARACTER_SPLIT
    拆出完整角色、头部、发型/头饰、上装、下装、腿部与鞋履

[4] 物料拆解 MATERIAL_SPLIT
    拆出布料、金属、漆、玉石、皮革、纹样、扣件、流苏、饰品等物料

[5] 道具拆解 PROP_SPLIT
    拆出武器、手持物、腰挂物、机械件、徽章、器物并生成独立道具设定

[6] 抠图 / 透明背景 CUTOUT
    提取指定角色或物件，保持原造型，输出透明背景资产

[7] 批量资产包 ASSET_PACK
    一次完成角色 + 服装 + 物料 + 道具 + H1/H2 纹样 + manifest

[8] 视频连续性包 VIDEO_CONTINUITY
    为 Wan / LTX / I2V 输出身份锚点、简化版资产与连续镜头提示词

[9] 仅提示词 PROMPT_ONLY
    只输出 Character Sheet / Key Art / Video Safe / Detail / Negative Prompt

直接回复数字、模式名，或上传图片并说明目标。
```

### 0.2 用户已经给出任务时

如果用户在首次调用时已经明确说：

```text
设计秦国
拆这张图
把角色抠出来
拆出所有道具
生成设定稿
```

不要强迫用户再次选菜单。

先用一行显示自动选择结果：

```text
已自动选择：[2] AUTO_DECOMPOSE — 自动拆解整图
```

然后立即执行。

### 0.3 模式自动判定

```yaml
intent_router:
  设计|生成|生图|画角色|设定稿: IMAGE_GENERATE
  拆解这张图|分析全部元素|自动拆图: AUTO_DECOMPOSE
  拆角色|人物拆分|角色部件: CHARACTER_SPLIT
  物料|材质|面料|饰件|纹样素材: MATERIAL_SPLIT
  道具|武器|器物|手持物: PROP_SPLIT
  抠图|去背景|透明背景|提取主体: CUTOUT
  全部拆出|做素材包|资产包|批量拆解: ASSET_PACK
  连续镜头|I2V|Wan|LTX|视频定型: VIDEO_CONTINUITY
  只要提示词|prompt only: PROMPT_ONLY
```

若同时命中多个模式，优先级：

```text
CUTOUT
→ ASSET_PACK
→ AUTO_DECOMPOSE
→ CHARACTER_SPLIT / MATERIAL_SPLIT / PROP_SPLIT
→ VIDEO_CONTINUITY
→ IMAGE_GENERATE
→ PROMPT_ONLY
```

---

## 1. 必须加载的内部文件

执行任务时按任务类型读取对应文件。不要只读取本入口文件后凭常识自由发挥。

### 所有模式

1. `references/research_and_cultural_safety.md`
2. `references/prompt_compiler.md`

### AUTO_DECOMPOSE / CHARACTER_SPLIT / MATERIAL_SPLIT / PROP_SPLIT / CUTOUT / ASSET_PACK

继续读取：

3. `references/asset_decomposition_and_cutout.md`
4. `schemas/asset_manifest.schema.json`
5. `templates/asset_manifest.yaml`

### 参考图拆解 / REDESIGN

继续读取：

3. `references/reference_deconstruction.md`

### 五常 / 多国群像 / CAST_SET

继续读取：

3. `references/five_nation_seed_library.md`
4. `schemas/cast_design.schema.json`

### 单角色设计 / IMAGE_GENERATE / DESIGN

继续读取：

3. `schemas/character_design.schema.json`
4. `templates/output_package.yaml`

### 示例

只把以下文件作为字段和输出结构示范，禁止把示例角色当固定模板：

- `examples/china_design_package.json`
- `examples/china_character_sheet_prompt.txt`

事实来源索引：`references/sources.md`。

---

## 2. 工具执行原则

### 2.1 有图像生成工具时

当用户选择 `IMAGE_GENERATE` 并明确要求生成图片，若环境存在图像生成工具：

1. 完成最小必要设计推导。
2. 编译 Character Sheet 或 Key Art Prompt。
3. **直接调用图像生成工具。**
4. 不要只给提示词然后声称“已经生图”。

### 2.2 有图像编辑/抠图工具时

用户选择 `CUTOUT`、`CHARACTER_SPLIT`、`PROP_SPLIT` 或明确要求实际提取图像时：

1. 先确认当前会话有可用原图。
2. 有图像编辑能力时，优先实际执行编辑/提取。
3. 不得在工具存在时只写“Photoshop 操作建议”代替任务。
4. 输出透明背景时，优先真实 alpha；不要用白底冒充透明背景。

### 2.3 没有对应工具时

诚实说明当前只能完成：

```text
asset manifest
crop / mask plan
cutout prompt
regeneration prompt
ComfyUI node plan
file naming plan
```

不得伪称已经生成 PNG 或已经完成真实抠图。

---

## 3. 原创与文化边界

### 3.1 参考图只抽象方法

允许学习：

- 符号分级。
- 大图腾、中纹样、小细节的层级逻辑。
- 现代服装骨架与历史文化表层的桥接。
- 颜色职责。
- 对称与局部不对称。
- 动态件承担运动色。
- 设定板信息布局。

不得复制完整组合：

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

相似项达到 5 项或以上必须重做。

### 3.2 国家不等于单一民族

国家角色不得默认使用某一民族脸型、肤色或节庆服装代表全国。

优先顺序：

```text
state/civilizational symbols
→ architecture / industrial / material systems
→ historically sourced garment structure
→ craft
→ color roles
→ face styling
```

民族/族群角色必须单独建立证据池，说明地域、时期和使用语境。

### 3.3 一主、两副、三细节

```yaml
H1_primary_symbol: 1
H2_secondary_systems: 2
H3_micro_details: <=3
```

H1：30 米外识别。
H2：中距离文化语义。
H3：近看奖励。

禁止多个同等级图腾无主次堆叠。

### 3.4 结构先于贴图

文化元素优先转译为：

```text
领口 / 肩线 / 衣襟 / 腰封 / 裙片 / 裤线 / 鞋跟
护手 / 扣件 / 头饰拓扑 / 道具动作
```

无法结构化表达时才进入表面纹样。

### 3.5 历史必须选层

```yaml
PRIMARY_ERA: 60%
SECONDARY_ERA: 25%
MODERN_BRIDGE: 15%
```

多个时代不能平均拼盘。

### 3.6 色彩必须有职责

```yaml
BASE_COLOR: 35-60%
STRUCTURE_COLOR: 15-30%
ATTACK_COLOR: 5-15%
MOTION_COLOR: 2-8%
SKIN_METAL_ACCENT: supplement_only
```

### 3.7 文字三分类

```text
SEMANTIC_TEXT  真正需要读懂
TEXTURE_TEXT   只承担纹理节奏
IDENTITY_TEXT  身份标签
```

AI 图像和视频中：SEMANTIC_TEXT 最多 1–2 处；TEXTURE_TEXT 改成宽笔痕、大字片段或抽象字形节奏；IDENTITY_TEXT 不得成为主要识别方式。

---

## 4. IMAGE_GENERATE：角色设计与生图

标准顺序：

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
→ 生图
→ JSON
→ QA
```

每个角色必须有冲突式命题：

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

### 4.1 剪影

```yaml
silhouette:
  primary_shape: I|A|X|H|T|S|O|column|inverted_triangle
  widest_point: head|shoulder|hip|skirt|cloak|weapon
  verticality: 0-100
  mass: light|medium|heavy
  motion_direction: ""
  long_range_identifiers: [3_to_5_items]
```

### 4.2 服装建筑七层

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

### 4.3 不对称与动态件

高度正式角色先建立对称，只允许一个非核心位置破坏对称：

```text
单侧袜/护腿
单边披风
单边耳饰
武器斜线
纹样密度左右差
发束差异
```

动态件最多 3 类；视频安全模式最多 2 类。

### 4.4 默认设定板布局

3:2 横版：

```text
LEFT 32%   full-body character
CENTER 38% back garment + head close-up + H1 motif + prop
RIGHT 30%  lower garment + garment construction + legs/shoes + palette/materials
```

必须包含：

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

本地模型文字不稳时，保留注释区并单独输出 `annotation_text`。

---

## 5. AUTO_DECOMPOSE：自动拆解整图

输入一张角色图、设定稿、概念板或包含角色的场景图后，自动执行：

```text
SOURCE_CLASSIFY
→ SUBJECT_DETECT
→ ASSET_CANDIDATES
→ OCCLUSION_CHECK
→ DEPENDENCY_GRAPH
→ PRIORITY_SORT
→ CUTOUT_PLAN
→ REGENERATION_PLAN
→ ASSET_MANIFEST
```

### 5.1 自动识别资产类型

至少检查：

```yaml
asset_classes:
  CHARACTER_FULLBODY: 完整角色主体
  CHARACTER_HEAD: 脸、头发、头饰
  GARMENT_UPPER: 内搭、外套、肩部、袖子
  GARMENT_LOWER: 裙、裤、衣摆、腰部结构
  LEG_FOOTWEAR: 袜、绑腿、左右鞋靴
  PROP_HANDHELD: 手持武器或器物
  PROP_WORN: 腰挂、胸针、徽章、背负物
  MATERIAL: 布、皮、金属、漆、玉、玻璃、木、珐琅等
  MOTIF_H1: 一级主图腾
  MOTIF_H2: 二级纹样系统
  ORNAMENT: 流苏、扣件、链、饰件
  TEXTURE_TEXT: 作为纹理的文字/书写
```

### 5.2 优先级

```text
P0  完整角色
P1  头部 / 上装 / 下装 / 鞋履 / 主道具
P2  头饰 / H1 / 副道具 / 主要饰件
P3  材料样本 / H2 / 微细节 / 文字纹理
```

### 5.3 遮挡规则

看不见的部分不能假装来自原图。

```yaml
occlusion_policy:
  visible: extract_directly
  partially_occluded: extract_visible_region_and_mark_occluded
  fully_hidden: do_not_claim_extraction
  reconstruction_allowed: false_by_default
```

用户要求补全背面、被手遮住的道具或不可见服装时，明确标记：

```text
SOURCE-DERIVED   原图可见
INFERRED         根据结构推断
REDESIGNED       新设计
```

---

## 6. CHARACTER_SPLIT：角色自动拆件

默认输出拆件清单：

```text
01 character_fullbody
02 character_head_face
03 hair
04 headpiece
05 base_inner_layer
06 upper_garment
07 shoulder_sleeve
08 waist_belt_hardware
09 lower_garment
10 leg_left
11 leg_right
12 footwear_left
13 footwear_right
14 motion_elements
15 identity_anchor
```

原则：

- 左右不对称必须分别拆。
- 发型和头饰默认分开。
- 内搭和外套默认分开。
- 手持道具不并入角色主体资产。
- 不把阴影当服装颜色。
- 不把背景纹样误判成角色纹样。

每个资产输出：

```yaml
asset_id: ""
asset_type: ""
source_visibility: FULL|PARTIAL|HIDDEN
occluded: true|false
bbox_description: ""
edge_complexity: LOW|MEDIUM|HIGH
transparency_target: true
preserve_exact_design: true
regeneration_prompt: ""
cutout_prompt: ""
filename: ""
```

---

## 7. MATERIAL_SPLIT：物料与材质拆解

“物料”分两层：

### 7.1 制作资产物料

```text
fabric_panel
leather_panel
metal_hardware
lacquer_surface
jade_stone_accent
glass_enamel
wood_bone_shell
embroidery_thread
trim_piping
button_clasp
chain_tassel_ribbon
motif_tile
texture_text_tile
```

### 7.2 设计语义物料

每种物料必须回答：

```text
它是什么？
来自哪个历史/文化/工业语境？
在角色上承担什么视觉职责？
是 BASE / STRUCTURE / ATTACK / MOTION 哪一层？
做视频时是否容易闪烁？
是否需要简化？
```

### 7.3 物料板输出

默认生成或编译：

```text
isolated material swatch
front-lit neutral sample
macro texture sample
construction application sample
video-safe simplified sample
```

不得把整件衣服截图当“布料贴图”。必须尽量去除人体阴影、透视和褶皱影响；无法真实还原平铺纹理时标记 `INFERRED_MATERIAL_SWATCH`。

---

## 8. PROP_SPLIT：道具拆解

自动区分：

```text
handheld_primary
handheld_secondary
worn_prop
back_prop
mechanical_prop
ceremonial_prop
weapon
container
ornament_prop
```

每个道具必须输出：

```yaml
prop_id: ""
name: ""
class: ""
source_visibility: FULL|PARTIAL|HIDDEN
silhouette_role: ""
action_role: ""
material_stack: []
main_axis: vertical|horizontal|diagonal|arc
symmetry: symmetric|asymmetric
moving_parts: []
character_dependency: independent|held|worn|attached
cutout_prompt: ""
turnaround_prompt: ""
detail_prompt: ""
filename: ""
```

设定图模式默认包含：

```text
front view
side view
back or underside when visible/known
grip or attachment detail
material callout
H1/H2 motif placement
scale reference relative to character
```

不可见背面不声称“原稿背面”；只能标记推断或重设计。

---

## 9. CUTOUT：抠图 / 透明背景

### 9.1 核心要求

```text
preserve exact subject identity
preserve exact silhouette
preserve original color blocking
preserve asymmetry
preserve prop geometry
remove background only
transparent background
clean alpha edge
no white halo
no colored fringe
no redesign
no extra ornament
no pose change unless requested
```

### 9.2 抠图对象

支持：

```text
完整角色
单个人物
头部
服装部件
鞋履
武器
道具
纹样
饰件
物料块
```

### 9.3 多主体图片

多主体时先编号：

```text
SUBJECT_01 left character
SUBJECT_02 center prop
SUBJECT_03 right garment panel
```

用户未指定时，优先完整角色主体；`AUTO_DECOMPOSE` 或 `ASSET_PACK` 则全部建立 manifest。

### 9.4 边缘 QA

检查：

```text
hair strands
fur
lace
transparent veil
chain
weapon gap
finger gap
tassel threads
semi-transparent glass
motion blur
```

若半透明区域无法可靠保留，必须说明并优先生成重建版透明资产，而不是把边缘硬切坏。

---

## 10. ASSET_PACK：批量资产包

对一张完成角色图或设定稿，默认资产包：

```text
{character_id}/
├─ character/
│  ├─ {id}_character_fullbody.png
│  ├─ {id}_head_face.png
│  ├─ {id}_hair.png
│  └─ {id}_headpiece.png
├─ garment/
│  ├─ {id}_base_layer.png
│  ├─ {id}_upper_garment.png
│  ├─ {id}_waist_hardware.png
│  ├─ {id}_lower_garment.png
│  ├─ {id}_leg_left.png
│  ├─ {id}_leg_right.png
│  ├─ {id}_footwear_left.png
│  └─ {id}_footwear_right.png
├─ props/
│  ├─ {id}_prop_01_{name}.png
│  └─ {id}_prop_02_{name}.png
├─ materials/
│  ├─ {id}_material_01_{name}.png
│  └─ {id}_material_02_{name}.png
├─ motifs/
│  ├─ {id}_motif_H1.png
│  ├─ {id}_motif_H2_A.png
│  └─ {id}_motif_H2_B.png
├─ continuity/
│  ├─ identity_anchor.yaml
│  └─ video_safe_prompt.txt
└─ manifest.json
```

默认所有可独立资产目标为透明背景 PNG。

### 10.1 自动执行策略

有图像工具时：

```text
先创建 manifest
→ P0
→ P1
→ P2
→ P3
→ 每项边缘/身份 QA
→ 汇总失败项
```

不要一次把所有资产塞进一张“素材总图”冒充拆件。

---

## 11. VIDEO_CONTINUITY：视频连续性包

必须建立：

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

连续镜头固定附加：

```text
preserve the same identity, same facial structure, same hairstyle,
same headpiece, same clothing construction, same asymmetry, same footwear,
same prop and same color blocking; do not redesign the character
```

视频风险：

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

视频安全简化：

```text
fine calligraphy → broad ink rhythm
micro embroidery → broad motif silhouette
many tassels → 1–3 tassels
multiple ornaments → one fixed headpiece
complex engravings → plain geometry + distinctive guard/hardware
```

---

## 12. 群像模式

多角色先做 `Design Difference Matrix`。

硬规则：

- 不允许所有角色都长发。
- 不允许所有角色都拿剑。
- 不允许所有角色都短裙。
- 不允许所有角色都胸口贴国旗。
- 不允许所有角色都金色刺绣。
- 每个角色至少一个独占轮廓锚点。
- 群像角色最宽点不能全部相同。
- 道具线方向不能全部相同。

五常研究种子使用 `references/five_nation_seed_library.md`；种子只是研究方向，不是固定人设。

---

## 13. Prompt 输出

IMAGE_GENERATE / DESIGN / REDESIGN 必须生成：

```text
PROMPT_A  FULL CHARACTER DESIGN SHEET
PROMPT_B  SINGLE CHARACTER KEY ART
PROMPT_C  VIDEO-SAFE CONTINUITY VERSION
PROMPT_D  DETAIL SHEET
NEGATIVE  NEGATIVE PROMPT
```

资产拆解模式追加：

```text
PROMPT_E  ISOLATED CHARACTER ASSET
PROMPT_F  ISOLATED MATERIAL SWATCH
PROMPT_G  ISOLATED PROP TURNAROUND
PROMPT_H  TRANSPARENT CUTOUT / BACKGROUND REMOVAL
```

具体词法使用 `references/prompt_compiler.md` 与 `references/asset_decomposition_and_cutout.md`。

---

## 14. 输出模式定义

### IMAGE_GENERATE / DESIGN

文化研究摘要、核心命题、历史层、剪影、七层服装建筑、H1/H2/H3、色彩、材料、头部、道具、不对称、动态件、设定板布局、四套 Prompt、连续性锚点、JSON、QA；用户要求生图时直接生成。

### ANALYZE

一句话设计核心、18 层拆解、设计公式、可抽象原则、不可复制组合、AI 制作风险。

### REDESIGN

原设计保留 5 点、删除 5 点、结构升级 5 点，再生成原创新方案和 Prompt。

### AUTO_DECOMPOSE

自动资产分类表 + 遮挡报告 + P0/P1/P2/P3 优先级 + asset manifest + 每项 cutout/regeneration prompt。

### CHARACTER_SPLIT

角色拆件清单 + 左右不对称保护 + 每项透明资产指令。

### MATERIAL_SPLIT

物料分类 + 文化来源职责 + swatch prompt + video-safe material prompt。

### PROP_SPLIT

道具清单 + 主轴/材料/动作职责 + cutout/turnaround/detail prompt。

### CUTOUT

实际执行可用的图像提取工具；否则输出 mask/cutout plan。不得虚构文件。

### ASSET_PACK

完整目录、manifest、批量执行顺序、文件名和失败项。

### CAST_SET

群像主题和差异矩阵，再逐人输出设计包，最后输出 Master Cast Prompt。

### PROMPT_ONLY

只输出 Prompt，不解释。

---

## 15. QA

### 15.1 角色设计 QA / 100

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

### 15.2 资产拆解 QA

```yaml
asset_qa:
  subject_identity_preserved: true
  silhouette_preserved: true
  color_blocking_preserved: true
  asymmetry_preserved: true
  prop_geometry_preserved: true
  background_removed: true
  alpha_edge_clean: true
  no_white_halo: true
  no_invented_hidden_detail: true
  source_vs_inferred_labeled: true
```

### 15.3 直接 FAIL

- 大面积国旗成为主体设计。
- 国家名或国徽成为唯一识别器。
- 四个以上同等级大图腾。
- 多朝代无主次拼盘。
- 单一民族服装等同整个多民族国家。
- 用生理脸型或肤色代表国家制度。
- 直接复制参考角色完整组合。
- 群像人物剪影高度相似。
- 视频版保留满身细小文字。
- 抠图时重新设计主体。
- 把白色背景当透明背景。
- 将被遮挡、不可见部分声称为“原图拆出”。

---

## 16. 最小调用

```text
设计法国国拟
```

→ `IMAGE_GENERATE`；用户明确说“生成图片”时直接生图。

```text
上传一张设定稿 + 全部拆出来
```

→ `ASSET_PACK`。

```text
拆这张图里的角色和道具
```

→ `AUTO_DECOMPOSE`，然后执行 CHARACTER + PROP 两类。

```text
把中间人物抠出来，透明背景
```

→ `CUTOUT`。

```text
把衣服的布料、金属和纹样做成物料板
```

→ `MATERIAL_SPLIT`。

```text
做五常群像
```

→ `CAST_SET`。

---

## 17. 最终原则

**首次加载，先给用户生图与资产功能菜单。**

**用户已经说清任务，自动选模式并立即执行。**

**先查证据池，不先堆符号。**

**先定冲突，不先选颜色。**

**先定剪影，不先画纹样。**

**先做结构，不先贴国旗。**

**一主、两副、三细节。**

**国家不等于单一民族。**

**现代层必须存在。**

**资产拆解先建 manifest，再拆件。**

**原图可见、结构推断、新设计必须分开标记。**

**抠图只移除背景，不擅自改角色。**

**AI 视频版本必须主动降细节。**

**参考作品只学习设计方法，不复制其独特角色组合。**
