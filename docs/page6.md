# シンプルな家計簿アプリBudgetyを作ってみよう！
[前の章はこちら](./page5.md)

前章で`top.vue`にデータを受け渡す事が出来たので、今度はフォーム部分を進めていきます。  

## 8. Event UpとEmit
前回Propsの説明した時に、親から子へPropsを通じてデータを受け渡す事をProps Downと呼ぶ事を学びました。  
同じ様に子から親へデータを受け渡す、親のmethodsなどを利用する方法があり、その事を ***Event Up*** と呼びます。  

### 8-1. 子から親へ
まずは`main-content.vue`に新しくState、Mothodsを追加していきましょう。

```vue
<template>
    <div class="mainContent">
        <the-header />
        <top
            :total-amount="totalAmount"
            :income-total="incomeTotal"
            :expenses-total="expensesTotal"
        />
        <middle v-on:submit-handler="submitHandler" />
        <bottom />
    </div>
</template>

<script>
// 省略
export default {
    // 省略
    data() {
        return {
            totalAmount: 0,
            incomeTotal: 0,
            expensesTotal: 0,
            allItem: {
                inc: [],
                exp: []
            }
        }
    },
    methods: {
        submitHandler(formData) {
            console.log(formData)
        }
    }
}
</script>
```

#### 8-1-1. v-on
子コンポーネントのカスタムタグを記述するとき、`v-on`ディレクティブで子のイベントをハンドルしておきます。  
`main-content.vue`では`middle.vue`に`submit-handler`と言う名前のイベントをハンドルしました。  
これで子コンポーネントから`submit-handler`を呼び出せば、`submit-handler`に紐付いている親のメソッド`submitHandler()`を発火させる事が出来ます。

また親子のイベントを関連付ける以外にも、javascriptの`addEventListener()`やjQueryの`on()`の様にクリックイベントやマウスオーバーのイベントを要素に付与する際にも使います。イベントの詳細は[公式ドキュメント](https://jp.vuejs.org/v2/api/#v-on)を参照してください。

```vue
<button @click="submitButtonEvent">submit</button>
```

> `v-on`は`@`で省略する事が出来ます。
> ```vue
> <child-component v-on:child-event="parentEvent">
> <child-component @child-event="parentEvent">
> ```

同じく子にあたる`middle.vue`も編集していきます。
```vue
<template>
    <section class="middle">
        <div class="form">
            <select class="form__kind" name="kind" v-model="inputKind">
                <option value="inc">inc</option>
                <option value="exp">exp</option>
            </select>
            <div class="form__desc">
                <input type="text" placeholder="Income or expenses description" v-model="inputDesc">
            </div>
            <div class="form__value">
                <input type="number" min="1" step="0.01" placeholder="100" v-model="inputValue">
            </div>
            <div class="form__submit">
                <button v-on:click="clickHandler">submit</button>
            </div>
        </div>
    </section>
</template>

<script>
    export default {
        data() {
            return {
                inputKind: 'inc',
                inputDesc: '',
                inputValue: 0
            }
        },
        methods: {
            clickHandler() {
                  // middle.vueのStateを親に渡しやすい様にオブジェクトにまとめる
                const formData = {
                    kind: this.inputKind,
                    desc: this.inputDesc,
                    value: this.inputValue
                }
                
                // 親から受け取ったイベントを$emitで発火。
                // と同時にformDataを親に返す。
                this.$emit('submit-handler', formData)
            }
        }
    }
</script>

<!-- 省略 -->
```

#### 8-1-2. $emit
子コンポーネントは適当なタイミングで(殆どの場合methodsの中で実行されます。) `$emit` を使って親から渡されたイベント（Budgetyの場合は`submit-handler`）を実行します。  
この時に２つ目以降の引数を任意にデータを持たせ親側に渡すことも出来ます。

```vue
<!-- 子が親から受け取ったイベントを発火 -->
<template>
    <button @click="clickHandler"></button>
</template>

<script>
export default {
    methods: {
        clickHandler() {
            // このthisはvueのインスタンスを指します。
            // 第一引数は、親で子コンポーネントタグに指定したv-onのプロパティ名
            this.$emit('parent-event', { message: 'from child' })
        }
    }
}
</script>
```
```vue
<!-- 親が子のイベントを検知して登録したハンドラが呼び出される-->
<template>
    <child-component @parent-event="clickEvent" />
</template>

<script>
export default {
    //省略
    methods: {
        // 子から渡されたデータは引数で受け取ります。
        clickEvent(message) {
            alert(message)
        }
    }
}
</script>
```

#### 8-1-2. v-model - 双方向データバインディング 
フォームに入力された値と`data`プロパティに指定した変数を結びつけます。  
v-modelはフォームの`valueプロパティ`にバインドされます。  
つまり、data
　  
> ##### チョイ足しポイント
> 今のままでは毎回Submitボタンをマウスで押さないといけません。  
> これをEnterキーを押してもSubmitが出来るようにしてください。

### 8-2. ２つの問題の解消 - 10min
ここで一度、先ほど記述したコードをブラウザで確認してみましょう。  
フォームに適当に入力をし、Submitボタンを押してみてください。  
画面上には何も表示されませんが、WebインスペクタやDevToolを開くとコンソール上にオブジェクトとして  
表示されているはずです。

```javascript
{kind: "inc", desc: "test description", value: "100"}
```

さて、実際に実行してみて２箇所ほど気になる部分がありました。  
みなさんお気付きになられましたか？
　  
　  
　  
　  
　  
　  
　  
　  
　  
はい、そうですね。 `value`の値が文字列になっています。
もう一つは分かりますか？  

これはsubmitボタンを押した後も入力したデータがそのままになっている事です。  
今回はこれらを修正します。と言っても何ら難しい事はありません。  

という事でみなさん自身の手で修正してみましょう！  

#### やる事
親が受け取るデータのうち、valueが文字列なので数値にする。  
submitボタンを押した後フォームをクリアする。  

#### 制限事項
今回は全て`middle.vue`の中で完結させてください。  

#### 答え
ちょっと難しいなぁと思った方はこちらにサンプルコードを置いておきます。

<details><summary>答え<b>(まずはトライ！)</b></summary><div>

```vue
<!-- /src/components/middle.vue -->
<script>
    export default {
        data() {
            return {
                inputKind: 'inc',
                inputDesc: '',
                inputValue: 0
            }
        },
        methods: {
            clickHandler() {
                // middle.vueのStateを扱いやすい様にオブジェクトにまとめる
                const formData = {
                    kind: this.inputKind,
                    desc: this.inputDesc,
                    value: Number(this.inputValue)
                }
                // Stateを初期化
                this.inputKind = 'inc'
                this.inputDesc = ''
                this.inputValue = 0
                
                // 親から受け取ったイベントを$emitで発火。
                // と同時にformDataを親に返す。
                this.$emit('submit-handler', formData)
            }
        }
    }
</script>
```

</div></details>
　  

出来ましたか？  
これでちょっとした問題も解決したので次に進みます。

[前に戻る](./page5.md)　 [次に進む](./page7.md) 

