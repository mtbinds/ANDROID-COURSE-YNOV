
# Partie 5 : Cours pratique

---

### Table des Matières

- [Partie 5 : Cours pratique](#partie-5--cours-pratique)
    - [Table des Matières](#table-des-matières)
    - [1. Application To-Do List](#1-application-to-do-list)
      - [Objectifs](#objectifs)
      - [Étapes de Développement](#étapes-de-développement)
    - [2. Application de Profil Utilisateur](#2-application-de-profil-utilisateur)
      - [Objectifs](#objectifs-1)
      - [Étapes de Développement](#étapes-de-développement-1)
    - [3. Application de News avec API](#3-application-de-news-avec-api)
      - [Objectifs](#objectifs-2)
      - [Étapes de Développement](#étapes-de-développement-2)
    - [4. Évaluation du Projet Final](#4-évaluation-du-projet-final)
      - [Objectifs](#objectifs-3)
      - [Étapes de Développement](#étapes-de-développement-3)
    - [5. Conclusion et Ressources](#5-conclusion-et-ressources)
    - [6. Dépôt de l'application](#6-dépôt-de-lapplication)

---

### 1. Application To-Do List

Créer une application simple de gestion de tâches avec **Jetpack Compose**.

#### Objectifs

- Gérer les états de tâche **(ajout, suppression, affichage)**.
- Créer une interface utilisateur pour lister les tâches.

#### Étapes de Développement

1. **Configuration de l’interface** : Utiliser `LazyColumn` pour afficher la liste des tâches.
2. **Gestion des états** : Utiliser `mutableStateListOf` pour stocker et gérer les tâches.
3. **Fonctionnalité d’ajout** : Ajouter une `TextField` pour saisir des tâches et un `Button` pour les ajouter.

```kotlin
@Composable
fun ToDoApp() {
    val tasks = remember { mutableStateListOf<String>() }
    var newTask by remember { mutableStateOf("") }

    Column {
        TextField(value = newTask, onValueChange = { newTask = it })
        Button(onClick = {
            if (newTask.isNotBlank()) {
                tasks.add(newTask)
                newTask = ""
            }
        }) {
            Text("Ajouter tâche")
        }
        LazyColumn {
            items(tasks) { task ->
                Text(task)
            }
        }
    }
}
```

---

### 2. Application de Profil Utilisateur

Construire une application qui affiche un profil utilisateur avec des informations dynamiques.

#### Objectifs

- Utiliser des données dynamiques via `ViewModel`.
- Afficher des informations de profil comme **le nom**, **l’âge**, et **une photo**.

#### Étapes de Développement

1. **Création de `UserViewModel`** : Gérer l’état et les données dynamiques du profil utilisateur.
2. **Affichage des données** : Utiliser `Image` pour la photo de profil, `Text` pour le nom et les détails.

```kotlin
@Composable
fun UserProfileScreen(viewModel: UserViewModel = viewModel()) {
    val user by viewModel.userProfile.collectAsState()

    Column {
        user?.let {
            Image(painter = rememberImagePainter(it.photoUrl), contentDescription = "Photo de profil")
            Text("Nom : ${it.name}")
            Text("Âge : ${it.age}")
        }
    }
}
```

---

### 3. Application de News avec API

Construire une application qui consomme une `API REST` pour afficher des actualités.

#### Objectifs

- Intégrer `Retrofit` pour la consommation d'`API`.
- Gérer les données avec `ViewModel` et afficher les actualités.

#### Étapes de Développement

1. **Intégration de Retrofit** : Configurer l'`API` avec une interface `Retrofit`.
2. **Création de `NewsViewModel`** : Récupérer et stocker les données `API`.
3. **Affichage des données** : Utiliser `LazyColumn` pour lister les articles.

```kotlin
interface NewsApiService {
    @GET("v2/top-headlines")
    suspend fun getTopHeadlines(@Query("country") country: String): Response<NewsResponse>
}

@Composable
fun NewsScreen(viewModel: NewsViewModel = viewModel()) {
    val articles by viewModel.articles.collectAsState()

    LazyColumn {
        items(articles) { article ->
            Text(article.title)
        }
    }
}
```

---

### 4. Évaluation du Projet Final

Développer une application complète en utilisant les fonctionnalités apprises, avec persistance et navigation avancée.

#### Objectifs

- Intégrer des composants avancés de `Compose` et des fonctionnalités de persistance.
- Gérer une navigation multi-écrans et la persistance avec `Room`.

#### Étapes de Développement

1. **Structure de l’Application** : Créer plusieurs écrans pour différents modules de l’application.
2. **Navigation Compose** : Utiliser `NavHost` pour naviguer entre les écrans.
3. **Persistance des Données avec Room** : Enregistrer les données de manière persistante.
4. **Fonctionnalités Avancées** : Ajouter des animations et des transitions pour les interactions.

**Exemple de navigation multi-écrans :**

```kotlin
@Composable
fun AppNavigation() {
    val navController = rememberNavController()
    NavHost(navController, startDestination = "home") {
        composable("home") { HomeScreen(navController) }
        composable("details/{itemId}") { backStackEntry ->
            val itemId = backStackEntry.arguments?.getString("itemId")
            DetailsScreen(navController, itemId)
        }
    }
}
```

---

### 5. Conclusion et Ressources

Ces travaux pratiques vous permettront de maîtriser l'écosystème `Compose` et de créer des applications interactives et modernes.

---

### 6. Dépôt de l'application 

Veuillez déposer votre application **(To-Do List)** **(.apk)** archivée **(.zip)** en suivant [ce lien](https://classroom.google.com/c/NzI3NDY5MzYzNTI0?cjc=bzzt6ir)


Pour aller plus loin :

- [Documentation Jetpack Compose](https://developer.android.com/jetpack/compose)
- [Tutoriels sur la consommation d'API avec Retrofit](https://square.github.io/retrofit/)
- [Documentation Room pour la persistance de données](https://developer.android.com/training/data-storage/room)

---