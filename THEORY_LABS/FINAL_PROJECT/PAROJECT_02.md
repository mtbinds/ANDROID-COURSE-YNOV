
# Projet Final : Application de Suivi des Habitudes de Vie (Healthy Habits)

## Objectifs du Projet

Ce projet vise à développer une application mobile de suivi des habitudes de vie saines, intégrant des fonctionnalités de suivi, des rappels, des statistiques et des options de personnalisation de thème.

---

## Fonctionnalités Principales

1. **Tableau de bord des habitudes** : Vue d’ensemble des habitudes de l’utilisateur et suivi des progrès quotidiens.
2. **Suivi et ajout d’habitudes** : Formulaire de création et gestion des habitudes de l’utilisateur.
3. **Statistiques et graphiques** : Visualisation des progrès avec des graphiques et des statistiques.
4. **Rappels d’habitudes avec WorkManager** : Notifications pour rappeler à l’utilisateur de suivre ses habitudes.
5. **Profil utilisateur et personnalisation de thèmes** : Options de modification du profil et bascule entre mode clair et sombre.

---

## Étapes de Développement

### 1. Configuration et Architecture du Projet

1. **Initialiser un projet Android** avec `Kotlin` et `Compose`, en ajoutant les dépendances pour : `Room`, `Retrofit` (si une `API` est utilisée), `ViewModel`, `WorkManager`, et `Hilt` pour `DI`.
2. **Structure MVVM** : Séparer le projet en modules `Model`, `ViewModel`, et `Repository` pour garantir la modularité et la testabilité.

---

### 2. Tableau de Bord des Habitudes

1. **Créer une entité Room pour les habitudes et enregistrements quotidiens**.

```kotlin
@Entity(tableName = "habits")
data class Habit(
    @PrimaryKey(autoGenerate = true) val id: Int = 0,
    val title: String,
    val description: String,
    val targetFrequency: Int, // ex. 5 fois par semaine
    val category: String
)

@Entity(tableName = "habit_records")
data class HabitRecord(
    @PrimaryKey(autoGenerate = true) val id: Int = 0,
    val habitId: Int,
    val date: String, // date de réalisation
    val completed: Boolean = false
)
```

2. **Configurer le DAO et la base de données Room** pour gérer les habitudes et les enregistrements.

```kotlin
@Dao
interface HabitDao {
    @Insert(onConflict = OnConflictStrategy.REPLACE)
    suspend fun insertHabit(habit: Habit)

    @Query("SELECT * FROM habits")
    fun getAllHabits(): Flow<List<Habit>>
}

@Database(entities = [Habit::class, HabitRecord::class], version = 1)
abstract class AppDatabase : RoomDatabase() {
    abstract fun habitDao(): HabitDao
}
```

3. **Créer un `HabitRepository`** pour centraliser les données des habitudes et des enregistrements.

4. **`HabitViewModel`** : Utiliser `LiveData` et `Flow` pour gérer et observer les habitudes.

5. **Afficher la liste des habitudes** avec `LazyColumn` et ajouter une option pour marquer une habitude comme réalisée.

---

### 3. Suivi et Ajout d’Habitudes

1. **Créer un formulaire d’ajout d’habitudes** avec des `TextField` pour le titre, la description, la fréquence cible et la catégorie.

```kotlin
@Composable
fun AddHabitForm(viewModel: HabitViewModel) {
    var title by remember { mutableStateOf("") }
    var description by remember { mutableStateOf("") }
    var frequency by remember { mutableStateOf("") }
    var category by remember { mutableStateOf("") }

    Column {
        TextField(value = title, onValueChange = { title = it }, label = { Text("Titre") })
        TextField(value = description, onValueChange = { description = it }, label = { Text("Description") })
        TextField(value = frequency, onValueChange = { frequency = it }, label = { Text("Fréquence cible") })
        TextField(value = category, onValueChange = { category = it }, label = { Text("Catégorie") })
        Button(onClick = { 
            viewModel.addHabit(Habit(title = title, description = description, targetFrequency = frequency.toInt(), category = category)) 
        }) {
            Text("Ajouter")
        }
    }
}
```

2. **Enregistrer les habitudes** dans `Room` et les afficher dans le tableau de bord.

---

### 4. Statistiques et Graphiques

1. **Calculer les statistiques d’achèvement des habitudes** (par ex. fréquence de réalisation hebdomadaire).
2. **Créer des graphiques interactifs** montrant l’évolution des habitudes (ex. histogramme de fréquence hebdomadaire).

3. **Utiliser des animations** pour rendre les graphiques plus attractifs.

---

### 5. Rappels avec WorkManager

1. **Configurer WorkManager** pour envoyer une notification de rappel selon l’heure définie par l’utilisateur.

```kotlin
class ReminderWorker(appContext: Context, workerParams: WorkerParameters) : CoroutineWorker(appContext, workerParams) {
    override suspend fun doWork(): Result {
        // Logique de notification de rappel
        return Result.success()
    }
}

val reminderRequest = PeriodicWorkRequestBuilder<ReminderWorker>(1, TimeUnit.DAYS).build()
WorkManager.getInstance(context).enqueue(reminderRequest)
```

2. **Personnaliser les rappels** : Permettre à l’utilisateur de définir ses propres heures de rappel.

---

### 6. Profil Utilisateur et Personnalisation de Thème

1. **Créer un écran de profil utilisateur** permettant la modification du nom, de la photo et de l’heure de rappel.

2. **Bascule entre mode sombre et clair** : Ajouter une option pour changer de thème dans les préférences utilisateur.

```kotlin
@Composable
fun HealthyHabitsTheme(darkTheme: Boolean, content: @Composable () -> Unit) {
    val colors = if (darkTheme) darkColors() else lightColors()
    MaterialTheme(colors = colors, content = content)
}
```

---

### 7. Tests et Déploiement

1. **Tests unitaires** : Tester la logique des `ViewModel` et des transformations de données.
2. **Tests d'interface** : Vérifier les interactions dans le formulaire et les rappels.
3. **Optimiser le code** : Minifier le code pour le Play Store.

---

## Conclusion

Ce projet Healthy Habits permet de mettre en œuvre Jetpack Compose et d'approfondir les concepts de persistance de données, de gestion d'état et d'interface utilisateur avancée.

---

### Ressources

- [Documentation Jetpack Compose](https://developer.android.com/jetpack/compose/documentation)
- [Guide complet sur Room Database](https://developer.android.com/training/data-storage/room)
- [Documentation WorkManager](https://developer.android.com/topic/libraries/architecture/workmanager)

---