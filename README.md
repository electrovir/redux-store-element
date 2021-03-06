[![Published on webcomponents.org](https://img.shields.io/badge/webcomponents.org-published-blue.svg)](https://www.webcomponents.org/element/owner/my-element)
# &lt;redux-store&gt;
A custom element allowing a more declarative use of Redux.

# Introduction
This custom element offers the basic Redux API declaratively. Because this is a web component, it should be compatible with all major front-end JavaScript frameworks and libraries that allow full interaction with the DOM, including [Polymer](https://github.com/Polymer/polymer), [SkateJS](https://github.com/skatejs/skatejs), [Bosonic](https://github.com/bosonic/bosonic), [X-Tag](https://github.com/x-tag/x-tag), [React](https://github.com/facebook/react), [Angular 2](https://github.com/angular/angular), [Angular 1](https://github.com/angular/angular.js/), [Ember](https://github.com/emberjs/ember.js/), vanilla JavaScript, etc.

## Simple and Declarative
Before we begin, I just want to highlight how easy it is to work with this element declaratively:

* Hook up your root reducer (similar to Redux `createStore`): 

  ```
  <redux-store root-reducer="[[rootReducer]]"></redux-store>
  ```
* Dispatch actions (similar to Redux `dispatch`): 

  ```
  <redux-store action="[[action]]"></redux-store>
  ```
* Listen for state changes (similar to Redux `subscribe`): 

  ```
  <redux-store on-statechange="mapStateToThis"></redux-store>
  ```
* Explicitly grab the state, but only if you must, prefer listening for state changes (similar to Redux `getState`):

  ```
  //HTML
  <redux-store id="redux-store-element"></redux-store>
  
  //JS
  const reduxStoreElement = this.querySelector('#redux-store-element');
  const state = reduxStoreElement.getState();
  ```

That is the entirety of the API exposed to you through HTML.

# Installation and Setup
Run the following:
```
bower install --save redux-store-element
npm install --save redux
```

If you support TypeScript files directly on the client, such as if you are using [Zwitterion](https://github.com/lastmjs/zwitterion), import `redux-store.html`:
```
<link rel="import" href="bower_components/redux-store-element/redux-store.html">
```

Otherwise, import `redux-store-dist.html`:
```
<link rel="import" href="bower_components/redux-store-element/redux-store-dist.html">
```

This is a web component, meaning its underlying technologies have not yet been implemented in all browsers. Include this polyfill (automatically installed with this package) to ensure support across all modern browsers:
```
<script src="bower_components/webcomponentsjs/webcomponents-lite.min.js"></script>
```

The following examples are written with Polymer. It shouldn't be too hard to adapt them to other libraries and frameworks, keeping in mind their data-binding systems and DOM interactions:

## Creating the root reducer
At some point before you begin dispatching actions, you need to pass in your root reducer to any `<redux-store></redux-store>` element through the root-reducer attribute. From the example:
```
<link rel="import" href="../../../lib/bower_components/polymer/polymer.html">

<link rel="import" href="../../../src/redux-store.html">
<link rel="import" href="../input-area/input-area.component.html">
<link rel="import" href="../text/text.component.html">

<dom-module id="test-app">
    <template>
        <redux-store root-reducer="[[rootReducer]]"></redux-store>
        <test-input-area></test-input-area>
        <test-text></test-text>
    </template>

    <script>
        Polymer({
            is: 'test-app',
            ready: function() {

                var initialState = {
                    temp: 'initial temp'
                };

                this.rootReducer = function(state, action) {

                    if (!state) {
                        return initialState;
                    }

                    switch(action.type) {
                        case 'CHANGE_TEMP': {
                            var newState = Object.assign({}, state);

                            newState.temp = action.newTemp;

                            return newState;
                        }
                        default: {
                            return state;
                        }
                    };
                };

            }
        });
    </script>
</dom-module>

```

## Subscribing to state changes
If your component needs to listen to state changes, simply pop a `<redux-store></redux-store>` element in and pass a listener function name in for the `statechange` event. From the example:

```
<link rel="import" href="../../../lib/bower_components/polymer/polymer.html">

<link rel="import" href="../../../src/redux-store.html">

<dom-module id="test-text">
    <template>
        <redux-store on-statechange="mapStateToThis"></redux-store>

        <div id="testText">Text from input above will go here</div>
    </template>

    <script>
        Polymer({
            is: 'test-text',
            mapStateToThis: function(e) {
                this.$.testText.innerHTML = e.detail.state.temp;
            }
        });
    </script>
</dom-module>
```

## Dispatching actions
To dispatch from within an element, first bind the action property of the element to the action property on `<redux-store></redux-store>`. When you are ready to dispatch an action, set the action property on the element to the action that you want to dispatch. From the example:

```
<link rel="import" href="../../../src/redux-store.html">

<dom-module id="test-input-area">
    <template>
        <redux-store action="[[action]]"></redux-store>

        <input id="testInput" type="text">
        <button on-click="handleClick">Dispatch</button>
    </template>

    <script>
        Polymer({
            is: 'test-input-area',
            handleClick: function(e) {
                this.action = {
                    type: 'CHANGE_TEMP',
                    newTemp: this.$.testInput.value
                };
            }
        });
    </script>
</dom-module>
```

## Multiple Stores
By default, there is one store for the entire application, meaning that each instance of a `<redux-store></redux-store>` will use the same store. You can however use multiple stores if you've determined it is necessary for your application. Simply reference the store name as follows:

* Hook up your root reducer: `<redux-store root-reducer="[[rootReducer]]" store-name="fooStore"></redux-store>`
* Dispatch actions: `<redux-store action="[[action]]" store-name="fooStore"></redux-store>`
* Listen for state changes: `<redux-store on-statechange="mapStateToThis" store-name="fooStore"></redux-store>`

## Important Things to Know:
* You must pass in your root reducer to any `<redux-store></redux-store>` before you can dispatch actions and receive state changes
* By default there is one store for the entire application. Each instance of a `<redux-store></redux-store>` will use the same store
* The `statechange` event supplies the redux state in the `detail.state` property on the event

## Development
To play with the example, clone the repo, go to the root of the project, and run the following commands:
```
bower install
npm install
npm start
```
Open up to localhost:[whatever port the terminal says]. The port is usually localhost:8080. Go to the example directory and open example/index.html.
