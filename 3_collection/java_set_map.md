# Riassunto: Set e Map in Java

## Set

- L'interfaccia `Set` rappresenta una collezione che non può contenere elementi duplicati
- Modella matematicamente il concetto di insieme
- Implementazioni principali: HashSet, LinkedHashSet, TreeSet

### HashSet

- Implementazione più comune dell'interfaccia Set
- Basata su una tabella hash
- Non garantisce alcun ordinamento degli elementi
- Le operazioni di base (aggiunta, rimozione, verifica presenza) hanno complessità O(1)

**Esempio di HashSet:**
```java
import java.util.HashSet;
import java.util.Set;

public class EsempioHashSet {
    public static void main(String[] args) {
        // Creazione di un HashSet
        Set<String> linguaggi = new HashSet<>();
        
        // Aggiunta di elementi
        linguaggi.add("Java");
        linguaggi.add("Python");
        linguaggi.add("C++");
        linguaggi.add("JavaScript");
        
        // Tentativo di aggiungere un duplicato (non verrà aggiunto)
        boolean aggiunto = linguaggi.add("Java");
        System.out.println("Elemento duplicato aggiunto: " + aggiunto);  // Stampa: false
        
        // Verifica della presenza di un elemento
        System.out.println("Contiene 'Python': " + linguaggi.contains("Python"));  // Stampa: true
        
        // Dimensione del set
        System.out.println("Dimensione: " + linguaggi.size());  // Stampa: 4
        
        // Iterazione sugli elementi (ordine non garantito)
        System.out.println("Elementi nel set:");
        for (String linguaggio : linguaggi) {
            System.out.println(linguaggio);
        }
        
        // Rimozione di un elemento
        linguaggi.remove("C++");
        
        // Verifica se il set è vuoto
        System.out.println("Il set è vuoto: " + linguaggi.isEmpty());  // Stampa: false
    }
}
```

### TreeSet

- Implementazione di Set che utilizza una struttura ad albero (Red-Black Tree)
- Gli elementi sono ordinati in modo naturale o tramite un comparatore fornito
- Operazioni di base hanno una complessità O(log n), più lenta rispetto a HashSet

**Esempio di TreeSet:**
```java
import java.util.TreeSet;
import java.util.Set;
import java.util.Comparator;

public class EsempioTreeSet {
    public static void main(String[] args) {
        // TreeSet con ordinamento naturale (alfabetico per le stringhe)
        Set<String> nomiOrdinati = new TreeSet<>();
        
        nomiOrdinati.add("Carlo");
        nomiOrdinati.add("Alice");
        nomiOrdinati.add("Bob");
        nomiOrdinati.add("Diana");
        
        System.out.println("Nomi in ordine alfabetico:");
        for (String nome : nomiOrdinati) {
            System.out.println(nome);  // Stampa: Alice, Bob, Carlo, Diana
        }
        
        // TreeSet con comparatore personalizzato (per lunghezza)
        Set<String> nomiPerLunghezza = new TreeSet<>(new Comparator<String>() {
            @Override
            public int compare(String s1, String s2) {
                // Prima ordina per lunghezza
                int lunghezzaComparison = Integer.compare(s1.length(), s2.length());
                // Se la lunghezza è uguale, ordina alfabeticamente
                return lunghezzaComparison != 0 ? lunghezzaComparison : s1.compareTo(s2);
            }
        });
        
        nomiPerLunghezza.add("Carlo");
        nomiPerLunghezza.add("Alice");
        nomiPerLunghezza.add("Bob");
        nomiPerLunghezza.add("Diana");
        nomiPerLunghezza.add("Eva");
        
        System.out.println("\nNomi ordinati per lunghezza e poi alfabeticamente:");
        for (String nome : nomiPerLunghezza) {
            System.out.println(nome);  // Stampa: Bob, Eva, Alice, Carlo, Diana
        }
    }
}
```

**Esempio con classi personalizzate:**
```java
import java.util.TreeSet;
import java.util.Set;

public class EsempioTreeSetOggetti {
    static class Studente implements Comparable<Studente> {
        private int matricola;
        private String nome;
        
        public Studente(int matricola, String nome) {
            this.matricola = matricola;
            this.nome = nome;
        }
        
        @Override
        public int compareTo(Studente altro) {
            // Ordinamento basato sulla matricola
            return Integer.compare(this.matricola, altro.matricola);
        }
        
        @Override
        public String toString() {
            return "Studente{matricola=" + matricola + ", nome='" + nome + "'}";
        }
    }
    
    public static void main(String[] args) {
        Set<Studente> studenti = new TreeSet<>();
        
        studenti.add(new Studente(103, "Carlo"));
        studenti.add(new Studente(101, "Alice"));
        studenti.add(new Studente(102, "Bob"));
        
        System.out.println("Studenti ordinati per numero di matricola:");
        for (Studente s : studenti) {
            System.out.println(s);  // Ordinati per matricola: 101, 102, 103
        }
    }
}
```

## Map

- L'interfaccia `Map` rappresenta una collezione di coppie chiave-valore dove le chiavi non possono essere duplicate
- Implementazioni principali: HashMap, TreeMap

### HashMap

- Implementazione più comune dell'interfaccia Map
- Basata su una tabella hash
- Non garantisce alcun ordinamento delle chiavi
- Le operazioni di base (aggiunta, rimozione, verifica presenza) hanno complessità O(1)

**Esempio di HashMap:**
```java
import java.util.HashMap;
import java.util.Map;

public class EsempioHashMap {
    public static void main(String[] args) {
        // Creazione di una HashMap
        Map<String, Integer> eta = new HashMap<>();
        
        // Aggiunta di coppie chiave-valore
        eta.put("Alice", 25);
        eta.put("Bob", 30);
        eta.put("Carlo", 22);
        
        // Accesso a un valore tramite chiave
        System.out.println("Età di Bob: " + eta.get("Bob"));  // Stampa: 30
        
        // Aggiornamento di un valore
        eta.put("Alice", 26);
        System.out.println("Nuova età di Alice: " + eta.get("Alice"));  // Stampa: 26
        
        // Verifica dell'esistenza di una chiave
        System.out.println("Contiene 'Diana': " + eta.containsKey("Diana"));  // Stampa: false
        
        // Verifica dell'esistenza di un valore
        System.out.println("Contiene età 22: " + eta.containsValue(22));  // Stampa: true
        
        // Dimensione della mappa
        System.out.println("Numero di persone: " + eta.size());  // Stampa: 3
        
        // Iterazione sulle chiavi
        System.out.println("\nPersone presenti:");
        for (String nome : eta.keySet()) {
            System.out.println(nome);
        }
        
        // Iterazione sui valori
        System.out.println("\nEtà presenti:");
        for (Integer anni : eta.values()) {
            System.out.println(anni);
        }
        
        // Iterazione sulle coppie chiave-valore
        System.out.println("\nPersone e relative età:");
        for (Map.Entry<String, Integer> entry : eta.entrySet()) {
            System.out.println(entry.getKey() + ": " + entry.getValue() + " anni");
        }
        
        // Rimozione di una coppia tramite chiave
        eta.remove("Carlo");
        System.out.println("\nDopo la rimozione di Carlo, dimensione: " + eta.size());  // Stampa: 2
        
        // Verifica se la mappa è vuota
        System.out.println("La mappa è vuota: " + eta.isEmpty());  // Stampa: false
    }
}
```

**Esempio pratico di utilizzo di Map:**
```java
import java.util.HashMap;
import java.util.Map;

public class ConteggioParole {
    public static void main(String[] args) {
        String testo = "Java è un linguaggio di programmazione orientato agli oggetti. " +
                        "Java è utilizzato per sviluppare applicazioni desktop, web e mobile. " +
                        "Java è uno dei linguaggi più popolari al mondo.";
        
        // Dividiamo il testo in parole
        String[] parole = testo.split("\\s+");
        
        // Mappa per contare le occorrenze di ciascuna parola
        Map<String, Integer> conteggio = new HashMap<>();
        
        // Conteggio delle parole
        for (String parola : parole) {
            // Rimuoviamo la punteggiatura e convertiamo in minuscolo
            parola = parola.replaceAll("[^a-zA-Z]", "").toLowerCase();
            
            // Aggiorniamo il conteggio
            if (!parola.isEmpty()) {
                // Se la parola esiste, incrementa il conteggio, altrimenti imposta a 1
                conteggio.put(parola, conteggio.getOrDefault(parola, 0) + 1);
            }
        }
        
        // Stampa dei risultati
        System.out.println("Conteggio delle parole:");
        for (Map.Entry<String, Integer> entry : conteggio.entrySet()) {
            System.out.println(entry.getKey() + ": " + entry.getValue());
        }
    }
}
```

**Esempio di TreeMap:**
```java
import java.util.TreeMap;
import java.util.Map;

public class EsempioTreeMap {
    public static void main(String[] args) {
        // TreeMap ordina le chiavi naturalmente (alfabeticamente per le stringhe)
        Map<String, Integer> punteggi = new TreeMap<>();
        
        punteggi.put("Carlo", 85);
        punteggi.put("Alice", 92);
        punteggi.put("Bob", 78);
        punteggi.put("Diana", 90);
        
        System.out.println("Punteggi in ordine alfabetico:");
        for (Map.Entry<String, Integer> entry : punteggi.entrySet()) {
            System.out.println(entry.getKey() + ": " + entry.getValue());
            // Stampa: Alice, Bob, Carlo, Diana (in ordine alfabetico)
        }
    }
}
```