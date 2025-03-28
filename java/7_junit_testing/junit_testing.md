# Riassunto del Testing in Java

## Introduzione al Testing

- **Importanza del testing**: Quando si scrive codice, è inevitabile introdurre errori che compromettono il funzionamento del software
- La correttezza di un'applicazione è determinata da quanto riesce a rispettare le specifiche
- Il testing permette di identificare gli errori in modo metodico

## Principio di Pareto nel Testing

- **Regola 80-20**: Circa l'80% dei problemi in un'applicazione deriva dal 20% del codice
- È più efficiente dare priorità ai test delle parti più critiche e utilizzate piuttosto che cercare di testare ogni possibile scenario

## Qualità del Codice Software

La qualità del codice si misura su quattro dimensioni principali:
- **Affidabilità**: comportamento coerente e preciso in diverse condizioni
- **Manutenibilità**: facilità di comprensione, modifica ed estensione del codice
- **Efficienza**: ottimizzazione delle risorse e delle prestazioni
- **Robustezza**: capacità di gestire input inaspettati senza errori critici

## Pratiche per Migliorare la Qualità del Codice

- **Refactoring**: miglioramento della struttura senza modificare il comportamento
- **Code Reviews**: analisi collaborativa del codice
- **Design Patterns**: uso di soluzioni consolidate per problemi comuni
- **Documentazione**: spiegazione del funzionamento e dell'architettura

## Tipologie di Testing

1. **Unit Testing**: test di singole unità o componenti in isolamento
2. **Integration Testing**: verifica dell'interazione tra componenti
3. **Functional Testing**: verifica della funzionalità secondo i requisiti
4. **Regression Testing**: controllo che le modifiche non abbiano compromesso funzionalità esistenti
5. **Performance Testing**: valutazione di reattività, scalabilità e stabilità
6. **Security Testing**: identificazione delle vulnerabilità
7. **Usability Testing**: facilità d'uso e esperienza utente
8. **Compatibility Testing**: funzionamento su diverse piattaforme

## Mocking

- Tecnica per simulare il comportamento di oggetti complessi o dipendenze esterne
- Permette di isolare l'unità di codice da testare
- **Mockito**: framework Java per la creazione di mock objects

```java
// Esempio di utilizzo di Mockito
@Test
public void testGetUserEmail() {
    // Creazione del mock
    UserRepository mockRepo = Mockito.mock(UserRepository.class);
    
    // Definizione del comportamento
    when(mockRepo.findByName("Alice")).thenReturn(new User("Alice", "alice@example.com"));
    
    // Utilizzo del mock
    UserService userService = new UserService(mockRepo);
    String email = userService.getUserEmail("Alice");
    
    // Verifica
    assertEquals("alice@example.com", email);
    verify(mockRepo).findByName("Alice");
}
```

## Test Driven Development (TDD)

Approccio in cui i test vengono scritti prima del codice, seguendo un ciclo iterativo:
1. **Red**: Scrivi un test che fallisce
2. **Green**: Scrivi il codice minimo per far passare il test
3. **Refactor**: Ottimizza il codice mantenendo i test passanti

## Continuous Integration (CI)

- Pratica di sviluppo in cui gli sviluppatori integrano regolarmente il loro lavoro
- Ogni integrazione viene verificata con build e test automatizzati
- Strumenti comuni: Jenkins, GitHub Actions, Travis CI

## Code Coverage

- Metrica che misura la quantità di codice eseguita durante i test
- Tipi principali:
  - **Line Coverage**: percentuale di linee di codice eseguite
  - **Branch Coverage**: percentuale di rami decisionali eseguiti
  - **Method Coverage**: percentuale di metodi chiamati

## JUnit 5

- Framework di test unitario per Java
- **Ciclo di vita dei test**:
  - Setup: `@BeforeAll`, `@BeforeEach`
  - Esecuzione: `@Test`
  - Pulizia: `@AfterEach`, `@AfterAll`

### Principali annotazioni JUnit

- `@Test`: marca un metodo come caso di test
- `@BeforeEach`: eseguito prima di ogni test
- `@AfterEach`: eseguito dopo ogni test
- `@BeforeAll`: eseguito una volta prima di tutti i test (metodo statico)
- `@AfterAll`: eseguito una volta dopo tutti i test (metodo statico)
- `@Disabled`: disabilita un test

### Asserzioni JUnit

```java
assertEquals(expected, actual);    // Verifica uguaglianza
assertTrue(condition);             // Verifica che sia vero
assertNull(actual);                // Verifica che sia null
assertSame(expected, actual);      // Verifica che siano lo stesso oggetto
fail(message);                     // Fallisce il test con messaggio
```

## Best Practices per il Testing

1. **Separazione del codice**: mantenere test in `src/main/test`
2. **Package corrispondenti**: struttura dei test simile al codice sorgente
3. **Test basati su scenari reali**: maggiore rilevanza e comprensione
4. **Nomi significativi**: preferire pattern come `givenRadius_whenCalculateArea_thenReturnArea()`
5. **Una asserzione per test**: verifica di un solo risultato atteso
6. **Test separati**: ogni test verifica uno scenario specifico
7. **Mock per dipendenze esterne**: isolamento dell'unità di test
8. **Pattern AAA**:
   - **Arrange**: preparazione del test
   - **Act**: esecuzione dell'azione da testare
   - **Assert**: verifica dei risultati

```java
@Test
void shouldReturnCorrectSum() {
    // Arrange
    Calculator calculator = new Calculator();
    int a = 5;
    int b = 3;
    
    // Act
    int result = calculator.add(a, b);
    
    // Assert
    assertEquals(8, result);
}
```

Il testing è una pratica fondamentale per garantire la qualità del software, ridurre il debito tecnico e aumentare la fiducia nelle modifiche al codice. Un approccio sistematico al testing, con l'utilizzo di strumenti appropriati come JUnit e Mockito, contribuisce significativamente alla stabilità e manutenibilità del software nel lungo periodo.