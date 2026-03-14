---
name: gantt-chart
description: ロードマップからガントチャートHTMLを自動生成するスキル
---

# ガントチャート生成スキル

## 概要
クライアントのロードマップ（MD）をもとに、半年分のガントチャートHTMLを生成する。
GitHub Pagesで公開し、クライアントにURLを共有できる形にする。

## デザインルール

### 全体
- **白背景 + ゴールド（#c9a96e）アクセント**の高級感あるデザイン
- フォント: `Noto Sans JP` + `Cormorant Garamond`（ヘッダー・ラベル）
- Google Fonts CDN読み込み
- レスポンシブ対応（min-width: 1100px でスクロール）

### ヘッダー（sticky固定）
- 背景: `linear-gradient(135deg, #fff 0%, #faf8f3 100%)`
- 下線: `2px solid #e8d5a3`
- 構成:
  - `ROADMAP — GANTT CHART`（Cormorant Garamond, letter-spacing: 3px, uppercase）
  - タイトル（1.6rem, 700）
  - サブタイトル（0.85rem, #888）
  - 凡例（色付き□ + カテゴリ名）
  - 右上に `Updated: YYYY.MM.DD`

### カラーパレット（カテゴリ別）
```
契約・法務:    linear-gradient(135deg, #8B6DB5, #a88bc7)
商品設計:      linear-gradient(135deg, #b8942e, #c9a96e)
動画制作:      linear-gradient(135deg, #4a7fb5, #6a9fd5)
ファネル:      linear-gradient(135deg, #6B3FA0, #8B6DB5)
LP制作:        linear-gradient(135deg, #5a9e6f, #7ab88a)
イベント:      linear-gradient(135deg, #d4782c, #e89a5c)
販売:          linear-gradient(135deg, #c26a5a, #d88a7c)
分析:          linear-gradient(135deg, #607d8b, #90a4ae)
協会/組織:     linear-gradient(135deg, #1a1a2e, #2d2d4e)
コンテンツ:    linear-gradient(135deg, #26a69a, #4db6ac)
```
※ カテゴリはクライアントの事業に合わせて変更可

### グリッド構成
- 左列: タスク名（240px固定）
- 右: 28列（7ヶ月 × 4週）
- 月ヘッダー: `grid-template-columns: 240px repeat(7, 1fr)`
- 週ヘッダー + タスク行: `grid-template-columns: 240px repeat(28, 1fr)`
- 月の区切り線: `border-left: 1px solid #e8e4dc`

### バー
- 高さ: 16px, border-radius: 8px, opacity: 0.85
- ホバー: opacity: 1, transform: scaleY(1.2)
- 2週以上のバーにはラベル表示（0.55rem, 白文字）
- マイルストーン（★）: border-radius: 4px, box-shadow付き, 太字表示
- 複数セルにまたがるバーの幅はJavaScriptで`cell.offsetWidth * span`で計算
- `window.resize`時にも再計算

### セクション行
- 背景: `#faf8f3`
- 左ボーダー: `3px solid #c9a96e`
- 月名 + テーマを表示

### ツールチップ
- `mouseenter` / `mousemove` / `mouseleave` で制御
- 背景: `rgba(26,26,46,0.95)`, 白文字, border-radius: 6px
- `position: fixed`, マウスに追従

### 下部サマリー
- 3カラムのカード（`grid-template-columns: repeat(auto-fit, minmax(300px, 1fr))`)
- カード: 白背景, 上ボーダー `3px solid #c9a96e`
- リスト項目に色付き●（カテゴリ色）

### フッター
- `produced by いちじく` のみ（日本語テキストでURLや日付を書かない）
- フッターの枠は最小限

## データ構造

```javascript
const tasks = [
  {
    section: '3月',        // セクション名（月）
    name: 'タスク名',      // 表示名
    type: 'contract',      // カテゴリ（CSSクラス名）
    start: 0,              // 開始位置（0起算、0=3月W1, 4=4月W1, ...）
    end: 1,                // 終了位置
    tip: '担当 詳細説明',  // ツールチップ
    milestone: true        // マイルストーンフラグ（任意）
  },
  ...
];
```

## 生成手順

1. クライアントのロードマップMDを読み込む
2. タスクデータを `tasks` 配列に変換
3. カテゴリを事業に合わせて決定（カラーパレットを調整）
4. HTMLテンプレートにデータを埋め込む
5. `docs/` フォルダに `roadmap.html` として保存
6. `git add → commit → push`
7. GitHub Pages URLを表示

## 完了時のルール
1. チャットに **完成時刻** を表示（ファイル内ではなくチャット上）
2. **公開URL** をコードブロックで表示（クライアントにコピー共有用）
3. 完了音を鳴らす: `afplay -r 0.5 /System/Library/Sounds/Submarine.aiff`
