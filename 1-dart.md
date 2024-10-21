# Fiche d'exercices : Découverte du langage Dart

## Exercice 1 : Compréhension de code simple
Lisez le code Dart ci-dessous et expliquez ce qu'il fait. Identifiez les particularités syntaxiques du langage Dart par rapport à ce que vous connaissez (Java, C#, JavaScript).

```dart
void main() {
  var name = 'Alice';
  var age = 25;
  print('Name: $name, Age: $age');
}
```

<details>
<summary>Solution</summary>

Le code déclare deux variables avec le mot-clé `var`, affiche les valeurs en utilisant l'interpolation de chaînes avec `$`. Dart est moins verbeux que Java ou C# pour la déclaration des types grâce à l'inférence.

</details>


## Exercice 2 : Boucles et collections

Écrivez une fonction Dart qui prend une liste d'entiers en paramètre et retourne la somme de tous les éléments impairs de cette liste.


<details>
<summary>Solution</summary>

```dart
int sumOfOddNumbers(List<int> numbers) {
  return numbers.where((n) => n.isOdd).reduce((a, b) => a + b);
}
```

</details>


## Exercice 3 : Fonctions anonymes et lambdas
Que produit le code suivant ? Expliquez l'utilisation des fonctions anonymes et la différence avec les lambdas en Java ou C#.

```dart
void main() {
  var list = [1, 2, 3, 4];
  list.forEach((n) => print(n * 2));
}
```

<details>
<summary>Solution</summary>

Le code imprime 2, 4, 6, 8. Les fonctions anonymes sont similaires aux lambdas en Java et C#. Dart utilise une syntaxe concise avec `=>` pour des fonctions à expression unique.

</details>


## Exercice 4 : Classes et objets
Créez une classe `Person` en Dart avec deux champs `name` et `age`, ainsi qu'une méthode `greet` qui affiche un message personnalisé. Comparez avec la manière de créer des classes en Java et C#.

<details>
<summary>Solution</summary>

```dart
class Person {
  String name;
  int age;

  Person(this.name, this.age);

  void greet() {
    print('Hello, my name is $name and I am $age years old.');
  }
}

void main() {
  var p = Person('John', 30);
  p.greet();
}
```
</details>


## Exercice 5 : Paramètres nommés et facultatifs
Expliquez le fonctionnement des paramètres nommés et facultatifs en Dart à partir du code suivant. Comment se compare-t-il avec TypeScript ?

```dart
void greet({String name = 'Guest', int age = 18}) {
  print('Hello $name, you are $age years old.');
}

void main() {
  greet(age: 25);
}
```


<details>
<summary>Solution</summary>

Le code Dart utilise des paramètres nommés avec des valeurs par défaut. Cela est similaire à TypeScript, mais Dart demande des accolades `{}` pour les paramètres nommés.

</details>


## Exercice 6 : Asynchronisme avec `async` et `await`
Expliquez ce que fait ce programme et comparez-le avec la gestion de l'asynchronisme en JavaScript (promesses) et en C# (Tâches).

```dart
Future<String> fetchData() async {
  await Future.delayed(Duration(seconds: 2));
  return 'Data fetched';
}

void main() async {
  print('Fetching data...');
  var data = await fetchData();
  print(data);
}
```

<details>
<summary>Solution</summary>

Le programme retarde l'exécution de 2 secondes avant de renvoyer 'Data fetched'. Dart utilise `async` et `await` comme C# et JS pour gérer l'asynchronisme, mais son modèle est plus proche de celui de JS avec `Future`.

</details>


## Exercice 7 : Gestion des exceptions
Modifiez la fonction suivante pour gérer les exceptions potentielles lorsque `fetchData` échoue. Comparez avec la gestion des exceptions en C# et Java.

```dart
Future<String> fetchData() async {
  throw Exception('Data not found');
}
void main() async {
  print('Fetching data...');
  var data = await fetchData();
  print(data);
}
```

<details>
<summary>Solution</summary>

```dart
Future<String> fetchData() async {
  throw Exception('Data not found');
}

void main() async {
  try {
    print('Fetching data...');
    var data = await fetchData();
    print(data);
  } catch (e) {
    print('Error: $e');
  }
}
```
</details>


## Exercice 8 : Extensions et opérateurs

Créez une extension sur le type `int` pour ajouter une méthode `isEvenOrOdd` qui retourne une chaîne indiquant si l'entier est pair ou impair.

<details>

<summary>Solution</summary>

```dart
extension EvenOrOdd on int {
  String isEvenOrOdd() {
    return this.isEven ? 'Even' : 'Odd';
  }
}

void main() {
  print(5.isEvenOrOdd()); // Odd
  print(2.isEvenOrOdd()); // Even
}
```

</details>

## Exercice 9 : Généricité
Écrivez une fonction générique en Dart qui prend une liste de n'importe quel type et retourne la première valeur de la liste. Comparez avec la syntaxe générique en C# et Java.

<details>

<summary>Solution</summary>

```dart
T getFirst<T>(List<T> items) {
  return items[0];
}

void main() {
  print(getFirst([1, 2, 3])); // 1
  print(getFirst(['a', 'b', 'c'])); // a
}
```

</details>


## Exercice 10 : Comprendre les mixins en Dart

Lisez le code suivant qui montre l’utilisation de mixins en Dart. Ensuite, répondez aux questions qui suivent pour tester votre compréhension.

```dart
mixin Walker {
  void walk() => print('Walking');
  void common() => print('Common method from Walker');
}

mixin Swimmer {
  void swim() => print('Swimming');
  void common() => print('Common method from Swimmer');
}

class Animal {
  void breathe() => print('Breathing');
  void common() => print('Common method from Animal');
}

class Human extends Animal with Walker, Swimmer {
  void speak() => print('Speaking');
  void common() => print('Common method from Human');
}

void main() {
  var human = Human();
  human.breathe(); // From Animal
  human.walk();    // From Walker mixin
  human.swim();    // From Swimmer mixin
  human.speak();   // From Human class
  human.common();  // From ?
}
```

### Questions :

1. Quelle est la différence entre l’utilisation des mixins et l’héritage simple en Dart ?
2. Pourquoi utilise-t-on le mot-clé `with` dans ce code ? Quelle est son utilité dans ce contexte ?
3. Que se passerait-il si la classe `Human` n'étendait pas `Animal` mais utilisait uniquement les mixins `Walker` et `Swimmer` ?
4. Est-il possible pour la classe `Human` d’avoir plusieurs mixins ? Expliquez votre réponse.
5. Comment Dart gère-t-il la priorité si plusieurs mixins contiennent des méthodes ayant le même nom ?

<details>
<summary>Solution</summary>

1. Les mixins permettent de réutiliser du code dans plusieurs classes sans utiliser l'héritage. Ils permettent d'ajouter des fonctionnalités à une classe sans créer une relation parent-enfant.
2. Le mot-clé `with` est utilisé pour ajouter des mixins à une classe. Il permet d'ajouter des fonctionnalités supplémentaires à une classe sans créer une hiérarchie de classes.
3. Si la classe `Human` n'étend pas `Animal`, elle ne pourra pas accéder à la méthode `breathe` de la classe `Animal`.
4. Oui, une classe peut utiliser plusieurs mixins en les séparant par des virgules.
5. Dart utilise l'ordre de déclaration des mixins pour résoudre les conflits de noms. La méthode du mixin déclaré en dernier l'emporte. Dans ce cas, la méthode `common` de la classe `Human` sera appelée. Si on la retire de la classe `Human`, c'est la méthode `common` du mixin `Swimmer` qui sera appelée. Etc.

</details>


## Exercice 11 : Mixins et héritage

Créez deux mixins `Flyable` (qui peut voler) et `Swimmable` (qui peut nager) et appliquez-les à une classe `Duck` (qui obtiendra donc 2 méthodes `fly` et `swim`). Expliquez comment Dart gère les mixins par rapport à l'héritage multiple en C#.

<details>

<summary>Solution</summary>

```dart
mixin Flyable {
  void fly() => print('Flying');
}

mixin Swimmable {
  void swim() => print('Swimming');
}

class Duck with Flyable, Swimmable {}

void main() {
  var duck = Duck();
  duck.fly(); // Flying
  duck.swim(); // Swimming
}
```

</details>


## Exercice 12 : Déclaration et utilisation de `late`

Créez une classe `Circle` en Dart avec un champ `radius`. Utilisez le mot-clé `late` pour différer l'initialisation d'un autre champ appelé `area`. L'aire doit être calculée seulement lorsque la méthode `calculateArea` est appelée pour la première fois.

**Indications :**
- Le champ `area` ne doit pas être initialisé immédiatement dans le constructeur.
- Utilisez `late` pour garantir que l'initialisation de `area` sera différée mais effectuée avant toute utilisation.

<details>

<summary>Solution</summary>

```dart
class Circle {
  double radius;
  late double area;

  Circle(this.radius);

  void calculateArea() {
    area = 3.1415 * radius * radius;
    print('Area calculated: $area');
  }
}

void main() {
  var circle = Circle(10);
  // L'initialisation de `area` n'a pas encore eu lieu ici.
  circle.calculateArea(); // L'aire est calculée ici.
}
```
</details>


## Exercice 13 : Utilisation de `late` avec une variable complexe

Créez une classe `LazyInitialization` qui contient un champ `late` nommé `data`, qui doit être initialisé la première fois que la méthode `fetchData` est appelée. Simulez un processus de récupération de données (par exemple via un délai de 2 secondes) avant de l'initialiser.

**Indications :**
- Utilisez le mot-clé `late` pour différer l'initialisation de `data`.
- Simulez la récupération de données avec un `Future.delayed`.


<details>

<summary>Solution</summary>

```dart
class LazyInitialization {
  late String data;

  Future<void> fetchData() async {
    print('Fetching data...');
    await Future.delayed(Duration(seconds: 2));
    data = 'Data fetched from server';
    print('Data initialized: $data');
  }
}

void main() async {
  var lazy = LazyInitialization();
  // L'initialisation de `data` est différée
  await lazy.fetchData(); // `data` est initialisée ici.
}
```
</details>

## Exercice 14 : Les Streams 

Lisez attentivement le code Dart suivant qui utilise un `Stream` pour émettre une série de nombres entiers. Répondez ensuite aux questions qui suivent pour tester votre compréhension.

```dart
Stream<int> numberStream() async* {
  for (int i = 1; i <= 5; i++) {
    await Future.delayed(Duration(seconds: 1));
    yield i;
  }
}

void main() async {
  await for (var number in numberStream()) {
    print('Received: $number');
  }
}
```

#### Questions :

1. Que fait la fonction `numberStream` ?
2. À quoi sert le mot-clé `yield` dans ce contexte ?
3. Pourquoi utilise-t-on `await` dans la boucle `for` dans `main` ?
4. Comment ce code diffère-t-il de l'utilisation d'un simple `Future` ?
5. Que se passerait-il si le `Future.delayed` était retiré du code ?

<details>
<summary>Solution</summary>

1. La fonction `numberStream` crée un `Stream` asynchrone qui émet des nombres de 1 à 5 avec un délai de 1 seconde entre chaque émission.
2. Le mot-clé `yield` est utilisé pour émettre des valeurs à partir du `Stream`. Il permet de suspendre l'exécution de la fonction et de renvoyer une valeur.
3. Le `await` dans la boucle `for` permet d'attendre la fin de chaque émission avant de continuer l'exécution.
4. Ce code utilise un `Stream` pour émettre des valeurs de manière asynchrone, ce qui permet de gérer les événements de manière continue.
5. Si le `Future.delayed` était retiré, les nombres seraient émis instantanément sans délai.

</details>

## Exercice 15 : StreamController

Créez un programme Dart qui utilise un `StreamController` pour émettre des événements à partir de la console. Chaque fois que l'utilisateur tape une entrée, celle-ci doit être transmise par le `Stream` et affichée par un auditeur.

#### Indications :
- Utilisez un `StreamController`.
- Capturez l'entrée utilisateur avec `stdin.readLineSync()`.


<details>

<summary>Solution</summary>

```dart
import 'dart:async';
import 'dart:io';

void main() {
  var controller = StreamController<String>();

  // Auditeur du stream
  controller.stream.listen((data) {
    print('Received: $data');
  });

  // Émission des événements via le stream
  while (true) {
    stdout.write('Enter text: ');
    var input = stdin.readLineSync();
    if (input == 'exit') {
      break;
    }
    controller.add(input!);
  }

  controller.close();
}
```

</details>


## Exercice 16 : Création d'un Stream de chaînes de caractères

Écrivez une fonction `sentencesGenerator` qui émet un **Stream** de chaînes de caractères représentant des phrases aléatoires. Chaque phrase doit être construite en concaténant un nom et une action choisis au hasard dans deux listes distinctes (par exemple : `["Alice", "Bob", "Charlie"]` et `["mange", "court", "lit"]`). Ajoutez un délai de 1 seconde entre chaque phrase émise. Utilisez cette fonction dans un programme pour afficher chaque phrase au fur et à mesure qu'elle est émise.

#### Indications :
- Utilisez un **Stream** asynchrone pour émettre les chaînes de caractères.
- Chaque phrase doit être générée aléatoirement en combinant un élément de chaque liste.
- Ajoutez un délai de 1 seconde entre chaque émission.

<details>

<summary>Solution</summary>

```dart
import 'dart:async';
import 'dart:math';

Stream<String> sentencesGenerator() async* {
  var names = ["Alice", "Bob", "Charlie"];
  var actions = ["mange", "court", "lit"];
  var random = Random();

  while (true) {
    // Choix aléatoire d'un nom et d'une action
    var name = names[random.nextInt(names.length)];
    var action = actions[random.nextInt(actions.length)];

    // Concaténation du nom et de l'action pour former une phrase
    yield '$name $action';

    // Délai de 1 seconde
    await Future.delayed(Duration(seconds: 1));
  }
}

void main() async {
  await for (var phrase in sentencesGenerator()) {
    print('Phrase générée: $phrase');
  }
}
```

**Explication :**
- La fonction `sentencesGenerator` génère des phrases aléatoires en choisissant un nom et une action à partir de deux listes, puis les concatène.
- Un délai d'une seconde est ajouté entre chaque émission grâce à `Future.delayed`.
- Le programme principal utilise `await for` pour écouter le flux et afficher les phrases générées.

</details>