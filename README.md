# kotlin-styleguide
Opinions on a flexible language.

## Table of Contents
1. Files
2. Variables
3. Classes
   1. Enums
   2. Sealed Classes
   3. Data Classes
4. Objects
5. Interfaces
6. Functions
   1. Class Functions
   2. Extension Functions

___________
## 1. Files
___________
Files should be named according to their majority content. For example:

```
class User {}
```
should be named `User.kt`.

```
interface IUser {...}
```
should be named `IUser.kt`

When the content of the file is not contained within a single enclosure, consider naming the file after the general concept that the code acts upon. For example:
```
fun<T> List.of(t: T) {}

fun<T> List.from(fn: () => T) {}
```
could be named `ListExtensions.kt`

----------
## 2. Variables
----------
Variables should be named in regards to their data or state that they hold. Prefer lengthy (but descriptive) variable names over short (but confusing) variables.

```
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

```
class User() {} // good
class Foo() {} // bad
```

It is common to use suffix a class with a design pattern. If this helps create readability, it is recommended. Attempt to stick with existing (or team agreed upon) design patterns.

```
class UserRepository() {} // good
```

### 3.1 Enum Classes
Enums should be named according to the value they represent. If we needed an enum to determine if the user's calendar format was days, weeks, months, or years we would build one like so:

```
enum class CalendarFormat {
  DAYS, WEEKS, MONTHS, YEARS
}
```

Enum constants should be uppercase.
```
enum class CalendarFormat {
  DAYS, WEEKS, MONTHS, YEARS // good
}

enum class CalendarFormat {
  days, weeks, months, years // bad
}
```

----------
## 4. Objects
---------
Objects should be named similarly to classes (see below). The are no special prefixes or suffixes to differentiate between objects and classes. Do not include `Singleton`,  `Object`, or `Instance` in the object name.

```
object MyObject {} // good
object IMyObject {} // bad
object MyObjectImpl {} // bad
object MyObjectSingleton {} // bad
```

Objects should be named according to their responsibility. For example, assume we have an object that we use to format a username.

```
object UsernameFormatter {} // good
object UserFormatter {} // bad, this does not format a user
object UsernameFormatterInstance {} // bad, instance is implied
```

It is common to use suffix an object with a design pattern. If this helps create readability, it is recommended. Attempt to stick with existing (or team agreed upon) design patterns.

```
object UserRepository {} // good
```

----------
## 5. Interfaces
----------

1. Interface names should begin with an `I`. For example `IDataFacade`.
2. Direct implementations of the interface should not begin with an `I` and not end with an `Impl`.

Good:
```
interface IDataFacade {}
class HttpApi: IDataFacade {}
```

Good:
```
interface IDataFacade {}
class DataFacade: IDataFacade{}
```

Bad:
```
interface DataFacade {}
class HttpApi: DataFacade {}
```

Bad:
```
interface IDataFacade {} // this isnt bad
class DataFacadeImpl: IDataFacade {} // this is
```
----------
## 6. Functions
----------
