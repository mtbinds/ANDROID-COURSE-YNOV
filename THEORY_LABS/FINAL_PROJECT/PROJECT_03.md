
# Projet Final : Application de Suivi de Lecture (Book Tracker)

## Objectif du Projet

Développer une application Android complète pour gérer une bibliothèque personnelle et suivre la progression de lecture, tout en utilisant les concepts avancés de `Kotlin` et `Jetpack Compose`.

---

## Fonctionnalités Principales

1. **Tableau de bord des livres** : Afficher la liste des livres avec des filtres.
2. **Ajout, édition et suppression de livres** : Formulaire pour gérer les livres dans la bibliothèque.
3. **Suivi de la progression de lecture** : Indiquer et visualiser l’avancement de lecture des livres.
4. **Recherche de livres via API** : Intégrer une API de recherche pour obtenir des informations complémentaires.
5. **Profil utilisateur et personnalisation** : Permettre la gestion de profil et bascule du mode clair/sombre.

---

## Étapes de Développement

### 1. Configuration et Architecture du Projet

1. **Créer un projet Android avec Kotlin et Jetpack Compose**.
2. **Ajouter les dépendances nécessaires** pour `Room (persistance locale)`, `Retrofit (API livres)`, `ViewModel`, et `Hilt` (injection de dépendances).
3. **Structurer le projet** en modules `Model`, `ViewModel`, `Repository`, et `UI` en suivant l'architecture `MVVM` pour une meilleure modularité.

---

### 2. Tableau de Bord des Livres

1. **Créer les entités Room pour les livres et la progression**.

```kotlin
@Entity(tableName = "books")
data class Book(
    @PrimaryKey(autoGenerate = true) val id: Int = 0,
    val title: String,
    val author: String,
    val pageCount: Int,
    val status: String // "Non lu", "En cours", "Terminé"
)

@Entity(tableName = "reading_progress")
data class ReadingProgress(
    @PrimaryKey(autoGenerate = true) val id: Int = 0,
    val bookId: Int,
    val pagesRead: Int,
    val date: String // Date de l'enregistrement
)
```

2. **Configurer RoomDatabase avec DAO** pour gérer les opérations de création, lecture, mise à jour et suppression **(CRUD)** pour les livres.

```kotlin
@Dao
interface BookDao {
    @Insert(onConflict = OnConflictStrategy.REPLACE)
    suspend fun insertBook(book: Book)

    @Query("SELECT * FROM books")
    fun getAllBooks(): Flow<List<Book>>

    @Query("DELETE FROM books WHERE id = :bookId")
    suspend fun deleteBook(bookId: Int)
}

@Database(entities = [Book::class, ReadingProgress::class], version = 1)
abstract class BookDatabase : RoomDatabase() {
    abstract fun bookDao(): BookDao
}
```

3. **Utiliser `LazyColumn`** pour afficher la liste des livres avec des filtres pour trier par catégorie.

4. **Ajouter un `BookRepository`** pour centraliser la gestion des données de la bibliothèque.

---

### 3. Ajout, Édition et Suppression de Livres

1. **Créer un formulaire pour ajouter ou modifier un livre** avec des champs `TextField` pour le titre, l’auteur, le nombre de pages, et le statut de lecture.

```kotlin
@Composable
fun BookForm(viewModel: BookViewModel) {
    var title by remember { mutableStateOf("") }
    var author by remember { mutableStateOf("") }
    var pageCount by remember { mutableStateOf("") }
    var status by remember { mutableStateOf("Non lu") }

    Column {
        TextField(value = title, onValueChange = { title = it }, label = { Text("Titre") })
        TextField(value = author, onValueChange = { author = it }, label = { Text("Auteur") })
        TextField(value = pageCount, onValueChange = { pageCount = it }, label = { Text("Nombre de pages") })
        DropdownMenu(expanded = true, onDismissRequest = { }) {
            DropdownMenuItem(onClick = { status = "Non lu" }) { Text("Non lu") }
            DropdownMenuItem(onClick = { status = "En cours" }) { Text("En cours") }
            DropdownMenuItem(onClick = { status = "Terminé" }) { Text("Terminé") }
        }
        Button(onClick = { viewModel.addBook(Book(title = title, author = author, pageCount = pageCount.toInt(), status = status)) }) {
            Text("Ajouter")
        }
    }
}
```

2. **Ajouter des fonctionnalités d'édition et de suppression de livres** avec des `Dialog` pour la confirmation de suppression.

---

### 4. Suivi de la Progression de Lecture

1. **Ajouter une option pour indiquer le nombre de pages lues** et mettre à jour la progression.

2. **Utiliser un `ProgressBar`** pour indiquer la progression de lecture d'un livre en pourcentage.

3. **Historiser la progression** : Enregistrer chaque mise à jour de la progression dans la base de données et afficher un graphique de progression.

---

### 5. Recherche de Livres via API

1. **Configurer Retrofit** pour consommer une **API externe (Google Books API)** afin de rechercher des livres.

2. **Créer une interface de recherche** avec `TextField` pour entrer le titre ou l’auteur, et afficher les résultats dans un `LazyColumn`.

3. **Permettre d’ajouter un livre trouvé via l’API** directement dans la bibliothèque locale.

---

### 6. Profil Utilisateur et Personnalisation

1. **Créer un écran de profil utilisateur** pour modifier le nom et ajouter une photo de profil.

2. **Ajouter une option pour basculer entre le mode clair et le mode sombre**.

```kotlin
@Composable
fun BookTrackerTheme(darkTheme: Boolean, content: @Composable () -> Unit) {
    val colors = if (darkTheme) darkColors() else lightColors()
    MaterialTheme(colors = colors, content = content)
}
```

3. **Enregistrer les préférences utilisateur** dans Room pour conserver le thème sélectionné.

---

### 7. Tests et Déploiement

1. **Tests unitaires** : Tester les fonctionnalités du `BookViewModel` et des méthodes du Repository.
2. **Tests d'interface** : Vérifier l'ajout, la suppression, et l’édition de livres.
3. **Optimisation et préparation pour le déploiement** : Configurer les autorisations, les signatures et les fichiers `.aab` pour le Play Store.

---

## Conclusion

Le projet **Book Tracker** permet de développer une application **Android** complète et interactive en utilisant `Jetpack Compose`, avec des fonctionnalités avancées telles que la gestion de bibliothèque, l’intégration d'une API externe, le suivi de progression et la personnalisation du thème.

---

### Ressources

- [Documentation Jetpack Compose](https://developer.android.com/jetpack/compose/documentation)
- [Google Books API](https://developers.google.com/books/docs/v1/getting_started)
- [Guide sur Room Database](https://developer.android.com/training/data-storage/room)
- [Guide pour les notifications WorkManager](https://developer.android.com/topic/libraries/architecture/workmanager)

---