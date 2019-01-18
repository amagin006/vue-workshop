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
- `main-content.vue`から`allItem`を`bottom.vue`に受け渡す。変数名は同じく`allItem`にしてください。  
- `bottom.vue`の最後の`</ul>`( expensesのul )の閉じタグの下に下記のコードを挿入してください。
  
  ```vue
      <!-- 時間の都合上このコードの説明は割愛します。 -->
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

[前に戻る](./page7.md)　 [次に進む](./page9.md) 
