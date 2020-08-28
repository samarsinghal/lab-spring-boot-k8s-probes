To get started, change into the `demo` sub directory.

```execute
cd ~/exercises/demo
```

This holds the source code for the application we will be working with.

To see the list of files included with the application run:

```execute
tree .
```

To enable the actuator module for the Java application, we need to edit the `pom.xml` file which contains the project and configuration details used by Maven to build the project.

Open up the the `pom.xml` file in the editor.

```editor:open-file
file: ~/exercises/demo/pom.xml
line: 21
```

The actuator module then needs to be added to the list of dependencies for the application.

```editor:insert-lines-before-line
file: exercises/demo/pom.xml
line: 21
text: |+2
          <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
          </dependency>

...
```
