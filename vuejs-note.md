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

