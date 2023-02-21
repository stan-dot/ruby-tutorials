# Implementing Designs

<small>**Topics Covered:** HAML, SCSS, bootstrap, design principles</small><br><br>

In this worksheet we will cover the basics of styling websites built using Ruby on Rails. In particular we will look at tools such as Moqups and the Bootstrap framework.

If you have difficulties please refer to the content on https://learn.shefcompsci.org.uk, the Rails guides at https://guides.rubyonrails.org/v7.0/, or ask for help.

## Preparation
Use your code from the previous worksheet or alternatively check out the solution from:
```
git@git.shefcompsci.org.uk:com3420-2022-23/materials/implementing-designs.git
```

Then run the following commands to setup the project:
```
cd implementing-designs
bin/setup
```
You can then run
```
bundle exec rails s
```
and in a second terminal or tab
```
bin/webpacker-dev-server
```
to start up the application. You can now visit http://localhost:3000 to see your application running.

## Goal of the worksheet


## Task 1 - Moqups
Moqups is a tool for creating interactive user interface mockups. Please visit https://app.moqups.com, log in with the account you created following our previous invitation email, and create a blank project for this task.

**Please note:** You will not be able to export an interactive PDF from the personal project you have created. When you are working on your team project be careful to follow the instructions in our Using Moqups guide to open the team project we have created for you, which enables additional features.

Create an interactive version of the [hand-drawn sketch](worksheets/mockup-sketch.jpg) of the homepage above using Moqups. This sketch can be found in the same directory as this worksheet within the git project. You might also wish to use the [Paletton](https://paletton.com/) tool to design a colour scheme for your mockup.

## Shakapacker
Shakapacker integrates webpack into Rails. Webpack is used to bundle assets for serving to the frontend. Those assets are primarily JavaScript, but it can also be used to manage images, fonts and CSS assets.

Bundling assets typically involves running various pre-processors on the frontend assets within your codebase to create a version of the code that is smaller in size, requires fewer requests and is more widely compatible with different browsers. For example webpack may be used to remove unnecessary white space, merge multiple files into a single file and convert more modern JavaScript standards to older standards.

The process of bundling these assets is often called 'compiling'. When in the development environment your assets will be compiled differently to how they are on production, this is often to allow easier development or debugging in the local environment.

When generating a Rails application, you will generally start with two entry files:
* **app/packs/entrypoints/application.js**<br> This entry file contains directives that load associated JavaScript files and the code contained within them.
* **app/packs/entrypoints/styles.js**<br> This entry file contains directives that load associated stylesheets and the relevant style definitions.

You should not include CSS or JavaScript directly in the entry files - this is generally bad practice and makes it difficult to understand where the code has come from. You should create individual files, and then require these in the relevant entry file.

These individual files can be stored in the following locations:

1. Inside **app/packs/scripts**<br> This is where you should put any assets you write yourself as part of the application. JavaScript scripts should be placed inside a relevant subfolder, and stylesheets should be placed in the styles folder. App JavaScript can be included like this:
```
import '../scripts/card_list';
```
2. Inside a JavaScript package<br> If a package contains assets that you are expected to use (for example, the Bootstrap package), the package will be written in such a way that you can include them here. Package JavaScript can be included like this:
```
import "package_name";
```


## Task 2 - Add a new SCSS stylesheet

CSS (cascading stylesheets) are the standard way of styling web pages on the Internet. However, they can become unwieldy if your website has a lot of styling on it. SCSS is a way of writing stylesheets such that they are easier to maintain than a normal CSS file. This is due to SCSS' inbuilt features such as variables and nesting.

When your application is deployed, Shakapacker will 'compile' your SCSS files into CSS, which can then be loaded natively by the browser.

1. Create a new stylesheet in **app/pack/styles** - you should call the file: background.scss
2. Within the file, add the following code:<br>
```
body {
  background-color: red;
}
```

3. Require the new stylesheet in your **styles.js** entry file by adding the following line at the bottom of the file:<br>
```
import "../styles/background";
```

4. Revisit your application and check the background colour has changed to red.

## Task 3 - Variables in SCSS

SCSS allows you to assign values to variables, so that values used commonly in the stylesheet can be shared across the application. This is good for maintainability as frequently used colours can be stored without duplicating the hexadecimal representation, and variables can be given descriptive names such as **$brand_header**, for example.

1. Update your **background.scss** file to include the following variables at the top:<br>
```
$colour_one: FireBrick;
$colour_two: CornflowerBlue;
```


2. Update the **background-color** line to use the first colour variable:<br>
```
background-color: $colour_one;
```


3. Refresh your application in your browser, and check that the background colour matches the variable used.
4. Switch to the second variable in **background.scss** and check that the background colour changes.

SCSS also has a number of inbuilt methods that you can use to adjust predefined colour variables such as **color.scale**. These can be enabled by adding **@use "sass:color";** to an SCSS file. You can learn more by looking at the SCSS documentation at: https://sass-lang.com/documentation/modules/color


## Haml

Haml is a templating engine for HTML. Instead of needing to write plain HTML, Haml relies on the relative indentation of each tag to determine when the tag is opened and closed. This can make HTML much quicker to write and easier to maintain. You can also embed Ruby code within your Haml to support dynamic generation of HTML. For example this Haml:

```
%table.table
  %thead
    %tr
      %th Code
      %th Name
      %th Description
  %tbody
    - @products.each do |product|
      %tr
        %td= product.code
        %td= product.name
        %td= product.description
```

would replace this HTML:

```
<table class="table">
  <thead>
    <tr>
      <th>Code</th>
      <th>Name</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <% @products.each do |product| %>
      <tr>
        <td>
          <%= product.code %>
        </td>
        <td>
          <%= product.name %>
        </td>
        <td>
          <%= product.description %>
        </td>
      </tr>
    <% end %>
  </tbody>
</table>
```

As you can see the Haml is much shorter than the HTML version and easier to avoid accidental nesting errors with large and complex views.

You can refer to the Haml reference at https://haml.info/docs/yardoc/file.REFERENCE.html for more information about Haml and its inbuilt functions.

## Task 4 - Update the navigation menu

Currently when we view our application at http://localhost:3000 we can the see the structure of the homepage is quite different to the design we're aiming for. Our first task is to update the navigation menu. The navigation is contained within our applications layout. Layouts let us define the content that appears around each view within our application. The layout used by default is contained in **app/views/layouts/application.html.haml**. If you open this file you will see the head and body sections of the html, within the body section will be the navbar.

1. In **application.html.haml** remove the epiGenesys logo and add links to Products and Categories so it matches the menu in the mockup. The code should look like this

```
        = link_to '/', class: 'navbar-brand' do
          hutCommerce
        %button.navbar-toggler{ type: :button, data: { bs_toggle: :collapse, bs_target: '#navbar-content' }, aria: { controls: 'navbar-content', expanded: 'false', label: 'Toggle navigation' } }
          %span.navbar-toggler-icon
        #navbar-content.navbar-collapse.collapse
          %nav.navbar-nav
            .nav-item
              = link_to products_path, class: 'nav-link' do
                Products
            .nav-item
              = link_to categories_path, class: 'nav-link' do
                Categories
```


## Task 5 - Create the new homepage

Currently the application roots to the Products index page and we have an empty **pages/home.html.haml** file. Let's start building out the new homepage.

1. Change the root of the application to the **pages#home** page by changing line 6 in the **routes.rb** file to<br>
```
  root to: 'pages#home'
```

2. Download an image of your choice to use on the homepage (where the placeholder is). Save this file as **products.png** inside the **app/packs/images** directory

3. Add the content for the homepage by changing the contents of **home.html.haml** to:<br>
```
%h1
  Products to suit your #{content_tag(:u, "every")} need

= image_pack_tag 'products.png', height: 200

%p
  Whatever you're doing, whatever you need. We're sure you'll find our extensive range of carefully curated bits and bobs exciting!

%button
  Click to see our selection

%table
  %thead
    %tr
      %th
        Item
      %th
        Description
  - Product.all.each do |product|
    %tr
      %td
        = product.name
      %td
        = product.description
```

You can now add products using the products page - these should then show up on your homepage.

## Bootstrap

Bootstrap is an open source library that allows you to style web applications using a consistent layout and interface.<br>
For this module you will be using Bootstrap 5.2. The documentation for this can be found at: https://getbootstrap.com/docs/5.2

## Taks 6 - Implementing Bootstrap javascript
The bootstrap framework provides both CSS and Javascript to use in your application. You may have noticed we are already using bootstrap components in our **application.html.haml** file and we have loaded the CSS file in our **entrypoints/styles.js** file.<br>
However we haven't loaded the javascript yet. If you reduce the width of your browser window you should see the menu items replaced by a menu icon on the right hand side of the page. However if you click on this icon nothing will happen. This is because that component requires bootstrap javascript to work.

1. Add the following line to line 2 of the **entrypoints/application.js** file<br>
```
import "bootstrap";
```

2. Check that clicking on the menu icon now shows the menu items we added in task 4. 

## Task 7 - Use the Bootstrap grid and table on the homepage

One of the key features of Bootstrap is it's grid system. This uses containers, rows and columns to layout and align content. An introduction to the grid system can be found at:<br>
https://getbootstrap.com/docs/5.2/layout/grid/

We can use grid system and the default table styling that bootstrap provides to make our homepage look more like the mockup within **home.html.haml**:<br>

1. Put the heading and text content in one column (.col) and the image in another. Both should be within the same row (.row)

2. Add the **table** and **table-striped** class to the table 

3. Make the image size responsive but adding the 'img-fluid' class to the image tag

4. Your **home.html.haml** should now look like:<br>
```
.row
  .col
    %h1
      Products to suit your #{content_tag(:u, "every")} need

    %p
      Whatever you're doing, whatever you need. We're sure you'll find our extensive range of carefully curated bits and bobs exciting!

  .col
    = image_pack_tag 'products.png', class: 'img-fluid'

%button
  Click to see our selection

%table.table.table-striped
  %thead
    %tr
      %th
        Product
      %th
        Description
  - Product.all.each do |product|
    %tr
      %td
        = product.name
      %td
        = product.description
```

## Task 8 - Use the bootstrap collapse component to show the products table

We can utilise the bootstrap collapse component to implement hiding / showing the products table on the homepage. We already have an example of a collapse component in the navbar toggler in the navigation menu on **application.html.haml**
More information about the bootstrap collapse component can be found: https://getbootstrap.com/docs/5.2/components/collapse/

1. Add the bootstrap btn and btn-primary classes to the 'Click to see our selection' button

2. Add the data attributes required for the collapse component to the button (use the navbar toggler as an example)

3. Add the collapse class and the ID you specified in step 2 to the table

4. Your button and table elements should now look like:<br>
```
%button.btn.btn-primary{type: :button, data: { bs_toggle: :collapse, bs_target: '#products-table' }, aria: { controls: 'products-table', expanded: 'false' } }
  Click to see our selection

#products-table.collapse
  %table.table.table-striped.table-success
    %thead
      %tr
        %th
          Product
        %th
          Description
    - Product.all.each do |product|
      %tr
        %td
          = product.name
        %td
          = product.description
```

5. Check that clicking the button hides and shows the products table


## Task 9 - Customising bootstrap variables

Bootstrap has a number of predefined variables that can be amended to customise the Bootstrap layout depending on your specific requirements.
Within your project, there is a **variables.scss** file that can be used for this purpose. You must place any variables you wish to amend before the @import statement relating to variables, otherwise the default Bootstrap variables will be used.
You can see an example **variables.scss** file at: https://github.com/twbs/bootstrap/blob/v5.2.0/scss/_variables.scss

Remove the import statement for your background.scss file within the styles.js manifest.
Refresh your application within the browser to ensure the background has returned to the normal colour.
Within your **variables.scss** file, add a new variable to change the background colour of the body between the two @import statements:<br>
```
@import "~bootstrap/scss/functions";

$body-bg: PapayaWhip;

@import "~bootstrap/scss/variables";
```


Return to your browser and refresh your application - the background colour should have changed to PapayaWhip.
Try adding and removing a few other variables to see what effect they have on the application (for example, $body-color).

## Task 10 - Theming bootstrap

Bootstrap allows us to define a set of colours that are used as the site theme. These are defined in the variable $theme-colors. This variable is a hash where the keys define the Bootstrap identifier of the colour and the values define the colour to be used.
You should not override this hash directly, but you can use variables (such as $light) to set the base colour for the theme.
Set the light colour in the theme to #008b8b in your variables.scss file:<br>
```
$light: #008b8b;
```


Refresh your browser, and the menu bar should have changed to a dark cyan.
Try to change the light variable to a colour of your choosing.
The full list of theme colour variables is available at: https://github.com/twbs/bootstrap/blob/v5.2.0/scss/_variables.scss#L300

## Task 11 - Further optimise styling of homepage

The homepage should now more closely resemble the mockup you created in task 1. Now refering to the bootstrap documentation (https://getbootstrap.com/docs/5.2) edit color, fonts and layouts such that your homepage matches more precisely the mockup. Consider how to organise your scss across multiple files to improve their maintainability.
