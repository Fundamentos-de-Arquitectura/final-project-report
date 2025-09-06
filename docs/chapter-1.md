# Capítulo I: Introducción
## 1.1 Startup Profile
### 1.1.1 Descripción de la Startup
### 1.1.2 Perfiles de integrantes del equipo

|                               | Apellido y Nombre                                 | Carrera                | Acerca de                                                                                                                                                                                                                                                                         | Habilidades                                                                                                                                                                        |
|-------------------------------|---------------------------------------------------|------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ![romina](/assets/romina.png) | Maita Falckenheiner, Romina Guadalupe(u202213765) | Ingeniería de Software | Soy estudiante a tiempo completo, me apasiona programar y me gustaría especializarme en Data Science o Desarrollo de Videojuegos en un futuro.                                                                                                                                    | Liderazgo, competitividad y organización.                                                                                                                                          |
| ![werner](/assets/werner.png) | Lang Nassi, Werner Khalil(u202310003)             | Ingeniería de Software | Estudiante de la Universidad Peruana de Ciencias Aplicadas (UPC), cursando en 6.º ciclo. Soy un estudiante que le gusta investigar cosas nuevas.                                                                                                                                  | Investigador, Innovador, Analista, Cooperativo.                                                                                                                                    |
| ![paolo](/assets/paolo.png)   | Torres Flores, Paolo Alessandro(u20221f613)       | Ingeniería de Software | Soy un estudiante de 7mo ciclo con afán de mejorar cada día que pasa. Tengo muchas aspiraciones, ya sea en mi carrera como personalmente, y lucho diariamente para acercarme cada vez más a ellas.                                                                                | Me considero una persona adaptable al entorno, sé trabajar en equipo y aprendo rápido. Mentalidad para resolver problemas. Conocimiento básico de las funcionalidades de software. |

## 1.2 Solution Profile
La solución propuesta es una aplicación web de gestión financiera para restaurantes, diseñada específicamente para brindar a los dueños de restaurantes una herramienta clara, simple y eficiente que les permita controlar y optimizar la administración de su negocio.

La plataforma integra diferentes módulos que resuelven los principales puntos de dolor del segmento objetivo:

Dashboard financiero con reportes diarios, semanales y mensuales que muestran de manera visual las ganancias, pérdidas y tendencias de ventas.

Gráficos que destacan los platos más vendidos y los de menor rotación, facilitando la toma de decisiones estratégicas.

Exportación a Excel para que los dueños tengan un respaldo de su información y puedan compartirla fácilmente con contadores o socios.

Gestión del menú, con opción de agregar, editar o eliminar platos de manera rápida.

Vista de ventas por mesa, que permite conocer en detalle qué se vende, dónde y en qué volumen.

Notificaciones inteligentes que alertan cuando un producto o insumo está cerca de agotarse, reduciendo pérdidas por falta de stock o exceso de compras.

El perfil de la solución se centra en la usabilidad y accesibilidad, ofreciendo una herramienta intuitiva para usuarios que no necesariamente dominan la contabilidad o los sistemas complejos. Al enfocarse en reportes visuales, alertas automáticas y funciones prácticas, la aplicación se convierte en un aliado estratégico para mejorar la rentabilidad, reducir desperdicios y dar mayor control a los dueños de restaurantes sobre su operación diaria.
### 1.2.1 Nombre del producto

### 1.2.2 Antecedentes y problemática

### 1.2.3 Lean UX Process
En esta sección, se presenta el proceso de Lean UX que se ha seguido para el desarrollo de la plataforma KeepItFresh. Este proceso incluye la creación de un Lean UX Problem Statement, Assumptions, Hypothesis Statements y un Lean UX Canvas. El objetivo es definir claramente el problema que se busca resolver, las suposiciones que se tienen sobre los usuarios y el producto, así como las hipótesis que guiarán el desarrollo del mismo.

#### 1.2.3.1 Lean UX Problem Statement
En el entorno laboral actual, los restaurantes enfrentan dificultades para monitorear y gestionar sus finanzas de manera efectiva. Esto debido a la falta de herramientas adecuadas y el trabajo tedioso que esto suele ser, para hacerlo de forma continua y detallada. 
Lo cual da a una falta de visibilidad y datos concretos que puede llevar a gastos innecesarios.<br>

El desafío que hay actualmente es que las soluciones actuales no proveen este servicio a un precio accesible y ,al mismo tiempo, mantienen una alta calidad de atención.<br>

¿Cómo podemos ofrecer a los restaurantes una plataforma con un precio accesible que al mismo tiempo ofrezca la calidad que estos necesitan?

#### 1.2.3.2 Lean UX Assumptions

**Business Assumptions**

1. Creemos que los dueños de restaurantes están dispuestos a pagar una suscripción mensual si la aplicación les ayuda a reducir pérdidas y aumentar ganancias.
2. Creemos que los dueños prefieren una herramienta digital antes que llevar la contabilidad con Excel o en cuadernos.
3. Creemos que los restaurantes pequeños y medianos son el segmento inicial que más valorará la solución.
4. Creemos que nuestra mayor competencia serán los sistemas de punto de venta (POS) que ofrecen reportes financieros básicos.
5. Creemos que la adopción y retención dependerán de que el dueño use la aplicación de forma constante para revisar finanzas y tomar decisiones.

**User Assumptions**

1. Creemos que los dueños necesitan reportes claros de ganancias y pérdidas para tener control real del negocio.
2. Creemos que los dueños requieren identificar los platos más vendidos para ajustar el menú y maximizar rentabilidad.
3. Creemos que los dueños valoran la opción de exportar reportes a Excel porque facilita compartir la información con socios o contadores.
4. Creemos que los dueños necesitan alertas cuando un insumo está por agotarse para evitar quiebres de stock.
5. Creemos que los dueños prefieren visualizar la información en gráficos y paneles consolidados en lugar de revisar tablas largas.

**Solution Assumptions**

1. Creemos que un panel con gráficos de ingresos, gastos y utilidades permitirá al dueño entender mejor la salud financiera de su restaurante
2. Creemos que la función de exportar a Excel será clave para la adopción, ya que se integra con su forma de trabajo actual.
3. Creemos que mostrar reportes históricos (diarios, semanales y mensuales) ayudará al dueño a tomar decisiones estratégicas.
4. Creemos que incluir un módulo para registrar y analizar ventas de platos permitirá al dueño identificar patrones de consumo.
5. Creemos que un sistema de notificaciones sobre inventario crítico reducirá pérdidas y mejorará la planificación de compras.

#### 1.2.3.3 Lean UX Hypothesis

**Business Hypotheses**

1. Creemos que ofrecer la app como suscripción mensual para dueños de restaurantes logrará ingresos recurrentes.
Sabremos que esto es cierto cuando al menos el 60% de los usuarios de prueba se conviertan en clientes de pago después de 30 días.

2. Creemos que simplificar la gestión financiera en un solo dashboard logrará que los dueños perciban la app como indispensable.
Sabremos que esto es cierto cuando al menos el 70% de los usuarios ingresen al dashboard 3 o más veces por semana.

3. Creemos que exportar a Excel permitirá mayor adopción de la app en restaurantes pequeños y medianos.
Sabremos que esto es cierto cuando más del 50% de los usuarios exporten al menos un reporte en el primer mes.

**User Hypotheses**

Creemos que al mostrar reportes de ganancias y pérdidas, los dueños tomarán decisiones más rápidas. 
Sabremos que esto es cierto cuando el 80% de los usuarios consulte reportes semanales sin apoyo de otras herramientas externas.

Creemos que un ranking de platos más vendidos ayudará al dueño a optimizar el menú.
Sabremos que esto es cierto cuando al menos el 50% de los usuarios haga ajustes de menú en los primeros 2 meses.

Creemos que las notificaciones de inventario crítico ayudarán a los dueños a reducir quiebres de stock.
Sabremos que esto es cierto cuando los usuarios reporten una disminución de al menos 30% en pedidos no atendidos por falta de insumos.

**Solution Hypotheses**

1. Creemos que consolidar los reportes financieros (diarios, semanales y mensuales) en gráficos fáciles de leer logrará mayor claridad en el control del negocio.
Sabremos que esto es cierto cuando el 70% de los usuarios valore positivamente la usabilidad de los gráficos en encuestas de satisfacción.

2. Creemos que incluir el módulo de ventas por plato permitirá al dueño identificar patrones de consumo.
Sabremos que esto es cierto cuando al menos el 40% de los usuarios lo consulte semanalmente en los primeros 3 meses.

3. Creemos que un sistema de alertas en tiempo real (notificaciones) ayudará al dueño a planificar mejor las compras.
Sabremos que esto es cierto cuando el 60% de los usuarios reduzca en un 20% sus compras de emergencia durante el primer trimestre.
#### 1.2.3.4 Lean UX Canvas

## 1.3 Segmentos objetivo
**Dueños de restaurante:**
El segmento objetivo del proyecto son los dueños de restaurantes, ya que son quienes enfrentan directamente la necesidad de tener un mayor control sobre las finanzas de su negocio. En la gestión diaria de un restaurante, los propietarios deben tomar decisiones rápidas sobre costos, precios de los platos, control de inventario y rentabilidad. Sin embargo, muchas veces carecen de herramientas simples y accesibles que les brinden información en tiempo real y de manera visual.

Este proyecto se enfoca en ellos porque:
- Son los principales responsables de las finanzas y de la toma de decisiones estratégicas.
- Necesitan optimizar el control de ganancias y pérdidas sin depender de procesos manuales o conocimientos contables avanzados.
- Requieren una solución que les permita ahorrar tiempo y reducir errores, a través de reportes automáticos y visualizaciones claras.
- Valoran funciones prácticas como las notificaciones de inventario bajo, que impactan directamente en la reducción de desperdicios y en la continuidad del servicio.
