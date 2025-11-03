# Capítulo IV: Product Architecture Design

## 4.1 Design Concepts, Viewpoints & ER Diagrams

### 4.1.1 Principles Statements

La arquitectura de **FoodFlow** se construye bajo principios que aseguran coherencia con los objetivos definidos en los capítulos previos:

- **Desacoplamiento:** cada módulo opera como microservicio independiente para permitir evolución y despliegue aislado.
- **Seguridad por diseño:** control de acceso mediante JWT.
- **Mantenibilidad:** separación clara de capas (Controller, Service, Repository) y uso de patrones consistentes.
- **Usabilidad y accesibilidad:** la interfaz Angular sigue lineamientos Material Design y prioriza fluidez en dispositivos móviles.
- **Observabilidad:** logs y métricas por servicio para detectar errores o caídas.

Estos principios derivan de los requerimientos no funcionales (RNF) y la visión del Capítulo I orientada a reducir pérdidas y mejorar la toma de decisiones.


### 4.1.2 Approaches Statements, Architectural Styles & Patterns

El sistema adopta una arquitectura de **microservicios con enfoque DDD (Domain-Driven Design)**.  
Cada *bounded context* se implementa como un servicio independiente desplegable en contenedores.

| Bounded Context | Microservicio         | Descripción                                                                             |
|-----------------|-----------------------|-----------------------------------------------------------------------------------------|
| Inventory       | Inventory Service     | Control de productos y cantidades.                                                      |
| Menu            | Menu Service          | Administración de los platos de la carta, con sus precios, categorías y disponibilidad. |
| Report          | Reports Service       | Generación de informes financieros y operativos.                                        |
| Analytics       | Reports Service       | Visualización y análisis de métricas de negocio.                                        |
| IAM             | IAM Service           | Autenticación y autorización basada en roles.                                           |
| Profiles        | Profiles Service      | Datos personales y configuraciones de usuario.                                          |
| Subscription    | Subscriptions Service | Gestión de planes SaaS y periodos de servicio.                                          |
| Orders          | Orders Service        | Muestra información estática para pruebas, sin operaciones CRUD.                        |

**API Gateway** centraliza peticiones, aplica seguridad y distribuye tráfico hacia los microservicios internos.  
El estilo adoptado es **RESTful**, con comunicación **REST Client** y documentación de endpoints mediante **Swagger**.
Únicamente Inventory y Orders services se comunican con Apache Kafka.

#### Patrones aplicados:
- Repository + Service Layer: aislamiento de persistencia.
- Factory Method: instanciación controlada de entidades.
- Template Method: flujo de generación de reportes.
- Singleton: control del contexto de seguridad y configuración.

---

### 4.1.3 Context Diagram

El sistema tiene como actor principal al **dueño del restaurante**, quien accede desde una interfaz web Angular.

<img width="267" height="620" alt="contexto" src="https://github.com/user-attachments/assets/67481017-f88b-4c69-b8d5-e9dac63b09fe" />


**Flujo general:**

1. El usuario accede al frontend (Angular).
2. El API Gateway enruta solicitudes según el servicio.
3. Los microservicios procesan o devuelven datos desde la base MySQL.
4. El usuario recibe la respuesta o visualiza información consolidada en el dashboard.

---

### 4.1.4 Approach-Driven Viewpoints Diagrams

Las vistas arquitectónicas se organizan de acuerdo con el modelo **C4**:

- **Context View:** define límites entre usuario, frontend y backend.
- **Container View:** representa los microservicios y sus interacciones REST.
- **Component View:** muestra controladores, servicios y repositorios por dominio.
- **Code View:** refleja clases y entidades base (Java Spring Boot).

Estas vistas garantizan trazabilidad entre requerimientos y decisiones arquitectónicas, siguiendo el método **ADD**.

Diagrama de actvidad

<img width="3508" height="700" alt="Untitled diagram-2025-10-09-034604" src="https://github.com/user-attachments/assets/c55947bf-afc2-4729-8492-a3c308429ab6" />

Diagrama de estados

<img alt="Untitled diagram-2025-10-09-035453" src="https://github.com/user-attachments/assets/12a5a58e-0f0b-46bd-96e9-97859e9c63ab" />

Diagrama de clases 

<img alt="Untitled diagram-2025-10-09-034906" src="https://github.com/user-attachments/assets/7cd76b45-2db5-4829-947f-626c5e9c8989" />


<img alt="1" src="https://github.com/user-attachments/assets/215ac1e6-a2cf-402c-860a-5bc39099118a" />

<img alt="2" src="https://github.com/user-attachments/assets/7de7aabb-a9af-430c-8132-bd98b56e7e32" />

<img alt="3" src="https://github.com/user-attachments/assets/375c8a3e-7681-464c-b13c-a81289038fe0" />

<img alt="4" src="https://github.com/user-attachments/assets/7b090aca-b29f-4046-b750-085e7ca219ab" />

<img alt="5" src="https://github.com/user-attachments/assets/050f0fe3-540b-4dd1-ac65-31cc6efa786f" />


# Tabla de Características del Diagrama de Clases

## Servicio de Suscripción

| Clase                      | Tipo             | Descripción                                               | Propósito                                                                                                                                      |
|----------------------------|------------------|-----------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------|
| **SubscriptionController** | Controlador      | Maneja las peticiones HTTP relacionadas con suscripciones | Expone endpoints REST para que los usuarios puedan ver planes, suscribirse, consultar su suscripción y cancelarla                              |
| **SubscriptionService**    | Servicio         | Contiene la lógica de negocio de las suscripciones        | Orquesta el proceso completo: valida datos, procesa pagos, activa suscripciones y gestiona renovaciones                                        |
| **Subscription**           | Entidad          | Representa una suscripción activa de un usuario           | Almacena información de la suscripción individual: qué usuario, qué plan eligió, cuándo inicia/termina, estado del pago                        |
| **SubscriptionPlan**       | Entidad/Catálogo | Representa los planes de suscripción disponibles          | Define los diferentes planes que se ofrecen (Básico, Premium, Enterprise) con sus precios, características y límites                           |
| **SubscriptionRepository** | Repositorio      | Acceso a datos de suscripciones de usuarios               | Maneja operaciones CRUD de las **suscripciones activas/históricas de usuarios**. Cada vez que un usuario se suscribe, se crea un registro aquí |
| **PlanRepository**         | Repositorio      | Acceso a datos de planes disponibles                      | Maneja operaciones CRUD de los **planes que ofreces como negocio** (catálogo de productos). Son los planes base que no cambian por usuario     |
| **SubscriptionStatus**     | Enum             | Estados posibles de una suscripción                       | Rastrea el flujo: desde que el usuario selecciona un plan, valida pago, procesa, hasta que queda activa o falla                                |
| **BillingPeriod**          | Enum             | Períodos de facturación                                   | Define si el plan se cobra mensual, trimestral o anualmente                                                                                    |
| **PaymentClient**          | Cliente          | Interfaz para procesar pagos                              | Se comunica con una pasarela de pago externa (Stripe, PayPal, etc.) para procesar transacciones                                                |
| **PaymentData**            | DTO              | Datos de pago del usuario                                 | Encapsula información sensible: tarjeta, titular, fecha de expiración, CVV                                                                     |
| **PaymentResult**          | DTO              | Resultado del procesamiento de pago                       | Contiene si el pago fue exitoso, ID de transacción y mensajes de error si aplica                                                               |

---

## Servicio de Inventario

| Clase                   | Tipo        | Descripción                                      | Propósito                                                                                            |
|-------------------------|-------------|--------------------------------------------------|------------------------------------------------------------------------------------------------------|
| **InventoryController** | Controlador | Maneja peticiones HTTP de inventario             | Expone endpoints para registrar productos, actualizar stock y consultar inventario                   |
| **InventoryService**    | Servicio    | Lógica de negocio del inventario                 | Gestiona operaciones de productos: crear, actualizar stock, descontar ingredientes usados en órdenes |
| **Product**             | Entidad     | Representa un producto/ingrediente en inventario | Almacena información del producto: nombre, categoría, precio de compra, stock actual, proveedor      |
| **ProductStatus**       | Enum        | Estados del producto                             | Indica el estado del producto en el sistema: pendiente, registrado, stock disponible, bajo o agotado |
| **ProductRepository**   | Repositorio | Acceso a datos de productos                      | Maneja operaciones CRUD de productos en inventario                                                   |

---

## Servicio de Menú

| Clase              | Tipo        | Descripción                     | Propósito                                                                       |
|--------------------|-------------|---------------------------------|---------------------------------------------------------------------------------|
| **MenuController** | Controlador | Maneja peticiones HTTP del menú | Expone endpoints para crear platos y consultar el menú                          |
| **MenuService**    | Servicio    | Lógica de negocio del menú      | Gestiona la creación y consulta de platos disponibles para venta                |
| **Dish**           | Entidad     | Representa un plato del menú    | Almacena información del plato: nombre, descripción, precio de venta, categoría |
| **DishStatus**     | Enum        | Estados del plato               | Indica si el plato está en proceso de creación o ya está registrado en el menú  |
| **DishRepository** | Repositorio | Acceso a datos de platos        | Maneja operaciones CRUD de platos del menú                                      |

---

## Servicio de Órdenes

| Clase                | Tipo                   | Descripción                              | Propósito                                                                                       |
|----------------------|------------------------|------------------------------------------|-------------------------------------------------------------------------------------------------|
| **OrderController**  | Controlador            | Maneja peticiones HTTP de órdenes        | Expone endpoints para cargar órdenes, consultarlas y filtrar por rango de fechas                |
| **OrderService**     | Servicio               | Lógica de negocio de órdenes             | Procesa órdenes de venta, calcula totales, actualiza inventario y coordina con otros servicios  |
| **Order**            | Entidad                | Representa una orden de venta            | Almacena información de la orden: número, items vendidos, total, fecha, estado de procesamiento |
| **OrderItem**        | Entidad                | Representa un ítem dentro de una orden   | Detalle de cada plato vendido: qué plato, cantidad, precio unitario, subtotal                   |
| **OrderStatus**      | Enum                   | Estados de la orden                      | Rastrea el procesamiento: cargada, procesando, actualizando stock, registrada, completada       |
| **OrderRepository**  | Repositorio            | Acceso a datos de órdenes                | Maneja operaciones CRUD de órdenes, con filtros por fecha y estado                              |
| **MessagingService** | Servicio de mensajería | Publica eventos entre microservicios     | Comunica eventos importantes: orden procesada, actualización de stock, suscripción activada     |
| **StockUpdateEvent** | Evento                 | Evento de actualización de inventario    | Mensaje que se envía cuando una orden requiere descontar stock                                  |
| **InventoryClient**  | Cliente                | Interfaz para comunicarse con Inventario | Permite al servicio de órdenes consultar y actualizar el inventario de otros microservicios     |

---

## Servicio de Reportes

| Clase                 | Tipo        | Descripción                        | Propósito                                                                                                         |
|-----------------------|-------------|------------------------------------|-------------------------------------------------------------------------------------------------------------------|
| **ReportsController** | Controlador | Maneja peticiones HTTP de reportes | Expone endpoints para generar reportes de ingresos, pérdidas y consultar reportes almacenados                     |
| **ReportsService**    | Servicio    | Lógica de negocio de reportes      | Genera reportes consolidados obteniendo datos de órdenes e inventario, calcula métricas financieras               |
| **Report**            | Entidad     | Representa un reporte generado     | Almacena metadatos del reporte: tipo, período, estado de generación, datos calculados                             |
| **IncomeReport**      | DTO         | Reporte de ingresos                | Contiene métricas de ventas: total de ventas, cantidad de órdenes, promedio por orden, ventas por plato y por día |
| **LossReport**        | DTO         | Reporte de pérdidas/gastos         | Contiene métricas de compras: total de compras, cantidad de compras, gastos por categoría y por día               |
| **ReportType**        | Enum        | Tipo de reporte                    | Define si es un reporte de ingresos o de pérdidas/gastos                                                          |
| **ReportStatus**      | Enum        | Estados del reporte                | Rastrea el proceso de generación: solicitado, generando, obteniendo datos, calculando, generado, almacenado       |
| **ReportRepository**  | Repositorio | Acceso a datos de reportes         | Maneja operaciones CRUD de reportes generados, con filtros por tipo y fecha                                       |

## Diagrama de contenedores 

<img alt="contenedores" src="https://github.com/user-attachments/assets/2b0231c1-0869-40ee-ba53-8645b19eb128" />

## Diagrama de componentes

<img alt="c1" src="https://github.com/user-attachments/assets/5a142364-277e-4e40-984a-3a65dc4e2def" />

<img alt="c2" src="https://github.com/user-attachments/assets/12d23836-b139-49b1-88ec-9cfb1eaead3a" />

<img alt="c3" src="https://github.com/user-attachments/assets/9f8e1fd3-5006-4cb7-beb9-24bf79dac2ff" />

<img alt="c4" src="https://github.com/user-attachments/assets/40a2610b-85ad-444b-b91b-f518b05fc4b9" />

<img alt="c5" src="https://github.com/user-attachments/assets/ade772bc-0968-4f07-877c-70e7b11e9132" />

---

### 4.1.5 Relational Database Diagram

La base de datos **MySQL** mantiene las entidades descritas en los capítulos anteriores:

<img alt="Untitled diagram-2025-10-09-040617" src="https://github.com/user-attachments/assets/e360620e-0a50-40c8-951a-b29ca1736343" />

## Entidades del Sistema

### 1. USER (Usuario)
Almacena la información de los usuarios del sistema.

| Campo      | Tipo        | Descripción                                       |
|------------|-------------|---------------------------------------------------|
| id         | string (PK) | Identificador único del usuario                   |
| email      | string (UK) | Correo electrónico único del usuario              |
| name       | string      | Nombre completo del usuario                       |
| role       | string      | Rol del usuario en el sistema (admin, user, etc.) |
| created_at | datetime    | Fecha de creación del registro                    |
| updated_at | datetime    | Fecha de última actualización                     |

**Propósito**: Gestionar los usuarios que interactúan con el sistema y pueden adquirir suscripciones.

---

### 2. SUBSCRIPTION (Suscripción)
Registra las suscripciones activas e históricas de los usuarios.

| Campo          | Tipo        | Descripción                                                  |
|----------------|-------------|--------------------------------------------------------------|
| id             | string (PK) | Identificador único de la suscripción                        |
| user_id        | string (FK) | Referencia al usuario suscrito                               |
| plan_id        | string (FK) | Referencia al plan seleccionado                              |
| status         | string      | Estado de la suscripción (ACTIVO, EXPIRADO, CANCELADO, etc.) |
| start_date     | datetime    | Fecha de inicio de la suscripción                            |
| end_date       | datetime    | Fecha de finalización de la suscripción                      |
| auto_renew     | boolean     | Indica si la suscripción se renueva automáticamente          |
| payment_method | string      | Método de pago utilizado                                     |
| transaction_id | string      | ID de la transacción de pago                                 |
| created_at     | datetime    | Fecha de creación del registro                               |
| updated_at     | datetime    | Fecha de última actualización                                |

**Propósito**: Controlar el acceso de los usuarios a las funcionalidades del sistema según su plan activo.

---

### 3. SUBSCRIPTION_PLAN (Plan de Suscripción)
Define los diferentes planes de suscripción disponibles.

| Campo          | Tipo        | Descripción                                         |
|----------------|-------------|-----------------------------------------------------|
| id             | string (PK) | Identificador único del plan                        |
| name           | string      | Nombre del plan (Básico, Premium, Enterprise, etc.) |
| description    | string      | Descripción detallada del plan                      |
| price          | decimal     | Precio del plan                                     |
| billing_period | string      | Periodo de facturación (MENSUAL, TRIMESTRAL, ANUAL) |
| features       | string      | Lista de características incluidas (JSON o texto)   |
| max_users      | int         | Número máximo de usuarios permitidos                |
| max_orders     | int         | Número máximo de órdenes permitidas                 |
| created_at     | datetime    | Fecha de creación del registro                      |
| updated_at     | datetime    | Fecha de última actualización                       |

**Propósito**: Definir las opciones de suscripción disponibles con sus características y limitaciones.

---

### 4. PAYMENT_TRANSACTION (Transacción de Pago)
Registra todas las transacciones de pago relacionadas con suscripciones.

| Campo           | Tipo        | Descripción                                            |
|-----------------|-------------|--------------------------------------------------------|
| id              | string (PK) | Identificador único de la transacción                  |
| subscription_id | string (FK) | Referencia a la suscripción asociada                   |
| amount          | decimal     | Monto de la transacción                                |
| currency        | string      | Moneda utilizada (USD, PEN, etc.)                      |
| status          | string      | Estado de la transacción (EXITOSO, FALLIDO, PENDIENTE) |
| transaction_id  | string      | ID único de la transacción del proveedor de pagos      |
| payment_method  | string      | Método de pago usado (tarjeta, PayPal, etc.)           |
| processed_at    | datetime    | Fecha y hora en que se procesó el pago                 |
| created_at      | datetime    | Fecha de creación del registro                         |

**Propósito**: Mantener un historial completo de todas las transacciones de pago para auditoría y seguimiento.

---

### 5. PRODUCT (Producto)
Almacena información de los productos del inventario.

| Campo          | Tipo        | Descripción                                           |
|----------------|-------------|-------------------------------------------------------|
| id             | string (PK) | Identificador único del producto                      |
| name           | string      | Nombre del producto                                   |
| category       | string      | Categoría del producto                                |
| purchase_price | decimal     | Precio de compra del producto                         |
| current_stock  | int         | Stock actual disponible                               |
| unit           | string      | Unidad de medida (kg, unidad, litro, etc.)            |
| purchase_date  | datetime    | Fecha de compra del producto                          |
| supplier       | string      | Proveedor del producto                                |
| status         | string      | Estado del producto (DISPONIBLE, STOCK_BAJO, AGOTADO) |
| created_at     | datetime    | Fecha de creación del registro                        |
| updated_at     | datetime    | Fecha de última actualización                         |

**Propósito**: Gestionar el inventario de productos y materia prima del negocio.

---

### 6. DISH (Platillo)
Contiene los platillos disponibles en el menú.

| Campo       | Tipo        | Descripción                                               |
|-------------|-------------|-----------------------------------------------------------|
| id          | string (PK) | Identificador único del platillo                          |
| name        | string      | Nombre del platillo                                       |
| description | string      | Descripción del platillo                                  |
| price       | decimal     | Precio de venta del platillo                              |
| category    | string      | Categoría del platillo (entrada, principal, postre, etc.) |
| status      | string      | Estado del platillo (DISPONIBLE, NO_DISPONIBLE)           |
| created_at  | datetime    | Fecha de creación del registro                            |
| updated_at  | datetime    | Fecha de última actualización                             |

**Propósito**: Administrar el menú de platillos ofrecidos a los clientes.

---

### 7. ORDER (Orden)
Registra las órdenes realizadas por los clientes.

| Campo        | Tipo        | Descripción                                          |
|--------------|-------------|------------------------------------------------------|
| id           | string (PK) | Identificador único de la orden                      |
| order_number | string (UK) | Número único de orden para identificación            |
| total_amount | decimal     | Monto total de la orden                              |
| status       | string      | Estado de la orden (CARGADA, PROCESANDO, COMPLETADA) |
| order_date   | datetime    | Fecha y hora en que se realizó la orden              |
| processed_at | datetime    | Fecha y hora en que se procesó la orden              |
| created_at   | datetime    | Fecha de creación del registro                       |
| updated_at   | datetime    | Fecha de última actualización                        |

**Propósito**: Gestionar las órdenes de los clientes y su procesamiento.

---

### 8. ORDER_ITEM (Item de Orden)
Detalla los platillos incluidos en cada orden.

| Campo      | Tipo        | Descripción                           |
|------------|-------------|---------------------------------------|
| id         | string (PK) | Identificador único del item          |
| order_id   | string (FK) | Referencia a la orden principal       |
| dish_id    | string (FK) | Referencia al platillo ordenado       |
| dish_name  | string      | Nombre del platillo (desnormalizado)  |
| quantity   | int         | Cantidad de platillos ordenados       |
| unit_price | decimal     | Precio unitario del platillo          |
| subtotal   | decimal     | Subtotal del item (cantidad × precio) |
| created_at | datetime    | Fecha de creación del registro        |

**Propósito**: Detallar el contenido específico de cada orden.

---

### 9. REPORT (Reporte)
Almacena reportes generados del sistema.

| Campo         | Tipo        | Descripción                                          |
|---------------|-------------|------------------------------------------------------|
| id            | string (PK) | Identificador único del reporte                      |
| type          | string      | Tipo de reporte (INCOME, LOSS)                       |
| period_from   | date        | Fecha de inicio del periodo reportado                |
| period_to     | date        | Fecha de fin del periodo reportado                   |
| total_income  | decimal     | Total de ingresos en el periodo                      |
| total_loss    | decimal     | Total de pérdidas en el periodo                      |
| net_profit    | decimal     | Ganancia neta (ingresos - pérdidas)                  |
| profit_margin | decimal     | Margen de ganancia porcentual                        |
| status        | string      | Estado del reporte (GENERANDO, GENERADO, ALMACENADO) |
| generated_at  | datetime    | Fecha de generación del reporte                      |
| income_data   | string      | Datos de ingresos en formato JSON                    |
| loss_data     | string      | Datos de pérdidas en formato JSON                    |
| created_at    | datetime    | Fecha de creación del registro                       |
| updated_at    | datetime    | Fecha de última actualización                        |

**Propósito**: Generar y almacenar reportes financieros del negocio.

---

### 10. INCOME_DETAIL (Detalle de Ingresos)
Desglosa la información detallada de ingresos en un reporte.

| Campo               | Tipo        | Descripción                            |
|---------------------|-------------|----------------------------------------|
| id                  | string (PK) | Identificador único del detalle        |
| report_id           | string (FK) | Referencia al reporte principal        |
| total_sales         | decimal     | Total de ventas en el periodo          |
| order_count         | int         | Cantidad de órdenes procesadas         |
| average_order_value | decimal     | Valor promedio por orden               |
| sales_by_dish       | string      | Ventas desglosadas por platillo (JSON) |
| sales_by_day        | string      | Ventas desglosadas por día (JSON)      |
| created_at          | datetime    | Fecha de creación del registro         |

**Propósito**: Proporcionar análisis detallado de los ingresos generados.

---

### 11. LOSS_DETAIL (Detalle de Pérdidas)
Desglosa la información detallada de pérdidas en un reporte.

| Campo                 | Tipo        | Descripción                              |
|-----------------------|-------------|------------------------------------------|
| id                    | string (PK) | Identificador único del detalle          |
| report_id             | string (FK) | Referencia al reporte principal          |
| total_purchases       | decimal     | Total de compras en el periodo           |
| purchase_count        | int         | Cantidad de compras realizadas           |
| purchases_by_category | string      | Compras desglosadas por categoría (JSON) |
| purchases_by_day      | string      | Compras desglosadas por día (JSON)       |
| created_at            | datetime    | Fecha de creación del registro           |

**Propósito**: Proporcionar análisis detallado de las pérdidas o gastos incurridos.

---

## Relaciones entre Entidades

| Relación                           | Cardinalidad | Descripción                                                                                              |
|------------------------------------|--------------|----------------------------------------------------------------------------------------------------------|
| USER → SUBSCRIPTION                | 1:N          | Un usuario puede tener múltiples suscripciones (historial de suscripciones actuales y pasadas)           |
| SUBSCRIPTION → SUBSCRIPTION_PLAN   | N:1          | Cada suscripción pertenece a un plan específico; un plan puede ser usado por múltiples suscripciones     |
| SUBSCRIPTION → PAYMENT_TRANSACTION | 1:N          | Una suscripción puede generar múltiples transacciones (pagos iniciales, renovaciones, intentos fallidos) |
| ORDER → ORDER_ITEM                 | 1:N          | Una orden contiene múltiples items; cada item pertenece a una única orden                                |
| DISH → ORDER_ITEM                  | 1:N          | Un platillo puede estar en múltiples items de orden; cada item referencia un único platillo              |
| REPORT → INCOME_DETAIL             | 1:N          | Un reporte puede tener múltiples detalles de ingresos (aunque típicamente será 1:1)                      |
| REPORT → LOSS_DETAIL               | 1:N          | Un reporte puede tener múltiples detalles de pérdidas (aunque típicamente será 1:1)                      |

---

### 4.1.6 Design Patterns

| Microservicio | Patrón principal           | Finalidad                                   |
|---------------|----------------------------|---------------------------------------------|
| Inventory     | Repository / Observer      | Sincronización de stock.                    |
| Menu          | Repository / Factory       | Alta, baja y edición de platos de la carta. |
| Reports       | Strategy / Template Method | Generación y formato de informes.           |
| IAM           | Singleton / Proxy          | Control de autenticación.                   |
| Subscriptions | Repository / Observer      | Renovaciones y vencimientos.                |
| Orders        | Repository (solo lectura)  | Consulta de datos estáticos de ejemplo.     |

---

### 4.1.7 Tactics

| Atributo de calidad | Táctica aplicada                                 |
|---------------------|--------------------------------------------------|
| Seguridad           | JWT, HTTPS, roles definidos en base a usuario.   |
| Rendimiento         | Cache de consultas y paginación en reportes.     |
| Disponibilidad      | Circuit breaker y balanceo básico en gateway.    |
| Usabilidad          | UI Angular con navegación intuitiva.             |
| Mantenibilidad      | Separación en capas y documentación Swagger.     |
| Escalabilidad       | Contenerización de servicios con Docker Compose. |

---

## 4.2 Architectural Drivers

### 4.2.1 Design Purpose

La arquitectura de FoodFlow está diseñada para cumplir con los objetivos del negocio, priorizando tres atributos de calidad clave: **Seguridad, Usabilidad y Desempeño**. Estos son los drivers arquitectónicos más importantes. Adicionalmente, la plataforma también considera atributos de calidad generales como la **Mantenibilidad** y **Escalabilidad**, los cuales, si bien son secundarios, soportan la evolución a largo plazo de los atributos principales.

---

### 4.2.2 Primary Functionality (Primary User Stories)

| Código | User Story resumida                             |
|--------|-------------------------------------------------|
| US04   | Registrar y actualizar productos en inventario. |
| US06   | Administrar platos del menú.                    |
| US11   | Generar reportes financieros y de operaciones.  |
| US01   | Visualizar métricas en dashboard.               |

---

### 4.2.3 Quality Attribute Scenarios

| Atributo       | Estímulo                  | Entorno       | Respuesta                 | Medida              |
|----------------|---------------------------|---------------|---------------------------|---------------------|
| Desempeño      | Usuario solicita reporte  | Carga normal  | Respuesta en <3s          | Tiempo de respuesta |
| Seguridad      | Login desde red pública   | Producción    | Validación JWT + HTTPS    | Token válido        |
| Usabilidad     | Primer ingreso            | Nueva sesión  | Guía inicial en dashboard | Comprensión <1 min  |


---

### 4.2.4 Constraints

- Tecnologías fijas: **Angular, Spring Boot, MySQL**.
- Presupuesto acotado: sin infraestructura redundante.
- Servicio **Orders** permanece en modo lectura (mock).
- El módulo de menú gestiona únicamente los platos, no los pedidos.

---

### 4.2.5 Architectural Concerns

- Mantener coherencia de datos entre Inventory y Menu Management.
- Evitar duplicación de entidades compartidas (por ejemplo, Producto vs Plato).
- Asegurar versionado de APIs en gateway.
- Establecer logs unificados y monitoreo básico por servicio.

## 4.3 ADD Iterations

## 4.3.1 Iteration 1: Core System Foundation

### 4.3.1.1 Architectural Design Backlog 1

| Descripción                                   | Prioridad | Complejidad |
|-----------------------------------------------|-----------|-------------|
| Sistema debe responder en menos de 3 segundos | Alta      | Media       |
| Dashboard con reportes visuales               | Alta      | Media       |
| Gestión de inventario                         | Alta      | Media       |
| Arquitectura modular para escalabilidad       | Alta      | Alta        |
| Compatibilidad web multiplataforma            | Media     | Baja        |

### 4.3.1.2 Establish Iteration Goal by Selecting Drivers

**Objetivo de la Iteración 1:** Establecer la arquitectura base del sistema FoodFlow que soporte el dashboard financiero y la gestión básica de inventario, asegurando rendimiento adecuado y modularidad para futuras expansiones.

**Drivers Seleccionados:**
- **(Driver de Negocio - Propósito):** Dashboard Interactivo.
- **(Driver de Negocio - Propósito):** Gestión de Inventario.
- **(Atributo de Calidad - Desempeño):** El sistema muestra el reporte de inventario en una tabla con un tiempo de respuesta ≤ 3 segundos.
- **(Mantenibilidad):** Se elige un diseño modular para facilitar futuras mejoras de **Usabilidad** y **Desempeño** sin alterar la estabilidad del sistema.

### 4.3.1.3 Choose One or More Elements of the System to Refine

**Elementos del Sistema a Refinar:**
1. **Capa de Presentación (Frontend)**
2. **Capa de Lógica de Negocio (Backend Services)**
3. **Capa de Datos (Database Layer)**
4. **Capa de Integración (API Gateway)**

### 4.3.1.4 Choose One or More Design Concepts That Satisfy the Selected Drivers

**Conceptos de Diseño Seleccionados:**

1. **Arquitectura de Microservicios**
  - Satisface el atributo de **Mantenibilidad**, que a su vez es clave para la evolución a largo plazo de la **Usabilidad** y el **Desempeño**. Al ser modular, permite mejorar o escalar servicios de forma independiente sin afectar la experiencia del usuario.

2. **Patrón Model-View-Controller (MVC)**
  - Proporciona una separación clara de responsabilidades dentro de cada microservicio.
  - Satisface los drivers de negocio **"Dashboard Interactivo"** y **"Gestión de Inventario"** al organizar la lógica de presentación, negocio y datos.

3. **Base de Datos Relacional con Índices Optimizados**
- Satisface el atributo de calidad de **Desempeño** en las consultas.
- Asegura la integridad de los datos financieros.

### 4.3.1.5 Instantiate Architectural Elements, Allocate Responsibilities, and Define Interfaces
- **Frontend Service**: UI en Angular, consume APIs REST.
- **Reports Service**: genera reportes financieros (`/api/v1/reports/*`).
- **Inventory Service**: gestiona stock            (`/api/v1/inventory/*`).
- **Data Service**: repositorios y transacciones.
- **API Gateway**: entrada única al sistema.

### 4.3.1.6 Sketch Views (C4 & UML) and Record Design Decisions

#### C4 Context
Usuario (Dueño) ──► Frontend (Angular) ──► API Gateway ──► Services (Dashboard, Inventory) ──► Database


#### UML (simplificado)
[Frontend] → [DashboardService]
[Frontend] → [InventoryService]
[DashboardService] ↔ [Database]
[InventoryService] ↔ [Database]


#### Design Decisions

| Decisión       | Justificación               | Alternativas  | Trade-off                 |
|----------------|-----------------------------|---------------|---------------------------|
| Microservicios | Modularidad y escalabilidad | Monolito, SOA | Mayor complejidad inicial |
| MySQL          | Integridad ACID             | PostgreSQL    | Menos flexibilidad NoSQL  |

### 4.3.1.7 Analysis of Current Design and Review Iteration Goal (Kanban Board)

| TO DO               | IN PROGRESS      | TESTING            | DONE                      |
|---------------------|------------------|--------------------|---------------------------|
| Optimizar consultas | Configuración DB | API Gateway básico | Estructura microservicios |
|                     | Reports Service  | Endpoints reports  | Frontend base             |

---

## 4.3.2 Iteration 2: Business Logic and Integration

### 4.3.2.1 Architectural Design Backlog 2

| Descripción                         | Prioridad | Complejidad |
|-------------------------------------|-----------|-------------|
| Sistema de órdenes completo         | Alta      | Alta        |
| Reportes financieros avanzados      | Alta      | Media       |
| Seguridad de datos financieros      | Alta      | Alta        |
| Backup automático                   | Media     | Media       |

### 4.3.2.2 Establish Iteration Goal by Selecting Drivers
**Objetivo:** Implementar la lógica de negocio completa para órdenes y reportes avanzados con seguridad y exportación.

### 4.3.2.3 Choose One or More Elements of the System to Refine
- Order Service
- Financial Reports Service
- Security Layer
- Export Service

### 4.3.2.4 Choose One or More Design Concepts That Satisfy the Selected Drivers
- DDD (aggregates y servicios de dominio).
- JWT Authentication.
- Event-driven architecture (event bus).
- Strategy pattern para exportación.

### 4.3.2.5 Instantiate Architectural Elements, Allocate Responsibilities, and Define Interfaces
- **Order Service**: ciclo de vida de órdenes, validación de stock.
- **Reports Service**: métricas de rentabilidad (`/api/v1/reports/*`).
- **Security Service**: login, refresh, auditoría (`/api/v1/login/*`).
- **Export Service**: exportar Excel/PDF/CSV.

### 4.3.2.6 Sketch Views (C4 & UML)

#### C4 Component
[OrderService] ──► [InventoryService]
[OrderService] ──► [EventBus]
[ReportsService] ──► [Database]
[ExportService] ──► [ReportsService]
[SecurityService] ──► [API Gateway]


#### Sequence Diagram (crear orden)
Usuario → Frontend: POST /orders
Frontend → OrderService
OrderService → InventoryService (valida stock)
InventoryService → OrderService (OK)
OrderService → EventBus (OrderCreated)
OrderService → DB (guardar orden)
OrderService → Frontend (OK)


### 4.3.2.7 Analysis of Current Design and Review Iteration Goal (Kanban Board)

| TO DO                  | IN PROGRESS    | TESTING             | DONE             |
|------------------------|----------------|---------------------|------------------|
| Backup automático      | Event Bus      | Export Excel básico | Order Service    |
| Auditoría de seguridad | Export Service | Validaciones orden  | Reports Service  |


---

## 4.3.3 Iteration 3: Performance Optimization & UX

### 4.3.3.1 Architectural Design Backlog 3

| Descripción                       | Prioridad | Complejidad |
|-----------------------------------|-----------|-------------|
| Respuesta < 3s con carga          | Alta      | Alta        |
| Interfaz responsive y accesible   | Alta      | Media       |
| Escalabilidad a 50+ usuarios      | Media     | Alta        |

### 4.3.3.2 Establish Iteration Goal by Selecting Drivers
**Objetivo:** Optimizar rendimiento, asegurar disponibilidad y mejorar experiencia de usuario.

### 4.3.3.3 Choose One or More Elements of the System to Refine
- Performance Layer
- Monitoring & Observability
- Load Balancer
- UX Components

### 4.3.3.4 Choose One or More Design Concepts That Satisfy the Selected Drivers
- Circuit breaker.
- Progressive Web App.
- Horizontal scaling + load balancing.

### 4.3.3.5 Instantiate Architectural Elements, Allocate Responsibilities, and Define Interfaces
- **Load Balancer**: distribución de carga.
- **Frontend UX**: PWA accesible y responsive.

### 4.3.3.6 Sketch Views (Deployment)
[CDN] → [Load Balancer] → [API Gateway] → [Services Cluster] → [DB Cluster]
│
[Reports]


### 4.3.3.7 Analysis of Current Design and Review Iteration Goal (Kanban Board)
| TO DO         | IN PROGRESS   | TESTING               | DONE                |
|---------------|---------------|-----------------------|---------------------|
| Auto-scaling  | PWA           | Load balancer         | Multi-level caching |
| Benchmarks    | UI responsive | CDN                   | Circuit breaker     |
| Recovery plan |               | Monitoring dashboards | Health check system |

---

## 4.3.4 Iteration 4: Login & Authentication (IAM)

### 4.3.4.1 Architectural Design Backlog 4

| Descripción                 | Prioridad | Complejidad |
|-----------------------------|-----------|-------------|
| Autenticación JWT segura    | Alta      | Alta        |
| Gestión de usuarios         | Alta      | Media       |
| Integración multi-servicios | Alta      | Alta        |

### 4.3.4.2 Establish Iteration Goal
**Objetivo:** Crear servicio IAM independiente para login, sign-up y refresh tokens.

### 4.3.4.3 Elements to Refine
- AuthController
- AuthService
- UserRepository
- TokenProvider

### 4.3.4.4 Design Concepts
- JWT + refresh tokens.
- Password hashing (bcrypt).
- Middleware de autorización.

### 4.3.4.5 Instantiate Elements
- `POST /login`, `POST /sign-up`, `POST /refresh`.
- UserRepository con tabla `Users`.
- TokenProvider que firma tokens.

### 4.3.4.6 Sketch Views
Usuario → AuthController → AuthService → UserRepository
AuthService → TokenProvider (genera JWT)
AuthService → Frontend (devuelve tokens)


### 4.3.4.7 Analysis (Kanban)
| TO DO    | IN PROGRESS    | TESTING           | DONE                  |
|----------|----------------|-------------------|-----------------------|
| MFA      | Middleware JWT | Validación tokens | Login/Sign-up/Refresh |
| Auditing |                |                   | UserRepository        |

---

## 4.3.5 Iteration 5: Analytics Module

### 4.3.5.1 Architectural Design Backlog 5

| Descripción                  | Prioridad | Complejidad |
|------------------------------|-----------|-------------|
| Visualización de métricas    | Alta      | Media       |
| Actualización en tiempo real | Media     | Media       |

### 4.3.5.2 Establish Iteration Goal
**Objetivo:** Implementar módulo de analytics para visualizar ingresos y pérdidas en gráficos.

### 4.3.5.3 Elements to Refine
- ReportsService
- ChartingLibrary (frontend)

### 4.3.5.4 Design Concepts
- Frontend con librerías de gráficos.

### 4.3.5.5 Instantiate Elements
- `GET /api/v1/reports/summary`

### 4.3.5.6 Sketch Views
[Analytics] ← [ReportsService]
[Analytics] → [Frontend Charts]


### 4.3.5.7 Analysis (Kanban)
| TO DO       | IN PROGRESS        | TESTING       | DONE      |
|-------------|--------------------|---------------|-----------|
| Nuevos KPIs | Integración Charts | Validar datos | Analytics |

---

## 4.3.6 Iteration 6: Menu Module

### 4.3.6.1 Architectural Design Backlog 6

| Descripción                        | Prioridad | Complejidad |
|------------------------------------|-----------|-------------|
| Registrar platos en el menú        | Alta      | Media       |
| Vincular con inventario            | Alta      | Media       |

### 4.3.6.2 Establish Iteration Goal
**Objetivo:** Permitir registro de platos vinculados con inventario.

### 4.3.6.3 Elements to Refine
- OrderService
- InventoryService (extensión)

### 4.3.6.4 Design Concepts
- Relación Plato–Producto.


### 4.3.6.5 Instantiate Elements
- `POST /api/v1/orders/dishes`
- `GET /api/v1/orders/dishes`

### 4.3.6.6 Sketch Views
[OrderService] → [InventoryService]


### 4.3.6.7 Analysis (Kanban)
| TO DO       | IN PROGRESS             | TESTING        | DONE                |
|-------------|-------------------------|----------------|---------------------|
| CRUD platos | Relación con inventario | Validar datos  | OrderService básico |

---

## 4.3.7 Iteration 7: Payment Integration (IzyPay)

### 4.3.7.1 Architectural Design Backlog 7

| Descripción                   | Prioridad | Complejidad |
|-------------------------------|-----------|-------------|
| Integración con IzyPay        | Alta      | Alta        |
| Seguridad transaccional       | Alta      | Alta        |

### 4.3.7.2 Establish Iteration Goal
**Objetivo:** Integrar pagos con IzyPay como servicio externo.

### 4.3.7.3 Elements to Refine
- PaymentService
- External API Gateway

### 4.3.7.4 Design Concepts
- API REST + Webhooks.
- Mensajería asíncrona para confirmar pagos.

### 4.3.7.5 Instantiate Elements
- `POST /api/v1/payments/initiate`
- `POST /api/v1/payments/confirm`

### 4.3.7.6 Sketch Views
[Frontend] → [PaymentService] → [IzyPay API]
IzyPay → [PaymentService] (webhook confirmación)


### 4.3.7.7 Analysis (Kanban)
| TO DO    | IN PROGRESS    | TESTING               | DONE               |
|----------|----------------|-----------------------|--------------------|
| Webhooks | PaymentService | Validar transacciones | Integración básica |

---

## 4.3.8 Iteration 8: Cloud Deployment & Scalability

### 4.3.8.1 Architectural Design Backlog 8

| Descripción                  | Prioridad | Complejidad |
|-----------------------------|-----------|-------------|
| Alta disponibilidad en nube | Alta      | Alta        |

### 4.3.8.2 Establish Iteration Goal
**Objetivo:** Desplegar en Azure (backend) y Netlify (frontend).

### 4.3.8.3 Elements to Refine
- Infraestructura cloud
- Contenedores Docker
- Balanceador de carga

### 4.3.8.4 Design Concepts
- Azure App Service + MySQL managed.
- Docker para orquestación.

### 4.3.8.5 Instantiate Elements
- Backend → Azure
- Frontend → Netlify
- Database → Azure MySQL

### 4.3.8.6 Sketch Views
[Netlify Frontend] → [Azure App Service Backend] → [Azure MySQL]
Backend ←→ [Docker Cluster]


### 4.3.8.7 Analysis (Kanban)
| TO DO                 | IN PROGRESS  | TESTING         | DONE                |
|-----------------------|--------------|-----------------|---------------------|
| Configurar deployment | Deploy Azure | Load balancing  | Frontend en Netlify |
|                       |              | Monitoreo cloud | Backend en Azure    |

---
