Here are some things you might like to do with Twin. They're arranged (approximately) from most common to most obscure.

# Write "hello world" #
  1. Extract the twin RC package
  1. Run sharpclaws.exe
  1. Create a new Java project
  1. Add the twin-client library to your project
  1. `import org.ebayopensource.webdriver.*;`
  1. Write the following main method:
```
Application app = new Application(new URL("http://localhost:4444"));
app.open("notepad", null);
try {
    Window mainWindow = app.getWindow();
    mainWindow.type("Hello world!");
} finally {
    app.close();
}
```

# Add a new application to automate #
  1. In your RC directory, open **sharpclaws.xml** in a text editor.
  1. Add the name and path of your application
```
<param name="app.calc.name">Windows Calculator</param>
<param name="app.calc.path">C:\Windows\system32\calc.exe</param>
```
  1. Restart sharpclaws.exe
  1. Refer to the application by its name: **Windows Calculator**, from your tests.

# Locate elements inside your application #

  1. Get an overview of your element tree using UiSpy (Windows SDK tools) or the TwinIDE.
![http://twin.googlecode.com/svn/wiki/images/uispy.png](http://twin.googlecode.com/svn/wiki/images/uispy.png)
  1. Choose your search root (desktop, main window, another element)
```
Window root = app.getWindow();
```
  1. `import static org.ebayopensource.twin.Criteria.*`
  1. Use the get descendants/children methods with appropriate [Criteria](Criteria.md).
```
Window dialog = root.getChild(type(Window.class))
Button button = dialog.getDescendant(type(Button.class).and(id("2"))); 
```

# Interact with elements #

  1. Some actions are valid for all elements
```
Element myElement;
myElement.click();
Dimension d = myElement.getSize();
myElement.type("Text\n");
myElement.sendKeys("{ESC}");
```

  1. Some actions are valid for particular [ControlType](ControlType.md)s
```
Window myWindow = (Window)myElement;
myWindow.setSize(100, 200);
myWindow.close();
```

  1. Some actions are valid for particular [ControlPattern](ControlPattern.md)s
```
if(myElement.hasControlPattern(Expandable.class))
    ((Expandable)myElement).setExpanded(true);
((Toggle)myElement).toggle();
```

# Take a screenshot #

  1. Define the element to capture
```
Element myElement;
```
  1. Capture the screenshot
```
Screenshot screenshot1 = myElement.getScreenshot();  
```
  1. Do something with it
```
// Save to disk
File target = screenshot1.saveIn(new File("C:/"));
System.out.println("Saved as "+target); // e.g. C:/1234-12345678.png
```

# Run tests using TestNG #
  1. Create an annotation that will configure your test methods
```
public @interface TwinTest {
    String application();
    String version() default null;
}
```
  1. Create a class implementing IInvokedMethodListener
```
public class TwinListener implements IInvokedMethodListener { }
```
  1. Add a thread-local static field to hold the current Application
```
public static ThreadLocal<Application> currentApp = new ThreadLocal<Application>();
```
  1. In **beforeInvocation**, create the Application object and store it in **app**
```
public void beforeInvocation(IInvokedMethod meth, ITestResult result) {
    TwinTest ann = meth.getTestMethod().getMethod().getAnnotation(TwinTest.class);
    if(ann != null) {
        Application app = new Application(new URL("http://localhost:4444"));
        app.open(ann.application(), ann.version());
        currentApp.set(app);
    }
}
```
  1. In **afterInvocation**, close the Application object
```
public void afterInvocation(IInvokedMethod method, ITestResult testResult) {
    Application app = currentApp.get();
    currentApp.set(null);
    if(app != null)
        app.close();
}
```
  1. Add the listener to your test class
```
@Listeners({TwinTest.class})
public class TestNotepad { ... }
```
  1. Annotate your tests
```
@Test @TwinTest("notepad", null) 
public void test() { ... } 
```
  1. Within your tests, access the application through the listener
```
Application app = TwinListener.currentApp.get();
```

# Open a file in the target application #
  1. Open the 'File Open' dialog, locate the filename field
```
app.getWindow().menu("File").openMenu().item("Open...").click();
Window dialog = app.getWindow().getChild(Criteria.type(Window.class));
Edit filenameField = dialog.getDescendant(Criteria.type(Edit.class));
```
  1. Upload the file to the RC
```
Attachment att = app.upload(new File("C:/local/file.txt"));
String remotePath = att.getFile(); // e.g. "C:/temp/12345.txt"
```
  1. Refer to it by its remote path
```
filenameField.type(remotePath);
dialog.button("Open").click();
```

# Attach to a long-running application #
  1. Configure the application in sharpclaws.xml as normal
  1. Set the 'launch-strategy' capability to 'attach'
```
<param name="app.myapp.launch-strategy">attach</param>
```

# Change configuration files before the app is launched #
  1. Enable file-based session setup for your app, by editing sharpclaws.xml
```
<param name="app.myapp.sessionSetup.files">true</param>
```
  1. After you create your Application object but before you call **open()**, add a session setup parameter
```
Application app = new Application("http://localhost:4444/");
HashMap<String,Object> myFile = new HashMap<String,Object>();
myFile.put("path", "C:\\file.txt"); // remote file to overwrite
String contents = base64(data); // Base64 encode using Apache commons codec or similar
myFile.put("data", contents);
app.addSessionSetup(Arrays.asList(myFile), true); // require it to be supported
```