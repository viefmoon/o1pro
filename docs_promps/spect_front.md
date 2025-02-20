<specification_planning>

## 1. Arquitectura general de la aplicación y flujos de navegación

- Identificaré que se usará Expo (con EAS) y React Navigation como base de la navegación.
- Definiré el tipo de navegadores (Stack, Tabs, Drawer) según se requiera en el proyecto.

## 2. Estructura y organización del proyecto en React Native (Expo)

- Describiré la estructura de carpetas: `app/`, `components/`, `navigators/`, `screens/`, `theme/`, etc.
- Incluiré las convenciones de nomenclatura y la lógica detrás de los archivos de configuración de Expo (`app.json`, `app.config.ts`) y `eas.json`.

## 3. Especificaciones de cada funcionalidad (pantallas, componentes, lógica)

- Para cada feature, mencionaré las pantallas involucradas, su flujo de usuario y cómo se estructuran los componentes que la implementan.
- Detallaré escenarios de error y edge cases (por ejemplo, offline, campos vacíos, etc.).

## 4. Diseño de pantallas, rutas y manejo de navegación (React Navigation)

- Explicaré la configuración principal: `AppNavigator`, `createNativeStackNavigator`, `useNavigation`, etc.
- Si se usaran tabs o drawers, detallaré su configuración.
- Indicaré ejemplos de rutas protegidas y/o deep linking (si procede).

## 5. Sistema de diseño (temas, estilos, tipografía, spacing, componentes base)

- Listaré la paleta de colores, cómo se define la tipografía y spacing.
- Mencionaré el uso de `useAppTheme` y la integración con theming (light/dark).
- Aclararé cómo se compone la librería de componentes base (Button, Text, Card, Icon, etc.) y cómo se personalizan.

## 6. Arquitectura de componentes (organización, props, modularidad)

- Describiré cómo se dividen los componentes en `components/` y `screens/` (presentational vs. container).
- Uso de TypeScript para tipar las props de componentes.
- Normas de reusabilidad y tamaño de los componentes.

## 7. Manejo del estado (MobX-State-Tree, Redux, Zustand o la librería elegida)

- Como en la plantilla base hay MST, explicaré la configuración: RootStore, Providers, etc.
- Definiré la estructura recomendada de los stores, slices o models.
- Mencionaré la serialización, la persistencia y las integraciones con la base de datos local.

## 8. Conexión con EAS para build y deployment (entornos, variables de entorno)

- Describiré cómo se definen los perfiles (production, preview, development) en `eas.json`.
- Indicaré pasos para hacer un build con `expo run:android` o `expo run:ios` y cómo se usa `expo start --dev-client`.

## 9. Pruebas (unitarias con Jest, tests de componentes, e2e con Maestro u otras herramientas)

- Explicaré cómo usar `jest` con la configuración existente y las pruebas de componentes con `@testing-library/react-native`.
- Mencionaré la posibilidad de usar Maestro para e2e y qué scripts se configuran para correr las pruebas.

## 10. Accesibilidad (lineamientos y consideraciones)

- Incluiré menciones a la importancia de `accessibilityLabel`, `importantForAccessibility` y la compatibilidad con VoiceOver/TalkBack.

## 11. Internacionalización (si aplica, i18n)

- Describiré la configuración con `react-i18next` (o la que se haya dispuesto), la organización de ficheros de traducción y fallback.

## 12. Gestión de assets (imágenes, íconos, fuentes)

- Explicaré cómo se manejan en `assets/images/` y `assets/icons/`, el uso de `AutoImage`, e integración con Google Fonts o fuentes propias.

## 13. Optimización de performance (lazy loading, inlineRequires, etc.)

- Mencionaré las prácticas: `inlineRequires`, memoización, `useCallback`, `useMemo`.
- Uso de Reanimated en caso de animaciones más complejas.

## 14. Estrategia de publicación (App Stores, TestFlight, Play Internal Testing)

- Reforzaré los pasos: `eas build`, `eas submit`, las credenciales y configuraciones.

## 15. Conclusión y checklist final

- Resumen final de buenas prácticas.
- Referencias a la documentación de Expo, React Navigation, MST, etc.

<specification_planning>

# Especificación Técnica Frontend Expo React Native – Nombre Del Proyecto

## 1. Resumen del Sistema

Este documento describe la implementación frontend en React Native (usando Expo) para el proyecto **Nombre Del Proyecto**. El objetivo principal es proveer una app móvil que satisfaga los requisitos de negocio, manejando la navegación, estados, interfaces y comunicación con el backend de manera confiable y escalable.

### Alcance

- Desarrollo de la interfaz de usuario en React Native con TypeScript.
- Uso de Expo para compilar y distribuir la app.
- Implementación de flujos principales (inicio de sesión, pantallas de listado/detalle, etc.).
- Manejo de estado y persistencia con la librería seleccionada (por defecto MobX-State-Tree).
- Integraciones con servicios externos (APIs, notificaciones, etc.) según sea necesario.

### Flujos Principales de Usuario

- **Autenticación**: registro, inicio de sesión y recuperación de contraseña.
- **Vista principal**: listado de items (ej.: productos, pedidos, etc.).
- **Detalle**: vista detallada de un ítem y acciones (edición, borrado, etc.).
- **Otras características** según la naturaleza del proyecto (por ejemplo, perfiles de usuario, carritos de compra, etc.).

### Arquitectura General

- **Expo** para el flujo de compilación y despliegue multiplataforma.
- **React Navigation** para la navegación.
- **Manejo de estado** con MST (opcionalmente, Redux u otra librería).
- **API** consumida con `apisauce` (u otra librería HTTP).
- **Entorno** configurado con TypeScript, EAS y las buenas prácticas recomendadas.

---

## 2. Estructura del Proyecto Expo React Native
````

MyProject
│
├─ app.config.ts / app.json // Configuración principal de Expo
├─ eas.json // Perfiles de build EAS
├─ index.tsx // Registro principal de la app
├─ app/
│ ├─ app.tsx // Punto de entrada de la app
│ ├─ components/ // Componentes reutilizables (Button, Card, etc.)
│ ├─ config/ // Configs de entorno y ajustes
│ ├─ devtools/ // Config de Reactotron o debug
│ ├─ i18n/ // Ficheros de traducción
│ ├─ models/ // Manejo de estado (MST/Redux)
│ ├─ navigators/ // Definición de rutas y pantallas
│ ├─ screens/ // Pantallas principales
│ ├─ services/ // Llamadas a APIs y otros servicios
│ ├─ theme/ // Estilos (colores, tipografía)
│ └─ utils/ // Funciones utilitarias
├─ assets/
│ ├─ images/ // Imágenes (logo, fondos, etc.)
│ └─ icons/ // Íconos de la app
├─ test/ // Tests unitarios / e2e
└─ ...

````

1. **app.config.ts / app.json**: contiene la configuración de Expo, con info del bundle ID, iconos, splash, permisos, etc.
2. **eas.json**: define los perfiles de build para desarrollo, preview y producción (EAS).
3. **index.tsx**: registra el `App` con `registerRootComponent`.
4. **app/app.tsx**: punto de entrada real, inicializa i18n, Reactotron (opcional), y monta las rutas principales.
5. **app/navigators/**: define la estructura de navegación (Stack, Tabs, Drawer, etc.).
6. **app/screens/**: cada pantalla principal en un archivo (por ejemplo `LoginScreen.tsx`, `HomeScreen.tsx`).
7. **app/components/**: componentes UI genéricos y reutilizables (ej.: Button, Header, Icon).
8. **app/models/**: para MobX-State-Tree (o la librería elegida). Suele incluir `RootStore.ts`, definiciones de modelos, etc.
9. **app/services/**: contiene la definición de `api.ts` u otros servicios (ej.: notificaciones, integraciones).
10. **app/theme/**: define colores, tipografías, espaciados y estilos base para el theming.
11. **test/**: para pruebas unitarias (Jest), pruebas de componentes y pruebas e2e (por ejemplo, con Maestro).

---

## 3. Especificación de Funcionalidades

### 3.1 Autenticación
- **Descripción**: Permite a los usuarios registrarse, iniciar sesión y recuperar contraseña.
- **Pantallas**:
  - `LoginScreen.tsx`: pantalla de login con email/contraseña, botón “Recuperar contraseña”.
  - `RegisterScreen.tsx`: formulario de registro con validaciones.
  - `ForgotPasswordScreen.tsx`: permite solicitar un enlace o código para recuperar la cuenta.
- **Flujo**:
  1. Usuario ingresa credenciales y pulsa "Iniciar sesión".
  2. App llama a la API (`/auth/login`) vía `apisauce` y, si es correcto, guarda el token (MobX-State-Tree).
  3. Navega a la pantalla principal (`HomeScreen.tsx`).
- **Componentes**:
  - Form fields con validación (email, password).
  - Botones y feedback de error (si falla).
- **Errores y edge cases**:
  - Campos vacíos, formato de email inválido.
  - API caído o sin conexión (offline).
  - Mensajes de error con i18n (ej.: "Usuario/Contraseña no válidos").
- **Integración con API**:
  - Endpoint `/auth/register`, `/auth/login`, `/auth/forgot-password`.
  - Manejo de tokens (JWT) guardados en el Store.

### 3.2 Pantalla Principal (ej.: Lista de Ítems)
- **Descripción**: Muestra un listado de datos (productos, órdenes, etc.) con botón para agregar nuevo.
- **Pantallas**:
  - `HomeScreen.tsx` o `ItemListScreen.tsx`.
- **Flujo**:
  1. Carga inicial desde API (ej.: `GET /items`).
  2. MST actualiza su store con la respuesta.
  3. Renderiza la lista usando un FlatList o FlashList.
  4. Cada ítem navega al detalle.
- **Errores y edge cases**:
  - Lista vacía, sin conexión, paginación si es necesaria.
- **Integración con API**:
  - `GET /items`, con `apisauce`.

### 3.3 Detalle de Ítem
- **Descripción**: Muestra los detalles y acciones (editar, eliminar).
- **Pantallas**:
  - `ItemDetailScreen.tsx`.
- **Flujo**:
  1. Usuario elige un ítem en la lista.
  2. Se navega con param (id ítem).
  3. Pantalla hace fetch (si no está en la store) o lee MST.
  4. Muestra detalles y botones (editar/eliminar).
- **Errores y edge cases**:
  - Ítem no encontrado (404).
  - Sin conexión.
- **Integración con API**:
  - `GET /items/:id`, `PUT /items/:id`, `DELETE /items/:id`.

(Se repite este esquema para otras funcionalidades clave.)

---

## 4. Navegación y Rutas

### 4.1 Configuración de React Navigation
- Uso de `createNativeStackNavigator` para stacks principales.
- Posible uso de `createBottomTabNavigator` para tabs.
- `AppNavigator.tsx`:
  ```tsx
  const Stack = createNativeStackNavigator<AppStackParamList>()

  export const AppNavigator = () => {
    return (
      <Stack.Navigator screenOptions={{ headerShown: false }}>
        <Stack.Screen name="Login" component={LoginScreen} />
        <Stack.Screen name="Register" component={RegisterScreen} />
        <Stack.Screen name="MainTabs" component={MainTabNavigator} />
        {/* otras pantallas */}
      </Stack.Navigator>
    )
  }
````

### 4.2 Ejemplos de uso de navegación

- `navigation.navigate("ItemDetail", { id: selectedId })`.
- `navigation.goBack()` para retroceder.
- Rutas protegidas: se puede hacer chequeo en `AppNavigator` o con un guard.

---

## 5. Sistema de Diseño y Theming

### 5.1 Paleta de Colores

```js
const palette = {
  neutral100: "#FFFFFF",
  neutral800: "#191015",
  primary500: "#C76542",
  // ...
};
```

- Colores semánticos (ej.: `background`, `border`, `text`) derivados del palette.

### 5.2 Tipografía

- Uso de fuentes definidas en `app/theme/typography.ts` (ej.: SpaceGrotesk).
- Configuración de carga con `useFonts` en `app.tsx`.

### 5.3 Estilos Base y Componentes

- Espacios con un `spacing` que define XS, SM, MD, etc.
- Componentes base (`Button`, `Text`, `Card`, `Header`, `Icon`, etc.).
- Theming con `useAppTheme()` para modo oscuro/claro.

---

## 6. Arquitectura de Componentes

### 6.1 Organización

- `app/components/` para componentes pequeños y reutilizables.
- `app/screens/` para cada pantalla.
- Cada componente tipado con TypeScript (ej.: `interface ButtonProps { ... }`).

### 6.2 Definición de Props

- Uso de `FC<Props>` o `function MyComponent(props: Props)`.
- Estilos se manejan con `$style` o `themed($style)`.

### 6.3 Manejo de Estado

- **MobX-State-Tree**:
  - `RootStore.ts` con `types.model("RootStore", {...})`.
  - Inyección en la app con `<StoreProvider>`.
  - Acciones asíncronas para llamados a API.

---

## 7. Integraciones

### 7.1 Integración con APIs

- Se sugiere `apisauce` (ej.: `const api = create({ baseURL, timeout })`).
- Manejo de errores con interceptores o utilidades (parse status code).

### 7.2 Expo Plugins

- Ej.: `withSplashScreen` para resolver doble splash en Android.
- `app.config.ts` con:
  ```ts
  {
    plugins: [require("./plugins/withSplashScreen").withSplashScreen],
  }
  ```

### 7.3 Otros Servicios Externos (Auth, notificaciones push, analítica)

- Por ejemplo, integración con Expo Push Notifications (`expo-notifications`).
- Manejo de llaves y secrets con variables de entorno (usando `.env` y prebuild).

---

## 8. EAS (Expo Application Services)

- **eas.json** con perfiles:
  ```json
  {
    "build": {
      "development": {
        "extends": "production",
        "distribution": "internal",
        "android": { "gradleCommand": ":app:assembleDebug" },
        "ios": { "buildConfiguration": "Debug", "simulator": true }
      },
      "production": {}
    }
  }
  ```
- `npx expo prebuild --clean` si se requiere regenerar proyectos nativos.
- `eas build --platform ios/android --profile production`.

---

## 9. Accesibilidad

- Uso de `accessibilityLabel`, `accessible={true}` en componentes.
- Revisión de contraste de colores y tamaños de texto.

---

## 10. Pruebas

### 10.1 Pruebas Unitarias y de Componentes

- **Jest** configurado con `jest-expo`.
- `@testing-library/react-native` para probar interacciones:
  ```tsx
  test("renders welcome message", () => {
    const { getByText } = render(<WelcomeScreen />);
    expect(getByText("Welcome")).toBeTruthy();
  });
  ```

### 10.2 Pruebas E2E

- **Maestro** (sugerido).
  - Scripts: `"test:maestro": "maestro test .maestro/FavoritePodcast.yaml"`
  - Flujos clave: login, listado, agregar ítem, etc.

---

## 11. Internacionalización (opcional)

- Configuración con `i18n`, `react-i18next`:
  - Ficheros `en.ts`, `es.ts`, etc.
  - Hook `useTranslation()` en pantallas o wrapper `translate(key)`.
- Fallback a inglés si falla el locale detectado.

---

## 12. Estrategia de Publicación

- Uso de EAS:
  - `eas build -p ios --profile production` => genera IPA.
  - `eas submit -p ios`.
- TestFlight o Play Console.
- Versionado semántico en `app.json` (expo.version) y `expo.ios.buildNumber`.

---

## 13. Manejo de Assets

- Íconos y splash con Ignite generadores (o manual con `expo generate splash-screen`).
- Componentes `AutoImage` para adaptación de imágenes.
- Fuentes en `assets/fonts/`, registradas en `app.config.ts`.

---

## 14. Optimización de Performance (opcional)

- Activar `inlineRequires` en `metro.config.js`.
- Uso de `React.memo` / `useMemo` / `useCallback`.
- Control de renders con logs (Reactotron o Flipper).

---

## 15. Checklist Final y Notas Adicionales

- **Librerías principales**:
  - "react-native": "0.76.x",
  - "expo": "~52.x",
  - "react-navigation/native": "6.x",
  - "mobx-state-tree": "5.x", etc.
- **Pendientes**:
  - Ajustar branding final (iconos y splash).
  - Revisión de accesibilidad con VoiceOver / TalkBack.
- **Glosario**: MST (MobX-State-Tree), EAS (Expo Application Services).

**Fin de Especificación Técnica.**
