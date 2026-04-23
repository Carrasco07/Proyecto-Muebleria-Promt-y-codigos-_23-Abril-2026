Para configurar Firebase CLI y Node.js en Windows, necesitamos asegurar que el entorno sea estable y que las variables de entorno estén correctamente direccionadas. Aquí tienes la hoja de ruta técnica para lograrlo.

---

## 1. Software Necesario: Node.js y npm

Para ejecutar Firebase CLI (`firebase-tools`), necesitas **Node.js**, que incluye automáticamente **npm** (Node Package Manager).

### Verificación de Instalación
Antes de instalar nada, verifica si ya existen en tu sistema:
1. Abre la terminal (**PowerShell** o **CMD**).
2. Ejecuta los siguientes comandos:
   * `node -v`
   * `npm -v`

Si recibes un número de versión (ej. `v20.11.0`), ya está instalado. Si recibes un error de "comando no reconocido", procede con la instalación.

### Instalación Paso a Paso en Windows
1. **Descarga:** Ve a la página oficial de [Node.js](https://nodejs.org/) y elige la versión **LTS** (Long Term Support). Es la más estable para desarrollo.
2. **Ejecución:** Abre el instalador `.msi`.
3. **Configuración Global:** Durante el asistente, asegúrate de que la opción **"Add to PATH"** esté seleccionada (viene marcada por defecto). Esto permite que el comando `npm` funcione en cualquier carpeta.
4. **Herramientas Adicionales:** El instalador te preguntará si quieres instalar "Tools for Native Modules". **Marca la casilla**. Esto instalará Chocolatey y Python, necesarios para que algunas librerías de Node funcionen correctamente en Windows.

---

## 2. Instalación de Firebase CLI

Una vez que `npm` esté listo, instalamos las herramientas de Firebase de forma **global** (el parámetro `-g`).

### Comando de Instalación
Ejecuta este comando en tu terminal como **administrador**:
```bash
npm install -g firebase-tools
```

---

## 3. Comandos de Acceso y Gestión

Con la herramienta instalada, el siguiente paso es vincular tu terminal con tu infraestructura en la nube.

### Cómo acceder a Firebase con Google
Para iniciar sesión y dar permisos a la CLI:
1. En la terminal, escribe:
   ```bash
   firebase login
   ```
2. Se abrirá automáticamente una ventana en tu navegador.
3. Selecciona tu cuenta de Google donde creaste el proyecto de la mueblería.
4. Haz clic en **Permitir**. 
5. Regresa a la terminal; verás un mensaje de éxito: `Success! Logged in as usuario@gmail.com`.

---

## 4. Configuración de Firebase para Flutter

A diferencia de otros frameworks, Flutter utiliza una herramienta específica llamada **FlutterFire CLI** para automatizar la conexión (creación de archivos `google-services.json`, etc.).

### Flujo de Trabajo (Workflow)
Para integrar tu proyecto `crudmuebleria` con la consola:

<Steps>
{/* Reason: La secuencia es obligatoria. No puedes configurar FlutterFire sin haber activado primero los comandos de activación global en Dart. */}
  <Step title="Instalar FlutterFire CLI" subtitle="Desde la terminal">
    Ejecuta: `dart pub global activate flutterfire_cli`. Esto descarga la herramienta específica para Flutter.
  </Step>
  <Step title="Configurar el Proyecto" subtitle="Dentro de la carpeta del proyecto">
    Navega a `xfluttercarrasco0533/crudmuebleria` y ejecuta: `flutterfire configure`.
  </Step>
  <Step title="Vincular con la Consola" subtitle="Selección interactiva">
    La terminal te mostrará una lista de tus proyectos de Firebase. Selecciona el proyecto de la mueblería que creaste en la consola.
  </Step>
  <Step title="Generar Archivo de Configuración" subtitle="firebase_options.dart">
    La herramienta creará automáticamente un archivo en tu carpeta `lib` llamado `firebase_options.dart`. Este archivo contiene las credenciales necesarias para Android e iOS.
  </Step>
</Steps>

---

### Comandos Útiles de `firebase-tools`

| Comando | Función |
| :--- | :--- |
| `firebase projects:list` | Muestra todos los proyectos activos en tu cuenta de Firebase. |
| `firebase logout` | Cierra la sesión de la cuenta actual en la computadora. |
| `firebase help` | Muestra la lista completa de comandos disponibles. |

> **Nota para estudiantes:** Si al ejecutar `firebase` o `flutterfire` recibes un error de "script no permitido", ejecuta en PowerShell: `Set-ExecutionPolicy RemoteSigned -Scope CurrentUser`. Esto permite que Windows ejecute los scripts de estas herramientas.

---

<Elicitations message="¿Qué paso sigue en tu práctica?">
  <Elicitation label="Configurar Firebase en el código de Flutter" query="Muéstrame cómo inicializar Firebase en el main.dart usando el archivo firebase_options.dart generado por FlutterFire." />
  <Elicitation label="Solucionar errores comunes de Firebase CLI" query="Cuáles son los errores más comunes al instalar Firebase CLI en Windows y cómo solucionarlos." />
</Elicitations>
