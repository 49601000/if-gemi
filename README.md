# もしジェミ Prompt Builder Suite

「比喩 → 構造化 → 会話化 → レビュー抽出 → 部分修正」を5つのHTMLツールで回す創作支援パイプライン。  
各ツールは独立利用も可能。  
順番に使うと最も効果が高い設計です。

## ファイル一覧
| File | Purpose |
| --- | --- |
| `metaphor.html` | 比喩生成（発明と検査） |
| `scenario.html` | シナリオ構造化（Source/Target翻訳） |
| `conversation.html` | 会話生成（師匠－僕の会話台本化） |
| `review.html` | レビュー抽出（修正対象/修正意図の整理） |
| `proofreading.html` | 部分修正（惜しい箇所の赤入れ） |
| `index.html` | ランチャー / 保存データ管理 |

## 使い方（推奨フロー）
1. `metaphor.html` で比喩を作る  
2. `scenario.html` で構造骨子へ翻訳する  
3. `conversation.html` で会話台本へ整形する  
4. `review.html` で「修正対象」と「修正意図」を抽出する  
5. `proofreading.html` で必要箇所だけ磨く

## 役割の境界（A vs B）
- `metaphor.html`: 比喩の**発明と検査**
- `scenario.html`: 比喩の**構造翻訳**
- `conversation.html`: 構造の**会話化（読者向け表現）**
- `review.html`: 会話劇の**修正ポイント抽出（診断）**
- `proofreading.html`: 抽出結果を使った**部分修正・コース修正（処置）**

## 実運用メモ（iPhoneホーム画面）
- 各ツールは入力・設定・生成結果を `localStorage` に自動保存
- 保存キーは `moshiJemi_` prefix で統一
- ランチャーに戻って再度開くと、続きから再開可能
- 保存データ削除ボタンは `index.html` のみ
- クリア対象は `moshiJemi_` で始まるキーだけ（他ツール保存は触らない）

## Advanced（概要）

### `metaphor.html`
- Gateチェック付きで比喩を生成
- Source Domainを指定して比喩の方向性を固定
- 理論/失敗パターン/生成フローをタブで確認可能

### `scenario.html`
- Source/Target対応を崩さずにシナリオ骨子へ翻訳
- `Scenario Structure` / `Narrative Punch` をトグルで制御
- Prompt Previewを常時更新して編集可能

### `conversation.html`
- DSL先行で口調・代名詞・禁止語を固定
- 6パラメータは **deterministic分岐ではなく prompt steering parameter**
- 詳細仕様・UI→プロンプト対応は [`docs/conversation-spec.md`](docs/conversation-spec.md) を参照
- 実際の出力挙動は `conversation.html` の `buildPrompt()` が正本（specのfetchプレビュー機能は非採用）
- 注記: 同じ数値でもモデル差で効き方が多少変わるため、最終調整は実出力で確認する

### `review.html`
- `conversation.html` で生成した会話劇をレビューし、後続の部分修正用指示を抽出
- 出力は `【修正対象】` / `【修正意図】` の2ブロック
- 両ブロックは同じ番号で対応（`- 1.` / `- 2.` ...）し、貼り付け時に追跡しやすい形式
- `構造メモをレビュー対象に含める` のON/OFFを選択可能
- 保存キー: `moshiJemi_review_state`

### `proofreading.html`
- 全文再生成ではなく「指定範囲だけ修正」用途
- `出力形式` と `返却範囲` を分離して制御
- `review.html` の抽出結果（修正対象/修正意図）を投入して、局所修正に使う想定
- 仕上げの微調整（温度感、キャラ整合、比喩精度、オチ補強）に特化

## 補足
- ファイル名は `scenario.html` / `conversation.html` で統一
- 前段出力を次段で再利用するパイプライン構成
- `review.html` は「直す場所を決める」段階、`proofreading.html` は「実際に直す」段階
- モバイル運用では `moshiJemi_` prefix 保存 + `index.html` クリアが実務的
