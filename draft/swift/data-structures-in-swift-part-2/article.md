# Data Structures You Should Know

This is the second part of a two-part series on data structures in Swift. In the first part, [we delved into generics and built-in Swift collection types](https://www.pluralsight.com/guides/swift/data-structures-in-swift-part-1). 
Check it out in case you missed it. 

In this tutorial, we’re going implement some of the most popular data structures from scratch in Swift. 

Swift provides some handy collection types. We can use the generic __Array__, __Set__, and __Dictionary__ right away. Yet, there may be cases when we need custom collection types. In the upcoming chapters, we’ll take a closer look at stacks, queues, linked lists, and graphs.

# The Stack
The stack behaves like an array, in that it stores the elements in a specific order. However, unlike for arrays, we can’t randomly insert, retrieve and delete items from a stack. 

The stack only permits pushing a new item, that is, appending it to the end of the collection. Also, we can only remove the last item, also known as pop.

The stack provides a __Last-In-First-Out__ (in short, LIFO) access. The item that was added last (or pushed) can be accessed first (peek or pop).
Here’s an illustration of the LIFO behavior:

![description](https://raw.githubusercontent.com/pluralsight/guides/master/images/9ad7373a-1c7c-445c-bf4c-db53e493ecae.png)

The stack is useful if we need to **retrieve the elements in the order in which we added them**.
We could fulfill this requirements using an Array:
```
var numbers = [Int]()
numbers.append(1)
numbers.append(2)
numbers.append(3)
var last = numbers.last // peek - returns 3
last = numbers.popLast() // pop - returns 3
last = numbers.popLast() // pop - returns 2
last = numbers.popLast() // pop - returns 1
```
But, the array does not prevent us from (accidentally) accessing the elements in a different order:
```
last = numbers[2]
numbers.remove(at: 3)
```
Thus, if we need a strict LIFO behavior, we have to create a custom stack collection.
Our Stack needs three methods:  
- push() - to append a new item
- peek() - retrieves the last element without removing it
- pop() - removes the last element and returns it

Optionally, we can add a ```removeAll()``` method to wipe out the stack.

There are different ways to implement these requirements. Let’s follow a [protocol-oriented approach and declare a protocol first](https://www.pluralsight.com/guides/swift/protocol-oriented-programming-in-swift) .
```
protocol Stackable {
    associatedtype Element
    mutating func push(_ element: Element)
    func peek() -> Element?
    mutating func pop() -> Element?
    mutating func removeAll()
}
```
The Stackable protocol defines all the requirements needed to define a stack. 

We use an ```associatedtype``` called Element that acts as a placeholder. Types that adopt the Stackable protocol can provide the exact type.

Notice the use of ```mutating``` for all the methods which change the contents of the stack. We must mark methods that modify a structure as ```mutating```. 
Class methods can always modify the class. Thus we don’t need to use the ```mutating``` keyword in classes.

>We used the mutating keyword in the Stackable protocol to also allow structs adopt it.

Next, we implement a generic stack.
```
struct Stack<T>: Stackable {
    typealias Element = T

    fileprivate var elements = [Element]()
    
    mutating func push(_ element: Element) {
        elements.append(element)
    }
    
    func peek() -> Element? {
        return elements.last
    }
    
    mutating func pop() -> Element? {
        return elements.popLast()
    }
    
    mutating func removeAll() {
        elements.removeAll()
    }
}
```
```Stack``` can work with any type:
```
var stackInt = Stack<Int>()
var stackString = Stack<String>()
var stackDate = Stack<Date>()
```
We can use the stack as follows: 
```
stackInt.push(1)
print("push() ->", stackInt)  
// Output: push() -> [1]

stackInt.push(2) 
print("push() ->", stackInt)  
// Output: push() -> [1, 2]

stackInt.push(3) 
print("push() ->", stackInt)  
// Output: push() -> [1, 2, 3]

print("peek() ->", stackInt.peek() ?? "Stack is empty")  
// Output: peek() -> 3

print("peek() ->", stackInt.peek() ?? "Stack is empty")  
// Output: peek() -> 3

print("peek() ->", stackInt.peek() ?? "Stack is empty")  
// Output: peek() -> 3

print("pop() ->", stackInt.pop() ?? "Stack is empty")  
// Output: pop() -> 3

print("pop() ->", stackInt.pop() ?? "Stack is empty")  
// Output: pop() -> 2

print("pop() ->", stackInt.pop() ?? "Stack is empty")  
// Output: pop() -> 1

stackInt.pop() 
print(stackInt)  // Output:[]
```
For better readability, we can add a custom description through a type extension. The ```CustomStringConvertible``` protocol defines the ```description``` computed property. By implementing it, we can add a custom description to our type. For our ```Stack``` type, we simply log the contents of the internal ```elements``` array.
```
extension Stack: CustomStringConvertible {
    var description: String {
        return "\(elements)"
    }
}
```
# Queues
Unlike the stack, the queue is open at both ends. The queue provides __First-In-First-Out__ (FIFO) access. New elements are added to the bottom of the queue. We can retrieve elements from the top of the queue.

Here’s an illustration of the queue in action:

![description](https://raw.githubusercontent.com/pluralsight/guides/master/images/086a1691-cadd-492f-83eb-6a7365ea4fe2.png)

The ```Enqueuable``` protocol defines the requirements for a basic queue:
```
protocol Enqueuable {
    associatedtype Element
    mutating func enqueue(_ element: Element)
    func peek() -> Element?
    mutating func dequeue() -> Element?
    mutating func removeAll()
}
```
The ```enqueue(:)``` method adds an item to the bottom of the queue, whereas the ```dequeue()``` method removes and returns the element from the top. ```peek()``` simply returns the top element without removing it.

We could implement the ```Enqueuable``` protocol as follows:
```
struct Queue<T>: Enqueuable {
    typealias Element = T
    
    fileprivate var elements = [Element]()
    
    mutating func enqueue(_ element: Element) {
        elements.append(element)
    }
    
    func peek() -> Element? {
        return elements.first
    }
    
    mutating func dequeue() -> Element? {
        guard elements.isEmpty == false else {
            return nil
        }
        return elements.removeFirst()
    }
    
    mutating func removeAll() {
        elements.removeAll()
    }
}

extension Queue: CustomStringConvertible {
    var description: String {
        return "\(elements)"
    }
}
```
Now that we have a generic Queue, we can use it with any type:
```
var queueInt = Queue<Int>()
var queueString = Queue<String>()
var queueData = Queue<Data>()

queueInt.enqueue(1)
print("enqueue() ->", queueInt)
// Output: enqueue() -> [1]

queueInt.enqueue(2)
print("enqueue() ->", queueInt)
// Output: enqueue() -> [2]

queueInt.enqueue(3)
print("enqueue() ->", queueInt)
// Output: enqueue() -> [3]

print("peek() ->", queueInt.peek() ?? "Stack is empty")
// Output: peek() -> 1

print("peek() ->", queueInt.peek() ?? "Stack is empty")
// Output: peek() -> 1

print("peek() ->", queueInt.peek() ?? "Stack is empty")
// Output: peek() -> 1

print("dequeue() ->", queueInt.dequeue() ?? "Stack is empty")
// Output: dequeue() -> 1

print("dequeue() ->", queueInt.dequeue() ?? "Stack is empty")
// Output: dequeue() -> 2

print("dequeue() ->", queueInt.dequeue() ?? "Stack is empty")
// Output: dequeue() -> 3

queueInt.dequeue()
print(queueInt)
// Output: []
```
