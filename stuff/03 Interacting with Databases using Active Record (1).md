# Interacting with Databases using Active Record

<small>**Topics Covered:** Searching, associations, validations, scopes, querying</small><br><br>

In this worksheet we will continue the introduction of the Ruby on Rails web framework by expanding your existing ecommerce application.

If you have difficulties please refer to the material on https://learn.shefcompsci.org.uk, the Rails guides at https://guides.rubyonrails.org/v7.0/, or ask for help.

## Preparation
Use your code from the previous worksheet or alternatively check out the solution from:
```
git@git.shefcompsci.org.uk:com3420-2022-23/materials/interacting-with-databases-using-active-record.git
```

Then run the following commands to setup the project:
```
cd interacting-with-databases-using-active-record
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

## Task 1 - Searching products
It would be useful if customers could search for specific products by name, so that they do not have to scroll through a long list of products to find what they want.

We need to add a new feature to the application so that customers can enter a name in a text box and click 'Search', and then only products that match that name are displayed in a table. First we need to define a new route.

Open the routes file `config/routes.rb` and change:

```
resources :products
```

to

```
resources :products do
  post :search, on: :collection
end
```

This defines a *POST* (i.e. submit some fields filled in on a form) path called search for the products. The `on: :collection` code creates a path for all products and not an individual product.

If you run:

```
bundle exec rails routes
```

You can see the following path has been added:

```
search_products POST   /products/search(.:format)  products#search
```

We now need to create a search form in a View. We will add this to the top of the existing products index page.

Open the `app/views/products/index.html.haml` file and add the following code (highlighted in bold) after the product count text we added in the previous worksheet:

<pre>
%p There are #{@products.size} products available.

<strong>= simple_form_for :search, url: search_products_path, method: :post do |f|
  = f.input :name

  = f.submit 'Search'
  = link_to 'Reset', products_path

%br
</strong>
</pre>

The first argument to the `simple_form_for` method is the name of the parameters hash we can access in the controller when we submit the search form. For example, if we enter 'Laptop' in the name field in the form above and click the Search button here is how the form parameters are passed to the controller:

```
{"search" => {"name" => "Laptop"}}
 
# This can be accessed in the controller using:

params[:search][:name]
```

The `url` option states which path to submit the form parameters to when the Search button is clicked, in this case the search products path we created earlier. The method option states which HTTP verb to use and it must match what is specified for the url path in the routes file, in this case post.

The block passed to `simple_form_for` - indicated by `|f|` - enables us to add form inputs, labels, buttons, etc. More details can be found about all the possible options at [the Simple Form GitHub](https://github.com/heartcombo/simple_form).

We have created the path and view successfully, but now we need to add a Controller method so that when we POST some fields from our view to the search action it does something with them. The search_products path above states that the controller and action combination needs to be products#search so we need to add a search action in our products controller.

Open the `app/controllers/products_controller.rb` file and add the following code (highlighted in bold) after the destroy action:

<pre>
# DELETE /products/1
def destroy
  @product.destroy
  redirect_to products_url, notice: 'Product was successfully destroyed.'
end

<strong>
# POST /products/search
def search
  @products = Product.where(name: params[:search][:name])
  render :index
end
</strong>
</pre>

We can access what was entered in the name field on the search form using the parameters hash. It is a hash of hashes which is why to access the name entered we use the following code:

```
params[:search][:name]
```

To find the correct product(s) we need to query the database. We could write SQL (Structured Query Language) ourselves. However, Rails helpfully provides us with simple class methods on models that enable us to pass in parameters and let Rails handle the querying and fetching of records.

One of these methods is `where`, which takes a hash of options and retrieves all records that match them in the database. For example, `Product.where(name: params[:search][:name])` retrieves all product records where the name equals what was entered in the search form.

The result of the `where` method is an array of objects, and this is assigned to the instance variable `@products`.

The final line `render :index` renders the view for the `index` action, which is `app/views/products/index.html.haml`, and passes the `@products` instance variable to it.

Start a Rails server:

```
bundle exec rails s
```

In a separate terminal tab/window start a Webpack server:

```
bin/webpack-dev-server
```

Visit your application by going to http://localhost:3000 in your browser - if you create some products you should now be able to search for them (but note the search will be case-sensitive).

## Task 2 - Associating categories to products

We can currently manage categories, which have a code and a name, but we have no way of associating them with products. We will do this now.

Generate a migration that adds a `category_id` integer column to the products table. This will create a foreign key which allows you to associate a product to a category using the id in the database. Remember to run the migration:

```
bundle exec rails db:migrate
```

1. See https://guides.rubyonrails.org/v7.0/active_record_migrations.html#creating-a-migration for information about this.

2. Add a `belongs_to` association in `app/models/product.rb` so that a product `belongs_to` a category. For more information see: https://guides.rubyonrails.org/v7.0/association_basics.html#the-belongs-to-association

3. Add a link to the menu at the top of the application to access the categories index page, if you have not already done so. You can find the menu in: `app/views/layouts/application.html.haml`

4. Add a new field to `app/views/products/_form.html.haml` that allows you to assign a category when creating a product - use the field identifier 'category'. You can use the `association` helper to do this which is explained here: https://github.com/heartcombo/simple_form#associations

5. You will need to permit ‘category_id’ in the products controller. You can do this by adapting the existing method `product_params`.

6. Amend the views for the products index page and show page to display the name of the category associated with a product.

7. You will need to restart your Rails server for some of these changes to take effect.

## Task 3 - Validations

A very common task when building web applications is adding validations that check user input to ensure it is present and sensible. Active Record provides us with lots of helpers to make this easy.

Add the following validations for the `Product` model in `app/models/product.rb`:

```
validates :name, :description, :cost, presence: true
validates :cost, numericality: { greater_than: 0 }
```

The above tells Rails that the name, description and cost all have to be present, and that the cost must be greater than 0. Check the effect of this by trying to add a new product in your application with invalid data.

Implement some suitable validations for the `Category` model. You can find details of the available helpers here: https://guides.rubyonrails.org/v7.0/active_record_validations.html#validation-helpers

## Task 4 - Scopes

Scopes are a feature of Active Record that enable us to group a collection of records. This is typically a subset of one type of model that all share a common property or properties. We will now implement a feature to deactivate old categories.

Generate a migration to add a boolean column to the categories table, and call this column `active`.

Given that we probably have existing records in the database it is a good idea to give this field a sensible default. In this case we will set it to true.

```
add_column :categories, :active, :boolean, default: true
```

Remember to run the migration and restart your Rails server.

Add the new `active` field to the categories form (`f.input :active, as: :boolean`). Do not forget to permit it as a parameter in the controller. Also, make it visible on the categories index page (`%td= category.active ? 'Yes' : 'No'`).

Write a scope to return only the active categories. We do this in the category model.

```
scope :active, -> { where(active: true) }
```

The above defines a scope for categories which will return only the categories with the active field set to true.

You can check that the above is working by editing a few categories to make them inactive and then changing the categories controller to use the `active` scope.

You will have something that looks like:

```
def index
  @categories = Category.all
end
```

Which should be changed to use the scope:

```
def index
  @categories = Category.active
end
```

## Task 5 - Querying with associations

We will now amend the product search feature from Task 1 to also allow customers to search for products by category.

Add a select box to your existing products search form:

```
= f.input :category_id, as: :select, collection: Category.active
```

* `as: :select` - tells Simple Form that you would like a select box, rather than a text box.
* `collection: Category.active` - tells Simple Form to only include active categories using the scope we added in Task 4.

The only other thing needed is to handle this in the products controller by replacing the `search` method with the following:

```
def search
  @products = Product.where(category_id: params[:search][:category_id])
  @products = @products.where(name: params[:search][:name]) if params[:search][:name].present?
    
  render :index
end
```

There are two things to notice here. Firstly, we have added the line:

```
@products = Product.where(category_id: params[:search][:category_id])
```

Similar to before, we have obtained the `category_id` from the form parameters and retrieved products associated with that category.

Secondly, we have this line:

```
@products = @products.where(name: params[:search][:name]) if params[:search][:name].present?
```

At first glance this may look like we are overwriting `@products`, but what is happening here is that Active Record is chaining queries together.

`@products` will initially represent all of the products associated with the specified category, but the query will then be amended with an additional constraint of having a name that has matched the search (but only if a name was searched for).

Amend the `search` method so that results are returned when you search for a product by name without choosing a category.

## Task 6 - Many-to-many associations

Many-to-many relationships can be defined with a `has_and_belongs_to_many` association. We will be making a simple many-to-many relationship implemented using a linker table.

You can read more about these at: https://guides.rubyonrails.org/v7.0/association_basics.html#the-has-and-belongs-to-many-association

We are going to do this within our ecommerce application by adding tags to categories.

To begin, create a new 'tag' model:

```
bundle exec rails g model Tag name:string
```

Migrate your database to add the new table to it.

Generate a new migration to create the linker table:

```
bundle exec rails g migration create_join_table_categories_tags categories:index tags:index
```

Open the file that is generated, and it should look something like this:

```
class CreateJoinTableCategoriesTags < ActiveRecord::Migration[6.1]
  def change
    create_join_table :categories, :tags do |t|
      t.index [:category_id, :tag_id]
      t.index [:tag_id, :category_id]
    end
  end
end
```

If everything looks correct, migrate your database to generate the linker table.

You can generate some example tags by running the following command:

```
bundle exec rails runner '(1..8).each { |num| Tag.new(name: "Tag #{num}").save! }'
```

If you see an error at this point, make sure you have followed the above steps correctly, and that you have migrated your database to add the two new tables.

Before you can start associating tags with categories, you need to tell Active Record about the relationship between the two models.

Update the category model first so that the line *in bold* below is added to it:

<pre>
# app/models/category.rb
class Category < ApplicationRecord
  <strong>has_and_belongs_to_many :tags</strong>
  # ...
</pre>

Then update the tag model with the line *in bold* below:

<pre>
# app/models/tag.rb
class Tag < ApplicationRecord
  <strong>has_and_belongs_to_many :categories</strong>
  # ...
</pre>

Amend the form for creating new and editing existing categories to add the following line which enables the addition of tags to a category:

```
= f.association :tags, as: :check_boxes
```

Update the categories controller to permit the setting of tags:

```
...
  def category_params
    params.require(:category).permit(:code, :name, :active, tag_ids: [])
  end
...
```

You should now be able to specify tags for a category. Amend the categories index page to display them using this: `category.tags.map(&:name).join(', ')`
