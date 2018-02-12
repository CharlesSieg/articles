# Development Tools for macOS: Part 1 - iOS Development
I do a lot of development work in macOS. I write iOS apps. I create web services that run on Node.js. I use databases in SQLite and MySQL. I write shell scripts and Python scripts. Sometimes I write React Native apps or full-stack React web apps. I create CloudFormation and Terraform scripts. I use the whole range of AWS products and services including SQS, SNS, Lambda, Aurora, ElastiCache, DynamoDB, Redshift, Kinesis, ElasticSearch, you name it. I thought it would be a good idea to itemize the resources I use in my work and publish it here in case it is useful to someone.

## [Xcode](https://developer.apple.com/xcode/)
I suspect pretty much everyone who does any Mac or iOS development has installed and regularly updates Xcode. It’s Apple’s official IDE for building Mac and iOS applications. It’s overall quality and stability rises and falls with each new release - Xcode 9 is in the waning phase - but it gets the job done. I use it to write application code and unit and integration tests. I [avoid using Interface Builder](https://medium.com/@charlessieg/avoid-storyboards-and-interface-builder-cc3363de4782) as much as possible.
Xcode is available in the Mac App Store and is free.

## [AppCode](https://www.jetbrains.com/objc)
AppCode is an excellent IDE made by JetBrains, a company which makes IDEs for many languages. I use a number of their products and have been a big fan going all the way back to their C# [Resharper](https://www.jetbrains.com/resharper) product which first came out over a decade ago.
AppCode fills pretty much the same role as Xcode except that it has a number of features that I find useful and which are not present in Xcode. One is its ability to find unnecessary imports, i.e. a module that is imported at the top of a Swift file but which is not actually used by the code in that file. Another is its ability to find unused constants and variables. You might have constants which were once used but, somewhere along the way, became unused. Unless you realize this and manually delete the constant, they hang around forever. Unused constants, etc. in AppCode automatically appear grayed-out so they are easy to spot and clean up. SwiftLint, described below, is also easier to set up and use in AppCode than in Xcode.
AppCode is available on a subscription basis for $199 per year or $19.90 per month. There is a free 30-day trial and AppCode is part of an “All Products Pack” subscription which includes access to most, if not all, of JetBrains’ tools for a single price. This becomes cost-effective if you use more than 3 of the tools.

## [SwiftLint](https://github.com/realm/SwiftLint)
SwiftLint is a tool created and maintained by [Realm](https://realm.io) (makers of the excellent [Realm database](https://realm.io/products/realm-database)) for enforcing Swift style and conventions. It can be easily added to AppCode, Xcode, or even [Atom](https://atom.io) if you use that for development. Approximately 75 rules are included but any of them can be enabled or disabled by modifying a configuration file. You can also create your own rules using a Regex-based syntax.
I use SwiftLint to make sure all of my code looks the same. SwiftLint does also have the ability to automatically correct certain violations.
SwiftLint is free to use and is available by simply cloning [the GitHub repo](https://github.com/realm/SwiftLint).

## [DB Browser for SQLite](http://sqlitebrowser.org)
DB Browser for SQLite is basically a visual tool to explore SQLite databases. You can create, design, and edit SQLite databases and associated tables and indexes. You can perform queries, browse, add, edit, or delete records, or import and export data. If you are writing iOS applications which store data in SQLite databases, this tool is invaluable.
I do recommend using SQLite directly for data access as opposed to using Core Data. Another good alternative is to use Realm. A list of frameworks, libraries, and packages for iOS development will be provided in a future article.

I do not use a package manager in my iOS development yet. I've wanted to because I'm used to using them with Python and Node.js but just haven't found any iOS package managers that work without adding a lot of overhead. For example, both CocoaPods and Carthage, in my opinion, are a pain to work with. The closest I've gotten is the [Swift Package Manager](https://swift.org/package-manager/) maintained on Swift.org. The main issue I have with them is that they are just not as easy to work with as, say, npm, the Node Package Manager. If I want to add a package with npm, it's:

`npm install mysql -save`

This installs the mysql package and it updates the Node project's package.json file. Nothing else to do; I can simply include the package in my program and it just works.*
