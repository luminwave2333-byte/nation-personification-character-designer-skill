# Prompt Compiler

## 1. Goal

把设计说明转换成 AI 模型更容易保持一致的提示词。

原则：

- 先身份锚点
- 再轮廓
- 再服装结构
- 再大色块
- 再大纹样
- 最后微细节

不要把 30 个名词一口气并列。

## 2. Prompt order

```text
[FORMAT]
[TARGET + ARCHETYPE]
[IDENTITY ANCHOR]
[SILHOUETTE]
[HEAD]
[UPPER GARMENT]
[LOWER GARMENT]
[LEG + FOOT]
[PROP]
[H1]
[H2]
[COLOR ROLES]
[MATERIALS]
[ASYMMETRY]
[TENSION]
[LAYOUT]
[QUALITY]
```

## 3. Identity anchor grammar

Use definitive language:

```text
she has / he has / the character has
wears exactly
one [item]
single [item]
left leg only
right shoulder only
```

Avoid:

```text
maybe
inspired by many things
various accessories
ornate details everywhere
```

## 4. Motif grammar

Bad:

```text
Chinese dragon, calligraphy, clouds, mountains, phoenix, bamboo, plum blossom, jade, red lanterns
```

Good:

```text
one dominant S-curve dragon-derived structural motif,
secondary vertical ink rhythm on selected skirt panels,
small metallic-thread knot details only at the waist and cuffs
```

## 5. Color grammar

Bad:

```text
red white black gold color scheme
```

Good:

```text
matte ivory-white dominates the garment field,
deep ink-black defines hair, seams and lower-leg structure,
vermilion red appears only at the inner collar, eye focus and two sharp garment cuts,
warm gold is restricted to moving tassel-like hardware
```

## 6. Video-safe conversion

Replace:

```text
fine calligraphy → broad ink strokes / two large glyph-like marks
micro embroidery → broad embroidered silhouette
many tassels → 2–3 tassels
multiple hair ornaments → one fixed headpiece
complex sword engraving → plain metal blade + distinctive guard
```

## 7. Flux/Qwen still prompt hints

For character sheet:

```text
professional 2D character concept art
fashion design board
full-body figure dominant on the left
orthographic garment studies
clean white design paper
thin technical callout lines
reserved annotation boxes
material swatches
palette chips
precise line art
```

## 8. Wan/LTX I2V continuity block

Always append:

```text
preserve the same identity, same facial structure, same hairstyle,
same headpiece, same outfit construction, same asymmetry, same footwear,
same prop and same color blocking; do not redesign the character
```

Then:

```text
one primary action only, one controlled camera movement only
```

## 9. Chinese annotation package

Generate separately:

```yaml
annotation_text:
  - target: headpiece
    text: "高冠结构，压住顶部重心"
  - target: shoulder
    text: "硬肩建立国家礼仪感"
  - target: motif
    text: "主图腾只在远距位置出现"
  - target: palette
    text: "攻击色集中于脸与胸口"
  - target: asymmetry
    text: "单侧结构打破徽章式对称"
```

For local image models, do not ask the model to render these characters. Overlay later.
