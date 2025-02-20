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
  - Status
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

# Enums del Sistema

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

*Nota:* Se utilizará la convención de nomenclatura PascalCase para módulos, controladores y servicios; y camelCase para métodos, variables y propiedades.

## 3. Especificación de API
A continuación se definen los módulos y sus endpoints principales. *Nota:* Todos los endpoints se prefijan con `/api/v1` (o la versión que aplique).

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
- **Entidad:** `UserEntity` (campos: email, contraseña, provider, roleId, statusId, etc.)

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

- **Restaurant (Tenant)**
  - id: number
  - name: string
  - logoId: FK → File (opcional)
  - Relaciones:
    - users: User[] (OneToMany)
    - orders: Order[] (OneToMany)
    - tables: Table[] (OneToMany)
    - products: Product[] (OneToMany)
    - areas: Area[] (OneToMany)
    - employees: Employee[] (OneToMany)
    - logo: File (ManyToOne)

- **User**
  - id: number
  - email: string | null
  - password: string
  - provider: enum (email, google, etc.)
  - roleId: FK → Role
  - statusId: FK → Status
  - tenantId: FK → Restaurant (para multi-tenant)
  - deletedAt: Date (soft delete)
  - Relaciones:
    - restaurant: Restaurant (ManyToOne)
    - role: Role (ManyToOne)
    - status: Status (ManyToOne)
    - orders: Order[] (OneToMany)
    - orderModifications: OrderModification[] (OneToMany)

- **Role**
  - id: number
  - name: string
  - Relaciones:
    - users: User[] (OneToMany)

- **Status**
  - id: number
  - name: string (active/inactive)
  - Relaciones:
    - users: User[] (OneToMany)

- **Area**
  - id: number
  - tenantId: FK → Restaurant
  - name: string
  - description: string (opcional)
  - deletedAt: Date (soft delete)
  - Relaciones:
    - restaurant: Restaurant (ManyToOne)
    - tables: Table[] (OneToMany)

- **Order**
  - id: number
  - tenantId: FK → Restaurant
  - userId: FK → User (quién creó)
  - tableId: FK → Table (opcional)
  - dailyNumber: number // Número de orden del día (ej: 001, 002, etc.)
  - dailyCounterId: FK → DailyCounter
  - scheduledAt: Date (si es pedido futuro)
  - status: enum (pending, preparing, etc.)
  - orderType: enum (DINE_IN, DELIVERY, PICKUP, DRIVE_THRU) // Tipo de orden
  - subtotal: number // Nuevo campo: total antes de descuentos
  - total: number // total después de descuentos
  - deletedAt: Date
  - customerProfileId: FK → CustomerProfile (opcional)
  - deliveryAddressId: FK → CustomerAddress (opcional, requerido si orderType es DELIVERY)
  - isWhatsAppOrder: boolean // Indica si el pedido fue realizado por WhatsApp
  - deliveryNotes: string (opcional) // Notas adicionales para delivery
  - discounts: OrderDiscount[] (OneToMany) // Nueva relación
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

- **OrderModification**
  - id: number
  - orderId: FK → Order
  - userId: FK → User (usuario que realizó la modificación)
  - modificationType: string (ej.: creación, actualización, cancelación)
  - modificationTimestamp: Date
  - details: string (descripción o detalles de la modificación)
  - deletedAt: Date (opcional para soft delete)
  - Relaciones:
    - order: Order (ManyToOne)
    - user: User (ManyToOne)

- **OrderItem**
  - id: number
  - orderId: FK → Order
  - productId: FK → Product
  - quantity: number
  - basePrice: number // Precio base del producto
  - finalPrice: number // Precio después de descuentos
  - status: enum (OrderItemStatus)
  - statusChangedAt: Date
  - preparationNotes: string (opcional)
  - deletedAt: Date
  - discounts: OrderItemDiscount[] (OneToMany) // Nueva relación
  - preparationScreenId: FK → PreparationScreen // Nueva relación
  - Relaciones:
    - order: Order (ManyToOne)
    - product: Product (ManyToOne)
    - modifiers: OrderItemModifier[] (OneToMany)
    - statusHistory: OrderItemStatusHistory[] (OneToMany)
    - preparationScreen: PreparationScreen (ManyToOne)
    - preparationQueue: PreparationQueueItem (OneToOne)

- **OrderItemModifier**
  - id: number
  - orderItemId: FK → OrderItem
  - modifierName: string
  - additionalPrice: number
  - deletedAt: Date
  - Relaciones:
    - orderItem: OrderItem (ManyToOne)

- **Table**
  - id: number
  - tenantId: FK → Restaurant
  - name: string
  - areaId: FK → Area
  - isTemporary: boolean // Indica si es una mesa temporal
  - temporaryIdentifier: string (opcional) // Identificador descriptivo para mesas temporales (ej: "Evento Boda 12/12", "Extensión Terraza")
  - parentTableId: FK → Table (opcional) // Para mesas temporales derivadas de una mesa principal
  - deletedAt: Date
  - Relaciones:
    - restaurant: Restaurant (ManyToOne)
    - area: Area (ManyToOne)
    - orders: Order[] (OneToMany)
    - reservations: Reservation[] (OneToMany)
    - parentTable: Table (ManyToOne)
    - temporaryTables: Table[] (OneToMany)

- **Product**
  - id: number
  - tenantId: FK → Restaurant
  - name: string
  - price: number
  - stock: number
  - subCategoryId: FK → SubCategory
  - photoId: FK → File (opcional)
  - deletedAt: Date
  - preparationScreenId: FK → PreparationScreen // Nueva relación
  - estimatedPrepTime: number // Tiempo estimado de preparación en minutos
  - Relaciones:
    - restaurant: Restaurant (ManyToOne)
    - subCategory: SubCategory (ManyToOne)
    - variants: ProductVariant[] (OneToMany)
    - orderItems: OrderItem[] (OneToMany)
    - preparationScreen: PreparationScreen (ManyToOne)

- **ProductVariant**
  - id: number
  - productId: FK → Product
  - name: string
  - additionalPrice: number
  - deletedAt: Date
  - Relaciones:
    - product: Product (ManyToOne)

- **Category**
  - id: number
  - tenantId: FK → Restaurant
  - name: string
  - description: string (opcional)
  - deletedAt: Date (soft delete)
  - Relaciones:
    - restaurant: Restaurant (ManyToOne)
    - subCategories: SubCategory[] (OneToMany)

- **SubCategory**
  - id: number
  - tenantId: FK → Restaurant
  - categoryId: FK → Category
  - name: string
  - description: string (opcional)
  - deletedAt: Date (soft delete)
  - Relaciones:
    - restaurant: Restaurant (ManyToOne)
    - category: Category (ManyToOne)
    - products: Product[] (OneToMany)

- **Reservation**
  - id: number
  - tenantId: FK → Restaurant
  - tableId: FK → Table
  - startTime: Date
  - endTime: Date
  - customerName: string
  - deletedAt: Date
  - Relaciones:
    - restaurant: Restaurant (ManyToOne)
    - table: Table (ManyToOne)

- **Payment**
  - id: number
  - orderId: FK → Order
  - method: enum (stripe, clip, cash)
  - amount: number
  - status: enum (captured, refunded, etc.)
  - deletedAt: Date
  - Relaciones:
    - order: Order (OneToOne)

- **Employee**
  - id: number
  - tenantId: FK → Restaurant
  - name: string
  - role: string (chef, mesero, etc.)
  - salary: number
  - hireDate: Date
  - status: enum (ACTIVE, INACTIVE, ON_LEAVE)
  - documentId: string (opcional) // Documento de identidad
  - contactPhone: string
  - emergencyContact: jsonb // Contacto de emergencia
  - bankInfo: jsonb (opcional) // Información bancaria
  - benefits: jsonb // Beneficios asignados
  - deletedAt: Date
  - Relaciones:
    - restaurant: Restaurant (ManyToOne)
    - schedules: EmployeeSchedule[] (OneToMany)
    - timeOffRequests: EmployeeTimeOffRequest[] (OneToMany)
    - payrolls: EmployeePayroll[] (OneToMany)

- **File**
  - id: number
  - path: string
  - filename: string
  - mimetype: string
  - size: number
  - tenantId: FK → Restaurant
  - deletedAt: Date (soft delete)
  - Relaciones:
    - restaurant: Restaurant (ManyToOne)
    - restaurantLogos: Restaurant[] (OneToMany)
    - productPhotos: Product[] (OneToMany)

- **DailyCounter**
  - id: number
  - tenantId: FK → Restaurant
  - date: Date // Fecha del contador (YYYY-MM-DD)
  - type: enum ('order') // Para futura extensibilidad
  - currentNumber: number // Último número usado
  - prefix: string (opcional) // Prefijo personalizable (ej: 'ORD-')
  - deletedAt: Date (soft delete)
  - Relaciones:
    - restaurant: Restaurant (ManyToOne)
    - orders: Order[] (OneToMany)

- **CustomerProfile**
  - id: number
  - tenantId: FK → Restaurant
  - name: string
  - phone: string
  - email: string (opcional)
  - preferences: jsonb (opcional)
  - lastVisit: Date
  - deletedAt: Date (soft delete)
  - Relaciones:
    - restaurant: Restaurant (ManyToOne)
    - orders: Order[] (OneToMany)
    - reservations: Reservation[] (OneToMany)
    - addresses: CustomerAddress[] (OneToMany)

- **CustomerAddress**
  - id: number
  - customerProfileId: FK → CustomerProfile
  - street: string
  - number: string
  - apartment: string (opcional)
  - city: string
  - state: string
  - country: string
  - zipCode: string
  - reference: string (opcional)
  - isDefault: boolean
  - latitude: number (opcional)
  - longitude: number (opcional)
  - deletedAt: Date (soft delete)
  - Relaciones:
    - customerProfile: CustomerProfile (ManyToOne)
    - orders: Order[] (OneToMany)

- **OrderItemStatusHistory**
  - id: number
  - orderItemId: FK → OrderItem
  - status: enum (OrderItemStatus)
  - changedAt: Date
  - changedBy: FK → User
  - notes: string (opcional)
  - Relaciones:
    - orderItem: OrderItem (ManyToOne)
    - user: User (ManyToOne)

- **OrderDiscount**
  - id: number
  - orderId: FK → Order
  - type: enum (DiscountType)
  - value: number // Valor del descuento (porcentaje o monto fijo)
  - code: string (opcional) // Para descuentos con código promocional
  - description: string
  - authorizedBy: FK → User (opcional)
  - appliedAt: Date
  - expiresAt: Date (opcional)
  - minOrderValue: number (opcional)
  - maxDiscountAmount: number (opcional)
  - deletedAt: Date
  - Relaciones:
    - order: Order (ManyToOne)
    - authorizer: User (ManyToOne)

- **OrderItemDiscount**
  - id: number
  - orderItemId: FK → OrderItem
  - type: enum (DiscountType)
  - value: number
  - description: string
  - authorizedBy: FK → User (opcional)
  - appliedAt: Date
  - deletedAt: Date
  - Relaciones:
    - orderItem: OrderItem (ManyToOne)
    - authorizer: User (ManyToOne)

- **PromotionalCode**
  - id: number
  - tenantId: FK → Restaurant
  - code: string
  - type: enum (DiscountType)
  - value: number
  - description: string
  - startDate: Date
  - endDate: Date
  - usageLimit: number (opcional)
  - currentUsage: number
  - minOrderValue: number (opcional)
  - maxDiscountAmount: number (opcional)
  - isActive: boolean
  - deletedAt: Date
  - Relaciones:
    - restaurant: Restaurant (ManyToOne)
    - usageHistory: PromoCodeUsage[] (OneToMany)

- **PromoCodeUsage**
  - id: number
  - promoCodeId: FK → PromotionalCode
  - orderId: FK → Order
  - usedAt: Date
  - discountAmount: number
  - Relaciones:
    - promoCode: PromotionalCode (ManyToOne)
    - order: Order (ManyToOne)

- **BillRequest**
  - id: number
  - tenantId: FK → Restaurant
  - orderId: FK → Order
  - tableId: FK → Table
  - requestedAt: Date
  - printedAt: Date (opcional)
  - status: enum (REQUESTED, PRINTED, PAID, CANCELLED)
  - requestedBy: FK → User
  - printedBy: FK → User (opcional)
  - amount: number
  - splitType: enum (FULL, SPLIT_EQUAL, SPLIT_ITEMS) (opcional)
  - splitAmount: number (opcional, para casos de división)
  - notes: string (opcional)
  - deletedAt: Date
  - Relaciones:
    - order: Order (ManyToOne)
    - table: Table (ManyToOne)
    - requester: User (ManyToOne)
    - printer: User (ManyToOne)
    - splitDetails: BillSplitDetail[] (OneToMany)

- **BillSplitDetail**
  - id: number
  - billRequestId: FK → BillRequest
  - items: jsonb // Array de OrderItems incluidos
  - amount: number
  - status: enum (PENDING, PAID)
  - paymentId: FK → Payment (opcional)
  - deletedAt: Date
  - Relaciones:
    - billRequest: BillRequest (ManyToOne)
    - payment: Payment (ManyToOne)

- **PreparationScreen**
  - id: number
  - tenantId: FK → Restaurant
  - name: string // Ej: "Cocina Caliente", "Bar", "Cocina Fría"
  - description: string (opcional)
  - isActive: boolean
  - displayOrder: number // Para ordenar múltiples pantallas
  - deletedAt: Date (soft delete)
  - Relaciones:
    - restaurant: Restaurant (ManyToOne)
    - products: Product[] (OneToMany)
    - orderItems: OrderItem[] (OneToMany)
    - preparationQueue: PreparationQueueItem[] (OneToMany)

- **PreparationQueueItem**
  - id: number
  - screenId: FK → PreparationScreen
  - orderItemId: FK → OrderItem
  - priority: number // Para items urgentes o VIP
  - status: enum (PENDING, IN_PROGRESS, READY, DELIVERED)
  - queuedAt: Date
  - startedAt: Date (opcional)
  - completedAt: Date (opcional)
  - estimatedTime: number // Tiempo estimado en minutos
  - assignedTo: FK → User (opcional)
  - notes: string (opcional)
  - deletedAt: Date (soft delete)
  - Relaciones:
    - screen: PreparationScreen (ManyToOne)
    - orderItem: OrderItem (ManyToOne)
    - assignedUser: User (ManyToOne)

- **EmployeeSchedule**
  - id: number
  - employeeId: FK → Employee
  - startTime: Date
  - endTime: Date
  - status: enum (SCHEDULED, IN_PROGRESS, COMPLETED, CANCELLED)
  - type: enum (REGULAR, OVERTIME, HOLIDAY)
  - notes: string (opcional)
  - createdBy: FK → User
  - updatedBy: FK → User (opcional)
  - deletedAt: Date (soft delete)
  - Relaciones:
    - employee: Employee (ManyToOne)
    - creator: User (ManyToOne)
    - updater: User (ManyToOne)
    - timeOffRequest: EmployeeTimeOffRequest (OneToOne, opcional)

- **EmployeeTimeOffRequest**
  - id: number
  - employeeId: FK → Employee
  - startDate: Date
  - endDate: Date
  - type: enum (VACATION, SICK_LEAVE, PERSONAL, OTHER)
  - status: enum (PENDING, APPROVED, REJECTED, CANCELLED)
  - reason: string
  - approvedBy: FK → User (opcional)
  - approvedAt: Date (opcional)
  - notes: string (opcional)
  - attachments: string[] (opcional, URLs de documentos)
  - deletedAt: Date (soft delete)
  - Relaciones:
    - employee: Employee (ManyToOne)
    - approver: User (ManyToOne)
    - schedule: EmployeeSchedule (OneToOne, opcional)

- **EmployeePayroll**
  - id: number
  - employeeId: FK → Employee
  - periodStart: Date
  - periodEnd: Date
  - baseSalary: number
  - overtimeHours: number
  - overtimeRate: number
  - reportedTips: number
  - deductions: jsonb // Deducciones varias
  - benefits: jsonb // Beneficios adicionales
  - totalPayment: number
  - status: enum (DRAFT, APPROVED, PAID)
  - paidAt: Date (opcional)
  - paidBy: FK → User (opcional)
  - notes: string (opcional)
  - deletedAt: Date (soft delete)
  - Relaciones:
    - employee: Employee (ManyToOne)
    - payer: User (ManyToOne)

*Nota:* Cada entidad puede incluir índices para `tenantId`, `email`, etc., para mejorar el 
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
    - `createSchedule(employeeId, scheduleData)`
    - `updateSchedule(scheduleId, data)`
    - `cancelSchedule(scheduleId)`
    - `getEmployeeSchedules(employeeId, dateRange)`
    - `getScheduleConflicts(scheduleData)`
    - `getSchedulesByDateRange(startDate, endDate)`
    - `markScheduleCompleted(scheduleId)`

- **EmployeeTimeOffService**
  - Métodos:
    - `requestTimeOff(employeeId, requestData)`
    - `approveRequest(requestId, approverId)`
    - `rejectRequest(requestId, approverId, reason)`
    - `cancelRequest(requestId)`
    - `getPendingRequests()`
    - `getEmployeeRequests(employeeId)`
    - `checkAvailability(employeeId, dateRange)`

- **PayrollService**
  - Métodos:
    - `generatePayroll(employeeId, periodRange)`
    - `calculateOvertimeHours(employeeId, periodRange)`
    - `addDeduction(payrollId, deductionData)`
    - `addBenefit(payrollId, benefitData)`
    - `approvePayroll(payrollId)`
    - `processPayment(payrollId)`
    - `generatePayrollReport(periodRange)`
    - `calculateTotalPayroll(periodRange)`

**Manejo de Casos de Error:**

- Se lanza `HttpException` con códigos 404, 422, 409, etc., según el caso.
- Uso de custom exceptions para conflictos de negocio (ej.: mesa ya ocupada).

## 6. Autenticación y Autorización

**Estrategia:**

- Uso de dos estrategias de JWT: una para token de acceso y otra para token de refresco.
- Roles y Guards: Decorador `@Roles(RoleEnum.admin)` y guard que valida si `request.user.role.id` 
coincide.

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
  Uso de transformaciones (por ejemplo, `@Transform(lowerCaseTransformer)`) y exclusión de campos 
  sensibles con `@Exclude`.

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
  Uso del logger integrado de NestJS con niveles (log, error, warn) y posible integración con 
  servicios externos (Datadog).

- **Monitoreo:**  
  Health checks en `/health` para verificar conectividad a la DB, Supabase, etc., y envío de alertas 
  mediante Slack u otros canales.

## 11. Documentación API

- **Swagger/OpenAPI:**  
  Configurado en `main.ts` mediante `SwaggerModule` y `DocumentBuilder`, documentando cada endpoint, 
  DTO, parámetros y respuestas.
  
- **Convenciones:**  
  Inclusión de descripciones y ejemplos en los DTOs.
  
- **UI:**  
  La documentación accesible a través de `/docs`.

## 12. Despliegue y CI/CD

- **Entornos:**  
  Staging y Production, con variables de entorno para DB, JWT, etc.

- **Despliegue:**  
  Utilización de contenedores Docker y ejecución automática de migraciones y seeds (o mediante 
  script).

### Flujo de Solicitud y Pago de Cuenta
1. Cliente solicita la cuenta
   - Mesa actualiza estado a BILL_REQUESTED
   - Se crea registro en BillRequest
   - Se notifica a personal

2. Mesero imprime la cuenta
   - BillRequest actualiza estado a PRINTED
   - Se registra quién imprimió y cuándo
   - Se puede reimprimir si necesario

3. Proceso de división (opcional)
   - Cliente puede solicitar división
   - Sistema permite división por montos o items
   - Se crean múltiples BillSplitDetails

4. Proceso de pago
   - Cada parte puede pagarse independientemente
   - Se registra método de pago por cada parte
   - Sistema mantiene tracking hasta completar total

5. Cierre de cuenta
   - Al completar pagos, mesa libera estado
   - Se marca orden como completada
   - Se actualiza historial

**Validaciones para Mesas Temporales:**

- El identificador temporal debe ser único dentro del restaurante
- Si se especifica parentTableId, debe existir y no ser temporal
- No se pueden hacer reservas en mesas temporales más allá de su fecha de expiración
- Validar disponibilidad del nombre/número de mesa
- Al convertir a permanente, validar conflictos con otras mesas

**Casos de Uso:**

1. Eventos Especiales
   - Crear mesas temporales para un evento
   - Identificar claramente el evento (ej: "Mesa VIP Evento Corp")

2. Extensiones de Área
   - Derivar mesas temporales de mesas existentes
   - Mantener referencia a mesa principal
   - Identificar la extensión (ej: "Extensión Terraza Norte")

3. Pruebas de Layout
   - Crear configuraciones temporales
   - Evaluar eficiencia antes de hacer permanente
   - Identificar el propósito (ej: "Prueba Layout Verano")

4. Manejo de Picos de Demanda
   - Habilitar espacios adicionales temporalmente
   - Gestionar capacidad flexible
   - Identificar el propósito (ej: "Extra Temporada Alta")
