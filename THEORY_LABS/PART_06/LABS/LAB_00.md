
# TP Pratique : Base de Données, Architecture MVVM, Flux de Données, et WorkManager avec Jetpack Compose

## Objectifs du TP

Ce **TP** aborde les concepts avancés de gestion de données et d’architecture dans `Jetpack Compose`, incluant l’intégration de `Room`, le modèle `MVVM`, et la gestion de tâches en arrière-plan avec `WorkManager`, pour commencer je vous conseille de travailler au préalable sur [ce lab](https://developer.android.com/codelabs/basic-android-kotlin-compose-persisting-data-room?hl=fr#0).

---

### Table des Matières

1. [Room Database avec Compose](#1-room-database-avec-compose)
2. [Architecture MVVM et Clean Architecture](#2-architecture-mvvm-et-clean-architecture)
3. [Flux de données avec Flow et LiveData](#3-flux-de-données-avec-flow-et-livedata)
4. [Jetpack WorkManager](#4-jetpack-workmanager)
5. [Conclusion et Ressources](#5-conclusion-et-ressources)

---

### 1. Room Database avec Compose

`Room` est une bibliothèque de persistance locale qui permet de gérer les bases de données **SQLite** de manière optimisée.

#### Exercice 1 : Créer une base de données avec Room

1. Définissez une entité `Task` avec des attributs `id`, `title`, et `isComplete`.
2. Créez une interface `TaskDao` avec des méthodes pour `insérer`, `supprimer`, et `récupérer toutes les tâches`.

```kotlin
@Entity(tableName = "tasks")
data class Task(
    @PrimaryKey(autoGenerate = true) val id: Int = 0,
    val title: String,
    val isComplete: Boolean = false
)

@Dao
interface TaskDao {
    @Insert(onConflict = OnConflictStrategy.REPLACE)
    suspend fun insertTask(task: Task)

    @Query("SELECT * FROM tasks")
    fun getAllTasks(): Flow<List<Task>>
}
```

3. Configurez la `RoomDatabase` pour inclure `TaskDao`.

```kotlin
@Database(entities = [Task::class], version = 1)
abstract class AppDatabase : RoomDatabase() {
    abstract fun taskDao(): TaskDao
}
```

**Objectif** : Apprendre à configurer une base de données **Room** et créer un **DAO** pour manipuler les données.

---

### 2. Architecture MVVM et Clean Architecture

L’architecture `MVVM` simplifie la gestion d’état en séparant les responsabilités dans une application.

#### Exercice 2 : Implémenter une architecture MVVM avec Clean Architecture

1. Créez un `TaskRepository` qui encapsule la logique d’accès aux données de la base de données **Room**.

```kotlin
class TaskRepository(private val taskDao: TaskDao) {
    fun getAllTasks(): Flow<List<Task>> = taskDao.getAllTasks()
    suspend fun insertTask(task: Task) = taskDao.insertTask(task)
}
```

2. Créez un `TaskViewModel` qui interagit avec le `TaskRepository` pour gérer les tâches.

```kotlin
class TaskViewModel(private val repository: TaskRepository) : ViewModel() {
    val tasks: LiveData<List<Task>> = repository.getAllTasks().asLiveData()
    fun addTask(task: Task) = viewModelScope.launch { repository.insertTask(task) }
}
```

3. Ajoutez un `TaskViewModelFactory` pour injecter le `TaskRepository` dans le ViewModel :

```kotlin
import androidx.lifecycle.ViewModel
import androidx.lifecycle.ViewModelProvider

class TaskViewModelFactory(private val repository: TaskRepository) : ViewModelProvider.Factory {
    override fun <T : ViewModel> create(modelClass: Class<T>): T {
        if (modelClass.isAssignableFrom(TaskViewModel::class.java)) {
            @Suppress("UNCHECKED_CAST")
            return TaskViewModel(repository) as T
        }
        throw IllegalArgumentException("Unknown ViewModel class")
    }
}
```

4. Utilisez le `ViewModel` dans un composable pour afficher une liste de tâches.

```kotlin
@Composable
fun TaskListScreen(viewModel: TaskViewModel = viewModel()) {
    val tasks by viewModel.tasks.observeAsState(emptyList())
    LazyColumn {
        items(tasks) { task ->
            Text(task.title)
        }
    }
}
```

**Objectif** : Comprendre et implémenter l’architecture `MVVM` en séparant les données, la logique métier et l’interface utilisateur.

---

### 3. Flux de Données avec Flow et LiveData

**Compose** gère les données réactives avec `Flow` et `LiveData` pour une **UI** en temps réel.

#### Exercice 3 : Utiliser Flow pour la gestion de données réactives

1. Configurez `Flow` dans `TaskRepository` pour émettre des listes de tâches.
2. Utilisez `collectAsState` pour observer les changements de données dans un composable.

```kotlin
@Composable
fun TaskFlowScreen(viewModel: TaskViewModel = viewModel()) {
    val tasks by viewModel.tasksFlow.collectAsState(initial = emptyList())
    LazyColumn {
        items(tasks) { task ->
            Text(task.title)
        }
    }
}
```

**Objectif** : Apprendre à utiliser `Flow` pour l’affichage dynamique de données.

#### Transformations et Combinaisons de Flux

3. Utilisez `combine` pour combiner deux flux de données.

```kotlin
val combinedTasks = flowA.combine(flowB) { dataA, dataB ->
    // Logique de combinaison
}
```

---

### 4. Jetpack WorkManager

`WorkManager` est conçu pour exécuter des tâches longues ou répétitives en arrière-plan.

#### Exercice 4 : Planifier une tâche périodique avec WorkManager

1. Créez un `Worker` qui synchronise des données.
2. Configurez un `PeriodicWorkRequest` pour exécuter la tâche toutes les heures.

```kotlin
class SyncWorker(appContext: Context, workerParams: WorkerParameters) : Worker(appContext, workerParams) {
    override fun doWork(): Result {
        // Logique de synchronisation
        return Result.success()
    }
}

val syncRequest = PeriodicWorkRequestBuilder<SyncWorker>(1, TimeUnit.HOURS).build()
WorkManager.getInstance(context).enqueue(syncRequest)
```

3. Configurez des **contraintes de réseau** pour n’exécuter le travail que lorsque l’appareil est connecté au **WiFi**.

```kotlin
val constraints = Constraints.Builder()
    .setRequiredNetworkType(NetworkType.UNMETERED) // WiFi uniquement
    .build()

val syncRequest = PeriodicWorkRequestBuilder<SyncWorker>(1, TimeUnit.HOURS)
    .setConstraints(constraints)
    .build()

WorkManager.getInstance(context).enqueue(syncRequest)
```

**Objectif** : Configurer **WorkManager** pour planifier et exécuter des tâches en arrière-plan avec contraintes.

---

### 5. Conclusion et Ressources

Ce **TP** vous a permis de vous familiariser avec `Room`, l’architecture `MVVM`, les flux de données réactifs, et la gestion de tâches en arrière-plan.

#### Ressources

- [Guide complet sur Room Database](https://developer.android.com/training/data-storage/room)
- [Jetpack WorkManager Documentation](https://developer.android.com/topic/libraries/architecture/workmanager)

---