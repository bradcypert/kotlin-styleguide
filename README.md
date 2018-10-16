# kotlin-styleguide
Opinions on a flexible language

## Table of Contents
1. Naming
   1. Files
   2. Interfaces
   3. Objects
   4. Classes
   5. Enums
   6. Variables
   7. Properties
   8. Functions
2. Types
   1. Sealed Classes
   2. Enums
   3. Standard Classes
3. Interfaces
4. Functions
   1. Class Functions
   2. Extension Functions

___________
## 1. Naming
___________
### 1.1 Files
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

### 1.2 Interfaces

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

### 1.3 Objects

### 1.4 Classes

### 1.5 Enums

### 1.6 Variables

### 1.7 Properties

### 1.8 Functions
----------
## 2. Types
----------
----------
## 3. Interfaces
----------
----------
## 4. Functions
----------
