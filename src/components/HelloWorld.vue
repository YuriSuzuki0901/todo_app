<template>
  <div>
    <form>
      <button v-on:click="addTodo($event)">タスク追加</button>
      <!-- v-on ディレクティブを使うことでイベント発火時の JavaScript の実行が可能になる-->
      <button type="button" @click="removeTodo($event)">タスク削除</button>
      <p>input: <input type="text" v-model="newTodo"></p>
      <p>task: {{ newTodo }}</p>
    </form>
    <div class="task-list">
      <label class="task-list__item"
             v-for="(todo, key) in todos" :key="key">
        <input type="checkbox" v-model="todo.done">
        <input type="checkbox" v-model="todo.editing">
        <input v-if="todo.editing" v-model="todo.text" @keyup.enter="todo.editing = !todo.editing">
        <span v-else>{{ todo.text }}</span>
      </label>
    </div>

    <p>Done</p>
        <div class="task-list">
      <label class="task-list__item"
             v-for="(todo, key) in todosDone" :key="key">
        <span>{{ todo.text }}</span>
      </label>
    </div>

  </div>
</template>

<script>

const STORAGE_KEY = 'todos-vuejs-demo2' //varとは変数の宣言方法の一つ　再宣言、再代入が可能　constも変数宣言方法の一つだが再宣言、再代入が禁止
var todoStorage = {
  // 
  fetch: function () {
    // function関数とは、様々な処理を1つにまとめて、名前をつけることができるもの。動詞みたいなもの。rubyだとコントローラのdefでアクションを定義したのと同じ。コントローラのクラス内にdef~endで定義することで同じクラスのインスタンスオブジェクトに適応することができるやつ。引数は他動詞の目的語みたいなもの。
    //関数宣言や関数式といった定義の仕方があるがこれはどういう定義の仕方なのか
    var todos = JSON.parse(localStorage.getItem(STORAGE_KEY) || '[]') //JavaScriptでJSONデータを受け取った場合、プログラムで利用しやすい形式(オブジェクトデータ)に変換する必要があるからJSON.parseを行う。「JSON.parse(JSONデータ)とすることでオブジェクトデータに変換。そうすることによって使いやすいデータになる。
    todos.forEach(function (todo, index) { //配列.forEach( 処理 )のように配列データに対してforEachを実行する
      todo.id = index
    })
    todoStorage.uid = todos.length
    return todos
  },
  save: function (todos) { //saveは引数todosに対しての処理。それをここで定義している
    localStorage.setItem(STORAGE_KEY, JSON.stringify(todos)) //JSON.parseの逆。JSON.stringify( オブジェクトデータ )とすることでオブジェクトデータをjsonデータ化することができる。jsonデータでストレージに保存する。
  }
}

const STORAGE_DONE_KEY = 'todos-vuejs-demo_done' //varとは変数の宣言方法の一つ　再宣言、再代入が可能　constも変数宣言方法の一つだが再宣言、再代入が禁止
var todoDoneStorage = {
  fetch: function () {
    // function関数とは、様々な処理を1つにまとめて、名前をつけることができるもの。動詞みたいなもの。rubyだとコントローラのdefでアクションを定義したのと同じ。コントローラのクラス内にdef~endで定義することで同じクラスのインスタンスオブジェクトに適応することができるやつ。引数は他動詞の目的語みたいなもの。
    //関数宣言や関数式といった定義の仕方があるがこれはどういう定義の仕方なのか
    var todosDone = JSON.parse(localStorage.getItem(STORAGE_DONE_KEY) || '[]') //JavaScriptでJSONデータを受け取った場合、プログラムで利用しやすい形式(オブジェクトデータ)に変換する必要があるからJSON.parseを行う。「JSON.parse(JSONデータ)とすることでオブジェクトデータに変換。そうすることによって使いやすいデータになる。
    todosDone.forEach(function (todo, index) { //配列.forEach( 処理 )のように配列データに対してforEachを実行する
      todo.id = index
    })
    todoDoneStorage.uid = todosDone.length
    return todosDone
  },
  save: function (todosDone) { //saveは引数todosに対しての処理。それをここで定義している
    localStorage.setItem(STORAGE_DONE_KEY, JSON.stringify(todosDone)) //JSON.parseの逆。JSON.stringify( オブジェクトデータ )とすることでオブジェクトデータをjsonデータ化することができる。jsonデータでストレージに保存する。
  }
}

export default { //export defaultはコンポーネント化したい場合に使う。コンポーネントとは部品のこと。ほかの場所からも呼び出せるようになる。単一ファイル構成の場合はこれが使われる
  name: 'hello',
  data: function () {
    return {
      msg: 'Welcome to Your Vue.js App',
      todos : [],
      todosDone: [],
      newTodo: ""
    }
  },
  watch: { //watchとはデータの変化を監視して、関数を実行する
    // オプションを使う場合はオブジェクト形式にする
    todos: {
      // 引数はウォッチしているプロパティの変更後の値
      handler: function (todos) {
        todoStorage.save(todos)
      },
      // deep オプションでネストしているデータも監視できる
      deep: true  //配列の中身が変わった場合にも監視イベントを発生させる場合はtrue
    },
    todosDone: {
      handler: function (todosDone) {
        todoDoneStorage.save(todosDone)
      },
      deep: true  //配列の中身が変わった場合にも監視イベントを発生させる場合はtrue
    }

  },
  created() {
    // インスタンス作成時に自動的に fetch() する
    this.todos = todoStorage.fetch()
    this.todosDone = todoDoneStorage.fetch()
  },
  methods: {
    addTodo: function(event) {
      event.preventDefault();
      let text = this.newTodo && this.newTodo.trim(); //letは再宣言が禁止　変数宣言の役割
      if (!text) {
        return
      }
      this.todos.push({
        text: text,
        done: false,
        editing: false
      })
      this.newTodo = ''
    },
    removeTodo: function (event) {
      event.preventDefault();
      // for (let i = this.todos.length - 1; i >= 0; i--) {
      //   if (this.todos[i].done === true) {
      //     this.todos.splice(i, 1) //splice 既存の要素を取り除いたり、置き換えたり
      //   }
      // }
      // this.todos = this.todos.filter(function(todo) {
      //   return todo.done === false;
      // })

      let todos2 = []
      let todos3 = this.todosDone;
      this.todos.map(function(todo) {
        if (todo.done === false) {
          todos2.push(todo)
        };
        if (todo.done === true) {
          todos3.push(todo)
        };
      })
      this.todos = todos2
      this.todosDone = todos3

  }
  }
}
</script>

<style lang="scss" scoped>
@mixin flex-vender() {
  display: flex;
  display: -webkit-flex;
  display: -moz-flex;
  display: -ms-flex;
  display: -o-flex;
}
.task-list {
  @include flex-vender;
  flex-direction: column;
  align-items: center;
  &__item {
    width: 270px;
    text-align: left;
    $element: #{&};
    &--checked {
      @extend #{$element};
      color: #85a6c6;
    }
  }
}
</style>