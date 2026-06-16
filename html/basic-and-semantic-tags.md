# 自己紹介サイト制作課題を通して学んだこと（HTML - タグ編）

## head
### `meta charset=""`
- 文字コードを設定する。（`charset="UTF-8"`）

### `meta name=""`
- `content` とセットで使用する。
- `name=""` の中身に合わせて内容が変化するため、必ずしもレスポンシブのためのものではない。  
  （例：name="`description`" content="`検索結果に表示される文章`" 等）
- レスポンシブ対応のために使用する場合、cssだけではなくhtml側で以下のように設定しておく必要がある。  
  `name="viewport" content="width=device-width, initial-scale=1"`
  - `viewport`：表示領域・ウィンドウ枠
  - `width=device-width`：viewportを端末の幅に合わせる（**vwの基準**となる）
  - `initial-scale=1`：初期表示時のズーム倍率

> 関連：[MDN(meta name viewport)](https://developer.mozilla.org/ja/docs/Web/HTML/Reference/Elements/meta/name/viewport) / [MDN(meta name)](https://developer.mozilla.org/ja/docs/Web/HTML/Reference/Elements/meta/name)

### `title`
- ページのタイトルを設定する。ブラウザのタブや検索結果に表示される。

> 関連：[MDN](https://developer.mozilla.org/ja/docs/Web/HTML/Reference/Elements/title)

### `link`
- 外部ファイル（CSS・Googleフォント・Font Awesomeのアイコン等）を読み込む。
  - `href=""`：参照先（URLやファイルパス）
  - `rel=""`：関係性を指定
    - `stylesheet`：CSSファイル、Font Awesome
    - `icon`：ファビコン
    - `preconnect`：外部サービスとの接続を事前に確立（読込みの待ち時間短縮）

> 関連：[MDN](https://developer.mozilla.org/ja/docs/Web/HTML/Reference/Elements/link)
