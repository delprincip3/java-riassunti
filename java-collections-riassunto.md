# Riassunto: Collections, List e Generics in Java

## Collections Framework

- Le Collections in Java rappresentano una gerarchia di interfacce e classi che forniscono strutture dati e algoritmi per memorizzare e manipolare gruppi di oggetti
- Superano le limitazioni degli array tradizionali
- Una collezione può essere visualizzata come una "borsa" che contiene diversi oggetti

**Principali interfacce nelle Collections:**
- **Collection**: interfaccia di base per gestire gruppi di oggetti
- **List**: collezione ordinata che permette elementi duplicati
- **Set**: collezione che non permette duplicati
- **Map**: collezione di coppie chiave-valore, dove ogni chiave è unica

**Vantaggi delle Collections:**
- Flessibilità
- Metodi avanzati
- Sicurezza sui tipi
- Migliori prestazioni

**Esempio di importazione delle classi Collections:**
```java
import java.util.List;
import java.util.ArrayList;
import java.util.Set;
import java.util.HashSet;
import java.util.Map;
import java.util.HashMap;
```

## List e ArrayList

**Limitazioni degli array:**
- Dimensione fissa dopo la creazione
- Complicato inserire valori nel mezzo
- Mancanza di metodi avanzati per elaborazione dati

**Esempio di array con dimensione fissa:**
```java
// Array di interi con dimensione fissa 5
int[] numeri = new int[5];
numeri[0] = 10;
// Non è possibile espandere l'array oltre 5 elementi
```

**ArrayList:**
- Implementa l'interfaccia List
- Offre metodi avanzati per accesso, modifica, inserimento e rimozione di elementi basati su posizioni specifiche
- Utilizza un array come struttura dati sottostante, ma con dimensione modificabile

**Esempio di creazione e utilizzo di ArrayList:**
```java
// Creazione di un ArrayList
ArrayList<String> nomi = new ArrayList<>();

// Aggiunta di elementi
nomi.add("Mario");
nomi.add("Luigi");
nomi.add("Giovanna");

// Accesso agli elementi
String primoNome = nomi.get(0);  // "Mario"

// Rimozione di un elemento
nomi.remove(1);  // Rimuove "Luigi"

// Dimensione dell'ArrayList
int dimensione = nomi.size();  // 2
```

**Best practice:**
- Dichiarare il tipo statico come List e il tipo dinamico come ArrayList per:
  - Flessibilità nel cambio di implementazione
  - Adesione ai principi SOLID
  - Maggiore generalità e riutilizzo

**Esempio di dichiarazione con best practice:**
```java
// Dichiarazione utilizzando l'interfaccia List
List<String> nomi = new ArrayList<>();

// In futuro si può cambiare l'implementazione senza modificare il resto del codice
// List<String> nomi = new LinkedList<>();
```

**Caratteristiche di ArrayList:**
- Aggiunta elementi: alloca un nuovo array con capacità maggiore quando necessario
- Accesso elementi: in tempo costante O(1) tramite indice
- Rimozione elementi: può richiedere tempo O(n) nel caso peggiore
- Mantiene l'ordine di inserimento (non ordina automaticamente)

**Esempio di ordinamento di un ArrayList:**
```java
import java.util.Collections;
import java.util.List;
import java.util.ArrayList;

public class EsempioOrdinamento {
    public static void main(String[] args) {
        List<Integer> numeri = new ArrayList<>();
        numeri.add(5);
        numeri.add(2);
        numeri.add(8);
        numeri.add(1);
        
        // Prima dell'ordinamento: [5, 2, 8, 1]
        System.out.println("Prima dell'ordinamento: " + numeri);
        
        // Ordinamento naturale
        Collections.sort(numeri);
        
        // Dopo l'ordinamento: [1, 2, 5, 8]
        System.out.println("Dopo l'ordinamento: " + numeri);
    }
}
```

## LinkedList vs ArrayList

**Esempio di LinkedList:**
```java
import java.util.LinkedList;
import java.util.List;

public class EsempioLinkedList {
    public static void main(String[] args) {
        List<String> linkedList = new LinkedList<>();
        
        // Aggiunta di elementi
        linkedList.add("Primo");
        linkedList.add("Secondo");
        linkedList.add("Terzo");
        
        // Inserimento efficiente all'inizio
        linkedList.add(0, "Nuovo Primo");
        
        // Iterazione sulla lista
        for (String elemento : linkedList) {
            System.out.println(elemento);
        }
    }
}
```

**Quando usare ArrayList:**
- Quando l'accesso casuale agli elementi è prioritario
- Quando inserimenti e rimozioni sono rare
- Quando si conosce approssimativamente la dimensione iniziale

**Quando usare LinkedList:**
- Quando inserimenti e rimozioni sono frequenti
- Quando non serve accesso casuale rapido
- Quando la dimensione della lista varia frequentemente

**Esempio di confronto performance:**
```java
import java.util.ArrayList;
import java.util.LinkedList;
import java.util.List;

public class ConfrontoPerformance {
    public static void main(String[] args) {
        List<Integer> arrayList = new ArrayList<>();
        List<Integer> linkedList = new LinkedList<>();
        
        // Test inserimento all'inizio
        long inizio = System.nanoTime();
        for (int i = 0; i < 100000; i++) {
            arrayList.add(0, i);
        }
        long fine = System.nanoTime();
        System.out.println("Tempo inserimento ArrayList: " + (fine - inizio) / 1000000 + " ms");
        
        inizio = System.nanoTime();
        for (int i = 0; i < 100000; i++) {
            linkedList.add(0, i);
        }
        fine = System.nanoTime();
        System.out.println("Tempo inserimento LinkedList: " + (fine - inizio) / 1000000 + " ms");
        
        // Test accesso casuale
        inizio = System.nanoTime();
        for (int i = 0; i < 100000; i++) {
            arrayList.get(i / 2);
        }
        fine = System.nanoTime();
        System.out.println("Tempo accesso ArrayList: " + (fine - inizio) / 1000000 + " ms");
        
        inizio = System.nanoTime();
        for (int i = 0; i < 100000; i++) {
            linkedList.get(i / 2);
        }
        fine = System.nanoTime();
        System.out.println("Tempo accesso LinkedList: " + (fine - inizio) / 1000000 + " ms");
    }
}
```

## Generics

**Problemi senza generics:**
- Mancanza di sicurezza dei tipi
- Rischio di ClassCastException
- Assenza di controllo statico
- Minore chiarezza del codice

**Esempio senza generics (pre-Java 5):**
```java
import java.util.ArrayList;
import java.util.List;

public class SenzaGenerics {
    public static void main(String[] args) {
        List lista = new ArrayList();
        
        // Possiamo aggiungere qualsiasi tipo di oggetto
        lista.add("Una stringa");
        lista.add(100);  // Un intero (autoboxing)
        lista.add(new Object());
        
        // È necessario un cast esplicito per recuperare gli elementi
        String primoElemento = (String) lista.get(0);  // OK
        
        // Questo causerà un ClassCastException a runtime
        try {
            String secondoElemento = (String) lista.get(1);  // Runtime error!
        } catch (ClassCastException e) {
            System.out.println("Errore di cast: " + e.getMessage());
        }
    }
}
```

**Vantaggi dei generics:**
- Permettono di specificare il tipo di oggetti che una collezione può contenere
- Il compilatore controlla i tipi degli oggetti aggiunti alla collezione
- Prevengono errori di tipo a runtime
- Eliminano la necessità di cast espliciti

**Esempio con generics:**
```java
import java.util.ArrayList;
import java.util.List;

public class ConGenerics {
    public static void main(String[] args) {
        // Specifichiamo che la lista contiene solo String
        List<String> lista = new ArrayList<>();
        
        lista.add("Prima stringa");
        lista.add("Seconda stringa");
        
        // Errore di compilazione, non possiamo aggiungere un intero
        // lista.add(100);  // Errore di compilazione!
        
        // Non è necessario il cast
        String elemento = lista.get(0);
        
        // Iterazione tipizzata
        for (String s : lista) {
            System.out.println(s.toUpperCase());  // Possiamo usare i metodi di String direttamente
        }
    }
}
```

**Esempio di classe generica personalizzata:**
```java
public class Contenitore<T> {
    private T oggetto;
    
    public void setOggetto(T oggetto) {
        this.oggetto = oggetto;
    }
    
    public T getOggetto() {
        return oggetto;
    }
    
    public static void main(String[] args) {
        // Contenitore di String
        Contenitore<String> contenitoreString = new Contenitore<>();
        contenitoreString.setOggetto("Hello World");
        String s = contenitoreString.getOggetto();  // Nessun cast necessario
        
        // Contenitore di Integer
        Contenitore<Integer> contenitoreInteger = new Contenitore<>();
        contenitoreInteger.setOggetto(42);
        int i = contenitoreInteger.getOggetto();  // Nessun cast necessario
    }
}
```
