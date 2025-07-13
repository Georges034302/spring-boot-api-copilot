# 🚀 spring-boot-api-copilot
This repository demonstrates secure API development with Spring Boot, GitHub Copilot–assisted scaffolding, and full CI/CD workflows to Azure App Service. It includes unit testing with MockMVC and Faker, modular architecture, and GitHub Security best practices.

## 🧱 Overview

| Component | Description |
|----------|-------------|
| App      | Spring Boot REST API (`PlayerApp`) |
| Data     | `Player` class with mock data (ID, name, score 0–10) |
| API      | `GET /api/player/{id}`, `GET /api/players` |
| Test     | JUnit 5 tests with **Faker**  + **MockMVC**|
| CI       | GitHub Actions for **build + test** |
| CD       | GitHub Actions to **deploy to Azure App Service** |
| Tools    | VS Code or IntelliJ with GitHub Copilot |

---

## ✅ Step 1: Create Spring Boot Project

### ⛏️ Option A: Use Spring Initializr
>*Prompt:\
> Create a Spring Boot project using curl and Spring Initializr with the following:**
> - Java 21  
> - Maven project  
> - Dependency: Spring Web  
> - Project name and artifactId: `PlayerApp`  
> - Save and unzip into repo main directory, then delete the ZIP file.

```bash
mkdir PlayerApp
cd PlayerApp && curl https://start.spring.io/starter.zip \
  -d dependencies=web \
  -d name=PlayerApp \
  -d artifactId=PlayerApp \
  -d type=maven-project \
  -d language=java \
  -d javaVersion=21 \
  -o PlayerApp.zip
unzip PlayerApp.zip
rm PlayerApp.zip 
```

Run the app:
```bash
./mvnw spring-boot:run
```
---
## 📁 Step 2: Project Structure with Copilot

> *Prompt: Create this Spring Boot structure under `com.example.PlayerApp`:*

```
src/main/java/com/example/PlayerApp/
│
├── model/
│   └── Player.java
│
├── repository/
│   └── PlayerRepository.java
│
├── service/
│   └── PlayerService.java
│
├── controller/
│   └── PlayerController.java
│
└── PlayerAppApplication.java
```

---

## 🛠️ Step 3: Develop Model, Repository, and Service (with Copilot)

### 📄 Player.java (Model)
> *Prompt:
>  Create a Player class in PlayerApp/model with:  
>  id: String (3-digit, "000"–"999")  
>  name: String ("Player-<random 0-100>")  
>  score: int (random 0–100)  
>  Include a no-argument constructor that generates random values for all fields\
>  Include a parameterized constructor for setting all fields manually\
>  getters and setters for all fields.*

#### ✅ Expected Outcome:
```java
public class Player {
    private String id; 
    private String name; 
    private int score; 
    // constructors, getters, setters
}
```

---

### 📄 PlayerRepository.java
> *Prompt:  
> Create a repository interface for Player in PlayerApp/repository with:  
> getById(String id): returns a Player  
> getAll(): returns a List<Player>  
> Use Player from the model package.*

#### ✅ Outcome Classes:
```java
public interface PlayerRepository {
    Player getById(String id);
    List<Player> getAll();
}
```

---

### 📄 PlayerService.java
> *Prompt:  
> Create a PlayerService class in PlayerApp/service that uses PlayerRepository.  
> Add methods to get a Player by id and to get all players.  
> Implement logic to call the repository methods.  
> Use Player and PlayerRepository from their respective packages.*

#### ✅ Expected Outcome:
```java
public class PlayerService {
    ...
}
```

---

### 📄 PlayerController.java
> *Prompt:  
> Create a PlayerController class in PlayerApp/controller.  
> Annotate with @RestController and map to "/api".  
> Inject PlayerService via constructor.  
> Add endpoint GET /player/{id} → returns a Player as ResponseEntity, or 404 if not found.  
> Add endpoint GET /players → returns all players as a list.*

#### ✅ Expected Outcome:
```java
@RestController
@RequestMapping("/api")
public class PlayerController {

    private final PlayerService service;

    @GetMapping("/player/{id}")
    public ResponseEntity<Player> getPlayer(@PathVariable String id) { ... }

    @GetMapping("/players")
    public List<Player> getAllPlayers() { ... }
}
```

---

### 📄 MockPlayerRepository
> *Prompt:  
> Create MockPlayerRepository.java class in  
> PlayerApp/src/main/java/com/example/PlayerApp/repository/  
> Annotate with @Repository.  
> Use a private final List<Player> called players.  
> In the constructor, add 5 new Player() objects to the list using a loop.  
> Implement getById(String id): return the Player with the matching id, or null if not found.  
> Implement getAll(): return the list of players.*

#### ✅ Expected Outcome:
```java
@Repository
public class MockPlayerRepository implements PlayerRepository {
    private final List<Player> players = new ArrayList<>();

    public MockPlayerRepository() { ... }

    @Override
    public Player getById(String id) { ... }

    @Override
    public List<Player> getAll() { ... }
}

```

---

## ✅ Step 4: Test API Locally
```bash
./mvnw spring-boot:run
```
Visit:
- http://\<hostname\>/api/players
- http://\<hostname\>/api/player/\<id\>

---

##  📝 Step 5: Add Unit Test Dependencies (Faker + MockMVC) 

### Add Faker dependency to pom.xml
> *Prompt:  
> Add the following dependency `javafaker` to the pom.xml file to use JavaFaker in tests only:  

```xml
<dependency>
  <groupId>com.github.javafaker</groupId>
  <artifactId>javafaker</artifactId>
  <version>1.0.2</version>
  <scope>test</scope>
</dependency>
```

### Add MockMVC dependency to pom.xml
> *Prompt:  
> Add the necessary dependency for MockMvc to the pom.xml file so that MockMvc can be used in test classes.*
 
```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-test</artifactId>
  <scope>test</scope>
</dependency>
```

---

## 🧪 Step 6: Create Unit test with MockMVC + Fakerusing Copilot:


### 📄 PlayerServiceTest.java and PlayerControllerTest.java
> *Prompt:  
> Create the following Unit test classes in PlayerApp/src/test/java/com/example/PlayerApp/: 
> PlayerServiceTest.java
> PlayerControllerTest.java
> PlayerServiceTest.java: test PlayerService methods (e.g., getAll, getById), using custom Player objects.  
> PlayerControllerTest.java: test PlayerController endpoints (GET /players, GET /player/{id}) using MockMvc.  
> Include tests for both Player constructors (custom and no-argument)
> getAll returns non-empty list  
> getPlayer returns correct player  
> getPlayer returns null for unknown ID  
> add player with Faker name and score.*  

#### ✅ Expected Outcome:
```java
@WebMvcTest(controllers = com.example.PlayerApp.controller.PlayerController.class)
class PlayerControllerTest {

 ...

    @Test
    void getAllReturnsNonEmptyList() throws Exception { ... }

    @Test
    void getPlayerReturnsCorrectPlayer() throws Exception { ... }

    @Test
    void getPlayerReturnsNotFoundForUnknownId() throws Exception { ... }

    @Test
    void addPlayerWithFakerNameAndScore() { ... }

    @Test
    void customModelConstructorWithFaker() { ... }

}
```
```java
class PlayerServiceTest {

    private PlayerRepository repository;
    private PlayerService service;
    private Faker faker;

    @BeforeEach
    void setUp() { ... }

    @Test
    void getAllReturnsNonEmptyList() { ... }

    @Test
    void getPlayerReturnsCorrectPlayer() { ... }

    @Test
    void getPlayerReturnsNullForUnknownId() { ... }

    @Test
    void addPlayerWithFakerNameAndScore() { ... }

    @Test
    void customModelConstructorWithFaker() { ... }

}
```

---

## ✅ Step 7: Run Unit Tests Locally
```bash
./mvnw test
```

---

## 🤖 Step 8: Generate ci.yml (with Copilot)
> Prompt:
> Provide CLi to create `.github/workflows/ci.yml` in github root repo
> Provide GitHub Actions YAML workflow `ci.yml` with the following specifications:
> Trigger on push events to the `main` branch
> Run on the latest Ubuntu environment
> Checkout the repository code using `actions/checkout@v3`
> Setup Java JDK 21 using the Eclipse Temurin distribution with `actions/setup-java@v3`
> Format the output as a valid YAML file.

#### ✅ Expected Outcome:

```bash
cd /workspaces/ghcp-course
mkdir -p .github/workflows
touch .github/workflows/ci.yml
```

```yaml

name: Java CI

on:
  push:
    branches: [ "main" ]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v3

      - name: Set up JDK 21
        uses: actions/setup-java@v3
        with:
          java-version: '21'
          distribution: 'temurin'

      - name: Build and test with Maven
        run: mvn
```

---

## 🚢 Step 9: Generate cd.yml for Azure Deployment

> Prompt:\
> Provide CLi to create `.github/workflows/cd.yml` in github root repo\
> Provide GitHub Actions YAML workflow `cd.yml` with the following specifications:\
> triggers on push events to the `main` branch\
> Run on the latest Ubuntu environment\
> Login to Azure using `azure/login@v1` with the secret `AZURE_CREDENTIALS`\
> Checkout the repository using `actions/checkout@v3`\
> Setup Java JDK 21 with Eclipse Temurin using `actions/setup-java@v3`\
> Build the project with Maven using `mvn clean package`\
> Deploy the built JAR file located in `target/*.jar` to Azure Web App using `azure/webapps-deploy@v2`\
> Use the secret `AZURE_WEBAPP_PUBLISH_PROFILE` for deployment authentication\
> Use the secret `APP_NAME` to specify the `app-name` parameter for deployment\
> Format the output as a valid YAML file


#### ✅ Expected Outcome:

```bash
cd /workspaces/ghcp-course
mkdir -p .github/workflows
touch .github/workflows/cd.yml
```

```yaml
name: Deploy to Azure

on:
  push:
    branches: [ "main" ]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v3

      - name: Set up Java
        uses: actions/setup-java@v3
        with:
          java-version: '21'
          distribution: 'temurin'

      - name: Build with Maven
        run: mvn clean package
        working-directory: PlayerApp

      - name: Azure Login
        uses: azure/login@v1
        with:
          creds: ${{ secrets.AZURE_CREDENTIALS }}

      - name: Deploy to Azure Web App
        uses: azure/webapps-deploy@v2
        with:
          app-name: ${{ secrets.APP_NAME }}
          publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
          package: PlayerApp/target/*.jar
```

---

## ✅ Step 10: Test Deployed API
> *Prompt: \
> Given APP_NAME = java-sb-api-app-17d309 \
> Provide URL to get all players \
> Provide URL to get player by id = < >*

#### ✅ Expected Outcome:
- https://<APP_NAME>.azurewebsites.net/api/players
- https://<APP_NAME>.azurewebsites.net/api/player/1

---

## ✅ Summary

| Task                       | Tool / Prompt                      |
|----------------------------|------------------------------------|
| Build Spring App           | `start.spring.io` + Copilot        |
| Create Model + Controller  | Copilot inline prompts             |
| Write Tests                | Copilot + Faker + MockMVC          |
| CI                         | GitHub Actions YAML via Copilot    |
| CD to Azure                | YAML + Azure publish profile       |
| Test via Browser/Postman   | API + Copilot-generated data       |


---

#### 🧑‍🏫 Author: Georges Bou Ghantous
<sub><i>This repository demonstrates secure Spring Boot API development using GitHub Copilot, CI/CD with GitHub Actions, and deployment to Azure App Service— integrating testing, documentation, and GitHub Security tools for DevOps education.</i></sub>