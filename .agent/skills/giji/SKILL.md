---
name: giji
description: 文字起こしテキストから議事録（MD）とスライド（HTML）を自動生成するスキル
---

# 議事録生成スキル（giji）

## 概要
Zoomなどの打ち合わせの文字起こしテキストファイルを指定して発動する。
テキストを分析し、**議事録（Markdown）** と **プレゼンスライド（HTML）** の2つを自動生成する。

## 使い方
ユーザーが文字起こしファイルのパスを指定して「議事録にして」等と依頼した時に発動。

## 実行手順

### Step 1: テキスト読み込み
- 指定されたテキストファイルを全文読み込む（800行ずつ分割して読む）
- 参加者、議題、討議内容、合意事項、ネクストアクションを把握する

### Step 2: 議事録MD作成
テキストファイルと同じディレクトリに `議事録_[会議名].md` を作成。

#### 構成テンプレート:
```markdown
# 議事録：[参加者A] × [参加者B] [会議形式]

**日時：** [日時]
**参加者：**
- **[参加者Aさん]** — [役割]
- **[参加者B（ちいくじ）]** — [役割]

---

## 1. 打ち合わせの目的
[目的の要約]

## 2. [参加者A]さんの現状
[現状の整理]

## 3. 課題・ニーズ
[課題の整理]

## 4. ちいくじの提案
[提案内容]

## 5. 合意事項
[料金・契約・スケジュール等]

## 6. ちいくじのネクストアクション
[リスト]

## 7. [参加者A]さんのネクストアクション
[リスト]
```

### Step 3: スライドHTML作成
同じディレクトリに `議事録スライド.html` を作成。

#### デザインルール:
- **白背景 + ゴールド（#c9a96e）アクセント** の高級感あるデザイン
- フォント: `Noto Sans JP` + `Cormorant Garamond`（番号・英語部分）
- 左端にゴールドのグラデーションライン（4px）
- セクション番号を右下に16remの大きな透かし文字（opacity: 0.10）で背景に溶け込ませる

#### レイアウト構成:
- **ヘッダー部分（固定）**: Section番号 + タイトル + ディバイダー → 中央揃え、padding: 48px 100px
- **コンテンツ部分（スクロール可能）**: `overflow-y: auto` + `align-items: center`
- タイトルスライドのみ全体中央配置（やや上寄り：`padding-bottom: 120px`）

#### CSS設計:

```css
/* スライド基本 */
.slide {
  padding: 0; /* header/bodyに分離 */
  transform: translateY(-30px); /* 上から下へアニメーション */
}

/* ヘッダー固定 */
.slide-header {
  padding: 48px 100px 0;
  text-align: center;
  display: flex; flex-direction: column; align-items: center;
}

/* コンテンツスクロール */
.slide-body {
  padding: 20px 100px 60px;
  overflow-y: auto;
  display: flex; flex-direction: column; align-items: center;
}

/* 背景番号 */
.slide-bg-num {
  position: absolute; right: 60px; bottom: 20px;
  font-size: 16rem; color: rgba(201,169,110,0.10);
}

/* ナビ・ページ番号（小さく控えめ） */
.nav button { width: 28px; height: 28px; font-size: 0.7rem; }
.page-indicator { font-size: 0.7rem; color: #ccc; }
```

#### コンポーネント:

| 要素 | 背景 | ボーダー | 文字色 |
|------|------|---------|--------|
| カード | #fafaf8 | #eee + 上部ゴールドline | タイトル: #b8942e |
| ハイライト | #faf8f3 | 左3px #c9a96e | — |
| テーブルTH | #faf8f3 | 下2px #c9a96e | #b8942e |
| タグ(通常) | transparent | #ddd | #666 |
| タグ(gold) | #faf8f3 | #c9a96e | #b8942e |
| 緊急カード | — | 上部に赤グラデ | #c26a5a |

#### フッター:
- 全スライド共通: `produced by いちじく`
- font-size: 0.65rem, color: #ccc, 下部中央

#### 操作:
- 矢印キー（← →）・スペースキーでスライド切替
- スワイプ対応
- 右下に小さなナビボタン、左下にページ番号

### Step 4: GitHub Pagesにデプロイ
1. `pages/[英語名]/index.html` にスライドHTMLをコピー（英語名はクライアント名のローマ字）
2. git add → commit → push
3. GitHub PagesのURL: `https://linemailgift.github.io/agent-workspace/pages/[英語名]/`

```bash
mkdir -p pages/[英語名]
cp "[スライドHTMLパス]" pages/[英語名]/index.html
git add -A && git commit -m "[クライアント名]議事録スライド追加" && git push
```

### Step 5: ブラウザで開く
```bash
open "[生成したHTMLファイルのパス]"
```

### Step 6: 完了処理
1. チャットに **完成時刻** を表示
2. **GitHub PagesのURL** を表示
3. 完了音を鳴らす:
```bash
afplay -r 0.5 /System/Library/Sounds/Submarine.aiff
```

---

## 基本ルール（必ず守ること）

1. **ちいくじ = ユーザー本人**。「氏」は付けない
2. 「知育時」「チーフジ」等の誤変換 → 「ちいくじ」に修正
3. **クライアントには敬称「さん」を付ける**（例：おがたつさん）。呼び捨て禁止
4. **UTAGE** はシステム名称。「宴（UTAGE）」「宴」ではなく **UTAGE** とだけ表記
5. 金額は控えめに表示（大きいフォントや装飾は避ける）。クライアント向け資料なので驚かせない
6. セクションの位置は全スライドで統一する（ページ切替時に安定感が出る）
