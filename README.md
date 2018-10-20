# kotlin-styleguide
Opinions on a flexible language.

## Table of Contents
1. [Files](#files)
2. [Variables](#variables)
3. [Classes](#classes)
   1. [Enums](#enums)
   2. [Sealed Classes](#sealed-classes)
   3. [Data Classes](#data-classes)
   4. [Constructors](#constructors)
   5. [Class Properties](#class-properties)
   6. [Visibility Modifiers](#visibility-modifiers)
4. [Objects](#objects)
5. [Interfaces](#interfaces)
6. [Functions](#functions)
   1. [Class Functions](#class-functions)
   2. [Extension Functions](#extension-functions)
   3. [Anonymous Functions](#anonymous-functions)

___________
## <a name="files"></a>1. Files
___________
Files should be named according to their majority content. For example:

```kotlin
class User {}
```

should be named `User.kt`.

```kotlin
interface IUser {...}
```

should be named `IUser.kt`

When the content of the file is not contained within a single enclosure, consider naming the file after the general concept that the code acts upon. For example:

```kotlin
fun<T> List.of(t: T) {}

fun<T> List.from(fn: () => T) {}
```

could be named `ListExtensions.kt`

----------

## <a name="variables"></a>2. Variables

----------
Variables should be named in regards to their data or state that they hold. Prefer lengthy (but descriptive) variable names over short (but confusing) variables.

```kotlin
var isUserActive = true // good
var isActive = true // okay
var foo = true // bad
```

When storing boolean values in variables, begin the variable name with `is`.

```
var isActive = true // good
var active = true // bad
```

----------

## <a name="classes"></a>3. Classes

----------
There are no special prefixes or suffixes to differentiate between classes and other major types. For example, this guide recommends using `I` as a prefix to interfaces, classes have no such expectation.

Classes should be named according to their responsibility. For example, a class that holds data for a user could simply be handled like so:

```kotlin
class User() {} // good
class Foo() {} // bad
```

It is common to suffix a class with a design pattern. If this helps create readability, it is recommended. Attempt to stick with existing (or team agreed upon) design patterns.

```kotlin
class UserRepository() {} // good
```

Classes are final in Kotlin and for a good reason. Only open a class if it is intended to be extended.



### <a name="enum-classes"></a>3.1 Enum Classes

Enums should be named according to the value they represent. If we needed an enum to determine if the user's calendar format was days, weeks, months, or years we would build one like so:

```kotlin
enum class CalendarFormat {
  DAYS, WEEKS, MONTHS, YEARS
}
```

Enum constants should be uppercase.
```kotlin
enum class CalendarFormat {
  DAYS, WEEKS, MONTHS, YEARS // good
}

enum class CalendarFormat {
  days, weeks, months, years // bad
}
```

### <a name="sealed-classes"></a>3.2 Sealed Classes

Prefer Sealed classes over enums when working with categorical values, especially if **some** of the enumerations require additional data.

**Good:**
```kotlin
sealed class CalendarFormat {
  class Days: CalendarFormat()
  class Weeks: CalendarFormat()
  data class Months (val isLeapYear: Boolean): CalendarFormat()
  class Years: CalendarFormat()
}
```

**Bad:**
```kotlin
enum class CalendarFormat(val isLeapYear: Boolean) {
  DAYS(false),
  WEEKS(false),
  MONTHS(true),
  YEARS(false)
}
```

### <a name="data-classes"></a>3.3 Data Classes

Use data class when creating simple data structures. Prefer to keep data classes simple and without business logic.

```kotlin
data class User(val name: String, val password: String) // good
class User(val name: String, val password: String) {} // bad
```

### <a name="constructors"></a>3.4 Constructors
Omit the constructor keyword unless you need to provide an annotation or visibility modifier to the constructor.

**Good:**

```kotlin
class User(val name: String) {} // no keyword
class UserInfo @Inject constructor(val dob: Date) {} // annotated
class UserPhoto private constructor(val url: String) {} // private
```

**Bad:**

```kotlin
class User constructor(val name: String) {} // unnecessary
```

Use an init block for any logic that needs to take place upon construction.

**Good:**

```kotlin
class User(val name: String) {
  private val upperName: String
  init {
      upperName = name.toUpperCase()
  }
}
```

Use only **ONE** init block to help reduce complexity.

**Good:**

```kotlin
class User(val name: String) {
  private val upperName: String
  private val lowerName: String
  init {
      upperName = name.toUpperCase()
      lowerName = name.toLowerCase()
  }
}
```

**Bad:**

```kotlin
class User(val name: String) {
  private val upperName: String
  init {
    upperName = name.toUpperCase()
  }
  private val lowerName: String
  init {
    lowerName = name.toLowerCase()
  }
}
```

Consider using a primary constructor with default parameters instead of a secondary constructor.

**Good:**

```kotlin
class User(val first: String, val last: String = "Johnson") {}

User(first = "Ted")
```

**Bad:**

```kotlin
class User(val first: String, val last: String) {
  constructor(first: String): this(first, "Johnson") {}
}
```

Call constructors using named parameters if the constructor accepts 2 or more parameters.

```kotlin
UserEmail("Brad.Cypert@thatReallyPopularEmailService.com") // good
User(first = "Brad", last = "Cypert", age = 25) // good
Car("Volkswagen", "GTI", 2018, 4, 4) // bad
```


### <a name="class-properties"></a>3.5 Class Properties

If a public property must be backed by a private property, prefix `_` to the public property name to create the private property name. Example:

**Good:**
```kotlin
private val _loginChanges: MutableLiveData<Boolean> = MutableLiveData<Boolean>()
val loginChanges: LiveData<Boolean> = _loginChanges
```

**Bad:**
```kotlin
private val someLoginChanges: MutableLiveData<Boolean> = MutableLiveData<Boolean>()
val loginChanges: LiveData<Boolean> = someLoginChanges
```

Better yet, override the getter for the public property to return the expected property (this is particularly useful when holding a mutable type privately, but exposing an immutable type).

```kotlin
private val _loginChanges: MutableLiveData<Boolean> = MutableLiveData<Boolean>()
val loginChanges: LiveData<Boolean> get() = _loginChanges
```

### <a name="visibility-modifiers"></a>3.6 Visibility Modifiers
* Avoid use of the `public` modifier.
* Default to `private`.
* Consider using `protected` when it needs to be visibile in subclasses, too.
* Avoid use of `internal` unless necessary.

----------
## <a name="objects"></a>4. Objects
---------
Objects should be named similarly to classes (see below). The are no special prefixes or suffixes to differentiate between objects and classes. Do not include `Singleton`,  `Object`, or `Instance` in the object name.

```kotlin
object MyObject {} // good
object IMyObject {} // bad
object MyObjectImpl {} // bad
object MyObjectSingleton {} // bad
```

Objects should be named according to their responsibility. For example, assume we have an object that we use to format a username.

```kotlin
object UsernameFormatter {} // good
object UserFormatter {} // bad, this does not format a user
object UsernameFormatterInstance {} // bad, instance is implied
```

It is common to use suffix an object with a design pattern. If this helps create readability, it is recommended. Attempt to stick with existing (or team agreed upon) design patterns.

```kotlin
object UserRepository {} // good
```

----------
## <a name="interfaces"></a>5. Interfaces
----------

1. Interface names should begin with an `I`. For example `IDataFacade`.

**Good:**
```kotlin
interface IDataFacade {}
class HttpApi(): IDataFacade {}
```

**Good:**
```kotlin
interface IDataFacade {}
class DataFacade(): IDataFacade{}
```

**Bad:**
```kotlin
interface DataFacade {} // needs an "I"
class HttpApi(): DataFacade {}
```

Direct implementations of the interface should not begin with an `I` and not end with an `Impl`.

**Bad:**

```kotlin
interface IDataFacade {} // this isnt bad
class DataFacadeImpl(): IDataFacade {} // this is
```

----------
## <a name="functions"></a>6. Functions
----------

Functions are intended to achieve something and should be named as with, preferably starting with a verb.

```kotlin
fun user() { ... } // bad
fun getUser() { ... } // good
```

A function that returns a boolean can start with `get` or, more preferably, `is` (or similar). Using `is` to pose a simple yes/no question clearly indicates that a boolean is expected.

```kotlin
fun getIsActive(): Boolean { ... } // okay
fun isActive(): Boolean { ... } // good
fun hasEmail(): Boolean { ... } // good
fun email(): Boolean { ... } // bad
```

Always use a function expression when a function would otherwise only contain one expression unless that expression would span multiple lines

```kotlin
fun isActive(user: User) = user.isActive && user.isNotBanned // good
fun isActive(user: User): Boolean {
  return user.isActive && user.isNotBanned
} // bad

fun isActive(user: User) = user.isActive &&
  user.isNotBanned &&
  user.isPaid &&
  user.isNotLegacy // bad
```

### <a name="class-functions"></a>6.1 Class Functions

Prefer using public properties instead of functions that begin with `get`.
```kotlin
var user = User() // good
fun getUser() { ... } // bad
```

Override getters and/or setters instead of providing a custom get/set function.

**Good:**
```kotlin
var name = ""
  get() = field.toUpperCase()
  set(v: String) { field = v.toLowerCase()}
```

**Bad:**

```kotlin
private var _name = ""

fun getName(): String = _name.toUpperCase()
fun setName(v: String) { _name = v.toLowerCase }
```

When simply modifing the visibility of the accessor method, do not provide a method body.

**Good:**
```kotlin
var name: String = "Brad"
  private set
```

**Bad:**
```kotlin
var name: String = "Brad"
  private set(n: String) { field = n }
```

### <a name="extension-functions"></a>6.2 Extension Functions
Yes

### <a name="anonymous-functions"></a>6.3 Anonymous Functions

Prefer lambda expressions over anonymous functions.

```kotlin
listOf<Int>(1,2,3).find { it % 2 == 0 } // good
listOf<Int>(1,2,3).find(fun (i: Int): Boolean { return i % 2 == 0 }) // bad
```

Use clear parameter names when writing lamdba expressions if the intentions of the lambda is not obvious.

```kotlin
listOf<Int>(1,2,3).map { value -> value + 1 } // good
listOf<Int>(1,2,3).map { x -> x + 1 } // okay, obvious lambda body
listOf<Int>(1,2,3).map { it + 1 } // okay, obvious lambda body
listOf<Int>(1,2,3).map { user -> user + 1 } // bad, unclear function body
listOf<Int>(1,2,3).map {
  val isDivisibleByTwo = it % 2 == 0
  if (isDivisibleByTwo) {
    arbitratyMap[it]
  } else {
    0
  }
} // bad, not clear function body or implications if `it` parameter
```

When defining lambdas that are nested, provide a name for each parameter to the lambda so as to avoid unclear nesting of `it`s.

```kotlin
val l = listOf(
  listOf(1,2,3),
  listOf(4,5,6),
  listOf(7,8,9)
)

l.map { sublist ->
  sublist.map { nestedValue -> nestedValue * 2 }
} // good

l.map {
  it.map { it * 2 }
} // bad
```

If the lambda body is not simple (IE: if it has any nested lambdas), insert a line break between the parameter declaration and the lambda body.

```kotlin
l.map { sublist ->
  sublist.map { nestedValue -> nestedValue * 2 }
} // good

l.map { sublist ->
  sublist.map { nestedValue ->
    nestedValue * 2
  }
} // good

l.map { sublist -> sublist.map { nestedValue -> nestedValue * 2 } } // bad
```

When defining anonymous functions as parameters, indicate the the param is a function in the name. This helps readability when calling the function with multiple function parameters.

```kotlin
fun<T> find(l: List<T>, searchFn: (T) => Boolean, defaultFn: () => T) // good
fun<T> find(l: List<T>, search: (T) => Boolean, default: () => T) // bad
```
