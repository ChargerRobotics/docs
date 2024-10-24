Static Abuse
=============

Static abuse is one of the common pitfalls that many beginner Java developers fall into, caused partly by its ease-of-use.
Static abuse occurs when, unsurprisingly, the ``static`` keyword is abused.

Let's take a look at an example to illustrate this.

Let's say this is our robot program:

.. code-block:: java

   public class Robot extends TimedRobot {
     private Gyro gyro;
     private Drivetrain drivetrain;

     @Override
     public void robotInit() {
       // pretend we create an actual gyro here
       this.gyro = new Gyro(0); 
       // pretend we have a Drivetrain class that handles the drivetrain
       this.drivetrain = new Drivetrain();
     }

     // ...
   }

However, we want to make field-centric drive, meaning we to access robot header data in the drivetrain.
An easy way to do this is to make ``gyro`` a static field, so we can access it from anywhere.

.. code-block:: java
   
   public class Robot extends TimedRobot {
     private static Gyro gyro;
     private Drivetrain drivetrain;

     @Override
     public void robotInit() {
       // pretend we create an actual gyro here
       Robot.gyro = new Gyro(0); 
       this.drivetrain = new Drivetrain();
     }

     public static Gyro getGyro() {
       return gyro;
     }

     // ...
   }

.. code-block:: java

   public class Drivetrain {
     private final Gyro gryo = Robot.getGyro(); // we can access gyro here!

     public DriveTrain() {
       // ...
     }

     // ...
   }

Although simple, this system is inherently flawed. ``static`` items, specifically fields, represent **global state**.
This makes it much more difficult to test and debug code, as anything can modify and change that static field at any time.

Issues also arise as the robot expands. Let's say we now have two gyroscopes on the robot, one on a turret, and the other on the drivetrain.
Obviously, the drivetrain should be using the gryoscope on the drivetrain, not the turret.
However, since the ``Drivetrain`` class is so coupled with the ``Robot`` class, it's not an easy fix.
We'd have to make two static gyro fields, and have the ``Drivetrain`` class use the gyro on the drivetrain, and have the ``Turret`` class use the gyro on the turret.

So, how do we fix this?

We can use something called **Dependency Injection**, or DI for short. Dependency injection is a pattern where dependencies of a class get injected into it, most commonly through its constructor.

Here's the final code, updated using DI:

.. code-block:: java

   public class Robot extends TimedRobot {
     private Gyro drivetrainGyro; // not static!
     private Gyro turretGyro;
     private Drivetrain drivetrain;

     @Override
     public void robotInit() {
       // pretend we create actual gyros here
       this.drivetrainGyro = new Gyro(0); 
       this.turretGyro = new Gyro(1); 

       // we inject the gyro through the Drivetrain constructor
       this.drivetrain = new Drivetrain(drivetrainGyro);
     }

     // ...
   }

.. code-block:: java

   public class Drivetrain {
     private final Gyro gryo; // we can still access gyro here...

     public DriveTrain(Gyro gryo) {
       this.gyro = gyro; // but we inject it through its constructor instead of using a static method

       // ...
     }

     // ...
   }

By doing this, our code is much less coupled, which makes it more reusable and scalable.
Our ``Drivetrain`` class no longer directly depends on the ``Robot`` class, so we can be much more flexible with how we create drivetrains.

So what are some big takeaways?

- Fields should almost never be static

  - The only exception to this rule is public, unmodifiable constants (``public static final``)

- Use DI when appropriate

- Classes should be able to function without the ``Robot`` class as much as possible

- Be aware of how a robot program can grow, writing the necessary code infrastructure now, not later

Still a bit hung up on the whole DI pattern?
You can watch `this video <https://www.youtube.com/watch?v=J1f5b4vcxCQ>`_ by CodeAesthetic that goes much more in depth into DI.

