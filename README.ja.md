# Roomake — 開発ノート

[English](README.md) · [简体中文](README.zh-CN.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Deutsch](README.de.md) · [Italiano](README.it.md) · [Español](README.es.md)

**Roomake**（[www.roomake.top](https://www.roomake.top)）は、AIインテリアデザインのWebアプリです。部屋の写真を1枚アップロードし、部屋タイプと最大4つのスタイルを選ぶ（または希望を自由に記述する）と、ビフォーアフターのレンダリング画像が返ってきます。運営は RooMake Teams です。

このリポジトリに製品のソースコードは**含まれていません**。記録しているのは、このサイトがどう作られているかということ——アーキテクチャ、多言語構成、SEO の意思決定、デプロイメントの規律——であり、それを7つの言語で書いています。書き残した理由は、半分は自分たちのため、もう半分は、「本物の i18n と本物の SEO を備えたクレジット制の AI 画像生成製品を、小さなチームがどう出荷するのか」という同じ質問に、答え続けることになったからです。

似たものを作っているなら——AI 画像生成、クレジット、決済、多ロケール SEO——このノートはいくつかの回り道を省いてくれるはずです。ここで述べる実践は、なぜそうするのかを教えてくれた失敗も含めて、すべて本番環境で実際に運用しているものです。

## 収録内容

| ドキュメント | 内容 |
|---|---|
| [01 · 概要](docs/ja/01-overview.md) | Roomake とは何か、プロダクトの姿、2つのサイト（.top / .cn） |
| [02 · 技術スタック](docs/ja/02-tech-stack.md) | Next.js 16、レンダリングパイプライン、クレジットと決済、データ層 |
| [03 · i18n アーキテクチャ](docs/ja/03-i18n.md) | 1つのコードベースで6ロケール、hreflang ポリシー、CJK フォント、ローカライズされた法務ページ |
| [04 · SEO プレイブック](docs/ja/04-seo.md) | 技術 SEO、構造化データ、`llms.txt`、AI クローラーポリシー |
| [05 · デプロイメント](docs/ja/05-deployment.md) | Docker、ビルド時のスコープフラグ、キャッシュ、ロールバックの規律 |

すべてのドキュメントは **English · 简体中文 · 日本語 · 한국어 · Deutsch · Italiano · Español** で利用できます。各ファイル冒頭の言語行で切り替えてください。

## クイックファクト

| | |
|---|---|
| プロダクト | AI ルームリデザイン：6部屋タイプ × 34スタイルプリセット。最大4スタイルを組み合わせるか、自由記述でブリーフを入力 |
| フロントエンド | Next.js 16（App Router）、React 19、TypeScript、Tailwind CSS、サーバーコンポーネント優先 |
| レンダリング | Flux 系の画像モデルを、ホスト型プロバイダー API 経由で、薄い内部ゲートウェイの背後に配置して利用 |
| 決済 | プリペイド式クレジット、1レンダリングにつきクレジット1消費、レンダリング失敗時は自動返金。海外は Creem が merchant of record（マーチャント・オブ・レコード）として対応 |
| プロダクトのロケール | English + 简体中文 が稼働中 · 日本語 / Deutsch / Français / 한국어 は順次公開 |
| ドキュメントの言語 | EN · ZH · JA · KO · DE · IT · ES |
| デプロイメント | リバースプロキシと CDN の背後で Docker（standalone ビルド）。独立した2つのリージョンデプロイメント |

## リンク

- プロダクト: [www.roomake.top](https://www.roomake.top) · 中国本土: [www.roomake.cn](https://www.roomake.cn)
- Roomake は [Chuangwit](https://www.chuangwit.com) のプロダクトです
- このノートに関する質問や訂正: `support@roomake.top`

## ライセンス

このリポジトリのテキストは [Creative Commons Attribution 4.0](LICENSE) のもとでライセンスされています。翻訳・改変・再利用は自由です。帰属表記は歓迎しますが、ライセンス条件の範囲を超えて義務ではありません。
