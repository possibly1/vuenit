## mounting
### vanilla
```js
const Component = Vue.extend(c);
const vm = new Component();
```

### avoriaz
```js
const vm = avoriaz.mount(c);
```

### vue-test
```js
const vm = vueTest.mount(c);
```

### vue-unit
```js
t.beforeEach(vueUnit.beforeEachHooks);
vueUnit.mount(c);
t.afterEach(vueUnit.afterEachHooks);
```

### vuenit
```js
const vm = vuenit.mount(c);
```

## computed properties
### vanilla
```js
t.is(vm.foo, 'foo');
```

### avoriaz
```js
t.is(vm.computed().foo(), 'foo');
```

### vue-test
???

### vue-unit
```js
t.is(vm.foo, 'foo');
```

### vuenit
```js
t.is(vm.foo, 'foo');
```

## methods
### vanilla
```js
t.is(vm.foo(), 'foo');
```

### avoriaz
```js
t.is(vm.methods().foo(), 'foo');
```

### vue-test
???

### vue-unit
### vanilla
```js
t.is(vm.foo(), 'foo');
```

### vuenit
### vanilla
```js
t.is(vm.foo(), 'foo');
```

## access data
### vanilla
```js
t.is(vm.foo, 'foo');
```

### avoriaz
```js
t.is(vm.data().foo, 'foo');
```

### vue-test
???

### vue-unit
```js
t.is(vm.foo, 'foo');
```

### vuenit
```js
t.is(vm.foo, 'foo');
```

## update data
### vanilla
```js
vm.foo = 'bah';
t.is(vm.foo, 'bah');
```

### avoriaz
```js
vm.setData({ foo : 'bah' });
t.is(vm.data().foo, 'bah');
```

### vue-test
???

### vue-unit
```js
vm.foo = 'bah';
t.is(vm.foo, 'bah');
```

### vuenit
```js
vm.foo = 'bah';
t.is(vm.foo, 'bah');
```

## set props
### vanilla
```js
const Component = Vue.extend(c);
const vm = new Component({
  propsData : {
    foo : 'bah'
  }
});
```

### avoriaz
```js
const vm = avoriaz.mount(c, {
  propsData : {
    foo : 'bah'
  }
});
```

### vue-test
???

### vue-unit
```js
const vm = vueUnit.mount(c, {
  props : {
    foo : 'bah'
  }
});
```

### vuenit
```js
const vm = vuenit.component(c, {
  props : {
    foo : 'bah'
  }
});
```

## access props
### vanilla
```js
t.is(vm.foo, 'foo');
```

### avoriaz
```js
t.is(vm.propsData().foo, 'foo');
```

### vue-test
???

### vue-unit
```js
t.is(vm.foo, 'foo');
```

### vuenit
```js
t.is(vm.foo, 'foo');
```

## update props
### vanilla
???

### avoriaz
???

### vue-test
???

### vue-unit
???

### vuenit
```js
vm.propsData.foo = 'bah';
await vm.$nextTick();
t.is(vm.foo, 'bah');
```

## slots
### vanilla
???

### avoriaz
???

### vue-test
???

### vue-unit
```js
const vm = vueUnit.component(c, {
  slots : {
    header : '<h1>Header</h1>'
    default : '<div>Body</div>',
    footer : '<h4>Footer</h4>'
  }
});
```

### vuenit
```js
const vm = vuenit.component(c, {
  slots : {
    header : '<h1>Header</h1>'
    default : '<div>Body</div>',
    footer : '<h4>Footer</h4>'
  }
});
```

## component name
### vanilla
```js
t.is(vm.$options.name, 'Foo');
```

### avoriaz
```js
t.is(vm.name(), 'Foo');
```

### vue-test
```js
t.is(vm.$options.name, 'Foo');
```

### vue-unit
```js
t.is(vm.$options.name, 'Foo');
```

### vuenit
```js
t.is(vm.$name, 'Foo');
```

## emit events
### vanilla
```js
vm.$emit('click', {});
```

### avoriaz
```js
vm.simulate('click'); // cannot attach a payload...
```

### vue-test
```js
vm.trigger('click');
```

### vue-unit
```js
vm.$emit('click', {});
```

### vuenit
```js
vm.$emit('click', {});
```

## emit dom events
### vanilla
```js
vm.$el.querySelector('button').dispatchEvent(new Event('click'));
```

### avoriaz
```js
vm.find('button')[0].simulate('click');
```

### vue-test
```js
vm.trigger('click');
```

### vue-unit
```js
vm.$el.querySelector('button').dispatchEvent(new Event('click'));
```

### vuenit
```js
vm.$find('button').$trigger('click');
```

## listen to events
### vanilla
```js
vm.$on('customEvent', spy);
```

### avoriaz
 ???

### vue-test
???

### vue-unit
```js
vm.$on('customEvent', spy);
```

### vuenit
```js
vm.$on('customEvent', spy);
// or
vuenit.component(c, {
  on : {
    customEvent : spy
  }
});
```

## find child component
### vanilla
???

### avoriaz
```js
vm.find(MyComponent);
```

### vue-test
???

### vue-unit
???

### vuenit
```js
vm.$find(MyComponent);
vm.$find('myComponent');
```

## find dom element
### vanilla
```js
vm.$el.querySelector('.myClass');
```

### avoriaz
```js
vm.find('.myClass');
```

### vue-test
```js
vm.find('.myClass');
```

### vue-unit
### vanilla
```js
vm.$el.querySelector('.myClass');
```

### vuenit
```js
vm.$find('.myClass');
```

## contains component
### vanilla
???

### avoriaz
```js
t.true(vm.contains(Component));
```

### vue-test
???

### vue-unit
???

### vuenit
```js
t.true(vm.$contains(Component));
```

## contains dom element
### vanilla
```js
t.truthy(vm.$el.querySelector('.myClass'));
```

### avoriaz
```js
t.true(vm.contains('.myClass'));
```

### vue-test
```js
t.true(vm.contains('.myClass'));
```

### vue-unit
```js
t.truthy(vm.$el.querySelector('.myClass'));
```

### vuenit
```js
t.true(vm.$contains('.myClass'));
```

## check component attribute
### vanilla
```js
t.is(vm.$el.getAttribute('id'), 'foo');
```

### avoriaz
```js
t.true(vm.hasAttribute('id', 'foo'));
```

### vue-test
???

### vue-unit
```js
t.is(vm.$el.getAttribute('id'), 'foo');
```

### vuenit
```js
t.is(vm.$el.getAttribute('id'), 'foo');
```

## check dom attribute
### vanilla
```js
t.is(vm.$el.querySelector('.myClass').getAttribute('id'), 'foo');
```

### avoriaz
```js
t.true(vm.find('.myClass')[0].hasAttribute('id', 'foo'));
```

### vue-test
```js
t.true(vm.find('.myClass')[0].getAttribute('id'), 'foo');
```

### vue-unit
```js
t.is(vm.$el.querySelector('.myClass').getAttribute('id'), 'foo');
```

### vuenit
```js
t.is(vm.$find('.myClass').getAttribute('id'), 'foo');
```

## check component class
### vanilla
```js
t.true(vm.$el.classList.contains('myClass'));
```

### avoriaz
```js
t.true(vm.hasClass('myClass'));
```

### vue-test
```js
t.true(vm.hasClass('myClass'));
```

### vue-unit
```js
t.true(vm.$el.classList.contains('myClass'));
```

### vuenit
```js
t.true(vm.$el.classList.contains('myClass'));
```

## check dom class
### vanilla
```js
t.true(vm.$el.querySelector('.myClass').classList.contains('myClass'));
```

### avoriaz
```js
t.true(vm.find('.myClass')[0].hasClass('myClass')); // possibly a redundant test!
```

### vue-test
???

### vue-unit
```js
t.true(vm.$el.querySelector('.myClass').classList.contains('myClass'));
```

### vuenit
```js
t.true(vm.$find('.myClass').classList.contains('myClass'));
```

## check component style
### vanilla
```js
t.is(vm.$el.style.width, '100%');
```

### avoriaz
```js
t.true(vm.hasStyle('width', '100%'));
```

### vue-test
???

### vue-unit
```js
t.is(vm.$el.style.width, '100%');
```

### vuenit
```js
t.is(vm.$el.style.width, '100%');
```

## check dom style
### vanilla
```js
t.is(vm.$el.querySelector('.myClass').style.width, '100%');
```

### avoriaz
```js
t.true(vm.find('.myClass').hasStyle('width', '100%'));
```

### vue-test
???

### vue-unit
### vanilla
```js
t.is(vm.$el.querySelector('.myClass').style.width, '100%');
```

### vuenit
```js
t.is(vm.$find('.myClass').style.width, '100%');
```

## check html
### vanilla
```js
t.is(vm.$el.outerHTML, '<div/>');
```

### avoriaz
```js
t.is(vm.html(), '<div/>');
```

### vue-test
```js
t.is(vm.html(), '<div/>');
```

### vue-unit
```js
t.is(vm.$el.outerHTML, '<div/>');
```

### vuenit
```js
t.is(vm.$html, '<div/>');
```

## check component matches selector
# vanilla
```js
t.is(vm.$el.tagName, 'DIV');
```

### avoriaz
```js
t.true(vm.is('div'));
```

### vue-test
```js
t.true(vm.matches('div'));
```

### vue-unit
```js
t.is(vm.$el.tagName, 'DIV');
```

### vuenit
```js
t.is(vm.$el.tagName, 'DIV');
```


## check component is empty
### vanilla
```js
t.is(vm.$el.children.length, 0);
```

### avoriaz
```js
t.true(vm.isEmpty());
```

### vue-test
```js
t.true(vm.isEmpty());
```

### vue-unit
```js
t.is(vm.$el.children.length, 0);
```

### vuenit
```js
t.is(vm.$el.children.length, 0);
```

## inject dependencies
### vanilla
???

### avoriaz
???

### vue-test
???

### vue-unit
???

### vuenit
```js
const vm = vuenit.component(c, {
  inject : { $store, $route, service }
});
```

## mock components
### vanilla
???

### avoriaz
???

### vue-test
???

### vuenit
```js
var vm = vuenit.component(c, {
  components : {
    myComponent : '<span>mock content</span>'
  }
});
```

## shallow render
### vanilla
???

### avoriaz
???

### vue-test
???

### vuenit
```js
var vm = vuenit.shallow(c);
```
