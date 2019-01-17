# シンプルな家計簿アプリBudgetyを作ってみよう！
[前の章はこちら](./page5.md)

前章で`top.vue`にデータを受け渡す事が出来たので、今度はフォーム部分を進めていきます。  

## 8. Event UpとEmit
前回Propsの説明した時に、親から子へPropsを通じてデータを受け渡す事をProps Downと呼ぶ事を学びました。  
同じ様に子から親へデータを受け渡す、親のmethodsなどを利用する方法があり、その事を ***Event Up*** と呼びます。  

### 8-1. 子から親へ
まずは`main-content.vue`に新しくState、Mothodsを追加していきましょう。

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

<script>
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
                <button @click="clickHandler">submit</button>
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
                // middle.vueのStateを扱いやすい様にオブジェクトにまとめる
                const formData = {
                    kind: this.inputKind,
                    desc: this.inputDesc,
                    value: this.inputValue
                }
                this.$emit('submit-handler', formData)
            }
        }
    }
</script>

<!-- 省略 -->
```

#### 8-1-1. v-on
子コンポーネントのカスタムタグを記述するとき、`v-on`ディレクティブで子のイベントをハンドルしておきます。  
`main-content.vue`では`middle.vue`に`submit-handler`と言う名前のイベントをハンドルしました。  
これで子コンポーネントから`submit-handler`を呼び出せば、`submit-handler`に紐付いている親のメソッド`submitHandler()`を発火させる事が出来ます。

> `v-on`は`@`で省略する事が出来ます。
> ```vue
> <child-component v-on:child-event="parentEvent">
> <child-component @child-event="parentEvent">
> ```

#### 8-1-2. $emit



[前に戻る](./page5.md)　 [次に進む](./page7.md) 