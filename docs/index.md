# mkdocs-example

## mkdocsとgithub pagesを用いた静的サイトの作成方法

1. 準備

    ```
    sudo apt-get install -y mkdocs
    or
    pip install mkdocs-material
    ```

2. mkdocs作成

    ```sh
    mkdocs new .
    ```
    このコマンドで、mkdocs.ymlとdocs/が作成される

    ```yml
    site_name: My Docs
    theme:
        name: material
    ```
    mkdocs-materialというテーマを使用する

    `./docs/`以下に`*.md`で書きたいことを書く

3. deploy

    **githubのリポジトリをremoteで作成**

    `gh-pages`ブランチを作成し、push
    ```sh
    git checkout -b gh-pages
    git push origin gh-pages
    ```

    `main`ブランチにて、*.mdやmkdocs.ymlなどのファイル群をpush
    ```sh
    git add -A
    git commit -m "<commit-message>"
    git push origin main
    ```

    **github actionsの設定を行う**

    `リポジトリ->Setting->Pages->Build and deployment` の設定

    - Source: Deploy form a branch
    - Branch: gh-pages, /root
    
    `リポジトリ->Setting->Actions->General->workflow permissions`で`Read and write permissions`にチェック

    `./.github/workflows/deploy.yaml`を作成し、push
    ```yaml
    on:
      push:
        branches:
        - main
    jobs:
      deploy:
        runs-on: ubuntu-latest
        steps:
          - uses: actions/checkout@v2
          - uses: actions/setup-python@v2
            with:
              python-version: 3.9
          - name: setup mkdocs project
            if: ${{ success() }}
            env:
              REF: ${{ github.ref }}
            run: |
              export VER=`echo "${REF##*/}"`
              echo "version=${VER}" >> $GITHUB_ENV
              git remote set-url origin https://github-actions:${GITHUB_TOKEN}@github.com/${GITHUB_REPOSITORY}
              git config --local user.name "${GITHUB_ACTOR}"
              git config --local user.email "${GITHUB_ACTOR}@users.noreply.github.com"
              git fetch
          - run: pip install mkdocs-material pymdown-extensions
          - run: mkdocs gh-deploy --force
    ```

    github actionsのworkflowが終わると、ページにアクセスできるようになる

-----------------
# mkdosc column

## アラート修飾: markdown拡張機能

!!! note
    This is note
    
    * a
    * b

    | |a|b
    |:------|-----|-----
    |c|ac|bc
    |d|ad|bd

!!! abstract
    This is abstract

!!! info
    This is info

!!! tip
    This is tip

!!! success
    This is success, or check

!!! question
    This is question

!!! warning
    This is warning

!!! failure
    This is failure

!!! danger
    This is danger

!!! bug
    This is bug

!!! example
    This is example

!!! cite
    This is cite

### setting

```yaml
markdown_extensions:
    - admonition
    - pymdownx.superfences
```

## コンテンツの折りたたみ: markdown拡張機能

???+ note
    This is note
    
    ???+ note "nested note 1"
        This is nested note 1
    ???+ note "nested note 2"
        This is nested note 2

### setting

```yaml
markdown_extensions:
    - pymdownx.details
```

