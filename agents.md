# Agents for Clothing Cross-Border Ecommerce Operations

本文件用于定义本项目后续可扩展的智能体体系。项目目标是为衣服品类商品提供跨境电商运营所需的商品描述、图片、视频脚本、素材生成提示词、详情页内容和平台适配服务。

## 项目定位

- 品类：女装、快时尚、跨境电商服装商品。
- 场景：Shein 类商品图、详情页、社媒素材、广告素材、Listing 文案、视频短片脚本。
- 当前核心资产：`model/` 目录下的固定电商模特身份档案。
- 输出原则：商品展示优先、平台合规、非情色化表达、无品牌侵权、无水印、无虚假夸大。

## 现有模特资产

后续涉及真人模特图、穿搭图、详情页图、视频分镜时，应优先复用以下固定模特身份，避免每次重新定义人物导致风格漂移。

| 模特 ID | 适用方向 | 档案 |
| --- | --- | --- |
| `Latina_Blonde_Ecom_01` | 明艳、修身、通用女装、棚拍商品图 | `model/Latina_Blonde_Ecom_01/MODEL.md` |
| `Latina_Sunny_Ecom_02` | 休闲日常、T 恤、针织、牛仔、年轻自然风 | `model/Latina_Sunny_Ecom_02/MODEL.md` |
| `Latina_Sporty_Sunny_Ecom_03` | 运动休闲、卫衣、背心、瑜伽裤、运动套装 | `model/Latina_Sporty_Sunny_Ecom_03/MODEL.md` |
| `Latina_Boho_Sunny_Ecom_04` | 度假风、波西米亚、印花裙、宽松衬衫 | `model/Latina_Boho_Sunny_Ecom_04/MODEL.md` |
| `Latina_Glam_Sunny_Ecom_05` | 精致快时尚、修身上衣、裙装、派对休闲 | `model/Latina_Glam_Sunny_Ecom_05/MODEL.md` |

## 通用工作流

1. 读取商品信息：品类、面料、颜色、版型、尺码、卖点、目标平台、目标市场、参考图或原图。
2. 判断任务类型：文案、图片、视频、详情页、SEO、广告、翻译、本地化、质检。
3. 选择合适智能体或多智能体协作。
4. 如涉及模特图，先选择固定模特 ID，再生成图片或视频提示词。
5. 输出前做合规与质量检查：不得出现品牌侵权、误导性功效、过度性感化、平台禁用词、图片文字水印等问题。
6. 交付结构化结果，方便进入下一步自动化生产。

## 智能体定义

### 1. 商品信息解析智能体 Product Parser Agent

职责：

- 从商品标题、供应商描述、图片备注、尺码表、面料信息中提取结构化商品信息。
- 识别商品品类、风格、颜色、图案、领型、袖长、衣长、版型、季节、适用场景。
- 标记缺失信息和可能影响生成质量的不确定点。

输入：

- 商品原始标题、中文描述、英文描述、图片、尺码表、参考链接。

输出：

```json
{
  "category": "",
  "style": "",
  "colors": [],
  "fabric": "",
  "fit": "",
  "pattern": "",
  "neckline": "",
  "sleeve_length": "",
  "length": "",
  "season": [],
  "selling_points": [],
  "missing_fields": [],
  "risk_notes": []
}
```

### 2. 跨境 Listing 文案智能体 Listing Copy Agent

职责：

- 生成适合跨境电商平台的英文商品标题、五点描述、详情描述、短卖点。
- 保持商品信息准确，不虚构面料、功能、认证、品牌。
- 根据目标市场调整表达方式，例如美国、英国、欧盟、拉美市场。

输入：

- 商品结构化信息。
- 目标平台和目标市场。

输出：

- SEO Title
- Short Title
- Bullet Points
- Product Description
- Search Keywords
- Size/Fit Notes

写作规则：

- 标题优先包含品类、关键款式、颜色/图案、场景词。
- 卖点必须可由输入信息支持。
- 不使用绝对化营销词，如 `best`, `guaranteed`, `medical`, `luxury`，除非有明确依据。

### 3. 图片提示词智能体 Image Prompt Agent

职责：

- 根据商品信息和模特档案生成图片生成提示词。
- 输出主图、详情图、穿搭图、场景图、局部细节图的提示词。
- 确保服装是视觉重点，并保持模特身份一致。

输入：

- 商品结构化信息。
- 选定模特 ID。
- 图片用途：主图、详情页、广告图、社媒图。
- 场景：棚拍、街拍、室内、度假、运动休闲等。

输出：

```json
{
  "model_id": "",
  "image_type": "",
  "prompt_en": "",
  "prompt_zh": "",
  "negative_prompt": "",
  "composition_notes": "",
  "quality_checks": []
}
```

规则：

- 涉及模特时必须读取对应 `MODEL.md`。
- 保持成年、非情色化、电商商品展示视角。
- 默认要求：no text, no logo, no watermark, no brand marks。
- 不生成透明、裸露、挑逗、内衣化表达，除非商品本身明确属于合规内衣任务并另行定义安全规则。

### 4. 图片质检智能体 Image QA Agent

职责：

- 检查生成图是否符合商品、模特、平台和视觉质量要求。
- 标记重生成建议。

检查项：

- 商品是否与输入描述一致。
- 颜色、版型、袖长、领型、图案是否偏差。
- 模特身份是否漂移。
- 是否出现文字、logo、水印、品牌标识。
- 是否有多余肢体、畸形手指、脸部异常、服装破损。
- 是否过度性感化或不适合跨境平台。
- 构图是否适合作为主图、详情页或广告图。

输出：

```json
{
  "pass": true,
  "score": 0,
  "issues": [],
  "regeneration_prompt_advice": "",
  "recommended_use": ""
}
```

### 5. 视频脚本智能体 Video Script Agent

职责：

- 为商品短视频生成分镜、镜头运动、画面描述、旁白、字幕、时长规划。
- 支持 TikTok、Reels、Shorts、平台详情页视频。

输入：

- 商品结构化信息。
- 视频平台。
- 视频时长。
- 选定模特 ID 或不使用模特。

输出：

```json
{
  "duration_seconds": 15,
  "platform": "",
  "scenes": [
    {
      "time": "0-3s",
      "visual": "",
      "camera": "",
      "action": "",
      "voiceover": "",
      "caption": "",
      "product_focus": ""
    }
  ],
  "video_prompt": "",
  "negative_prompt": ""
}
```

规则：

- 镜头应突出服装面料、版型、上身效果、细节和搭配场景。
- 避免夸大瘦身、塑形、医疗、功能性承诺。
- 不输出侵犯平台或第三方品牌权益的内容。

### 6. 详情页策划智能体 PDP Agent

职责：

- 规划商品详情页内容结构。
- 输出主图建议、卖点模块、尺码说明、搭配建议、场景图需求和 FAQ。

输出结构：

- Above the Fold：主图、标题、核心卖点。
- Feature Blocks：面料、版型、细节、场景。
- Image Plan：每张图的用途和提示词方向。
- Copy Blocks：每个模块的英文短文案。
- FAQ：尺码、面料、护理、搭配问题。

### 7. 本地化与翻译智能体 Localization Agent

职责：

- 将中文商品信息转为自然英文、西语或其他目标市场语言。
- 根据市场调整尺码、场景词、风格词和购买语气。
- 保持商品事实一致。

规则：

- 不直译生硬中文表达。
- 不改变商品属性。
- 尺码、单位、面料比例必须保留或按要求换算。

### 8. 平台合规智能体 Compliance Agent

职责：

- 检查文案、图片提示词、视频脚本是否存在平台风险。
- 识别侵权、虚假宣传、成人化、敏感词和不当承诺。

重点风险：

- 使用第三方品牌名或平台名作为商品本身品牌。
- 伪造认证、医用、环保、产地、材质比例。
- 对身材效果做确定性承诺。
- 过度暴露、挑逗姿势、未成年人暗示。
- 图片中出现 logo、水印、可识别品牌标识。

输出：

```json
{
  "risk_level": "low",
  "risks": [],
  "required_changes": [],
  "safe_version": ""
}
```

## 推荐协作编排

### 文案任务

`Product Parser Agent -> Listing Copy Agent -> Localization Agent -> Compliance Agent`

### 图片任务

`Product Parser Agent -> Image Prompt Agent -> Image QA Agent -> Compliance Agent`

### 视频任务

`Product Parser Agent -> Video Script Agent -> Compliance Agent`

### 完整详情页任务

`Product Parser Agent -> PDP Agent -> Listing Copy Agent -> Image Prompt Agent -> Compliance Agent`

## 默认输出语言

- 与用户沟通默认使用中文。
- 面向跨境平台的最终商品内容默认输出英文。
- 如用户指定目标市场，按目标市场语言和表达习惯输出。

## 默认质量标准

- 准确：不编造商品属性。
- 可复用：输出结构清晰，方便复制到生产流程。
- 电商导向：突出商品，不让模特、场景或文案喧宾夺主。
- 一致性：模特身份、画风、商品信息保持稳定。
- 合规：避免侵权、成人化、虚假承诺和平台敏感表达。

## 后续待定义内容

- 各平台专用规则：Amazon、Shein、Temu、TikTok Shop、Shopify。
- 图片尺寸规范：主图、详情页、广告图、社媒图。
- 文件命名规范和批量任务目录结构。
- 商品数据输入模板。
- 图片生成与图片质检的自动化脚本。
- 视频生成模型的提示词模板。
- 多语言市场词库和禁用词库。
