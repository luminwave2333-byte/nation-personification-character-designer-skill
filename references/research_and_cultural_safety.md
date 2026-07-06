# Research and Cultural Safety

## 1. Why this file exists

国家拟人最容易犯的错误不是“画得不漂亮”，而是：

- 把一个时代当全部历史
- 把一个族群当整个国家
- 把礼服当日常服
- 把博物馆藏品的宗教/婚礼语境删掉，只留“好看”
- 把旅游符号当文化

本文件用于强制研究与标注。

## 2. Evidence ledger template

```yaml
evidence_ledger:
  - claim: "..."
    level: A|B|C|D
    source_title: "..."
    source_org: "..."
    source_url: "..."
    design_use: "..."
    context_limit: "..."
```

### context_limit examples

- “19 世纪俄罗斯北部/中部女性服装，不代表全国现代日常服。”
- “宫廷服饰语境，不应直接描述为普通民众服装。”
- “这是设计推断，不是历史事实。”

## 3. Intangible heritage handling

活态文化强调由社群认同、实践与持续再创造。

因此：

- 不要把文化冻结成“古老、神秘、原始”。
- 优先说明传统的使用场景。
- 若未确认社群归属，避免把某工艺归给整个国家。

## 4. Multi-ethnic states

国家角色默认不指定“民族脸”。

识别优先顺序：

```text
state/civilizational symbols
→ architecture/industrial/material systems
→ historically sourced clothing structure
→ craft
→ color roles
→ face styling
```

面部只服务角色人格。

## 5. Religious elements

分三类：

```text
SACRED_CORE   神圣文本、法器、礼拜服、圣物
RITUAL_FORM   仪式结构、穿着方式、空间秩序
AESTHETIC_ECHO 几何、色彩、材料、光影联想
```

无明确语境时只用 AESTHETIC_ECHO。

## 6. Political current-events layer

如果用户要求“结合当前国际局势”，必须先进行实时网络检索。

设计说明中分开：

```text
historical/cultural identity
current political commentary
```

不要把当前政府、领导人或短期事件等同于整个国家或民族。

## 7. Originality gate

参考作品存在明确独特角色时，计算结构相似性。

以下项目每重复一项 +1：

- same hair silhouette
- same headpiece topology
- same upper garment architecture
- same lower garment architecture
- same leg asymmetry
- same prop class
- same H1 totem placement
- same H2 texture placement
- same color role mapping

总分 >= 5９必须重做。
