# Nation Personification Character Designer Skill

用于原创国家拟人、文明拟人、地区拟人、民族/族群、朝代与历史文化角色设计的可加载 Skill。

它不是“国家名 + 国旗 + 民族服装”的提示词集合，而是一套完整设计流程：

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
→ JSON / QA
```

## 直接加载

GitHub 文件页：

```text
https://github.com/luminwave2333-byte/nation-personification-character-designer-skill/blob/main/SKILL.md
```

Raw：

```text
https://raw.githubusercontent.com/luminwave2333-byte/nation-personification-character-designer-skill/main/SKILL.md
```

私有仓库状态下，访问方必须具有 GitHub 鉴权和本仓库读取权限；不带鉴权的第三方“一键导入器”通常无法读取 private raw URL。

## 目录

```text
.
├─ SKILL.md
├─ README.md
├─ references/
│  ├─ reference_deconstruction.md
│  ├─ five_nation_seed_library.md
│  ├─ research_and_cultural_safety.md
│  ├─ prompt_compiler.md
│  └─ sources.md
├─ schemas/
│  ├─ character_design.schema.json
│  └─ cast_design.schema.json
├─ templates/
│  ├─ input_brief.yaml
│  └─ output_package.yaml
└─ examples/
   ├─ china_design_package.json
   └─ china_character_sheet_prompt.txt
```

## 使用示例

```text
使用 nation-personification-character-designer，设计五常国拟群像。先做 Design Difference Matrix，再逐人设计，要求五人剪影、材料、道具线方向和历史层明显不同。
```

```text
按照 nation-personification-character-designer 分析这张角色设定稿，只学习符号分级、色彩职责和动态件逻辑；重新设计原创中国国拟，不复制原角色的双马尾、冠饰、短西装、书法短裙、单黑袜和长剑组合。
```

```text
使用 PROMPT_ONLY 模式，为俄罗斯国拟输出 Character Sheet、Key Art、Video Safe、Detail Sheet 和 Negative Prompt。
```

## 核心原则

- 轮廓先于纹样。
- 结构先于贴图。
- 历史必须选主次层。
- 国家不等于单一民族。
- 文化符号使用“一主、两副、三细节”。
- 国旗和国家名不能成为主要识别方式。
- 现代国家角色必须存在现代桥接层。
- 参考作品只抽象设计方法，不复制完整角色组合。
- Wan / LTX / I2V 连续镜头必须输出 identity anchor 与 video risk profile。

## 输出

单角色默认输出完整设计包、四套英文提示词、负面约束、视频连续性锚点、结构化 JSON 和 QA。

群像默认先输出差异矩阵，再逐个设计，并生成 Master Cast Prompt。
