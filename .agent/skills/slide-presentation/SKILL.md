---
name: slide-presentation
description: 議事録・提案書などからプレゼンテーションスライド（HTML）を自動生成するスキル
---

# スライド制作スキル

## 概要
クライアント打ち合わせの議事録（MD）や提案情報をもとに、ブラウザ表示可能なHTMLスライドを生成する。

---

## デザインシステム

### カラーパレット（デフォルト）
```
メインカラー:    linear-gradient(135deg, #8B5CF6, #7C3AED)    — 紫
アクセント:      linear-gradient(135deg, #8B5CF6, #EC4899)     — 紫→ピンク
背景（外枠）:    linear-gradient(135deg, #8B5CF6 0%, #7C3AED 30%, #EC4899 100%)
コンテンツ背景:  #FFFFFF（白）
パネル背景:      #F3F0FF（薄紫）, #FAF5FF
テキスト:        #1F2937（濃紺チャコール）
サブテキスト:    #6B7280
見出しアクセント: #7C3AED
カプセル見出し背景: linear-gradient(135deg, #8B5CF6, #A78BFA)
カプセル見出し文字: #FFFFFF
```

### フォント
```css
@import url('https://fonts.googleapis.com/css2?family=Noto+Sans+JP:wght@300;400;500;700;900&display=swap');

body { font-family: 'Noto Sans JP', sans-serif; }
```

| 用途 | サイズ | ウェイト | 色 |
|------|--------|---------|-----|
| スライドタイトル（表紙） | 2.8rem | 900 | #1F2937 |
| ページ見出し | 1.6rem | 700 | #1F2937 |
| カプセル見出し | 1.0rem | 600 | #FFFFFF |
| 本文 | 0.95rem | 400 | #1F2937 |
| サブテキスト | 0.8rem | 300 | #6B7280 |
| フッター | 0.65rem | 300 | #9CA3AF |

### レイアウト（16:9、1920×1080想定）

```
┌──────────────────────────────────────────┐
│ ◀ 紫→ピンク グラデーション外枠（8px）       │
│ ┌──────────────────────────────────────┐ │
│ │                                      │ │
│ │  ┃ ページ見出し                       │ │  ← 左に紫の縦バー（4px）
│ │                                      │ │
│ │  ┌─ カプセル見出し ─┐                 │ │  ← 丸角グラデーション背景
│ │                                      │ │
│ │  [コンテンツエリア]                    │ │  ← 白背景
│ │                                      │ │
│ │  フッター                            │ │  ← produced by いちじく
│ └──────────────────────────────────────┘ │
└──────────────────────────────────────────┘
```

---

## スライドの種類（テンプレート）

### 1. 表紙（Title Slide）
- 中央揃えの大きなタイトル
- サブタイトル（チーム名・日付）
- 背景に半透明の大文字テキスト（ウォーターマーク）
- 下部にキャッチコピー（帯状）

### 2. 概要（Overview Slide）
- 左に紫縦バー付きの見出し
- カプセル型サブ見出し（紫グラデーション + 白文字）
- 2カラム構成（アイコン + テキスト）
- 各項目: 円形アイコン（64px, 紫寄りの色）+ 見出し + 説明文

### 3. 詳細説明（Detail Slide）
- 番号付き見出し（例: 「詳細① ステップ配信設計」）
- 左に紫縦バー
- カプセル型サブ見出し
- フロー図 or 箇条書き + 右側にイラスト/モックアップ

### 4. グリッド（Grid Slide）
- 2×2 or 3カラムのカードレイアウト
- 各カード: 薄紫背景 + 円形アイコン + 見出し + 説明
- 均等配分

### 5. 料金・比較表（Pricing Slide）
- 3カラム構成（松竹梅プラン）
- 各プランのヘッダー: グラデーション背景
- 大きな価格表示（2.5rem, 紫色）
- 白カード内に箇条書きで内容

### 6. ステップ・フロー（Step Slide）
- 番号（01, 02, 03...）を大きく表示（Cormorant Garamond, 2rem）
- 各ステップに円形アイコン
- ステップ間に矢印（→ or ─►）
- 下にカプセル型ラベル + 説明文

### 7. サポート・安心（Support Slide）
- 3つの安心ポイント
- 各ポイント: アイコン + 見出し + 説明文
- グリッドレイアウト

---

## 共通デザイン要素

### カプセル型見出し
```css
.capsule-heading {
  display: inline-block;
  background: linear-gradient(135deg, #8B5CF6, #A78BFA);
  color: #fff;
  padding: 8px 28px;
  border-radius: 25px;
  font-size: 1.0rem;
  font-weight: 600;
  letter-spacing: 1px;
}
```

### 円形アイコン
```css
.circle-icon {
  width: 64px;
  height: 64px;
  border-radius: 50%;
  background: #F3F0FF;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 1.5rem;
  color: #7C3AED;
}
```

### 左縦バー見出し
```css
.bar-heading {
  border-left: 4px solid #7C3AED;
  padding-left: 16px;
  font-size: 1.6rem;
  font-weight: 700;
  color: #1F2937;
}
```

### 薄紫パネル
```css
.panel {
  background: #F3F0FF;
  border-radius: 12px;
  padding: 24px;
}
```

---

## ナビゲーション

```javascript
// 矢印キーでスライド切替
document.addEventListener('keydown', (e) => {
  if (e.key === 'ArrowRight' || e.key === 'ArrowDown') nextSlide();
  if (e.key === 'ArrowLeft' || e.key === 'ArrowUp') prevSlide();
});
```

- 左下に「◀ ▶」ボタン
- 右下にページ番号「3 / 11」
- スライド切替時にフェードインアニメーション

---

## アニメーション

```css
@keyframes fadeInUp {
  from { opacity: 0; transform: translateY(20px); }
  to { opacity: 1; transform: translateY(0); }
}

.slide.active .animate { animation: fadeInUp 0.5s ease-out forwards; }
```

- スライド表示時に各要素が `fadeInUp` で順次登場
- `.animate-delay-1`, `.animate-delay-2` で遅延制御（0.1s刻み）

---

## 生成手順

1. 入力情報（議事録MD / 提案内容 / メモ）を読み込む
2. スライド構成を設計（何枚、何の種類で構成するか）
3. 各スライドのコンテンツを配置
4. HTMLファイルとして生成（単一HTML、外部依存なし）
5. ブラウザで表示確認
6. GitHub Pagesにデプロイ

## 出力ルール

- フッター: `produced by いちじく`（余計なテキスト不要）
- 単一HTMLファイル（CSS/JSインライン）
- Google Fonts CDNのみ外部読み込み可
- レスポンシブはPC優先（1920×1080基準）
- 印刷用CSSは不要（ブラウザ表示専用）

---

## GitHub Pagesデプロイ手順

HTMLを作成したら**必ず以下の手順でGitHub Pagesに公開し、URLをチャットに表示する。**

### ⚠️ 絶対ルール：URLに日本語を入れない

- **パスに日本語は絶対に使わない**（URLエンコードで長くなるため）
- すべてのファイルは `pages/クライアント英名/ファイル名.html` に配置する
- このURLはそのままコピーしてクライアントに渡すものなので、短くきれいに保つ

### 1. ファイルの配置ルール

リポジトリルート（`事業/■AGENT/`）直下の `pages/` フォルダに英語名で配置する。

```
pages/
├── nakagawa/
│   └── proposal.html
├── miki/
│   ├── proposal.html
│   ├── minutes.html
│   └── roadmap.html
├── masumi/
│   └── roadmap.html
└── ogatatsu/
    └── roadmap.html
```

| 用途 | ファイル名 |
|------|-----------|
| 提案書スライド | `proposal.html` |
| 議事録スライド | `minutes.html` |
| ロードマップ | `roadmap.html` |
| その他 | 英数字のみ、内容を表す短い名前 |

### 2. Git操作

// turbo
```bash
cd "リポジトリルート"
cp "元の日本語ファイル.html" pages/クライアント英名/ファイル名.html
git add pages/クライアント英名/ファイル名.html
git commit -m "Add ファイル説明"
git push origin main
```

### 3. デプロイ確認

// turbo
```bash
sleep 60 && curl -s -o /dev/null -w "%{http_code}" "公開URL"
```

### 4. URLの表示（必須）

チャット上にコードブロックで**必ず公開URLを表示する**。そのままコピペでクライアントに渡せる形式にすること。

```
https://linemailgift.github.io/agent-workspace/pages/クライアント英名/ファイル名.html
```

### クライアント名マッピング

| クライアント | 英名 | 専用リポジトリ |
|-------------|------|--------------|
| ますみ | masumi | masumi-competitors |
| おがたつ | ogatatsu | ogatatsu-competitors |
| MIKI（伊達さん） | miki | — (agent-workspace) |
| 中川 | nakagawa | — (agent-workspace) |
| えるらぼ | erurabo | — (agent-workspace) |
| まい | mai | — (agent-workspace) |

**専用リポジトリがあるクライアント**はそちらのルート直下に配置する。
