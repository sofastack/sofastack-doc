## How to compile
> Install JDK7 and above, and Maven 3.2.5 and above.

Directly download the [code](https://github.com/sofastack/jarslink) and then execute the following command:

```text
cd sofa-jarslink
mvn clean install
```

**Note: you cannot compile the code under a sub-directory (i.e., sub-module). Since there are many modules, the configuration is restricted to the root directory only to avoid repetitive configuration of some packaging plugins such as the formatting plugin and License plugin. There will be an error message if you execute the packaging command in a sub-module.**
