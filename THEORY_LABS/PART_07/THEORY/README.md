
# Partie 6 : Concepts Avancés avec Firestore

---

### Table des Matières

1. [Firestore avec Jetpack Compose](#1-firestore-avec-jetpack-compose)  
2. [Architecture MVVM et Clean Architecture](#2-architecture-mvvm-et-clean-architecture)  
3. [Flux de données avec Firestore et Flow](#3-flux-de-données-avec-firestore-et-flow)  
4. [Gestion hors ligne et performances](#4-gestion-hors-ligne-et-performances)  
5. [Conclusion et Ressources](#5-conclusion-et-ressources)  

---

### 1. Firestore avec Jetpack Compose

**Firestore** est une base de données **NoSQL** en temps réel et flexible, parfaitement intégrée avec **Jetpack Compose** pour construire des interfaces réactives.

#### Configuration de Firestore

1. Créez un projet sur la console **Firebase**.  
2. Ajoutez votre application **Android** au projet en fournissant le `package name`.  
3. Configurez le fichier `google-services.json` dans le dossier `app` de votre projet `Android`.  
4. Ajoutez les dépendances nécessaires à `build.gradle` :

```gradle
dependencies {
    implementation (libs.firebase.firestore.ktx)
}
```

#### Opérations CRUD (Create, Read, Update, Delete)

**Créer un document** :

```kotlin
val db = Firebase.firestore
val user = hashMapOf(
    "name" to "Alice",
    "email" to "alice@example.com"
)

db.collection("users")
    .add(user)
    .addOnSuccessListener { documentReference ->
        Log.d("Firestore", "Document ajouté avec ID : ${documentReference.id}")
    }
    .addOnFailureListener { e ->
        Log.e("Firestore", "Erreur lors de l'ajout du document", e)
    }
```

**Lire des données en temps réel** :

```kotlin
db.collection("users")
    .addSnapshotListener { snapshots, e ->
        if (e != null) {
            Log.w("Firestore", "Erreur lors de l'écoute", e)
            return@addSnapshotListener
        }

        for (doc in snapshots!!) {
            Log.d("Firestore", "${doc.id} => ${doc.data}")
        }
    }
```

**Mettre à jour un document** :

```kotlin
val updates = hashMapOf<String, Any>(
    "name" to "Alice Updated"
)

db.collection("users").document("documentId")
    .update(updates)
    .addOnSuccessListener { Log.d("Firestore", "Mise à jour réussie") }
    .addOnFailureListener { e -> Log.w("Firestore", "Erreur de mise à jour", e) }
```

---

### 2. Architecture MVVM et Clean Architecture

L’architecture **MVVM** avec **Firestore** permet une séparation claire des responsabilités et améliore la maintenabilité de votre application.

#### Structure des Couches

1. **Data Layer** : Interactions avec **Firestore** via des **Repositories**.
2. **Domain Layer** : Contient les règles métier et les cas d'utilisation.
3. **Presentation Layer** : **ViewModels** exposant des données réactives à l'interface.

#### Exemple de Repository avec Firestore

```kotlin
class UserRepository(private val firestore: FirebaseFirestore) {

    fun getUsers(): Flow<List<User>> = callbackFlow {
        val listener = firestore.collection("users")
            .addSnapshotListener { snapshots, e ->
                if (e != null) {
                    close(e)
                } else {
                    val users = snapshots!!.map { it.toObject(User::class.java) }
                    trySend(users)
                }
            }
        awaitClose { listener.remove() }
    }
}
```

#### Injection de Dépendances avec Hilt

```kotlin
@Module
@InstallIn(SingletonComponent::class)
object FirestoreModule {

    @Provides
    fun provideFirestore(): FirebaseFirestore = Firebase.firestore

    @Provides
    fun provideUserRepository(firestore: FirebaseFirestore): UserRepository =
        UserRepository(firestore)
}
```

---

### 3. Flux de données avec Firestore et Flow

**Flow** simplifie la gestion des données réactives, en combinaison avec **Firestore** pour une synchronisation en temps réel.

#### Exemple avec ViewModel et Flow

```kotlin
class UserViewModel(private val userRepository: UserRepository) : ViewModel() {

    private val _users = MutableStateFlow<List<User>>(emptyList())
    val users: StateFlow<List<User>> = _users

    init {
        viewModelScope.launch {
            userRepository.getUsers().collect { _users.value = it }
        }
    }
}
```

#### Opérateurs Flow utiles

- `map` : Transformer les données.
- `filter` : Filtrer les résultats.
- `combine` : Fusionner plusieurs flux.

---

### 4. Gestion hors ligne et performances

#### Activer la persistance hors ligne

```kotlin
Firebase.firestore.setFirestoreSettings(
    FirestoreSettings.Builder()
        .setPersistenceEnabled(true)
        .build()
)
```

#### Pagination avec Firestore

Utiliser `limit()` et `startAfter()` pour gérer de grandes collections.

```kotlin
db.collection("users")
    .orderBy("name")
    .startAfter(lastDocumentSnapshot)
    .limit(10)
    .get()
    .addOnSuccessListener { result ->
        for (document in result) {
            Log.d("Firestore", "${document.id} => ${document.data}")
        }
    }
```

#### Optimisation des requêtes

- Structurer les collections avec des **sous-collections**.
- Ajouter des index pour des requêtes complexes.

---

### 5. Conclusion et Ressources

**Firestore**, lorsqu’il est bien intégré avec **Jetpack Compose** et une architecture moderne comme **MVVM**, permet de créer des applications réactives, performantes, et maintenables.

**Ressources recommandées :**  

- [Documentation Firestore](https://firebase.google.com/docs/firestore)  
- [Tutoriels Jetpack Compose](https://developer.android.com/jetpack/compose)  
- [MVVM avec Compose](https://developer.android.com/topic/architecture)

---