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
- Control de mesas, áreas y zonas del restaurante
- Reservas
- Gestión de empleados (turnos, sueldos)
- Registro de modificaciones de órdenes por parte de los usuarios
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
- Registro de modificaciones de órdenes (para llevar un historial de cambios realizados por el usuario)

## Mesas y Áreas

- CRUD completo para mesas
- Gestión de áreas:
  - Se incorpora la entidad **Area** para definir zonas o espacios dentro del restaurante (ej.: Salón, Terraza, Bar)
- Asignación de mesas a áreas y órdenes
- Combinación y división de mesas

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
  - Product
  - Category
  - Order
  - Payment
  - Reservation
  - Table
  - Area
  - OrderModification
  - ProductVariant
  - DailyCounter
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
- **Registro de Modificaciones:** Se mantiene un historial de modificaciones realizadas a las órdenes por parte de los usuarios.

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

_Nota:_ Se utilizará la convención de nomenclatura PascalCase para módulos, controladores y servicios; y camelCase para métodos, variables y propiedades.

## 3. Especificación de API

A continuación se definen los módulos y sus endpoints principales. _Nota:_ Todos los endpoints se prefijan con `/api/v1`.

### 3.1 Auth Module

**Descripción:** Encargado de la autenticación de usuarios, manejo de roles y recuperación de contraseñas.

**Endpoints:**

- **POST `/api/v1/auth/email/register`**  
  Registra un nuevo usuario en el sistema.  
  _Body:_ `AuthRegisterLoginDto { email, password, firstName, lastName, ... }`
- **POST `/api/v1/auth/email/login`**  
  Realiza login y retorna token, refreshToken, tokenExpires, y datos del usuario.
- **POST `/api/v1/auth/refresh`**  
  Renueva el token de acceso.  
  _Auth Header:_ `Bearer <refreshToken>`
- **GET `/api/v1/auth/me`**  
  Devuelve datos del usuario logeado.  
  _Auth Header:_ `Bearer <token>`
- **PATCH `/api/v1/auth/me`**  
  Actualiza datos del usuario logeado (ej.: cambio de password, update de foto).
- **POST `/api/v1/auth/forgot/password`**  
  Solicita cambio de contraseña, enviando un email con enlace.
- **POST `/api/v1/auth/reset/password`**  
  Cambia la contraseña usando un hash temporal recibido por correo.
- **POST `/api/v1/auth/logout`**  
  Invalida la sesión actual (elimina el refresh token).

**DTOs y Entidades:**

- **DTOs:** `AuthRegisterLoginDto`, `AuthEmailLoginDto`, `AuthUpdateDto`, etc.
- **Entidad:** `UserEntity` (campos: email, contraseña, provider, roleId, isActive, tenantId, deletedAt, etc.)

**Validaciones:**

- Contraseñas con `MinLength(6)`.
- Email con `@IsEmail()`.

**Manejo de Errores:**

- 422 (Unprocessable Entity) si el email ya está registrado o el password es incorrecto.

### 3.2 Orders Module

**Descripción:** Gestiona la creación y actualización de órdenes, el estatus de preparación y la programación de pedidos futuros.

**Endpoints:**

- **POST `/api/v1/orders`**  
  Crea una nueva orden.  
  _Body:_ `CreateOrderDto { items, type (dine-in/delivery/pickup), scheduledAt?, ... }`
- **GET `/api/v1/orders`**  
  Lista todas las órdenes con paginación y filtros (por fecha, estado, etc.).
- **GET `/api/v1/orders/:id`**  
  Retorna los detalles de una orden específica.
- **PATCH `/api/v1/orders/:id`**  
  Actualiza la orden (ej.: cambiar estado, añadir items).
- **DELETE `/api/v1/orders/:id`**  
  Cancela la orden.

- **Registro de Modificaciones:**  
  Cada acción sobre una orden (creación, modificación, cancelación) se registra en la entidad `OrderModification`, enlazando la orden con el usuario que realizó la acción.

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

### 3.3 Tables y Áreas Module

**Descripción:** Control de mesas y áreas (combinación, división, asignación a órdenes, etc.).

**Endpoints:**

- **Mesas:**
  - **POST `/api/v1/tables`**
  - **GET `/api/v1/tables`**
  - **GET `/api/v1/tables/:id`**
  - **PATCH `/api/v1/tables/:id`**
  - **DELETE `/api/v1/tables/:id`**
  - **POST `/api/v1/tables/:id/merge`** – Para combinar varias mesas en una.
  - **POST `/api/v1/tables/:id/split`** – Para dividir mesas.
- **Áreas:**

  - **POST `/api/v1/areas`** – Crea una nueva área en el restaurante.
  - **GET `/api/v1/areas`** – Lista todas las áreas.
  - **PATCH `/api/v1/areas/:id`** – Actualiza una área.
  - **DELETE `/api/v1/areas/:id`** – Elimina una área.

- **Solicitud de Cuenta:**
  - **POST `/api/v1/tables/:id/request-bill`** - Registra solicitud de cuenta
  - **GET `/api/v1/tables/:id/bill-status`** - Obtiene estado de la cuenta
  - **POST `/api/v1/tables/:id/print-bill`** - Marca la cuenta como impresa
  - **POST `/api/v1/tables/:id/split-bill`** - Divide la cuenta según criterios

### 3.4 Menu Module

**Descripción:** Manejo de categorías, subcategorías, productos, variantes, modificadores y stock.

**Endpoints:**

- **Categorías y Subcategorías:**
  - **POST `/api/v1/menu/categories`**
  - **GET `/api/v1/menu/categories`**
  - **POST `/api/v1/menu/categories/:id/products`**
- **Productos:**
  - **GET `/api/v1/menu/products`** – Permite filtrar por categoría, disponibilidad, etc.
  - **PATCH `/api/v1/menu/products/:id`**
  - **DELETE `/api/v1/menu/products/:id`**
- **Variantes:**
  - **POST `/api/v1/menu/products/:id/variants`** – Agrega una variante a un producto.
  - **GET `/api/v1/menu/variants`**
- **Modificadores:**
  - Los modificadores en los pedidos se gestionan mediante la entidad `OrderItemModifier`.

### 3.5 Payments Module

**Descripción:** Integra Clip, Stripe y efectivo. Permite iniciar o capturar pagos y gestionar reembolsos.

**Endpoints:**

- **POST `/api/v1/payments/intent`** – Crea un intent de pago (Stripe).
- **POST `/api/v1/payments/clip`** – Procesa un cobro con Clip (en sitio).
- **POST `/api/v1/payments/cash`** – Marca el pago como efectivo.
- **POST `/api/v1/payments/refund`** – Solicita reembolso en Stripe o Clip.

### 3.6 Reservations Module

**Descripción:** Manejo de reservas de mesas, confirmación y recordatorios automáticos.

**Endpoints:**

- **POST `/api/v1/reservations`**
- **GET `/api/v1/reservations`**
- **GET `/api/v1/reservations/:id`**
- **PATCH `/api/v1/reservations/:id`**
- **DELETE `/api/v1/reservations/:id`**

### 3.7 Employees Module

**Descripción:** Sistema integral para gestión de empleados, incluyendo programación de turnos, solicitudes de tiempo libre y nómina.

**Endpoints:**

- **Empleados Base:**
  - **POST `/api/v1/employees`**
  - **GET `/api/v1/employees`**
  - **GET `/api/v1/employees/:id`**
  - **PATCH `/api/v1/employees/:id`**
  - **DELETE `/api/v1/employees/:id`**
- **Programación:**
  - **POST `/api/v1/employees/schedules`** - Crea nuevo turno
  - **GET `/api/v1/employees/schedules`** - Lista turnos (filtrable)
  - **PATCH `/api/v1/employees/schedules/:id`** - Actualiza turno
  - **DELETE `/api/v1/employees/schedules/:id`** - Cancela turno
  - **GET `/api/v1/employees/:id/schedules`** - Turnos por empleado
- **Tiempo Libre:**
  - **POST `/api/v1/employees/time-off`** - Nueva solicitud
  - **GET `/api/v1/employees/time-off`** - Lista solicitudes
  - **PATCH `/api/v1/employees/time-off/:id`** - Actualiza solicitud
  - **POST `/api/v1/employees/time-off/:id/approve`** - Aprueba solicitud
  - **POST `/api/v1/employees/time-off/:id/reject`** - Rechaza solicitud
- **Nómina:**
  - **POST `/api/v1/employees/payroll`** - Genera nómina
  - **GET `/api/v1/employees/payroll`** - Lista nóminas
  - **GET `/api/v1/employees/:id/payroll`** - Nóminas por empleado
  - **PATCH `/api/v1/employees/payroll/:id`** - Actualiza nómina
  - **POST `/api/v1/employees/payroll/:id/approve`** - Aprueba nómina
  - **POST `/api/v1/employees/payroll/:id/process-payment`** - Procesa pago

**DTOs:**

- CreateEmployeeScheduleDto
- UpdateEmployeeScheduleDto
- TimeOffRequestDto
- PayrollGenerationDto
- PayrollUpdateDto

**Validaciones:**

- No solapamiento de turnos para un mismo empleado
- Validación de fechas coherentes en solicitudes
- Verificación de saldo disponible para vacaciones
- Validación de montos y cálculos en nómina

**Manejo de Errores:**

- 409 para conflictos de horarios
- 422 para solicitudes inválidas de tiempo libre
- 403 para intentos no autorizados de aprobación

### 3.8 Reports/Facturación Module

**Descripción:** Genera reportes de ventas, inventario, sueldos, conciliación de pagos y facturación.

**Endpoints:**

- **GET `/api/v1/reports/sales`** (parámetros: rango de fechas, etc.)
- **GET `/api/v1/reports/employee**
- **GET `/api/v1/reports/inventory**
- **GET `/api/v1/reports/facturation**

### 3.9 WhatsApp + IA Module

**Descripción:** Módulo para recibir pedidos vía WhatsApp y procesarlos mediante IA (texto/audio).

**Endpoints:**

- **POST `/api/v1/whatsapp/webhook** – Recibe notificaciones de WhatsApp e integra agentes IA (ej.: LLM) para parsear y mapear a productos.
- **GET `/api/v1/whatsapp/conversations** – Devuelve el historial de conversaciones.
- **POST `/api/v1/whatsapp/:conversationId/send** – Envía mensaje al cliente desde la app.

## 4. Esquema de la Base de Datos

### 4.1 Enums del Sistema

**OrderType**

- DINE_IN: Para consumo en el restaurante
- DELIVERY: Para entrega a domicilio
- PICKUP: Para recoger en el restaurante
- DRIVE_THRU: Para recoger en auto

**OrderStatus**

- PENDING: Orden recibida
- CONFIRMED: Orden confirmada
- PREPARING: En preparación
- READY: Lista para entrega/servir
- IN_DELIVERY: En camino (para delivery)
- COMPLETED: Entregada/Servida
- CANCELLED: Cancelada
- REJECTED: Rechazada
- BILL_REQUESTED: Cuenta solicitada
- BILL_PRINTED: Cuenta impresa
- PENDING_PAYMENT: Pendiente de pago

**OrderItemStatus**

- PENDING: Item recibido, pendiente de preparación
- IN_PROGRESS: En proceso de preparación
- READY: Listo para servir/entregar
- DELIVERED: Entregado al cliente
- CANCELLED: Cancelado
- REFUNDED: Reembolsado
- ON_HOLD: En espera (ej: falta de ingredientes)
- REJECTED: Rechazado por cocina

**DiscountType**

- PERCENTAGE: Descuento porcentual
- FIXED_AMOUNT: Monto fijo
- PROMO_CODE: Código promocional
- LOYALTY_POINTS: Puntos de fidelidad
- EMPLOYEE: Descuento para empleados
- MANAGER: Descuento autorizado por gerente
- HAPPY_HOUR: Descuento por horario especial
- BULK: Descuento por cantidad

### 4.2 Entidades Principales

// Datos Base del Tenant

- **Restaurant (Tenant)**

  - id: number (PK, required)
  - name: string (required)
  - logoId: FK → File (nullable)
  - isActive: boolean (required, default: true)
  - deletedAt: Date (nullable)
  - Relaciones:
    - users: User[] (OneToMany)
    - orders: Order[] (OneToMany)
    - tables: Table[] (OneToMany)
    - products: Product[] (OneToMany)
    - areas: Area[] (OneToMany)
    - employees: Employee[] (OneToMany)
    - logo: File (ManyToOne)

- **User**

  - id: number (PK, required)
  - username: string (required, unique)
  - email: string (required, unique)
  - password: string (required)
  - roleId: FK → Role (required)
  - isActive: boolean (required, default: true)
  - tenantId: FK → Restaurant (required)
  - deletedAt: Date (nullable)
  - Relaciones:
    - restaurant: Restaurant (ManyToOne)
    - role: Role (ManyToOne)
    - orders: Order[] (OneToMany)
    - orderModifications: OrderModification[] (OneToMany)
    - profile: UserProfile (OneToOne)

- **Role**

  - id: number (PK, required)
  - name: string (required, unique)
  - Relaciones:
    - users: User[] (OneToMany)

- **File**
  - id: number (PK, required)
  - path: string (required)
  - filename: string (required)
  - mimetype: string (required)
  - size: number (required)
  - tenantId: FK → Restaurant (required)
  - deletedAt: Date (nullable)
  - Relaciones:
    - restaurant: Restaurant (ManyToOne)
    - restaurantLogos: Restaurant[] (OneToMany)
    - productPhotos: Product[] (OneToMany)

// Pedidos y Transacciones

- **Order**

  - id: number (PK, required)
  - tenantId: FK → Restaurant (required)
  - userId: FK → User (required)
  - tableId: FK → Table (nullable)
  - dailyNumber: number (required)
  - dailyCounterId: FK → DailyCounter (required)
  - scheduledAt: Date (nullable)
  - status: enum (required, default: 'PENDING')
  - orderType: enum (required)
  - subtotal: number (required)
  - total: number (required)
  - deletedAt: Date (nullable)
  - customerProfileId: FK → CustomerProfile (nullable)
  - deliveryAddressId: FK → CustomerAddress (nullable)
  - isWhatsAppOrder: boolean (required, default: false)
  - Relaciones:
    - restaurant: Restaurant (ManyToOne)
    - user: User (ManyToOne)
    - table: Table (ManyToOne, requerido si orderType es DINE_IN)
    - orderItems: OrderItem[] (OneToMany)
    - orderModifications: OrderModification[] (OneToMany)
    - payment: Payment (OneToOne)
    - dailyCounter: DailyCounter (ManyToOne)
    - customerProfile: CustomerProfile (ManyToOne)
    - deliveryAddress: CustomerAddress (ManyToOne)
    - discounts: OrderDiscount[] (OneToMany)

- **OrderModification**

  - id: number (PK, required)
  - orderId: FK → Order (required)
  - userId: FK → User (required)
  - modificationType: string (required)
  - modificationTimestamp: Date (required, default: now())
  - details: string (required)
  - deletedAt: Date (nullable)
  - Relaciones:
    - order: Order (ManyToOne)
    - user: User (ManyToOne)

- **OrderItem**

  - id: number (PK, required)
  - orderId: FK → Order (required)
  - productId: FK → Product (required)
  - quantity: number (required, min: 1)
  - basePrice: number (required)
  - finalPrice: number (required)
  - status: enum (required, default: 'PENDING')
  - statusChangedAt: Date (required, default: now())
  - preparationNotes: string (nullable)
  - deletedAt: Date (nullable)
  - preparationScreenId: FK → PreparationScreen (required)
  - Relaciones:
    - order: Order (ManyToOne)
    - product: Product (ManyToOne)
    - modifiers: OrderItemModifier[] (OneToMany)
    - statusHistory: OrderItemStatusHistory[] (OneToMany)
    - preparationScreen: PreparationScreen (ManyToOne)
    - preparationQueue: PreparationQueueItem (OneToOne)
    - discounts: OrderItemDiscount[] (OneToMany)

- **OrderItemModifier**

  - id: number (PK, required)
  - orderItemId: FK → OrderItem (required)
  - modifierId: FK → ProductModifier (required)
  - quantity: number (required, min: 1)
  - price: number (required)
  - deletedAt: Date (nullable)
  - Relaciones:
    - orderItem: OrderItem (ManyToOne)
    - modifier: ProductModifier (ManyToOne)

- **OrderItemStatusHistory**

  - id: number (PK, required)
  - orderItemId: FK → OrderItem (required)
  - status: enum (required)
  - changedAt: Date (required, default: now())
  - changedBy: FK → User (required)
  - Relaciones:
    - orderItem: OrderItem (ManyToOne)
    - user: User (ManyToOne)

- **OrderDiscount**

  - id: number (PK, required)
  - orderId: FK → Order (required)
  - type: enum (required)
  - value: number (required)
  - code: string (nullable)
  - description: string (required)
  - authorizedBy: FK → User (nullable)
  - appliedAt: Date (required, default: now())
  - expiresAt: Date (nullable)
  - minOrderValue: number (nullable)
  - maxDiscountAmount: number (nullable)
  - deletedAt: Date (nullable)
  - Relaciones:
    - order: Order (ManyToOne)
    - authorizer: User (ManyToOne)

- **OrderItemDiscount**

  - id: number (PK, required)
  - orderItemId: FK → OrderItem (required)
  - type: enum (required)
  - value: number (required)
  - description: string (required)
  - authorizedBy: FK → User (nullable)
  - appliedAt: Date (required, default: now())
  - deletedAt: Date (nullable)
  - Relaciones:
    - orderItem: OrderItem (ManyToOne)
    - authorizer: User (ManyToOne)

- **Payment**

  - id: number (PK, required)
  - orderId: FK → Order (required, unique)
  - method: enum (required)
  - amount: number (required)
  - status: enum (required, default: 'PENDING')
  - deletedAt: Date (nullable)
  - Relaciones:
    - order: Order (OneToOne)

- **DailyCounter**

  - id: number (PK, required)
  - tenantId: FK → Restaurant (required)
  - date: Date (required)
  - type: enum (required, default: 'order')
  - currentNumber: number (required, default: 0)
  - prefix: string (nullable)
  - deletedAt: Date (nullable)
  - Relaciones:
    - restaurant: Restaurant (ManyToOne)
    - orders: Order[] (OneToMany)

- **BillRequest**

  - id: number (PK, required)
  - tenantId: FK → Restaurant (required)
  - orderId: FK → Order (required)
  - tableId: FK → Table (required)
  - requestedAt: Date (required, default: now())
  - printedAt: Date (nullable)
  - status: enum (required, default: 'REQUESTED')
  - requestedBy: FK → User (required)
  - printedBy: FK → User (nullable)
  - amount: number (required)
  - splitType: enum (nullable)
  - splitAmount: number (nullable)
  - notes: string (nullable)
  - deletedAt: Date (nullable)
  - Relaciones:
    - order: Order (ManyToOne)
    - table: Table (ManyToOne)
    - requester: User (ManyToOne)
    - printer: User (ManyToOne)
    - splitDetails: BillSplitDetail[] (OneToMany)

- **BillSplitDetail**

  - id: number (PK, required)
  - billRequestId: FK → BillRequest (required)
  - items: jsonb (required) // Array de OrderItems incluidos
  - amount: number (required)
  - status: enum (required, default: 'PENDING')
  - paymentId: FK → Payment (nullable)
  - deletedAt: Date (nullable)
  - Relaciones:
    - billRequest: BillRequest (ManyToOne)
    - payment: Payment (ManyToOne)

// Mesas y Reservas

- **Area**

  - id: number (PK, required)
  - tenantId: FK → Restaurant (required)
  - name: string (required)
  - description: string (nullable)
  - deletedAt: Date (nullable)
  - Relaciones:
    - restaurant: Restaurant (ManyToOne)
    - tables: Table[] (OneToMany)

- **Table**

  - id: number (PK, required)
  - tenantId: FK → Restaurant (required)
  - name: string (required)
  - areaId: FK → Area (required)
  - isTemporary: boolean (required, default: false)
  - temporaryIdentifier: string (nullable)
  - parentTableId: FK → Table (nullable)
  - deletedAt: Date (nullable)
  - Relaciones:
    - restaurant: Restaurant (ManyToOne)
    - area: Area (ManyToOne)
    - orders: Order[] (OneToMany)
    - reservations: Reservation[] (OneToMany)
    - parentTable: Table (ManyToOne)
    - temporaryTables: Table[] (OneToMany)

- **Reservation**

  - id: number (PK, required)
  - tenantId: FK → Restaurant (required)
  - tableId: FK → Table (required)
  - startTime: Date (required)
  - endTime: Date (required)
  - customerName: string (required)
  - deletedAt: Date (nullable)
  - Relaciones:
    - restaurant: Restaurant (ManyToOne)
    - table: Table (ManyToOne)

// Menú y Productos

- **Category**

  - id: number (PK, required)
  - tenantId: FK → Restaurant (required)
  - name: string (required)
  - description: string (nullable)
  - deletedAt: Date (nullable)
  - Relaciones:
    - restaurant: Restaurant (ManyToOne)
    - subCategories: SubCategory[] (OneToMany)

- **SubCategory**

  - id: number (PK, required)
  - tenantId: FK → Restaurant (required)
  - categoryId: FK → Category (required)
  - name: string (required)
  - description: string (nullable)
  - deletedAt: Date (nullable)
  - Relaciones:
    - restaurant: Restaurant (ManyToOne)
    - category: Category (ManyToOne)
    - products: Product[] (OneToMany)

- **Product**

  - id: number (PK, required)
  - tenantId: FK → Restaurant (required)
  - name: string (required)
  - price: number (required)
  - stock: number (required, default: 0)
  - subCategoryId: FK → SubCategory (required)
  - photoId: FK → File (nullable)
  - estimatedPrepTime: number (required) // Tiempo estimado de preparación en minutos
  - deletedAt: Date (nullable)
  - preparationScreenId: FK → PreparationScreen (required)
  - Relaciones:
    - restaurant: Restaurant (ManyToOne)
    - subCategory: SubCategory (ManyToOne)
    - variants: ProductVariant[] (OneToMany)
    - orderItems: OrderItem[] (OneToMany)
    - preparationScreen: PreparationScreen (ManyToOne)

- **ProductVariant**

  - id: number (PK, required)
  - productId: FK → Product (required)
  - name: string (required)
  - additionalPrice: number (required)
  - deletedAt: Date (nullable)
  - Relaciones:
    - product: Product (ManyToOne)

- **ModifierGroup**

  - id: number (PK, required)
  - tenantId: FK → Restaurant (required)
  - name: string (required)
  - description: string (nullable)
  - minSelections: number (required, default: 0)
  - maxSelections: number (required)
  - isRequired: boolean (required, default: false)
  - deletedAt: Date (nullable)
  - Relaciones:
    - restaurant: Restaurant (ManyToOne)
    - modifiers: ProductModifier[] (OneToMany)
    - products: Product[] (ManyToMany)

- **ProductModifier**

  - id: number (PK, required)
  - groupId: FK → ModifierGroup (required)
  - name: string (required)
  - price: number (required, default: 0)
  - sortOrder: number (required, default: 0)
  - deletedAt: Date (nullable)
  - Relaciones:
    - group: ModifierGroup (ManyToOne)
    - orderItemModifiers: OrderItemModifier[] (OneToMany)

// Clientes

- **CustomerProfile**

  - id: number (PK, required)
  - tenantId: FK → Restaurant (required)
  - name: string (required)
  - phone: string (required, unique)
  - email: string (nullable, unique)
  - preferences: jsonb (nullable)
  - lastVisit: Date (required)
  - deletedAt: Date (nullable)
  - Relaciones:
    - restaurant: Restaurant (ManyToOne)
    - orders: Order[] (OneToMany)
    - reservations: Reservation[] (OneToMany)
    - addresses: CustomerAddress[] (OneToMany)

- **CustomerAddress**

  - id: number (PK, required)
  - customerProfileId: FK → CustomerProfile (required)
  - street: string (required)
  - number: string (required)
  - apartment: string (nullable)
  - city: string (required)
  - state: string (required)
  - country: string (required)
  - zipCode: string (required)
  - reference: string (nullable)
  - isDefault: boolean (required, default: false)
  - latitude: number (nullable)
  - longitude: number (nullable)
  - deletedAt: Date (nullable)
  - Relaciones:
    - customerProfile: CustomerProfile (ManyToOne)
    - orders: Order[] (OneToMany)

// Empleados y Nómina

- **Employee**

  - id: number (PK, required)
  - tenantId: FK → Restaurant (required)
  - name: string (required)
  - role: string (required)
  - salary: number (required)
  - hireDate: Date (required)
  - status: enum (required, default: 'ACTIVE')
  - documentId: string (nullable, unique)
  - contactPhone: string (required)
  - emergencyContact: jsonb (required)
  - bankInfo: jsonb (nullable)
  - benefits: jsonb (required)
  - deletedAt: Date (nullable)
  - Relaciones:
    - restaurant: Restaurant (ManyToOne)
    - schedules: EmployeeSchedule[] (OneToMany)
    - timeOffRequests: EmployeeTimeOffRequest[] (OneToMany)
    - payrolls: EmployeePayroll[] (OneToMany)

- **EmployeeSchedule**

  - id: number (PK, required)
  - employeeId: FK → Employee (required)
  - startTime: Date (required)
  - endTime: Date (required)
  - status: enum (required, default: 'SCHEDULED')
  - type: enum (required, default: 'REGULAR')
  - notes: string (nullable)
  - createdBy: FK → User (required)
  - updatedBy: FK → User (nullable)
  - deletedAt: Date (nullable)
  - Relaciones:
    - employee: Employee (ManyToOne)
    - creator: User (ManyToOne)
    - updater: User (ManyToOne)
    - timeOffRequest: EmployeeTimeOffRequest (OneToOne)

- **EmployeeTimeOffRequest**

  - id: number (PK, required)
  - employeeId: FK → Employee (required)
  - startDate: Date (required)
  - endDate: Date (required)
  - type: enum (required)
  - status: enum (required, default: 'PENDING')
  - reason: string (required)
  - approvedBy: FK → User (nullable)
  - approvedAt: Date (nullable)
  - notes: string (nullable)
  - attachments: string[] (nullable)
  - deletedAt: Date (nullable)
  - Relaciones:
    - employee: Employee (ManyToOne)
    - approver: User (ManyToOne)
    - schedule: EmployeeSchedule (OneToOne)

- **EmployeePayroll**

  - id: number (PK, required)
  - employeeId: FK → Employee (required)
  - periodStart: Date (required)
  - periodEnd: Date (required)
  - baseSalary: number (required)
  - overtimeHours: number (required, default: 0)
  - overtimeRate: number (required)
  - reportedTips: number (required, default: 0)
  - deductions: jsonb (required)
  - benefits: jsonb (required)
  - totalPayment: number (required)
  - status: enum (required, default: 'DRAFT')
  - paidAt: Date (nullable)
  - paidBy: FK → User (nullable)
  - notes: string (nullable)
  - deletedAt: Date (nullable)
  - Relaciones:
    - employee: Employee (ManyToOne)
    - payer: User (ManyToOne)

// Otros

- **PromotionalCode**

  - id: number (PK, required)
  - tenantId: FK → Restaurant (required)
  - code: string (required, unique)
  - type: enum (required)
  - value: number (required)
  - description: string (required)
  - startDate: Date (required)
  - endDate: Date (required)
  - usageLimit: number (nullable)
  - currentUsage: number (required, default: 0)
  - minOrderValue: number (nullable)
  - maxDiscountAmount: number (nullable)
  - isActive: boolean (required, default: true)
  - deletedAt: Date (nullable)
  - Relaciones:
    - restaurant: Restaurant (ManyToOne)
    - usageHistory: PromoCodeUsage[] (OneToMany)

- **PromoCodeUsage**

  - id: number (PK, required)
  - promoCodeId: FK → PromotionalCode (required)
  - orderId: FK → Order (required)
  - usedAt: Date (required, default: now())
  - discountAmount: number (required)
  - Relaciones:
    - promoCode: PromotionalCode (ManyToOne)
    - order: Order (ManyToOne)

- **PreparationScreen**

  - id: number (PK, required)
  - tenantId: FK → Restaurant (required)
  - name: string (required)
  - description: string (nullable)
  - isActive: boolean (required, default: true)
  - displayOrder: number (required, default: 0)
  - deletedAt: Date (nullable)
  - Relaciones:
    - restaurant: Restaurant (ManyToOne)
    - products: Product[] (OneToMany)
    - orderItems: OrderItem[] (OneToMany)
    - preparationQueue: PreparationQueueItem[] (OneToMany)

- **PreparationQueueItem**

  - id: number (PK, required)
  - screenId: FK → PreparationScreen (required)
  - orderItemId: FK → OrderItem (required)
  - priority: number (required, default: 0)
  - status: enum (required, default: 'PENDING')
  - queuedAt: Date (required, default: now())
  - startedAt: Date (nullable)
  - completedAt: Date (nullable)
  - estimatedTime: number (required)
  - assignedTo: FK → User (nullable)
  - notes: string (nullable)
  - deletedAt: Date (nullable)
  - Relaciones:
    - screen: PreparationScreen (ManyToOne)
    - orderItem: OrderItem (ManyToOne)
    - assignedUser: User (ManyToOne)

- **UserProfile**

  - id: number (PK, required)
  - userId: FK → User (required, unique)
  - firstName: string (required)
  - lastName: string (required)
  - birthDate: Date (nullable)
  - gender: enum (nullable)
  - phoneNumber: string (required)
  - address: string (nullable)
  - city: string (nullable)
  - state: string (nullable)
  - country: string (nullable)
  - zipCode: string (nullable)
  - avatarId: FK → File (nullable)
  - emergencyContact: jsonb (required)
  - deletedAt: Date (nullable)
  - Relaciones:
    - user: User (OneToOne)
    - avatar: File (ManyToOne)

_Nota:_ Cada entidad puede incluir índices para `tenantId`, `email`, etc., para mejorar el
rendimiento.

## 5. Servicios y Lógica de Negocio

### 5.1 Servicios Core

- **AuthService**

  - Métodos: `validateLogin()`, `register()`, `refreshToken()`, `logout()`.
  - Manejo de tokens JWT y refresh tokens.

- **OrdersService**

  - Métodos: `createOrder()`, `updateOrder()`, `cancelOrder()`, `findAll()`, `findById()`.
  - Lógica de validación de horarios y estados de la orden.
  - Registro de cada modificación en `OrderModification`.
  - Manejo de números de orden diarios:
    - Obtención y actualización atómica del siguiente número disponible
    - Reset automático al cambiar de día
    - Formateo del número (ej: padding con ceros)

- **TablesService**

  - Métodos para crear, actualizar y eliminar mesas, incluyendo `mergeTables()` y `splitTables()`.
  - Nuevos métodos:
    - `createTemporaryTable(data: CreateTemporaryTableDto)`
    - `convertToPermanent(id: number)`
    - `getTemporaryTables(filters?: TemporaryTableFilters)`

- **AreasService**

  - Métodos para gestionar áreas de un restaurante (crear, actualizar, listar).

- **MenuService**

  - Métodos: `createCategory()`, `createProduct()`, `updateProductStock()`, etc.
  - Gestión de variantes de productos mediante `ProductVariant`.

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

- **DailyCounterService**

  - Métodos:
    - `getNextNumber(type: string, date: Date)`: Obtiene y reserva el siguiente número
    - `getCurrentCounter(type: string, date: Date)`: Obtiene el contador actual
    - `resetCounter(type: string, date: Date)`: Reinicia el contador para una fecha

- **DiscountService**

  - Métodos:
    - `applyOrderDiscount(orderId, discountData)`
    - `applyItemDiscount(orderItemId, discountData)`
    - `validatePromoCode(code, orderId)`
    - `calculateOrderDiscounts(order)`
    - `removeDiscount(discountId)`
    - `createPromoCode(promoData)`
    - `deactivatePromoCode(promoId)`

- **BillRequestService**

  - Métodos:
    - `requestBill(tableId, options)`: Registra solicitud de cuenta
    - `printBill(billRequestId)`: Marca cuenta como impresa
    - `splitBill(billRequestId, splitOptions)`: Maneja división de cuenta
    - `getPendingBills()`: Lista cuentas pendientes
    - `getBillStatus(billRequestId)`: Obtiene estado actual
    - `cancelBillRequest(billRequestId)`: Cancela solicitud
  - Manejo de:
    - Diferentes tipos de división de cuenta
    - Impresión de tickets
    - Seguimiento de estado de pago
    - Notificaciones a personal

- **PreparationScreenService**

  - Métodos:
    - `createScreen(screenData)`
    - `updateScreen(screenId, data)`
    - `getActiveScreens()`
    - `getScreenQueue(screenId)`
    - `assignItemToUser(queueItemId, userId)`
    - `updateItemStatus(queueItemId, status)`
    - `getScreenMetrics(screenId)` // Tiempos promedio, items pendientes, etc.
    - `reorderScreens(screenOrder[])`

- **PreparationQueueService**

  - Métodos:
    - `addToQueue(orderItemId, screenId)`
    - `updateQueueItem(itemId, data)`
    - `getPendingItems(screenId)`
    - `getInProgressItems(screenId)`
    - `getReadyItems(screenId)`
    - `calculatePriority(orderItem)`
    - `estimateWaitTime(screenId)`
    - `reassignItem(itemId, newScreenId)`

- **EmployeeScheduleService**

  - Métodos:
    - `
