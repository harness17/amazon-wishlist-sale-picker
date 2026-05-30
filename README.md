# Amazon Wishlist Sale Picker

Amazon.co.jp の欲しいものリストを開くと、**「セールのみ表示」ボタン**が追加されます。ボタンを押すと全件をスキャンしてセール中の商品だけに絞り込みます。

## 機能

- **セール判定** — タイムセールバッジ・打ち消し価格・価格下落通知・日英キーワードの 4 系統で判定
- **割引率フィルター** — ポップアップのスライダーで「○% 以上の割引のみ表示」に絞り込み
- **全件自動読み込み** — ボタン押下時に lazy load を自動スクロールで全件展開（オーバーレイで進捗表示）
- **全リスト横断スキャン** — サイドバーの複数ウィッシュリストを順に開き、結果ページにセール商品を集約
- **日英 UI 切替** — popup と結果ページの表示言語を日本語 / English で切り替え
- **ローカル完結** — 外部サーバーへの通信なし、`storage` 権限のみ使用

## 対応 URL

- `https://www.amazon.co.jp/hz/wishlist/ls/*`
- `https://www.amazon.co.jp/hz/wishlist/genericItemsPage/*`
- `https://www.amazon.co.jp/gp/registry/wishlist/*`

amazon.com など海外 Amazon は対象外です。Amazon.co.jp を英語表示にした場合の英語セール文言は検出対象に含めています。

## インストール

Chrome / Firefox のストアには未公開です。手動でインストールしてください。

1. このリポジトリを clone またはダウンロード
   ```bash
   git clone https://github.com/harness17/amazon-wishlist-sale-picker.git
   ```
2. `.\scripts\build-dev.ps1` を実行（拡張をビルド。コード変更のたびに実行）
3. `chrome://extensions/` を開く
4. 右上「デベロッパーモード」をオン
5. 「パッケージ化されていない拡張機能を読み込む」→ `dist/dev/chrome` を選択（パスは固定。以後は手順2の再実行 → 🔄 リロードで反映）

Firefox は `.\scripts\build-dev.ps1 -Target all` を実行し、`about:debugging#/runtime/this-firefox` から `dist/dev/firefox/manifest.json` を一時的なアドオンとして読み込みます。

> 開発用ロードはバージョン名のない固定パス `dist/dev/<browser>/` を使うため、版を上げてもフォルダを選び直す必要はありません。出力先 `dist/` は Git 管理外です。ストア提出用の版番号付きパッケージは `scripts/package-release.ps1` で別途生成します。

## 開発

### 回帰テスト

`detectSale()` の単体検証を実行します（jsdom が必要）:

```bash
cd amazon-wishlist-sale-picker
npm install --no-save jsdom
node verify-detect.mjs
```

```
✓ B0DQWP6LX1 (タイムセール 15%OFF)
✓ B07TKLB1ML (通常価格)
✓ B00ICCU4U4 (価格下落 30%)
✓ B0TESTSALE (amazon.com limited time deal 20% off)
✓ B0TESTDROP (amazon.com price dropped 30%)
✓ B0TESTHIDDEN (amazon.com aria-label 20% off)
```

### ファイル構成

```
amazon-wishlist-sale-picker/
├── extension/
│   ├── background.js      横断スキャンの状態管理と結果ページ起動
│   ├── shared/            detectSale() と日英表示辞書
│   ├── content/           ウィッシュリストページに注入されるスクリプトとCSS
│   ├── popup/             割引率フィルターの設定 UI
│   ├── results/           横断スキャン結果ページ
│   └── icons/             16 / 48 / 128px アイコン（プレースホルダー）
├── manifests/
│   ├── chrome.json        Chrome 配布用 Manifest V3
│   └── firefox.json       Firefox 配布用 Manifest V3
├── scripts/               リリースパッケージ生成
├── store-assets/          ストア提出用スクリーンショットと過去パッケージ
├── fixtures/              jsdom テスト用の実 DOM スニペット
│   ├── HOW_TO_CAPTURE.md  新パターン追加手順
│   ├── sale-item.html
│   ├── normal-item.html
│   ├── price-drop-item.html
│   └── amazon-com-*.html   Amazon.co.jp 英語表示相当の文言検証 fixture
└── verify-detect.mjs      回帰検証スクリプト
```

### 新しい DOM パターンが見つかったとき

1. DevTools で対象の `li[data-id]` 要素を「Copy outerHTML」
2. `fixtures/<ASIN>.html` として保存
3. `verify-detect.mjs` にテストケースを追加
4. `node verify-detect.mjs` で全件 PASS を確認
5. 必要なら `extension/shared/detect.js` の検出ロジックを修正

詳細: [fixtures/HOW_TO_CAPTURE.md](fixtures/HOW_TO_CAPTURE.md)

### リリースパッケージ

Chrome と Firefox のリリースは `manifests/chrome.json` と `manifests/firefox.json` で別々に管理します。配布時は `extension/` の中身と対象 manifest を組み合わせます。

```powershell
.\scripts\package-release.ps1 -Target chrome
.\scripts\package-release.ps1 -Target firefox
.\scripts\package-release.ps1 -Target all
```

生成物は `dist/<target>/` に出力され、Git には含めません。

## ロードマップ

今後の開発計画は [ROADMAP.md](ROADMAP.md) を参照してください。

## ライセンス

MIT
