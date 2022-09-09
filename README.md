# Factory

## Learning Goals

- Explain the factory pattern
- Use the pattern in Java

## Introduction

A **factory** (also sometimes called the factory method) is a creational design
pattern that provides a common interface for creating objects without exposing
how the object is created to the client. It is one of the most commonly used
design patterns in Java when it comes to creating various objects.

We use the factory pattern when we want to let client classes decide which
implementations of an interface they want to use. Here are the highlights of the
factory pattern:

- The base class is usually an abstract class or an interface and cannot be
  instantiated directly.
- Several classes implement the parent class' methods, each with their own
  implementation.
- The factory class controls the instantiation of one of the child classes,
  usually based on criteria it receives to determine which child class to
  instantiate and return.
- The driver class then uses the factory class to ask for an object to be
  created. The driver does not know or care about the  
  specific concrete child classes of the parent class or interface.

Factory methods work well in conjunction with generics, since generics allow the
clients to specify the types of objects that a generic class should use.

## Real World Example

Consider the transportation industry. There are plenty of ways to travel and
get around nowadays! There are cars, buses, planes, trains, and boats just to
name a few modes of transportation. There are also newer ways to travel - like
by driving an electric car. Even if we used standard inheritance, the
implementation of the way we travel may change rather frequently. We might even
get to a point where implementing a new change is rather difficult in the
current system. This scenario would greatly benefit from a factory design
pattern. Let's take this example and see how to implement this pattern!

## Implementation

Using the example we described above, we will be implementing the factory
design pattern. For reference, consider the diagram below:

![Transportation Factory](https://curriculum-content.s3.amazonaws.com/java-mod-2/factory/Factory-UML.png)

## Step 1: Create the Interface

We will first declare the `Vehicle` interface that will be common to all modes
of transportation. To keep the example simple, let's assume the interface only
has one method that its subclasses will need to implement:

```java
package com.flatiron.transportation;

public interface Vehicle {
    void travel();
}
```

## Step 2: Create the Implementing Classes

Even though there do exist other transportation vehicles aside from a car, a
boat, and a plane, to keep the example simple, we will stick to implementing
just these three. We will also only have the class contain the `travel()`
method for now too:

```java
package com.flatiron.transportation;

public class Car implements Vehicle {

    @Override
    public void travel() {
        System.out.println("Traveling by car!");
    }
}
```

```java
package com.flatiron.transportation;

public class Boat implements Vehicle {

    @Override
    public void travel() {
        System.out.println("Traveling by boat!");
    }
}
```

```java
package com.flatiron.transportation;

public class Plane implements Vehicle {

    @Override
    public void travel() {
        System.out.println("Traveling by plane!");
    }
}
```

So far, all we have done is created an interface that has three subclasses -
nothing we haven't seen or done so far. Now it's time to create the factory
class.

## Step 3: Create the Factory Class

When defining our factory class, it should be noted that the factory will just
instantiate and return an object of one of our concrete classes (`Car`, `Boat`, 
or `Plane`) based on the given information. Therefore, it really only has one
method. This method will usually be `static` as well since it should not need
an instance of the factory class to be called.

```java
package com.flatiron.transportation;

public class TransportationFactory {

  public enum TransportationMode {
    CAR,
    BOAT,
    PLANE
  }

  public static Vehicle buildVehicle(String vehicle) {
    TransportationMode transportationMode = TransportationMode.valueOf(vehicle.toUpperCase());

    switch (transportationMode) {
      case CAR:
        return new Car();
      case BOAT:
        return new Boat();
      case PLANE:
        return new Plane();
      default:
        return null;
    }
  }
}
```

## Step 4: Use the Factory

It's time to test out our factory design pattern! To test it out, let's first
go ahead and create our driver class to call the `TransportationFactory` we
created above:

```java
package com.flatiron.transportation;

public class TransportationDriver {

    public static void main(String[] args) {
        Vehicle car = TransportationFactory.buildVehicle("CAR");
        Vehicle boat = TransportationFactory.buildVehicle("BOAT");
        Vehicle plane = TransportationFactory.buildVehicle("PLANE");

        car.travel();
        boat.travel();
        plane.travel();
    }
}
```

If we run the above code, we can verify that the factory design pattern we
implemented works too!

```plaintext
Traveling by car!
Traveling by boat!
Traveling by plane!
```
