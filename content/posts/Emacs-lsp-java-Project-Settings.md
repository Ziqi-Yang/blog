+++
title = "Emacs lsp-java Project Settings"
author = ["Zarkli Leonardo"]
description = "Create a java project with third libriaries support"
date = 2022-12-21T00:00:00+08:00
tags = ["emacs", "doom-emacs"]
categories = ["emacs"]
draft = false
+++

`lsp-java` is an awesome package for `lsp-mode` to open a java project. However, after some testing, I found it
has difficulty in importing third party libraries with <span class="underline">pure</span> java project(not use maven, Gradle). Moreover, it
only has a limited ability to interact with a Gradle project. However, it has great support for `maven` project, which
means we can use maven to import third party java libraries.
This article will focus on import a local library by maven.


## Initialize a project {#initialize-a-project}

```bash
mvn -B archetype:generate -DgroupId=PacMan -DartifactId=PacMan -Darchet
ypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4
```

This command will create a folder named `PacMan=(=artifactId`), where there is folder called `src`, and its structure is like:

```nil
├── src
│   ├── main
│   │   └── java
│   │       └── PacMan(`groudId`)
```

If your `groupId` is "com.pacman.xx", then the directory structure may be like:

```nil
├── src
│   ├── main
│   │   └── java
│   │       └── com
                └── pacman
                    └── xx
```

In our example, you can write code in the `pacman` folder.


## Specify java version: {#specify-java-version}

edit `pom.xml` file in the project root directory

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">

  <!-- ... -->

  <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>17</maven.compiler.source>
    <maven.compiler.target>17</maven.compiler.target>
  </properties>

  <!-- ... -->
</project>
```


## Add third party libriary {#add-third-party-libriary}

```bash
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
  <!-- ... -->

  <dependencies>

    <!-- ... -->

    <dependency>
      <groupId>ucd.comp2011j</groupId>
      <artifactId>game-engine</artifactId>
      <version>1.00</version>
      <scope>system</scope>
      <systemPath>${basedir}/libs/COMP2011J.Game.Engine.jar</systemPath>
    </dependency>
  </dependencies>

  <!-- ... -->
</project>
```

Note that `${basedir}` stands for the directory that `pom.xml` located.


## Run Project: {#run-project}

create a `Makefile` in the project root directory:

```makefile
all: compile

compile:
	mvn package
	java -cp "target/PacMan-1.0-SNAPSHOT.jar:<your third party libriary relative path>" PacMan.GameStart # <Package Name>.<Class Name>

clean:
	mvn clean
```

Use command `make` to run, and `make clean` to clean

In `doom emacs`, you can type `<SPC>pR` and enter `make` to run the project
