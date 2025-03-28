
# Il Pattern DAO (Data Access Object) in Java

## Definizione e Scopo
Il Data Access Object (DAO) è un pattern di progettazione utilizzato per separare la logica di accesso ai dati dalla logica di business. Fornisce un'interfaccia astratta per accedere a specifici tipi di dati, rendendo il codice più modulare e testabile.

## Obiettivo Principale
Fornire un'interfaccia di astrazione tra l'applicazione e le operazioni di persistenza dei dati, permettendo al codice di business di interagire con i dati senza conoscere i dettagli dell'implementazione della persistenza.

## Vantaggi

### 1. Isolamento della Logica di Accesso ai Dati
L'accesso ai dati è incapsulato all'interno di classi DAO dedicate, rendendo il codice dell'applicazione principale indipendente dalla tecnologia di persistenza utilizzata (JDBC, JPA, Hibernate, ecc.).

**Esempio**: 
```java
// La classe business non sa nulla dei dettagli di connessione al DB
public class UserService {
    private UserDao userDao; // Usa solo l'interfaccia
    
    public User findUserByEmail(String email) {
        return userDao.getUserByEmail(email); // Non è necessario sapere come viene implementata la ricerca
    }
}
```

### 2. Facilitazione del Cambiamento del Database
Poiché il DAO separa la logica di accesso ai dati dal resto dell'applicazione, è possibile cambiare la sorgente di dati (es. da MySQL a Oracle) senza dover modificare il codice di business.

**Esempio**: 
```java
// Implementazione con MySQL
public class MySqlUserDao implements UserDao {
    @Override
    public User getUserByEmail(String email) {
        // Usa driver MySQL e query specifiche per MySQL
    }
}

// Implementazione con Oracle
public class OracleUserDao implements UserDao {
    @Override
    public User getUserByEmail(String email) {
        // Usa driver Oracle e query specifiche per Oracle
    }
}

// Il codice business rimane invariato indipendentemente dall'implementazione utilizzata
```

### 3. Miglioramento della Testabilità
Le classi DAO possono essere facilmente sostituite con versioni mock o stub durante i test, rendendo più semplice il testing isolato della logica di business.

**Esempio**: 
```java
// Mock per i test
public class MockUserDao implements UserDao {
    private List<User> fakeUsers = new ArrayList<>();
    
    public MockUserDao() {
        // Popola con dati di test
        fakeUsers.add(new User("test@example.com", "Test User"));
    }
    
    @Override
    public User getUserByEmail(String email) {
        return fakeUsers.stream()
                .filter(u -> u.getEmail().equals(email))
                .findFirst()
                .orElse(null);
    }
}

// Test della business logic
@Test
public void testUserService() {
    UserService service = new UserService(new MockUserDao());
    User user = service.findUserByEmail("test@example.com");
    assertNotNull(user);
    assertEquals("Test User", user.getUsername());
}
```

## Struttura del Pattern DAO

Il pattern DAO è composto generalmente da tre componenti principali:

### 1. DAO Interface
Definisce le operazioni CRUD (Create, Read, Update, Delete) che possono essere eseguite su un'entità.

```java
public interface UserDao {
    List<User> getAllUsers();
    User getUserByEmail(String email);
    void saveUser(User user);
    void deleteUser(User user);
}
```

### 2. DAO Implementation
Implementazione concreta dei metodi definiti nell'interfaccia DAO. Contiene la logica specifica per interagire con il database o con altre sorgenti di dati.

```java
public class UserDaoImpl implements UserDao {
    private List<User> users;
    
    public UserDaoImpl() {
        users = new ArrayList<>();
    }
    
    @Override
    public List<User> getAllUsers() {
        return users;
    }
    
    @Override
    public User getUserByEmail(String email) {
        for (User user : users) {
            if (user.getEmail().equals(email)) {
                return user;
            }
        }
        return null;
    }
    
    @Override
    public void saveUser(User user) {
        users.add(user);
    }
    
    @Override
    public void deleteUser(User user) {
        users.remove(user);
    }
}
```

### 3. Model (o Entity)
Rappresenta l'oggetto su cui verranno eseguite le operazioni CRUD, tipicamente una classe POJO (Plain Old Java Object).

```java
public class User {
    private String username;
    private String email;
    
    public User(String username, String email) {
        this.username = username;
        this.email = email;
    }
    
    // Getters e Setters
    
    @Override
    public String toString() {
        return "User{" +
            "username='" + username + '\'' +
            ", email='" + email + '\'' +
            '}';
    }
}
```

## Esempio di Utilizzo Completo

```java
// Client code - Main application
public class Main {
    public static void main(String[] args) {
        // Creazione dell'implementazione del DAO
        UserDao userDao = new UserDaoImpl();
        
        // Creazione di utenti
        User user1 = new User("Mario Rossi", "mario@example.com");
        User user2 = new User("Luigi Verdi", "luigi@example.com");
        
        // Salvataggio degli utenti
        userDao.saveUser(user1);
        userDao.saveUser(user2);
        
        // Recupero di tutti gli utenti
        System.out.println("Tutti gli utenti:");
        List<User> users = userDao.getAllUsers();
        for (User user : users) {
            System.out.println(user);
        }
        
        // Ricerca di un utente per email
        System.out.println("\nRicerca utente per email:");
        User foundUser = userDao.getUserByEmail("mario@example.com");
        System.out.println(foundUser);
        
        // Eliminazione di un utente
        System.out.println("\nEliminazione utente:");
        userDao.deleteUser(user1);
        
        // Verifica dell'eliminazione
        System.out.println("\nUtenti dopo l'eliminazione:");
        users = userDao.getAllUsers();
        for (User user : users) {
            System.out.println(user);
        }
    }
}
```

## Applicazioni Pratiche
- Applicazioni web con persistenza dei dati
- Sistemi di gestione database
- Applicazioni enterprise con accesso a più sorgenti dati
- Sistemi che devono supportare diversi tipi di database

## Estensioni e Varianti
- **Factory Pattern**: Spesso usato insieme al DAO per creare le implementazioni concrete dei DAO
- **Singleton Pattern**: Le implementazioni DAO possono essere singleton per evitare connessioni multiple
- **Repository Pattern**: Una variante più moderna che può sostituire o integrare il DAO

## Considerazioni per l'Implementazione
- Definire interfacce chiare e coerenti
- Gestire correttamente le eccezioni dei database
- Considerare l'uso di pool di connessioni per migliorare le prestazioni
- Implementare il pattern transaction quando necessario per garantire l'integrità dei dati

Il Pattern DAO è fondamentale nelle applicazioni Java enterprise per mantenere una chiara separazione tra business logic e data access, facilitando manutenzione, test e riutilizzo del codice.