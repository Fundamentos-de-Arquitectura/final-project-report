# Capítulo IV: Product Architecture Design

## 4.1 Desing Concepts, ViewPoints & ER Diagrams
El presente diseño se realiza aplicando el método Attribute-Driven Design (ADD), identificando principios, enfoques, estilos y patrones arquitectónicos
### 4.1.1 Principles Statements
Para el diseño de la arquitectura representada en el diagrama, se establecen los siguientes principios:

- **Desacoplamiento de microservicios**  
  Cada servicio (Suscripción, Reportes, IAM, Inventario, Orders, Profiles) se diseña y despliega de forma independiente, facilitando la escalabilidad horizontal, el mantenimiento y la posibilidad de reemplazar o actualizar componentes sin afectar al resto.

  Cada microservicio se desarrolla y despliega con su propia base de datos.
  Esto asegura independencia en el ciclo de vida y permite escalar o modificar servicios sin afectar al resto.

- **Seguridad por diseño**  
  Se integra la seguridad en todos los niveles, desde el API Gateway con Spring Cloud Gateway hasta la autenticación y autorización gestionadas por el servicio IAM.  
  Se utilizan conexiones seguras y políticas de acceso basadas en roles.

  Todas las funcionalidades de la plataforma requieren autenticación previa mediante login con JWT (JSON Web Tokens), gestionado por el microservicio IAM.
  Las contraseñas se almacenan con algoritmos de hash seguro y la comunicación entre frontend y backend se asegura mediante HTTPS/TLS.

  El message broker (Kafka) se configura con autenticación SASL y cifrado en tránsito.

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

Diagramas C4:

### 4.1.5 Relational/Non Relational Database Diagram

### 4.1.6 Design Patterns

A continuación, se describen los patrones de diseño qeu se aplicarán:

| Patrón                                | Propósito general                                                        | Aplicación en FoodFlow / microservicios                                                                                                                                           | Justificación contextual                                                                                                             |
|---------------------------------------|--------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------|
| **Singleton**                         | Garantizar que una clase tenga una única instancia                       | El microservicio **IAM** implementa un único punto de control para la autenticación, la emisión de tokens y la gestión de roles de usuario.                                       | Se asegura un manejo consistente y centralizado de la seguridad, evitando estados inconsistentes en la gestión de credenciales.      |
| **Factory Method / Abstract Factory** | Crear objetos de una familia sin depender de su implementación concreta  | El microservicio **Reports** utiliza fábricas para generar diferentes tipos de reportes (diario, semanal, mensual) y adaptadores de persistencia para distintas fuentes de datos. | Facilita la extensión de nuevos formatos de reportes sin modificar la lógica cliente, manteniendo flexibilidad en el procesamiento.  |
| **Strategy**                          | Encapsular algoritmos intercambiables detrás de una interfaz común       | En **Reports** se aplican diferentes estrategias de cálculo de pérdidas y ganancias, como costo promedio, según la necesidad del análisis financiero.                             | Permite cambiar o agregar métodos de cálculo sin alterar el resto del sistema, garantizando flexibilidad en el análisis contable.    |
| **Observer**                          | Permitir que objetos se suscriban a cambios en otro objeto               | El microservicio **Inventory** actualiza sus módulos internos de alertas cuando cambia el stock de un producto, notificando al dueño en caso de que un insumo se agote.           | Los componentes internos reaccionan automáticamente a cambios en inventario, generando alertas sin acoplarse directamente al núcleo. |
| **Decorator**                         | Agregar responsabilidades a objetos dinámicamente                        | El **API Gateway** y sus middleware incorporan validaciones adicionales como logging, control de acceso y verificación de tokens de forma dinámica.                               | Permite añadir capas transversales sin modificar la lógica base de enrutamiento, reforzando seguridad y trazabilidad de peticiones.  |
| **Adapter**                           | Permitir que clases con interfaces incompatibles trabajen juntas         | **Inventory** dispone de adaptadores que permiten integrar librerías externas de cálculo de costos o conectores con sistemas de terceros.                                         | Asegura compatibilidad con APIs externas y facilita la interoperabilidad con herramientas ajenas al núcleo de la plataforma.         |
| **Template Method**                   | Definir el esqueleto de un algoritmo dejando pasos concretos a subclases | En **Reports** se define un flujo general de generación de reportes, delegando pasos específicos como filtrado de datos o agregación a subclases especializadas.                  | Reduce duplicación de lógica común entre diferentes tipos de reportes, manteniendo un proceso estándar y extensible.                 |

### 4.1.7 Tactics
A continuación, se describen las tácticas arquitectónicas implementadas en la arquitectura de FoodFlow para abordar los atributos de calidad identificados:

**Seguridad**

- Autenticación y Autorización Centralizada: el microservicio IAM administra las credenciales de los dueños de restaurante mediante login con JWT, garantizando control de acceso uniforme.

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
### 4.2.X Iteration N: \<Iteration Name\>
#### 4.2.X.1 Architectural Design Backlog N
#### 4.2.X.2 Establish Iteration Goal by Selecting Drivers
#### 4.2.X.3 Choose One or More Elements of the System to Refine
#### 4.2.X.4 Choose One or More Design Concepts That Satisfy the Selected Drivers
#### 4.2.X.5 Instantiate Architectural Elements, Allocate Responsibilities, and Define Interfaces
#### 4.2.X.6 Sketch Views (C4 & UML) and Record Design Decisions
#### 4.2.X.7 Analysis of Current Design and Review Iteration Goal (Kanban Board)
