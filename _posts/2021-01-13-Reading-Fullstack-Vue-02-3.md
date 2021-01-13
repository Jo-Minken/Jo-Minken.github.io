---
layout: post
title: "Reading: Fullstack Vue Part2 03"
date: 2021-01-13
categories: [Vue]
---

A note taking from Fullstack Vue.<br>

Covers: Part II: Vuex and Routing
- VI - Form Handling

## VI - Form Handling
### Basic Button
```javascript
# form_handling/02-basic-button/main.js

methods: {
    onButtonClick(evt) {
        const button = evt.target;
        console.log(`The user clicked ${button.name}: ${button.value}`);
    }
}
```
---
```javascript
template: `
    <div>
        <button @click="onButtonClick"
            name="button-hoodie"
            value="fullstack-hoodie"
            class="ui button">Hoodie</button>
        <button @click="onButtonClick"
            name="button-tee"
            value="fullstack-tee"
            class="ui button">Tee</button>
        <button @click="onButtonClick"
            name="button-fitted-cap"
            value="fullstack-fitted-cap"
            class="ui button">Fitted Cap</button>
        <button @click="onButtonClick"
            name="button-jacket"
            value="fullstack-jacket"
            class="ui button">Jacket</button>
    </div>`,
```

### Basic Input
```javascript
# form_handling/03-basic-input/main.js

const InputForm = {
template: `
    <div class="input-form">
        <form @submit="submitForm" class="ui form">
            <div class="field">
                <input ref="newItem" type="text" placeholder="Add an item!">
            </div>
            <button class="ui button">Submit</button>
        </form>
    <div>`
}
new Vue({
    el: '#app',
    components: {
        'input-form': InputForm
    }
})
```

`@submit` event handler is called either by clicking the Submit button, or by pressing enter/return while the form has focus.

#### Accessing DOM elements with `$refs`
Vue allows us to use `refs` (references) to easily access a DOM element in a component.

```javascript
# form_handling/03-basic-input/main.js
methods: {
    submitForm(evt) {
        // prevent the browser’s default action of submitting the form
        evt.preventDefault();
        console.log(this.$refs.newItem.value)
    }
}
```

#### Multiple Fields
Form elements Template:
```html
<div class="field">
    <label>New Item</label>
    <input v-model="fields.newItem" type="text" placeholder="Add an item!" />
</div>
<div class="field">
    <label>Email</label>
    <input v-model="fields.email" type="text" placeholder="What's your email?" />
</div>
<div class="field">
    <label>Urgency</label>
    <select v-model="fields.urgency" class="ui fluid search dropdown">
        <option disabled value="">Please select one</option>
        <option>Nonessential</option>
        <option>Moderate</option>
        <option>Urgent</option>
    </select>
</div>
<div class="field">
    <div class="ui checkbox">
        <input v-model="fields.termsAndConditions" type="checkbox" />
        <label>I accept the terms and conditions</label>
    </div>
</div>
```
---
```javascript
# form_handling/07-basic-form-validation/main.js

data() {
    return {
        fields: {
            newItem: '',
            email: '',
            urgency: '',
            termsAndConditions: false
        },
        items: []
    }
},
```

### Validations
Validation can be both on the level of the <u>individual field</u> and on <u>the form</u> as a whole.

#### Add fieldErrors
---
```javascript
# form_handling/07-basic-form-validation/main.js

data() {
    return {
        fields: {
            newItem: '',
            email: '',
            urgency: '',
            termsAndConditions: false
        },
        fieldErrors: {
            newItem: undefined,
            email: undefined,
            urgency: undefined,
            termsAndConditions: undefined
        },
        items: []
    }
},
```
---
```html
<!-- New Item Field -->
<div class="field">
    <label>New Item</label>
    <input v-model="fields.newItem" type="text" placeholder="Add an item!" />
    <span style="color: red">{% raw %}{{ fieldErrors.newItem }}{% endraw %}</span>
</div>

<!-- Email Field -->
<div class="field">
    <label>Email</label>
    <input v-model="fields.email" type="text" placeholder="What's your email?" />
    <span style="color: red">{% raw %}{{ fieldErrors.email }}{% endraw %}</span>
</div>

<!-- Urgency Field -->
<div class="field">
    <label>Urgency</label>
    <select v-model="fields.urgency" class="ui fluid search dropdown">
        <option disabled value="">Please select one</option>
        <option>Nonessential</option>
        <option>Moderate</option>
        <option>Urgent</option>
    </select>
    <span style="color: red">{% raw %}{{ fieldErrors.urgency }}{% endraw %}</span>
</div>

<!-- Terms and conditions checkbox -->
<div class="field">
    <div class="ui checkbox">
        <input v-model="fields.termsAndConditions" type="checkbox" />
        <label>I accept the terms and conditions</label>
        <span style="color: red">{% raw %}{{ fieldErrors.termsAndConditions }}{% endraw %}</span>
    </div>
</div>
```

#### validateForm
```javascript
# form_handling/07-basic-form-validation/main.js

methods: {
    submitForm(evt) {
        ...
    },
    validateForm(fields) {
        const errors = {};
        if (!fields.newItem) errors.newItem = "New Item Required";
        if (!fields.email) errors.email = "Email Required";
        if (!fields.urgency) errors.urgency = "Urgency Required";
        if (!fields.termsAndConditions) {
            errors.termsAndConditions = "Terms and conditions have to be approved";
        }

        if (fields.email && !this.isEmail(fields.email)) {
            errors.email = "Invalid Email";
        }
        return errors;
    },
    isEmail(email) {
        const re = /\S+@\S+\.\S+/;
        return re.test(email);
    }
}
```

FYR: [validator.js](https://github.com/validatorjs/validator.js) - A library of string validators and sanitizers.

#### submitForm
```javascript
# form_handling/07-basic-form-validation/main.js

submitForm(evt) {
    evt.preventDefault();

    this.fieldErrors = this.validateForm(this.fields);
    if (Object.keys(this.fieldErrors).length) return;

    this.items.push(this.fields.newItem);
    this.fields.newItem = '';
    this.fields.email = '';
    this.fields.urgency = '';
    this.fields.termsAndConditions = false;
},
```

#### Field Validation
Field level validations are often important for two reasons:
- A field could check the format of its input while the user types/selects in real-time.
- When the field incorporates its validation error message, it frees the parent form from having to keep track of it.

Introduce two new field level validations:
- The new item input has to be <u>under twenty characters</u>.
- Urgency level has to be either **Moderate** or **Urgent**.

##### character-length limit
```javascript
# form_handling/08-basic-field-validation/main.js

computed: {
    isNewItemInputLimitExceeded() {
        return this.fields.newItem.length >= 20;
    }
}
```
---
```html
<div class="field">
    <label>New Item</label>
    <input v-model="fields.newItem" type="text" placeholder="Add an item!" />
    
    <span style="float: right">{% raw %}{{ fields.newItem.length }}{% endraw %}/20</span>
    <span style="color: red">{% raw %}{{ fieldErrors.newItem }}{% endraw %}</span>
    <span v-if="isNewItemInputLimitExceeded" style="color: red; display: block">
        Must be under twenty characters
    </span>
</div>
```

##### urgency level validation
```javascript
# form_handling/08-basic-field-validation/main.js

computed: {
    isNewItemInputLimitExceeded() {
        return this.fields.newItem.length >= 20;
    },
    isNotUrgent() {
        return this.fields.urgency === 'Nonessential';
    }
}
```
---
```html
<div class="field">
    <label>Urgency</label>
    <select v-model="fields.urgency" class="ui fluid search dropdown">
        <option disabled value="">Please select one</option>
        <option>Nonessential</option>
        <option>Moderate</option>
        <option>Urgent</option>
    </select>
    <span style="color: red">{% raw %}{{ fieldErrors.urgency }}{% endraw %}</span>
    <span v-if="isNotUrgent" style="color: red; display: block">
        Must be moderate to urgent
    </span>
</div>
```

Take an extra step and prevent the user from submitting by **disabling the submit button** when either of the field error validations is present by setting the value of `disabled prop`.
```html
<button :disabled="isNewItemInputLimitExceeded || isNotUrgent"
        class="ui button">
    Submit
</button>
```

##### Computed Caching vs Methods
We could achieve the same result using `methods` to dictate validations but **computed properties are cached** and will **only reevaluate when its dependencies have changed**.<br>
Method invocations will **always run when component re-rendering happens** which **can be expensive** if the method functionality performs a lot of computations.

FYR: [Vue.js - Computed Caching vs Methods](https://vuejs.org/v2/guide/computed.html#Computed-Caching-vs-Methods)

### Async Persistence
We will use `localStorage` to store the data and cover three aspects of persistence: saving, loading, and handling errors.

The `localStorage` API allows you to read and write to a *key-value store* in the user’s browser. Items stored in localStorage have no expiry.
```javascript
// store items to localStorage with setItem()
localStorage.setItem('gas', 'pop');

// retrieve items with getItem()
localStorage.getItem('gas');
```

Make a few changes to our component:
1. Modify `data()` to keep track of persistence status.(saving, loading, or encounting errors)
2. Make a request to get any previously persisted data and load it into `data()`.
3. Update `submitForm() event handler` to trigger a save.
4. Change our component template so that the Submit button reflects the current save status and prevents the user from performing an unwanted action like a double-save, as well as display a loading indicator in the items list when data is still being fetched.

#### Modify `data()`
Introduce two new properties, `loading` and `saveStatus`, for the component to keep track of.<br>
`saveStatus` have four possible values: `READY`, `SAVING`, `SUCCESS` and `ERROR`.

```javascript
# form_handling/09-remote-persist/main.js

data() {
    return {
        ...

        loading: false,
        saveStatus: 'READY'
    }
},
```

#### 1. and 2. data request when instance created
Interact with an `apiClient object` to persist and retrieve data.
```javascript
# form_handling/09-remote-persist/main.js

let apiClient = {
    loadItems: function () {
        return {
            then: function (callback) {
                setTimeout(() => {
                    callback(JSON.parse(localStorage.items || '[]'));
                }, 1000);
            },
        };
    },
    saveItems: function (items) {
        const success = !!(this.count++ % 2);
        return new Promise((resolve, reject) => {
            setTimeout(() => {
                if (!success) return reject({ success });
                localStorage.items = JSON.stringify(items);
                return resolve({ success });
            }, 1000);
        });
    },
    count: 1,
}
```

Request any previously saved data when Vue instance is created:
```javascript
# form_handling/09-remote-persist/main.js

created() {
    this.loading = true,
    apiClient.loadItems().then((items) => {
        this.items = items;
        this.loading = false;
    });
},
```

*Safari workaround*<br>
Safari may throw a `SecurityError` (DOM Exception 18) when localStorage is attempting to be accessed through a simple HTML file.<br>
To work around this, disable local file restrictions with **Develop > Disable Local File Restrictions**.

#### 3. data update after submitForm()
```javascript
# form_handling/09-remote-persist/main.js

submitForm(evt) {
    evt.preventDefault();

    this.fieldErrors = this.validateForm(this.fields);
    if (Object.keys(this.fieldErrors).length) return;

    const items = [...this.items, this.fields.newItem];

    this.saveStatus = 'SAVING';
    apiClient.saveItems(items)
    .then(() => {
        this.items = items;
        this.fields.newItem = '';
        this.fields.email = '';
        this.fields.urgency = '';
        this.fields.termsAndConditions = false;
        this.saveStatus = 'SUCCESS';
    })
    .catch((err) => {
        console.log(err);
        this.saveStatus = 'ERROR';
    });
},
```

#### 4. Modify Template to reflect the saveStatus
Show saving status in submit button:
```html
<button v-if="saveStatus === 'SAVING'"
        disabled class="ui button">
    Saving...
</button>

<button v-if="saveStatus === 'SUCCESS'"
        :disabled="isNewItemInputLimitExceeded || isNotUrgent"
        class="ui button">
    Saved! Submit another
</button>

<button v-if="saveStatus === 'ERROR'"
        :disabled="isNewItemInputLimitExceeded || isNotUrgent"
        class="ui button">
    Save Failed - Retry?
</button>

<button v-if="saveStatus === 'READY'"
        :disabled="isNewItemInputLimitExceeded || isNotUrgent"
        class="ui button">
    Submit
</button>
```

UI improvement: show loading indicator within the item list by [Sementic UI](https://semantic-ui.com/)
```html
<div class="ui segment">
    <h4 class="ui header">Items</h4>
    <ul>
        <div v-if="loading" class="ui active inline loader"></div>
        <li v-for="item in items" class="item">{% raw %}{{ item }}{% endraw %}</li>
    </ul>
</div>
```

### Vuex
Refactor the app to work within a larger app using Vuex.

```javascript
# form_handling/10-vuex-app/store.js

const state = {}
const mutations = {}
const actions = {}
const getters = {}

window.store = new Vuex.Store({
    state,
    mutations,
    actions,
    getters
})
```
---
```javascript
# form_handling/10-vuex-app/main.js

// Add sore object to Vue instance
new Vue({
    el: '#app',
    store,
    components: {
        'input-form': InputForm
    }
})
```

#### Vuex state
```javascript
# form_handling/10-vuex-app/store.js

/* 
 * === state ===
 * Leave component data() object to involve only 
 * - fieldErrors
 * - loading
 * - saveStatus 
 */

const state = {
    fields: {
        newItem: '',
        email: '',
        urgency: '',
        termsAndConditions: false
    },
    items: []
}
```
---
```javascript
# form_handling/10-vuex-app/store.js

data() {
    return {
        fieldErrors: {
            newItem: undefined,
            email: undefined,
            urgency: undefined,
            termsAndConditions: undefined
        },
        loading: false,
        saveStatus: 'READY'
    }
},
```

Bind the `store data` with the appropriate input fields for vuex.

```javascript
# form_handling/10-vuex-app/store.js

template: `
    ...
    <input v-model="newItem" type="text"
            placeholder="Add an item!" />
    ...
`,
computed: Vuex.mapGetters({
    newItem: 'newItem',
})
```

We already have the `v-model` directive set-up and **it doesn't work well with Vuex state**.<br>
Because `v-model` aims to `directly mutate the state property` it bounds to.

Solution 1(standard): Bind the value of the input to the `data property` and listen for any changes in the input.<br>
Invoke an action/mutation when a change is made.

```javascript
# form_handling/10-vuex-app/index.js

template: `
    ...
    <input :value="newItem" @input="onInputChange"
            type="text" placeholder="Add an item!" />
    ...
`,
computed: Vuex.mapGetters({
    newItem: 'newItem',
}),
methods: {
    onInputChange(evt) {
        this.$store.commit('UPDATE_INPUT', evt.target.value);
    }
}
```

Solution 2: Keep `v-model` binding but instead using a two-way computed property, approach with `get()` and `set()`.

```javascript
# form_handling/10-vuex-app/index.js

template: `
    ...
    <input v-model="newItem" type="text"
            placeholder="Add an item!" />
    ...
`,
computed: {
    newItem: {
        get() {
            return this.$store.state.fields.newItem;
        },
        set(val) {
            this.$store.commit('UPDATE_INPUT', value);
        }
    }
}
```

#### Vuex mutations
```javascript
# form_handling/10-vuex-app/store.js

const mutations = {
    UPDATE_NEW_ITEM (state, payload) {
        state.fields.newItem = payload;
    },
    UPDATE_EMAIL (state, payload) {
        state.fields.email = payload;
    },
    UPDATE_URGENCY (state, payload) {
        state.fields.urgency = payload;
    },
    UPDATE_TERMS_AND_CONDITIONS (state, payload) {
        state.fields.termsAndConditions = payload;
    },
    UPDATE_ITEMS (state, payload) {
        state.items = payload
    },
    CLEAR_FIELDS () {
        state.fields.newItem = '';
        state.fields.email = '';
        state.fields.urgency = '';
        state.fields.termsAndConditions = false
    }
}
```

#### Vuex getters
```javascript
# form_handling/10-vuex-app/store.js

const getters = {
    newItem: state => state.fields.newItem,
    newItemLength: state => state.fields.newItem.length,
    isNewItemInputLimitExceeded: state => state.fields.newItem.length >= 20,
    email: state => state.fields.email,
    urgency: state => state.fields.urgency,
    isNotUrgent: state => state.fields.urgency === 'Nonessential',
    termsAndConditions: state => state.fields.termsAndConditions,
    items: state => state.items
}
```
---
```javascript
# form_handling/10-vuex-app/index.js

computed: Vuex.mapGetters({
    newItem: 'newItem',
    newItemLength: 'newItemLength',
    isNewItemInputLimitExceeded: 'isNewItemInputLimitExceeded',
    email: 'email',
    urgency: 'urgency',
    isNotUrgent: 'isNotUrgent',
    termsAndConditions: 'termsAndConditions',
    items: 'items'
}),
```

Update input value when input changed:
```html
<!-- New Item Input -->
<input :value="newItem" @input="onInputChange"
        name="NEW_ITEM" type="text" placeholder="Add an item!" />

<!-- Email Input -->
<input :value="email" @input="onInputChange"
        name="EMAIL" type="text" placeholder="What's your email?" />

<!-- Urgency Dropdown -->
<select :value="urgency" @change="onInputChange"
        name="URGENCY" class="ui fluid search dropdown">
    <option disabled value="">Please select one</option>
    <option>Nonessential</option>
    <option>Moderate</option>
    <option>Urgent</option>
</select>

<!-- Terms and Conditions Checkbox -->
<input :checked="termsAndConditions" @change="onInputChange"
        name="TERMS_AND_CONDITIONS" type="checkbox" />
```
---
```javascript
# form_handling/10-vuex-app/index.js

onInputChange(evt) {
    onst element = evt.target;
    onst value =
        element.name === "TERMS_AND_CONDITIONS"
            ? element.checked
            : element.value;
    this.$store.commit(`UPDATE_${element.name}`, value);
},
```

#### Vuex actions
Create the actions that get the items list from the server and persist the newly added item to the server upon a successful form submit.

```javascript
# form_handling/10-vuex-app/store.js

const actions = {
    loadItems (context, payload) {
        const items = payload;
        apiClient.loadItems().then((items) => {
            context.commit('UPDATE_ITEMS', items);
        });
    },
    saveItems (context, payload) {
        const items = payload;
        apiClient.saveItems(items).then(() => {
            context.commit('UPDATE_ITEMS', items);
            context.commit('CLEAR_FIELDS');
        });
    }
}
```

Dispatch the `loadItems` action the moment the component is created:
```javascript
# form_handling/10-vuex-app/main.js

created() {
    this.loading = true;
    this.$store.dispatch('loadItems')
        .then((response) => {
            this.loading = false;
        })
        .catch((error) => {
            console.log(error);
        })
},
```

It won't currently work as intended, because **Vuex actions are asynchronous** as well.<br>
We need to let the dispatch function know when the action is complete by **returning a Promise** and then resolving it.

```javascript
# form_handling/10-vuex-app/store.js

const actions = {
    loadItems (context, payload) {
        return new Promise((resolve, reject) => {
            const items = payload;
            apiClient.loadItems().then((items) => {
                context.commit('UPDATE_ITEMS', items);
                resolve(items);
            }, (error) => {
                reject(error);
            });
        });
    },
    saveItems (context, payload) {
        return new Promise((resolve, reject) => {
            const items = payload;
            apiClient.saveItems(items).then((response) => {
                context.commit('UPDATE_ITEMS', items);
                context.commit('CLEAR_FIELDS');
                resolve(response);
            }, (error) => {
                reject(error);
            });
        });
    }
}
```

The actions now return a `Promise object` to the dispatcher which then either gets resolved or rejected depending on the success of the asynchronous server calls.

Modify the `submitForm()` method to dispatch the saveItems action when
the form is submitted.

```javascript
# form_handling/10-vuex-app/main.js

submitForm(evt) {
    evt.preventDefault();

    this.fieldErrors = this.validateForm(this.$store.state.fields);
    if (Object.keys(this.fieldErrors).length) return;

    const items = [
        ...this.$store.state.items,
        this.$store.state.fields.newItem
    ];

    this.saveStatus = 'SAVING';
    this.$store.dispatch('saveItems', items)
        .then(() => {
            this.saveStatus = 'SUCCESS';
        })
        .catch((err) => {
            console.log(err);
            this.saveStatus = 'ERROR';
        });
},
```

### Form Modules
- [vee-validate](https://vee-validate.logaretm.com/v3)<br>
  A simple plugin that focuses on input validation on the template (HTML) itself.
- [vuelidate](https://vuelidate.js.org/)<br>
  A lightweight library that decouples validations from the template and instead require them declared within a component’s/instance’s model.
- [vue-multiselect](https://vue-multiselect.js.org/)<br>
  With no jQuery dependency, vue-multiselect allows for a customizable select box with support for single select, multiple select, tagging, dropdowns, filtering, etc.
- [vue-form-generator](https://github.com/vue-generators/vue-form-generator/)<br>
  Sketch out the entire form in a JSON schema.
