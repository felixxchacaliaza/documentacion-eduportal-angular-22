# 💡 Guía del Estudiante • Sesión 06.1.1 (Senior Boost)
## Cápsula Técnica: Directivas Semánticas de Calificaciones (`ScoreColorDirective`) y Pipe Puro de Estado Académico (`GradeStatusPipe`)

> **Curso:** Desarrollo Web Frontend Profesional con Angular 22  
> **Proyecto Transversal:** EduPortal — Sistema de Gestión Escolar SaaS  
> **Tipo de Sesión:** `boost` (Cápsula Técnica Avanzada - 20 min)  
> **ID Oficial:** `sesion-06.1.1`  
> **Rama de Trabajo:** `feature/sesion-06.1.1`  
> **Tag Oficial:** `sesion-06.1.1`

---

## ⚡ Sincronización y Punto de Partida con Git

Antes de iniciar el trabajo práctico de hoy, identifica tu situación actual para ejecutar los comandos adecuados en tu terminal:

```bash
# 0. PASO PREVIO OBLIGATORIO: Descargar todos los tags oficiales más recientes desde GitHub
git fetch origin --tags
```

---

### 🟢 Situación 1: No completaste la sesión anterior o tu código tiene errores (Rescate Rápido)
> 💡 **Recomendado si te atrasaste o tienes fallos difíciles de solucionar:**  
> Este comando descarta cambios pendientes no guardados y crea una rama limpia para la sesión de hoy partiendo con el 100% de la funcionalidad oficial de la sesión anterior entregada por el docente.

```bash
# Crear tu rama de trabajo directamente sobre el TAG oficial de la sesión anterior:
git checkout -f tags/sesion-06.1.0 -b feature/sesion-06.1.1

# Instalar dependencias por si hubo nuevos paquetes agregados:
npm install
```

---

### 🟡 Situación 2: Completaste la sesión anterior "a tu modo" (en tu propia rama)
Si desarrollaste la sesión anterior con tu propio código y deseas continuar, elige una de estas dos alternativas:

#### 🔹 Alternativa 2.A: Continuar trabajando sobre tu código personal
> Usa esta opción si tu código funciona correctamente y quieres construir la nueva sesión sobre tu propia implementación previa.

```bash
# 1. Asegúrate de guardar y confirmar tus cambios en tu rama actual:
git add .
git commit -m "feat: culminacion de la sesion anterior con logica propia"

# 2. Crear la nueva rama para la sesión de hoy partiendo de tu código personal:
git checkout -b feature/sesion-06.1.1
```

#### 🔹 Alternativa 2.B: Respaldar tu código pero continuar con la solución oficial del docente
> Usa esta opción si deseas conservar tu código para revisión futura, pero prefieres trabajar hoy sobre la base oficial estándar para evitar discrepancias.

```bash
# 1. Crear una rama de respaldo personal para NO perder tu avance:
git checkout -b mi-respaldo-sesion-anterior

# 2. Crear la rama de hoy partiendo 100% limpio del TAG oficial del docente:
git checkout tags/sesion-06.1.0 -b feature/sesion-06.1.1
```

---

### 🔵 Situación 3: Tu rama de trabajo está en la sesión anterior y deseas jalar el nuevo tag oficial
*(Ejemplo común en clase: Tu rama está alineada al tag/commit anterior como `sesion-06.1.0` y deseas actualizarla para recibir los cambios oficiales sin tener que crear una rama desde cero).*

Tienes dos métodos según prefieras:

#### 🔹 Opción 3.1: Nivelación limpia total (Sobrescribir tu rama con el Tag Oficial — Recomendada)
> ⚠️ Esta opción sobrescribe el contenido de tu rama para que coincida exactamente con el código del docente. Es la vía más segura para evitar conflictos de merge.

```bash
# 1. Asegurarte de estar ubicado en tu rama de trabajo:
git checkout feature/sesion-06.1.1

# 2. Sobrescribir y nivelar tu rama con el tag oficial anterior de forma exacta:
git reset --hard tags/sesion-06.1.0

# 3. (Solo si ya habías hecho push previo a GitHub) Forzar la sincronización en remoto:
git push origin feature/sesion-06.1.1 --force
```

#### 🔹 Opción 3.2: Fusión conservando tus commits personales (`git merge`)
> 📌 Esta opción combina los commits del tag oficial dentro de tu rama conservando tu historial. Si modificaste los mismos archivos que el docente, deberás resolver los conflictos manualmente.

```bash
# 1. Ubicarte en tu rama de trabajo:
git checkout feature/sesion-06.1.1

# 2. Fusionar el tag oficial en tu rama:
git merge tags/sesion-06.1.0 -m "merge: sincronizar con tag oficial sesion-06.1.0"

# 3. Si Git detecta conflictos, resuélvelos en tu editor (VS Code), guarda y confirma:
# git add .
# git commit -m "fix: resolucion de conflictos con tag oficial"
```

---

### 🟣 Situación 4: Inspección rápida del código final terminado de esta sesión
> 📌 Si solo deseas ver, ejecutar o consultar la solución completa y probada de la sesión de hoy (modo solo lectura o referencia):

```bash
git checkout tags/sesion-06.1.1
```
---

## 🎯 ¿Qué aprenderás en esta Cápsula?

En versiones anteriores de Angular, decorar y manipular elementos del DOM anfitrión solía hacerse mediante decoradores imperativos como `@HostBinding('class.score--passing')` o inyectando `ElementRef` y llamando a `Renderer2.addClass()`.

En **Angular 22**, la arquitectura moderna adopta el bloque **`host: {}` en los metadatos de la directiva** combinado con **Signal Inputs** y **`computed()`**. Esto permite:
1. **Manipulación Declarativa y Reactiva:** Cero llamadas imperativas o subscripciones manuales.
2. **Compatibilidad Zoneless Total:** Angular actualiza las clases CSS en el tick exacto en que la Signal cambia su valor.
3. **Pipes Puros Reutilizables:** `GradeStatusPipe` encapsula la lógica de diagnóstico MINEDU sin sobrecargar los componentes.

---

## 🛠️ Implementación Paso a Paso

### 1. Directiva de Atributo Semántica: `ScoreColorDirective`

Archivo: `src/app/shared/directives/score-color/score-color.directive.ts`

```typescript
import {
  Directive,
  computed,
  input
} from '@angular/core';

@Directive({
  selector: '[appScoreColor]',
  standalone: true,
  host: {
    '[class.score--passing]': 'isPassing()',
    '[class.score--failing]': 'isFailing()',
    '[class.score--empty]': 'isEmpty()'
  }
})
export class ScoreColorDirective {
  /** Entrada reactiva del puntaje enlazada al selector de la directiva */
  public readonly score = input<number | string | null | undefined>(null, {
    alias: 'appScoreColor'
  });

  /** Normalización numérica en caliente */
  public readonly numericScore = computed<number | null>(() => {
    const raw = this.score();
    if (raw === null || raw === undefined || raw === '') {
      return null;
    }
    const num = typeof raw === 'number' ? raw : parseFloat(String(raw).trim());
    return isNaN(num) ? null : num;
  });

  public readonly isPassing = computed<boolean>(() => {
    const val = this.numericScore();
    return val !== null && val >= 11;
  });

  public readonly isFailing = computed<boolean>(() => {
    const val = this.numericScore();
    return val !== null && val < 11;
  });

  public readonly isEmpty = computed<boolean>(() => {
    return this.numericScore() === null;
  });
}
```

---

### 2. Pipe Puro de Diagnóstico Académico: `GradeStatusPipe`

Archivo: `src/app/shared/pipes/grade-status/grade-status.pipe.ts`

```typescript
import { Pipe, PipeTransform } from '@angular/core';

@Pipe({
  name: 'gradeStatus',
  standalone: true,
  pure: true
})
export class GradeStatusPipe implements PipeTransform {
  public transform(value: number | string | null | undefined): string {
    if (value === null || value === undefined || value === '') {
      return 'Pendiente';
    }

    const num = typeof value === 'number' ? value : parseFloat(String(value).trim());
    if (isNaN(num)) {
      return 'Pendiente';
    }

    return num >= 11 ? 'Aprobado' : 'Desaprobado';
  }
}
```

---

### 3. Consumo Declarativo en Plantillas

En `GradeBookMatrixComponent`:

```html
<!-- Chip de promedio final -->
<span class="average-chip" [appScoreColor]="row.get('finalAverage')?.value">
  {{ row.get('finalAverage')?.value }}
</span>

<!-- Chip de condición académica con pipe puro -->
<span class="status-chip" [appScoreColor]="row.get('finalAverage')?.value">
  {{ row.get('finalAverage')?.value | gradeStatus }}
</span>
```

---

## 🧪 Verificación de Calidad

```bash
# 1. Ejecutar pruebas unitarias de la directiva y pipe
npm run test:unit

# 2. Ejecutar suite E2E en Playwright
npm run test:e2e

# 3. Compilación limpia
npm run build
```
Resultado: **75/75 pruebas unitarias aprobadas, 19/19 E2E aprobados y 0 errores de compilación.**
