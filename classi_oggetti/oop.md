# Classi e Oggetti in Java: Guida Completa

## 1. Classi e Pacchetti

### Classi
- Una classe è un raggruppamento concettuale di oggetti con caratteristiche e comportamenti simili
- Immagina le classi come "stampini per biscotti" e gli oggetti come i biscotti stessi

**Esempio:**
```java
public class Automobile {
    // Attributi
    private String marca;
    private String modello;
    private int anno;

    // Metodi
    public void accendi() {
        System.out.println("L'automobile è accesa!");
    }
}

// Creazione di oggetti
Automobile ferrari = new Automobile();
Automobile tesla = new Automobile();
```

### Pacchetti
- Ogni classe Java appartiene a un pacchetto
- Convenzione consigliata: Utilizzare il nome di dominio inverso (es. `com.azienda`)

**Esempio:**
```java
package com.example.veicoli;

public class Automobile {
    // Classe nel pacchetto com.example.veicoli
}
```

### Modificatori di Accesso
- Quattro modificatori: `private`, `protected`, `public`, e `default`
- `private`: Accessibile solo all'interno della stessa classe
- `protected`: Accessibile all'interno dello stesso pacchetto e dalle sottoclassi
- `public`: Accessibile ovunque
- `default`: Accessibile all'interno dello stesso pacchetto

**Esempio:**
```java
public class Automobile {
    private String targa;         // Accessibile solo all'interno della classe
    protected int chilometraggio; // Accessibile nel pacchetto e sottoclassi
    public String modello;        // Accessibile ovunque
    int anno;                     // Accessibile solo nel pacchetto
}
```

## 2. Incapsulamento

### Principi Fondamentali
- Combina dati e comportamenti (variabili e metodi) in un'unica unità
- Nasconde i dettagli di implementazione
- Espone solo un'interfaccia accessibile per l'interazione
- Protegge lo stato interno dell'oggetto

**Esempio:**
```java
public class ContoCorrente {
    private double saldo;  // Attributo privato

    // Metodi per gestire il saldo
    public void deposita(double importo) {
        if (importo > 0) {
            saldo += importo;
        }
    }

    public void preleva(double importo) {
        if (importo > 0 && importo <= saldo) {
            saldo -= importo;
        }
    }

    public double getSaldo() {
        return saldo;
    }
}
```

## 3. Getter e Setter

### Scopo
- Accedere e modificare attributi privati
- Mantenere i principi di incapsulamento
- Fornire protezione e validazione dei dati

**Esempio:**
```java
public class Studente {
    private String nome;
    private int eta;

    // Getter
    public String getNome() {
        return nome;
    }

    // Setter con validazione
    public void setEta(int eta) {
        if (eta > 0 && eta < 120) {
            this.eta = eta;
        } else {
            System.out.println("Età non valida");
        }
    }
}
```

## 4. Costruttori

### Punti Chiave
- Stesso nome della classe
- Nessun tipo di ritorno
- Possono essere sovraccaricati
- Garantiscono che l'oggetto sia "pronto all'uso"

**Esempio:**
```java
public class Automobile {
    private String marca;
    private String modello;

    // Costruttore senza parametri
    public Automobile() {
        this.marca = "Non specificata";
        this.modello = "Non specificato";
    }

    // Costruttore con parametri
    public Automobile(String marca, String modello) {
        this.marca = marca;
        this.modello = modello;
    }
}
```

## 5. Passaggio per Valore e Riferimento

### Tipi Primitivi
- Passati per valore
- Viene passata una copia del valore effettivo
- Modifiche all'interno di un metodo non influenzano la variabile originale

**Esempio:**
```java
public void modificaNumero(int x) {
    x = 10;  // Modifica solo la copia locale
}

int numero = 5;
modificaNumero(numero);
System.out.println(numero);  // Stampa ancora 5
```

### Tipi Riferimento
- Passati per valore del riferimento
- Più variabili possono puntare allo stesso oggetto
- Modifiche allo stato dell'oggetto si riflettono sull'oggetto originale

**Esempio:**
```java
public void modificaOggetto(Automobile auto) {
    auto.setModello("Nuovo Modello");  // Modifica l'oggetto originale
}

Automobile miaAuto = new Automobile("Ferrari", "Vecchio Modello");
modificaOggetto(miaAuto);
System.out.println(miaAuto.getModello());  // Stampa "Nuovo Modello"
```

## 6. Metodi Fondamentali

### toString()
- Converte l'oggetto in una rappresentazione testuale
- Può essere sovrascritto per fornire output personalizzato

**Esempio:**
```java
@Override
public String toString() {
    return "Automobile{" +
           "marca='" + marca + '\'' +
           ", modello='" + modello + '\'' +
           '}';
}
```

### equals()
- Confronta l'equivalenza degli oggetti
- `==` verifica l'identità
- `equals()` verifica il valore

### hashCode()
- Utilizzato con `equals()`
- Deve essere coerente con `equals()`

## 7. Membri Statici

### Caratteristiche
- Appartengono alla classe, non a specifiche istanze
- Una singola copia condivisa da tutte le istanze della classe
- Accessibili senza creare un'istanza dell'oggetto

**Esempio:**
```java
public class ContatoreSoci {
    private static int totaleSoci = 0;  // Variabile statica condivisa

    public static int getTotaleSoci() {
        return totaleSoci;
    }

    public void registraNuovoSocio() {
        totaleSoci++;  // Incrementa il contatore statico
    }
}
```

## Conclusione
Comprendere questi concetti di programmazione orientata agli oggetti è fondamentale per scrivere codice Java robusto e manutenibile.