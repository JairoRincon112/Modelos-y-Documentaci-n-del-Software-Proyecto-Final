## Modelos y Documentación del Software_ Proyecto: Gestión de Reservas Hotel El Bosque

## Autores: Jairo Andres Rincon Blanco y Andres Camilo Cuvides Ortega

## Docente: Hely Suarez Marin


1. Descripción General del Sistema y Objetivos

El Sistema de Gestión de Reservas Hoteleras para el Hotel El Bosque tiene como objetivo principal automatizar y centralizar los procesos de interacción con el huésped, gestión de habitaciones y administración de tarifas.


2. Modelos Funcionales (Behavioral Models)
2.1. Diagrama de Casos de Uso (Use Case Diagram)
Este diagrama establece el alcance funcional del sistema, mostrando las interacciones entre los actores y el software. Es la base para definir las funciones del sistema.

Fragmento de código

@startuml CasoDeUso_HotelElBosque
left to right direction

' Definición de Actores
actor Huésped
actor Recepcionista
actor Administrador

' Definición del Límite del Sistema
rectangle "Sistema de Gestión Hotelera" {
    usecase (Buscar Disponibilidad) as UC1
    usecase (Realizar Reserva) as UC2
    usecase (Gestionar Reserva) as UC3
    usecase (Registrar Check-in) as UC4
    usecase (Registrar Check-out) as UC5
    usecase (Gestionar Catálogo) as UC6
    usecase (Generar Reportes) as UC7
    usecase (Procesar Pago) as UC8
    usecase (Cancelar Reserva) as UC9
    
    UC2 .> UC8 : <<include>>
    UC5 .> UC8 : <<include>>
    UC3 .> UC9 : <<include>>
}

' Relaciones de Asociación
Huésped -- UC1
Huésped -- UC2
Huésped -- UC9

Recepcionista -- UC1
Recepcionista -- UC3
Recepcionista -- UC4
Recepcionista -- UC5

Administrador -- UC6
Administrador -- UC7
@enduml

![img](/images/CASOS_DE_USO.png)


2.2. Diagrama de Actividades (Activity Diagram)

Este diagrama detalla el flujo de trabajo secuencial de un proceso, incluyendo las decisiones clave, como la validación del pago de la reserva.

Fragmento de código

@startuml Actividad_RealizarReserva
title Flujo de Actividad: Realizar Reserva (Online)

start
:Huésped ingresa fechas y tipo;
:Consultar Disponibilidad en BD;

if (¿Hay habitaciones disponibles?) then (Sí)
  :Mostrar Opciones y Precios;
  :Huésped selecciona y suministra datos;
  :Iniciar Proceso de Pago;
  
  if (¿Pago exitoso?) then (Sí)
    :Crear Objeto Reserva;
    :Cambiar estado de Reserva a Confirmada;
    :Enviar Correo de Confirmación;
    stop
  else (No)
    :Mostrar Mensaje de Error de Pago;
    end
  endif

else (No)
  :Mostrar mensaje de no disponibilidad;
  end
endif
@enduml

![img](/images/D_ACTIVIDADES.png)


3. Modelos Estructurales (Structural Models)
   
3.1. Diagrama de Clases (Class Diagram)

Este diagrama es el esqueleto del sistema. Modela los conceptos principales con sus atributos, métodos y asociaciones (multiplicidad), sirviendo de base para el diseño de la base de datos.

Fragmento de código

@startuml Clase_HotelElBosque
hide empty members

' Clases Esenciales
class TipoHabitacion {
  + idTipo: int <<PK>>
  + nombre: varchar (Ej: Suite) <<Unique>>
  + tarifaBase: decimal
  + actualizarTarifa()
}

class Habitacion {
  + idHabitacion: int <<PK>>
  + numero: varchar <<Unique>>
  + tipoId: int <<FK>>
  + estado: enum
  + cambiarEstado()
}

class Huesped {
  + idHuesped: int <<PK>>
  + email: varchar <<Unique>>
  + registrar()
}

class Reserva {
  + idReserva: int <<PK>>
  + huespedId: int <<FK>>
  + habitacionId: int <<FK>>
  + estado: enum
  + calcularCosto()
}

class Factura {
  + idFactura: int <<PK>>
  + reservaId: int <<FK, Unique>>
  + estadoPago: enum
  + generarFactura()
}

' Relaciones
TipoHabitacion "1" -- "*" Habitacion : contiene >
Huesped "1" -- "*" Reserva : realiza >
Habitacion "1" -- "*" Reserva : asignada en >
Reserva "1" -- "1" Factura : genera >
@enduml

![img](/images/D_CLASES.png)

3.2. Diagrama de Objetos (Object Diagram)

Representa una instancia concreta del diagrama de clases en un momento específico, validando la coherencia del modelo con ejemplos de datos reales.

Fragmento de código

@startuml Objeto_HotelElBosque
title Diagrama de Objetos (Instancia de Reserva)

object Habitacion_101 {
  numero = "101"
  estado = "Ocupada"
}

object Huesped_JuanPerez {
  id = 502
  nombre = "Juan Perez"
}

object Reserva_99 {
  id = 99
  fechaLlegada = 2025-12-01
  estado = "CheckIn"
}

object Factura_55 {
  id = 55
  total = 350.00
  estadoPago = "Pagada"
}

Huesped_JuanPerez "realiza" --> Reserva_99
Habitacion_101 "asignada" --> Reserva_99
Reserva_99 "genera" --> Factura_55
@enduml

![img](/images/D_OBJETOS.png)

3.3. Diagrama de Paquetes (Package Diagram)

Se utiliza para organizar el sistema en módulos lógicos (Paquetes), facilitando el desarrollo modular y la escalabilidad del proyecto.

Fragmento de código

@startuml Paquete_HotelElBosque
title Diagrama de Paquetes (Modularización)

package "Gestión Hotelera" {
    package "Presentación (UI)" {
        [Huésped Web]
        [Terminal Recepción]
    }

    package "Lógica de Negocio (Backend)" {
        [Módulo Reservas]
        [Módulo Facturación]
        [Módulo Seguridad]
    }
    
    [Módulo Reservas] --> [Módulo Facturación] : "genera"
    [Módulo Seguridad] --> [Módulo Reservas] : "autentica"
}

package "Persistencia" {
    [Base de Datos]
}

[Lógica de Negocio (Backend)] --> [Base de Datos] : "accede"
@enduml

![img](/images/D_PAQUETES.png)


4. Modelos de Interacción y Estados
   
4.1. Diagrama de Secuencia (Sequence Diagram)

Modela la interacción temporal de los objetos. El modelado de Registrar Check-in demuestra la comunicación entre la Interfaz, el Controlador y la Base de Datos para asegurar la trazabilidad.

Fragmento de código

@startuml Secuencia_Checkin
title Secuencia: Registrar Check-in

actor Recepcionista
participant Interfaz
participant ControladorReserva
database Habitacion
database Reserva

Recepcionista -> Interfaz : Ingresar ID de Reserva
Interfaz -> ControladorReserva : buscarReserva(id)
ControladorReserva -> Reserva : SELECT reserva, huesped
alt Reserva Válida
    ControladorReserva --> Interfaz : Mostrar datos de Reserva y Huésped

    Recepcionista -> Interfaz : confirmarCheckin(reservaId, habitacionNumero)
    Interfaz -> ControladorReserva : registrarCheckin(reservaId, habitacionNumero)
    
    ControladorReserva -> Habitacion : UPDATE estado='Ocupada'
    ControladorReserva -> Reserva : UPDATE estado='CheckIn'
    
    ControladorReserva --> Interfaz : Éxito (Habitación asignada)
else Reserva Inválida
    ControladorReserva --> Interfaz : Mostrar Error
end
@enduml

![img](/images/D_SECUENCIA.png)

![img](/images/D_SECUENCIA_CANCELACION.png)

4.2. Diagrama de Comunicación (Communication Diagram)

Este diagrama se enfoca en las asociaciones necesarias entre los objetos para que el flujo se complete. Muestra la misma lógica del Diagrama de Secuencia, pero enfatiza las conexiones y el flujo de mensajes mediante la numeración, sin centrarse en la línea de vida temporal. Su propósito es validar que la estructura de la clase y sus asociaciones (llaves foráneas en la práctica) soportan la interacción requerida para la funcionalidad, como la actualización coordinada de la reserva y la habitación.

Fragmento de código

@startuml Comunicacion_Checkin
title Comunicación: Registrar Check-in

actor Recepcionista
participant ":Interfaz" as UI
participant ":ControladorReserva" as CR
database "Habitacion:DB" as HDB
database "Reserva:DB" as RDB

' 1. Búsqueda y Validación
Recepcionista -> UI : 1: Ingresar ID de Reserva
UI -> CR : 2: buscarReserva(id)
CR -> RDB : 3: SELECT Reserva
RDB -> CR : 4: Resultado

' 5. Actualización de Estados
CR -> HDB : 5: UPDATE Habitacion.estado='Ocupada'
CR -> RDB : 6: UPDATE Reserva.estado='CheckIn'
CR -> UI : 7: Éxito

UI -> Recepcionista : 8: Mostrar Confirmación

@enduml

![img](/images/D_COMUNICACION.png)

4.3. Diagrama de Estados (State Machine Diagram)

Garantiza que el flujo de la clase Reserva se mantenga válido y coherente a lo largo de su ciclo de vida, evitando transiciones incorrectas.

Fragmento de código

@startuml Estado_Reserva
title Ciclo de Vida de la Reserva

[*] --> Pendiente

state Pendiente {
    Pendiente --> Confirmada : Pagar Reserva
    Pendiente --> Cancelada : Expirar Tiempo
}

Confirmada --> CheckIn : Registrar Llegada
CheckIn --> Ocupada : Check-in completado
Ocupada --> CheckOut : Registrar Salida

CheckOut --> Completada : Pago de Factura
CheckOut --> PendienteFactura : Factura Pendiente

PendienteFactura --> Completada : Procesar Pago

Cancelada --> [*]
Completada --> [*]
@enduml


![img](/images/D_ESTADOS.png)

4.4. Diagrama de Tiempo (Timing Diagram)

Muestra la evolución de los participantes en un eje temporal (fechas), ayudando a justificar campos de fecha y aportando a la optimización del proceso. Se utiliza la notación Mermaid para una mayor compatibilidad.

Fragmento de código

%%{init: {'theme': 'base'}}%%
gantt
    title Evolución Temporal del Estado de la Reserva
    dateFormat  YYYY-MM-DD
    
    section Huésped
    Solicitando         :Huesped_1, 2025-12-01, 7d
    Esperando           :Huesped_2, after Huesped_1, 3d
    Confirmado          :Huesped_3, after Huesped_2, 30d

    section Sistema (Estado de Reserva)
    Pendiente           :Sistema_1, 2025-12-01, 10d
    Confirmada          :Sistema_2, after Sistema_1, 290d
    
    section Estancia
    CheckIn (Llegada)   :Sistema_3, 2026-10-06, 1d
    Ocupada             :Sistema_4, after Sistema_3, 3d
    CheckOut            :Sistema_5, after Sistema_4, 1d
    Completada          :Sistema_6, after Sistema_5, 10d 


![img](/images/D_TIEMPO.png)


5. Modelos de Implementación y Arquitectura
   
5.1. Diagrama de Componentes (Component Diagram)
   
Explica la división interna del software en módulos y cómo se interconectan para cumplir las funciones.

Fragmento de código

@startuml Componente_HotelElBosque
title Diagrama de Componentes

component [WebApp Frontend] as FE
component [API Rest/Backend] as API
component [Módulo de Facturación] as Billing
component [Pasarela de Pago (Externo)] as Pago
component [Servidor de Base de Datos (DB)] as DB

FE -- (API) : "HTTP/JSON"
API -- Billing : "Interno"
API -right-> Pago : "SSL/API Call"
API -- DB : "JDBC/ORM"

[Terminal Recepción] -- FE : "Navegador"

@enduml

![img](/images/D_COMPONENTES.png)


5.2. Diagrama de Despliegue (Deployment Diagram)

Indica la distribución física de los artefactos en los nodos de hardware (servidores, PCs). Esto es crucial para la validación de la infraestructura en producción.

Fragmento de código

@startuml Despliegue_HotelElBosque
title Diagrama de Despliegue

node "Cloud Server / VPS" as Cloud {
    node "Servidor Web" as WebServer {
        component "Aplicación Web (FE)" as AppWeb
        component "API REST (Backend)" as API
    }
    node "Servidor BD" as DBServer {
        database "Esquema el_bosque_db" as DB
    }
}

node "Red Local Hotel" as LAN {
    node "Terminal Recepción" as Terminal {
        artifact "Navegador Cliente" as Browser
    }
}

node "Internet" as Internet {
    node "Dispositivo Huésped" as Mobile {
        artifact "Navegador Móvil" as MobileBrowser
    }
}

node "Proveedor de Pagos" as PaymentGateway

AppWeb -- API : HTTP/JSON
API -- DB : JDBC/SQL
Terminal -- AppWeb : HTTP (Intra-Red)
Mobile -- AppWeb : HTTPS (Internet)
API -- PaymentGateway : HTTPS/API
@enduml


![img](/images/D_DESPLIEGUE.png)

5.3. Diagrama de Instalación (Installation Diagram)

Este diagrama es una vista detallada del proceso de puesta en marcha, especificando los artefactos de software y las dependencias de runtime. Su objetivo es comunicar al equipo de DevOps los requisitos de software exactos para cada nodo, asegurando que el ambiente de producción (Ej: Servidor de Producción con OS Linux y Runtime) esté configurado correctamente para ejecutar el Código Fuente del Sistema.

Fragmento de código

@startuml Instalacion_HotelElBosque
title Diagrama de Instalación y Requisitos

node "Servidor de Producción" as Server {
    artifact "Sistema Operativo Linux" as OS
    artifact "Servidor Web Nginx/Apache" as WebS
    artifact "Runtime PHP/NodeJS/Java" as Runtime
    artifact "Código Fuente del Sistema" as Code
    artifact "Sistema de Gestión DB (MySQL)" as MySQL
}

node "Terminal de Recepción" as ClientPC {
    artifact "Sistema Operativo Windows/Mac" as OS_Client
    artifact "Navegador Moderno (Chrome/Firefox)" as Browser_Client
}

OS -> WebS : requiere
WebS -> Runtime : utiliza
Runtime -> Code : ejecuta
Runtime -> MySQL : acceso a datos
@enduml

![img](/images/D_INSTALACION.png)


6. Conclusión

Este proyecto consolidó la importancia del Lenguaje Unificado de Modelado (UML) como herramienta crítica en la Ingeniería de Software. Modelar el Hotel El Bosque mediante diagramas como el de Clases y Secuencia nos permitió establecer una estructura de negocio coherente y validar la lógica interna del sistema. El uso de los diagramas de Despliegue y Componentes aseguró que la arquitectura técnica estuviera alineada con los requisitos de escalabilidad. En esencia, la documentación UML sirvió como un puente indispensable entre los requerimientos del negocio y la implementación de la base de datos y el backend. La rigurosidad en la notación es fundamental para la profesionalización del ciclo de vida del software.
