---
title: Decorators and Concerns in Ruby
author: codemilan
date: 2024-09-22 02:00:00 +0545
categories: [programming, design patterns]
tags: [ruby, rails] # TAG names should always be lowercase
post_images_dir: 'decorators-and-concerns-in-ruby-2024-09-22'
---

### Concerns

Concerns are a way to encapsulate reusable code that can be shared across controllers or models. This is particularly useful if you find yourself writing the same code in multiple places. Let's see how you can use them effectively in Rails.

### Scenario

Imagine you have multiple controllers that need to handle pagination. Instead of writing the pagination logic in each controller, you can use a concern to keep your code DRY (Don't Repeat Yourself).

### Step-by-Step Guide

#### 1\. Create a Concern

First, create a concern module in the `app/controllers/concerns` directory. Let's call it `Paginatable`.

```ruby
# app/controllers/concerns/paginatable.rb
module Paginatable
    extend ActiveSupport::Concern

    included do
        before_action :set_page, only: [:index]
    end    

    def set_page
        @page = params[:page] || 1
    end

    def paginate(collection, per_page = 10)
        collection.page(@page).per(per_page)
    end 
end
```

In this concern, we define a `set_page` method to fetch the page number from the parameters, defaulting to 1 if not provided. The `paginate` method uses the `page` and `per` methods, which are part of the `kaminari` gem (a pagination gem).

#### 2\. Include the Concern in Controllers

Next, include this concern in any controller that needs pagination. For example, let’s include it in the `PostsController` and `CommentsController`.

```ruby
# app/controllers/posts_controller.rb
 class PostsController < ApplicationController
    include Paginatable
    
    def index
        @posts = paginate(Post.all)
    end 
end

# app/controllers/comments_controller.rb
 class CommentsController < ApplicationController
    include Paginatable
    
    def index
        @comments = paginate(Comment.all)
    end
end
```

#### 3\. Add Pagination to Views

Finally, update your views to display pagination links. Assuming you're using the `kaminari` gem, you can add the pagination links as follows:

```erb

<!-- app/views/posts/index.html.erb -->
<%= render @posts %>
<%= paginate @posts %>

<!-- app/views/comments/index.html.erb -->
<%= render @comments %>
<%= paginate @comments %>
```

### Benefits

* **DRY Code**: The pagination logic is centralized in one place, making your codebase cleaner and easier to maintain.
* **Reusability**: Easily include the pagination functionality in any controller by including the `Paginatable` concern.

### Additional Tips

* You can add more methods to your concerns if needed, such as setting default per-page limits or handling edge cases.
* Concerns can also be used in models to share logic like validations, callbacks, or scopes.

By using concerns in this way, you enhance the modularity and maintainability of your Rails application. Happy coding! ✔️🔨🤖🔧

### Decorator Design Pattern Overview

The **Decorator Design Pattern** is a structural design pattern that allows behavior to be added to an individual object dynamically, without affecting the behavior of other objects from the same class. Decorators provide a flexible alternative to subclassing for extending functionality.

In simple terms, a **decorator wraps an object** to add extra functionality without modifying the original object's code. It allows functionality to be added incrementally by "stacking" decorators.

### Key Concepts of Decorator Pattern

* **Composition over Inheritance**: Instead of creating subclasses to extend functionality, you wrap the original class with another class (the decorator).
* **Open/Closed Principle**: The decorator allows you to extend an object's behavior without modifying its structure, keeping the original class "closed for modification" but "open for extension."
* **Wrapper**: The decorator wraps the original object and delegates calls to it while adding its own behavior.

### Use-Cases of Decorator Pattern using Ruby

Let’s explore **types of use-cases** for decorators, how to use them in Ruby, and when they are most beneficial.

* * *

1\. **Simple Case: Adding New Behavior Dynamically**
----------------------------------------------------

Imagine you have a simple `Coffee` class, and you want to add new features to it like milk, sugar, etc., but without modifying the `Coffee` class.

### Code Example

```ruby
# Basic component
class Coffee
    def cost
        5
    end
end  

# Base Decorator (other decorators will inherit from this) 
class CoffeeDecorator
    def initialize(coffee)
        @coffee = coffee 
    end
    
    def cost
        @coffee.cost
    end
end

# Concrete Decorator 1: Adding Milk 
class MilkDecorator < CoffeeDecorator
    def cost
        @coffee.cost + 2
    end
end  

# Concrete Decorator 2: Adding Sugar
class SugarDecorator < CoffeeDecorator
    def cost
        @coffee.cost + 1
    end
end

# Usage
coffee = Coffee.new
puts "Plain Coffee: $#{coffee.cost}"
# => 5  
coffee_with_milk = MilkDecorator.new(coffee) 
puts "Coffee with Milk: $#{coffee_with_milk.cost}" 
# => 7  
coffee_with_milk_and_sugar = SugarDecorator.new(coffee_with_milk) 
puts "Coffee with Milk and Sugar: $#{coffee_with_milk_and_sugar.cost}" 
# => 8
```

### Explanation

* `Coffee` class represents a basic object (in this case, a cup of coffee).
* `CoffeeDecorator` is the base class for decorators that will wrap around the `Coffee` object.
* `MilkDecorator` and `SugarDecorator` are concrete decorators that modify the behavior by adding their own cost.

This pattern allows you to add different combinations of features without modifying the original `Coffee` class.

* * *

2\. **Conditional Decoration**
------------------------------

Sometimes you need to apply additional behavior to an object based on certain conditions. In Ruby, this is easily achievable using decorators.

### Code Example: Logging Decorator

Suppose we have a `Service` class that performs actions, and we want to log each action **only under certain conditions**, such as in the production environment.

```ruby
class Service
    def perform
        "Performing important work!"
    end
end

class LoggingDecorator
    def initialize(service)
        @service = service
    end
    
    def perform
        puts "Logging: Start service at #{Time.now}"
        result = @service.perform
        puts "Logging: End service at #{Time.now}"
        result
    end
end

# Usage
service = Service.new  if ENV['RAILS_ENV'] == 'production'
service = LoggingDecorator.new(service)
puts service.perform
```

### Explanation

* The `LoggingDecorator` adds logging functionality only when the app is in the production environment.
* This type of conditional decoration allows you to decide when to apply extra functionality without cluttering the base class with unnecessary behavior.

* * *

3\. **Chaining Multiple Decorators**
------------------------------------

A significant strength of the decorator pattern is that you can chain multiple decorators together to extend an object's functionality step-by-step.

### Code Example: Chained Decorators for Text Formatting

Imagine a scenario where you have a basic `Text` class, and you want to apply multiple types of formatting (bold, italics, underline) in various combinations.

```ruby
class Text
    def initialize(content)
        @content = content
    end
    
    def content
        @content
    end
end

class BoldDecorator
    def initialize(text)
        @text = text
    end
    
    def content
         "<b>#{@text.content}</b>"
    end
end

class ItalicDecorator
    def initialize(text)
        @text = text   
    end
    
    def content
        "<i>#{@text.content}</i>"
    end
end

class UnderlineDecorator
    def initialize(text)
        @text = text
    end
    
    def content
        "<u>#{@text.content}</u>"
    end 
end

# Usage
text = Text.new("Hello, world!")
bold_text = BoldDecorator.new(text)
puts bold_text.content 
# => "<b>Hello, world!</b>"
italic_bold_text = ItalicDecorator.new(bold_text)
puts italic_bold_text.content 
# => "<i><b>Hello, world!</b></i>"
underline_italic_bold_text = UnderlineDecorator.new(italic_bold_text)
puts underline_italic_bold_text.content
# => "<u><i><b>Hello, world!</b></i></u>"
```

### Explanation

* Each decorator wraps the original text object and applies a new transformation (`BoldDecorator`, `ItalicDecorator`, `UnderlineDecorator`).
* The decorators can be stacked in different combinations, giving you flexibility in applying various types of formatting.
* You don’t need to modify the `Text` class to support formatting.

* * *

4\. **Behavioral Extension (Methods that Don't Exist in the Original Object)**
------------------------------------------------------------------------------

Sometimes, you want to extend an object's behavior with methods that don't exist in the original class. Decorators can add entirely new methods to an object.

### Code Example: Adding Behavior

```ruby
class Report
    def generate
        "Report data"
    end
end

class EmailDecorator
    def initialize(report)
        @report = report
    end
    
    def generate
        @report.generate
    end
    
    def send_email     
        puts "Sending email with report: #{@report.generate}"
    end
end
# Usage
report = Report.new
email_decorator = EmailDecorator.new(report)
puts email_decorator.generate 
# => "Report data" 
email_decorator.send_email
# => "Sending email with report: Report data"`
```

### Explanation

* `EmailDecorator` adds a new behavior `send_email` that doesn’t exist in the original `Report` class.
* This shows how decorators can add entirely new methods and responsibilities without changing the original object's class.

* * *

5\. **Using Decorators for Object Transformation (Type Modification)**
----------------------------------------------------------------------

Another use-case is when you want to **modify how an object interacts with other parts of the system**. For example, a decorator might wrap a database object and transform its output for use in the presentation layer.

### Code Example: Formatting Database Models for Display

```ruby
class Product
    attr_reader :name, :price
    
    def initialize(name, price)
        @name = name
        @price = price
    end
end

class ProductDecorator
   def initialize(product)
        @product = product
    end
    
    def display_price
        "$#{'%.2f' % @product.price}"
    end
    
    def title
        @product.name.upcase
    end
end
# Usage 
product = Product.new("Laptop", 999.95)
decorated_product = ProductDecorator.new(product)
puts decorated_product.display_price
# => "$999.95"
puts decorated_product.title
# => "LAPTOP"`
```

### Explanation

* The decorator changes how the product's name and price are displayed without modifying the `Product` class itself.
* This is helpful when you want to transform data for presentation, especially when dealing with models directly from the database.

## Implementing Decorators in Rails

Decorators in Rails are used to extend or modify the behavior of models or other objects without altering their underlying code. They’re a part of the **Decorator Design Pattern** and allow for a clean separation between business logic (which stays in the model) and presentation logic (which goes in the decorator).

### Why Use Decorators?

Decorators are useful for:

* Keeping your models focused on business logic.
* Adding view-specific methods or transformations.
* Avoiding "fat" models by moving code related to formatting or presentation elsewhere.

Instead of putting everything in your models or views, decorators help organize code that doesn't belong strictly to business logic. This keeps things more readable, maintainable, and modular.

While Rails doesn’t have a built-in decorator pattern, you can easily implement it using gems like `draper` or by manually creating decorator classes.

#### Example 1: Using Draper Gem

Let's use the `draper` gem, which is a popular and simple way to add decorators in Rails.

* * *

1\. **Add Draper Gem**: First, add `draper` to your `Gemfile` and run `bundle install`:

```ruby
gem 'draper'
```

* * *

2\. **Generate a Decorator**: Generate a decorator for a model (e.g., `Post`):

```bash
rails generate decorator Post
```

This will create a decorator class in `app/decorators/post_decorator.rb`.

* * *

3\. **Define Presentation Logic**: Add methods for display-related logic in the `PostDecorator` class.

```ruby
# app/decorators/post_decorator.rb
    class PostDecorator < Draper::Decorator
    delegate_all
    # Example of a custom method
    def formatted_publish_date
        object.published_at.strftime("%B %d, %Y")
    end
    
    def truncated_content
        h.truncate(object.content, length: 100)
    end
end
```

* `delegate_all` delegates all methods to the original model (`object`).
* Use `h` to call view helpers (like `truncate`, `number_to_currency`, etc.).

* * *

4\. **Use the Decorator in Controllers/Views**: To use the decorator in your controller or view, you can decorate your model:

```ruby
# In a controller 
@post = Post.find(params[:id]).decorate
# In a view
<p>Published on: <%= @post.formatted_publish_date %></p> <p>Content: <%= @post.truncated_content %></p>
```

#### Example 2: Manually Creating a Simple Decorator

If you don't want to use the `draper` gem, you can manually create a decorator class. Here's how:

* * *

1\. **Create a Decorator Class**: You can place your decorator in `app/decorators`.

```ruby
# app/decorators/post_decorator.rb
 class PostDecorator
    def initialize(post)
        @post = post
    end
    
    def formatted_publish_date
        @post.published_at.strftime("%B %d, %Y")
    end
    
    def truncated_content
        @post.content.truncate(100)
    end
end
```

* * *

2\. **Use the Decorator**:

```ruby
# In a controller 
@post = PostDecorator.new(Post.find(params[:id]))  
# In a view 
<p>Published on: <%= @post.formatted_publish_date %></p> <p>Content: <%= @post.truncated_content %></p>
```

* * *

### Advantages of Decorators

* **Separation of Concerns**: Business logic stays in the model, while presentation-related logic is moved to decorators.
* **Clean Views**: Reduces the need for complex view helpers.
* **Reusability**: Easily reuse decorated logic across different views.

### When to Use Decorators

* When you have methods that are only relevant to views (like formatting dates, truncating text, or converting units).
* If you find yourself adding too many methods in the model that are only for display purposes.

This is a great way to keep things tidy and enhance maintainability in your Rails apps! 💡

### Conclusion

The **Decorator Design Pattern** provides a flexible, modular, and scalable way to add functionality to objects in Ruby. Ruby’s dynamic nature makes implementing decorators easy, whether you do it manually or use libraries like `draper`. Decorators help keep your code **clean**, **modular**, and **easy to maintain**.
