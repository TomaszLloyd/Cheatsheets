## Unity and C# Cheatsheet
The following are useful code snippets, design patterns, naming conventions, and best practices that I've found are handy to reference while developing for Unity3D. Your mileage may vary.

### Naming Conventions

Public vs Private Variables:
+ public variables are camelCase
+ public methods are PascalCase
+ private variables and methods have an underscore prefix

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

### Mesh Generation
+ Instead of setting normals, for 99% of applications it's faster to use the `RecalculateNormals()` functions.