
# Partie 7 : Concepts Avancés avec Jetpack

---

### Table des Matières

1. [Room Database avec Compose](#1-room-database-avec-compose)
2. [Architecture MVVM et Clean Architecture](#2-architecture-mvvm-et-clean-architecture)
3. [Flux de données avec Flow et LiveData](#3-flux-de-données-avec-flow-et-livedata)
4. [Jetpack WorkManager](#4-jetpack-workmanager)
5. [Conclusion et Ressources](#5-conclusion-et-ressources)

---

### 1. Room Database avec Compose

`Room` simplifie la gestion des bases de données en local, avec des fonctionnalités avancées pour gérer les relations et les transactions.

#### Gestion des Relations entre Entités

- **Relations `@OneToMany`** : Utiliser `@Relation` pour définir des relations entre tables.
- **Jointures Avancées** : Créer des vues composites à partir de jointures entre tables pour simplifier l’accès aux données.

```kotlin
@Entity
data class User(
    @PrimaryKey val userId: Int,
    val name: String
)

@Entity
data class Task(
    @PrimaryKey val taskId: Int,
    val userId: Int,
    val description: String
)

data class UserWithTasks(
    @Embedded val user: User,
    @Relation(
        parentColumn = "userId",
        entityColumn = "userId"
    )
    val tasks: List<Task>
)
```

#### Gestion des Migrations

Les migrations permettent de gérer les changements dans la structure de la base de données tout en conservant les données existantes.

```kotlin
val MIGRATION_1_2 = object : Migration(1, 2) {
    override fun migrate(database: SupportSQLiteDatabase) {
        database.execSQL("ALTER TABLE Task ADD COLUMN dueDate INTEGER")
    }
}
```

#### Transactions avec Room

- Utiliser des transactions pour garantir l’intégrité des données lors d’opérations complexes.
- `@Transaction` : Anotter les méthodes **DAO** qui impliquent plusieurs opérations.

---

### 2. Architecture MVVM et Clean Architecture

L'architecture **Clean Architecture** renforce la séparabilité des composants avec des couches strictes pour le modèle de données, le domaine, et l’interface utilisateur.

#### Structure des Couches

1. **Data Layer** : Gère les sources de données **(API, base de données locale)** via un **Repository**.
2. **Domain Layer** : Contient la logique métier avec des **UseCases**.
3. **Presentation Layer** : Utilise **ViewModel** pour l’interface.

#### Exemple de Repository Pattern

Intégrer le **Repository** avec des sources locales et distantes tout en gérant le cache.

```kotlin
class TaskRepository(
    private val taskDao: TaskDao,
    private val apiService: ApiService
) {
    fun getTasks(): Flow<List<Task>> = flow {
        val tasksFromDb = taskDao.getAllTasks().first()
        emit(tasksFromDb)
        try {
            val tasksFromApi = apiService.getTasks()
            taskDao.insertTasks(tasksFromApi)
            emit(tasksFromApi)
        } catch (e: Exception) {
            // Gestion des erreurs
        }
    }
}
```

#### Injection de Dépendances avec Hilt

`Hilt` simplifie la gestion des dépendances en fournissant des modules injectables.

```kotlin
@Module
@InstallIn(SingletonComponent::class)
object AppModule {
    @Provides
    fun provideTaskRepository(
        taskDao: TaskDao,
        apiService: ApiService
    ): TaskRepository = TaskRepository(taskDao, apiService)
}
```

---

### 3. Flux de données avec Flow et LiveData

`Flow` et `LiveData` permettent la gestion de données réactives en flux avec des transformations avancées.

#### StateFlow et SharedFlow

- **StateFlow** : Utilisé pour émettre des états actuels.
- **SharedFlow** : **Flux** partagé pour la gestion des événements uniques (ex. affichage de messages d'erreur).

#### Transformations et Opérateurs

- Utiliser `combine` et `zip` pour combiner plusieurs flux de données.

```kotlin
val combinedFlow = flowA.combine(flowB) { dataA, dataB ->
    // Transforme les deux flux en un seul
}
```

#### Gestion des Erreurs

- **catch** : Gérer les erreurs en milieu de flux.
- **retry** : Réessayer automatiquement les opérations après échec.

---

### 4. Jetpack WorkManager

`WorkManager` est conçu pour exécuter des tâches longues et récurrentes en arrière-plan.

#### Chaînes de Tâches et Conditions

- Créer des chaînes de tâches qui s'exécutent en séquence.
- Ajouter des contraintes pour les exécutions conditionnelles **(ex. seulement en WiFi)**.

#### Exemple de Chaîne de WorkRequests

```kotlin
val compressionWork = OneTimeWorkRequestBuilder<CompressWorker>().build()
val uploadWork = OneTimeWorkRequestBuilder<UploadWorker>().build()

WorkManager.getInstance(context)
    .beginWith(compressionWork)
    .then(uploadWork)
    .enqueue()
```

#### Suivi et État des Tâches

- Utiliser `WorkInfo` pour surveiller et reporter l’état d’avancement de la tâche.

---

### 5. Conclusion et Ressources

Ces concepts avancés optimisent les performances des applications en assurant la persistance des données, la gestion des états, et la synchronisation en arrière-plan.

Pour aller plus loin :

- [Documentation complète sur Room](https://developer.android.com/training/data-storage/room)
- [Hilt pour l’injection de dépendances](https://developer.android.com/training/dependency-injection/hilt-android)
- [Tutoriels avancés sur WorkManager](https://developer.android.com/topic/libraries/architecture/workmanager)

---