# Document Generator by Foliant

要件定義書や各種設計資料をソースコードと同じリポジトリで管理して設計と実装の乖離を防ぐ、という取り組みを支える手段として作成したプロジェクトです。開発プロジェクトの `docs` ディレクトリ配下に設置して使うようなイメージです。
Markdownなどのプレーンテキストで記述されたドキュメントを、[Foliant](https://github.com/foliant-docs/foliant)をつかってPDFやWord文書などの形式に書き出します。

# 書き出し方法

FoliantはMakrdownやPlantUMLなどプレーンテキストで記述した文章を、Word文書もしくはウェブサイトの形式に書き出すことができます。
書き出すには下記コマンドを実行してください。尚、実行にはDockerが必要です。

書き出すファイル群の構成やUMLなどの図の取り扱いについてはfoliant.ymlを編集してください。
Foliantの詳しい仕様については下記ドキュメントを参照してください。
https://foliant-docs.github.io/docs/

## ウェブサイトとして書き出す

以下のコマンドを実行します。

```shell
docker-compose run --rm foliant make --log ./logs --with mkdocs site
```

ローカル環境上でウェブサイトとしてアクセスしたい場合はPythonやPHPのビルトインサーバーを下記コマンドで実行します。

### Pythonの場合

```shell
python3 -m http.server -d ./mkdocs
```

### PHPの場合

```shell
php -S localhost:8000 -t ./mkdocs
```

## Word文書として書き出す

以下のコマンドを実行します。

```shell
docker-compose run --rm foliant make --log ./logs docx
```

# ディレクトリ構成

```
.
├── docker              Docker関連ファイル
├── logs                Foliantが出力するログ
└── src                 資料の元ファイル群
```

# 注意点

FoliantではPlantUMLやGraphvizの記述を `<plantuml/>` や `<graphviz/>` で括る形式になっていますが、これだと例えばVSCodeのMarkdown Preview Enhancedで意図したとおりにプレビューすることができません。
この場合、コマンドパレットから `Markdown Preview Enhanced: Extend Parser` を開いて `onWillParseMarkdown()` に以下を追加することで正しくプレビューすることができます。

```javascript
onWillParseMarkdown: async function(markdown) {
    return new Promise((resolve, reject) => {
        markdown = markdown.replace(
            /\<graphviz[^>]*\>([\w\W]+?)\<\/graphviz\>/g,
            (whole, content) => `
                \`\`\`dot
                ${content}
                \`\`\`
            `,
        );
        markdown = markdown.replace(
            /\<plantuml[^>]*\>([\w\W]+?)\<\/plantuml\>/g,
            (whole, content) => `
                \`\`\`plantuml
                ${content}
                \`\`\`
            `,
        );
        return resolve(markdown)
    })
}
```
