
# Fondamentali di Java - Riassunto Completo

## 1. Introduzione a Java

### Cos'è Java
Java è un linguaggio di programmazione orientato agli oggetti, sviluppato da Sun Microsystems (ora di proprietà di Oracle). È progettato per essere indipendente dalla piattaforma grazie al concetto "write once, run anywhere" (scrivi una volta, esegui ovunque).

### Caratteristiche principali
- **Orientato agli oggetti**: Tutto in Java è un oggetto, eccetto i tipi primitivi
- **Indipendente dalla piattaforma**: Il codice Java viene compilato in bytecode che può essere eseguito su qualsiasi dispositivo con la Java Virtual Machine (JVM)
- **Robusto**: Java include gestione automatica della memoria (garbage collection) e controllo dei tipi
- **Sicuro**: La JVM fornisce un ambiente di esecuzione sicuro

### Struttura di un programma Java
```java
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello, World!");
    }
}
```

- **Classe**: Unità fondamentale in Java (nell'esempio, `HelloWorld`)
- **Metodo main**: Punto di ingresso dell'applicazione
- **Istruzioni**: Comandi eseguiti dal programma

### JDK, JRE e JVM
- **JDK** (Java Development Kit): Strumenti per sviluppare applicazioni Java
- **JRE** (Java Runtime Environment): Ambiente per eseguire applicazioni Java
- **JVM** (Java Virtual Machine): Macchina virtuale che esegue il bytecode Java

## 2. Variabili e Operatori

### Variabili
Una variabile è un contenitore per memorizzare dati. In Java, ogni variabile ha un tipo specifico.

```java
// Dichiarazione e inizializzazione di una variabile
int numero = 10;
String nome = "Mario";
```

### Tipi di dati primitivi
- **byte**: 8 bit, da -128 a 127
- **short**: 16 bit, da -32,768 a 32,767
- **int**: 32 bit, da -2^31 a 2^31-1
- **long**: 64 bit, da -2^63 a 2^63-1
- **float**: 32 bit, numeri a virgola mobile
- **double**: 64 bit, numeri a virgola mobile
- **char**: 16 bit, carattere Unicode
- **boolean**: true o false

### Tipi di dati riferimento
- **Classi**: String, Array, classi personalizzate
- **Interfacce**
- **Array**

### Operatori in Java
- **Aritmetici**: +, -, *, /, %
- **Relazionali**: ==, !=, >, <, >=, <=
- **Logici**: &&, ||, !
- **Assegnazione**: =, +=, -=, *=, /=, %=
- **Incremento/Decremento**: ++, --
- **Bitwise**: &, |, ^, ~, <<, >>, >>>

```java
int a = 10;
int b = 20;
int somma = a + b;       // Operatore aritmetico
boolean confronto = a < b; // Operatore relazionale
```

## 3. Metodi

### Definizione di metodo
Un metodo è un blocco di codice che esegue una specifica operazione.

```java
// Struttura di un metodo
tipoRitorno nomeMetodo(parametri) {
    // corpo del metodo
    return valore; // se il tipo di ritorno non è void
}
```

### Esempio di metodo
```java
public int somma(int a, int b) {
    return a + b;
}
```

### Parametri e argomenti
- **Parametri**: Variabili nella definizione del metodo
- **Argomenti**: Valori passati quando si chiama il metodo

### Overloading dei metodi
Creazione di metodi con lo stesso nome ma parametri diversi.

```java
public int somma(int a, int b) {
    return a + b;
}

public double somma(double a, double b) {
    return a + b;
}
```

### Metodi statici vs non statici
- **Statici**: Appartengono alla classe, non all'oggetto
- **Non statici**: Appartengono all'istanza dell'oggetto

## 4. Strutture di Controllo

### Condizionali
- **if-else**: Esegue codice basato su una condizione
  ```java
  if (condizione) {
      // codice se condizione è vera
  } else {
      // codice se condizione è falsa
  }
  ```

- **switch**: Seleziona uno tra diversi blocchi di codice
  ```java
  switch (espressione) {
      case valore1:
          // codice
          break;
      case valore2:
          // codice
          break;
      default:
          // codice default
  }
  ```

### Cicli
- **for**: Ciclo con contatore
  ```java
  for (int i = 0; i < 10; i++) {
      // codice da ripetere
  }
  ```

- **while**: Ciclo che continua finché la condizione è vera
  ```java
  while (condizione) {
      // codice da ripetere
  }
  ```

- **do-while**: Simile al while ma esegue il codice almeno una volta
  ```java
  do {
      // codice da ripetere
  } while (condizione);
  ```

- **for-each**: Ciclo specifico per collezioni
  ```java
  for (Tipo elemento : collezione) {
      // codice da ripetere
  }
  ```

## 5. Input con Scanner

### La classe Scanner
```java
import java.util.Scanner;

public class InputExample {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        
        System.out.print("Inserisci il tuo nome: ");
        String nome = scanner.nextLine();
        
        System.out.print("Inserisci la tua età: ");
        int eta = scanner.nextInt();
        
        System.out.println("Ciao " + nome + ", hai " + eta + " anni.");
        
        scanner.close(); // Importante chiudere lo scanner
    }
}
```

### Metodi principali di Scanner
- **nextLine()**: Legge una riga di testo
- **next()**: Legge una parola
- **nextInt()**: Legge un intero
- **nextDouble()**: Legge un numero a virgola mobile
- **nextBoolean()**: Legge un booleano

### Gestione degli input errati
```java
try {
    int numero = scanner.nextInt();
} catch (InputMismatchException e) {
    System.out.println("Input non valido!");
    scanner.next(); // Pulisce lo scanner
}
```

## 6. Tipi di Errori e Debugging

### Tipi di errori
- **Errori di sintassi**: Errori nel codice che impediscono la compilazione
- **Errori di runtime**: Errori che si verificano durante l'esecuzione
- **Errori logici**: Il programma funziona ma non produce i risultati attesi

### Debugging
- **Uso di System.out.println()**: Mostra valori durante l'esecuzione
- **Debugger IDE**: Strumento potente per seguire l'esecuzione passo-passo
- **Try-catch**: Gestione delle eccezioni

### Eccezioni comuni
- **NullPointerException**: Tentativo di utilizzare un riferimento null
- **ArrayIndexOutOfBoundsException**: Accesso a un indice di array non valido
- **ArithmeticException**: Operazione aritmetica non valida (es. divisione per zero)
- **InputMismatchException**: Input non corrispondente al tipo atteso

## 7. Array

### Definizione e inizializzazione
```java
// Dichiarazione
int[] numeri;

// Inizializzazione
numeri = new int[5]; // Array di 5 interi

// Dichiarazione e inizializzazione combinata
int[] numeri = {1, 2, 3, 4, 5};
```

### Accesso agli elementi
```java
int primo = numeri[0]; // Accesso al primo elemento
numeri[1] = 10;        // Modifica del secondo elemento
```

### Array multidimensionali
```java
// Dichiarazione di una matrice 3x3
int[][] matrice = new int[3][3];

// Inizializzazione diretta
int[][] matrice = {
    {1, 2, 3},
    {4, 5, 6},
    {7, 8, 9}
};
```

### Operazioni comuni sugli array
- **Iterazione**:
  ```java
  for (int i = 0; i < numeri.length; i++) {
      System.out.println(numeri[i]);
  }
  
  // Oppure con for-each
  for (int numero : numeri) {
      System.out.println(numero);
  }
  ```

- **Ricerca**:
  ```java
  boolean trovato = false;
  for (int numero : numeri) {
      if (numero == valoreDaCercare) {
          trovato = true;
          break;
      }
  }
  ```

- **Ordinamento**:
  ```java
  import java.util.Arrays;
  
  Arrays.sort(numeri); // Ordinamento in-place
  ```

### Vantaggi e limiti degli array
- **Vantaggi**: Accesso rapido agli elementi, efficienza di memoria
- **Limiti**: Dimensione fissa, difficoltà nell'inserire/rimuovere elementi

---

