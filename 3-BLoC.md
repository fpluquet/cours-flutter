## L'architecture en BLoC avec flutter_bloc

### **Objectifs**
- Comprendre l'architecture BLoC avec la bibliothèque `flutter_bloc`.
- Apprendre à séparer la logique métier de l'interface utilisateur.
- Utiliser les classes `Bloc`, `Event`, et `State` de `flutter_bloc` pour gérer l'état.
- Implémenter un modèle BLoC simple avec `flutter_bloc` dans une application Flutter.

### **Introduction à BLoC**

L'architecture BLoC (Business Logic Component) est un modèle de conception qui sépare la logique métier de l'interface utilisateur dans une application Flutter. Elle permet de gérer l'état de manière réactive et de structurer le code de manière modulaire et maintenable.

#### Avantages de BLoC

1. **Séparation des préoccupations** : Permet de séparer la logique métier de l'interface utilisateur.
2. **Réactivité** : Facilite la mise à jour de l'interface utilisateur en réponse aux événements.
3. **Maintenabilité** : Permet de structurer le code de manière modulaire et réutilisable.


### **Introduction à flutter_bloc**

La bibliothèque **flutter_bloc** simplifie la mise en œuvre de l'architecture BLoC en Flutter. Elle fournit des outils intégrés pour gérer la logique de l'application et l'état de manière réactive, avec des classes spécialement conçues pour structurer le code de façon modulaire et maintenable.

#### Avantages de `flutter_bloc`

1. **Structure** : Fournit un modèle organisé et extensible pour la gestion d'état.
2. **Réactivité** : Permet à l'interface utilisateur de réagir facilement aux changements d'état.
3. **Testabilité** : Facilite les tests unitaires et d'intégration.

### **Concepts Clés**

1. **Bloc** : La classe principale qui contient la logique métier et gère les événements pour émettre des états.
2. **Event** : Représente une action ou un événement utilisateur (ex : clic sur un bouton).
3. **State** : Représente l'état de l'application à un instant donné, envoyé en réponse à un événement.
4. **BlocProvider** : Un widget qui fournit le Bloc à l'arborescence des widgets.
5. **BlocBuilder** : Un widget qui reconstruit l'interface utilisateur en réponse à un changement d'état.

### **Implémentation avec flutter_bloc**

#### 1. Ajouter `flutter_bloc` au projet

Demander à `flutter` d'ajouter la dépendance `flutter_bloc` au projet.

```bash
flutter pub add flutter_bloc
```

#### 2. Créer les classes Event et State

Créez les classes pour gérer les événements et les états.

##### Exemple (CounterEvent et CounterState)

```dart
// counter_event.dart
abstract class CounterEvent {}

class IncrementCounter extends CounterEvent {}
```

```dart
// counter_state.dart
abstract class CounterState {
  final int counter;
  CounterState(this.counter);
}

class CounterInitial extends CounterState {
  CounterInitial() : super(0);
}

class CounterUpdated extends CounterState {
  CounterUpdated(int counter) : super(counter);
}
```

#### 3. Créer la classe Bloc

La classe Bloc gère les événements et émet des états en réponse en utilisant `on<Event>((event, emit) => ...)`. Chaque événement est associé à une action qui modifie l'état. L'état initial est défini dans le constructeur de la classe Bloc (`super(CounterInitial())`). Les nouveaux états sont émis en utilisant `emit(State)`. Chaque `emit` déclenche la reconstruction des widgets associés via l'utilisation du `BlocBuilder`.

##### Exemple de Code (CounterBloc)

```dart
// counter_bloc.dart
import 'package:flutter_bloc/flutter_bloc.dart';
import 'counter_event.dart';
import 'counter_state.dart';

class CounterBloc extends Bloc<CounterEvent, CounterState> {
  CounterBloc() : super(CounterInitial()) {
    on<IncrementCounter>((event, emit) {
      final newCount = state.counter + 1;
      emit(CounterUpdated(newCount));
    });
  }
}
```

On peut voir que la classe `CounterBloc` étend la classe `Bloc` de `flutter_bloc` et définit le type de `Event` et de `State` qu'elle gère. Dans le constructeur, on définit l'état initial du Bloc (`CounterInitial()`). Ensuite, on utilise la méthode `on<Event>` pour définir la logique à exécuter en réponse à un événement spécifique. Dans cet exemple, lorsqu'un événement `IncrementCounter` est reçu, on incrémente le compteur et on émet un nouvel état `CounterUpdated` avec la nouvelle valeur du compteur.

Les états devraient être en lecture seule et ne doivent pas être modifiés directement (remarquez que le champ `value` de `CounterState` est déclaré comme `final`). Ils sont émis par le Bloc en réponse aux événements et sont utilisés pour mettre à jour l'interface utilisateur.

#### 4. Fournir le Bloc à l’Interface Utilisateur

Utiliser le `BlocProvider` pour rendre le BLoC accessible dans l’arborescence des widgets.

##### Exemple de Code (main.dart)

```dart
import 'package:flutter/material.dart';
import 'package:flutter_bloc/flutter_bloc.dart';
import 'counter_bloc.dart';
import 'counter_event.dart';

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: BlocProvider(
        create: (_) => CounterBloc(),
        child: CounterScreen(),
      ),
    );
  }
}
```

On voit que le `BlocProvider` est utilisé pour fournir le `CounterBloc` à l'interface utilisateur. Le `BlocProvider` crée une instance du Bloc et la rend accessible à tous les widgets descendants. Dans cet exemple, le `CounterBloc` est fourni à l'écran `CounterScreen` et tous les widgets descendants peuvent y accéder.

Si vous avez besoin de fournir plusieurs blocs à l'interface utilisateur, vous pouvez utiliser `MultiBlocProvider` pour fournir plusieurs blocs à la fois.

#### 5. Utiliser le Bloc dans un Widget

Dans l’interface utilisateur, utilisez `BlocBuilder` pour reconstruire le widget lorsque l’état change.

##### Exemple de Code (CounterScreen.dart)

```dart
import 'package:flutter/material.dart';
import 'package:flutter_bloc/flutter_bloc.dart';
import 'counter_bloc.dart';
import 'counter_event.dart';
import 'counter_state.dart';

class CounterScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    final counterBloc = context.read<CounterBloc>();

    return Scaffold(
      appBar: AppBar(
        title: Text('Counter Bloc Example'),
      ),
      body: Center(
        child: BlocBuilder<CounterBloc, CounterState>(
          builder: (context, state) {
            return Text(
              'Counter: ${state.counter}',
              style: TextStyle(fontSize: 24),
            );
          },
        ),
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: () {
          counterBloc.add(IncrementCounter());
        },
        child: Icon(Icons.add),
      ),
    );
  }
}
```


On peut voir que le `BlocBuilder` est utilisé pour reconstruire le widget en réponse à un changement d'état. Le `BlocBuilder` prend le type de Bloc et de State en paramètre de types et reconstruit le widget chaque fois que l'état du Bloc change. Dans cet exemple, le texte affichant le compteur est mis à jour en fonction de l'état actuel du Bloc.

Le `context.read<CounterBloc>()` est utilisé pour accéder au `CounterBloc` fourni par le `BlocProvider` dans l'arborescence des widgets. On émet un événement `IncrementCounter` en appuyant sur le bouton `FloatingActionButton`, ce qui incrémente le compteur et met à jour l'interface utilisateur. La méthode `add(Event)` sur le bloc récupéré est utilisée pour ajouter un événement au Bloc.


## Bonnes pratiques pour flutter_bloc

### 1. Séparation des Responsabilités

#### Garder le BLoC Pur
- Le BLoC ne doit contenir que la logique métier
- Éviter tout code UI dans le BLoC
- Ne pas référencer de widgets ou de contexte Flutter dans le BLoC
- Maintenir une séparation claire entre la logique et la présentation

```dart
// ❌ Mauvaise pratique
class UserBloc extends Bloc<UserEvent, UserState> {
  UserBloc() : super(UserInitial()) {
    on<LoadUser>((event, emit) {
      // Ne pas faire cela
      showDialog(
        context: context,
        builder: (context) => LoadingDialog(),
      );
    });
  }
}

// ✅ Bonne pratique
class UserBloc extends Bloc<UserEvent, UserState> {
  UserBloc() : super(UserInitial()) {
    on<LoadUser>((event, emit) {
      emit(UserLoading());
      // Logique métier pure
      final user = await userRepository.getUser();
      emit(UserLoaded(user));
    });
  }
}
```

### 2. Gestion des États

#### États Immutables
- Toujours créer des états immutables
- Utiliser des champs `final`
- Implémenter `copyWith()` pour les modifications d'état
- Éviter de modifier directement l'état

```dart
// ✅ Bonne pratique
class UserState {
  final String name;
  final int age;
  final bool isLoggedIn;

  const UserState({
    required this.name,
    required this.age,
    required this.isLoggedIn,
  });

  UserState copyWith({
    String? name,
    int? age,
    bool? isLoggedIn,
  }) {
    return UserState(
      name: name ?? this.name,
      age: age ?? this.age,
      isLoggedIn: isLoggedIn ?? this.isLoggedIn,
    );
  }
}
```

#### Granularité des États
- Créer des états distincts pour chaque situation significative
- Éviter les états trop génériques
- Utiliser des classes distinctes plutôt que des booléens

```dart
// ❌ Mauvaise pratique
class UserState {
  final bool isLoading;
  final bool hasError;
  final User? user;
}

// ✅ Bonne pratique
abstract class UserState {}
class UserInitial extends UserState {}
class UserLoading extends UserState {}
class UserLoaded extends UserState {
  final User user;
  UserLoaded(this.user);
}
class UserError extends UserState {
  final String message;
  UserError(this.message);
}
```

### 3. Gestion des Événements

#### Événements Descriptifs
- Nommer les événements de manière claire et descriptive
- Inclure toutes les données nécessaires dans l'événement
- Éviter les événements génériques

```dart
// ❌ Mauvaise pratique
class UpdateUser extends UserEvent {
  final Map<String, dynamic> data;
}

// ✅ Bonne pratique
class UpdateUserProfile extends UserEvent {
  final String name;
  final int age;
  final String location;

  UpdateUserProfile({
    required this.name,
    required this.age,
    required this.location,
  });
}
```

### 4. Optimisation des Performances

#### Utilisation Appropriée des Widgets
- Utiliser `BlocBuilder` uniquement quand nécessaire
- Préférer `BlocSelector` pour des mises à jour ciblées
- Utiliser `BlocListener` pour les effets de bord
- Éviter les reconstructions inutiles

```dart
// ❌ Mauvaise pratique - Reconstruction complète
BlocBuilder<UserBloc, UserState>(
  builder: (context, state) {
    return Column(
      children: [
        Text(state.user.name),
        ExpensiveWidget(), // Se reconstruit inutilement
      ],
    );
  },
);

// ✅ Bonne pratique - Reconstruction ciblée
Column(
  children: [
    BlocSelector<UserBloc, UserState, String>(
      selector: (state) => state.user.name,
      builder: (context, name) => Text(name),
    ),
    ExpensiveWidget(), // Ne se reconstruit pas
  ],
);
```

#### Gestion de la Mémoire
- Fermer les BLoCs quand ils ne sont plus nécessaires
- Utiliser `BlocProvider` au bon niveau dans l'arbre des widgets
- Éviter de créer des BLoCs inutilement

```dart
// ✅ Bonne pratique
@override
void dispose() {
  bloc.close();
  super.dispose();
}
```

### 5. Tests

#### Tests Unitaires
- Tester chaque événement du BLoC individuellement
- Vérifier les transitions d'état
- Mocker les dépendances

```dart
// ✅ Bonne pratique
void main() {
  late UserBloc bloc;
  late MockUserRepository repository;

  setUp(() {
    repository = MockUserRepository();
    bloc = UserBloc(repository);
  });

  blocTest<UserBloc, UserState>(
    'émet [UserLoading, UserLoaded] quand LoadUser réussit',
    build: () => bloc,
    act: (bloc) => bloc.add(LoadUser()),
    expect: () => [
      isA<UserLoading>(),
      isA<UserLoaded>(),
    ],
  );
}
```

### 6. Architecture

#### Organisation du Code
- Structurer les fichiers par feature
- Séparer les événements et les états dans des fichiers distincts
- Regrouper les BLoCs liés

```plaintext
lib/
  ├── features/
  │   ├── auth/
  │   │   ├── bloc/
  │   │   │   ├── auth_bloc.dart
  │   │   │   ├── auth_event.dart
  │   │   │   └── auth_state.dart
  │   │   ├── repository/
  │   │   └── ui/
  │   └── user/
  │       ├── bloc/
  │       ├── repository/
  │       └── ui/
  └── shared/
```

#### Dépendances
- Injecter les dépendances plutôt que de les créer dans le BLoC
- Utiliser des repositories pour l'accès aux données
- Favoriser l'inversion de dépendance

```dart
// ✅ Bonne pratique
class UserBloc extends Bloc<UserEvent, UserState> {
  final UserRepository userRepository;
  
  UserBloc(this.userRepository) : super(UserInitial());
}
```

### 7. Documentation

#### Documentation du Code
- Documenter les événements et les états
- Expliquer les transitions d'état complexes
- Inclure des exemples d'utilisation

```dart
/// Représente l'état d'un utilisateur dans l'application
/// 
/// [UserLoaded] est émis quand les données de l'utilisateur
/// sont chargées avec succès
/// 
/// Exemple:
/// ```dart
/// BlocBuilder<UserBloc, UserState>(
///   builder: (context, state) {
///     if (state is UserLoaded) {
///       return Text(state.user.name);
///     }
///     return LoadingIndicator();
///   },
/// )
/// ```
abstract class UserState {}
```

## Cubit vs Bloc

`Cubit` est une variante plus simple de `Bloc` qui ne nécessite pas de définir des événements explicites. Il est utile pour les cas d'utilisation simples où la logique est directe et ne nécessite pas de gestion d'événements complexes. Il ressemble beaucoup aux `Context`s de React alors.

`Bloc` est plus adapté pour les cas d'utilisation complexes où la logique nécessite une gestion fine des événements et des états.

### **Exemple de Cubit**

```dart
import 'package:flutter_bloc/flutter_bloc.dart';

class CounterCubit extends Cubit<int> {
  CounterCubit() : super(0);

  void increment() => emit(state + 1);
}
```

Dans cet exemple, `CounterCubit` étend `Cubit<int>` et définit l'état initial à 0. La méthode `increment` incrémente l'état actuel et émet la nouvelle valeur. Le `Cubit` est utilisé de manière similaire à un `Bloc`, mais sans la nécessité de définir des événements explicites.

## **Exemple d'utilisation de Cubit**

```dart
import 'package:flutter/material.dart';
import 'package:flutter_bloc/flutter_bloc.dart';

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: BlocProvider(
        create: (_) => CounterCubit(),
        child: CounterScreen(),
      ),
    );
  }
}

class CounterScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    final counterCubit = context.read<CounterCubit>();

    return Scaffold(
      appBar: AppBar(
        title: Text('Counter Cubit Example'),
      ),
      body: Center(
        child: BlocBuilder<CounterCubit, int>(
          builder: (context, state) {
            return Text(
              'Counter: $state',
              style: TextStyle(fontSize: 24),
            );
          },
        ),
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: () {
          counterCubit.increment();
        },
        child: Icon(Icons.add),
      ),
    );
  }
}
```

Dans cet exemple, le `CounterCubit` est fourni à l'interface utilisateur via le `BlocProvider` et utilisé dans le `BlocBuilder` pour mettre à jour l'interface utilisateur en réponse aux changements d'état. L'incrémentation du compteur est gérée par la méthode `increment` du `CounterCubit`.


## **Conclusion**

La bibliothèque `flutter_bloc` offre une approche modulaire et réactive pour structurer la logique métier dans une application Flutter. En séparant la logique de l'interface, `flutter_bloc` facilite la maintenabilité et améliore la réactivité de l'application.

## Exercices

### Exercice 1

Sans regarder le code ci-dessus, essayez d'implémenter un modèle BLoC simple pour gérer un compteur en utilisant la bibliothèque `flutter_bloc`.

1. Créez un nouveau projet Flutter et ajoutez la bibliothèque `flutter_bloc`.
2. Implémentez un modèle BLoC simple pour gérer un compteur.
3. Utilisez le `BlocProvider` pour fournir le Bloc à l'interface utilisateur.
4. Utilisez le `BlocBuilder` pour mettre à jour l'interface utilisateur en réponse aux changements d'état.
5. Testez l'application en incrémentant le compteur et en vérifiant que l'interface utilisateur se met à jour correctement.

<details>
    <summary>Solution</summary>
    
```dart
// counter_event.dart
abstract class CounterEvent {}

class IncrementCounter extends CounterEvent {}

// counter_state.dart
abstract class CounterState {
    final int counter;
    CounterState(this.counter);
}

class CounterInitial extends CounterState {
    CounterInitial() : super(0);
}

class CounterUpdated extends CounterState {
    CounterUpdated(int counter) : super(counter);
}

// counter_bloc.dart
import 'package:flutter_bloc/flutter_bloc.dart';
import 'counter_event.dart';
import 'counter_state.dart';

class CounterBloc extends Bloc<CounterEvent, CounterState> {
    CounterBloc() : super(CounterInitial()) {
    on<IncrementCounter>((event, emit) {
        final newCount = state.counter + 1;
        emit(CounterUpdated(newCount));
    });
    }
}

// main.dart

import 'package:flutter/material.dart';
import 'package:flutter_bloc/flutter_bloc.dart';
import 'counter_bloc.dart';
import 'counter_event.dart';
import 'counter_state.dart';

void main() {
    runApp(MyApp());
}

class MyApp extends StatelessWidget {
    @override
    Widget build(BuildContext context) {
    return MaterialApp(
        debugShowCheckedModeBanner: false,
        home: BlocProvider(
        create: (_) => CounterBloc(),
        child: CounterScreen(),
        ),
    );
    }
}

class CounterScreen extends StatelessWidget {
    @override
    Widget build(BuildContext context) {
    final counterBloc = context.read<CounterBloc>();

    return Scaffold(
        appBar: AppBar(
        title: Text('Counter Bloc Example'),
        ),
        body: Center(
        child: BlocBuilder<CounterBloc, CounterState>(
            builder: (context, state) {
            return Text(
                'Counter: ${state.counter}',
                style: TextStyle(fontSize: 24),
            );
            },
        ),
        ),
        floatingActionButton: FloatingActionButton(
        onPressed: () {
            counterBloc.add(IncrementCounter());
        },
        child: Icon(Icons.add),
        ),
    );
    }
}
```
</details>

### Exercice 2

Essayez d'implémenter un modèle BLoC simple pour gérer un formulaire de connexion en utilisant la bibliothèque `flutter_bloc`. Si on saisit le nom d'utilisateur `admin`et le mot de passe `p@ssword`, l'interface utilisateur doit se mettre à jour pour afficher un message de connexion réussie et si on saisit un autre nom d'utilisateur ou mot de passe, l'interface utilisateur doit afficher un message d'erreur.

<details>
    <summary>Solution</summary>
    
```dart
import 'package:flutter/material.dart';
import 'package:flutter_bloc/flutter_bloc.dart';

// login_event.dart
abstract class LoginEvent {}

class SubmitLogin extends LoginEvent {
  final String username;
  final String password;

  SubmitLogin(this.username, this.password);
}

// login_state.dart
abstract class LoginState {}

class LoginInitial extends LoginState {}

class LoginSuccess extends LoginState {}

class LoginError extends LoginState {}

// login_bloc.dart

class LoginBloc extends Bloc<LoginEvent, LoginState> {
  LoginBloc() : super(LoginInitial()) {
    on<SubmitLogin>((event, emit) {
      if (event.username == 'admin' && event.password == 'p@ssword') {
        emit(LoginSuccess());
      } else {
        emit(LoginError());
      }
    });
  }
}

// main.dart

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      debugShowCheckedModeBanner: false,
      home: BlocProvider(
        create: (_) => LoginBloc(),
        child: LoginScreen(),
      ),
    );
  }
}

class LoginScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    final loginBloc = context.read<LoginBloc>();
    final usernameController = TextEditingController();
    final passwordController = TextEditingController();

    return Scaffold(
      appBar: AppBar(
        title: const Text('Login Bloc Example'),
      ),
      body: Center(
        child: Padding(
          padding: const EdgeInsets.all(8.0),
          child: Column(
            mainAxisAlignment: MainAxisAlignment.center,
            children: [
              TextField(
                controller: usernameController,
                decoration: const InputDecoration(labelText: 'Username'),
              ),
              TextField(
                controller: passwordController,
                decoration: const InputDecoration(labelText: 'Password'),
                obscureText: true,
              ),
              ElevatedButton(
                onPressed: () {
                  final username = usernameController.text;
                  final password = passwordController.text;
                  loginBloc.add(SubmitLogin(username, password));
                },
                child: const Text('Login'),
              ),
              BlocBuilder<LoginBloc, LoginState>(
                builder: (context, state) {
                  if (state is LoginSuccess) {
                    return const Text('Login Successful');
                  } else if (state is LoginError) {
                    return const Text('Login Error');
                  } else {
                    return Container();
                  }
                },
              ),
            ],
          ),
        ),
      ),
    );
  }
}
```

</details>

### Exercice 3

Essayez d'implémenter un modèle BLoC simple pour gérer une liste de tâches en utilisant la bibliothèque `flutter_bloc`. L'interface utilisateur doit afficher une liste de tâches et un bouton pour ajouter une nouvelle tâche. Lorsqu'on appuie sur le bouton, une nouvelle tâche doit être ajoutée à la liste et l'interface utilisateur doit se mettre à jour pour afficher la nouvelle liste de tâches.

<details>
    <summary>Solution</summary>
    
```dart
import 'package:flutter/material.dart';
import 'package:flutter_bloc/flutter_bloc.dart';

// task_event.dart
abstract class TaskEvent {}

class AddTask extends TaskEvent {
  final String task;

  AddTask(this.task);
}

// task_state.dart
abstract class TaskState {
  final List<String> tasks;
  TaskState(this.tasks);
}

class TaskInitial extends TaskState {
  TaskInitial() : super([]);
}

class TaskUpdated extends TaskState {
  TaskUpdated(List<String> tasks) : super(tasks);
}

// task_bloc.dart

class TaskBloc extends Bloc<TaskEvent, TaskState> {
  TaskBloc() : super(TaskInitial()) {
    on<AddTask>((event, emit) {
      final newTasks = List<String>.from(state.tasks)..add(event.task);
      emit(TaskUpdated(newTasks));
    });
  }
}

// main.dart

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: BlocProvider(
        create: (_) => TaskBloc(),
        child: TaskScreen(),
      ),
    );
  }
}

class TaskScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    final taskBloc = context.read<TaskBloc>();
    final taskController = TextEditingController();

    return Scaffold(
      appBar: AppBar(
        title: Text('Task Bloc Example'),
      ),
      body: SingleChildScrollView(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            Padding(
              padding: const EdgeInsets.all(8.0),
              child: TextField(
                controller: taskController,
                decoration: InputDecoration(labelText: 'Task'),
              ),
            ),
            ElevatedButton(
              onPressed: () {
                final task = taskController.text;
                taskBloc.add(AddTask(task));
                taskController.clear();
              },
              child: Text('Add Task'),
            ),
            BlocBuilder<TaskBloc, TaskState>(
              builder: (context, state) {
                return Column(
                  children: state.tasks
                      .map((task) => ListTile(title: Text(task)))
                      .toList(),
                );
              },
            ),
          ],
        ),
      ),
    );
  }
}
```

</details>

### Exercice 4

Reprenez l'exemple de l'exercice 3 et ajoutez la possibilité de supprimer une tâche de la liste et de marquer une tâche comme terminée. L'interface utilisateur doit afficher un bouton pour supprimer une tâche et un bouton pour marquer une tâche comme terminée. Lorsqu'on appuie sur le bouton de suppression, la tâche doit être supprimée de la liste et l'interface utilisateur doit se mettre à jour pour afficher la nouvelle liste de tâches. Lorsqu'on appuie sur le bouton de terminaison, la tâche doit être marquée comme terminée et l'interface utilisateur doit se mettre à jour pour afficher la nouvelle liste de tâches.

<details>
    <summary>Solution</summary>
    
```dart
// task_event.dart
import 'package:flutter/material.dart';
import 'package:flutter_bloc/flutter_bloc.dart';

abstract class TaskEvent {}

class AddTask extends TaskEvent {
  final String task;

  AddTask(this.task);
}

class RemoveTask extends TaskEvent {
  final String task;

  RemoveTask(this.task);
}

class ToggleCompleteTask extends TaskEvent {
  final String task;

  ToggleCompleteTask(this.task);
}

// task_state.dart
abstract class TaskState {
  final List<Task> tasks;
  TaskState(this.tasks);
}

class TaskInitial extends TaskState {
  TaskInitial() : super([]);
}

class TaskUpdated extends TaskState {
  TaskUpdated(List<Task> tasks) : super(tasks);
}

class Task {
  final String name;
  final bool completed;

  Task(this.name, this.completed);
}

// task_bloc.dart

class TaskBloc extends Bloc<TaskEvent, TaskState> {
  TaskBloc() : super(TaskInitial()) {
    on<AddTask>((event, emit) {
      final newTasks = List<Task>.from(state.tasks)
        ..add(Task(event.task, false));
      emit(TaskUpdated(newTasks));
    });

    on<RemoveTask>((event, emit) {
      final newTasks = List<Task>.from(state.tasks)
        ..removeWhere((task) => task.name == event.task);
      emit(TaskUpdated(newTasks));
    });

    on<ToggleCompleteTask>((event, emit) {
      final newTasks = state.tasks.map((task) {
        if (task.name == event.task) {
          return Task(task.name, !task.completed);
        } else {
          return task;
        }
      }).toList();
      emit(TaskUpdated(newTasks));
    });
  }
}

// main.dart

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      debugShowCheckedModeBanner: false,
      home: BlocProvider(
        create: (_) => TaskBloc(),
        child: TaskScreen(),
      ),
    );
  }
}

class TaskScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    final taskBloc = context.read<TaskBloc>();
    final taskController = TextEditingController();

    return Scaffold(
      appBar: AppBar(
        title: Text('Task Bloc Example'),
      ),
      body: SingleChildScrollView(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            Padding(
              padding: const EdgeInsets.all(8.0),
              child: TextField(
                controller: taskController,
                decoration: InputDecoration(labelText: 'Task'),
              ),
            ),
            ElevatedButton(
              onPressed: () {
                final task = taskController.text;
                taskBloc.add(AddTask(task));
                taskController.clear();
              },
              child: Text('Add Task'),
            ),
            BlocBuilder<TaskBloc, TaskState>(
              builder: (context, state) {
                return Column(
                  children: state.tasks
                      .map((task) => ListTile(
                            title: Text(task.name,
                                style: TextStyle(
                                  decoration: task.completed
                                      ? TextDecoration.lineThrough
                                      : TextDecoration.none,
                                )),
                            trailing: Row(
                              mainAxisSize: MainAxisSize.min,
                              children: [
                                IconButton(
                                  icon: Icon(Icons.delete),
                                  onPressed: () {
                                    taskBloc.add(RemoveTask(task.name));
                                  },
                                ),
                                IconButton(
                                  icon: Icon(task.completed
                                      ? Icons.check_box
                                      : Icons.check_box_outline_blank),
                                  onPressed: () {
                                    taskBloc.add(ToggleCompleteTask(task.name));
                                  },
                                ),
                              ],
                            ),
                          ))
                      .toList(),
                );
              },
            ),
          ],
        ),
      ),
    );
  }
}
```

</details>

### Exercice 5

Reprenez le code précédent et implémentez un nouveau BLoC pour gérer une liste de catégories en plus. Chaque tâche doit être associée à une catégorie. L'interface utilisateur doit afficher une liste de catégories et une liste de tâches. Lorsqu'on appuie sur une catégorie, la liste des tâches doit se mettre à jour pour afficher uniquement les tâches associées à cette catégorie. Quand on ajoute une tâche, elle est ajoutée automatiquement à la catégorie sélectionnée. 

Il y a donc deux blocs qui sont mutuellement dépendants : les tâches à afficher dépendent de la catégorie sélectionnée.

Hardcodez les catégories "Work", "Personal" et "Shopping" pour cet exercice.

Créez un petit widget pour afficher les catégories en bas de l'écran (via un BottomNavigationBar par exemple) et permettre à l'utilisateur de sélectionner une catégorie.

<details>
    <summary>Solution</summary>
    
```dart
import 'package:flutter/material.dart';
import 'package:flutter_bloc/flutter_bloc.dart';

// Events
abstract class TaskEvent {}

class AddTask extends TaskEvent {
  final String task;
  final String category;
  AddTask(this.task, this.category);
}

class RemoveTask extends TaskEvent {
  final String task;
  RemoveTask(this.task);
}

class ToggleCompleteTask extends TaskEvent {
  final String task;
  ToggleCompleteTask(this.task);
}

abstract class CategoryEvent {}

class SelectCategory extends CategoryEvent {
  final String category;
  SelectCategory(this.category);
}

// States
class Task {
  final String name;
  final bool completed;
  final String category;

  Task(this.name, this.completed, this.category);

  Task copyWith({String? name, bool? completed, String? category}) {
    return Task(
      name ?? this.name,
      completed ?? this.completed,
      category ?? this.category,
    );
  }
}

class TaskState {
  final List<Task> tasks;
  final bool isLoading;
  final String? error;

  TaskState({
    required this.tasks,
    this.isLoading = false,
    this.error,
  });

  TaskState copyWith({
    List<Task>? tasks,
    bool? isLoading,
    String? error,
  }) {
    return TaskState(
      tasks: tasks ?? this.tasks,
      isLoading: isLoading ?? this.isLoading,
      error: error,
    );
  }
}

class CategoryState {
  final List<String> categories;
  final String selectedCategory;

  CategoryState({
    required this.categories,
    required this.selectedCategory,
  });

  CategoryState copyWith({
    List<String>? categories,
    String? selectedCategory,
  }) {
    return CategoryState(
      categories: categories ?? this.categories,
      selectedCategory: selectedCategory ?? this.selectedCategory,
    );
  }
}

// BLoCs
class TaskBloc extends Bloc<TaskEvent, TaskState> {
  TaskBloc() : super(TaskState(tasks: [])) {
    on<AddTask>((event, emit) {
      final newTasks = List<Task>.from(state.tasks)
        ..add(Task(event.task, false, event.category));
      emit(state.copyWith(tasks: newTasks));
    });

    on<RemoveTask>((event, emit) {
      final newTasks =
          state.tasks.where((task) => task.name != event.task).toList();
      emit(state.copyWith(tasks: newTasks));
    });

    on<ToggleCompleteTask>((event, emit) {
      final newTasks = state.tasks.map((task) {
        if (task.name == event.task) {
          return task.copyWith(completed: !task.completed);
        }
        return task;
      }).toList();
      emit(state.copyWith(tasks: newTasks));
    });
  }
}

class CategoryBloc extends Bloc<CategoryEvent, CategoryState> {
  CategoryBloc(List<String> categories)
      : super(CategoryState(
          categories: categories,
          selectedCategory: categories.first,
        )) {
    on<SelectCategory>((event, emit) {
      emit(state.copyWith(selectedCategory: event.category));
    });
  }
}

// UI Components
class TaskScreen extends StatelessWidget {
  const TaskScreen({super.key});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('Task Manager'),
      ),
      bottomNavigationBar: const CategoriesBottomNavigationBar(),
      body: const Column(
        children: [
          CategorySelector(),
          AddTaskForm(),
          Expanded(child: TaskList()),
        ],
      ),
    );
  }
}

class CategorySelector extends StatelessWidget {
  const CategorySelector({super.key});

  @override
  Widget build(BuildContext context) {
    return BlocBuilder<CategoryBloc, CategoryState>(
      buildWhen: (previous, current) =>
          previous.categories != current.categories ||
          previous.selectedCategory != current.selectedCategory,
      builder: (context, state) {
        return OverflowBar(
          alignment: MainAxisAlignment.center,
          children: state.categories.map((category) {
            final isSelected = category == state.selectedCategory;
            return TextButton(
              style: TextButton.styleFrom(
                backgroundColor:
                    isSelected ? Theme.of(context).primaryColor : null,
                foregroundColor:
                    isSelected ? Theme.of(context).colorScheme.onPrimary : null,
              ),
              onPressed: () {
                context.read<CategoryBloc>().add(SelectCategory(category));
              },
              child: Text(category),
            );
          }).toList(),
        );
      },
    );
  }
}

class AddTaskForm extends StatefulWidget {
  const AddTaskForm({super.key});

  @override
  State<AddTaskForm> createState() => _AddTaskFormState();
}

class _AddTaskFormState extends State<AddTaskForm> {
  final _taskController = TextEditingController();

  @override
  void dispose() {
    _taskController.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return Padding(
      padding: const EdgeInsets.all(8.0),
      child: Row(
        children: [
          Expanded(
            child: TextField(
              controller: _taskController,
              decoration: const InputDecoration(labelText: 'Task'),
            ),
          ),
          ElevatedButton(
            onPressed: () {
              final task = _taskController.text.trim();
              if (task.isNotEmpty) {
                final category =
                    context.read<CategoryBloc>().state.selectedCategory;
                context.read<TaskBloc>().add(AddTask(task, category));
                _taskController.clear();
              }
            },
            child: const Text('Add'),
          ),
        ],
      ),
    );
  }
}

class TaskList extends StatelessWidget {
  const TaskList({super.key});

  @override
  Widget build(BuildContext context) {
    return BlocBuilder<CategoryBloc, CategoryState>(
      buildWhen: (previous, current) =>
          previous.selectedCategory != current.selectedCategory,
      builder: (context, categoryState) {
        return BlocBuilder<TaskBloc, TaskState>(
          buildWhen: (previous, current) => previous.tasks != current.tasks,
          builder: (context, taskState) {
            final filteredTasks = taskState.tasks
                .where(
                    (task) => task.category == categoryState.selectedCategory)
                .toList();

            return ListView.builder(
              itemCount: filteredTasks.length,
              itemBuilder: (context, index) {
                final task = filteredTasks[index];
                return TaskListItem(task: task);
              },
            );
          },
        );
      },
    );
  }
}

class TaskListItem extends StatelessWidget {
  final Task task;

  const TaskListItem({
    super.key,
    required this.task,
  });

  @override
  Widget build(BuildContext context) {
    return ListTile(
      title: Text(
        task.name,
        style: TextStyle(
          decoration: task.completed ? TextDecoration.lineThrough : null,
        ),
      ),
      trailing: Row(
        mainAxisSize: MainAxisSize.min,
        children: [
          IconButton(
            icon: const Icon(Icons.delete),
            onPressed: () {
              context.read<TaskBloc>().add(RemoveTask(task.name));
            },
          ),
          IconButton(
            icon: Icon(
              task.completed ? Icons.check_box : Icons.check_box_outline_blank,
            ),
            onPressed: () {
              context.read<TaskBloc>().add(ToggleCompleteTask(task.name));
            },
          ),
        ],
      ),
    );
  }
}

class CategoriesBottomNavigationBar extends StatelessWidget {
  const CategoriesBottomNavigationBar({super.key});

  static const icons = {
    'Work': Icons.work,
    'Personal': Icons.person,
    'Shopping': Icons.shopping_cart,
  };

  @override
  Widget build(BuildContext context) {
    return BlocBuilder<CategoryBloc, CategoryState>(
      buildWhen: (previous, current) =>
          previous.categories != current.categories ||
          previous.selectedCategory != current.selectedCategory,
      builder: (context, state) {
        return BottomNavigationBar(
          items: state.categories
              .map((category) => BottomNavigationBarItem(
                    icon: Icon(icons[category]!),
                    label: category,
                  ))
              .toList(),
          currentIndex: state.categories.indexOf(state.selectedCategory),
          onTap: (index) {
            final category = state.categories[index];
            context.read<CategoryBloc>().add(SelectCategory(category));
          },
        );
      },
    );
  }
}

void main() {
  runApp(const MyApp());
}

class MyApp extends StatelessWidget {
  const MyApp({Key? key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      debugShowCheckedModeBanner: false,
      home: MultiBlocProvider(
        providers: [
          BlocProvider(
            create: (_) => CategoryBloc(['Work', 'Personal', 'Shopping']),
          ),
          BlocProvider(
            create: (_) => TaskBloc(),
          ),
        ],
        child: const TaskScreen(),
      ),
    );
  }
}
```

Le `MultiBlocProvider` est utilisé pour fournir les deux Blocs à l'interface utilisateur. Le `CategoryBloc` gère les catégories et le `TaskBloc` gère les tâches. Lorsqu'on appuie sur une catégorie, le `CategoryBloc` émet un événement `SelectCategory` pour mettre à jour la catégorie sélectionnée. Le `TaskBloc` utilise la catégorie sélectionnée pour filtrer les tâches affichées dans l'interface utilisateur.

L'utilisation de `buildWhen:` dans les `BlocBuilder` permet de contrôler quand les widgets sont reconstruits en fonction des changements d'état. Cela permet d'optimiser les performances en évitant les reconstructions inutiles.

</details>