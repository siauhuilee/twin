UIAutomation is the .NET automation and accessibility API that most of Twin is based on. It is available in .NET 3.5 or later.

# Overview #

[UIAutomation](http://msdn.microsoft.com/en-us/library/ms747327.aspx) provides a tree of [AutomationElement](http://msdn.microsoft.com/en-us/library/system.windows.automation.automationelement.aspx)s rooted at the Desktop. It represents all elements of a the Windows UI.
The tree is populated by AutomationProviders which implement the UIAutomation API in terms of Win32, WPF, and other graphical toolkits.

There are two APIs to navigate this tree:
  * A traversal API based on the [TreeWalker](http://msdn.microsoft.com/en-us/library/system.windows.automation.treewalker.aspx) class.
  * A search API based on AutomationElement.[FindAll](http://msdn.microsoft.com/en-us/library/system.windows.automation.automationelement.findall.aspx)()

Elements have properties that can be queried, such as:
  * **ControlType** - describes the type of element (e.g. Button)
  * **Name** - the text of textfields, menu items etc
  * **AutomationId** - a programmatic ID for the element, useful but rarely set
  * **Bounds** - the rectangle this element occupies on screen

Elements can provide any number of ControlPatterns, which define extra behaviours and properties, for example, a window might support:

  * **TransformPattern** - Resize and move the window
  * **WindowPattern** - Maximized/Minimized/Restored property, close the window.

# Bugs and issues #

## Threading ##

Although AutomationElements don't have a documented thread-affinity, passing them between threads (even if not used concurrently) seems to cause problems in some cases.

Additionally when there are interactions with COM components (e.g. an embedded MSHTML control) the accessing thread should be in [Single-Threaded Apartment mode](http://msdn.microsoft.com/en-us/library/system.threading.apartmentstate.aspx). You can only have one such thread per process.

In Twin RC the HTTP requests are handled in separate (MTA) threads. When we need to access the UIAutomation APIs, a job is added to a queue and processed by the STA worker thread.

## FindAll filtering ##

According to the [documentation](http://msdn.microsoft.com/en-us/library/ee671698(v=vs.85).aspx#views), the [FindAll](http://msdn.microsoft.com/en-us/library/system.windows.automation.automationelement.findall.aspx)() method should search the raw (unfiltered) automation tree. You can search filtered trees (such as the Control view) by passing the corresponding Condition to FindAll.

In fact, FindAll appears to search the Control view, and so will never report elements not present in this view. Some UI structure is filtered out of the Control view, and sometimes that structure is needed to identify elements.

The TreeWalker based API works correctly on the raw view, so Twin uses that mechanism to perform its own searches and doesn't use FindAll or FindFirst.

(Twin contains an almost-drop-in replacement of FindAll that is implemented in terms of [TreeWalker.RawTreeView](http://msdn.microsoft.com/en-us/library/system.windows.automation.treewalker.rawviewwalker.aspx)).

## ControlType names ##

[ControlType](ControlType.md)s act similarly to (object-oriented) classes, and it's convenient to model them as classes/interfaces in the client library. However the names present a problem - there's a control type called List, and many programming languages have a commonly-used class called List. Instead of having to disambiguate every time, we renamed these control types:

  1. List > ListControl
  1. Tree > TreeControl
  1. ScrollBar > ScrollBarControl (due to [the way we support scrolling](Scrolling.md))