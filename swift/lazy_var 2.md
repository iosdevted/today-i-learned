# [Swift] lazy var or let when creating views programmatically

Lazy initialization is a concept where initialization (construction) of variable content is delayed until its first usage. First access to such variable triggers initialization. Since content is not created until variable is used (needed) using lazy initialized variables can save resources.

That is primary drive behind lazy initialization. You don't create something until you need it. That is also logic you will use when deciding whether something should be lazy var or not.

If you are dealing with views (or anything else) that are always visible (needed) there is little point in using lazy initialization. On the other hand when you are dealing with instances that are not always needed - then using lazy var is justified.

If your view is always visible in presented view controller, you will not accomplish much by making it lazy. If it is visible only under specific circumstances - for instance when user expands some collapsed panel - then making it lazy makes sense. It will make your view controller load faster and use less memory by default.

As far as thread safety is concerned, lazy var are not thread safe in Swift.

That means if two different threads try to access the same lazy var at the same time, before such variable has been initialized it is possible that one of the threads will access partially constructed instance.

## References

[Make "lazy var" threadsafe](https://bugs.swift.org/browse/SR-1042)