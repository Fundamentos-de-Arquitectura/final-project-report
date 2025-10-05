# Capítulo V: Product Implementation, Validation & Deployment

El presente capítulo documenta el proceso de **implementación, validación y despliegue** del sistema *FoodFlow*, una aplicación web de gestión financiera para restaurantes desarrollada bajo una arquitectura de **microservicios RESTful**.  
El proyecto fue construido aplicando metodologías ágiles, asegurando un producto usable, seguro y de buen desempeño.

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

![WhatsApp Image 2025-10-05 at 12 12 29 PM](https://github.com/user-attachments/assets/f0f83d4f-a5d8-4867-ac9a-adeba86eb358)


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
      <td rowspan="2">US1</td>
      <td rowspan="2">Visualizar ingresos y pérdidas</td>
      <td>T1.1</td>
      <td>API: Ingresos y Pérdidas</td>
      <td>Desarrollar endpoint para obtener ingresos y pérdidas totales con variación.</td>
      <td>8</td>
      <td>Werner Lang</td>
      <td>Done</td>
    </tr>
    <tr>
      <td>T1.2</td>
      <td>FE: Dashboard Cards</td>
      <td>Implementar la visualización de los montos de ingresos y pérdidas en el dashboard de Angular.</td>
      <td>12</td>
      <td>Werner Lang</td>
      <td>Done</td>
    </tr>
    <tr>
      <td rowspan="2">US2</td>
      <td rowspan="2">Identificar platos más populares</td>
      <td>T2.1</td>
      <td>API: Top 5 Platos</td>
      <td>Crear el servicio backend para calcular y devolver el ranking de los 5 platos más vendidos.</td>
      <td>10</td>
      <td>Romina Maita</td>
      <td>Done</td>
    </tr>
    <tr>
      <td>T2.2</td>
      <td>FE: Gráfico Top Dishes</td>
      <td>Implementar la sección "Top 5 Dishes" en el dashboard con barras comparativas en Angular.</td>
      <td>14</td>
      <td>Romina Maita</td>
      <td>Done</td>
    </tr>
    <tr>
      <td rowspan="2">US3</td>
      <td rowspan="2">Visualizar inventario actual</td>
      <td>T3.1</td>
      <td>API: Listar Inventario</td>
      <td>Desarrollar el endpoint para obtener la lista completa de productos del inventario.</td>
      <td>6</td>
      <td>Paolo Torres</td>
      <td>Done</td>
    </tr>
    <tr>
      <td>T3.2</td>
      <td>FE: Tabla de Inventario</td>
      <td>Crear la tabla en Angular para mostrar stock, costo unitario y unidad de medida.</td>
      <td>10</td>
      <td>Paolo Torres</td>
      <td>Done</td>
    </tr>
    <tr>
      <td rowspan="2">US4</td>
      <td rowspan="2">Agregar un nuevo producto al inventario</td>
      <td>T4.1</td>
      <td>API: Agregar Producto</td>
      <td>Desarrollar el endpoint de POST para registrar un nuevo producto en la base de datos.</td>
      <td>8</td>
      <td>Paolo Torres</td>
      <td>Done</td>
    </tr>
    <tr>
      <td>T4.2</td>
      <td>FE: Formulario Agregar Producto</td>
      <td>Crear el formulario en Angular para ingresar los datos del nuevo producto y conectar con el backend.</td>
      <td>12</td>
      <td>Paolo Torres</td>
      <td>Done</td>
    </tr>
    <tr>
      <td rowspan="2">US5</td>
      <td rowspan="2">Validar campos al agregar producto</td>
      <td>T5.1</td>
      <td>BE: Validaciones de Producto</td>
      <td>Implementar validaciones en el backend para campos obligatorios y formato de datos (ej. costo unitario numérico).</td>
      <td>6</td>
      <td>Paolo Torres</td>
      <td>Done</td>
    </tr>
    <tr>
      <td>T5.2</td>
      <td>FE: Mensajes de Error</td>
      <td>Implementar validaciones y mensajes de error reactivos en el formulario de Angular.</td>
      <td>8</td>
      <td>Paolo Torres</td>
      <td>Done</td>
    </tr>
    <tr>
      <td rowspan="3">US6</td>
      <td rowspan="3">Agregar Nuevo Plato</td>
      <td>T6.1</td>
      <td>API: Registro de Plato</td>
      <td>Crear endpoint para agregar nuevos platos al menú con validaciones de campos.</td>
      <td>10</td>
      <td>Romina Maita</td>
      <td>Done</td>
    </tr>
    <tr>
      <td>T6.2</td>
      <td>FE: Formulario de Nuevo Plato</td>
      <td>Desarrollar el componente de formulario en Angular para registro de platos.</td>
      <td>12</td>
      <td>Romina Maita</td>
      <td>Done</td>
    </tr>
    <tr>
      <td>T6.3</td>
      <td>FE: Validación de Precio y Error</td>
      <td>Implementar la validación de formato de precio y mensajes de confirmación/error en el frontend (Angular).</td>
      <td>6</td>
      <td>Romina Maita</td>
      <td>Done</td>
    </tr>
    <tr>
      <td rowspan="2">US11</td>
      <td rowspan="2">Visualizar órdenes existentes</td>
      <td>T11.1</td>
      <td>API: Obtener Órdenes</td>
      <td>Crear endpoint para listar las órdenes (mesa, platos, precio total, fecha) ordenadas cronológicamente.</td>
      <td>8</td>
      <td>Romina Maita</td>
      <td>Done</td>
    </tr>
    <tr>
      <td>T11.2</td>
      <td>FE: Tabla de Órdenes</td>
      <td>Desarrollar la tabla en Angular para mostrar el historial de órdenes.</td>
      <td>10</td>
      <td>Romina Maita</td>
      <td>Done</td>
    </tr>
    <tr>
      <td>US12</td>
      <td>Registrar una nueva orden</td>
      <td>T12.1</td>
      <td>FE: Botón "New Order"</td>
      <td>Implementar el botón "New Order" en la vista de órdenes y configurar la navegación (routing en Angular) al formulario de creación.</td>
      <td>4</td>
      <td>Romina Maita</td>
      <td>Done</td>
    </tr>
    <tr>
      <td rowspan="2">US13</td>
      <td rowspan="2">Crear un nuevo orden</td>
      <td>T13.1</td>
      <td>FE: Formulario Creación de Orden</td>
      <td>Crear la interfaz de Angular para seleccionar mesa, plato y fecha, incluyendo la lógica para manejar la lista de detalles del orden.</td>
      <td>16</td>
      <td>Romina Maita</td>
      <td>Done</td>
    </tr>
    <tr>
      <td>T13.2</td>
      <td>API: Consultar Platos y Mesas</td>
      <td>Desarrollar endpoints para obtener la lista de platos y mesas disponibles (Dropdowns).</td>
      <td>6</td>
      <td>Romina Maita</td>
      <td>Done</td>
    </tr>
    <tr>
      <td rowspan="3">US14</td>
      <td rowspan="3">Confirmar un orden</td>
      <td>T14.1</td>
      <td>API: Guardar Orden</td>
      <td>Crear el endpoint de POST para registrar la orden final y sus detalles en la base de datos.</td>
      <td>10</td>
      <td>Romina Maita</td>
      <td>Done</td>
    </tr>
    <tr>
      <td>T14.2</td>
      <td>BE: Actualización de Inventario</td>
      <td>Implementar la lógica en el backend para descontar el stock de los productos del inventario al confirmar la orden.</td>
      <td>8</td>
      <td>Romina Maita</td>
      <td>Done</td>
    </tr>
    <tr>
      <td>T14.3</td>
      <td>FE: Botón Confirmar</td>
      <td>Implementar el botón "Confirm Order" y la llamada al servicio de guardar orden.</td>
      <td>4</td>
      <td>Romina Maita</td>
      <td>Done</td>
    </tr>
    <tr>
      <td>US16</td>
      <td>Calcular el total por plato</td>
      <td>T16.1</td>
      <td>FE: Cálculo Automático</td>
      <td>Implementar la lógica en el componente Angular para calcular y mostrar el precio total al cambiar la cantidad.</td>
      <td>5</td>
      <td>Romina Maita</td>
      <td>Done</td>
    </tr>
    <tr>
      <td>US17</td>
      <td>Cancelar creación de orden</td>
      <td>T17.1</td>
      <td>FE: Botón Cancelar</td>
      <td>Implementar la función de cancelar que limpia el formulario de la orden y retorna a la vista de historial de órdenes.</td>
      <td>3</td>
      <td>Romina Maita</td>
      <td>Done</td>
    </tr>
    <tr>
      <td rowspan="2">US18</td>
      <td rowspan="2">Visualización de Reportes Financieros Diarios</td>
      <td>T18.1</td>
      <td>API: Reporte Diario</td>
      <td>Desarrollar el endpoint para obtener ingresos y gastos diarios y su variación respecto al día anterior.</td>
      <td>12</td>
      <td>Werner Lang</td>
      <td>Done</td>
    </tr>
    <tr>
      <td>T18.2</td>
      <td>FE: Vista de Reportes Diarios</td>
      <td>Crear la interfaz de reportes en Angular para mostrar los datos diarios con la lógica de variación (+/-).</td>
      <td>15</td>
      <td>Werner Lang</td>
      <td>Done</td>
    </tr>
    <tr>
      <td rowspan="2">US19</td>
      <td rowspan="2">Navegación entre Períodos de Tiempo</td>
      <td>T19.1</td>
      <td>API: Reportes Semanales/Mensuales</td>
      <td>Crear endpoints que permitan agregar los datos de ingresos y gastos por semana y mes.</td>
      <td>10</td>
      <td>Werner Lang</td>
      <td>Done</td>
    </tr>
    <tr>
      <td>T19.2</td>
      <td>FE: Tabs de Navegación</td>
      <td>Implementar la navegación (Daily, Weekly, Monthly Tabs) en Angular para cambiar la vista de reportes.</td>
      <td>8</td>
      <td>Werner Lang</td>
      <td>Done</td>
    </tr>
  </tbody>
</table>

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

- US01 - Visualización de Ingresos y Pérdidas

```Feature: Visualización de Ingresos y Pérdidas
  Como dueño de restaurante
  Quiero ver en el dashboard un resumen de mis ingresos y pérdidas
  Para conocer rápidamente la situación financiera de mi negocio

  Scenario: Visualización de métricas financieras en el dashboard al acceder
    Given que soy un dueño de restaurante con datos financieros registrados
    When accedo a la pantalla principal del dashboard
    Then debo ver el monto total de Ingresos en un panel dedicado
    And debo ver el monto total de Pérdidas en un panel dedicado
    And debo ver el porcentaje de variación (+ o -) de los ingresos
```

- US02 - Identificar Platos Más Populares

```Feature: Identificar Platos Más Populares
  Como dueño de restaurante
  Quiero ver un listado de mis platos más vendidos
  Para saber cuáles son los más rentables y tomar decisiones sobre el menú

  Scenario: Visualización del Top 5 Dishes
    Given que accedo al dashboard
    When consulto la sección "Top 5 Dishes"
    Then debo ver un ranking de los 5 platos más populares
    And cada plato del ranking debe mostrar barras comparativas de ventas
```

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

- US06 - Agregar nuevo plato

```Feature: Gestión de Platos del Menú
  Como dueño de restaurante
  Quiero agregar nuevos platos al menú
  Para mantener mi oferta actualizada y atractiva

  Scenario: Agregar plato exitosamente con datos válidos
    Given que estoy en el formulario de gestión de menú
    When completo Nombre, Descripción y Precio con valores válidos
    And hago clic en el botón "Add Dish"
    Then el nuevo plato aparece en la lista de platos existentes
    And veo un mensaje de confirmación
    And los campos del formulario se resetean a su estado inicial

  Scenario: Validación de campos obligatorios al agregar plato
    Given que intento agregar un plato
    When intencionalmente no completo el campo "Nombre del Plato"
    And hago clic en el botón "Add Dish"
    Then el sistema muestra un mensaje de error indicando que el campo "Nombre del Plato" es requerido
    And el plato no se agrega a la lista

  Scenario: Validación de formato de precio
    Given que estoy en el formulario para agregar un nuevo plato
    When ingreso un valor de precio no numérico o negativo
    And hago clic en el botón "Add Dish"
    Then el sistema muestra un mensaje de error sobre el formato del precio
    And no me permite guardar el plato
```

- US11 - Visualización y Control de Órdenes Registradas

```Feature: Visualización y Control de Órdenes Registradas
  Como dueño de restaurante
  Quiero ver las órdenes registradas con sus detalles clave
  Para llevar un control claro y rápido de los órdenes

  Scenario: Visualización inicial de órdenes en la tabla
    Given que tengo órdenes registradas en el sistema
    When navego a la sección "Orders"
    Then debo ver una tabla con las órdenes
    And la tabla muestra las columnas: Mesa, Platos, Precio Total y Fecha

  Scenario: Las órdenes se muestran en orden cronológico
    Given que existen múltiples órdenes con diferentes fechas de registro
    When consulto la tabla de órdenes
    Then las órdenes están listadas en orden cronológico descendente por defecto (más recientes primero)

  Scenario: Identificación de detalles en cada orden
    Given que estoy visualizando la tabla de órdenes
    When reviso cualquier fila de la tabla
    Then puedo identificar claramente el número de mesa asociado a la orden
    And puedo ver el resumen de los platos pedidos en esa orden
```

- US12 - Inicio del Registro de una Nueva Orden

```Feature: Inicio del Registro de una Nueva Orden
  Como dueño de restaurante
  Quiero registrar una nueva orden
  Para mantener actualizado el historial de órdenes

  Scenario: Navegación al formulario de nueva orden
    Given que estoy en la sección "Orders"
    When hago clic en el botón principal "New Order"
    Then el sistema me redirige a la pantalla o formulario de "Add New Order"
```

- US13 - Detalle de Creación de Orden

```Feature: Detalle de Creación de Orden
  Como dueño de restaurante
  Quiero registrar los detalles de un nuevo orden (mesa, plato, fecha)
  Para llevar un control digital de las órdenes

  Scenario: Agregar un plato al detalle de la orden
    Given que estoy en la pantalla "Add New Order"
    When selecciono el Número de Mesa
    And selecciono un Plato del menú y su Cantidad
    Then el plato se añade a la tabla de detalles del orden
    And la tabla de detalles muestra la Cantidad, el Precio Unitario y el Precio Total calculado para ese plato
```

- US14 - Finalización y Registro de un Orden

```Feature: Finalización y Registro de un Orden
  Como dueño de restaurante
  Quiero confirmar un orden
  Para que quede registrado en el sistema y se actualice el inventario

  Scenario: Confirmación exitosa y registro del orden
    Given que he completado todos los detalles del orden con al menos un plato
    When presiono el botón "Confirm Order"
    Then el sistema guarda el orden completo en la base de datos
    And el stock de los productos utilizados se descuenta del inventario
    And el orden aparece en el historial de órdenes
```

- US16 - Cálculo Automático del Total de Plato

```Feature: Cálculo Automático del Total de Plato
  Como dueño de restaurante
  Quiero que el sistema calcule automáticamente el precio total de cada plato
  Para evitar errores de facturación manual

  Scenario: Cálculo de total basado en cantidad
    Given que he agregado el plato "Hamburguesa" con un Precio Unitario de $10.00 al detalle de la orden
    When cambio la Cantidad de 1 a 3
    Then el sistema actualiza automáticamente el campo "Precio Total" a $30.00
```

- US17 - Cancelación de Orden en Progreso

```Feature: Cancelación de Orden en Progreso
  Como dueño de restaurante
  Quiero poder cancelar la creación de un orden
  Para evitar guardar información incorrecta o incompleta

  Scenario: Cancelar y limpiar el formulario
    Given que he comenzado a registrar una orden con algunos platos agregados
    When presiono el botón "Cancel"
    Then el sistema limpia todos los datos del formulario de "Add New Order"
    And no se guarda ningún registro de orden en la base de datos
    And soy redirigido a la vista de historial de órdenes
```

- US18 - Visualización de Reportes Financieros Diarios

```Feature: Visualización de Reportes Financieros Diarios
  Como dueño de restaurante
  Quiero ver mis ingresos y gastos diarios y su comparación
  Para tomar decisiones informadas sobre el rendimiento financiero

  Scenario: Visualización del resumen de ingresos diarios
    Given que estoy en la sección "Reports"
    When selecciono la pestaña "Daily"
    Then debo ver el monto total de ingresos generados en el día
    And debo ver un indicador de porcentaje de cambio respecto al día anterior

  Scenario: Visualización del resumen de gastos diarios
    Given que estoy visualizando los reportes diarios
    When reviso la sección de gastos
    Then debo ver el monto total de gastos incurridos en el día
    And debo ver un indicador de porcentaje de cambio de gastos respecto al día anterior
```

- US19 - Navegación de Períodos de Tiempo en Reportes

```Feature: Navegación de Períodos de Tiempo en Reportes
  Como dueño de restaurante
  Quiero poder alternar entre reportes diarios, semanales y mensuales
  Para analizar tendencias en diferentes períodos de tiempo

  Scenario: Cambio de vista a reportes semanales
    Given que estoy en la vista de reportes diarios
    When hago clic en la pestaña de navegación "Weekly"
    Then los datos financieros se actualizan para mostrar el agregado de la semana actual
    And la pestaña "Weekly" permanece visualmente activa

  Scenario: Cambio de vista a reportes mensuales
    Given que estoy visualizando reportes semanales
    When hago clic en la pestaña de navegación "Monthly"
    Then los datos financieros se actualizan para mostrar el agregado del mes en curso
    And todos los gráficos y tablas muestran información mensual
```

---

#### 5.3.1.4 Execution Evidence for Sprint Review

Durante el Sprint 1 se ejecutaron las pruebas de los módulos desarrollados en el backend y frontend, verificando la correcta interacción entre los servicios y la interfaz de usuario.  
La ejecución de los escenarios Gherkin se realizó utilizando la metodología **BDD (Behavior Driven Development)**, a través de los `.feature files` definidos para cada historia de usuario implementada.

Los resultados de la ejecución validaron la correcta respuesta de los endpoints RESTful del backend y su coherencia con las funcionalidades del frontend.  
A continuación se resume la evidencia obtenida:

| Módulo        | Endpoint principal                              | Resultado de ejecución                                  | Estado |
|---------------|-------------------------------------------------|---------------------------------------------------------|--------|
| Autenticación | `/api/v1/auth/sign-up` / `/api/v1/auth/sign-in` | Registro e inicio de sesión exitosos                    | Passed |
| Dashboard     | `/api/v1/dashboard`                             | Visualización correcta de ingresos/pérdidas             | Passed |
| Inventario    | `/api/v1/inventory`                             | Listado, registro y validación de productos             | Passed |
| Menú          | `/api/v1/menu`                                  | Creación y validación de platos                         | Passed |
| Órdenes       | `/api/v1/orders`                                | Registro, visualización y confirmación de órdenes       | Passed |
| Reportes      | `/api/v1/reports`                               | Visualización de ingresos, gastos y navegación temporal | Passed |

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
<img width="1220" height="639" alt="image" src="https://github.com/user-attachments/assets/9b6ebaf0-f1e5-4178-b3cd-5617c76094a2" />


---

 **Conclusión del Sprint 1:**  
 El equipo completó la implementación de todos los módulos principales de *FoodFlow*, exceptuando las historias de usuario **US07, US08, US09, US10, US15 y US20**, las cuales quedaron planificadas para el siguiente sprint.  
 Se cumplieron los objetivos de desarrollo, validación e integración de los microservicios, logrando un producto funcional y listo para su despliegue controlado.
