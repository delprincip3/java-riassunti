# Maven 101 - Riassunto

## Cos'è Apache Maven?

Maven è uno strumento di gestione del progetto e di automazione del build basato sul concetto di Project Object Model (POM). È comunemente utilizzato per progetti basati su Java e ha lo scopo di semplificare e standardizzare il processo di build.

## Project Object Model (POM)

Il Project Object Model (POM) è il file XML che contiene tutte le informazioni necessarie a Maven per costruire, gestire e distribuire il progetto. Contiene dettagli come:

- Versione del progetto
- Dipendenze
- Plugin
- Altre configurazioni

### Struttura Base del POM

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.example</groupId>
    <artifactId>my-app</artifactId>
    <version>1.0-SNAPSHOT</version>
</project>
```

Elementi principali:
- `<project>`: Elemento radice del file, tutti gli altri elementi sono nidificati all'interno di questo tag
- `<modelVersion>`: Versione del modello POM (4.0.0 è l'unica versione attualmente utilizzata dalla maggior parte dei progetti Maven)
- `<groupId>`: ID del gruppo, solitamente un identificatore unico per l'organizzazione o il progetto principale (es. com.example)
- `<artifactId>`: ID dell'artefatto, il nome del progetto o modulo specifico, utilizzato come nome del file risultante (es. my-app.jar)
- `<version>`: Versione corrente del progetto

## Gestione delle Dipendenze

Maven semplifica notevolmente la gestione delle dipendenze. Basta includere le dipendenze nel file POM, e Maven si occupa di scaricare le librerie necessarie (incluse le loro dipendenze) e di gestire le versioni corrette.

### Esempio di Dipendenze

```xml
<dependencies>
    <dependency>
        <groupId>ch.qos.logback</groupId>
        <artifactId>logback-classic</artifactId>
        <version>1.2.10</version>
    </dependency>
    <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-api</artifactId>
        <version>1.7.32</version>
    </dependency>
</dependencies>
```

### Identificatore Univoco

La combinazione di `groupId`, `artifactId` e `version` forma un identificatore univoco per ogni artefatto, usato da Maven per gestire le dipendenze:

```
com.example:my-app:1.0-SNAPSHOT
```

## Repository Maven

I repository in Maven sono directory contenenti file JAR preconfezionati e i relativi metadati, necessari per il funzionamento del progetto:

1. **Local Repository**: Situato sulla macchina di sviluppo, contiene tutte le dipendenze scaricate
2. **Central Repository**: Un repository pubblico gestito dalla comunità Maven, utilizzato quando una dipendenza non è presente nel repository locale
3. **Remote Repository**: Un repository situato su un server web. Se una dipendenza non è presente nel repository centrale, Maven la scarica da un repository remoto

### Ricerca delle Dipendenze

Quando si cerca di utilizzare una libreria che non è presente nel repository locale, Maven cerca prima nel repository locale, poi nel repository centrale e, infine, nei repository remoti configurati.

### Configurazione di Repository

```xml
<repositories>
    <repository>
        <id>central</id>
        <url>https://repo.maven.apache.org/maven2</url>
    </repository>
</repositories>
```

## Principio di "Convenzione su Configurazione"

Maven adotta il principio di "Convenzione su Configurazione", il che significa che, finché si seguono le convenzioni predefinite, non è necessario configurare esplicitamente molti dettagli.

La struttura standard di un progetto Maven è:

```
project-home/
├── src/
│   ├── main/
│   │   ├── java/
│   │   └── resources/
│   └── test/
│       ├── java/
│       └── resources/
├── target/
└── pom.xml
```

Questa struttura consente a Maven di trovare automaticamente il codice sorgente, le risorse e i test, senza richiedere configurazioni esplicite.

## Esempi Pratici

### Esempio di Integrazione Maven con Librerie di Logging

In un'applicazione Java che utilizza logback e slf4j per il logging, il file POM conterrà:

```xml
<dependencies>
    <dependency>
        <groupId>ch.qos.logback</groupId>
        <artifactId>logback-classic</artifactId>
        <version>1.2.10</version>
    </dependency>
    <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-api</artifactId>
        <version>1.7.32</version>
    </dependency>
</dependencies>
```

Questo è sufficiente per scaricare automaticamente le librerie necessarie e gestire le versioni corrette.

## Vantaggi di Maven

1. **Gestione automatica delle dipendenze**: Maven scarica e gestisce tutte le dipendenze necessarie
2. **Struttura standard del progetto**: Tutti i progetti Maven seguono una struttura coerente
3. **Automazione del build**: Maven automatizza il processo di compilazione, test e packaging
4. **Riusabilità**: I progetti Maven possono essere facilmente condivisi e integrati in altri progetti
5. **Estensibilità**: Maven può essere esteso con plugin per funzionalità personalizzate

## Conclusione

Maven è uno strumento potente per la gestione di progetti Java che semplifica enormemente il processo di build e la gestione delle dipendenze. Utilizzando il concetto di Project Object Model e seguendo le convenzioni standard, Maven permette agli sviluppatori di concentrarsi sulla scrittura del codice piuttosto che sulla configurazione del build.