# Capítulo IV: Product Architecture Design

## 4.1 Desing Concepts, ViewPoints & ER Diagrams
El presente diseño se realiza aplicando el método Attribute-Driven Design (ADD) v3 del SEI, identificando principios, enfoques, estilos y patrones arquitectónicos
### 4.1.1 Principles Statements
Para el diseño de la arquitectura representada en el diagrama, se establecen los siguientes principios:

- **Desacoplamiento de microservicios**  
  Cada servicio (Suscripción, Reportes, IAM, Inventario, Orders, Profiles) se diseña y despliega de forma independiente, facilitando la escalabilidad horizontal, el mantenimiento y la posibilidad de reemplazar o actualizar componentes sin afectar al resto.
<br><br>
- **Seguridad por diseño**  
  Se integra la seguridad en todos los niveles, desde el API Gateway con Spring Cloud Gateway hasta la autenticación y autorización gestionadas por el servicio IAM.  
  Se utilizan conexiones seguras y políticas de acceso basadas en roles.
  <br><br>
- **Escalabilidad y alta disponibilidad**  
  Los contenedores de base de datos MySQL y el Message Broker (Apache Kafka) se preparan para replicación y balanceo, garantizando tolerancia a fallos y crecimiento según demanda.
  <br><br>
- **Reutilización de componentes**  
  La lógica de negocio común se abstrae en bibliotecas y APIs compartidas entre los microservicios para evitar duplicación de código y facilitar la coherencia en las respuestas.
  <br><br>
- **Observabilidad y monitoreo**  
  Se prioriza la incorporación de métricas, trazas y logs centralizados que permitan detectar incidentes y facilitar el mantenimiento preventivo.
  <br><br>
- **Consistencia de interfaz**  
  La *Single Page Application* en Angular mantiene un diseño coherente y unificado para los usuarios finales, integrando la Landing Page con las distintas funcionalidades sin interrumpir la experiencia de navegación.


### 4.1.2 Approaches Statements, Architectural Styles & Patterns

#### Approaches Statements
- **Domain-Driven Design (DDD)**  
  La lógica del dominio se modela en microservicios que representan contextos bien definidos (orders, reports, inventory, etc.), reflejando fielmente las reglas del negocio.
  <br><br>
- **Attribute-Driven Design (ADD)**  
  Las decisiones de arquitectura priorizan atributos de calidad como usabilidad, seguridad y desempeño, asegurando que los servicios cumplan los requisitos no funcionales.
  <br><br>
- **Event-Driven Architecture**  
  La comunicación asíncrona mediante Apache Kafka permite el desacoplamiento de procesos y la respuesta eficiente a eventos de negocio, especialmente en módulos de Orders e Inventario.
  <br><br>
#### Architectural Styles
- **Microservicios**  
  La aplicación se divide en servicios independientes, cada uno con su propia base de datos y ciclo de vida, comunicándose a través de APIs y mensajería.
  <br><br>
- **Cliente-Servidor**  
  El frontend (Landing Page + SPA Angular) actúa como cliente que consume los servicios expuestos en el backend.
  <br><br>
- **API Gateway Pattern**  
  Spring Cloud Gateway centraliza la entrada de las solicitudes, proporcionando enrutamiento, autenticación y balanceo de carga.
  <br><br>
#### Patrones
- **Modelo-Vista-Controlador (MVC)**  
  Implementado en el frontend Angular para mantener la separación de responsabilidades entre la vista, la lógica de presentación y los datos.
  <br><br>
- **Circuit Breaker**  
  Para manejar fallos temporales en la comunicación entre microservicios y garantizar resiliencia.
  <br><br>
- **Database per Service**  
  Cada microservicio cuenta con su propia base de datos MySQL para asegurar independencia de datos y evitar cuellos de botella.
  <br><br>
- **Publisher-Subscriber**  
  Apache Kafka permite la distribución de eventos a múltiples consumidores, clave para la actualización de inventarios en tiempo real.
  <br><br>
### 4.1.3 Context Diagram

<img width="276" height="557" alt="image" src="https://github.com/user-attachments/assets/fbb14dd3-77b0-4e1d-b029-77c18f19b80f" />


### 4.1.4 Approach Driven ViewPoints Diagrams
### 4.1.5 Relational/Non Relational Database Diagram
### 4.1.6 Design Patterns
### 4.1.7 Tactics

## 4.2 Architectural Drivers
### 4.1.8 Design Purpose
### 4.1.9 Primary Functionality (Primary User Stories)
### 4.1.10 Quality Attribute Scenarios
### 4.1.11 Constraints
### 4.1.12 Architectural Concerns

## 4.3 ADD Iterations
### 4.2.X Iteration N: \<Iteration Name\>
#### 4.2.X.1 Architectural Design Backlog N
#### 4.2.X.2 Establish Iteration Goal by Selecting Drivers
#### 4.2.X.3 Choose One or More Elements of the System to Refine
#### 4.2.X.4 Choose One or More Design Concepts That Satisfy the Selected Drivers
#### 4.2.X.5 Instantiate Architectural Elements, Allocate Responsibilities, and Define Interfaces
#### 4.2.X.6 Sketch Views (C4 & UML) and Record Design Decisions
#### 4.2.X.7 Analysis of Current Design and Review Iteration Goal (Kanban Board)
