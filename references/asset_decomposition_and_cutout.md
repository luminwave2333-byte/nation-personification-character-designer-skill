# Asset Decomposition and Cutout Protocol

本文件定义 `AUTO_DECOMPOSE`、`CHARACTER_SPLIT`、`MATERIAL_SPLIT`、`PROP_SPLIT`、`CUTOUT` 与 `ASSET_PACK` 的执行标准。

目标不是“看图列元素”，而是把角色图或设定稿转换成真正可用于 ComfyUI、I2V、角色一致性、素材复用和后续重绘的资产清单。

---

## 1. Source Classification

先识别输入类型：

```yaml
source_type:
  CHARACTER_SHEET: 多区域角色设定板
  SINGLE_CHARACTER: 单角色全身或半身图
  GROUP_CAST: 多角色群像
  SCENE: 场景中包含角色与道具
  PROP_SHEET: 道具设定板
  MATERIAL_BOARD: 材质/纹样板
  MIXED_BOARD: 混合概念板
```

记录：

```yaml
source_profile:
  source_type: ""
  subject_count: 0
  panel_count_estimate: 0
  background_complexity: LOW|MEDIUM|HIGH
  text_density: LOW|MEDIUM|HIGH
  source_resolution_quality: LOW|MEDIUM|HIGH
  extraction_feasibility: LOW|MEDIUM|HIGH
```

---

## 2. Detection Pass

按以下顺序扫描，不要只看中心人物：

```text
A. 人物主体
B. 头部与发型
C. 头饰
D. 上装与内搭
E. 腰部结构
F. 下装
G. 左右腿部
H. 左右鞋履
I. 手持道具
J. 穿戴式道具
K. 背负物
L. H1 主图腾
M. H2 次级纹样
N. 材质样本
O. 扣件、链、流苏、挂饰
P. 文字纹理
Q. 设计板中的重复视图
```

重复视图不应作为不同资产重复输出。

---

## 3. Asset Dependency Graph

每个资产标记依赖关系：

```text
ROOT_CHARACTER
├─ HEAD
│  ├─ FACE
│  ├─ HAIR
│  └─ HEADPIECE
├─ GARMENT
│  ├─ BASE_LAYER
│  ├─ UPPER
│  ├─ SHOULDER_SLEEVE
│  ├─ WAIST
│  ├─ LOWER
│  ├─ LEG_LEFT
│  ├─ LEG_RIGHT
│  ├─ FOOTWEAR_LEFT
│  └─ FOOTWEAR_RIGHT
├─ PROP
│  ├─ HELD_PRIMARY
│  ├─ HELD_SECONDARY
│  ├─ WORN
│  └─ ATTACHED
├─ MOTIF
│  ├─ H1
│  ├─ H2_A
│  └─ H2_B
└─ MATERIAL
   ├─ FABRIC
   ├─ METAL
   ├─ STONE_GLASS
   ├─ LACQUER_WOOD
   └─ TRIM_ORNAMENT
```

`attached` 资产可独立拆解，但必须记录 attachment point。

---

## 4. Visibility and Occlusion

每项资产必须标记：

```yaml
source_visibility: FULL|PARTIAL|HIDDEN
occluded: true|false
source_status: SOURCE_DERIVED|INFERRED|REDESIGNED
```

规则：

- `FULL`：轮廓和主要结构完整可见，可直接提取。
- `PARTIAL`：只提取可见区域；不可自动补全后声称来自原图。
- `HIDDEN`：不能执行“原图抠出”。
- `INFERRED`：根据对称、重复视图或服装逻辑推断。
- `REDESIGNED`：为了生产重新设计。

默认 `reconstruction_allowed: false`。

只有用户明确要求“补全/重建/生成背面”时，才允许生成推断资产。

---

## 5. Automatic Priority

```text
P0  ROOT CHARACTER
P1  HEAD / UPPER / LOWER / FOOTWEAR / PRIMARY PROP
P2  HEADPIECE / H1 / SECONDARY PROP / MAJOR ORNAMENT
P3  MATERIAL / H2 / MICRO DETAIL / TEXTURE TEXT
```

原因：先锁定身份与轮廓，再拆高频生产资产，最后处理细节。

---

## 6. Character Split Rules

默认拆分：

```text
character_fullbody
head_face
hair
headpiece
base_inner_layer
upper_garment
shoulder_sleeve
waist_belt_hardware
lower_garment
leg_left
leg_right
footwear_left
footwear_right
motion_elements
```

特殊规则：

- 左右鞋不同：必须分别拆。
- 单腿袜：必须保持左右侧，不允许镜像交换。
- 双马尾/双发束：如结构完全相同可记录 paired asset；如饰件不同必须分别拆。
- 发饰与头发默认分层。
- 外套与内搭默认分层。
- 武器与手持物不并入 `character_fullbody` 的独立资产版本。
- 对完整角色抠图时可保留原本手持物，但 manifest 同时建立独立 prop 资产。

---

## 7. Material Split Rules

不要把“衣服区域截图”直接称为无缝材质。

区分：

```yaml
material_asset_type:
  SOURCE_PATCH: 原图局部材质区域
  FLAT_SWATCH: 尽量去透视的平铺样本
  MACRO_REFERENCE: 微距视觉参考
  INFERRED_MATERIAL_SWATCH: 根据原图重新构建的材质样本
  VIDEO_SAFE_SWATCH: 降高频细节版本
```

每种材质记录：

```yaml
material_id: ""
name: ""
material_family: FABRIC|LEATHER|METAL|LACQUER|STONE|GLASS|WOOD|BONE|ENAMEL|THREAD|OTHER
source_status: SOURCE_DERIVED|INFERRED|REDESIGNED
color_role: BASE|STRUCTURE|ATTACK|MOTION|ACCENT
cultural_or_historical_context: ""
surface_properties:
  roughness: LOW|MEDIUM|HIGH
  reflectivity: LOW|MEDIUM|HIGH
  translucency: LOW|MEDIUM|HIGH
  weave_or_grain: ""
video_risk: LOW|MEDIUM|HIGH
simplification: ""
```

高频风险：

```text
小字
密集刺绣
细链
珠串
发丝
蕾丝孔洞
复杂珐琅
高频金属雕刻
```

Video-safe 版本优先放大主节奏、减少重复单元。

---

## 8. Prop Split Rules

道具首先按依赖分类：

```yaml
character_dependency:
  independent: 可独立存在
  held: 手持
  worn: 穿戴
  attached: 固定在服装/武器/头饰
```

再按用途分类：

```text
weapon
ceremonial_prop
mechanical_prop
container
instrument
writing_tool
measurement_tool
worn_prop
ornament_prop
```

每件道具必须锁定：

```text
main axis
overall proportion
grip/attachment point
material stack
moving parts
H1/H2 placement
relationship to character scale
```

如果原图只显示斜视图，不生成“真实正投影视图”声明；生成的 turnaround 必须标为 inferred turnaround。

---

## 9. Cutout Protocol

### 9.1 Base Instruction

```text
remove the background only; preserve the exact subject identity, exact silhouette,
exact pose, exact clothing construction, exact asymmetry, exact footwear,
exact prop geometry and exact color blocking; transparent background, clean alpha,
no white halo, no colored fringe, no extra decoration, no redesign
```

### 9.2 Alpha Edge Priority

按风险从高到低检查：

```text
hair strands
fur
lace
transparent veil
glass
chain
tassel
finger gaps
weapon cutouts
motion blur
```

### 9.3 Semi-transparent Objects

玻璃、薄纱、半透明材质不得直接硬阈值删除。

无法可靠保留 alpha 时：

1. 标记 `alpha_uncertain: true`。
2. 保留原图可见区域。
3. 生成 `transparent_regeneration_prompt`。
4. 不伪称为完美抠图。

### 9.4 Multi-subject Images

先编号：

```text
SUBJECT_01: left character
SUBJECT_02: center prop
SUBJECT_03: right garment panel
```

单次 CUTOUT 未指定对象时，默认选择面积最大且最完整的人物主体。

---

## 10. Regeneration Prompts

### Character Asset

```text
isolated exact same character asset, preserve the same identity, face, hairstyle,
headpiece, costume construction, asymmetry and color blocking, neutral canonical pose,
full body centered, transparent background, clean alpha edge, no prop unless specified,
no redesign, no alternate costume, no added accessory
```

### Garment Asset

```text
isolated garment component from the same design, preserve exact construction seams,
panel lengths, closure logic, motif placement and color blocking, displayed as a clean
production asset, no mannequin details unless required, transparent background
```

### Material Swatch

```text
isolated flat material swatch derived from the source design, front-lit neutral sample,
minimal perspective, reduced body shadow, preserve base color and material identity,
clean square sample, no garment silhouette, no logo, no text unless texture text is the target
```

### Prop Turnaround

```text
same prop design, isolated professional prop concept sheet, front view, side view,
attachment or grip detail, material callouts, preserve exact proportion and main axis,
no redesign, no extra ornament, clean neutral design board
```

### Transparent Cutout

```text
remove background only, exact same subject, exact same pose and silhouette,
transparent background, clean alpha edge, preserve fine hair and tassel edges,
no white halo, no colored fringe, no redesign, no added details
```

---

## 11. File Naming

使用小写 snake_case；文化专名使用稳定英文转写。

```text
{id}_character_fullbody.png
{id}_head_face.png
{id}_hair.png
{id}_headpiece.png
{id}_upper_garment.png
{id}_lower_garment.png
{id}_leg_left.png
{id}_leg_right.png
{id}_footwear_left.png
{id}_footwear_right.png
{id}_prop_01_{name}.png
{id}_material_01_{name}.png
{id}_motif_h1.png
{id}_motif_h2_a.png
{id}_motif_h2_b.png
```

不要使用：

```text
image1.png
final.png
final2.png
new.png
weapon.png
```

---

## 12. Batch Execution Report

ASSET_PACK 完成或计划完成后输出：

```yaml
batch_report:
  total_assets: 0
  extracted: 0
  regenerated: 0
  inferred: 0
  blocked_by_occlusion: 0
  failed: 0
  transparent_assets: 0
  qa_failed_assets: []
  next_priority: []
```

每一个失败项说明具体原因，不使用“模型问题”作为笼统结论。
