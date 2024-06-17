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

