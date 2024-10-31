
# Partie 4 : Approfondissement de Jetpack Compose

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

`Compose` offre des options puissantes pour créer des thèmes personnalisés, ce qui permet d'assurer la cohérence visuelle d'une application.

#### Créer un Thème d'Application

- Utiliser `MaterialTheme` pour centraliser les couleurs, typographies, et formes.
- **Palette de Couleurs** : Définir les couleurs principales et secondaires avec `darkColors()` et `lightColors()`.

```kotlin
@Composable
fun AppTheme(content: @Composable () -> Unit) {
    MaterialTheme(
        colors = lightColors(
            primary = Color(0xFF6200EE),
            primaryVariant = Color(0xFF3700B3),
            secondary = Color(0xFF03DAC6)
        ),
        typography = Typography(
            body1 = TextStyle(
                fontFamily = FontFamily.Default,
                fontWeight = FontWeight.Normal,
                fontSize = 16.sp
            )
        )
    ) {
        content()
    }
}
```

#### Typographies Personnalisées

Créer des styles de texte personnalisés et les appliquer à travers l'application.

---

### 2. Animations

`Compose` permet de créer des animations fluides et interactives.

#### Les Fonctions `animate*`

- **`animateDpAsState`** et **`animateColorAsState`** pour animer des propriétés de taille et de couleur.

```kotlin
@Composable
fun ColorChangeAnimation() {
    var isEnabled by remember { mutableStateOf(false) }
    val backgroundColor by animateColorAsState(
        targetValue = if (isEnabled) Color.Green else Color.Red
    )
    Box(
        modifier = Modifier
            .size(100.dp)
            .background(backgroundColor)
            .clickable { isEnabled = !isEnabled }
    )
}
```

#### Transitions et Animations de Contenu

- **`updateTransition`** pour des transitions fluides entre états.
- **`AnimatedVisibility`** pour des éléments apparaissant et disparaissant avec des effets.

---

### 3. Listes et RecyclerView Compose

`Compose` remplace le RecyclerView classique par `LazyColumn` et `LazyRow`, des composants de liste performants.

#### Utilisation de `LazyColumn` et `LazyRow`

Les `LazyColumn` et `LazyRow` permettent d'afficher des listes optimisées.

```kotlin
@Composable
fun ItemList(items: List<String>) {
    LazyColumn {
        items(items) { item ->
            Text(text = item, modifier = Modifier.padding(8.dp))
        }
    }
}
```

#### Optimisations Avancées

- Utiliser `remember` pour optimiser la réutilisation des composants et réduire les recompositions.

---

### 4. Gestion d'Entrées Utilisateur Avancée

`Composer` des formulaires complexes avec validation.

#### Validation de Champs

Vérifier les entrées utilisateur en utilisant des règles de validation avec `TextField`.

```kotlin
@Composable
fun ValidatedTextField() {
    var text by remember { mutableStateOf("") }
    var isError by remember { mutableStateOf(false) }

    TextField(
        value = text,
        onValueChange = {
            text = it
            isError = text.length < 5
        },
        isError = isError,
        label = { Text("Enter at least 5 characters") }
    )
}
```

#### Gestion de Formulaires Multistep

Utiliser `rememberSaveable` pour persister les valeurs entre les recompositions dans un formulaire multi-étapes.

---

### 5. Interactions Avancées

`Compose` permet des interactions avancées avec les utilisateurs, telles que les gestures et le **drag & drop**.

#### GestureDetector

Détecter des gestes complexes.

```kotlin
@Composable
fun GestureBox() {
    Box(
        modifier = Modifier
            .size(150.dp)
            .background(Color.Gray)
            .pointerInput(Unit) {
                detectTapGestures(
                    onDoubleTap = { /* action on double tap */ },
                    onLongPress = { /* action on long press */ }
                )
            }
    )
}
```

#### Drag & Drop

Créer des interactions de **drag and drop**.

---

### 6. Conclusion et Ressources

Pour aller plus loin :

- [Documentation complète de Jetpack Compose](https://developer.android.com/jetpack/compose)
- [Animations avancées dans Compose](https://developer.android.com/jetpack/compose/animation)

---