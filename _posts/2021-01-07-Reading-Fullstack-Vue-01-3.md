---
layout: post
title: "Reading: Fullstack Vue Part1 03"
date: 2021-01-07
categories: [Vue]
---

A note taking from Fullstack Vue.<br>

Covers: Part I: Basics
- III - Custom Events

## III - Custom Events
### JavaScript Custom Events
Example:
```javascript
// Creating the event
let event = new CustomEvent("customEvent", {
    detail: { book: "FullStack Vue " }
});

// Dispatching the event
element.dispatchEvent(event);

// Listening for the event
 element.addEventListener("customEvent", e =>
    console.log("This book is " + e.detail.book)
 );
```

### Vue Custom Events
Vue's event system is primarily used for **communication between components**, as opposed to communication between DOM nodes.

### Example: Note-Taking app
```javascript
# main.js

const inputComponent = {
    template: `<input
                :placeholder="placeholder"
                v-model="input"
                @keyup.enter="monitorEnterKey"
                class="input is-small" type="text" />`,
    props: ['placeholder'],
    data () {
        return {
            input: ''
        }
    }
}

new Vue({
    // ...
    data: {
        notes: [],
        timestamps: [],
        placeholder: 'Enter a note'
    },
    // ...
})
```

Vue custom events are triggered using `$emit` while specifying the name of the custom event:
```javascript
this.$emit('nameOfEvent');
```

The `$emit` function allows for a second optional argument that allows the caller to pass arbitrary values along with the emitted event:
```javascript
this.$emit('nameOfEvent', {
    data: {
        book: 'FullStack Vue'
    }
});
```

The `v-on directive` must be used in parent templates to listen to events emitted by children.

Add `monitorEnterKey` method to the inputComponent:
```javascript
# main.js
const inputComponent = {
    // ...,
    methods: {
        monitorEnterKey() {
            this.$emit('add-note', {
                note: this.input,
                timestamp: new Date().toLocaleString()
            });
            this.input = '';
        }
    }
}
```

Since the `add-note custom event` is triggered within the `input-component`, we have to create the event listener on the `<input-component></input-component>` template. <br>
This event listener can trigger a method in the root instance to update the parent.

```html
# index.html

<input-component @add-note="addNote" :placeholder="placeholder"></input-component>
```

Create the `addNote()` method in root instance:
```javascript
# main.js

new Vue({
// ...
    methods: {
        addNote(event) {
            this.notes.push(event.note);
            this.timestamps.push(event.timestamp);
        }
    },
// ...
});
```
---
```html
# index.html
<div class="columns">
    <div class="column has-text-centered">
        <strong>Notes</strong>
        <div v-for="note in notes" class="notes">
            {% raw %}{{ note }}{% endraw %}
        </div>
    </div>
    <div class="column has-text-centered">
        <strong>Timestamp</strong>
        <div v-for="timestamp in timestamps" class="timestamps">
            {% raw %}{{ timestamp }}{% endraw %}
        </div>
    </div>
</div>
```

#### Use Event Bus in app
New a feature of showing the number  of notes that has been entered:
- way 1: Specify a new data property `noteCount` on the root instance which gets incremented with the `addNote()` method and pass it down as props to note-count-component.
- way 2: Directly trigger an event from input-component, to increment a `noteCount` property within note-count-component. (Event Bus)

An `Event Bus` is a `Vue instance` that is used to enable isolated components to subscribe and publish custom events between each other.

In Vue, an Event Bus can be created by instantiating a new Vue instance.

```javascript
import Vue from 'vue';
const EventBus = new Vue();
export default EventBus;

// global event bus
window.EventBus = new Vue();

// Emitting events
EventBus.$emit('nameOfEvent', {
    data: {
        book: 'FullStack Vue'
    }
});

// Listening on events
EventBus.$on("nameOfEvent", e =>
    console.log("This book is " + e.data.book)
);
```

Refactoring `this.$emit` to `Eventbus.$emit`:
```javascript
# main.js
const EventBus = new Vue();
const inputComponent = {
    // ...,
    methods: {
        monitorEnterKey() {
            EventBus.$emit('add-note', {
                note: this.input,
                timestamp: new Date().toLocaleString()
            });
            this.input = '';
        }
    }
}
```

Introduce the `EventBus.$on()` call method somewhere in root instance to listen on and subsequently call the `addNote()` method when the event is triggered.<br>
We'll implement this callback within the component's **created** lifecycle hook.

#### Vue instance lifecycle
The lifecycle refers to the time an instance has been created, mounted, updated, and destroyed.

```javascript
new Vue({
    el: '#app',
    data: {
        count: 'FullStack Vue'
    },
    updated () {
        console.log('The count is ' + this.count);
    },
    methods: {
        updateCount () {
            his.count++;
        }
    }
});
```

FYR: [Lifecycle Diagram - Vue.js Document](https://vuejs.org/v2/guide/instance.html#Lifecycle-Diagram)

![Vue Instance Lifecycle](https://vuejs.org/images/lifecycle.png)

Since the **created** hook refers to the moment of creation, it's the perfect time to set up the event listener (`EventBus.$on`).

Add `created hook` to the Vue instance:

```javascript
# main.js

new Vue({
    // ...,
    created() {
        EventBus.$on('add-note', event => this.addNote(event));
    },
    // ...,
});
```

Remove the event listener that's been declared on `<input-component></input-component>`:

```html
# index.html

<input-component :placeholder="placeholder"></input-component>
```

#### note-count-component
```html
# index.html

<div id="app">
    <div class="notes-section">
        <!-- The notes-section -->
    </div>
    <note-count-component></note-count-component>
</div>
```
---
```javascript
# main.js
const EventBus = new Vue();

const inputComponent = {
    // ...
};

const noteCountComponent = {
    template: `<div class="note-count">
                    Note count: <strong>{% raw %}{{ noteCount }}{% endraw %}
                </strong></div>`,
    data () {
        return {
            noteCount: 0
        }
    },
    created() {
        // when add-note event is triggered
        // increase noteCount property value by one
        EventBus.$on('add-note', event => this.noteCount++);
    }
};

new Vue({
    // ...,
    components: {
        'input-component': inputComponent,
        // register the component
        'note-count-component': noteCountComponent
    }
});
```

### Custom events and managing data
In much larger Vue applications, the global Event Bus can be extracted to its own separate file (e.g.
event-bus.js) and imported whenever needed. <br>
The Event Bus itself can also be modified to have more internal logic to communicate with a server or a real-time backend.

A global Event Bus is not the recommended way of managing data between components.<br>
Having a large number of event emitters/listeners sporadically placed in your components can make code frustrating to maintain and can become a source of bugs.

Vuex is the preferred method for managing data within applications.
