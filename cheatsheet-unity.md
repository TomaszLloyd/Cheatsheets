### Unity Cheatsheet

##### Editor

+ Add a custom Event Handler in Inspector.
```
using UnityEngine.Events;

public UnityEvent customEvent;

public void Foo() {
	customEvent.Invoke();
}
```