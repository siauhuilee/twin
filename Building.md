Twin uses [Maven](http://maven.apache.org/) as a build system. It consists of a hierarchy of Maven projects, and when you're set up correctly a single `mvn package` will build them all.

This page will tell you what you need to get to that point.

# Components #

The most important subprojects are:

  * **twin/client/java** - the Java client library for accessing Twin
  * **twin/ide** - the graphical tool for exploring Twin. This uses the Java client library.
  * **twin/rc** - the Twin Remote Control (server). The RC includes a copy of the IDE, so that you can use it as an applet in your browser.

# Requirements #

To build any code, you will need:
  * [Maven 2.2.1](http://maven.apache.org/download.html). (Maven 3 may work, I'm not sure)

The Java client and the IDE are written in Java 6, so you will need:
  * [Java SE 6 JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html)

The RC (and its other supporting components) are written in .NET and use Windows APIs, so you will need:
  * Windows XP or later
  * [.NET Framework 3.5 SP1](http://www.microsoft.com/downloads/en/details.aspx?FamilyID=ab99342f-5d1a-413d-8319-81da479ab0d7&displaylang=en). (If you have Visual Studio you may not need this)
  * [Windows SDK for .NET Framework 3.5 SP1](http://www.microsoft.com/downloads/en/details.aspx?FamilyID=c17ba869-9671-4330-a63e-1fd44e0e2505&displaylang=en)

# Building #

  1. Check out the code
  1. From the checked-out directory, run `mvn install` (not `mvn package`, because of [this bug](http://jira.codehaus.org/browse/MDEP-291)).
  1. open the twin.sln solution with visual studio
  1. build the solution.The output is in twin\twin\rc\target\Debug ( you need a sharpclaws.xml specified in the same location to start testing an application )

# Troubleshooting #

Here are the most common problems building Twin:

_None yet. Please add a comment if you run into issues!_