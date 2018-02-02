# vue.js-note
个人关于vue.js的一些学习笔记



##**vue.js**
Vue (读音 /vjuː/，类似于 view) 是一套用于构建用户界面的渐进式框架。与其它大型框架不同的是，Vue 被设计为可以自底向上逐层应用。Vue 的核心库只关注视图层，不仅易于上手，还便于与第三方库或既有项目整合。另一方面，当与现代化的工具链以及各种支持类库结合使用时，Vue 也完全能够为复杂的单页应用提供驱动。

##**起步**
创建一个 .html 文件，然后通过如下方式引入 Vue：

    <script src="https://cdn.jsdelivr.net/npm/vue"></script>

##**声明式渲染**



Vue.js 的核心是一个允许采用简洁的模板语法来声明式地将数据渲染进 DOM 的系统：

    <div id="app">
      {{ message }}
    </div>
    //js
    var app = new Vue({
      el: '#app',
      data: {
       message: 'Hello Vue!'
          }
        })

输出Hello Vue! 
我们已经成功创建了第一个 Vue 应用！看起来这跟渲染一个字符串模板非常类似，但是 Vue 在背后做了大量工作。现在数据和 DOM 已经被建立了关联，所有东西都是响应式的。我们要怎么确认呢？打开你的浏览器的 JavaScript 控制台 (就在这个页面打开)，并修改 app.message 的值，你将看到上例相应地更新。

除了文本插值，我们还可以像这样来绑定元素特性：

    <div id="app-2">
      <span v-bind:title="message">
        鼠标悬停几秒钟查看此处动态绑定的提示信息！
      </span>
    </div>
    //js
        var app2 = new Vue({
          el: '#app-2',
          data: {
            message: '页面加载于 ' + new Date().toLocaleString()
          }
        })

这里我们遇到了一点新东西。你看到的 v-bind 特性被称为指令。指令带有前缀 v-，以表示它们是 Vue 提供的特殊特性。可能你已经猜到了，它们会在渲染的 DOM 上应用特殊的响应式行为。在这里，该指令的意思是：“将这个元素节点的 title 特性和 Vue 实例的 message 属性保持一致”。
如果你再次打开浏览器的 JavaScript 控制台，输入 app2.message = '新消息'，就会再一次看到这个绑定了 title 特性的 HTML 已经进行了更新。

##**条件与循环**

控制切换一个元素是否显示也相当简单：

    <div id="app-3">
      <p v-if="seen">现在你看到我了</p>
    </div>
    var app3 = new Vue({
      el: '#app-3',
      data: {
        seen: true
      }
    })

继续在控制台输入 app3.seen = false，你会发现之前显示的消息消失了。
这个例子演示了我们不仅可以把数据绑定到 DOM 文本或特性，还可以绑定到 DOM 结构。此外，Vue 也提供一个强大的过渡效果系统，可以在 Vue 插入/更新/移除元素时自动应用过渡效果。
还有其它很多指令，每个都有特殊的功能。例如，v-for 指令可以绑定数组的数据来渲染一个项目列表：

    <div id="app-4">
      <ol>
        <li v-for="todo in todos">
          {{ todo.text }}
        </li>
      </ol>
    </div>
    var app4 = new Vue({
      el: '#app-4',
      data: {
        todos: [
          { text: '学习 JavaScript' },
          { text: '学习 Vue' },
          { text: '整个牛项目' }
        ]
      }

    })
##**处理用户输入**
为了让用户和你的应用进行交互，我们可以用 v-on 指令添加一个事件监听器，通过它调用在 Vue 实例中定义的方法：

    <div id="app-5">
      <p>{{ message }}</p>
      <button v-on:click="reverseMessage">逆转消息</button>
    </div>
    //js
    var app5 = new Vue({
      el: '#app-5',
      data: {
        message: 'Hello Vue.js!'
      },
      methods: {
        reverseMessage: function () {
          this.message = this.message.split('').reverse().join('')
        }
      }
    })
注意在 reverseMessage 方法中，我们更新了应用的状态，但没有触碰 DOM——所有的 DOM 操作都由 Vue 来处理，你编写的代码只需要关注逻辑层面即可。

###Vue 还提供了 v-model 指令，它能轻松实现表单输入和应用状态之间的双向绑定。

    <div id="app-6">
      <p>{{ message }}</p>
      <input v-model="message">
    </div>
    //js
    var app6 = new Vue({
      el: '#app-6',
      data: {
        message: 'Hello Vue!'
      }
    })

##**组件化应用构建**
组件系统是 Vue 的另一个重要概念，因为它是一种抽象，允许我们使用小型、独立和通常可复用的组件构建大型应用。仔细想想，几乎任意类型的应用界面都可以抽象为一个组件树：

在 Vue 里，一个组件本质上是一个拥有预定义选项的一个 Vue 实例。在 Vue 中注册组件很简单：

    // 定义名为 todo-item 的新组件
    Vue.component('todo-item', {
      template: '<li>这是个待办项</li>'
    })
现在你可以用它构建另一个组件模板：

    <ol>
      <!-- 创建一个 todo-item 组件的实例 -->
      <todo-item></todo-item>
    </ol>

但是这样会为每个待办项渲染同样的文本，这看起来并不炫酷。我们应该能从父作用域将数据传到子组件才对。让我们来修改一下组件的定义，使之能够接受一个 prop：

    Vue.component('todo-item', {
      // todo-item 组件现在接受一个
      // "prop"，类似于一个自定义特性。
      // 这个 prop 名为 todo。
      props: ['todo'],
      template: '<li>{{ todo.text }}</li>'
    })
现在，我们可以使用 v-bind 指令将待办项传到循环输出的每个组件中：

    <div id="app-7">
      <ol>
        <!--
          现在我们为每个 todo-item 提供 todo 对象
          todo 对象是变量，即其内容可以是动态的。
          我们也需要为每个组件提供一个“key”，稍后再
          作详细解释。
        -->
        <todo-item
          v-for="item in groceryList"
          v-bind:todo="item"
          v-bind:key="item.id">
        </todo-item>
      </ol>
    </div>
    //js
    Vue.component('todo-item', {
      props: ['todo'],
      template: '<li>{{ todo.text }}</li>'
    })
    
    var app7 = new Vue({
      el: '#app-7',
      data: {
        groceryList: [
          { id: 0, text: '蔬菜' },
          { id: 1, text: '奶酪' },
          { id: 2, text: '随便其它什么人吃的东西' }
        ]
      }
    })
尽管这只是一个刻意设计的例子，但是我们已经设法将应用分割成了两个更小的单元。子单元通过 prop 接口与父单元进行了良好的解耦。我们现在可以进一步改进 <todo-item> 组件，提供更为复杂的模板和逻辑，而不会影响到父单元。


#**Vue 实例**
##**创建一个 Vue 实例**
每个 Vue 应用都是通过用 Vue 函数创建一个新的 Vue 实例开始的：

    var vm = new Vue({
      // 选项
    })

虽然没有完全遵循 MVVM 模型，但是 Vue 的设计也受到了它的启发。因此在文档中经常会使用 vm (ViewModel 的缩写) 这个变量名表示 Vue 实例。
当创建一个 Vue 实例时，你可以传入一个选项对象。这篇教程主要描述的就是如何使用这些选项来创建你想要的行为。作为参考，你也可以在 API 文档 中浏览完整的选项列表。
##**数据与方法**
当一个 Vue 实例被创建时，它向 Vue 的响应式系统中加入了其 data 对象中能找到的所有的属性。当这些属性的值发生改变时，视图将会产生“响应”，即匹配更新为新的值。

    // 我们的数据对象
    var data = { a: 1 }
    
    // 该对象被加入到一个 Vue 实例中
    var vm = new Vue({
      data: data
    })
    
    // 它们引用相同的对象！
    vm.a === data.a // => true
    
    // 设置属性也会影响到原始数据
    vm.a = 2
    data.a // => 2
    
    // ……反之亦然
    data.a = 3
    vm.a // => 3
当这些数据改变时，视图会进行重渲染。值得注意的是只有当实例被创建时 data 中存在的属性才是响应式的。也就是说如果你添加一个新的属性，比如：

    vm.b = 'hi'
那么对 b 的改动将不会触发任何视图的更新。如果你知道你会在晚些时候需要一个属性，但是一开始它为空或不存在，那么你仅需要设置一些初始值。比如：

    data: {
      newTodoText: '',
      visitCount: 0,
      hideCompletedTodos: false,
      todos: [],
      error: null
    }
这里唯一的例外是使用 Object.freeze()，这会阻止修改现有的属性，也意味着响应系统无法再追踪变化。

    var obj = {
      foo: 'bar'
    }
    
    Object.freeze(obj)
    
    new Vue({
      el: '#app',
      data () {
        return {
          obj
        }
      }
    })
    <div id="app">
      <p>{{ obj.foo }}</p>
      <!-- 这里的 `obj.foo` 不会更新！ -->
      <button @click="obj.foo = 'baz'">Change it</button>
    </div>
除了数据属性，Vue 实例还暴露了一些有用的实例属性与方法。它们都有前缀 $，以便与用户定义的属性区分开来。例如：

    var data = { a: 1 }
    var vm = new Vue({
      el: '#example',
      data: data
    })
    
    vm.$data === data // => true
    vm.$el === document.getElementById('example') // => true
    
    // $watch 是一个实例方法
    vm.$watch('a', function (newValue, oldValue) {
      // 这个回调将在 `vm.a` 改变后调用
    })
##**实例生命周期钩子**
每个 Vue 实例在被创建时都要经过一系列的初始化过程——例如，需要设置数据监听、编译模板、将实例挂载到 DOM 并在数据变化时更新 DOM 等。同时在这个过程中也会运行一些叫做生命周期钩子的函数，这给了用户在不同阶段添加自己的代码的机会。
比如 created 钩子可以用来在一个实例被创建之后执行代码：

    new Vue({
      data: {
        a: 1
      },
      created: function () {
        // `this` 指向 vm 实例
        console.log('a is: ' + this.a)
      }
    })
    // => "a is: 1"
有一些其它的钩子，在实例生命周期的不同阶段被调用，如 mounted、updated 和 destroyed。生命周期钩子的 this 上下文指向调用它的 Vue 实例。

**不要在选项属性或回调上使用箭头函数，比如 created: () => console.log(this.a) 或 vm.$watch('a', newValue => this.myMethod())。因为箭头函数是和父级上下文绑定在一起的，this 不会是如你所预期的 Vue 实例，经常导致 Uncaught TypeError: Cannot read property of undefined 或 Uncaught TypeError: this.myMethod is not a function 之类的错误。**
##**生命周期图示**
下图展示了实例的生命周期。
![](leanote://file/getImage?fileId=5a67737c0ed709394f000006)

##**模板语法**
Vue.js 使用了基于 HTML 的模板语法，允许开发者声明式地将 DOM 绑定至底层 Vue 实例的数据。所有 Vue.js 的模板都是合法的 HTML ，所以能被遵循规范的浏览器和 HTML 解析器解析。

在底层的实现上，Vue 将模板编译成虚拟 DOM 渲染函数。结合响应系统，Vue 能够智能地计算出最少需要重新渲染多少组件，并把 DOM 操作次数减到最少。

如果你熟悉虚拟 DOM 并且偏爱 JavaScript 的原始力量，你也可以不用模板，直接写渲染 (render) 函数，使用可选的 JSX 语法。

##**插值**
文本
数据绑定最常见的形式就是使用“Mustache”语法 (双大括号) 的文本插值：

    <span>Message: {{ msg }}</span>

Mustache 标签将会被替代为对应数据对象上 msg 属性的值。无论何时，绑定的数据对象上 msg 属性发生了改变，插值处的内容都会更新。
通过使用 v-once 指令，你也能执行一次性地插值，当数据改变时，插值处的内容不会更新。但请留心这会影响到该节点上所有的数据绑定：

    <span v-once>这个将不会改变: {{ msg }}</span>

##**原始HTML**
双大括号会将数据解释为普通文本，而非 HTML 代码。为了输出真正的 HTML，你需要使用 v-html 指令：

    <p>Using mustaches: {{ rawHtml }}</p>
    <p>Using v-html directive: <span v-html="rawHtml"></span></p>

这个 span 的内容将会被替换成为属性值 rawHtml，直接作为 HTML——会忽略解析属性值中的数据绑定。注意，你不能使用 v-html 来复合局部模板，因为 Vue 不是基于字符串的模板引擎。反之，对于用户界面 (UI)，组件更适合作为可重用和可组合的基本单位。

##**特性**
Mustache 语法不能作用在 HTML 特性上，遇到这种情况应该使用 v-bind 指令：

    <div v-bind:id="dynamicId"></div>
在布尔特性的情况下，它们的存在即暗示为 true，v-bind 工作起来略有不同，在这个例子中：

    <button v-bind:disabled="isButtonDisabled">Button</button>
如果 isButtonDisabled 的值是 null、undefined 或 false，则 disabled 特性甚至不会被包含在渲染出来的 <button> 元素中。

##**使用 JavaScript 表达式**
迄今为止，在我们的模板中，我们一直都只绑定简单的属性键值。但实际上，对于所有的数据绑定，Vue.js 都提供了完全的 JavaScript 表达式支持。

    {{ number + 1 }}
    
    {{ ok ? 'YES' : 'NO' }}
    
    {{ message.split('').reverse().join('') }}
    
    <div v-bind:id="'list-' + id"></div>

这些表达式会在所属 Vue 实例的数据作用域下作为 JavaScript 被解析。有个限制就是，每个绑定都只能包含单个表达式，所以下面的例子都不会生效。

    <!-- 这是语句，不是表达式 -->
    {{ var a = 1 }}
    
    <!-- 流控制也不会生效，请使用三元表达式 -->
    {{ if (ok) { return message } }}

模板表达式都被放在沙盒中，只能访问全局变量的一个白名单，如 Math 和 Date 。你不应该在模板表达式中试图访问用户定义的全局变量。

##**指令**
指令 (Directives) 是带有 v- 前缀的特殊属性。指令属性的值预期是单个 JavaScript 表达式 (v-for 是例外情况，稍后我们再讨论)。指令的职责是，当表达式的值改变时，将其产生的连带影响，响应式地作用于 DOM。回顾我们在介绍中看到的例子：

    <p v-if="seen">现在你看到我了</p>
这里，v-if 指令将根据表达式 seen 的值的真假来插入/移除 <p> 元素。

##**参数**

一些指令能够接收一个“参数”，在指令名称之后以冒号表示。例如，v-bind 指令可以用于响应式地更新 HTML 属性：

    <a v-bind:href="url">...</a>
在这里 href 是参数，告知 v-bind 指令将该元素的 href 属性与表达式 url 的值绑定。



另一个例子是 v-on 指令，它用于监听 DOM 事件：

    <a v-on:click="doSomething">...</a>
在这里参数是监听的事件名。我们也会更详细地讨论事件处理。

##**修饰符**
修饰符 (Modifiers) 是以半角句号 . 指明的特殊后缀，用于指出一个指令应该以特殊方式绑定。例如，.prevent 修饰符告诉 v-on 指令对于触发的事件调用 event.preventDefault()：

    <form v-on:submit.prevent="onSubmit">...</form>
在接下来对 v-on 和 v-for 等功能的探索中，你会看到修饰符的其它例子。

##**缩写**
v- 前缀作为一种视觉提示，用来识别模板中 Vue 特定的特性。当你在使用 Vue.js 为现有标签添加动态行为 (dynamic behavior) 时，v- 前缀很有帮助，然而，对于一些频繁用到的指令来说，就会感到使用繁琐。同时，在构建由 Vue.js 管理所有模板的单页面应用程序 (SPA - single page application) 时，v- 前缀也变得没那么重要了。因此，Vue.js 为 v-bind 和 v-on 这两个最常用的指令，提供了特定简写：
##v-bind 缩写

    <!-- 完整语法 -->
    <a v-bind:href="url">...</a>
    
    <!-- 缩写 -->
    <a :href="url">...</a>
##v-on 缩写

   

     <!-- 完整语法 -->
        <a v-on:click="doSomething">...</a>
        
        <!-- 缩写 -->
        <a @click="doSomething">...</a>

它们看起来可能与普通的 HTML 略有不同，但 : 与 @ 对于特性名来说都是合法字符，在所有支持 Vue.js 的浏览器都能被正确地解析。而且，它们不会出现在最终渲染的标记中。缩写语法是完全可选的，但随着你更深入地了解它们的作用，你会庆幸拥有它们。

##**基础例子**

    <div id="example">
      <p>Original message: "{{ message }}"</p>
      <p>Computed reversed message: "{{ reversedMessage }}"</p>
    </div>
    var vm = new Vue({
      el: '#example',
      data: {
        message: 'Hello'
      },
      computed: {
        // 计算属性的 getter
        reversedMessage: function () {
          // `this` 指向 vm 实例
          return this.message.split('').reverse().join('')
        }
      }
    })

结果：

    Original message: "Hello"
    Computed reversed message: "olleH"

这里我们声明了一个计算属性 reversedMessage。我们提供的函数将用作属性 vm.reversedMessage 的 getter 函数：

    console.log(vm.reversedMessage) // => 'olleH'
    vm.message = 'Goodbye'
    console.log(vm.reversedMessage) // => 'eybdooG'

你可以打开浏览器的控制台，自行修改例子中的 vm。vm.reversedMessage 的值始终取决于 vm.message 的值。

你可以像绑定普通属性一样在模板中绑定计算属性。Vue 知道 vm.reversedMessage 依赖于 vm.message，因此当 vm.message 发生改变时，所有依赖 vm.reversedMessage 的绑定也会更新。而且最妙的是我们已经以声明的方式创建了这种依赖关系：计算属性的 getter 函数是没有副作用 (side effect) 的，这使它更易于测试和理解。

##**计算属性缓存 vs 方法**
你可能已经注意到我们可以通过在表达式中调用方法来达到同样的效果：

    <p>Reversed message: "{{ reversedMessage() }}"</p>

    // 在组件中
    methods: {
      reversedMessage: function () {
        return this.message.split('').reverse().join('')
      }
    }

我们可以将同一函数定义为一个方法而不是一个计算属性。两种方式的最终结果确实是完全相同的。然而，不同的是计算属性是基于它们的依赖进行缓存的。计算属性只有在它的相关依赖发生改变时才会重新求值。这就意味着只要 message 还没有发生改变，多次访问 reversedMessage 计算属性会立即返回之前的计算结果，而不必再次执行函数。
这也同样意味着下面的计算属性将不再更新，因为 Date.now() 不是响应式依赖：

    computed: {
      now: function () {
        return Date.now()
      }
    }


相比之下，每当触发重新渲染时，调用方法将总会再次执行函数。
我们为什么需要缓存？假设我们有一个性能开销比较大的的计算属性 A，它需要遍历一个巨大的数组并做大量的计算。然后我们可能有其他的计算属性依赖于 A 。如果没有缓存，我们将不可避免的多次执行 A 的 getter！如果你不希望有缓存，请用方法来替代。

们将不可避免的多次执行 A 的 getter！如果你不希望有缓存，请用方法来替代。
##**计算属性 vs 侦听属性**
Vue 提供了一种更通用的方式来观察和响应 Vue 实例上的数据变动：侦听属性。当你有一些数据需要随着其它数据变动而变动时，你很容易滥用 watch——特别是如果你之前使用过 AngularJS。然而，通常更好的做法是使用计算属性而不是命令式的 watch 回调。细想一下这个例子：

    <div id="demo">{{ fullName }}</div>
    var vm = new Vue({
      el: '#demo',
      data: {
        firstName: 'Foo',
        lastName: 'Bar',
        fullName: 'Foo Bar'
      },
      watch: {
        firstName: function (val) {
          this.fullName = val + ' ' + this.lastName
        },
        lastName: function (val) {
          this.fullName = this.firstName + ' ' + val
        }
      }
    })

上面代码是命令式且重复的。将它与计算属性的版本进行比较：

    var vm = new Vue({
      el: '#demo',
      data: {
        firstName: 'Foo',
        lastName: 'Bar'
      },
      computed: {
        fullName: function () {
          return this.firstName + ' ' + this.lastName
        }
      }
    })

好得多了，不是吗？

##**计算属性的 setter**
计算属性默认只有 getter ，不过在需要时你也可以提供一个 setter ：

    // ...
    computed: {
      fullName: {
        // getter
        get: function () {
          return this.firstName + ' ' + this.lastName
        },
        // setter
        set: function (newValue) {
          var names = newValue.split(' ')
          this.firstName = names[0]
          this.lastName = names[names.length - 1]
        }
      }
    }
    // ...

现在再运行 vm.fullName = 'John Doe' 时，setter 会被调用，vm.firstName 和 vm.lastName 也会相应地被更新。

##**侦听器**
虽然计算属性在大多数情况下更合适，但有时也需要一个自定义的侦听器。这就是为什么 Vue 通过 watch 选项提供了一个更通用的方法，来响应数据的变化。当需要在数据变化时执行异步或开销较大的操作时，这个方式是最有用的。
例如：

    <div id="watch-example">
      <p>
        Ask a yes/no question:
        <input v-model="question">
      </p>
      <p>{{ answer }}</p>
    </div>
    <!-- 因为 AJAX 库和通用工具的生态已经相当丰富，Vue 核心代码没有重复 -->
    <!-- 提供这些功能以保持精简。这也可以让你自由选择自己更熟悉的工具。 -->
    <script src="https://cdn.jsdelivr.net/npm/axios@0.12.0/dist/axios.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/lodash@4.13.1/lodash.min.js"></script>
    <script>
    var watchExampleVM = new Vue({
      el: '#watch-example',
      data: {
        question: '',
        answer: 'I cannot give you an answer until you ask a question!'
      },
      watch: {
        // 如果 `question` 发生改变，这个函数就会运行
        question: function (newQuestion, oldQuestion) {
          this.answer = 'Waiting for you to stop typing...'
          this.getAnswer()
        }
      },
      methods: {
        // `_.debounce` 是一个通过 Lodash 限制操作频率的函数。
        // 在这个例子中，我们希望限制访问 yesno.wtf/api 的频率
        // AJAX 请求直到用户输入完毕才会发出。想要了解更多关于
        // `_.debounce` 函数 (及其近亲 `_.throttle`) 的知识，
        // 请参考：https://lodash.com/docs#debounce
        getAnswer: _.debounce(
          function () {
            if (this.question.indexOf('?') === -1) {
              this.answer = 'Questions usually contain a question mark. ;-)'
              return
            }
            this.answer = 'Thinking...'
            var vm = this
            axios.get('https://yesno.wtf/api')
              .then(function (response) {
                vm.answer = _.capitalize(response.data.answer)
              })
              .catch(function (error) {
                vm.answer = 'Error! Could not reach the API. ' + error
              })
          },
          // 这是我们为判定用户停止输入等待的毫秒数
          500
        )
      }
    })
    </script>
在这个示例中，使用 watch 选项允许我们执行异步操作 (访问一个 API)，限制我们执行该操作的频率，并在我们得到最终结果前，设置中间状态。这些都是计算属性无法做到的。


##**Class 与 Style 绑定**
操作元素的 class 列表和内联样式是数据绑定的一个常见需求。因为它们都是属性，所以我们可以用 v-bind 处理它们：只需要通过表达式计算出字符串结果即可。不过，字符串拼接麻烦且易错。因此，在将 v-bind 用于 class 和 style 时，Vue.js 做了专门的增强。表达式结果的类型除了字符串之外，还可以是对象或数组。
绑定 HTML Class
对象语法
我们可以传给 v-bind:class 一个对象，以动态地切换 class：

    <div v-bind:class="{ active: isActive }"></div>

上面的语法表示 active 这个 class 存在与否将取决于数据属性 isActive 的 truthiness。
你可以在对象中传入更多属性来动态切换多个 class。此外，v-bind:class 指令也可以与普通的 class 属性共存。当有如下模板:

    <div class="static"
         v-bind:class="{ active: isActive, 'text-danger': hasError }">
    </div>
    和如下 data：
    data: {
      isActive: true,
      hasError: false
    }

结果渲染为：

    <div class="static active"></div>

当 isActive 或者 hasError 变化时，class 列表将相应地更新。例如，如果 hasError 的值为 true，class 列表将变为 "static active text-danger"。
绑定的数据对象不必内联定义在模板里：

    <div v-bind:class="classObject"></div>
    data: {
      classObject: {
        active: true,
        'text-danger': false
      }
    }

渲染的结果和上面一样。我们也可以在这里绑定一个返回对象的计算属性。这是一个常用且强大的模式：

    <div v-bind:class="classObject"></div>
    data: {
      isActive: true,
      error: null
    },
    computed: {
      classObject: function () {
        return {
          active: this.isActive && !this.error,
          'text-danger': this.error && this.error.type === 'fatal'
        }
      }
    }

##**数组语法**
我们可以把一个数组传给 v-bind:class，以应用一个 class 列表：

    <div v-bind:class="[activeClass, errorClass]"></div>
    data: {
      activeClass: 'active',
      errorClass: 'text-danger'
    }

渲染为：

    <div class="active text-danger"></div>

如果你也想根据条件切换列表中的 class，可以用三元表达式：

    <div v-bind:class="[isActive ? activeClass : '', errorClass]"></div>

这样写将始终添加 errorClass，但是只有在 isActive 是 truthy[1] 时才添加 activeClass。
不过，当有多个条件 class 时这样写有些繁琐。所以在数组语法中也可以使用对象语法：

    <div v-bind:class="[{ active: isActive }, errorClass]"></div>

##**用在组件上**
这个章节假设你已经对 Vue 组件有一定的了解。当然你也可以先跳过这里，稍后再回过头来看。
当在一个自定义组件上使用 class 属性时，这些类将被添加到该组件的根元素上面。这个元素上已经存在的类不会被覆盖。
例如，如果你声明了这个组件：

    Vue.component('my-component', {
      template: '<p class="foo bar">Hi</p>'
    })

然后在使用它的时候添加一些 class：

    <my-component class="baz boo"></my-component>

HTML 将被渲染为:

    <p class="foo bar baz boo">Hi</p>

对于带数据绑定 class 也同样适用：

    <my-component v-bind:class="{ active: isActive }"></my-component>

当 isActive 为 truthy[1] 时，HTML 将被渲染成为：

    <p class="foo bar active">Hi</p>

##**绑定内联样式**
##对象语法
v-bind:style 的对象语法十分直观——看着非常像 CSS，但其实是一个 JavaScript 对象。CSS 属性名可以用驼峰式 (camelCase) 或短横线分隔 (kebab-case，记得用单引号括起来) 来命名：

    <div v-bind:style="{ color: activeColor, fontSize: fontSize + 'px' }"></div>
    data: {
      activeColor: 'red',
      fontSize: 30
    }

直接绑定到一个样式对象通常更好，这会让模板更清晰：

    <div v-bind:style="styleObject"></div>
    data: {
      styleObject: {
        color: 'red',
        fontSize: '13px'
      }
    }

同样的，对象语法常常结合返回对象的计算属性使用。

##数组语法
v-bind:style 的数组语法可以将多个样式对象应用到同一个元素上：

    <div v-bind:style="[baseStyles, overridingStyles]"></div>

##自动添加前缀
当 v-bind:style 使用需要添加浏览器引擎前缀的 CSS 属性时，如 transform，Vue.js 会自动侦测并添加相应的前缀。

##多重值
2.3.0+
从 2.3.0 起你可以为 style 绑定中的属性提供一个包含多个值的数组，常用于提供多个带前缀的值，例如：

    <div :style="{ display: ['-webkit-box', '-ms-flexbox', 'flex'] }"></div>

这样写只会渲染数组中最后一个被浏览器支持的值。在本例中，如果浏览器支持不带浏览器前缀的 flexbox，那么就只会渲染 display: flex。


##**条件渲染**

##v-if
在字符串模板中，比如 Handlebars，我们得像这样写一个条件块：

   

     <!-- Handlebars 模板 -->
        {{#if ok}}
          <h1>Yes</h1>
        {{/if}}

在 Vue 中，我们使用 v-if 指令实现同样的功能：

    <h1 v-if="ok">Yes</h1>

也可以用 v-else 添加一个“else 块”：

    <h1 v-if="ok">Yes</h1>
    <h1 v-else>No</h1>
    在 <template> 元素上使

##用 v-if 条件渲染分组
因为 v-if 是一个指令，所以必须将它添加到一个元素上。但是如果想切换多个元素呢？此时可以把一个 <template> 元素当做不可见的包裹元素，并在上面使用 v-if。最终的渲染结果将不包含 <template> 元素。

    <template v-if="ok">
      <h1>Title</h1>
      <p>Paragraph 1</p>
      <p>Paragraph 2</p>
    </template>

v-else
你可以使用 v-else 指令来表示 v-if 的“else 块”：

    <div v-if="Math.random() > 0.5">
      Now you see me
    </div>
    <div v-else>
      Now you don't
    </div>

v-else 元素必须紧跟在带 v-if 或者 v-else-if 的元素的后面，否则它将不会被识别。


##v-else-if

##2.1.0 新增
v-else-if，顾名思义，充当 v-if 的“else-if 块”，可以连续使用：

    <div v-if="type === 'A'">
      A
    </div>
    <div v-else-if="type === 'B'">
      B
    </div>
    <div v-else-if="type === 'C'">
      C
    </div>
    <div v-else>
      Not A/B/C
    </div>

类似于 v-else，v-else-if 也必须紧跟在带 v-if 或者 v-else-if 的元素之后。
用 key 管理可复用的元素
Vue 会尽可能高效地渲染元素，通常会复用已有元素而不是从头开始渲染。这么做除了使 Vue 变得非常快之外，还有其它一些好处。例如，如果你允许用户在不同的登录方式之间切换：

    <template v-if="loginType === 'username'">
      <label>Username</label>
      <input placeholder="Enter your username">
    </template>
    <template v-else>
      <label>Email</label>
      <input placeholder="Enter your email address">
    </template>

那么在上面的代码中切换 loginType 将不会清除用户已经输入的内容。因为两个模板使用了相同的元素，input不会被替换掉——仅仅是替换了它的 placeholder。
自己动手试一试，在输入框中输入一些文本，然后按下切换按钮：

这样也不总是符合实际需求，所以 Vue 为你提供了一种方式来表达“这两个元素是完全独立的，不要复用它们”。只需添加一个具有唯一值的 key 属性即可：

    <template v-if="loginType === 'username'">
      <label>Username</label>
      <input placeholder="Enter your username" key="username-input">
    </template>
    <template v-else>
      <label>Email</label>
      <input placeholder="Enter your email address" key="email-input">
    </template>

现在，每次切换时，输入框都将被重新渲染。请看：


##v-show
另一个用于根据条件展示元素的选项是 v-show 指令。用法大致一样：

    <h1 v-show="ok">Hello!</h1>

不同的是带有 v-show 的元素始终会被渲染并保留在 DOM 中。v-show 只是简单地切换元素的 CSS 属性 display。
注意，v-show 不支持 <template> 元素，也不支持 v-else。
##v-if vs v-show
v-if 是“真正”的条件渲染，因为它会确保在切换过程中条件块内的事件监听器和子组件适当地被销毁和重建。
v-if 也是惰性的：如果在初始渲染时条件为假，则什么也不做——直到条件第一次变为真时，才会开始渲染条件块。
相比之下，v-show 就简单得多——不管初始条件是什么，元素总是会被渲染，并且只是简单地基于 CSS 进行切换。
一般来说，v-if 有更高的切换开销，而 v-show 有更高的初始渲染开销。因此，如果需要非常频繁地切换，则使用 v-show 较好；如果在运行时条件很少改变，则使用 v-if 较好。
v-if 与 v-for 一起使用
当 v-if 与 v-for 一起使用时，v-for 具有比 v-if 更高的优先级。
