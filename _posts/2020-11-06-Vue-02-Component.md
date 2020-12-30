---
layout: post
title: "Vue 02 Component"
date: 2020-11-06
categories: [Vue]
---

##  Vue.js 102-01 Environment Setting
A note taking from [Vue.jsコンポーネント入門 (1) 環境設定](https://www.hypertextcandy.com/vuejs-components-introduction-environment-setting)

Vue.js Component Develop:
- Vue.Component
- Single File Component (SFC) 単一ファイルコンポーネント

Tool List:
- Node and npm: package management
- Webpack: module bundler モジュールバンドル
- Babel: transpiler (ECMAScript to JavaScript) トランスパイル
- [vue-loader](https://vue-loader.vuejs.org/): a loader for webpack to auther SFC Vue components
- [vue-cli](https://cli.vuejs.org/guide/): scafflod tool for vue project (no need to set up webpack manually)


- [Vue Components Starter Kit](https://github.com/MasahiroHarada/vue-components-starter-kit): source code of the tutorial for practice
- [How to setup a vue project with webpack](https://dev.to/lavikara/setup-vue-webpack-and-babel-boo): hand to hand tutorial to set up the environment manually

### Structure for a New Project
```
- package.json
- package-lock.json
- public
  -- index.html
- src
  -- components
  -- index.js
- webpack.config.js
```

### New a component: Hello
```vue
# components/Hello.vue

<template>
  <p>Hello, {% raw %}{{ name }}{% endraw %}</p>
</template>

<style scoped>
h1 {
  color: teal;
}
</style>

<script>
export default {
  data () {
    return {
      name: 'world'
    }
  }
}
</script>
```

import `Hello.vue` to the index.js
```javascript
# src/index.js

import Vue from "vue"
import Hello from "./components/Hello.vue"

new Vue({
    el: "#app",
    components: {
        hello: Hello
    }
})
```

Use the `Hello component` as HTML tag
```html
# index.html

<!DOCTYPE html>
<html lang="ja">
    <head>
        <meta charset="UTF-8">
        <title>Vue Component</title>
    </head>
    <body>
        <div id="app">
            <hello></hello>
        </div>
        <script src="/bundle.js"></script>
    </body>
</html>
```

#### npm commands
- `npm run build`: run webpack build
- `npm run serve`: start the local server at http://localhost:9000
- `npm run start`: build and run server

###  Vue.js 102-02 Vue Component
A note taking from [Vue.jsコンポーネント入門 (2) コンポーネントとは何か？](https://www.hypertextcandy.com/vuejs-components-introduction-component-explained)

Components are reusable page parts (部品化する)

#### Example: Hello world
```vue
# Hello.vue

<template>
    <p>Hello, {% raw %}{{ name }}{% endraw %}</p>
</template>

<script>
export default {
    props: ["name"]
}
</script>
```
---
```html
<hello name="world"></hello>
```

#### Example: AnchorLink
```vue
# Anchor.vue

<template>
    <span class="anchor" @click="onClick">
        <slot></slot>
    </span>
</template>

<script>
export default {
    props: ["href"],
    methods: {
        onClick () {
            location.href = this.href
        }
    }
}
</script>

<style scoped>
.anchor {
    color: blue;
    text-decoration: underline;
}
</style>
```
---
```html
<anchor href="https://qiita.com">Qiita</anchor>
```

Vue file:
- `<template>`: defines the HTML part
- `<script>`: defines the JavaScript part
- `<style>`: defines the CSS part

FYR: [Vue.js コンポーネントの基本](https://jp.vuejs.org/v2/guide/components.html)

###  Vue.js 102-03 Props
A note taking from [Vue.jsコンポーネント入門 (3) propsによるデータの受け渡し](https://www.hypertextcandy.com/vuejs-components-introduction-props)

Props(その要素の属性) are used to pass the data to components and define the accessible prop list for the component

#### Example: AnchorLink
```vue
# src/components/AnchorLink.vue

<template>
    <span class="anchor" @click="onClickLink">{% raw %}{{ text }}{% endraw %}</span>
</template>

<script>
export default {
    props: {
        text: String, // key: prop name, value: data type
        href: String
    },
    methods: {
        onClickLink () {
            window.location.href = this.href
        }
    }
}
</script>

<style scoped>
.anchor {
    color: blue;
    cursor: pointer;
    text-decoration: underline;
}
</style>
```
---
```javascript
# src/index.js

import Vue from "vue"
import AnchorLink from "./components/AnchorLink.vue"

new Vue({
    el: "#app",
    components: {
        AnchorLink // ES2015: no need to assign value 
                   // with the same name of component
                   // キー（タグ名）と値が同名（AnchorLink）なので、ES2015 の省略記法を用いました。
    }
})
```
---
```html
# index.html

<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>Vue Component</title>
</head>
<body>
    <div id="app">
        <anchor-link 
         href="https://qiita.com"
         text="Qiita"
        ></anchor-link>
    </div>
    <script src="./dist/main.js"></script>
</body>
```

FYR: [Vue.js スタイルガイド](https://jp.vuejs.org/v2/style-guide/)

Add more props to the component
```vue
# src/components/AnchorLink.vue

<template>
    <span class="anchor" @click="onClickLink">{% raw %}{{ text }}{% endraw %}</span>
</template>

<script>
export default {
    props: {
        text: {
            type: String,
            required: true
        },
        href: {
            type: String,
            required: true
        },
        newTab: {
            type: Boolean,
            default: false
        }
    },
    methods: {
        onClickLink () {
            if (this.newTab === true) {
                window.open(this.href, "_blank")
            }
            else {
                window.location.href = this.href
            }
        }
    }
}
</script>

<style scoped>
.anchor {
    color: blue;
    cursor: pointer;
    text-decoration: underline;
}
</style>
```
---
```html
# index.html

<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>Vue Component</title>
</head>
<body>
    <div id="app">
        <anchor-link 
         href="https://qiita.com"
         text="Qiita"
         :new-tab="true"
        ></anchor-link>
        <!--
            when the property value is not string, add colon ":" before the property name
            属性値を単なる文字列ではなく JavaScript の値として渡したい場合は属性名の頭にコロン : を付加します。
            "true" という文字列を渡したいのではなく、true という JavaScript の真偽値を渡したいため、
            属性名の頭にコロンを付加して :new-tab と記述します。
        -->
    </div>
    <script src="./dist/main.js"></script>
</body>
```

###  Vue.js 102-04 Events Emit
A note taking from [Vue.jsコンポーネント入門 (4) $emitによるイベントの発行](https://www.hypertextcandy.com/vuejs-components-introduction-emit-events)

event handler:
- `v-on:event-name` (`@event-name`): DOM events eg. click, submit
- `$emit`: customized events and could work with default DOM events

#### Example: Event Buttons
```vue
# src/components/EventButtons.vue

<template>
    <div>
        <button @click="emitEventOne">Event 1</button>
        <button @click="emitEventTwo">Event 2</button>
        <button @click="emitEventThree">Event 3</button>
    </div>
</template>

<script>
export default {
    methods: {
        // emit events イベントを発行する
        emitEventOne() {
            this.$emit("event-one")
            // $emit は Vue インスタンスに定義されたメソッドで、ひとつ以上の引数を持ちます。
            // 1つめの引数は、イベントの名前です。
            // 2つめ以降の引数は、イベントハンドラに渡される引数です。
        },
        emitEventTwo() {
            this.$emit("event-two", "This is an argument")
        },
        emitEventThree() {
            this.$emit("event-three", 123, { name: "three" })
        }
    }
}
</script>
```
---
```javascript
import Vue from "vue"
import EventButtons from "./components/EventButtons.vue"

const  = new Vue({
    el: "#app",
    data: {
        arguments: []
    },
    components: {
        EventButtons
    },
    methods: {
        // event handlers
        onEventOne() {
            alert("Event 1")
        },
        onEventTwo(arg) {
            this.arguments = []
            this.arguments.push(argument)
        },
        onEventThree(arg1, arg2) {
            this.arguments = []
            this.arguments.push(arg1)
            this.arguments.push(arg2)
        }
    }
})
```

!Notice: for reusable components, data should be a closure function

> コンポーネントの data オプションは関数でなければなりません。各インスタンスが返されるデータオブジェクトの独立したコピーを保持できるためです。<br>
> FYR: [コンポーネントの再利用](https://jp.vuejs.org/v2/guide/components.html#%E3%82%B3%E3%83%B3%E3%83%9D%E3%83%BC%E3%83%8D%E3%83%B3%E3%83%88%E3%81%AE%E5%86%8D%E5%88%A9%E7%94%A8)

```html
# index.html

<!DOCTYPE html>
<html lang="ja">
    <head>
        <meta charset="UTF-8">
        <title>Vue Component</title>
    </head>
    <body>
        <div id="app">
            <!-- 
                events subscribe イベントを購読する
                <component @イベント名="イベントハンドラ名"></component>
            -->
            <event-buttons
             @event-one="onEventOne"
             @event-two="onEventTwo"
             @event-three="onEventThree"
            ></event-buttons>
            <pre v-for="argument in arguments">{% raw %}{{ argument }}{% endraw %}</pre>
        </div>
        <script src="./dist/main.js"></script>
    </body>
</html>
```

### Event Emit / Subscribe / Handle: event-one
```javascript
// Vue Component イベント発行
this.$emit('event-one')

// HTML Component イベント購読
@event-one="onEventOne"

// index.js: Vue App methods イベントハンドラー
onEventOne () {
  alert('Event 1')
}
```

FYR: [A Vue Event Handling Cheatsheet – The Essentials](https://learnvue.co/2020/01/a-vue-event-handling-cheatsheet-the-essentials/)

#### Example: Guess Number 数当てゲーム

- start button: initialize a game
- input: receive guessing number
- result: right guess or not
- hint: guessing is greater or smaller than the answer
- remain trying times: how many times of guessing can be tried

##### Event Emit:
```vue
# src/components/NumberGuess.vue

<template>
    <div>
        <p>
            <button @click="start">start</button>
        </p>
        <div v-if="answer > 0">
            <input
                type="number"
                v-model.number="num"
                @keyup.enter="guess"
            />
        </div>
    </div>
</template>

<script>
export default {
    data() {
        return {
            answer: 0,
            num: 0,
            tryCountLeft: 10
        }
        // data プロパティの値がオブジェクトではなく、オブジェクトを返す関数になっています。
        // The "data" option should be a function that returns a per-instance value
        // data set should be only accessible for the specific component instance.
    },
    methods: {
        start() {
            this.answer = Math.floor(Math.random() * 99 + 1) // get a random integer from 1-99
            this.num = 0
            this.tryCountLeft = 10

            this.$emit("start", this.tryCountLeft)
        },
        guess() {
            this.tryCountLeft -= 1

            if (this.answer === this.num) {
                // Guessing right, win
                this.$emit("collect", this.tryCountLeft)
                return
            }

            if (this.tryCountLeft === 0) {
                // Run out of try times, lose
                this.$emit("lose", this.tryCountLeft)
            }
            else if (this.answer < this.num) {
                // Guessing too large
                this.$emit("lower", this.tryCountLeft)
            }
            else {
                // Guessing too small
                this.$emit("higher", this.tryCountLeft)
            }
        }
    }
}
</script>
```
---
```javascript
Math.floor(Math.random() * (max - min) + min); 
//The maximum is exclusive and the minimum is inclusive
```

FYR: [2 つの値の間のランダムな整数を得る](https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Global_Objects/Math/random)

##### Event Subscribe:
```html
#index.html

<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>Vue Component Tutorial</title>
</head>
<body>
    <div id="app">
        <h1>数当てゲーム</h1>
        <number-guess
         @start="onStart"
         @collect="onCollect"
         @lower="onLower"
         @higher="onHigher"
         @lose="onLose"
        ></number-guess>
        <p>{% raw %}{{ message }}{% endraw %}</p>
        <p>残り：{% raw %}{{ tryCountLeft }}{% endraw %}回</p>
    </div>
    <script src="./dist/main.js"></script>
</body>
</html>
```

##### Event Handle:
```javascript
# src/index.js

import Vue from "vue"
import NumberGuess from "./components/NumberGuess.vue"

const app = new Vue({
    el: "#app",
    data: {
        message: "スタートボタンを押してください",
        tryCountLeft: 0
    },
    components: {
        NumberGuess
    },
    methods: {
        onStart (tryCountLeft) {
            this.message = "スタート！"
            this.tryCountLeft = tryCountLeft
        },
        onCollect (tryCountLeft) {
            this.message = "当たり！"
            this.tryCountLeft = tryCountLeft
        },
        onLower (tryCountLeft) {
            this.message = "もっと小さいです"
            this.tryCountLeft = tryCountLeft
        },
        onHigher (tryCountLeft) {
            this.message = "もっと大きいです️"
            this.tryCountLeft = tryCountLeft
        },
        onLose () {
            this.message = 'ゲームオーバー'
            this.tryCountLeft = tryCountLeft
        }
  }
})
```

### Vue.js 102-05 Communications Between Components
A note taking from [Vue.jsコンポーネント入門 (5) コンポーネント間のコミュニケーション](https://www.hypertextcandy.com/vuejs-components-introduction-communication-between-components)

Component Tree: like DOM Tree, the relationship between components could be drawn as Component Tree

疎結合(そけつごう)(loose coupling)なコンポーネントが望ましい:
- 再利用のしやすさ
- 保守性: 他のコンポーネントとの関連性を制限する

疎結合の実現するため、他のコンポーネントが持つデータの参照は基本的にできません。<br>
他コンポーネントへのデータの受け渡し方法：
- 親から子へのデータの受け渡し　→　`props`
- 子から親へのイベントの通知　→　`$emit`

#### Example: Markdown Editor
コンポーネントツリー
```
root
└─ パネル MarkdownPanel
   ├─ 入力欄 MarkdownEditor
   └─ プレビュー MarkdownPreview
```

##### MarkdownPanel Component
Change to specific tab by variable `activeTab`
- 1: MarkdownEditor
- 2: MarkdownPreview

Hold the data of the input `content` from users

```vue
# src/components/MarkdownPanel

<template>
  <div class="panel">
    <div class="panel-header">
      <figure class="avatar avatar-lg">
        <img src="http://i.pravatar.cc/320" alt="">
      </figure>
      <div class="panel-title">User Name</div>
    </div>
    <div class="panel-nav">
      <ul class="tab">
        <li class="tab-item" :class="{ active: activeTab === 1 }">
          <a href="#" @click.prevent="activeTab = 1">Write</a>
        </li>
        <li class="tab-item" :class="{ active: activeTab === 2 }">
          <a href="#" @click.prevent="activeTab = 2">Preview</a>
        </li>
      </ul>
    </div>
    <div class="panel-body" v-show="activeTab === 1">
      <MarkdownEditor v-model="content" />
    </div>
    <div class="panel-body" v-show="activeTab === 2">
      <MarkdownPreview :text="content" />
    </div>
    <div class="panel-footer">
      <div class="text-right">
        <button class="btn btn-primary">Submit</button>
      </div>
    </div>
  </div>
</template>

<script>
import MarkdownEditor from "./MarkdownEditor.vue"
import MarkdownPreview from "./MarkdownPreview.vue"

export default {
  components: {
    MarkdownEditor,
    MarkdownPreview
  },
  data () {
    return {
      activeTab: 1,
      content: ""
    }
  }
}
</script>
```

##### MarkdownEditor Component
```vue
# src/components/MarkdownEditor

<template>
  <div class="form-group">
    <textarea
      class="form-input"
      :value="text"
      @input="onInput"
      rows="10"
    ></textarea>
  </div>
</template>

<script>
export default {
  props: {
    value: { type: String, required: true }
  },
  data () {
    return { text: this.value }
  },
  methods: {
    onInput ($event) {
      this.text = $event.target.value
      this.$emit("input", this.text)
    }
  }
}
</script>
```

##### MarkdownPreview Component
```vue
# src/components/MarkdownPreview

<template>
    <div>
        <div class="empty" v-if="text === ''">
            No content yet.
        </div>
        <div v-else v-html="html"></div>
    </div>
</template>

<script>
import marked from "marked"

export default {
  props: {
    text: { type: String, required: true }
  },
  computed: {
    html () {
      return marked(this.text)
    }
  }
}
</script>
```
Use [marked.js](https://github.com/markedjs/marked) to convert markdown to HTML

Dataflow:
```
-------------
MarkdownPanel
-------------
↓ content   ↑ text
↓ (:value)  ↑ (@input)
--------------
MarkdownEditor
--------------
↓ text      ↑ $event.target.value
↓ (:value)  ↑ (@input)
--------
textarea
--------
```

> - MarkdownPanel がマークダウン文字列のデータ（content）を管理しています。
> - v-model で MarkdownEditor に content を渡します。
> - ユーザーの入力があると MarkdownEditor から MarkdownPanel にイベントとともに入力値が通知され、MarkdownPanel が管理する content が変更されます。
> - MarkdownPanel から MarkdownPreview に props で content を渡しているため、content のデータが変更されるとそれが MarkdownPreview にも伝わります。
> - MarkdownPreview では算出プロパティ html でマークダウン文字列から HTML への変換を行なっています。<br>
> -- [Vue.jsコンポーネント入門 (5) コンポーネント間のコミュニケーション](https://www.hypertextcandy.com/vuejs-components-introduction-communication-between-components)

FYR: [Vue.js最初の難関、「props down, event up」を初心者にわかるように解説してみた](https://future-architect.github.io/articles/20200401/)

##### Entry Point: index.js / index.html
```javascript
# src/index.js

import Vue from "vue"
import MarkdownPanel from "./components/MarkdownPanel.vue"

const app = new Vue({
    el: "#app",
    components: {
        MarkdownPanel
    }
})
```
---
```html
# index.html

<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>Vue Component</title>
</head>
<body>
    <div id="app">
        <div class="container">
            <div class="columns">
                <div class="column col-6 col-md-12 col-mx-auto">
                    <markdown-panel></markdown-panel>
                </div>
            </div>
        </div>
    </div>
    <script src="./dist/main.js"></script>
</body>
</html>
```

### Vue.js 102-06 Slots
A note taking from [Vue.jsコンポーネント入門 (6) slotによるコンテンツの差し込み](https://www.hypertextcandy.com/vuejs-components-introduction-slots)

> slotとは親となるコンポーネント側から、子のコンポーネントのテンプレートの一部を差し込む機能です。<br>
> -- [Vue.jsのslotの機能を初心者にわかるように解説してみた](https://future-architect.github.io/articles/20200428/)

#### Example: Card
```vue
# src/components/Card.vue

<template>
    <div class="card">
        <div class="card-image">
            <slot name="image"></slot>
        </div>
        <div class="card-header">
            <div class="card-title h5">
                <slot name="title"></slot>
            </div>
            <div class="card-subtitle text-gray">
                <slot name="subtitle"></slot>
            </div>
        </div>
        <div class="card-body">
            <slot name="body"></slot>
        </div>
        <div class="card-footer">
            <slot name="button"></slot>
        </div>
    </div>
</template>
```
---
```javascript
# src/index.js

import Vue from "vue"
import Card from "./components/Card.vue"

const app = new Vue({
    el:"#app",
    components: {
        Card
    },
    methods: {
        onClick() {
            alert("Clicked!")
        }
    }
})
```
---
```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>Vue Component</title>
</head>
<body>
    <div id="app">
        <div class="container">
            <div class="columns">
                <div class="column col-6 col-md-8 col-mx-auto">
                    <card>
                        <img slot="image" src="https://source.unsplash.com/g1Kr4Ozfoac/1600x900">
                        <span slot="title">Microsoft</span>
                        <span slot="subtitle">Software and hardware</span>
                        <p slot="body">Empower every person and every organization on the planet to achieve more.</p>
                        <button slot="button" class="btn btn-primary" @click="onClick">Click me</button>
                    </card>
                </div>
            </div>
        </div>
    </div>
    <script src="./dist/main.js"></script>
</body>
</html>
```

### Vue.js 102-07 Lifecycle Hooks
A note taking from [Vue.jsコンポーネント入門 (7) ライフサイクルフック](https://www.hypertextcandy.com/vuejs-components-introduction-lifecycle-hooks)

```vue
<script>
export default {
    data() {
        return {
            // ...
        }
    },
    // created hook: called when Vue instance is created
    created() {
        // ... do something when Vue instance is created
    }
  }
</script>
```

Lifecycle of a Vue component:
- Vue instance created: `beforeCreate`, `created` - initiate AJAX calls
- mounted to DOM: `beforeMount`, `mounted` - initiate JS libraries
- data / page updated: `beforeUpdate`, `updated`
- destroy: `beforeDestroy`, `destroyed`
- others: `activated`, `deactivated`, `errorCaptured`

FYR:
- [Vue.js - Vue Instance - Lifecycle Diagram](https://vuejs.org/v2/guide/instance.html#Lifecycle-Diagram)
- [Vue.js - Lifecycle Hooks](https://vuejs.org/v2/api/index.html#Options-Lifecycle-Hooks)
