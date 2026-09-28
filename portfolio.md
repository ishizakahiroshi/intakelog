---
cover:
  path: portfolio/overview-2026-09-28.jpg
  alt: {ja: "intakelog の紹介動画", en: "intakelog overview video"}
video:
  provider: youtube
  id: "0k_sGDsLzJo"
  durationSeconds: 20
schemaVersion: 1
color: "#4a8672"
initials: "il"
cat: {"ja":"取り込み台帳 / 構想","en":"Intake ledger / Planned"}
tagline: {"ja":"何を、いつ、どこから取り込んだか。","en":"What arrived, when, and from where."}
short: {"ja":"開発マシンへのツールや依存の取り込みを、追記型の台帳に残す CLI の構想。現在は設計段階で、CLI は未実装です。","en":"A planned CLI for an append-only ledger of tools and dependencies brought onto a development machine. The CLI is not implemented yet."}
tech: ["JavaScript","Node.js","osv-scanner"]
store: null
live: null
guide: null
featured: false
---
## ja

パッケージ管理・リポジトリ取得など複数の経路を横断し、取り込み日時・入手元・理由を記録する CLI を計画しています。脆弱性の判定は osv-scanner に委ね、依存の自動削除や更新は行わない設計です。現在は構想・設計段階です。紹介動画は完成アプリの操作映像ではなく、構想の説明です。

## en

The design proposes a CLI that records when tools and dependencies were acquired, their source and the reason, across multiple installation methods. Vulnerability matching is delegated to osv-scanner, with no automatic dependency removal or upgrade. This is a design-stage project; the overview video illustrates the concept, not a finished application.
