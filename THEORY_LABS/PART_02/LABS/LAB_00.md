
# TP Pratique : Découverte de Jetpack Compose

## Objectifs du TP

Ce TP vous permettra de vous familiariser avec les concepts de base de `Jetpack Compose` en suivant des exercices guidés et des exemples pratiques pour chaque module.

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

`Jetpack Compose` simplifie la création d'interfaces avec des fonctions `@Composable`. Dans cette section, nous explorerons la création de composants simples.

#### Exercice 1 : Créer un composable simple

1. Créez un composable `Greeting` qui affiche un message de salutation avec `Text`.
2. Testez le composable en l’appelant depuis la fonction `setContent` dans votre activité principale.

```kotlin
@Composable
fun Greeting(name: String) {
    Text(text = "Hello, $name!")
}

@Preview
@Composable
fun PreviewGreeting() {
    Greeting("Compose")
}
```

**Objectif** : Familiarisez-vous avec l'annotation `@Composable` et les prévisualisations.

---

### 2. Layouts de base

Les layouts dans Compose permettent d'organiser les composants à l'écran.

#### Exercice 2 : Créer un layout combiné

1. Utilisez `Row`, `Column`, et `Box` pour structurer une interface utilisateur.
2. Créez une `Column` qui contient plusieurs `Row`, et placez des `Text` et `Button` dans chaque `Row`.

```kotlin
@Composable
fun LayoutExample() {
    Column {
        Row(horizontalArrangement = Arrangement.SpaceBetween) {
            Text("Gauche")
            Text("Droite")
        }
        Box(modifier = Modifier.fillMaxSize()) {
            Text("Centré dans la Box")
        }
    }
}
```

**Objectif** : Apprenez à structurer des interfaces avec les layouts de base.

---

### 3. Gestion des États

Dans `Compose`, l'état est utilisé pour rendre les interfaces interactives.

#### Exercice 3 : Implémenter un compteur

1. Créez un composable `Counter` qui affiche un nombre et augmente sa valeur chaque fois qu'un `Button` est cliqué.
2. Utilisez `remember` et `mutableStateOf` pour stocker et modifier l’état.

```kotlin
@Composable
fun Counter() {
    var count by remember { mutableStateOf(0) }
    Button(onClick = { count++ }) {
        Text("Count: $count")
    }
}
```

**Objectif** : Maîtriser la gestion d'état en utilisant `remember` et `mutableStateOf`.

---

### 4. Textes et Styles

Compose permet de personnaliser les styles des textes avec `TextStyle`.

#### Exercice 4 : Personnaliser un texte

1. Créez un composable `StyledText` qui affiche du texte stylisé.
2. Utilisez `TextStyle` pour modifier la taille, la couleur et le poids de la police.

```kotlin
@Composable
fun StyledText() {
    Text(
        text = "Texte stylisé",
        style = TextStyle(
            fontSize = 20.sp,
            fontWeight = FontWeight.Bold,
            color = Color.Blue
        )
    )
}
```

**Objectif** : Personnalisez l’apparence du texte pour différentes utilisations.

---

### 5. Composants d'Interface

`Compose` offre des composants d'interface pour capturer des actions utilisateur.

#### Exercice 5 : Créer un formulaire

1. Créez un formulaire basique avec `TextField`, `Checkbox`, et `Button`.
2. Stockez les valeurs de chaque composant dans un état pour les afficher lors de la soumission.

```kotlin
@Composable
fun FormExample() {
    var name by remember { mutableStateOf("") }
    var accepted by remember { mutableStateOf(false) }
    
    Column {
        TextField(value = name, onValueChange = { name = it }, label = { Text("Nom") })
        Checkbox(checked = accepted, onCheckedChange = { accepted = it })
        Button(onClick = { /* Envoyer */ }) {
            Text("Envoyer")
        }
    }
}
```

**Objectif** : Utilisez différents composants d’interface et gérez leurs états.

---

### 6. Navigation Compose

`Compose` permet la navigation entre écrans grâce à `NavController`.

#### Exercice 6 : Créer une navigation multi-écrans

1. Configurez une navigation simple avec `NavController` entre deux écrans : `HomeScreen` et `DetailScreen`.
2. Ajoutez un `Button` dans `HomeScreen` pour accéder à `DetailScreen`.

```kotlin
@Composable
fun NavigationExample() {
    val navController = rememberNavController()
    NavHost(navController, startDestination = "home") {
        composable("home") { HomeScreen(navController) }
        composable("detail") { DetailScreen(navController) }
    }
}
```

**Objectif** : Maîtriser la navigation de base entre plusieurs écrans, pour plus de détails, vous pouvez accomplir [cet exemple](https://developer.android.com/codelabs/basic-android-kotlin-compose-navigation?hl=fr#0) sur **Google Developers**.

---

### 7. Conclusion et Ressources

En terminant ce TP, vous avez exploré les bases de `Jetpack Compose` et expérimenté avec différents composants et concepts.

#### Ressources

- [Documentation officielle de Jetpack Compose](https://developer.android.com/jetpack/compose)
- [Exemples de projets Jetpack Compose](https://developer.android.com/jetpack/compose/samples)

---