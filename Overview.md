Twin is a tool for automating Windows graphical applications.  That is, it enables you to write code to click buttons, enter text, and view the results just as a user would.

Twin was developed at eBay to allow automatic functional testing of our Windows applications. The design is based on the web automation tool [Selenium/WebDriver](http://www.seleniumhq.org), and it can be used in similar ways.

# The moving parts #

![http://twin.googlecode.com/svn/wiki/images/movingparts.png](http://twin.googlecode.com/svn/wiki/images/movingparts.png)

Twin has a client/server architecture: you write **automation code** using the **client library** to perform tasks in an application.
When the program runs, the client library will connect to the **remote control server** and send it commands - the server launches the **target application** and does the actual button-clicking.

Why do we do this? It has several advantages:

  * **Ability to run target applications on a remote machine**. Particularly for software testing, it's convenient to run your tests from a development machine, but you want the target application to be in a known state (e.g. a fixed VM).

  * **Ability to write automation code in any language**. All you need is a client library for your language, which is much easier to port than the entire project. Currently only the Java client library is available, but as the protocol is HTTP+JSON based, other languages can be added.

  * **Running an automation farm**. Instead of pointing the client at a remote control server, it can be pointed at a hub that manages a collection of remote controls. Each session is allocated to a different remote controls, so many automation programs can be run at once. This is the same idea as Selenium Grid, and Twin will be compatible with Selenium 2 Grid.

A more detailed breakdown of these components is below.

## Twin IDE ##

Beside the client library and the remote control (RC), Twin comes with the 'IDE'. This is just an automation application (the yellow box above) but instead of carrying out a fixed task, it uses the twin-client API to examine the [ElementTree](ElementTree.md), and presents it graphically for you to explore.

![http://twin.googlecode.com/svn/wiki/images/ide.png](http://twin.googlecode.com/svn/wiki/images/ide.png)

Currently you can only look, but in the future the IDE will support performing actions too, and will help you writing automation code.

# Getting started #

  1. Grab the RC package (named something like twin-rc-standalone.zip) and extract it. Run sharpclaws.exe, it will start a web server on port 4444.
  1. Go to http://localhost:4444/ in your browser to see the RC status, you should see Paint and Notepad preconfigured as applications.
  1. Go to http://localhost:4444/ide in your browser to load the IDE as an applet, you can open Paint and Notepad sessions and view their element tree.
  1. Now grab the java client package (named something like twin-client-standalone.jar). Open up your favourite IDE and write the following program:
```
import org.ebayopensource.twin.*;
import org.ebayopensource.twin.element.*;

public static void main(String[] args) throws Exception {
    Application app = new Application(new URL("http://localhost:4444/"));
    app.open("notepad", null);
    Window window = app.getWindow();
    window.type("Hello world");
    Thread.sleep(5000);
    app.close();
}
```
  1. Check out the javadoc for the client package to see how to do more things. For some common patterns, we have some [Recipes](Recipes.md).
  1. Edit sharpclaws.xml (in the RC package) to automate other apps.

# The moving parts, part 2 #

![http://twin.googlecode.com/svn/wiki/images/movingparts2.png](http://twin.googlecode.com/svn/wiki/images/movingparts2.png)

Here you can see the components of both the 'client' and 'server' parts of Twin, and the messages passed between them. This example shows how you might test Notepad.

## Client ##

Your automation code is simply a java application (or JUnit test, etc) that uses the APIs provided by twin-client.jar. It might look something like this:
```
Application app = new Application(new URL("http://server:4444/")); // URL of the remote control
app.open("notepad", null); // launch app configured as 'notepad' on remote control, any version
Window window = app.getWindow();
window.type("Hello world!");
window.getScreenshot().save(new File("C:/notepad.png"));
app.close();
```

The diagram shows the app.getWindow() call, which locates the application's main window (waiting up to 30 seconds for it to appear).

## Client/Server protocol ##

All communications between the client and the server are over HTTP, with the messages in JSON format.

The first request is triggered by app.open() and attempts to start a sesssion. The client provides a list of **capabilities** it needs, in this case it is simply:
```
{ "applicationName": "notepad" }
```

The server should start an application matching the capabilities and return a session ID uniquely identifying the app. All subsequent requests from the client include this session ID, so separate sessions don't confuse the server.
The last thing the client does is to close the session, which kills the application.

The intervening requests perform actions and queries on the ElementTree of graphical components, which is rooted at the desktop. `app.getWindow()` causes the client to ask the server for the children of the desktop, which are the top-level windows.

## Server ##

The server is a servlet called "twin-rc" sitting in an HTTP servlet container called "sharpclaws". It maintains a list of named application configurations - these are the applications that are available to be automated. These are configured in the sharpclaws.xml:
```
<param name="app.notepad.name">notepad</param>
<param name="app.notepad.path">C:\windows\notepad.exe</param>
```

In response to session requests from clients, the configured applications can be launched and remote-controlled.
The servlet is written in C# and accesses the applications through the .NET 3.5 [UIAutomation library](http://msdn.microsoft.com/en-us/library/ms747327.aspx). This library is itself implemented using APIs like Win32 and WPF.