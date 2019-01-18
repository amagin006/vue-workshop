# シンプルな家計簿アプリBudgetyを作ってみよう！
[前の章はこちら](./page6.md)

この章ではフォームから受け取ったデータを`main-content.vue`のStateに反映し、
合計やパーセントなどを計算していきます。

## 9. フォームのデータをStateに反映する。
メッソド`submitHandler()`にて`middle.vue`から受け取ったデータを良い感じにStateに反映していきます。  
まずは最も簡単と思われる`totalAmount`を計算していきましょう。

### 9-1. totalAmount,incomeTotal,expensesTotalを計算する -10min
これもそんなに難しい事はないので皆さんでやってみましょう。

#### やること
フォームから受け取ったデータを使って`totalAmount`を計算する。  
※ヒント: 収支タイプに気をつけてください。

#### 答え
ちょっと難しいなぁと思った方はこちらにサンプルコードを置いておきます。

<details><summary>答え<b>(まずはトライ！)</b></summary><div>

```javascript
// /src/components/middle.vue
submitHandler(formData) {
    if(formData.kind === 'inc') {
        this.totalAmount = this.totalAmount + formData.value
        this.incomeTotal = this.incomeTotal + formData.value
    } else {
        this.totalAmount = this.totalAmount - formData.value
        this.expensesTotal = this.expensesTotal + formData.value
    }
}
```

</div></details>
　  

### 9-2. バランスの表示を改善する。
これで各々の合計の計算が可能になり、更にそれをStateとして状態を保てるようになりました。  
ここでフォームに適当なデータを入力してみてください。  
`totalAmount`などの値が更新された事により、既にPropsにて紐付けられた`top.vue`側でその値を受け取り、  
Viewに即座に反映され表示されます。

しかしこのままでは桁数が増えてもカンマ区切りにならず若干見難かったりしますし、  
プラスの状態の時にも`+`の文字があった方が見た目的にもUX的にもベターでしょう。  

よし、じゃあメソッドを使って良い感じにしよう！と思ったそこのあなた、そうあなたです。  
ちょっと待った！惜しい！実に惜しい！  
確かにメソッドを通して値を加工しても良いのですが、Vueにはもっと便利な機能があるのでそちらを使いましょう！

### 9-2-1. Filter
`Filter`とは文字数を丸めたり、数字にカンマを入れると言ったテキストベースの変換処理に特化した機能です。  
`Filter`の登録には`filters`オプションに登録する事でそのコンポーネントの中でのみ使用できます。

```vue
<script>
export default {
    // 省略
    filters: {
        //filterメソッドの第一引数として対象のデータが渡されます。
        filterName(val) {
            // 大文字に変換してデータを返す
            return val.toUpperCase() 
        }
    }
}
</script>
```

> `filters`と複数形な事に気を付けてください。  
> `Filter`は制限として`this`にアクセスする事が出来ません。  
> グローバルなFilterも作れますが、今回は時間の都合上説明を割愛します。

#### 9-2-2. Filterの使い方
登録した`Filter`はマスタッシュまたは`v-bind`ディレクティブの値にパイプ`|`でフィルタ名をつなげる事で呼び出します。

```vue
<!-- example -->
<template>
    <div>
        <!--マスタッシュで使う場合-->
        {{ 対象のデータ | フィルタ名 }}
        <!--v-bindで使う場合-->
        <div :class="対象のデータ | フィルタ名"></div>
    </div>
</template>
```

さて、Filterの使い方も分かったところでバランスの値を改善しましょう。  
`expenses`のパーセンテージも静的なままなので、これを動的に表示出来るように合わせて処理を追加します。  
`top.vue`を以下の様に編集してください。

```vue
<template>
    <section class="top">
        <h1 class="budget__title">Available Budget in {{ getCurrentDate() }}:</h1>
        <p class="budget__total">{{ totalAmount | balanceFormatter }}</p>
        <div class="budget__income">
            <p class="budget__text">income</p>
            <p class="budget__value"> {{ incomeTotal | balanceFormatter }} </p>
            <p class="budget__percentage"></p>
        </div>
        <div class="budget__expenses">
            <p class="budget__text">expense</p>
            <p class="budget__value"> {{ expensesTotal | balanceFormatter }}</p>
            <p class="budget__percentage">{{ expPercentage }}%</p>
        </div>
    </section>
</template>

<script>
    import { format } from 'date-fns'
    export default {
        props: {
            totalAmount: Number,
            incomeTotal: Number,
            expensesTotal: Number
        },
         computed: {
             expPercentage() {
                 return (this.incomeTotal <= 0) ? 0 : Math.round((-this.expensesTotal / this.incomeTotal) * 100)
             }
         },
        methods: {
            getCurrentDate() {
                return format(new Date, 'MMMM, yyyy')
            }
        },
        // フィルタを登録する
        filters: {
            balanceFormatter(val) {
                const splitVal = Math.abs(val).toFixed(2).split('.')
                if (val < 0) {
                    return `- ${Number(splitVal[0]).toLocaleString()}.${splitVal[1]}`
                } else if (val === 0) {
                    return '0.00'
                } else {
                    return `+ ${Number(splitVal[0]).toLocaleString()}.${splitVal[1]}`
                }
            }
        }
    }
</script>
```

良い感じになりましたね！ 

しかし、また新しく`computed`オプションが出てきました。  
見た目はメソッドの様ですが何が違うのでしょうか？

### 9-3. Computed - 算出プロパティ
算出プロパティは任意の処理を含める事の出来るデータです。  
関数の様な見た目をしていますが、template上では`data`に登録されたState、つまりは変数と同じ様に扱えます。 
マスタッシュやディレクティブの値にJavascriptの式を直接使用する事も出来ますが、`template`内にやたらと式を書き込んでしまうと見通しが悪くなり可読性が悪化するだけでなく、保守性と言う視点からも好ましくありません。  
そこでこの算出プロパティに置き換える事でテンプレートの可読性を保つことが出来ます。

#### 9-3-1. Computedの使い方
`computed`オプションの使い方は至って簡単です。  
オプション配下にmethodsと同じ様に関数を登録してください。 
登録された関数は`return`で何かしらの値を返す事が必須となります。

```vue
<template>
    <div>
        <p>square: {{ square }}</p>
        <p>cube volume: {{ cube }}</p>
    </div>
</template>

<script>
export default {
    data() {
        width: 10
    },
    computed: {
        square() {
            return this.width * this.width
        },
        cube() {
            //computedプロパティはdataと同じ様にthisでアクセス出来ます。
            //他のcomputedプロパティからもmethodsプロパティからもアクセス出来ます。
            return this.square * this.width
        }
    }
}
</script>
```

#### 9-3-2. ComputedとMethodsの違い
ComputedとMethodsの大きな違いとして、Computedはリアクティブな依存データに基づき結果を**キャッシュ**します。  
キャッシュを再構築するトリガーになるのは、 ***リアクティブなデータのみ*** です。  
キャッシュ機能によってリアクティブな元データに変更があるまでは、何度処理をしても関数内の処理は一度しか行われません。  

最初は少し分かり辛いComputedかもしれませんが、扱いなれるととても使いやすい仕組みです。
Methodsは呼ばれた分だけ何度も処理を実行する為負荷が大きくなる事がありますが、Computedではそういった部分も避ける事が出来ます。

さて、それでは本題の方へと戻りましょう。  

### 9-4. 配列として収支データを保存する
`totalAmount`が計算が終わったら、今度は収入・支出をそれぞれ配列のデータとして保持出来るようにします。
これも特に難しい事はないのでサクッと下記の様に編集してください。

```vue
<script>
// 省略
export default {
    //省略
    methods: {
        // リファクタリングしました。
        submitHandler({ kind, desc, value }) {
            if(kind === 'inc') {
                this.totalAmount += value
                this.incomeTotal += value
            } else {
                this.totalAmount -= value
                this.expensesTotal -= value
            }
            this.allItem[kind].push({ desc, value })
            console.log(this.allItem)
        }
    }
}
</script>
```

ブラウザで確認すると

[前に戻る](./page6.md)　 [次に進む](./page8.md) 
