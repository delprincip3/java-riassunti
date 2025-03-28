# Le API REST in Java: Principi e Implementazione

## Cos'è un Web Service

Un Web Service rappresenta un servizio offerto da un dispositivo elettronico (il server) ad altri dispositivi elettronici (i client) utilizzando il web come mezzo di comunicazione. Sono funzionalità esposte da un server che possono essere invocate da programmi esterni tramite la rete e, dopo l'elaborazione, restituiscono un risultato.

## Architettura REST

REST (Representational State Transfer) è un'architettura per lo sviluppo di servizi web. Il concetto principale di REST è quello di separare le responsabilità dell'interfaccia utente e dei dati, basandosi sulle risorse e sulle operazioni standard HTTP per interagire con tali risorse.

### Caratteristiche Principali di REST

1. **Client-Server**: Il client è responsabile della gestione dell'interfaccia e dell'esperienza utente, mentre il server gestisce i dati e l'elaborazione delle richieste.

2. **Stateless**: Ogni richiesta dal client al server deve contenere tutte le informazioni necessarie per comprendere e completare la richiesta. Il server non dovrebbe memorizzare alcun contesto delle richieste precedenti del client.

3. **Cacheable**: Le risposte del server devono indicare se possono essere memorizzate nella cache dal client, questo aiuta a migliorare le prestazioni e ridurre il carico del server.

4. **Layered System**: Il sistema può essere composto da più livelli, in cui ogni livello svolge una funzione diversa (come la sicurezza, il bilanciamento del carico, ecc.).

### Verbi HTTP in REST

Le risorse sono identificate tramite URI (Uniform Resource Identifier) e i verbi HTTP standard vengono utilizzati per operare su di esse:

- **GET**: Recupera la rappresentazione di una risorsa. Non deve avere effetti collaterali.
- **POST**: Crea una nuova risorsa.
- **PUT**: Aggiorna una risorsa esistente o la crea se non esiste.
- **DELETE**: Elimina una risorsa.

### Esempio di Caso d'Uso

Supponiamo di avere un'applicazione per la gestione degli utenti. Gli utenti possono essere rappresentati come risorse con un identificatore univoco:

- `GET /users`: Recupera la lista di tutti gli utenti
- `GET /users/{id}`: Recupera i dettagli di un singolo utente
- `POST /users`: Crea un nuovo utente
- `PUT /users/{id}`: Aggiorna i dettagli di un utente esistente
- `DELETE /users/{id}`: Elimina un utente

## Codici di Stato HTTP

I codici di stato HTTP sono risposte standard fornite da server HTTP in risposta alle richieste dei client. Indicano lo stato della richiesta, cioè se è stata elaborata correttamente, se si è verificato un errore, o se ci sono ulteriori azioni da intraprendere.

I codici di stato sono composti da tre cifre e sono suddivisi in diverse categorie principali:

- **1xx (Informativi)**: Il server ha ricevuto la richiesta e il processo è in corso
- **2xx (Successo)**: La richiesta è stata ricevuta, compresa e accettata con successo
- **3xx (Redirezioni)**: L'utente deve compiere ulteriori azioni per completare la richiesta
- **4xx (Errori del Client)**: La richiesta contiene una sintassi errata o non può essere soddisfatta
- **5xx (Errori del Server)**: Il server ha fallito nell'elaborare una richiesta valida

## Endpoints

Un endpoint di comunicazione è un punto di connessione dove le pagine HTML, i metodi o le risorse sono esposti al mondo esterno. In questo contesto, un endpoint è un URL attraverso il quale è possibile accedere a un servizio del Web Service. Ogni Web Service può avere più endpoint.

### Composizione di un Endpoint

Un endpoint è una URL univoca su un server web che rappresenta una risorsa con cui un client può interagire:

- **Protocollo**: Protocollo utilizzato per la comunicazione, solitamente http o https
- **Nome del dominio**: L'indirizzo dove risiede l'API, ad esempio api.example.com
- **Path (Percorso)**: Specifica la risorsa, ad esempio /users o /books/1
- **Query Parameters**: Parametri opzionali che vengono passati nell'URL

Esempio: `https://api.example.com/books/1?sort=asc`

### Passaggio di Parametri

I parametri possono essere passati a un endpoint in vari modi:

1. **Parametri di Route (Path Parameters)**: Integrati nel percorso URL, come `/books/1`
2. **Parametri di Query (Query Parameters)**: Aggiunti dopo un punto interrogativo, come `/books?author=John&limit=10`
3. **Parametri nel corpo della richiesta (Body Parameters)**: Inviati nel corpo della richiesta, di solito come JSON
4. **Parametri di intestazione (Header Parameters)**: Inviati nelle intestazioni HTTP

## Best Practices per le API REST

- Utilizzare nomi plurali per rappresentare collezioni (es. `/books/{book_id}`)
- Restituire oggetti JSON piuttosto che array a livello superiore
- Utilizzare stringhe come identificatori negli endpoint delle API anche se il database memorizza valori numerici
- Non usare "404" per indicare "Non Trovato", perché spesso questo comportamento può confondere
- Usare un formato di errore strutturato con campi come messaggio di errore, tipo di errore e causa

## REST API in Java

### JAX-RS

JAX-RS (Java API for RESTful Web Services) è una specifica di Java che facilita lo sviluppo di servizi web RESTful. È parte integrante della piattaforma Java EE e fu introdotta come parte di Java EE 6 nel 2009.

#### Caratteristiche di JAX-RS

- **Annotazioni**: JAX-RS sfrutta ampiamente le annotazioni per semplificare la creazione di servizi RESTful (`@Path`, `@GET`, `@POST`, `@PUT`, `@DELETE`...)
- **Supporto per JSON e XML**: JAX-RS supporta nativamente la serializzazione e deserializzazione di oggetti Java in formati JSON e XML

#### Esempio di JAX-RS

```java
@Path("/users")
public class UserResource {
    
    @GET
    @Produces(MediaType.APPLICATION_JSON)
    public String getUsers() {
        return "[{\"name\": \"John Doe\", \"email\": \"john.doe@example.com\"}]";
    }
}
```

```java
public class Main {
    public static void main(String[] args) {
        ResourceConfig config = new ResourceConfig(UserResource.class);
        SimpleServer server = SimpleServer.create("/api", config);
        System.out.println("Server started at: http://localhost:8080/api");
    }
}
```

#### Svantaggi di JAX-RS

- **Curva di apprendimento**: Richiede una buona comprensione delle annotazioni, dei concetti REST e del ciclo di vita delle applicazioni Java
- **Integrazione**: Difficoltà nel rendere JAX-RS interoperabile con certe tecnologie
- **Configurazioni**: Per applicazioni più complesse può essere necessario un notevole overhead di configurazione
- **Limitata Flessibilità**: È parte di una specifica più ampia di Jakarta EE/Java EE, porta con sé un certo peso in termini di infrastruttura e dipendenze

### Javalin

Javalin è un framework web leggero scritto per Java e Kotlin, costruito sopra il server web Jetty, il che lo rende altamente performante.

#### Caratteristiche di Javalin

- **Semplicità**: Progettato per essere estremamente semplice e intuitivo
- **Ideale per API RESTful**: Fornisce strumenti facili da usare per la gestione delle richieste HTTP
- **Gestione diretta degli endpoint**: La definizione dei percorsi (routes) è semplice e con poche righe di codice
- **Middleware e filtri**: Supporta l'uso di middleware per elaborare richieste e risposte
- **Testing**: Buona integrazione con i framework di testing esistenti
- **Microservizi**: Adatto per la costruzione di microservizi leggeri

#### Esempio Javalin

```java
import io.javalin.Javalin;

public class UserApi {
    public static void main(String[] args) {
        var app = Javalin.create().start(7000);
        
        app.get("/users", ctx -> {
            ctx.json(Map.of("users", List.of(
                Map.of("name", "John", "email", "john@example.com")
            )));
        });
        
        app.post("/users", ctx -> {
            // Parsing del corpo della richiesta
            var user = ctx.bodyAsClass(User.class);
            // Salvataggio dell'utente e restituzione
            ctx.status(201).json(user);
        });
    }
}
```

## Confronto tra JAX-RS e Javalin

| Caratteristica | JAX-RS | Javalin |
|----------------|--------|---------|
| Complessità    | Media-Alta | Bassa |
| Configurazione | Può essere complessa | Semplice e minima |
| Annotazioni    | Basato su annotazioni | Approccio più programmatico |
| Performance    | Buona | Eccellente (framework leggero) |
| Curva di apprendimento | Ripida | Bassa |
| Adatto per     | Applicazioni Enterprise complesse | Microservizi, API semplici, prototipazione rapida |
| Integrazione   | Java EE/Jakarta EE | Indipendente, facile integrazione con altri framework |

## Conclusione

Le API REST rappresentano uno standard ampiamente adottato per la creazione di servizi web interoperabili. In Java, esistono diverse opzioni per implementare queste API, da framework completi come JAX-RS a soluzioni più leggere come Javalin. La scelta dipende dalle esigenze specifiche del progetto, dalla complessità dell'applicazione e dalle preferenze del team di sviluppo.

Le best practices per le API REST, come l'uso corretto dei verbi HTTP, la strutturazione degli endpoint e la gestione appropriata degli errori, rimangono fondamentali indipendentemente dal framework scelto per l'implementazione.