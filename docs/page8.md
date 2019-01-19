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
  
- 上記のテスト表示が確認できたら先程`</ul>`( expensesのul )の閉じタグの上に追記したコードを削除してください。

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
                return (kind === 'exp') ? -val : val
            }
        }
    }
</script>
<!-- 省略 -->
```


### 10-3. 繰り返し
さて、最後の仕上げにかかります。  
`main-content.vue`から受け取る収支データは、収支双方のデータを**配列**として保持しています。  
せっかくデータを配列で持っているなら通常のプログラムの様に`for`や`while`を使いループ処理をしたいところです。  
もちろんVueにもテンプレート内でタグを繰り返し描画する為の機能があります。それが ***`v-for`*** ディレクティブです。  
`list-item.vue`をコンポーネント化した最大の理由がここにあり、  
共通して使い回せる形にする事により繰り返し処理を行いやすくする為だったのです。

#### 10-3-1. v-for
`v-for`の使い方は以下の通りです。配列だけでなくObjectを扱う事も可能です。  
また値を取り出すだけでなく、indexの取得やObjectであれば`key value`の取得も可能です。

```vue
<template>
    <ul>
        <li v-for="item in itemList" :key="item.id"> {{ item.name }} </li>
        
        <!-- indexを取得する事も可能 -->
        <li v-for="(item, index) in itemList" :key="item.id"> {{ item.name }} </li>
        
        <!-- itemListがObjectであればKey Valueも取得出来る -->
        <li v-for="(item, key, index) in itemList" :key="item.id"> {{ item.name }} </li>
    </ul>
</template>
```

#### 10-3-2. キーの役割
サンプルコードにさり気なく混じっている`key`属性は`v-for`を扱う上で重要なポイントです。  
`key`属性は要素の識別と効率的な描画処理をさせるため、各要素に ***ユニークな`key`属性*** を設定する事が強く推奨されています。  

キーがない場合、Vueは要素の移動を最小限に抑えるアルゴリズムを使用し、可能な限りその場で同じタイプの要素にパッチ適用/再利用しようとします。キーがある場合は、キーの順序の変化に基づいて要素を並べ替え、そして、もはや存在しないキーを持つ要素は常に削除/破棄されます。  

それでは早速`v-for`を用いて`list-item`を配列に含まれたデータの数だけ描画しましょう。  
あわせて`10-2`で変更を加えた`item-list.vue`にPropsでデータを受け渡します。   

`bottom.vue`を以下の様に編集してください。

```vue
<!-- /src/component/bottom.vue -->
<template>
    <section class="bottom">
        <div class="list">
            <h2 class="list__title list__title--inc">income</h2>
            <ul class="list__main inc">
                <list-item
                    v-for="(data, index) in allItem.inc"
                    kind="inc"
                    :item-data="data"
                    :percentage="calcPercentage(data.value, 'inc')"
                    :key="`inc${index}`"
                />
            </ul>
        </div>
        <div class="list">
            <h2 class="list__title list__title--exp">expenses</h2>
            <ul class="list__main exp">
                <list-item
                    v-for="(data, index) in allItem.exp"
                    kind="exp"
                    :item-data="data"
                    :percentage="calcPercentage(data.value, 'exp')"
                    :key="`exp${index}`"
                />
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
            // 個別の収支の比率を計算する。
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

おめでとうございます！これで全ての作業が完了しました！お疲れ様でした！  
さっそくブラウザでBudgetyを使ってみましょう！  

無事に動作していますか？  
何か挙動がおかしい人は、どこかが間違っているかもしれません。  
もう一度コードを見直してみましょう。

### 10-4. （おまけ）条件分岐
今回は出番はなかったのですが、皆さん`for`が`v-for`として存在するなら`if`も...と思いますよね？  
はい、もちろん`v-if`が存在します。  
次の例は`conditions`の値が`true`だった時のみ`<div>`要素を表示します。  

```vue
<template>
    <div v-if="conditions">conditionsがtrueなら描画される。</div>
</template>
```

`false`の場合、要素はDOMレベルで削除されすべての監視は解除されます。  
コンポーネントであればインスタンスは破棄され、次に描画される時には初期化されています。  
内側にディレクティブやコンポーネントを多様していたり、  
特定のデータを持っていないとエラーが起きる場合には`v-if`を使用すると良いでしょう。  

[前に戻る](./page7.md)　 [次に進む](./page9.md) 
