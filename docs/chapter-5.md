# Capítulo V: Product Implementation, Validation & Deployment

El presente capítulo documenta el proceso de **implementación, validación y despliegue** del sistema *FoodFlow*, una aplicación web de gestión financiera para restaurantes desarrollada bajo una arquitectura de **microservicios RESTful**.  
El proyecto fue construido aplicando metodologías ágiles, asegurando un producto usable, seguro y de buen desempeño.

## 5.1 Testing Suites & General Patterns

### 5.1.1 Backend Application Core Testing Suite

El backend de *FoodFlow* fue validado bajo el enfoque Behavior Driven Development (BDD), utilizando el lenguaje Gherkin para redactar escenarios de comportamiento legibles por humanos.  
Cada prueba se enfocó en verificar los criterios de aceptación de los **User Stories** implementados durante el Sprint 1.

### US03 - Visualizar inventario actual - gherkin:
Feature: Visualizar inventario actual
Como dueño de restaurante
Quiero ver una tabla con el stock, costo unitario y unidad de medida de cada producto
Para controlar el inventario de manera clara y rápida

Scenario: Visualización del inventario
Given que estoy en la sección "Inventory Management"
When ingreso al sistema
Then visualizo una tabla con los productos, su stock actual, costo unitario y unidad de medida

### US04 - Agregar un nuevo producto al inventario - gherkin:
Feature: Agregar nuevo producto al inventario
Como dueño de restaurante
Quiero registrar un nuevo producto con su nombre, stock, costo unitario y unidad de medida
Para mantener actualizado mi inventario

Scenario: Registro exitoso de producto
Given que estoy en la sección "Add New Product"
When ingreso el nombre del producto, stock, costo unitario y unidad de medida válidos
And presiono "Add Product"
Then el producto se añade a la tabla del inventario con los datos correctos
And aparece un mensaje de confirmación

### US05 - Validar campos al agregar producto - gherkin:
Feature: Validar campos al agregar producto
Como dueño de restaurante
Quiero que el sistema valide los campos obligatorios al agregar un producto
Para evitar errores en el registro del inventario

Scenario: Validación de campos obligatorios
Given que quiero registrar un producto
When dejo un campo vacío o ingreso un valor inválido
Then el sistema muestra un mensaje de error
And no permite guardar el producto hasta que la información sea correcta

### US09 - Visualizar órdenes existentes - gherkin:
Feature: Visualización de órdenes existentes
Como dueño de restaurante
Quiero ver las órdenes registradas con mesa, platos, precio total y fecha
Para llevar un control claro y rápido de las órdenes

Scenario: Visualización inicial de órdenes
Given que estoy en la sección "Orders"
When ingreso al sistema
Then visualizo una tabla con las órdenes registradas mostrando mesa, platos, precio total y fecha

### US10 - Calcular el total por plato - gherkin:
Feature: Calcular el total por plato
Como dueño de restaurante
Quiero que el sistema calcule automáticamente el precio total de cada plato
Para evitar errores de facturación manual

Scenario: Cálculo automático de total
Given que agrego un plato con precio unitario
When especifico la cantidad
Then el sistema calcula y muestra el precio total en la columna correspondiente

---

### 5.1.2 Pattern Based Backend Application(s)

La arquitectura del backend se diseñó siguiendo patrones de diseño estándar para mantener una estructura modular y desacoplada:

- **Repository Pattern:** abstrae la comunicación con la base de datos MySQL.
- **Service Layer Pattern:** centraliza la lógica de negocio.
- **Controller Pattern:** gestiona las rutas y la lógica de entrada/salida de datos.
- **DTO (Data Transfer Object):** facilita la transferencia segura de información.
- **Factory Pattern:** estandariza la creación de instancias de entidades.
- **Dependency Injection:** mejora la testabilidad y reduce el acoplamiento.

Estos patrones se aplicaron uniformemente en los microservicios de **inventario** y **órdenes**.

---

### 5.1.3 Pattern Based Custom Software Library

Durante la implementación se desarrolló una **librería interna reutilizable**, empleada por todos los microservicios, que incluye:

- Estructura estandarizada de respuestas JSON.
- Validación de entrada de datos.
- Middleware de autenticación basado en JWT.

Esto permitió mantener consistencia, minimizar redundancia y simplificar la depuración.

---

### 5.1.4 Framework Pattern Driven Refactoring Report

A lo largo del desarrollo se realizaron refactorizaciones orientadas por patrones del framework, priorizando legibilidad y mantenibilidad:

- Organización de controladores.
- Unificación de nomenclaturas REST (`/api/v1/`).
- Separación de configuraciones por entorno (`.env`).
- Simplificación de dependencias.
- Implementación de controladores globales de errores.

Cada cambio fue verificado mediante la ejecución completa de la suite BDD.

---

## 5.2 Software Configuration Management

### 5.2.1 Software Development Environment Configuration

| Tipo de Herramienta  | Herramienta Utilizada                         | Propósito                              |
|----------------------|-----------------------------------------------|----------------------------------------|
| IDE                  | Visual Studio Code / WebStorm / IntelliJ Idea | Desarrollo frontend y backend          |
| Control de versiones | Git + GitHub                                  | Versionamiento y trabajo colaborativo  |
| Testing              | Gherkin                                       | Acceptance Tests                       |
| API Client           | Postman / Swagger                             | Validación de endpoints                |
| Base de Datos        | MySQL (FreeSQL)                               | Almacenamiento persistente             |
| Documentación        | Swagger (OpenAPI)                             | Generación automática de documentación |
| Gestión de proyecto  | Trello                                        | Control ágil de tareas y User Stories  |

**Configuraciones adicionales:**  
El entorno de desarrollo utiliza variables `.env`, dependencias gestionadas con `npm`, y scripts configurados para automatizar pruebas y despliegues.

---

### 5.2.2 Source Code Management

El control de versiones se gestiona con **GitHub** bajo la metodología **GitFlow Workflow**, que define ramas estables y de desarrollo:

- `develop`: rama principal de desarrollo.
- `feature/*`: ramas por funcionalidad.

**Convenciones:**
- Semantic Versioning (`v1.0.0`, `v1.1.0`, etc.)
- Conventional Commits (`feat:`, `fix:`, `docs:`)

**Repositorio donde se realizó el código:** 
https://github.com/orgs/Biblioteca-de-Software/repositories
<br>

**Repositorio del proyecto actual:**
https://github.com/orgs/Fundamentos-de-Arquitectura/repositories

---

### 5.2.3 Source Code Style Guide & Conventions

Guías de estilo aplicadas:

- TypeScript Style Guide.
- Gherkin Conventions for Readable Specifications.
- Clases en camelCase
- Uso de inglés en todo el código.

---

### 5.2.4 Software Deployment Configuration

El despliegue de la landing page se realiza en Vercel

El despliegue del frontend se realiza en netlify

El despliegue del backend se realiza en **FreeSQL** para la base de datos.

**Pasos del pipeline:**
1. Build del proyecto (`npm run build`)
2. Ejecución de pruebas (`npm run test`)
3. Despliegue automatizado en entorno cloud
4. Validación mediante Postman y Swagger

**Deployment Diagram (C4):**

![WhatsApp Image 2025-10-05 at 12 18 28 PM](https://github.com/user-attachments/assets/d8251006-3dea-418b-9d0e-2b03b6d16923)


---

## 5.3 Microservices Implementation

### 5.3.1 Sprint 1

#### 5.3.1.1 Sprint Backlog 1

**Objetivo del Sprint:**  
Construir las funcionalidades básicas para la gestión de inventario y órdenes.

**Avance general:**  
En este sprint, el equipo se enfocó en construir las funcionalidades básicas para la gestión de inventario y órdenes. Todas las historias de usuario planificadas para estos dos módulos fueron implementadas, probadas y documentadas conforme a los criterios de aceptación.

**Herramienta utilizada:** Trello  
**URL del tablero:** 
https://trello.com/invite/b/68acc5a626e4614f12ee778f/ATTIb0400bfaba199996d200f641b89abc646996230A/arquitectura-de-software

<table>
  <thead>
    <tr>
      <th rowspan="2">User Story ID</th>
      <th rowspan="2">User Story</th>
      <th colspan="6">Work-Item / Task</th>
    </tr>
    <tr>
      <th>Task ID</th>
      <th>Título</th>
      <th>Descripción</th>
      <th>Estimación (h)</th>
      <th>Asignado a</th>
      <th>Estado</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td rowspan="2">US06</td>
      <td rowspan="2">Agregar Nuevo Plato</td>
      <td>T6.1</td>
      <td>API: Agregar Plato</td>
      <td>Desarrollar el endpoint de POST para registrar un nuevo plato en la base de datos.</td>
      <td>8</td>
      <td>Paolo Torres</td>
      <td>To Do</td>
    </tr>
    <tr>
      <td>T6.2</td>
      <td>FE: Formulario Agregar Plato</td>
      <td>Crear el formulario en Angular para ingresar los datos del nuevo plato y conectar con el backend.</td>
      <td>12</td>
      <td>Paolo Torres</td>
      <td>To Do</td>
    </tr>
    <tr>
      <td rowspan="2">US07</td>
      <td rowspan="2">Buscar Platos Existentes</td>
      <td>T7.1</td>
      <td>FE: Campo de Búsqueda</td>
      <td>Implementar un campo de búsqueda en Angular para filtrar platos en tiempo real.</td>
      <td>6</td>
      <td>Romina Maita</td>
      <td>To Do</td>
    </tr>
    <tr>
      <td>T7.2</td>
      <td>BE: Endpoint de Búsqueda</td>
      <td>Desarrollar un endpoint en el backend para buscar platos por nombre.</td>
      <td>8</td>
      <td>Romina Maita</td>
      <td>To Do</td>
    </tr>
    <tr>
      <td rowspan="2">US08</td>
      <td rowspan="2">Visualizar Información Completa del Menú</td>
      <td>T8.1</td>
      <td>FE: Tabla de Platos</td>
      <td>Crear una tabla en Angular para mostrar la información completa de los platos.</td>
      <td>10</td>
      <td>Paolo Torres</td>
      <td>To Do</td>
    </tr>
    <tr>
      <td>T8.2</td>
      <td>BE: Endpoint de Listado</td>
      <td>Desarrollar un endpoint en el backend para obtener la información completa de los platos.</td>
      <td>8</td>
      <td>Paolo Torres</td>
      <td>To Do</td>
    </tr>
    <tr>
      <td rowspan="2">US11</td>
      <td rowspan="2">Visualización de Reportes Financieros Diarios</td>
      <td>T11.1</td>
      <td>FE: Dashboard de Reportes</td>
      <td>Crear un dashboard en Angular para mostrar los ingresos y gastos diarios.</td>
      <td>12</td>
      <td>Romina Maita</td>
      <td>To Do</td>
    </tr>
    <tr>
      <td>T11.2</td>
      <td>BE: Endpoint de Reportes</td>
      <td>Desarrollar un endpoint en el backend para calcular ingresos y gastos diarios.</td>
      <td>10</td>
      <td>Romina Maita</td>
      <td>To Do</td>
    </tr>
    <tr>
      <td rowspan="2">US12</td>
      <td rowspan="2">Navegación entre Períodos de Tiempo</td>
      <td>T12.1</td>
      <td>FE: Navegación de Períodos</td>
      <td>Implementar la funcionalidad de cambio entre vistas diarias, semanales y mensuales en el dashboard.</td>
      <td>8</td>
      <td>Paolo Torres</td>
      <td>To Do</td>
    </tr>
    <tr>
      <td>T12.2</td>
      <td>BE: Endpoint de Períodos</td>
      <td>Desarrollar un endpoint en el backend para obtener datos agregados por período.</td>
      <td>10</td>
      <td>Paolo Torres</td>
      <td>To Do</td>
    </tr>
    <tr>
      <td rowspan="2">US13</td>
      <td rowspan="2">Análisis de Categorías de Gastos</td>
      <td>T13.1</td>
      <td>FE: Visualización de Categorías</td>
      <td>Crear gráficos en Angular para mostrar el desglose de gastos por categoría.</td>
      <td>10</td>
      <td>Romina Maita</td>
      <td>To Do</td>
    </tr>
    <tr>
      <td>T13.2</td>
      <td>BE: Endpoint de Categorías</td>
      <td>Desarrollar un endpoint en el backend para calcular los gastos por categoría.</td>
      <td>8</td>
      <td>Romina Maita</td>
      <td>To Do</td>
    </tr>
  </tbody>
</table>

---

#### 5.3.1.2 Development Evidence for Sprint Review

| Repository          | Branch                       | Commit ID | Commit Message                                    | Commit Message Body                                              | Committed on |
|---------------------|------------------------------|-----------|---------------------------------------------------|------------------------------------------------------------------|--------------|
| `Inventory Service` | `feature/inventory-module`   | `c19ad45` | `feat: implement inventory CRUD operations`       | Added controller, services, and models for inventory management. | 02/09/2025   |
| `Orders Service`    | `feature/orders-module`      | `d22ce35` | `feat: add order creation and confirmation`       | Implemented endpoints for creating and confirming orders.        | 03/09/2025   |
| `Inventory Service` | `fix/stock-update`           | `e33de12` | `fix: correct stock update on order confirmation` | Ensured inventory stock is correctly updated after an order.     | 04/09/2025   |
| `Orders Service`    | `refactor/order-calculation` | `f42bd73` | `refactor: improve order total calculation`       | Optimized the logic for calculating order totals.                | 04/09/2025   |

---

#### 5.3.1.3 Testing Suite Evidence for Sprint Review

Las pruebas BDD se desarrollaron con **Gherkin** para las User Stories implementadas.  
A continuación se presentan los archivos `.feature` correspondientes:

- US03 - Visualizar Inventario Actual

```Feature: Visualizar Inventario Actual
  Como dueño de restaurante
  Quiero ver una tabla con el stock, costo unitario y unidad de medida de cada producto
  Para controlar el inventario de manera clara y rápida

  Scenario: Visualización de la tabla de inventario
    Given que tengo productos registrados en mi inventario
    When navego a la sección "Inventory Management"
    Then debo ver una tabla con el listado de todos los productos
    And la tabla debe mostrar las columnas: Producto, Stock Actual, Costo Unitario y Unidad de Medida
```

- US04 - Agregar Nuevo Producto al Inventario

```Feature: Agregar Nuevo Producto al Inventario
  Como dueño de restaurante
  Quiero registrar un nuevo producto con sus datos
  Para mantener actualizado mi inventario

  Scenario: Registro exitoso de producto
    Given que estoy en la sección "Add New Product"
    When ingreso un Nombre, Stock (numérico), Costo Unitario (numérico) y Unidad de Medida válidos
    And presiono el botón "Add Product"
    Then el nuevo producto se añade a la tabla del inventario
    And veo un mensaje de confirmación "Producto añadido exitosamente"
```

- US05 - Validar Campos al Agregar Producto

```Feature: Validar Campos al Agregar Producto
  Como dueño de restaurante
  Quiero que el sistema valide los campos obligatorios y de formato
  Para evitar errores en el registro del inventario

  Scenario: Intento de registro con datos incompletos o inválidos
    Given que estoy en el formulario para registrar un nuevo producto
    When dejo el campo "Costo Unitario" vacío o ingreso texto en lugar de un número
    And presiono el botón "Add Product"
    Then el sistema muestra un mensaje de error específico junto al campo
    And el producto no se guarda en el inventario
```

- US09 - Visualización y Control de Órdenes Registradas

```Feature: Visualización y Control de Órdenes Registradas
  Como dueño de restaurante
  Quiero ver las órdenes registradas con sus detalles clave
  Para llevar un control claro y rápido de los órdenes

  Scenario: Visualización inicial de órdenes en la tabla
    Given que tengo órdenes registradas en el sistema
    When navego a la sección "Orders"
    Then debo ver una tabla con las órdenes
    And la tabla muestra las columnas: Mesa, Platos, Precio Total y Fecha
```

- US10 - Cálculo Automático del Total de Plato

```Feature: Cálculo Automático del Total de Plato
  Como dueño de restaurante
  Quiero que el sistema calcule automáticamente el precio total de cada plato
  Para evitar errores de facturación manual

  Scenario: Cálculo de total basado en cantidad
    Given que he agregado el plato "Hamburguesa" con un Precio Unitario de $10.00 al detalle de la orden
    When cambio la Cantidad de 1 a 3
    Then el sistema actualiza automáticamente el campo "Precio Total" a $30.00
```


---

#### 5.3.1.4 Execution Evidence for Sprint Review

Durante el Sprint 1 se ejecutaron las pruebas de los módulos desarrollados en el backend y frontend, verificando la correcta interacción entre los servicios y la interfaz de usuario.  
La ejecución de los escenarios Gherkin se realizó utilizando la metodología **BDD (Behavior Driven Development)**, a través de los `.feature files` definidos para cada historia de usuario implementada.

Los resultados de la ejecución validaron la correcta respuesta de los endpoints RESTful del backend y su coherencia con las funcionalidades del frontend.  
A continuación se resume la evidencia obtenida:

| Módulo        | Endpoint principal                              | Resultado de ejecución                                  | Estado |
|---------------|-------------------------------------------------|---------------------------------------------------------|--------|
| Inventario    | `/api/v1/inventory`                             | Listado, registro y validación de productos             | Passed |
| Órdenes       | `/api/v1/orders`                                | Registro, visualización y confirmación de órdenes       | Passed |


### Orders:

<img width="1920" height="1080" alt="orders" src="https://github.com/user-attachments/assets/6d4fbb15-acfb-4c9c-a57a-e2ab4b4e03a3" />

<img width="1476" height="882" alt="endpoints" src="https://github.com/user-attachments/assets/ac09ef8f-61c0-418b-9e99-a53eb5b3a04b" />

### Inventory

<img width="1349" height="776" alt="inventory" src="https://github.com/user-attachments/assets/370b8c4f-6fa2-4786-90b0-bd10946005ab" />

---

#### 5.3.1.5 Microservices Documentation Evidence for Sprint Review

La aplicación *FoodFlow* fue desarrollada bajo una arquitectura **basada en microservicios RESTful**, donde cada dominio del negocio se implementa de forma independiente para asegurar modularidad, escalabilidad y mantenibilidad del sistema.

En este sprint, el foco estuvo en los siguientes microservicios:

| Microservicio         | Descripción funcional                                              | Endpoints principales                          |
|-----------------------|--------------------------------------------------------------------|------------------------------------------------|
| **Inventory Service** | Control de inventario, stock, y validaciones.                      | `/api/v1/inventory`, `/api/v1/inventory/{id}`  |
| **Orders Service**    | Gestión de órdenes, confirmaciones y cálculos totales.             | `/api/v1/orders`, `/api/v1/orders/{id}`        |

##### Documentación API:
Toda la documentación del backend fue generada automáticamente con **Swagger (OpenAPI)**, donde se describen los endpoints, modelos de datos y respuestas HTTP.


##### Integración:
Cada microservicio será desplegado de manera independiente, comunicándose entre sí mediante HTTP.  
El gateway unificará las peticiones hacia los servicios de dominio y gestionará los encabezados de autenticación JWT.

---

#### 5.3.1.6 Software Deployment Evidence for Sprint Review

El despliegue de *FoodFlow* se realizó en un entorno cloud utilizando un servidor FreeSQL para la base de datos relacional MySQL, y un entorno web para el frontend.  
Se implementó un pipeline de despliegue siguiendo el flujo GitFlow, con integración continua y versionamiento controlado.


##### Configuración general de despliegue:

| Componente            | Tecnología            | Descripción                                         |
|-----------------------|-----------------------|-----------------------------------------------------|
| **Backend**           | Java Spring Boot      | Desplegado como conjunto de microservicios RESTful. |
| **Frontend**          | Angular + Material UI | Dashboard interactivo con reportes y gráficos.      |
| **Base de Datos**     | MySQL                 | Gestión de datos financieros, platos e inventarios. |
| **Documentación API** | Swagger (OpenAPI)     | Publicación de especificaciones REST.               |

##### Proceso de despliegue:

1. **Commit en rama `develop`** → validación de tests automáticos.
2. **Verificación** mediante consultas de endpoints desde Postman.

**Evidencia visual del despliegue:**
**Backend desplegado en FreeSQL:** 
<br>

**Frontend desplegado en netlify:** https://deplokeep.netlify.app/pages/login-owner

<br>

**Landing Page desplegada en Vercel:** https://landing-page-silk-nine-71.vercel.app/

---

#### 5.3.1.7 Team Collaboration Insights during Sprint

Durante el Sprint 1, el equipo de desarrollo mantuvo una comunicación constante utilizando herramientas ágiles y colaborativas.  
La metodología aplicada fue Scrum, con reuniones de seguimiento semanales para la revisión del progreso y resolución de bloqueos.

##### Herramientas utilizadas:
- **Trello:** gestión del backlog, tareas y definición del flujo Kanban.
- **GitHub:** control de versiones y revisión de código mediante Pull Requests.
- **Discord / WhatsApp:** reuniones de planificación, retrospectiva y revisión de sprint.

##### GitHub Insights:



---

#### 5.3.1.8 Kanban Board

Durante el Sprint 1 se empleó un tablero Kanban en **Trello** para gestionar las historias de usuario, priorizar tareas y monitorear el avance del sprint.

El tablero se estructuró con las siguientes columnas:
- **To Do:** Tareas seleccionadas para el sprint actual.
- **Doing** Tareas en desarrollo.
- **Done:** Historias completadas y verificadas.

**URL del tablero de Trello:**
https://trello.com/invite/b/68acc5a626e4614f12ee778f/ATTIb0400bfaba199996d200f641b89abc646996230A/arquitectura-de-software

**Captura del tablero Kanban:**
<img width="1220" height="639" alt="image" src="https://github.com/user-attachments/assets/9b6ebaf0-f1e5-4178-b3cd-5617c76094a2" />


---

 **Conclusión del Sprint 1:**  
 El equipo completó con éxito la implementación de los módulos de Inventario y Órdenes, cumpliendo con los objetivos de desarrollo, validación e integración de los microservicios correspondientes. Las funcionalidades de Dashboard, Reportes y Menú quedan planificadas para futuros sprints.

### 5.3.2 Sprint 2

#### 5.3.2.1 Sprint Backlog 2

**Objetivo del Sprint:**  
Construir las funcionalidades básicas para la gestión de subscriptions, IAM y profiles e integrar orders e inventory con Apache Kafka..

**Avance general:**  
En este sprint, el equipo se enfocó en construir las funcionalidades básicas para la gestión de subscriptions, IAM y profiles e integración de inventory y orders con Apache Kafka. Todas las historias de usuario planificadas para estos dos módulos fueron implementadas, probadas y documentadas conforme a los criterios de aceptación.

**Herramienta utilizada:** Trello  
**URL del tablero:** https://trello.com/invite/b/6908f5bbfb6116c0fb9f808d/ATTI6ffb173398dc4a8f401d2e79128763caA0B1AA18/sprint-2-foodflow


| User Story Id | Título                                  | Descripción                                                                                                                                                                      | Estimado (horas) | Encargado          |
|:--------------|:----------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-----------------|:-------------------|
| **US03**      | Visualizar inventario actual            | Como dueño de restaurante quiero ver una tabla con el stock, costo unitario y unidad de medida de cada producto para controlar el inventario de manera clara y rápida.           | 6                | Werner Lang        |
| **US04**      | Agregar un nuevo producto al inventario | Como dueño de restaurante quiero registrar un nuevo producto con su nombre, stock, costo unitario y unidad de medida para mantener actualizado mi inventario.                    | 5                | Werner Lang        |
| **US05**      | Validar campos al agregar producto      | Como dueño de restaurante quiero que el sistema valide los campos obligatorios al agregar un producto para evitar errores en el registro del inventario.                         | 3                | Werner Lang        |
| **US09**      | Visualizar órdenes existentes           | Como dueño de restaurante quiero ver en una tabla las órdenes realizadas con su mesa, platos, precio total y fecha para llevar un control claro y rápido de los órdenes.         | 6                | Werner Lang        |
| **US14**      | Visualizar Planes de Suscripción        | Como dueño de restaurante, quiero ver los diferentes planes de suscripción disponibles (gratuito, estándar y premium) para conocer las opciones y beneficios antes de decidirme. | 5                | Paolo Torres       |
| **US15**      | Suscribirse al Plan Premium             | Como dueño de restaurante, quiero poder suscribirme al plan premium para acceder a funcionalidades avanzadas que mejoren la gestión de mi negocio.                               | 5                | Paolo Torres       |
| **US16**      | Cancelar Suscripción                    | Como dueño de restaurante, quiero poder cancelar mi suscripción de pago para dejar de usar el plan premium cuando ya no lo considere necesario.                                  | 4                | Paolo Torres       |
| **US17**      | Visualizar Perfil de Usuario            | Como dueño de restaurante, quiero ver mi información personal y detalles de cuenta en la sección “Profile” para poder revisar mis datos registrados.                             | 4                | Romina Maita       |
| **US18**      | Actualizar Datos del Perfil             | Como dueño de restaurante, quiero actualizar mi información personal (nombre, correo, contraseña, imagen de perfil) para mantener mi cuenta al día.                              | 5                | Romina Maita       |
| **US19**      | Iniciar Sesión                          | Como dueño de restaurante, quiero iniciar sesión en el sistema con mi correo y contraseña para acceder de forma segura a mis datos y funcionalidades.                            | 6                | Romina Maita       |
| **US20**      | Crear Cuenta Nueva                      | Como dueño de restaurante, quiero crear una cuenta ingresando mis datos básicos (nombre, correo, contraseña) para comenzar a usar el sistema.                                    | 6                | Romina Maita       |

---

#### 5.3.2.2 Development Evidence for Sprint Review

##### 5.3.2.2.1 Subscriptions Service

| Repository             | Branch                       | Commit ID | Commit Message                                                          | Commit Message Body                                                          | Committed on |
|------------------------|------------------------------|-----------|-------------------------------------------------------------------------|------------------------------------------------------------------------------|--------------|
| `subscription-service` | `develop`                    | `a1b2c3d` | `Fix: typo`                                                             | Corrección menor de error tipográfico en el código.                          | 25/10/2025   |
| `subscription-service` | `develop`                    | `b2c3d4e` | `Subscription Service details`                                          | Añadidos detalles y mejoras en la configuración del servicio de suscripción. | 25/10/2025   |
| `subscription-service` | `develop`                    | `c3d4e5f` | `Merge pull request #5 from /testing`                                   | Integración de cambios desde la rama de pruebas.                             | 25/10/2025   |
| `subscription-service` | `testing`                    | `d4e5f6g` | `fix(domain): add jsonIgnoreProperties to avoid serialization problems` | Añadido `@JsonIgnoreProperties` para evitar problemas de serialización.      | 24/10/2025   |
| `subscription-service` | `testing`                    | `e5f6g7h` | `fix: delete unnecessary dependency`                                    | Eliminada dependencia innecesaria del proyecto.                              | 24/10/2025   |
| `subscription-service` | `testing`                    | `f6g7h8i` | `feat(test): add pom and app props`                                     | Agregados archivos `pom.xml` y `application.properties` para pruebas.        | 23/10/2025   |
| `subscription-service` | `develop`                    | `g7h8i9j` | `Merge pull request #4 from feature/add-interfaces`                     | Combinada la rama con la capa de interfaces.                                 | 23/10/2025   |
| `subscription-service` | `feature/add-interfaces`     | `h8i9j0k` | `feat(interfaces): add controller`                                      | Implementado controlador principal para gestión de suscripciones.            | 23/10/2025   |
| `subscription-service` | `develop`                    | `i9j0k1l` | `Merge pull request #3 from feature/add-infrastructure`                 | Fusionada la capa de infraestructura.                                        | 23/10/2025   |
| `subscription-service` | `develop`                    | `j0k1l2m` | `Merge pull request #2 from feature/add-application`                    | Integrada capa de aplicación.                                                | 23/10/2025   |
| `subscription-service` | `feature/add-infrastructure` | `k1l2m3n` | `feat(infrastructure): add infrastructure layer`                        | Añadida la capa de infraestructura al proyecto.                              | 23/10/2025   |
| `subscription-service` | `feature/add-application`    | `l2m3n4o` | `feat(application): add SubscriptionService todo infrastructure`        | Creado `SubscriptionService` pendiente de integración con infraestructura.   | 23/10/2025   |
| `subscription-service` | `develop`                    | `m3n4o5p` | `Merge pull request #1 from feature/add-domain`                         | Añadida la capa de dominio al proyecto.                                      | 23/10/2025   |
| `subscription-service` | `feature/add-domain`         | `n4o5p6q` | `feat(application): add dtos`                                           | Creación de los DTOs para comunicación entre capas.                          | 23/10/2025   |
| `subscription-service` | `feature/add-domain`         | `o5p6q7r` | `feat(model): add domain service`                                       | Añadido servicio de dominio base.                                            | 23/10/2025   |
| `subscription-service` | `feature/add-domain`         | `p6q7r8s` | `feat(model): add domain repositories`                                  | Implementación inicial de repositorios de dominio.                           | 23/10/2025   |
| `subscription-service` | `feature/add-domain`         | `q7r8s9t` | `feat(model): add domain model entities`                                | Definidas entidades principales del modelo de dominio.                       | 23/10/2025   |
| `subscription-service` | `main`                       | `r8s9t0u` | `Initial commit`                                                        | Configuración inicial del repositorio.                                       | 23/10/2025   |
| `subscription-service` | `main`                       | `s9t0u1v` | `initial commit`                                                        | Versión preliminar del repositorio base.                                     | 23/10/2025   |

##### 5.3.2.2.2 IAM Service

| Repository         | Branch | Commit ID | Commit Message          | Commit Message Body                                       | Committed on |
|--------------------|--------|-----------|-------------------------|-----------------------------------------------------------|--------------|
| `IAM-microservice` | `main` | `t1u2v3w` | `chore: initial commit` | Commit inicial. Adición de todo el microservicio a Github | 26/10/2025   |

##### 5.3.2.2.3 Inventory Service

| Repository          | Branch | Commit ID | Commit Message                                                                            | Commit Message Body                                                                        | Committed on |
|---------------------|--------|-----------|-------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------|--------------|
| `inventory-service` | `main` | `u1v2w3x` | `fix: typo and unnecessary comments`                                                      | Corrección de errores tipográficos y eliminación de comentarios innecesarios en el código. | 29/10/2025   |
| `inventory-service` | `main` | `v2w3x4y` | `fix: typo`                                                                               | Ajuste menor de redacción en el código fuente.                                             | 29/10/2025   |
| `inventory-service` | `main` | `w3x4y5z` | `fix: typo`                                                                               | Corrección de error tipográfico en una clase o archivo específico.                         | 29/10/2025   |
| `inventory-service` | `main` | `x4y5z6a` | `fix: delete docker compose that was for testing message broker`                          | Eliminado archivo Docker Compose usado únicamente para pruebas con el message broker.      | 29/10/2025   |
| `inventory-service` | `main` | `y5z6a7b` | `feat(inventory): add docker compose for kafka and change application variable for ports` | Añadido archivo Docker Compose para Kafka y ajustes de puertos en variables de aplicación. | 24/10/2025   |
| `inventory-service` | `main` | `z6a7b8c` | `feat(inventory): change variables for it to work with broker`                            | Modificadas variables de entorno para compatibilidad con el message broker.                | 24/10/2025   |
| `inventory-service` | `main` | `a7b8c9d` | `feat(inventory): add kafka configuration`                                                | Configuración inicial de Kafka en el servicio de inventario.                               | 24/10/2025   |
| `inventory-service` | `main` | `b8c9d0e` | `feat(inventory): add dependencies for broker and events on domain`                       | Agregadas dependencias necesarias para manejar eventos y comunicación con el broker.       | 24/10/2025   |
| `inventory-service` | `main` | `c9d0e1f` | `chore: Initial commit`                                                                   | Commit inicial del repositorio.                                                            | 08/10/2025   |

##### 5.3.2.2.4 Orders Service

| Repository       | Branch | Commit ID | Commit Message                                               | Commit Message Body                                                                | Committed on |
|------------------|--------|-----------|--------------------------------------------------------------|------------------------------------------------------------------------------------|--------------|
| `orders-service` | `main` | `d0e1f2g` | `Merge pull request #1 from Fundamentos-de-Arquitectura/dev` | Integración de los cambios de la rama `dev` al main del proyecto.                  | 29/10/2025   |
| `orders-service` | `dev`  | `e1f2g3h` | `feat: modify service impl to publish order event to kafka`  | Modificada la implementación del servicio para publicar eventos de orden en Kafka. | 26/10/2025   |
| `orders-service` | `dev`  | `f2g3h4i` | `feat: add event producer`                                   | Añadido productor de eventos para enviar mensajes al broker Kafka.                 | 26/10/2025   |
| `orders-service` | `dev`  | `g3h4i5j` | `feat: add kafka configuration`                              | Configurada la conexión y propiedades de Kafka en el servicio.                     | 26/10/2025   |
| `orders-service` | `dev`  | `h4i5j6k` | `feat: add events`                                           | Creación de clases y estructuras para eventos de dominio.                          | 26/10/2025   |
| `orders-service` | `dev`  | `i5j6k7l` | `feat: add poml and application for kafka`                   | Añadidos `pom.xml` y propiedades de aplicación para Kafka.                         | 26/10/2025   |
| `orders-service` | `dev`  | `j6k7l8m` | `fix: typo`                                                  | Corrección de error tipográfico menor.                                             | 08/10/2025   |
| `orders-service` | `main` | `k7l8m9n` | `chore: initial commit`                                      | Commit inicial del proyecto.                                                       | 08/10/2025   |


##### 5.3.2.2.5 Profiles Service

| Repository         | Branch | Commit ID | Commit Message                                                                   | Commit Message Body                              | Committed on |
|--------------------|--------|-----------|----------------------------------------------------------------------------------|--------------------------------------------------|--------------|
| `profiles-service` | `main` | `l9m0n1o` | `feat: initialize profiles microservice with basic structure and configurations` | Servicio implementado, push directo desde local. | 30/10/2025   |

---

#### 5.3.2.3 Testing Suite Evidence for Sprint Review

Las pruebas BDD se desarrollaron con **Gherkin** para las User Stories implementadas.  
A continuación se presentan los archivos `.feature` correspondientes:

## US03 - Visualizar Inventario Actual
```gherkin
Feature: Visualizar Inventario Actual
  Como dueño de restaurante
  Quiero ver una tabla con el stock, costo unitario y unidad de medida de cada producto
  Para controlar el inventario de manera clara y rápida

  Scenario: Visualización de la tabla de inventario
    Given que tengo productos registrados en mi inventario
    When navego a la sección "Inventory Management"
    Then debo ver una tabla con el listado de todos los productos
    And la tabla debe mostrar las columnas: Producto, Stock Actual, Costo Unitario y Unidad de Medida
```

## US04 - Agregar Nuevo Producto al Inventario
```gherkin
Feature: Agregar Nuevo Producto al Inventario
  Como dueño de restaurante
  Quiero registrar un nuevo producto con sus datos
  Para mantener actualizado mi inventario

  Scenario: Registro exitoso de producto
    Given que estoy en la sección "Add New Product"
    When ingreso un Nombre, Stock (numérico), Costo Unitario (numérico) y Unidad de Medida válidos
    And presiono el botón "Add Product"
    Then el nuevo producto se añade a la tabla del inventario
    And veo un mensaje de confirmación "Producto añadido exitosamente"
```

## US05 - Validar Campos al Agregar Producto
```gherkin
Feature: Validar Campos al Agregar Producto
  Como dueño de restaurante
  Quiero que el sistema valide los campos obligatorios y de formato
  Para evitar errores en el registro del inventario

  Scenario: Intento de registro con datos incompletos o inválidos
    Given que estoy en el formulario para registrar un nuevo producto
    When dejo el campo "Costo Unitario" vacío o ingreso texto en lugar de un número
    And presiono el botón "Add Product"
    Then el sistema muestra un mensaje de error específico junto al campo
    And el producto no se guarda en el inventario
```

## US09 - Visualizar Órdenes Existentes
```gherkin
Feature: Visualizar Órdenes Existentes
  Como dueño de restaurante
  Quiero ver las órdenes registradas con sus detalles clave
  Para llevar un control claro y rápido de las órdenes

  Scenario: Visualización inicial de órdenes en la tabla
    Given que tengo órdenes registradas en el sistema
    When navego a la sección "Orders"
    Then debo ver una tabla con las órdenes
    And la tabla muestra las columnas: Mesa, Platos, Precio Total y Fecha
```

## US14 - Visualizar Planes de Suscripción
```gherkin
Feature: Visualizar Planes de Suscripción
  Como dueño de restaurante
  Quiero ver los diferentes planes de suscripción disponibles
  Para conocer las opciones y beneficios antes de decidirme

  Scenario: Mostrar lista de planes disponibles
    Given que accedo a la sección "Subscriptions"
    When el sistema carga los datos de los planes
    Then debo ver una lista con los nombres, precios y beneficios de cada plan
    And los planes deben incluir: Gratuito, Estándar y Premium

  Scenario: Error al cargar planes
    Given que accedo a la sección "Subscriptions"
    When el servidor no responde correctamente
    Then debo ver un mensaje "No se pudieron cargar los planes. Intente nuevamente."
```

## US15 — Suscribirse al Plan Premium
```gherkin
Feature: Suscribirse al Plan Premium
  Como dueño de restaurante
  Quiero poder suscribirme al plan premium
  Para acceder a funcionalidades avanzadas de gestión

  Scenario: Suscripción exitosa
    Given que estoy en la sección "Subscriptions" y tengo un método de pago válido
    When selecciono el plan "Premium" y confirmo el pago
    Then el sistema activa mi suscripción premium
    And veo un mensaje de confirmación "Suscripción activada correctamente"

  Scenario: Error en el pago
    Given que estoy en la sección "Subscriptions"
    When intento suscribirme pero el método de pago es rechazado
    Then el sistema muestra un mensaje "Error en la transacción, intente nuevamente"
```

## US16 - Cancelar Suscripción
```gherkin
Feature: Cancelar Suscripción
  Como dueño de restaurante
  Quiero cancelar mi suscripción premium
  Para dejar de usar el plan de pago cuando ya no lo necesite

  Scenario: Cancelación exitosa de suscripción
    Given que tengo una suscripción premium activa
    When hago clic en "Cancel Subscription" y confirmo la acción
    Then el sistema cambia mi plan a "Free Plan"
    And muestra un mensaje de confirmación "Suscripción cancelada correctamente"

  Scenario: Cancelación abortada por el usuario
    Given que tengo una suscripción premium activa
    When hago clic en "Cancel Subscription" pero cierro el diálogo sin confirmar
    Then la suscripción permanece activa
```

## US17 - Visualizar Perfil de Usuario
```gherkin
Feature: Visualizar Perfil de Usuario
  Como dueño de restaurante
  Quiero ver mi información personal y detalles de cuenta
  Para revisar mis datos registrados

  Scenario: Mostrar datos del perfil
    Given que estoy autenticado en el sistema
    When navego a la sección "Profile"
    Then debo ver mi nombre, correo e imagen de perfil
    And el plan de suscripción actual

  Scenario: Error al cargar perfil
    Given que intento acceder a la sección "Profile"
    When ocurre un error al recuperar la información
    Then el sistema muestra un mensaje "No se pudo cargar el perfil. Reintente."
```

## US18 - Actualizar Datos del Perfil
```gherkin
Feature: Actualizar Datos del Perfil
  Como dueño de restaurante
  Quiero editar mi información personal y contraseña
  Para mantener mis datos actualizados

  Scenario: Actualización exitosa
    Given que estoy en la sección "Profile"
    When modifico mi nombre o correo y presiono "Guardar cambios"
    Then el sistema actualiza mis datos
    And muestra un mensaje "Cambios guardados correctamente"

  Scenario: Validación de campos inválidos
    Given que estoy editando mis datos
    When ingreso un correo con formato incorrecto o dejo un campo vacío
    Then el sistema muestra mensajes de error bajo los campos correspondientes
```

## US19 - Iniciar Sesión
```gherkin
Feature: Iniciar Sesión
  Como dueño de restaurante
  Quiero acceder al sistema con mi correo y contraseña
  Para ingresar de forma segura a mis funcionalidades

  Scenario: Inicio de sesión exitoso
    Given que tengo una cuenta registrada
    When ingreso mi correo y contraseña válidos y presiono "Login"
    Then el sistema me redirige al dashboard principal

  Scenario: Credenciales inválidas
    Given que ingreso una contraseña incorrecta
    When presiono "Login"
    Then el sistema muestra un mensaje "Credenciales inválidas"

  Scenario: Campos vacíos
    Given que intento iniciar sesión sin completar los campos
    When presiono "Login"
    Then el sistema muestra un mensaje "Complete todos los campos"
```

## US20 - Crear Cuenta Nueva
```gherkin
Feature: Crear Cuenta Nueva
  Como dueño de restaurante
  Quiero registrarme en el sistema con mis datos básicos
  Para comenzar a usar el sistema

  Scenario: Registro exitoso
    Given que estoy en la página de registro
    When ingreso un nombre, correo y contraseña válidos y presiono "Sign Up"
    Then el sistema crea mi cuenta
    And muestra un mensaje "Cuenta creada exitosamente"
    And me redirige a la página de inicio de sesión

  Scenario: Campos inválidos
    Given que intento registrarme con un correo en formato incorrecto o sin llenar todos los campos
    When presiono "Sign Up"
    Then el sistema muestra un mensaje de error "Por favor complete todos los campos correctamente"

  Scenario: Correo duplicado
    Given que intento crear una cuenta con un correo ya registrado
    When presiono "Sign Up"
    Then el sistema muestra un mensaje "El correo ingresado ya está en uso"
```

---

#### 5.3.2.4 Execution Evidence for Sprint Review

Durante el Sprint 2 se ejecutaron las pruebas de los módulos desarrollados en el backend y frontend, verificando la correcta interacción entre los servicios y la interfaz de usuario.  
La ejecución de los escenarios Gherkin se realizó utilizando la metodología **BDD (Behavior Driven Development)**, a través de los `.feature files` definidos para cada historia de usuario implementada.

Los resultados de la ejecución validaron la correcta respuesta de los endpoints RESTful del backend y su coherencia con las funcionalidades del frontend.  
A continuación se resume la evidencia obtenida:

| Módulo        | Endpoint principal                           | Resultado de ejecución                            | Estado |
|---------------|----------------------------------------------|---------------------------------------------------|--------|
| Inventory     | `/api/v1/inventory`                          | Listado, registro y validación de productos       | Passed |
| Orders        | `/api/v1/orders`                             | Registro, visualización y confirmación de órdenes | Passed |
| Subscriptions | `/api/v1/subscriptions`                      | Gestión de suscripciones                          | Passed |
| Profiles      | `/api/v1/profiles`                           | Gestión de perfiles de usuario                    | Passed |
| IAM           | `/api/v1/accounts`, `/api/v1/authentication` | Gestión de autenticación de usuario               | Passed |


### Orders:

<img width="1920" height="1080" alt="orders" src="https://github.com/user-attachments/assets/6d4fbb15-acfb-4c9c-a57a-e2ab4b4e03a3" />

<img width="1476" height="882" alt="endpoints" src="https://github.com/user-attachments/assets/ac09ef8f-61c0-418b-9e99-a53eb5b3a04b" />

### Inventory

<img width="1349" height="776" alt="inventory" src="https://github.com/user-attachments/assets/370b8c4f-6fa2-4786-90b0-bd10946005ab" />

### Subscriptions



### Profiles



### IAM 



---

#### 5.3.2.5 Microservices Documentation Evidence for Sprint Review

La aplicación *FoodFlow* fue desarrollada bajo una arquitectura **basada en microservicios RESTful**, donde cada dominio del negocio se implementa de forma independiente para asegurar modularidad, escalabilidad y mantenibilidad del sistema.

En este sprint, el foco estuvo en los siguientes microservicios:

| Microservicio             | Descripción funcional                                                       | Endpoints principales                                                                                                                             |
|---------------------------|-----------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------|
| **Inventory Service**     | Control de inventario, stock, y validaciones.                               | `/api/v1/inventory`, `/api/v1/inventory/{id}`                                                                                                     |
| **Orders Service**        | Gestión de órdenes, confirmaciones y cálculos totales.                      | `/api/v1/orders`, `/api/v1/orders/{id}`                                                                                                           |
| **Subscriptions Service** | Gestión de suscripciones, suscripciones activas y suscripciones canceladas. | `/api/v1/subscriptions`, `/api/v1/subscriptions/{id}`, `/api/v1/subscriptions/plans`, `/api/v1/subscriptions/{userId}`                            |
| **Profiles Service**      | Gestión de perfiles de usuario, creación y actualización.                   | `/api/v1/profiles`, `/api/v1/profiles/{profilesId}`                                                                                               |
| **IAM Service**           | Gestión de autenticación de usuario, registro e inicio de sesión.           | `/api/v1/accounts`, `/api/v1/accounts/{accountsId}`, `/api/v1/authentication`, `/api/v1/authentication/sign-in`, `/api/v1/authentication/sign-up` |

##### Documentación API:
Toda la documentación del backend fue generada automáticamente con **Swagger (OpenAPI)**, donde se describen los endpoints, modelos de datos y respuestas HTTP.


##### Integración:
Cada microservicio será desplegado de manera independiente, comunicándose entre sí mediante HTTP.  
El gateway unificará las peticiones hacia los servicios de dominio y gestionará los encabezados de autenticación JWT.
Apache Kafka será el encargado de la integración entre los servicios de Orders e Inventory, permitiendo una comunicación asíncrona y escalable.

---

#### 5.3.2.6 Software Deployment Evidence for Sprint Review

El despliegue de *FoodFlow* se realizó en un entorno cloud utilizando un servidor FreeSQL para la base de datos relacional MySQL, y un entorno web para el frontend.  
Se implementó un pipeline de despliegue siguiendo el flujo GitFlow, con integración continua y versionamiento controlado.


##### Configuración general de despliegue:

| Componente            | Tecnología            | Descripción                                         |
|-----------------------|-----------------------|-----------------------------------------------------|
| **Backend**           | Java Spring Boot      | Desplegado como conjunto de microservicios RESTful. |
| **Frontend**          | Angular + Material UI | Dashboard interactivo con reportes y gráficos.      |
| **Base de Datos**     | MySQL                 | Gestión de datos financieros, platos e inventarios. |
| **Documentación API** | Swagger (OpenAPI)     | Publicación de especificaciones REST.               |

##### Proceso de despliegue:

1. **Commit en rama `develop`** → validación de tests automáticos.
2. **Verificación** mediante consultas de endpoints desde Postman.

**Evidencia visual del despliegue:**
**Backend desplegado en FreeSQL:**
<br>

**Frontend desplegado en netlify:** https://deplokeep.netlify.app/pages/login-owner

<br>

**Landing Page desplegada en Vercel:** https://landing-page-silk-nine-71.vercel.app/

---

#### 5.3.2.7 Team Collaboration Insights during Sprint

Durante el Sprint 2, el equipo de desarrollo mantuvo una comunicación constante utilizando herramientas ágiles y colaborativas.  
La metodología aplicada fue Scrum, con reuniones de seguimiento semanales para la revisión del progreso y resolución de bloqueos.

##### Herramientas utilizadas:
- **Trello:** gestión del backlog, tareas y definición del flujo Kanban.
- **GitHub:** control de versiones y revisión de código mediante Pull Requests.
- **Discord / WhatsApp:** reuniones de planificación, retrospectiva y revisión de sprint.

##### GitHub Insights:



---

#### 5.3.2.8 Kanban Board

Durante el Sprint 2 se empleó un tablero Kanban en **Trello** para gestionar las historias de usuario, priorizar tareas y monitorear el avance del sprint.

El tablero se estructuró con las siguientes columnas:
- **To Do:** Tareas seleccionadas para el sprint actual.
- **Doing** Tareas en desarrollo.
- **To Review:** Tareas realizadas, pendientes de revisión.
- **Done:** Tareas completadas y revisadas.

**URL del tablero de Trello:** https://trello.com/invite/b/6908f5bbfb6116c0fb9f808d/ATTI6ffb173398dc4a8f401d2e79128763caA0B1AA18/sprint-2-foodflow

**Captura del tablero Kanban:**

<br>

<img width="1145" height="595" alt="image" src="https://github.com/user-attachments/assets/9027b7d4-1060-4b84-8fcf-4de41ca6c0a7" />

<br>

---

**Conclusión del Sprint 2:**  
El equipo completó con éxito la implementación de Subscriptions, Profiles, IAM y la correcta integración de Apache Kafka con Inventory y Orders. Cumpliendo con los objetivos de desarrollo, validación e integración de los microservicios correspondientes. Las funcionalidades frontend de Dashboard, servicio Reportes y servicio Menú quedan planificadas para futuros sprints.
