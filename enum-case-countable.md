## Get the count of the enums

Many times you would want to know the count of all the cases in an enum, eg you have a enum which represents each row / section in a tableview and in the numberOfRows method you would get the total number of rows count. 
Below implementation will help you achieve exactly that.


```
// enum which provides a count of its cases
protocol CaseCountable {
    static func countCases() -> Int
    static var count : Int { get }
}


// provide a default implementation to count the cases for Int enums assuming starting at 0 and contiguous
extension CaseCountable where Self : RawRepresentable, Self.RawValue == Int {
    // count the number of cases in the enum
    static func countCases() -> Int {
        // starting at zero, verify whether the enum can be instantiated from the Int and increment until it cannot
        var count = 0
        while let _ = Self(rawValue: count) { count+=1 }
        return count
    }
}
```

Usage Eg

```
private enum Section: Int, CaseCountable {
        case empty = 0
        case recentCustomers
        
        static var count = Section.countCases()
    }
```

Here you can see the enum confroms to the CaseCountable Protocol. Here Section.count will return the total number of cases. 
You can note here is that the enum should be of Int type and the cases will be numbered continuously.

tags: swift, enum, count, enum count
