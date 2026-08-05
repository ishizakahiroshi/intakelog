<!-- このファイルはプロジェクト固有ルールのみを書く。個人/グローバル AI ルール
（言語・確認スタイル・出力フォーマット等）は各 AI ツールのグローバル設定へ。
fresh public clone でも有効な内容に保つこと。 -->

# intakelog 開発ガイド

## プロジェクト概要

intakelog は「開発マシンに何をいつどこから取り込んだか」を記録する CLI。npm / pnpm / bun だけでなく、
scoop / winget / pip / cargo / go / `git clone` / `curl | sh` まで含めた**全経路**の取り込みイベントを、
append-only の台帳に「日時・名前・版・入手元 URL・完全性ハッシュ・導入理由・取り込んだ主体」で焼き付ける。

lockfile と SBOM は「**今**何が入っているか」という状態しか持たない。サプライチェーン事案が起きた日に
実際に必要になるのは「**いつ**入れたか」「**どこから**来たか」だが、それはどこにも残っていない。
発端は 2026-08-04 の keyv / cacheable 侵害で、攻撃開始時刻以降に何を install したかを
`node_modules` の mtime から推定するしかなかったこと。推定ではなく事実で答えられるようにするのが目的。

脆弱性の判定は osv-scanner に委譲し、intakelog は台帳と経路の横断収集とレポートに専念する。

## やらないこと（スコープ外）

AI から機能追加を打診しないための明示的な切り捨て。

- **脆弱性スキャナの自前実装**。DB 照合は osv-scanner に委譲する。偽陰性は利用者の判断を誤らせる最悪の害なので、その責任を負わない
- **自動修復**（勝手にパッケージを消す・上げる・lockfile を書き換える）。観測と記録に徹する
- **常時稼働のサーバー / SaaS / Web ダッシュボード**。出力はローカルの静的ファイル 1 枚
- **単体 exe のビルド**。Windows の SmartScreen を踏むだけで得がない
- **ランタイム依存の追加**。実行時依存はゼロを維持する（サプライチェーンを見る道具が依存を抱える矛盾を避ける）
- **GUI**・多言語 UI・自動アップデート

## 技術スタック

| 項目 | 採用 | 備考 |
|---|---|---|
| 言語 | TypeScript | Node 互換を保つ（bun 専用にしない） |
| 実行時依存 | **なし** | 標準ライブラリのみ。devDependencies は可 |
| 外部ツール | osv-scanner | 脆弱性照合を委譲。未検出時はインストール方法を案内する |
| 配布 | npm レジストリのみ | `npx intakelog` で試せる / 常用はグローバル導入 |
| 台帳 | CSV（append-only） | 人が grep でき、git 差分が読める形式を優先 |
| レポート | 単一 HTML | 依存なし・self-contained・ホスティングしない |

## ディレクトリ構成

<!-- TODO: 実装開始後に埋める。現時点は骨格のみ。 -->

| パス | 役割 |
|---|---|
| `scripts/secrets-scan.mjs` | secrets-scan の scanner（layer 2/3/4 共通） |
| `.githooks/` | pre-commit hook（layer 2） |
| `docs/local/` | 作業ノート（tracked） |

## 主要コマンド

<!-- TODO: 実装開始後に埋める。 -->

- secrets-scan（手動）: `node scripts/secrets-scan.mjs --staged --block`
- hook 有効化（clone 直後）: `bash scripts/install-hooks.sh` または `pwsh scripts/install-hooks.ps1`

## AI 作業共通ルール

ビルド・コミット禁止、secrets-scan 責務、plan/bugfix/pending md の作成ルール等の AI 作業共通ルールは、各利用者のグローバル AI 設定に従う（作者環境の例: `~/.claude/CLAUDE.md` および `~/.claude/guides/`）。

このリポジトリ固有のルール:

- **走査できなかったものを黙って捨てない。** 未走査・取得失敗は必ず件数を出して目立たせる。
  黙って捨てると出力が「異常なし」に化け、利用者に誤った安心を与える。
  プロトタイプ段階で実際に 3 回踏んだ（エコシステム別 DB の未取得・`go.sum` と `go.mod` の取り違え・
  CLI 引数の空白による空応答）。いずれも「0 件」と「未検査」が見分けられない形で出ていた
- **台帳は append-only。** 既存行を書き換えない・並べ替えない。訂正は新しい行を足して理由を書く
- **推測で日時を埋めない。** 取得できない値は空にする。推定値を事実として残すのが最も害が大きい
- **台帳の実データを公開物に含めない。** 台帳には private リポ名・絶対パス・社内ホスト名が入りうる。
  公開するのはフォーマットの仕様まで
- **fixture は合成データで書く。** 実環境の値を転記しない

## secrets-scan（このリポジトリの配線）

書く瞬間の責務（固有名詞の一般化・fixture は合成データ等）は上記「AI 作業共通ルール」の参照先に従う。このリポジトリ固有の配線は以下:

- scanner: `scripts/secrets-scan.mjs`（手動実行: `node scripts/secrets-scan.mjs --staged --block`）
- layer 2: `.githooks/pre-commit`（`core.hooksPath=.githooks` 方式。実行時依存ゼロを保つため husky は使わない）
- layer 3: `.github/workflows/secrets-scan.yml` / layer 4: release ゲート
- env (full coverage に必要・未設定なら構造 regex のみで継続): `KB_ROOT` / `FAMILY_ROOT`。設定詳細は `scripts/secrets-scan.mjs` の冒頭コメント
- 参照実装・設計詳細: `worklog-bridge` リポの `docs/local/secrets-scan-design/`（gitignored・公開しない）

## 関連ドキュメント

| 項目 | パス |
|---|---|
| ユーザー向け README | `README.md` |
| Codex/他 AI 用入口 | `AGENTS.md` |
| ローカル作業ノート | `docs/local/`（存在する場合） |
