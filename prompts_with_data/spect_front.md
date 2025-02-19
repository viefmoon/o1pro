# Especificación Técnica para Frontend Expo React Native – Proceso de Generación de Especificación

Eres un arquitecto de software experto encargado de crear especificaciones técnicas detalladas para la parte Frontend de aplicaciones utilizando React Native con Expo. Tus especificaciones serán utilizadas como entrada directa para sistemas AI de planificación y generación de código, por lo que deben ser precisas, estructuradas y completas.

## Requerimientos Iniciales

### 1. Requerimiento del Proyecto

Primero, revisa cuidadosamente el requerimiento del proyecto:

```plaintext
<project_request>
{{insert_request_here}}
</project_request>
```

### 2. Technical Specification del Backend

Antes de continuar, revisa cuidadosamente la especificación técnica del backend:

```plaintext
<technical_specification>
{{insert_backend_spec_here}}
</technical_specification>
```

### 3. Plantilla Base para frontend

Finalmente, revisa cuidadosamente la plantilla base:

```plaintext
<starter_template>
{{insert_template_here}}
</starter_template>
```

## Tarea

Tu tarea es generar una especificación técnica completa basada en la información anterior.

## Proceso de Planificación

Antes de crear la especificación final, analiza los requerimientos del proyecto y planifica tu enfoque. Envuelve tu proceso de pensamiento en etiquetas `<specification_planning>`, considerando:

- Arquitectura general de la aplicación y flujos de navegación.
- Estructura y organización del proyecto en React Native (Expo).
- Especificaciones de cada funcionalidad (pantallas, componentes, lógica).
- Diseño de pantallas, rutas y manejo de navegación (React Navigation).
- Sistema de diseño (temas, estilos, tipografía, spacing, componentes base).
- Arquitectura de componentes (organización, props, modularidad).
- Manejo del estado (MobX-State-Tree, Redux, Zustand o la librería elegida).
- Integración con servicios externos (APIs REST/GraphQL, push notifications, etc.).
- Conexión con EAS para build y deployment (entornos, variables de entorno).
- Pruebas (unitarias con Jest, tests de componentes, e2e con Maestro u otras herramientas).
- Accesibilidad (lineamientos y consideraciones).
- Internacionalización (si aplica, uso de i18n).
- Gestión de assets (imágenes, íconos, fuentes).
- Optimización de performance (lazy loading, inlineRequires, etc.).
- Estrategia de publicación (App Stores, TestFlight, Play Internal Testing).

Para cada área anterior:

- Proporciona un desglose paso a paso de lo que se debe incluir (por ejemplo, qué librerías se usarían y qué configuraciones se requieren).
- Enumera los posibles desafíos o puntos que requieran clarificación con el equipo de producto o diseño.
- Considera posibles casos extremos y planes de fallback (por ejemplo, ¿qué pasa si falla la carga de assets o si no hay internet?).

## Especificación Técnica Final (Formato Markdown)

Después de tu análisis, genera la especificación técnica final utilizando la siguiente estructura en Markdown:

```markdown
# Especificación Técnica Frontend Expo React Native - {Nombre del Proyecto}

## 1. Resumen del Sistema
- **Propósito central y alcance del frontend** (rol de la app en el dispositivo)
- **Flujos principales de usuario** (ej.: registro, login, pantallas de lista/detalle)
- **Arquitectura general** (Expo, TypeScript, etc.)

## 2. Estructura del Proyecto Expo React Native
- **Desglose detallado de la organización de carpetas** (`app/`, `components/`, `navigators/`, `screens/`, `theme/`, etc.)
- **Configuración principal** (app.json, app.config.ts, EAS, etc.)
- **Convenciones de nomenclatura**

## 3. Especificación de Funcionalidades
Para cada funcionalidad o “feature” principal:

### 3.1 {Nombre de la Funcionalidad}
- **Descripción / Historia de usuario**
- **Lista de pantallas involucradas** (si aplica)
- **Flujos o casos de uso** (navegación y lógica UI)
- **Estructura de los componentes** (propiedades, estados, eventos)
- **Manejo de errores y edge cases** (ej.: offline, inputs vacíos, etc.)
- **Integración con APIs (si aplica)**

## 4. Navegación y Rutas
### 4.1 Configuración de React Navigation
- **Stack Navigator, Tab Navigator, Drawer** (si aplica)
- **Deep Linking** (si fuera necesario)
- **Rutas y parámetros principales**

### 4.2 Ejemplos de uso de navegación
- **Cómo navegar** (ej.: `navigate`, `goBack`, `resetRoot`)
- **Rutas protegidas** (si aplica)

## 5. Sistema de Diseño y Theming
### 5.1 Paleta de colores
- **Colores primarios, secundarios**, neutrales, alertas, etc. (con códigos hex)

### 5.2 Tipografía
- **Fuentes** (familias, tamaños, pesos)
- Configuración de fuentes en Expo

### 5.3 Estilos Base y Componentes
- **Estilos globales** (espacios, tamaños)
- **Componentes reutilizables** (Button, Text, Card, Header, etc.)
- **Cómo manejar estados** (hover, focus, pressed) en RN

## 6. Arquitectura de Componentes
### 6.1 Organización
- **Carpetas** (`components/` vs. `screens/`, etc.)
- **Principios** (componentes presentacionales, container, etc.)

### 6.2 Definición de Props e Interfaces (TypeScript)
- Tipos para props y estados
- Uso de `React.FC` o funciones anónimas

### 6.3 Manejo de Estado
- **MobX-State-Tree** / Redux / Zustand / Context API (según corresponda)
- **Estructura del store**, acciones, etc.

## 7. Integraciones
### 7.1 Integración con APIs
- **Apisauce, Axios u otro** (endpoints, headers, manejo de respuestas)
- **Manejo de errores** (retry, timeouts, logs)

### 7.2 Expo Plugins
- **Config plugins** (por ejemplo, SplashScreen, Fonts, SecureStore, etc.)
- **Configuración en app.config.ts** o app.json

### 7.3 Otros Servicios Externos (Auth, notificaciones push, analítica)
- Uso de Firebase, AWS Amplify, etc. (si aplica)
- Configuración de llaves y entornos

## 8. EAS (Expo Application Services)
- **Flujo de builds**: desarrollo, preview, producción
- **Configuraciones en eas.json** (perfiles de build)
- **Configuración de variables de entorno** (si aplica)
- **Distribución en iOS y Android** (TestFlight, Play Internal Testing)

## 9. Accesibilidad
- **Lineamientos** (uso de `accessibilityLabel`, `importantForAccessibility`)
- **Soporte de screen readers** (VoiceOver, TalkBack)
- **Pruebas de accesibilidad**

## 10. Pruebas
### 10.1 Pruebas Unitarias y de Componentes
- **Jest** (estructura de test, coverage)
- **React Native Testing Library** (pruebas de componentes, snapshots)
- Ejemplos de casos de prueba

### 10.2 Pruebas E2E (Maestro / Detox / etc.)
- Configuración de scripts
- Flujos clave a probar
- Entornos de test

## 11. Internacionalización (opcional, si aplica)
- **Configuración de i18n** (react-i18next o librería elegida)
- **Cómo agregar nuevos idiomas**
- **Manejo de strings y fallback**

## 12. Estrategia de Publicación
- **App Stores** (descripción breve del proceso)
- **Versionado** (semver, convención interna)
- **Automatización con EAS** (canales de release, etc.)

## 13. Manejo de Assets
- **Íconos, imágenes y fuentes** (dónde se ubican, cómo se cargan)
- **Optimizaciones** (carga perezosa, compresión, `AutoImage`)

## 14. Optimización de Performance (opcional)
- **Uso de inlineRequires** en metro.config.js
- **Uso de memo, PureComponent** donde sea relevante
- **Seguimiento de rendimiento** (flipper, logs)

## 15. Checklist Final y Notas Adicionales
- **Resumen de librerías críticas** (versiones, link a docs)
- **Pendientes y riesgos** (si existen aspectos no resueltos)
- **Glosario / Siglas** (si el proyecto lo requiere)

Asegúrate de que tu especificación final sea muy detallada, proporcionando guía de implementación específica cuando sea posible. Incluye ejemplos concretos de código para funcionalidades complejas (por ejemplo, configuración de `<Stack.Navigator>` o un snippet de la store MST) y define claramente cómo se interrelacionan los componentes y pantallas.

Una vez que finalices, se usará esta especificación en un sistema AI de generación de código para construir la aplicación frontend en React Native con Expo.