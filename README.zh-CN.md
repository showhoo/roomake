# Roomake 建站笔记

[English](README.md) · [简体中文](README.zh-CN.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Deutsch](README.de.md) · [Italiano](README.it.md) · [Español](README.es.md)

**Roomake**（[www.roomake.top](https://www.roomake.top)）是一款 AI 室内设计 Web 应用：上传一张房间照片，选择房型和最多四种风格（也可以自己写一段需求描述），即可获得改造前后的对比渲染图。运营方为 RooMake Teams。

本仓库**不包含**产品源码，只记录这个网站是怎么建起来的——架构、多语言方案、SEO 决策、部署纪律——并用七种语言写成。写下来的原因，一半是留给自己，一半是因为总有人在私信里问同一类问题：一个小团队是怎么把「积分制 AI 生图产品 + 真正的多语言 + 真正的 SEO」同时做出来的。

如果你也在做类似的产品——AI 图像生成、积分体系、支付、多语言 SEO——这些笔记应该能帮你少走几段弯路。文中的每一条实践都在生产环境真实运行过，包括那些让我们吃过亏、从而明白「为什么要这样做」的错误。

## 目录

| 文档 | 内容 |
|---|---|
| [01 · 总览](docs/zh/01-overview.md) | Roomake 是什么、产品形态、两个站点（.top / .cn）的关系 |
| [02 · 技术栈](docs/zh/02-tech-stack.md) | Next.js 16、生图管线、积分与支付、数据层 |
| [03 · i18n 架构](docs/zh/03-i18n.md) | 一套代码跑六个语言、hreflang 政策、中日韩字体、本地化法务页 |
| [04 · SEO 打法](docs/zh/04-seo.md) | 技术 SEO、结构化数据、`llms.txt`、AI 爬虫政策 |
| [05 · 部署](docs/zh/05-deployment.md) | Docker、构建期 scope 开关、缓存、回滚纪律 |

每篇文档均有 **英文 · 简体中文 · 日本語 · 한국어 · Deutsch · Italiano · Español** 七个语言版本——通过每个文件顶部的语言行切换。

## 速览

| | |
|---|---|
| 产品 | AI 房间重设计：6 种房型 × 34 种风格预设，最多混选 4 种，或自由文字描述 |
| 前端 | Next.js 16（App Router）、React 19、TypeScript、Tailwind CSS，服务端组件优先 |
| 生图 | Flux 系图像模型，经托管服务商 API 调用，中间隔一层薄内部网关 |
| 支付 | 预充值积分，一次渲染扣一分，渲染失败自动退款；国际支付走 Creem（merchant of record） |
| 产品语言 | 英文 + 简体中文已上线 · 日本語 / Deutsch / Français / 한국어 陆续开放 |
| 笔记语言 | 中 · EN · JA · KO · DE · IT · ES |
| 部署 | Docker（standalone 构建），反向代理 + CDN，两个相互独立的区域部署 |

## 链接

- 产品：[www.roomake.top](https://www.roomake.top) · 中国站：[www.roomake.cn](https://www.roomake.cn)
- Roomake 是[创为](https://www.chuangwit.com)旗下产品
- 关于笔记的问题或勘误：`support@roomake.top`

## 许可

本仓库文字内容采用 [CC BY 4.0](LICENSE) 许可——欢迎翻译、改写、复用，按许可条款注明出处即可。
