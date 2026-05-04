## Donde estoy ahora (on-premise)
La solución actual se divide en diferentes centros de datos. Los componentes clave de la solucion on-premise son los siguientes: 
- **Capitales (Ciudades)**: Hay 32  capitales y Bogota, representan ciudades, NO IPS (hospitales)
- **Concentradores regionales**: Son como mini-centros de datos regionales. Por ejemplo:
	- Region Andina
	- Region Pacifico
	- Region caribe
	Estos concentradores, agrupa varias ciudades y recibe el trafico de las ciudades. Estos concentradores tienen:
		-  Firewalls
		-  VPN
		-  Proxy
-  **Sede principal**: Es el datacenter principal, en este se encuentra la base de datos, aplicaciones y el storage.

La conectividad entre estos componentes es privada, es decir, tiene VPN sitio a sitio. Por lo tanto, no es Internet publico.

```
IPS (hospitales)  
↓  
Capitales (ciudades)  
↓  
Concentradores regionales  
↓  
Sede principal (on-prem)  
```

### Sede principal
Es el datacenter principal donde reside la lógica central del sistema.
Contiene tres dominios principales :
#### 1. Datos
- PostgreSQL (prod / QA / dev)
- Redis (colas, caché, integración)
#### 2. Aplicaciones
- VMs con Docker
- Servicios modulares (admisión, autorizaciones, laboratorio, etc.)
#### 3. Plataforma y almacenamiento
- Archivos clínicos (documentos, imágenes, resultados)
- Servidores web (Nginx/Apache)
- Monitoreo tradicional (ej: Icinga)

### Datos
Actualmente, aunque el sistema aspira a consolidar datos a nivel nacional, el inventario de datos no es completamente confiable ni unificado. Existen problemas como duplicados, pérdida de información, backups incompletos y datos sin metadatos adecuados.
Por ello, se deben considerar dos realidades:
- **Ambición país:** volumen potencial de datos en escala de petabytes si se consolidaran todas las fuentes. (CRECIMIENTO A FUTURO)
- **Inventario certificado:** conjunto de datos actualmente auditado y utilizable, que representa un subconjunto del total y es el que se usará como base para migración y estimaciones de costos. (ESTO ES LO QUE TOMO EN CUENTA PARA LA MIGRACIÓN Y LOS COSTOS REALES Y LABORATORIOS)
### Limitaciones
- Dependencia fuerte de la sede principal (cuello de botella)
- Problemas de latencia en regiones alejadas
- Escalabilidad limitada (infraestructura física)
- Complejidad para manejar grandes volúmenes de datos
- Monitoreo tradicional no unificado
- Dificultad para alta disponibilidad multi-región
## A donde quiero llegar (visión)
Actualmente existen dolores como
- Desigualdad de red: regiones con diferentes latencias y mala conectividad
- Costos altos:  Storage + transferencias > computo
- RTO/ RPO: no todo es igual de critico, diferenciar prioridades
- Migración compleja: No puedo apagar el sistema
- Seguridad: Auditorias obligatorias 

La plataforma debe ser:
- multi-región
- cloud-native
- Git como fuente de verdad
- Zero Trust
- observabilidad completa
- automatizada (DevOps + CloudOps)

Para resolver los problemas de calidad, fragmentación y crecimiento del dato, se propone una estrategia de modernización basada en una plataforma analítica unificada.
Esta incluirá:
- Ingesta de datos desde sistemas operacionales (PostgreSQL, almacenamiento objeto y bus de interoperabilidad).
- Procesamiento y limpieza, incluyendo deduplicación, validación y trazabilidad del dato.
- Organización en capas (por ejemplo: bronce, plata y oro) para diferenciar datos crudos, procesados y listos para analítica.
- Gobernanza del dato, con clasificación de información sensible, control de accesos y auditoría de consultas.
- Separación de entornos (desarrollo, analítica, producción) para evitar riesgos de seguridad.

Cuando el proveedor sea Azure, esta estrategia se soportará sobre Microsoft Fabric; en otros casos, se propondrá un stack equivalente.
## Que hay en el medio
Dado que la plataforma de salud no puede interrumpir su operación, la transición desde el entorno on-premise hacia la nube debe realizarse de forma progresiva, controlada y con capacidad de reversión. Esta estrategia contempla la convivencia temporal entre ambos entornos, minimizando riesgos operativos y garantizando continuidad del servicio.

### 4.1 Inventario y clasificación
Como punto de partida, se debe realizar una clasificación de los componentes del sistema según su criticidad y función:

- **Crítico en línea:** servicios de autorizaciones, APIs y mensajería en tiempo real.
- **Analítico:** sistemas de reportes, analítica y procesamiento de datos.
- **Archivo:** almacenamiento histórico, documentos clínicos e imagenología.

Adicionalmente, se deben identificar dependencias técnicas entre componentes, incluyendo bases de datos (PostgreSQL), sistemas de caché (Redis) y almacenamiento de objetos.

Esta clasificación permite definir prioridades y estrategias diferenciadas de migración, evitando tratar todos los componentes de forma homogénea.

### 4.2 Migración por oleadas
La migración se realizará en fases (oleadas), evitando trasladar todo el sistema de manera simultánea.
#### Oleada A – Fundaciones
- Aprovisionamiento de infraestructura en la nube
- Configuración de red, identidad y seguridad
- Establecimiento de conectividad híbrida con el entorno on-premise

En esta fase no se migran datos ni cargas productivas.
#### Oleada B – Integración
- Despliegue de APIs y servicios intermedios en la nube
- Habilitación de comunicación entre nube y on-premise
- Inicio de consumo parcial de servicios desde la nube

Esta fase permite validar la conectividad y operación híbrida.
#### Oleada C – Migración de datos y cargas
- Migración progresiva de datos según su criticidad (caliente, templado, frío)
- Validación de integridad mediante checksums
- Ejecución de pruebas de restauración y consistencia

Cada oleada debe permitir rollback en caso de fallos, evitando impacto en la operación global.
### 4.3 Red y gravedad de datos
La estrategia de migración debe considerar el concepto de **gravedad de datos**, donde grandes volúmenes de información condicionan la ubicación de las aplicaciones.

Para ello, se plantean las siguientes prácticas:
- Minimizar transferencias innecesarias de datos
- Evitar mover la misma información múltiples veces
- Evaluar mecanismos de transferencia eficiente, tales como:
    - dispositivos de transferencia offline (ej. Data Box o equivalentes)
    - enlaces dedicados o ampliación de VPN
- Considerar costos de transferencia (egreso) y latencia en decisiones de arquitectura
### 4.4 Convivencia híbrida
Durante la migración coexistirán los entornos on-premise y cloud, por lo que se requiere una arquitectura híbrida que garantice operación continua.
Esta incluirá:
- Resolución DNS híbrida (privada y pública)
- Enrutamiento por región
- Acceso desde capitales y concentradores sin afectar cumplimiento regulatorio
- Integración segura entre servicios distribuidos

El objetivo es mantener la disponibilidad del sistema mientras se completa la transición.

### 4.5 Corte y rollback
La migración a producción en la nube debe basarse en criterios medibles:
- Cumplimiento de SLOs (latencia, disponibilidad, tasa de errores)
- Validación de integridad de datos
- Estabilidad operativa bajo carga

En caso de incumplimiento, se debe contar con mecanismos de rollback que permitan retornar a la operación on-premise por oleadas específicas, evitando afectaciones generalizadas.

### 4.6 Operación dual

Durante el proceso de migración, será necesario operar simultáneamente ambos entornos.
Esto implica:
- Monitoreo independiente y correlacionado de on-premise y nube
- Definición clara de responsabilidades operativas
- Gestión de incidentes diferenciada según el origen del problema
Por ejemplo:
- Fallos de conectividad regional → equipos de red
- Fallos en clúster o servicios cloud → equipos de plataforma
### 4.7 Decisiones de arquitectura (ADR)

Se debe documentar un **Architecture Decision Record (ADR)** que justifique la estrategia de migración adoptada, incluyendo:
- Alternativas evaluadas
- Riesgos identificados
- Trade-offs técnicos y operativos
- Justificación de la estrategia basada en oleadas