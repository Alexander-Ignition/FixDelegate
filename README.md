# FixDelegate

- [Class only protocols AnyObject vs class](https://forums.swift.org/t/class-only-protocols-class-vs-anyobject/11507)
- [Class and Subtype existentials](https://github.com/apple/swift-evolution/blob/master/proposals/0156-subclass-existentials.md)

```swift
protocol DelegateA: class {}

protocol DelegateB: AnyObject {}
```

```bash
& swiftc -print-ast main.swift
```

```swift
internal protocol DelegateA : AnyObject {
}

internal protocol DelegateB : AnyObject {
}
```

```bash
& swiftc -dump-parse main.swift
(source_file "main.swift"
  (protocol range=[main.swift:1:1 - line:1:28] "DelegateA" requirement signature=<null> inherits: <null>)
  (protocol range=[main.swift:3:1 - line:3:32] "DelegateB" requirement signature=<null> inherits: <null>))
```

```bash
& swiftc -dump-ast main.swift
(source_file "main.swift"
  (protocol range=[main.swift:1:1 - line:1:28] "DelegateA" <Self : DelegateA> interface type='DelegateA.Protocol' access=internal non-resilient requirement signature=<Self where Self : AnyObject> inherits: AnyObject)
  (protocol range=[main.swift:3:1 - line:3:32] "DelegateB" <Self : DelegateB> interface type='DelegateB.Protocol' access=internal non-resilient requirement signature=<Self where Self : AnyObject> inherits: AnyObject))
```
