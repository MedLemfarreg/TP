# Série 1 — Synchrone, asynchrone et fonctions comme valeurs

**Nom :** Lemfarreg  **Prénom :** Mohammed  **Groupe :** SICRAT  **Date :** 03/11/2026

> Mode d'emploi : répondez sous chaque question, à la place des points de suspension (`…`). Les blocs `dart` sont pour votre code, les blocs sans langage pour la sortie observée dans le terminal.

## Partie A — TD

### TD 1 — Vocabulaire
**a)** Classez en *synchrone* / *asynchrone* :
1. Attendre au guichet jusqu'à son tour : synchrone
2. Lancer une machine à laver et ranger la cuisine : asynchrone
3. Appeler un ami et attendre qu'il décroche sans rien faire d'autre : synchrone
4. Commander un plat et s'asseoir pendant la préparation : asynchrone
5. Calculer `3 * 4` dans un programme : synchrone

**b)** Rapide ou lent ? Trier 10 nombres : rapide | Télécharger une image : lent | Lire un capteur Bluetooth : lent | Comparer deux entiers : rapide

**c)** Images perdues pendant 200 ms à 60 images/s (détaillez le calcul) :

200 ms = 0,2 s, et on affiche 60 images par seconde, donc :

60 × 0,2 = 12 images

Environ 12 images sont perdues pendant 200 ms.

**d)** Différence entre concurrence et parallélisme :

La concurrence, c'est gérer plusieurs tâches en même temps en les faisant avancer chacune à tour de rôle (on peut n'avoir qu'un seul cœur). Le parallélisme, c'est exécuter vraiment plusieurs tâches au même instant, sur plusieurs cœurs.

### TD 2 — Pourquoi bloquer est un problème
Pourquoi `sleep(Duration(seconds: 2))` fige-t-il l'écran ? Quel adjectif décrit une opération qui ne fige pas le programme ?

`sleep` bloque le fil d'exécution principal pendant 2 secondes : le programme ne peut plus rien faire d'autre, ni redessiner l'écran, ni réagir aux clics. L'écran est donc figé. Une opération qui ne fige pas le programme est dite asynchrone (non bloquante).

### TD 3 — Types de fonctions
a) `(int a, int b) => a + b` : une fonction qui prend deux `int` et retourne un `int` → `int Function(int, int)`
b) `(String s) => s.length` : une fonction qui prend un `String` et retourne un `int` → `int Function(String)`
c) `() => print('ok')` : une fonction sans paramètre qui ne retourne rien → `void Function()`
d) `(double x) => x > 0` : une fonction qui prend un `double` et retourne un `bool` → `bool Function(double)`
e) `(List<int> l) => l.first` : une fonction qui prend une `List<int>` et retourne un `int` → `int Function(List<int>)`

### TD 4 — Prédire la sortie
Sortie du programme (une valeur par ligne) :
```
Closure: (int) => int
16
```
Différence entre `f` et `f(4)` :

`f` est la fonction elle-même (on la manipule comme une valeur, sans l'exécuter). `f(4)` appelle la fonction avec 4 et donne son résultat, ici 16.

### TD 5 — Paramètres nommés
**a)** Appels valides ? (oui/non + justification si refus)
1. `inscrire('Sara', filiere: 'MIP');` : oui. `nom` est positionnel, `filiere` est donné par son nom, et `age` prend sa valeur par défaut.
2. `inscrire('Sara');` : non. Il manque `filiere`, qui est un paramètre nommé obligatoire (`required`).
3. `inscrire('Sara', 20, filiere: 'MIP');` : non. Il y a un seul paramètre positionnel (`nom`) ; `age` est un paramètre nommé, donc il faut écrire `age: 20`.
4. `inscrire('Lina', filiere: 'GI', age: 20);` : oui. Les paramètres nommés peuvent être écrits dans n'importe quel ordre.
5. `inscrire(nom: 'Omar', filiere: 'GI');` : non. `nom` est positionnel, on ne peut pas l'appeler avec `nom:`.

**b)** Valeur de `age` dans l'appel 1 : `18`, la valeur par défaut de la signature, puisqu'on n'a pas donné `age`.

**c)** Réécriture de `estPair` en syntaxe flèche :
```dart
bool estPair(int n) => n % 2 == 0;
```
Pourquoi la flèche est-elle impossible avec deux instructions ?

La flèche `=>` ne accepte qu'une seule expression, dont la valeur est retournée automatiquement. Avec deux instructions, il faut un bloc `{ ... }` avec un `return`.

## Partie B — TP

### TP 1 — Mesurer le blocage
**1. Code (3 messages, `sleep`, `Stopwatch`) :**
```dart
import 'dart:io';

void main() {
  final chrono = Stopwatch()..start();
  print('A');
  sleep(const Duration(milliseconds: 500));
  print('B');
  sleep(const Duration(milliseconds: 500));
  print('C');
  print('Temps total : ${chrono.elapsedMilliseconds} ms');
}
```
Sortie observée :
```
A
B
C
Temps total : 1002 ms
```
**2. Sortie observée du programme avec `Future.delayed` :**
```dart
void main() {
  final chrono = Stopwatch()..start();
  print('A');
  Future.delayed(const Duration(milliseconds: 500), () => print('C'));
  print('B');
  print('Temps total : ${chrono.elapsedMilliseconds} ms');
}
```
```
A
B
Temps total : 3 ms
C
```
**3. Pourquoi `C` apparaît-il après `B` ?**

Parce que `Future.delayed` ne bloque pas : il programme l'affichage de `C` pour dans 500 ms et le programme continue tout de suite avec `B`. `C` s'affiche plus tard, quand le délai est terminé. Avec `sleep`, au contraire, tout reste bloqué pendant l'attente (1 seconde au total).

### TP 2 — Boîte à outils de fonctions
```dart
// 1. triple
int triple(int x) => x * 3;

// 2. repeter
String repeter(String s, int n) {
  var resultat = '';
  for (var i = 0; i < n; i++) {
    resultat += s;
  }
  return resultat;
}

// 3. transformer
List<int> transformer(List<int> liste, int Function(int) f) {
  return liste.map(f).toList();
}

// 4. seuil (bonus)
bool seuil(int x, int limite) => x >= limite;

// main
void main() {
  print(triple(4));
  print(repeter('ha', 3));
  print(transformer([1, 2, 3], triple));
  print(seuil(10, 5));
}
```
Sortie observée :
```
12
hahaha
[3, 6, 9]
true
```

### TP 3 — Mini-calculatrice
```dart
// 1-2. tester + quatre appels
double add(double a, double b) => a + b;
double sub(double a, double b) => a - b;
double mul(double a, double b) => a * b;
double div(double a, double b) => a / b;

void tester(double Function(double, double) f) {
  print(f(3, 4));
}

// 3. choisir
double Function(double, double) choisir(String op) {
  switch (op) {
    case '+':
      return add;
    case '-':
      return sub;
    case '*':
      return mul;
    case '/':
      return div;
    default:
      throw ArgumentError('Opération inconnue');
  }
}

// 4. afficherFiche + trois appels
void afficherFiche(String nom, int age, String filiere) {
  print('Nom : $nom');
  print('Age : $age');
  print('Filière : $filiere');
}

void main() {
  tester(add);
  tester(sub);
  tester(mul);
  tester(div);

  print(choisir('*')(5, 6));

  afficherFiche('Sara', 18, 'MIP');
  afficherFiche('Lina', 20, 'GI');
  afficherFiche('Omar', 17, 'SICRAT');
}
```
Sortie observée :
```
7.0
-1.0
12.0
0.75
30.0
Nom : Sara
Age : 18
Filière : MIP
Nom : Lina
Age : 20
Filière : GI
Nom : Omar
Age : 17
Filière : SICRAT
```

## Remarques / difficultés rencontrées
Le plus difficile, c'était de bien distinguer une fonction (`f`) de son appel (`f(4)`). J'ai aussi mis un peu de temps à comprendre que `Future.delayed` ne bloque pas le programme : le code continue et la suite s'exécute plus tard, grâce à la boucle d'événements.
