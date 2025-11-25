## Modelos y Documentación del Software_ Proyecto: Gestión de Reservas Hotel El Bosque

## Autores: Jairo Andres Rincon Blanco y Andres Camilo Cuvides Ortega

## Docente: Hely Suarez Marin


1. Descripción General del Sistema y Objetivos

El Sistema de Gestión de Reservas Hoteleras para el Hotel El Bosque tiene como objetivo principal automatizar y centralizar los procesos de interacción con el huésped, gestión de habitaciones y administración de tarifas.

2. Modelos de Estructura y Estáticos

2.1. Diagrama de Clases (Clase Diagram)

Este es el corazón estructural del sistema, modelando las entidades del dominio de negocio. Cada clase, con sus atributos y relaciones, se traduce directamente en las tablas de la base de datos, sirviendo como puente entre el análisis y la implementación real.

El modelo incluye clases fundamentales como Reserva, Habitacion, Huesped y Factura, reflejando los conceptos esenciales del negocio.

- Estructura del Modelo

Clase

Atributos Clave (Restricciones)

Métodos/Operaciones

Habitacion

idHabitacion: int [PK], numero: varchar [Unique], tipoId: int [FK], estado: enum

cambiarEstado(), obtenerDisponibilidad()

Reserva

idReserva: int [PK], huespedId: int [FK], fechaLlegada: date, estado: enum

confirmarReserva(), calcularCosto()

Factura

idFactura: int [PK], reservaId: int [FK, Unique], total: decimal, estadoPago: enum

generarFactura(), procesarPago()

Relación entre Clases: Las asociaciones clave son la relación $1 \longrightarrow *$ (uno a muchos) entre Huesped y Reserva (un huésped realiza muchas reservas), y la relación $1 \longrightarrow 1$ (uno a uno) entre Reserva y Factura (cada reserva genera exactamente una factura).

2.2. Diagrama de Objetos (Object Diagram)

Similar al diagrama de clases, el diagrama de objetos presenta instancias concretas de las clases en un momento específico. Su función es validar que las relaciones entre las entidades son coherentes y que el modelo puede ser "poblado" con datos reales.

- Instancia de Ejemplo

Huesped: Juan Pérez (id: 502, email: juan.p@mail.com)

Reserva: #99 (id: 99, llegada: 2025-12-01, estado: Ocupada)

Habitacion: #101 (número: 101, estado: Ocupada)

El objeto Huesped: Juan Pérez está asociado a la Reserva: #99, la cual a su vez tiene asignada la Habitacion: #101.

2.3. Diagrama de Paquetes (Package Diagram)

Este diagrama organiza el sistema en módulos lógicos para facilitar la escalabilidad y el mantenimiento.

Paquete Presentación: Contiene la interfaz web para el huésped y la terminal de la recepcionista.

Paquete Lógica de Negocio: Encapsula la lógica de los Módulo Reservas, Módulo Habitaciones, y Módulo Facturación.

Paquete Seguridad: Maneja la autenticación, roles y permisos.

Paquete Persistencia: Representa la capa de acceso a la Base de Datos.

3. Modelos Funcionales y de Comportamiento

3.1. Diagrama de Casos de Uso (Use Case Diagram)

El diagrama de casos de uso es fundamental para definir el alcance funcional del sistema. Muestra a los actores que usan el sistema y las actividades clave que pueden realizar.

- Actores y Funcionalidad

Huésped: Acciones relacionadas con la búsqueda y gestión de sus propias reservas (Realizar Reserva, Cancelar Reserva).

Recepcionista: Acciones de gestión operativa (Registrar Check-in, Registrar Check-out, Gestionar Reserva).

Administrador: Acciones de configuración (Gestionar Catálogo, Generar Reportes).

3.2. Diagrama de Actividades (Activity Diagram)

Este diagrama representa el flujo de trabajo paso a paso para un proceso específico, como una receta. Se elaboró siguiendo la secuencia completa de Realizar Reserva, incluyendo bifurcaciones para decisiones reales del negocio.

Ejemplo de Bifurcación: El diagrama de actividades muestra si el cliente Aprueba o Rechaza la cotización (en este caso, si el Pago es Exitoso o Fallido), permitiendo identificar y modelar caminos alternativos dentro del proceso.

3.3. Diagrama de Estados (State Machine Diagram)

El diagrama de estados garantiza la coherencia del ciclo de vida de una entidad. Modela cómo la clase Reserva evoluciona a lo largo del tiempo, previniendo transiciones ilógicas o estados incorrectos.

Ciclo de Vida de Reserva:

$$Pendiente \rightarrow Confirmada \rightarrow CheckIn \rightarrow Ocupada \rightarrow CheckOut \rightarrow Completada$$

4. Modelos de Interacción e Implementación

4.1. Diagrama de Secuencia (Sequence Diagram)

Los diagramas de secuencia muestran el orden cronológico de los mensajes intercambiados entre los componentes (actores, interfaz, backend y base de datos).

Modelamos el proceso de Registrar Check-in para demostrar cómo el sistema:

Busca la reserva en la Base de Datos.

El Controlador (lógica de negocio) realiza el cambio de estado.

Se actualiza el estado de la Reserva y el estado de la Habitacion simultáneamente, garantizando la trazabilidad de datos.

Este diagrama es clave para demostrar cómo se comunican los componentes internos del sistema.

4.2. Diagrama de Componentes (Component Diagram)

Este diagrama explica cómo se conectan las partes internas del software (módulos) y sus responsabilidades dentro de la solución.

Muestra la conexión entre el WebApp Frontend (Interfaz), el API Rest/Backend (Lógica de Negocio), y cómo este último interactúa con los módulos externos (Ej. Pasarela de Pago o Módulo de Email/Notificaciones).

Aporta valor explicando la arquitectura del backend al mostrar la división de responsabilidades en la API.

4.3. Diagrama de Despliegue (Deployment Diagram)

El diagrama de despliegue muestra la arquitectura física del sistema, indicando en qué nodos (servidores, computadoras) se instalan y ejecutan los componentes del software. Esto permite validar que el sistema puede funcionar en un entorno de producción.

Nodos Principales:

Cloud Server / VPS: Aloja el Servidor Web (Frontend y API) y el Servidor BD.

Red Local Hotel: Contiene la Terminal Recepción (PC) que accede al sistema.

Internet: Representa los Dispositivo Huésped.

4.4. Diagrama de Tiempo (Timing Diagram)

Este diagrama es fundamental para la optimización del proceso productivo. Muestra la duración de las actividades, desde que se crea la reserva hasta el check-out.

Su propósito es: Justificar la necesidad de campos temporales (fecha_creacion, fecha_llegada) y evaluar la eficiencia del proceso para, por ejemplo, asegurar que el tiempo entre CheckOut y Limpieza sea mínimo.

5. Conclusión General

El conjunto de diagramas UML construidos para el Sistema de Gestión de Reservas del Hotel El Bosque cumple con los estándares profesionales al:

Describir el qué: (Casos de Uso) Definen el alcance funcional.

Explicar el cómo: (Actividades, Secuencia, Estados) Detallan el comportamiento y la lógica interna.

Mostrar la estructura: (Clases, Componentes, Despliegue) Definen la arquitectura lógica y física.
