Hi 👋,

Here I send you a **fixed** version that now should work with `mvn javafx:run` in any maven environment. Below is a short explanation of the changes I made, why they were necessary, and how they help you get comfortable with Maven for the rest of the course.

---

### 1️⃣ Added a **property for the main class**

`<main-game-class>com.codegym.games.racer.RacerGame</main-game-class>`

- **Why?**  
  The JavaFX plugin (and the assembly plugin later) need a single place to read the fully‑qualified name of the class that contains `public static void main(String[])`. By putting it in a property we can reuse it everywhere without typing the long name repeatedly.

- **What you gain:**  
  If you ever rename the class, you only have to change it in one spot.

---

### 2️⃣ Cleaned up the **dependencies**

| Original                             | Fixed                                          |
| ------------------------------------ | ---------------------------------------------- |
| Only `javafx-controls` was declared. | Added `javafx-fxml` **and** `javafx-graphics`. |
|                                      |                                                |

- **Why?**  
  The game uses FXML files and graphics APIs, so the extra JavaFX modules are required at runtime. Without them the application crashes with “module not found” errors.

- **What you gain:**  
  All the libraries the project actually needs are declared explicitly, making the build reproducible on any machine.

---

### 3️⃣ Removed **unused / incorrect plugins**

| Removed / Fixed                                                                         | Reason                                                                                                                   |
| --------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------ |
| `maven-install-plugin` (version 3.1.1)                                                  | Not needed for a normal build; Maven installs the artifact automatically.                                                |
| `org.apache.maven.surefire:common-java5`                                                | This artifact does not exist (wrong groupId/artifactId) and caused Maven warnings.                                       |
| `maven-jar-plugin` with `<rsrcMainClass>`                                               | The `rsrcMainClass` element is not part of the standard JAR plugin schema, so Maven ignored it.                          |
| `maven-dependency-plugin` that copied libs to `target/lib` during the **package** phase | The assembly plugin already bundles all dependencies, so the manual copy step was redundant and made the manifest messy. |

- **Why?**  
  Keeping only the plugins you actually need reduces confusion and prevents malformed manifests (the source of the `ClassNotFoundException` you saw).

- **What you gain:**  
  A much simpler `pom.xml` that is easier to read and maintain.

---

### 4️⃣ Introduced the **Maven Assembly Plugin**

- **Why?**  
  The assignment asks you to be able to run the program with one command. The assembly plugin creates a **“fat” JAR** that contains **all** the required libraries inside a single file, so the JVM can find everything without needing a special bootstrap loader.

- **What you gain:**  
  After `mvn clean package` you will have a file like  
  `target/project-maven-1.0-jar-with-dependencies.jar` that you can start with.

---

### 5️⃣ Updated the **JavaFX Maven Plugin** configuration

`<plugin>     <groupId>org.openjfx</groupId>    <artifactId>javafx-maven-plugin</artifactId>    <version>0.0.8</version>    <configuration>        <mainClass>${main-game-class}</mainClass>        <options>            <option>--add-modules</option>            <option>javafx.controls,javafx.fxml</option>        </options>    </configuration> </plugin>`

- **What you gain:**  
  `mvn javafx:run` now starts the game directly, which is perfect for quick development cycles.

---

### 6️⃣ Cleaned up the **Surefire (JUnit) plugin**

- **Why?**  
  The original configuration used a non‑existent Surefire artifact. The official `maven-surefire-plugin` is the correct one, and we keep the same exclusion rule you wanted (skip the intentionally broken test class).

- **What you gain:**  
  Your unit tests run normally, and the noisy `StrangeTest` class is ignored as intended.


