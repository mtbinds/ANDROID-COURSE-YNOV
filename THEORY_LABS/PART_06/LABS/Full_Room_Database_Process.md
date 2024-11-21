
# Room Database avec Jetpack Compose

`Room` est une bibliothèque de persistance locale qui facilite la gestion des bases de données **SQLite** de manière optimisée.

---

## **Exercice : Créer une base de données avec Room**

1. **Définir une entité `Task` avec les attributs `id`, `title`, et `isComplete`.**
2. **Créer une interface `TaskDao` avec des méthodes pour `insérer`, `supprimer`, et `récupérer toutes les tâches`.**
3. **Construire une interface utilisateur avec Jetpack Compose.**

---

## **Étape 1 : Ajouter les dépendances Room**

Dans votre fichier `build.gradle.kts` (module `app`), ajoutez les dépendances nécessaires à **Room**.

### **Dépendances :**

```kotlin
dependencies {
    val roomVersion = "2.6.1" // Dernière version de Room

    implementation("androidx.room:room-runtime:$roomVersion")
    kapt("androidx.room:room-compiler:$roomVersion")
    implementation("androidx.room:room-ktx:$roomVersion")
}
```

### **Plugins :**

Ajoutez le plugin **kapt** dans le bloc `plugins` :

```kotlin
plugins {
    id("com.android.application") // ou "com.android.library"
    id("org.jetbrains.kotlin.kapt") // Plugin KAPT
}
```

---

## **Étape 2 : Créer une entité `Task`**

L'entité représente une table dans la base de données Room. Voici l'exemple pour une table `tasks` :

```kotlin
import androidx.room.Entity
import androidx.room.PrimaryKey

@Entity(tableName = "tasks")
data class Task(
    @PrimaryKey(autoGenerate = true) val id: Int = 0,
    val title: String,
    val isComplete: Boolean = false
)
```

---

## **Étape 3 : Créer une interface DAO**

**TaskDao** définit les opérations à effectuer sur la base de données, telles que l'insertion, la récupération et la suppression des tâches.

```kotlin
import androidx.room.Dao
import androidx.room.Insert
import androidx.room.OnConflictStrategy
import androidx.room.Query
import kotlinx.coroutines.flow.Flow

@Dao
interface TaskDao {
    @Insert(onConflict = OnConflictStrategy.REPLACE)
    suspend fun insertTask(task: Task)

    @Query("SELECT * FROM tasks")
    fun getAllTasks(): Flow<List<Task>>

    @Query("DELETE FROM tasks WHERE id = :taskId")
    suspend fun deleteTaskById(taskId: Int)
}
```

---

## **Étape 4 : Créer la base de données `TaskDatabase`**

Créez une classe **TaskDatabase** pour définir la base de données.

```kotlin
import androidx.room.Database
import androidx.room.RoomDatabase

@Database(entities = [Task::class], version = 1, exportSchema = false)
abstract class TaskDatabase : RoomDatabase() {
    abstract fun taskDao(): TaskDao
}
```

---

## **Étape 5 : Initialiser la base de données**

Configurez un **singleton** pour initialiser la base de données.

```kotlin
import android.content.Context
import androidx.room.Room

object DatabaseProvider {
    @Volatile
    private var INSTANCE: TaskDatabase? = null

    fun getDatabase(context: Context): TaskDatabase {
        return INSTANCE ?: synchronized(this) {
            val instance = Room.databaseBuilder(
                context.applicationContext,
                TaskDatabase::class.java,
                "task_database"
            ).build()
            INSTANCE = instance
            instance
        }
    }
}
```

---

## **Étape 6 : Créer le Repository et ViewModel**

### **Repository**
Le **repository** est une abstraction des interactions avec le DAO.

```kotlin
import kotlinx.coroutines.flow.Flow

class TaskRepository(private val taskDao: TaskDao) {
    val allTasks: Flow<List<Task>> = taskDao.getAllTasks()

    suspend fun insert(task: Task) {
        taskDao.insertTask(task)
    }

    suspend fun deleteTaskById(taskId: Int) {
        taskDao.deleteTaskById(taskId)
    }
}
```

### **ViewModel**

Le **ViewModel** permet à l'interface utilisateur de réagir aux changements de données.

```kotlin
import androidx.lifecycle.ViewModel
import androidx.lifecycle.viewModelScope
import kotlinx.coroutines.launch

class TaskViewModel(private val repository: TaskRepository) : ViewModel() {
    val allTasks = repository.allTasks

    fun insert(task: Task) {
        viewModelScope.launch {
            repository.insert(task)
        }
    }

    fun deleteTaskById(taskId: Int) {
        viewModelScope.launch {
            repository.deleteTaskById(taskId)
        }
    }
}
```

---

## **Étape 7 : Créer TaskViewModelFactory**

Ajoutez une classe `TaskViewModelFactory` pour fournir une instance de `TaskViewModel` avec un `TaskRepository` :

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

---

## **Étape 8 : Construire l'interface utilisateur avec Jetpack Compose**

Voici un composable **TaskScreen** qui affiche la liste des tâches, permet de marquer les tâches comme complètes et de les supprimer.

```kotlin
import androidx.compose.foundation.layout.*
import androidx.compose.material.*
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.unit.dp

@Composable
fun TaskScreen(viewModel: TaskViewModel) {
    val tasks by viewModel.allTasks.collectAsState(initial = emptyList())

    Column {
        tasks.forEach { task ->
            Row(
                modifier = Modifier
                    .fillMaxWidth()
                    .padding(8.dp),
                verticalAlignment = Alignment.CenterVertically
            ) {
                Text(task.title)
                Spacer(modifier = Modifier.weight(1f))
                Checkbox(
                    checked = task.isComplete,
                    onCheckedChange = { isChecked ->
                        viewModel.insert(task.copy(isComplete = isChecked))
                    }
                )
                IconButton(onClick = { viewModel.deleteTaskById(task.id) }) {
                    Icon(Icons.Default.Delete, contentDescription = "Delete")
                }
            }
        }
    }
}
```

---

## **Étape 9 : Connecter tout dans `MainActivity`**

Enfin, dans votre **MainActivity**, utilisez `setContent` pour afficher l'interface **Compose** en ajoutant **3 Tâches**.

```kotlin
import android.os.Bundle
import androidx.activity.ComponentActivity
import androidx.activity.compose.setContent
import androidx.lifecycle.ViewModelProvider

class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        val database = DatabaseProvider.getDatabase(this)
        val repository = TaskRepository(database.taskDao())
        val factory = TaskViewModelFactory(repository)
        val viewModel = ViewModelProvider(this, factory).get(TaskViewModel::class.java)
        
        // Ajout de 3 tâches
        
        viewModel.insert(Task(title = "Sample Task 1"))
        viewModel.insert(Task(title = "Sample Task 2"))
        viewModel.insert(Task(title = "Sample Task 3"))

        setContent {
            TaskScreen(viewModel = viewModel)
        }
    }
}
```

---

Avec ces étapes, vous obtenez une application fonctionnelle qui utilise **Room Database** pour la persistance des données et **Jetpack Compose** pour l'interface utilisateur. 🎉