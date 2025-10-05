OpenGL remains a C-library at its core. Since most of C's language-constructs don't translate well to other higher-level languages, OpenGL was developed with several abstractions in mind. One of those abstractions are objects in OpenGL.

An object in OpenGL is a collection of options that represent a subset of OpenGL's state. For example, an object could represent the settings of the drawing window; we could then set its size, how many colours it supports, etc.

```c++
struct object_name {
	float option1;
	float option2;
	char[] name;
}
```

Whenever we want to use objects, it generally looks like this:

```c++
// The State of OpenGL
struct OpenGL_Context {
	...
	object_name* object_Window_Target;
	...
}

// create object
unsigned int objectId = 0;
glGenObject(1, &objectId);
// bind / assign object to context
glBindObject(GL_WINDOW_TARGET, objectId);
// set options of object currently bound to GL_WINDOW_TARGET
glSetObjectOption(GL_WINDOW_TARGET, GL_OPTION_WINDOW_WIDTH. 800);
glSetObjectOption(GL_WINDOW_TARGET, GL_OPTION_WINDOW_HEIGHT. 600);
// set context target back to default
glBindObject(GL_WINDOW_TARGET, 0);
```

This code is a workflow you'll frequently see when working with OpenGL.
- We first create an object and store a reference to it as an ID
- Then we bind the object to the target location of the context
- Next we set the window options
- Finally we unbind the object by setting the current object id of the window target to 0
- The options we set are stored in the object referenced by `objectId` and restored as soon as we bind the object back to `GL_WINDOW_TARGT`

The great thing about objects is that we can define more than one object in our application, set their options, and whenever we start an operation that uses OpenGL's state, we bind the object with our preferred settings. There are objects that act as container objects for 3D model data and whenever we want to draw to one of them, we bind the object containing the model data that we want to draw.

Having several objects allows us to specify many models and whenever we want to draw to a specific model, we simply bind the corresponding object before drawing without setting all their options again.