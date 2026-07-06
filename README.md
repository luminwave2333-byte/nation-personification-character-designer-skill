# Nation Personification Character Designer Skill

用于原创国家拟人、文明拟人、地区拟人、民族/族群、朝代与历史文化角色设计，以及角色图/设定稿的自动资产拆解。

当前版本：`v1.1.0`

## 加载后首先出现功能菜单

Skill 被加载或激活后，在没有明确任务时会先显示：

```text
[1] 生图设计 IMAGE_GENERATE
[2] 自动拆解整图 AUTO_DECOMPOSE
[3] 角色拆解 CHARACTER_SPLIT
[4] 物料拆解 MATERIAL_SPLIT
[5] 道具拆解 PROP_SPLIT
[6] 抠图 / 透明背景 CUTOUT
[7] 批量资产包 ASSET_PACK
[8] 视频连续性包 VIDEO_CONTINUITY
[9] 仅提示词 PROMPT_ONLY
```

用户已经直接给出任务时，Skill 会自动判定模式并立即执行，不强迫再次选择。

## 核心设计流程

它不是“国家名 + 国旗 + 民族服装”的提示词集合，而是一套设计流程：

```text
对象类型
→ 文化/历史证据池
→ 历史层级
→ 核心张力
→ 剪影
→ 服装建筑
→ 一主、两副、三细节
→ 色彩职责
→ 材料叙事
→ 头部锚点
→ 道具动作
→ 不对称破局
→ AI 风险检查
→ Prompt 编译
→ 生图 / JSON / QA
```

## 资产拆解流程

上传一张角色图、角色设定稿或概念板后，可执行：

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

自动识别：

```text
完整角色
脸 / 头部
头发
头饰
内搭
上装
肩袖
腰部结构
下装
左右腿
左右鞋履
手持道具
穿戴式道具
H1 主图腾
H2 二级纹样
布料 / 皮革 / 金属 / 漆 / 玉石 / 玻璃 / 木等物料
流苏 / 扣件 / 链 / 饰件
文字纹理
```

拆解遵循 `SOURCE_DERIVED / INFERRED / REDESIGNED` 三状态。被遮挡或看不见的部分不会被伪称为“从原图抠出”。

## 直接加载

GitHub 文件页：

```text
https://github.com/luminwave2333-byte/nation-personification-character-designer-skill/blob/main/SKILL.md
```

Raw：

```text
https://raw.githubusercontent.com/luminwave2333-byte/nation-personification-character-designer-skill/main/SKILL.md
```

私有仓库状态下，访问方必须具有 GitHub 鉴权和本仓库读取权限；不带鉴权的第三方一键导入器通常无法读取 private raw URL。

## 目录

```text
.
├─ SKILL.md
├─ README.md
├─ references/
│  ├─ reference_deconstruction.md
│  ├─ asset_decomposition_and_cutout.md
│  ├─ five_nation_seed_library.md
│  ├─ research_and_cultural_safety.md
│  ├─ prompt_compiler.md
│  └─ sources.md
├─ schemas/
│  ├─ character_design.schema.json
│  ├─ cast_design.schema.json
│  └─ asset_manifest.schema.json
├─ templates/
│  ├─ input_brief.yaml
│  ├─ output_package.yaml
│  └─ asset_manifest.yaml
└─ examples/
   ├─ china_design_package.json
   └─ china_character_sheet_prompt.txt
```

## 使用示例

### 生图

```text
使用这个 Skill，设计秦统一六国后的秦国拟人，并直接生成 3:2 横版角色设定稿。
```

### 自动全部拆解

```text
上传设定稿后：全部拆出来，做 ASSET_PACK。
```

默认先建立 manifest，再按 P0/P1/P2/P3 拆角色、服装、道具、纹样和物料。

### 角色拆件

```text
拆出这个角色的完整人物、头部、发型、头饰、上装、下装、左右鞋。
```

### 物料板

```text
把角色上的布料、金属、漆面、玉色件和主纹样拆成独立物料板。
```

### 道具

```text
拆出所有武器和腰挂物，每个道具做独立透明资产和 turnaround prompt。
```

### 抠图

```text
把中间角色抠出来，保持原造型和姿势，透明背景。
```

有可用图像编辑工具时，Skill 要求实际执行图像提取；没有对应工具时只能诚实输出 mask/cutout plan 与提示词，不能虚构 PNG 已经生成。

### 群像

```text
使用 nation-personification-character-designer，设计五常国拟群像。先做 Design Difference Matrix，再逐人设计，要求五人剪影、材料、道具线方向和历史层明显不同。
```

## 默认资产包

```text
{character_id}/
├─ character/
├─ garment/
├─ props/
├─ materials/
├─ motifs/
├─ continuity/
└─ manifest.json
```

可独立资产默认目标为透明背景 PNG。

## 核心原则

- 首次加载先给用户生图与资产功能选项。
- 用户说清任务后自动选模式并立即执行。
- 轮廓先于纹样。
- 结构先于贴图。
- 历史必须选主次层。
- 国家不等于单一民族。
- 文化符号使用“一主、两副、三细节”。
- 国旗和国家名不能成为主要识别方式。
- 现代国家角色必须存在现代桥接层。
- 资产拆解先建 manifest，再进行抠图或重建。
- 原图可见、结构推断、新设计必须分开标记。
- 抠图只移除背景，不擅自修改角色或道具。
- 参考作品只抽象设计方法，不复制完整角色组合。
- Wan / LTX / I2V 连续镜头必须输出 identity anchor 与 video risk profile。

## 输出

单角色设计默认输出完整设计包、四套英文提示词、负面约束、视频连续性锚点、结构化 JSON 和 QA。

资产拆解默认输出 asset manifest、遮挡报告、优先级、文件名、cutout/regeneration prompt 与 batch report。

群像默认先输出差异矩阵，再逐个设计，并生成 Master Cast Prompt。
