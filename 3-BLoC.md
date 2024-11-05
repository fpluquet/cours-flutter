# L'architecture en BLoC avec flutter_bloc

## **Objectifs**
- Comprendre l'architecture BLoC avec la bibliothèque `flutter_bloc`.
- Apprendre à séparer la logique métier de l'interface utilisateur.
- Utiliser les classes `Bloc`, `Event`, et `State` de `flutter_bloc` pour gérer l'état.
- Implémenter un modèle BLoC simple avec `flutter_bloc` dans une application Flutter.

## **Introduction à BLoC**

L'architecture BLoC (Business Logic Component) est un modèle de conception qui sépare la logique métier de l'interface utilisateur dans une application Flutter. Elle permet de gérer l'état de manière réactive et de structurer le code de manière modulaire et maintenable.

### Avantages de BLoC

1. **Séparation des préoccupations** : Permet de séparer la logique métier de l'interface utilisateur.
2. **Réactivité** : Facilite la mise à jour de l'interface utilisateur en réponse aux événements.
3. **Maintenabilité** : Permet de structurer le code de manière modulaire et réutilisable.


## **Introduction à flutter_bloc**

La bibliothèque **flutter_bloc** simplifie la mise en œuvre de l'architecture BLoC en Flutter. Elle fournit des outils intégrés pour gérer la logique de l'application et l'état de manière réactive, avec des classes spécialement conçues pour structurer le code de façon modulaire et maintenable.

### Avantages de `flutter_bloc`

1. **Structure** : Fournit un modèle organisé et extensible pour la gestion d'état.
2. **Réactivité** : Permet à l'interface utilisateur de réagir facilement aux changements d'état.
3. **Testabilité** : Facilite les tests unitaires et d'intégration.

## **Concepts Clés**

1. **Bloc** : La classe principale qui contient la logique métier et gère les événements pour émettre des états.
2. **Event** : Représente une action ou un événement utilisateur (ex : clic sur un bouton).
3. **State** : Représente l'état de l'application à un instant donné, envoyé en réponse à un événement.
4. **BlocProvider** : Un widget qui fournit le Bloc à l'arborescence des widgets.
5. **BlocBuilder** : Un widget qui reconstruit l'interface utilisateur en réponse à un changement d'état.

## **Implémentation avec flutter_bloc**

### 1. Ajouter `flutter_bloc` au projet

Demander à `flutter` d'ajouter la dépendance `flutter_bloc` au projet.

```bash
flutter pub add flutter_bloc
```

### 2. Créer les classes Event et State

Créez les classes pour gérer les événements et les états.

#### Exemple (CounterEvent et CounterState)

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

### 3. Créer la classe Bloc

La classe Bloc gère les événements et émet des états en réponse en utilisant `on<Event>((event, emit) => ...)`. Chaque événement est associé à une action qui modifie l'état. L'état initial est défini dans le constructeur de la classe Bloc (`super(CounterInitial())`). Les nouveaux états sont émis en utilisant `emit(State)`. Chaque `emit` déclenche la reconstruction des widgets associés via l'utilisation du `BlocBuilder`.

#### Exemple de Code (CounterBloc)

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

### 4. Fournir le Bloc à l’Interface Utilisateur

Utiliser le `BlocProvider` pour rendre le BLoC accessible dans l’arborescence des widgets.

#### Exemple de Code (main.dart)

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

### 5. Utiliser le Bloc dans un Widget

Dans l’interface utilisateur, utilisez `BlocBuilder` pour reconstruire le widget lorsque l’état change.

#### Exemple de Code (CounterScreen.dart)

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


## **Bonnes Pratiques**

1. **Éviter la logique UI dans le Bloc** : Le Bloc doit contenir uniquement la logique métier. N'y incluez pas de code d'interface utilisateur.
2. **Utiliser `BlocListener` pour les actions uniques** : Utilisez `BlocListener` pour les actions qui ne nécessitent pas de reconstruction, comme les notifications.
3. **Optimiser les performances** : N’abusez pas des mises à jour de l’état pour éviter les reconstructions excessives de l’interface utilisateur.


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

// task_event.dart
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
  final String category;

  Task(this.name, this.completed, this.category);
}

// category_event.dart
abstract class CategoryEvent {}

class SelectCategory extends CategoryEvent {
  final String category;

  SelectCategory(this.category);
}

// category_state.dart
abstract class CategoryState {
  final List<String> categories;
  final String selectedCategory;

  CategoryState(this.categories, this.selectedCategory);
}

class CategoryInitial extends CategoryState {
  CategoryInitial(List<String> categories)
      : super(categories, categories.first);
}

class CategoryUpdated extends CategoryState {
  CategoryUpdated(List<String> categories, String selectedCategory)
      : super(categories, selectedCategory);
}

class CategoryBloc extends Bloc<CategoryEvent, CategoryState> {
  CategoryBloc(List<String> categories) : super(CategoryInitial(categories)) {
    on<SelectCategory>((event, emit) {
      emit(CategoryUpdated(state.categories, event.category));
    });
  }
}

// task_bloc.dart
class TaskBloc extends Bloc<TaskEvent, TaskState> {
  TaskBloc() : super(TaskInitial()) {
    on<AddTask>((event, emit) {
      final newTasks = List<Task>.from(state.tasks)
        ..add(Task(event.task, false, event.category));
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
          return Task(task.name, !task.completed, task.category);
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
      home: MultiBlocProvider(
        providers: [
          BlocProvider(
            create: (_) => CategoryBloc(['Work', 'Personal', 'Shopping']),
          ),
          BlocProvider(
            create: (_) => TaskBloc(),
          ),
        ],
        child: TaskScreen(),
      ),
    );
  }
}

class TaskScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    final categoryBloc = context.read<CategoryBloc>();
    final taskBloc = context.read<TaskBloc>();
    final taskController = TextEditingController();

    categoryBloc.stream.listen((state) {
      print('Category: ${state.selectedCategory}');
    });

    return Scaffold(
      appBar: AppBar(
        title: const Text('Task Bloc Example'),
      ),
      bottomNavigationBar: CategoriesBottomNavigationBar(),
      body: Column(
        mainAxisAlignment: MainAxisAlignment.start,
        crossAxisAlignment: CrossAxisAlignment.center,
        children: [
          BlocBuilder<CategoryBloc, CategoryState>(
            builder: (context, state) {
              return OverflowBar(
                alignment: MainAxisAlignment.center,
                children: state.categories
                    .map((category) => TextButton(
                          style: ElevatedButton.styleFrom(
                            backgroundColor: category == state.selectedCategory
                                ? Theme.of(context)
                                    .buttonTheme
                                    .colorScheme
                                    ?.primary
                                : null,
                            foregroundColor: category == state.selectedCategory
                                ? Theme.of(context)
                                    .buttonTheme
                                    .colorScheme
                                    ?.onPrimary
                                : null,
                          ),
                          onPressed: () {
                            categoryBloc.add(SelectCategory(category));
                          },
                          child: Text(category),
                        ))
                    .toList(),
              );
            },
          ),
          Padding(
            padding: const EdgeInsets.all(8.0),
            child: TextField(
              controller: taskController,
              decoration: const InputDecoration(labelText: 'Task'),
            ),
          ),
          ElevatedButton(
            onPressed: () {
              final task = taskController.text;
              taskBloc.add(AddTask(
                  task, context.read<CategoryBloc>().state.selectedCategory));
              taskController.clear();
            },
            child: const Text('Add Task'),
          ),
          // On regarde le bloc de catégorie pour être mis à jour quand la catégorie sélectionnée est changée
          BlocBuilder<CategoryBloc, CategoryState>(builder: (context, state) {
            // On regarde le bloc de tâches pour afficher les tâches de la catégorie sélectionnée
            return BlocBuilder<TaskBloc, TaskState>(
              builder: (context, state) {
                return Column(
                  children: state
                      .tasks // On va filtrer les tâches pour ne garder que celles de la catégorie sélectionnée
                      .where((task) =>
                          task.category ==
                          context.read<CategoryBloc>().state.selectedCategory)
                      .map((task) => ListTile(
                            title: Text(
                              task.name,
                              style: task.completed
                                  ? const TextStyle(
                                      decoration: TextDecoration.lineThrough)
                                  : null,
                            ),
                            trailing: Row(
                              mainAxisSize: MainAxisSize.min,
                              children: [
                                IconButton(
                                  icon: const Icon(Icons.delete),
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
            );
          }),
        ],
      ),
    );
  }
}

class CategoriesBottomNavigationBar extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    const icons = {
      'Work': Icons.work,
      'Personal': Icons.person,
      'Shopping': Icons.shopping_cart,
    };
    return BlocBuilder<CategoryBloc, CategoryState>(builder: (context, state) {
      return BottomNavigationBar(
        items: state.categories
            .map((category) => BottomNavigationBarItem(
                  icon: Icon(icons[category]),
                  label: category,
                ))
            .toList(),
        currentIndex: state.categories.indexOf(state.selectedCategory),
        onTap: (index) {
          final category = context.read<CategoryBloc>().state.categories[index];
          context.read<CategoryBloc>().add(SelectCategory(category));
        },
      );
    });
  }
}
```

Le `MultiBlocProvider` est utilisé pour fournir les deux Blocs à l'interface utilisateur. Le `CategoryBloc` gère les catégories et le `TaskBloc` gère les tâches. Lorsqu'on appuie sur une catégorie, le `CategoryBloc` émet un événement `SelectCategory` pour mettre à jour la catégorie sélectionnée. Le `TaskBloc` utilise la catégorie sélectionnée pour filtrer les tâches affichées dans l'interface utilisateur.

</details>