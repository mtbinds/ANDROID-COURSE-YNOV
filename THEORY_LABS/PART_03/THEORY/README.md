
# Partie 3 : Intégration Android et Compose

---

### Table des Matières

1. [Cycle de vie de l’activité et des fragments](#1-cycle-de-vie-de-lactivité-et-des-fragments)
2. [AppCompat et Compose](#2-appcompat-et-compose)
3. [ViewModel et Compose](#3-viewmodel-et-compose)
4. [Gestion des ressources](#4-gestion-des-ressources)
5. [Conclusion et Ressources](#5-conclusion-et-ressources)

---

### 1. Cycle de vie de l’activité et des fragments

`Jetpack Compose` s'intègre dans le cycle de vie d'**Android**, mais introduit des particularités liées à son paradigme déclaratif et réactif.

#### Cycle de vie des Activités et Fragments

- **Activité** : L'activité est le conteneur principal qui gère les éléments d'interface. Elle passe par des états de cycle de vie tels que `onCreate`, `onStart`, `onResume`, `onPause`, `onStop`, et `onDestroy`.
- **Fragment** : Similaire à l'activité, mais conçu pour une modularité plus fine de l'interface.

#### Intégration de Compose

L'annotation `@Composable` permet à une fonction de gérer ses propres recompositions sans se soucier du cycle de vie. Cependant, il est important de gérer les états de manière efficace.

**Gestion des recompositions** :

- Les recompositions sont déclenchées chaque fois qu'un état observable change.
- Utiliser `remember` pour persister l'état entre recompositions.

**Exemple :**

```kotlin
@Composable
fun LifecycleAwareComponent() {
    val lifecycleOwner = LocalLifecycleOwner.current
    DisposableEffect(lifecycleOwner) {
        val observer = LifecycleEventObserver { _, event ->
            when(event) {
                Lifecycle.Event.ON_START -> println("Commencez!")
                Lifecycle.Event.ON_STOP -> println("Arrêtez!")
                else -> Unit
            }
        }
        lifecycleOwner.lifecycle.addObserver(observer)
        onDispose { lifecycleOwner.lifecycle.removeObserver(observer) }
    }
}
```

---

### 2. AppCompat et Compose

`Compose` peut être intégré dans des projets **Android** existants basés sur les vues traditionnelles.

#### AndroidView et Compose dans des vues XML

- **AndroidView** : Permet d’intégrer des vues **XML** dans un environnement `Compose`.
- **ComposeView** : Permet d'intégrer des composants `Compose` dans des fragments **XML** classiques.

**Exemple :**

```kotlin
@Composable
fun AndroidViewExample() {
    AndroidView(factory = { context ->
        TextView(context).apply {
            text = "Vue XML intégrée dans Compose"
        }
    })
}
```

#### Utiliser Compose dans une application AppCompat

- **ComposeView** dans les activités **XML** traditionnelles pour afficher les composants `Compose`.
- **Compatibilité avec AppCompat** pour ajouter des composants modernes sans refonte complète.

---

### 3. ViewModel et Compose

Le **ViewModel** est un composant clé pour gérer l’état de l’interface dans les applications **Android** modernes, et `Compose` s’intègre parfaitement avec.

#### Utilisation du ViewModel dans Compose

- **Avantages** : Gestion des chahttps://genius.com/Insanity-battle-rap-freestyle-lyricser `viewModel()` pour accéder aux instances du `ViewModel`.

```kotlin
@Composable
fun MyScreen(viewModel: MyViewModel = viewModel()) {
    val uiState by viewModel.uiState.collectAsState()
    Text(text = "Données de l'état: ${uiState.data}")
}
```

#### Communication entre ViewModel et Composables

Utiliser `LiveData` ou `StateFlow` pour observer les changements d'état.

---

### 4. Gestion des ressources

`Compose` permet un accès fluide aux ressources telles que les couleurs, les chaînes de caractères, et les dimensions.

#### Accéder aux ressources

- **Couleurs** : `colorResource(id = R.color.colorPrimary)` pour les couleurs définies dans les ressources.
- **Chaînes de caractères** : `stringResource(id = R.string.app_name)` pour les chaînes localisées.
- **Dimensions** : `dimensionResource(id = R.dimen.padding_standard)` pour les valeurs de padding et marges.

**Exemple :**

```kotlin
@Composable
fun ResourceExample() {
    Text(
        text = stringResource(id = R.string.hello_world),
        color = colorResource(id = R.color.colorAccent),
        modifier = Modifier.padding(dimensionResource(id = R.dimen.padding_large))
    )
}
```

---

### 5. Conclusion et Ressources

**Compose** s'intègre naturellement dans l'écosystème **Android**, apportant de nouvelles fonctionnalités tout en restant compatible avec les architectures existantes.

Pour approfondir :

- [Guide officiel sur Jetpack Compose](https://developer.android.com/jetpack/compose)
- [Documentation ViewModel](https://developer.android.com/topic/libraries/architecture/viewmodel)

---