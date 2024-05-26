# Welcome to MkDocs

For full documentation visit [mkdocs.org](https://www.mkdocs.org).

## Commands

- `mkdocs new [dir-name]` - 建立新的 MKdocs 專案
- `mkdocs serve` - 啟動一個支援熱更新的 MKdocs 伺服器
- `mkdocs build` - 建立 MKdocs 專案的靜態網頁
- `mkdocs -h` - 印出 MKdocs 的所有幫助訊息
- `mkdocs gh-deploy --force` - 手動佈署靜態網頁至 GitHub pages

## Project layout

    mkdocs.yml    # The configuration file.
    docs/
        index.md  # The documentation homepage.
        ...       # Other markdown pages, images and other files.

## Tips
- 每個 docs/md 文件，勿使用 H1 標題 (因為不會顯示在 table on contents)
  - 因為 MKdocs 預設 md 文件的置頂標題就是 H1 標題，所以從 H2 標題開始撰寫文件內容
- 粗體字: `**粗體字**` 當遇到繁體中文時，因為非空白字元作為分隔符號的語言，因此需要在前, 後加上空白字元，才能顯示正確的粗體字
    - 解決方案: 用 HTML 標籤 <b> </b> 來表示粗體字
- 下劃線: 使用 HTML 標籤 <u> </u> 來表示下劃線

## References

- [Mkdocs 部署靜態網頁至 GitHub pages 組態說明 (mkdocs.yml)](https://zhuanlan.zhihu.com/p/613038183)
