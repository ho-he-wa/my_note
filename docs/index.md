- Table of Content
{:toc}

# index
## ノート


- [PHPの各バージョンでの変化](./php_versions.html) ([.md](./php_versions.md))
- [ドメイン駆動設計についてのノート](./ddd-domain_driven_design.html) ([.md](./ddd-domain_driven_design.md))



## GitHub Pages
### デフォルト表示ファイル

GitHub PagesのURLでファイル指定なしの場合は特にファイルリストが表示されるわけではない。404エラーになる。

`index.html` 、`index.md` 、`README.md` のいずれかがあればそのファイルが表示される。 [^default_file]

[^default_file]: https://docs.github.com/ja/pages/getting-started-with-github-pages/creating-a-github-pages-site

### リンクと.md等のレンダリング

GitHub Pagesで `.md` などのファイルへのリンクを作成する場合は拡張子を `.html` にする。 [^link_extension]

こうすることでレンダリングされたページが表示される。

[^link_extension]: https://docs.github.com/ja/pages/getting-started-with-github-pages/creating-a-github-pages-site#next-steps

### オートリンク

なぜかオートリンクが機能しない(2024/07/02現在)。

次の2つのURLのうち上の方はリンクになっていないはず。

https://docs.github.com/ja/pages/getting-started-with-github-pages/creating-a-github-pages-site

[https://docs.github.com/ja/pages/getting-started-with-github-pages/creating-a-github-pages-site](https://docs.github.com/ja/pages/getting-started-with-github-pages/creating-a-github-pages-site)
