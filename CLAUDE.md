# 子供達学習用ツール — 共通ガイドライン

このフォルダ配下のすべての学習HTMLファイルに適用する設計ルールです。
新しいファイルを作成・編集するときは必ずこのガイドラインに従ってください。

---

## 対象ユーザー

- **小学3年生を中心とした小学生**（kanekoさんのお子さん）
- iPadでの使用を主想定（タッチ操作・大きめのタップ領域）

---

## UIデザイン原則

### 文字・言語
- **ひらがな優先**。漢字を使う場合は小学3年生までの配当漢字に限定
  - OK例：山、川、学、力、数、算
  - NG例：確認、選択、正解（→「かくにん」「えらぶ」「せいかい」と書く）
- ボタンラベルは短く、動詞で終わる（「はじめる」「もどる」「えらぶ」）
- エラーや間違いの説明は責めず、応援するトーン（「ざんねん！」より「もういちど！」）

### タッチ・操作
- タップ領域は最低 44px（iOSのHIG推奨値）
- `touch-action: manipulation` で300ms遅延を排除
- `-webkit-tap-highlight-color: transparent` でタップハイライト除去
- ボタンには `:active` 時の押し込みアニメーション（`translateY` + `box-shadow` 縮小）

### レイアウト・サイズ
- フォントサイズはすべて `clamp(min, vw, max)` でスケーラブルに
- 最大幅は `min(900px, 92vw)` 程度を目安
- 子ども向けアプリは大きめフォント（問題文は `clamp(2.5rem, 6vw, 7rem)` 程度）

### フォント
- **Zen Maru Gothic**（本文・ボタン）— Google Fonts
- **Kaisei Decol**（タイトル・見出し）— Google Fonts
- 両フォントを `<link>` で読み込む

### 共通 `<head>` テンプレート（新規アプリ必須）

```html
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<meta name="apple-mobile-web-app-capable" content="yes">
<meta name="apple-mobile-web-app-status-bar-style" content="default">
<title>アプリ名</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Zen+Maru+Gothic:wght@400;700;900&family=Kaisei+Decol:wght@400;700&display=swap" rel="stylesheet">
```

`maximum-scale=1.0` と Apple 向け meta の2行は Safari の挙動差を防ぐために必須。省略しないこと。

### カラー
- 子ども向けアプリ：ラベンダー・ピンク・ミント系（明るく楽しい）
- トップページ（まなびのへや）：落ち着いたパープル・グレー系
- 正解：緑系 `#78D98A`、不正解：ピンク系 `#FF8FAA`

---

## ファイル構成

```
子供達学習用/
├── CLAUDE.md              ← このファイル（共通ガイドラインの正本）
├── AGENTS.md              ← CLAUDE.md を参照（エージェント向け案内のみ記載）
├── index.html             ← 統合トップページ（親が設定する落ち着いたデザイン）
└── math/
    ├── multiplication-division.html
    ├── division-with-remainder.html
    ├── length-mm-km.html
    └── 教科書PDF/
        ├── 単元マップ.md
        └── 小3算数教科書(...).pdf
```

### トップページ（index.html）の役割
- 初回起動時に名前入力画面を表示 → LocalStorageに保存
- 名前はあとから変更・削除できる
- 教科タブ（さんすう・こくご・えいご）で切り替え
- 各学習ファイルへのリンクと進捗表示（開いた回数・連続学習日数）
- リンク先は同じタブで開く（`<a>` タグ、`target` 指定なし）

### 学習アプリ共通の仕様
- 単一HTMLファイル（外部JS・CSSなし）
- 画面切り替えは `.screen` / `.screen.active` クラスの付け替え
- **全アプリにホームナビバーを設置**（`🏠 まなびのへや` ボタン）
  - スタート画面・結果画面：確認なしで即移動
  - 学習中・問題の途中：「もどると今のもんだいはきえるよ」確認ポップアップ
  - リンク先パス：`../index.html`

---

## LocalStorage キー規則

| キー | 内容 |
|------|------|
| `manabi_room_name` | ユーザー名（JSON文字列） |
| `manabi_room_days` | 学習日の配列（`["2025-04-01", ...]`） |
| `manabi_room_module_{id}` | 各モジュールの進捗 `{ visitCount, lastVisited }` |
| `manabi_room_parent_pin` | 保護者設定用PIN（4桁数字の文字列）。タイトルを5回タップで入力画面が出る |

---

## 教材追加フロー

新しい学習アプリを追加するときの手順：

1. **単元マップ確認** — `math/教科書PDF/単元マップ.md` でPDFファイルとページ範囲を確認
2. **PDF → 画像変換** — `pdftoppm -png -r 150 -f <開始ページ> -l <終了ページ> <PDFパス> /tmp/unit/p`
3. **内容把握** — 画像を読んで問題パターン・出題形式を把握
4. **HTML作成** — このガイドラインに沿って単一HTMLファイルを `math/` 配下に作成
5. **index.html に導線追加** — `UNITS` 配列と `MODULES` 配列に新しいエントリを追加
6. **単元マップ更新** — 単元マップの「学習アプリ」欄を ✅ にしてファイル名を記載

---

## 複数の子どもへの対応
- 別のお子さん用には別のHTMLファイルを作成する（LocalStorageのキーが共有されるため）
- 例：`index_hanako.html`

---

## 今後追加予定の教科
- こくご（ことばあそび、よみとりなど）
- えいご（アルファベット、えいごあそびなど）

`index.html` の `SUBJECTS` 配列に追加していく設計になっている。
