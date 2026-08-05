# Agent Entry Point (intakelog)

このリポジトリの運用ガイダンスは `CLAUDE.md` を正本とする。

- プロジェクト概要・ルール: `./CLAUDE.md`
- ユーザー向けドキュメント: `./README.md`
- ローカル/プライベート追記（存在する場合・コミットしない）: `./CLAUDE.local.md` / `./AGENTS.local.md` / `./docs/local/`

個人/グローバル AI ルールは意図的にこのリポジトリの外に置く。各 AI ツールの
グローバル設定を使うこと。本ファイルは fresh public clone でも有効に保つ。

## Non-negotiables (full detail in CLAUDE.md)

- **走査できなかったものを黙って捨てない**。未走査・取得失敗は必ず件数を出す（黙って捨てると「異常なし」に化ける）
- **台帳は append-only**。既存行を書き換えない。訂正は行を足す
- **推測で日時を埋めない**。取得できない値は空にする
- **実行時依存を増やさない**。脆弱性スキャナも自前実装せず osv-scanner に委譲する

- ビルド・コミット禁止、secrets-scan 責務、plan/bugfix/pending md の作成ルール等の AI 作業共通ルールは、各利用者のグローバル AI 設定に従う（作者環境の例: `~/.claude/CLAUDE.md` および `~/.claude/guides/`）
- secrets-scan のこのリポジトリの配線（scanner パス・手動実行コマンド等）は `CLAUDE.md` の「secrets-scan（このリポジトリの配線）」節を参照

ガイダンス間で矛盾が出たら `CLAUDE.md` を優先する。

<!-- many-ai-cli の承認マーカーブロックはここに自動注入される。本ファイルでは持たない。 -->
