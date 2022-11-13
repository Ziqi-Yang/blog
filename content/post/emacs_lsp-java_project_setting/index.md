---
title: Emacs lsp-java Project Settings
description: Create a java project with third libriaries support
slug: emacs-lsp-java-project-setting
date: 2022-11-13 00:00:00+0000
categories:
    - emacs
tags:
    - emacs
---

`lsp-java` is an awesome package for `lsp-mode` to open a java proect. However, after some testing, I found it 
has difficulty in importing third party libriaries with __pure__ java project(not use maven, gradle). Moreover, it 
only has a limited ability to interact with a gradle project. However, it has great support for `maven` project, which
means we can use maven to import third party java libriaries.  
This article will focus on import a local libriary by maven.

## Init a project

```bash
mvn -B archetype:generate -DgroupId=PacMan -DartifactId=PacMan -Darchet
ypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4
```
This command will create a folder named `PacMan`(`artifactId`), where there is folder called `src`, and its structure is like:
```
├── src
│   ├── main
│   │   └── java
│   │       └── PacMan(`groudId`)
```
If your groupId is `com.pacman.xx`, then the directory structure may be like:
```
├── src
│   ├── main
│   │   └── java
│   │       └── com
                └── pacman
                    └── xx
```
In our example, you can write code in the `PacMan` folder.

## specify java versoin:

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

## Add third party libriary

```xml
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
note that `${basedir}` stands for the directory that `pom.xml` located.

## Run Project:
create a `Makefile` in the project root directory:

```Makefile
all: compile

compile:
	mvn package
	java -cp "target/PacMan-1.0-SNAPSHOT.jar:<your third party libriary relative path>" PacMan.GameStart # <Package Name>.<Class Name>

clean:
	mvn clean
```

use command `make` to run, and `make clean` to clean  
In `doom emacs`, you can type `<SPC>pR` and enter `make` to run the project
