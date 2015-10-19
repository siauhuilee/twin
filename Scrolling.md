In most cases, Twin exposes the behaviour of UiAutomation fairly directly, without too much 'wrapping'. However for scrolling elements, the tools UIAutomation provides seem unsatisfactory:
  * There are two mechanisms - ScrollBar controls and the Scroll pattern. Neither is universally available.
  * There are cases where UIAutomation doesn't recognise the scroll bars at all, and we need Win32 calls.

So for scrolling within elements, we have implemented a different model.

# Concept #
  * The client can request a 'scroll axis' for a particular element. For example we might want a horizontal scroll axis for a text area.
  * The server will examine the element tree to find the most likely way to scroll within that element. It might find a scroll bar, or a child that has the Scroll pattern, etc. A handle to this axis is returned to the client.
  * The axis supports two operations: report its current position on a scale from 0 to 1, and set its current position.
  * The client implements desired operations in terms of scroll axes.
  * For operations like 'scroll so child X is visible' multiple scrolls are required to determine how the scrollbar affects the position of child X. This seems to be necessary, as the underlying APIs don't provide enough information to avoid it.

# Protocol #

  * To request an axis, you GET `/element/<element-id>/axisX` (or `axisY`).
  * Axis handles live at `/element/<element-id>/axis/<axis-id>`.
  * (TODO: these could be combined into the same URL, aren't they already cached?)
  * Axis handles support GET or POST to get/set the current value

# Usage #

In Java:
```
ScrollBar bar = myPane.getVerticalScrollBar();
double oldPos = bar.getPosition();
bar.setPosition(oldPos + 0.1); // 10%
bar.scrollVisible(myButton);
```