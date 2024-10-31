
# Projet Final : Application de Gestion d'Événements (Event Manager)

## Objectifs du Projet

Ce projet a pour objectif de créer une application complète de gestion d'événements en intégrant les compétences en `Kotlin`, `Jetpack Compose`, `Room`, `MVVM`, `WorkManager`, et plus.

### Fonctionnalités

1. **Page d'accueil avec liste d'événements** : Afficher et filtrer les événements avec des animations.
2. **Ajout, modification et suppression d'événements** : Utilisation de formulaires et validation.
3. **Profil utilisateur** : Gestion d'un profil avec des préférences de thème.
4. **Actualités sur les événements via API** : Consommer une `API REST` pour les événements en direct.
5. **Thème personnalisable** : Mode sombre et clair avec `MaterialTheme`.
6. **Mise à jour en arrière-plan** : Synchroniser les données d'événements avec `WorkManager`.

---

## Étapes de Développement

### 1. Configuration et Architecture du Projet

1. **Initialiser un projet Android** avec `Kotlin` et `Jetpack Compose`.
2. **Ajouter les dépendances** pour `Room`, `Retrofit`, `ViewModel`, `LiveData`, `WorkManager` et `Hilt` pour l'injection de dépendances.
3. **Structurer l'architecture MVVM** :
   
   - **Model** : Classes de données pour les événements et le profil utilisateur.
   - **ViewModel** : Gestion de la logique d'interaction pour chaque écran.
   - **Repository** : Sources de données (base de données locale avec `Room` et `API` distante).
   - **UI (Composables)** : Interface utilisateur avec les composants de `Jetpack Compose`.

---

### 2. Page d'Accueil et Liste des Événements

1. **Créer une entité Room pour les événements**.

```kotlin
@Entity(tableName = "events")
data class Event(
    @PrimaryKey(autoGenerate = true) val id: Int = 0,
    val title: String,
    val description: String,
    val date: String,
    val location: String,
    val category: String
)
```

2. **Configurer le DAO et la base de données Room**.

```kotlin
@Dao
interface EventDao {
    @Query("SELECT * FROM events ORDER BY date DESC")
    fun getAllEvents(): Flow<List<Event>>

    @Insert(onConflict = OnConflictStrategy.REPLACE)
    suspend fun insertEvent(event: Event)

    @Delete
    suspend fun deleteEvent(event: Event)
}

@Database(entities = [Event::class], version = 1)
abstract class AppDatabase : RoomDatabase() {
    abstract fun eventDao(): EventDao
}
```

3. **Créer le `EventRepository`** pour centraliser les interactions avec la base de données et l’`API`.

4. **Configurer le `EventViewModel`** avec des `LiveData` et `Flow` pour gérer les données de la liste d'événements.

5. **Utiliser `LazyColumn` pour afficher les événements** en ajoutant des animations lors de l’ajout et de la suppression d’éléments.

---

### 3. Gestion des Événements : Ajout, Modification et Suppression

1. **Créer un formulaire d’ajout d’événements** avec des `TextField` pour le titre, la description, la date, et le lieu.

```kotlin
@Composable
fun AddEventForm(viewModel: EventViewModel) {
    var title by remember { mutableStateOf("") }
    var description by remember { mutableStateOf("") }
    var date by remember { mutableStateOf("") }
    var location by remember { mutableStateOf("") }

    Column {
        TextField(value = title, onValueChange = { title = it }, label = { Text("Titre") })
        TextField(value = description, onValueChange = { description = it }, label = { Text("Description") })
        TextField(value = date, onValueChange = { date = it }, label = { Text("Date") })
        TextField(value = location, onValueChange = { location = it }, label = { Text("Lieu") })
        Button(onClick = { viewModel.addEvent(Event(title = title, description = description, date = date, location = location)) }) {
            Text("Ajouter")
        }
    }
}
```

2. **Valider les entrées utilisateur** pour garantir des données cohérentes.

3. **Implémenter la suppression avec confirmation** : Utiliser un `Dialog` pour demander confirmation avant de supprimer un événement.

---

### 4. Profil Utilisateur

1. **Créer une entité `UserProfile` dans Room** pour stocker les préférences.

```kotlin
@Entity(tableName = "user_profile")
data class UserProfile(
    @PrimaryKey val userId: Int = 1,
    val name: String,
    val preferredCategory: String,
    val themePreference: Boolean // true pour mode sombre, false pour mode clair
)
```

2. **Créer un `ProfileRepository`** pour accéder aux données du profil.

3. **Implémenter un écran de profil utilisateur** permettant de modifier le nom et les préférences de catégorie et de thème.

```kotlin
@Composable
fun ProfileScreen(viewModel: ProfileViewModel) {
    val profile by viewModel.profile.observeAsState(UserProfile())
    Column {
        Text("Nom : ${profile.name}")
        Text("Catégorie Préférée : ${profile.preferredCategory}")
        Text("Thème : ${if (profile.themePreference) "Sombre" else "Clair"}")
    }
}
```

---

### 5. Actualités sur les Événements via API

1. **Configurer Retrofit** avec l'`URL` de l'`API` des actualités d'événements.

2. **Créer le `NewsRepository`** pour consommer l'`API` et stocker les données dans `Room` pour persistance.

3. **Afficher les actualités dans un `LazyColumn`** avec `NewsViewModel` et actualisation des données en temps réel grâce à `Flow`.

---

### 6. Thème Personnalisé et Mode Sombre

1. **Configurer `MaterialTheme` avec des thèmes personnalisés**.

2. **Ajouter une option de bascule** pour le mode sombre dans le profil utilisateur et persister cette préférence dans `Room`.

```kotlin
@Composable
fun EventManagerTheme(darkTheme: Boolean, content: @Composable () -> Unit) {
    val colors = if (darkTheme) darkColors() else lightColors()
    MaterialTheme(colors = colors, content = content)
}
```

---

### 7. Mise à Jour en Arrière-Plan avec WorkManager

1. **Créer un `Worker`** pour synchroniser les événements toutes les `24 heures`.

2. **Configurer des contraintes** (ex : connexion WiFi) pour limiter l'exécution en arrière-plan.

3. **Ajouter des notifications** en cas de nouvelles actualités, pour informer l'utilisateur des mises à jour.

```kotlin
class SyncEventsWorker(appContext: Context, workerParams: WorkerParameters) : CoroutineWorker(appContext, workerParams) {
    override suspend fun doWork(): Result {
        // Code de synchronisation des événements
        return Result.success()
    }
}

val syncRequest = PeriodicWorkRequestBuilder<SyncEventsWorker>(1, TimeUnit.DAYS).build()
WorkManager.getInstance(context).enqueue(syncRequest)
```

---

### 8. Tests et Déploiement

1. **Tests unitaires** : Écrire des tests pour `EventViewModel` et `ProfileViewModel`.
2. **Tests d'interface** : Vérifier les interactions utilisateur dans les formulaires d’ajout et de modification d’événements.
3. **Optimisation et déploiement** : Préparer le fichier `.aab`, configurer les autorisations et les fichiers de signature pour le **Play Store**.

---

## Conclusion

Ce projet final permet de maîtriser `Jetpack Compose` en construisant une application **Android** moderne. Cette application intègre toutes les notions du cours et est prête pour une publication sur le **Play Store**.

---

### Ressources

- [Documentation Jetpack Compose](https://developer.android.com/jetpack/compose/documentation)
- [Utilisation de Room avec Compose](https://developer.android.com/training/data-storage/room)
- [Configuration de WorkManager](https://developer.android.com/topic/libraries/architecture/workmanager)
- [Guide de test d'interface avec Compose](https://developer.android.com/jetpack/compose/testing)

---