# kotlin-styleguide
Opinions on a flexible language.

## Table of Contents
1. Files
2. Variables
3. Classes
   1. Enums
   2. Sealed Classes
   3. Data Classes
   4. Constructors
   5. Class Properties
   6. Visibility Modifiers
4. Objects
5. Interfaces
6. Functions
   1. Class Functions
   2. Extension Functions

___________
## 1. Files
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

## 2. Variables

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

## 3. Classes

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



### 3.1 Enum Classes

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

### 3.2 Sealed Classes

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

### 3.3 Data Classes

Use data class when creating simple data structures. Prefer to keep data classes simple and without business logic.

```kotlin
data class User(val name: String, val password: String) // good
class User(val name: String, val password: String) {} // bad
```

### 3.4 Constructors
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

Consider using a primary constructor with nullable parameters instead of a secondary constructor.

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

### 3.5 Class Properties

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

### 3.6 Visibility Modifiers
* Avoid use of the `public` modifier.
* Default to `private`.
* Consider using `protected` when it needs to be visibile in subclasses, too.
* Avoid use of `internal` unless necessary.

----------
## 4. Objects
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
## 5. Interfaces
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
## 6. Functions
----------
