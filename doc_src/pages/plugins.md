---
title: Plugin API
tags: docs
---

Via the [microplugin](https://github.com/brianreavis/microplugin.js) interface,
features can be added to Selectize without modifying the main library.
This is great because it protects against code bloat, allows for lean builds,
and allows for addons to be sanely isolated. The plugin system isn't meant
to be sexy; it's lean, makes very few assumptions, and gives the developer
complete control.

[**Example Plugins**](examples/plugins)

**A few notes:**
- All plugins live in their own folders in ["src/plugins"](https://github.com/orchidjs/orchid-select/tree/master/src/plugins).
- Plugin names should follow the format: `/[a-z_]+$`
- JS source should live in a "plugin.js" file (required).
- CSS should live in a "plugin.scss" file (optional). It will be bundled at build time.
- Plugins are initialized right before the control is setup.
  This means that if you want to listen for events on any of the control's
  elements, you should override the `setup()` method (see ["DOM Events"](#dom-events)).

## Plugin Usage

#### Without Options

```js
new Selectize('#select',{
	plugins: ['plugin_a', 'plugin_b']
});
```

#### With Options

```js
new Selectize('#select',{
	plugins: {
		'plugin_a': { /* ... */ },
		'plugin_b': { /* ... */ }
	}
});
```

For a more detailed description of plugin option formats and how the plugin system works, check out the [microplugin](https://github.com/brianreavis/microplugin.js) documentation.


## Creating Plugins


### Boilerplate

```js
Selectize.define('plugin_name', function(plugin_options) {
	// options: plugin-specific options
	// this: Selectize instance
});
```

#### Adding Dependencies

```js
Selectize.define('plugin_name', function(plugin_options) {
	this.require('another_plugin');
});
```

#### Method Hooks

Execute plugin code 'before' or 'after' existing Selectize methods

```js
Selectize.define('plugin_name', function(plugin_options) {
	this.hook('after','setup',function(){
		// .. additional setup
	});
});
```

#### Overriding Methods
Use the 'instead' hook to override existing methods.

**Note:** If the method you're overriding returns a value, make sure the
overridden function returns a value as well.

```js
Selectize.define('plugin_name', function(plugin_options) {
	var original_setup = this.setup;
	this.hook('instead','setup',function(){
		// .. custom setup
		return original_setup.apply(this, arguments);
	});
});
```


#### DOM Events
If you want to add event listeners to selectize elements, add them after the `setup()` method.

```js
Selectize.define('plugin_name', function(plugin_options) {
	this.hook('after','setup',function(){
		this.control.addEventListener('click',function(evt){
			alert('the control was clicked');
		});
	});
});
```