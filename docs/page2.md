# シンプルな家計簿アプリBudgetyを作ってみよう！
[前の章はこちら](./index.md)
 

Chapter1でVue Cliを用いて作成したプロジェクトを各自愛用するエディタ、IDEで開きましょう。  
それじゃあ早速アプリ開発を...と、その前にプロジェクトの構造の確認と不要なファイルを削除し初期化しましょう。

> **小ネタ**   
> VSCodeユーザーはプロジェクトディレクトリに移動後、 `$ code .` のコマンドでターミナルから直接開けます。  
> Atom, WebStormなどのJetBrains製品でも同じ様な事が出来ます。  

## 2. プロジェクトの構造と初期化
Vue Cliによって作成されたプロジェクトの構造は下記の様な形になっています。  
srcフォルダには主にVueのコンポーネントなど、最終的にコンパイルされるファイルを追加していき、publicには画像や外部スタイルシートなど、そのまま公開される静的なファイルを追加していきます。

### 2-1. ディレクトリー構造の確認
```text
-- project-name
    -- src
        -- assets  
            -- logo.png
        -- components
            -- HelloWorld.vue
    -- public
        -- favicon.ico
        -- index.html
    -- App.vue
    -- main.js
    -- babel.config.js
    -- package.json
    -- README.md
    -- yarn.lock
```
### 2-2. 不要なサンプルファイルを削除する
src/components/にある `HelloWorld.vue` は不要なので削除してください。  

### 2-3. App.vueを修正する
`HelloWorld.vue`は別のコンポーネント`App.vue`から既に参照されている為、エラーが発生します。  
App.vueを開いてコードを下記の様に編集してください。合わせてcssを少し初期化します。
```vue
<!-- ./src/App.vue -->
<template>
  <div id="app"></div>
</template>

<script>
export default {
    name: 'app'
}
</script>

<style>
/* Initialize */
* {
    box-sizing: border-box;
}

html {
    font-size: 62.5%;
}

body {
    background: #f1f1f1;
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    margin: 0;
    padding: 0;
}

h1,h2,h3,h4,p,ul,li {
    margin: 0;
    padding: 0;
    list-style: none;
}

/*
#app{} は不要なので削除
*/
</style>
```

これでエラーが解消されました。  
一方ブラウザで開いたページは真っ白の何もない状態になっていると思います。  
これは初期状態ではApp.vueがHelloWorld.vueを読み込み表示していた物を削除し、修正後のコードはただの空Divのみを表示しているからです。

## 3. Single File Component
Vueでアプリケーションを作るうえで幾つか方法があるのですが、今回はいきなり **`Single File Component`** を用いて開発をしていきます。ちなみに日本語では **`単一ファイルコンポーネント`** と呼ばれます。
### 3-1. SFCとは？
**Single File Component**とはコンポーネントの定義方法の一つで、コンポーネントの作成をより快適にする為の仕組みです。 コンポーネントの数が多くなりテンプレートも大きくなると一つのファイルだけでは管理しきれません。SFCはコンポーネントを構成するHTML・CSS・Javascriptを `.vue`と言う拡張子の付いた一つのファイルにまとめて管理します。各コンポーネントの情報がSFCを見れば一目瞭然なので、ファイルの行き来をする煩わしさからも開放されます。

SFCは以下のような構造をしています。
```vue
<template>
    <div class="example">
        <h1 class="example__title"> {{ title }} </h1>
        <p> This is example page. </p>
    <div>
</template>

<script>
    export default {
        name: 'example',
        deta() {
            return {
                title: 'Example'
            }
        }
    }
</script>

<style scoped>
    .example {
        border: solid 1px #ccc
        padding: 12px 24px;
    }
    .example__title {
        color: #444;
        font-size: 2rem;
    }
</style>
```

SFCの各項目については追々解説するので、今はこんな感じなんだなー程度に眺めておいてください。  
では次章から改めてアプリを実際に作り始めましょう。  

[前に戻る](./index.md)　 [次に進む](./page3.md) 