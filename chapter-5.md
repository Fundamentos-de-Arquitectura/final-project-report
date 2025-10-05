# Capítulo V: Product Implementation, Validation & Deployment

El presente capítulo documenta el proceso de **implementación, validación y despliegue** del sistema *FoodFlow*, una aplicación web de gestión financiera para restaurantes desarrollada bajo una arquitectura de **microservicios RESTful**.  
El proyecto fue construido aplicando metodologías ágiles, asegurando un producto usable, seguro y de buen desempeño.

---

## 5.1 Testing Suites & General Patterns

### 5.1.1 Backend Application Core Testing Suite

El backend de *FoodFlow* fue validado bajo el enfoque Behavior Driven Development (BDD), utilizando el lenguaje Gherkin para redactar escenarios de comportamiento legibles por humanos.  
Cada prueba se enfocó en verificar los criterios de aceptación de los **User Stories** implementados durante el Sprint 1.

### US01 - Visualizar ingresos y pérdidas - gherkin:

Feature: Visualización de ingresos y pérdidas
Como dueño de restaurante
Quiero ver en el dashboard un resumen de mis ingresos y pérdidas
Para conocer rápidamente la situación financiera de mi negocio

Scenario: Visualización de métricas financieras en el dashboard
Given que estoy en la pantalla principal del dashboard
When ingreso al sistema
Then puedo visualizar el monto total de ingresos y pérdidas
And el porcentaje de variación positiva o negativa

### US02 - Identificar platos más populares - gherkin:
Feature: Identificar platos más populares
Como dueño de restaurante
Quiero ver un listado de mis platos más vendidos
Para saber cuáles son los más rentables y tomar decisiones sobre el menú

Scenario: Visualización de Top 5 Dishes
Given que accedo al dashboard
When consulto la sección "Top 5 Dishes"
Then puedo ver un ranking de los platos más populares
And cada plato se muestra con su porcentaje de ventas y barra comparativa

### US03 - Visualizar inventario actual - gherkin:
Feature: Visualizar inventario actual
Como dueño de restaurante
Quiero ver una tabla con el stock, costo unitario y unidad de medida
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
When ingreso nombre, stock, costo unitario y unidad de medida válidos
And presiono "Add Product"
Then el producto se añade a la tabla del inventario con los datos correctos
And aparece un mensaje de confirmación

### US05 - Validar campos al agregar producto - gherkin:
Feature: Validar campos al agregar producto
Como dueño de restaurante
Quiero que el sistema valide los campos obligatorios
Para evitar errores en el registro del inventario

Scenario: Validación de campos obligatorios
Given que quiero registrar un producto
When dejo un campo vacío o ingreso un valor inválido
Then el sistema muestra un mensaje de error
And no permite guardar el producto hasta que la información sea correcta

### US06 - Agregar nuevo plato - gherkin:
Feature: Agregar nuevo plato
Como dueño de restaurante
Quiero agregar nuevos platos al menú
Para mantener mi oferta actualizada y atractiva

Scenario: Agregar plato exitosamente
Given que estoy en la página de gestión de menú
When completo el formulario con nombre, descripción y precio válidos
And hago clic en "Add Dish"
Then el nuevo plato se agrega a la lista de platos existentes
And aparece un mensaje de confirmación
And los campos del formulario se limpian automáticamente

Scenario: Validación de campos obligatorios
Given que intento agregar un plato sin completar todos los campos requeridos
When hago clic en "Add Dish"
Then el sistema muestra mensajes de error indicando los campos faltantes
And el plato no se agrega a la lista

Scenario: Validación de precio
Given que estoy completando el formulario de nuevo plato
When ingreso un precio inválido (negativo, texto o vacío)
Then el sistema muestra un mensaje de error sobre el formato del precio
And no permite guardar el plato hasta corregirlo

### US11 - Visualizar órdenes existentes - gherkin:
Feature: Visualización de órdenes existentes
Como dueño de restaurante
Quiero ver las órdenes registradas con mesa, platos, precio total y fecha
Para llevar un control claro y rápido de las órdenes

Scenario: Visualización inicial de órdenes
Given que estoy en la sección "Orders"
When ingreso al sistema
Then visualizo una tabla con las órdenes registradas mostrando mesa, platos, precio total y fecha

Scenario: Ordenamiento cronológico
Given que existen múltiples órdenes en diferentes fechas
When consulto la tabla de órdenes
Then las órdenes deben aparecer en orden cronológico descendente

Scenario: Identificación de órdenes por mesa
Given que visualizo las órdenes registradas
When reviso cada fila
Then puedo identificar la mesa y los platos correspondientes

### US12 - Registrar una nueva orden - gherkin:
Feature: Registrar una nueva orden
Como dueño de restaurante
Quiero registrar una nueva orden desde el botón "New Order"
Para mantener actualizado el historial de órdenes

Scenario: Navegación al formulario de nueva orden
Given que estoy en la sección "Orders"
When hago clic en el botón "New Order"
Then el sistema me lleva al formulario para registrar la nueva orden

### US13 - Crear un nuevo orden - gherkin:
Feature: Crear un nuevo orden
Como dueño de restaurante
Quiero registrar una nueva orden seleccionando mesa, plato y fecha
Para llevar un control digital de las órdenes

Scenario: Registro de nueva orden
Given que estoy en la pantalla "Add New Order"
When selecciono número de mesa, plato y fecha
Then el sistema muestra el orden en la tabla de detalles
And se visualizan cantidad, precio unitario y total

### US14 - Confirmar un orden - gherkin:
Feature: Confirmar un orden
Como dueño de restaurante
Quiero confirmar un orden
Para que quede registrado en el sistema y se actualice el inventario

Scenario: Confirmación exitosa del orden
Given que he agregado uno o más platos al orden
When presiono el botón "Confirm Order"
Then el orden se guarda en la base de datos
And se refleja en el historial de órdenes

### US16 - Calcular el total por plato - gherkin:
Feature: Calcular el total por plato
Como dueño de restaurante
Quiero que el sistema calcule automáticamente el precio total de cada plato
Para evitar errores de facturación manual

Scenario: Cálculo automático de total
Given que agrego un plato con precio unitario
When especifico la cantidad
Then el sistema calcula y muestra el precio total en la columna correspondiente

### US17 - Cancelar creación de orden - gherkin:
Feature: Cancelar creación de orden
Como dueño de restaurante
Quiero cancelar la creación de una orden
Para no guardar información incorrecta

Scenario: Cancelar formulario
Given que estoy en el formulario "Add New Order"
When presiono el botón "Cancel"
Then el sistema limpia los datos sin guardar nada en la base

### US18 - Visualización de reportes financieros diarios - gherkin:
Feature: Reportes financieros diarios
Como dueño de restaurante
Quiero ver mis ingresos y gastos diarios en un dashboard
Para tomar decisiones informadas sobre el rendimiento financiero

Scenario: Visualización de ingresos diarios
Given que estoy en la sección "Reports"
When selecciono la pestaña "Daily"
Then debo ver el total de ingresos del día
And el porcentaje de cambios respecto al día anterior

Scenario: Visualización de gastos diarios
Given que estoy en la sección de reportes diarios
When reviso la sección de gastos
Then debo ver el total de gastos del día
And el porcentaje de cambios respecto al día anterior

### US19 - Navegación entre períodos de tiempo - gherkin:
Feature: Navegación entre períodos de tiempo
Como dueño de restaurante
Quiero alternar entre reportes diarios, semanales y mensuales
Para analizar tendencias en diferentes períodos

Scenario: Cambio a vista semanal
Given que estoy visualizando reportes diarios
When hago clic en la pestaña "Weekly"
Then debo ver los datos financieros agregados de la semana actual
And la pestaña "Weekly" debe aparecer como activa

Scenario: Cambio a vista mensual
Given que estoy en cualquier vista de reportes
When selecciono la pestaña "Monthly"
Then debo ver los datos financieros del mes en curso
And los gráficos deben actualizarse con datos mensuales

---

### 5.1.2 Pattern Based Backend Application(s)

La arquitectura del backend se diseñó siguiendo patrones de diseño estándar para mantener una estructura modular y desacoplada:

- **Repository Pattern:** abstrae la comunicación con la base de datos MySQL.
- **Service Layer Pattern:** centraliza la lógica de negocio.
- **Controller Pattern:** gestiona las rutas y la lógica de entrada/salida de datos.
- **DTO (Data Transfer Object):** facilita la transferencia segura de información.
- **Factory Pattern:** estandariza la creación de instancias de entidades.
- **Dependency Injection:** mejora la testabilidad y reduce el acoplamiento.

Estos patrones se aplicaron uniformemente en los microservicios de **autenticación**, **menú**, **ventas** y **reportes financieros**.

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

<!-- Inserta aquí la imagen del diagrama de despliegue -->

---

## 5.3 Microservices Implementation

### 5.3.1 Sprint 1

#### 5.3.1.1 Sprint Backlog 1

**Objetivo del Sprint:**  
Construir la base funcional del sistema, implementando los módulos de autenticación, menú, ventas y dashboard financiero.

**Avance general:**  
En este sprint se completaron todas las historias de usuario planificadas, **excepto las US07, US08, US09, US10, US15 y US20**, que quedaron pendientes para el siguiente sprint.  
El resto fueron implementadas, probadas y documentadas conforme a los criterios de aceptación.

**Herramienta utilizada:** Trello  
**URL del tablero:** 
https://trello.com/invite/b/68acc5a626e4614f12ee778f/ATTIb0400bfaba199996d200f641b89abc646996230A/arquitectura-de-software

---

#### 5.3.1.2 Development Evidence for Sprint Review

| Repository         | Branch                   | Commit ID | Commit Message                                          | Commit Message Body                                                | Committed on |
|--------------------|--------------------------|-----------|---------------------------------------------------------|--------------------------------------------------------------------|--------------|
| `foodflow-backend` | `feature/auth-service`   | `a14be92` | `feat: implement user registration and login endpoints` | Added authentication controller, JWT service, and database models. | 02/09/2025   |
| `foodflow-backend` | `feature/menu-module`    | `b21cf34` | `feat: add menu CRUD operations`                        | Implemented menu management endpoints.                             | 03/09/2025   |
| `foodflow-backend` | `feature/sales-module`   | `b32dd11` | `feat: add sales registration endpoint`                 | Added controller and service for daily sales registration.         | 04/09/2025   |
| `foodflow-backend` | `feature/reports-module` | `b41ac72` | `feat: dashboard report endpoint`                       | Implemented financial dashboard aggregation.                       | 04/09/2025   |

---

#### 5.3.1.3 Testing Suite Evidence for Sprint Review

Las pruebas BDD se desarrollaron con **Gherkin** para las User Stories implementadas.  
A continuación se presentan los archivos `.feature` correspondientes:



#### 5.3.1.4 Execution Evidence for Sprint Review

Durante el Sprint 1 se ejecutaron las pruebas de los módulos desarrollados en el backend y frontend, verificando la correcta interacción entre los servicios y la interfaz de usuario.  
La ejecución de los escenarios Gherkin se realizó utilizando la metodología **BDD (Behavior Driven Development)**, a través de los `.feature files` definidos para cada historia de usuario implementada.

Los resultados de la ejecución validaron la correcta respuesta de los endpoints RESTful del backend y su coherencia con las funcionalidades del frontend.  
A continuación se resume la evidencia obtenida:

| Módulo        | Endpoint principal                              | Resultado de ejecución                                  | Estado   |
|---------------|-------------------------------------------------|---------------------------------------------------------|----------|
| Autenticación | `/api/v1/auth/sign-up` / `/api/v1/auth/sign-in` | Registro e inicio de sesión exitosos                    | ✅ Passed |
| Dashboard     | `/api/v1/dashboard`                             | Visualización correcta de ingresos/pérdidas             | ✅ Passed |
| Inventario    | `/api/v1/inventory`                             | Listado, registro y validación de productos             | ✅ Passed |
| Menú          | `/api/v1/menu`                                  | Creación y validación de platos                         | ✅ Passed |
| Órdenes       | `/api/v1/orders`                                | Registro, visualización y confirmación de órdenes       | ✅ Passed |
| Reportes      | `/api/v1/reports`                               | Visualización de ingresos, gastos y navegación temporal | ✅ Passed |

**Capturas de ejecución de pruebas:**
<!-- Insertar imágenes de ejecución de pruebas de Postman, Jest o Cucumber -->
<!-- ![Ejecución de pruebas backend](URL_AQUI) -->
<!-- ![Ejecución de pruebas frontend](URL_AQUI) -->

---

#### 5.3.1.5 Microservices Documentation Evidence for Sprint Review

La aplicación *FoodFlow* fue desarrollada bajo una arquitectura **basada en microservicios RESTful**, donde cada dominio del negocio se implementa de forma independiente para asegurar modularidad, escalabilidad y mantenibilidad del sistema.

##### Estructura de microservicios:

| Microservicio         | Descripción funcional                                              | Endpoints principales                          |
|-----------------------|--------------------------------------------------------------------|------------------------------------------------|
| **Auth Service**      | Manejo de registro, login y autenticación JWT.                     | `/api/v1/auth/sign-up`, `/api/v1/auth/sign-in` |
| **Menu Service**      | CRUD de platos del menú.                                           | `/api/v1/menu`, `/api/v1/menu/{id}`            |
| **Inventory Service** | Control de inventario, stock, y validaciones.                      | `/api/v1/inventory`, `/api/v1/inventory/{id}`  |
| **Orders Service**    | Gestión de órdenes, confirmaciones y cálculos totales.             | `/api/v1/orders`, `/api/v1/orders/{id}`        |
| **Reports Service**   | Generación de reportes financieros diarios, semanales y mensuales. | `/api/v1/reports`                              |

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
<!-- Insertar aquí capturas de GitHub Insights -->

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
<!-- ![Kanban Board Sprint 1](URL_AQUI) -->

---

 **Conclusión del Sprint 1:**  
 El equipo completó la implementación de todos los módulos principales de *FoodFlow*, exceptuando las historias de usuario **US07, US08, US09, US10, US15 y US20**, las cuales quedaron planificadas para el siguiente sprint.  
 Se cumplieron los objetivos de desarrollo, validación e integración de los microservicios, logrando un producto funcional y listo para su despliegue controlado.