## Block Party

It's time to get a little abstract. In programming we use classes to represent real-world objects. In Swiftris, our objects are shapes and four independent parts, building *blocks* if you will, compose each shape. Let's represent these independent blocks as objects. We'll do so by creating a brand new class named `Block`.

> Follow the same steps found in the *Array We Go* checkpoint to create a brand new Swift file named `Block`.

Replace its generated content with the following:

```swift(Block.swift)
-import Foundation
+import SpriteKit

// #1
+let NumberOfColors: UInt32 = 6

// #2
+enum BlockColor: Int, CustomStringConvertible {

// #3  
+    case Blue = 0, Orange, Purple, Red, Teal, Yellow

// #4
+    var spriteName: String {
+        switch self {
+        case .Blue:
+            return "blue"
+        case .Orange:
+          return "orange"
+        case .Purple:
+          return "purple"
+        case .Red:
+            return "red"
+        case .Teal:
+          return "teal"
+        case .Yellow:
+            return "yellow"
+        }
+    }

// #5
+    var description: String {
+        return self.spriteName
+    }

// #6
+    static func random() -> BlockColor {
+        return BlockColor(rawValue:Int(arc4random_uniform(NumberOfColors)))!
+    }
+}
```

As you may have guessed, this isn't everything you'll need to represent a block. The first part of this file defines an *enumeration*: `BlockColor`. Swiftris supports six different colors in total.

At **#1** we define the number of colors available to Swiftris, six. At **#2** we declare the enumeration. Its type is `Int` and it *implements* the `CustomStringConvertible` protocol.

[Learn more about Protocols in Swift.](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/Protocols.html)

Classes, structures and enums that implement `CustomStringConvertible` are capable of generating human-readable strings when debugging or printing their value to the console.

At **#3** we provide the full list of enumerable options, one for each color beginning with `Blue` at `0` and ending at `5` with `Yellow`.

[Read about Swift's Enumerations.](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/Enumerations.html)

At **#4** we define a *computed property*, `spriteName`. A computed property is one that behaves like a typical variable, but when accessing it, a code block generates its value each time. We could have put this inside of a function named, `getSpriteName()` but a computed property is the better design choice.

`spriteName` returns the correct filename for the given color, we use a simple `switch…case` statement to achieve this.

At **#5** we declare yet another computed property, `description`. Adhering to the `CustomStringConvertible` property requires us to provide this function. Without it, our code will fail to compile. It returns the `spriteName` of the color to describe the object.

[Interested in reading more about computed properties? Try here.](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/Properties.html)

At **#6**, we declare a static function named `random()`. As you may have guessed, this function returns a random choice among the colors found in `BlockColor`. It creates a BlockColor using the `rawValue:Int` initializer to setup an enumeration which assigned to the numerical value passed into it, in our case numbers `0` through `5`.

Now that we've mastered the colors, let's do what we came here to do:

```swift(Block.swift)
    static func random() -> BlockColor {
        return BlockColor(rawValue:Int(arc4random_uniform(NumberOfColors)))!
    }
}

// #7
+class Block: Hashable, CustomStringConvertible {
// #8
+    // Constants
+    let color: BlockColor

// #9
+    // Properties
+    var column: Int
+    var row: Int
+    var sprite: SKSpriteNode?

// #10
+    var spriteName: String {
+        return color.spriteName
+    }

// #11
+    var hashValue: Int {
+        return self.column ^ self.row
+    }

// #12
+    var description: String {
+        return "\(color): [\(column), \(row)]"
+    }

+    init(column:Int, row:Int, color:BlockColor) {
+        self.column = column
+        self.row = row
+        self.color = color
+    }
+}

// #13
+func ==(lhs: Block, rhs: Block) -> Bool {
+    return lhs.column == rhs.column && lhs.row == rhs.row && lhs.color.rawValue == rhs.color.rawValue
+}
```

Quick, we promise. At **#7**, we declare `Block` as a class which implements both the `CustomStringConvertible` and `Hashable` protocols. `Hashable` allows us to store `Block` in `Array2D`.

At **#8** we define our `color` property as `let`, meaning once we assign it, it can no longer be re-assigned. A block should not be able to change colors mid-game unless you decide to make *Swiftris: Epileptic Adventures*.

[Dive deeper into `var` vs `let`.](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/TheBasics.html#//apple_ref/doc/uid/TP40014097-CH5-ID310)

At **#9** we declare a `column` and `row`. These properties represent the location of the `Block` on our game board. The `SKSpriteNode` will represent the visual element of the `Block` which `GameScene` will use to render and animate each `Block`.

At **#10** we provided a convenient shortcut for recovering the sprite's file name. It shortened our code from `block.color.spriteName` to `block.spriteName`.

At **#11**, we implemented the `hashValue` calculated property, which `Hashable` requires us to provide. We return the exclusive-or of our `row` and `column` properties to generate a unique integer for each `Block`.

At **#12**, we implement `description` as we must do to adhere to the `CustomStringConvertible` protocol. We can place `CustomStringConvertible` object types in the middle of a string by surrounding them with `\(` and `)`.

For a blue `Block` at row 3, column 8, printing that `Block` would look like:
```swift
print("This block is \(block)")
// Prints "This block is blue: [8, 3]"
```

[Dig into the `CustomStringConvertible` protocol.](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Reference/Swift_CustomStringConvertible_Protocol/)

At **#13** we create a custom operator, `==`, when comparing one `Block` with another. It returns `true` if both `Block`s are in the same location and of the same color. The `Hashable` protocol inherits from the `Equatable` protocol, which requires us to provide this operator.

[Read more about custom operators in Swift.](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/AdvancedOperators.html)

Congratulations! You will probably *never* have to touch this file again. We managed to cram a lot of Swift techniques all into one place and we're proud of that at Bloc – no 'k'.
