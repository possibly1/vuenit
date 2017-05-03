# vuenit
Vue Unit Test Helpers

[![npm version](https://badge.fury.io/js/vuenit.svg)](https://badge.fury.io/js/vuenit)
[![Build Status](https://travis-ci.org/jackmellis/vuenit.svg?branch=master)](https://travis-ci.org/jackmellis/vuenit)
[![Code Climate](https://codeclimate.com/github/jackmellis/vuenit/badges/gpa.svg)](https://codeclimate.com/github/jackmellis/vuenit)
[![Test Coverage](https://codeclimate.com/github/jackmellis/vuenit/badges/coverage.svg)](https://codeclimate.com/github/jackmellis/vuenit/coverage)

- [vuenit.component](#component)  
- [vuenit.directive](#directive)  
- [vuenit.store](#store)  
- [vuenit.http](#http)  
- [vuenit.cleanUp](#cleanUp)  
- [vuenit.trigger](#trigger)  

## Component  
`vuenit.component(componentDefinition, options)`

The component function creates an instance of a specified component. Plugin values such as $router can be injected in before the component is initialised, and props can also be passed in as if they were real values.  

__vuenit__ will also handle converting `template`s into `render` functions if the component hasn't already been compiled at some point.

The function takes two parameters: `Component` and `Options`:  

### component  
This can either be a string (the name of a pre-registered component) or a component definition object.
```javascript
import component from 'components/myComponent.vue';
vuenit.component(component);

vuenit.component('my-globally-registered-component');
```

### options  
Options should be an object with the following properties, all of which are optional:  

### props  
`{ props : { foo : 'foo', bah : true } }`  

Specify props that should be passed into the component, these should match up to the props used by your component.  

Props are passed in through an intermediary component. All this means is that if you update a prop's value, you will need to wait for Vue's next render cycle before the value is passed into your component:
```javascript
var vm = vuenit.component(component, options);

vm.propsData.myProp = 'changed';

vm.myProp !== 'changed';

vm.$nextTick(() => {
  vm.myProp === 'changed';
});
```
The component instance has a `propsData` property that allow you to change the prop value.


### inject  
`{ inject : { $router : {}, someService : {} } }`  

Properties to inject into the component. These will be attached to the component instance. This uses [vue-inject](https://www.npmjs.com/package/vue-inject) to resolve the injected values and attach them during the **beforeCreate** hook.  

If you pass a function as an injected property, **vuenit** will assume it is a factory function and will return the result of the function:

```javascript
{
  inject : {
    myFactory(){
      return {};
    }
  }
}
```
This also means you can leverage *vue-inject*'s dependency injection:
```javascript
{
  inject : {
    myFactory($timeout, $log){
      return function(m){
        $timeout(() => $log(m), 250);
      };
    }
  }
}
```
However, this feature also means that if you want to inject a function into your component, you must wrap it in another function, otherwise it will be invoked as a factory:
```javascript
{
  inject : {
    $http : $http, // if $http is a function, this will not work
    $http : () => $http // will work
  }
}
```

### store  
`{ store : {} }`  

This is an option to set up a fake vuex-style store for the component. This is simply a convenience wrapper that uses the `vuenit.store()` function (see below) and adds it into the above *inject* option.  
```javascript
{
  store : {
    items : [
      { id : 1 }
    ]
  }
}
```
...is the equivalent of...  
```javascript
{
  inject : {
    $store : vuenit.store({
      items : [
        { id : 1 }
      ]
    })
  }
}
```

### http
`{ http : {} }`  

Injects an object as $http into the component instance. If set to `true`, it will create a http instance using `vuenit.http()`.
```javascript
{
  http : true
}
```
...is the equivalent of...
```javascript
{
  inject : {
    $http : () => vuenit.http()
  }
}
```

### innerHTML  
`{ innerHTML : '' }`

Set the inner html of the component. This is useful if you want to test a component that has slots in it:  
```javascript
{
  innerHTML : '<span slot="component-slot>xxx</span>"'
}
```

### slots
`{ slots : 'default slot' | { slotName : 'html', default : 'default slot' } }`

Allows you to insert slot content into the component.
```js
{
  slots : {
    header : '<h1>Header</h1>',
    default : '<div>Body</div>'
  }
}
```
..is equivalent to..
```js
{
  innerHTML : '<h1 slot="header">Header</h1><div>Body</div>'
}
```

### name  
`{ name : 'my-component' }`

The name of the component. Vuenit requires components to be named. If your component definition does not a have a name property, you can provide one here.  

The `props`, `inject`, and `store` options all become reactive after creating the component, meaning that you can update their values directly and the component instance will update as expected:  

```javascript
var vm = vuenit.component(myComponent, options);
vm.computedFromFoo // 'foo'
options.store.state.foo = 'bah';
vm.computedFromFoo // 'bah'
```

### install
`{ install(Vue, injector){} }`

The component is built using an extended Vue (i.e. with `Vue.extend()`) and an isolated inject (i.e. `injector.spawn()`). The `install` method allows you to configure them both before creating the component.
```javascript
vuenit.component(myComponent, {
  install(Vue, injector){
    Vue.use(somePlugin);
    injector.service('myService', /*...*/);
  }
});
```

### components  
`{ components : { myComponent : '<div/>' } }`

Allows you to replace child components. The components themselves can either be component definitions, or just a string template.
```javascript
vuenit.component(c, {
  components : {
    componentA : '<div>string template</div>',
    componentB : {
      template : '<div>component object</div>'
    },
    componentC : true
  }
});

vuenit.component(c, {
  components : ['componentA', 'componentB', 'componentC']
});
```
If you don't provide a string or object, it will default to a `<div></div>` template.

### stubComponents
`{ stubComponents : true | '<div/>' | {} }`

If true, it will automatically stub all known components. You can specify a template or component definition and this will be used for stubbing components.

### filters
`{ filters : { myFilter : v => v } }`

Allows you to replace filters with stubbed values. If set to true, the filter will just return the original value.
```js
vuenit.component(c, {
  filters : {
    myFilter(v){
      return v.split('').reverse().join('');
    },
    bypassThisFilter : true
  }
});

vuenit.component(c, ['myFilter', 'dateFilter']); // will stub both filters
```

### stubFilters
`{ stubFilters : true | v => v }`

If true, it will bypass all known filters and just return the original string. Alternatively, you can set a default filter function.

### on
`{ on : { eventName : () => {} } }`

Allows you to register event listeners on the component.
```js
var vm = vuenit.component(c, {
  on : {
    'custom-event' : function(){}
  }
});
// it is easy enough to add more listeners later:
vm.$on('some-other-event', function(){});
// and just as easy to emit component events:
vm.$emit('custom-event');
```

### config
`vuenit.component.config = { /*...*/ }`  

All of the above options can be set as default options by setting the `config` property on `vuenit.component`.

## vm
The return value is the component instance, so all data, props, computed values, methods, etc. can be accessible directly:
```js
const vm = vuenit.component(c);
c.myProp;
c.computedValue;
c.myMethod();
c.myInjectedObject;
// Any internal vue variables can also be accessed:
c.$el.querySelector('div');
```

The instance is also given a number of additional properties and methods to help testing:
### $name
Returns the name of the component.
```js
vm.$name === 'MyComponent';
```

### $html
Returns the html of the component. This is the equivalent of accessing the component's `$el.outerHTML` property. The property will always return the *current* state of the instance.
```js
vm.$html === '<div/>'
```

### propsData
The props that are being passed into the component instance. This essentially exposes the component's parent data and allows you to update prop values on the fly. Vue handles prop changes asynchronously which means that if you change a prop, you must wait until the next render cycle before your component is updated with the new value.
```js
vm.propsData.foo = 'bah';
await vm.$nextTick();
vm.computedFromFoo === 'bah';
```

### $find
`vm.$find('componentName' | componentDefinition | '.css-selector')`  

Searches the component instance and returns an array of either matching components or matching elements.  

To find a component, you can either pass the component name as a string, or a component definition object. The component must have a name property, otherwise it will not be able to find it. Vuenit will automatically set the name property for locally-registered components and stubbed components set with the `components` option. However, it will not do this for nested components.  
The returned components will all have these augmented properties on them.
```js
let child = vm.$find('childComponent')[0];
let grandchild = child.$find(componentDefinitionObject)[0];
```

To find an element, pass in any valid css selector. It will return an array of matching elements. The returned elements are just plain DOM objects with no *magical* properties applied:
```js
let divs = vm.$find('div');
let content = divs.map(d => d.innerHTML).join('\n');
```

### $findOne
`vm.$findOne('componentName' | componentDefinition | '.css-selector')`

Works just like `$find` except it returns on the first matching object.
```js
let child = vm.$findOne('childComponent');
```

### $contains
`vm.$contains('componentName' | componentDefinition | '.css-selector')`

Returns `true` or `false` depending on whether the specified matcher exists within the component.

## Directive
`vuenit.directive({ directiveName : directiveDefinition }, options)`  

The directive function allows you to test out directives.

In the background it creates a dummy component (a `div` element by default) with the directive applied as an attribute. The component instance is then returned so you can test the effects of the directive on the component (and its html content).  

The function takes two parameters: `Directive` and `Options`:  

### directive  
This can either be a string, for a globally defined directive. Otherwise it should be an object containing `name : definition object`.  

It is possible to supply more than one directive and you can mix definition objects and globally-defined directives. You can even include built-in directives like v-if.  

```javascript
vuenit.directive('global-directive');

vuenit.directive({
  'local-directive' : function(el, binding){}
});

vuenit.directive([
  'global-directive',
  'v-if',
  {
    'local-directive' : function(el, binding){}
  }
]);
```

### options  
Options should be an object with the following properties, all of which are optional:  

### expression  
The expression to pass into the directive.  

```javascript
vuenit.directive('test', { expression : '1===1' });
```
would output  
```
v-test="1===1"
```

You can use this in conjunction with *props* to use variables as well.  
```javascript
vuenit.directive('test', {
  expression : 'x===y',
  props : {
    x : 1,
    y : 1
  }
});
```  
This would allow you to then update the props and re-test the expression.  
```javascript
props.y = 2;
vm.$nextTick(() => {
  vm.$el.outerHTML // would reflect any changes made by the directive
});
```

### argument  
Passes an argument into the directive:  
```javascript
vuenit.directive('test', { argument : 'foo' });
```  
would output  
```
v-test:foo
```  

### modifiers  
Passes modifiers to the directive, this can either be an array or a string.

```javascript
vuenit.directive('test', { modifiers : ['foo', 'bah'] });
```  
would output  
```
v-test.foo.bah
```  

### directiveName
If you pass in multiple directives, you can configure each one separately by adding a property for that directive onto the options object:
```javascript
vuenit.directive({foo, bah}, {
  foo : {
    modifiers : 'mod'
  },
  bah : {
    expression : 'exp'
  }
});
```

### props  
Props to be passed into the component. This will then be available within the `expression`.

### element  
`{ element : 'span' }`  

The element to use for the component the directive will be placed on. By default this is a div.  

### template  
`{ template : '<v-element v-directive></v-element>' }`  

It is possible to completely override the component template. Note that the created directive is inserted into the template by adding a `v-directive` attribute on the html.  
```javascript
vuenit.directive('test', { template : '<input v-directive>' });
```

### config
`vuenit.directive.config = { /*...*/ }`  

All of the above options can be set as default options by setting the `config` property on `vuenit.directive`.

## Store  
`vuenit.store({})`  

This creates a vuex-style store that is extremely lightweight. The store is intended for injecting into a component, so that you don't have create an entire vux store, with all of its validation and logic, in order to test your component.

The store takes a configuration object as its only parameter. The configuration object can take two forms: *full-featured* and *state-only*. In many cases when unit testing, you won't be too bothered about whether a commit has changed some stateful data, or a dispatch has done anything other than returned a promise; therefore the *state-only* configuration allows you to just quickly spin-up a store with a bare-bones structure.

## state-only
```javascript
vuenit.store({
  loading : false,
  users : {
    users : [],
    userId : 1
  },
  nestedModules : {
    moduleA : {
      foo : 'bah'
    }
  }
});
```
This will create a store with a state property that matches the above definition:
```javascript
store.state.loading // false
store.state.users.users // []
store.state.nestedModules.moduleA.foo // 'bah'
```
It will also create a module for *each nested object*. This means the above store would have the following modules:  
- users  
- nestedModules  
- nestedModules/moduleA  

## full-featured
A full-featured configuration allows you to add getters, mutations, and dispatch events, just like a real vuex setup...
```javascript
vuenit.store({
  state : {
    loading : false
  },
  getters : {
    isLoading(state, localGetters, rootState){
      return !!state.loading;
    }
  },
  mutations : {
    LOADING(state, payload){
      state.loading = !!payload;
    }
  },
  actions : {
    loading({commit}, payload){
      commit('LOADING', payload);
    }
  },
  modules : {
    users : {
      // ...
    }
  }
});
```
This allows for a much more advanced store, but it requires a lot more setup and can make your tests more complicated. If you find you are having to set up an entire store for your tests, you may want to think about decoupling your code, or just using the real Vuex module instead of a mock one.

## mixed
There will be times when you want a simple store, but with some extra bits. vuenit is quite happy to accept a mix of simple and complicated configuration options, on a per-module basis:
```javascript
vuenit.store({
  loading : false,
  simple : {
    users : []
  },
  complex : {
    state : {
      features : []
    },
    getters : {
      /* ... */
    },
    mutations : {
      /* ... */
    }
  }
});
```

Once created, the mock store can be used like the real one. The `commit` and `dispatch` methods will just return a stubbed response rather than throwing an error if the matching mutation/action is not available.

*The map methods ({mapState, mapGetters, mapMutations, mapActions}) of Vuex should all work with the vuenit store without any additional steps.*

### when
`store.when(commit/action name)`  
Allows you to determine a return value or callback function for any dispatch or commit. The method returns an object with the following options:

#### return
Return a value when a commit or action is received with this name
```javascript
store.when('module/UPDATE').return({});
```

#### call
Call a method. The method receives the payload object as its argument.
```javascript
store.when('module/action').call(payload => {});
```

#### stop
Returns an unresolved promise.

#### throw
Throws an error when the specified commit/action is received.
```javascript
store.when('LOADING').throw();
```

## http
Creates a mock http object. This mimics the pattern of many ajax modules, and *axios* in particular. The idea is that you would inject a $http property into your Vue component and use it to mock ajax requests in your code.

The returned object is a callable function that takes a configuration object.
```javascript
var $http = vuenit.http();
$http({
  url : '/api/1',
  method : 'post',
  data : {}
});
```
Upon receiving a request, http checks for a matching response set via the `when` method. If it finds one, the response is returned as a promise, otherwise an error is thrown.

http contains a number of methods that will be familiar to most apis:

### get
`http.get(url, config)`

### put
`http.put(url, data, config)`

### post
`http.post(url, data, config)`

### delete
`http.delete(url, config)`

### patch

`http.patch(url, data, config)`
### options
`http.options(url, config)`

### when
`http.when([method], [url])`

The when method allows you to define responses for certain calls.

Both the method and url are optional, and can either be a string or a regular expression.

*When a request matches multiple response definitions, the last response will always be used*

The when method returns an object with the following options:
#### return
```javascript
http.when('get', '/api/1').return({});
```

#### call
```javascript
http.when('api/1').call(config => {});
```

#### stop
Returns an unresolved promise.
```javascript
http.when('get', '/api/1').stop();
```

#### reject
Returns a rejected response with the provided value
```javascript
http.when('get', '/api/1').reject(new Error());
```

### otherwise
Set a response for when an unmatched request is received.
```javascript
http.otherwise().reject();
```
This is the same as calling `when` with no parameters, except that unlike when, it is put at the bottom of the stack.

### strict
`true` by default. If set, unmatched requests will be rejected with an error. If false, all ummatched requests will just return an empty promise.

### latestWins
`true` by default. Set to false to reverse the priority order when multiple responses match a request.



## cleanUp
This is a simple function that removes any globally-assigned components and directives from the main Vue object.
```javascript
afterEach(() => {
  vuenit.cleanUp();
});
```

## trigger
`vuenit.trigger(element | elements | vm, 'eventName' | Event, { arguments })`
The trigger method allows you to trigger a native DOM event on an element. It accepts either a HTML element, an array of elements, or a component instance. If passed a component instance, it will trigger the event on the instance's root element. The event name can either be a string, or an Event object.
```js
vuenit.trigger(vm.$find('button'), 'click');

vuenit.trigger(vm, 'click', { button : 2 });

vuenit.trigger(vm.$find('button-component', new Event('click')));
```
