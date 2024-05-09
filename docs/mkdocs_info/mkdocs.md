# Welcome to MkDocs

For full documentation visit [mkdocs.org](https://www.mkdocs.org).

## Commands

- `mkdocs new [dir-name]` - Create a new project.
- `mkdocs serve` - Start the live-reloading docs server.
- `mkdocs build` - Build the documentation site.
- `mkdocs -h` - Print help message and exit.
- `mkdocs gh-deploy --force` - Deploy your project documentation manually.

## Project layout

    mkdocs.yml    # The configuration file.
    docs/
        index.md  # The documentation homepage.
        ...       # Other markdown pages, images and other files.

## Tips
- 每個 docs/md 文件，勿使用 H1 標題 (因為不會顯示在 table on contents)
  - 因為 MKdocs 預設 md 文件的置頂標題就是 H1 標題，所以從 H2 標題開始撰寫文件內容

## References

- [Mkdocs 部署靜態網頁至 GitHub pages 組態說明 (mkdocs.yml)](https://zhuanlan.zhihu.com/p/613038183)
