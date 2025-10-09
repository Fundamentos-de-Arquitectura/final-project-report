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

El **API Gateway** centraliza peticiones, aplica seguridad y distribuye tráfico hacia los microservicios internos.  
El estilo adoptado es **RESTful**, con comunicación **HTTP/JSON** y documentación de endpoints mediante **Swagger**.

#### Patrones aplicados:
- Repository + Service Layer: aislamiento de persistencia.
- Factory Method: instanciación controlada de entidades.
- Template Method: flujo de generación de reportes.
- Singleton: control del contexto de seguridad y configuración.

---

### 4.1.3 Context Diagram

El sistema tiene como actor principal al **dueño del restaurante**, quien accede desde una interfaz web Angular.

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

---

### 4.1.5 Relational Database Diagram

La base de datos **MySQL** mantiene las entidades descritas en los capítulos anteriores:


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

La arquitectura de **FoodFlow** busca ofrecer una plataforma modular y segura para la gestión de inventarios, menú y reportes financieros en tiempo real, asegurando continuidad y escalabilidad futura.

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
- **(Desempeño):** Respuesta en menos de 3 segundos
- **(Funcionalidad):** Dashboard con reportes visuales
- **(Funcionalidad):** Gestión de inventario
- **(Constraints):** Arquitectura modular

### 4.3.1.3 Choose One or More Elements of the System to Refine

**Elementos del Sistema a Refinar:**
1. **Capa de Presentación (Frontend)**
2. **Capa de Lógica de Negocio (Backend Services)**
3. **Capa de Datos (Database Layer)**
4. **Capa de Integración (API Gateway)**

### 4.3.1.4 Choose One or More Design Concepts That Satisfy the Selected Drivers

**Conceptos de Diseño Seleccionados:**

1. **Arquitectura de Microservicios**
  - Satisface CON-01 (modularidad)
  - Facilita escalabilidad independiente de módulos

2. **Patrón Model-View-Controller (MVC)**
  - Separación clara de responsabilidades
  - Satisface UC-01 y UC-02 (funcionalidades del dashboard e inventario)

3. **Base de Datos Relacional con Índices Optimizados**
- Satisface rendimiento en consultas
- Integridad de datos financieros

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
