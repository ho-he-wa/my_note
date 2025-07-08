# GitHub Pagesについてのノート
## デフォルト表示ファイル

GitHub PagesのURLでファイル指定なしの場合は特にファイルリストが表示されるわけではない。404エラーになる。

`index.html` 、`index.md` 、`README.md` のいずれかがあればそのファイルが表示される。 [^default_file]

[^default_file]: https://docs.github.com/ja/pages/getting-started-with-github-pages/creating-a-github-pages-site

## リンクと.mdのレンダリング

GitHub Pagesで `.md` などのファイルへのリンクを作成する場合は拡張子を `.html` にする。 [^link_extension]

こうすることでレンダリングされたページが表示される。

[^link_extension]: https://docs.github.com/ja/pages/getting-started-with-github-pages/creating-a-github-pages-site#next-steps

## .adoc

.adoc (Asciidoc) には対応していない。[^jekyll_file]

>  JekyllはMarkdownおよびHTMLファイルを取り込み、選択したレイアウトに基づいて、完成された静的ウェブサイトを作成します。

[^jekyll_file]: https://docs.github.com/ja/pages/setting-up-a-github-pages-site-with-jekyll/about-github-pages-and-jekyll#about-jekyll

## オートリンク

なぜかオートリンクが機能しない(2024/07/02現在)。

次の2つのURLのうち上の方はリンクになっていないはず。

https://docs.github.com/ja/pages/getting-started-with-github-pages/creating-a-github-pages-site

[https://docs.github.com/ja/pages/getting-started-with-github-pages/creating-a-github-pages-site](https://docs.github.com/ja/pages/getting-started-with-github-pages/creating-a-github-pages-site)

https://github.com/marketplace/actions/asciidoctor-ghpages

