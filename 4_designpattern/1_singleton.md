# Il Pattern Singleton in Java

## Introduzione al Pattern Singleton

Il Singleton è un **pattern creazionale** che garantisce che una classe abbia una sola istanza in tutto il programma, fornendo al contempo un punto di accesso globale a questa istanza. Questo pattern è particolarmente utile in situazioni dove è necessario:

- Controllare risorse condivise
- Proteggere contro sovrascritture accidentali

## Caratteristiche Fondamentali del Singleton

Tutte le implementazioni del pattern Singleton condividono questi elementi chiave:

1. **Costruttore privato**: impedisce ad altri oggetti di utilizzare l'operatore `new` con la classe Singleton
2. **Metodo statico di creazione**: funziona come un costruttore alternativo
3. **Metodo getInstance()**: restituisce sempre la stessa istanza della classe

## Quando Utilizzare il Pattern Singleton

Il Singleton è utile quando:

- Una classe deve avere una singola istanza disponibile per tutti i client del programma
- Si necessita di un controllo più rigoroso sulle variabili globali rispetto a quello offerto da variabili statiche

## Criticità del Pattern Singleton

Nonostante la sua popolarità, il Singleton:

- Viola il **principio di responsabilità singola** (SRP) perché risolve due problemi contemporaneamente:
  - Gestisce la logica aziendale o funzionalità della classe (es. connessione al database)
  - Si occupa anche dell'unicità dell'istanza

## Implementazioni del Pattern Singleton

### 1. Eager Initialization (Inizializzazione Anticipata)

```java
public class EagerInitSingleton {
    // L'istanza viene creata immediatamente al caricamento della classe
    private static final EagerInitSingleton myInstance = new EagerInitSingleton();
    
    // Costruttore privato impedisce l'istanziazione esterna
    private EagerInitSingleton() {}
    
    // Metodo pubblico per ottenere l'istanza
    public static EagerInitSingleton getInstance() {
        return myInstance;
    }
}
```

Caratteristiche principali:
- L'istanza viene creata quando la classe viene caricata
- Utilizza `final` per impedire che myInstance venga modificata
- Thread-safe per natura
- Carica l'istanza anche se potrebbe non essere mai utilizzata

### 2. Lazy Initialization (Inizializzazione Tardiva)

```java
public class LazyInitSingleton {
    // L'istanza viene dichiarata ma non inizializzata subito
    private static LazyInitSingleton myInstance;
    
    // Costruttore privato
    private LazyInitSingleton() {}
    
    // L'istanza viene creata solo quando il metodo getInstance viene chiamato la prima volta
    public static LazyInitSingleton getInstance() {
        if (myInstance == null) {
            myInstance = new LazyInitSingleton();
        }
        return myInstance;
    }
}
```

Caratteristiche principali:
- L'istanza viene creata solo quando viene richiesta per la prima volta
- Non è thread-safe nella forma base (potrebbero crearsi più istanze in ambiente multi-thread)
- Risparmia risorse se l'istanza non viene mai utilizzata

## Vantaggi del Pattern Singleton

1. Garantisce che una classe abbia una sola istanza
2. Fornisce un punto di accesso globale a tale istanza
3. L'oggetto Singleton viene inizializzato solo quando richiesto per la prima volta (nella versione lazy)

## Svantaggi del Pattern Singleton

1. Può rendere difficile il testing del codice
2. Viola il principio di responsabilità singola
3. Richiede un trattamento speciale in ambienti multi-thread (nella versione lazy)
4. Può diventare un antipattern se abusato

## Esempi di Utilizzo Pratico

Il pattern Singleton è comunemente utilizzato per:

- Gestori di configurazione
- Connessioni a database
- Pool di oggetti
- Cache
- Logger di sistema

## Considerazioni sulla Thread Safety

La versione Lazy del Singleton non è thread-safe di default. In ambienti multi-thread, sono necessarie soluzioni come:

1. Sincronizzazione del metodo getInstance()
2. Double-checked locking
3. Holder statico interno

```java
// Esempio di implementazione thread-safe con double-checked locking
public class ThreadSafeSingleton {
    private static volatile ThreadSafeSingleton instance;
    
    private ThreadSafeSingleton() {}
    
    public static ThreadSafeSingleton getInstance() {
        if (instance == null) {
            synchronized (ThreadSafeSingleton.class) {
                if (instance == null) {
                    instance = new ThreadSafeSingleton();
                }
            }
        }
        return instance;
    }
}
```

Il pattern Singleton è uno dei design pattern più semplici ma anche più controversi in Java, essendo allo stesso tempo potente quando usato correttamente e problematico quando abusato.