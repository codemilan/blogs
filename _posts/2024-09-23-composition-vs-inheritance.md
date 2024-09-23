---
title: Composition vs Inheritance
author: codemilan
date: 2024-09-23 02:00:00 +0545
categories: [programming, design patterns]
tags: [ruby] # TAG names should always be lowercase
post_images_dir: 'composition-vs-inheritance-2024-09-23'
---

The principle **"Composition over Inheritance"** suggests that, instead of using inheritance to extend functionality, it's often better to achieve it by combining objects and delegating behavior. This promotes flexibility and helps avoid the rigid, tightly-coupled hierarchies that inheritance can create. In composition, you build classes by combining various small, focused components rather than extending classes through inheritance.

### Why Favor Composition Over Inheritance?

1. **Loose Coupling**: Composition results in loose coupling because each component can be swapped out or modified without affecting the others.
2. **Single Responsibility**: It promotes separation of concerns by allowing components to focus on a single responsibility.
3. **Flexibility**: You can compose different behaviors at runtime, while inheritance is static and defined at compile time.

Let’s break this down with examples.

* * *

1\. **Inheritance Example**
---------------------------

### Problem

Imagine you are building a notification system where you want to send notifications via different mediums like `Email` and `SMS`. Using inheritance, you might structure your classes like this:

```ruby
# Base Notification class
class Notification
   def send(message)
        raise NotImplementedError, 'Subclasses must implement send method'
    end
end 

# Email notification class
class EmailNotification < Notification
   def send(message)
        puts "Sending Email: #{message}"
   end 
end

# SMS notification class
class SMSNotification < Notification
   def send(message)
        puts "Sending SMS: #{message}"
    end
end

# Usage 
email = EmailNotification.new email.send("Hello via Email")
# => Sending Email: Hello via Email  
sms = SMSNotification.new sms.send("Hello via SMS")
# => Sending SMS: Hello via SMS
```

### Issues with Inheritance

* **Rigid hierarchy**: If you need to add more notification types like `PushNotification` or `SlackNotification`, you will need to add more subclasses.
* **Code Duplication**: Every subclass must implement the `send` method, and the more notification types you add, the more repetitive code you'll get.
* **Difficult to Extend**: What if you want a `Notification` that combines both `Email` and `SMS`? This requires either multi-level inheritance (which gets complex fast) or duplicating logic in subclasses.

* * *

2\. **Composition Example**
---------------------------

### Solution

Now let's refactor the same problem using **Composition**. Instead of subclassing `Notification`, we’ll build small, composable classes for each behavior (e.g., email and SMS sending) and combine them.

```ruby
# Separate behaviors
class EmailSender
    def send(message)
        puts "Sending Email: #{message}"
    end
end

class SMSSender
    def send(message)
        puts "Sending SMS: #{message}"    
    end
end  

# Notification class uses composition to send notifications
class Notification   
    def initialize(sender)
        @sender = sender
    end

    def send(message)
        @sender.send(message)
    end
end

# Usage
email_sender = EmailSender.new
sms_sender = SMSSender.new
email_notification = Notification.new(email_sender)
email_notification.send("Hello via Email")
# => Sending Email: Hello via Email
sms_notification = Notification.new(sms_sender)
sms_notification.send("Hello via SMS")
# => Sending SMS: Hello via SMS
```

### Benefits of Composition

* **Flexibility**: You can easily swap or combine behaviors. For example, you could add more types of senders (`PushNotificationSender`, `SlackSender`) without changing the `Notification` class.
* **Single Responsibility**: Each class (`EmailSender`, `SMSSender`) has a single responsibility and can be modified or replaced independently.
* **No Rigid Hierarchies**: There’s no need for complex inheritance trees. The system remains flexible and scalable.

* * *

3\. **Combining Behaviors (Composition Example)**
-------------------------------------------------

Now, let’s take this further by showing how composition can **combine multiple behaviors** dynamically.

```ruby
# Behavior classes
class EmailSender
  def send(message)
    puts "Sending Email: #{message}"
  end
end

class SMSSender
  def send(message)
    puts "Sending SMS: #{message}"
  end
end

# Composite sender class that can combine multiple senders
class MultiSender
  def initialize(*senders)
    @senders = senders
  end

  def send(message)
    @senders.each { |sender| sender.send(message) }
  end
end

# Usage
email_sender = EmailSender.new sms_sender = SMSSender.new
# Single sender example
email_notification = Notification.new(email_sender)
email_notification.send('Email only')
# => Sending Email: Email only  # Composite example with multiple
senders multi_sender = MultiSender.new(email_sender, sms_sender)
multi_notification = Notification.new(multi_sender)
multi_notification.send('Email and SMS')
# => Sending Email: Email and SMS # => Sending SMS: Email and SMS
```

### Explanation

* **Flexible Composition**: By using `MultiSender`, we can dynamically combine behaviors. In this case, the notification sends both an email and an SMS without modifying the `Notification` class.
* **Dynamic Behavior**: The `MultiSender` class takes multiple senders as input, allowing you to compose any number of senders without writing extra subclasses.

* * *

4\. **Use Case: Decorator Pattern with Composition**
----------------------------------------------------

Another use-case for **composition over inheritance** is when adding or modifying behavior dynamically. This is typically seen in the **Decorator Pattern**, where you can "wrap" objects in decorators to enhance their functionality.

### Code Example: Decorator with Composition

```ruby
class BaseNotification
  def send(message)
    puts("Base Notification: #{message}")
  end
end

# Decorators that extend functionality
class EmailDecorator
  def initialize(notification)
    @notification = notification
  end

  def send(message)
    @notification.send(message)
    puts "Also sending Email: #{message}"
  end
end

class SMSDecorator
  def initialize(notification)
    @notification = notification
  end

  def send(message)
    @notification.send(message)
    puts "Also sending SMS: #{message}"
  end
end
# Usage
notification = BaseNotification.new
email_notification = EmailDecorator.new(notification)
sms_email_notification = SMSDecorator.new(email_notification)
sms_email_notification.send('Hello')
# => Base Notification: Hello
# => Also sending Email: Hello
# => Also sending SMS: Hello
```

### Explanation

* **Base Class (`BaseNotification`)**: Defines the basic notification behavior.
* **Decorators (`EmailDecorator`, `SMSDecorator`)**: Use composition to wrap the base class and add additional behavior.
* **Flexibility**: You can apply any combination of decorators dynamically to change behavior without altering the core `BaseNotification`.

This is another clear example of **composition** giving us more flexibility than **inheritance** would.

* * *

## Comparison Summary

Aspect | Inheritance | Composition |
--- | --- | --- |
**Structure** | Class hierarchy (fixed structure) | Object composition (flexible structure) |
**Flexibility** | Hard to change or combine behaviors dynamically | Easy to combine, swap, and modify behaviors |
**Coupling** | Tight coupling between parent and child classes | Loose coupling through composition |
**Reuse** | Subclasses reuse parent functionality, but it's limited to the hierarchy | More reusable as components can be mixed and matched |
**Adding New Features** | Requires subclassing or modifying the hierarchy | Can be done by composing new behaviors |
**Single Responsibility** | Often violated due to inheriting unnecessary methods | Easier to follow SRP by separating behaviors |

* * *

## Conclusion

**Composition over Inheritance** encourages building systems by combining smaller, more focused objects rather than extending large class hierarchies. This leads to:

* **More modular, flexible, and maintainable code**.
* **Less code duplication** and **better reuse** of individual behaviors.
* Easier to follow design principles like **Single Responsibility** and **Open/Closed Principle**.

In most cases, when deciding between composition and inheritance, composition tends to result in more scalable and maintainable code.

## Why most programmer prefers inheritance rather than composition ?

Although **composition** offers many benefits over **inheritance** in terms of flexibility, maintainability, and modularity. However, many programmers still tend to prefer inheritance in many programming languages for several reasons:

## 1\. **Ease of Use & Simplicity**

* **Inheritance is built-in and intuitive**: Most object-oriented programming (OOP) languages, including Ruby, Python, Java, and C++, emphasize inheritance as one of the core principles of OOP. The concept of "is-a" relationships (where one class is a more specialized version of another) feels natural to many programmers.

* **Fewer moving parts**: Inheritance can seem simpler because it uses a well-defined hierarchy where a child class automatically inherits everything from the parent. This is easy to reason about and requires fewer components compared to composition, where objects must be combined or wired together manually.

    **Example**: To create a specialized `Car` from a `Vehicle`, inheritance is quick:

    ```ruby
    class Vehicle
        def move
            puts "Moving!"
        end 
    end  
    
    class Car < Vehicle
        def honk
            puts "Honk!"
        end    
    end  
     
    car = Car.new 
    car.move  # => Moving! 
    car.honk  # => Honk!
    ```

    This is simple and straightforward, and you get access to both the `move` and `honk` methods without explicitly wiring anything.

* * *

## 2\. **Inheritance Feels Familiar & Encouraged in OOP**

* **Strong OOP Paradigm**: OOP has long emphasized inheritance as one of the fundamental principles (along with encapsulation, abstraction, and polymorphism). Many developers learn OOP through the lens of inheritance, so they naturally use it.

* **Inheritance is encouraged by language design**: Many languages promote the use of inheritance. For instance, Java has `extends`, C++ has `public inheritance`, and Ruby uses `class < ParentClass`. The design of these languages makes inheritance a primary way to extend and reuse code.

    **Example in Java**:

    ```java
    class Animal {
        public void speak() { 
            System.out.println("Animal speaks");     
        }
    }

    class Dog extends Animal { 
        public void speak() {
            System.out.println("Dog barks");     
        } 
    }
    ```

    Here, it's simple to see how inheritance is hardwired into the syntax and encourages the creation of class hierarchies.

* * *

## 3\. **Perceived Simplicity of Reuse**

* **Automatic reuse**: Inheritance automatically provides access to all methods and properties of the parent class without needing to re-define or explicitly link behaviors. This "free" access makes inheritance feel convenient when extending functionality.

    **Example**:

    ```ruby
    class Vehicle
        attr_accessor :speed
       
        def initialize(speed)
            @speed = speed 
        end 
        
        def move
            puts "Moving at #{speed} km/h" 
        end 
    end
    
    class Car < Vehicle
        def open_trunk     
            puts "Trunk is open"
        end 
    end

    car = Car.new(60)
    car.move        # => Moving at 60 km/h 
    car.open_trunk  # => Trunk is open
    ```

    Without much additional work, a `Car` inherits both the `move` functionality and the `speed` attribute from the `Vehicle` class. It’s easy to get extended behavior without extra wiring.

* * *

## 4\. **Inheritance Supports Polymorphism Easily**

* **Polymorphism** is one of the key features of OOP, and inheritance allows you to leverage polymorphism easily. It enables a subclass to override methods of a parent class and allows objects of different types to be treated uniformly if they share the same parent.

    **Example (Method Overriding)**:

    ```ruby
    class Shape 
        def area
           raise "Must be implemented by subclass"
        end 
    end 
    
    class Rectangle < Shape 
        def initialize(width, height)  
            @width = width  
            @height = height 
        end  
        
        def area  
            @width * @height 
        end
    end 
    
    class Circle < Shape 
        def initialize(radius)   
            @radius = radius  
        end  
        
        def area   
            Math::PI * @radius**2  
        end 
    end  
    
    shapes = [Rectangle.new(3, 4), Circle.new(5)] 
    shapes.each { |shape| puts shape.area }
    ```

    With inheritance, you can easily treat all `Shape` objects the same way (`Rectangle`, `Circle`, etc.) and use method overriding to define their specific behaviors. This makes inheritance a natural choice for polymorphic behavior.

* * *

## 5\. **Faster to Prototype & Build with Inheritance**

* **Speed of Development**: For simple applications or prototypes, inheritance offers a quick way to extend behavior with minimal upfront design. This speed of getting a working solution can be attractive, especially in the early stages of development.

    **Example**:

    ```ruby
    class Person
        def initialize(name, age) 
            @name = name   
            @age = age  
        end 
        
        def info 
            "Name: #{@name}, Age: #{@age}"
        end 
    end 
    
    class Employee < Person 
        def initialize(name, age, job_title) 
            super(name, age)  
            @job_title = job_title 
        end  
        
        def info  
            super + ", Job Title: #{@job_title}" 
        end
    end 
    
    employee = Employee.new("Alice", 30, "Developer") 
    puts employee.info
    ```

    Here, inheritance allows a developer to quickly extend functionality from `Person` to `Employee` without needing to design additional classes for composing or delegating behavior.

* * *

## 6\. **Inheritance Provides a Natural Hierarchy for "Is-A" Relationships**

* **When there’s a clear "is-a" relationship**, inheritance can be a very natural way to model your system. For example, a `Dog` **is a** `Mammal`, or a `Car` **is a** `Vehicle`. In such cases, inheritance feels logical and clean.

    **Example**:

    ```ruby
    class Animal  
        def breathe   
            puts "Breathing!"  
        end 
    end
    
    class Dog < Animal
        def bark     
            puts "Barking!"
        end 
    end

    dog = Dog.new dog.breathe  # => Breathing!
    dog.bark   # => Barking!
    ```

    In this case, the "is-a" relationship between `Dog` and `Animal` is very clear, and inheritance feels natural for adding specialized behavior (`bark`) while retaining general behavior (`breathe`).

* * *

## 7\. **Historical Legacy and Lack of Awareness of Composition**

* **Legacy Systems**: Many codebases and libraries were built with a heavy focus on inheritance because it has been widely promoted since the advent of object-oriented programming. Developers often inherit this mindset from existing codebases.
* **Lack of Awareness**: Some developers may simply not be familiar with the benefits of composition or have not encountered scenarios where composition would be more beneficial. Since inheritance is heavily featured in introductory programming courses, composition often isn’t taught as an alternative until much later in a developer’s learning journey.

* * *

## Why You Should Still Consider Composition

Even though inheritance is widely used, composition offers **greater flexibility**, especially as your system grows more complex. Here’s a summary of why you might want to use composition more often:

* **Avoid deep hierarchies**: Inheritance can result in deep, rigid class hierarchies that are difficult to maintain or extend.
* **Better flexibility and reusability**: With composition, you can combine different behaviors dynamically and even swap them out at runtime.
* **Follows SOLID principles**: Composition better adheres to principles like the **Single Responsibility Principle** and the **Open/Closed Principle** by isolating different behaviors into separate objects.

## Conclusion: Why Programmers Prefer Inheritance (But Should Consider Composition)

Programmers often prefer inheritance because it’s **easy**, **familiar**, and directly supported by language syntax, allowing them to quickly extend functionality. It also provides a clear structure for "is-a" relationships and supports polymorphism out of the box.

However, **composition** offers more flexibility and modularity, making it a better choice for more complex, growing systems where maintainability, reuse, and avoiding tight coupling are critical.

While inheritance is a good tool for simple hierarchies, **composition** should be considered when:

* Behavior needs to be extended or combined dynamically.
* Multiple unrelated behaviors are needed (e.g., logging, notifications, etc.).
* Flexibility and loose coupling are priorities.

## Use-cases where inheritance will be better option than composition

While **composition** is often favored for flexibility and maintainability, there are cases where **inheritance** is a better choice, especially in terms of **security**, **enforcing constraints**, and **ease of doability** (i.e., implementation simplicity for specific scenarios).

Let’s break down **why inheritance can be more suitable** in some cases, focusing on areas like security and certain design requirements.

## 1\. **Security and Controlled Access**

Inheritance can sometimes offer **better control over sensitive behavior** or **data access** by enforcing strict relationships and encapsulation. When a subclass inherits from a parent class, it is guaranteed to follow the access control mechanisms and security protocols defined by the parent class.

### Case 1: Enforcing Data Access Rules

Imagine a system where access to certain data needs to be tightly controlled. A parent class can define strict access rules (private/protected attributes or methods), ensuring that any subclass must adhere to these rules, rather than trying to implement the rules from scratch.

**Example**: Let’s say you have a base class that deals with secure user data. You might use inheritance to ensure all subclasses handling that data respect the same access rules.

```ruby
class SecureData
    def initialize(data)
        @data = data
    end

    # Sensitive data can only be accessed via a secure method
    protected

    def encrypted_data
        encrypt(@data)
    end

    private

    def encrypt(data)
        "ENCRYPTED: #{data}"
        # Simplified encryption
    end
end

class UserProfile < SecureData
    def display_encrypted_data
        encrypted_data
        # Accessing protected method from parent
    end
end
user_profile = UserProfile.new('Sensitive User Info')
puts user_profile.display_encrypted_data
# => "ENCRYPTED: Sensitive User Info"
# Trying to access `encrypt` directly from outside will fail:
# user_profile.encrypt("data") # => Raises error: private method `encrypt' called
```

### Why Inheritance is Useful Here

* **Security of sensitive data**: The sensitive `encrypt` method is private in the `SecureData` class and can only be accessed through the proper methods defined in the class or its subclasses.
* **Enforced Access Control**: Subclasses must follow the same rules about accessing the sensitive data, as they cannot easily bypass or change protected/private methods.

This is important for security-related code, where making sure the correct access control is applied is critical.

* * *

## 2\. **Enforcing a Framework or API Structure**

Some systems rely on **strict hierarchies** that define a clear structure of how things should behave. In these cases, inheritance ensures that every subclass **follows the same framework or API contract**. This can be crucial for consistency, security, and reliability.

#### Case 2: Enforcing a Secure API

Imagine a scenario where you are building a system that needs to implement a specific, secure API interface. You can define the base API structure using inheritance to enforce the behavior of subclasses.

**Example**:

```ruby
class SecureAPI
  def initialize(api_key)
    @api_key = api_key
  end

  def authenticate
    raise 'API key is invalid!' unless valid_key?
  end

  # Abstract method to be implemented by subclasses
  def request_data
    raise NotImplementedError, 'Subclasses must implement request_data'
  end

  private

  def valid_key?
    @api_key == 'secret_api_key'
    # Simplified key validation
  end
end

class UserDataAPI < SecureAPI
  def request_data
    authenticate # Secure authentication
    'Fetching user data securely...'
  end
end
api = UserDataAPI.new('secret_api_key')
puts api.request_data
# => Fetching user data securely...
# Invalid API key will fail the authentication
UserDataAPI.new('wrong_api_key')
# invalid_api.request_data # => Raises error: API key is invalid!
```

### Why Inheritance is Preferred Here

* **Consistent Enforcement of Security**: By using inheritance, every API subclass is forced to use the authentication mechanism, ensuring security. The base class defines the authentication rules that cannot be easily bypassed.
* **Standardized API Structure**: Subclasses must implement the `request_data` method, enforcing that all API objects follow the same secure design.
* **Avoiding Duplication**: You don’t have to repeat authentication logic in every API subclass. Inheritance guarantees that subclasses cannot exist without adhering to security policies.

* * *

## 3\. **When Object Hierarchy Reflects Real-World Domain**

In some cases, **inheritance** aligns more naturally with **real-world relationships**, where certain entities are naturally hierarchical. This also provides an added benefit of **ensuring type safety** and **preventing misuse** of the objects by enforcing the intended use.

### Case 3: Security-Related Roles and Permissions

In a security system, roles such as `User`, `Admin`, and `SuperAdmin` are hierarchically structured. Each type of user has progressively more privileges. Inheritance can be used to express this relationship, enforcing behavior that fits within each role.

**Example**:

```ruby
class User
  def initialize(name)
    @name = name
  end

  def access_dashboard
    'Accessing basic user dashboard'
  end
end

class Admin < User
  def access_dashboard
    'Accessing admin dashboard'
  end

  def manage_users
    'Managing users'
  end
end

class SuperAdmin < Admin
  def access_dashboard
    'Accessing super admin dashboard with full control'
  end

  def manage_servers
    'Managing servers'
  end
end
# Usage
user = User.new('Alice')
admin = Admin.new('Bob')
super_admin = SuperAdmin.new('Charlie')
puts user.access_dashboard
# => Accessing basic user dashboard
puts admin.access_dashboard
# => Accessing admin dashboard
puts admin.manage_users
# => Managing users
puts super_admin.access_dashboard
# => Accessing super admin dashboard with full control
puts super_admin.manage_servers
# => Managing servers
# A User cannot perform Admin actions, maintaining security:
# user.manage_users
# => Raises NoMethodError
```

### Why Inheritance Fits Here

* **Role Hierarchy**: Inheritance models the hierarchical relationship between users, admins, and super admins naturally. Each subclass builds on the behavior of its parent while adding new privileges.
* **Security Through Role Enforcement**: A basic `User` cannot call `manage_users` or `manage_servers`, because those methods exist only in the `Admin` and `SuperAdmin` classes. This ensures that only the appropriate roles can access sensitive operations.
* **Preventing Privilege Escalation**: By keeping roles in a clear inheritance hierarchy, it becomes impossible for a low-level user to accidentally or intentionally gain access to higher-level permissions without being an instance of a higher class (e.g., `Admin` or `SuperAdmin`).

* * *

## 4\. **Simpler and Faster to Implement for Small Hierarchies**

In small systems where the **hierarchy is unlikely to change**, inheritance can be faster and more straightforward to implement. It allows you to extend classes without needing to wire up complex composition mechanisms. This can be especially beneficial when **security is a smaller concern** but **simplicity and speed** are the primary goals.

### Case 4: Quick Security Enhancement

Imagine you need a quick, easy way to extend a simple logging system to include secure logging for sensitive data.

**Example**:

```ruby
class Logger
  def log(message)
    puts "Log: #{message}"
  end
end

class SecureLogger < Logger
  def log(message)
    puts "Secure Log: [ENCRYPTED] #{encrypt(message)}"
  end

  private

  def encrypt(message)
    "ENCRYPTED: #{message.reverse}"
    # Simple encryption
  end
end
# Usage
logger = Logger.new
secure_logger = SecureLogger.new
logger.log('Normal message')
# => Log: Normal message
secure_logger.log('Sensitive data')
# => Secure Log: [ENCRYPTED] atad evitisneS
```

### Why Inheritance is Better in This Scenario

* **Quick to Implement**: Inheritance allows you to quickly extend `Logger` and create a `SecureLogger` that adds encryption with minimal code changes.
* **Lightweight & Straightforward**: There’s no need for complex composition mechanisms because the behavior is simple and can be safely extended through inheritance.
* **Security Enhancement Without Redesign**: You achieve the goal of adding secure logging without needing to redesign the entire logging system.

* * *

## 5\. **When Subclassing Enforces Validity and Consistency**

In some cases, you want to **enforce consistency** across your objects, making sure they adhere to specific rules or constraints. Inheritance ensures that objects follow a certain structure and behave in predictable ways, which can be crucial for security-related systems.

### Case 5: Validating User Types in Authentication

Consider a system that requires authentication but must differentiate between regular users and premium users. Inheritance can enforce this distinction, ensuring that only valid `PremiumUser` or `RegularUser` objects are created.

**Example**:

```ruby
class User
  def initialize(username)
    @username = username
  end

  def access_content
    raise 'Not implemented'
  end
end

class RegularUser < User
  def access_content
    'Accessing basic content'
  end
end

class PremiumUser < User
  def access_content
    'Accessing premium content'
  end
end
# Usage
regular = RegularUser.new('Alice')
premium = PremiumUser.new('Bob')
puts regular.access_content
# => Accessing basic content
puts premium.access_content
# => Accessing premium content
# Invalid users cannot be instantiated due to enforced structure
# random_user = User.new("Charlie")
# => Raises error: Not implemented
```

### Why Inheritance Helps

* **Enforcing Valid Subclasses**: Only valid user types (`RegularUser`, `PremiumUser`) can be instantiated and used. The base `User` class cannot be used on its own, ensuring that all users fall under specific categories.
* **Security Through Structure**: The structure guarantees that certain behaviors (like accessing premium content) are only available to specific users, preventing unauthorized access.

* * *

## Conclusion: When Inheritance is a Better Option

While **composition** is often more flexible and better for managing complex, growing systems, **inheritance** has its place, particularly in:

1. **Security scenarios**: Where enforcing strict rules and access controls across a hierarchy is critical.
2. **Frameworks and API structures**: Where you need to guarantee that certain behaviors are inherited and cannot be bypassed.
3. **Hierarchical real-world relationships**: Such as roles and permissions, where the relationships are naturally hierarchical.
4. **Simpler scenarios**: When you need to quickly extend functionality in a small system without introducing extra complexity.

In these cases, **inheritance provides consistency, control, and security**, and its use can ensure that certain behaviors are always followed across subclasses, making it a powerful tool in these contexts.
