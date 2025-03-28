# Il Pattern MVC (Model-View-Controller) in Java

## Definizione e Scopo
L'MVC (Model, View, e Controller) è un pattern architetturale utilizzato per organizzare il codice in layer specifici, rendendo il processo di sviluppo più gestibile e modulare grazie alla separazione delle responsabilità (Separation of Concerns, SoC).

## I Tre Componenti Principali dell'MVC

### 1. Model
- Rappresenta i dati e la logica di business dell'applicazione
- È un oggetto o un POJO (Plain Old Java Object) che incapsula i dati e la logica per gestirli
- Non si occupa direttamente di come i dati vengono presentati all'utente
- Funge da "cervello" dell'applicazione

**Cosa troviamo all'interno di questo layer:**
- Classi in grado di connettersi al database e interagire con esso
- Classi che mappano le entità del database su oggetti
- Classi che mappano funzionalità specifiche dell'applicazione

**Esempio:**
```java
public class Task {
    private int id;
    private String description;
    private boolean completed;
    
    public Task(int id, String description) {
        this.id = id;
        this.description = description;
        this.completed = false;
    }
    
    // Getters e setters
    public int getId() { return id; }
    public String getDescription() { return description; }
    public boolean isCompleted() { return completed; }
    public void setCompleted(boolean completed) { this.completed = completed; }
}
```

### 2. View
- Gestisce la presentazione dei dati
- Responsabile del rendering dell'interfaccia utente (UI) e della visualizzazione delle informazioni contenute nel Model
- Riceve i dati dal Controller e li mostra all'utente
- Non contiene logica di business o manipolazione dei dati

**Cosa troviamo all'interno di questo layer:**
- Codice che gestisce una galleria di immagini nella nostra app web
- Codice che cambia il colore di un punto in verde dopo il completamento del caricamento di un file
- Codice che ordina l'elenco degli ordini di un utente per prezzo

**Esempio:**
```java
public class TaskView {
    public void showTasks(List<Task> tasks) {
        System.out.println("ELENCO DELLE ATTIVITÀ:");
        for (Task task : tasks) {
            String status = task.isCompleted() ? "[✓]" : "[ ]";
            System.out.println(status + " " + task.getDescription());
        }
    }
    
    public void showMessage(String message) {
        System.out.println("Messaggio: " + message);
    }
}
```

### 3. Controller
- Intermediario tra layer Model e View
- Responsabile della ricezione dei comandi dell'utente e della loro implementazione
- Controlla il flusso di dati dall'utente (attraverso la View) al Model e aggiorna la vista quando i dati cambiano
- Funge da ponte tra la vista e il modello

**Cosa troviamo all'interno di questo layer:**
- Codice che cattura un evento accaduto su una pagina web
- Codice che verifica la correttezza dell'input inviato dall'utente
- Codice che determina quale codice di stato HTTP restituire

**Esempio:**
```java
public class TaskController {
    private TaskModel model;
    private TaskView view;
    
    public TaskController(TaskModel model, TaskView view) {
        this.model = model;
        this.view = view;
    }
    
    public void addTask(String description) {
        if (description != null && !description.trim().isEmpty()) {
            model.addTask(new Task(model.getNextId(), description));
            view.showMessage("Attività aggiunta con successo!");
        } else {
            view.showMessage("Errore: la descrizione non può essere vuota");
        }
    }
    
    public void markTaskAsCompleted(int taskId) {
        boolean success = model.markTaskAsCompleted(taskId);
        if (success) {
            view.showMessage("Attività completata!");
        } else {
            view.showMessage("Errore: attività non trovata");
        }
    }
    
    public void showAllTasks() {
        List<Task> tasks = model.getAllTasks();
        view.showTasks(tasks);
    }
}
```

## Caso d'Uso: Applicazione Todo List
Immagina di creare un'app di lista delle cose da fare. Questa app permetterà agli utenti di creare attività e organizzarle in elenchi:

- **Model**: Nel modello di un'app todo potrebbe definire cosa sia un "compito" e che una "lista" è una raccolta di compiti
- **View**: Il codice della vista definirà l'aspetto visivo dei todos e delle liste. I compiti potrebbero avere un carattere grande o essere di un certo colore
- **Controller**: Il controller potrebbe definire come un utente aggiunge un compito o ne segna un altro come completato, collegando il pulsante di aggiunta della vista al modello

## Applicazione in Scenari Web Moderni

In un server moderno che espone servizi API, a cui si collegano applicazioni web/mobile, non abbiamo più codice client e server misto all'interno dello stesso progetto:

- Il componente "Controller" conterrà il codice per gli endpoint del server
- Il componente "View" risiederà interamente sul lato client
- La comunicazione tra Model e View avviene necessariamente attraverso i controller

```java
// Controller (lato server)
@RestController
@RequestMapping("/api/tasks")
public class TaskApiController {
    @Autowired
    private TaskService taskService;
    
    @GetMapping
    public List<TaskDto> getAllTasks() {
        return taskService.findAllTasks();
    }
    
    @PostMapping
    public ResponseEntity<TaskDto> createTask(@RequestBody TaskDto taskDto) {
        TaskDto created = taskService.createTask(taskDto);
        return ResponseEntity.status(HttpStatus.CREATED).body(created);
    }
}

// View (lato client, es. JavaScript/React)
function TaskList() {
    const [tasks, setTasks] = useState([]);
    
    useEffect(() => {
        fetch('/api/tasks')
            .then(response => response.json())
            .then(data => setTasks(data));
    }, []);
    
    return (
        <div>
            <h2>Elenco Attività</h2>
            <ul>
                {tasks.map(task => (
                    <li key={task.id}>
                        {task.completed ? '✓' : '○'} {task.description}
                    </li>
                ))}
            </ul>
        </div>
    );
}
```

## Il Service Layer
Il layer di servizio è un'aggiunta all'architettura MVC che introduce un ulteriore livello di astrazione tra il Controller e il Model:

- Responsabile dell'implementazione della logica di business dell'applicazione
- Centralizza la logica che potrebbe essere utilizzata da più controller

```java
@Service
public class TaskService {
    @Autowired
    private TaskRepository taskRepository;
    
    public List<TaskDto> findAllTasks() {
        return taskRepository.findAll().stream()
                .map(this::convertToDto)
                .collect(Collectors.toList());
    }
    
    public TaskDto createTask(TaskDto taskDto) {
        Task task = new Task();
        task.setDescription(taskDto.getDescription());
        task.setCompleted(false);
        
        Task saved = taskRepository.save(task);
        return convertToDto(saved);
    }
    
    private TaskDto convertToDto(Task task) {
        TaskDto dto = new TaskDto();
        dto.setId(task.getId());
        dto.setDescription(task.getDescription());
        dto.setCompleted(task.isCompleted());
        return dto;
    }
}
```

## Vantaggi del Service Layer

1. **Separazione delle Responsabilità**: Migliora la separazione delle responsabilità. Il Controller si occupa della logica di flusso, mentre il layer di servizio gestisce la logica di business.

2. **Riutilizzo del Codice**: La logica di business centralizzata nel layer di servizio può essere riutilizzata da diversi controller.

3. **Manutenibilità**: Centralizzare la logica di business in un singolo livello facilita la manutenzione e l'aggiornamento del codice.

## Vantaggi dell'MVC in Generale

1. **Modularità**: L'applicazione è divisa in componenti con responsabilità ben definite
2. **Riutilizzo**: I componenti possono essere riutilizzati in diverse parti dell'applicazione
3. **Manutenibilità**: Il codice è più facile da mantenere grazie alla separazione delle responsabilità
4. **Testabilità**: I componenti possono essere testati individualmente
5. **Adattabilità**: È più facile adattare l'applicazione ai cambiamenti

## Implementazione Pratica in un'Applicazione Java

```java
// Esempio completo di implementazione MVC con Service Layer per un'app Todo

// 1. Model
@Entity
public class Todo {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String title;
    private boolean completed;
    
    // Getters e setters
}

// 2. Repository (accesso ai dati)
@Repository
public interface TodoRepository extends JpaRepository<Todo, Long> {
    List<Todo> findByCompleted(boolean completed);
}

// 3. Service Layer
@Service
public class TodoService {
    private final TodoRepository repository;
    
    @Autowired
    public TodoService(TodoRepository repository) {
        this.repository = repository;
    }
    
    public List<Todo> getAllTodos() {
        return repository.findAll();
    }
    
    public Todo createTodo(Todo todo) {
        todo.setCompleted(false);
        return repository.save(todo);
    }
    
    public Todo toggleTodo(Long id) {
        Todo todo = repository.findById(id)
                .orElseThrow(() -> new NotFoundException("Todo non trovato"));
        todo.setCompleted(!todo.isCompleted());
        return repository.save(todo);
    }
}

// 4. Controller
@RestController
@RequestMapping("/api/todos")
public class TodoController {
    private final TodoService service;
    
    @Autowired
    public TodoController(TodoService service) {
        this.service = service;
    }
    
    @GetMapping
    public List<Todo> getAllTodos() {
        return service.getAllTodos();
    }
    
    @PostMapping
    public ResponseEntity<Todo> createTodo(@RequestBody Todo todo) {
        Todo created = service.createTodo(todo);
        return ResponseEntity.status(HttpStatus.CREATED).body(created);
    }
    
    @PutMapping("/{id}/toggle")
    public Todo toggleTodo(@PathVariable Long id) {
        return service.toggleTodo(id);
    }
}

// 5. View (nel caso di una app web moderna sarebbe implementata in JavaScript)
// Qui è rappresentata da una semplice Thymeleaf template

/* todo-list.html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <title>Todo List</title>
</head>
<body>
    <h1>Todo List</h1>
    <ul>
        <li th:each="todo : ${todos}">
            <span th:text="${todo.completed ? '✓' : '○'}"></span>
            <span th:text="${todo.title}"></span>
            <button th:onclick="'toggleTodo(' + ${todo.id} + ')'">Toggle</button>
        </li>
    </ul>
    <form id="new-todo-form">
        <input type="text" id="title" placeholder="Nuovo todo" />
        <button type="submit">Aggiungi</button>
    </form>
    <script>
        // Codice JavaScript per interagire con l'API
        function toggleTodo(id) {
            fetch(`/api/todos/${id}/toggle`, { method: 'PUT' })
                .then(() => window.location.reload());
        }
        
        document.getElementById('new-todo-form').addEventListener('submit', function(e) {
            e.preventDefault();
            const title = document.getElementById('title').value;
            fetch('/api/todos', {
                method: 'POST',
                headers: { 'Content-Type': 'application/json' },
                body: JSON.stringify({ title: title })
            }).then(() => window.location.reload());
        });
    </script>
</body>
</html>
*/
```

## Conclusione

Il pattern MVC e la sua estensione con il Service Layer rappresentano un approccio strutturato e modulare allo sviluppo di applicazioni, specialmente quelle web. Questi pattern favoriscono una chiara separazione delle responsabilità, rendendo il codice più mantenibile, testabile e scalabile. In applicazioni Java moderne, specialmente quelle basate su framework come Spring, questi pattern sono ampiamente utilizzati e rappresentano una solida base per lo sviluppo di software robusto e ben organizzato.