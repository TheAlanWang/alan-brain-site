### JDK
Java Development Kit — build + run toolkit
The JDK is the full Java toolkit for **developing** Java apps.
- Compile: `.java` → `.class` using `javac`
- Run: run `.class` / `.jar` using `java`
- Includes extra tools: `jar`, debuggers, diagnostic tools, etc.

### JRE
Java Runtime Environment — run only
The **JRE** is the Java **runtime** needed to **run** Java applications.
- Includes the **JVM** + core libraries
- Does **not** include developer tools like `javac`
- You use it when you only need to run an app, not build it

### JAR
Java Archive — packaged application output
A **JAR** is a **package file** (like a zip) that bundles compiled Java classes and resources.
- Spring Boot often builds an executable “fat/uber” JAR that includes dependencies.
- Run it with:
```bash
java -jar app.jar
```

### JVM
JVM stands for **Java Virtual Machine**. It’s the runtime engine that **runs Java programs**.
Here’s the typical pipeline:
- You write `Hello.java`
- The Java compiler (`javac`) compiles it into **bytecode**: `Hello.class`
- The **JVM** loads and executes that bytecode on your machine