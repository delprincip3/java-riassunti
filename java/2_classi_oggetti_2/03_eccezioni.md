# Eccezioni in Java - Riassunto

## Concetti Fondamentali

Un'eccezione è un evento che si verifica durante l'esecuzione di un programma e interrompe il normale flusso delle istruzioni. Quando si verifica un'eccezione, Java genera un oggetto di tipo `Exception` che contiene informazioni sull'errore. Se l'eccezione non viene gestita correttamente, il programma terminerà in modo anomalo.

Le eccezioni sono eventi eccezionali che non dovrebbero verificarsi regolarmente durante il normale corso delle operazioni. La gestione delle eccezioni permette di:
- Fornire feedback all'utente sull'errore
- Prendere un'azione alternativa

## Meccanismi di Gestione delle Eccezioni

### 1. Try-Catch

```java
try {
    // Codice che potrebbe generare un'eccezione
} catch (TipoEccezione e) {
    // Codice per gestire l'eccezione
}
```

**Esempio**:
```java
public class DivisionExample {
    public static void main(String[] args) {
        try {
            int a = 10;
            int b = 0;
            int result = a / b;  // Questo genererà un'eccezione
            System.out.println("Il risultato è: " + result);
        } catch (ArithmeticException e) {
            System.out.println("Errore: Divisione per zero non consentita.");
        }
    }
}
```

### 2. Gestione di Più Eccezioni

È possibile gestire più tipi di eccezioni utilizzando più blocchi `catch`. L'ordine dei blocchi catch è importante: devono essere ordinati dal più specifico al più generico, altrimenti il compilatore darà errore.

```java
try {
    int[] numbers = {1, 2, 3};
    int result = numbers[5] / 0;
} catch (ArrayIndexOutOfBoundsException e) {
    System.out.println("Errore: Indice dell'array fuori limite.");
} catch (ArithmeticException e) {
    System.out.println("Errore: Divisione per zero non consentita.");
}
```

### 3. Finally

Il blocco `finally` viene utilizzato per eseguire codice che deve essere eseguito sempre, indipendentemente dal fatto che si sia verificata un'eccezione o meno.

```java
try {
    // Codice che potrebbe generare un'eccezione
} catch (Exception e) {
    // Codice per gestire l'eccezione
} finally {
    // Codice che verrà eseguito sempre
}
```

Il blocco `finally` è particolarmente utile per:
- Liberare risorse
- Chiudere file
- Gestire operazioni di logging
- Altre operazioni di pulizia

Il codice nel blocco `finally` viene eseguito sempre, anche se nel blocco `try` si verifica un'eccezione e viene catturata, o se il blocco `catch` genera un'altra eccezione.

L'unico caso in cui il blocco `finally` potrebbe non essere eseguito è se il programma termina in modo anomalo (es. con `System.exit()`) o con un'interruzione dell'esecuzione.

## Lancio di Eccezioni

### 1. Throws

La parola chiave `throws` viene utilizzata nella dichiarazione di un metodo per indicare che il metodo può generare una o più eccezioni, delegando al chiamante la gestione dell'eccezione.

```java
public void metodoCheLanciaEccezione() throws IOException {
    // Codice che potrebbe generare un'eccezione di tipo IOException
}
```

### 2. Throw

La parola chiave `throw` viene utilizzata per generare manualmente un'eccezione all'interno di un blocco di codice.

```java
public class CustomExceptionExample {
    public void checkAge(int age) {
        if (age < 18) {
            throw new IllegalArgumentException("Età inferiore a 18 anni non consentita");
        } else {
            System.out.println("Accesso consentito");
        }
    }
}
```

## Gerarchia delle Eccezioni

In Java, tutte le eccezioni fanno parte di una gerarchia di classi che eredita dalla classe `Throwable`. La gerarchia determina come le eccezioni vengono gestite e propagate.

- `Throwable` (classe base)
  - `Error`: errori gravi e irrecuperabili (es. `OutOfMemoryError`)
  - `Exception`: eccezioni recuperabili
    - `RuntimeException` e sottoclassi: eccezioni non verificate
    - Altre sottoclassi di `Exception`: eccezioni verificate

### Eccezioni Verificate e Non Verificate

1. **Eccezioni Verificate**: sottoclassi di `Exception` che non sono sottoclassi di `RuntimeException`
   - Il compilatore richiede che vengano gestite esplicitamente o dichiarate con `throws`
   - Esempio: `IOException`, `SQLException`

```java
public void leggiFile(String filePath) throws IOException {
    BufferedReader reader = new BufferedReader(new FileReader(filePath));
    String line = reader.readLine();
    reader.close();
}
```

2. **Eccezioni Non Verificate**: sottoclassi di `RuntimeException`
   - Non richiedono di essere gestite esplicitamente
   - Il compilatore non obbliga a catturarle o dichiararle
   - Esempio: `ArithmeticException`, `NullPointerException`

```java
public int dividi(int a, int b) {
    return a / b;  // Può generare ArithmeticException
}
```

## Best Practices

1. **Cattura eccezioni specifiche**: evita di catturare tutte le eccezioni con `catch(Exception e)`, preferisci catturare i tipi specifici di eccezioni che possono verificarsi.

2. **Non ignorare le eccezioni**: non lasciare mai vuoti i blocchi `catch`. Se non puoi gestire l'eccezione, almeno registrala con un messaggio di log.

```java
try {
    // Codice che può generare un'eccezione
} catch (IOException e) {
    e.printStackTrace();  // Registra l'errore per il debug
}
```

3. **Non usare le eccezioni per il flusso normale**: le eccezioni devono essere usate per situazioni eccezionali, non per controllare il flusso regolare dell'applicazione.

```java
// SBAGLIATO
try {
    int result = Integer.parseInt("abc");  // Genera NumberFormatException
} catch (NumberFormatException e) {
    result = 0;  // Non usare le eccezioni per la logica del programma
}

// CORRETTO
if (isNumeric(input)) {
    result = Integer.parseInt(input);
} else {
    result = 0;
}
```

## Eccezioni Custom

Java permette di creare eccezioni personalizzate estendendo la classe `Exception` o `RuntimeException`. Questo è utile quando vuoi definire condizioni di errore specifiche per la tua applicazione.

```java
// Eccezione verificata (estende Exception)
class SaldoInsufficienteException extends Exception {
    public SaldoInsufficienteException(String messaggio) {
        super(messaggio);
    }
}

// Uso dell'eccezione custom
public void preleva(double importo) throws SaldoInsufficienteException {
    if (saldo < importo) {
        throw new SaldoInsufficienteException("Saldo insufficiente per prelevare " + importo);
    }
    saldo -= importo;
}
```

Si può anche creare un'eccezione non verificata estendendo `RuntimeException`:

```java
class ConfigurazioneMissingException extends RuntimeException {
    public ConfigurazioneMissingException(String messaggio) {
        super(messaggio);
    }
}
```

## Conclusione

La gestione delle eccezioni in Java permette di creare applicazioni più robuste che possono affrontare situazioni inaspettate in modo elegante. Utilizzando correttamente i meccanismi `try-catch-finally`, `throws`, `throw` e conoscendo la gerarchia delle eccezioni, si può migliorare significativamente la qualità del codice e l'esperienza dell'utente.