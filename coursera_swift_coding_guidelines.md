# Coursera Swift coding guidelines

## General

	* No semilcolons, unless it is required
	* No paranthesis, unless it is required
	* Each variable or constant must be on a separate line
	* `let` is preferred, `var` is only used when immutability is strict a requirement
	* Always use named parameters
	* Indentation = 4 spaces (no TAB)

## Variables or constants

* Avoid using self to keep code clean. Swift does not require the use of self. Use self only when absoultely needed.
* Use type inference as far as possible

*Preferred*

	let course = "Course"

*Not preferred*

    let course: String = "Course"

Example:

	var courseTitle: String
	
### Ordering instance variables

Order the instance variables in the class, with this order :

    * Immutable 
    * Optional immutables
    * Variables
    * Optional variables

## If-Else

    if let name = optionalName {
    	greeting = "Hello, \(name)"
	} else {
	   greeting = "Bye"
	}
	
## Optionals
      
Use if let syntax if the unwrapping involves more than 1 statement or when the optional is to be used as a conditional. 

Use `?` in other cases.


Example:

    if let unwrapped = optional {
    	unwrapped statement 1;
    	unrwapped statement 2;
    }
    
    optional?.optionalFunction() // evaluates to nil if optional is not present
    
 
 Use optional chaining when you need to go through a series of optionals
 
 Example:
 
    if let validCorgis = coursera.mobileTeam?.coreTeam?.corgis {
    	validCorgis
    } 
    
## Classes

* Class prefixes are not needed
    
## Functions


* Always use named parameters when using tuples in functions using the `#` parameter
* Nest functions whenever possible

Example:

    func findIndexOfString(#inputString: String, #array: [String]) -> (index: Int?, name: String?) {
        for (index, value) in enumerate(array) {
            if (value == string) {
                return (index, string)
            }
        }
        return (nil, nil)
    }
 
## KVO

* Functional Guidelines
	* If a class wants to KVO a property then it must supply a class context
	* Super should be called in the KVO observation function
	* Always remove observer on deinit
* Style Guidelines
	* Each KVO key path match should call its own update/changed function
	* Switch statements are often the best way to follow the logical 'return-first' guideline for the observer function
		- remember in Swift cases do not fallthrough by default

Example:

import Foundation
class ClassB: NSObject {
    var someProperty: Int = 0
}

private var NewClassKVOContext = "NewClassKVOContext"

class NewClass: NSObject {
    var classB: ClassB;

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
    
    //MARK: Deinit
    
    deinit {
        classB.removeObserver(self, forKeyPath: "someProperty", context: &newClassKVOContext)
    }
}
