# 🎓 Guía del Estudiante • Sesión 04.2
## Nivelación Arquitectónica: Menú Jerárquico Dinámico (/api/menus), Precarga con ResolveFn, withComponentInputBinding() y Bloqueo Institucional por Año Escolar en eduportal-web

> **Curso:** Desarrollo Web Frontend Profesional con Angular 22  
> **Proyecto:** EduPortal — Sistema de Gestión Escolar SaaS  
> **Rama Oficial:** `feature/sesion-04.2`  
> **Tag Oficial:** `sesion-04.2`  

---

## 🎯 Objetivos de la Sesión

En esta sesión aprenderás a:
1. Conectar el menú lateral (Sidebar) a una API REST real (`/api/menus`) usando MongoDB.
2. Organizar la aplicación bajo el patrón **Option Group**, agrupando opciones relacionadas (Académico, Mantenimiento, Estudiantes).
3. Construir una función recursiva/jerárquica que arma un árbol de navegación en memoria.
4. Precargar datos con `ResolveFn` de Angular 22 para evitar parpadeos visuales al navegar.
5. Gestionar el bloqueo de formularios si el año escolar está cerrado usando Signals reactivos.
6. Aplicar buenas prácticas de maquetación: vistas a ancho completo (`width: 100%`) sin centrado artificial.

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
git checkout -f tags/sesion-04.1.0 -b feature/sesion-04.2.0

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
git checkout -b feature/sesion-04.2.0
```

#### 🔹 Alternativa 2.B: Respaldar tu código pero continuar con la solución oficial del docente
> Usa esta opción si deseas conservar tu código para revisión futura, pero prefieres trabajar hoy sobre la base oficial estándar para evitar discrepancias.

```bash
# 1. Crear una rama de respaldo personal para NO perder tu avance:
git checkout -b mi-respaldo-sesion-anterior

# 2. Crear la rama de hoy partiendo 100% limpio del TAG oficial del docente:
git checkout tags/sesion-04.1.0 -b feature/sesion-04.2.0
```

---

### 🔵 Situación 3: Tu rama de trabajo está en la sesión anterior y deseas jalar el nuevo tag oficial
*(Ejemplo común en clase: Tu rama está alineada al tag/commit anterior como `sesion-04.1.0` y deseas actualizarla para recibir los cambios oficiales sin tener que crear una rama desde cero).*

Tienes dos métodos según prefieras:

#### 🔹 Opción 3.1: Nivelación limpia total (Sobrescribir tu rama con el Tag Oficial — Recomendada)
> ⚠️ Esta opción sobrescribe el contenido de tu rama para que coincida exactamente con el código del docente. Es la vía más segura para evitar conflictos de merge.

```bash
# 1. Asegurarte de estar ubicado en tu rama de trabajo:
git checkout feature/sesion-04.2.0

# 2. Sobrescribir y nivelar tu rama con el tag oficial anterior de forma exacta:
git reset --hard tags/sesion-04.1.0

# 3. (Solo si ya habías hecho push previo a GitHub) Forzar la sincronización en remoto:
git push origin feature/sesion-04.2.0 --force
```

#### 🔹 Opción 3.2: Fusión conservando tus commits personales (`git merge`)
> 📌 Esta opción combina los commits del tag oficial dentro de tu rama conservando tu historial. Si modificaste los mismos archivos que el docente, deberás resolver los conflictos manualmente.

```bash
# 1. Ubicarte en tu rama de trabajo:
git checkout feature/sesion-04.2.0

# 2. Fusionar el tag oficial en tu rama:
git merge tags/sesion-04.1.0 -m "merge: sincronizar con tag oficial sesion-04.1.0"

# 3. Si Git detecta conflictos, resuélvelos en tu editor (VS Code), guarda y confirma:
# git add .
# git commit -m "fix: resolucion de conflictos con tag oficial"
```

---

### 🟣 Situación 4: Inspección rápida del código final terminado de esta sesión
> 📌 Si solo deseas ver, ejecutar o consultar la solución completa y probada de la sesión de hoy (modo solo lectura o referencia):

```bash
git checkout tags/sesion-04.2.0
```
---

## 📂 PARTE 1: Interfaces y Contratos de Datos

Copia cada contrato en su archivo correspondiente:

```typescript
// src/app/core/api/models/interfaces/menu.interface.ts
// src/app/core/api/models/interfaces/menu.interface.ts

/**
 * ============================================================================
 * Menu Interface
 * ============================================================================
 * Representa un registro de navegación en el catálogo de menús de MongoDB.
 * ============================================================================
 */
export interface Menu {
  readonly id: string;
  readonly code: string;
  readonly key: string;
  readonly label: string;
  readonly icon: string;
  readonly path: string | null;
  readonly parent: string | null;
  readonly order: number;
  readonly enabled: boolean;
}
```

```typescript
// src/app/core/api/models/interfaces/menu-node.interface.ts
// src/app/core/api/models/interfaces/menu-node.interface.ts
import { Menu } from './menu.interface';

/**
 * ============================================================================
 * MenuNode Interface
 * ============================================================================
 * Estructura jerárquica de menú que anida nodos hijos (Option Groups).
 * ============================================================================
 */
export interface MenuNode extends Menu {
  readonly children?: readonly MenuNode[];
}
```

```typescript
// src/app/shared/layout/sidebar/models/navigation-sub-item.interface.ts
// src/app/shared/layout/sidebar/models/navigation-sub-item.interface.ts

/**
 * ============================================================================
 * NavigationSubItem Interface
 * ============================================================================
 * Opción hija que pertenece a un grupo desplegable en el Sidebar.
 * ============================================================================
 */
export interface NavigationSubItem {
  readonly path: string;
  readonly label: string;
  readonly icon?: string;
  readonly badge?: string;
}
```

```typescript
// src/app/shared/layout/sidebar/models/navigation-item.interface.ts
// src/app/shared/layout/sidebar/models/navigation-item.interface.ts
import { NavigationSubItem } from './navigation-sub-item.interface';

/**
 * ============================================================================
 * NavigationItem Interface
 * ============================================================================
 * Opción de primer nivel del Sidebar (Option Group desplegable u opción directa).
 * ============================================================================
 */
export interface NavigationItem {
  readonly label: string;
  readonly icon: string;
  readonly path?: string;
  readonly badge?: string;
  readonly children?: readonly NavigationSubItem[];
}
```

---

## 📂 PARTE 2: Catálogo de Endpoints y Servicios

```typescript
// src/app/core/api/endpoints.ts
import { environment } from '../../../environments/environment';

export const API_ENDPOINTS = {
  areas: `${environment.apiUrl}/areas`,
  subjects: `${environment.apiUrl}/subjects`,
  levels: `${environment.apiUrl}/levels`,
  grades: `${environment.apiUrl}/grades`,
  sections: `${environment.apiUrl}/sections`,
  classrooms: `${environment.apiUrl}/classrooms`,
  rules: `${environment.apiUrl}/rules`,
  academicYears: `${environment.apiUrl}/academic-years`,
  gradeSections: `${environment.apiUrl}/grade-sections`,
  gradeSubjects: `${environment.apiUrl}/grade-subjects`,
  menus: `${environment.apiUrl}/menus`,
} as const;
```

```typescript
// src/app/core/api/services/menu.service.ts
// src/app/core/api/services/menu.service.ts
import { Injectable, inject, signal } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { firstValueFrom } from 'rxjs';
import { API_ENDPOINTS } from '../endpoints';
import { ApiResponse } from '../models/interfaces/api-response.interface';
import { Menu } from '../models/interfaces/menu.interface';
import { MenuNode } from '../models/interfaces/menu-node.interface';
import { NavigationItem } from '../../../shared/layout/sidebar/models/navigation-item.interface';

/**
 * ============================================================================
 * MenuService
 * ============================================================================
 * Servicio reactivo para la consulta y jerarquización de los menús del sistema.
 * Consume `GET /api/menus` y produce estructuras de árbol para navegación.
 * ============================================================================
 */
@Injectable({
  providedIn: 'root'
})
export class MenuService {
  private readonly http = inject(HttpClient);
  private readonly endpoint = API_ENDPOINTS.menus;

  /** Estado reactivo de los menús jerárquicos cargados */
  public readonly menuTree = signal<readonly MenuNode[]>([]);
  public readonly navigationItems = signal<readonly NavigationItem[]>([]);
  public readonly isLoading = signal<boolean>(false);

  /**
   * Obtiene la lista plana de menús desde el backend.
   */
  public async getRawMenus(): Promise<Menu[]> {
    const response = await firstValueFrom(
      this.http.get<ApiResponse<Menu[]>>(this.endpoint)
    );
    return response.data;
  }

  /**
   * Obtiene y ensambla el árbol jerárquico de menús activos ordenados.
   */
  public async getMenuTree(): Promise<MenuNode[]> {
    this.isLoading.set(true);
    try {
      const rawMenus = await this.getRawMenus();
      const tree = this.buildMenuTree(rawMenus);
      this.menuTree.set(tree);
      this.navigationItems.set(this.transformToNavigationItems(tree));
      return tree;
    } finally {
      this.isLoading.set(false);
    }
  }

  /**
   * Transforma una lista plana de registros de menú en un árbol jerárquico.
   */
  public buildMenuTree(menus: readonly Menu[]): MenuNode[] {
    const enabledMenus = menus.filter(m => m.enabled);
    const parentMenus = enabledMenus
      .filter(m => !m.parent)
      .sort((a, b) => a.order - b.order);

    return parentMenus.map(parent => {
      const children = enabledMenus
        .filter(child => child.parent === parent.id)
        .sort((a, b) => a.order - b.order);

      return {
        ...parent,
        children: children.length > 0 ? children : undefined
      };
    });
  }

  /**
   * Convierte nodos de menú en contratos de presentación del Sidebar.
   */
  public transformToNavigationItems(tree: readonly MenuNode[]): NavigationItem[] {
    return tree.map(node => ({
      label: node.label,
      icon: node.icon,
      path: node.path || undefined,
      children: node.children?.map(c => ({
        label: c.label,
        icon: c.icon,
        path: c.path || ''
      }))
    }));
  }
}
```

```typescript
// src/app/core/api/services/academic-year.service.ts
// src/app/core/api/services/academic-year.service.ts
import { Injectable, inject, signal, computed } from '@angular/core';
import { HttpClient, HttpParams } from '@angular/common/http';
import { firstValueFrom } from 'rxjs';
import { API_ENDPOINTS } from '../endpoints';
import { ApiResponse } from '../models/interfaces/api-response.interface';
import { AcademicYear } from '../models/interfaces/academic-year.interface';

/**
 * ============================================================================
 * AcademicYearService
 * ============================================================================
 * Gestión del ciclo lectivo institucional y control de bloqueo reactivo.
 * ============================================================================
 */
@Injectable({
  providedIn: 'root',
})
export class AcademicYearService {
  private readonly http = inject(HttpClient);
  private readonly endpoint = API_ENDPOINTS.academicYears;

  /** Año lectivo activo seleccionado en la plataforma */
  public readonly activeYear = signal<string>('2026');

  /** Entidad completa del año lectivo cargado */
  public readonly activeYearData = signal<AcademicYear | null>(null);

  /**
   * Signal computado: Indica si el año lectivo se encuentra cerrado
   * impidiendo operaciones de mutación o apertura de periodos.
   */
  public readonly isYearLocked = computed<boolean>(() => {
    const current = this.activeYearData();
    return current ? current.status === 'CERRADO' : false;
  });

  /**
   * 1. Listar todos los años lectivos (opcionalmente filtrados por status o enabled)
   * GET /api/academic-years
   */
  public async getAcademicYears(params?: { status?: string; enabled?: boolean }): Promise<AcademicYear[]> {
    let httpParams = new HttpParams();
    if (params?.status) {
      httpParams = httpParams.set('status', params.status);
    }
    if (params?.enabled !== undefined) {
      httpParams = httpParams.set('enabled', String(params.enabled));
    }

    const response = await firstValueFrom(
      this.http.get<ApiResponse<AcademicYear[]>>(this.endpoint, { params: httpParams })
    );
    return response.data;
  }

  /**
   * 2. Obtener un año lectivo por ID o año (ej: "2026")
   * GET /api/academic-years/:id
   */
  public async getAcademicYearByIdOrYear(idOrYear: string): Promise<AcademicYear> {
    const response = await firstValueFrom(
      this.http.get<ApiResponse<AcademicYear>>(`${this.endpoint}/${idOrYear}`)
    );
    return response.data;
  }

  /**
   * 3. Cargar y establecer reactivamente el año escolar activo
   */
  public async loadActiveYear(year: string = '2026'): Promise<AcademicYear> {
    const data = await this.getAcademicYearByIdOrYear(year);
    this.activeYear.set(year);
    this.activeYearData.set(data);
    return data;
  }
}
```

---

## 📂 PARTE 3: Resolver de Rutas y Configuración

```typescript
// src/app/core/resolvers/menu.resolver.ts
// src/app/core/resolvers/menu.resolver.ts
import { inject } from '@angular/core';
import { ResolveFn } from '@angular/router';
import { MenuService } from '../api/services/menu.service';
import { MenuNode } from '../api/models/interfaces/menu-node.interface';

/**
 * ============================================================================
 * menuResolver
 * ============================================================================
 * Precarga el árbol jerárquico de navegación escolar antes de instanciar el layout.
 * ============================================================================
 */
export const menuResolver: ResolveFn<MenuNode[]> = () => {
  const menuService = inject(MenuService);
  return menuService.getMenuTree();
};
```

```typescript
// src/app/app.config.ts
import { ApplicationConfig, provideBrowserGlobalErrorListeners } from '@angular/core';
import { provideHttpClient, withFetch } from '@angular/common/http';
import { provideRouter, withComponentInputBinding } from '@angular/router';
import { routes } from './app.routes';

export const appConfig: ApplicationConfig = {
  providers: [
    provideBrowserGlobalErrorListeners(),
    provideRouter(routes, withComponentInputBinding()),
    provideHttpClient(withFetch()),
  ],
};
```

```typescript
// src/app/app.routes.ts
import { Routes } from '@angular/router';
import { menuResolver } from './core/resolvers/menu.resolver';

/**
 * ============================================================================
 * App Root Routes (Enrutamiento Principal de EduPortal)
 * ============================================================================
 * Carga el layout base con Sidebar y Header, precargando el catálogo de menús
 * vía `menuResolver` e inyectando submódulos vía Lazy Loading.
 * ============================================================================
 */
export const routes: Routes = [
  {
    path: '',
    loadComponent: () =>
      import('./core/layouts/main-layout/main-layout.component').then(m => m.MainLayoutComponent),
    resolve: {
      menuTree: menuResolver
    },
    children: [
      {
        path: '',
        redirectTo: 'dashboard',
        pathMatch: 'full'
      },
      {
        path: 'dashboard',
        loadComponent: () =>
          import('./features/dashboard/pages/dashboard-page/dashboard-page.component').then(
            m => m.DashboardPageComponent
          )
      },
      {
        path: 'alumnos',
        loadComponent: () =>
          import('./features/students/pages/students-page/students-page.component').then(
            m => m.StudentsPageComponent
          )
      },
      {
        path: '',
        loadChildren: () =>
          import('./features/academic').then(m => m.ACADEMIC_ROUTES)
      },
      {
        path: '',
        loadChildren: () =>
          import('./features/maintenance').then(m => m.MAINTENANCE_ROUTES)
      },
    ]
  }
];
```

---

## 📂 PARTE 4: Sidebar Reactivo

```typescript
// src/app/shared/layout/sidebar/sidebar.component.ts
// src/app/shared/layout/sidebar/sidebar.component.ts
import { Component, input, signal, computed, inject } from '@angular/core';
import { RouterLink, RouterLinkActive } from '@angular/router';
import { MatIconModule } from '@angular/material/icon';
import { MenuService } from '../../../core/api/services/menu.service';
import { NavigationItem } from './models/navigation-item.interface';

/**
 * ============================================================================
 * SidebarComponent
 * ============================================================================
 * Menú lateral de navegación con soporte de Option Groups dinámicos desde la API.
 * ============================================================================
 */
@Component({
  selector: 'app-sidebar',
  imports: [RouterLink, RouterLinkActive, MatIconModule],
  templateUrl: './sidebar.component.html',
  styleUrl: './sidebar.component.scss'
})
export class SidebarComponent {
  private readonly menuService = inject(MenuService);

  /** Signal input reactivo recibido desde el componente padre: */
  public readonly isCollapsed = input<boolean>(false);

  /** Opción para recibir items personalizados por input binding si fuera requerido: */
  public readonly itemsInput = input<readonly NavigationItem[] | null>(null, { alias: 'navItems' });

  /** Control de grupos desplegados (inicia abierto 'Gestión Académica' y 'Mantenimiento'): */
  public readonly openGroups = signal<Set<string>>(new Set(['Gestión Académica', 'Académico', 'Mantenimiento']));

  /**
   * Catálogo reactivo de navegación escolar:
   * Prioriza el input directo, luego los datos del MenuService (MongoDB), y como fallback el catálogo base.
   */
  public readonly navItems = computed<readonly NavigationItem[]>(() => {
    const inputVal = this.itemsInput();
    if (inputVal && inputVal.length > 0) {
      return inputVal;
    }
    const fromService = this.menuService.navigationItems();
    if (fromService && fromService.length > 0) {
      return fromService;
    }
    return this.fallbackNavItems;
  });

  public toggleGroup(label: string): void {
    const current = new Set(this.openGroups());
    if (current.has(label)) {
      current.delete(label);
    } else {
      current.add(label);
    }
    this.openGroups.set(current);
  }

  public isGroupOpen(label: string): boolean {
    return this.openGroups().has(label);
  }

  /** Catálogo de respaldo estático en caso de desconexión momentánea de la API: */
  private readonly fallbackNavItems: readonly NavigationItem[] = [
    { path: '/dashboard', label: 'Panel de Control', icon: 'dashboard' },
    {
      label: 'Gestión Académica',
      icon: 'school',
      children: [
        { path: '/grados-secciones', label: 'Salones y Secciones', icon: 'class' },
        { path: '/grados-asignaturas', label: 'Malla Curricular', icon: 'auto_stories' },
        { path: '/asignaturas', label: 'Asignaturas', icon: 'menu_book' },
      ]
    },
    {
      label: 'Mantenimiento',
      icon: 'tune',
      children: [
        { path: '/niveles', label: 'Niveles Educativos', icon: 'layers' },
        { path: '/areas', label: 'Áreas Curriculares', icon: 'category' },
      ]
    }
  ];
}
```

```html
<!-- src/app/shared/layout/sidebar/sidebar.component.html -->
<!-- src/app/shared/layout/sidebar/sidebar.component.html -->
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
      @for (item of navItems(); track item.label) {
        <li class="app-sidebar__item">
          @if (item.children && item.children.length > 0) {
            <!-- Ítem de Option Group desplegable -->
            <button
              type="button"
              class="app-sidebar__link app-sidebar__link--group"
              [class.app-sidebar__link--group-open]="isGroupOpen(item.label)"
              (click)="toggleGroup(item.label)"
              [title]="isCollapsed() ? item.label : ''"
            >
              <mat-icon class="app-sidebar__icon">{{ item.icon }}</mat-icon>

              @if (!isCollapsed()) {
                <span class="app-sidebar__label">{{ item.label }}</span>
                @if (item.badge) {
                  <span class="app-sidebar__badge badge badge--info">{{ item.badge }}</span>
                }
                <mat-icon class="app-sidebar__arrow-icon">
                  {{ isGroupOpen(item.label) ? 'expand_less' : 'expand_more' }}
                </mat-icon>
              }
            </button>

            <!-- Submenú de opciones hijas del grupo -->
            @if (!isCollapsed() && isGroupOpen(item.label)) {
              <ul class="app-sidebar__submenu">
                @for (subItem of item.children; track subItem.path) {
                  <li class="app-sidebar__submenu-item">
                    <a
                      [routerLink]="subItem.path"
                      routerLinkActive="app-sidebar__submenu-link--active"
                      class="app-sidebar__submenu-link"
                    >
                      @if (subItem.icon) {
                        <mat-icon class="app-sidebar__submenu-icon">{{ subItem.icon }}</mat-icon>
                      }
                      <span class="app-sidebar__submenu-label">{{ subItem.label }}</span>
                      @if (subItem.badge) {
                        <span class="app-sidebar__badge badge badge--info">{{ subItem.badge }}</span>
                      }
                    </a>
                  </li>
                }
              </ul>
            }
          } @else {
            <!-- Enlace directo individual -->
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
          }
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

---

## 📂 PARTE 5: Estilos SCSS Centralizados

```scss
// src/app/shared/styles/_variables.scss
// src/app/shared/styles/_variables.scss

// Paleta Institucional EduPortal SaaS
$primary: var(--primary, #e40035);
$primary-hover: var(--primary-hover, #c8002f);
$primary-light: var(--primary-light, #fff1f2);

$secondary: var(--secondary, #0f172a);
$secondary-light: var(--secondary-light, #1e293b);
$accent: var(--accent, #00b4d8);

// Fondos y Superficies
$bg-page: var(--bg-page, #f8fafc);
$bg-surface: var(--bg-surface, #ffffff);
$border-color: var(--border-color, #e2e8f0);

// Textos
$text-main: var(--text-main, #0f172a);
$text-muted: var(--text-muted, #64748b);
$text-light: var(--text-light, #94a3b8);

// Estados Escolares
$success: var(--success, #10b981);
$success-bg: var(--success-bg, #ecfdf5);
$warning: var(--warning, #f59e0b);
$warning-bg: var(--warning-bg, #fffbeb);
$danger: var(--danger, #ef4444);
$danger-bg: var(--danger-bg, #fef2f2);
$info: var(--info, #3b82f6);
$info-bg: var(--info-bg, #eff6ff);

// Sombras y Radios
$shadow-sm: var(--shadow-sm, 0 1px 2px 0 rgba(0, 0, 0, 0.05));
$shadow-md: var(--shadow-md, 0 4px 6px -1px rgba(0, 0, 0, 0.1));
$shadow-lg: var(--shadow-lg, 0 10px 15px -3px rgba(0, 0, 0, 0.1));

$radius-sm: var(--radius-sm, 6px);
$radius-md: var(--radius-md, 10px);
$radius-lg: var(--radius-lg, 16px);
$radius-full: var(--radius-full, 9999px);
```

```scss
// src/app/shared/styles/_mixins.scss
// src/app/shared/styles/_mixins.scss

/**
 * Mixin para forzar el contenedor de vista a ancho completo sin centrado artificial
 */
@mixin full-width-view {
  display: flex;
  flex-direction: column;
  width: 100%;
  box-sizing: border-box;
}

/**
 * Mixin para tarjetas de superficie limpia con borde sutil y sombra suave
 */
@mixin surface-card {
  background: var(--bg-surface, #ffffff);
  border: 1px solid var(--border-color, #e2e8f0);
  border-radius: var(--radius-md, 10px);
  box-shadow: var(--shadow-sm, 0 1px 2px 0 rgba(0, 0, 0, 0.05));
}

/**
 * Mixin para alineación flex de barra de herramientas horizontal
 */
@mixin flex-between {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 1rem;
}

/**
 * Mixin para grilla responsiva de tarjetas métricas
 */
@mixin kpi-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(220px, 1fr));
  gap: 1.25rem;
  margin-bottom: 1.5rem;
  width: 100%;
}
```

```scss
// src/app/shared/styles/_kpi-card.scss
// src/app/shared/styles/_kpi-card.scss
@use './variables' as *;
@use './mixins' as *;

.kpi-cards-grid {
  @include kpi-grid;
}

.kpi-card {
  @include surface-card;
  padding: 1.25rem;
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
  position: relative;
  overflow: hidden;
  transition: transform 0.2s ease, box-shadow 0.2s ease;

  &:hover {
    transform: translateY(-2px);
    box-shadow: $shadow-md;
  }

  &__header {
    display: flex;
    align-items: center;
    justify-content: space-between;
  }

  &__label {
    font-size: 0.8125rem;
    font-weight: 600;
    text-transform: uppercase;
    letter-spacing: 0.05em;
    color: $text-muted;
  }

  &__icon-box {
    width: 36px;
    height: 36px;
    border-radius: $radius-sm;
    display: flex;
    align-items: center;
    justify-content: center;

    mat-icon {
      font-size: 20px;
      width: 20px;
      height: 20px;
    }

    &--primary {
      background-color: $primary-light;
      color: $primary;
    }

    &--success {
      background-color: $success-bg;
      color: $success;
    }

    &--warning {
      background-color: $warning-bg;
      color: $warning;
    }

    &--info {
      background-color: $info-bg;
      color: $info;
    }
  }

  &__value {
    font-size: 1.875rem;
    font-weight: 700;
    color: $text-main;
    line-height: 1.1;
  }

  &__footer {
    display: flex;
    align-items: center;
    gap: 0.35rem;
    font-size: 0.75rem;
    color: $text-muted;
  }
}
```

---

## 📂 PARTE 6: Pruebas Unitarias TDD

```typescript
// tests/menu-leveling.test.ts
// tests/menu-leveling.test.ts
import { describe, it } from 'node:test';
import assert from 'node:assert/strict';

// Interface replicas for pure test execution
interface TestMenu {
  id: string;
  code: string;
  key: string;
  label: string;
  icon: string;
  path: string | null;
  parent: string | null;
  order: number;
  enabled: boolean;
}

interface TestMenuNode extends TestMenu {
  children?: TestMenuNode[];
}

interface TestNavigationItem {
  label: string;
  icon: string;
  path?: string;
  children?: { label: string; icon?: string; path: string }[];
}

function buildMenuTree(menus: readonly TestMenu[]): TestMenuNode[] {
  const enabledMenus = menus.filter(m => m.enabled);
  const parentMenus = enabledMenus
    .filter(m => !m.parent)
    .sort((a, b) => a.order - b.order);

  return parentMenus.map(parent => {
    const children = enabledMenus
      .filter(child => child.parent === parent.id)
      .sort((a, b) => a.order - b.order);

    return {
      ...parent,
      children: children.length > 0 ? children : undefined
    };
  });
}

function transformToNavigationItems(tree: readonly TestMenuNode[]): TestNavigationItem[] {
  return tree.map(node => ({
    label: node.label,
    icon: node.icon,
    path: node.path || undefined,
    children: node.children?.map(c => ({
      label: c.label,
      icon: c.icon,
      path: c.path || ''
    }))
  }));
}

function computeIsYearLocked(status: 'PLANIFICACION' | 'ACTIVO' | 'CERRADO'): boolean {
  return status === 'CERRADO';
}

describe('Sesión 04.2: Nivelación Arquitectónica - Menú y Bloqueo de Año Escolar', () => {

  const mockMenus: TestMenu[] = [
    { id: '1', code: 'MEN-001', key: 'DASHBOARD', label: 'Panel de Control', icon: 'dashboard', path: '/dashboard', parent: null, order: 1, enabled: true },
    { id: '2', code: 'MEN-004', key: 'GROUP_ACADEMIC', label: 'Gestión Académica', icon: 'class', path: null, parent: null, order: 2, enabled: true },
    { id: '3', code: 'MEN-011', key: 'GRADE_SECTIONS', label: 'Salones y Secciones', icon: 'meeting_room', path: '/grados-secciones', parent: '2', order: 1, enabled: true },
    { id: '4', code: 'MEN-012', key: 'GRADE_SUBJECTS', label: 'Malla Curricular', icon: 'account_tree', path: '/grados-asignaturas', parent: '2', order: 2, enabled: true },
    { id: '5', code: 'MEN-013', key: 'SUBJECTS', label: 'Catálogo de Asignaturas', icon: 'menu_book', path: '/asignaturas', parent: '2', order: 3, enabled: true },
    { id: '6', code: 'MEN-009', key: 'GROUP_MAINTENANCE', label: 'Mantenimiento', icon: 'tune', path: null, parent: null, order: 3, enabled: true },
    { id: '7', code: 'MEN-014', key: 'LEVELS', label: 'Niveles Educativos', icon: 'stairs', path: '/niveles', parent: '6', order: 1, enabled: true },
    { id: '8', code: 'MEN-015', key: 'AREAS', label: 'Áreas Curriculares', icon: 'category', path: '/areas', parent: '6', order: 2, enabled: true },
    { id: '9', code: 'MEN-999', key: 'DISABLED_ITEM', label: 'Opción Deshabilitada', icon: 'block', path: '/test', parent: null, order: 99, enabled: false }
  ];

  it('TDD-01: buildMenuTree debe omitir items con enabled=false', () => {
    const tree = buildMenuTree(mockMenus);
    const disabled = tree.find(m => m.key === 'DISABLED_ITEM');
    assert.equal(disabled, undefined);
  });

  it('TDD-02: buildMenuTree debe ordenar los padres por el campo order ascendente', () => {
    const tree = buildMenuTree(mockMenus);
    assert.equal(tree.length, 3);
    assert.equal(tree[0].key, 'DASHBOARD');
    assert.equal(tree[1].key, 'GROUP_ACADEMIC');
    assert.equal(tree[2].key, 'GROUP_MAINTENANCE');
  });

  it('TDD-03: buildMenuTree debe anidar las opciones hijas dentro de su correspondiente Option Group', () => {
    const tree = buildMenuTree(mockMenus);
    const academicGroup = tree.find(m => m.key === 'GROUP_ACADEMIC');
    assert.ok(academicGroup);
    assert.equal(academicGroup?.children?.length, 3);
    assert.equal(academicGroup?.children?.[0].key, 'GRADE_SECTIONS');
    assert.equal(academicGroup?.children?.[1].key, 'GRADE_SUBJECTS');
    assert.equal(academicGroup?.children?.[2].key, 'SUBJECTS');
  });

  it('TDD-04: transformToNavigationItems debe generar contratos de Sidebar con Option Groups y subitems', () => {
    const tree = buildMenuTree(mockMenus);
    const navItems = transformToNavigationItems(tree);

    assert.equal(navItems.length, 3);
    // Dashboard es enlace directo
    assert.equal(navItems[0].path, '/dashboard');
    assert.equal(navItems[0].children, undefined);

    // Gestión Académica es un Option Group con 3 hijos
    assert.equal(navItems[1].label, 'Gestión Académica');
    assert.equal(navItems[1].path, undefined);
    assert.equal(navItems[1].children?.length, 3);
    assert.equal(navItems[1].children?.[0].path, '/grados-secciones');
    assert.equal(navItems[1].children?.[1].path, '/grados-asignaturas');
    assert.equal(navItems[1].children?.[2].path, '/asignaturas');
  });

  it('TDD-05: computeIsYearLocked debe bloquear solo cuando status es CERRADO', () => {
    assert.equal(computeIsYearLocked('CERRADO'), true, 'Año cerrado debe estar bloqueado');
    assert.equal(computeIsYearLocked('ACTIVO'), false, 'Año activo debe estar desbloqueado');
    assert.equal(computeIsYearLocked('PLANIFICACION'), false, 'Año en planificación debe estar desbloqueado');
  });
});
```

### Ejecutar Pruebas y Compilar:
```bash
npm run test:unit && npm run build
```

---

## 🏁 Entrega y Sincronización en Git

```bash
git add .
git commit -m "feat(sesion-04.2): nivelacion arquitectonica completa con menu dinamico y bloqueo reactivo"
git push -u origin feature/sesion-04.2
```
