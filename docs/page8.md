# シンプルな家計簿アプリBudgetyを作ってみよう！
[前の章はこちら](./page7.md)

Bugdetyの作業もいよいよ大詰めになってきました。  
残り最後の収支リスト部分の作業に入ります。

## 10. 収支リストを仕上げる
まずは`top.vue`と同じ様に`main-content.vue`から`bottom.vue`データを受け渡しましょう。  
どうやるか覚えてますか？

### 10-1. データの受け渡し - 10min
ここは復習のために皆さんでやってみましょう！

#### やる事
- `main-content.vue`から`incomeTotal`,`expensesTotal`を受け渡す。
- `main-content.vue`から`allItem`を`bottom.vue`に受け渡す。変数名は同じく`allItem`にしてください。  
- `bottom.vue`の最後の`</ul>`( expensesのul )の閉じタグの下に下記のコードを挿入してください。
  
  ```vue
      <div v-if="allItem.inc.length" style="font-size: 2rem;">
          {{ allItem.inc[0].desc }}: {{ allItem.inc[0].value }}
      </div>
  ```

- 上記２つが終わったら、フォームからSubmitしてみてください。以下の様な表示が出れば成功です。  
  ```text
  description: 100
  ```

#### 答え
ちょっと難しいなぁと思った方はこちらにサンプルコードを置いておきます。  

<details><summary>答え<b>(まずはトライ！)</b></summary><div>

```vue
<!-- /src/components/main-content.vue -->
<template>
    <!-- 省略 -->
    <bottom :all-item="allItem" />
</template>
<!-- 省略 -->
```

```vue
<!-- /src/components/bottom.vue -->
<template>
    <section class="bottom">
        <div class="list">
            <h2 class="list__title list__title--inc">income</h2>
            <ul class="list__main">
                <list-item />
                <list-item />
            </ul>
        </div>
        <div class="list">
            <h2 class="list__title list__title--exp">expenses</h2>
            <ul class="list__main">
                <list-item />
                <list-item />
                <list-item />
                <list-item />
            </ul>
            <div v-if="allItem.inc.length" style="font-size: 1.6rem;">
                {{ allItem.inc[0].desc }}: {{ allItem.inc[0].value }}
            </div>
        </div>
    </section>
</template>

<script>
    import ListItem from './list-item.vue'
    export default {
        components: {
            ListItem
        }
    }
</script>
<!-- 省略 -->
```

</div></details>
　  

フォームから入力したディスクリプションと値が、expensesのリスト最下部に表示されましたか？  
確認できたら次に進みます。

### 10-2. 更にデータを受け渡す
しかしながら、本来リストの描画をするのは`list-item.vue`の役割であって、`bottom.vue`は動的に追加されるリストをまとめているに過ぎません。そこで更に`list-item.vue`にデータを受け渡します。

`item-list.vue`を次のように編集してください。

```vue
<!-- /src/components/list-item.vue -->
<template>
    <li class="listItem">
        <p class="listItem__title">{{ itemData.desc | balanceFormatter }}</p>
        <!-- この様にFilterはつなげる事も出来ます
             また、引数を渡すこともできます。      -->
        <p class="listItem__value">{{ itemData.value | toNegative(kind) | balanceFormatter }}</p>
        <p class="listItem__percentage">{{ percentage | balanceFormatter }}</p>
    </li>
</template>

<script>
    export default {
        props: {
            itemData: Object,
            percentage: Number,
            kind: String
        },
        filters: {
            // テンプレートから引数を受ける場合、第２引数以降で受けます
            toNegative(val, kind) {
                if (kind === 'exp') {
                    return -val
                } else {
                    return val
                }
            }
        }
    }
</script>
<!-- 省略 -->
```

続いて`bottom.vue`を編集します。
```vue
<template>
    <section class="bottom">
        <div class="list">
            <h2 class="list__title list__title--inc">income</h2>
            <ul v-if="allItem.inc.length" class="list__main inc">
                <template v-for="data in allItem.inc">
                    <list-item
                        kind="inc"
                        :item-data="data"
                        :percentage="calcPercentage(data.value, 'inc')"
                        :key="data.desc"
                    />
                </template>
            </ul>
        </div>
        <div class="list">
            <h2 class="list__title list__title--exp">expenses</h2>
            <ul v-if="allItem.exp.length" class="list__main exp">
                <template v-for="data in allItem.exp">
                    <list-item
                        kind="exp"
                        :item-data="data"
                        :percentage="calcPercentage(data.value, 'exp')"
                        :key="data.desc"
                    />
                </template>
            </ul>
        </div>
    </section>
</template>

<script>
    import ListItem from './list-item.vue'
    export default {
        components: {
            ListItem
        },
        props: {
            incomeTotal: Number,
            expensesTotal: Number,
            allItem: Object
        },
        methods: {
            calcPercentage(val, kind) {
                if(kind === 'inc') {
                    return (this.incomeTotal <= 0) ? 0 : Math.round((val / this.incomeTotal) * 100)
                } else {
                    return Math.round((-val / this.expensesTotal) * 100)
                }
            }
        }
    }
</script>
<!-- 省略 -->
```

[前に戻る](./page7.md)　 [次に進む](./page9.md) 
