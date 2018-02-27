# Overloaded Guard Statements

One of the goals I have in writing software is to create maintainable code. This comes from my experience in the .NET world and the [PASSMADE acronym](http://www.alliedc.com/pass-made-principal-for-software-architecture/) ("M" is for Maintainable). One of the measurements of maintainability is, how easy will it be for the new guy to read my code and understand what it does? [Improving the readability of your code improves its maintainability.](https://madhuraoakblog.wordpress.com/2014/04/30/readability-and-maintainability-of-code/)

I've seen some Swift code recently where a developer produced a very large [guard](http://nshipster.com/guard-and-defer/) statement. It looked something like this:

```swift
guard let firstName = receipt.firstName, let lastName = receipt.lastName, let city = receipt.city, let postalCode = receipt.postalCode else {
	return
}
```

I have two issues with this code. One is that it just isn't readable. While I do most of my work on a 27" iMac and have lots of horizontal space in my code editor, anyone with a smaller screen has to scroll this line to see the whole thing.

Second, there is no easy way to tell which on the guard conditions failed. In fact, there is no logging here, and no hard stop. Usually I see guard statements like this in place to keep later code from executing which, in this case, depends on the **firstName**, **lastName**, **city**, and **postalCode** variables. There is a very good chance that a failed guard here leads to an error condition elsewhere. It may not *crash* here, but the error is just propagated down the line. Debugging this sort of issue is really annoying because you end up having to step through many lines of code or throw in some random breakpoints, hoping they stop execution somewhere that gives a clue to the error.

A better way to write this, which does improve readability, would be:

```swift
guard
	let firstName = receipt.firstName,
	let lastName = receipt.lastName,
	let city = receipt.city,
	let postalCode = receipt.postalCode
else {
	return
}
```

This is much more readable. However, there is still no way to tell which guard condition is failing and it still just allows the program to run without handling the fact that the expected data is missing.

```Swift
guard let firstName = receipt.firstName else {
	preconditionFailure("receipt.firstName is nil.")
}

guard let lastName = receipt.lastName else {
	preconditionFailure("receipt.lastName is nil.")
}

guard let city = receipt.city else {
	preconditionFailure("receipt.city is nil.")
}

guard let postalCode = receipt.postalCode else {
	preconditionFailure("receipt.postalCode is nil.")
}
```

Much better. This version is still very easy to read and it now also resolves the other issue. Each guard statement is independent of the others and the use of **[preconditionFailure](https://developer.apple.com/documentation/swift/1539374-preconditionfailure)** allows this code to be testable and it emits a useful message for debugging purposes. ***One thing to keep in mind about preconditionFailure is that it fails in both Debug and Release builds.*** If you want the benefit of failing in Debug build but still want to avoid crashing at all costs in Release builds, you can use **[assertionFailure](https://developer.apple.com/documentation/swift/1539616-assertionfailure)** instead. Here is a good article on how to choose which failure is appropriate in Swift.