---
layout: post
title: "Elastic Grids"
date: 2013-07-03 16:40
comments: true
categories: CSS
---


### Grid Systems
Modern Web design has gravitated away from using tables for layout (as they are semantically incorrect, inflexible and lead to bloated hard to manage layout) and towards CSS based layouts. CSS grid systems simplify the application of CSS layouts. Popular grid systems such as the [960 Grid System](http://960.gs) or [Twitter Bootstrap](http://twitter.github.io/bootstrap/) are great for content centric systems like CMS systems or Web Portals, but are not ideal for line of business applications. Primarily, they lack the ability to define areas for content that stretch to use all of the available screen real estate, along side areas that have fixed width's or heights. The up and coming [CSS Flexible Box Layout Module](http://www.w3.org/TR/css3-flexbox/) is looking to address this, but it is [likely to be years](http://caniuse.com/flexbox) before this is usable in most main stream browsers. In the mean time, I have attempted to address this concern with the *Elastic Grid System*. 

#### The Elastic Grid system

The Elastic Grid system allows us to define columns or rows, that can be either a fixed width within the parent container, or can stretch or shrink to consume all of the space available between a number of columns from the left of the area to a number of columns from the right of the area, or a number of rows from the top of the area to a number of rows from the bottom of the area. Columns are defined to be a fixed width (e.g. 60 pixels) and rows are defined to be a fixed height (e.g. 50 pixels). Areas that are fixed width areas are simply defined from a column on the left to another column on the left, a column on the right to another column on the right, at row at the top to another row at the top or a row at the bottom to another row at the bottom. 

The following diagram illustrates how this layout method works for columns.

![](http://i.imgur.com/PL0w9fj.jpg)

Rows work in a similar fashion, although obviously vertically from the row top-1 down to the row bottom-1.

As an example of the actual HTML code used for layout, a column that started 120 pixels from the left and was 600 pixels wide, and was in a row 40 pixels from the bottom and 80 pixels high would be defined as follows:

```html
<div class="container-elastic">
    <div class="row bottom-2 span-2">
		<div class="column left-2 span-10">
		</div>
	</div>
</div>
``` 

Elastic grids can be nested inside one another. For example you may wish to nest a grid with a Fixed width but variable height, inside an element in another grid with a fixed height but elastic width. Alternatively you may just wish to use an elastic grid for fixed layout, within another elastic grid. If you have content in a variable height area that you want to become scroll-able when the container is smaller than the content, simply apply a class with the overflow-x: auto style to a div surrounding that area. I use the convention of creating a  `scroll-pane` class in my solution to encapsulate this behavior. 

The following is an example of a typical complex layout that you may want to implement in the application.

![](http://i.imgur.com/ecMSJEz.jpg)

An example of the HTML required to define a layout such is this follows:

```html
<div class="container-elastic">
    <div class="row top-1 stretch-to-bottom-2 layout-panel">
		<!-- We don't have to use div's for our layout. -->
		<!-- Fieldsets are often a good choice when you want an -->
		<!-- area with a title (legend) -->
		<fieldset class="column left-1 stretch-to-right-7">
			<legend>Variable width and height thing !</legend>
			<!-- Variable width and height content in here. It is likely -->
			<!-- you will want to make this scrollable -->
		</fieldset>

		<fieldset class="column right-6 span-6">
			<div class="container-elastic nested">
           		<div class="row top-1 stretch-to-bottom-2">
					<div class="scroll-pane">
						<!-- Scrollable content in here. -->
>
						<!-- As our parent container is a fixed width anyway -->
						<!-- we don't really need to define any columns if we don't want to -->
						<!-- Typically we would use a fluid grid in here to --> 
						<!-- lay our content out inside our fixed width parent container -->
					</div>
				    <div class="row bottom-1">
						<div class="column left-1 stretch-to-right-1">
							<!-- Buttons in here ! -->
						<div>
					</div>
				</div>
            </div>			
		</fieldset>
	</div>
    <div class="row bottom-1">
		<div class="column left-1 stretch-to-right-1">
			<!-- Buttons in here ! -->
		<div>
	</div>
</div>
```

Click [here](elastic-example/index.html) to see an actual working example of this layout.

In my next blog post, I will go over the CSS code, and show how we generate it using less. 







  
