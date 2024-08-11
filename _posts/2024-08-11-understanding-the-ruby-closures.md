---
title: Understanding the Ruby Closures
author: codemilan
date: 2024-08-11 02:00:00 +0545
categories: [programming]
tags: [ruby] # TAG names should always be lowercase
post_images_dir: '' # 'understanding-the-ruby-closures-2024-08-11'
---

In Ruby, the term "closure" usually refers to objects that capture the surrounding context in which they were defined, allowing them to be executed later while retaining access to the variables and methods available in that context. There are three main types of closures in Ruby:

1.  **Blocks**: Blocks are anonymous functions that can be passed to methods. They are defined using either curly braces `{}` or `do...end`. Blocks can access variables from the scope in which they were defined.
    
2.  **Procs**: Procs are objects that encapsulate blocks. They can be stored in variables, passed to methods, and called multiple times. Procs are created using `Proc.new`, `proc`, or the `lambda` method. Unlike blocks, Procs can be reused and passed around like any other object.
    
3.  **Lambdas**: Lambdas are a special kind of Proc with more strict argument handling and return behavior. A lambda checks the number of arguments passed to it and throws an error if there is a mismatch. Unlike regular Procs, lambdas treat the `return` statement differently; they return control to the caller of the lambda, not the enclosing method.
    

**Example:**

``` ruby
# Block example 
def example_block
  yield "Hello" if block_given?
end

example_block { |msg| puts msg }  

# Proc example 
proc_example = Proc.new { |msg| puts msg }
proc_example.call("Hello from Proc")  

# Lambda example 
lambda_example = ->(msg) { puts msg }
lambda_example.call("Hello from Lambda")
```
### Technical Details

Closures in Ruby capture and store the surrounding context in which they were created. This captured context includes the variables, methods, and even the execution environment. The mechanism that enables this is often referred to as "variable binding" or "environment binding."

Under the hood, Ruby closures store references to the variables and methods in a structure known as the "binding." The binding is an object that contains all the variables and their values in the current execution context at the time the closure is created. This allows the closure to "carry" its context with it wherever it goes.

1.  **Variable Binding**: When a closure (like a block, Proc, or lambda) is defined, it "remembers" the environment where it was created. This environment includes any local variables or methods that were in scope at the time of its creation. These variables are stored in the closure's internal state, allowing the closure to access them even when it's executed later in a different scope.
    
2.  **Storage**: The closure's internal state, including the variable bindings, is stored in the closure object itself. This means that the closure has its own copy of the variables that were in scope when it was created. If those variables change after the closure is created, the closure will still refer to the original values unless they are explicitly changed within the closure.

**Example:**

```ruby
def create_closure
  msg = "Hello from closure"
  Proc.new { puts msg } 
end  

closure = create_closure
closure.call # Output: "Hello from closure" 
# Even after msg is out of scope in the method, the closure still holds onto it.
```

In this example, `msg` is out of scope after the `create_closure` method returns. However, the `Proc` created inside the method captures `msg` and retains access to it. When `closure.call` is executed, it still has access to `msg` even though the variable is no longer available in the outer scope.

**What happens if "msg" variable is garbage collected before the closure is called ?**

In Ruby, closures hold a reference to the variables they capture, which prevents those variables from being garbage collected as long as the closure itself is still accessible. This means that as long as the closure (`Proc`, `lambda`, or `block`) exists and can be called, the variables it references cannot be garbage collected.

**Detailed Explanation:**

When a closure captures a variable, the Ruby interpreter creates a reference to that variable within the closure's internal environment. This reference ensures that the variable remains in memory and is not eligible for garbage collection, even if it goes out of scope in the context where it was originally defined.

**Example:**

```ruby
def create_closure
  msg = "Hello from closure"
  Proc.new { puts msg }
end
closure = create_closure # At this point, `msg` is out of scope in the method,
# but it is not garbage collected because `closure` holds a reference to it.
closure.call # Output: "Hello from closure"`
```

In this example:

*   The variable `msg` is captured by the `Proc` created inside the `create_closure` method.
*   Even after `msg` goes out of scope when the method exits, it is not garbage collected because the `Proc` holds a reference to it.
*   When `closure.call` is invoked, the `Proc` accesses `msg`, demonstrating that `msg` is still available.

**What If the Closure Itself Is No Longer Needed?**

If the closure is no longer referenced by any part of the program, then both the closure and the variables it captured become eligible for garbage collection. This ensures that memory is managed efficiently and unused objects are cleaned up.

In short, as long as the closure is alive and has the potential to be executed, the variables it captures are not garbage collected. Ruby's garbage collector only frees the memory for those variables when the closure itself is no longer accessible.

### Example: Using a Closure in a Rails Controller

Here's an example of how closures can be used in a Rails context. This example demonstrates the use of closures to capture the context within a controller action and then execute it later, perhaps in a background job or a callback.

```ruby
class UsersController < ApplicationController
  def create
    user_params = params.require(:user).permit(:name, :email,:password)
    user = User.new(user_params)
    
    if user.save 
      # Closure capturing the user object and a success message
      success_callback = Proc.new do
        logger.info "User #{user.name} was successfully created."
        UserMailer.welcome_email(user).deliver_later
      end
      
      # The closure is passed to another method to be executed later
      process_user(user, success_callback)
      
      redirect_to user_path(user), notice: 'User was successfully created.'
    else       
      render :new
    end   
  end

  private
  
  def process_user(user, callback)
    # Simulate some processing
    sleep 2 # Imagine this is a long-running process
    
    # Execute the callback after processing 
    callback.call if user.persisted?
  end
end
```

**Explanation:**

1.  **Capture Context in Closure**:
    
    *   In the `create` action, the `success_callback` closure captures the `user` object and a success message. This closure is defined using `Proc.new`.
2.  **Pass Closure for Later Execution**:
    
    *   The `success_callback` is then passed to the `process_user` method. This method simulates some processing (like a long-running task) and later executes the callback using `callback.call`.
3.  **Delayed Execution**:
    
    *   The closure is executed after the processing completes, allowing the `UserMailer.welcome_email(user).deliver_later` to be called and the log message to be written.

This example shows how closures can capture the local context within a Rails controller action and be executed at a later point, which is particularly useful for tasks like background processing, delayed notifications, or complex business logic that depends on the state of the application at a specific time.

**Execution Details:**

In Ruby (and Rails), when you call a method, the entire method executes in a single thread of execution unless you explicitly create a new thread, use asynchronous processing, or offload work to a background job. In the given example, closure is called synchronously within the same thread of execution after the `process_user` method is invoked.:

1.  **Method Call Sequence**:
    
    *   The `create` method starts executing.
    *   Inside `create`, after the user is saved, the `process_user` method is called, and the `success_callback` closure is passed to it.
    *   The `process_user` method runs synchronously, meaning it completes its operations (including invoking the closure) before control returns to the `create` method.
2.  **Closure Execution**:
    
    *   The closure (`success_callback.call`) is invoked inside `process_user` after all other processing in that method.
    *   Since `process_user` is called from within the `create` method, `create` will not finish executing until after `process_user` (and hence the closure) has finished.

**In Short:**

*   The `create` method is called.
*   The user is created and saved.
*   `process_user` is called with the closure.
*   The closure is executed within `process_user`.
*   `process_user` returns, and then `create` finishes executing.

So, **`create` will not return until after the closure has been executed**. The Rails response will only be sent to the client after the entire controller action (`create`) has completed.

### Guidelines and Use cases

Closures in Ruby are powerful tools that capture the environment in which they are defined, allowing for flexible and dynamic programming. Here are some common use cases and guidelines for when to use closures:

### 1\. **Deferred Execution**

Closures allow you to define a block of code that can be executed later, often based on certain conditions or events. This is useful in scenarios where the exact behavior depends on runtime conditions.

**Use Case**: **Callbacks and Hooks**

In Rails, callbacks are often implemented using closures. For example, you might want to run a specific piece of code after a model is saved.

```ruby
class User < ApplicationRecord
  after_create :send_welcome_email
  
  private

  def send_welcome_email
    Proc.new { UserMailer.welcome_email(self).deliver_later }.call
  end
end
```

### 2\. **Encapsulation of Logic**

Closures can encapsulate logic that needs to be passed around as an object. This is useful when you want to pass behavior (not just data) to methods or objects.

**Use Case**: **Passing Behavior to Methods**

Suppose you have a method that applies different kinds of discounts to products. You can use a closure to define the discount logic and pass it to the method.

```ruby 
def apply_discount(products, discount_proc)
  products.each { |product|
    product.price = discount_proc.call(product.price)
  }
end
ten_percent_off = Proc.new { |price| price * 0.9 }
apply_discount(Product.all, ten_percent_off)
```

### 3\. **Dynamic Method Generation**

Closures can be used to generate methods dynamically, which is particularly useful in metaprogramming. This allows Rubyists to write DRY (Don't Repeat Yourself) code and reduce boilerplate.

**Use Case**: **Dynamic Finders in Active Record**

In earlier versions of Rails, dynamic finders like `find_by_name_and_email` were generated using metaprogramming and closures.

```ruby
class User < ApplicationRecord
  [:name, :email].each do |attribute|
    define_singleton_method("find_by_#{attribute}") do |value|
      where(attribute => value).first
    end
  end
end
```

### 4\. **Lazy Evaluation**

Closures allow for lazy evaluation, meaning the code is not executed until it is needed. This can improve performance and resource management in certain scenarios.

**Use Case**: **Lazy Initialization**

Suppose you have a resource-intensive operation that should only be executed if needed.

```ruby
class Report
  def initialize
    @data = Proc.new { fetch_data }   
  end
  
  def data
    @data.call if @data.is_a?(Proc)
    
    @data
  end
  
  private
  
  def fetch_data
    sleep 2 # Simulate a heavy operation
    
    "Fetched data"
  end
end

report = Report.new
puts report.data # Fetches the data only when needed
```
### 5\. **Context Preservation**

Closures preserve the context they were created in, making them ideal for scenarios where you need to maintain access to specific variables or methods even when the closure is executed in a different scope.

**Use Case**: **Event Handling**

In event-driven programming, closures are often used to capture and preserve the state at the time the event handler was registered.
```ruby
class Button
  def initialize(label)
    @label = label   
  end
  
  def on_click(&block)
    @click_handler = block
  end
  
  def click
    @click_handler.call if @click_handler
  end
end

button = Button.new("Submit")
button.on_click { puts "Button #{@label} clicked" }
button.click  # Outputs "Button Submit clicked"
```

### When to Use Closures

*   **When you need to defer execution**: Use closures when you need to define a block of code to be executed later, based on some condition or event.
*   **When encapsulating logic**: Use closures when you need to pass around behavior, not just data, or when you want to encapsulate reusable logic.
*   **When dynamic or lazy evaluation is required**: Use closures when you want to define operations that should only be performed when absolutely necessary.
*   **When preserving context is essential**: Use closures when you need to retain access to variables or methods from the scope in which the closure was defined.

Closures are highly versatile and can be used to write more modular, maintainable, and efficient code when applied appropriately.
