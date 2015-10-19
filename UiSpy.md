UISpy is a Microsoft tool for viewing the UiAutomation element tree, and performing actions on elements. This tree is almost the same as the Twin ElementTree (we filter it by process ID and rename some things to avoid common names like List).

UISpy can be a useful tool for determining the structure of your target application and determining the [Criteria](Criteria.md) you should use to identify elements. [TwinIDE](TwinIDE.md) is likely a better choice, however.

![http://twin.googlecode.com/svn/wiki/images/uispy.png](http://twin.googlecode.com/svn/wiki/images/uispy.png)

# Getting it #
UISpy is included with the [Windows SDK for .NET 3.5 SP1](http://www.microsoft.com/downloads/en/details.aspx?FamilyID=ab99342f-5d1a-413d-8319-81da479ab0d7&displaylang=en) and is located the start menu under **Microsoft Windows SDK** > **Tools**.

Later versions of the Windows SDK may include the 'Inspect Objects' tool instead, which is similar.

Some versions of Visual Studio install UISpy, and some do not.

# Raw View #

The default view in UISpy is the **Control View**, which filters out some structural elements. Select **View** > **Raw View**, this is the view that corresponds to Twin.

# Alternatives #

The [TwinIDE](TwinIDE.md) supports browsing the UI tree in a similar way.

Later versions of the Windows SDK may include the 'Inspect Objects' tool instead of UISpy, this does essentially the same thing.