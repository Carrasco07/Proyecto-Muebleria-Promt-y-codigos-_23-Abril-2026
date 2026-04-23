Para desarrollar este proyecto como un arquitecto de software, seguiremos una estructura profesional que separa la lógica de Firebase, el diseño de la interfaz y la integración con **Antigravity** (que en el ecosistema Flutter se refiere comúnmente a una arquitectura de estados limpios o librerías de micro-servicios).

---

## 1. Configuración de Entorno y Carpetas

Primero, organizamos el espacio de trabajo en tu sistema de archivos:

* **Ruta principal:** `xfluttercarrasco0533/`
* **Proyecto Flutter:** `crudmuebleria/`

```bash
# Comandos para terminal
mkdir xfluttercarrasco0533
cd xfluttercarrasco0533
flutter create crudmuebleria
cd crudmuebleria
```

---

## 2. Base de Datos Firestore (Consola Firebase)

1.  Ve a [Firebase Console](https://console.firebase.google.com/).
2.  Crea un proyecto llamado **"Muebleria"**.
3.  En el menú lateral, selecciona **Firestore Database**.
4.  Haz clic en **Crear base de datos** (modo prueba para desarrollo).
5.  Crea una **Colección** llamada `Muebles`.
6.  Agrega un documento inicial con los campos:
    * `nombre`: (String)
    * `precio`: (Number)
    * `stock`: (Number)

---

## 3. Integración de Librerías y Pubspec.yaml

Para este proyecto, necesitamos las herramientas oficiales de Firebase. El archivo `pubspec.yaml` debe quedar así:

```yaml
dependencies:
  flutter:
    sdk: flutter
  # Core de Firebase
  firebase_core: ^2.24.2
  # Firestore para el CRUD
  cloud_firestore: ^4.14.0
  # Antigravity (o manejo de estados similar)
  provider: ^6.1.1 
```

**Instalación:** Ejecuta `flutter pub get` en la terminal.

---

## 4. Estructura de Proyecto (Agentes y Roles)

Aplicando una metodología de "Agentes y Roles", dividiremos el código en responsabilidades claras:

* **Data Agent (Service):** Se encarga de hablar con Firebase.
* **Logic Agent (Provider):** Gestiona el estado y flujo de datos.
* **UI Agent (View):** Muestra los widgets en tonos azules y grises.

<Steps>
{/* Reason: El orden es crítico para asegurar que Firebase se inicialice antes de que la UI intente leer datos. */}
  <Step title="Inicializar Firebase" subtitle="Archivo main.dart">
    Configura el punto de entrada para que el motor de Flutter espere a Firebase.
  </Step>
  <Step title="Crear el Modelo" subtitle="Archivo mueble_model.dart">
    Define la estructura de los datos (Nombre, Precio, Stock).
  </Step>
  <Step title="Implementar el Servicio CRUD" subtitle="Archivo firebase_service.dart">
    Escribe las funciones para Create, Read, Update y Delete.
  </Step>
  <Step title="Diseñar la Interfaz" subtitle="Archivo home_page.dart">
    Crea los widgets con el esquema de colores solicitado.
  </Step>
</Steps>

---

## 5. Código Funcional (Dart)

### A. El Modelo de Datos
`lib/models/mueble.dart`

```dart
class Mueble {
  String id;
  String nombre;
  double precio;
  int stock;

  Mueble({required this.id, required this.nombre, required this.precio, required this.stock});

  // Convertir de Firestore a Objeto
  factory Mueble.fromFirestore(Map<String, dynamic> data, String id) {
    return Mueble(
      id: id,
      nombre: data['nombre'] ?? '',
      precio: (data['precio'] ?? 0).toDouble(),
      stock: data['stock'] ?? 0,
    );
  }
}
```

### B. Servicio CRUD (Skill: Data Management)
`lib/services/firebase_service.dart`

```dart
import 'cloud_firestore/cloud_firestore.dart';

FirebaseFirestore db = FirebaseFirestore.instance;

// CREATE
Future<void> addMueble(String nombre, double precio, int stock) async {
  await db.collection('Muebles').add({
    'nombre': nombre,
    'precio': precio,
    'stock': stock,
  });
}

// READ (Stream para tiempo real)
Stream<List<Mueble>> getMuebles() {
  return db.collection('Muebles').snapshots().map((snapshot) =>
      snapshot.docs.map((doc) => Mueble.fromFirestore(doc.data(), doc.id)).toList());
}

// UPDATE
Future<void> updateMueble(String id, String nombre, double precio, int stock) async {
  await db.collection('Muebles').doc(id).update({
    'nombre': nombre,
    'precio': precio,
    'stock': stock,
  });
}

// DELETE
Future<void> deleteMueble(String id) async {
  await db.collection('Muebles').doc(id).delete();
}
```

### C. Interfaz de Usuario (Estética Azul y Gris)
`lib/screens/home_page.dart`

```dart
import 'package:flutter/material.dart';
import '../services/firebase_service.dart';

class MuebleriaHome extends StatelessWidget {
  final Color darkBlue = const Color(0xFF1A237E);
  final Color lightGrey = const Color(0xFFF5F5F5);

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      backgroundColor: lightGrey,
      appBar: AppBar(
        title: Text('Mueblería CRUD', style: TextStyle(color: Colors.white)),
        backgroundColor: darkBlue,
      ),
      body: StreamBuilder(
        stream: getMuebles(),
        builder: (context, snapshot) {
          if (!snapshot.hasData) return Center(child: CircularProgressIndicator());
          return ListView.builder(
            itemCount: snapshot.data!.length,
            itemBuilder: (context, index) {
              var mueble = snapshot.data![index];
              return Card(
                color: Colors.white,
                margin: EdgeInsets.symmetric(horizontal: 15, vertical: 8),
                child: ListTile(
                  title: Text(mueble.nombre, style: TextStyle(fontWeight: FontWeight.bold, color: darkBlue)),
                  subtitle: Text('Precio: \$${mueble.precio} | Stock: ${mueble.stock}'),
                  trailing: Row(
                    mainAxisSize: MainAxisSize.min,
                    children: [
                      IconButton(icon: Icon(Icons.edit, color: Colors.blueGrey), onPressed: () => _showForm(context, mueble)),
                      IconButton(icon: Icon(Icons.delete, color: Colors.redAccent), onPressed: () => deleteMueble(mueble.id)),
                    ],
                  ),
                ),
              );
            },
          );
        },
      ),
      floatingActionButton: FloatingActionButton(
        backgroundColor: darkBlue,
        child: Icon(Icons.add, color: Colors.white),
        onPressed: () => _showForm(context, null),
      ),
    );
  }

  // Formulario para Crear/Actualizar (Skill: Modal Flow)
  void _showForm(BuildContext context, dynamic mueble) {
    // Aquí implementarías un AlertDialog con TextFields para nombre, precio y stock
    // Llamando a addMueble o updateMueble según corresponda.
  }
}
```

---

## 6. Metodología Paso a Paso para Estudiantes

Para una práctica guiada basada en **Antigravity**, los estudiantes deben ver el desarrollo como un sistema de engranajes:

1.  **Rol de Arquitecto:** Diseña la carpeta `lib/` con subcarpetas `models`, `services`, `screens`.
2.  **Agente de Datos:** Configura Firebase y crea el archivo de servicios. Su única "skill" es leer/escribir en la nube.
3.  **Flujo de Trabajo:** * Primero se define **qué** guardamos (Model).
    * Segundo se define **cómo** lo guardamos (Service).
    * Tercero se define **dónde** se ve (UI).

> **Tip de Estilo:** El uso de `StreamBuilder` es vital para que la app se actualice sola cuando cambies algo en la consola de Firebase, permitiendo ver el flujo de datos en tiempo real.

<Elicitations message="¿Deseas profundizar en alguna parte técnica?">
  <Elicitation label="Ver código completo del formulario modal" query="Muestra el código completo del AlertDialog en Flutter para agregar y editar muebles con validación de campos." />
  <Elicitation label="Explicar arquitectura Antigravity" query="Explica detalladamente cómo aplicar los principios de Antigravity para separar la lógica de negocio de la UI en Flutter." />
</Elicitations>
