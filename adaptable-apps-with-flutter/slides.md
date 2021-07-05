---
marp: true
theme: gaia
paginate: true
---
<!-- _class: lead -->

![bg left:40% 50%](https://cdn.worldvectorlogo.com/logos/flutter-logo.svg)
# **Adaptable Apps with Flutter**
##### by
### Brad Cypert

---

# _“What separates design from art is that design is meant to be… functional.”_
#### &nbsp;&nbsp;– Cameron Moll

---
<!-- _class: lead -->
# <!-- fit --> What _is_ Functional Design?

---

# Responsive Design

A design approach that makes content render well on a variety of devices and window or screen sizes from a minimum to a maximum display size. 

---

# Adaptive Design

A design approach that fundamentally changes the way that content is accessed in order to best cater towards the user's expectations of how content on their device should be accessed.

---

# From the Flutter Docs

Adapting an app to run on different device types, such as mobile and desktop, requires dealing with mouse and keyboard input, as well as touch input. It also means there are different expectations about the app’s visual density, how component selection works (cascading menus vs bottom sheets, for example), using platform-specific features (such as top-level windows), and more.

---

# What is Flutter?

Flutter is Google's UI toolkit for building beautiful, natively compiled applications for mobile, web, desktop, and embedded devices from a single codebase.

- Not Responsive
- Not Adaptive
- Provides the tools to make Responsive and Adaptive Apps.
- Powered by Dart

--- 

<!-- _class: lead -->

# What the heck is Dart? 🎯

---

![bg top:10% 60%](./images/github-octoverse-2019-language-change.png)

---

![bg top:10% 45%](./images/stack-overflow-2019-most-popular.png)

--- 

![bg top:10% 40%](./images/stack-overflow-2019-most-loved.png)

--- 

# Dart's Claim to Fame

### In 2013, Google Announced that the Dart VM would be built into chrome, allowing you to write client-side applications in Dart.

---

# Dart's Claim to Fame

### In 2015, Google killed that chrome feature.

https://techcrunch.com/2015/03/25/google-will-not-integrate-its-dart-programming-language-into-chrome/

---

# What is Dart?

- A "General Purpose" programming language
- Optimized for Client Development.
- Compiles to ARM, x64, JavaScript, Mobile, Web, Desktop
- 🔥 Hot Reloading 🔥
- [Isolate-based concurrency](https://dart.dev/guides/language/language-tour#isolates)
- Sound Null Safety
- Tools for Profiling, Logging, and Debugging

---

# Dart is Familiar

![bg left:65%](./images/familiar-dart.jpg)

---

```dart
import 'package:cloud_firestore/cloud_firestore.dart';
import 'package:flutter/material.dart';
import 'package:luna_journal/models/pet.dart';
import 'package:luna_journal/repositories/adaptable_repo.dart';
import 'package:luna_journal/repositories/household_repo.dart';
import 'package:provider/provider.dart';

import 'auth_repo.dart';

class PetRepo extends AdaptableRepo<Pet> {

  final collection = FirebaseFirestore.instance.collection("pets");

  final BuildContext context;
  AuthRepo authRepo;
  HouseholdRepo householdRepo;

  PetRepo({this.context}) {
    this.authRepo = Provider.of<AuthRepo>(this.context, listen: false);
    this.householdRepo = Provider.of<HouseholdRepo>(this.context, listen: false);
  }

  Stream<Pet> getById(String id) {
    return collection.doc(id).get().asStream().map((event) => Pet().fromFirebaseSnapshotDocument(event));
  }
}
```

---

# Dart's syntax is conducive to building component (or widget) trees

---

```dart
return Container(
  child: Column(
    children: <Widget>[
      StreamBuilder(
        key: ValueKey(true),
        stream: medicationVm.getMedicationsStream(),
        builder: (BuildContext context, AsyncSnapshot<QuerySnapshot> snapshot) {
          if (snapshot.hasError) {
            return new Text('Error: ${snapshot.error}');
          }
          switch (snapshot.connectionState) {
            case ConnectionState.waiting:
              return new Text('Loading...');
            default:
              var entries = snapshot.data.docs
                .map((e) => Medication().fromFirebaseSnapshotDocument(e))
                .toList();

              if (entries.isEmpty) {
                return Padding(
                  padding: const EdgeInsets.fromLTRB(8.0, 16.0, 8.0, 16.0),
                  child: Center(
                    child: Text("No Medications yet! Let's add one with the button below!")
                  ),
                );
              }
              return ListView.builder(
                scrollDirection: Axis.vertical,
                shrinkWrap: true,
                padding: const EdgeInsets.all(8),
                itemCount: entries.length,
                itemBuilder: (BuildContext context, int index) {
                  return MedicationListItem(
                    medication: entries[index],
                    onPressed: () => {
                        Navigator.pushNamed(context, '/medications/${entries[index].documentID}')
                    },
                  );
               });
              }
            },
          ),
        ],
    ));
```

---
<!-- _class: lead -->
# That's a large chunk of Flutter code!

---
<!-- _class: lead -->
# How does Flutter work?

---

![bg 65%](./images/flutter-docs-architecture.png)

---
<!-- _class: lead -->
```dart
void main() async {
    runApp(MyApp());
}
```

---

<!-- _class: lead -->
```dart
class MyApp extends StatelessWidget {

  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return MultiProvider(
      providers: [
        Provider<Box<String>>(create: (ctx) => Hive.box("cache")),
        ... // DI
      ],
      child: MainAppContent()
    );
  }
}
```
---
<!-- _class: lead -->
```dart
class MainAppContent extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    
    var themeService = Provider.of<ThemeService>(context);
    
    return MaterialApp(
        title: 'Luna Journal',
        theme: themeService.getThemeData(themeService.activeTheme),
        debugShowCheckedModeBanner: false,
        initialRoute: '/',
        onGenerateRoute: (settings) => router
            .matchRoute(context, settings.name, routeSettings: settings)
            .route
    );
  }
}

```
<!-- _class: lead -->
---
```dart
  @override
  Widget build(BuildContext context) {
    return Scaffold(
        appBar: AppBar(
          title: Text("Medications"),
          actions: [
            if (!editMode)
              Container(
                padding: EdgeInsets.only(right: 8),
                child: IconButton(
                    icon: Icon(FontAwesomeIcons.edit),
                    onPressed: () => {
                          setState(() {
                            editMode = !editMode;
                          })
                        }),
              )
          ],
        ),
        drawer: null,
        body: editMode ? getEditForm() : getViewContainer());
  }
```

---

<!-- _class: lead -->
# So you put some widgets in a tree

---
<!-- _class: lead --->
# Before we go any further.

# I need you know...

---

<!-- _class: lead -->
# I am not a designer.

---
# You get something like this.

## Not too bad for a non-designer.

![bg left:40% 60%](./images/luna-journal-home-ios.png)

---
# And that looks bad regardless of whether I'm a designer or not.
![bg left:40% 95%](./images/luna-journal-home-ipad.png)

---

<!-- _class: lead -->
# So, you open your XCode Project and modify it so that iPad isn't supported.

---

<!-- _class: lead -->
# Except that's not an option!


---

```dart
GridView.count(
    primary: true,
    shrinkWrap: true,
    padding: const EdgeInsets.all(10),
    crossAxisSpacing: 0,
    mainAxisSpacing: 0,
    crossAxisCount: 3,
    children: <Widget>[
        ... // truncated for brevity
    ]),
```

# What a troublemaker!

---

# Introducing the MediaQuery

```dart
MediaQuery.of(context);
```
| Methods | What they return |
|-------- | -------------|
| size    | screen size dimensions |
| devicePixelRatio | number of device pixels per logical pixel |
| orientation | Portrait or Landscape |
| textScaleFactor | number of font pixels for each logical pixel |
| viewInsets | parts of the display that are obscured by system ui like the keyboard  |

---

```dart
crossAxisCount: MediaQuery.of(context).size.width > 900 ? 5 : 3,
```
```dart
GridView.count(
    primary: true,
    shrinkWrap: true,
    padding: const EdgeInsets.all(10),
    crossAxisSpacing: 0,
    mainAxisSpacing: 0,
    crossAxisCount: MediaQuery.of(context).size.width > 900 ? 5 : 3,
    children: <Widget>[
        ... //truncated for brevity
    ]
)
```
## That's _better_, but we can do more than that.
![bg left:40% 90%](./images/luna-journal-home-ipad-5row.png)

---

```dart
Widget build(BuildContext context) {
    var dialogFactory = DialogFactory(context: context);
    return Container(
      padding: const EdgeInsets.all(4),
      child: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: <Widget>[
            IconButton(
              iconSize: 36,
              icon: Icon(image, 
                color: this.disabled ? Theme.of(context).disabledColor : Theme.of(context).colorScheme.onBackground),
              tooltip: this.tooltip,
              onPressed: this.disabled ? () {
                dialogFactory.createDialog( title: SELECT_PET_TITLE, content: SELECT_PET_CONTENT).show();
              } : this.onPressed,
            ),
            Text(this.text, style: TextStyle(
                fontSize: 16, 
                color: this.disabled ? Theme.of(context).disabledColor : Theme.of(context).colorScheme.onBackground), 
                textAlign: TextAlign.center
            ),
          ],
        )
      ),
    );
```

---

```dart
IconButton(
    iconSize: 36,
    ...
),
```

```dart
IconButton(
    iconSize: MediaQuery.of(context).orientation == Orientation.portrait ?
                (MediaQuery.of(context).size.aspectRatio * 4) * 20 :
                (MediaQuery.of(context).size.aspectRatio * 0.8) * 60,
    ...
),
```

---

## Aspect Ratio Tips

- Phones are slimmer than tablets (creating a more extreme ratio)
- Aspect Ratio changes with Orientation
- Multiply by a "Weight" to help direct how much influence the aspect ratio actually has. (0.5 to 2 is pretty common)

---

```dart
Text(this.text, 
    style: TextStyle(
        fontSize: 16
        ...
        ),
)
```

```dart
var mediaData = MediaQuery.of(context);

Text(this.text, 
    style: TextStyle(
            fontSize: mediaData.textScaleFactor *
            (mediaData.size.aspectRatio.clamp(0.8, 1.2)) *
            16,
            ...
)
```

---

![bg 94%](./images/dart-clamp.png)

---

# There are other tools like Flex that help with responsive widgets, too.

// TODO: If Im under 60 minutes, add some examples of flex too

---

# We've made our app more responsive, but how do we make it _Adaptive_?

---

- ## Tablets, Laptops and Desktops have tighter (closer to 1) aspect ratios than phones
    - ### More space to work with
- ## Watches also have a tigher aspect ratio than phones
    - ### LESS space to work with.

---

- ## Think about how people use the device you're building for.
    - ### Most people would prefer to perform heavy-workloads on a desktop or laptop computer.
    - ### Tablets have become a staple for multi-taskers, artists, and note-takers.
    - ### I hate typing on a phone. Don't make me do it.

---

![bg 50%](./images/luna-journal-ios-layoutbuilder.png)

![bg 50%](./images/luna-journal-ios-layoutbuilder-select.png)

---
# Why is pet selection hidden behind a dropdown? 

- Its critical enough to keep on the home screen.
- I ran out of space.

---

# On Tablet

- Its critical enough to keep on the home screen.
- I have PLENTY of space.

---

# LayoutBuilder

- A widget
- The "cream of the crop" for adaptive and responsive layouts
- Use for building large layouts
- Use for deciding how small widgets should render
- [Widget of the Week: Layout Builder](https://www.youtube.com/watch?v=IYDVcriKjsw&feature=emb_title)
- [Layout Builder Docs](https://api.flutter.dev/flutter/widgets/LayoutBuilder-class.html)


---

# Master-Detail View

- List on the left, detail on the right

![bg right:60% 90%](./images/mdv.png)

---

![bg 40%](./images/luna-journal-layoutbuilder-ipad.png)