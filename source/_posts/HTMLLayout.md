# HTML Layout in the MVC Client 

### LESS and Bundling in the MVC Web Project
	
Less is a CSS pre-processor that has become very popular in the last few years. It allows you to extend css with variables, mix-ins, nested rules, functions & operations. Basically this allows for coherent, manageable and reusable CSS styling. More information on css can be found at http://lesscss.org.

As less is a css **pre-processor**, it needs to be processed, or compiled, into regular css before being sent to the client<sup>1</sup>. To facilitate this we use the MVC bundling and minification support. Native LESS support out of the box is not great in MVC, so we use the excellent [BundleTransformer](http://bundletransformer.codeplex.com/) library, which along with having native LESS (and SASS/SCSS/CoffeeScript/Typescript) support, also improves on the css and js minification that comes with the standard `System.Web.Optimization` library.

In our project, the bundling and minification is configured in the `BundleConfig.cs` file in the `App_Start` folder of the MVC Web project. As `BundleTransformer` is configured to handle LESS, LESS files can configured for bundling in pretty much the same way as regular css files. An example of the configuration code for a LESS file follows:

```C#
    public class BundleConfig
    {
        // For more information on Bundling, visit http://go.microsoft.com/fwlink/?LinkId=254725
        public static void RegisterBundles(BundleCollection bundles)
        {
            var cssTransformer = new CssTransformer();
            var jsTransformer = new JsTransformer();
            var nullOrderer = new NullOrderer();
.
.
.
	        // Main Csss Bundles/Site.less & Kendo Styles 
            var siteBundle = new Bundle("~/Content/css");
            siteBundle.Include(
                "~/Content/normalize.less",
                "~/Content/KendoUI/kendo.common.less",
                "~/Content/Site.less");

            siteBundle.Transforms.Add(cssTransformer);
            siteBundle.Orderer = nullOrderer;
            bundles.Add(siteBundle);
.
.
.
		}
	}
```

To include the css output from these bundles in our application we would simply add the following Razor directive to the HEAD of our HTML page.

```HTML
<!DOCTYPE html>
<html lang="en">
    <head>
 		@Styles.Render("~/Content/css")
	</head>
.
.
.
```

<sup>1 - Actually you can process LESS on the client side using a javascript library, but this comes with it's own issues</sup>

### Structure of Less and CSS in the Dart MVC project
Currently, all style information for the DART MVC client is applied using external CSS styles. Inline styles should be avoided unless absolutely necessary. One caveat to this, is that it is sometimes required to apply style information at runtime using javascript, and libraries such as KendoUI use this extensively to get around shortcomings in HTML layout functionality, however, static in-line `style` attributes should not be required and should be avoided in favour if references to class definitions in external style sheets.

External styles sheets are organised under the Content folder of the MVC Web project. 

![Folder Structure](http://i.imgur.com/muD2DLH.jpg)

Typically most site wide style information will be in the Site.less file. Styles only relevant to individual areas can be referenced in a separate *{AreaName}.less* file, such as the ssi.less file pictured above. Site wide variables, such as theme colours, standard widths/heights etc are kept in the variables.less file which can then be `@import`ed into other LESS stylesheets. [*normalize.less*](http://necolas.github.io/normalize.css/) is a third party CSS reset, *mixins.less* contain re-usable css components (mostly borrowed from [Twitter Bootstrap](http://twitter.github.io/bootstrap/)\) and *grid.less* contains our grid systems described below. KendoUI also heavily uses LESS to define the themes used by their controls. Our customisations to thier themes can be found in the *KendoUI/kendo.dart.less* file. The other Kendo LESS files are standard files from the KendoUI distribution and should not be touched. Additionally, we include the *kendo.dart.less* file in our own *Site.Less* file so we can re-use the theme variables and mixins to style our own non-kendo components.

### Grid Systems
Modern Web design has gravitated away from using tables for layout (as they are semantically incorrect, inflexible and lead to bloated hard to manage layout) and towards CSS based layouts. CSS grid systems simplify the application of CSS layouts. Popular grid systems such as the [960 Grid System](http://960.gs) or [Twitter Bootstrap](http://twitter.github.io/bootstrap/) are great for content centric systems like CMS systems or Web Portals, but are not ideal for line of business applications such as Dart. Primarily, they lack the ability to define areas for content that stretch to use all of the available screen real estate, along side areas that have fixed width's or heights. We address this concern with our *Elastic Grid System*. Additionally we have a simple fluid (i.e. percentage width based) grid system for layout within our elastic containers.

#### The Fluid Grid system
The Fluid Grid system simply divides a content element into 12 equal columns, and optionally (up to) 30 rows of (lineheight) + 15 pixels, which should be roughly 35 pixels. This is primarily used to organise and align content within a fixed width container. A typical use would be laying out form labels and inputs on a data entry form. To use the fluid grid system, simply declare a parent container (typically a div) with the class `container-fluid`. Apply the classes `span-x` or `skip-x`, where x is 1 to 12, to specify how many columns the elements spans, and alternative, how many columns to the left of the content are *skipped* before the content is rendered. You can additionally define parent elements above the column aligned elements with the class `row` that can span or skip 35 pixel high rows.

The following example shows a fluid grid layout that has multiple rows with labels that take the first third of the containers width (i.e 4 of 12 columns) with values taking the remaining tow thirds of the containers width (8 of 12 columns):

```HTML
<div class="container-fluid form-panel">
    <div class="row">
        <label class="span-4">Status</label>
        <div class="span-8">@Model.StatusDescription</div>
    </div>
    <div class="row">
        <label class="span-4">Programme</label>
        <div class="span-8">@Model.ProgrammeDescription</div>
    </div>
    
    <div class="row">
        <label class="span-4">Cust Code</label>
        <div class="span-8">@Model.CustomerReference</div>
    </div>
.
.
.
</div>
```

#### The Elastic Grid system

The Elastic Grid system allows us to define columns or rows, that can be either a fixed width within the parent container, or can stretch or shrink to consume all of the space available between a number of columns from the left of the area to a number of columns from the right of the area, or a number of rows from the top of the area to a number of rows from the bottom of the area. Columns are defined to be a fixed width (60 pixels) and rows are defined to be a fixed height (50 pixels). Areas that are fixed width areas are simply defined from a column on the left to another column on the left, a column on the right to another column on the right, at row at the top to another row at the top or a row at the bottom to another row at the bottom. 

The following diagram illustrates how this layout method works for columns.

![](http://i.imgur.com/PL0w9fj.jpg)

Rows work in a similar fashion, although obviously vertically from the row top-1 down to the row bottom-1.

As an example of the actual HTML code used for layout, a column that started 120 pixels from the left and was 600 pixels wide, and was in a row 40 pixels from the bottom and 80 pixels high would be defined as follows:

```HTML
<div class="container-elastic">
    <div class="row bottom-2 span-2">
		<div class="column left-2 span-10">
		</div>
	</div>
</div>
``` 

Elastic grids can be nested inside one another. For example you may wish to nest a grid with a Fixed width but variable height, inside an element in another grid with a fixed height but elastic width. Alternatively you may just wish to use an elastic grid for fixed layout, within another elastic grid. When you nest an elastic grid within another elastic grid you should add the CSS style `nested` to the container element along with `container-elastic`. Additionally, if you have content in a variable height area that you want to become scrollable when the container is smaller than the content, simply apply the `scroll-pane` class to a div surrounding that area.

The following is an example of a typical complex layout that you may want to implement in the application.

![](http://i.imgur.com/ecMSJEz.jpg)

An example of the HTML required to define a layout such is this follows:

```HTML
<div class="container-elastic full-height full-width">
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
 







  
