This guide walks you through using Maven to build a simple Java project.

What you'll build
-----------------

You'll create an application that provides the time of day and then build it with Maven.

What you'll need
----------------

 - About 15 minutes
 - A favorite text editor or IDE
 - [JDK 6][jdk] or later

[jdk]: http://www.oracle.com/technetwork/java/javase/downloads/index.html

How to complete this guide
--------------------------

Like all Spring's [Getting Started guides](/guides/gs), you can start from scratch and complete each step, or you can bypass basic setup steps that are already familiar to you. Either way, you end up with working code.

To **start from scratch**, move on to [Set up the project](#scratch).

To **skip the basics**, do the following:

 - [Download][zip] and unzip the source repository for this guide, or clone it using [git](/understanding/git):
`git clone https://github.com/springframework-meta/gs-maven.git`
 - cd into `gs-maven/initial`.
 - Jump ahead to [Install Maven](#initial).

**When you're finished**, you can check your results against the code in `gs-maven/complete`.
[zip]: https://github.com/springframework-meta/gs-maven/archive/master.zip

<a name="scratch"></a>
Set up the project
------------------

First you'll need to setup a Java project for Maven to build. To keep the focus on Maven, make the project as simple as possible for now.

### Create the directory structure

In a project directory of your choosing, create the following subdirectory structure; for example, with `mkdir -p src/main/java/hello` on *nix systems:

    └── src
        └── main
            └── java
                └── hello

### Create Java classes

Within the `src/main/java/hello` directory, you can create any Java classes you want. To maintain consistency with the rest of this guide, create these two classes: `HelloWorld.java` and `Greeter.java`.

`src/main/java/hello/HelloWorld.java`
```java
package hello;

import org.joda.time.LocalTime;

public class HelloWorld {
  public static void main(String[] args) {
    LocalTime currentTime = new LocalTime();
    System.out.println("The current local time is: " + currentTime);

    Greeter greeter = new Greeter();
    System.out.println(greeter.sayHello());
  }
}
```

`src/main/java/hello/Greeter.java`
```java
package hello;

public class Greeter {
  public String sayHello() {
    return "Hello world!";
  }
}
```


<a name="initial"></a>
### Install Maven

Now that you have a project that is ready to be built with Maven, the next step is to install Maven.

Maven is downloadable as a zip file at http://maven.apache.org/download.cgi. Only the binaries are required, so look for the link to apache-maven-_{version}_-bin.zip or apache-maven-_{version}_-bin.tar.gz.

Once you have downloaded the zip file, unzip it to your computer. Then add the _bin_ folder to your path.

To test the Maven installation, run `mvn` from the command-line:

```sh
$ mvn -v
```

If all goes well, you should be presented with some information about the Maven installation. It will look similar to (although perhaps slightly different from) the following:

```sh
Apache Maven 3.0.5 (r01de14724cdef164cd33c7c8c2fe155faf9602da; 2013-02-19 07:51:28-0600)
Maven home: /usr/share/maven
Java version: 1.7.0_09, vendor: Oracle Corporation
Java home: /Library/Java/JavaVirtualMachines/jdk1.7.0_09.jdk/Contents/Home/jre
Default locale: en_US, platform encoding: UTF-8
OS name: "mac os x", version: "10.8.3", arch: "x86_64", family: "mac"
```

Congratulations! You now have Maven installed.

Define a simple Maven build
-----------------------------
Now that Maven is installed, you need to create a Maven project definition. Maven projects are defined with an XML file named _pom.xml_. Among other things, this file gives the project's name, version, and dependencies that it has on external libraries.

Create a file named _pom.xml_ at the root of the project and give it the following contents:

```XML
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<groupId>org.springframework.gs</groupId>
	<artifactId>gs-maven-initial</artifactId>
	<version>0.1.0</version>
	<packaging>jar</packaging>
</project>
```

With the exception of the optional `<packaging>` element, this is the simplest possible _pom.xml_ file necessary to build a Java project. It includes the following details of the project configuration:

* `<modelVersion>`. POM model version (always 4.0.0).
* `<groupId>`. Group or organization that the project belongs to. Often expressed as an inverted domain name.
* `<artifactId>`. Name to be given to the project's library artifact (for example, the name of its JAR or WAR file).
* `<version>`. Version of the project that is being built.
* `<packaging>` - How the project should be packaged. Defaults to "jar" for JAR file packaging. Use "war" for WAR file packaging.

> **Note:** When it comes to choosing a versioning scheme, Spring recommends the [semantic versioning](http://semver.org) approach.

At this point you have a minimal, yet capable Maven project defined.


Build Java code
------------------
Maven is now ready to build the project. You can execute several build lifecycle goals with Maven now, including goals to compile the project's code, create a library package (such as a JAR file), and install the library in the local Maven dependency repository.

To try out the build, issue the following at the command line:

```sh
$ mvn compile
```

This will run Maven, telling it to execute the _compile_ goal. When it's finished, you should find the compiled _.class_ files in the _target/classes_ directory.

Since it's unlikely that you'll want to distribute or work with _.class_ files directly, you'll probably want to run the _package_ goal instead:

```sh
$ mvn package
```

The _package_ goal will compile your Java code, run any tests, and finish by packaging the code up in a JAR file within the _target_ directory. The name of the JAR file will be based on the project's `<artifactId>` and `<version>`. For example, given the minimal _pom.xml_ file from before, the JAR file will be named _gs-maven-initial-0.1.0.jar_.

> __Note__ : If you've changed the value of `<packaging>` from "jar" to "war", the result will be a WAR file within the _target_ directory instead of a JAR file.

Maven also maintains a repository of dependencies on your local machine (usually in a _.m2/repository_ directory in your home directory) for quick access to project dependencies. If you'd like to install your project's JAR file to that local repository, then you should invoke the `install` goal:

```sh
$ mvn install
```

The _install_ goal will compile, test, and package your project's code and then copy it into the local dependency repository, ready for another project to reference it as a dependency.

Speaking of dependencies, now it's time to declare dependencies in the Maven build.

Declaring Dependencies
----------------------
The simple Hello World sample is completely self-contained and does not depend on any additional libraries. Most applications, however, depend on external libraries to handle common and/or complex functionality.

For example, suppose that in addition to saying "Hello World!", you want the application to print the current date and time. While you could use the date and time facilities in the native Java libraries, you can make things more interesting by using the Joda Time libraries.

First, change HelloWorld.java to look like this:

```java
package hello;

import org.joda.time.LocalTime;

public class HelloWorld {
  public static void main(String[] args) {
    LocalTime currentTime = new LocalTime();
    System.out.println("The current local time is: " + currentTime);

    Greeter greeter = new Greeter();
    System.out.println(greeter.sayHello());
  }
}
```

Here `HelloWorld` uses Joda Time's `LocalTime` class to get and print the current time. 

If you were to run `mvn compile` to build the project now, the build would fail because you've not declared Joda Time as a compile dependency in the build. You can fix that by adding the following lines to _pom.xml_ (within the `<project>` element):

```xml
<dependencies>
    <dependency>
        <groupId>joda-time</groupId>
        <artifactId>joda-time</artifactId>
        <version>2.2</version>
    </dependency>	
</dependencies>
```

This block of XML declares a list of dependencies for the project. Specifically, it declares a single dependency for the Joda Time library. Within the `<dependency>` element, the dependency coordinates are defined by three subelements:

* `<groupId>` - The group or organization that the dependency belongs to.
* `<artifactId>` - The library that is required.
* `<version>` - The specific version of the library that is required.

By default, all dependencies are scoped as `compile` dependencies. That is, they should be available at compile-time (and if you were building a WAR file, including in the _/WEB-INF/libs_ folder of the WAR). Additionally, you may specify a `<scope>` element to specify one of the following scopes:

* `provided` - Dependencies that are required for compiling the project code, but that will be provided at runtime by a container running the code (e.g., the Java Servlet API).
* `test` - Dependencies that are used for compiling and running tests, but not required for building or running the project's runtime code.

Now if you run `mvn compile` or `mvn package`, Maven should resolve the Joda Time dependency from the Maven Central repository and the build will be successful.

Here's the completed `pom.xml` file:

`pom.xml`
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>org.springframework.gs</groupId>
    <artifactId>gs-maven</artifactId>
    <packaging>jar</packaging>
    <version>1.0.0</version>
</project>
```

Next Steps
----------
Congratulations! You have now created a very simple, yet effective Maven project definition for building Java projects.
