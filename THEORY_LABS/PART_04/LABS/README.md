
# TP Pratique : Styles, Animations et Interactions Avancées avec Jetpack Compose

## Objectifs du TP

Ce TP vous permettra d’explorer des concepts avancés de `Jetpack Compose` pour créer des interfaces utilisateur stylées et interactives.

---

### Table des Matières

1. [Thèmes et styles avancés](#1-thèmes-et-styles-avancés)
2. [Animations](#2-animations)
3. [Listes et RecyclerView Compose](#3-listes-et-recyclerview-compose)
4. [Gestion d'entrées utilisateur avancée](#4-gestion-dentrées-utilisateur-avancée)
5. [Interactions avancées](#5-interactions-avancées)
6. [Conclusion et Ressources](#6-conclusion-et-ressources)

---

### 1. Thèmes et Styles Avancés

Dans `Compose`, les thèmes permettent de créer un design cohérent dans toute l’application.

#### Exercice 1 : Créer un thème d'application personnalisé

1. Créez un thème en définissant une palette de couleurs et une typographie.
2. Appliquez le thème personnalisé à votre application.

```kotlin
@Composable
fun MyTheme(content: @Composable () -> Unit) {
    val colors = lightColors(
        primary = Color(0xFF6200EA),
        secondary = Color(0xFF03DAC5)
    )
    val typography = Typography(
        body1 = TextStyle(fontWeight = FontWeight.Bold, fontSize = 18.sp)
    )
    MaterialTheme(colors = colors, typography = typography) {
        content()
    }
}
```

**Objectif** : Comprendre la personnalisation des couleurs et de la typographie.

---

### 2. Animations

Les animations permettent de rendre les interfaces plus fluides et interactives.

#### Exercice 2 : Créer une animation de changement de couleur

1. Utilisez `animateColorAsState` pour animer le changement de couleur d'un composant.
2. Ajoutez un `Button` pour déclencher l'animation.

```kotlin
@Composable
fun ColorAnimationExample() {
    var isGreen by remember { mutableStateOf(true) }
    val backgroundColor by animateColorAsState(if (isGreen) Color.Green else Color.Red)
    
    Box(modifier = Modifier
        .size(100.dp)
        .background(backgroundColor)
        .clickable { isGreen = !isGreen }
    )
}
```

**Objectif** : Maîtriser les transitions de couleur avec `animateColorAsState`.

---

### 3. Listes et RecyclerView Compose

`Compose` utilise `LazyColumn` et `LazyRow` pour des listes performantes.

#### Exercice 3 : Créer une liste d'éléments

1. Utilisez `LazyColumn` pour afficher une liste de 100 éléments.
2. Ajoutez un composant pour chaque élément et modifiez son état en fonction des interactions.

```kotlin
@Composable
fun ListExample() {
    LazyColumn {
        items(100) { index ->
            Text("Élément #$index", modifier = Modifier.padding(8.dp))
        }
    }
}
```

**Objectif** : Apprendre à utiliser `LazyColumn` pour afficher des listes longues efficacement.

---

### 4. Gestion d'Entrées Utilisateur Avancée

Construire des formulaires avec validation permet de recueillir des informations de manière interactive.

#### Exercice 4 : Créer un formulaire avec validation

1. Créez un formulaire avec des champs `TextField` pour le nom et l'email.
2. Validez que l’email est au format correct avant de soumettre.

```kotlin
@Composable
fun FormWithValidation() {
    var name by remember { mutableStateOf("") }
    var email by remember { mutableStateOf("") }
    var isEmailValid by remember { mutableStateOf(true) }
    
    Column {
        TextField(value = name, onValueChange = { name = it }, label = { Text("Nom") })
        TextField(
            value = email,
            onValueChange = { 
                email = it
                isEmailValid = android.util.Patterns.EMAIL_ADDRESS.matcher(email).matches()
            },
            isError = !isEmailValid,
            label = { Text("Email") }
        )
        Button(onClick = { /* Traitement de soumission */ }) {
            Text("Soumettre")
        }
    }
}
```

**Objectif** : Apprendre à créer un formulaire avec des validations de base.

---

### 5. Interactions Avancées

`Compose` permet des interactions avancées avec les gestes utilisateur.

#### Exercice 5 : Ajouter une détection de gestes

1. Utilisez `pointerInput` et `detectTapGestures` pour créer un composant qui détecte un appui long.
2. Changez la couleur de fond en fonction de l’appui.

```kotlin
@Composable
fun GestureBox() {
    var color by remember { mutableStateOf(Color.Gray) }
    
    Box(modifier = Modifier
        .size(100.dp)
        .background(color)
        .pointerInput(Unit) {
            detectTapGestures(onLongPress = { color = Color.Red })
        }
    )
}
```

**Objectif** : Apprendre à utiliser `pointerInput` pour créer des interactions avancées.

---

### 6. Conclusion et Ressources

Ce TP vous a permis de vous familiariser avec les styles avancés, les animations, et les interactions utilisateur en `Jetpack Compose`.

#### Ressources

- [Guide avancé sur les thèmes et styles](https://developer.android.com/jetpack/compose/design)
- [Animations en Jetpack Compose](https://developer.android.com/jetpack/compose/animation)

---