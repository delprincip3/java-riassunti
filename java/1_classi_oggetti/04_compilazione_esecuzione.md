# Compilazione ed Esecuzione in Java: Guida Definitiva

## 🚀 Concetti Fondamentali

### 1. Processo di Compilazione

#### Cos'è la Compilazione?
- Trasformazione del codice sorgente `.java` in bytecode `.class`
- Il compilatore Java traduce il codice in un formato comprensibile dalla Java Virtual Machine (JVM)

#### Esempio Pratico
```bash
# Compilazione base di un file Java
javac NomeClasse.java

# Compilazione con gestione dei package
javac -d . NomeClasse.java
```

### 2. Package: Organizzazione Logica

#### Struttura dei Package
- Simili a cartelle in un archivio fisico
- Raggruppano classi correlate
- Migliorano la manutenibilità del codice

#### Esempio di Struttura
```
progetto/
│
└── com/
    └── azienda/
        └── modulo/
            ├── ClassePrincipale.java
            └── AltraClasse.java
```

### 3. Fully Qualified Class Name (FQCN)

#### Definizione
- Nome completo di una classe: `nomepacchetto.nomeclasse`
- Identificazione univoca di una classe nell'applicazione

#### Esempio
```java
// FQCN: com.azienda.modulo.ClassePrincipale
package com.azienda.modulo;
public class ClassePrincipale { }
```

### 4. Esecuzione dei Programmi Java

#### Comandi Principali
```bash
# Esecuzione base
java NomeClasse

# Esecuzione con specificazione del classpath
java -cp . NomeClasse

# Esecuzione diretta del sorgente (Java 11+)
java NomeClasse.java
```

### 5. Classpath

#### Cosa è il Classpath?
- Percorso dove la JVM cerca le classi e i package
- Può essere specificato tramite:
  - Variabile di ambiente
  - Opzione `-cp` o `-classpath`

#### Esempio
```bash
java -cp /percorso/classi NomeClasse
```

## 📦 Archivi JAR (Java Archive)

### Concetti Chiave
- Formato per distribuire applicazioni Java
- Può contenere:
  - Classi compilate
  - Risorse
  - Metadati

#### Creazione di un JAR
```bash
# Creazione base
jar cf nomeprogramma.jar percorso/classi

# Creazione con manifest personalizzato
jar cfm nomeprogramma.jar mymanifest.txt -C percorso/classi .
```

### 6. Parametri da Linea di Comando

#### Passaggio di Argomenti
```java
public class EsempioParametri {
    public static void main(String[] args) {
        // args contiene tutti i parametri passati
        for(String arg : args) {
            System.out.println(arg);
        }
    }
}

# Esecuzione con parametri
java EsempioParametri param1 param2 param3
```

## 💡 Best Practices

1. Usa sempre i package per organizzare il codice
2. Compila con `-d .` per creare la struttura di directory corretta
3. Specifica sempre il classpath se necessario
4. Usa gli archivi JAR per distribuire applicazioni
5. Gestisci con attenzione i parametri da linea di comando

## ⚠️ Avvertenze Comuni

- Controllare sempre la correttezza del FQCN
- Verificare che il classpath sia configurato correttamente
- Prestare attenzione alla gerarchia dei package
- Gestire correttamente gli argomenti nel metodo `main()`

**Conclusione**: La comprensione del processo di compilazione ed esecuzione è fondamentale per sviluppare applicazioni Java robuste e ben strutturate.