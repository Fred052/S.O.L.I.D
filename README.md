# S.O.L.I.D

## What is Solid?

5 code architecture principles that, when applied together, intend to make it more likely that a programmer will create a
system that is easy to maintain and extend over time.


- Single Responsibility Principle (SRP)
- Open and Closed Principle (OCP)
- Liskov Substitution Principle (LSP)
- Interface Segregation Principle (ISP)
-  Dependency Inversion Principle (DIP)


  ## Single Responsibility Principle (SRP)

 NOTES:  A class should only be  responsible for one thing

Our Problem (This is not a SRP)
 ```swift
struct Product {
    let price: Double
}

struct Invoice {
    var products: [Product]
    let id = NSUUID().uuidString
    var discountPercentage: Double = 0
    
    var total: Double {
        let total = products.map({$0.price}).reduce(0, {$0 + $1})
        let discountedAmount = total * (discountPercentage / 100)
        return total - discountedAmount
    }
    
    func printInvoice() {
        print("----------------------")
        print("Invoice id: \(id)")
        print("Total cost $\(total)")
        print("Discounts: \(discountPercentage)")
        print("----------------------")
    }
    
    func saveInvoice() {
        // Save invoice data locally or to database
    }
}

let products: [Product] = [
    .init(price: 99.99),
    .init(price: 9.99),
    .init(price: 24.99)
]

let invoice = Invoice(products: products, discountPercentage: 20)
invoice.printInvoice()
```

Problem Solved (This is a SRP Code)

```swift
struct Product {
    let price: Double
}

struct Invoice {
    var products: [Product]
    let id = NSUUID().uuidString
    var discountPercentage: Double = 0
    
    var total: Double {
        let total = products.map({$0.price}).reduce(0, {$0 + $1})
        let discountedAmount = total * (discountPercentage / 100)
        return total - discountedAmount
    }
}

struct InvoicePrinter {
    let invoice: Invoice
    
    func printInvoice() {
        print("----------------------")
        print("Invoice id: \(invoice.id)")
        print("Total cost $\(invoice.total)")
        print("Discounts: \(invoice.discountPercentage)")
        print("----------------------")
    }
}

struct InvoicePersistence {
    let invoice: Invoice
    func saveInvoice() {
        // Save invoice data locally or to database
    }
}

let products: [Product] = [
    .init(price: 99.99),
    .init(price: 9.99),
    .init(price: 24.99)
]

let invoice = Invoice(products: products, discountPercentage: 20)
let printer = InvoicePrinter(invoice: invoice)
let persistence = InvoicePersistence(invoice: invoice)

printer.printInvoice()
persistence.saveInvoice()
```

Or Other Way 

```swift
struct Product {
    let price: Double
}

struct Invoice {
    var products: [Product]
    let id = NSUUID().uuidString
    var discountPercentage: Double = 0
    
    var total: Double {
        let total = products.map({$0.price}).reduce(0, {$0 + $1})
        let discountedAmount = total * (discountPercentage / 100)
        return total - discountedAmount
    }
    
    func printInvoice() {
       let printer = InvoicePrinter(invoice: self)
        printer.printInvoice()
    }
    
    func saveInvoice() {
        let persistence = InvoicePersistence(invoice: self)
        persistence.saveInvoice()
    }
}

struct InvoicePrinter {
    let invoice: Invoice
    
    func printInvoice() {
        print("----------------------")
        print("Invoice id: \(invoice.id)")
        print("Total cost $\(invoice.total)")
        print("Discounts: \(invoice.discountPercentage)")
        print("----------------------")
    }
}

struct InvoicePersistence {
    let invoice: Invoice
    func saveInvoice() {
        // Save invoice data locally or to database
    }
}

let products: [Product] = [
    .init(price: 99.99),
    .init(price: 9.99),
    .init(price: 24.99)
]

let invoice = Invoice(products: products, discountPercentage: 20)
invoice.printInvoice()

let invoice2 = Invoice(products: products)
invoice2.printInvoice()
```


## Open/Closed Principle (OCP)

SOFTWARE ENTITIES (CLASSES, MODULES, FUNCTIONS, ETC.) SHOULD BE OPEN FOR EXTENSION, BUT CLOSED FOR MODIFICATION. In other words, we can add additional functionality (extention) without touching the existing code (modification) of an object.

Unsuitable Code

```swift
struct InvoicePersistenceOCP {
    let invoice: Invoice
    
    func saveInvoiceToCoreData() {
        print("Save invoice to CoreData \(invoice.id)")
    }
    
    func saveInvoiceToDatabase() {
        print("Save invoice to Firestore \(invoice.id)")
    }
}
```


Suitable Code

```swift
protocol InvoicePersistable {
    func save(invoice: Invoice)
}

struct CoreDataPersistence: InvoicePersistable {
    func save(invoice: Invoice) {
        print("Save invoice to CoreData \(invoice.id)")
    }
}

struct DatabasePersistence: InvoicePersistable {
    func save(invoice: Invoice) {
        print("Save invoice to Firestore \(invoice.id)")
    }
}
```

## Liskov Substitution Principle (LSP)

Derived or child classes/structures must be substitutable for their base or parent classes

Unsuitable Code

```swift
class Bird {
    func fly() {
    }
}

class Ostrich: Bird {
    override func fly() {
        fatalError("Ostriches can't fly")
    }
}
```

Suitable Code

```swift
class Bird {
    // General bird criteria
}

class FlyingBird: Bird {
    func fly() {
    }
}

class Sparrow: FlyingBird {
    // Sparrow Criteria
}

class Ostrich: Bird {
    // Ostrich Criteria
}
```

## Interface Segregation Principle (ISP)

Do not force any client to implement an interface which is irrevelant to them.

Unsuitable Code

```swift
protocol GestureProtocol {
    func didTap()
    func didDoubleTap()
    func didLongPress()
}

struct SuperButton: GestureProtocol {
    func didTap() {
        
    }
    
    func didDoubleTap() {
        
    }
    
    func didLongPress() {
        
    }
}

struct DoubleTapButton: GestureProtocol {
    func didTap() { }
    
    func didDoubleTap() {
        print("DEBUG: Double tap...is")
    }
    
    func didLongPress() {}
}
```

Suitable Code 

```swift
protocol SingleTapProtocol {
    func didTap()
}

protocol DoubleTapProtocol {
    func didDoubleTap()
}

protocol LongPressProtocol {
    func didLongPress()
}


struct SuperBotton: SingleTapProtocol, DoubleTapProtocol, LongPressProtocol  {
    func didTap() {
        
    }
    
    func didDoubleTap() {
        
    }
    
    func didLongPress() {
        
    }
}

struct DoubleTapButton: DoubleTapProtocol {
    func didDoubleTap() {
    }
}
```




