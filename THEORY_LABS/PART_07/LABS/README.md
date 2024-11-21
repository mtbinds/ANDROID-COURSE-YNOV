
# TP Pratique : Gestion des tâches avec Firestore et Jetpack Compose

Dans ce **TP**, vous allez apprendre à créer une application **Android** qui utilise **Firestore** comme base de données pour gérer des tâches. Vous exploiterez **Jetpack Compose** pour construire une interface réactive et moderne. Avant de commencer, nous vous conseillons de suivre [ce lab](https://firebase.google.com/codelabs/build-android-app-with-firebase-compose?hl=fr#0) pour vous familiariser avec les bases.

---

## Objectifs du TP

- **Configurer Firebase Firestore** dans un projet **Android**.
- Définir un **modèle de données** pour représenter les tâches.
- Implémenter les **opérations CRUD (Create, Read, Update, Delete)** avec **Firestore**.
- Structurer votre code en utilisant un **Repository** et un **ViewModel**.
- Construire une interface utilisateur moderne avec **Jetpack Compose**.

---

## Étape 1 : Configurer Firebase Firestore

### 1. **Créer un projet Firebase**

- Rendez-vous sur [Firebase Console](https://console.firebase.google.com) et créez un nouveau projet.
- Suivez les instructions pour configurer Firebase avec votre application Android.

### 2. **Ajouter Firebase à votre application**

- Dans **Firebase Console**, ajoutez votre application **Android** en renseignant son **ID d'application**.
- Téléchargez le fichier `google-services.json` fourni par **Firebase**.
- Placez ce fichier dans le dossier `app` de votre projet **Android**.

### 3. **Ajouter les dépendances Firebase**

Dans le fichier `build.gradle` (module `app`), ajoutez la dépendance suivante pour Firestore :

```gradle
dependencies {
    implementation ("com.google.firebase:firebase-firestore-ktx")
}
```

### 4. **Synchroniser votre projet**

Assurez-vous de synchroniser votre projet avec **Gradle** pour appliquer les changements.

---

## Étape 2 : Définir le modèle de données `Task`

Un modèle représente la structure des données que vous allez manipuler dans Firestore.

1. **Créer une classe `Task`** :

```kotlin
data class Task(
    val id: String = "",
    val title: String = "",
    val isComplete: Boolean = false
)
```

2. **Structure des documents dans Firestore** :

Chaque document de la collection `tasks` représentera une tâche avec les champs `id`, `title`, et `isComplete`.

---

## Étape 3 : Implémenter un Repository pour Firestore

Le **Repository** sert d'interface entre Firestore et le reste de votre application.

```kotlin
import com.google.firebase.firestore.FirebaseFirestore
import kotlinx.coroutines.flow.callbackFlow

class TaskRepository(private val firestore: FirebaseFirestore) {

    fun getAllTasks() = callbackFlow {
        val listener = firestore.collection("tasks")
            .addSnapshotListener { snapshot, e ->
                if (e != null) {
                    close(e)
                    return@addSnapshotListener
                }

                val tasks = snapshot?.documents?.mapNotNull {
                    it.toObject(Task::class.java)?.copy(id = it.id)
                } ?: emptyList()
                trySend(tasks)
            }
        awaitClose { listener.remove() }
    }

    suspend fun insertTask(task: Task) {
        firestore.collection("tasks").add(task)
    }

    suspend fun deleteTask(taskId: String) {
        firestore.collection("tasks").document(taskId).delete()
    }
}
```

---

## Étape 4 : Créer un `ViewModel` pour gérer les tâches

Le **ViewModel** gère les données en interaction avec le Repository et les expose à l'interface utilisateur.

```kotlin
import androidx.lifecycle.ViewModel
import androidx.lifecycle.viewModelScope
import kotlinx.coroutines.flow.MutableStateFlow
import kotlinx.coroutines.flow.StateFlow
import kotlinx.coroutines.launch

class TaskViewModel(private val repository: TaskRepository) : ViewModel() {

    private val _tasks = MutableStateFlow<List<Task>>(emptyList())
    val tasks: StateFlow<List<Task>> = _tasks

    init {
        viewModelScope.launch {
            repository.getAllTasks().collect { _tasks.value = it }
        }
    }

    fun addTask(task: Task) {
        viewModelScope.launch {
            repository.insertTask(task)
        }
    }

    fun deleteTask(taskId: String) {
        viewModelScope.launch {
            repository.deleteTask(taskId)
        }
    }
}
```

### Créer une `TaskViewModelFactory`

Ajoutez une **Factory** pour fournir une instance de `TaskRepository` au **ViewModel** :

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

## Étape 5 : Construire l'interface utilisateur avec Jetpack Compose

### Afficher la liste des tâches

```kotlin
@Composable
fun TaskScreen(viewModel: TaskViewModel) {
    val tasks by viewModel.tasks.collectAsState(initial = emptyList())

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
                        viewModel.addTask(task.copy(isComplete = isChecked))
                    }
                )
                IconButton(onClick = { viewModel.deleteTask(task.id) }) {
                    Icon(Icons.Default.Delete, contentDescription = "Delete")
                }
            }
        }
    }
}
```

### Ajouter une nouvelle tâche

```kotlin
@Composable
fun AddTask(viewModel: TaskViewModel) {
    var taskTitle by remember { mutableStateOf("") }

    Row(modifier = Modifier.padding(8.dp)) {
        TextField(
            value = taskTitle,
            onValueChange = { taskTitle = it },
            label = { Text("New Task") },
            modifier = Modifier.weight(1f)
        )
        Button(onClick = {
            if (taskTitle.isNotBlank()) {
                viewModel.addTask(Task(title = taskTitle))
                taskTitle = ""
            }
        }) {
            Text("Add")
        }
    }
}
```

### Intégrer les composables dans `MainActivity`

```kotlin
import android.os.Bundle
import androidx.activity.ComponentActivity
import androidx.activity.compose.setContent
import com.google.firebase.firestore.FirebaseFirestore

class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        val firestore = FirebaseFirestore.getInstance()
        val repository = TaskRepository(firestore)
        val factory = TaskViewModelFactory(repository)
        val viewModel = ViewModelProvider(this, factory).get(TaskViewModel::class.java)

        setContent {
            Column {
                AddTask(viewModel)
                TaskScreen(viewModel)
            }
        }
    }
}
```

---

## Étape 6 : Tester l'application

1. **Lancer l'application** et ajoutez quelques tâches.
2. Vérifiez que les tâches sont synchronisées en temps réel avec **Firestore**.
3. Supprimez une tâche et observez les mises à jour dans l'interface utilisateur.

---

## Conclusion

Félicitations ! Vous avez appris à :

- Configurer **Firebase Firestore** avec une application **Android**.
- Implémenter les opérations **CRUD** avec **Firestore**.
- Structurer une application avec un **Repository** et un **ViewModel**.
- Construire une interface réactive et moderne avec **Jetpack Compose**.

---

## Ressources supplémentaires

- [Documentation Firestore](https://firebase.google.com/docs/firestore)
- [Tutoriel Jetpack Compose](https://developer.android.com/jetpack/compose)
- [Firebase avec Kotlin](https://firebase.google.com/docs/android/setup)

---