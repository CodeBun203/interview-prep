# Tutorial: Java + Spring Boot, and Scala (the JVM)

> Goal: build one small REST service in Java/Spring and learn enough Scala to be honest about it.
> Framing: "Spring Boot is the JVM analog of what I do daily with FastAPI." Claim **"I've built
> with"**, not **"expert"**.

---

## PART 1 — JAVA + SPRING BOOT

### Mental model
Java: compiled, statically typed, runs on the **JVM**, object-oriented. **Spring Boot** is the
dominant backend framework — **dependency injection (DI)**, auto-configuration, embedded server.
Your FastAPI instincts transfer: controller≈router, service≈business logic, repository≈ORM.

### Step 1 — scaffold (use start.spring.io or CLI)
Pick dependencies: *Spring Web*, *Spring Data JPA*, *PostgreSQL Driver*. You get a Maven/Gradle
project. Build/run:
```bash
./mvnw spring-boot:run        # Maven wrapper (or ./gradlew bootRun)
./mvnw test
./mvnw package                # builds a runnable .jar
```

### Step 2 — a REST endpoint (maps 1:1 to your FastAPI mental model)
```java
@RestController
@RequestMapping("/api/parts")
public class PartController {

    private final PartRepository repo;          // constructor injection (DI)
    public PartController(PartRepository repo) { this.repo = repo; }

    @GetMapping("/{id}")
    public Part getOne(@PathVariable Long id) {
        return repo.findById(id)
                   .orElseThrow(() -> new ResponseStatusException(HttpStatus.NOT_FOUND));
    }

    @PostMapping
    public Part create(@Valid @RequestBody Part part) {   // @Valid = Pydantic-style validation
        return repo.save(part);
    }
}
```

### Step 3 — entity + repository (JPA = the ORM, like SQLAlchemy)
```java
@Entity
public class Part {
    @Id @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    @NotBlank private String sku;     // Bean Validation
    private int quantity;
    // getters/setters (or use Lombok @Data)
}

public interface PartRepository extends JpaRepository<Part, Long> {
    List<Part> findBySkuContaining(String sku);   // Spring generates the query from the name!
}
```
Terms: **POJO/Bean, dependency injection, JPA/Hibernate (ORM), entity, repository, `@Autowired`/
constructor injection, application context, annotations.**

### Step 4 — testing (JUnit 5 + Mockito + MockMvc)
```java
@WebMvcTest(PartController.class)
class PartControllerTest {

    @Autowired MockMvc mvc;
    @MockBean PartRepository repo;        // mock the dependency

    @Test
    void returns404WhenMissing() throws Exception {
        when(repo.findById(99L)).thenReturn(Optional.empty());
        mvc.perform(get("/api/parts/99"))
           .andExpect(status().isNotFound());
    }
}
```
- **JUnit 5 (Jupiter)** = the test framework; **`@Test`**, **`assertEquals`**, **`@BeforeEach`**.
- **Mockito** = mocking (`when(...).thenReturn(...)`, `verify(...)`).
- **MockMvc** / **`@SpringBootTest`** = web-layer / full-context integration tests.
- **Testcontainers** = real Postgres in a container for integration tests.
- **AssertJ** = fluent assertions (`assertThat(x).isEqualTo(y)`).

### Common Java libraries to name
| Need | Library |
|---|---|
| Web/backend | **Spring Boot**, Spring MVC, Spring Security |
| ORM | **Hibernate / JPA**, Spring Data |
| Build | **Maven**, **Gradle** |
| Testing | **JUnit 5**, **Mockito**, AssertJ, Testcontainers |
| JSON | **Jackson** |
| Boilerplate | **Lombok** |
| HTTP client | Spring `WebClient`, OkHttp |
| Reactive | Project Reactor / Spring WebFlux (async) |

---

## PART 2 — SCALA

### Mental model
Runs on the JVM, **blends OOP + functional programming (FP)**. Strong static types, immutability by
default (`val`), pattern matching, higher-order functions. The language of **Apache Spark** — that's
your honest hook ("I used Scala/PySpark for batch data work").

### The language in one screen
```scala
val name = "Hayden"          // immutable (prefer this)
var count = 0                // mutable (avoid)

def add(a: Int, b: Int): Int = a + b        // last expression is the return

case class Route(target: String, secret: Option[String])   // immutable data + equality for free

// pattern matching (the signature feature)
def describe(r: Route): String = r.secret match {
  case Some(_) => s"${r.target} (protected)"
  case None    => s"${r.target} (open)"
}

// functional collection ops (map/filter/fold — like Python comprehensions, but core)
val nums = List(1, 2, 3, 4)
val evens = nums.filter(_ % 2 == 0).map(_ * 10)   // List(20, 40)
val total = nums.foldLeft(0)(_ + _)               // 10

// Option instead of null; Try/Either for errors
val parsed: Option[Int] = "42".toIntOption
```
Terms: **immutability, `val` vs `var`, case class, pattern matching, `Option`/`Some`/`None`,
higher-order functions, `map`/`filter`/`fold`, for-comprehension, trait (≈ interface), companion
object, type inference.**

### A tiny service
- **Akka HTTP** or **http4s** (pure FP) for web; **Play** is the full framework.
- But for *your* story, the realistic use is **Spark** (see `spark-mongodb.md`).

### Testing in Scala
```scala
import org.scalatest.funsuite.AnyFunSuite

class RouteTest extends AnyFunSuite {
  test("protected route is described correctly") {
    val r = Route("http://x", Some("secret"))
    assert(describe(r).contains("protected"))
  }
}
```
- **ScalaTest** (most common — `FunSuite`, `FlatSpec`, matchers), **specs2**, **ScalaCheck**
  (property-based testing — generates random inputs to find edge cases).
- Build tool: **sbt** (`sbt test`), or Maven/Gradle.

### Common Scala libraries
| Need | Library |
|---|---|
| Big data | **Apache Spark** (your hook) |
| Web (FP) | http4s, ZIO, Cats Effect |
| Web (actor) | Akka / Pekko HTTP |
| Testing | **ScalaTest**, ScalaCheck, specs2 |
| Build | **sbt** |
| FP toolkit | Cats, Scalaz |

### Interview soundbites (JVM)
- Java/Spring: "Spring Boot maps cleanly to my FastAPI work — controllers, DI, JPA repositories,
  JUnit/Mockito tests. I've built REST services with it; my production backend is Python."
- Scala: "I've used Scala in a functional style — immutability, pattern matching, `Option` over
  null — mainly for Spark batch jobs. I'm comfortable reading and writing it; it's not my daily
  driver." **(Honest, defensible, no overclaim.)**
