#Introduction

Vue.js is a library for building interactive web interfaces. 
#####The goal of Vue.js: 
provide the benefits of **reactive data binding**(响应数据绑定) and **composable view components**(组合视图组件) with an API that is as simple as possible.

Vue.js itself is not a full-blown framework - it is focused on the view layer only. Therefore it is very easy to pick up and to integrate with other libraries or existing projects. On the other hand, when used in combination with proper tooling and supporting libraries, Vue.js is also perfectly capable of powering sophisticated Single-Page Applications.


##Reactive Data Binding
At the core of Vue.js is a reactive data-binding system that makes it extremely simple to keep your data and the DOM in sync. When using jQuery to manually manipulate the DOM, the code we write is often imperative, repetitive and error-prone. Vue.js embraces the concept of data-driven view. In plain words, it means we use special syntax in our normal HTML templates to "bind" the DOM to the underlying data. Once the bindings are created, the DOM will then be kept in sync with the data. Whenever you modify the data, the DOM updates accordingly. As a result, most of our application logic is now directly manipulating data, rather than messing around with DOM updates. This makes our code easier to write, easier to reason about and easier to maintain.

For the simplest possible example:

    <!-- this is our View -->
    <div id="example-1">
        Hello {{name}}!
    </div>

    //this is our Model
    var exampleData = {
        name: "Vue.js"
    }

    //create a Vue instance, or , a "ViewModel"
    //which links the View and the Model
    var exampleVM = new Vue({
        el: "#example-1",
        data: exampleData
    })

Result:

    Hello Vue.js!

This looks pretty similar to just rendering a template, but Vue.js has done a lot of work under the hood. The data and the DOM are now linked, and everything is now reactive.

Note that we didn't have to write any DOM-manipulating code: the HTML template, enhanced with the bindings, is a declarative mapping of the underlying data state, which is in turn just plain JavaScript objects. Our view is entirely data-driven.

Let's look at a second example:

    <div id="example-2">
        <p v-if="greeting">Hello!</p>
    </div>

    var exampleVM2 = new Vue({
        el:"#example-2",
        data:{
            greeting:true
        }
    })

    Hello!


Here we are encountering something new. The `v-if` attribute you are seeing is called a **Directive**. Directives are prefixed with `v-` to indicate that they are special attributes provided by Vue.js, and as you may have guessed, they apply special reactive behavior to the rendered DOM. Go ahead and set `exampleVM2.greeting` to `false` in the console. You should see the "Hello!" message desappear.

This second example demonstrates that not only can we bind DOM text to the data, we can also bind the structure of the DOM to the data. Moreover, Vue.js also provides a powerful transition effect system that can automatically apply transition effects when elements are inserted/removed by Vue.

There are quite a few other directives, each with its own special functionality. For example the `v-for` directive for displaying items in an Array, or the `v-bind` directive for binding HTML attributes. We will discuss the full data-binding syntax with more details later.


##Component System

The Component System is another important concept in Vue.js, because it's an abstraction that allows us to build large-scale applications composed of small, self-contained, and often reusable components. If we think about it, almost any type of application interface can be abstracted into a tree of components.

In fact, a typical large application built with Vue.js would form a tree of components. We will talk a lot more about components later in the guide, but here's an (imaginary) example of what an app's template would look like with components:

    <div id="app">
        <app-nav></app-nav>
        <app-view>
            <app-sidebar></app-sidebar>
            <app-content></app-content>
        </app-view>
    </div>

You may have noticed that Vue.js components are very similar to Custom Elements, which is part of the Web Components Spec(https://www.w3.org/wiki/WebComponents/). In fact, Vue.js' component syntax is loosely modeled after the spec. For example, Vue components implement the Slot API and the `is` special attribute. However, there are a few key differences:

1. The Web Components Spec is still very much a work in progress, and is not natively implemented in every browser. In comparison, Vue.js components don't require any polyfills and works consistently in all supported browsers(IE9 and above). When needed, Vue.js components can also be wrapped inside a native custom element.
2. Vue.js components provide important features that are not available in plain custom elements, most notably cross-component data flow, custom event communication and dynamic component switching with transition effects.

The component system is the foundation for building large apps with Vue.js. In addition, the Vue.js ecosystem also provides advanced tooling and various supporting libraries that can be put together to create a more "framework" like system.




#The Vue Instance

##Constructor

Every Vue.js app is bootstrapped by creating a **root Vue instance** with the `Vue` constructor function:

    var vm = new Vue({
        //options
    })

A Vue instance is essentially a ViewModel as defined in the MVVM pattern, hence the variable name `vm` you will see throughout the docs.

When you instantiate a Vue instance, you need to pass in an **options object** which can contain options for data, template, element to mount on, methods, lifecycle callbacks and more. The full list of options can be found in the API reference.

The `Vue` constructor can be extended to create reusable **component constructors** with pre-defined options:

    var MyComponent = Vue.extend({
        //extension options
    })

    //all instances of 'MyComponent' are created with the pre-defined extension options
    var myComponentInstance = new MyComponent()

Although you can create extended instances imperatively, in most cases you will be registering a component constructor as a custom element and composing them in templates declaratively. We will talk about the component system in detail later. For now, you just need to know that all Vue.js components are essentially extended Vue instances.


##Properties and Methods
Each Vue instance proxies all the properties found in its `data` object:

    var data = {a:1}
    var vm = new Vue({
        data: data
    })

    vm.a === data.a // ->true

    // setting the property also affects original data
    vm.a = 2
    data.a // ->2

    //...and vice-versa
    data.a = 3
    vm.a //->3

It should be noted that **only these proxied properties are reactive**. If you attach a new property to the instance after it has been created, it will not trigger any view updates. We will discuss the reactivity system in detail later.

In addition to data properties, Vue instances expose a nunber of useful instance properties and methods. These properties and methods are prefixed with `$` to differentiate from proxied data properties. For example:

    var data = {a:1}
    var vm = new Vue({
        el: "#example",
        data: data
    })

    vm.$data === data // ->true
    vm.$el === document.getElementById("example")  // -> true

    // $watch is an instance method
    vm.$watch("a", function(newVal, oldVal){
        // this callback will be called when 'vm.a' changes
    })

Consult the API reference for the full list of instance properties and methods.


##Instance Lifecycle
Each Vue instance goes through a series of initialization steps when it is created - for example, it needs to set up data observation, compile the template, and create the necessary data bindings. Along the way, it will also invoke some **lifecycle hooks**, which give us the opportunity to execute custom logic. For example, the `created` hook is called after the instance is created:

    var vm = new Vue({
        data:{
            a: 1
        },
        created: function(){
            //'this' points to the vm instance
            console.log("a is: " + this.a)
        }
    })
    // -> "a is : 1"

    There are also other hooks which will be called at different stages of the instance's lifecycle, for example `compiled`, `ready` and `destroyed`. All lifecycle hooks are called with their `this` context pointing to the Vue instance invoking it. Some users may have been wondering whrer the concept of "controllers" lives in the Vue.js world, and the answer is: there are no controllers in Vue.js. Your custom logic for a component would be split among these lifecycle hooks.


##Lifecycle Diagram




#Data Binding Syntax

Vue.js uses a DOM-based templating implementation. This means that all Vue.js templates are essentially valid, parsable HTML enhanced with some special attributes. Keep that in mind, since this makes Vue templates fundamentally different from string-based templates.

##Interpolations(插值)

###Text
The most basic form of data binding is text interpolation using the "Mustache" syntax (double curly braces):

    <span>Message: {{msg}} </span>
    
The mustache tag will be replaced with the value of the `msg` property on the corresponding data object. It will also be updated whenever the data object's `msg` property changes.

You can also perform one-time interpolations that do not updata on data change:

    <span>This will never change: {{* msg}}</span>

###Raw HTML
The double mustaches interprets the data as plain text, not HTML. In order to output real HTML, you will need to use triple mustaches:

    <div>{{{ raw_html }}}</div>

The contents are inserted as plain HTML - data bindings are ignored. If you need to reuse template pieces, you should use partials.

>Note: Dynamically rendering arbitrary HTML on your website can be very dangerous because it can easily lead to XSS attacks. Only use HTML interpolation on trusted content and never on user-provided content.


###Attributes
Mustaches can also be used inside HTML attributes:

    <div id="item-{{id}}"></div>

Note that attribute interpolations are disallowed in Vue.js directives and special attributes. Don't worry, Vue.js will raise warnings for you when mustaches are used in wrong places.



##Binding Expressions
The text we put inside mustache tags are called binding expressions. In Vue.js, a binding expression consists of a single JavaScript expression optionally followed by one or more filters.

### JavaScript Expressions
So far we've only been binding to simple property keys in our templates. But Vue.js actually supports the full power of JavaScript expressions inside data bindings:

    {{number + 1}}
    {{ok ? "YES" : "NO"}}
    {{message.split(" ").reverse().join(" ")}}

These expressions will be evaluated in the data scope of the owner Vue instance. One restriction is that each binding can only contain **one single expression**, so the following will NOT work:

    <!-- this is a statement, not an expression -->
    {{var a = 1 }}

    <!-- flow control won't work either, use ternary expressions -->
    {{if(ok){ return message }}}

###Filters
Vue.js allows you to append optional "filters" to the end of an expression, denoted by the "pipe" symbol:

    {{ message | capitalize }}

Here we are "piping" the value of the `message` expression through the built-in `capitalize` filter, which is in fact just a JavaScript function that returns the capitalized value. Vue.js provides a number of built-in filters,  and we will talk about how to write your own filters later.

Note that the pipe syntax is not part of JavaScript syntax, therefore you cannot mix filters inside expressions; you can only append them at the end of an expression.

Filters can be chained:

    {{ message | filterA | filterB }}

Filters can also take arguments:

    {{message | filterA "arg1" arg2}}

The filter function always receives the expression's value as the first argument. Quoted arguments are interpreted as plain string, while un-quoted ones will be evaluated as expressions. Here, the plain string `"arg1"` will be passed into the filter as the second argument, and the value of expression `arg2` will be evaluated and passed in as the third argument.



##Directives

Directives are special attributes with the `v-` prefix. Directive attribute values are expected to be binding expressions, so the rules about JavaScript expressions and filters mentioned above apply here as well. A directive's job is to reactively apply special behavior to the DOM when the value of its expression changes. Let's review the example we saw in the introduction:

    <p v-if="greeting">Hello!</p>

Here, the `v-if` directive would remove/insert the `<p>` element based on the truthiness of the value of the expression `greeting`.

###Arguments
Some directives can take an "argument", denoted by a colon after the directive name. For example, the `v-bind` directive is used to reactively update an HTML attribute:

    <a v-bind:href="url"></a>

Here `href` is the argument, which tells the `v-bind` directive to bind the element's `href` attribute to the value of the expression "url". You may have noticed this achieves the same result as an attribute interpolation using `href="{{url}}` : that is correct, and in fact, attribute interpolations are translated into `v-bind` bindings internally.

Another example is the `v-on` directive, which listens to DOM events:

    <a v-on:click="doSomething">

Here the argument is the event name to listen to. We will talk about event handling in more detail too.

###Modifiers(修饰符)
Modifiers are special postfixes denoted by a dot, which indicate that a directive should be bound in some special way. For example, the `.literal` modifier tells the directive to interpret its attribute value as a literal string rather than an expression:

    <a v-bind:href.literal="/a/b/c"></a>

Of course, this seems pointless because we can just do `href="/a/b/c"` instead of using a directive. The example here is just for demonstrating the syntax. We will see more practical uses of modifiers later.


##Shorthands
The `v-` prefix serves as a visual cue for identifying Vue-specific attributes in your templates.

###v-bind shorthand

    <!-- full syntax -->
    <a v-bind:href="url"></a>
    <!-- shorthand -->
    <a :href="url"></a>

###v-on Shorthand

    <!-- full syntax -->
    <a v-on:click="doSomething"></a>
    <!-- shorthand -->
     <a @click="doSomething"></a>




#Computed Properties
In-template expressions(在模板中绑定表达式) are very convenient, but they are really meant for simple operations only. Templates are meant to describe the structure of your view.
Putting too much logic into your templates can make them bloated and hard to maintain. This is why Vue.js limits binding expressions to one expression only. For any logic that requires more than one expression, you should use a compued property.

###basic example

    <div id="example">
        a={{a}}, b={{b}}
    </div>

    var vm = new Vue({
        el:"#example",
        data:{
            a:1
        },
        computed:{
            //a computed getter
            b: function(){
                // `this` points to the vm instance
                return this.a + 1
            }
        }
    })

Result:
    
    a=1,b=2

Here we have declared a computed property `b`. The function we provided will be used as the getter function for the property `vm.b`:

    console.log(vm.b) // ->2
    vm.a=2
    console.log(vm.b) // ->3

You can open the console and play with the example vm yourself. The value of `vm.b` is always dependent on the value of `vm.a`.


### Computed Property  vs  .$watch
Vue.js does provide an API method called `$watch` that allows you to observe data changes on a Vue instance. When you have some data that needs to change based on some other data, it is tempting to use `$watch` - especially if you are coming from an AngularJS background.
However, it is often a better idea to use a computed property rather than an imperative `$watch` callback. Consider this example:

    //HTML
    <div id="demo">{{fullName}}</div>

    //JS
    var vm = new Vue({
        el:"#demo",
        data:{
            firstName:"Foo",
            lastName:"Bar",
            fullName:"Foo Bar"
        }
    })
    vm.$watch('firstName', function(val){
        this.fullName = val + ' ' + this.lastName
    })
    vm.$watch('lastName', function(val){
        this.fullName = this.firstName + ' ' + val
    })

The above code is imperative and repetitive. Compare it with a computed property version:

    var vm = new Vue({
        el:"#demo",
        data:{
            firstName:"Foo",
            lastName:"Bar"
        },
        computed:{
            fullName: function(){
                return this.firstName + ' ' + this.lastName
            }
        }
    });

Much better.


###Computed Setter
Computed properties are by default getter-only, but you can also provide a setter when you need it:

    //...
    computed:{
        fullName:{
            get:function(){
                return this.firstName + " " + this.lastName;
            },
            set:function(newValue){
                var names = newValue.split(' ');
                this.firstName = name[0];
                this.lastName = name[names.length - 1]
            }
        }
    }
    //...


Now when you call `vm.fullName = "John Doe`, the setter will be invoked and `vm.firstName` and `vm.lastName` will be updated accordingly.

