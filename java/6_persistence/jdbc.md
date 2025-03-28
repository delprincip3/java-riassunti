Ecco un riassunto completo del contenuto del PDF su JDBC in formato Markdown:

# Riassunto Java Database Connectivity (JDBC)

## Cos'è JDBC
- **JDBC** (Java Database Connectivity) è un'API fornita da Java per consentire alle applicazioni di interagire con i database relazionali
- Fornisce un'interfaccia standard per eseguire operazioni SQL
- Agisce come un ponte tra le applicazioni Java e i database relazionali
- Permette di scrivere codice indipendente dal database specifico utilizzato

## Vantaggi di JDBC
- **Portabilità**: il codice può funzionare con database diversi senza modifiche
- **Astrazione e indipendenza dal database**: nasconde le complessità dei singoli database
- **Standardizzazione**: offre un modo uniforme di accedere ai dati
- **Compatibilità con SQL**: supporta le istruzioni SQL standard

## Svantaggi di JDBC
- **Gestione manuale delle risorse**: richiede una gestione esplicita delle connessioni
- **Verboso**: necessita di molto codice per operazioni semplici
- **Limitata astrazione con SQL**: le query SQL devono essere scritte manualmente
- **Performance**: può avere overhead rispetto a soluzioni native

## Architettura JDBC
L'architettura JDBC comprende:
1. **API JDBC**: interfacce e classi per interagire con i database
2. **JDBC Driver Manager**: gestisce i driver e fornisce connessioni
3. **JDBC Driver**: componenti software che convertono le richieste Java in un formato comprensibile per il DBMS

## Tipi di Driver JDBC
1. **Driver Tipo 1**: convertono JDBC in ODBC (meno efficienti)
2. **Driver Tipo 2**: scritti in parte in Java e in parte in codice nativo (richiedono librerie sul client)
3. **Driver Tipo 3**: utilizzano un protocollo indipendente dal database per comunicare con un server intermedio
4. **Driver Tipo 4**: completamente scritti in Java, comunicano direttamente con il database (più veloci e utilizzati)

## Interazione con il Database: Passaggi Base
1. **Importare le classi JDBC necessarie**:
   ```java
   import java.sql.Connection;
   import java.sql.DriverManager;
   import java.sql.ResultSet;
   import java.sql.Statement;
   import java.sql.SQLException;
   ```

2. **Caricare il driver**:
   ```java
   try {
       Class.forName("org.postgresql.Driver");
   } catch (ClassNotFoundException e) {
       e.printStackTrace();
   }
   ```

3. **Creare una connessione**:
   ```java
   String url = "jdbc:postgresql://localhost:5432/nome_del_database";
   String username = "nome_utente";
   String password = "password";
   Connection conn = null;
   
   try {
       conn = DriverManager.getConnection(url, username, password);
       System.out.println("Connessione al database PostgreSQL stabilita con successo!");
   } catch (SQLException e) {
       e.printStackTrace();
   } finally {
       // Gestione della chiusura della connessione
   }
   ```

4. **Creare un oggetto Statement**:
   ```java
   Statement stmt = null;
   try {
       stmt = conn.createStatement();
   } catch (SQLException e) {
       e.printStackTrace();
   }
   ```

5. **Eseguire una query**:
   ```java
   String sql = "SELECT * FROM utenti";
   ResultSet rs = null;
   try {
       rs = stmt.executeQuery(sql);
   } catch (SQLException e) {
       e.printStackTrace();
   }
   ```

6. **Elaborare i risultati**:
   ```java
   try {
       while (rs.next()) {
           int id = rs.getInt("id");
           String nome = rs.getString("nome");
           String email = rs.getString("email");
           System.out.println("ID: " + id + ", Nome: " + nome + ", Email: " + email);
       }
   } catch (SQLException e) {
       e.printStackTrace();
   }
   ```

7. **Chiudere le risorse**:
   ```java
   try {
       if (rs != null) rs.close();
       if (stmt != null) stmt.close();
       if (conn != null) conn.close();
       System.out.println("Connessione PostgreSQL chiusa.");
   } catch (SQLException e) {
       e.printStackTrace();
   }
   ```

## Statement vs PreparedStatement

### Statement
- Utilizzato per eseguire query SQL statiche (senza parametri dinamici)
- Meno efficiente: ogni query viene analizzata, compilata e ottimizzata dal DBMS
- Meno sicuro: vulnerabile a SQL injection

**Esempio di SQL Injection con Statement**:
```java
Statement stmt = conn.createStatement();
String userId = "1 OR '1'='1'"; // Input pericoloso dall'utente
String sql = "SELECT * FROM utenti WHERE id = " + userId;
ResultSet rs = stmt.executeQuery(sql);
```

### PreparedStatement
- Estende Statement e viene utilizzato per query SQL precompilate con parametri
- Più efficiente: la query viene compilata una sola volta e può essere eseguita più volte
- Più sicuro: i parametri vengono passati separatamente dalla query, prevenendo SQL injection

**Esempio di PreparedStatement**:
```java
String sql = "SELECT * FROM utenti WHERE id = ?";
PreparedStatement pstmt = conn.prepareStatement(sql);
pstmt.setString(1, userId); // L'input dell'utente viene gestito come parametro
ResultSet rs = pstmt.executeQuery();
```

### Principali interfacce dell'API JDBC
- **Driver**: gestisce le connessioni con il database
- **Connection**: rappresenta una connessione attiva con un database
- **Statement**: esegue comandi SQL statici
- **PreparedStatement**: esegue query SQL precompilate e parametrizzate
- **ResultSet**: rappresenta i dati ottenuti da una query
- **RowSet**: versione più flessibile di ResultSet che supporta operazioni di navigazione e aggiornamento

## Esempio completo di applicazione JDBC

```java
import java.sql.*;

public class JDBCExample {
    public static void main(String[] args) {
        // Parametri di connessione
        String url = "jdbc:postgresql://localhost:5432/testdb";
        String username = "postgres";
        String password = "password";
        
        // Dichiarazione delle risorse
        Connection conn = null;
        PreparedStatement pstmt = null;
        ResultSet rs = null;
        
        try {
            // Caricamento del driver (opzionale nelle versioni recenti di Java)
            Class.forName("org.postgresql.Driver");
            
            // Connessione al database
            conn = DriverManager.getConnection(url, username, password);
            System.out.println("Connessione stabilita con successo!");
            
            // Creazione di una query parametrizzata
            String sql = "SELECT * FROM dipendenti WHERE dipartimento = ? AND stipendio > ?";
            pstmt = conn.prepareStatement(sql);
            
            // Impostazione dei parametri
            pstmt.setString(1, "IT");
            pstmt.setDouble(2, 50000.0);
            
            // Esecuzione della query
            rs = pstmt.executeQuery();
            
            // Elaborazione dei risultati
            while (rs.next()) {
                int id = rs.getInt("id");
                String nome = rs.getString("nome");
                String cognome = rs.getString("cognome");
                double stipendio = rs.getDouble("stipendio");
                
                System.out.printf("ID: %d, Nome: %s %s, Stipendio: %.2f EUR\n", 
                                  id, nome, cognome, stipendio);
            }
            
        } catch (ClassNotFoundException e) {
            System.out.println("Driver PostgreSQL non trovato");
            e.printStackTrace();
        } catch (SQLException e) {
            System.out.println("Errore durante l'interazione con il database");
            e.printStackTrace();
        } finally {
            // Chiusura delle risorse (sempre in ordine inverso rispetto all'apertura)
            try {
                if (rs != null) rs.close();
                if (pstmt != null) pstmt.close();
                if (conn != null) conn.close();
                System.out.println("Risorse rilasciate correttamente");
            } catch (SQLException e) {
                System.out.println("Errore durante la chiusura delle risorse");
                e.printStackTrace();
            }
        }
    }
}
```

## In sintesi
- JDBC fornisce un'interfaccia standardizzata per connettere applicazioni Java a database relazionali
- Il PreparedStatement è preferibile allo Statement per sicurezza ed efficienza
- È importante gestire correttamente le risorse con try-catch-finally o try-with-resources
- Si tratta di un'API fondamentale per lo sviluppo di applicazioni Java che necessitano di persistenza dei dati