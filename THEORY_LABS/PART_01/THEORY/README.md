
# Partie 1 : Introduction à Kotlin

---

### Table des Matières

1. [Bases de Kotlin](#1-bases-de-kotlin)
2. [Collections et boucles](#2-collections-et-boucles)
3. [Classes et objets](#3-classes-et-objets)
4. [Fonctions avancées et Lambda](#4-fonctions-avancées-et-lambda)
5. [Gestion des nulls](#5-gestion-des-nulls)
6. [Conclusion et Ressources](#6-conclusion-et-ressources)

---

### 1. Bases de Kotlin

`Kotlin` est un langage de programmation moderne, concis, et conçu pour être interopérable avec `Java`. Il supporte des concepts avancés comme la programmation fonctionnelle et offre des fonctionnalités telles que la null safety pour des applications plus sûres.

#### Types de données et Variables

`Kotlin` dispose de types basiques comme :

- **Int** : Entiers
- **Double** : Décimaux
- **Boolean** : Valeurs booléennes

Les variables en `Kotlin` peuvent être :

- **val** : Valeur immuable (ne peut pas être modifiée après attribution).
- **var** : Valeur mutable.

**Exemple :**

```kotlin
val nombre = 10 // immuable
var compteur = 5 // mutable
```

#### Fonctions

Les fonctions en `Kotlin` peuvent avoir des paramètres par défaut et des types de retour explicites.

```kotlin
fun ajouter(a: Int, b: Int = 5): Int {
    return a + b
}
```

#### Structures conditionnelles

`Kotlin` utilise `if` comme expression et possède également `when` pour les conditions multiples.

```kotlin
fun evaluation(score: Int): String {
    return when {
        score >= 90 -> "Excellent"
        score >= 70 -> "Bien"
        else -> "À améliorer"
    }
}
```

---

### 2. Collections et Boucles

`Kotlin` propose plusieurs types de collections :

#### Types de collections

- **List** : Ordonnée, peut contenir des doublons.
- **Set** : Uniquement des éléments uniques.
- **Map** : Paires clé-valeur.

```kotlin
val liste = listOf(1, 2, 3)
val ensemble = setOf("A", "B", "C")
val dictionnaire = mapOf("clé1" to "valeur1", "clé2" to "valeur2")
```

#### Boucles

`Kotlin` simplifie les boucles grâce à des méthodes comme `forEach` et `map`.

```kotlin
liste.forEach { println(it) }
val nouvellesValeurs = liste.map { it * 2 }
```

---

### 3. Classes et Objets

`Kotlin` prend en charge la **programmation orientée objet**, avec des fonctionnalités comme les **classes de données** et les **objets singleton**.

#### Classes et Constructeurs

Les classes peuvent avoir des constructeurs principaux et secondaires.

```kotlin
class Personne(val nom: String, var age: Int) {
    fun sePresenter() = "Je suis $nom et j'ai $age ans"
}
```

#### Héritage et Interfaces

`Kotlin` utilise `open` pour permettre l'héritage.

```kotlin
open class Animal(val nom: String) {
    open fun faireDuBruit() = "$nom fait un bruit"
}

class Chien(nom: String) : Animal(nom) {
    override fun faireDuBruit() = "$nom aboie"
}
```

---

### 4. Fonctions avancées et Lambda

Les **lambdas** et les **fonctions d'ordre supérieur** sont des composants puissants de `Kotlin`.

#### Lambdas et Fonctions de Niveau Supérieur

Les **lambdas** permettent de passer des blocs de code comme paramètres.

```kotlin
val multiplier = { a: Int, b: Int -> a * b }
println(multiplier(2, 3)) // Résultat : 6
```

#### Extensions

Les **fonctions d'extension** permettent d'ajouter des fonctionnalités aux classes existantes.

```kotlin
fun String.inverser(): String {
    return this.reversed()
}
println("Bonjour".inverser()) // Résultat : ruojnoB
```

---

### 5. Gestion des Nulls

`Kotlin` offre une gestion de la sécurité des nulls pour éviter les erreurs de nullité.

#### Null Safety

`Kotlin` utilise `?` pour indiquer qu'une variable peut être `null`.

```kotlin
val nom: String? = null
println(nom?.length) // Utilisation de l'opérateur safe-call
```

#### Opérateurs pour la gestion des nulls

- **Elvis (`?:`)** : Fournit une valeur par défaut si la variable est `null`.
- **`!!`** : Force une variable nullable à ne pas être `null`, ce qui peut provoquer une exception si c'est le cas.

```kotlin
val longueur = nom?.length ?: 0
```

---

### 6. Conclusion et Ressources

`Kotlin` est un langage riche, parfait pour le développement Android et les applications de serveur grâce à sa sécurité et ses fonctionnalités modernes.

Pour approfondir :

- [Documentation officielle de Kotlin](https://kotlinlang.org/docs/reference/)
- [Cours interactifs Kotlin sur Kotlin Playground](https://play.kotlinlang.org/)

---