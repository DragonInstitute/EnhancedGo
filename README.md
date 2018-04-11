# Enhanced Go

Enhanced Go provides some external feature support which can make you happy.

All the external feature will be generated to original go code.

# Grammar

Defines(Original go code):

```go
type someStructure struct { }

func genArray() []someStructure { /* ... */ }

func genElement() someStructure { /* ... */ }

func genPtrArray() []*someStructure { /* ... */ }

func genElementPtr() *someStructure { /* ... */ }
```

## Extension

```go
func extension (arr []*someStructure) Add(s *someStructure) []*someStructure { /* ... */ }

func testFunc() {
    arr := genArray()
    arr.Add(genElement())
}
```

The keyword `extension` after `func`. Will remove receiver and put it as a parameter.

The output code will be like this:

```go
func Add(arr []*someStructure, s *someStructure) []*someStructure { }

func testFunc() {
    arr := genArray()
    Add(arr, genElement())
}
```

## Fake Generic Code Generator

The code generation will generate different implementations instead of type check.

```go
func extension <T> (arr []T) Contains(s T) bool { /* ... */ }

func testFunc() {
    arr := genArray()
    arr.Contains(genElement())

    ptrArr := genPtrArray()
    arr.Contains(genElementPtr())
}
```

Will replace all generic type T to actual type.

Will generate:

```go
func Contains_Ptr_someStructure__(arr []*someStructure, s *someStructure) bool { /* ... */ }

func Contains_someStructure__(arr []someStructure, s someStructure) bool { /* ... */ }

func testFunc() {
    arr := genArray()
    Contains_someStructure__(arr, genElement())

    ptrArr := genPtrArray()
    Contains_Ptr_someStructure__(ptrArr, genElementPtr())
}
```

### Type Check

Fake Generic will check type, make sure the caller pass the same type.

### Type To String Rule

Just from left to right.

### Generic New/Make

Depends on the actual type of T, the appropriate new/make will be automatically chosen.