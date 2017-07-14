### Unity Cheatsheet

#### Editor

##### Add a custom Event Handler in Inspector.
```c#
using UnityEngine.Events;

public UnityEvent customEvent;

public void Foo() {
	customEvent.Invoke();
}
```