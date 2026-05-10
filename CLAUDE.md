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
- **ひらがな優先**。漢字を使う場合は**小学2年生までの配当漢字に限定**（対象ユーザーは3年生だが、3年生漢字は学年途中で未習得のものがあるため）
  - OK例：山、川、学、力、数、算、万（すべて2年生以下）
  - NG例：筆（3年）、位（3年）、億（4年）、比（5年）、超（中学）→ ひらがな表記にする
  - **例外（単元理解に必要な用語）**：学習対象の単元名・概念・単位として必要な漢字は2年生超でも使用可。ただし **必ず `<ruby>` タグでフリガナを付ける**こと
    ```html
    <!-- 例：4年生漢字「億」を学習対象として示す場合 -->
    一<ruby>億<rt>おく</rt></ruby>
    <!-- 例：3年生漢字「位」を算数用語として示す場合 -->
    <ruby>位<rt>くらい</rt></ruby>
    ```
  - 同じ語が同一画面内で複数回出る場合は、初出のみフリガナを付け2回目以降は省略してよい
- ボタンラベルは短く、動詞で終わる（「はじめる」「もどる」「えらぶ」）
- エラーや間違いの説明は責めず、応援するトーン（「ざんねん！」より「もういちど！」）

### 間違い時のフィードバック（必須ルール）
- **間違い時は「なぜ違うか」の解説パネルを、「つぎのもんだい▶」ボタンと同一画面に表示する**
  - 解説パネル（`.exp-panel` クラス推奨）：正解の理由・考え方のポイントを子ども向けに説明
  - 「❌ もういちど！」+ 「こたえは〇〇だよ」+ 解説パネル + 「つぎのもんだい▶」ボタンを1画面に収める
  - 正解時は解説パネルを表示しない（シンプルに祝福するだけ）
  - 解説は問題タイプに応じて `getExplanation(q)` 関数で動的生成する
  - `.exp-panel` の推奨スタイル：
    ```css
    .exp-panel {
      background: #FFFBEB;
      border-left: 4px solid #FCD34D;
      border-radius: 0 12px 12px 0;
      padding: 10px 14px;
      font-size: clamp(0.82rem, 1.5vw + 0.7vh, 1rem);
      color: #78350F;
      line-height: 1.75;
    }
    .exp-panel strong { color: #92400E; }
    ```

### タッチ・操作
- タップ領域は最低 44px（iOSのHIG推奨値）
- `touch-action: manipulation` で300ms遅延を排除
- `-webkit-tap-highlight-color: transparent` でタップハイライト除去
- ボタンには `:active` 時の押し込みアニメーション（`translateY` + `box-shadow` 縮小）

### PC（デスクトップ）最適化
- **iPadメインだが、将来のPC利用を見越して最初からPC最適化も組み込む**
- ホバー効果は `@media (hover: hover)` で囲む（タッチデバイスに誤適用しないため）
- ボタン・カード・選択肢など全インタラクティブ要素に `:hover` スタイルを付与
- `transition: all 0.15s` 等でアニメーションをスムーズに
- 例：
  ```css
  @media (hover: hover) {
    .btn-example:hover { background: var(--accent-dark); }
    .card-example:hover { transform: translateY(-2px); box-shadow: 0 6px 20px rgba(0,0,0,0.15); }
    .btn-choice:hover:not(:disabled) { border-color: var(--accent); background: var(--accent-lt); }
  }
  ```

### レイアウト・サイズ
- **ビューポートを最大限に使う**：`body { height: 100svh; overflow: hidden; display: flex; flex-direction: column; align-items: center; }` で画面全体を埋める
- `.screen.active { display: flex; flex-direction: column; height: 100%; overflow: hidden; }` でアクティブ画面がビューポートを占有
- 問題カード・選択肢など主要コンテンツは `flex: 1; min-height: 0;` で残り高さを埋める
- コンテンツが溢れる可能性のある領域（フィードバック表示後など）は `overflow-y: auto` でスクロール対応
- スタート・結果画面は `.start-body / .result-body { flex: 1; justify-content: center; }` で縦中央揃え
- フォントサイズは `clamp(min, Xvw + Yvh, max)` で**幅・高さ両方**に連動させる（`vw` のみ禁止）
  - 例：問題文 `clamp(1.5rem, 3vw + 2.5vh, 4rem)`、選択肢 `clamp(1.3rem, 2.5vw + 2.5vh, 3.5rem)`
- 最大幅は `min(900px, 92vw)` 程度を目安
- 選択肢グリッドは `grid-template-rows: 1fr 1fr` で縦にも均等に埋める

### フォント
- **Zen Maru Gothic**（本文・ボタン）— Google Fonts
- **Kaisei Decol**（タイトル・見出し）— Google Fonts
- 両フォントを `<link>` で読み込む

### 共通 `<head>` テンプレート（新規アプリ必須）

```html
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
<meta name="apple-mobile-web-app-capable" content="yes">
<meta name="apple-mobile-web-app-status-bar-style" content="default">
<title>アプリ名</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Zen+Maru+Gothic:wght@400;700;900&family=Kaisei+Decol:wght@400;700&display=swap" rel="stylesheet">
```

- `maximum-scale=1.0` と Apple 向け meta の2行は Safari の挙動差を防ぐために必須。省略しないこと。
- `viewport-fit=cover` は iPhone のノッチ・ホームバー周辺の `env(safe-area-inset-*)` を有効にするために必須。
- body の `min-height` は `100svh`（small viewport height）を使うこと。`100vh` は iOS Safari でアドレスバーを含む高さになり下部が隠れる。
- body の `padding` は `max(env(safe-area-inset-top), 上余白)` の形式で safe-area に対応させること。

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
