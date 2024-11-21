
# Partie 2 : Introduction à Jetpack Compose

---

### Table des Matières

1. [Fondamentaux de Jetpack Compose](#1-fondamentaux-de-jetpack-compose)
2. [Layouts de base](#2-layouts-de-base)
3. [Gestion des états](#3-gestion-des-états)
4. [Textes et Styles](#4-textes-et-styles)
5. [Composants d'interface](#5-composants-dinterface)
6. [Navigation Compose](#6-navigation-compose)
7. [Conclusion et Ressources](#7-conclusion-et-ressources)

---

### 1. Fondamentaux de Jetpack Compose

`Jetpack Compose` est un **framework déclaratif** pour la création d'interfaces utilisateur **(UI)** sous **Android**, reposant sur des concepts modernes de programmation réactive. Contrairement à l'approche **XML**, `Compose` permet une meilleure **lisibilité** et **flexibilité** des composants d'interface.

#### Concepts clés

- **Composables** : Les éléments d'interface de base. Ce sont des fonctions annotées par `@Composable` qui permettent de créer des composants.
- **Programmation réactive** : `Compose` suit un paradigme réactif où l'interface est automatiquement mise à jour en fonction de l'état sous-jacent.

```kotlin
@Composable
fun Greeting(name: String) {
    Text(text = "Hello, $name!")
}
```

#### Annotations en détail

- `@Composable` : Indique que la fonction peut être utilisée comme élément d'interface.
- **Règles d'utilisation** : Les fonctions composables ne doivent pas renvoyer de valeurs, elles construisent uniquement l'UI.

---

### 2. Layouts de base

`Jetpack Compose` fournit des composants de mise en page simples et flexibles :

#### `Row`, `Column` et `Box`

- **`Row`** : Place les éléments en ligne (horizontalement).
- **`Column`** : Place les éléments en colonne (verticalement).
- **`Box`** : Permet la superposition d'éléments.

#### Études de cas : Positionnement avancé

1. **Alignement dans `Row` et `Column`** avec des propriétés comme `horizontalArrangement` et `verticalAlignment`.
2. **Modèle `Box` avec contenu centré** :

   ```kotlin
   @Composable
   fun CenteredBoxExample() {
       Box(
           modifier = Modifier.fillMaxSize(),
           contentAlignment = Alignment.Center
       ) {
           Text("Centré dans une Box")
       }
   }
   ```

3. **Combinaison de Layouts** :

   ```kotlin
   @Composable
   fun ComplexLayout() {
       Column {
           Row(horizontalArrangement = Arrangement.SpaceBetween) {
               Text("Texte à gauche")
               Text("Texte à droite")
           }
           Box {
               Text("Texte superposé dans Box")
           }
       }
   }
   ```

---

### 3. Gestion des états

Le modèle réactif de `Compose` met à jour l'interface utilisateur en fonction des changements d'état.

#### Types d'état dans Compose

- **`remember`** : Conserve l'état lors de la recomposition, utile pour des états temporaires.
- **`rememberSaveable`** : Maintient l'état lors de la rotation de l'appareil.
- **`mutableStateOf`** : Définit une variable d'état modifiable.

#### Optimisations avec `derivedStateOf`

- Utilisé pour créer des états dépendants de valeurs calculées, limitant la recomposition.

```kotlin
@Composable
fun DerivedStateExample() {
    var input by remember { mutableStateOf("") }
    val isValid by remember { derivedStateOf { input.length > 5 } }
    Text(text = if (isValid) "Valide" else "Non valide")
}
```

---

### 4. Textes et Styles

`Compose` permet des styles riches et personnalisés pour le texte.

#### Typographies et TextStyles

`Compose` inclut un ensemble de typographies prédéfinies, mais on peut les personnaliser pour des styles spécifiques.

```kotlin
@Composable
fun StyledText() {
    Text(
        text = "Compose Stylisé",
        style = TextStyle(
            fontSize = 18.sp,
            fontWeight = FontWeight.Bold,
            color = Color.Gray,
            fontStyle = FontStyle.Italic
        )
    )
}
```

#### Thème et Couleurs

Les thèmes permettent de centraliser les couleurs et styles pour une cohérence à travers l'application.

```kotlin
@Composable
fun AppTheme(content: @Composable () -> Unit) {
    MaterialTheme(
        colors = darkColors(
            primary = Color.Blue,
            secondary = Color.Cyan
        )
    ) {
        content()
    }
}
```

---

### 5. Composants d'interface

`Compose` offre des composants d'interface basiques et avancés pour une interactivité riche.

#### Composants interactifs

- **Boutons** : Boutons de commande.
- **TextField** : Entrée de texte avec validation.
- **LazyColumn / LazyRow** : Listes performantes.
- **Scaffold** : Gère les structures d'écran avancées comme la barre d'application.

Exemple d'un composant `LazyColumn` :

```kotlin
@Composable
fun ListExample() {
    LazyColumn {
        items(100) {
            Text("Item #$it")
        }
    }
}
```

---

### 6. Navigation Compose

`Compose` utilise une `API` flexible pour la navigation entre écrans.

#### Concepts de navigation

- **NavController** : Contrôle les destinations et gère l'historique.
- **NavHost et composable** : Définit les destinations et les transitions.

```kotlin
@Composable
fun AppNavigation() {
    val navController = rememberNavController()
    NavHost(navController, startDestination = "home") {
        composable("home") { HomeScreen(navController) }
        composable("profile/{userId}") { backStackEntry ->
            val userId = backStackEntry.arguments?.getString("userId")
            ProfileScreen(navController, userId)
        }
    }
}
```

---

### 7. Conclusion et Ressources

`Jetpack Compose` transforme la façon dont les applications **Android** sont conçues avec des interfaces modernes et des processus de gestion d'état simplifiés. Pour aller plus loin :

- [Documentation officielle de Jetpack Compose](https://developer.android.com/jetpack/compose)
- [Codelabs Jetpack Compose](https://developer.android.com/courses)

---
