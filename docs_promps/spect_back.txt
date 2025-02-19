<specification_planning>

# Arquitectura central del sistema y flujos de trabajo clave

## Propuesta de Arquitectura
- Arquitectura modular y escalable donde cada dominio (órdenes, menús, empleados, etc.) se maneja a través de módulos NestJS independientes
- Capa de persistencia con enfoque multi-tenant
  - Cada restaurante (tenant) identificado mediante tenantId
  - Manejo vía esquema en base de datos u otra lógica (ej: tabla Restaurant/Tenant)
  - TenantId referenciado en principales entidades

## Flujos de Trabajo Principales
- Creación/gestión de órdenes (dine-in, delivery, pickup)
- Manejo de menú y disponibilidad de productos  
- Control de mesas y áreas del restaurante
- Reservas y eventos
- Gestión de empleados (turnos, sueldos)
- Procesamiento de pedidos vía WhatsApp con IA
- Integración con pasarelas de pago (Stripe, Clip, efectivo)
- Sincronización offline con Supabase
- Reportes y facturación

# Estructura y organización del proyecto NestJS

## Principios de Organización
- Módulo por Dominio
- Carpeta auth para autenticación/roles
- Carpeta database para configuración/migraciones  
- Carpeta common/utils para utilidades

## Estructura por Módulo
- Esquema hexagonal con:
  - Carpeta domain
  - Carpeta dto
  - Carpeta infrastructure
    - Relational repository
    - Mappers
    - Entidades TypeORM
  - Service como orquestador principal

# Especificaciones detalladas de endpoints y servicios

## Auth
- Rutas:
  - Registro
  - Login
  - Refresh token
  - Logout
  - Confirmación de email
- JWT con roles y guards

## Orders
- Endpoints:
  - Crear
  - Actualizar
  - Cancelar
  - Programar
  - Listar
  - Filtrar
- Soporte múltiples estados (pendiente, en preparación, listo, entregado)

## Mesas
- CRUD completo
- Asignación a órdenes
- Combinación y división

## Menú
- CRUD de:
  - Categorías
  - Subcategorías  
  - Productos
- Manejo de:
  - Variantes
  - Modificadores
  - Ingredientes
- Control de stock

## Pagos
- Integración:
  - Stripe
  - Clip
  - Efectivo
- Endpoints:
  - Iniciar pago
  - Capturar pago
  - Reembolsos

## Reservas
- CRUD completo
- Notificaciones de recordatorio

## Empleados
- CRUD completo
- Gestión de:
  - Roles
  - Check-in/out
  - Cálculo de sueldos
  - Reportes de asistencia

## Reportes/Facturación
- Resúmenes de ventas
- Reportes de inventario y consumo
- Exportación CSV/PDF

## WhatsApp con IA
- Webhook para recibir mensajes
- Procesamiento con IA:
  - Router
  - Order Mapper
  - Query
- Mensajes de confirmación
- Notificaciones de cambios

## Offline Sync
- Endpoints para sincronización con Supabase

# Diseño de esquema de base de datos

## Base de datos
- PostgreSQL/Relacional
- Tablas principales:
  - Restaurant
  - User
  - Role
  - Status
  - Product
  - Category
  - Order
  - Payment
  - Reservation
  - Table
- TenantId en cada tabla
- Llaves foráneas e índices optimizados

# Implementación de DTOs y entidades

## Por Módulo
- DTOs:
  - Create
  - Update
  - Query/Filter
- Entidades TypeORM en infrastructure/persistence/relational/entities
- Mappers entre entidades y objetos de dominio

# Implementación de autenticación y autorización

- JWT Strategy
- Refresh Token Strategy
- Guards basados en roles
- Decoradores @Roles()

# Validación y transformación de datos

- Class-validator y class-transformer en DTOs
- Interceptores para:
  - Serialization
  - Conversión de campos

# Estrategia de manejo de errores

- HttpException
- Exception Filters
- Formato estándar de respuesta de error

# Implementación de logging y monitoreo

- Logger global NestJS
- Integración con plataforma de monitoreo
- Notificaciones de:
  - Fallas en pagos
  - Fallas de sincronización

# Estrategia de pruebas

## Unitarias
- Jest
- Mock de servicios externos

## E2E
- Contenedores Docker
- Base de datos efímera
- Cobertura de endpoints principales

# Configuración Final

- Dependencias en package.json
- Scripts de migraciones y seeds
- Documentación swagger
- Pipeline CI

# Áreas que Requieren Clarificación

- Approach multi-tenant exacto
- Manejo e infraestructura de IA
- Detalles API WhatsApp Business
- Configuración Supabase

</specification_planning>
# Especificación Técnica Backend NestJS - Sistema de Gestión Integral para Restaurantes

## 1. Resumen del Sistema
El Sistema de Gestión Integral para Restaurantes provee funcionalidad para manejar todos los aspectos operativos de un negocio de comida. Integra la administración de órdenes, mesas, menú, reservas, empleados, facturación y pagos, así como la recepción automatizada de pedidos vía WhatsApp con IA.

**Incluye:**

- **Gestión de Órdenes:** (dine-in, delivery, pickup) con seguimiento de estatus en tiempo real.
- **Control de Mesas y Áreas:** (salón, bar, etc.), reservas y eventos.
- **Manejo de Menú:** con categorías, subcategorías, variantes y modificadores.
- **Gestión de Empleados:** roles, turnos, sueldos y asistencias.
- **Pagos:** mediante Stripe y Clip (además de efectivo), con integración de facturación.
- **Módulo Offline:** con sincronización a Supabase.
- **Pedidos vía WhatsApp:** con procesamiento de texto y audio usando IA.

**Arquitectura:**

- Arquitectura modular de NestJS siguiendo principios hexagonales (domain, infrastructure, application).
- Enfoque multi-tenant: cada restaurante (tenant) puede personalizar su configuración y menú.
- Soporte de notificaciones en tiempo real (WebSockets) y uso de PostgreSQL con TypeORM.

## 2. Estructura del Proyecto NestJS
Se propone la siguiente organización de carpetas:

```
src/
  app.module.ts
  main.ts
  auth/
    auth.module.ts
    auth.controller.ts
    auth.service.ts
    strategies/
    dto/
    ...
  orders/
    orders.module.ts
    orders.controller.ts
    orders.service.ts
    domain/
    dto/
    infrastructure/
  tables/
    tables.module.ts
    ...
  menu/
    menu.module.ts
    ...
  payments/
    payments.module.ts
    ...
  reservations/
    reservations.module.ts
    ...
  employees/
    employees.module.ts
    ...
  reports/
    reports.module.ts
    ...
  whatsapp/
    whatsapp.module.ts
    ...
  shared/
    filters/
    interceptors/
    pipes/
    decorators/
    ...
  database/
    config/
    migrations/
    seeds/
    ...
  utils/
    ...
```

- **app.module.ts:** Coordina la importación de todos los submódulos.
- **Módulos de dominio:** auth, orders, tables, menu, payments, reservations, employees, reports, whatsapp.
- **Módulo shared/utils:** Contiene lógica reutilizable (filtros de excepciones, transformadores, pipes).
- **Módulo database:** Configuración y servicios de TypeORM, migraciones y seeds.

*Nota:* Se utilizará la convención de nomenclatura PascalCase para módulos, controladores y servicios; y camelCase para métodos, variables y propiedades.

## 3. Especificación de API
A continuación se definen los módulos y sus endpoints principales. *Nota:* Todos los endpoints se prefijan con `/api/v1` (o la versión que aplique).

### 3.1 Auth Module
**Descripción:** Encargado de la autenticación de usuarios, manejo de roles y recuperación de contraseñas.

**Endpoints:**

- **POST /auth/email/register**  
  Registra un nuevo usuario en el sistema.  
  _Body:_ `AuthRegisterLoginDto { email, password, firstName, lastName, ... }`
  
- **POST /auth/email/login**  
  Realiza login y retorna token, refreshToken, tokenExpires, y datos del usuario.
  
- **POST /auth/refresh**  
  Renueva el token de acceso.  
  _Auth Header:_ `Bearer <refreshToken>`
  
- **GET /auth/me**  
  Devuelve datos del usuario logeado.  
  _Auth Header:_ `Bearer <token>`
  
- **PATCH /auth/me**  
  Actualiza datos del usuario logeado (ej.: cambio de password, update de foto).
  
- **POST /auth/forgot/password**  
  Solicita cambio de contraseña, enviando un email con enlace.
  
- **POST /auth/reset/password**  
  Cambia la contraseña usando un hash temporal recibido por correo.
  
- **POST /auth/logout**  
  Invalida la sesión actual (elimina el refresh token).

**DTOs y Entidades:**

- **DTOs:** `AuthRegisterLoginDto`, `AuthEmailLoginDto`, `AuthUpdateDto`, etc.
- **Entidad:** `UserEntity` (campos: email, contraseña, provider, roleId, statusId, etc.)

**Validaciones:**

- Contraseñas con `MinLength(6)`.
- Email con `@IsEmail()`.

**Manejo de Errores:**

- 422 (Unprocessable Entity) si el email ya está registrado o el password es incorrecto.

### 3.2 Orders Module
**Descripción:** Gestiona la creación y actualización de órdenes, el estatus de preparación y la programación de pedidos futuros.

**Endpoints:**

- **POST /orders**  
  Crea una nueva orden.  
  _Body:_ `CreateOrderDto { items, type (dine-in/delivery/pickup), scheduledAt?, ... }`
  
- **GET /orders**  
  Lista todas las órdenes con paginación y filtros (por fecha, estado, etc.).
  
- **GET /orders/:id**  
  Retorna los detalles de una orden específica.
  
- **PATCH /orders/:id**  
  Actualiza la orden (ej.: cambiar estado, añadir items).
  
- **DELETE /orders/:id**  
  Cancela la orden.

**DTOs y Entidades:**

- **DTOs:** `CreateOrderDto`, `UpdateOrderDto`.
- **Entidad:** `OrderEntity` (campos: id, restaurantId, userId, total, scheduledAt, status, etc.)
- **Relación:** `OrderItemEntity`.

**Validaciones y Transformaciones:**

- Uso de `enum` para el tipo de orden (dine-in/delivery/pickup).
- El campo `scheduledAt` no puede ser una fecha pasada.

**Manejo de Errores:**

- 404 si la orden no existe.
- 422 si la orden ya está en estado "completado" y se intenta modificar.

### 3.3 Tables Module
**Descripción:** Control de mesas y áreas (combinación, división, asignación a órdenes, etc.).

**Endpoints:**

- **POST /tables**
- **GET /tables**
- **GET /tables/:id**
- **PATCH /tables/:id**
- **DELETE /tables/:id**
- **POST /tables/:id/merge** – Para combinar varias mesas en una.
- **POST /tables/:id/split** – Para dividir mesas.

### 3.4 Menu Module
**Descripción:** Manejo de categorías, subcategorías, productos, variantes, modificadores y stock.

**Endpoints:**

- **POST /menu/categories**
- **GET /menu/categories**
- **POST /menu/categories/:id/products**
- **GET /menu/products** – Permite filtrar por categoría, disponibilidad, etc.
- **PATCH /menu/products/:id**
- **DELETE /menu/products/:id**

### 3.5 Payments Module
**Descripción:** Integra Clip, Stripe y efectivo. Permite iniciar o capturar pagos y gestionar reembolsos.

**Endpoints:**

- **POST /payments/intent** – Crea un intent de pago (Stripe).
- **POST /payments/clip** – Procesa un cobro con Clip (en sitio).
- **POST /payments/cash** – Marca el pago como efectivo.
- **POST /payments/refund** – Solicita reembolso en Stripe o Clip.

### 3.6 Reservations Module
**Descripción:** Manejo de reservas de mesas, confirmación y recordatorios automáticos.

**Endpoints:**

- **POST /reservations**
- **GET /reservations**
- **GET /reservations/:id**
- **PATCH /reservations/:id**
- **DELETE /reservations/:id**

### 3.7 Employees Module
**Descripción:** Alta y modificación de empleados, gestión de turnos, sueldos, roles y asistencias.

**Endpoints:**

- **POST /employees**
- **GET /employees**
- **GET /employees/:id**
- **PATCH /employees/:id**
- **DELETE /employees/:id**
- **POST /employees/:id/checkin**
- **POST /employees/:id/checkout**

### 3.8 Reports/Facturación Module
**Descripción:** Genera reportes de ventas, inventario, sueldos, conciliación de pagos y facturación.

**Endpoints:**

- **GET /reports/sales** (parámetros: rango de fechas, etc.)
- **GET /reports/employee**
- **GET /reports/inventory**
- **GET /reports/facturation**

### 3.9 WhatsApp + IA Module
**Descripción:** Módulo para recibir pedidos vía WhatsApp y procesarlos mediante IA (texto/audio).

**Endpoints:**

- **POST /whatsapp/webhook** – Recibe notificaciones de WhatsApp e integra agentes IA (ej.: LLM) para parsear y mapear a productos.
- **GET /whatsapp/conversations** – Devuelve el historial de conversaciones.
- **POST /whatsapp/:conversationId/send** – Envía mensaje al cliente desde la app.

## 4. Esquema de la Base de Datos

### 4.1 Entidades Principales

- **Restaurant (Tenant)**
  - id: string | uuid
  - name: string
  - logo: string (opcional)
  - ...

- **User**
  - id: number (auto)
  - email: string | null
  - password: string
  - provider: enum (email, google, etc.)
  - roleId: FK → Role
  - statusId: FK → Status
  - tenantId: FK → Restaurant (para multi-tenant)
  - deletedAt: Date (soft delete)

- **Role**
  - id: number
  - name: string

- **Status**
  - id: number
  - name: string (active/inactive)

- **Order**
  - id: number
  - tenantId: FK → Restaurant
  - userId: FK → User (quién creó)
  - tableId: FK → Table (opcional)
  - scheduledAt: Date (si es pedido futuro)
  - status: enum (pending, preparing, etc.)
  - total: number
  - deletedAt: Date

- **OrderItem**
  - id: number
  - orderId: FK → Order
  - productId: FK → Product
  - quantity: number
  - price: number
  - deletedAt: Date

- **Table**
  - id: number
  - tenantId: FK → Restaurant
  - name: string
  - area: string (ej.: “salón”, “terraza”)
  - deletedAt: Date

- **Product**
  - id: number
  - tenantId: FK → Restaurant
  - name: string
  - price: number
  - stock: number
  - categoryId: FK → Category
  - photoId: FK → File (opcional)
  - deletedAt: Date

- **Category**
  - id: number
  - tenantId: FK → Restaurant
  - name: string

- **Reservation**
  - id: number
  - tenantId: FK → Restaurant
  - tableId: FK → Table
  - startTime: Date
  - endTime: Date
  - customerName: string
  - deletedAt: Date

- **Payment**
  - id: number
  - orderId: FK → Order
  - method: enum (stripe, clip, cash)
  - amount: number
  - status: enum (captured, refunded, etc.)
  - deletedAt: Date

- **Employee**
  - id: number
  - tenantId: FK → Restaurant
  - name: string
  - role: string (chef, mesero, etc.)
  - salary: number
  - deletedAt: Date

*Nota:* Cada entidad puede incluir índices para `tenantId`, `email`, etc., para mejorar el rendimiento.

## 5. Servicios y Lógica de Negocio

### 5.1 Servicios Core

- **AuthService**
  - Métodos: `validateLogin()`, `register()`, `refreshToken()`, `logout()`.
  - Manejo de tokens JWT y refresh tokens.

- **OrdersService**
  - Métodos: `createOrder()`, `updateOrder()`, `cancelOrder()`, `findAll()`, `findById()`.
  - Lógica de validación de horarios y estados de la orden.

- **TablesService**
  - Métodos para crear, actualizar y eliminar mesas, incluyendo `mergeTables()` y `splitTables()`.

- **MenuService**
  - Métodos: `createCategory()`, `createProduct()`, `updateProductStock()`, etc.

- **PaymentsService**
  - Métodos: `createPaymentIntent()`, `processClipPayment()`, `markCashPayment()`, `refund()`.
  - Manejo de APIs externas (Stripe, Clip).

- **ReservationsService**
  - CRUD de reservas y validación de disponibilidad de mesa.

- **EmployeesService**
  - CRUD de empleados, control de check-in/out y cálculo de nómina.

- **ReportsService**
  - Generación de reportes (ventas, inventario, sueldos) y exportación en CSV/PDF.

- **WhatsAppService**
  - Procesamiento de mensajes entrantes: encolamiento, parseo con IA y creación de órdenes.
  - Envío de mensajes de confirmación.

**Manejo de Casos de Error:**

- Se lanza `HttpException` con códigos 404, 422, 409, etc., según el caso.
- Uso de custom exceptions para conflictos de negocio (ej.: mesa ya ocupada).

## 6. Autenticación y Autorización

**Estrategia:**

- Uso de dos estrategias de JWT: una para token de acceso y otra para token de refresco.
- Roles y Guards: Decorador `@Roles(RoleEnum.admin)` y guard que valida si `request.user.role.id` coincide.

**Implementación:**

- `JwtStrategy` y `JwtRefreshStrategy` ubicados en `auth/strategies`.
- Definidos en `auth.module.ts` e inyectados en `AuthService`.

**Middleware:**

- Aplicación del guard `AuthGuard('jwt')` en rutas protegidas.
- Validación de roles mediante `RolesGuard`.

## 7. Validación y Transformación

- **class-validator:**  
  Cada DTO utiliza decorators como `@IsString()`, `@IsNotEmpty()`, `@IsEnum()`, etc.

- **class-transformer:**  
  Uso de transformaciones (por ejemplo, `@Transform(lowerCaseTransformer)`) y exclusión de campos sensibles con `@Exclude`.

- **Pipes e Interceptores:**  
  Implementación de un `ValidationPipe` global y de interceptores para logging y formateo.

## 8. Manejo de Errores

- **Filtros de Excepción:**  
  Se puede implementar un `HttpExceptionFilter` global para formatear la salida de errores.

- **Errores Personalizados:**  
  Clases que extienden `HttpException` para casos de negocio específicos.

- **Respuestas:**  
  Estructura JSON con `{ status, errors: { ... } }`.

## 9. Logging y Monitoreo

- **Logging:**  
  Uso del logger integrado de NestJS con niveles (log, error, warn) y posible integración con servicios externos (Datadog).

- **Monitoreo:**  
  Health checks en `/health` para verificar conectividad a la DB, Supabase, etc., y envío de alertas mediante Slack u otros canales.

## 10. Pruebas

### 10.1 Pruebas Unitarias

- **Configuración:**  
  Empleo de Jest y pruebas con mocks en la inyección de dependencias.
  
- **Casos de Ejemplo:**  
  - AuthService: validación de credenciales y manejo de tokens caducados.
  - OrdersService: creación de una orden con items vacíos y cancelación de orden en estado “completada”.

### 10.2 Pruebas E2E

- **Docker:**  
  Levantamiento de un contenedor con la base de datos y la aplicación.
  
- **Escenarios Principales:**  
  Registro y login, manipulación de órdenes, actualización de menú, reservas, etc.
  
- **Datos de Prueba:**  
  Seeds con usuarios (admin, user).

## 11. Documentación API

- **Swagger/OpenAPI:**  
  Configurado en `main.ts` mediante `SwaggerModule` y `DocumentBuilder`, documentando cada endpoint, DTO, parámetros y respuestas.
  
- **Convenciones:**  
  Inclusión de descripciones y ejemplos en los DTOs.
  
- **UI:**  
  La documentación accesible a través de `/docs`.

## 12. Despliegue y CI/CD

- **Entornos:**  
  Staging y Production, con variables de entorno para DB, JWT, etc.

- **Pipeline de CI/CD:**  
  Uso de GitHub Actions (por ejemplo, con un archivo `docker-e2e.yml`) para ejecutar lint, tests y pruebas E2E.

- **Despliegue:**  
  Utilización de contenedores Docker y ejecución automática de migraciones y seeds (o mediante script).

- **Infraestructura:**  
  Posible requerimiento de un orquestador (Kubernetes) u otra plataforma.

Esta especificación provee la guía para la implementación del backend NestJS con un enfoque modular, detallando la configuración multi-tenant, la lógica de órdenes, la integración de pagos y la sincronización offline, así como la interacción con WhatsApp e IA. Además, se incluyen validaciones y pruebas para garantizar la fiabilidad del sistema.