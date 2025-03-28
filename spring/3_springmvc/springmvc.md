# Spring Framework: MVC e REST - Riassunto

## Spring MVC

Spring MVC è un modulo del framework Spring che consente di sviluppare applicazioni web secondo il pattern Model-View-Controller. Offre numerosi vantaggi:

- Separazione chiara dei ruoli tra i diversi layer dell'applicazione
- Configurazione e gestione uniforme del framework e delle classi di modello
- Flessibilità e riusabilità delle logiche di business
- Gestione automatica del ciclo di vita degli oggetti

### Caratteristiche principali

- Un progetto Web MVC utilizza la dipendenza `spring-boot-starter-web`, che include:
  - `spring-boot` (che include `spring-core` e `spring-context` per l'iniezione delle dipendenze)
  - `spring-webmvc` (il framework Spring Web MVC)
  - `tomcat-embed-core` (per eseguire l'applicazione in un server Tomcat embedded)

- La struttura è basata sul pattern MVC:
  - **Model**: rappresentato da una mappa `Map<String, Object>` con coppie chiave-valore
  - **View**: template di pagine web che determinano come visualizzare i dati
  - **Controller**: gestisce le richieste HTTP, elabora i dati e restituisce la view appropriata

### Flusso di richiesta

1. L'utente invia una richiesta tramite browser
2. La richiesta viene ricevuta dal `DispatcherServlet` (Front Controller)
3. Il `DispatcherServlet` consulta l'`HandlerMapping` per identificare il controller appropriato
4. Il controller elabora la richiesta e popola il modello con i dati
5. Il controller restituisce un oggetto `ModelAndView` che include il nome della view e i dati
6. Il `DispatcherServlet` usa il `ViewResolver` per trovare la view corrispondente
7. La view genera l'output HTML utilizzando i dati del modello
8. La risposta viene restituita al client

### Esempi di annotazioni

- `@Controller`: Identifica una classe come controller
- `@GetMapping("/path")`: Mappa un metodo a una richiesta HTTP GET
- `@PostMapping("/path")`: Mappa un metodo a una richiesta HTTP POST
- `@RequestMapping`: Può essere usata a livello di classe per definire un path base
- `@RequestParam`: Estrae parametri dalla query string
- `@PathVariable`: Estrae variabili dal path della URL

**Esempio di controller:**

```java
@Controller
@RequestMapping("/api")
public class TestController {
    
    @GetMapping("/testGet")
    public String testGET() {
        return "Calling GET /testGet";
    }
    
    @PostMapping("/testPost")
    public String testPOST() {
        return "Calling POST /testPost";
    }
}
```

## REST (REpresentational State Transfer)

REST è uno stile architetturale che definisce linee guida per la trasmissione dei dati in sistemi distribuiti.

### Principi di REST

1. **Client-Server**: Separazione tra client e server
2. **Stateless**: Ogni richiesta è indipendente, senza informazioni di sessione
3. **Caching**: Le risposte possono essere memorizzate nella cache
4. **Identificazione delle risorse**: Ogni risorsa ha un identificativo univoco
5. **Rappresentazione delle risorse**: Le risorse possono essere rappresentate in vari formati (JSON, XML, ecc.)
6. **Sistema multilivello**: L'architettura è organizzata in livelli indipendenti

### Operazioni CRUD e metodi HTTP

- **POST**: Crea una risorsa (Create)
- **GET**: Recupera una risorsa (Read)
- **PUT**: Aggiorna una risorsa (Update)
- **DELETE**: Cancella una risorsa (Delete)

### Codici di stato HTTP

- **2xx**: Successo (es. 200 OK, 201 Created)
- **3xx**: Redirection (es. 304 Not Modified)
- **4xx**: Errore del client (es. 400 Bad Request, 404 Not Found)
- **5xx**: Errore del server (es. 500 Internal Server Error)

## Spring REST

Spring semplifica l'implementazione di servizi RESTful. Ecco alcuni elementi chiave:

- Utilizzo di `@RestController` anziché `@Controller` (include implicitamente `@ResponseBody`)
- I controller REST restituiscono direttamente oggetti che vengono convertiti in JSON
- Supporto per formati di richiesta/risposta multipli

### Pattern architetturale

Un pattern comune per Web Service REST in Spring Boot prevede 4 strati:
- **Controller**: Gestisce le richieste HTTP
- **Service**: Contiene la logica di business
- **Repository**: Interagisce con il database
- **Model**: Rappresenta i dati

**Esempio di controller REST:**

```java
@RestController
@RequestMapping("/api")
public class UserController {
    @Autowired
    UserService userService;
    
    @GetMapping("/users/current")
    public ResponseEntity<User> getCurrentUser() {
        User currentUser = userService.getCurrentUser();
        return new ResponseEntity<>(currentUser, HttpStatus.OK);
    }
}
```

## Thymeleaf

Thymeleaf è un template engine che consente di creare viste in formato HTML. È ben integrato con Spring MVC.

### Funzionalità principali

- Utilizza HTML standard con attributi speciali del namespace `th:`
- I template sono visualizzabili anche senza rendering del server
- Supporta espressioni per accedere agli oggetti del modello

### Passaggio dati dal controller alla view

Ci sono diverse opzioni:
- `Map<String, Object>`
- Interfaccia `Model`
- Classe `ModelMap`
- Classe `ModelAndView`

### Esempi di attributi Thymeleaf

- `th:text="${variable}"`: Inserisce il valore di una variabile
- `th:each="item: ${itemList}"`: Itera su una collezione
- `th:if="${condition}"`: Rendering condizionale
- `th:action="@{/path}"`: URL per form
- `th:field="*{fieldName}"`: Binding dei campi del form

**Esempio di form con Thymeleaf:**

```html
<form th:action="@{/addAlunno}" th:object="${alunno}" method="post">
    <p>
        Nome: <input type="text" th:field="*{nome}" />
    </p>
    <p>
        Id: <input type="text" th:field="*{id}" />
    </p>
    <p>
        Email: <input type="text" th:field="*{email}" />
    </p>
    <p>
        <input type="submit" value="Submit" />
        <input type="reset" value="Reset" />
    </p>
</form>
```

### Validazione dei form

Spring supporta la Java Validation API con annotazioni come:
- `@NotNull`
- `@Max`
- `@Min`
- `@Size`

## Esempi pratici

### Controller che restituisce una vista Thymeleaf

```java
@Controller
public class HelloController {
    @GetMapping("/helloThymeleaf/{name}")
    public String hello(Model model, @PathVariable String name) {
        model.addAttribute("name", name);
        return "helloPage";
    }
}
```

### Template Thymeleaf

```html
<!DOCTYPE HTML>
<html xmlns:th="https://www.thymeleaf.org">
<head>
    <title>Hello</title>
</head>
<body>
    <h1>Hello <span th:text="${name}"></span>, welcome!</h1>
</body>
</html>
```

### Controller REST

```java
@RestController
@RequestMapping("/api")
public class TestController {
    @GetMapping("/testGet")
    public String testGET() {
        return "Calling GET /testGet";
    }
    
    @PostMapping("/testBodyRequest")
    public String testBodyRequest(@RequestBody String body) {
        return "Parametri: " + body;
    }
}
```

### Utilizzo di ResponseEntity per controllo completo

```java
@GetMapping("/testResponseEntity")
public ResponseEntity<String> getResponseEntity() {
    HttpHeaders headers = new HttpHeaders();
    headers.add("customHeader", "headervalue");
    String body = "Test della responseEntity";
    
    ResponseEntity<String> responseEntity = 
        new ResponseEntity<>(body, headers, HttpStatus.OK);
    
    return responseEntity;
}
```

Questo riassunto copre i concetti fondamentali di Spring MVC, REST e Thymeleaf, fornendo una base solida per lo sviluppo di applicazioni web con il framework Spring.