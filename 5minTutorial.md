# Setting up the server #

Make sure you have .NET framework installed ( version  3.0 or above ) [.NET framework download](http://www.microsoft.com/net/download.aspx)

Download TWIN remote control : twin-1.0.zip [here](http://code.google.com/p/twin/downloads/list), unzip on the machine when your AUT is installed and run the sharpclaws executable.

If you're using windows 7, you may have to unblock the files first. Right click on each file, properties. In the general tab, click the Unblock button at the bottom.

Check that the server is running : http://yourmachineIP:4444/ ( use the machine IP rather than localhost to be able to use the IDE ).
If the server is running properly, you should see :

![http://twin.googlecode.com/svn/wiki/images/status.png](http://twin.googlecode.com/svn/wiki/images/status.png)

(Twin only recognizes notepad because that's the only application configured by default.
To add more application, update sharpclaws.xml, and add your application name and the path where it's installed.)


# Writing the first test #

Now that the server is running, you can now create your first test. The only langage completed to pilot the TWIN server is java. .NET is coming soon.

Download twin-client-standalone-1.0.jar [here](http://code.google.com/p/twin/downloads/list)
Start a new java project and add the jar to the project classpath.
Create a new class with a main ( updating the URL you pass as a parameter to the application to match the IP of the machine where sharpclaws.exe has been launched ):

```
import java.io.File;
import java.io.IOException;
import java.net.URL;

import org.ebayopensource.twin.Application;
import org.ebayopensource.twin.TwinException;
import org.ebayopensource.twin.element.Window;


public class Demo {

	
	public static void main(String[] args) throws TwinException, IOException {
		Application app = new Application(new URL("http://192.168.39.185:4444")); // URL of the remote control
		app.open("notepad", null); // launch app configured as 'notepad' on remote control, any version
		Window window = app.getWindow();
		window.type("Hello world!");
		window.getScreenshot().save(new File("screenshot.png"));
		app.close();
	}

}
```

Launch it.
You should see notepad starting, and a screenshot will be created in the root folder of you project.