The graphical components of an application are exposed to you as a tree of **elements**.

![http://twin.googlecode.com/svn/wiki/images/ide.png](http://twin.googlecode.com/svn/wiki/images/ide.png)
  * The root of the tree is the desktop element
  * The children of the desktop are your application's "top level window" elements
  * Windows have child elements representing title bars, buttons, and panes
  * These trees can be deep, e.g. **window** contains **pane** contains **pane** contains **button**.

All elements have some common properties, such as

  * ID - a programmer-assigned identifier
  * Name - the text of the element, if any
  * ClassName - the Win32 Class of the element
  * ControlType - the type of element represented (e.g. TitleBar)

and all elements support certain actions like `click()` and `getDescendants(...)`.

# Navigating #

The client API provides access to the Desktop element, and operations to get the children and parent of an element. In Java:

```
Element desktop = app.getDesktop();
List<Element> topLevelChildren = desktop.getChildren();
for(Element e : topLevelChildren())
    System.out.println(e.getName());
```

Retrieving all children is cumbersome and inefficient, so getChildren() can take a parameter specifying [search criteria](Criteria.md).

```
import static org.ebayopensource.twin.Criteria.*;
List<Element> newDocuments = desktop.getChildren(name("Untitled"));
```

You can also search all descendants rather than just immediate children
```
List<Element> buttons = desktop.getDescendants(name("OK").and(type(Button.class)));
```

# Element-specific behavior #

Elements of certain [ControlType](ControlType.md)s support extra actions and properties. For example a Window element can be close()d and has a Minimized property.

Elements can also support [ControlPattern](ControlPattern.md)s, such as 'Expandable'. ControlPatterns also provide actions and properties, like Expanded. These are distinct from the ControlType because while one TreeItem might be Expandable, another might not be.

In the Java client, Element, Window and Expandable are all interfaces, and you can cast returned Elements to the appropriate type, as well as search by type.