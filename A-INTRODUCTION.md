#VUE.JS: A INTRODUCTION

Vue.js is a library for building web interfaces. Together with some other tools you can also call it a "framework", although it's more like s set of optional tools that work together really well.

##REACTIVITY
>Keeping the state and the view in sync is hard. Or is it?

Let's start with the most basic task: displaying data. Suppose we have a simple object:
    
    var object = {
        message: "Hello world!"
    }

And a template:

    <div id="example">
        {{message}}
    </div>

And here's how we bind the data and the template together with Vue:

    new Vue({
        el: "#example",
        data: object
    })

Vue has converted the object and made it "reactive". When you set `object.message` to something else, the rendered HTML updates automatically.
More importantly, there's no need to worry about calling `$apply` in a timeout, or calling `serState()`, or listening to store events, or creating framework-proprietary observables like `ko.observable()` or `Ember.Object.create()` ... it just works.

Vue also provides computed properties:

    var example = new Vue({
        data: {
            a:1
        },
        computed:{
            b:function(){
                return this.a + 1
            }
        }
    })

    //both a & b are proxied on the created instance.
    example.a // -> 1
    example.b // -> 2
    example.a++
    example.b // ->3

The computed property `b` tracks `a` as a dependency, and is automatically kept in sync.

In addition, POJO-based reactivity makes it trivially easy to integrate with any type of data-source or state management solutions. (https://github.com/vuejs/vue-rx/blob/master/vue-rx.js#L22-L51)


##COMPONENTS
> Ok, the data binding is neat for small demos. What about big apps?

When it comes to structuring complex interfaces, Vue takes an approach that is very similar to React: it's components all the way down. Let's make our example a reusable component:

    var Example = Vue.extend({
        template: "<div>{{message}}</div>",
        data: function(){
            return {
                message: "Hello Vue.js!"
            }
        }
    })

    //register it with the tag <example>
    Vue.component("example", Example)

Now we can use the component in other templates simply as a custom element:

    <example></example>

Components can contain other components, and they form a tree that represents your UI. To make them actually composable, Vue components can also:
    
1. Define how it expects to receive data from its parent using `props`;
2. Emit custom events to trigger actions in parent scope;
3. Compose parent injected content with its own template using <slot>.

We are not going to go into the detail

