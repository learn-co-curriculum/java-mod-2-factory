# Factory

## Learning Goals

- Explain the factory pattern
- Use the pattern in Java

## Introduction

Use the factory pattern when you want to let client classes decide which
implementations of an interface they want to use. Here are the highlights of the
Factory pattern:

- The base class is usually abstract or an interface and cannot be instantiated
  directly
- Several classes implement the base class' methods, each with their own
  implementation
- Factory class controls the instantiation of one of the subclasses, usually
  based on criteria it receives to determine which base class to instantiate and
  return
- "Running" class then uses the Factory class to ask for an object to be
  created. The runner does not know or care about the  
  specific concrete subclasses of the base class or interface

Factory methods work well in conjunction with generics, since generics allow the
clients to specify the types of objects that a generic class should use. E.g.
the Java method `Map.ofEntries()` that can create Map objects from the
collections that are passed into it.

## Implementation

For our example, let's start building a camera system that will give us good
opportunities to implement the following patterns:

- Factory: we will use this pattern to support cameras from different
  manufacturers
- Facade: we will use this pattern to abstract the details of the complex
  functionality required for a camera to take a photograph.
- Adapter: we will use this pattern to retrofit digital cameras into the class
  structure we designed in the previous step

Let's start by creating a base class to model a traditional, film-based SLR
camera. We'll make this an abstract class because we need a specific
implementation of its function for each camera manufacturer. We're not making it
an interface, however, because at this point we have determined that all cameras
take the same basic steps in order to take a photograph, even if the exact
implementation of those steps is manufacturer-specific. Later, we will see how
that decision becomes problematic once we consider a new type of camera that
does not require some of the steps that traditional cameras do. We are
intentionally going through this process of making a decision now that will lead
to limitations in the future because that's exactly how things happen on real
projects and with real systems - no one can predict the future.

```java
public abstract class Camera {
}
```

A quick primer on how SLR cameras work might help understand this example. An
SLR camera is a camera where the light that enter the lens goes is redirected to
the camera's viewfinder by a mirror, providing the photographer with a "live
view" of the scene they are trying to photograph. When the photographer is ready
to take a picture, they push the shutter button. This flips the mirror out of
the way of the incoming light and directs it towards the film in the camera. The
shutter then opens to let the light through and the light hits the film and
exposes it to make the impression of the scene coming through the lens.

Describing this (simplified) process in a series of steps looks like this:

1. Engage the film mechanism to prepare to roll the film for the next exposure
2. Roll the film for the exposure to come
3. Release the film mechanism
4. Open (flip up) the mirror to redirect the light towards the film (instead of
   the viewfinder)
5. Open the shutter (set shutter speed, initialize shutter, activate shutter,
   release shutter)
6. Close (flip down) the mirror to direct the incoming light from the lens back
   to the viewfinder again

Since every manufacturer makes their own hardware, we want to have different
implementations of the functionality to manage a) the film, b) the mirror and c)
the shutter. So we create 3 interfaces:

1. `FilmOperations`
2. `ShutterOperations`
3. `MirrorOperations`

Putting this together in the `Camera` class gives us the following:

```java
package com.flatiron.patterns;

public abstract class Camera {
    private FilmOperations filmOps;
    private ShutterOperations shutterOps;
    private MirrorOperations mirrorOps;

    public Camera(FilmOperations filmOps, ShutterOperations shutterOps, MirrorOperations mirrorOps) {
        this.filmOps = filmOps;
        this.shutterOps = shutterOps;
        this.mirrorOps = mirrorOps;
    }

    public void takePhotograph(double shutterSpeed) {
        Logger.getInstance().log(getName() + " is taking a photograph");

        filmOps.engageFilmMechanism();
        filmOps.rollFilm();
        filmOps.releaseFilmMechanism();

        mirrorOps.openMirror();;

        shutterOps.setShutterSpeedSetting(shutterSpeed);
        shutterOps.initializeShutter();
        shutterOps.activateShutter();
        shutterOps.releaseShutter();

        mirrorOps.closeMirror();

        Logger.getInstance().log(getName() + " is done taking this photograph");
    }

    public abstract String getName() ;
}
```

Our new interfaces look like this:

```java
public interface FilmOperations {
    public void engageFilmMechanism();
    public void rollFilm();
    public void releaseFilmMechanism();
    public String getName();
}
```

```java
public interface ShutterOperations {
    public void setShutterSpeedSetting(double shutterSpeed);
    public void initializeShutter();
    public void activateShutter();
    public void releaseShutter();
    public String getName();
}
```

```java
public interface MirrorOperations {

    public void openMirror();
    public void closeMirror();
    public String getName();
}
```

Now we need the manufacturer-specific implementations of a) the abstract
`Camera` base class and b) the operations interfaces:

```java
public class CanonCamera extends Camera {
    public CanonCamera(FilmOperations filmOps, ShutterOperations shutterOps, MirrorOperations mirrorOps) {
        super(filmOps, shutterOps, mirrorOps);
    }


    public String getName() {
        return "Canon";
    }
}
```

```java
public class NikonCamera extends Camera {
    public NikonCamera(FilmOperations filmOps, ShutterOperations shutterOps, MirrorOperations mirrorOps) {
        super(filmOps, shutterOps, mirrorOps);
    }


    public String getName() {
        return "Nikon";
    }
}
```

As you can see, each camera implementation is very simple because the steps of
"taking a picture" are the same and rely on the implementations of the
operations interfaces, which look like this:

`FilmOperations` implementations:

```java
public class CanonFilm implements FilmOperations {

    public void engageFilmMechanism() {
        Logger.getInstance().log(getName() + " has engaged");
    }


    public void rollFilm() {
        Logger.getInstance().log(getName() + " has rolled");
    }


    public void releaseFilmMechanism() {
        Logger.getInstance().log(getName() + " has been released");
    }


    public String getName() {
        return "Canon Film";
    }
}
```

```java
public class NikonFilm implements FilmOperations {

    public void engageFilmMechanism() {
        Logger.getInstance().log(getName() + " has engaged");
    }


    public void rollFilm() {
        Logger.getInstance().log(getName() + " has rolled");
    }


    public void releaseFilmMechanism() {
        Logger.getInstance().log(getName() + " has been released");
    }


    public String getName() {
        return "Nikon Film";
    }
}
```

`ShutterOperations` implementations:

```java
public class CanonShutter implements ShutterOperations {

    public void setShutterSpeedSetting(double shutterSpeed) {
        Logger.getInstance().log(getName() + " has set its speed to " + shutterSpeed + " seconds");
    }


    public void initializeShutter() {
        Logger.getInstance().log(getName() + " has been initialized");
    }


    public void activateShutter() {
        Logger.getInstance().log(getName() + " has been activated");
    }


    public void releaseShutter() {
        Logger.getInstance().log(getName() + " has been released");
    }


    public String getName() {
        return "Canon Shutter";
    }
}
```

```java
public class NikonShutter implements ShutterOperations {

    public void setShutterSpeedSetting(double shutterSpeed) {
        Logger.getInstance().log(getName() + " has set its speed to " + shutterSpeed + " seconds");
    }


    public void initializeShutter() {
        Logger.getInstance().log(getName() + " has been initialized");
    }


    public void activateShutter() {
        Logger.getInstance().log(getName() + " has been activated");
    }


    public void releaseShutter() {
        Logger.getInstance().log(getName() + " has been released");
    }


    public String getName() {
        return "Nikon Shutter";
    }
}
```

`MirrorOperations` implementations:

```java
public class CanonMirror implements MirrorOperations {

    public void openMirror() {
        Logger.getInstance().log((getName() + " is open"));
    }


    public void closeMirror() {
        Logger.getInstance().log((getName() + " is closed"));
    }


    public String getName() {
        return "Canon Mirror";
    }
}
```

```java
public class NikonMirror implements MirrorOperations {

    public void openMirror() {
        Logger.getInstance().log((getName() + " is open"));
    }


    public void closeMirror() {
        Logger.getInstance().log((getName() + " is closed"));
    }


    public String getName() {
        return "Nikon Mirror";
    }
}
```

Now that we have our classes well defined, this is where we implement a factory
so that we have the ability to request cameras of different manufacturers:

```java
public class CameraFactory {
    public enum CameraManufacturer {
        NIKON_FILM("Nikon Film"),
        CANON_FILM("Canon Film");

        String name;

        private CameraManufacturer(String name) {
            this.name = name;
        }
    }

    public static Camera makeCamera(CameraManufacturer manufacturer) {
        if (manufacturer == CameraManufacturer.NIKON_FILM) {
            return new NikonCamera(new NikonFilm(), new NikonShutter(), new NikonMirror());
        } else if (manufacturer == CameraManufacturer.CANON_FILM) {
            return new CanonCamera(new CanonFilm(), new CanonShutter(), new CanonMirror());
        }

        return null; // will never happen because we're using an enum but required to satisfy the compiler
    }
}
```

In our lab, we will create a `Photographer` class and a `PhotoStudio` class to
use the functionality we just created.
