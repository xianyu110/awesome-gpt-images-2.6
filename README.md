# Awesome GPT Image 2.6（Images 2.6）

> 非官方精选合集：介绍 OpenAI **Images 2.6** / **GPT Image 2.6**（Flare & Sunburst）的模型特性、接入方式与可复现案例画廊。  
> 图例与提示词均整理自 OpenAI 官方公告与开发者文档；仓库内图片为文档示例的镜像缓存，版权归原作者/OpenAI 所有，仅作学习索引。

![Images 2.6](images/images-2-6-social.png)

| 项目 | 内容 |
|------|------|
| 产品名（ChatGPT / Codex） | **Images 2.6** |
| API 型号 | `gpt-image-2.6-flare`、`gpt-image-2.6-sunburst` |
| 厂商 | OpenAI |
| 公开发布 | **2026-11-04**（UTC 口径的官方发布日） |
| 可用入口 | ChatGPT、Codex、Images API（`/v1/images/generations`、`/v1/images/edits`） |

本页 GitHub Pages：<https://xianyu110.github.io/awesome-gpt-images-2.6/>

---

## 简介

OpenAI 于 2026 年 11 月 4 日推出 **Images 2.6**。官方称相对上一代，图像生成速度约 **提升 50%**；在 ChatGPT / Codex 全订阅档位可用，API 侧提供两档模型：

- **GPT Image 2.6 Flare**（`gpt-image-2.6-flare`）：偏重**快速迭代**。`quality=low` 时官方称最快约 **6 秒**；海报、产品图等版式质量有改善。
- **GPT Image 2.6 Sunburst**（`gpt-image-2.6-sunburst`）：偏重**细节与编辑一致性**；`quality` 为 medium/high 时更适合精细构图与多轮精修。

两款模型均支持文生图与图生图/编辑，官方强调角色、产品与品牌细节在编辑轮次中更易保持一致，并新增 **原生 GIF 动画**、**SVG 导出**、API 侧 **markup（标注）引导编辑**，以及独立的 **`complexity`（规划复杂度）** 参数。

> 命名说明：对外产品页多用 **Images 2.6**；API 文档与模型卡使用 **GPT Image 2.6 Flare / Sunburst**。社区口语中的「GPT Images 2.6」一般即指上述产品线。

主要来源：[Introducing Images 2.6](https://openai.com/index/introducing-images-2-6/) · [Image generation](https://developers.openai.com/api/docs/guides/image-generation) · [Image prompting](https://developers.openai.com/api/docs/guides/image-prompting)

---

## 模型特性

### 能力边界（据官方文档）

| 能力 | 说明 |
|------|------|
| 生成 | `POST /v1/images/generations`，文本提示 → 图像 |
| 编辑 | `POST /v1/images/edits`，参考图 / mask / markup + 提示 → 图像 |
| 输入模态 | 文本、图像 |
| 输出格式 | `png` / `jpeg` / `webp`；**生成**另支持 `svg`、`gif`（编辑端不支持 GIF/SVG 输出） |
| 透明背景 | `background=transparent`，配合 PNG/WebP/SVG |
| GIF | `frame_count`（约 2–64，以当前文档为准）、`frame_duration_ms`（约 20–2000，10ms 对齐） |
| SVG | 静态矢量路径；不可流式、不可 partial；`n=1` |
| Markup 编辑 | 与首张输入图同尺寸的透明 PNG 标注层，圈出要改区域 |
| 流式 | 支持 partial images（GIF/SVG 除外） |

### 关键参数（GPT Image 2.6）

| 参数 | 取值 | 作用 |
|------|------|------|
| `model` | `gpt-image-2.6-flare` / `gpt-image-2.6-sunburst` | 选型 |
| `quality` | `auto` / `low` / `medium` / `high` / `xhigh` / `max` | 渲染细节与保真度 |
| `complexity` | `low` / `medium` / `high`（省略时默认 `high`） | 生成前规划程度；与 quality 独立，无单独计费项 |
| `size` | `auto` 或如 `1024x1024`、`1536x1024`、`1024x1536`；自定义宽高需为 16 倍数，纵横比约 1:3–3:1 | 输出尺寸 |
| `background` | `auto` / `opaque` / `transparent` | 背景 |
| `output_format` | `png` / `jpeg` / `webp` / `svg` / `gif` | 输出格式 |
| `markup` | 透明 PNG 叠加层（仅 edits） | 局部编辑定位 |

### 与上一代的差异（官方表述摘要）

- 速度：Images 2.6 宣称较之前约快 **50%**；Flare 在 low quality 可达约 6 秒。
- 新增：原生 **GIF**、**SVG**、API **markup**、独立 **`complexity`**。
- 定位延续 2.5 的 Flare（快）/ Sunburst（精）双轨；官方建议从对应的 2.5 工作流迁移到 2.6 同名档位，再对比延迟与质量。
- 编辑侧：官方称角色/产品/品牌一致性更好；2.6 **不支持** `input_fidelity`、`reasoning.summary`（文档明确）。

### 定价快照（模型卡，可能变动）

Flare 与 Sunburst 公开模型卡上的 token 价目一致（以官网为准）：

| 类型 | 价格 |
|------|------|
| 文本输入 | $5 / 1M tokens（缓存输入 $1.25） |
| 图像输入 | $8 / 1M tokens（缓存输入 $2） |
| 图像输出 | $30 / 1M tokens |

详见：[Flare 模型卡](https://developers.openai.com/api/docs/models/gpt-image-2.6-flare) · [Sunburst 模型卡](https://developers.openai.com/api/docs/models/gpt-image-2.6-sunburst) · [Pricing](https://openai.com/api/pricing/)

### 已知限制（官方）

- 复杂提示可能延迟较长（文档提到可达约 2 分钟量级）。
- 文字渲染已改善，但精确排版仍可能出错。
- 跨多次生成的角色/品牌一致性仍可能漂移，需复述约束或固定参考图。
- 结构化构图的精确定位仍有挑战。
- 内容审核：`moderation=auto|low`；违规返回 `moderation_blocked` 等。

---

## 快速开始 / 接入

### 官方（推荐）

1. 在 [OpenAI Platform](https://platform.openai.com/) 创建项目并完成组织验证（部分图像能力需要）。
2. 设置环境变量 `OPENAI_API_KEY`。
3. 调用 Images API，例如：

```bash
curl -X POST "https://api.openai.com/v1/images/generations" \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "gpt-image-2.6-sunburst",
    "prompt": "A minimalist product poster of a ceramic mug on limestone, soft window light, no text",
    "quality": "medium",
    "complexity": "low",
    "size": "1024x1024",
    "output_format": "png"
  }'
```

Python 示例（摘自官方引导的模型 ID 习惯）：

```python
from openai import OpenAI
import base64

client = OpenAI()
result = client.images.generate(
    model="gpt-image-2.6-flare",
    prompt="A children's book drawing of a veterinarian listening to a baby otter with a stethoscope.",
    quality="medium",
    size="1024x1024",
)
Path = __import__("pathlib").Path
Path("otter.png").write_bytes(base64.b64decode(result.data[0].b64_json))
```

更多： [Image generation guide](https://developers.openai.com/api/docs/guides/image-generation) · [Image prompting guide](https://developers.openai.com/api/docs/guides/image-prompting)

ChatGPT 用户可直接在对话中使用 Images 2.6（含 GIF）；无需写代码。

### 国内/兼容访问笔记（可选）

官方 Platform / ChatGPT 仍是首选。若因网络或支付需要临时走兼容入口，下列为社区常用渠道备忘（**非广告**；可用性、合规与计费以各站点当时条款为准，本仓库不做保证）。调用 API 时模型名仍建议写 `gpt-image-2.6-flare` / `gpt-image-2.6-sunburst`（以网关实际挂载为准）。

| 渠道类型 | 访问网址 | 适用场景 |
|----------|----------|----------|
| GPT 国内高速镜像站 | https://trygpt.asia/list/#/home | 适合网页端免梯直接对话、工作流轻量体验 |
| MomoAI 包月套餐 | https://momoai.asia/home | 适合持续使用、希望按月订阅套餐的用户 |
| 全模型 API 聚合站 | https://tryallapi.com/ | 适合开发者调用、低延迟直连、多模型一站式接入 |
| 官方代充通道 | https://chongzhi.trygpt.asia/ | 解决海外支付门槛，官方账户直充 |
| Trymodel 多模型聊天 | https://trymodel.chat/ | 适合在同一网页中体验不同 AI 模型的对话 |

补充：在 tryallapi 注册/邀请场景可自行附加 `aff=5A6A`（例如 `https://tryallapi.com/?aff=5A6A`），非必需。相关工具店铺（可选）：[https://wzyp.cn/shop/maynorai](https://wzyp.cn/shop/maynorai)

---

## 案例画廊

下列案例提示词与示意输出整理自 OpenAI 官方文档/公告、官方 X，以及**可核实的社区 X 原帖**（作者自称 Images 2.6 / GPT Image 2.6 / Flare / Sunburst，且附带生成结果）。图片保存在本仓库 `images/`。**不编造提示词**；仅收录原帖可见原文，不完整则标明「提示词见原帖」或「提示词未公开」。跳过纯新闻转发、隐私风险真人照动图、明显 IP 侵权角色。

### 1. Complexity 对比：纸雕足球夺冠（Sunburst）

**场景**：同一提示、同一 `quality=medium`，对比 `complexity=low|medium|high` 对构图与「CHAMPIONS」位置的影响。

**提示词**：

```text
A premium handcrafted layered paper-cut illustration of a football team celebrating a championship victory inside a packed stadium.
```

**参数提示**：`model=gpt-image-2.6-sunburst`，`size=1024x1536`，`quality=medium`

| Low | Medium | High |
|-----|--------|------|
| ![](images/paper-cut-football-effort-low-gpt-image-2-6-sunburst.webp) | ![](images/paper-cut-football-effort-medium-gpt-image-2-6-sunburst.webp) | ![](images/paper-cut-football-effort-high-gpt-image-2-6-sunburst.webp) |

来源：[Image prompting · Compare complexity](https://developers.openai.com/api/docs/guides/image-prompting)

---

### 2. 原生循环 GIF：提灯小猴（Sunburst）

**提示词**：

```text
A little monkey in an olive-green hooded coat stands in a snowy pine forest at dusk. A wooden cabin glows in the background. The monkey holds a glowing amber paper lantern at chest height, slowly lifts it, looks up and blinks, then lowers it back to its starting pose. Keep the lantern in its hands and the camera still. Make the motion loop smoothly.
```

**参数提示**：`output_format=gif`，`size=512x512`，`quality=high`，`frame_count=9`，`frame_duration_ms=200`（约 1.8s 循环）

![Monkey lantern GIF](images/monkey-lantern-gpt-image-2-6-sunburst.gif)

来源：[Image prompting · Generate a looping GIF](https://developers.openai.com/api/docs/guides/image-prompting)

---

### 3. 纪实摄影风：渔民与狗（Flare vs Sunburst）

**提示词**：

```text
Create a photorealistic candid photograph of an elderly sailor standing on a small fishing boat.
He has weathered skin with visible wrinkles, pores, and sun texture, and a few faded traditional sailor tattoos on his arms.
He is calmly adjusting a net while his dog sits nearby on the deck. Shot like a 35mm film photograph, medium close-up at eye level, using a 50mm lens.
Soft coastal daylight, shallow depth of field, subtle film grain, natural color balance.
The image should feel honest and unposed, with real skin texture, worn materials, and everyday detail. No glamorization, no heavy retouching.
```

| Flare | Sunburst |
|-------|----------|
| ![](images/photorealism-gpt-image-2-6-flare.webp) | ![](images/photorealism-gpt-image-2-6-sunburst.webp) |

来源：[Image prompting · Control style and lighting](https://developers.openai.com/api/docs/guides/image-prompting)

---

### 4. 信息图：全自动咖啡机流程（Sunburst）

**提示词**：

```text
Create a detailed Infographic of the functioning and flow of an automatic coffee machine like a Jura.
From bean basket, to grinding, to scale, water tank, boiler, etc.
I'd like to understand technically and visually the flow.
```

![Coffee machine infographic](images/infographic-coffee-machine-gpt-image-2-6-sunburst.webp)

来源：[Image prompting · Explain a process visually](https://developers.openai.com/api/docs/guides/image-prompting)

---

### 5. 精确文案广告：Thread「Yours to Create.」（Sunburst）

**提示词**：

```text
Give me a cool in culture ad / fashion shot for a brand called Thread.
It's a hip young street brand. The ad shows a group of friends hanging out together with the tagline "Yours to Create."
Make it feel like a polished campaign image for a youth streetwear audience: stylish, contemporary, energetic, and tasteful.
Use clean composition, strong color direction, natural poses, and premium fashion photography cues.
Render the tagline exactly once, clearly and legibly, integrated into the ad layout.
No extra text, no watermarks, no unrelated logos.
```

![Thread ad](images/thread-ad-gpt-image-2-6-sunburst.webp)

来源：[Image prompting · Render exact text](https://developers.openai.com/api/docs/guides/image-prompting)

---

### 6. SVG Logo：Field & Flour 面包店

**提示词**：

```text
Create an original, non-infringing logo for a company called Field & Flour, a local bakery.
The logo should feel warm, simple, and timeless. Use clean, vector-like shapes, a strong silhouette, and balanced negative space.
Favor simplicity over detail so it reads clearly at small and large sizes. Flat design, minimal strokes, no gradients unless essential.
Fully transparent background. Deliver a single centered logo with generous padding, clean alpha edges, and no solid backdrop, scenery, checkerboard, or watermark.
```

**参数提示**：`output_format=svg`，`n=1`

| Flare SVG | Sunburst SVG | Sunburst 栅格示例 |
|-----------|--------------|-------------------|
| ![](images/field-and-flour-gpt-image-2-6-flare.svg) | ![](images/field-and-flour-gpt-image-2-6-sunburst.svg) | ![](images/logo-generation-1-gpt-image-2-6-sunburst.webp) |

来源：[Image prompting · Generate SVG / Design a reusable logo](https://developers.openai.com/api/docs/guides/image-prompting)

---

### 7. 四格漫画：宠物在家狂欢（Sunburst）

**提示词**：

```text
Create a short vertical comic-style reel with 4 panels.
Panel 1: The owner leaves through the front door. The pet is framed in the window behind them, small against the glass, eyes wide, paws pressed high, the house suddenly quiet.
Panel 2: The door clicks shut. Silence breaks. The pet slowly turns toward the empty house, posture shifting, eyes sharp with possibility.
Panel 3: The house transformed. The pet sprawls across the couch like it owns the place, crumbs nearby, sunlight cutting across the room like a spotlight.
Panel 4: The door opens. The pet is seated perfectly by the entrance, alert and composed, as if nothing happened.
```

![Comic reel](images/comic-reel-gpt-image-2-6-sunburst.webp)

来源：[Image prompting · Turn a story into a comic strip](https://developers.openai.com/api/docs/guides/image-prompting)

---

### 8. UI 预览：农贸市场 App（Sunburst）

**提示词**：

```text
Create a realistic mobile app UI mockup for a local farmers market.
Show today's market with a simple header, a short list of vendors with small photos and categories, a small "Today's specials" section, and basic information for location and hours.
Design it to be practical, and easy to use. White background, subtle natural accent colors, clear typography, and minimal decoration.
It should look like a real, well-designed, beautiful app for a small local market.
Place the UI mockup in an iPhone frame.
```

![Farmers market UI](images/ui-farmers-market-gpt-image-2-6-sunburst.webp)

来源：[Image prompting · Create an interface preview](https://developers.openai.com/api/docs/guides/image-prompting)

---

### 9. 科学教育图：细胞呼吸一览（Sunburst）

**提示词**：

```text
Create a simple biology diagram titled "Cellular Respiration at a Glance" for high school students.

Show how glucose turns into energy inside a cell. Include glycolysis, the Krebs cycle, and the electron transport chain.
Use arrows to connect the steps, and label the main molecules: glucose, pyruvate, ATP, NADH, FADH2, CO2, O2, and H2O.
Make it look like a clean classroom handout or slide, with a white background, simple icons, clear labels, and easy-to-read text.

Avoid tiny text, extra decoration, or anything that makes the diagram hard to understand.
```

![Cellular respiration](images/scientific-educational-cellular-respiration-gpt-image-2-6-sunburst.webp)

来源：[Image prompting · Create scientific and educational visuals](https://developers.openai.com/api/docs/guides/image-prompting)

---

### 10. 路演幻灯片：Market Opportunity（Sunburst）

**提示词**（文中 TAM/SAM/SOM 等数字为官方示例中的**虚构**数据，使用前请替换为真实数据）：

```text
Create one pitch-deck slide titled **"Market Opportunity"** that feels like a real Series A fundraising slide from a YC-backed startup.

Use a clean white background, modern sans-serif typography like Inter, and a crisp, minimal layout. The slide should include:

* A TAM/SAM/SOM concentric-circle diagram in muted blues and grays
* Specific, believable market sizing numbers:

  * **TAM:** $42B
  * **SAM:** $8.7B
  * **SOM:** $340M
* A clean bar chart below showing market growth from **2021 to 2026**, with a subtle upward trend
* Small footnotes: **"AGI Research, 2024"** and **"Internal analysis"**
* A company logo placeholder in the bottom-right corner

The design should look like it belongs in a deck that actually raised money: highly readable text, clear data hierarchy, polished spacing, and professional startup-style visual language.

Avoid clip art, stock photography, gradients, shadows, decorative elements, or anything that feels generic or overdesigned.
```

![Market slide](images/market-opportunity-slide-gpt-image-2-6-sunburst.webp)

来源：[Image prompting · Build slides, diagrams, and charts](https://developers.openai.com/api/docs/guides/image-prompting)

---

### 11. Markup 局部编辑：厨房花瓶改色（Flare）

左：标注预览输入；右：Flare 编辑结果。

**提示词**：

```text
Recolor the striped vase inside the red circle to solid cobalt blue ceramic. Keep its shape, the dried stems, and the rest of the kitchen unchanged. Remove the red circle from the finished photo.
```

| 输入（含标注） | Flare 输出 |
|----------------|------------|
| ![](images/markup-vase-input-gpt-image-2-6.webp) | ![](images/markup-vase-gpt-image-2-6-flare.webp) |

来源：[Image prompting · Guide an edit with markup](https://developers.openai.com/api/docs/guides/image-prompting) · [Image generation · markup](https://developers.openai.com/api/docs/guides/image-generation)

---

### 12. 透明抠图产品（Sunburst）

**提示词**：

```text
Extract the product from the input image and isolate it on a fully transparent background.
Output: centered product, crisp silhouette, no halos/fringing.
Preserve product geometry and label legibility exactly.
Add only light polishing. Do not add a solid backdrop, checkerboard, scenery, or shadow.
Do not restyle the product; remove the background and preserve clean alpha transparency.
```

![Product cutout](images/extract-product-gpt-image-2-6-sunburst.webp)

来源：[Image prompting · Create a transparent product cutout](https://developers.openai.com/api/docs/guides/image-prompting)

---

### 13. 节日贺卡：旧泰迪熊（Sunburst）

**提示词**：

```text
Create a Christmas holiday card illustration.

Scene:
a cozy Christmas scene with an old teddy bear sitting inside a keepsake box, slightly worn fur, soft stitching repairs, placed near a window with falling snow outside. The scene suggests the child has grown up, but the memories remain.

Mood:
Warm, nostalgic, gentle, emotional.

Style:
Premium holiday card photography, soft cinematic lighting,
realistic textures, shallow depth of field,
tasteful bokeh lights, high print-quality composition.

Constraints:
- Original artwork only
- No trademarks
- No watermarks
- No logos

Include ONLY this card text (verbatim):
"Merry Christmas — some memories never fade."
```

![Holiday card](images/christmas-holiday-card-teddy-gpt-image-2-6-sunburst.webp)

来源：[Image prompting · Design a holiday card](https://developers.openai.com/api/docs/guides/image-prompting)

---

### 14. 收藏玩具包装：复古螺旋桨飞机（Sunburst）

**提示词**：

```text
Create a collectible action figure of a vintage-style toy propeller airplane with rounded wings, a front-mounted spinning propeller, slightly worn paint edges, classic childhood proportions, designed as a nostalgic holiday collectible, in blister packaging.

Concept:
A nostalgic holiday collectible inspired by the simple toy airplanes
children used to play with during winter holidays.
Evokes warmth, imagination, and childhood wonder.

Style:
Premium toy photography, realistic plastic and painted metal textures,
studio lighting, shallow depth of field,
sharp label printing, high-end retail presentation.

Constraints:
- Original design only
- No trademarks
- No watermarks
- No logos

Include ONLY this packaging text (verbatim):
"Christmas Memories Edition"
```

![Toy airplane](images/christmas-collectible-toy-airplane-gpt-image-2-6-sunburst.webp)

来源：[Image prompting · Design collectible merchandise](https://developers.openai.com/api/docs/guides/image-prompting)

---

### 15. 公告案例：OpenArt 股价表情 GIF 精灵表

OpenAI 发布页展示由 **OpenArt** 制作、标注为 Images 2.6 能力的 chibi 办公室职员动画精灵表（下跌流泪 / 上涨撒钱）。下列英文提示词为公告原文。

**提示词 1（下跌）**：

```text
Create a single animation sprite sheet for a seamless looping GIF. A super-cute chibi adult office worker sits at a desk watching a computer monitor with a clearly plunging stock-market line chart. The sequence: hopeful expression, notices the chart falling, eyes widen, face crumples, large cartoon tears stream down both cheeks, then quietly wipes tears while still staring at the falling chart. Oversized round head, tiny body, big expressive eyes, soft rounded features, messy fluffy hair, white shirt and loosened tie. Exactly 6 sequential frames arranged in a precise 3-column by 2-row grid, read left-to-right then top-to-bottom. Every panel has identical dimensions. Fixed front three-quarter camera, same character, same desk, monitor, outfit, and cozy miniature office background. Clear incremental facial and tear motion; sixth frame transitions smoothly toward the first. Polished kawaii chibi 3D toy-like style, warm pastel lighting, funny and sympathetic mood. No captions, no words, no numbers, no logos, no speech bubbles, no panel labels, no borders, no gutters.
```

**提示词 2（上涨）**：

```text
Create a single animation sprite sheet for a seamless looping GIF. A super-cute chibi adult office worker sits at a desk watching a computer monitor with a clearly soaring stock-market line chart and a large upward arrow. The sequence: notices the chart rising, eyes widen with delight, jumps up cheering, grabs a handful of generic paper banknotes with no readable currency markings, then happily tosses the banknotes into the air like confetti while laughing. Oversized round head, tiny body, big sparkling expressive eyes, soft rounded features, messy fluffy hair, white shirt and loosened tie. Exactly 6 sequential frames arranged in a precise 3-column by 2-row grid, read left-to-right then top-to-bottom. Every panel has identical dimensions. Fixed front three-quarter camera, same character, desk, monitor, outfit, and cozy miniature office background. Clear incremental body, arm, and flying-paper motion; sixth frame transitions smoothly toward the first. Polished kawaii chibi 3D toy-like style, bright celebratory pastel lighting, funny joyful mood. No captions, no words, no numbers, no logos, no real currency symbols, no speech bubbles, no panel labels, no borders, no gutters.
```

**本仓库图片**：公告社交头图缓存 `images/images-2-6-social.png`；精灵表成图以公告页为准（页面资源多为动态/CDN，未二次镜像逐帧大图，避免失效）。

来源：[Introducing Images 2.6 · Animate your ideas with GIFs](https://openai.com/index/introducing-images-2-6/)（Created by OpenArt）

---

### 16. 历史语境：1969 年 Bethel 户外人群（生成）

**参数**：`size=1024x1536`，`quality=medium`。官方标注 GPT Image 2.6 Flare / Sunburst。

**提示词**：

```text
Create a realistic outdoor crowd scene in Bethel, New York on August 16, 1969.
Photorealistic, period-accurate clothing, staging, and environment.
```

| Flare | Sunburst |
|:-----:|:--------:|
| ![world flare](images/world-knowledge-gpt-image-2-6-flare.webp) | ![world sunburst](images/world-knowledge-gpt-image-2-6-sunburst.webp) |

来源：[Image prompting · Use historical and real-world context](https://developers.openai.com/api/docs/guides/image-prompting)

---

### 17. 布局保持翻译：咖啡机信息图 → 西班牙语（编辑）

以案例 4 的咖啡机信息图为输入；官方 2.6 Flare / Sunburst 输出。

**参数**：`size=1024x1536`，`quality=high`。

**提示词**：

```text
Translate the text in the infographic to Spanish. Do not change any other aspect of the image.
```

| Flare | Sunburst |
|:-----:|:--------:|
| ![sp flare](images/infographic-coffee-machine-sp-gpt-image-2-6-flare.webp) | ![sp sunburst](images/infographic-coffee-machine-sp-gpt-image-2-6-sunburst.webp) |

来源：[Image prompting · Translate while preserving layout](https://developers.openai.com/api/docs/guides/image-prompting)

---

### 18. 风格迁移：像素风摩托骑士（编辑）

输入为文档中的像素风参考图；官方标注 2.6 Flare / Sunburst。

**参数**：`size=1024x1536`，`quality=medium`。

**提示词**：

```text
Use the same style from the input image and generate a man riding a motorcycle on a white background.
```

| Flare | Sunburst |
|:-----:|:--------:|
| ![moto flare](images/motorcycle-gpt-image-2-6-flare.webp) | ![moto sunburst](images/motorcycle-gpt-image-2-6-sunburst.webp) |

来源：[Image prompting · Transfer a visual style](https://developers.openai.com/api/docs/guides/image-prompting)

---

### 19. 身份保持换装（编辑）

人物照片 + 三件服装参考；官方 2.6 Flare / Sunburst。

**参数**：`size=1024x1536`，`quality=medium`。

**提示词**：

```text
Edit the image to dress the woman using the provided clothing images. Do not change her face, facial features, skin tone, body shape, pose, or identity in any way. Preserve her exact likeness, expression, hairstyle, and proportions. Replace only the clothing, fitting the garments naturally to her existing pose and body geometry with realistic fabric behavior. Match lighting, shadows, and color temperature to the original photo so the outfit integrates photorealistically, without looking pasted on. Do not change the background, camera angle, framing, or image quality, and do not add accessories, text, logos, or watermarks.
```

| Flare | Sunburst |
|:-----:|:--------:|
| ![outfit flare](images/outfit-gpt-image-2-6-flare.webp) | ![outfit sunburst](images/outfit-gpt-image-2-6-sunburst.webp) |

来源：[Image prompting · Preserve identity and change clothing](https://developers.openai.com/api/docs/guides/image-prompting)

---

### 20. 多图合成：把狗放入场景（编辑）

场景图为 image 1、狗为 image 2；官方 2.6 Flare / Sunburst。

**参数**：`size=1024x1536`，`quality=medium`。

**提示词**：

```text
Place the dog from the second image into the setting of image 1, right next to the woman, use the same style of lighting, composition and background. Do not change anything else.
```

| Flare | Sunburst |
|:-----:|:--------:|
| ![dog flare](images/test-woman-with-dog-gpt-image-2-6-flare.webp) | ![dog sunburst](images/test-woman-with-dog-gpt-image-2-6-sunburst.webp) |

来源：[Image prompting · Combine references](https://developers.openai.com/api/docs/guides/image-prompting)

---

### 21. 草图转写实山谷（编辑）

**参数**：`size=1024x1536`，`quality=medium`。

**提示词**：

```text
Turn this drawing into a photorealistic image.
Preserve the exact layout, proportions, and perspective.
Choose realistic materials and lighting consistent with the sketch intent.
Do not add new elements or text.
```

| Flare | Sunburst |
|:-----:|:--------:|
| ![valley flare](images/realistic-valley-gpt-image-2-6-flare.webp) | ![valley sunburst](images/realistic-valley-gpt-image-2-6-sunburst.webp) |

来源：[Image prompting · Turn a drawing into a realistic image](https://developers.openai.com/api/docs/guides/image-prompting)

---

### 22. 局部移除：拿掉手中的花（编辑）

**参数**：`size=1024x1536`，`quality=medium`。

**提示词**：

```text
Remove the flower from man's hand. Do not change anything else.
```

| Flare | Sunburst |
|:-----:|:--------:|
| ![nofower flare](images/man-with-no-flower-gpt-image-2-6-flare.webp) | ![nofower sunburst](images/man-with-no-flower-gpt-image-2-6-sunburst.webp) |

来源：[Image prompting · Remove an object](https://developers.openai.com/api/docs/guides/image-prompting)

---

### 23. 人物入景：营地与熊（编辑）

以博物馆女性照片为输入；官方强调省略 `input_fidelity`。

**参数**：`size=1024x1536`，`quality=medium`。

**提示词**：

```text
Generate a highly realistic action scene where this person is running away from a large, realistic brown bear attacking a campsite. The image should look like a real photograph someone could have taken, not an overly enhanced or cinematic movie-poster image.
She is centered in the image but looking away from the camera, wearing outdoorsy camping attire, with dirt on her face and tears in her clothing. She is clearly afraid but focused on escaping, running away from the bear as it destroys the campsite behind her.
The campsite is in Yosemite National Park, with believable natural details. The time of day is dusk, with natural lighting and realistic colors. Everything should feel grounded, authentic, and unstyled, as if captured in a real moment. Avoid cinematic lighting, dramatic color grading, or stylized composition.
```

| Flare | Sunburst |
|:-----:|:--------:|
| ![scene flare](images/scene-gpt-image-2-6-flare.webp) | ![scene sunburst](images/scene-gpt-image-2-6-sunburst.webp) |

来源：[Image prompting · Insert a person into a scene](https://developers.openai.com/api/docs/guides/image-prompting)

---

### 24. 多轮精修起点：洗发水公路广告牌（编辑）

以透明抠图洗发水产品为输入；官方 2.6 Flare / Sunburst。

**参数**：`size=1024x1536`，`quality=medium`。

**提示词**：

```text
Create a realistic billboard mockup of the shampoo on a highway scene during sunset.
Billboard text (EXACT, verbatim, no extra characters):
"Fresh and clean"
Typography: bold sans-serif, high contrast, centered, clean kerning.
Ensure text appears once and is perfectly legible.
No watermarks, no logos.
```

| Flare | Sunburst |
|:-----:|:--------:|
| ![bb flare](images/billboard-gpt-image-2-6-flare.webp) | ![bb sunburst](images/billboard-gpt-image-2-6-sunburst.webp) |

来源：[Image prompting · Create the starting image](https://developers.openai.com/api/docs/guides/image-prompting)

---

### 25. 多轮精修：广告牌改为冬夜雪景（编辑）

以上一步各模型广告牌输出为输入。

**参数**：`size=1024x1536`，`quality=medium`。

**提示词**：

```text
Make it look like a winter evening with snowfall.
```

| Flare | Sunburst |
|:-----:|:--------:|
| ![winter flare](images/billboard-winter-gpt-image-2-6-flare.webp) | ![winter sunburst](images/billboard-winter-gpt-image-2-6-sunburst.webp) |

来源：[Image prompting · Change one condition](https://developers.openai.com/api/docs/guides/image-prompting)

---

### 26. 角色一致性：建立儿童书主角（生成）

**参数**：`size=1024x1536`，`quality=medium`。

**提示词**：

```text
Create a children’s book illustration introducing a main character.

Character:
A young, storybook-style hero inspired by a little forest outlaw,
wearing a simple green hooded tunic, soft brown boots, and a small belt pouch.
The character has a kind expression, gentle eyes, and a brave but warm demeanor.
Carries a small wooden bow used only for helping, never harming.

Theme:
The character protects and rescues small forest animals like squirrels, birds, and rabbits.

Style:
Children’s book illustration, hand-painted watercolor look,
soft outlines, warm earthy colors, whimsical and friendly.
Proportions suitable for picture books (slightly oversized head, expressive face).

Constraints:
- Original character (no copyrighted characters)
- No text
- No watermarks
- Plain forest background to clearly showcase the character
```

| Flare | Sunburst |
|:-----:|:--------:|
| ![cb1 flare](images/childrens-book-illustration-1-gpt-image-2-6-flare.webp) | ![cb1 sunburst](images/childrens-book-illustration-1-gpt-image-2-6-sunburst.webp) |

来源：[Image prompting · Establish the character](https://developers.openai.com/api/docs/guides/image-prompting)

---

### 27. 角色一致性：续写雪后救松鼠（编辑）

复用上一步角色图；官方 2.6 Flare / Sunburst。

**参数**：`size=1024x1536`，`quality=medium`。

**提示词**：

```text
Continue the children’s book story using the same character.

Scene:
The same young forest hero is gently helping a frightened squirrel
out of a fallen tree after a winter storm.
The character kneels beside the squirrel, offering reassurance.

Character Consistency:
- Same green hooded tunic
- Same facial features, proportions, and color palette
- Same gentle, heroic personality

Style:
Children’s book watercolor illustration,
soft lighting, snowy forest environment,
warm and comforting mood.

Constraints:
- Do not redesign the character
- No text
- No watermarks
```

| Flare | Sunburst |
|:-----:|:--------:|
| ![cb2 flare](images/childrens-book-illustration-2-gpt-image-2-6-flare.webp) | ![cb2 sunburst](images/childrens-book-illustration-2-gpt-image-2-6-sunburst.webp) |

来源：[Image prompting · Continue the story](https://developers.openai.com/api/docs/guides/image-prompting)

---

### 28. 室内家具替换：白椅 → 木椅（编辑）

**参数**：`size=1536x1024`，`quality=medium`。

**提示词**：

```text
In this room photo, replace ONLY the white chairs with chairs made of wood.
Preserve camera angle, room lighting, floor shadows, and surrounding objects.
Keep all other aspects of the image unchanged.
Photorealistic contact shadows and fabric texture.
```

| Flare | Sunburst |
|:-----:|:--------:|
| ![chairs flare](images/kitchen-chairs-gpt-image-2-6-flare.webp) | ![chairs sunburst](images/kitchen-chairs-gpt-image-2-6-sunburst.webp) |

来源：[Image prompting · Change furniture in a room](https://developers.openai.com/api/docs/guides/image-prompting)

---

### 29. 官方账号宣传片（有图无提示词）

OpenAI / OpenAIDevs 发布 Images 2.6 时附带宣传视频；下列为视频预览静帧镜像，**非** API 示例输出。

**提示词未公开**（宣传成片，无逐步复现提示）。

| @OpenAI | @OpenAIDevs |
|:-------:|:-----------:|
| ![oa](images/openai-official-images-2-6-tweet-preview.jpg) | ![devs](images/openaidevs-2-6-tweet-preview.jpg) |

来源：[OpenAI 帖](https://x.com/OpenAI/status/2118124944928432327) · [OpenAIDevs 帖](https://x.com/OpenAIDevs/status/2118124753340756055)（2026-11-04）

---


### 30. 社区 · Flare 后空翻 GIF × 三档 complexity（@gabrielchua）

作者用 **GPT Image 2.6 Flare** 生成站立后空翻循环，并并排对比 low / medium / high complexity；OpenAIDevs 曾转发。

**提示词（原帖可见简短版）**：

```text
create a GIF of a man doing a standing backflip
```

| 预览静帧 | 并排对比动图镜像 |
|:--------:|:----------------:|
| ![](images/community-gabrielchua-backflip-poster.jpg) | ![](images/community-gabrielchua-backflip.gif) |

来源：[X 原帖 @gabrielchua](https://x.com/gabrielchua/status/2118135343354720648)（2026-11-05）

---

### 31. 社区 · 超现实灰度人脸弹跳 GIF（@tianmakibo）

作者明确用 Image 2.6 测 GIF；原帖未公开完整提示词。

**提示词未公开**（见原帖）。

| 静帧拼图 | GIF 镜像 |
|:--------:|:--------:|
| ![](images/community-tianmakibo-faces-still.jpg) | ![](images/community-tianmakibo-faces.gif) |

来源：[X 原帖 @tianmakibo](https://x.com/tianmakibo/status/2118263296520519786)（2026-11-05）

---

### 32. 社区 · 夏日元气少女花园喷水 GIF（@nima2qisi2）

作者在评论区给出完整指令；**需自备服装参考图**（提示词写「穿着参考图中的…」）。

**提示词（作者评论区原文）**：

```text
请以这个内容创建一个 GIF，内容是： # JAPANESE SUMMER｜夏日元气少女
**10秒日系青春活力MV视频提示词**

**画面规格：** 9:16 竖屏，10秒，30fps，写实摄影质感。

**人物设定：**
22岁成年东亚女性，清透冷白皮肤，短款深棕色波波头，轻薄空气刘海，五官甜美可爱，身材匀称。穿着参考图中的蓝色泳装、黄色镂空针织罩衫及牛仔短裤。人物保持统一，性格活泼俏皮，笑容自然，有青春少女的灵动感。

**整体风格：**
Japanese Youth MV / Japanese Summer Pop / Youthful Cinematic Montage。

明亮通透的夏日阳光，翠绿花园，蓝色天空，橙色充气泳圈，水花飞溅。采用日本青春电影式混剪，快速切换全景、中景、近景和人物特写。画面具有轻微胶片颗粒、柔和高光、自然镜头运动。

### 01｜10秒分镜设计

**0—1.2秒｜开场钩子**
低机位快速推进，女主坐在橙色充气泳圈上，双手拿着水枪，突然发现镜头，露出惊喜的笑容，水流突然朝镜头喷来。

**1.2—2.4秒｜俏皮特写**
快速硬切面部近景，女主被水花溅到，轻轻眯眼，随后露出调皮笑容，发丝随着动作轻轻晃动。阳光在脸颊形成漂亮的光斑。

**2.4—3.6秒｜青春动态**
侧面低机位，女主赤脚轻轻踢起草坪上的水花，镜头快速横向跟拍，橙色泳圈在前景掠过，形成自然遮挡转场。

**3.6—4.8秒｜视觉节奏**
俯拍镜头，女主拿着水枪在草坪上转身，水流划出弧线。镜头快速向下俯冲，再跟随水花向上抬起。

**4.8—6.0秒｜少女感特写**
正面近景，女主双手举起水枪，突然向镜头喷水，水珠在阳光下形成晶莹的高光，女主笑着躲避镜头。

**6.0—7.3秒｜活力爆发**
快速硬切至侧后方，女主从泳圈上轻巧起身，转身甩动头发，黄色针织罩衫随动作自然摆动，镜头进行半圈快速环绕。

**7.3—8.7秒｜夏日氛围**
广角镜头，女主在草坪上轻快地转圈，双手张开，阳光穿过树叶洒落，水花从前景飞过，画面充满夏日青春气息。

**8.7—10秒｜记忆点结尾**
突然切换正面近景，女主向镜头靠近，俏皮地歪头眨眼，随后拿起水枪再次喷水，水花覆盖整个镜头，最后定格在明亮的白色水花中。

### 02｜音乐与剪辑

原创日系青春J-POP配乐：
- 轻快的电吉他、明亮合成器、活泼贝斯与清脆鼓点。
- 约155—165 BPM，具有夏日偶像MV的轻盈节奏。
- 开场直接进入音乐，不要冗长铺垫。
- 每次鼓点配合一次硬切或动作变化。
- 中间加入少量0.3—0.5秒高速慢动作水花特写，再迅速恢复正常速度。
- 结尾音乐与水花覆盖镜头同步收束。

### 03｜摄影与画面约束

1. 混剪必须具有明显的景别变化，避免全程一个机位。
2. 人物始终是画面视觉中心，保持同一张脸、发型和服装。
3. 人物动作自然轻快，表情丰富，笑容甜美但不过度表演。
4. 保留真实皮肤纹理、自然阳光与水珠物理效果。
5. 镜头切换干净利落，利用水花、泳圈和人物动作完成匹配剪辑。
6. 不要广告片质感，不要商业产品展示，不要字幕、文字、Logo、水印。
7. 避免人物变脸、服装改变、手指畸形、肢体错位及水枪结构变形。

**最终效果：像一支10秒的日本夏日青春偶像MV，轻松、明亮、活泼、俏皮，让人感受到夏天的快乐。**

```

| 静帧 | GIF 镜像 |
|:----:|:--------:|
| ![](images/community-nima-summer-still.jpg) | ![](images/community-nima-summer.gif) |

来源：[X 原帖](https://x.com/nima2qisi2/status/2118150226330734787) · [提示词评论](https://x.com/nima2qisi2/status/2118150229811920912)（2026-11-05）

---

### 33. 社区 · 白宫外观 360° 环绕 GIF（@nima2qisi2）

**提示词（原帖原文）**：

```text
【白宫外观】360°环绕
帮我做一个真实白宫外观的360°环绕模仿无人机拍摄，要真实还原建筑环境的实际外观情况，做成gif
```

![](images/community-nima-whitehouse-1.gif)

来源：[X 原帖 @nima2qisi2](https://x.com/nima2qisi2/status/2118295936606798006)（2026-11-05）

---

### 34. 社区 · Q 版反应表情包模板（@0xluffy_eth）

可复用表情包提示词；**需自备角色参考图**。

**提示词（作者原文）**：

```text
请以我上传的角色图为参考，按「表情包主题」做一张可下载的动态 GIF。

【角色保持一致】锁发型/配色/标志道具，Q 版比例全帧不变
【自动设计动作】一个核心笑点，夸张一点，首尾能循环
【自动配字】2～8 个字网感短句，顶部粗圆体+白描边
【画面】正方形纯白底，2～3 秒循环，聊天表情包规格

表情包主题：【填你的梗，比如「周一不想上班」】
```

![](images/community-luffy-sticker-1.gif)

来源：[X 原帖 @0xluffy_eth](https://x.com/0xluffy_eth/status/2118235544459387112)（2026-11-05）

---

### 35. 社区 ·「真香！」吃白饭表情 GIF（@Gorden_Sun）

**需角色参考图**（作者附参考静帧）。

**提示词（原帖原文）**：

```text
使用这个角色，生成一张GIF图，内容是她吃大白米饭，图片里有文字：真香！
```

| 参考图 | GIF 镜像 |
|:------:|:--------:|
| ![](images/community-gorden-rice-ref.jpg) | ![](images/community-gorden-rice.gif) |

来源：[X 原帖 @Gorden_Sun](https://x.com/Gorden_Sun/status/2118166046175908016)（2026-11-05）

---

### 36. 社区 · 像素风角色待机/攻击循环（@LerSentAI）

作者自称 Image 2.6 直出多角度待机与攻击动作；原帖未公开完整提示词。

**提示词未公开**（见原帖）。

![](images/community-lersent-1.gif)

来源：[X 原帖 @LerSentAI](https://x.com/LerSentAI/status/2118173626424025414)（2026-11-05）

---

### 37. 社区 · 绿色大帽子角色读报循环（@testingcatalog）

标注 Made with ChatGPT Images 2.6 for testing；原帖未公开提示词。

**提示词未公开**（见原帖）。

![](images/community-testingcatalog.gif)

来源：[X 原帖 @testingcatalog](https://x.com/testingcatalog/status/2118138826212385225)（2026-11-05）

---

### 38. 社区 · 角色心跳舞 low/medium/high（@Mayz1169）

作者称用 Codex + Image 2.6 让角色做 heartbeat dance，并排三档。

**提示词见原帖（简短说明，非逐步参数）**：原帖写 “make my character do a heartbeat dance”，完整工程提示未公开。

| 预览 | 片段镜像 |
|:----:|:--------:|
| ![](images/community-mayz-heartbeat-poster.jpg) | ![](images/community-mayz-heartbeat.gif) |

来源：[X 原帖 @Mayz1169](https://x.com/Mayz1169/status/2118241781074862569)（2026-11-05）

---

## 选型建议（官方引导摘要）

| 你的现状 | 建议先测 |
|----------|----------|
| 现有 GPT Image 2 / 2.5 Flare 已够用，更在意速度 | `gpt-image-2.6-flare` |
| 复杂任务、细节或编辑精度不足，或在用 2.5 Sunburst | `gpt-image-2.6-sunburst` |

同一提示、参考图、尺寸与 `quality` 下对比两档；编辑任务可从 `complexity=low` 起步，版式/多元素场景再升 medium/high。

---

## 参考资料

1. [Introducing Images 2.6 \| OpenAI](https://openai.com/index/introducing-images-2-6/)（2026-11-04）
2. [Image generation \| OpenAI API](https://developers.openai.com/api/docs/guides/image-generation)
3. [Image prompting \| OpenAI API](https://developers.openai.com/api/docs/guides/image-prompting)
4. [GPT-Image-2.6 Flare 模型卡](https://developers.openai.com/api/docs/models/gpt-image-2.6-flare)
5. [GPT-Image-2.6 Sunburst 模型卡](https://developers.openai.com/api/docs/models/gpt-image-2.6-sunburst)
6. [OpenAI API Pricing](https://openai.com/api/pricing/)
7. [界面新闻快讯摘要](https://www.jiemian.com/article/15295411.html)（中文报道，非官方）
8. [OpenAI · Images 2.6 公告帖](https://x.com/OpenAI/status/2118124944928432327)
9. [OpenAIDevs · GPT-Image-2.6 API 公告帖](https://x.com/OpenAIDevs/status/2118124753340756055)
10. 社区索引参考（非本仓库从属）：[JasonZhu Images 2.6 Prompt Library](https://jasonzhu.ai/en/prompts/chatgpt-images-2-6)

---

## 免责声明

- 本仓库为**非官方**学习索引，与 OpenAI 无隶属关系。
- 示例图与提示词版权归 OpenAI / 原作者；镜像仅供教育索引，请勿商用盗用。
- 模型能力、价格、限额以官方最新文档为准，本 README 可能滞后。
- 生成内容须遵守 OpenAI 使用政策与当地法律。

## 致谢 / Thanks

- 感谢 [LINUX DO](https://linux.do/) 社区佬友对开源分享的支持与讨论氛围。

## License

教程与仓库编排代码采用 [MIT](LICENSE)。第三方图片与提示词保留原权利。
