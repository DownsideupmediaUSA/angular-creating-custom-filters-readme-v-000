# Creating custom Filters

## Overview

Just like services and directives, we can also create custom filters to use in our application.

## Objectives

- Describe custom filters
- Create a custom filter
- Use a custom filter with a single value
- Use a custom filter with a dataset

## Custom Filters

Custom filters come in two types - single value filters and dataset filters. We can apply a custom filter to filter an array in an `ng-repeat`, or we can apply a filter to an expression to format the expression differently. Both of these are declared via the `.filter` method.

```js
function MyCustomFilter() {
	// wooo!
}

angular
	.module('app')
	.filter('myCustomFilter', MyCustomFilter);
```

Looks familiar, yes?

Our custom filters should return a function that will then process the data passed to it. Let's create a filter to make our expression uppercase.

## Single value filters

When we return a function in our filter, as such:

```js
function MyCustomFilter() {
	return function (str) {

	};
}

angular
	.module('app')
	.filter('myCustomFilter', MyCustomFilter);
```

We get the expression's value passed through to the function. Anything we then returned from our returned function will be put in place of the expression. We could return the string `'hello'` but that wouldn't be any use to anyone.

Let's return the `str` variable, in uppercase.

```js
function makeUppercase() {
	return function (str) {
		return str.toUpperCase();
	};
}

angular
	.module('app')
	.filter('makeUppercase', makeUppercase);
```

Awesome! We can now use this like any other filter in Angular.

```html
<div>
	{{ 'My String' | makeUppercase }}

	{{ ctrl.variable | makeUppercase }}
</div>
```

Which will result in:

```html
<div>
	MY STRING

	OTHER STRING <!-- ctrl.variable = 'other string' -->
</div>
```

## Arguments

We can also pass arguments through to our custom filters. Much like what we've done with the `date` filter before, we can pass through as many arguments we like to the filter, and they're then the 2nd, 3rd, etc, argument passed through to our function.

We can add a toggle to our `makeUppercase` filter on whether or not to *actually* make the text uppercase.

```html
<div>
	{{ 'My String' | makeUppercase:true }}

	{{ ctrl.variable | makeUppercase:false }}
</div>
```

We can then receive this argument in our function, and not make it uppercase if it's false.

```js
function makeUppercase() {
	return function (str, active) {
		return active ? str.toUpperCase() : str;
	};
}

angular
	.module('app')
	.filter('makeUppercase', makeUppercase);
```

As the `true` or `false` can also point to a variable's value instead (`{{ 'My String' | makeUppercase:variableName }}`), we can turn our filters on and off based on different variables. For instance, for administrators, we might want their name to be uppercase, but all other members just leave their names the same.

Awesome!

## Filters for datasets

If we apply this filter to a data-set (for instance on an `ng-repeat`), we simply get passed the array instead of a string expression.

Assume that we have this:

```html
<ul>
	<li ng-repeat="album in ctrl.albums | startsWithLetter:'a'">
	</li>
</ul>
```

This is calling a custom filter named `startsWithLetter`, passing through `'a'` as an argument. We can then define our custom filter as follows:

```js
function makeUppercase() {
	return function (items, letter) {
		// items = ctrl.albums (an array of albums)
		// letter = 'a'
	};
}

angular
	.module('app')
	.filter('makeUppercase', makeUppercase);
```

Now we can filter through the array like we would in native JavaScript:

```js
function startsWithLetter() {
	return function (items, letter) {
		// items = ctrl.albums (an array of albums)
		// letter = 'a'

		return items.filter(function (item) {
			return item.name[0] === letter;
		});
	};
}

angular
	.module('app')
	.filter('startsWithLetter', startsWithLetter);
```

This will filter out our array, only returning items there the `name` property begins with the letter `a`. The array you return from this function will be all the elements that are displayed in the rendered `ng-repeat`.
