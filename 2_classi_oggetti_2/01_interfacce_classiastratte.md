# Classi Astratte e Interfacce in Java: Guida Definitiva

## 🏗️ Classi Astratte: Concetti Fondamentali

### 1. Definizione di Classe Astratta

#### Cosa Sono?
- Classi che rappresentano concetti generici non istanziabili
- Utilizzate per raccogliere caratteristiche comuni tra oggetti correlati
- Definite con la parola chiave `abstract`

#### Esempio Pratico
```java
public abstract class Animale {
    // Metodo concreto (implementato)
    public void respira() {
        System.out.println("L'animale respira");
    }
    
    // Metodo astratto (da implementare nelle sottoclassi)
    public abstract void emettiSuono();
}
```

### 2. Caratteristiche Principali

- Non possono essere istanziate direttamente
- Possono contenere:
  - Metodi concreti (con implementazione)
  - Metodi astratti (senza implementazione)
  - Campi e costruttori

### 3. Principio Open/Closed

#### Cosa Significa?
- Aperto per estensioni
- Chiuso per modifiche

#### Esempio
```java
public abstract class Mobilio {
    // Metodo comune non modificabile
    public void mostraMateriale() {
        System.out.println("Materiale: Legno");
    }
    
    // Metodo da implementare nelle sottoclassi
    public abstract void assembla();
}

public class Sedia extends Mobilio {
    @Override
    public void assembla() {
        System.out.println("Assemblaggio sedia");
    }
}
```

## 🤝 Interfacce: Concetti Fondamentali

### 1. Definizione di Interfaccia

#### Cosa Sono?
- Contratti che definiscono comportamenti
- Insieme di metodi che una classe deve implementare
- Modalità per ottenere un tipo di "ereditarietà multipla"

#### Esempio Pratico
```java
public interface Movable {
    // Metodo astratto (implicitamente pubblico)
    void move();
    
    // Metodo di default (Java 8+)
    default void stop() {
        System.out.println("Movimento fermato");
    }
    
    // Metodo statico
    static void descrizione() {
        System.out.println("Interfaccia per oggetti mobili");
    }
}
```

### 2. Tipi di Metodi nelle Interfacce

1. **Metodi Astratti**
   - Solo dichiarazione
   - Devono essere implementati dalle classi

2. **Metodi di Default** (Java 8+)
   - Implementazione predefinita
   - Possono essere sovrascritti

3. **Metodi Statici** (Java 8+)
   - Appartengono all'interfaccia
   - Non modificabili dalle classi che implementano

4. **Metodi Privati** (Java 9+)
   - Utilizzabili internamente
   - Per rifattorizzazione di metodi di default

### 3. Esempio di Implementazione Multipla

```java
public class Smartphone implements Telefono, Fotocamera {
    // Implementa metodi di entrambe le interfacce
    public void chiama() { /* ... */ }
    public void scattaFoto() { /* ... */ }
}
```

## 🔍 Differenze Chiave

### Classi Astratte vs Interfacce

| Caratteristica | Classe Astratta | Interfaccia |
|---------------|-----------------|-------------|
| Istanziazione | Mai | Mai |
| Ereditarietà | Singola | Multipla |
| Metodi | Concreti e astratti | Astratti, default, statici, privati |
| Campi | Può avere campi | Solo costanti |
| Uso Principale | Definire oggetti | Definire comportamenti |

## 💡 Best Practices

1. Usa classi astratte per:
   - Condividere codice tra classi correlate
   - Definire una struttura base con stato comune

2. Usa interfacce per:
   - Definire comportamenti per classi non correlate
   - Implementare "ereditarietà multipla"
   - Garantire contratti specifici

## ⚠️ Avvertenze Comuni

- Non confondere astrazione con implementazione
- Scegliere tra classe astratta e interfaccia in base al contesto
- Mantenere le interfacce snelle e focused
- Usare l'ereditarietà con parsimonia

**Conclusione**: Classi astratte e interfacce sono strumenti potenti per la progettazione orientata agli oggetti, ciascuno con un ruolo specifico nel design del software.