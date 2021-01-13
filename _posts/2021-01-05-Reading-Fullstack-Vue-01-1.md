---
layout: post
title: "Reading: Fullstack Vue Part1 01"
date: 2021-01-05
categories: [Vue]
---

A note taking from Fullstack Vue.<br>

Covers: Part I: Basics
- I - Your first Vue.js Web Application

# Part I: Basics
## I - Your first Vue.js Web Application
We're going to build a simple voting application (named UpVote!) that takes inspiration from popular social feed websites like Reddit and Hacker News.

### Development environment setup
With Chrome, Vue provides an incredibly useful extension, [Vue.js devtools](https://github.com/vuejs/vue-devtools) that simplifies debugging of Vue applications.

To make the Vue devtools work for pages opened via `file:// protocol`, you'll need to check **"Allow access to file URLs"** for the extension in Chrome's extension manager.

### Making the view data-driven
#### The Vue Instance
- The Vue instance is the **starting point** of all Vue applications
- A Vue instance accepts an `options object` which can contain details of the instance
- Root level Vue instances allow us to reference the DOM with which the instance is to be mounted/attached to

Create a Vue instance using `Vue` function:
```javascript
# main.js
new Vue({
    el: '#app',
    // connect data object with seed.js
    data: {
        submissions: Seed.submissions
    }
});
```

#### Data binding
- Use the Mustache syntax which is denoted by double curly braces **{% raw %}{{}}{% endraw %}**
- Binding HTML attributes(like href, id, src): **v-bind** attribute (known as a Vue directive)

Mustache syntax and v-bind:
```html
# index.html
<img class="image is-64x64" v-bind:src="submissions[0].submissionImage">

<a v-bind:href="submissions[0].url" class="has-text-info">
    {% raw %}{{ submissions[0].title }}{% endraw %}
</a>
```

### List rendering: `v-for` directive
The **v-for** directive is used to render a list of items based on a data source.

It's a common practice to specify a `key attribute` for every iterated element within a rendered v-for list to create unique bindings for each node's identity.

```html
# index.html
<article v-for="submission in submissions" v-bind:key="submission.id" class="media">
    <!-- Rest of the submission template -->
</article>
```

### Sorting: Computed properties
Computed properties are used to handle complex calculations of information that need to be displayed in the view.

```javascript
# main.js
new Vue({
    el: '#app',
    data: {
        submissions: Seed.submissions
    },
    computed: {
        sortedSubmissions () {
            return this.submissions.sort((a, b) => {
                return b.votes - a.votes
            });
        }
    }
});
```

Within a Vue instance, data object is referencable with `this`.

```html
# index.html
<article v-for="submission in sortedSubmissions" v-bind:key="submission.id" class="media">
    <!-- Rest of the submission template -->
</article>
```

### Event handling: `v-on` directive 
The v-on directive is used to create event listeners within the DOM.

In Vue, we can use the `v-on:click` directive to implement a click handler.<br>
We'll set this click event handler to call an **upvote(submission.id) method** whenever the up-vote icon is clicked.

```html
# index.html
<div class="media-right">
    <span class="icon is-small" v-on:click="upvote(submission.id)">
        <i class="fa fa-chevron-up"></i>
        <strong class="has-text-info">{% raw %}{{ submission.votes }}{% endraw %}</strong>
    </span>
</div>
```

A `methods property` exists in a Vue instance to allow us to define methods bound to that instance.<br>
Methods behave like normal JavaScript functions and are only evaluated when explicitly called.

```javascript
new Vue({
    el: '#app',
    data: {
        submissions: Seed.submissions
    },
    computed: {
        // ...,
    },
    methods: {
        upvote(submissionId) {
            // using the native JavaScript find() method to locate the submission object
            const submission = this.submissions.find(
                submission => submission.id === submissionId
            );
            submission.votes++;
        }
    }
});
```

- React: state is `immutable`
- Vue: state is `reactive`

### Class bindings: `v-bind` directive
Add a conditional class that displays a blue border around a submission when submission reaches 20 votes.

```html
# index.html
<article v-for="submission in sortedSubmissions" 
  v-bind:key="submission.id" 
  class="media"
  v-bind:class="{ 'blue-border': submission.votes >= 20 }">
    <!-- Rest of the submission template -->
</article>
```

### Components
Components are intended to be `self-contained modules` since we can group markup (HTML), logic (JS) and even styles (CSS) within them.

Let's create a new component for our application:
- The parent component which encompasses all the separate submissions - this will be the existing Vue instance.
- The new submission component which represents a single submission.

The standard method for creating a global Vue component is handled by using the `Vue.component` constructor method:
```javascript
Vue.component('submission-component', {
    // options
});
```

Let's assign our newly created submission-component to a constant variable and register it as part of the component option in our Vue instance.

```javascript
# main.js

// Vue components are Vue instances
const submissionComponent = {
    template: '<div>Hello World!</div>'
};
new Vue({
    // ...
});
```
In Vue instances, a template option exists that allows us to define the template of that instance.

FYR: [ES6's template literals](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals)

Update the submissionComponent:
```javascript
const submissionComponent = {
    template:
    `<div style="display: flex; width: 100%">
        <figure class="media-left">
            <img class="image is-64x64" v-bind:src="submission.submissionImage">
        </figure>
        <div class="media-content">
            <div class="content">
                <p>
                    <strong>
                        <a v-bind:href="submission.url" class="has-text-info">
                            {% raw %}{{ submission.title }}{% endraw %}
                        </a>
                        <span class="tag is-small">#{% raw %}{{ submission.id }}{% endraw %}</span>
                    </strong>
                    <br>
                    {% raw %}{{ submission.description }}{% endraw %}
                    <br>
                    <small class="is-size-7">
                        Submitted by:
                        <img class="image is-24x24" v-bind:src="submission.avatar">
                    </small>
                </p>
            </div>
        </div>
        <div class="media-right">
            <span class="icon is-small" v-on:click="upvote(submission.id)">
                <i class="fa fa-chevron-up"></i>
                <strong class="has-text-info">{% raw %}{{ submission.votes }}{% endraw %}</strong>
            </span>
        </div>
    </div>`
};
```
- The submission object in this template is currently undefined.<br>
  When this component is declared, we have to pass data from the parent component down to this child component.<br>
  Use `Vue props` to pass data from the root instance to this component.
- The `upvote()` click listener method needs to be mapped to a method within the submissionComponent.
  Transfer the upvote() method from the Vue instance to this component.

```html
# index.html

<article v-for="submission in sortedSubmissions"
    v-bind:key="submission.id"
    class="media"
    v-bind:class="{ 'blue-border': submission.votes >= 20 }">
    <submission-component></submission-component>
</article>
```

Define submisson-component in the `main.js` file:
```javascript
# main.js
new Vue({
// ...,
components: {
    'submission-component': submissionComponent
}
});
```

#### Props
In Vue, `props` are attributes that need to be given a value in the parent component and have to be explicitly declared in the child component.

The `v-bind directive` is used to bind dynamic values (or objects) as props in a parent instance.

Update the <article> element:
```html
# index.html
<article v-for="submission in sortedSubmissions"
    v-bind:key="submission.id"
    class="media"
    v-bind:class="{ 'blue-border': submission.votes >= 20 }">
    
    <submission-component
        v-bind:submission="submission"
        v-bind:submissions="sortedSubmissions">
    </submission-component>
</article>
```
---
```javascript
# main.js
const submissionComponent = {
    template: // ...omit
    ,
    props: ['submission', 'submissions'],
    methods: {
        upvote(submissionId) {
            const submission = this.submissions.find(
                submission => submission.id === submissionId
            );
            submission.votes++;
        }
    }
};
```

### `v-bind` and `v-on` shorthand syntax
The `v-bind` directive can be shortened with the `:` symbol:
```html
# full syntax
<img v-bind:src="submission.submissionImage" />

# shorthand syntax
<img :src="submission.submissionImage" />
```

The `v-on` directive can be shortened with the `@` symbol:
```html
# full syntax
<span v-on:click="upvote(submission.id)"></span>

# shorthand syntax
<span @click="upvote(submission.id)"></span>
```

### Recap
1. The Vue instance is the starting point of all Vue applications.
   The instance can have options like `data`, `computed` and `methods` and is often mounted to a DOM element.
2. The Mustache syntax can be used for data binding. The `v-bind` directive is used for binding HTML attributes.
3. Vue directives such as `v-for` can be used to manipulate the template based on the data provided.
   The `v-on` directive is used as an event handler to listen to DOM events.
4. Organize Vue apps with components.
