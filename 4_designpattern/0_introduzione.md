

# Design Patterns e Principi SOLID in Java

## Introduzione ai Pattern

I design pattern sono soluzioni tipiche a problemi ricorrenti nella progettazione del software. Essi:
- Rappresentano schemi predefiniti che definiscono un linguaggio comune
- Non sono pezzi specifici di codice, ma concetti generali
- Fungono da cassetta degli attrezzi di soluzioni collaudate

La maggior parte dei pattern sono descritti formalmente con:
- **Intento del pattern**: descrive brevemente il problema e la soluzione
- **Motivazione**: spiega dettagliatamente il problema e la soluzione
- **Struttura delle classi**: mostra ogni parte del pattern e come sono correlate
- **Esempio di codice**: dimostra l'implementazione in un linguaggio di programmazione

### Origini dei Pattern
- Il concetto è stato introdotto da Christopher Alexander nel libro "A Pattern Language: Towns, Buildings, Construction" per l'architettura urbana
- Nel 1994, quattro autori (Erich Gamma, John Vlissides, Ralph Johnson e Richard Helm), noti come "Gang of Four" (GoF), applicarono il concetto alla programmazione nel libro "Design Patterns: Elements of Reusable Object-Oriented Software"
- Il libro GoF contiene 23 pattern che risolvono vari problemi di progettazione orientata agli oggetti

### Vantaggi dei Pattern
1. Offrono soluzioni collaudate a problemi comuni
2. Insegnano come risolvere problemi utilizzando i principi OOP
3. Definiscono un linguaggio comune per comunicare efficacemente (es. "Usiamo un Singleton per quello")

### Critiche ai Pattern
1. **Soluzioni inefficienti**: possono essere implementati "alla lettera" senza adattarli al contesto
2. **Uso ingiustificato**: possono essere applicati anche dove un codice più semplice sarebbe sufficiente ("Se tutto ciò che hai è un martello, ogni cosa sembra un chiodo")

### Classificazione dei Pattern
I pattern variano per complessità, dettaglio e scala di applicabilità:
- **Idiomi**: pattern basilari a basso livello (specifici per un linguaggio)
- **Pattern creazionali**: forniscono meccanismi di creazione degli oggetti
  - Factory Method, Abstract Factory, Builder, Prototype, Singleton
- **Pattern strutturali**: come assemblare oggetti e classi in strutture più grandi
  - Adapter, Bridge, Composite, Decorator, Facade, Proxy
- **Pattern comportamentali**: gestiscono la comunicazione e le responsabilità
  - Chain of Responsibility, Command, Iterator, Observer, Strategy
- **Pattern architetturali**: soluzioni a livello di architettura dell'intero sistema
  - MVC, Microservices, Event-Driven Architecture

## GRASP (General Responsibility Assignment Software Patterns)

Principi per determinare le responsabilità delle classi e degli oggetti:

### Information Expert
- Assegna la responsabilità alla classe che ha le informazioni necessarie per adempiervi
- Assicura che dati e comportamento siano incapsulati
- **Esempio**: La classe `Ordine` calcola il totale dell'ordine perché possiede le informazioni sui prodotti

### Low Coupling
- Minimizza le dipendenze tra classi per ridurre l'impatto dei cambiamenti
- Promuove flessibilità e manutenibilità del codice
- **Esempio**: La classe `Ordine` dipende dall'interfaccia `ServizioPagamento` invece che da una specifica implementazione

### High Cohesion
- Raggruppa responsabilità e comportamenti correlati all'interno di una stessa classe
- Assicura che una classe abbia uno scopo ben definito e focalizzato
- **Esempio**: La classe `Inventario` contiene solo metodi relativi alla gestione dei prodotti

## Principi SOLID

Acronimo per i primi cinque principi della progettazione orientata agli oggetti formulati da Robert C. Martin:

### S - Single-responsibility Principle (Principio di Responsabilità Unica)
- "Una classe dovrebbe avere una e una sola ragione per cambiare"
- **Esempio negativo**: La classe `Studente` ha un metodo `calcolaMedia()` che dovrebbe essere in una classe separata come `CalcolatoreMedia`
- **Esempio corretto**: Separare le responsabilità in classi distinte

### O - Open-closed Principle (Principio Aperto-Chiuso)
- "Le entità software dovrebbero essere aperte all'estensione ma chiuse alla modifica"
- **Esempio negativo**: La classe `CalcolatorePunteggio` usa if/else per diversi tipi di esame
- **Esempio corretto**: Creare un'interfaccia `Esame` con diverse implementazioni

### L - Liskov Substitution Principle (Principio di Sostituzione di Liskov)
- "Ogni sottoclasse o classe derivata dovrebbe poter sostituire la sua classe base"
- **Esempio negativo**: Una sottoclasse `Studente` che lancia un'eccezione in un metodo ereditato
- **Esempio corretto**: Tutte le sottoclassi rispettano il comportamento della classe base

### I - Interface Segregation Principle (Principio di Segregazione delle Interfacce)
- "Un client non dovrebbe essere costretto a implementare un'interfaccia che non utilizza"
- **Esempio negativo**: Un'interfaccia `GestioneEsame` con metodi sia per registrazione che per valutazione
- **Esempio corretto**: Separare in due interfacce, `RegistrazioneEsame` e `ValutazioneEsame`

### D - Dependency Inversion Principle (Principio di Inversione delle Dipendenze)
- "Le entità devono dipendere da astrazioni, non da implementazioni concrete"
- **Esempio negativo**: `GestioneStudenti` dipende direttamente da `MySQLConnection`
- **Esempio corretto**: `GestioneStudenti` dipende dall'interfaccia `DatabaseConnection`

## Esempi concreti

### Information Expert
```java
public class Ordine {
    private List<Prodotto> prodotti;
    
    public Ordine(List<Prodotto> prodotti) {
        this.prodotti = prodotti;
    }
    
    public double calcolaTotaleOrdine() {
        double totale = 0;
        for (Prodotto prodotto : prodotti) {
            totale += prodotto.getPrezzo();
        }
        return totale;
    }
}
```

### Low Coupling
```java
public interface ServizioPagamento {
    void paga(double importo);
}

public class PayPalServizioPagamento implements ServizioPagamento {
    @Override
    public void paga(double importo) {
        System.out.println("Pagamento di " + importo + " tramite PayPal");
    }
}

public class Ordine {
    private ServizioPagamento servizioPagamento;
    
    public Ordine(ServizioPagamento servizioPagamento) {
        this.servizioPagamento = servizioPagamento;
    }
    
    public void processaPagamento(double importo) {
        servizioPagamento.paga(importo);
    }
}
```

### Open-Closed Principle
```java
// Cattivo esempio
class CalcolatorePunteggio {
    public int calcolaPunteggio(String tipoEsame, int punteggioBase) {
        if (tipoEsame.equals("Scritto")) {
            return punteggioBase + 10;
        } else if (tipoEsame.equals("Orale")) {
            return punteggioBase + 5;
        }
        return punteggioBase;
    }
}

// Buon esempio
interface Esame {
    int calcolaPunteggio(int punteggioBase);
}

class EsameScritto implements Esame {
    public int calcolaPunteggio(int punteggioBase) {
        return punteggioBase + 10;
    }
}

class EsameOrale implements Esame {
    public int calcolaPunteggio(int punteggioBase) {
        return punteggioBase + 5;
    }
}

class CalcolatorePunteggio {
    public int calcolaPunteggio(Esame esame, int punteggioBase) {
        return esame.calcolaPunteggio(punteggioBase);
    }
}
```

### Dependency Inversion
```java
// Cattivo esempio
class MySQLConnection {
    public void connect() {
        System.out.println("Connessione a MySQL");
    }
}

class GestioneStudenti {
    private MySQLConnection dbConnection;
    
    public GestioneStudenti() {
        this.dbConnection = new MySQLConnection();
    }
    
    public void connetti() {
        dbConnection.connect();
    }
}

// Buon esempio
interface DatabaseConnection {
    void connect();
}

class MySQLConnection implements DatabaseConnection {
    public void connect() {
        System.out.println("Connessione a MySQL");
    }
}

class GestioneStudenti {
    private DatabaseConnection dbConnection;
    
    public GestioneStudenti(DatabaseConnection dbConnection) {
        this.dbConnection = dbConnection;
    }
    
    public void connetti() {
        dbConnection.connect();
    }
}
```

Questi principi e pattern aiutano a sviluppare software più manutenibile, estensibile e robusto, facilitando la collaborazione tra sviluppatori grazie a un linguaggio comune.