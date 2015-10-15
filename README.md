#The Swift Style Guide for Wordpress Mobile apps
So you want to write some code for WordPress for iOS. That’s nice, thanks a lot. But before that take some minutes to read some tips that will probably make everyone’s life easier. Much of this guide is adapted from [Google's Objective-C Style Guide](http://google-styleguide.googlecode.com/svn/trunk/objcguide.xml). You should read that as well as [Apple's Coding Guidelines for Cocoa](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CodingGuidelines/CodingGuidelines.html).

## Before You Commit
* Review what you commit: make sure you’re not leaving out anything, and that everything that you commit is meant to be there. `git add -p` is your friend here.
* Check for whitespace. Not the most critical thing, but if you follow the previous step, you might see unnecessary white space added at the end of a line. Get rid of it
* No NSLog in commits. We all use NSLog (or our WP* FileLogger variants) to debug. Unless you really want that to be on production, and logged into the wordpress.log file, remove it before commit. Our app is verbose enough already.
* No commented out code. If the code was already there and you’re removing it to test something, don’t comment it, just remove it. You can always go back by checking out a previous version from source control.
* Avoid new compiler warnings. A compiler warning might seem harmless, but when we compile for the app store, we use `-Werror`, so those insignificant warnings turn into real errors and force us to investigate what’s wrong at the last minute. You really don’t want to be messing with the code at that point.

If there’s a ticket associated, make sure to use “refs #123″ or “fixes #123″ in the commit message. It makes easier to track why things changed

## Spacing and Formatting
### Spaces vs Tabs
Use only spaces, and indent 4 spaces at a time.
### Line Length
Each line of text in your code should try to be at most 100 characters long. Objective C is a fairly verbose language and occasionally it makes sense to extend past the 100 character limit. However, this should be a rare occurrence and not commonplace.
### Method Declarations and Definitions
One space should be used between the - or + and the return type, and no spacing in the parameter list except between parameters.
Methods should look like this:

```objective-c
- (void)doSomethingWithString:(NSString *)theString
{
  ...
}
```

If you have more than one parameter, giving each its own line is preferred. If multiple lines are used, align each using the colon before the parameter.

```objective-c
- (void)doSomethingWith:(GTMFoo *)theFoo
                   rect:(NSRect)theRect
               interval:(float)theInterval
{
  ...
}
```

### Method Invocations
Method invocations should be formatted much like method declarations. When there's a choice of formatting styles, follow the convention already used in a given source file.
Invocations should have all arguments on one line:
```objective-c
[myObject doFooWith:arg1 name:arg2 error:arg3];
```
or have one argument per line, with colons aligned:

```objective-c
[myObject doFooWith:arg1
               name:arg2
              error:arg3];
```

Don't use any of these styles:

```objective-c
[myObject doFooWith:arg1 name:arg2  // some lines with >1 arg
              error:arg3];

[myObject doFooWith:arg1
               name:arg2 error:arg3];

[myObject doFooWith:arg1
          name:arg2  // aligning keywords instead of colons
          error:arg3];
```

###Protocols
There should not be a space between the type identifier and the name of the protocol encased in angle brackets.
This applies to class declarations, instance variables, and method declarations. For example:
```objective-c
@interface MyProtocoledClass : NSObject<NSWindowDelegate> {
  id<MyFancyDelegate> _delegate;
}
- (void)setDelegate:(id<MyFancyDelegate>)aDelegate;
@end
```

###Blocks
Blocks are preferred to the target-selector pattern when creating callbacks, as it makes code easier to read. Code inside blocks should be indented four spaces.
There are several appropriate style rules, depending on how long the block is:
* If the block can fit on one line, no wrapping is necessary.
* If it has to wrap, the closing brace should line up with the first character of the line on which the block is declared.
* Code within the block should be indented four spaces.
* If the block is large, e.g. more than 20 lines, it is recommended to move it out-of-line into a local variable.
* If the block takes no parameters, there are no spaces between the characters ^{. If the block takes parameters, there is no space between the ^( characters, but there is one space between the ) { characters.
* Two space indents inside blocks are also allowed, but should only be used when it's consistent with the rest of the project's code.

```objective-c
// The entire block fits on one line.
[operation setCompletionBlock:^{ [self onOperationDone]; }];

// The block can be put on a new line, indented four spaces, with the
// closing brace aligned with the first character of the line on which
// block was declared.
[operation setCompletionBlock:^{
    [self.delegate newDataAvailable];
}];

// Using a block with a C API follows the same alignment and spacing
// rules as with Objective-C.
dispatch_async(_fileIOQueue, ^{
    NSString* path = [self sessionFilePath];
    if (path) {
      // ...
    }
});

// An example where the parameter wraps and the block declaration fits
// on the same line. Note the spacing of |^(SessionWindow *window) {|
// compared to |^{| above.
[[SessionService sharedService]
    loadWindowWithCompletionBlock:^(SessionWindow *window) {
        if (window) {
          [self windowDidLoad:window];
        } else {
          [self errorLoadingWindow];
        }
    }];

// Large blocks can be declared out-of-line.
void (^largeBlock)(void) = ^{
    // ...
};
[_operationQueue addOperationWithBlock:largeBlock];
```

### Braces

When writing code that needs braces we generally want the brace on a new line rather than on the same line. The exception to this rule would be for if statements or for statements.
```objective-c
// Bad
- (void)someMethod {
  // Do something
}

// Good
- (void)someMethod
{
 // Do something
}

// Good
if (condition) {
  // Do something
}

// Good
for (int i=0; i < len; i++) {
  // Do something
}
```

### If Statements

When writing if statements, make sure to use a curly brace even if it's a one line statement. Always put the opening curly brace on the same line as an if. The only exception to this rule is when the condition is long enough to need separating between multiple lines. Also make sure there is a space between the `if` and the opening parenthesis.
```objective-c
// Good
if (someValue != nil) {
  [self doSomething];
}

// Good
if (someReallyLongVariableName != nil
    && someOtherLongVariableName != nil)
{
  [self doSomething];
}

// Bad
if (someValue != nil)
  [self doSomething];
```

### Ternary Operators

Be cautious about using the ternary operator as it can make code very difficult to read. Only use a ternary operator if using it makes the code easier to read.

```objective-c

// Good
  int minVal = (a < b) ? a : b

// Bad
  [self.view addSubview:((currentlyVisibleView == self.photoSelectorView) ? self.textEditorView : self.photoSelectorView)];

```

### Multi Line Declarations

Don't declare a series of variables on one line but rather split them up into individual lines. The reason for this is that splitting them into multiple lines makes the code easier to read and it makes diffs easier to analyze.

```objective-c

// Bad
@property (nonatomic, copy) NSString *username, *url, *password;

// Good
@property (nonatomic, copy) NSString *username;
@property (nonatomic, copy) NSString *url;
@property (nonatomic, copy) NSString *password;

```

##Naming
Naming rules are very important in maintainable code. Objective-C method names tend to be very long, but this has the benefit that a block of code can almost read like prose, thus rendering many comments unnecessary.

When writing pure Objective-C code, we mostly follow standard [Objective-C naming rules](http://developer.apple.com/documentation/Cocoa/Conceptual/CodingGuidelines/CodingGuidelines.html).

Any class, category, method, or variable name may use all capitals for initialisms within the name. This follows Apple's standard of using all capitals within a name for initialisms such as URL, TIFF, and EXIF. An exception to this is when passing a URL as a _NSString_ we prefer to not to use all capitals. The reason for this is that a non all capitalized URL stands out as more obvious that the variable in question is a _NSString_ instead of a _NSURL_.

```objective-c
- (void)displayWebsite:(NSURL *)websiteURL
{
  ...
}

- (void)displayWebsite:(NSString *)websiteUrl
{
  ...
}
```

###File Names
File names should reflect the name of the class implementation that they contain—including case.  

File names for categories should include the name of the class being extended, e.g. _NSString+Helpers.h_ or _UIColors+Helpers.h_

###Class Names
Class names (along with category and protocol names) should start as uppercase and use mixed case to delimit words.

In application-level code, prefixes on class names should generally be avoided. Having every single class with same prefix impairs readability for no benefit. When designing code to be shared across multiple applications, prefixes are acceptable and recommended (e.g. _WPXMLRPCEncoder_).

###Objective-C Method Names
Method names should start as lowercase and then use mixed case. Each named parameter should also start as lowercase.  
The method name should read like a sentence if possible, meaning you should choose parameter names that flow with the method name. (e.g. _convertPoint:fromRect_: or _replaceCharactersInRange:withString:_). See [Apple's Guide to Naming Methods](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CodingGuidelines/Articles/NamingMethods.html#//apple_ref/doc/uid/20001282-BCIGIJJF) for more details.  

Accessor methods should be named the same as the variable they're "getting", but they should not be prefixed with the word "get". For example:
```objective-c
- (id)getDelegate;  // AVOID
- (id)delegate;    // GOOD
```

###Variable Names
Variables names start with a lowercase and use mixed case to delimit words. Instance variables have leading underscores. For example: _myLocalVariable_, *_myInstanceVariable*.  

**Common Variable Names**

Do not use Hungarian notation for syntactic attributes, such as the static type of a variable (int or pointer). Give as descriptive a name as possible, within reason. Don't worry about saving horizontal space as it is far more important to make your code immediately understandable by a new reader. For example:
```objective-c
// Bad
int w;
int nerr;
int nCompConns;
tix = [[NSMutableArray alloc] init];
obj = [someObject object];
p = [network port];

// Good
int numErrors;
int numCompletedConnections;
tickets = [[NSMutableArray alloc] init];
userInfo = [someObject object];
port = [network port];
```
**Instance Variables**  
Instance variables are mixed case and should be prefixed with an underscore e.g. *_usernameTextField*.

###Comments
Though a pain to write, they are absolutely vital to keeping our code readable. The following rules describe what you should comment and where. But remember: while comments are very important, the best code is self-documenting. Giving sensible names to types and variables is much better than using obscure names and then trying to explain them through comments.  

When writing your comments, write for your audience: the next contributor who will need to understand your code. Be generous—the next one may be you!  

**File Comments**  
A file may optionally start with a description of its contents.
Every file should contain the following items, in order:
* GPL License
* a basic description of the contents of the file if necessary.

If you make significant changes to a file with an author line, consider deleting the author line since revision history already provides a more detailed and accurate record of authorship.

**Declaration Comments**  
Every interface, category, and protocol declaration should have an accompanying comment describing its purpose and how it fits into the larger picture.
```objective-c
// A delegate for NSApplication to handle notifications about app
// launch and shutdown. Owned by the main app controller.
@interface MyAppDelegate : NSObject {
  ...
}
@end
```
If you have already described an interface in detail in the comments at the top of your file feel free to simply state "See comment at top of file for a complete description", but be sure to have some sort of comment.  

Additionally, each method in the public interface should have a comment explaining its function, arguments, return value, and any side effects.  

Document the synchronization assumptions the class makes, if any. If an instance of the class can be accessed by multiple threads, take extra care to document the rules and invariants surrounding multithreaded use.

##Cocoa and Objective-C Features
###Interface files

Interface files should be as short as possible: don't declare instance variables, and declare only properties and methods that need to be exposed to other classes. Everything else should go into an interface extension inside the implementation file. Remember that you don't need to declare private methods anymore with a modern Xcode version.

###Overridden NSObject Method Placement
It is strongly recommended and typical practice to place overridden methods of _NSObject_ at the top of an _@implementation_. This commonly applies (but is not limited) to the _init..._, _copyWithZone:_, and _dealloc_ methods. _init_... methods should be grouped together, followed by the _copyWithZone:_ method, and finally the _dealloc_ method.

###Initialization
Don't initialize variables to _0_ or _nil_ in the init method; it's redundant.  

All memory for a newly allocated object is initialized to _0_ (except for isa), so don't clutter up the init method by re-initializing variables to _0_ or _nil_.

###Keep the Public API Simple
Keep your class simple; avoid "kitchen-sink" APIs. If a method doesn't need to be public, don't make it so.  

### Use Root Frameworks
Include root frameworks over individual files.  

While it may seem tempting to include individual system headers from a framework such as Cocoa or Foundation, in fact it's less work on the compiler if you include the top-level root framework. The root framework is generally pre-compiled and can be loaded much more quickly. In addition, remember to use #import rather than #include for Objective-C frameworks.
```objective-c
// good
#import <Foundation/Foundation.h>

// avoid
#import <Foundation/NSArray.h>
...
```

###Avoid Accessors During init and dealloc
Instance subclasses may be in an inconsistent state during _init_ and _dealloc_ method execution, so code in those methods should avoid invoking accessors.  

Subclasses have not yet been initialized or have already deallocated when _init_ and _dealloc_ methods execute, making accessor methods potentially unreliable. Whenever practical, directly assign to and release ivars in those methods rather than rely on accessors.

```objective-c
// Good

- (id)init
{
  self = [super init];
  if (self) {
    _bar = [[NSMutableString alloc] init];
  }
  return self;
}

- (void)dealloc
{
  [_bar release];
  [super dealloc];
}

// Avoid

- (id)init
{
  self = [super init];
  if (self) {
    self.bar = [NSMutableString string];
  }
  return self;
}

- (void)dealloc
{
  self.bar = nil;
  [super dealloc];
}
```
###Setters copy NSStrings
Setters taking an _NSString_, should always copy the string it accepts.  

Never just retain the string. This avoids the caller changing it under you without your knowledge. Don't assume that because you're accepting an _NSString_ that it's not actually an _NSMutableString_.  

```objective-c
- (void)setFoo:(NSString *)aFoo
{
  [_foo autorelease];
  _foo = [aFoo copy];
}
```

###nil Checks
Use _nil_ checks for logic flow only.  

Use _nil_ checks for logic flow of the application, not for crash prevention. Sending a message to a nil object is handled by the Objective-C runtime. If the method has no return result, you're good to go. However if there is one, there may be differences based on runtime architecture, return size, and OS X version (see Apple's documentation for specifics).

###BOOL Pitfalls
Be careful when converting general integral values to _BOOL_. Avoid comparing directly with _YES_.  

_BOOL_ is defined as a signed char in Objective-C which means that it can have values other than _YES_ (1) and _NO_ (0). Do not cast or convert general integral values directly to _BOOL_. Common mistakes include casting or converting an array's size, a pointer value, or the result of a bitwise logic operation to a _BOOL_ which, depending on the value of the last byte of the integral result, could still result in a _NO_ value. When converting a general integral value to a _BOOL_ use ternary operators to return a _YES_ or _NO_ value.  

You can safely interchange and convert _BOOL_, *_Bool* and _bool_ (see C++ Std 4.7.4, 4.12 and C99 Std 6.3.1.2). You cannot safely interchange _BOOL_ and _Boolean_ so treat _Booleans_ as a general integral value as discussed above. Only use _BOOL_ in Objective C method signatures.  

Using logical operators (_&&_, _||_ and _!_) with _BOOL_ is also valid and will return values that can be safely converted to _BOOL_ without the need for a ternary operator.

```objective-c
// Bad

- (BOOL)isBold
{
  return [self fontTraits] & NSFontBoldTrait;
}
- (BOOL)isValid
{
  return [self stringValue];
}

// Good
- (BOOL)isBold
{
  return ([self fontTraits] & NSFontBoldTrait) ? YES : NO;
}
- (BOOL)isValid
{
  return [self stringValue] != nil;
}
- (BOOL)isEnabled
{
  return [self isValid] && [self isBold];
}
```
Also, don't directly compare _BOOL_ variables directly with _YES_. Not only is it harder to read for those well-versed in C, the first point above demonstrates that return values may not always be what you expect.
```objective-c
// Bad
BOOL great = [foo isGreat];
if (great == YES)
  // ...be great!

//Good
BOOL great = [foo isGreat];
if (great)
  // ...be great!
```
### Properties
Use of the _@property_ directive is preferred. Dot notation is allowed only for access to a declared @property.

Use of automatically synthesized instance variables is preferred.

**Location**  

A property's declaration must come immediately after the instance variable block of a class interface. A property's definition (if not using automatic synthesis) must come immediately after the _@implementation_ block in a class definition. They are indented at the same level as the _@interface_ or _@implementation_ statements that they are enclosed in.
```objective-c
@interface MyClass : NSObject
@property(copy, nonatomic) NSString *name;
@end

@implementation MyClass

- (id)init
{
  ...
}
@end
```
**Use Copy Attribute For Strings**  
NSString properties should always be declared with the _copy_ attribute.  

This logically follows from the requirement that setters for NSStrings always must use _copy_ instead of _retain_.  

**Dot notation**  
Dot notation is idiomatic style for Objective-C 2.0. It may be used when doing simple operations to get and set a _@property_ of an object, but should not be used to invoke other object behavior.
```objective-c
// Good
NSString *oldName = myObject.name;
myObject.name = @"Alice";
NSArray *array = [[NSArray arrayWithObject:@"hello"] retain];

// Bad
NSUInteger numberOfItems = array.count;  // not a property
array.release;                           // not a property
```
###Interfaces Without Instance Variables
Omit the empty set of braces on interfaces that do not declare any instance variables.
```objective-c
// Good
@interface MyClass : NSObject
// Does a lot of stuff
- (void)fooBarBam;
@end

// Bad
@interface MyClass : NSObject {
}
// Does a lot of stuff
- (void)fooBarBam;
@end
```

###NSNumber, NSArray and NSDictionary  Literals
For projects that use Xcode 4.4 or later with clang, the use of NSNumber, NSArray and NSDictionary [literals](http://clang.llvm.org/docs/ObjectiveCLiterals.html) is allowed and preferred.

NSNumber literals are used just like Objective C string literals. Boxing is used when necessary.
```objective-c
// NSNumber
NSNumber *fortyTwo = @42;
NSNumber *piOverTwo = @(M_PI / 2);
typedef NS_ENUM(NSUInteger, MyEnum) {
  MyEnumOne,
  MyEnumTwo = 2,
};
NSNumber *myEnum = @(MyEnumTwo);

// NSArray
NSArray *continents = @[@"North America", @"South America", @"Europe", @"Asia", @"Africa", @"Antarctica", @"Australia"];

// NSDictionary
NSDictionary *personInfo = @{ @"name" : @"John Doe", @"age" : @(25) };
```

###Constant definitions

Avoid using `#define` for constants, they should be defined using `const`. Constant names should start with an uppercase letter and use mixed case.

```objective-c
// Good
NSInteger const ReaderPostsToSync = 20;
NSString *const ReaderLastSyncDateKey = @"ReaderLastSyncDate";

// Bad
#define READER_POSTS_TO_SYNC 20;
#define READER_SYNC_DATE_KEY @"ReaderLastSyncDate";
```

Constants should be defined in the implementation file. If a class needs to expose a constant, it should be defined as `extern` on the interface file, then initialised in the implementation file:

```objective-c
//  Blog+Jetpack.h
extern NSString * const BlogJetpackErrorDomain;

//  Blog+Jetpack.m
NSString * const BlogJetpackErrorDomain = @"BlogJetpackError";
```
