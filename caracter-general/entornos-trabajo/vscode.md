# VSCODE TIPS

## TIPS y atajos.

* Shift + Ctlr + P: Para ejecutar acciones
* Ctlr + ,: Abre configuración
* Ctlr + b: Abre o cierra la barra de exploración.
* Ctlr + k --> p: Muestra los archivos abiertos.

## Configuraciones.

### Cambiar de cursor

Configuración --> buscar **cursor blinking** --> expand

### Selección etiquetas de cierre html

Configuración --> buscar **linked editing** --> habilitar

### Dar color a los pares de llaves

Configuración --> buscar **Bracket pair colorization** --> habilitar

### Mofificar color a las lineas de l par de claves

Configuración --> buscar **Bracket pairs** --> habilitar

### Desactivar Breadcrumbs

Configuración --> buscar **Breadcrumbs** --> deshabilitar

### Indicar en que método nos encontramos

Configuración --> buscar **Sticky scroll** --> habilitar

### Animación del cursor

Configuración --> buscar **Cursor animation** --> habilitar

### Configuración manual

```json
{
    "editor.cursorBlinking": "expand",
    "editor.linkedEditing": true,
    "editor.guides.bracketPairs": true,
    "breadcrumbs.enabled": false,
    "editor.stickyScroll.enabled": true,
    "editor.cursorSmoothCaretAnimation": "on",
    "workbench.iconTheme": "material-icon-theme",
    "terminal.integrated.defaultProfile.windows": "Git Bash", // Cambiar la consola
    "workbench.editor.showTabs": false, // Oculta las pestañas
    "editor.minimap.enabled": false, //Oculta el minimap
    "editor.scrollbar.vertical": "auto", // Hace que la barra vertical aparezca cuando se utilize
    "editor.overviewRulerBorder": false, // Oculta el border del scroll
    "editor.hideCursorInOverviewRuler": true // Oculta la posición del cursor en el scroll
}
```

```json
{
    "editor.cursorBlinking": "expand",
    "editor.linkedEditing": true,
    "editor.guides.bracketPairs": true,
    "breadcrumbs.enabled": false,
    "editor.stickyScroll.enabled": true,
    "editor.cursorSmoothCaretAnimation": "on",
    "terminal.integrated.defaultProfile.windows": "Git Bash",
    "workbench.editor.showTabs": "single",
    "editor.minimap.enabled": false,
    "editor.scrollbar.vertical": "auto",
    "editor.overviewRulerBorder": false,
    "editor.hideCursorInOverviewRuler": true,
    "editor.formatOnSave": true,
    "workbench.productIconTheme": "fluent-icons",
    "workbench.colorTheme": "One Dark Pro",
    "workbench.iconTheme": "material-icon-theme",
    "editor.guides.indentation": false,
    "indenticator.width": 0.5,
    "indenticator.color.dark": "rgba(255,255,255,0.1)",
    "terminal.integrated.env.windows": {},
    "console-ninja.featureSet": "Community",
    "redhat.telemetry.enabled": true,
    "maven.settingsFile": "C:\\Users\\ntsja\\trabajo\\tools\\apache-maven-3.3.9\\conf\\settings.xml",
    "java.configuration.maven.userSettings": "C:\\Users\\ntsja\\trabajo\\tools\\apache-maven-3.3.9\\conf\\settings.xml",
    "maven.executable.path": "C:\\Users\\ntsja\\trabajo\\tools\\apache-maven-3.3.9\\bin\\mvn",
    "maven.executable.preferMavenWrapper": false,
    "maven.terminal.useJavaHome": true
}
```
# Extensiones

* Spanish language: Para lenguaje en español.
* Fluent icons: Iconos 
* Material Icon Theme: Tambien cambia iconos
* Error Lends: Error en la misma linea
* Console Ninja: Resultado de la ejecución en el fuente
* Image preview: Previsualizar imágenes
* Indenticator: Visualiza la regla de indentación solo donde me encuentro
* Prettier: Formateador de código JS
* Auto Rename Tag: REnombra automáticamente etiquetas aunque no sean ficheros HTML.
* GITLEns: Mejora la info de git
* Code spell checker: corrije errores de código, con soporte español
* Markdown all in one: Para trabajar con markdown
* IntelliCode: IA para optimizar código

## Temas

Temas mas usados

* One Dark Pro
* Andromeda