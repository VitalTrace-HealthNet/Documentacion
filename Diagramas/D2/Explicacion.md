En el siguiente grafico se expondra la distribución geográfica de los diferentes componentes clave hablados den el [[Contexto]], que son
1. Capitales
2. Regiones 
3. Concentradores regionales
4. sede central
En este caso se añade otro componente a estos componentes clave que es la nube.

En el [[Contexto]] se define que la solucion va a estar usandose en 32 capitales + Bogota.
estas capitales van a estar agrupados en 6-10 concentradores regionales. Estas regiones tienen un problema, es que van a tener diferencias de latencia y conectividad.

Por lo tanto el reto es agrupar estos componentes en Colombia, pero debe tener sentido técnico.

## Estrategia 

Se toma en cuenta la siguiente estrategia, que se trata de agrupar por los siguientes factores
- **Cercania geografica**
- **Conectividad real**
- **Peso urbano**

## Informacion
### Peso urbano
Segun el DANE, en el 2019 , la distribucion de personaes en el pais se ve de la siguiente manera:
![[Pasted image 20260504154350.png|474]]
![[Pasted image 20260504154522.png|442]]
Donde se puede observar una gran concetracion de personas en las siguientes zonas:
- Bogota
- Cali
- Medellin
- Barranquilla
- Bucaramanga
- Pereria
- Villavicencio
### Conectividad
Segun la MinTic, las conexiones de internet en colombia se ven reflejadas de la sigueinte manera.
![[Pasted image 20260504155330.png]]
Donde se refleja el acceso de internet a las hogares. Tomamos esta medida como reflejo de la infrastructura de red instalada en estas zonas, donde observamos una infrastuctura de red desarrollada en las siguientes zonas
- Bogota
- Cali
- Medellin
- Barranquilla
- Bucaramanga
- Pereria
- Villavicencio
## Decisiones
Con el analisis de la informacion se decidio agrupar las diferentes capitales en 7 regiones (concentradores), que son los siguientes:
### 1. Región Centro

- **Concentrador:** Bogotá
- Capitales:
    - Bogotá
    - Tunja
    - Villavicencio
    - Ibagué
    - Neiva

Justificación:  
Centro político + mejor conectividad nacional

### 2. Región Occidente

- **Concentrador:** Cali
- Capitales:
    - Cali
    - Popayán
    - Pasto

Justificación:  
Salida al Pacífico + latencia desigual (importante decirlo)

###  3. Región Antioquia

- **Concentrador:** Medellín
- Capitales:
    - Medellín
    - Quibdó

Justificación:  
Hub fuerte + conecta zonas complejas (Chocó)

### 4. Región Caribe

- **Concentrador:** Barranquilla
- Capitales:
    - Barranquilla
    - Cartagena
    - Santa Marta
    - Sincelejo
    - Riohacha
    - Valledupar
 Justificación:  
Alta densidad + costa + tráfico alto

### 5. Región Santanderes

- **Concentrador:** Bucaramanga
- Capitales:
    - Bucaramanga
    - Cúcuta

 Justificación:  
Zona intermedia + frontera

### 6. Región Eje Cafetero

- **Concentrador:** Pereira
- Capitales:
    - Pereira
    - Manizales
    - Armenia

Justificación:  
Región compacta + buena conectividad

### 7. Región Orinoquía / Amazonía

- **Concentrador:** Villavicencio (o Bogotá si quieres simplificar)
- Capitales:
    - Yopal
    - Arauca
    - Leticia
    - Mitú
    - Puerto Carreño
    - Inírida
    - San José del Guaviare
    - Mocoa
    - Florencia

 Justificación:  
- baja conectividad
- latencias altas
- enlaces inestables

### Sede central
Debido a la cantidad de personas y la conectividad se escogio Bogota como la mejor zona para que actuara como sede principal

## Caja de información
```
Diagrama: D2 - Territorio Colombia

Proyecto: VitalTrace HealthNet

Versión: v1.0

Fecha: 2026-05-04

Autor(es): Santiago Hernandez

Descripción:

Diagrama de distribucion geografica de la solucion en colombia

con la plataforma VitalTrace desplegada en la nube.

Nota:

Diseño solamente (no desplegado en laboratorio)
```
## importante

La conectividad entre los concentradores regionales y la sede central se representa de forma abstracta como "VPN/MPLS" en este diagrama.

Esta representación no implica una decisión técnica final, sino una aproximación de alto nivel acorde al propósito del diagrama.

La selección definitiva entre VPN sobre internet, enlaces dedicados (MPLS) o una arquitectura híbrida será abordada en etapas posteriores del diseño (D3 - red híbrida), donde se evaluarán criterios como:

- Latencia y variabilidad entre regiones
- Costos de operación (CAPEX/OPEX)
- Requerimientos de disponibilidad y SLA
- Capacidad de escalabilidad nacional
- Cumplimiento regulatorio y seguridad

Por lo tanto, en esta etapa se prioriza la representación de patrones de conectividad y agregación de tráfico, en lugar de la tecnología específica de transporte.
**Esta decisión será formalizada mediante un ADR de conectividad en la fase de arquitectura de red.**
## links
https://experience.arcgis.com/experience/beb040b81cea405790ecd546acbdc916/page/Personas
https://datic.postdata.gov.co/