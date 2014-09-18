# Coursera Swift coding guidelines

## General

* No semicolons, unless they are required.
* No parentheses, unless they are required.
* Each variable or constant must be on a separate line.
* `let` is preferred, `var` is only used when mutability is strictly required.
* Always use named parameters.
* Indentation = 4 spaces (no TABs).

## Variables or constants

* Avoid using self to keep code clean. Swift does not require the use of self. Use self only when absolutely needed.
* Use type inference as far as possible.

*Preferred*

```swift
let course = "Course"
var courseTitle: String
```

*Not preferred*

```swift
let course: String = "Course"
```

### Ordering instance variables

Order the instance variables in the class, with this order:

* Immutable
* Optional immutable
* Variables
* Optional variables

## If-Else

```swift
if let name = optionalName {
  	greeting = "Hello, \(name)"
} else {
    greeting = "Bye"
}
```

## Optionals

Use `if let` syntax if the unwrapping involves more than 1 statement or when the optional is to be used as a conditional.

Use `?` in other cases.

Example:

```swift
if let unwrapped = optional {
	  unwrapped statement 1;
	  unwrapped statement 2;
}

optional?.optionalFunction()  // evaluates to nil if optional is not present
```

Use optional chaining when you need to go through a series of optionals.

Example:

```swift
if let validCorgis = coursera.mobileTeam?.coreTeam?.corgis {
  	validCorgis
}
```

## Classes

* Class prefixes are not needed.

## Functions

* Always use named parameters when using tuples in functions using the `#` parameter.
* Nest functions whenever possible.

Example:

```swift
func findIndexOfString(#inputString: String, #array: [String]) -> (index: Int?, name: String?) {
    for (index, value) in enumerate(array) {
        if (value == string) {
            return (index, string)
        }
    }
    return (nil, nil)
}
```

## KVO

* Functional Guidelines
	* If a class wants to KVO a property then it must supply a class context.
	* Super should be called in the KVO observation function.
	* Always remove observer on deinit.
* Style Guidelines
	* Each KVO key path match should call its own update/changed function.
	* Switch statements are often the best way to follow the logical 'return-first' guideline for the observer function.
		- Remember, in Swift cases do not fall-through by default.

Example:

```swift
import Foundation
class ClassB: NSObject {
    var someProperty: Int = 0
}

private var NewClassKVOContext = "NewClassKVOContext"

class NewClass: NSObject {
    var classB: ClassB

    override init() {
        classB = ClassB()
        super.init()
        classB.addObserver(self, forKeyPath: "someProperty", options: .Initial | .New, context: &newClassKVOContext)
    }

    // MARK: KVO Methods

    override func observeValueForKeyPath(keyPath: String!, ofObject object: AnyObject!, change: [NSObject : AnyObject]!, context: UnsafeMutablePointer<Void>) {
        if context != &newClassKVOContext {
            super.observeValueForKeyPath(keyPath, ofObject: object, change: change, context: context)
						return
        }

        switch keyPath {
        case "someProperty":
            somePropertyChanged(change[NSKeyValueChangeNewKey])
        default:
            super.observeValueForKeyPath(keyPath, ofObject: object, change: change, context: context)
        }
    }

    func somePropertyChanged(someProperty: AnyObject?) {
        if let validSomeProperty = someProperty as? Bool {
            // Do something
        }
    }

    // MARK: Deinit

    deinit {
        classB.removeObserver(self, forKeyPath: "someProperty", context: &newClassKVOContext)
    }
}
```
