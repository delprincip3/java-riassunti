

## Bean Scoping in Spring

Spring offre sei diverse tipologie di scope per i beans:

1. **Singleton** (default): Il container crea una singola istanza del bean. Tutte le richieste a quel bean restituiscono lo stesso oggetto, e ogni modifica si riflette ovunque. Si definisce con `@Scope("singleton")`.

2. **Prototype**: Il container crea una nuova istanza per ogni richiesta. Si definisce con `@Scope("prototype")`.

3. **Request**: Nelle applicazioni web, viene creata una nuova istanza per ogni richiesta HTTP. Si definisce con `@RequestScope`.

4. **Session**: Viene creata un'istanza per ogni sessione utente. Si definisce con `@SessionScope`.

5. **Application**: Scope per l'intera applicazione web.

6. **Websocket**: Specifico per connessioni websocket.

Gli ultimi quattro scope sono utilizzabili solo in contesti di applicazioni web.

## Dependency Injection e Autowiring

L'autowiring in Spring semplifica notevolmente la gestione delle dipendenze, implementando il principio "convention over configuration". Ci sono tre principali tipi di injection:

1. **Constructor Injection**: 
   - Le dipendenze vengono fornite tramite costruttore
   - Permette di dichiarare le dipendenze come `final` rendendole immutabili
   - Garantisce che le dipendenze siano inizializzate all'istanza dell'oggetto
   - Più adatto per dipendenze obbligatorie

2. **Setter Injection**:
   - Le dipendenze vengono fornite tramite metodi setter
   - Più flessibile con dipendenze opzionali
   - Permette la sostituzione della dipendenza dopo l'inizializzazione
   - Richiede controlli per evitare NullPointerException

3. **Field Injection**:
   - Le dipendenze vengono iniettate direttamente negli attributi con `@Autowired`
   - Meno codice da scrivere (no setter/costruttori)
   - Più difficile da testare in isolamento
   - Non permette l'uso di `final` per gli attributi

L'autowiring può essere configurato in diverse modalità:
- `no`: disabilita il meccanismo (default)
- `byName`: inietta basandosi sul nome del bean
- `byType`: inietta basandosi sul tipo della proprietà
- `constructor`: inietta attraverso il costruttore

Per gestire possibili ambiguità quando esistono più implementazioni della stessa interfaccia, si può usare l'annotazione `@Primary` per designare un bean preferito.

## Architettura Spring

L'architettura di Spring è modulare e si basa sul Core Container, attorno al quale si sviluppano vari moduli:

- **Core Container**: Gestisce il ciclo di vita dei bean e le loro dipendenze
  - Beans: definizione dei bean e configurazioni
  - Core e Context: fondamenta per gestione bean e contesti
  - Expression Language: accesso dinamico alle proprietà dei bean

- **Infrastruttura di supporto**:
  - Transactions: gestione dichiarativa delle transazioni
  - AOP: programmazione orientata agli aspetti per funzionalità trasversali
  - ORM: integrazione con tecnologie di mappatura oggetti-relazionale

- **Web e MVC**:
  - Web e Servlet: supporto per il modello MVC
  - WebApplication Context: contesto dedicato alle applicazioni web
  - Form Controllers: gestione della logica di controller e dati form

- **Supporto Enterprise**:
  - Email Sending: gestione email
  - Remote Access: comunicazioni remote

## Profili Spring

I profili permettono di configurare l'applicazione per diversi ambienti (sviluppo, test, produzione) offrendo:
- Flessibilità nella configurazione
- Isolamento degli ambienti
- Testing semplificato

Un profilo si può attivare:
- Tramite proprietà di avvio `-Dspring.profiles.active=dev`
- Tramite file di configurazione (application.properties o application.yml)

Questo permette, ad esempio, di utilizzare un database in memoria come H2 durante lo sviluppo (`dev`) e un database MySQL in produzione (`prod`).

L'utilizzo corretto di questi meccanismi rende Spring particolarmente adatto alla creazione di applicazioni enterprise robuste, modulari e facilmente manutenibili.