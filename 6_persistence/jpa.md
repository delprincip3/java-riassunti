
## Introduzione a JPA

JPA (Java Persistence API) è una specifica che definisce come rendere persistenti gli oggetti Java, permettendo loro di sopravvivere oltre il ciclo di vita dell'applicazione. JPA affronta il problema dell'Object-Relational Impedance Mismatch, ovvero le differenze fondamentali tra il modello di dati relazionale e il modello a oggetti.

L'ORM (Object Relational Mapping) si concentra sulla mappatura delle strutture delle classi Java costruite secondo il paradigma Object-Oriented alle strutture dei database relazionali. Questa corrispondenza è cruciale per realizzare applicazioni Enterprise con dati persistenti.

## Impedence Mismatch

Le principali differenze tra modello relazionale e modello a oggetti riguardano:
- Ereditarietà
- Associazioni e relazioni
- Navigazione tra oggetti
- Persistenza delle collezioni
- Transazioni e stato

## JPA vs JDBC

Con JDBC, l'interazione con il database richiede di scrivere query SQL e sincronizzare manualmente i campi con le proprietà degli oggetti Java. JPA automatizza gran parte di questo lavoro, permettendo agli sviluppatori di concentrarsi sulla logica applicativa.

JPA non è uno strumento o un framework utilizzabile direttamente, ma un insieme di concetti implementati da frameworks esterni come Hibernate o EclipseLink.

## Entity e Annotazioni

Le classi i cui oggetti vengono salvati sono chiamate Entity. Le principali annotazioni sono:
- `@Entity`: indica che la classe deve essere trattata come un'entità persistente
- `@Table`: definisce la tabella del database a cui mappare la classe
- `@Column`: specifica la colonna della tabella corrispondente a un attributo
- `@Id`: identifica l'attributo che funge da chiave primaria

## Entity Manager

L'EntityManager è l'interfaccia che permette di interagire con il persistence context e compiere operazioni sulle entità. Il persistence context rappresenta un insieme di istanze di entità il cui ciclo di vita viene gestito dal framework.

Le principali operazioni dell'EntityManager sono:
- `find`: recupera un'entità dal database tramite la sua chiave primaria
- `persist`: aggiunge un'entità al persistence context
- `merge`: aggiorna un'entità esistente o la crea se non esiste
- `remove`: elimina un'entità dal database
- `createQuery`: crea query per recuperare dati

Esempio di utilizzo:
```java
EntityManager em = entityManagerFactory.createEntityManager();
em.getTransaction().begin();
MyEntity entity = new MyEntity();
entity.setField("value");
em.persist(entity);
em.getTransaction().commit();
em.close();
```

## Ciclo di vita delle entità

Le entità possono essere in vari stati:
- New: oggetto appena creato
- Managed: entità gestita dal persistence context
- Detached: entità non più tracciata dal persistence context
- Removed: entità contrassegnata per la rimozione

## Persist vs Merge

- `persist`: inserisce una nuova entità nel database. Può essere usata solo quando l'entità non è già presente
- `merge`: aggiorna un'entità esistente o la crea se non esiste ancora

## Cascading

Il provider può propagare le operazioni effettuate su una entità alle entità collegate:
- `CascadeType.MERGE`: propaga gli UPDATE
- `CascadeType.PERSIST`: propaga il primo inserimento (INSERT)
- `CascadeType.REFRESH`: propaga l'aggiornamento dal database verso gli oggetti
- `CascadeType.DETACH`: propaga la rimozione dell'oggetto dalla persistenza
- `CascadeType.REMOVE`: propaga la rimozione dei dati (DELETE)
- `CascadeType.ALL`: tutti i precedenti

## Fetching

Il tipo di caricamento delle relazioni può essere specificato con il parametro `fetch`:
- `EAGER`: carica immediatamente l'entità e i suoi dati correlati
- `LAZY`: carica i dati correlati solo quando vengono effettivamente richiesti

La modalità predefinita dipende dal tipo di relazione:
- One-To-One: EAGER
- Many-To-One: EAGER
- One-To-Many: LAZY
- Many-To-Many: LAZY

## Primary Key

La generazione delle chiavi primarie può essere demandata a meccanismi automatici:
- `GenerationType.AUTO`: il provider sceglie la strategia più appropriata
- `GenerationType.IDENTITY`: utilizza la funzionalità di auto-incremento del database
- `GenerationType.SEQUENCE`: utilizza una sequenza del database
- `GenerationType.TABLE`: utilizza una tabella separata per gestire le chiavi primarie

Esempio:
```java
@Id
@GeneratedValue(strategy = GenerationType.IDENTITY)
private Long id;
```

## Relazioni tra entità

### One-to-One
Una relazione uno-a-uno indica che un'istanza di una classe è associata a una e una sola istanza di un'altra classe.

#### One-to-One Classica
Utilizza due tabelle separate, ciascuna con i propri dati, collegate da una foreign key.

```java
@Entity
public class User {
    @OneToOne
    private Address address;
}

@Entity
public class Address {
    @OneToOne(mappedBy = "address")
    public User user;
}
```

#### One-to-One Embedded
L'entità "embedded" non ha una propria identità nel database, ma le sue proprietà sono memorizzate nella stessa tabella dell'entità principale.

```java
@Entity
public class Azienda {
    @Embedded
    private Indirizzo indirizzo;
}

@Embeddable
public class Indirizzo {
    private String via;
    private String citta;
}
```

### One-to-Many / Many-to-One
Una relazione uno-a-molti indica che un oggetto è collegato a molti oggetti di un altro tipo.

```java
@Entity
public class User {
    @OneToMany(mappedBy = "user")
    public Set<Telephone> listaTelefono;
}

@Entity
public class Telephone {
    @ManyToOne
    @JoinColumn(name = "id_user")
    public User user;
}
```

### Many-to-Many
In una relazione molti-a-molti, entrambi gli oggetti utilizzano l'annotazione `@ManyToMany` e richiede una tabella di collegamento.

```java
@Entity
public class Impiegato {
    @ManyToMany
    @JoinTable(name = "impiegato_progetto",
              joinColumns = @JoinColumn(name = "idimpiegato"),
              inverseJoinColumns = @JoinColumn(name = "idprogetto"))
    private List<Progetto> progetti;
}

@Entity
public class Progetto {
    @ManyToMany(mappedBy = "progetti")
    private List<Impiegato> impiegati;
}
```

## Ereditarietà

JPA supporta tre strategie principali per rappresentare l'ereditarietà nei database:

### Single Table
Tutte le classi vengono mappate in un'unica tabella, con una colonna discriminatrice che indica il tipo di entità. Gli attributi delle classi figlio che non si applicano ad altre classi sono impostati come NULL.

```java
@Entity
@Inheritance(strategy = InheritanceType.SINGLE_TABLE)
public abstract class Veicolo {
    @Id
    private Long id;
    private Integer annoProduzione;
}

@Entity
public class Automobile extends Veicolo {
    private String targa;
    private String colore;
}
```

### Joined Table
Crea una tabella separata per la classe base e una per ogni classe derivata. Le tabelle figlio contengono solo gli attributi specifici della sottoclasse.

```java
@Entity
@Inheritance(strategy = InheritanceType.JOINED)
public abstract class Veicolo {
    @Id
    private Long id;
    private Integer annoProduzione;
}

@Entity
public class Natante extends Veicolo {
    private Integer numeroMotori;
    private Boolean fuoribordo;
}
```

### Table-Per-Class
Ogni classe concreta ha una propria tabella che contiene tutti gli attributi, inclusi quelli ereditati.

```java
@Entity
@Inheritance(strategy = InheritanceType.TABLE_PER_CLASS)
public abstract class Veicolo {
    @Id
    private Long id;
    private Integer annoProduzione;
}

@Entity
public class Automobile extends Veicolo {
    private String targa;
    private String colore;
}
```

## Confronto tra strategie di ereditarietà

- **Joined**: Schema più manutenibile perché ogni modifica è isolata alla sua tabella, ma richiede JOIN per ottenere tutti i dati di un'entità.
- **Table-Per-Class**: Massimizza la separazione, migliori performance rispetto a Joined (nessun JOIN), ma comporta ridondanza dei dati.
- **Single Table**: Migliori performance perché tutte le query vengono eseguite su una sola tabella, ma può diventare disordinata e difficile da gestire con molte sottoclassi.

## Mapped Superclass

È un caso speciale di ereditarietà dove la classe padre non è resa persistente direttamente:

```java
@MappedSuperclass
public abstract class BaseEntity {
    @Id
    private Long id;
    private Date createdAt;
}

@Entity
public class Customer extends BaseEntity {
    private String name;
}
```

## Conclusioni

JPA semplifica notevolmente la gestione della persistenza in Java, automatizzando la mappatura tra oggetti e database relazionali. È importante scegliere correttamente le strategie di mappatura e implementazione in base alle necessità specifiche dell'applicazione, considerando fattori come la struttura dei dati, il modello di ereditarietà, le performance e la manutenibilità.