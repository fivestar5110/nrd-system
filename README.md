# N.R.D. (Nageppanashi Race Director)[cite: 3]

N.R.Dは、FPVドローンレースの運営進行をサポートするために作成したブラウザベースのWebアプリケーションです。[cite: 3]

既存の素晴らしい計測ソフトや、AI実況をはじめとする最先端の配信ツールを外から拝見する中で、「もっとこんな画面や演出が出せたら面白いかもしれない」と思い立ちました。[cite: 3] プロの領域とは全く違う、素人なりのアプローチで自己満足的に機能を継ぎ足し続け、現在の形に組み上がったシステムです。[cite: 3]

## Concept: 現場を止めない、少しでも盛り上げる[cite: 3]
本システムは高度なサーバーやデータベースを持たず、ローカル環境のブラウザ（HTML/JS）単体で泥臭く動作します。[cite: 3]

1. **ローカル完結による運用継続（コア）**[cite: 3]
   通信トラブル等によるシステム停止リスクを避けるため、全てのデータ処理はブラウザの内部（IndexedDB）で完結するように設計しています。[cite: 3] 万が一ブラウザが終了してしまっても、自動保存されたデータから直前の状態に復帰し、なんとかレースの進行を止めずに済む仕組みを目指しました。[cite: 3]
2. **小さなエンタメ要素の付加**[cite: 3]
   正確なタイム計測は既存のシステムにお任せし、N.R.Dは「演出」に重きを置いています。[cite: 3] ベストラップ更新や順位変動を検知して画面にテロップを出したり、OBSと連携して短いリプレイ動画を再生したりと、会場の雰囲気を少しでも盛り上げるための機能を追加しています。[cite: 3]

## Architecture & Required Software[cite: 3]
N.R.Dは単体では計測ができません。[cite: 3] 先人たちが開発された以下のソフトウェアと連携させていただく形で動作します。[cite: 3]

*   **N.R.D (index.html)**[cite: 3]
    *   本システムです。[cite: 3] Chrome等のブラウザで開き、大会のブラケット（トーナメント表）管理やUI表示、各ソフトへの連携コマンド送信を行います。[cite: 3]
*   **Tiny View Plus (カスタム改修版)**[cite: 3]
    *   ラップ計測に対応したFPVマルチビューアーです。[cite: 2] 本家リポジトリ（t-asano/tinyviewplus）の素晴らしいシステムをベースとして利用させていただき、[cite: 2] N.R.Dと連動させるため、ラップ通過時に軽量なイベント（JSON）を出力する処理などをTVP側（C++）に独自追加して運用しています。[cite: 3]
*   **OBS Studio (連携強化版)**
    *   配信・録画ソフトです。[cite: 3] 今回のアップデートにより連携機能を大幅に強化しました。OBS WebSocket (v5) を経由して、N.R.Dから録画の開始/停止を完全同期させるだけでなく、リプレイバッファ機能を利用してハイライト動画（AUTO REPLAY）の自動保存からUI上でのシームレスな再生まで、一連の配信コントロールをN.R.D内で完結させています。

## Key Features[cite: 3]
*   **GUI Bracket Builder:** ドラフト生成アルゴリズムによるヒート組みの補助と、画面上でのブラケット編集機能。[cite: 3]
*   **Series Manager:** 長期的なシリーズ戦のためのポイント計算、有効ポイント制の集計、表記揺れのあるパイロット名の統合（名寄せ）機能。[cite: 3]
*   **Auto Replay System:** レース中の順位変動やベストラップ更新を検知し、OBSのリプレイバッファに保存。[cite: 3] ヒート終了後にN.R.D上で動画を再生する簡易ハイライト機能。[cite: 3]
*   **Live Viewer Sync:** レースの進行状況（現在順位や次のヒート情報）を、GAS（Google Apps Script）経由で観客のスマートフォンへ簡易的にテキスト配信する機能。[cite: 3]
*   **Lap Fixer:** 誤検知によるラップのズレを、後から等分分割したり無効化したりして手動補正する機能。[cite: 3]

## How to Use[cite: 3]
N.R.DはWebサーバーへのデプロイは不要で、ローカルPC上で動作します。[cite: 3]

### 1. Boot Sequence (起動とディレクトリ接続)[cite: 3]
1. ローカルサーバー（VS CodeのLive Server等）経由で `index.html` をブラウザで開きます。[cite: 3]
2. 起動画面にて、連携する各種ローカルディレクトリの読み書き権限をブラウザに付与します。[cite: 3]
   * **TVP RESULTS:** Tiny View Plus の `data` フォルダ（リザルトファイルとJSONを読み込みます）。[cite: 3]
   * **OBS VIDEO:** OBSがリプレイバッファを保存する動画フォルダ。[cite: 3]
   * **PILOT ICONS:** パイロットのアイコン画像を格納したフォルダ。[cite: 3]
   * **BGM PLAYLIST:** 会場BGM用の音声ファイル（mp3等）を入れたフォルダ。[cite: 3]
3. 接続が完了したら `[ IGNITION ]` をクリックしてUIを起動します。[cite: 3]（次回以降は `[ QUICK BOOT ]` で一括再接続が可能です）[cite: 3]

### 2. Race Direction (レース進行)[cite: 3]
1. 画面下部の `[ 🎤 DIRECTOR ]` から、CSV読み込みや前回の結果を利用してヒート構成（ドラフト）を作成し、ブラケットに展開します。[cite: 3]
2. `[ ▼ CONTROLS ]` パネルから目的のヒートを `ACTIVE` にし、レースをスタート（録画開始）します。[cite: 3]
3. 制限時間や規定周回数に達すると自動（または手動 `Space` キー）で計測・録画を終了します。[cite: 3]
4. 終了後、必要に応じて `[ ⚖️ JUDGE ]` 画面からラップ修正を行い、`[ ■ FINISH HEAT ]` で順位を確定させます。[cite: 3]

---

## Keyboard Shortcuts[cite: 3]
現場でのマウス操作の負担を減らすため、以下のショートカットキーを用意しています。[cite: 3]

| Key | Action |
| :--- | :--- |
| **`1`** | Change View (Dual / Best / Race の画面切り替え)[cite: 3] |
| **`2`** | View: BRACKET (トーナメント表表示)[cite: 3] |
| **`3`** | View: OVERALL (総合順位表示)[cite: 3] |
| **`4`** | View: SERIES (シリーズランキング表示)[cite: 3] |
| **`D`** | Toggle Dashboard (コントロールパネル開閉)[cite: 3] |
| **`F`** | Toggle Fullscreen (フルスクリーン切替)[cite: 3] |
| **`O`** | Open Director Modal (ディレクター画面開閉)[cite: 3] |
| **`S`** | Toggle Auto Scroll (自動スクロール ON/OFF)[cite: 3] |
| **`A`** | Call Active/Waiting Heat (ヒート開始アナウンス)[cite: 3] |
| **`E`** / **`Enter`** | Finish Active Heat / Start Next (ヒート確定 / 次へ進む)[cite: 3] |
| **`Space`** | Stop TVP (TVP計測強制終了)[cite: 3] |
| **`P`** | Toggle Ceremony (表彰式用BGM演出)[cite: 3] |
| **`J`** | Toggle BGM Play / Pause[cite: 3] |
| **`K`** | Play Next BGM (次の曲へ)[cite: 3] |
| **`[`** / **`]`** | BGM Volume Down / Up[cite: 3] |
| **`Z, X, C, V...`** | Manual Lap Trigger (カメラ枠順の手動ラップ追加)[cite: 3] |
| **`Shift + Z, X...`**| Manual Lap Delete (カメラ枠順の手動ラップ削除)[cite: 3] |