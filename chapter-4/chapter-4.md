# Capítulo IV: Product Architecture Design

## 4.1 Design Concepts, ViewPoints & ER Diagrams
El presente diseño se realiza aplicando el método Attribute-Driven Design (ADD), identificando principios, enfoques, estilos y patrones arquitectónicos
### 4.1.1 Principles Statements
Para el diseño de la arquitectura representada en el diagrama, se establecen los siguientes principios:

- **Desacoplamiento de microservicios**  
  Cada servicio (Subscription, Reports, Inventory, Orders, Profiles) se diseña y despliega de forma independiente, facilitando la escalabilidad horizontal, el mantenimiento y la posibilidad de reemplazar o actualizar componentes sin afectar al resto.

  Cada microservicio se desarrolla y despliega con su propia base de datos.
  Esto asegura independencia en el ciclo de vida y permite escalar o modificar servicios sin afectar al resto.

- **Seguridad por diseño**  
  Se integra la seguridad en todos los niveles, desde el API Gateway con Spring Cloud Gateway hasta la autenticación y autorización gestionadas por el servicio Profile.  
  Se utilizan conexiones seguras y políticas de acceso basadas en roles.

  Todas las funcionalidades de la plataforma requieren autenticación previa mediante login con JWT (JSON Web Tokens), gestionado por el microservicio Profile.
  Las contraseñas se almacenan con algoritmos de hash seguro y la comunicación entre frontend y backend se asegura mediante HTTPS/TLS.

  El message broker (Apache Kafka) se configura con autenticación SASL y cifrado en tránsito.

- **Escalabilidad y alta disponibilidad**  
  Los contenedores de base de datos MySQL y el Message Broker (Apache Kafka) se preparan para replicación y balanceo, garantizando tolerancia a fallos y crecimiento según demanda.
  La arquitectura orientada a servicios permite procesar reportes financieros en paralelo y responder eficientemente a consultas frecuentes.

- **Reutilización de componentes**  
  La lógica de negocio común se abstrae en bibliotecas y APIs compartidas entre los microservicios para evitar duplicación de código y facilitar la coherencia en las respuestas.
  <br><br>
- **Observabilidad y monitoreo**  
  Se prioriza la incorporación de métricas, trazas y logs centralizados que permitan detectar incidentes y facilitar el mantenimiento preventivo.
 
- **Consistencia de interfaz**  
  La Single Page Application (SPA) en Angular mantiene un diseño coherente y unificado para los usuarios finales, integrando la Landing Page con las distintas funcionalidades sin interrumpir la experiencia de navegación.
  La sincronización entre órdenes e inventario se maneja mediante eventos asincrónicos en el broker, aplicando el principio de eventual consistency. Esto reduce la dependencia de transacciones distribuidas.

### 4.1.2 Approaches Statements, Architectural Styles & Patterns

#### Approaches Statements
- **Domain-Driven Design (DDD)**
  La arquitectura se modela en función de los bounded contexts identificados: Orders, Inventory, Reports, IAM, Profiles y Subscriptions. Cada uno refleja reglas de negocio claras y aisladas, lo que permite separar responsabilidades y reducir la complejidad del dominio.

- **Attribute-Driven Design (ADD)**  
  Las decisiones de arquitectura priorizan atributos de calidad como usabilidad, seguridad y desempeño, asegurando que los servicios cumplan los requisitos no funcionales.
 
- **Event-Driven Architecture**  
  La comunicación asíncrona se utiliza en los procesos que requieren sincronización entre servicios sin bloquear la operación del sistema, como la relación entre Orders e Inventory. Cuando una orden ya registrada en el sistema actualiza el consumo de insumos, se publica un evento que el microservicio de inventario procesa para reflejar la reducción en stock.
  Esto permite manejar de forma eficiente escenarios de alto volumen de órdenes y reducir el acoplamiento entre servicios.

#### Architectural Styles
- **Microservicios**  
  La aplicación se divide en servicios independientes, cada uno con su propia base de datos y ciclo de vida, comunicándose a través de APIs y mensajería.
  
- **Cliente-Servidor**  
  El frontend (Landing Page + SPA Angular) actúa como cliente que consume los servicios expuestos en el backend.
  
- **API Gateway Pattern**  
  Spring Cloud Gateway centraliza la entrada de las solicitudes, proporcionando enrutamiento, autenticación y balanceo de carga.
  
#### Patrones
- **Modelo-Vista-Controlador (MVC)**  
  Implementado en el frontend Angular para mantener la separación de responsabilidades entre la vista, la lógica de presentación y los datos.
  <br><br>
- **Circuit Breaker**  
  Para manejar fallos temporales en la comunicación entre microservicios y garantizar resiliencia.
  
- **Database per Service**  
  Cada microservicio cuenta con su propia base de datos MySQL para asegurar independencia de datos y evitar cuellos de botella.
  
- **Publisher-Subscriber**  
  Apache Kafka permite la distribución de eventos a múltiples consumidores, clave para la actualización de inventarios en tiempo real.
  
### 4.1.3 Context Diagram

El diagrama de contexto ilustra la interacción entre los usuarios, el frontend y los microservicios del backend, destacando las principales rutas de comunicación y los flujos de datos.

<img width="276" height="557" alt="image" src="https://github.com/user-attachments/assets/fbb14dd3-77b0-4e1d-b029-77c18f19b80f" />

Nuestro único usuario es el dueño del restaurante, quien accede a la plataforma a través de una Landing Page y una Single Page Application (SPA) desarrollada en Angular. 
El servicio externo que usaremos es IzyPay, que se encargará de procesar los pagos realizados por los clientes del restaurante. 

### 4.1.4 Approach Driven ViewPoints Diagrams
El diseño arquitectónico de FoodFlow se representa mediante diferentes puntos de vista, siguiendo las buenas prácticas de modelado (UML y C4 Model). Estos diagramas buscan capturar cómo interactúan los componentes, cómo fluye la información y cómo se organizan las entidades de dominio.

Diagramas UML:
<br>
Diagrama de actividad:
<img width="3840" height="3344" alt="diagram_activity" src="https://github.com/user-attachments/assets/a38db829-32e0-42aa-9343-204af86dc8d3" />

<br>

Diagrama de estados:

<img width="3840" height="939" alt="state_diagram" src="https://github.com/user-attachments/assets/c8f5771f-1628-42cf-9ffd-7e12a9b5f5a5" />

<br>

Diagramas C4:

### 4.1.5 Relational/Non Relational Database Diagram

### 4.1.6 Design Patterns

A continuación, se describen los patrones de diseño qeu se aplicarán:

| Patrón                                | Propósito general                                                        | Aplicación en FoodFlow / microservicios                                                                                                                                           | Justificación contextual                                                                                                             |
|---------------------------------------|--------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------|
| **Singleton**                         | Garantizar que una clase tenga una única instancia                       | El microservicio **Profile** implementa un único punto de control para la autenticación, la emisión de tokens y la gestión de roles de usuario.                                   | Se asegura un manejo consistente y centralizado de la seguridad, evitando estados inconsistentes en la gestión de credenciales.      |
| **Factory Method / Abstract Factory** | Crear objetos de una familia sin depender de su implementación concreta  | El microservicio **Reports** utiliza fábricas para generar diferentes tipos de reportes (diario, semanal, mensual) y adaptadores de persistencia para distintas fuentes de datos. | Facilita la extensión de nuevos formatos de reportes sin modificar la lógica cliente, manteniendo flexibilidad en el procesamiento.  |
| **Strategy**                          | Encapsular algoritmos intercambiables detrás de una interfaz común       | En **Reports** se aplican diferentes estrategias de cálculo de pérdidas y ganancias, como costo promedio, según la necesidad del análisis financiero.                             | Permite cambiar o agregar métodos de cálculo sin alterar el resto del sistema, garantizando flexibilidad en el análisis contable.    |
| **Observer**                          | Permitir que objetos se suscriban a cambios en otro objeto               | El microservicio **Inventory** actualiza sus módulos internos de alertas cuando cambia el stock de un producto, notificando al dueño en caso de que un insumo se agote.           | Los componentes internos reaccionan automáticamente a cambios en inventario, generando alertas sin acoplarse directamente al núcleo. |
| **Decorator**                         | Agregar responsabilidades a objetos dinámicamente                        | El **API Gateway** y sus middleware incorporan validaciones adicionales como logging, control de acceso y verificación de tokens de forma dinámica.                               | Permite añadir capas transversales sin modificar la lógica base de enrutamiento, reforzando seguridad y trazabilidad de peticiones.  |
| **Adapter**                           | Permitir que clases con interfaces incompatibles trabajen juntas         | **Inventory** dispone de adaptadores que permiten integrar librerías externas de cálculo de costos o conectores con sistemas de terceros.                                         | Asegura compatibilidad con APIs externas y facilita la interoperabilidad con herramientas ajenas al núcleo de la plataforma.         |
| **Template Method**                   | Definir el esqueleto de un algoritmo dejando pasos concretos a subclases | En **Reports** se define un flujo general de generación de reportes, delegando pasos específicos como filtrado de datos o agregación a subclases especializadas.                  | Reduce duplicación de lógica común entre diferentes tipos de reportes, manteniendo un proceso estándar y extensible.                 |

### 4.1.7 Tactics
A continuación, se describen las tácticas arquitectónicas implementadas en la arquitectura de FoodFlow para abordar los atributos de calidad identificados:

**Seguridad**

- Autenticación y Autorización Centralizada: el microservicio Profile administra las credenciales de los dueños de restaurante mediante login con JWT, garantizando control de acceso uniforme.

- Protección de datos sensibles: las contraseñas se almacenan con algoritmos de hash, evitando exposición de información crítica.

- API Gateway seguro: se asegura que todas las peticiones pasen por el Gateway, donde se validan los tokens antes de redirigir a los microservicios.

- Conexiones seguras: se recomienda el uso de HTTPS/TLS para proteger la comunicación entre frontend y backend.

**Desempeño**
- Comunicación asíncrona con Kafka: la interacción entre Orders e Inventory se realiza mediante eventos, evitando bloqueos y permitiendo procesar un gran volumen de órdenes sin afectar la experiencia del usuario.

- Database per Service: cada microservicio usa su propia base de datos MySQL, lo que elimina cuellos de botella de acceso a datos compartidos.

- Proyecciones para reportes: el microservicio Reports mantiene vistas materializadas alimentadas por eventos de Orders e Inventory, reduciendo el tiempo de respuesta en consultas financieras.

**Usabilidad**
 
- Dashboards visuales: los reportes se presentan con gráficos comprensibles, reduciendo la curva de aprendizaje para los dueños de restaurantes.

- Consistencia en interfaz: la SPA en Angular mantiene un diseño uniforme y una navegación fluida.

- Feedback inmediato: el sistema entrega mensajes claros al usuario, como alertas cuando un producto de inventario está por agotarse.

**Resiliencia y disponibilidad**
- Circuit Breaker y Retry: se utilizan en llamadas síncronas para evitar cascadas de fallos y reintentar operaciones bajo condiciones controladas.

- Eventual Consistency: se adopta para sincronizar órdenes e inventario mediante eventos, reduciendo dependencia de transacciones distribuidas.

- Logs básicos y monitoreo: cada microservicio expone métricas mínimas para supervisar su estado y detectar fallos de forma preventiva.

**Escalabilidad**

- Despliegue independiente de microservicios: cada servicio se ejecuta en contenedores aislados, lo que facilita la escalabilidad selectiva.

- Particionamiento en Kafka: los eventos se distribuyen por claves como restaurantId, permitiendo paralelismo y procesamiento balanceado en escenarios de alto volumen.

## 4.2 Architectural Drivers

### 4.1.8 Design Purpose

FoodFlow tiene como propósito fundamental proporcionar una plataforma segura y escalable que permita a los dueños de restaurantes gestionar de manera eficiente sus finanzas y operaciones.
La arquitectura se orienta a cumplir con los siguientes objetivos:

| Objetivo                                   | Descripción                                                                                                                                                                                                   |
|--------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Soporte a la gestión financiera**        | Permitir la generación de reportes claros y confiables sobre ingresos, pérdidas y balances, reduciendo significativamente el tiempo que el dueño invierte en cálculos manuales.                               |
| **Automatización de procesos internos**    | Sincronizar automáticamente las órdenes con el inventario, evitando errores de registro manual y reflejando en tiempo real las pérdidas por insumos y las ganancias por ventas.                               |
| **Escalabilidad y mantenimiento a futuro** | Diseñar el sistema bajo el estilo de microservicios, facilitando la evolución del producto, el despliegue independiente de componentes y la incorporación de nuevas funcionalidades o integraciones externas. |
| **Seguridad como eje principal**           | Proteger la información sensible de los usuarios mediante autenticación centralizada, control de accesos y cifrado en la comunicación entre servicios.                                                        |
| **Usabilidad y simplicidad**               | Garantizar que la interfaz sea intuitiva y accesible, con dashboards visuales que permitan al dueño interpretar su estado financiero en menos de un minuto.                                                   |

### 4.1.9 Primary Functionality (Primary User Stories)

En esta sección se presentan las historias de usuario principales que guían el desarrollo de la arquitectura de la aplicación. Estas historias reflejan las necesidades y expectativas del dueño del restaurante, asegurando que la solución propuesta cumpla con los requisitos funcionales esenciales.

| Orden | User Story Id | Título                                        | Descripción                                                                                                                                                                                | Story Points |
|:------|:--------------|:----------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-------------|
| 1     | US1           | Visualizar ingresos y pérdidas                | Como dueño de restaurante quiero ver en el dashboard un resumen de mis ingresos y pérdidas para conocer rápidamente la situación financiera de mi negocio.                                 | 8            |
| 2     | US18          | Visualización de Reportes Financieros Diarios | Como dueño de restaurante, quiero ver mis ingresos y gastos diarios en un dashboard centralizado para tomar decisiones informadas sobre el rendimiento financiero de mi negocio.           | 8            |
| 3     | US19          | Navegación entre Períodos de Tiempo           | Como dueño de restaurante, quiero poder alternar entre reportes diarios, semanales y mensuales para analizar tendencias en diferentes períodos de tiempo.                                  | 8            |
| 4     | US20          | Análisis de Categorías de Gastos              | Como dueño de restaurante, quiero visualizar el desglose de mis gastos por categorías para identificar áreas donde puedo optimizar costos.                                                 | 8            |
| 5     | US3           | Visualizar inventario actual                  | Como dueño de restaurante quiero ver una tabla con el stock, costo unitario y unidad de medida de cada producto para controlar el inventario de manera clara y rápida.                     | 5            |
| 6     | US11          | Visualizar órdenes existentes                 | Como dueño de restaurante quiero ver en una tabla las órdenes realizadas con su mesa, platos, precio total y fecha para llevar un control claro y rápido de los pedidos.                   | 5            |
| 7     | US2           | Identificar platos más populares              | Como dueño de restaurante quiero ver un listado de mis platos más vendidos para saber cuáles son los más rentables y tomar decisiones sobre el menú.                                       | 5            |
| 8     | US6           | Agregar Nuevo Plato                           | Como dueño de restaurante, quiero agregar nuevos platos al menú para mantener mi oferta actualizada y atractiva para los clientes.                                                         | 5            |
| 9     | US8           | Visualizar Información Completa del Menú      | Como dueño de restaurante, quiero visualizar toda la información de mis platos (nombre, descripción, precio, ingredientes) en una tabla organizada para tener control total sobre mi menú. | 5            |

### 4.1.10 Quality Attribute Scenarios
Aquí se detallan los escenarios de calidad que guían las decisiones arquitectónicas, asegurando que la solución cumpla con los requisitos no funcionales esenciales para el éxito del proyecto.
### 4.1.11 Constraints
- Uso de microservicios con bases de datos independientes (MySQL) para cada servicio.
- Comunicación asíncrona entre Orders e Inventory mediante Apache Kafka.
- Implementación de un API Gateway con Spring Cloud Gateway para centralizar el acceso a los microservicios.
- Desarrollo del frontend como una Single Page Application (SPA) en Angular.
- Autenticación y autorización gestionadas por un microservicio IAM utilizando JWT.
- No utilizaremos IOT ni Machine Learning en esta versión del producto.
- El tiempo de desarrollo es limitado, por lo que se priorizarán las funcionalidades esenciales para el lanzamiento inicial. Tiempo estimado de 3 meses.

### 4.1.12 Architectural Concerns

**Seguridad de la información**
- Proteger las credenciales de acceso de los dueños de restaurante.
- Garantizar que solo usuarios autenticados puedan consultar reportes financieros y modificar el inventario.
- Evitar fugas de datos sensibles mediante cifrado en tránsito y almacenamiento seguro de contraseñas.
- Consistencia de datos entre órdenes e inventario
- Asegurar que cada orden registrada impacte correctamente en el inventario.
- Manejar escenarios de falta de stock y mantener un estado coherente en la plataforma.
- Desempeño en generación de reportes
- Los reportes diarios, semanales y mensuales deben generarse en tiempos razonables aun con un alto volumen de registros.
- Evitar bloqueos o latencias excesivas que perjudiquen la experiencia del usuario.

**Usabilidad para dueños de restaurantes**
- Interfaz intuitiva y clara, con dashboards que faciliten la interpretación de métricas financieras.
- Reducción del tiempo de análisis manual a través de visualizaciones gráficas y alertas automáticas.

**Escalabilidad de la solución**
- Capacidad de desplegar y escalar microservicios de manera independiente.
- Asegurar que el sistema soporte crecimiento en número de órdenes y productos gestionados.

**Resiliencia frente a fallos**
- Evitar que un fallo en un microservicio afecte la disponibilidad de toda la plataforma.
- Manejar interrupciones temporales en la comunicación entre servicios mediante mecanismos de recuperación.

**Mantenibilidad y evolución**
- Estructura modular que permita añadir nuevas funcionalidades (ej. otros tipos de reportes o integraciones externas) sin modificar el núcleo de la aplicación.
- Documentación clara para facilitar el trabajo de nuevos desarrolladores en el futuro.
- Restricciones tecnológicas
- Uso exclusivo de Angular en frontend, Spring Boot en backend y MySQL en las bases de datos.
- Despliegue en Azure, evitando dependencias en proveedores alternativos.

## 4.3 ADD Iterations

## 4.3.1 Iteration 1: Core System Foundation
### 4.3.1.1 Architectural Design Backlog 1

| Driver ID | Descripción                                               | Prioridad | Complejidad |
|-----------|-----------------------------------------------------------|-----------|-------------|
| QA-01     | Sistema debe responder en menos de 3 segundos             | Alta      | Media       |
| QA-02     | Disponibilidad mínima del 95%                             | Alta      | Alta        |
| UC-01     | Dashboard con reportes visuales                           | Alta      | Media       |
| UC-02     | Gestión de inventario con alertas                         | Alta      | Media       |
| CON-01    | Arquitectura modular para escalabilidad                   | Alta      | Alta        |
| CON-02    | Compatibilidad web multiplataforma                        | Media     | Baja        |

### 4.3.1.2 Establish Iteration Goal by Selecting Drivers

**Objetivo de la Iteración 1:** Establecer la arquitectura base del sistema FoodFlow que soporte el dashboard financiero y la gestión básica de inventario, asegurando rendimiento adecuado y modularidad para futuras expansiones.

**Drivers Seleccionados:**
- **QA-01 (Rendimiento):** Respuesta en menos de 3 segundos
- **UC-01 (Funcionalidad):** Dashboard con reportes visuales
- **UC-02 (Funcionalidad):** Gestión de inventario
- **CON-01 (Constraints):** Arquitectura modular

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
- Satisface QA-01 (rendimiento en consultas)
- Integridad de datos financieros

### 4.3.1.5 Instantiate Architectural Elements, Allocate Responsibilities, and Define Interfaces
- **Frontend Service**: UI en React, consume APIs REST.
- **Reports Service**: genera reportes financieros (`/api/v1/reports/*`).
- **Inventory Service**: gestiona stock y alertas (`/api/v1/inventory/*`).
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

| TO DO                   | IN PROGRESS      | TESTING            | DONE                      |
|-------------------------|------------------|--------------------|---------------------------|
| Notificaciones realtime | Configuración DB | API Gateway básico | Estructura microservicios |
| Optimizar consultas     | Reports Service  | Endpoints reports  | Frontend base             |

---

## 4.3.2 Iteration 2: Business Logic and Integration

### 4.3.2.1 Architectural Design Backlog 2

| Driver ID | Descripción                         | Prioridad | Complejidad |
|-----------|-------------------------------------|-----------|-------------|
| UC-03     | Sistema de órdenes completo         | Alta      | Alta        |
| UC-04     | Reportes financieros avanzados      | Alta      | Media       |
| QA-03     | Seguridad de datos financieros      | Alta      | Alta        |
| UC-05     | Exportación de reportes (Excel/PDF) | Media     | Media       |
| QA-04     | Backup automático                   | Media     | Media       |

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
EventBus → NotificationService
OrderService → DB (guardar orden)
OrderService → Frontend (OK)


### 4.3.2.7 Analysis of Current Design and Review Iteration Goal (Kanban Board)

| TO DO                  | IN PROGRESS    | TESTING             | DONE             |
|------------------------|----------------|---------------------|------------------|
| Backup automático      | Event Bus      | Export Excel básico | Order Service    |
| Auditoría de seguridad | Export Service | Validaciones orden  | Reports Service  |
|                        |                | JWT auth            | Security Service |

---

## 4.3.3 Iteration 3: Performance Optimization & UX

### 4.3.3.1 Architectural Design Backlog 3

| Driver ID | Descripción                          | Prioridad | Complejidad |
|-----------|--------------------------------------|-----------|-------------|
| QA-05     | Respuesta < 3s con carga             | Alta      | Alta        |
| UX-01     | Interfaz responsive y accesible      | Alta      | Media       |
| QA-07     | Escalabilidad a 50+ usuarios         | Media     | Alta        |

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

| Driver ID | Descripción                 | Prioridad | Complejidad |
|-----------|-----------------------------|-----------|-------------|
| SEC-01    | Autenticación JWT segura    | Alta      | Alta        |
| SEC-02    | Gestión de usuarios         | Alta      | Media       |
| SEC-03    | Integración multi-servicios | Alta      | Alta        |

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

| Driver ID | Descripción                  | Prioridad | Complejidad |
|-----------|------------------------------|-----------|-------------|
| UC-06     | Visualización de métricas    | Alta      | Media       |
| QA-08     | Actualización en tiempo real | Media     | Media       |

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

| Driver ID | Descripción                        | Prioridad | Complejidad |
|-----------|------------------------------------|-----------|-------------|
| UC-07     | Registrar platos en el menú        | Alta      | Media       |
| UC-08     | Vincular con inventario            | Alta      | Media       |
| QA-09     | Alertas de ingredientes faltantes. | Media     | Media       |

### 4.3.6.2 Establish Iteration Goal
**Objetivo:** Permitir registro de platos vinculados con inventario y emitir alertas cuando un insumo esté por agotarse.

### 4.3.6.3 Elements to Refine
- OrderService
- InventoryService (extensión)

### 4.3.6.4 Design Concepts
- Relación Plato–Producto.
- Alertas en frontend (no persistentes).

### 4.3.6.5 Instantiate Elements
- `POST /api/v1/orders/dishes`
- `GET /api/v1/orders/dishes`

### 4.3.6.6 Sketch Views
[OrderService] → [InventoryService]
[OrderService] → [Frontend Alerts]


### 4.3.6.7 Analysis (Kanban)
| TO DO       | IN PROGRESS             | TESTING | DONE                |
|-------------|-------------------------|---------|---------------------|
| CRUD platos | Relación con inventario | Alertas | OrderService básico |

---

## 4.3.7 Iteration 7: Payment Integration (IzyPay)

### 4.3.7.1 Architectural Design Backlog 7

| Driver ID | Descripción                   | Prioridad | Complejidad |
|-----------|-------------------------------|-----------|-------------|
| UC-10     | Integración con IzyPay        | Alta      | Alta        |
| QA-11     | Seguridad transaccional       | Alta      | Alta        |

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

| Driver ID | Descripción                 | Prioridad | Complejidad |
|-----------|-----------------------------|-----------|-------------|
| QA-12     | Alta disponibilidad en nube | Alta      | Alta        |

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
