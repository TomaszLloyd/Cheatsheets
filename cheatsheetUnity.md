## Unity and C# Cheatsheet
The following are useful code snippets, design patterns, naming conventions, and best practices that I've found are handy to reference while developing for Unity3D. Your mileage may vary.

### Naming Conventions

Public vs Private Variables:
+ public variables are camelCase
+ public methods are PascalCase
+ private variables and methods have an underscore prefix
+ note that Pascal Case also denotes that acronyms of three or more letters should only have the first letter capitalized such as `MyXmlValue` instead of `MyXMLValue`
+ avoid using abbreviations
+ use nouns
+ Exception methods end in the word `Exception` such as `public void NoValidLoginException() {}`

```c#
public bool camelCase;

private bool _camelCase;

public void PascalCase();

private void _PascalCase();

public struct PascalCase(){
	public float someFloat;
}
```

### Editor

#### Add a custom Event Handler in Inspector.
```c#
using UnityEngine.Events;

public UnityEvent customEvent;

public void SomeFunction() {
	customEvent.Invoke();
}
```
[Unity Documentation](https://docs.unity3d.com/Manual/UnityEvents.html)

#### Platform Dependent Compilation
+ preprocessor directives that let you partition your scripts to compile and execute a section of code exclusively for one of the supported platforms

```c#
#if UNITY_EDITOR
	some_function();
#endif
```
In the above, the `some_function();` code only exists when in the Unity Editor and will not exist whatsoever on runtime or when you compile. See [Unity Documentation here](https://docs.unity3d.com/Manual/PlatformDependentCompilation.html)

### Performance Tips

Various tips for more performant Unity and C# code
+ Multiplication is a less intensive function than division.
+ Bitwise register shifting is much faster than multiplication for integers (not applicable to C#/Unity unfortunately)
+ Memory management and garbage collection is automatic. You CAN use pointers but only in `unsafe mode`
+ verbatim text, i.e., escape characters are ignored `@"a"`

### Mesh Generation
+ Instead of setting normals, for 99% of applications it's faster to use the `RecalculateNormals()` functions.

### Unsorted Tips
Random tips that I need to sort
+ Try/Catch/Finally blocks 
+ Throw exceptions in the `try` block:
```c#
/* Read the config file, and return the integer value. If it does not exist, then this is a problem! */
try {
    string value = ConfigurationManager.AppSettings["Timeout"];
	if (value == null)
		throw new ConfigurationErrorsException("Timeout value is not in the configuration file.");
}
catch (Exception ex )
{
    throw; // <-- Throw the existing problem!
}
```
The `throw;` keyword means preserve the exception information and throw it up the call stack.

#### Scale:
The only game objects that should have a scale are the root or a leaf.
```

Root // ok to scale
 |_ First Child // do not scale!
   |_ First Grandchild (Leaf) // ok to scale
   |_ Second Grandchild  // do not scale!
   |  |_ Leaf  // ok to scale
   |
   |_ Third Grandchild  // do not scale!
     |_ Leaf  // ok to scale
     |_ Leaf  // ok to scale

```

### Namespaces

Nested namespaces
```c#
namespace CodeWorks
{
    namespace MyApplication
    {
// Do stuff }
}
```
... or like this:
```c#
namespace CodeWorks.MyApplication
{
    // Do stuff }
```