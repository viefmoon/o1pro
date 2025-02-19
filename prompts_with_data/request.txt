# Sistema de Gestión Integral para Restaurantes

## Descripción del Proyecto
Sistema móvil que permite gestionar todos los aspectos operativos de un restaurante o negocio de comida. La solución abarca desde la administración de órdenes, mesas, menú, empleados, reservas, reportes y facturación, hasta la realización de pedidos vía WhatsApp potenciados por inteligencia artificial.

Principales componentes:
- **Multitenancy**: cada restaurante (tenant) podrá operar de forma independiente y personalizar su propia configuración.
- Gestión de órdenes (creación, actualización, cancelación, programación y sincronización).
- Control de mesas y áreas.
- Manejo del menú (con categorías, subcategorías, artículos con imágenes, variantes, modificadores e ingredientes).
- Autenticación y roles (mediante JWT).
- Notificaciones y sincronización en tiempo real (usando WebSockets).
- Integración con impresoras térmicas para impresión de tickets.
- **Sincronización de una base de datos local con Supabase** (funcionamiento offline con respaldo periódico).
- **Integración de pagos** con Stripe y Clip (cada restaurante con su propia cuenta) y manejo de efectivo.
- Generación de resúmenes, reportes y facturación para la administración.
- Reservas de mesas y gestión de eventos.
- Gestión de empleados (check-in/checkout, control de ausencias, asignación de roles, cálculo de sueldos).
- Pedidos vía WhatsApp con IA (Gemini) para procesar texto, audios e interacciones.
- **Pantallas de preparación** (bar, cocina, etc.) configurables según se asocien a categorías o productos.

## Público Objetivo
- Restaurantes, bares, cafeterías y negocios de comida que buscan optimizar sus operaciones y mejorar la coordinación entre áreas.
- Negocios que desean una solución exclusivamente móvil para la gestión de órdenes, inventario, empleados, reservas y facturación.
- Empresas que buscan innovar en la toma de pedidos, incorporando la recepción automatizada de pedidos vía WhatsApp potenciada por IA.
- Empresas interesadas en licenciar esta plataforma a múltiples restaurantes dentro de una misma infraestructura (multitenant).

## Características Deseadas

### Gestión de Órdenes
- [ ] Crear, actualizar y cancelar órdenes
  - [ ] Soporte para órdenes dine-in, delivery y pickup (con filtrado de vista por cada tipo).
  - [ ] Inclusión de múltiples ítems por orden.
  - [ ] Gestión de ajustes (descuentos, cargos adicionales).
  - [ ] Seguimiento del estado de la orden en tiempo real.
  - [ ] Manejo de propinas (opcional).
    - [ ] Configuración regional de impuestos y propinas.
  - [ ] **Programar órdenes** para un horario específico (ej. pedidos futuros).
    - [ ] Distinción visual de órdenes programadas (se muestran ocultas en la vista principal y cuentan con una sección dedicada para su gestión).
    - [ ] Alerta temprana al personal antes del horario de la orden programada, mostrada en pantallas de preparación.
- [ ] **Formas de pago**
  - [ ] **Clip** para cobros presenciales (integración con lectores físicos).
  - [ ] **Efectivo** para pagos en el local.
  - [ ] **Stripe** para pagos online (delivery y pickup).
- [ ] **Descuentos**
  - [ ] Posibilidad de asignar descuentos a artículos específicos o a la orden completa.
  - [ ] Descuentos definidos como monto fijo o porcentaje.
  - [ ] Estos descuentos se reflejan automáticamente en el total de la orden y en la facturación final.
- [ ] **Estatus de productos y órdenes**
  - [ ] Cada producto (ítem de orden) tendrá un estatus de preparación (pendiente, en preparación, listo, entregado, etc.).
  - [ ] El estatus global de la orden dependerá de los estatus de todos sus ítems (ej. una orden se considera “completada” cuando todos los productos estén listos/entregados).

### Pantallas de Preparación (Cocina/Bar/etc.)
- [ ] Creación dinámica de pantallas según sea necesario (bar, parrilla, postres, etc.), asociadas a productos, categorías o subcategorías.
- [ ] Visualización de las órdenes y productos pendientes, en preparación y completados.
- [ ] Actualización de estados de los productos (ej. "en preparación", "listo").
- [ ] **Alertas de tiempo** (un único umbral global para el restaurante) cuando un producto o una orden exceda el tiempo configurado.
- [ ] Filtrar u ordenar las órdenes según categorías o tiempo de espera.
- [ ] Notificaciones al personal de servicio cuando un pedido esté listo.

### Control de Mesas y Áreas
- [ ] Gestión de mesas y áreas (salón, bar, etc.)
  - [ ] Asignación de mesas a órdenes.
  - [ ] Seguimiento de mesas disponibles y ocupadas.
  - [ ] Capacidad para combinar y dividir mesas.

### Manejo del Menú
- [ ] Gestión de productos y categorías
  - [ ] Creación y administración de categorías y subcategorías.
  - [ ] Configuración de artículos con imágenes, descripciones y precios.
  - [ ] Definición de variantes y modificadores (ingredientes extra, quitar ingredientes).
  - [ ] Gestión de inventario (stock) a nivel de artículo y variante.
  - [ ] Opciones de menú dinámicas para temporadas o eventos especiales.
- [ ] **Personalización por restaurante** (tenant)
  - [ ] Personalizar menú, colores, logos y otros aspectos de marca de forma independiente.

### Roles y Autenticación
- [ ] Sistema de autenticación basado en JWT
  - [ ] Definición de roles (administrador, mesero, chef de pizza, chef de hamburguesa, bar, etc.).
  - [ ] Control de acceso y permisos basado en roles.
  - [ ] Registro y recuperación de contraseña.

### Gestión de Empleados, Turnos y Sueldos
- [ ] Registro y administración de empleados
  - [ ] Asignación de roles y permisos.
  - [ ] Check-in/Check-out (control de asistencia).
  - [ ] Gestión de ausencias y reportes de inasistencias.
  - [ ] Visualización y asignación de horarios y turnos.
  - [ ] Cálculo automático de horas trabajadas para nómina.
- [ ] **Cálculo de sueldos**
  - [ ] Cada empleado tiene un sueldo base asignado.
  - [ ] Cálculo del sueldo en base a turnos trabajados, descuentos (ausencias, retardos) y extras (bonos, comisiones, etc.).
  - [ ] Generación de un reporte de sueldos por período (semanal, quincenal, mensual).
  - [ ] Opción de visualizar los totales y conceptos para cada empleado.

### Reservas y Gestión de Eventos
- [ ] Sistema de reservas de mesas
  - [ ] Reservas online a través de la aplicación o vía web.
  - [ ] Gestión de disponibilidad de mesas y horarios.
  - [ ] Confirmación y recordatorios automáticos de reservas.
- [ ] Gestión opcional de eventos y promociones
  - [ ] Creación y difusión de ofertas o promociones especiales.
  - [ ] Registro de inscripciones/confirmaciones de asistencia a eventos.

### Reportes, Resúmenes y Facturación
- [ ] Generación de reportes y resúmenes
  - [ ] Reportes de ventas (diarios, semanales, mensuales).
  - [ ] Reportes de órdenes (completadas, canceladas, en curso).
  - [ ] Reportes de inventario y consumo de productos.
  - [ ] Reportes de asistencia y turnos de personal.
  - [ ] Exportación en formatos PDF o CSV.
  - [ ] Herramientas de analítica (tendencias de ventas, productos más vendidos).
- [ ] **Sistema de Facturación por Restaurante**
  - [ ] Configuración de datos fiscales para cada tenant.
  - [ ] Generación de facturas y comprobantes.
  - [ ] Control de numeración de facturas por restaurante.
  - [ ] Conciliación de pagos (Stripe, Clip / Métodos offline).

### Integración de Pagos
- [ ] **Stripe** (online)
  - [ ] Soporte para pagos en línea (delivery y pickup).
  - [ ] Gestión de transacciones y conciliación de pagos.
  - [ ] Manejo de reembolsos y cancelaciones parciales.
- [ ] **Clip** (pagos en sitio)
  - [ ] Conexión a lectores de tarjetas Clip para cobros presenciales.
  - [ ] Generar la operación de cobro desde la app y recibir confirmación de pago en tiempo real.
  - [ ] Cada restaurante utilizará su propia cuenta de Clip.
  - [ ] Manejo de errores o fallas de lector en tiempo real.
- [ ] **Efectivo** (pagos en sitio)

### Gestión de Base de Datos y Sincronización
- [ ] Base de datos local en la aplicación móvil
  - [ ] Funcionamiento 100% offline.
  - [ ] Sincronización periódica (configurable) con Supabase para respaldo y actualización centralizada.
  - [ ] Resolución de conflictos de datos conservando la última edición y registrando el historial de cambios asociados a cada usuario.
  - [ ] No se contemplan datos sensibles más allá de la autenticación.

### Notificaciones y Sincronización en Tiempo Real
- [ ] Integración de WebSockets para actualizar el estado de órdenes y notificaciones en tiempo real.
  - [ ] Notificaciones push a usuarios (por rol) para pedidos nuevos o cambios de estado.
  - [ ] Alertas al personal de cocina o bar cuando se crea o modifica una orden relevante.

### Pedidos vía WhatsApp con IA
- [ ] Recepción de Pedidos por WhatsApp
  - [ ] Integración con línea de WhatsApp Business ya existente.
  - [ ] Verificación del webhook con token de verificación.
  - [ ] Módulo de gestión de la cuenta de WhatsApp Business.
- [ ] Procesamiento Automatizado de Mensajes
  - [ ] Procesamiento de mensajes de texto para extraer la intención y detalles del pedido.
  - [ ] Transcripción de mensajes de audio utilizando modelos como Whisper y/o Gemini.
  - [ ] Manejo de mensajes interactivos (botones y listas) para confirmar, modificar o cancelar pedidos.
- [ ] Integración de Agentes de IA
  - [ ] Utilización de múltiples agentes (Router, Order Mapper, Query) que analizan y procesan la información del pedido.
  - [ ] Extracción y mapeo de productos del menú mediante similitud textual y normalización.
  - [ ] Módulo de entrenamiento y mejora continua de modelos (feedback loop).
- [ ] Gestión de Flujo y Control
  - [ ] Encolamiento de mensajes por cliente para procesarlos secuencialmente.
  - [ ] Control de tasa de mensajes (rate limiting) para prevenir abusos o spam.
  - [ ] Envío de notificaciones y mensajes de confirmación (ej. bienvenida, resúmenes de pedido, errores).
- [ ] **Almacenamiento de conversaciones y auditoría**
  - [ ] Guardar historial de conversaciones de WhatsApp para referencia y monitoreo.
  - [ ] Interfaz con filtros (fecha, número de teléfono, palabras clave) para revisar las conversaciones.
  - [ ] Posibilidad de enviar mensajes directamente desde la app al WhatsApp del cliente.
- [ ] Integración con Otros Módulos
  - [ ] Pedidos recibidos por WhatsApp se integran con la gestión de órdenes y el sistema de pagos.
  - [ ] Permitir a los clientes actualizar su información de entrega mediante OTP o enlaces de verificación.

### Seeders y Configuración
- [ ] Scripts de inicialización de la base de datos
  - [ ] Inserción de datos básicos (productos, roles, mesas, usuarios).
  - [ ] Configuración inicial de parámetros (impuestos, propinas, zonas de reparto).

### Monitoreo y Mantenimiento (Sugerido)
- [ ] Dashboard interno para monitorear el estado del sistema.
  - [ ] Alertas en caso de falla de sincronización o caída de servicios.
  - [ ] Métricas de rendimiento (tiempo de respuesta, uso de CPU, memoria).
- [ ] Logs de actividad
  - [ ] Registro de operaciones importantes (creación/edición de órdenes, inicios de sesión).
  - [ ] Integración con herramientas de monitoreo (Datadog, New Relic, etc.) (opcional).

## Requerimientos de Diseño
- [ ] **Diseño con tema oscuro y claro** (opciones de personalización a nivel de usuario o de restaurante).
- [ ] Interfaz intuitiva y responsiva, optimizada para dispositivos móviles.
  - [ ] Diseño adaptativo para diversas resoluciones de pantalla (iOS y Android).
- [ ] Arquitectura escalable y modular basada en NestJS (backend) y React Native (frontend).
- [ ] Soporte completo para funcionamiento offline y sincronización asíncrona con Supabase.
- [ ] Experiencia de usuario enfocada en la rapidez y simplicidad (especialmente en entornos de alta rotación).
- [ ] Integración con características nativas de móvil (notificaciones push, acceso a GPS, cámara para escanear códigos QR, etc.).
- [ ] Consideraciones de accesibilidad (contraste, tamaños de fuente, compatibilidad con lectores de pantalla).
- [ ] **Personalización visual** para cada restaurante (colores, logos, estilos).

## Otras Notas
- No se requiere cumplimiento específico de leyes de protección de datos más allá de buenas prácticas para credenciales.
- El módulo de pedidos vía WhatsApp potencia el proceso de toma de pedidos con texto, audio e interacciones automatizadas.
- Se prevé una arquitectura multitenant para alojar múltiples restaurantes en la misma plataforma, cada uno con su propio sistema de facturación y configuración.
- Se recomienda planificar la infraestructura para alta escalabilidad y resiliencia (posible uso de contenedores Docker o servicios cloud).
