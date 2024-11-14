Advanced
=========

Bounded Generics
-----------------

Bounded generics are, as the name implies, generics that have a bound.
This bound is a superclass that the generic type must have. This can be either a normal class or an interface.

Let's look at our box example again, but this time with a bounded generic.
The bound we're going to use is ``BoxObject``, which is an interface that has a ``getSize`` method.

.. code-block:: java

   public interface BoxObject {
     /**
      * Gets the size of this object, in square meters
      */
     double getSize();
   }

Now, lets add that bound onto our ``Box`` class.

.. code-block:: java

   public class Box<T extends BoxObject> {
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

     /**
      * Gets the amount of space taken up by the object inside, in square meters.
      * If there is no item in the box, returns 0.
      */
     public double spaceTaken() {
       if (item == null) return 0;
       // we can use methods defined by the BoxObject interface here because we introduced that bound!
       // in other words, T must be a subclass of the interface BoxObject, so it must also implement all methods defined in BoxObject
       return this.item.getSize();
     }
   }

Now let's say we have this box object:

.. code-block:: java

   public class LaptopBoxObject implements BoxObject {
     private final double length;
     private final double width;
     private final double height;

     public LaptopBoxObject(double length, double width, double height) {
       this.length = length;
       this.width = width;
       this.height = height;
     }

     @Override
     public double getSize() {
       return length * width * height;
     }
   }

So now we can use ``Box`` like this:

.. code-block:: java

   Box<LaptopBoxObject> laptopBox = new Box<>();
   laptopBox.put(new LaptopBoxObject(0.02, 0.3, 0.2));

   // prints 0.0012 (0.02 * 0.3 * 0.2)
   System.out.println(laptopBox.spaceTaken());

   // if uncommented, this errors!
   // Box<String> stringBox = new Box<>();
   // this is because String is not a subclass of BoxObject

Wildcards
----------

Wildcards are generics that mean *some unknown type*.
Note that this is **not** the same as *any type*, and this distinction will become clear as we start using wildcards.

Wildcards can be denoted by using a ``?`` in place of where a type would usually go in a generic.

Using the same box example as before (without any bounds):

.. code-block:: java

   Box<?> unknownBox = new Box<>();
   // if uncommented, this errors!
   // unknownBox.put(10);
   // this is because the wildcard (?) means some unknown type
   // is it an integer? maybe, but it very well might be a String, or a List, or any other type

This looks useless at first, but it can be useful in cases where you don't care about the type of a generic.

Let's say we're writing an application that needs to print the contents of a list very frequently, so we decide to create a method that does it for us.
In this scenario, we don't care what the list contains, just that it's a list.
An implementation of this method could look like this:

.. code-block:: java

   public static void printList(List<?> list) {
     // we can use Object here because every type extends Object
     for (Object item : list) {
       System.out.println(item);
     }
   }

We can also use bounds with wildcards, restricting what type the unknown type could be.

Consider the following code snippet (using our box example):

.. code-block:: java

   public void printBoxList(Box<? extends List<?>> box) {
     List<?> list = box.take();
     printList(list);
   }

In this example,
we are defining a method that takes in a box that holds **some unknown type** that must **extend a list** containing elements **with some unknown type**.
Examples of objects that we could pass into this method are ``Box<ArrayList<String>>``, ``Box<LinkedList<Integer>>``, ``Box<List<Box<String>>>``, etc.
However, passing something like a ``Box<String>`` does **not** work because ``String`` does not implement ``List``.

Type Erasure
-------------

Type erasure in Java simply means that generic types get erased at runtime (or compile time, depending on who you ask).

Now this isn't usually a problem, since static type checking guarantees that all types match up, assuming no casting is being done.

However, it can be a problem when access a generic type *itself*.

For example, doing something like this, where ``T`` is a generic:

.. code-block:: java

   T.staticMethod();
   T.STATIC_FIELD;

Doing this in Java is not allowed and will cause a compile error.
Besides some specific use-cases (which I don't get into here), you should never find youself having to access static members of a generic type.
If you do, it may be a sign to redesign some of your code.

