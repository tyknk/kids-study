# 子供達学習用ツール — エージェント向けガイド

このプロジェクトの設計ルール・仕様の**正本は `CLAUDE.md`** です。
エージェント（Claude Code 以外のAIコーディングツール）もそちらを参照してください。

---

## このファイルの位置づけ

- `CLAUDE.md` — 共通ガイドラインの正本（UIデザイン・LocalStorage仕様・教材追加フローなど）
- `AGENTS.md` — 本ファイル（エージェント向けに CLAUDE.md を参照するよう案内するだけ）

設計に関する質問・変更はすべて `CLAUDE.md` を基準にしてください。

---

## クイックリファレンス

よく参照するポイントへのリンク（`CLAUDE.md` 内）：

- **UIデザイン原則**：ひらがな優先・44px タップ領域・Zen Maru Gothic フォント
- **カラー**：子どもアプリ = ラベンダー/ピンク/ミント、正解 = `#78D98A`、不正解 = `#FF8FAA`
- **ホームナビバー**：全アプリに `🏠 まなびのへや` ボタン、リンク先は `../index.html`
- **LocalStorage キー**：`manabi_room_name` / `manabi_room_days` / `manabi_room_module_{id}` / `manabi_room_parent_pin`
- **教材追加フロー**：単元マップ確認 → PDF変換 → HTML作成 → index.html に追加 → 単元マップ更新
