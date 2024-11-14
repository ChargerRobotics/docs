Generics
=========

Generics in Java can seem confusing at first glance, but they can be an extremely powerful tool in your Java arsenal.

So, what are generics, exactly?

Well, you've probably used them before!
Let's take a look at this code snippet:

.. code-block:: java

  List<String> list = new ArrayList<>();

That simple line already uses generics--they are the things inside the angle brackets (<>).

To illustrate this further, let's take a look at an example.

Say you have a box class, and it looks like this:

.. code-block:: java

   public class Box {
     private String item;

     public Box() { }

     /**
      * Takes the item out of the box, leaving it {@code null}
      */
     public String take() {
       String item = this.item;
       this.item = null;
       return item;
     }

     /**
      * Puts an item inside the box
      */
     public void put(String item) {
       this.item = item;
     }
   }

Pretty simple, nothing too complicated here.

But notice how the box can only hold strings. What if we want to hold a number? Or a custom class we've made?
An "easy" fix would be to just convert between a string and the desired type. However, this comes with its own slew of problems:

First off, the conversion can fail. The data inside the box is not guaranteed to be representable in that other data type.

But, arguably more important, there is no sign indicating what type the box is holding,
nor is there any checks to make sure the type lines up with what is expected.

You could also make the box hold a general ``Object``, but this fails in similar ways to the string method.

This can lead to lots of unexpected runtime errors and headaches from debugging. If only there was a way to represent a box that can hold **any** type...

You probably already guessed it, but we can use generics!

But first, let's update the code to use an ``Object`` instead.

.. code-block:: java

   public class Box {
     private Object item;

     public Box() { }

     /**
      * Takes the item out of the box, leaving it {@code null}
      */
     public Object take() {
       Object item = this.item;
       this.item = null;
       return item;
     }

     /**
      * Puts an item inside the box
      */
     public void put(Object item) {
       this.item = item;
     }
   }

So how do we make sure the actual type of the item is consistent with each box made?

.. code-block:: java

   public class Box<T> {
     private T item;

     public Box() { }

     /**
      * Takes the item out of the box, leaving it {@code null}
      */
     public T take() {
       T item = this.item;
       this.item = null;
       return item;
     }

     /**
      * Puts an item inside the box
      */
     public void put(T item) {
       this.item = item;
     }
   }

This may look confusing, but let's break it down.

In the class header, we define generics inside angle brackets.
Generics can be thought of as type arguments. Usually, arguments are **values**. However, generics are arguments that are **types**.
An object's generics are unique to that object--you can think of this as stating that all generics are **NOT** ``static``.

In the case of ``Box``, we define 1 generic and name it ``T``. Whenever we use ``T``, we are referring to that generic type.

Next, we use ``T`` in a field. This is read just like how a normal field is read: "field ``item`` has type ``T``".
The only difference here is that ``T`` is not a class that we have imported or created, rather, it is referring to the generic type.

The same applies to the other methods.

Since ``T`` is just some type, all of Java's type checking is still performed when working with generics.
For example, we can't assign ``item`` to a string, since a string is not a ``T``.

To use this, we can use the same syntax as the code snippet from the very beginning of this page.

.. code-block:: java

   // this creates a new box that holds a string
   Box<String> stringBox = new Box<>();

   // puts "some cool item" inside the box
   stringBox.put("some cool item");

   // if uncommented, this errors!
   // stringBox.put(10);
   // this is because stringBox is a box that holds strings, not integers
   // in more technical terms, #put takes in a argument as a T (which, in the case of stringBox, is String)
   // int is not a String, so we get a compile-time error!

   // takes the item out
   String item = stringBox.take();
   
   // if uncommented, this errors!
   // double doubleItem = stringBox.take();

Generics do have their limitations, however.
One such limitation that is very common is the fact that generics cannot be primitives.

What do I mean by primitive?

Primitives are types that are not traditional java objects. They do not extend ``Object``, and have no fields, methods, or constructors.
Primitive types include:

 * boolean
 * byte
 * char
 * short
 * int
 * long
 * float
 * double

For example, you **cannot** have a ``List<int>``.
You must use a wrapper type, which is essentially a class that *wraps* a primitive.
So instead of using a ``List<int>``, you would use a ``List<Integer>``.
Because ``Integer`` is an actual class, it can be used in generics.
Other wrapper types include ``Double``, ``Long``, ``Boolean``, etc. (notice the capitalization)

By leveraging generics, we can make our code much more flexible and scalable,
and understanding how they work greatly aids in using APIs that make use of them.

.. toctree::
   :glob:

   generics/*

