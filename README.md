# **AAQZ Language**

**AAQZ** is a functional programming language implemented in Typed Racket. It is designed to evaluate expressions with an emphasis on closures, primitives, and abstract syntax. AAQZ features strong typing, robust memory management, and comprehensive error handling.

---

## **Key Features**
- **Typed Abstract Syntax**: Explicit data types for expressions (`ExprC`) and values (`Value`).
- **Lambda Functions**: Support for higher-order functions and closures.
- **Primitives**: Built-in arithmetic and comparison operations.
- **Memory Store**: Efficient memory handling using a mutable vector.
- **Error Handling**: Detailed error messages for invalid expressions or operations.

---

## **Core Syntax**

### Expressions (`ExprC`)
| Constructor | Description                                   | Example                           |
|-------------|-----------------------------------------------|-----------------------------------|
| `numC`      | Represents a numeric constant                | `(numC 5)`                       |
| `idC`       | Represents an identifier (symbol)            | `(idC 'x)`                       |
| `appC`      | Represents a function application            | `(appC (idC '+) (list (numC 1)))`|
| `strC`      | Represents a string constant                 | `(strC "hello")`                 |
| `boolC`     | Represents a boolean value                   | `(boolC #t)`                     |
| `ifC`       | Represents a conditional expression          | `(ifC (boolC #t) (numC 1) (numC 0))` |
| `lamC`      | Represents a lambda (anonymous function)     | `(lamC '(x) (idC 'x))`           |

---

### Values (`Value`)
| Constructor | Description                                   | Example                           |
|-------------|-----------------------------------------------|-----------------------------------|
| `numV`      | Represents a numeric value                   | `(numV 10)`                      |
| `strV`      | Represents a string value                    | `(strV "world")`                 |
| `boolV`     | Represents a boolean value                   | `(boolV #t)`                     |
| `primV`     | Represents a primitive operation             | `(primV '+)`                     |
| `cloV`      | Represents a closure                         | `(cloV '(x) (idC 'x) topenv)`    |

---

### Types (`Ty`)
| Type   | Description                                     |
|--------|-------------------------------------------------|
| `num`  | Represents numeric types                       |
| `str`  | Represents string types                        |
| `bool` | Represents boolean types                       |
| `arrow`| Represents function types with arguments and return types |
| `call` | Represents a function call with arguments      |

---

## **Built-in Primitives**
The following primitives are supported and mapped to the `primV` type:

| Operator | Description               | Example                          |
|----------|---------------------------|----------------------------------|
| `+`      | Addition                  | `(top-interp '(+ 1 2))` → `3`   |
| `-`      | Subtraction               | `(top-interp '(- 5 2))` → `3`   |
| `*`      | Multiplication            | `(top-interp '(* 3 2))` → `6`   |
| `/`      | Division                  | `(top-interp '(/ 6 3))` → `2`   |
| `<=`     | Less than or equal        | `(top-interp '(<= 2 3))` → `true` |
| `equal?` | Equality comparison       | `(top-interp '(equal? 3 3))` → `true` |

---

## **Environment and Store**

### Environment (`Env`)
The environment maps identifiers to memory addresses:
```racket
(struct binding ([id : Symbol] [address : Integer]) #:transparent)
```
# Memory Store (sto)

A mutable vector holding Values.  
Provides efficient allocation and retrieval of values during evaluation.

## Key Functions

### interp (Interpreter)

Evaluates an `ExprC` within a given environment and store:

```racket
(interp [exp : ExprC] [env : Env] [sto : (Mutable-Vectorof Any)]) : Value
```
### primsolve

Executes a primitve operation:

```racket
(primsolve [op : Symbol] [args : (Listof Value)] [sto : (Mutable-Vectorof Any)]) : Value
```

### parse (Parser)

Converts concrete syntax (`Sexp`) into abstract syntax (`ExprC):

```racket
(parse [sexpr : Sexp]) : ExprC
```

# Error Handling

AAQZ includes robust error handling with descriptive messages:

- **Undefined variable:** `"AAQZ: variable undefined, given: ~e"`
- **Division by zero:** `"AAQZ: Cannot divide by zero given: ~e"`
- **Invalid function application:** `"AAQZ: Invalid function in application"`

## Example Programs

### Simple Arithmetic

```racket
(top-interp '(+ 1 2)) → 3
```

### Conditional Expression
```racket
(top-interp '(if true 1 0)) → 1
```

### Lambda Function
```racket
(top-interp '(((X) => (+ x 1)) 2)) → 3
```

### Nested Expressions 
```racket
(top-interp '(((x y) => (+ x (* y 2))) 3 4)) → 11
```

# Testing

## RackUnit Tests

The AAQZ language includes extensive unit tests using RackUnit to ensure reliability. Example:

```racket
(check-equal? (interp (numC 1) topenv topsto) (numV 1))
(check-exn (regexp (regexp-quote "dividebyzero")) 
           (lambda () (primsolve '/ (list (numV 7) (numV 0)) topsto)))
```
