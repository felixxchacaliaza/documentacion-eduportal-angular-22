# 📘 Guía del Estudiante • Sesión 01
## Arquitectura Base, Entorno de Desarrollo y App Shell SaaS

> **Curso:** Desarrollo Web Frontend Profesional con Angular 22  
> **Proyecto Transversal:** EduPortal — Sistema de Gestión Escolar SaaS  
> **Duración:** 3 Horas (180 Minutos) • 3 Bloques de 50 min y 2 Recesos de 10 min  
> **Stack:** Angular 22 • TypeScript • Zoneless • SCSS • Angular Material  
> **Backend de Apoyo:** `eduportal-api-mongodb` (`http://localhost:3000`) • MongoDB & Express  
> **Docente:** Félix Junior Chacaliaza Gutiérrez • Intevex Academy

---

## 🎯 Objetivos de la Sesión
1. **Entorno:** Instalar y verificar Node.js LTS, Git, Angular CLI v22 y MongoDB Community Server con Compass.
2. **Backend:** Poner en marcha la API REST en Express y poblar la base de datos con el seed escolar del 2026.
3. **Angular 22 Zoneless:** Crear el proyecto `eduportal-web` y activar el motor reactivo Zoneless en `app.config.ts`.
4. **Design System & BEM:** Configurar Angular Material, fuentes oficiales e implantar la metodología BEM + Sass con especificidad plana (`0, 0, 1, 0`).
5. **POO & Modelos:** Experimentar clases, constructores, inmutabilidad con `readonly` y herencia en el laboratorio (`oop-laboratory.ts`), y crear las interfaces del dominio escolar en `core/models/`.
6. **App Shell SaaS:** Maquetar el Navbar, Sidebar responsivo colapsable y MainLayout con enrutamiento de páginas bajo demanda (*Lazy Loading*).

---

## 🛠️ PARTE 1: Instalación del Entorno de Desarrollo

Asegúrate de contar con las siguientes herramientas instaladas y funcionando en tu sistema operativo:

### 1.1 Node.js (Entorno de Ejecución)
* **Versión requerida:** Node.js 20.x o 22.x LTS (Long Term Support).
* **Descarga:** [nodejs.org](https://nodejs.org/)
* **Validación en terminal:**
```bash
node -v
npm -v
```
*Salida esperada:* `v20.x.x` o `v22.x.x` en Node, y `10.x.x` en npm.

### 1.2 Git y Git Bash (Control de Versiones)
* **Herramienta:** Git y emulador Git Bash para Windows ([git-scm.com](https://git-scm.com/)).
* **Validación en terminal:**
```bash
git --version
```

### 1.3 Visual Studio Code y Extensiones Oficiales
* **Editor:** Visual Studio Code ([code.visualstudio.com](https://code.visualstudio.com/)).
* **Extensiones requeridas:**
  1. **Angular Language Service** (`Angular.ng-template`): Autocompletado y validación en plantillas HTML.
  2. **Prettier - Code Formatter** (`esbenp.prettier-vscode`): Formato consistente de código.
  3. **Material Icon Theme** (`PKief.material-icon-theme`): Identificación gráfica de archivos de Angular.
  4. **SCSS IntelliSense** (`mrmlnc.vscode-scss`): Autocompletado de estilos Sass.

### 1.4 Angular CLI (Línea de Comandos Oficial de Angular)
* **Instalación global:**
```bash
npm install -g @angular/cli
```
* **Validación:**
```bash
ng version
```

### 1.5 MongoDB Community Server & MongoDB Compass
* **Servidor NoSQL:** [MongoDB Community Server](https://www.mongodb.com/try/download/community) (puerto local: `27017`).
* **Cliente Gráfico:** MongoDB Compass para explorar las colecciones de la escuela.
* **Validación:**
```bash
mongosh --version
```
*(O abrir Compass y comprobar conexión en `mongodb://localhost:27017`)*.

#### ⚠️ Puntos de Fricción Frecuentes en la Instalación:
1. **Error de ExecutionPolicy en PowerShell:** Ejecuta `Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned` como Administrador o utiliza **Git Bash**.
2. **Servicio MongoDB no iniciado:** Abre `services.msc` en Windows y verifica que el servicio **MongoDB Server** esté en estado *En ejecución*.

---

## 🍃 PARTE 2: Puesta en Marcha del Backend y Base de Datos

El backend de EduPortal ya está construido en Node.js, Express y MongoDB. Debemos levantarlo para alimentar nuestra futura aplicación Angular.

### 2.1 Instalar dependencias del Backend
Abre una terminal y navega hasta la carpeta del backend:
```bash
cd "C:\Users\felix\Desktop\Curso Angular\eduportal-api-mongodb"
npm install
```

### 2.2 Poblar la Base de Datos (Seed Data)
Ejecuta el script de inicialización para crear las colecciones escolares con datos realistas del año lectivo 2026:
```bash
npm run db:seed
```

*Salida esperada en la consola:*
```text
🍃 Conexión exitosa a MongoDB: localhost/eduportal
🌱 Iniciando carga de datos en MongoDB...
🧹 Limpiando colecciones anteriores en MongoDB...
🔐 Registrando perfiles y matriz de permisos...
📅 Registrando año lectivo 2026 con 4 bimestres...
🏫 Registrando las 10 secciones de secundaria...
📚 Registrando las Asignaturas/Subjects Oficiales del MINEDU...
👨‍🎓 Generando 100 estudiantes distribuidos en 10 aulas...
👨‍🏫 Registrando Plana Docente Oficial con Jornada MINEDU...
👤 Creando usuarios institucionales (personal y docentes)...
✅ Base de datos MongoDB cargada exitosamente!
```

### 2.3 Iniciar el Servidor de la API y Validación en Navegador
Inicia el servidor en modo desarrollo:
```bash
npm run dev
```

*Verificación en el navegador:*  
Abre [http://localhost:3000/api/health](http://localhost:3000/api/health). Deberás recibir:

```json
{
  "status": "UP",
  "database": "CONNECTED",
  "uptime": 1.45,
  "timestamp": "2026-10-09T..."
}
```

> **⚠️ Importante:** Mantén esta terminal abierta en segundo plano durante toda la sesión.

---

## 🚀 PARTE 3: Creación del Proyecto Angular 22 y Núcleo Zoneless

Crearemos la aplicación frontend utilizando el CLI oficial de Angular y configuraremos el nuevo motor reactivo **Zoneless** de Angular 22.

### 3.1 Crear el Proyecto Base con el CLI
Abre una **segunda pestaña o ventana de terminal** y sitúate en la raíz del curso:

```bash
cd "C:\Users\felix\Desktop\Curso Angular"
ng new eduportal-web --routing --style=scss --skip-tests --ssr=false
cd eduportal-web
```

*Opciones que aplica el CLI:*
* `--routing`: Genera la tabla de rutas de navegación base en `app.routes.ts`.
* `--style=scss`: Habilita Sass con soporte de variables y anidamiento.
* `--skip-tests`: Omite archivos `*.spec.ts` para mantener limpia la arquitectura inicial.

### 3.2 Configurar la Arquitectura Zoneless en `app.config.ts`
Abre el proyecto en VS Code (`code .`). Abre el archivo `src/app/app.config.ts` y registra el proveedor Zoneless y el cliente HTTP:

```typescript
// src/app/app.config.ts
import { ApplicationConfig, provideExperimentalZonelessChangeDetection } from '@angular/core';
import { provideRouter } from '@angular/router';
import { provideHttpClient } from '@angular/common/http';
import { routes } from './app.routes';

export const appConfig: ApplicationConfig = {
  providers: [
    provideExperimentalZonelessChangeDetection(),
    provideRouter(routes),
    provideHttpClient()
  ]
};
```

#### 🧠 Concepto Teórico Clave: Single Page Application (SPA) vs Sitios Tradicionales (MPA)
* **MPA Tradicional:** Cada clic pide una página HTML nueva al servidor, destruye la pantalla actual y recarga todo desde cero con parpadeo blanco.
* **SPA Moderna (Angular 22):** El navegador descarga el `index.html` y los scripts una sola vez. Al navegar, Angular intercepta el enlace con la **History API** del navegador, pide solo los datos JSON a MongoDB y actualiza el área de la pantalla necesaria al instante.

```text
[ARQUITECTURA TRADICIONAL (MPA)]
Clic en enlace ➔ Petición HTTP ➔ Servidor envía HTML completo ➔ Destrucción del DOM ➔ Pantalla Blanca ➔ Lento

[ARQUITECTURA SPA (ANGULAR 22)]
Carga Inicial: Descarga index.html + Bundles JS (1 sola vez)
Navegación:    Clic ➔ Router intercepta (History API) ➔ Pide JSON al Backend ➔ Reemplaza el componente al instante
```

#### 🧠 Concepto Teórico Clave: ¿Qué es Zoneless y por qué marca un antes y un después?
* **Antes (con `zone.js`):** Cada evento asíncrono (`setTimeout`, `click`, etc.) forzaba a Angular a recorrer todo el árbol de componentes para detectar cambios a ciegas (*Dirty Checking*).
* **Ahora (Zoneless en Angular 22):**
  1. Se elimina la biblioteca `zone.js`, reduciendo el tamaño del bundle.
  2. La reactividad es granular basada en **Signals**.
  3. Angular sabe con precisión qué nodo del DOM depende de cada señal y actualiza quirúrgicamente solo ese elemento.

---

## 🎨 PARTE 4: Instalación de Extras y Design System

Instalaremos Angular Material y configuraremos la paleta visual oficial del sistema.

### 4.1 Instalar Angular Material y Componentes CDK
Ejecuta en la terminal de tu proyecto:
```bash
npm install @angular/material @angular/cdk
```

### 4.2 Configurar Fuentes e Iconos en `src/index.html`
Abre `src/index.html` y dentro de la etiqueta `<head>` añade las fuentes oficiales de Google (**Inter**, **Poppins**) y los iconos **Material Symbols**:

```html
<!-- src/index.html -->
<!doctype html>
<html lang="es">
<head>
  <meta charset="utf-8">
  <title>EduPortal • Sistema de Gestión Escolar</title>
  <base href="/">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <link rel="icon" type="image/x-icon" href="favicon.ico">
  
  <!-- Tipografías Oficiales: Inter & Poppins -->
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&family=Poppins:wght@500;600;700&display=swap" rel="stylesheet">
  
  <!-- Iconografía Oficial Material Symbols & Icons -->
  <link href="https://fonts.googleapis.com/icon?family=Material+Icons|Material+Symbols+Outlined" rel="stylesheet">
</head>
<body class="mat-typography">
  <app-root></app-root>
</body>
</html>
```

> **📚 Referencias:** `mat-typography` aplica la escala tipográfica estándar de Material Design al documento.

### 4.3 Configurar Estilos Globales en `src/styles.scss`
Reemplaza el contenido de `src/styles.scss` por el Design System institucional de EduPortal:

```scss
/* src/styles.scss */
@import '@angular/material/prebuilt-themes/azure-blue.css';

:root {
  /* Paleta Institucional EduPortal */
  --primary: #e40035;
  --primary-hover: #c8002f;
  --primary-light: #fff1f2;
  --secondary: #0f172a;
  --secondary-light: #1e293b;
  --accent: #00b4d8;
  
  /* Fondos y Superficies */
  --bg-page: #f8fafc;
  --bg-surface: #ffffff;
  --border-color: #e2e8f0;
  
  /* Textos */
  --text-main: #0f172a;
  --text-muted: #64748b;
  --text-light: #94a3b8;
  
  /* Estados Escolares */
  --success: #10b981;
  --success-bg: #ecfdf5;
  --warning: #f59e0b;
  --warning-bg: #fffbeb;
  --danger: #ef4444;
  --danger-bg: #fef2f2;
  --info: #3b82f6;
  --info-bg: #eff6ff;

  /* Sombras y Radios */
  --shadow-sm: 0 1px 2px 0 rgba(0, 0, 0, 0.05);
  --shadow-md: 0 4px 6px -1px rgba(0, 0, 0, 0.1);
  --shadow-lg: 0 10px 15px -3px rgba(0, 0, 0, 0.1);
  
  --radius-sm: 6px;
  --radius-md: 10px;
  --radius-lg: 16px;
  --radius-full: 9999px;

  /* Tipografías */
  --font-main: 'Inter', -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
  --font-heading: 'Poppins', var(--font-main);
}

/* Reset y Estructura Global */
* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

body {
  font-family: var(--font-main);
  background-color: var(--bg-page);
  color: var(--text-main);
  line-height: 1.5;
  -webkit-font-smoothing: antialiased;
}

/* Badges con Metodología BEM */
.badge {
  display: inline-flex;
  align-items: center;
  padding: 0.25rem 0.65rem;
  font-size: 0.75rem;
  font-weight: 600;
  border-radius: var(--radius-full);
  text-transform: uppercase;
  letter-spacing: 0.025em;

  &--success {
    background-color: var(--success-bg);
    color: var(--success);
  }

  &--danger {
    background-color: var(--danger-bg);
    color: var(--danger);
  }

  &--warning {
    background-color: var(--warning-bg);
    color: var(--warning);
  }

  &--info {
    background-color: var(--info-bg);
    color: var(--info);
  }
}
```

### 4.4 Arquitectura de Estilos con Metodología BEM + Sass (SCSS)
En proyectos empresariales usamos **BEM (Block, Element, Modifier)** junto con **Sass (SCSS)** para evitar el desorden de estilos globales:
* **Bloque (`Block`):** El componente independiente (ej. `.badge`, `.app-sidebar`).
* **Elemento (`__Element`):** Parte interna de un bloque (ej. `.app-sidebar__link`).
* **Modificador (`--Modifier`):** Estado o variante visual (ej. `.badge--success`, `.app-sidebar--collapsed`).

#### ⚡ La Sinergia BEM + Sass con el Selector Padre (`&`)
El selector ampersand (`&`) toma la clase padre y le añade el modificador o elemento limpiamente:

```scss
.badge {
  padding: 0.25rem 0.65rem;

  &--success {
    background-color: var(--success-bg);
    color: var(--success);
  }
}
```

#### 🧠 ¿Por qué BEM salva a las aplicaciones grandes de "La Guerra de Especificidad"?
Con BEM, cada regla se aplica a una clase única con especificidad plana (**0, 0, 1, 0**). Esto elimina selectores anidados profundos y la necesidad de usar `!important`.

| Tipo de Selector | Ejemplo | Especificidad | Recomendación |
| :--- | :--- | :---: | :---: |
| Estilo en línea | `<div style="...">` | **1, 0, 0, 0** | 🔴 Evitar siempre |
| Selector por ID | `#main-sidebar` | **0, 1, 0, 0** | 🔴 Evitar en componentes |
| **Clase BEM** | `.badge--success` | **0, 0, 1, 0** | 🟢 Estándar recomendado |
| Etiqueta anidada | `div > ul > li > a` | **0, 0, 0, 4** | 🟡 Frágil al modificar HTML |

#### 🧠 Variables CSS (`:root`) vs Variables Sass
* **Variables CSS (`var(--color)`):** Viven en el navegador en tiempo de ejecución, permitiendo cambiar temas (Light/Dark Mode) al instante.
* **Variables Sass (`$color`):** Se resuelven durante la compilación y desaparecen en el CSS final entregado al navegador.

---

## 📁 PARTE 5: Estructura de Carpetas Profesional

En EduPortal adoptamos la arquitectura modular estándar recomendada para proyectos empresariales en Angular:

### 5.1 Estructura Modular de 4 Capas

```text
src/app/
├── core/                  # Elementos únicos de la aplicación (Singletons)
│   ├── layouts/           # Plantillas estructurales (MainLayout)
│   ├── models/            # Entidades de dominio y contratos TypeScript
│   ├── sandbox/           # Laboratorio didáctico de POO en TypeScript
│   └── services/          # Servicios globales de estado y configuración
├── shared/                # Recursos reutilizables en múltiples módulos
│   ├── components/        # Componentes UI (Navbar, Sidebar)
│   ├── directives/        # Directivas de atributo y comportamiento
│   └── pipes/             # Pipes de formateo visual
└── features/              # Páginas y módulos de negocio de la escuela
    ├── dashboard/pages/   # Panel de control e indicadores
    ├── students/pages/    # Padrón oficial de alumnos
    └── grades/pages/      # Matriz de calificaciones bimestrales
```

* **`core/`:** Servicios globales (Singletons), guardas, layouts maestros y modelos del dominio. Se cargan una sola vez al iniciar la aplicación.
* **`shared/`:** Componentes comunes (Navbar, Sidebar, botones, pipes) reutilizables en cualquier pantalla.
* **`features/`:** Pantallas de negocio de la escuela (Dashboard, Alumnos, Notas), cargadas bajo demanda (*Lazy Loading*).

---

## 🧬 PARTE 6: Cátedra de POO en TypeScript & Modelos del SaaS

En esta sección exploraremos la **Programación Orientada a Objetos (POO) en TypeScript** a través de un laboratorio práctico y amigable inspirado en un **Universo de Superhéroes y Poderes**. Cada concepto se presenta **directamente al nivel de su bloque de código correspondiente**, construyendo paso a paso el archivo `oop-laboratory.ts` para luego definir los modelos oficiales del sistema SaaS.

### 6.1 Laboratorio Didáctico de POO: Universo de Superhéroes (`oop-laboratory.ts`)

Crea la carpeta de pruebas en:
```bash
mkdir -p src/app/core/sandbox
```

---

#### 6.1.1 Bloque 1: Clases Básicas, Constructor y el Operador `new`

```typescript
// Una clase es el molde o plano para crear personajes:
export class HeroeSimple {
  // Propiedades básicas (datos que tiene el héroe):
  alias: string;
  puntosDeVida: number;

  // El constructor se ejecuta automáticamente cuando nace el héroe con 'new':
  constructor(alias: string, puntosDeVida: number) {
    this.alias = alias;
    this.puntosDeVida = puntosDeVida;
  }

  // Método (acción que puede realizar):
  saludar(): string {
    return `¡Hola! Soy ${this.alias} y tengo ${this.puntosDeVida} puntos de vida.`;
  }
}

// Con el operador 'new' creamos una instancia real en memoria:
const spidey = new HeroeSimple('Spider-Man', 100);
console.log(spidey.saludar());
```

##### 💡 Conceptos Clave:
* **Clase (`class`):** Es como un plano arquitectónico que agrupa datos (propiedades) y acciones (métodos).
* **El Constructor (`constructor`):** Función especial que inicializa el objeto cuando se crea. La palabra clave `this` se refiere a la instancia actual del héroe.
* **El Operador `new`:** Reserva espacio en memoria y ejecuta el constructor para dar vida al objeto.

---

#### 6.1.2 Bloque 2: Modificadores de Acceso (`public`, `protected`, `private`) y *Parameter Properties*

```typescript
export class HeroeSeguro {
  // Parameter Properties de TypeScript:
  // Al escribir 'public', 'protected' o 'private' en los argumentos del constructor,
  // TypeScript crea las variables y les asigna el valor en una sola línea:
  constructor(
    public alias: string,               // PUBLIC: Su nombre de batalla (todos lo conocen)
    protected cuartelSecreto: string,   // PROTECTED: Solo este héroe y sus aliados (clases hijas) entran
    private _identidadReal: string      // PRIVATE: Su identidad civil secreta (nadie fuera de él la sabe)
  ) {}

  // Método público para revelar la identidad solo si el héroe lo decide:
  public revelarIdentidad(): string {
    return `Mi verdadera identidad es: ${this._identidadReal}`;
  }
}
```

##### 💡 Conceptos Clave:
* **Modificadores de Acceso:** Controlan quién puede ver o modificar la información:
  * **`public` (Acceso Total):** Visible desde cualquier parte del código (valor por defecto).
  * **`protected` (Acceso Familiar):** Visible dentro de la clase y en las clases hijas que hereden de ella (`extends`). Extraños no pueden acceder.
  * **`private` (Secreto Estricto):** Visible única y exclusivamente dentro de la propia clase.
* **Parameter Properties:** Ahorra escribir código repetitivo (`this.alias = alias`) colocando el modificador de acceso directo en el constructor.

---

#### 6.1.3 Bloque 3: La Directiva `readonly` y la Inmutabilidad

```typescript
export class HeroeInmutable {
  // 'readonly': Una vez que se le asigna un valor, ¡NUNCA MÁS se puede cambiar!
  public readonly universo: string = 'Tierra-616';

  constructor(
    public readonly idHeroe: string,    // Solo se asigna al nacer
    public alias: string                // Este sí puede cambiar con el tiempo
  ) {}
}

const flash = new HeroeInmutable('HERO-001', 'Flash');
flash.alias = 'Flash Escarlata'; // ✅ Permitido: 'alias' no tiene readonly

// ❌ ERROR de TypeScript (TS2540):
// Cannot assign to 'universo' because it is a read-only property.
// flash.universo = 'Tierra-999';
```

##### 💡 Conceptos Clave:
* **Inmutabilidad con `readonly`:** Protege las propiedades contra modificaciones accidentales después de crearse. Si intentas reasignar una propiedad `readonly`, TypeScript bloquea la compilación con el error `TS2540`.
* **`readonly` vs `const`:** `const` se usa para variables locales (`const gravedad = 9.8`), mientras que `readonly` se usa para propiedades de clases e interfaces.
* **Relevancia en Angular Moderno (Zoneless):** Trabajar con datos inmutables asegura que las señales reactivas funcionen de manera predecible, evitando alterar objetos existentes a ciegas en lugar de emitir nuevos estados.

---

#### 6.1.4 Bloque 4: El Contrato con Interfaces (`interface`), Genéricos (`<T>`) e `implements`

```typescript
// 1. LA INTERFAZ: El carnet de registro de la Liga (define los requisitos obligatorios)
// (Ahora que ya conocemos 'readonly', lo usamos para proteger los datos):
export interface HeroeContrato {
  readonly alias: string;
  presentarse(): string;
}

// Interfaz para habilidades especiales:
export interface Volador {
  volar(altitudMetros: number): string;
}

// 2. GENÉRICOS (<T>): Una mochila mágica que guarda CUALQUIER artefacto <T> sin usar 'any':
export interface Mochila<T> {
  readonly artefacto: T;
  readonly fechaObtencion: Date;
}

// Ejemplo: Una mochila que transporta un Escudo fuertemente tipado:
interface Escudo {
  defensa: number;
}

const mochilaCapitan: Mochila<Escudo> = {
  artefacto: { defensa: 100 },
  fechaObtencion: new Date()
};
```

##### 💡 Conceptos Clave:
* **`interface`:** Contrato puro que describe qué propiedades y métodos debe tener un objeto.
* **0 Bytes en Runtime (*Type Erasure*):** Las interfaces solo existen en TypeScript; desaparecen completamente al compilar a JavaScript.
* **Palabra Clave `implements`:** Una clase puede comprometerse a cumplir uno o múltiples contratos a la vez (`implements HeroeContrato, Volador`).
* **Genéricos (`<T>`):** Parámetros de tipo que permiten reutilizar contratos para diferentes tipos de datos de forma segura sin usar `any`.
* **Tipado Estructural (*Duck Typing*):** TypeScript se fija en la forma del objeto: si tiene las propiedades y métodos requeridos, es compatible sin importar el nombre de su clase.

---

#### 6.1.5 Bloque 5: Clases Abstractas (`abstract class`), Herencia (`extends`) y `super()`

```typescript
// 1. CLASE ABSTRACTA: El molde general de personajes (no se puede crear directamente con 'new')
export abstract class PersonajeBase implements HeroeContrato {
  public readonly universo: string = 'Tierra-616';

  constructor(
    public readonly alias: string,
    protected cuartelSecreto: string,
    private _identidadReal: string,
    public puntosDeVida: number = 100
  ) {}

  // Método Concreto: Lógica compartida que TODOS los héroes heredan gratis:
  public curarse(puntos: number): void {
    this.puntosDeVida += puntos;
  }

  public presentarse(): string {
    return `¡Soy ${this.alias} del universo ${this.universo}!`;
  }

  // Método Abstracto: ¡Obligación pura! Cada héroe ataca de manera diferente:
  public abstract atacar(): string;
}

// 2. CLASE HIJA: Hereda de PersonajeBase y cumple el contrato Volador:
export class HeroeVolador extends PersonajeBase implements Volador {
  constructor(
    alias: string,
    cuartelSecreto: string,
    identidadReal: string,
    public readonly velocidadKmH: number
  ) {
    // 🧠 'super()' llama al constructor del padre para inicializar la vida y el nombre:
    super(alias, cuartelSecreto, identidadReal, 120);
  }

  // Implementación obligatoria del ataque:
  public atacar(): string {
    return `💥 ¡${this.alias} dispara un rayo de energía repulsora!`;
  }

  // Implementación obligatoria del vuelo:
  public volar(altitudMetros: number): string {
    return `🚀 ¡${this.alias} vuela a ${altitudMetros} metros a ${this.velocidadKmH} km/h!`;
  }

  // El hijo sí puede entrar a su cuartel porque es protected:
  public irAlCuartel(): string {
    return `${this.alias} ingresó a su base: ${this.cuartelSecreto}`;
  }
}
```

##### 💡 Conceptos Clave:
* **Clase Abstracta (`abstract class`):** Molde general que no se puede instanciar con `new`. Sirve para compartir código entre clases hijas y definir métodos obligatorios.
* **Patrón *Template Method*:** Métodos concretos compartidos (`curarse`, `presentarse`) conviven con métodos abstractos obligatorios (`atacar`).
* **Herencia (`extends`):** Permite que una clase hija adquiera las propiedades y métodos de una clase padre.
* **Regla de `super()`:** En constructores de clases hijas, es obligatorio llamar a `super()` antes de usar `this`; de lo contrario se produce un error (`ReferenceError`).
* **Cadena de Prototipos (*Prototype Chain*):** Si llamas a un método que no está en la clase hija (como `curarse()`), JavaScript busca automáticamente en la clase padre.

---

#### 6.1.6 Bloque 6: Instanciación con `new`, Demostración en Vivo y Pruebas de Protección

```typescript
// Damos vida a Iron Man en memoria:
const ironMan = new HeroeVolador(
  'Iron Man',
  'Torre de los Vengadores',
  'Tony Stark',
  800
);

console.log('--- 🦸 LABORATORIO DE SUPERHÉROES ---');
console.log(ironMan.presentarse());
console.log(ironMan.atacar());
console.log(ironMan.volar(3000));
ironMan.curarse(30);
console.log(`Vida actual: ${ironMan.puntosDeVida}`);
console.log(ironMan.irAlCuartel());

// ⚠️ PRUEBAS DE PROTECCIÓN (Descomentar para ver los errores de TypeScript):
// ironMan.universo = 'Tierra-999';          // ❌ TS2540: 'universo' es readonly
// console.log(ironMan._identidadReal);       // ❌ TS2341: '_identidadReal' es private
// console.log(ironMan.cuartelSecreto);      // ❌ TS2445: 'cuartelSecreto' es protected
// const fantasma = new PersonajeBase('X', 'Cueva', 'Anon'); // ❌ TS2511: Clase abstracta
```

##### 💡 Conceptos Clave:
* **Instanciación:** El operador `new` ejecuta el constructor, enlaza la cadena de prototipos y retorna la instancia lista para operar.

---

#### 6.1.7 Código Completo Integrado de `src/app/core/sandbox/oop-laboratory.ts`

Copia el código completo y guárdalo en `src/app/core/sandbox/oop-laboratory.ts`:

```typescript
// src/app/core/sandbox/oop-laboratory.ts
/**
 * 🧪 LABORATORIO DE APRENDIZAJE: Programación Orientada a Objetos en TypeScript
 * 
 * 📌 OBJETIVO:
 * Comprender clases, herencia, modificadores de acceso, inmutabilidad (readonly)
 * e interfaces mediante un universo de superhéroes.
 */

// 1. LAS INTERFACES (Contratos)
export interface HeroeContrato {
  readonly alias: string;
  presentarse(): string;
}

export interface Volador {
  volar(altitudMetros: number): string;
}

export interface Mochila<T> {
  readonly artefacto: T;
  readonly fechaObtencion: Date;
}

// 2. LA CLASE ABSTRACTA (El Molde Base)
export abstract class PersonajeBase implements HeroeContrato {
  public readonly universo: string = 'Tierra-616';

  constructor(
    public readonly alias: string,
    protected cuartelSecreto: string,
    private _identidadReal: string,
    public puntosDeVida: number = 100
  ) {}

  public curarse(puntos: number): void {
    this.puntosDeVida += puntos;
  }

  public presentarse(): string {
    return `¡Soy ${this.alias} del universo ${this.universo}!`;
  }

  public revelarIdentidad(): string {
    return `Mi verdadera identidad es: ${this._identidadReal}`;
  }

  public abstract atacar(): string;
}

// 3. LA CLASE HIJA (Herencia e Implementación)
export class HeroeVolador extends PersonajeBase implements Volador {
  constructor(
    alias: string,
    cuartelSecreto: string,
    identidadReal: string,
    public readonly velocidadKmH: number
  ) {
    super(alias, cuartelSecreto, identidadReal, 120);
  }

  public atacar(): string {
    return `💥 ¡${this.alias} dispara un rayo de energía repulsora!`;
  }

  public volar(altitudMetros: number): string {
    return `🚀 ¡${this.alias} vuela a ${altitudMetros} metros a ${this.velocidadKmH} km/h!`;
  }

  public irAlCuartel(): string {
    return `${this.alias} ingresó a su base: ${this.cuartelSecreto}`;
  }
}

// 4. DEMOSTRACIÓN PRÁCTICA
const ironMan = new HeroeVolador(
  'Iron Man',
  'Torre de los Vengadores',
  'Tony Stark',
  800
);

console.log('--- 🦸 LABORATORIO DE SUPERHÉROES ---');
console.log(ironMan.presentarse());
console.log(ironMan.atacar());
console.log(ironMan.volar(3000));
ironMan.curarse(30);
console.log(`Vida actual: ${ironMan.puntosDeVida}`);
console.log(ironMan.irAlCuartel());
console.log(ironMan.revelarIdentidad());

// Mochila genérica tipada:
interface Escudo {
  defensa: number;
}

const mochilaCapitan: Mochila<Escudo> = {
  artefacto: { defensa: 100 },
  fechaObtencion: new Date()
};
console.log(`Defensa del escudo en mochila: ${mochilaCapitan.artefacto.defensa}`);
```

---

### 6.2 La Decisión Arquitectónica de Angular 22: ¿Por qué Interfaces para los Datos de Red?

* **Clases:** Se reservan para Componentes (`@Component`) y Servicios (`@Injectable`), que gestionan lógica viva y ciclo de vida en la aplicación.
* **Interfaces:** Se usan para modelar los datos JSON de MongoDB porque tienen **peso de 0 bytes** en runtime, no requieren mappers con `new` y permiten que la reactividad se maneje con **Signals `computed()`** en los componentes.

---

### 6.3 Modelos Oficiales del Proyecto EduPortal (Interfaces Puras e Inmutables)

Crea la carpeta oficial de modelos:
```bash
mkdir -p src/app/core/models
```

> **💡 Generar Interfaces con el CLI:** `ng generate interface core/models/student` (o `ng g i core/models/student`).

---

#### 6.3.1 `src/app/core/models/api-response.model.ts`

```typescript
// src/app/core/models/api-response.model.ts
export interface ApiResponse<T> {
  readonly success: boolean;
  readonly message?: string;
  readonly count?: number;
  readonly data: T;
}
```

* `<T>` permite que `data` adopte el tipo del recurso solicitado (`Student[]`, `Teacher`, etc.) con autocompletado en el editor.

---

#### 6.3.2 `src/app/core/models/student.model.ts`

```typescript
// src/app/core/models/student.model.ts
export type AcademicGrade =
  | '1° Secundaria'
  | '2° Secundaria'
  | '3° Secundaria'
  | '4° Secundaria'
  | '5° Secundaria';

export interface EmergencyContact {
  readonly id?: string;
  readonly name: string;
  readonly relationship: string;
  readonly phone: string;
}

export interface Student {
  readonly id: string;
  readonly dni: string;
  readonly firstName: string;
  readonly lastName: string;
  readonly email: string;
  readonly phone: string;
  readonly birthDate: string;
  readonly grade: AcademicGrade;
  readonly section: string;
  readonly status: 'ACTIVE' | 'INACTIVE';
  readonly averageGrade: number;
  readonly attendancePercentage: number;
  readonly emergencyContacts: readonly EmergencyContact[];
  readonly registeredAt: string;
  readonly avatarUrl?: string;
}
```

---

#### 6.3.3 `src/app/core/models/teacher.model.ts`

```typescript
// src/app/core/models/teacher.model.ts
export interface TeacherAssignment {
  readonly id: string;
  readonly grade: string;
  readonly section: string;
  readonly subjectId: string;
  readonly subjectName: string;
  readonly weeklyHours: number;
}

export interface Teacher {
  readonly id: string;
  readonly dni: string;
  readonly firstName: string;
  readonly lastName: string;
  readonly fullName: string;
  readonly email: string;
  readonly phone: string;
  readonly specialty: string;
  readonly contractedHours: number;
  readonly assignedHours: number;
  readonly status: 'ACTIVE' | 'INACTIVE';
  readonly assignments: readonly TeacherAssignment[];
}
```

---

#### 6.3.4 `src/app/core/models/subject.model.ts`

```typescript
// src/app/core/models/subject.model.ts
export type SubjectCategory = 'CIENCIAS' | 'HUMANIDADES' | 'TECNOLOGÍA' | 'ARTES' | 'IDIOMAS';

export interface Subject {
  readonly id: string;
  readonly code: string;
  readonly name: string;
  readonly category: SubjectCategory;
  readonly specialty?: string;
  readonly grade: string;
  readonly section: string;
  readonly classroom: string;
  readonly teacherName: string;
  readonly weeklyHours: number;
  readonly enrolledCount: number;
  readonly maxCapacity: number;
  readonly icon: string;
  readonly color: string;
  readonly status?: 'ACTIVE' | 'INACTIVE';
}
```

---

#### 6.3.5 `src/app/core/models/grade-section.model.ts`

```typescript
// src/app/core/models/grade-section.model.ts
export interface GradeSection {
  readonly id: string;
  readonly grade: string;
  readonly section: string;
  readonly classroom: string;
  readonly maxCapacity: number;
  readonly status: 'ACTIVE' | 'INACTIVE';
  readonly enrolledStudents?: number;
}
```

---

#### 6.3.6 `src/app/core/models/grade.model.ts`

```typescript
// src/app/core/models/grade.model.ts
export interface Grade {
  readonly id: string;
  readonly studentId: string;
  readonly subjectId: string;
  readonly academicYearId: string;
  readonly bimester1: number | null;
  readonly bimester2: number | null;
  readonly bimester3: number | null;
  readonly bimester4: number | null;
  readonly finalAverage: number;
  readonly isPassing: boolean;
}
```

---

#### 6.3.7 `src/app/core/models/user.model.ts`

```typescript
// src/app/core/models/user.model.ts
export type UserRole = 'ADMIN' | 'DIRECTOR' | 'TEACHER' | 'STUDENT';

export interface User {
  readonly id: string;
  readonly name: string;
  readonly email: string;
  readonly role: UserRole;
  readonly profileId?: string;
  readonly profileName?: string;
  readonly options?: readonly string[];
  readonly permissions?: Readonly<Record<string, 'FULL' | 'READ_ONLY'>>;
  readonly appAccess?: boolean;
  readonly webAccess?: boolean;
}
```

---

#### 6.3.8 `src/app/core/models/index.ts` (Barrel Export)

```typescript
// src/app/core/models/index.ts
export * from './api-response.model';
export * from './student.model';
export * from './teacher.model';
export * from './subject.model';
export * from './grade-section.model';
export * from './grade.model';
export * from './user.model';
```

---

## 🧠 PARTE 7: Fundamentos Teóricos — Componentes Standalone y Enrutamiento

Antes de ensamblar la interfaz, revisaremos los conceptos esenciales de componentes y navegación en Angular 22:

### 7.1 ¿Qué es un Componente y la Arquitectura de sus 4 Archivos?
Un componente es el bloque de construcción fundamental de la pantalla. En Angular profesional se estructura en **4 archivos**:
1. **Clase TypeScript (`*.component.ts`):** Cerebro del componente (variables, señales y métodos).
2. **Plantilla HTML (`*.component.html`):** Estructura visual renderizada en el navegador.
3. **Hoja de Estilos (`*.component.scss`):** Estilos visuales encapsulados que no se filtran a otras pantallas.
4. **Pruebas Unitarias (`*.component.spec.ts`):** Pruebas automatizadas (se abordarán a fondo en la Sesión 08 con Vitest).

#### 🧠 ¿Cómo funciona el Encapsulamiento de Estilos (`ViewEncapsulation.Emulated`) por debajo de la mesa?
Angular emula el Shadow DOM del navegador: genera un identificador único por componente (ej. `_ngcontent-ng-c123`) y reescribe los selectores CSS para que solo apliquen a sus propias etiquetas HTML, evitando colisiones de diseño.

* **Propiedades del decorador `@Component`:**
  * `selector`: Nombre de la etiqueta HTML personalizada (ej. `'app-navbar'`).
  * `templateUrl`: Ruta al archivo HTML visual.
  * `styleUrl`: Ruta a la hoja de estilos SCSS.
  * `imports`: Lista de dependencias directas que usa el template (iconos, directivas, otros componentes).

---

### 7.2 Comandos de Angular CLI para Generar Código (`ng generate`)

| Elemento | Comando Completo | Alias Recomendado |
| :--- | :--- | :--- |
| **Componente** | `ng generate component shared/components/navbar` | `ng g c shared/components/navbar` |
| **Interface** | `ng generate interface core/models/student` | `ng g i core/models/student` |
| **Servicio** | `ng generate service core/services/student` | `ng g s core/services/student` |

#### Banderas (Flags) Esenciales del Generador de Componentes:
* `--skip-tests`: Omite el archivo `*.spec.ts` para acelerar la maquetación inicial.
* `--type=component`: Garantiza la generación de archivos con el sufijo canónico (`*.component.ts`, `*.component.html`, `*.component.scss`).
* `--style=scss`: Garantiza el uso del preprocesador Sass/SCSS.

---

### 7.3 Standalone por Defecto en Angular 22
En Angular 22 todo componente es **Standalone por defecto**; ya no es necesario escribir `standalone: true` ni utilizar archivos `NgModule`.

---

### 7.4 El Sistema de Enrutamiento: `RouterOutlet`, `RouterLink` y `RouterLinkActive`
* **`<router-outlet />`:** Marco dinámico donde se proyecta la página activa según la URL.
* **`routerLink="/ruta"`:** Navegación instantánea mediante History API sin recargar la página.
* **`routerLinkActive="active"`:** Añade la clase CSS especificada al enlace cuando coincide con la URL activa.

---

### 7.5 Control Flow Moderno (`@for`) y Prevención de DOM Thrashing
```html
@for (item of navItems; track item.path) {
  <a [routerLink]="item.path">{{ item.label }}</a>
}
```
* **`track item.path`:** Cláusula obligatoria que permite al motor de Angular reutilizar los nodos del DOM existentes y evitar destruirlos y recrearlos (*DOM Thrashing*), garantizando 60 fps constantes.

---

### 7.6 Primer Contacto con la Reactividad Moderna: Signals e Inputs/Outputs
* **`signal()`:** Estado reactivo local; se lee en el HTML con paréntesis (ej. `isCollapsed()`).
* **`input()`:** Recibe datos reactivos desde un componente padre.
* **`output()`:** Emite eventos funcionales hacia el componente padre.

---

### 7.7 Documentación Oficial y Referencias de Consulta
* [Guía Oficial de Componentes en Angular 22](https://angular.dev/guide/components)
* [Control Flow (@if, @for, @switch)](https://angular.dev/guide/templates/control-flow)
* [Guía de Enrutamiento (Router)](https://angular.dev/guide/routing)

---

## 🖥️ PARTE 8: Montaje del App Shell (Layout del SaaS)

Construiremos el armazón visual de nuestra aplicación utilizando una arquitectura desacoplada de 3 archivos por componente (`.ts`, `.html`, `.scss`) con metodología BEM + Sass.

### 8.1 El Encabezado Institucional (`NavbarComponent`)
Genera el componente:
```bash
ng g c shared/components/navbar --skip-tests --type=component
```

#### `src/app/shared/components/navbar/navbar.component.ts`
```typescript
// src/app/shared/components/navbar/navbar.component.ts
import { Component, output } from '@angular/core';
import { MatIconModule } from '@angular/material/icon';
import { MatButtonModule } from '@angular/material/button';

@Component({
  selector: 'app-navbar',
  imports: [MatIconModule, MatButtonModule],
  templateUrl: './navbar.component.html',
  styleUrl: './navbar.component.scss'
})
export class NavbarComponent {
  // Emisor de evento funcional hacia el padre para alternar el menú lateral:
  public readonly toggleSidebar = output<void>();

  public onToggleClick(): void {
    this.toggleSidebar.emit();
  }
}
```

#### `src/app/shared/components/navbar/navbar.component.html`
```html
<!-- src/app/shared/components/navbar/navbar.component.html -->
<header class="app-navbar">
  <div class="app-navbar__left">
    <button
      mat-icon-button
      type="button"
      class="app-navbar__toggle-btn"
      aria-label="Alternar menú lateral"
      (click)="onToggleClick()"
    >
      <mat-icon>menu</mat-icon>
    </button>
    
    <div class="app-navbar__brand">
      <div class="app-navbar__logo-icon">
        <mat-icon>school</mat-icon>
      </div>
      <div class="app-navbar__brand-text">
        <h1 class="app-navbar__title">EduPortal</h1>
        <span class="app-navbar__subtitle">Gestión Escolar SaaS</span>
      </div>
    </div>
  </div>

  <div class="app-navbar__right">
    <div class="app-navbar__badge badge badge--success">
      Año Lectivo 2026
    </div>

    <button mat-icon-button type="button" aria-label="Notificaciones del sistema" class="app-navbar__icon-btn">
      <mat-icon>notifications</mat-icon>
    </button>

    <div class="app-navbar__user">
      <div class="app-navbar__avatar">
        <span>FJ</span>
      </div>
      <div class="app-navbar__user-info">
        <span class="app-navbar__user-name">Prof. Félix Chacaliaza</span>
        <span class="app-navbar__user-role">Administrador</span>
      </div>
    </div>
  </div>
</header>
```

#### `src/app/shared/components/navbar/navbar.component.scss`
```scss
/* src/app/shared/components/navbar/navbar.component.scss */
.app-navbar {
  display: flex;
  align-items: center;
  justify-content: space-between;
  height: 64px;
  padding: 0 1.5rem;
  background-color: var(--bg-surface);
  border-bottom: 1px solid var(--border-color);
  position: sticky;
  top: 0;
  z-index: 100;
  box-shadow: var(--shadow-sm);

  &__left {
    display: flex;
    align-items: center;
    gap: 1rem;
  }

  &__toggle-btn {
    color: var(--text-muted);
    transition: color 0.2s ease, background-color 0.2s ease;

    &:hover {
      color: var(--primary);
      background-color: var(--primary-light);
    }
  }

  &__brand {
    display: flex;
    align-items: center;
    gap: 0.75rem;
    text-decoration: none;
    user-select: none;
  }

  &__logo-icon {
    display: flex;
    align-items: center;
    justify-content: center;
    width: 36px;
    height: 36px;
    background: linear-gradient(135deg, var(--primary) 0%, var(--primary-hover) 100%);
    color: #ffffff;
    border-radius: var(--radius-sm);
    box-shadow: 0 2px 4px rgba(228, 0, 53, 0.25);

    mat-icon {
      font-size: 20px;
      width: 20px;
      height: 20px;
    }
  }

  &__brand-text {
    display: flex;
    flex-direction: column;
  }

  &__title {
    font-family: var(--font-heading);
    font-size: 1.15rem;
    font-weight: 700;
    line-height: 1.2;
    color: var(--secondary);
    margin: 0;
    letter-spacing: -0.01em;
  }

  &__subtitle {
    font-size: 0.7rem;
    font-weight: 500;
    color: var(--text-muted);
    text-transform: uppercase;
    letter-spacing: 0.05em;
  }

  &__right {
    display: flex;
    align-items: center;
    gap: 1.25rem;
  }

  &__badge {
    display: none;

    @media (min-width: 768px) {
      display: inline-flex;
    }
  }

  &__icon-btn {
    color: var(--text-muted);
    position: relative;

    &:hover {
      color: var(--text-main);
      background-color: var(--bg-page);
    }
  }

  &__user {
    display: flex;
    align-items: center;
    gap: 0.75rem;
    padding-left: 0.5rem;
    border-left: 1px solid var(--border-color);
  }

  &__avatar {
    width: 38px;
    height: 38px;
    border-radius: var(--radius-full);
    background-color: var(--secondary-light);
    color: #ffffff;
    display: flex;
    align-items: center;
    justify-content: center;
    font-weight: 600;
    font-size: 0.85rem;
    box-shadow: var(--shadow-sm);
  }

  &__user-info {
    display: none;
    flex-direction: column;

    @media (min-width: 640px) {
      display: flex;
    }
  }

  &__user-name {
    font-size: 0.85rem;
    font-weight: 600;
    color: var(--text-main);
    line-height: 1.2;
  }

  &__user-role {
    font-size: 0.72rem;
    color: var(--text-muted);
  }
}
```

---

### 8.2 El Menú Lateral Interactivo (`SidebarComponent`)
Genera el componente:
```bash
ng g c shared/components/sidebar --skip-tests --type=component
```

#### `src/app/shared/components/sidebar/sidebar.component.ts`
```typescript
// src/app/shared/components/sidebar/sidebar.component.ts
import { Component, input } from '@angular/core';
import { RouterLink, RouterLinkActive } from '@angular/router';
import { MatIconModule } from '@angular/material/icon';

interface NavigationItem {
  readonly path: string;
  readonly label: string;
  readonly icon: string;
  readonly badge?: string;
}

@Component({
  selector: 'app-sidebar',
  imports: [RouterLink, RouterLinkActive, MatIconModule],
  templateUrl: './sidebar.component.html',
  styleUrl: './sidebar.component.scss'
})
export class SidebarComponent {
  // Signal input reactivo recibido desde el componente padre:
  public readonly isCollapsed = input<boolean>(false);

  // Lista tipada de enlaces de navegación escolar:
  public readonly navItems: readonly NavigationItem[] = [
    { path: '/dashboard', label: 'Panel de Control', icon: 'dashboard' },
    { path: '/alumnos', label: 'Padrón de Alumnos', icon: 'people', badge: '100' },
    { path: '/notas', label: 'Calificaciones', icon: 'grade' }
  ];
}
```

#### `src/app/shared/components/sidebar/sidebar.component.html`
```html
<!-- src/app/shared/components/sidebar/sidebar.component.html -->
<aside class="app-sidebar" [class.app-sidebar--collapsed]="isCollapsed()">
  <nav class="app-sidebar__nav">
    <div class="app-sidebar__section-label">
      @if (!isCollapsed()) {
        <span>MENÚ PRINCIPAL</span>
      } @else {
        <span>•••</span>
      }
    </div>

    <ul class="app-sidebar__menu">
      @for (item of navItems; track item.path) {
        <li class="app-sidebar__item">
          <a
            [routerLink]="item.path"
            routerLinkActive="app-sidebar__link--active"
            class="app-sidebar__link"
            [title]="isCollapsed() ? item.label : ''"
          >
            <mat-icon class="app-sidebar__icon">{{ item.icon }}</mat-icon>
            
            @if (!isCollapsed()) {
              <span class="app-sidebar__label">{{ item.label }}</span>
              @if (item.badge) {
                <span class="app-sidebar__badge badge badge--info">{{ item.badge }}</span>
              }
            }
          </a>
        </li>
      }
    </ul>
  </nav>

  <div class="app-sidebar__footer">
    <div class="app-sidebar__version">
      @if (!isCollapsed()) {
        <span>EduPortal v22.0.0</span>
        <span class="app-sidebar__status-dot"></span>
      } @else {
        <span class="app-sidebar__status-dot" title="EduPortal v22.0.0 Online"></span>
      }
    </div>
  </div>
</aside>
```

#### `src/app/shared/components/sidebar/sidebar.component.scss`
```scss
/* src/app/shared/components/sidebar/sidebar.component.scss */
.app-sidebar {
  width: 250px;
  height: calc(100vh - 64px);
  background-color: var(--secondary);
  color: #94a3b8;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  transition: width 0.25s cubic-bezier(0.4, 0, 0.2, 1);
  overflow-x: hidden;
  box-shadow: var(--shadow-md);

  &--collapsed {
    width: 72px;

    .app-sidebar__section-label {
      text-align: center;
      padding: 1rem 0 0.5rem;
    }

    .app-sidebar__link {
      justify-content: center;
      padding: 0.75rem 0;
    }

    .app-sidebar__icon {
      margin-right: 0;
    }
  }

  &__nav {
    padding: 1rem 0;
  }

  &__section-label {
    padding: 0.75rem 1.25rem 0.5rem;
    font-size: 0.65rem;
    font-weight: 700;
    letter-spacing: 0.08em;
    color: #64748b;
    text-transform: uppercase;
    user-select: none;
  }

  &__menu {
    list-style: none;
    margin: 0;
    padding: 0 0.5rem;
    display: flex;
    flex-direction: column;
    gap: 0.25rem;
  }

  &__item {
    margin: 0;
  }

  &__link {
    display: flex;
    align-items: center;
    padding: 0.75rem 1rem;
    color: #94a3b8;
    text-decoration: none;
    border-radius: var(--radius-sm);
    font-size: 0.88rem;
    font-weight: 500;
    transition: background-color 0.15s ease, color 0.15s ease;
    white-space: nowrap;

    &:hover {
      background-color: rgba(255, 255, 255, 0.06);
      color: #ffffff;

      .app-sidebar__icon {
        color: var(--accent);
      }
    }

    &--active {
      background-color: var(--primary) !important;
      color: #ffffff !important;
      font-weight: 600;
      box-shadow: 0 2px 6px rgba(228, 0, 53, 0.35);

      .app-sidebar__icon {
        color: #ffffff !important;
      }

      .app-sidebar__badge {
        background-color: rgba(255, 255, 255, 0.2);
        color: #ffffff;
      }
    }
  }

  &__icon {
    font-size: 20px;
    width: 20px;
    height: 20px;
    margin-right: 0.85rem;
    transition: color 0.2s ease;
    flex-shrink: 0;
  }

  &__label {
    flex-grow: 1;
    overflow: hidden;
    text-overflow: ellipsis;
  }

  &__badge {
    margin-left: 0.5rem;
    padding: 0.15rem 0.5rem;
    font-size: 0.7rem;
  }

  &__footer {
    padding: 1rem;
    border-top: 1px solid rgba(255, 255, 255, 0.08);
  }

  &__version {
    display: flex;
    align-items: center;
    justify-content: space-between;
    font-size: 0.72rem;
    color: #64748b;
  }

  &__status-dot {
    width: 8px;
    height: 8px;
    background-color: var(--success);
    border-radius: var(--radius-full);
    box-shadow: 0 0 6px rgba(16, 185, 129, 0.6);
  }
}
```

---

### 8.3 El Layout Maestro (`MainLayoutComponent`)
Genera el componente contenedor:
```bash
ng g c core/layouts/main-layout --skip-tests --type=component
```

#### `src/app/core/layouts/main-layout/main-layout.component.ts`
```typescript
// src/app/core/layouts/main-layout/main-layout.component.ts
import { Component, signal } from '@angular/core';
import { RouterOutlet } from '@angular/router';
import { NavbarComponent } from '../../../shared/components/navbar/navbar.component';
import { SidebarComponent } from '../../../shared/components/sidebar/sidebar.component';

@Component({
  selector: 'app-main-layout',
  imports: [RouterOutlet, NavbarComponent, SidebarComponent],
  templateUrl: './main-layout.component.html',
  styleUrl: './main-layout.component.scss'
})
export class MainLayoutComponent {
  // Estado reactivo local que gestiona si el menú lateral está colapsado:
  public readonly isSidebarCollapsed = signal<boolean>(false);

  // Método que invierte el estado de la señal:
  public toggleSidebar(): void {
    this.isSidebarCollapsed.update(prev => !prev);
  }
}
```

#### `src/app/core/layouts/main-layout/main-layout.component.html`
```html
<!-- src/app/core/layouts/main-layout/main-layout.component.html -->
<div class="main-layout">
  <!-- Navbar superior con suscripción al evento toggleSidebar: -->
  <app-navbar (toggleSidebar)="toggleSidebar()"></app-navbar>

  <div class="main-layout__body">
    <!-- Sidebar con binding de lectura a la señal isSidebarCollapsed(): -->
    <app-sidebar [isCollapsed]="isSidebarCollapsed()"></app-sidebar>

    <!-- Área de contenido dinámico donde se inyectan las páginas del SaaS: -->
    <main class="main-layout__content">
      <div class="main-layout__container">
        <router-outlet />
      </div>
    </main>
  </div>
</div>
```

#### `src/app/core/layouts/main-layout/main-layout.component.scss`
```scss
/* src/app/core/layouts/main-layout/main-layout.component.scss */
.main-layout {
  display: flex;
  flex-direction: column;
  min-height: 100vh;
  background-color: var(--bg-page);

  &__body {
    display: flex;
    flex: 1;
    overflow: hidden;
  }

  &__content {
    flex: 1;
    overflow-y: auto;
    height: calc(100vh - 64px);
    background-color: var(--bg-page);
  }

  &__container {
    max-width: 1400px;
    margin: 0 auto;
    padding: 1.5rem;

    @media (min-width: 1024px) {
      padding: 2rem;
    }
  }
}
```

---

## 📄 PARTE 9: Páginas del SaaS y Enrutamiento con Carga Diferida

Construiremos las 3 páginas iniciales del sistema educativo y configuraremos las rutas hijas con Carga Diferida (*Lazy Loading*).

### 9.1 Página de Dashboard (`DashboardPageComponent`)
Genera la página:
```bash
ng g c features/dashboard/pages/dashboard-page --skip-tests --type=component
```

#### `src/app/features/dashboard/pages/dashboard-page.component.ts`
```typescript
// src/app/features/dashboard/pages/dashboard-page.component.ts
import { Component } from '@angular/core';
import { MatIconModule } from '@angular/material/icon';
import { RouterLink } from '@angular/router';

@Component({
  selector: 'app-dashboard-page',
  imports: [MatIconModule, RouterLink],
  templateUrl: './dashboard-page.component.html',
  styleUrl: './dashboard-page.component.scss'
})
export class DashboardPageComponent {}
```

#### `src/app/features/dashboard/pages/dashboard-page.component.html`
```html
<!-- src/app/features/dashboard/pages/dashboard-page.component.html -->
<div class="dashboard-page">
  <div class="dashboard-page__header">
    <h2 class="dashboard-page__title">Panel General del Colegio</h2>
    <p class="dashboard-page__subtitle">Resumen ejecutivo y métricas del año lectivo 2026</p>
  </div>

  <div class="dashboard-page__grid">
    <div class="kpi-card">
      <div class="kpi-card__icon kpi-card__icon--primary">
        <mat-icon>people</mat-icon>
      </div>
      <div class="kpi-card__info">
        <span class="kpi-card__label">Alumnos Matriculados</span>
        <span class="kpi-card__value">100</span>
      </div>
    </div>

    <div class="kpi-card">
      <div class="kpi-card__icon kpi-card__icon--accent">
        <mat-icon>badge</mat-icon>
      </div>
      <div class="kpi-card__info">
        <span class="kpi-card__label">Plana Docente</span>
        <span class="kpi-card__value">10</span>
      </div>
    </div>

    <div class="kpi-card">
      <div class="kpi-card__icon kpi-card__icon--success">
        <mat-icon>meeting_room</mat-icon>
      </div>
      <div class="kpi-card__info">
        <span class="kpi-card__label">Aulas Activas</span>
        <span class="kpi-card__value">10</span>
      </div>
    </div>

    <div class="kpi-card">
      <div class="kpi-card__icon kpi-card__icon--warning">
        <mat-icon>trending_up</mat-icon>
      </div>
      <div class="kpi-card__info">
        <span class="kpi-card__label">Promedio General</span>
        <span class="kpi-card__value">15.4</span>
      </div>
    </div>
  </div>

  <div class="dashboard-page__banner">
    <div class="dashboard-page__banner-content">
      <h3 class="dashboard-page__banner-title">¡Bienvenido a EduPortal SaaS!</h3>
      <p class="dashboard-page__banner-text">
        El App Shell institucional está montado. El backend en Express y MongoDB está conectado y listo para alimentar la vista de alumnos en la Sesión 02.
      </p>
      <a routerLink="/alumnos" class="dashboard-page__banner-btn">
        Ver Padrón de Alumnos
        <mat-icon>arrow_forward</mat-icon>
      </a>
    </div>
  </div>
</div>
```

#### `src/app/features/dashboard/pages/dashboard-page.component.scss`
```scss
/* src/app/features/dashboard/pages/dashboard-page.component.scss */
.dashboard-page {
  display: flex;
  flex-direction: column;
  gap: 2rem;

  &__header {
    display: flex;
    flex-direction: column;
    gap: 0.25rem;
  }

  &__title {
    font-family: var(--font-heading);
    font-size: 1.5rem;
    font-weight: 700;
    color: var(--secondary);
    margin: 0;
  }

  &__subtitle {
    font-size: 0.9rem;
    color: var(--text-muted);
    margin: 0;
  }

  &__grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(240px, 1fr));
    gap: 1.25rem;
  }

  &__banner {
    background: linear-gradient(135deg, var(--secondary) 0%, var(--secondary-light) 100%);
    border-radius: var(--radius-lg);
    padding: 2rem;
    color: #ffffff;
    box-shadow: var(--shadow-md);
  }

  &__banner-content {
    max-width: 650px;
    display: flex;
    flex-direction: column;
    gap: 0.75rem;
  }

  &__banner-title {
    font-family: var(--font-heading);
    font-size: 1.35rem;
    font-weight: 700;
    margin: 0;
  }

  &__banner-text {
    font-size: 0.92rem;
    line-height: 1.6;
    color: #cbd5e1;
    margin: 0;
  }

  &__banner-btn {
    display: inline-flex;
    align-items: center;
    gap: 0.5rem;
    background-color: var(--primary);
    color: #ffffff;
    text-decoration: none;
    font-weight: 600;
    font-size: 0.88rem;
    padding: 0.65rem 1.25rem;
    border-radius: var(--radius-sm);
    width: fit-content;
    margin-top: 0.5rem;
    transition: background-color 0.2s ease, transform 0.15s ease;

    &:hover {
      background-color: var(--primary-hover);
      transform: translateX(4px);
    }

    mat-icon {
      font-size: 18px;
      width: 18px;
      height: 18px;
    }
  }
}

.kpi-card {
  display: flex;
  align-items: center;
  gap: 1.25rem;
  padding: 1.25rem;
  background-color: var(--bg-surface);
  border: 1px solid var(--border-color);
  border-radius: var(--radius-md);
  box-shadow: var(--shadow-sm);
  transition: transform 0.2s ease, box-shadow 0.2s ease;

  &:hover {
    transform: translateY(-2px);
    box-shadow: var(--shadow-md);
  }

  &__icon {
    display: flex;
    align-items: center;
    justify-content: center;
    width: 48px;
    height: 48px;
    border-radius: var(--radius-md);

    mat-icon {
      font-size: 24px;
      width: 24px;
      height: 24px;
    }

    &--primary {
      background-color: var(--primary-light);
      color: var(--primary);
    }

    &--accent {
      background-color: #e0f2fe;
      color: #0284c7;
    }

    &--success {
      background-color: var(--success-bg);
      color: var(--success);
    }

    &--warning {
      background-color: var(--warning-bg);
      color: var(--warning);
    }
  }

  &__info {
    display: flex;
    flex-direction: column;
    gap: 0.15rem;
  }

  &__label {
    font-size: 0.8rem;
    font-weight: 500;
    color: var(--text-muted);
  }

  &__value {
    font-family: var(--font-heading);
    font-size: 1.4rem;
    font-weight: 700;
    color: var(--text-main);
  }
}
```

---

### 9.2 Página de Alumnos (`StudentsPageComponent`)
Genera la página:
```bash
ng g c features/students/pages/students-page --skip-tests --type=component
```

#### `src/app/features/students/pages/students-page.component.ts`
```typescript
// src/app/features/students/pages/students-page.component.ts
import { Component } from '@angular/core';
import { MatIconModule } from '@angular/material/icon';

@Component({
  selector: 'app-students-page',
  imports: [MatIconModule],
  templateUrl: './students-page.component.html',
  styleUrl: './students-page.component.scss'
})
export class StudentsPageComponent {}
```

#### `src/app/features/students/pages/students-page.component.html`
```html
<!-- src/app/features/students/pages/students-page.component.html -->
<div class="students-page">
  <div class="students-page__header">
    <div>
      <h2 class="students-page__title">Padrón Oficial de Alumnos</h2>
      <p class="students-page__subtitle">Colegio EduPortal • 100 Alumnos Matriculados en Base de Datos</p>
    </div>
    <div class="badge badge--success">10 Aulas Activas</div>
  </div>

  <div class="students-page__empty-state">
    <div class="students-page__empty-icon">
      <mat-icon>school</mat-icon>
    </div>
    <h3 class="students-page__empty-title">Vista Preparada para la Sesión 02</h3>
    <p class="students-page__empty-text">
      En la siguiente sesión crearemos el componente `StudentCardComponent` con Signals, Directivas de Estado y Pipes de notas para renderizar a los 100 estudiantes desde MongoDB.
    </p>
  </div>
</div>
```

#### `src/app/features/students/pages/students-page.component.scss`
```scss
/* src/app/features/students/pages/students-page.component.scss */
.students-page {
  display: flex;
  flex-direction: column;
  gap: 2rem;

  &__header {
    display: flex;
    align-items: center;
    justify-content: space-between;
    flex-wrap: wrap;
    gap: 1rem;
  }

  &__title {
    font-family: var(--font-heading);
    font-size: 1.5rem;
    font-weight: 700;
    color: var(--secondary);
    margin: 0;
  }

  &__subtitle {
    font-size: 0.9rem;
    color: var(--text-muted);
    margin: 0;
  }

  &__empty-state {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    text-align: center;
    padding: 4rem 2rem;
    background-color: var(--bg-surface);
    border: 1px dashed var(--border-color);
    border-radius: var(--radius-lg);
  }

  &__empty-icon {
    display: flex;
    align-items: center;
    justify-content: center;
    width: 64px;
    height: 64px;
    border-radius: var(--radius-full);
    background-color: var(--primary-light);
    color: var(--primary);
    margin-bottom: 1rem;

    mat-icon {
      font-size: 32px;
      width: 32px;
      height: 32px;
    }
  }

  &__empty-title {
    font-family: var(--font-heading);
    font-size: 1.15rem;
    font-weight: 600;
    color: var(--secondary);
    margin: 0 0 0.5rem;
  }

  &__empty-text {
    max-width: 500px;
    font-size: 0.88rem;
    color: var(--text-muted);
    line-height: 1.5;
    margin: 0;
  }
}
```

---

### 9.3 Página de Calificaciones (`GradesPageComponent`)
Genera la página:
```bash
ng g c features/grades/pages/grades-page --skip-tests --type=component
```

#### `src/app/features/grades/pages/grades-page.component.ts`
```typescript
// src/app/features/grades/pages/grades-page.component.ts
import { Component } from '@angular/core';
import { MatIconModule } from '@angular/material/icon';

@Component({
  selector: 'app-grades-page',
  imports: [MatIconModule],
  templateUrl: './grades-page.component.html',
  styleUrl: './grades-page.component.scss'
})
export class GradesPageComponent {}
```

#### `src/app/features/grades/pages/grades-page.component.html`
```html
<!-- src/app/features/grades/pages/grades-page.component.html -->
<div class="grades-page">
  <div class="grades-page__header">
    <div>
      <h2 class="grades-page__title">Matriz de Calificaciones Bimestrales</h2>
      <p class="grades-page__subtitle">Registro de notas vigesimales por asignatura y bimestre (MINEDU)</p>
    </div>
    <div class="badge badge--info">Bimestre 1 Activo</div>
  </div>

  <div class="grades-page__empty-state">
    <div class="grades-page__empty-icon">
      <mat-icon>analytics</mat-icon>
    </div>
    <h3 class="grades-page__empty-title">Módulo de Evaluación Escolar</h3>
    <p class="grades-page__empty-text">
      En la Sesión 06 y 07 construiremos la matriz interactiva con Formularios Reactivos tipados y persistencia en lote hacia la API REST.
    </p>
  </div>
</div>
```

#### `src/app/features/grades/pages/grades-page.component.scss`
```scss
/* src/app/features/grades/pages/grades-page.component.scss */
.grades-page {
  display: flex;
  flex-direction: column;
  gap: 2rem;

  &__header {
    display: flex;
    align-items: center;
    justify-content: space-between;
    flex-wrap: wrap;
    gap: 1rem;
  }

  &__title {
    font-family: var(--font-heading);
    font-size: 1.5rem;
    font-weight: 700;
    color: var(--secondary);
    margin: 0;
  }

  &__subtitle {
    font-size: 0.9rem;
    color: var(--text-muted);
    margin: 0;
  }

  &__empty-state {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    text-align: center;
    padding: 4rem 2rem;
    background-color: var(--bg-surface);
    border: 1px dashed var(--border-color);
    border-radius: var(--radius-lg);
  }

  &__empty-icon {
    display: flex;
    align-items: center;
    justify-content: center;
    width: 64px;
    height: 64px;
    border-radius: var(--radius-full);
    background-color: #eff6ff;
    color: #3b82f6;
    margin-bottom: 1rem;

    mat-icon {
      font-size: 32px;
      width: 32px;
      height: 32px;
    }
  }

  &__empty-title {
    font-family: var(--font-heading);
    font-size: 1.15rem;
    font-weight: 600;
    color: var(--secondary);
    margin: 0 0 0.5rem;
  }

  &__empty-text {
    max-width: 500px;
    font-size: 0.88rem;
    color: var(--text-muted);
    line-height: 1.5;
    margin: 0;
  }
}
```

---

### 9.4 Configurar Rutas Hijas y Lazy Loading en `src/app/app.routes.ts`
Abre `src/app/app.routes.ts` y define el enrutamiento con hijos bajo el layout principal:

```typescript
// src/app/app.routes.ts
import { Routes } from '@angular/router';

export const routes: Routes = [
  {
    path: '',
    loadComponent: () =>
      import('./core/layouts/main-layout/main-layout.component').then(m => m.MainLayoutComponent),
    children: [
      {
        path: '',
        redirectTo: 'dashboard',
        pathMatch: 'full'
      },
      {
        path: 'dashboard',
        loadComponent: () =>
          import('./features/dashboard/pages/dashboard-page.component').then(
            m => m.DashboardPageComponent
          )
      },
      {
        path: 'alumnos',
        loadComponent: () =>
          import('./features/students/pages/students-page.component').then(
            m => m.StudentsPageComponent
          )
      },
      {
        path: 'notas',
        loadComponent: () =>
          import('./features/grades/pages/grades-page.component').then(
            m => m.GradesPageComponent
          )
      }
    ]
  },
  {
    path: '**',
    redirectTo: 'dashboard'
  }
];
```

---

### 9.5 Limpiar el Componente Raíz `src/app/app.component.ts`
Reemplaza el archivo `src/app/app.component.ts` para que contenga únicamente el enrutador maestro:

```typescript
// src/app/app.component.ts
import { Component } from '@angular/core';
import { RouterOutlet } from '@angular/router';

@Component({
  selector: 'app-root',
  imports: [RouterOutlet],
  template: '<router-outlet />'
})
export class AppComponent {}
```

> *Nota:* Si el CLI generó `src/app/app.component.html` y `src/app/app.component.scss`, puedes eliminarlos o dejarlos vacíos, ya que `template: '<router-outlet />'` asume el control directo.

---

## 🚀 PARTE 10: Verificación y Comprobación en Vivo

Pondremos en marcha el servidor de desarrollo de Angular para validar que el App Shell y el enrutamiento funcionen sin errores en el navegador.

### 10.1 Puesta en Marcha con `ng serve` y Comprobación en Navegador
En la terminal de `eduportal-web`, ejecuta:

```bash
ng serve
```

Abre [http://localhost:4200](http://localhost:4200) en Google Chrome o Microsoft Edge.

*Lista de comprobación visual e interactiva:*
1. **Redirección inicial:** Al entrar a `http://localhost:4200/` debe redirigir inmediatamente a `/dashboard`.
2. **Encabezado (Navbar):** Muestra el logotipo de EduPortal, la insignia "Año Lectivo 2026", el icono de notificaciones y el avatar del usuario.
3. **Colapso del Menú Lateral:** Al hacer clic en el botón de hamburguesa (`menu`) en el Navbar, el Sidebar debe alternar suavemente su ancho entre 250px y 72px mediante transiciones CSS.
4. **Navegación sin recarga:** Haz clic en los enlaces *Panel de Control*, *Padrón de Alumnos* y *Calificaciones*. La URL debe cambiar instantáneamente, el enlace activo debe pintarse de rojo institucional (`--primary`) y la vista interna debe actualizarse sin parpadeo blanco ni recarga de página (`F5`).
5. **Consola limpia:** Presiona `F12` y verifica en la pestaña *Console* que no existan errores de TypeScript ni advertencias de Angular.

---

## 🧠 PARTE 11: Checklist Maestro de Conceptos Dominados en la Sesión 01

### 11.1 Checklist Oficial de Logros de la Sesión 01

#### 🏛️ Arquitectura & Entorno
- [x] Node.js 20/22 LTS, Git y Angular CLI v22 verificados e instalados.
- [x] Backend en Express levantado en puerto 3000 con seed oficial de MongoDB ejecutado exitosamente.
- [x] Proyecto creado con arquitectura Zoneless (`provideExperimentalZonelessChangeDetection`) sin `zone.js`.
- [x] Estructura de carpetas modular de 4 capas establecida (`core/`, `shared/`, `features/`).

#### 🧬 TypeScript Estricto & POO
- [x] Clases (`class`), estado, comportamiento y modelo de memoria en el Heap comprendidos.
- [x] Operador `new` y su proceso de instanciación en 5 pasos entendido a fondo.
- [x] Parameter Properties dominadas para eliminar boilerplate en constructores.
- [x] Modificadores de acceso (`public`, `protected`, `private`) y contraste con campos privados nativos (`#`).
- [x] Inmutabilidad estricta con directiva `readonly`, reglas de asignación y su rol crítico en Zoneless Signals.
- [x] Clases abstractas (`abstract class`) y patrón Template Method aplicados.
- [x] Herencia con `extends`, llamada forzosa a `super()` antes de `this` y búsqueda en la cadena de prototipos.
- [x] Distinción técnica entre `interface` (0 bytes en runtime) y `class` (molde en memoria con `new`).
- [x] Implementación estricta de contratos con `implements`.
- [x] Tipado Estructural (*Duck Typing*) asimilado frente a tipado nominal.
- [x] Tipos de Unión Literales implementados para erradicar el antipatrón *Stringly Typed*.
- [x] Tipos Genéricos (`<T>`) utilizados en `ApiResponse<T>` sin recurrir a `any`.
- [x] Modelos inmutables del dominio escolar creados en `core/models/` con Barrel Export (`index.ts`).

#### 🎨 Diseño & Arquitectura CSS (BEM + Sass)
- [x] Angular Material instalado y tipografías oficiales de Google (Inter, Poppins) e iconos configurados.
- [x] Variables CSS institucionales (`:root`) creadas para colores, sombras y radios.
- [x] Metodología BEM (Bloque, Elemento, Modificador) combinada con el selector padre Sass (`&`).
- [x] Prevención de la "Guerra de Especificidad" manteniendo selectores planos (`0, 0, 1, 0`).

#### 🧱 Componentes Standalone & Arquitectura de Archivos
- [x] Comprensión de los 4 archivos de ingeniería por componente (`.ts`, `.html`, `.scss`, `.spec.ts`).
- [x] Entendimiento de `ViewEncapsulation.Emulated` mediante atributos únicos `_ngcontent-ng-...`.
- [x] Comandos de generación de código del CLI con alias corto (`ng g c`, `ng g i`).
- [x] Asimilación de componentes Standalone nativos sin `standalone: true` ni `NgModule`.

#### 🔄 Data Binding & Control Flow Moderno
- [x] Control Flow nativo `@for` con cláusula obligatoria `track` para prevenir *DOM Thrashing*.
- [x] Renderizado condicional moderno con bloques `@if` y `@else`.
- [x] Property Binding unidireccional con corchetes `[propiedad]="valor"`.
- [x] Event Binding con paréntesis `(evento)="metodo()"`.

#### 🚦 Enrutamiento Profesional (Routing)
- [x] Enrutamiento SPA sin recarga mediante `<router-outlet />` y History API del navegador.
- [x] Navegación interna mediante directiva `routerLink` y detección de ruta activa con `routerLinkActive`.
- [x] Rutas hijas estructuradas bajo el layout maestro con Carga Diferida (*Lazy Loading* con `loadComponent`).
- [x] Redirección por defecto a `/dashboard` y comodín `**`.

#### ⚡ Reactividad Moderna Inicial
- [x] Señal primitiva `signal()` utilizada para el estado de colapso del menú lateral.
- [x] Emisión de eventos funcional con `output()` en `NavbarComponent`.
- [x] Signal Input con `input<boolean>()` en `SidebarComponent`.
