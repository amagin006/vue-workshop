# Vue.js ワークショップ
Vue.js初心者向けのワークショップです。  
初めて触る人も、ちょっとだけ触ったことがある人も一緒にマッタリとやっていきましょう。  
多少の経験があり、物足りない人には追加の要素もあります。  
（ワークショップでの解説はしません。）

## 0. 今日はやらないこと
本日は初心者向けと言う事もあり、Vue RouterやVuexの解説は行いません。  
また講師は普段、Typescriptを用いて開発を行っていますが、今回もその辺りは触れません。 

## 1. 始める前に - 環境の構築
実際にVueを使い始める前にプロジェクトを作成し開発環境を用意しましょう。  
PCには既にNode.jsが勿論インストールされていますよね？  
念の為チェックしておきましょう。
```
$ node -v
v10.15.0
```
この様に表示されればNodeは既にインストールされています。

次にVueのプロジェクトを色々とよしなにしてくれるVue Cliを用いてプロジェクトを作っていきます。
まずはVue Cli v3をインストールしましょう。

#### npm
```bash
$ npm install -g @vue/cli
```
#### yarn
```bash
$ yarn global add @vue/cli
```
     
### 1-2. Vue Cliを使ってプロジェクトを作成する。
インストールが終わったらvue cliを用いてプロジェクトを作成します。  
実行すると色々と聞かれますが今日はdefaultを使って進めます。
```bash
$ vue create project-nam
? Please pick a preset: (Use arrow keys)
> default (babel, eslint)
  Manually select features
```
　  
インストールが終わったら、早速エディタやIDEでプロジェクトを開いて始めましょう！
```bash
$ cd project-name

// VSCodeを使ってる人は以下のコマンドが使えます。
$ code .
```
WebStormなどのJetBrainsのIDEでも設定で同じような事が出来ます。  
Atomでもプラグイン？エクステンション？を入れれば可能みたいです。
　  
　  
> ##### チョイ足しポイント  
> Manually select featuresでカスタムしてプロジェクトを作ってみましょう。  
> その際にcss pre-processor(sassなど）や、vuexをインストールしてみましょう。

　  
それでは実際にVueを使ってアプリケーションを作っていきましょう。  
Let's get started!!  
　  
[次に進む](https://github.com/)
