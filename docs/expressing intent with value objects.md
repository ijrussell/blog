# Expressing Intent with Value Objects #

I've been using the Four Rules of Simple Design for a number of years. One of the four rules is "Expresses Intent". I've seen many C# codebases over the last twenty years and whilst they are generally getting better with regard to the naming of classes and methods, there is very little attention paid to properties. Value Objects have gained popularity with the use of Domain-Driven Design but the fundamentals are much older. 

In this post, I'm going to explain the basic problems I see and show some potential solutions. The code is not meant to be perfect as the intent of the post is to get you to think about why you'd want to make these changes to your codebase whilst showing some of the potential solutions.

## The Basics ##

Look at the simple class below.

![](https://i.imgur.com/GL6aejV.jpg)

There are some issues with this class:

It uses raw primitives for each of the properties. For example, whilst email addresses are strings, there are rules for how they are structured. 

Some of the properties will naturally exist as a grouping with a meaningful name for the whole thing. In this cae, the email and name properties look like obvious candidates.

The obvious starting point is to introduce an Email value object to replace the string property.

![](https://i.imgur.com/NrMERoA.jpg)

This is a simple read-only (immutable) class with a constructor. We have gained little except the change of the type of Email property in the class. 

The next stage is to introduce the email format checking rule.

![](https://i.imgur.com/GeOJWui.jpg)

We add the check into the constructor code and throw an exception if the value submitted is not valid. This means that our email property can never contain an invalid value.

The next stage is to group the two email properties and the three name properties into their own value objects.

![](https://i.imgur.com/G9wmrUr.jpg)

![](https://i.imgur.com/h3qkEsP.jpg)

This leaves us with a much better place but we should add some logic to handle the email verification process.

## Value Object Comparison ##

When comparing two different Value Objects, they are considered to be the same if a chosen subset of properties match. This means that the standard Reference Equality used by C# will not work. Thankfully there is a built-in solution by implementing the IEquatable<T> interface as seen in the example below.

![](https://i.imgur.com/CPZcBpN.jpg)

![](https://i.imgur.com/8htUMXB.jpg)

If you were adding products to a shopping cart, you would use the equality checking to determine the existence of the product within the cart.

## Operations in Value Objects ##

If we create a simple Money value object with properties for amount and currency as below.

![](https://i.imgur.com/lDsxmGd.jpg)

We generally have no need to compare Money instances for equality but we may need to add or subtract. To do this we need to add methods for Add and Subtract to the class and to overload the + and - operators. Not the code in the Add and Subtract methods that ensure that the instances use the same CurrencyCode.

![](https://i.imgur.com/pJqODXO.jpg)

## Summary ##

In summary, value objects do not have an identity so must be compared using IEquatable<T\>, they are immutable/read only, have rules in the constructor preventing the creation of an invalid instance and encapsulate their own functionality.  

There is nothing complicated about any of the changes that I have made but I think that they make the code more readable by better expressing the intent of the code. At the end of the day, it is up to you to decide if the extra work involved in making these changes is worth the potential gains in expressing the intent of your code. 

If you want to learn more about Value Objects, I recommend that you read [Hands-On Domain Driven Design with .NET](https://www.packtpub.com/application-development/hands-domain-driven-design-net "Hands-On Domain Driven Design with .NET") by Alexey Zimarev.

This blog post is part of the [C# Advent Calendar 2018](https://crosscuttingconcerns.com/The-Second-Annual-C-Advent)