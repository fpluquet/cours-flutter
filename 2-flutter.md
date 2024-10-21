# Fiche d'exercices : Découverte de Flutter

## Exercice 1 : Compréhension de l'arborescence des widgets

Lisez le code suivant et expliquez la hiérarchie des widgets dans l'application Flutter. Identifiez quel widget est responsable de chaque partie de l'interface.

```dart
import 'package:flutter/material.dart';

void main() => runApp(MyApp());

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: Scaffold(
        appBar: AppBar(
          title: Text('Hello Flutter'),
        ),
        body: Center(
          child: Text('Welcome to Flutter'),
        ),
      ),
    );
  }
}
```

### Questions :
1. Quel est le widget racine de cette application ?
2. Quelles sont les trois principales sections de l'interface dans ce code ?

<details>
<summary>Solution</summary>

1. Le widget racine est `MaterialApp`, qui fournit la structure de base pour une application Material Design.
2. Les trois principales sections sont :
   - `AppBar` : la barre d'en-tête avec un titre.
   - `Scaffold` : fournit une structure de base pour l'écran, avec une barre d'en-tête et un corps.
   - `Center` : centre le widget `Text` dans le corps.
</details>


## Exercice 2 : Production - Créer une interface simple

Créez une interface Flutter avec un bouton centré qui, lorsqu'il est pressé, affiche "Button pressed!" dans la console.

<details>
<summary>Solution</summary>

```dart
import 'package:flutter/material.dart';

void main() => runApp(MyApp());

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: Scaffold(
        appBar: AppBar(
          title: Text('Button Example'),
        ),
        body: Center(
          child: ElevatedButton(
            onPressed: () {
              print('Button pressed!');
            },
            child: Text('Press me'),
          ),
        ),
      ),
    );
  }
}
```
</details>


## Exercice 3 : Compréhension des widgets d'état

Lisez le code ci-dessous et expliquez comment il fonctionne. Quelle est la différence entre un `StatelessWidget` et un `StatefulWidget` dans Flutter ?

```dart
import 'package:flutter/material.dart';

void main() => runApp(MyApp());

class MyApp extends StatefulWidget {
  @override
  _MyAppState createState() => _MyAppState();
}

class _MyAppState extends State<MyApp> {
  String displayText = 'Hello';

  void changeText() {
    setState(() {
      displayText = 'Text changed!';
    });
  }

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: Scaffold(
        appBar: AppBar(
          title: Text('Stateful Widget Example'),
        ),
        body: Center(
          child: Column(
            mainAxisAlignment: MainAxisAlignment.center,
            children: <Widget>[
              Text(displayText),
              ElevatedButton(
                onPressed: changeText,
                child: Text('Change Text'),
              ),
            ],
          ),
        ),
      ),
    );
  }
}
```

<details>
<summary>Solution</summary>

1. Un `StatelessWidget` est un widget dont l'état ne change pas après sa création. Un `StatefulWidget`, comme `MyApp`, permet de gérer des changements d'état grâce à la méthode `setState`.
2. Ici, la méthode `setState` est utilisée pour mettre à jour l'affichage de `displayText` lorsqu'on appuie sur le bouton.
</details>


## Exercice 4 : Production - Liste déroulante avec sélection

Créez une application Flutter avec un menu déroulant (`DropdownButton`). L'application doit afficher la valeur sélectionnée sous le menu.

<details>
<summary>Solution</summary>

```dart
import 'package:flutter/material.dart';

void main() => runApp(MyApp());

class MyApp extends StatefulWidget {
  @override
  _MyAppState createState() => _MyAppState();
}

class _MyAppState extends State<MyApp> {
  String dropdownValue = 'One';

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: Scaffold(
        appBar: AppBar(
          title: Text('Dropdown Example'),
        ),
        body: Center(
          child: Column(
            mainAxisAlignment: MainAxisAlignment.center,
            children: <Widget>[
              DropdownButton<String>(
                value: dropdownValue,
                onChanged: (String? newValue) {
                  setState(() {
                    dropdownValue = newValue!;
                  });
                },
                items: <String>['One', 'Two', 'Three', 'Four']
                    .map<DropdownMenuItem<String>>((String value) {
                  return DropdownMenuItem<String>(
                    value: value,
                    child: Text(value),
                  );
                }).toList(),
              ),
              Text('Selected: $dropdownValue'),
            ],
          ),
        ),
      ),
    );
  }
}
```
</details>


## Exercice 5 : Compréhension des `FutureBuilder`

Lisez le code suivant et décrivez comment il fonctionne. À quel moment le `Future` est-il résolu, et comment le résultat est-il affiché à l'écran ?

```dart
import 'package:flutter/material.dart';

void main() => runApp(MyApp());

class MyApp extends StatelessWidget {
  Future<String> fetchData() async {
    await Future.delayed(Duration(seconds: 2));
    return 'Data loaded!';
  }

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: Scaffold(
        appBar: AppBar(
          title: Text('FutureBuilder Example'),
        ),
        body: Center(
          child: FutureBuilder<String>(
            future: fetchData(),
            builder: (context, snapshot) {
              if (snapshot.connectionState == ConnectionState.waiting) {
                return CircularProgressIndicator();
              } else if (snapshot.hasError) {
                return Text('Error: ${snapshot.error}');
              } else {
                return Text('Result: ${snapshot.data}');
              }
            },
          ),
        ),
      ),
    );
  }
}
```

<details>
<summary>Solution</summary>

- Le `FutureBuilder` attend que le `Future` retourné par `fetchData` soit résolu. Pendant l'attente (`ConnectionState.waiting`), il affiche un indicateur de progression circulaire. 
- Une fois le `Future` résolu, il affiche le texte "Data loaded!".
</details>


## Exercice 6 : Production - Formulaire avec validation

Créez une application Flutter avec un formulaire contenant un champ de texte pour le nom et un bouton de soumission. Lorsque l'utilisateur soumet le formulaire, vérifiez que le champ n'est pas vide avant d'afficher le message "Form submitted!" dans la console.

<details>
<summary>Solution</summary>

```dart
import 'package:flutter/material.dart';

void main() => runApp(MyApp());

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: Scaffold(
        appBar: AppBar(
          title: Text('Form Example'),
        ),
        body: MyForm(),
      ),
    );
  }
}

class MyForm extends StatefulWidget {
  @override
  _MyFormState createState() => _MyFormState();
}

class _MyFormState extends State<MyForm> {
  final _formKey = GlobalKey<FormState>();
  final _controller = TextEditingController();

  void _submitForm() {
    if (_formKey.currentState!.validate()) {
      print('Form submitted!');
    }
  }

  @override
  Widget build(BuildContext context) {
    return Padding(
      padding: const EdgeInsets.all(16.0),
      child: Form(
        key: _formKey,
        child: Column(
          children: <Widget>[
            TextFormField(
              controller: _controller,
              decoration: InputDecoration(labelText: 'Name'),
              validator: (value) {
                if (value == null || value.isEmpty) {
                  return 'Please enter your name';
                }
                return null;
              },
            ),
            ElevatedButton(
              onPressed: _submitForm,
              child: Text('Submit'),
            ),
          ],
        ),
      ),
    );
  }
}
```
</details>


## Exercice 7 : Compréhension des `ListView` (suite)

Lisez le code ci-dessous et expliquez comment fonctionne le `ListView.builder`. Quelle est la différence entre `ListView` et `ListView.builder` ?

```dart
import 'package:flutter/material.dart';

void main() => runApp(MyApp());

class MyApp extends StatelessWidget {
  final List<String> items = List<String>.generate(100, (i) => "Item $i");

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: Scaffold(
        appBar: AppBar(
          title: Text('ListView Example'),
        ),
        body: ListView.builder(
          itemCount: items.length,
          itemBuilder: (context, index) {
            return ListTile(
              title: Text('${items[index]}'),
            );
          },
        ),
      ),
    );
  }
}
```

### Questions :
1. Quelle est la principale différence entre `ListView` et `ListView.builder` en termes de performance ?
2. Pourquoi serait-il plus efficace d'utiliser `ListView.builder` pour une liste de grande taille ?

<details>
<summary>Solution</summary>

1. `ListView.builder` est plus performant pour de longues listes car il ne crée les widgets que lorsqu'ils sont visibles à l'écran, contrairement à `ListView`, qui crée tous les widgets en mémoire au moment de la construction.
2. Pour une longue liste, `ListView.builder` améliore les performances et réduit l'utilisation de la mémoire en construisant les éléments à la demande.
</details>


## Exercice 8 : Production - Créer un `GridView` d'images

Créez une application Flutter qui affiche un `GridView` de 6 images (vous pouvez utiliser des images en ligne ou des placeholders comme `NetworkImage`). Le `GridView` doit avoir deux colonnes.

<details>
<summary>Solution</summary>

```dart
import 'package:flutter/material.dart';

void main() => runApp(MyApp());

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: Scaffold(
        appBar: AppBar(
          title: Text('GridView Example'),
        ),
        body: GridView.count(
          crossAxisCount: 2,
          children: <Widget>[
            Image.network('https://via.placeholder.com/150'),
            Image.network('https://via.placeholder.com/150'),
            Image.network('https://via.placeholder.com/150'),
            Image.network('https://via.placeholder.com/150'),
            Image.network('https://via.placeholder.com/150'),
            Image.network('https://via.placeholder.com/150'),
          ],
        ),
      ),
    );
  }
}
```
</details>


## Exercice 9 : Compréhension du `Navigator`

Lisez le code suivant et expliquez comment fonctionne le `Navigator` pour la navigation entre deux écrans dans Flutter.

```dart
import 'package:flutter/material.dart';

void main() => runApp(MyApp());

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: FirstScreen(),
    );
  }
}

class FirstScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('First Screen')),
      body: Center(
        child: ElevatedButton(
          onPressed: () {
            Navigator.push(
              context,
              MaterialPageRoute(builder: (context) => SecondScreen()),
            );
          },
          child: Text('Go to Second Screen'),
        ),
      ),
    );
  }
}

class SecondScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('Second Screen')),
      body: Center(
        child: ElevatedButton(
          onPressed: () {
            Navigator.pop(context);
          },
          child: Text('Go back to First Screen'),
        ),
      ),
    );
  }
}
```

### Questions :
1. Quel est le rôle de `Navigator.push` et `Navigator.pop` dans ce code ?
2. Que se passerait-il si `Navigator.pop` était omis dans le deuxième écran ?

<details>
<summary>Solution</summary>

1. `Navigator.push` ajoute une nouvelle page à la pile de navigation, tandis que `Navigator.pop` la retire (ramenant l'utilisateur à l'écran précédent).
2. Si `Navigator.pop` est omis, l'utilisateur ne pourrait pas revenir à l'écran précédent sans quitter l'application.
</details>


## Exercice 10 : Production - Navigation avec des données

Créez une application qui permet à l'utilisateur de saisir son nom dans un champ de texte sur un premier écran, puis de passer à un deuxième écran qui affiche le nom saisi.

<details>
<summary>Solution</summary>

```dart
import 'package:flutter/material.dart';

void main() => runApp(MyApp());

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: FirstScreen(),
    );
  }
}

class FirstScreen extends StatelessWidget {
  final TextEditingController _controller = TextEditingController();

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('Enter your name')),
      body: Padding(
        padding: const EdgeInsets.all(16.0),
        child: Column(
          children: <Widget>[
            TextField(
              controller: _controller,
              decoration: InputDecoration(labelText: 'Name'),
            ),
            ElevatedButton(
              onPressed: () {
                Navigator.push(
                  context,
                  MaterialPageRoute(
                    builder: (context) => SecondScreen(name: _controller.text),
                  ),
                );
              },
              child: Text('Submit'),
            ),
          ],
        ),
      ),
    );
  }
}

class SecondScreen extends StatelessWidget {
  final String name;

  SecondScreen({required this.name});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('Second Screen')),
      body: Center(
        child: Text('Hello, $name!'),
      ),
    );
  }
}
```
</details>
