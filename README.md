# PCEM-SCSS-Methodology
A new CSS methodology that uses SCSS native functions, object oriented principles, and borrows concepts from BEM, OOCSS.

The following are its standards.

# Selectors
To maximize code reuse, selector performance, and location dependence of individual class names, avoid overly specific and overly general selectors and nesting, like the following:

```css
header ul {}
input.btn {}
ul.nav {}
.header span {}
#foo * {}
#content table {}
.foo {
	.bar {}
}
```

Absolutely no IDs and !important directives for styling. To further understand the reasons for these, go to the following link: https://cssguidelin.es/ and go to the “CSS Selectors” and “Specificity” sections.

# Naming Convention (PCEM: page component/container element/content modifier)

```css
Page{__component}{_element}{-description}{--modifier}
.Page {}
.Page--modifier {}
.Page__component {}
.Page__component-container {}	// i.e. .Landing__chart-container {}
.Page__component--modifier {}
.Page__component_element-countries--modifier {} // i.e. .Landing__chart_input-countries--small {}
```

A component is a group of at least 2 elements.
Modifier can refer to: (1) state, (2) color, (3) sizes, (4) theme, (5) layout.
Examples of layout can be: vertical-grid, horizontal-grid, or table grid.
In case you're wondering about how the naming would look like for multiple components per page and nested components, refer to the following:

```css
.Page__component1 {}
.Page__component2 {}
.Page__component1__component1.1 {}
```

**NOTE:** An element can only be present if component is also present.

## Reasons for the naming convention:

1) Gets rid of multiple class declarations for an HTML element, since everything would be basically defined within that single class name, as will be shown in the end of this file.
2) The naming convention itself avoids CSS class naming conflicts
3) This is an improvement from the BEM naming convention, since it more clearly indicates the difference between “blocks” and “elements”, which, in this case, is separated into Page, component, and element.
4) The naming convention provides a general idea of what the CSS class is doing on the HTML element, without having the need to indicate every CSS property, as in Atomic CSS (ACSS)
5) The naming convention provides a sense of “scoping”, since it indicates which HTML element, or elements, the class is being applied to. This is further enforced by the how class name is named, as shown in **reason #8’s** example; doing this reduces confusion, since developers can more easily pinpoint which page, component, and element the class name is referring to. Some may argue that overly specific naming conventions can reduce code reuse; this is, however, “fixed” by using extensions and mixins, as will be shown in the end of this file.
6) If used correctly, the naming convention gets rid of all **!important** directives.
7) The naming convention allows for easier file organization, since every CSS class within the “Page” name, would be all located in one place, which also makes debugging easier.
8) The naming convention has similar flexibility as to that of BEM. Similarly, multi-word class names are separated with a “-“ as shown in the following:

Homepage__advanced-search_filter-1—small

9) The naming convention can allow for separation of concerns, in terms of limiting “structural” styling to the page, component, and element, and allowing actual styling to the modifier.
10) The naming convention is intended for **NO CSS NESTING**, as will be shown in the end of the file.
11) Part of the rationality behind the naming convention is to view a page as a collection of components, whether independent and nested components, which then contains granular elements. So, this naming convention combines both BEM and Atomic CSS concepts. This is also to adhere to object-oriented programming principles, to add structure, predictability in behavior, and abstraction, which greatly reduces repetitive code.

## Cons:
1) Greater learning curve compared to other CSS methodologies and naming conventions, since properly defining and using mixins and extensions, as will be shown later, might get too abstracted.
2) Rules can get very restrictive.

**NOTE:** Since variable naming is solely intended for developers, don’t worry about having too verbose names. Saving a few extra bytes should be handled by CSS minifiers, not by developers.

For a detailed explanation on why I decided to not improve upon other CSS methodologies, read MethodsProsCons.docx

# File Structure
```
- scss/
 	| - main.scss		        - where everything is “imported”
	| - vendors/
	|	| - fonts/
	|	|_- icons/
	| - abstracts/
	|	| - _main.scss
	|	| - functions/
	|	|	| - _main.scss
	|	|	| - _numFuncs.scss
	|	|	    …
	|	|	|_- _colorFuncs.scss
	|	| - variables/
	|		| - _main.scss
	|		| - _colorNThemeVariables.scss		- dynamic theming using !default
	|		    …
	|		|_- _fontVariables.scss
	| - base/		        - this is where browser defaults are overridden
	| - _layout.scss		- vertical, horizontal, table grids, or none
	| - components/	          - contains generic (extensions) and dynamic (mixins) components
	|	| - _main.scss
	|	| - _btn.scss
	|	|	| - _main.scss
	|	|	| - interface/		  - contains boilerplate code for component’s autogenerated class names 
	|	|	| - implementation/	  - contains class property definitions
	|	|	|_- template/		  - static styles for extensions
	|	|     …
	|	| - _carousel.scss
	|	|	| - _main.scss
	|	|	| - interface/
	|	|	| - implementation/
	|	|	|_- template/
	| - pages/
		| - _main.scss
		| - _homepage.scss      - if page class contains numerous components, separate blocks with comments
		    …
		|_- _forum.scss
```
**Note:** Starting from the top, the architecture also shows the @import order for the root main.scss. Also notice how all scss files are partial files, except for main.scss. Partial files allows us to breakdown a file into many smaller files, aka partials, so, when scss is getting compiled into css, all the partials would be combined into one file, which significantly improves performance.

Themes can be dynamically set as shown in the following:
// in _themes.scss
```css
$theme1: (
	$color-background: #FFF;
	$color-text: #000;
	$color-header: #7979;
	$color-footer: #4646;
);

$theme2: (
	$color-background: #000;
	$color-text: #FFF;
	$color-header: #FB9B;
	$color-footer: #E4CD03;
);

@mixin choose-theme($theme: theme1) {
	@if ($theme == “theme1”) {
		$theme: $theme1;
		$color-background: #FFF;
		$color-text: #000;
		$color-header: #7979;
		$color-footer: #4646;
	}
	@else if ($theme == “theme2) {
		$theme: $theme2;
		$color-background: #000;
		$color-text: #FFF;
		$color-header: #FB9B;
		$color-footer: #E4CD03;
	}
}

$color-background: #FFF !default;
$color-text: #000 !default;
$color-header: #7979 !default;
$color-footer: #4646 !default;

$theme: $theme1 !default;

.theme1 {	@include choose-theme(“theme1”);		}

.theme2 {	@include choose-theme(“theme2”);		}
```

**NOTE:**: The overrides must come before the default variables, so, when we dynamically use JavaScript to changes classes from theme1 to theme2, $color1..3 would point to the new values. Also, the default values wouldn’t be overridden until an element’s class is switched to another theme. Additionally, the theme class should reside in the uppermost parent dom node.

# Pitfalls to Avoid

Generic classes, such as .form-…, .btn-…, or even .table-…, are high-level classes within their own files. They will serve as static templates. Static templates are optimally used through extensions in Sass, as opposed to using mixins, since incorrect use of mixins can introduce bloated code. Use placeholders when extending static templates, so the static classes themselves aren’t included. If a custom component, or “code flow”, becomes commonly used, abstract it using mixins, extensions, and functions:

**Extensions with placeholders**

**SCSS**								            
```css
%text-blue {							            
	color: blue;							    	
}

.article {
	@extend %text-blue;
}

.title {
	@extend %text-blue;
}
```

**CSS**
```css
.article, .title {
	color: blue;
}
```

**Extensions without placeholders**

**SCSS**								            
```css
.text-blue {							            
	color: blue;							     	
}

.article {
	@extend .text-blue;
}
```
**CSS**
```css
.text-blue, .article {
	color: blue;
}
```

**Mixins**

**Sass**								  
```css
@mixin text-blue {						        
	color: blue;								
}								        

.article {							        
	@include text-blue;						        
}								        

.title {
	@include text-blue;
}
```

**CSS**
```css
.article {
	color: blue;
}
.title {
	color: blue;
}
```

If a page, or component, needs a customized template, create a template mixin, instead:

**SCSS**
```css
@mixin border-radius($border-radius: 5px, $color: blue) {		
	border-radius: $border-radius;						
	color: $color;								
	@content							
}
									
.article {									
	@include border-radius($color: red);					
}										
									
.title {
	@include border-radius($border-radius: 10px) {
		font-size: 10px;
	}
}
```

**CSS**
```css
.article {
	border-radius: 5px;
	color: red;
}
.title {
	border-radius: 10px;
	color: blue;
	font-size: 10px;
}
```

When defining mixins, make sure to always include @content directives at the end of the mixin declaration, since this can be used to include additional properties to the overridden template. When using mixins with additional contents, ensure that there isn’t already a similar predefined property, since this can cause repetitions:

**SCSS**								
```css
.title {								
	@include border-radius($border-radius: 10px) {				
		color: red;							
  }										
}									
```

**CSS**
```css
.title {
	border-radius: 10px;
	color: blue;
	color: red;
}
```

An overly simplified template file can look like the following:

**Inside a button template file**
```css
%btn {
	padding: 3px 5px;
	margin: 5px 10px;
	color: grey;
}


@mixin btn($padding: 3px 5px, $margin: 5px 10px, $color: grey) {
	padding: $padding;
	margin: $margin;
	color: $color;

	@content;
}

**Inside file1 for a page/component					Inside file2 for a page/component**
.Homepage__info-request_btn--default {					.CTA__btn--success {
	@extend %btn;								@include btn($color: green){
}											font-size: 20px;
										}
									}
```
# Syntax Standards:
**Parenthesis**
Although it’s optional to use parenthesis when using conditional statements, for the sake of consistency from JavaScript’s syntax and clarity, please use of parenthesis, as shown in the following:

**Don’t do this**						
```css
@if $padding == “none” {						
	// something								
}									
@else if $padding != “something” {					
	// something								
}									
@else {									
	//something								
}									
```

**Do this instead**

```css
@if ($padding == “none”) {
	// something
}
@else if ($padding != “something”) {
	// something
}
@else {
	// something
}
```

This is, however, not true for loop statements, which include @for, @each, and @while:
**Right**
```css
@for $i from 1 to 4 {
	// something
}
```

**Wrong**
```css
@for ($1 from 1 to 4) {
	// something
}
```

**Quotations**
When using mixins and functions, although it’s optional to use quotations (either single or double) when trying to pass strings, please use either single or double quotations for clarity, unless the argument is a number, or a boolean. The same principles should be used when declaring parameters: 

**Don’t do this**								
```css
@include some-mixin($border: none) {					
	// something								
}									
```

**Do this instead**
```css
@include some-mixin($border: “none) {
	// something
}
```

**Commas**
When passing arguments to either mixins or functions, it’s optional to use commas as delimiters per succeeding argument. Again, for clarity and for reducing the chances of introducing unintended errors and bugs, please use commas:

**Don’t do this**							
```css
@include some-mixin (“none” none 0);				
```

**Do this instead**
```css
@include some-mixin (“none”, “none”, 0);
```

# Defining Variables

Due to the chances of defining an arbitrary number of variables for an arbitrary number of uses, for the sake of maximizing code reuse and maintaining for the possibility of UI themes, use the default directive (**!default**) coupled with descriptive color names:

**Don’t do this**								
```css
$colorWhite: //someHex							
```

**Do this instead**
```css
$white-background: //someHex !default;
```

The directive simply means that unless the current variable was already defined, use the indicated variable value:
**SCSS**
```css
$white: #fff !default;

.btn {									
	text: $white;								
}									

// override default
$white: #f0f2f7;
$white: #fff !default;

.btn {									
	text: $white;								
}									
```
**CSS**
```css
.btn {
	text: #fff;
}
.btn {
	text: #f0f2f7;
}
```

# Alignment and Grouping
Attempt to align common and related identical strings in declaration and group related declarations together:
```css
.bar {
	position: absolute;
	top:	  0;
	right:	  0;
	bottom: 0;
	left:	  0;
	margin-right: 	-10px;
	margin-left:	-10px;
	padding-right:	 10px;
	padding-left:	 10px;
}
```

# Coding Standard Example
The following is a simple example of how to use the standard. You can also experiment more on them using https://www.sassmeister.com/.

**In template file**

Since this template will be widely used across several pages, if you want to include a specific element within this template for a specific use case, include that element definition within the implementation file, and set the default definer value to false in the interface file, since we wouldn't want the other classes inheriting the specific element.
```css
%form {
  display: inline-block;
  font-weight: 300;
  text-align: center;
  white-space: nowrap;
  vertical-align: middle;
  user-select: none;
  border: 1px solid transparent;

  &:focus {
    outline: 0;
  }

  &:not(:disabled):not(.disabled) {
    cursor: pointer;
  }

  &:not(:disabled):not(.disabled):active,
  &:not(:disabled):not(.disabled).active {
    background-image: none;
  }
}

%form__input {
  font-weight: 350;
  text-align: left;
  padding: 9px 0;
}
```

**In Implementation file**

This implementation file is where all the individual values are set. Notice how the default child setters are used, if the user decides to not indicate the “use__” property value.
```css
@mixin form-implementation ($argList) {

  @if (type-of($argList) == "map") {
    
    @if (map-has-key($argList, "useParent") and map-get($argList, "useParent") == false) {
    }
    @else {
      display: inline-block;
      font-weight: 300;
      text-align: center;
      white-space: nowrap;
      vertical-align: middle;
      user-select: none;
      border: 1px solid transparent;
      
      @if (map-has-key($argList, "parent") and length(map-get($argList, "parent")) ) {
        @each $key, $value in map-get($argList, "parent") {
          #{$key}: $value;
        }
      }
    }
    
    @if (map-has-key($argList, "useFocus") and map-get($argList, "useFocus") == false) {
    }
    @else {
      &:focus {
        outline: 0;
	
	@if (map-has-key($argList, "focus") and length(map-get($argList, "focus")) ) {
          @each $key, $value in map-get($argList, "focus") {
            #{$key}: $value;
          }
        }
      }
    }
    
    @if (map-has-key($argList, "useNot") and map-get($argList, "useNot") == false) {
    }
    @else {
      &:not():disabled) :not(.disabled) {
        cursor: pointer;
	
	@if (map-has-key($argList, "not") and length(map-get($argList, "not")) ) {
          @each $key, $value in map-get($argList, "not") {
            #{$key}: $value;
          }
        }
      }
    }
    
    @if (map-has-key($argList, "useNotActive") and map-get($argList, "useNotActive") == false) {
    }
    @else {
      &:not(:disabled):not(.disabled):active,
      &:not(:disabled):not(.disabled).active {
        background-image: none;
	
	@if (map-has-key($argList, "notActive") and length(map-get($argList, "notActive")) ) {
          @each $key, $value in map-get($argList, "notActive") {
            #{$key}: $value;
          }
        }
      }
    }
  }
  @else {
    @warn “You’re calling a mixin without passing an argList, maybe you meant to use a template instead?”
  }
}

@mixin form__input-implementation ($argList) {
  @if (type-of($argList) == "map") {
    
    @if (map-has-key($argList, "useParent") and map-get($argList, "useParent") == false ) {
    }
    @else {
      font-weight: 350;
      text-align: left;
      padding: 9px 0;
      
      @if (map-has-key($argList, "parent") and length(map-get($argList, "parent")) ) {
          @each $key, $value in map-get($argList, "parent") {
            #{$key}: $value;
          }
      }
    }
  }
  @else {
    @warn “You’re calling a mixin without passing an argList, maybe you meant to use a template instead?”
  }
}

```

**In Interface file**

Notice how some of the default variable values are using global variables.
$emptyMap refers to a global empty map, and is used as a placeholder, like the following: $emptyMap: ();
Note how the interface file is analogous to the concept of “default constructors” in object oriented programming.

```css
@mixin form-interface ($argList: $emptyMap,
			// name generator parameters
			$page: "none", 			$component: "none", 
			$element: "none", 		$modifier: "none") {
                    
  @if ( $page == "none" ) {
    @warn "You're trying to create a class without a page name!";
  }
  @else if ( ($component == "none") and ($element == "none") ) {
    @warn "You need to specify a component or element class to generate";
  }
  @else if ( $component == "form" and $element == “input” ) {
    .#{$page}__#{$component}_#{$element} {
      @include form_input-implementation ($argList1);
    }
  }
  @else if ( $component == "form" ) {
    .#{$page}__#{$component} {
      @include form-implementation ($argList1);
    }
  }
}
```

**In page file**

Note how the variable “Homepage__formMap” uses “Homepage” as a specifier that this variable is located in the “page” folder and to also prevent variable naming conflicts. With the current setup, every passed argument for a mixin, via includes, is analogous to “Setters” in Java.
```css
$Homepage__formMap: (
  “focus”: (
    “color”: blue,
    “font-weight”: 400,
  ),
  “useNot”: false
);

@include form-interface($argList: $Homepage__formMap, $page: "Homepage", $component: "form");

```

**In generated CSS**
```css
.Homepage__form {
  display: inline-block;
  font-weight: 500;
  text-align: center;
  white-space: nowrap;
  vertical-align: middle;
  border: 1px solid transparent;
  font-size: 3em;
}
.Homepage__form:focus {
  outline: 0;
  // generated from “$Homepage__formMap” map
  color: blue;
  font-weight: 400;
}
// notice how the classname:not is gone because “useNot” was false
.Homepage__form:not(:disabled):not(.disabled):active, .Homepage__form:not(:disabled):not(.disabled).active {
  background-image: none;
}
```

**Responsive Design**

CSS attributes that depend on spacing and sizing:
background-size, border-width, border-radius, box-shadow, column-gap, column-width, flex-basis, font-size, grid-column-gap, grid-gap, grid-row-gap, height, left, letter-spacing, line-height, margin, outline-width, padding, right, text-indent, text-shadow, top, transform-origin, width, word-spacing\
You can use this [link](https://cssreference.io/) for reference.

Please use appropriate units for responsive design as indicated in this [link](https://www.w3schools.com/cssref/css_units.asp). Whenever possible, use dynamic units, else use px, unless you're working with PDFs or printed media, in which case cm, in, etc. are permissible.\
**rem:** strictly for text\
**%:** nested components only\
**vh & vw:** containers / wrappers\

**Don't use the following units:**\
**ch:** since this sizing doesn't event depend on browser window and the default font size from root or parent\
**em:** since this is open to parent inheritance and it's current element sizing, its calculated value will change during refactoring\
**ex:** since this is dependent on current element's font size\
**vmin & vmax:** since these can either depend on width or height, especially when user switches from portrait to landscape, or vice versa

**NEVER USE NEGATIVE VALUES FOR UNITS**


