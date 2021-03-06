Alchemy is a set of frameworks used by [MagicThemes](http://magicthemes.com) to aid them in Web Design and Development

Alchemy JoomlaBase
==================

Alchemy JoomlaBase is the starting point of building HTML5 Joomla Templates. It incorporates several technologies that's useful 
for making the template compatible with legacy browsers even when the template is purely HTML5.

Technologies
============

* Selected Parts of [HTML5BoilerPlate](http://github.com/paulirish/html5-boilerplate)
* Selected Parts of [Flaw{Less}](http://github.com/dominikGuzei/flawless.css) (grids and some concepts are borrowed from Flawless)
* [Modernizr](http://modernizr.com/) for detecting browser capabilities for granular styling and graceful degradation
* [PIE](http://css3pie.com/) for making Internet Explorer 6/7/8 render CSS3 stuff
* [LESS.js](http://github.com/cloudhead/less.js) - for leaner but very powerful CSS styling
* [IE7-js](code.google.com/p/ie7-js/) for fixing IE quirks
* [CSS Browser Selector](http://rafael.adm.br/css_browser_selector#contributors) for detecting and targeting specific browsers

####Features####

* Very Flexible Grid System - no, it's not just 960gs. We can implement any kind of grid system using FlawLess's Grid
* Responsive Web Design through Media queries
* Complete and semantically consistent HTML5 Joomla template overrides

We will be adding more standard technologies like Gzip, Minifiers, Aggregators, etc as we go.

Naming Conventions
==================

For consistent and understandable CSS styling, we have set some naming conventions which makes elements and CSS selectors easy to find. 

Basic Rules:

* We're not using CamelCase naming convention because it's ugly and inconsistent. 
* IDs are separated by a hyphen `-` per word. 
	* ID names should reflect where it is located. 
	* For example a parent element has an ID of `parent`, any ID located inside it should be named `parent-child`.
	* We use IDs to target an output generated by a specific file. *More explanation on the **layout** section*
* Avoid using more than one word as class name like `image_left`. Usually a class can be defined by "tags" like `class="image fl frame"`.
	* This way we can style specific classes like `image` for images, `fl` if we want it to float left, and `frame` if we want to put a frame on something. Then combine them all in an element.
* There are times when you need to use class names with 2 or more words This is applicable on classes where each word can't be applied as class name to others like `created_by` or `modified_by`.
	* If ever you need to make class names as one, each word should be separated by underscore `_` e.g. class_name


Layout
------

MagicThemes specific layout styles are prepended by `mt-`

The main grid container has an id of `mt-template`

	<body>
		<div id="mt-template">
			...
		</div>
	</body>

Inside `mt-template`, any layout-related elements should be prefixed by `mt-template-`.

For example, the header can be written like this:

	<body>
		<div id="mt-template">
			<header id="mt-template-header">
				...
			</header>
			...
		</div>
	</body>

*Note that the header doesn't have to use the `<header>` tag. It can be a `<div>` to group together `<header>` and `<nav>` and other elements.*

The component output should be inside a `<div>` tag that has an id of `mt-template-content`. 
Using HTML5 overrides, the more semantic `<section>` or `<article>` tags should be rendered by the component itself. The `mt-template-content` div is just for layout purposes.
	
	<body>
		<div id="mt-template">
			<header id="mt-template-header">
				...
			</header>
			<div id="mt-template-content">
			...
			</div>
		</div>
	</body>

Module groups are grouped together by a parent `<div>`. All individual modules are all rendered within a `<section>` tag. 
The id of the module group should be like this `mt-template-sidebar-[module position]`. For example, module left can look like this:

	<body>
		<div id="mt-template">
			<header id="mt-template-header">
				...
			</header>
			<div id="mt-template-content">
			...
			</div>
			<div id="mt-template-sidebar-left">
				...
			</div>
		</div>
	</body>

####Notes####

* The term "sidebar" is used instead of "module" as sidebar is more universally recognized. "Module" is a Joomla jargon. 
* We use `<jdoc:include type="modules" name="left" style="section" />` to render modules under a section.
* If you need to put an element outside `mt-template` (like an absolutely positioned box), just give it an ID of `mt-whatever`. The rule's purpose is to make sure you know where the element is located based on its ID.

Template Overrides
------------------

In our template overrides, we use a simple naming convention to quickly identify which output is being styled.

Rule: IDs of main container tags such as `section`, `div`, `article` should start with the component name, followed by the view, then nested IDs should be prepended by that combination `[component]-[view][-specific].`

To illustrate, in the main output of `com_content/archive/default.php`, the main section container has an ID of `content-archive`. If there is an ID within `content-archive`, it should be `content-archive-[specific]`.

If we have an override for K2, and we want to use an ID, then we use something like `k2-viewname-specific`.

Class Suffixes
--------------

By default, Joomla allows users to assign page suffixes to give specific styling for each page. It's a bad practice as it does not allow graceful degradation.

We're gonna use the page suffix as an extra class. For example, `com_content/article/default` contains this:

	<article id="content-article" class="<?php echo $this->params->get('pageclass_sfx', 'default');?>">

It used to be like this:

	<table class="contentpaneopen<?php echo $this->escape($this->params->get('pageclass_sfx')); ?>">

So using this convention, to give specific styling to this element, we simply do this:

	/* Fallback for everything (graceful degradation) */
	#content-article {
		...
	}
	
	/* More specific styling */
	#content-article.suffix{
		....
	}
	
	/* To style things inside */
	#content-article.suffix > header{
		....
	}

The extensive use of `$this->params->get('pageclass_sfx')` is also removed in the override. It's only used once in the main containing element. It's unnecessary to use it again and again for each class inside the containing element.

Module Positions
----------------

We should never use Joomla's default module position names. Why? so that when our template is installed, the site will not look "ruined". We append `mt-` to all our custom module positions like `mt-left`.


Grid System
===========

Our default Grid system is based on 12-column 960gs but we're not using the 960gs CSS classes. We're using FlawLess's uber flexible grid system, and we assign grids using LESS's mixin capability.
We don't put classes for grids! It makes the source code look ugly. 

Responsive Layout for Mobile Devices
====================================

Our CSS should respond to the media device's screen size using Responsive Web Design Technique.

For example when the screen size is equal or less than 480px:

	@media only screen and (max-width: 480px) {
		/* put layout styling here */
	}

How about background images and rounded corners?
================================================
In the past we have to use nested `<div>`s just to make a rounded corner or a fluid background. Now we don't need that. CSS3 has that feature and PIE makes some of those features usable within IE6, IE7, and IE8.

However there are times when a div for styling purposes is necessary. Feel free to do so under careful consideration. 

Some Classes Generated by the Alchemy's HTML5 override
======================================================

*Note that this is in LESS format*

Paginator or Filters
--------------------

	.paginator {
		.filter {
		
		}
		.limit {
		
		}
	}


Generic Tables
--------------

	table.listtable {
		thead td {
		
		}
		td.begin {
		
		}
		td.title{
		
		}
		td.edit{
		
		}
		td.description{
		
		}
		td.detail {
		
		}
		td.author {
		
		}
		td.date {
		
		}
		td.end {
		
		}
		tr.row1 {
			
		}
		tr.row2 {
			
		}
	}

*Ideally, tables maybe better styled using a mixin with generic elements so we can also apply it to special tables*

	table.listtable,
	table.datatable {
		.alchemy_table();
	}
	
	table.datatable {
		.alchemy_table();
		thead td {
			background: red;
		}
	}

Pagination
----------

	.pagination {
		.nav {
		
		}
		.count {
		
		}
	}

