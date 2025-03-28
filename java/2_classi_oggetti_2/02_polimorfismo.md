# Polimorfismo in Java: Una Guida Completa

## 🌟 Cos'è il Polimorfismo?

Il polimorfismo è come un supereroe della programmazione: lo stesso eroe può apparire in modi diversi a seconda della situazione!

### Analogia del Mondo Reale 🌍

Immagina un "Musicista":
- Su un palco, può suonare la chitarra
- In una classe, può insegnare musica
- A casa, può comporre canzoni

Proprio come questo musicista, un oggetto in Java può comportarsi diversamente in contesti differenti.

## 📘 Tipi di Polimorfismo

### 1. Polimorfismo di Ereditarietà

```java
// Classe base
class Animale {
    void faVerso() {
        System.out.println("Verso generico");
    }
}

// Sottoclassi specifiche
class Cane extends Animale {
    @Override
    void faVerso() {
        System.out.println("Bau! Bau!");
    }
}

class Gatto extends Animale {
    @Override
    void faVerso() {
        System.out.println("Miao! Miao!");
    }
}

// Esempio di utilizzo
public class EsempioPolimorfismo {
    public static void main(String[] args) {
        Animale[] animali = {
            new Cane(),
            new Gatto(),
            new Animale()
        };

        // Stesso metodo, comportamenti diversi!
        for (Animale animale : animali) {
            animale.faVerso();
        }
    }
}
```

### 2. Polimorfismo con Interfacce

```java
// Interfaccia
interface Pagabile {
    double calcolaPagamento();
}

// Implementazioni diverse
class Lavoratore implements Pagabile {
    private double stipendio;

    public Lavoratore(double stipendio) {
        this.stipendio = stipendio;
    }

    @Override
    public double calcolaPagamento() {
        return stipendio;
    }
}

class Freelancer implements Pagabile {
    private double oreLavorate;
    private double tariffaOraria;

    public Freelancer(double oreLavorate, double tariffaOraria) {
        this.oreLavorate = oreLavorate;
        this.tariffaOraria = tariffaOraria;
    }

    @Override
    public double calcolaPagamento() {
        return oreLavorate * tariffaOraria;
    }
}
```

## 🔍 Casting e Instanceof

```java
class Veicolo {
    void muovi() {
        System.out.println("Il veicolo si muove");
    }
}

class Auto extends Veicolo {
    void guidare() {
        System.out.println("Guido l'auto");
    }
}

public class EsempioCasting {
    public static void main(String[] args) {
        // Upcasting automatico
        Veicolo v = new Auto();
        v.muovi();  // Funziona

        // Downcasting con controllo
        if (v instanceof Auto) {
            Auto auto = (Auto) v;
            auto.guidare();  // Ora posso chiamare metodi specifici di Auto
        }
    }
}
```

## 💡 Vantaggi del Polimorfismo

1. **Flessibilità**: Codice più adattabile
2. **Estensibilità**: Facile aggiungere nuove classi
3. **Manutenibilità**: Struttura del codice più pulita

## 🚨 Insidie da Evitare

### Regole Rigorose di Override

```java
class ClasseBase {
    // Metodo che può essere sovrascritto
    protected void metodoSovrascrivibile() {}
}

class SottoClasse extends ClasseBase {
    // CORRETTO: Stesso modificatore o più accessibile
    @Override
    public void metodoSovrascrivibile() {}
}
```

### Confronto tra Oggetti

```java
class Persona {
    private String nome;

    @Override
    public boolean equals(Object obj) {
        // Controllo del tipo prima del confronto
        if (!(obj instanceof Persona)) return false;
        
        Persona altra = (Persona) obj;
        return this.nome.equals(altra.nome);
    }
}
```

## 🎓 Principi Avanzati

### Binding Dinamico

- Il metodo chiamato viene deciso a runtime
- La JVM sceglie l'implementazione basandosi sul tipo effettivo dell'oggetto

### Quando Usare il Polimorfismo

✅ **USA**:
- Quando hai gerarchie di classi ben definite
- Per creare sistemi flessibili e scalabili

❌ **NON USARE**:
- Per forzare relazioni non naturali tra classi
- Quando la gerarchia diventa troppo complessa

## 🌈 Conclusione

Il polimorfismo non è solo una tecnica di programmazione, è un modo di pensare al codice in modo più dinamico e intelligente!

### Pro Tips
- Mantieni le gerarchie semplici
- Usa interfacce per maggiore flessibilità
- Pensa sempre al "contratto" tra classi