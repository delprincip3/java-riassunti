# Spring Data: Riassunto Concettuale

## Introduzione a Spring Data

Spring Data è uno dei moduli principali del framework Spring, il cui compito è fornire allo sviluppatore tutti gli strumenti necessari per implementare la gestione della persistenza, sfruttando i meccanismi di Spring come la Dependency Injection. Permette di realizzare applicazioni con persistenza dei dati in modo rapido e standardizzato, favorendo la collaborazione tra sviluppatori e aumentando la manutenibilità del codice.

## Modalità di interazione con i database

Spring Data supporta diverse modalità di interazione con i database (inclusi i NoSQL). Per i database relazionali, le principali sono:

- **JDBC** (Java Database Connectivity)
- **JPA** (Java Persistence API)

## Il problema dell'impedenza

Quando si utilizza la programmazione orientata agli oggetti (OOP) per gestire la persistenza dei dati in un database si genera un conflitto tra:
- **Modello a oggetti**: utilizza classi
- **Modello relazionale**: utilizza tabelle

Questo conflitto, noto come "impedence mismatch" (conflitto di impedenza), si manifesta in differenze tra:
- Tipi di dati supportati
- Modalità di accesso ai dati
- Riferimenti tra entità

## Object Relational Mapping (ORM)

Per risolvere il conflitto di impedenza si utilizza l'ORM, che garantisce:
- Persistenza automatica e trasparente degli oggetti Java nelle tabelle relazionali
- Conversione bidirezionale tra oggetti e dati relazionali basata su metadati
- Hibernate è l'implementazione ORM più diffusa

### Vantaggi dell'ORM:

1. **Maggiore produttività**: riduce il codice di conversione (spesso bastano poche annotazioni)
2. **Performance**: buoni risultati nonostante l'elevato livello di astrazione
3. **Manutenibilità**: poco codice da gestire
4. **No vendor lock-in**: indipendenza dal DBMS specifico e dal dialetto SQL

## Pattern DAO (Data Access Object)

Spring Data promuove l'utilizzo del pattern DAO per:
- Separare le logiche di business da quelle di accesso e gestione dei dati
- Descrivere le operazioni di persistenza in interfacce specifiche
- Implementare la logica in apposite classi
- Cambiare i meccanismi di accesso ai dati (es. da JDBC a JPA) senza impattare sulla logica applicativa

### Caratteristiche del DAO:
- Solo gli oggetti definiti dal pattern possono interagire con il database
- Espongono metodi che possono essere invocati dagli altri componenti che necessitano dei dati
- Operano come adapter tra la business logic e il datasource

## Data Transfer Object (DTO)

I dati scambiati tra database e applicazione vengono incapsulati in oggetti DTO con:
- Campi privati corrispondenti ai dati da leggere/scrivere sul database
- Metodi getter/setter pubblici per l'accesso ai dati
- Eventuali metodi di utilità per confronti, stampe, ecc.
- Possibilità di aggregare dati da più fonti

## Spring JDBC

Spring JDBC è un modulo che semplifica l'uso di JDBC, occupandosi di:
- Aprire la connessione
- Creare ed eseguire i PreparedStatements
- Estrarre i risultati dal ResultSet
- Gestire le eccezioni
- Chiudere la connessione

JDBC è la modalità più semplice di persistenza in Java:
- Non richiede conoscenza di altri framework
- È basata direttamente su SQL
- Opera a livello "low level", vicino alla logica del DBMS
- Garantisce massimo controllo degli accessi e manipolazione dati
- Richiede più codice da scrivere

Spring JDBC utilizza principalmente **JdbcTemplate**, una classe basata sul pattern Template Method che riduce il codice boilerplate per l'accesso al database.

### Limiti di JDBC:
- Difficoltà con applicazioni complesse
- Necessità di mappare manualmente le proprietà dell'oggetto alle colonne del database
- Molta scrittura di codice per i RowMapper
- Mancanza di funzionalità avanzate (lazy loading, eager fetching, cascading)

## Spring JPA

Spring JPA offre pieno supporto alla Java Persistence API (JPA), implementando l'approccio ORM. Non implementa direttamente un JPA Provider ma sfrutta implementazioni come Hibernate (il provider di default).

### Configurazione JPA:
- Per i progetti Spring Boot, si può usare `spring-boot-starter-data-jpa`
- I parametri di configurazione vengono specificati nel file `application.properties`
- Hibernate richiede configurazioni specifiche, come `spring.jpa.hibernate.ddl-auto`

### Modalità DDL (Data Definition Language):
- **create**: rimuove le vecchie tabelle e crea le nuove
- **create-drop**: come create ma rimuove il db al termine delle operazioni
- **validate**: verifica che le tabelle esistano
- **update**: aggiorna o crea tabelle senza rimuovere quelle non più richieste
- **none**: disabilita il ddl

### Convenzioni di denominazione:
Spring Boot adotta una notazione "snake_case" per mappare entità Java in elementi DB:
- La classe `StazioneDiServizio` viene mappata sulla tabella `stazione_di_servizio`
- L'attributo `annoProduzione` viene mappato sulla colonna `anno_produzione`

## Mappatura delle entità con JPA

### Annotazioni base:
- **@Entity**: identifica una classe come entità JPA
- **@Table**: specifica il nome della tabella (opzionale se il nome corrisponde)
- **@Id**: identifica il campo come chiave primaria
- **@GeneratedValue**: specifica come generare i valori per l'ID

### Mapping delle relazioni:
1. **@OneToMany/@ManyToOne**: utilizzate per relazioni uno-a-molti
   - Esempio: un docente può tenere più corsi, ma un corso è erogato da un solo docente

2. **@OneToOne**: utilizzata per relazioni uno-a-uno
   - Esempio: le dispense associate a un corso

3. **@ManyToMany**: utilizzata per relazioni molti-a-molti
   - Esempio: studenti e corsi (uno studente frequenta più corsi, un corso ha più studenti)
   - Richiede una tabella di join configurata con @JoinTable

4. **Cascading**: permette di propagare le operazioni dall'entità padre alle entità figlie
   - Tipi: ALL, PERSIST, MERGE, REMOVE, REFRESH, DETACH

## Repository JPA

L'elemento principale per realizzare la gestione della persistenza in Spring JPA è il repository:
- Disaccoppia le operazioni di persistenza dai dettagli implementativi
- Fornisce funzioni CRUD pronte all'uso
- Implementazione automatica tramite interfacce

### Tipologie di repository:
1. **CrudRepository**: fornisce operazioni CRUD base
   - save(), findOne(), findAll(), count(), delete(), exists()

2. **PagingAndSortingRepository**: estende CrudRepository aggiungendo paginazione e ordinamento
   - findAll(Pageable pageable)

3. **JpaRepository**: il più completo, estende PagingAndSortingRepository
   - findAll(), save(), flush(), saveAndFlush(), deleteInBatch()

### Query Methods:
Definizione di metodi la cui implementazione viene generata automaticamente da Spring basandosi sul nome:
- Iniziano con prefissi come: findBy, find, readBy, read, getBy, get
- Seguiti dal nome delle proprietà e dagli operatori (And, Or, Between, LessThan, StartingWith...)
- Esempio: `findByTarga(String targa)`, `findByAnnoProduzioneBetween(Integer min, Integer max)`

### JPQL e Query personalizzate:
L'annotazione **@Query** permette di specificare query personalizzate:
- JPQL: Java Persistence Query Language, orientato al modello di oggetti
- Query native: SQL puro, specificato con nativeQuery=true
- Vantaggi JPQL: database agnostic, riferimento alle entità anziché alle tabelle
- Binding parametri: posizionale (?1) o named (:nome)

## Criteri di confronto delle soluzioni

- **Query Methods**: facili da usare ma limitate a query semplici
- **JPQL**: più potente ma richiede conoscenza della sintassi
- **Native Query**: potere massimo ma meno portabilità
- **Criteria API**: approccio programmatico type-safe ma più verboso

## Esempi pratici

### Esempio di entità JPA:
```java
@Entity
public class Automobile {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String targa;
    private int annoProduzione;
    private String colore;
    private String marca;
    
    @OneToOne
    private Motore motore;
    
    // getter e setter
}
```

### Esempio di repository:
```java
public interface AutomobileRepository extends JpaRepository<Automobile, Long> {
    List<Automobile> findByTarga(String targa);
    List<Automobile> findByAnnoProduzioneBetween(Integer min, Integer max);
    
    @Query("FROM Automobile WHERE marca in ('Alfa Romeo', 'Ferrari', 'Maserati')")
    List<Automobile> findAutoItaliane();
}
```

### Esempio di uso del repository:
```java
@Component
public class JpaTest implements CommandLineRunner {
    @Autowired
    private AutomobileRepository automobileRepository;
    
    @Override
    public void run(String... args) {
        // Carica tutti gli elementi
        List<Automobile> findAll = automobileRepository.findAll();
        
        // Carica per ID
        Optional<Automobile> find = automobileRepository.findById(1L);
        
        // Conta gli elementi
        long count = automobileRepository.count();
        
        // Salva un elemento
        Automobile auto = new Automobile();
        // Set delle proprietà...
        automobileRepository.save(auto);
        
        // Elimina un elemento
        automobileRepository.deleteById(123L);
        
        // Applica le modifiche al database
        automobileRepository.flush();
    }
}
```

Questo riassunto copre i concetti fondamentali di Spring Data, inclusi JDBC, JPA, Repository, ORM e le varie modalità di query. Le funzionalità di Spring Data semplificano significativamente lo sviluppo della persistenza rispetto all'uso diretto di JDBC o JPA.