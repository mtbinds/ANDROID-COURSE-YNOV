
# TP Pratique : Cycle de vie, ViewModel, et Gestion des ressources avec Jetpack Compose

## Objectifs du TP

Ce TP couvre l’intégration de `Jetpack Compose` avec le cycle de vie **Android**, la gestion des états avec `ViewModel`, et l’utilisation des ressources pour créer une interface utilisateur dynamique et robuste.

---

### Table des Matières

1. [Cycle de vie de l’activité et des fragments](#1-cycle-de-vie-de-lactivité-et-des-fragments)
2. [AppCompat et Compose](#2-appcompat-et-compose)
3. [ViewModel et Compose](#3-viewmodel-et-compose)
4. [Gestion des ressources](#4-gestion-des-ressources)
5. [Conclusion et Ressources](#5-conclusion-et-ressources)

---

### 1. Cycle de vie de l’activité et des fragments

`Jetpack Compose` doit souvent interagir avec le cycle de vie d’Android, notamment dans les activités et les fragments.

#### Exercice 1 : Gérer le cycle de vie avec un composable

1. Créez un composable qui affiche un message différent en fonction de l’état de cycle de vie (`ON_RESUME`, `ON_PAUSE`).
2. Utilisez `DisposableEffect` et `LifecycleEventObserver` pour surveiller le cycle de vie.

```kotlin
@Composable
fun LifecycleAwareComponent() {
    val lifecycleOwner = LocalLifecycleOwner.current
    DisposableEffect(lifecycleOwner) {
        val observer = LifecycleEventObserver { _, event ->
            when (event) {
                Lifecycle.Event.ON_RESUME -> println("En mode Actif")
                Lifecycle.Event.ON_PAUSE -> println("En Pause")
                else -> Unit
            }
        }
        lifecycleOwner.lifecycle.addObserver(observer)
        onDispose { lifecycleOwner.lifecycle.removeObserver(observer) }
    }
    Text("Composant qui observe le cycle de vie")
}
```

**Objectif** : Maîtriser l’intégration de composants Compose avec le cycle de vie d'Android.

---

### 2. AppCompat et Compose

`Compose` peut être intégré dans des projets `AppCompat` existants pour introduire de nouveaux composants sans refonte complète.

#### Exercice 2 : Intégrer une vue Compose dans une activité existante

1. Créez une activité utilisant un layout **XML**.
2. Ajoutez un `ComposeView` dans le layout **XML** et utilisez-le pour afficher un composable simple depuis le code de l'activité.

```xml
<androidx.compose.ui.platform.ComposeView
    android:id="@+id/compose_view"
    android:layout_width="match_parent"
    android:layout_height="wrap_content" />
```

**Dans l’activité :**

```kotlin
class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        findViewById<ComposeView>(R.id.compose_view).setContent {
            Text("Hello from Compose inside XML!")
        }
    }
}
```

**Objectif** : Apprendre à intégrer Compose dans des activités et layouts existants.

---

### 3. ViewModel et Compose

Le `ViewModel` est une solution pour gérer l’état de manière persistante dans `Compose`.

#### Exercice 3 : Utiliser ViewModel pour persister l’état d’un compteur

1. Créez un `CounterViewModel` avec une fonction pour incrémenter un compteur.
2. Utilisez le ViewModel dans un composable pour afficher et incrémenter la valeur du compteur.

```kotlin
class CounterViewModel : ViewModel() {
    private val _counter = MutableLiveData(0)
    val counter: LiveData<Int> = _counter

    fun increment() {
        _counter.value = (_counter.value ?: 0) + 1
    }
}

@Composable
fun CounterScreen(viewModel: CounterViewModel = viewModel()) {
    val count by viewModel.counter.observeAsState(0)
    Column {
        Text("Counter: $count")
        Button(onClick = { viewModel.increment() }) {
            Text("Increment")
        }
    }
}
```

**Objectif** : Apprendre à utiliser `ViewModel` pour persister l’état entre recompositions.

---

### 4. Gestion des Ressources

Compose offre des moyens simples pour accéder aux ressources dans Android.

#### Exercice 4 : Appliquer des couleurs et des chaînes de caractères

1. Créez un composable qui utilise `colorResource`, `stringResource`, et `dimensionResource` pour styliser son contenu.
2. Ajoutez des ressources de couleur, de chaînes, et de dimensions dans le fichier `res`.

```kotlin
@Composable
fun ResourceStyledComponent() {
    Text(
        text = stringResource(id = R.string.app_name),
        color = colorResource(id = R.color.colorPrimary),
        fontSize = dimensionResource(id = R.dimen.text_size_large).value.sp
    )
}
```

**Objectif** : Apprendre à accéder aux ressources **Android** dans un composable pour un style cohérent.

---

### 5. Conclusion et Ressources

En suivant ce TP, vous avez exploré l’intégration de `Compose` avec les cycles de vie **Android**, les `ViewModels`, et l’utilisation des ressources.

#### Ressources

- [Documentation Compose et Cycle de Vie](https://developer.android.com/jetpack/compose/lifecycle)
- [Documentation ViewModel avec Compose](https://developer.android.com/topic/libraries/architecture/viewmodel)

---