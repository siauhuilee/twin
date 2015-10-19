A control pattern represents a **unit of data and behaviour** that an element may have.

For example, an element that is Expandable has:

  * An expand action
  * A collapse action
  * An expanded/collapsed state

This is similar to the concept of an 'interface' in Java and other languages. However:

  * in Java, interfaces are generally fixed for a given type of object - e.g. all Strings are Serializable, but no Threads are.
  * in Twin, ControlPatterns are available on individual elements - one TreeItem might be Expandable, but another might not be.

## Note on Java client implementation ##

Note that in the Java client, ControlPatterns are implemented as interfaces. Any Java object representing an element is actually a [Proxy](http://download.oracle.com/javase/6/docs/api/java/lang/reflect/Proxy.html) implementing Element, some ControlType interface, and any number of ControlPattern interfaces. You can dynamically check the control types and patterns:

```
Element e = app.getDesktop().getDescendant(id("targetElt"));
if(e.is(Expandable.class)) {
   Expandable x = e.as(Expandable.class);
   x.expand();
}
```

### You shouldn't use instanceof! ###

Suppose you have an Element representing a TreeItem. You can store it in a TreeItem variable:

```
TreeItem x;
```

The common case is that a TreeItem is Expandable, so we want to make this simple.

```
x.expand(); // easy
x.as(Expandable.class).expand(); // not so easy
```

Therefore the TreeItem interface includes the Expandable interface, and in the uncommon case where you call expand() but the actual element is not Expandable, an exception is thrown.

Unfortunately as a side effect, this means for a non-expandable TreeItem:

```
if(x instanceof Expandable) { // always returns true
   Expandable e = (Expandable)x; // exception not thrown here
   x.expand(); // exception thrown here instead
}
```

so replace `instanceof` with `.is()` and casting with `.as()`

### Yeah, this is ugly ###

This isn't how you expect Java types to behave. The alternative is to strip down the ControlType interfaces, and not have them inherit any ControlPatterns. This would mean:

  * You use `x instanceof Expandable` instead of `x.is(Expandable.class)`
  * You use `(Expandable)x` instead of `x.as(Expandable.class)`
  * You need to use more casts, as Java won't understand that Windows tend to be Transformable, TreeItems Expandable etc.

Please give feedback here or on the mailing list as to which is better, we're really not sure.