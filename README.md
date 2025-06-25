# Estándares para Modelamiento de Datos
## Bases de Datos Relacionales y NoSQL

### Tabla de Contenidos
1. [Introducción](#introducción)
2. [Conceptos Fundamentales del Modelo Relacional](#conceptos-fundamentales-del-modelo-relacional)
3. [Niveles de Modelamiento de Datos](#niveles-de-modelamiento-de-datos)
4. [Normalización y Formas Normales](#normalización-y-formas-normales)
5. [Diccionario de Datos](#diccionario-de-datos)
6. [Tipos de Modelos de Datos](#tipos-de-modelos-de-datos)
7. [Bases de Datos No Relacionales (NoSQL)](#bases-de-datos-no-relacionales-nosql)
8. [Buenas Prácticas y Estándares](#buenas-prácticas-y-estándares)

---

## Introducción

Este documento establece los estándares y mejores prácticas para el modelamiento de datos en sistemas de bases de datos relacionales (Oracle, SQL Server, PostgreSQL) y no relacionales (NoSQL). El objetivo es garantizar consistencia, calidad y mantenibilidad en el diseño de estructuras de datos.

---

## Conceptos Fundamentales del Modelo Relacional

### Entidad
**Definición**: Objeto del mundo real que puede ser identificado de manera única y sobre el cual se almacena información en la base de datos.

**Características**:
- Representa un conjunto de objetos similares
- Tiene existencia independiente
- Posee atributos que la describen

**Ejemplos**:
- Cliente (persona que realiza compras)
- Producto (artículo vendido por la empresa)
- Pedido (solicitud de compra realizada por un cliente)

### Atributos
**Definición**: Propiedades o características que describen una entidad.

**Tipos de Atributos**:
- **Simples**: No se pueden dividir (nombre, edad)
- **Compuestos**: Se pueden dividir en componentes más pequeños (dirección: calle, ciudad, código postal)
- **Univalorados**: Un solo valor por entidad (número de identificación)
- **Multivalorados**: Múltiples valores por entidad (teléfonos de contacto)
- **Derivados**: Se calculan a partir de otros atributos (edad derivada de fecha de nacimiento)

**Ejemplo**:
```
Entidad: EMPLEADO
Atributos:
- id_empleado (identificador)
- nombre (simple, univalorado)
- direccion (compuesto: calle, ciudad, codigo_postal)
- telefonos (multivalorado)
- edad (derivado de fecha_nacimiento)
```

### Identificador (Clave Primaria)
**Definición**: Atributo o conjunto de atributos que identifican de manera única cada instancia de una entidad.

**Características**:
- Unicidad: No puede haber duplicados
- Minimidad: Debe contener el menor número de atributos posible
- Estabilidad: No debe cambiar frecuentemente
- No nulo: Siempre debe tener un valor

**Tipos de Claves**:
- **Clave Primaria**: Identificador principal de la entidad
- **Clave Candidata**: Cualquier atributo que puede ser clave primaria
- **Clave Foránea**: Referencia a la clave primaria de otra entidad
- **Clave Alternativa**: Clave candidata que no fue elegida como primaria

### Relación
**Definición**: Asociación entre dos o más entidades que representa una conexión lógica en el mundo real.

**Elementos de una Relación**:
- **Entidades participantes**: Las entidades que se relacionan
- **Cardinalidad**: Número de instancias que pueden participar
- **Modalidad**: Si la participación es obligatoria u opcional

**Ejemplo**:
```
CLIENTE realiza PEDIDO
- Entidades: CLIENTE, PEDIDO
- Verbo: "realiza"
- Un cliente puede realizar muchos pedidos
- Un pedido es realizado por un solo cliente
```

### Cardinalidad
**Definición**: Especifica el número de instancias de una entidad que pueden estar asociadas con una instancia de otra entidad.

**Tipos de Cardinalidad**:

1. **Uno a Uno (1:1)**
   - Una instancia de A se relaciona con una instancia de B
   - Ejemplo: PERSONA - PASAPORTE

2. **Uno a Muchos (1:N)**
   - Una instancia de A se relaciona con muchas instancias de B
   - Ejemplo: DEPARTAMENTO - EMPLEADOS

3. **Muchos a Muchos (M:N)**
   - Muchas instancias de A se relacionan con muchas instancias de B
   - Ejemplo: ESTUDIANTE - MATERIAS

**Notación**:
```
CLIENTE ||--o{ PEDIDO
(Un cliente puede tener cero o muchos pedidos)

EMPLEADO }o--|| DEPARTAMENTO
(Un empleado pertenece a un departamento)
```

### Generalización
**Definición**: Proceso de identificar características comunes entre entidades para crear una entidad más general (superclase).

**Conceptos Relacionados**:
- **Especialización**: Proceso inverso, crear subclases a partir de una superclase
- **Herencia**: Las subclases heredan atributos de la superclase
- **Disjunción**: Las subclases son mutuamente excluyentes
- **Completitud**: Toda instancia de la superclase debe pertenecer a alguna subclase

**Ejemplo**:
```
PERSONA (superclase)
- id_persona
- nombre
- fecha_nacimiento

    ↙        ↘
EMPLEADO    CLIENTE (subclases)
- salario   - credito_limite
- puesto    - fecha_registro
```

---

## Niveles de Modelamiento de Datos

### Modelo Conceptual
**Propósito**: Representar la realidad del negocio de manera independiente de la tecnología.

**Características**:
- Alto nivel de abstracción
- Independiente del SGBD
- Centrado en el negocio
- Utiliza lenguaje natural del dominio

**Elementos**:
- Entidades y sus relaciones
- Atributos principales
- Reglas de negocio básicas
- Restricciones conceptuales

**Herramientas**: Diagramas Entidad-Relación (ER)

### Modelo Lógico
**Propósito**: Traducir el modelo conceptual a estructuras que pueden ser implementadas en un SGBD relacional.

**Características**:
- Independiente del SGBD específico
- Define estructura de datos detallada
- Incluye todos los atributos
- Especifica tipos de datos genéricos

**Elementos**:
- Tablas y columnas
- Claves primarias y foráneas
- Índices conceptuales
- Restricciones de integridad
- Vistas lógicas

**Ejemplo**:
```
CLIENTE
- id_cliente (PK) : Integer
- nombre : String(100)
- email : String(255)
- fecha_registro : Date

PEDIDO
- id_pedido (PK) : Integer
- id_cliente (FK) : Integer
- fecha_pedido : DateTime
- total : Decimal(10,2)
```

### Modelo Físico
**Propósito**: Implementar el modelo lógico en un SGBD específico con optimizaciones de rendimiento.

**Características**:
- Específico del SGBD (Oracle, SQL Server, PostgreSQL)
- Incluye optimizaciones de rendimiento
- Define almacenamiento físico
- Considera aspectos de seguridad

**Elementos**:
- Scripts DDL específicos del SGBD
- Índices físicos y estrategias de indexación
- Particionamiento
- Espacios de tabla (tablespaces)
- Procedimientos almacenados
- Triggers
- Configuración de seguridad

**Ejemplo para PostgreSQL**:
```sql
CREATE TABLE cliente (
    id_cliente SERIAL PRIMARY KEY,
    nombre VARCHAR(100) NOT NULL,
    email VARCHAR(255) UNIQUE,
    fecha_registro TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    CONSTRAINT chk_email CHECK (email ~* '^[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}$')
);

CREATE INDEX idx_cliente_email ON cliente(email);
CREATE INDEX idx_cliente_fecha ON cliente(fecha_registro);
```

---

## Normalización y Formas Normales

### Normalización
**Definición**: Proceso sistemático de organizar datos para minimizar la redundancia y evitar anomalías de inserción, actualización y eliminación.

**Objetivos**:
- Eliminar redundancia de datos
- Evitar anomalías de actualización
- Garantizar integridad de datos
- Optimizar el almacenamiento

### Primera Forma Normal (1FN)
**Reglas**:
- Todos los atributos deben ser atómicos (indivisibles)
- No debe haber grupos repetitivos
- Cada celda debe contener un solo valor

**Ejemplo NO normalizado**:
```
CLIENTE
id | nombre | telefonos
1  | Juan   | 123456, 789012
```

**Ejemplo 1FN**:
```
CLIENTE
id | nombre | telefono
1  | Juan   | 123456
1  | Juan   | 789012
```

### Segunda Forma Normal (2FN)
**Reglas**:
- Estar en 1FN
- Todos los atributos no clave deben depender completamente de la clave primaria

**Ejemplo NO 2FN**:
```
PEDIDO_DETALLE
id_pedido | id_producto | cantidad | nombre_producto | precio_unitario
```

**Ejemplo 2FN**:
```
PEDIDO_DETALLE
id_pedido | id_producto | cantidad

PRODUCTO
id_producto | nombre_producto | precio_unitario
```

### Tercera Forma Normal (3FN)
**Reglas**:
- Estar en 2FN
- No debe haber dependencias transitivas (atributos no clave que dependan de otros atributos no clave)

**Ejemplo NO 3FN**:
```
EMPLEADO
id | nombre | id_departamento | nombre_departamento
```

**Ejemplo 3FN**:
```
EMPLEADO
id | nombre | id_departamento

DEPARTAMENTO
id_departamento | nombre_departamento
```

### Forma Normal de Boyce-Codd (FNBC)
**Reglas**:
- Estar en 3FN
- Todo determinante debe ser una superclave

### Cuarta Forma Normal (4FN)
**Reglas**:
- Estar en FNBC
- No debe haber dependencias multivaloradas

### Quinta Forma Normal (5FN)
**Reglas**:
- Estar en 4FN
- No debe haber dependencias de unión

---

## Diccionario de Datos

### Definición
Catálogo centralizado que contiene información detallada sobre todos los elementos de datos utilizados en el sistema.

### Componentes del Diccionario de Datos

#### Tabla de Entidades
| Campo | Descripción | Tipo | Ejemplo |
|-------|-------------|------|---------|
| Nombre_Entidad | Nombre de la entidad | VARCHAR(50) | CLIENTE |
| Descripcion | Propósito de la entidad | TEXT | Almacena información de clientes |
| Responsable | Área responsable | VARCHAR(50) | Ventas |
| Fecha_Creacion | Fecha de creación | DATE | 2024-01-15 |

#### Tabla de Atributos
| Campo | Descripción | Tipo | Ejemplo |
|-------|-------------|------|---------|
| Nombre_Entidad | Entidad propietaria | VARCHAR(50) | CLIENTE |
| Nombre_Atributo | Nombre del atributo | VARCHAR(50) | email |
| Tipo_Dato | Tipo de dato | VARCHAR(20) | VARCHAR(255) |
| Longitud | Longitud máxima | INTEGER | 255 |
| Nulo_Permitido | Permite valores nulos | BOOLEAN | FALSE |
| Clave | Tipo de clave | VARCHAR(20) | UNIQUE |
| Descripcion | Descripción detallada | TEXT | Correo electrónico del cliente |

#### Tabla de Relaciones
| Campo | Descripción | Tipo | Ejemplo |
|-------|-------------|------|---------|
| Nombre_Relacion | Nombre de la relación | VARCHAR(50) | CLIENTE_PEDIDO |
| Entidad_Padre | Entidad padre | VARCHAR(50) | CLIENTE |
| Entidad_Hija | Entidad hija | VARCHAR(50) | PEDIDO |
| Cardinalidad | Tipo de cardinalidad | VARCHAR(10) | 1:N |
| Descripcion | Descripción de la relación | TEXT | Un cliente puede realizar múltiples pedidos |

### Plantilla de Documentación por Tabla

```
TABLA: [NOMBRE_TABLA]
Descripción: [Descripción del propósito de la tabla]
Responsable: [Área o persona responsable]

COLUMNAS:
Nombre          | Tipo           | Nulo | Clave | Descripción
----------------|----------------|------|-------|------------------
id_cliente      | SERIAL         | NO   | PK    | Identificador único
nombre          | VARCHAR(100)   | NO   | -     | Nombre completo
email           | VARCHAR(255)   | SÍ   | UK    | Correo electrónico
fecha_registro  | TIMESTAMP      | NO   | -     | Fecha de registro

RESTRICCIONES:
- PK_CLIENTE: PRIMARY KEY (id_cliente)
- UK_CLIENTE_EMAIL: UNIQUE (email)
- CHK_EMAIL_FORMAT: CHECK (email ~* '^[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}$')

ÍNDICES:
- idx_cliente_email: (email)
- idx_cliente_fecha: (fecha_registro)

RELACIONES:
- PEDIDO.id_cliente → CLIENTE.id_cliente (FK)
```

---

## Tipos de Arquitecturas de Bases de Datos Relacionales

### Bases de Datos Transaccionales (OLTP)

#### Características Principales
- **Optimizadas para transacciones**: Diseñadas para procesar grandes volúmenes de transacciones cortas
- **Normalización hasta 3FN**: Reducen redundancia y garantizan integridad
- **Integridad ACID**: Atomicidad, Consistencia, Aislamiento, Durabilidad
- **Operaciones en tiempo real**: Inserción, actualización y eliminación frecuentes

#### Principios de Diseño OLTP
1. **Normalización obligatoria hasta 3FN**
2. **Minimizar redundancia de datos**
3. **Optimizar para escrituras frecuentes**
4. **Mantener integridad referencial estricta**
5. **Diseño orientado a transacciones individuales**

#### Ejemplo de Diseño OLTP Normalizado (3FN)

```sql
-- Sistema de Ventas OLTP
-- 1FN: Atributos atómicos, sin grupos repetitivos
-- 2FN: Dependencia funcional completa de la clave primaria
-- 3FN: Sin dependencias transitivas

-- Tabla de Clientes
CREATE TABLE cliente (
    id_cliente SERIAL PRIMARY KEY,
    codigo_cliente VARCHAR(20) UNIQUE NOT NULL,
    nombre VARCHAR(100) NOT NULL,
    apellido VARCHAR(100) NOT NULL,
    email VARCHAR(255) UNIQUE,
    telefono VARCHAR(20),
    fk_ciudad INTEGER NOT NULL,
    fecha_registro TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    activo BOOLEAN DEFAULT TRUE
);

-- Tabla de Ciudades (elimina dependencia transitiva ciudad->estado->país)
CREATE TABLE ciudad (
    id_ciudad SERIAL PRIMARY KEY,
    nombre_ciudad VARCHAR(100) NOT NULL,
    fk_estado INTEGER NOT NULL
);

-- Tabla de Estados
CREATE TABLE estado (
    id_estado SERIAL PRIMARY KEY,
    nombre_estado VARCHAR(100) NOT NULL,
    codigo_estado VARCHAR(10) NOT NULL,
    fk_pais INTEGER NOT NULL
);

-- Tabla de Países
CREATE TABLE pais (
    id_pais SERIAL PRIMARY KEY,
    nombre_pais VARCHAR(100) NOT NULL,
    codigo_iso VARCHAR(3) NOT NULL
);

-- Tabla de Productos
CREATE TABLE producto (
    id_producto SERIAL PRIMARY KEY,
    codigo_producto VARCHAR(50) UNIQUE NOT NULL,
    nombre_producto VARCHAR(200) NOT NULL,
    descripcion TEXT,
    precio_unitario DECIMAL(12,2) NOT NULL,
    fk_categoria INTEGER NOT NULL,
    stock_actual INTEGER DEFAULT 0,
    stock_minimo INTEGER DEFAULT 0,
    activo BOOLEAN DEFAULT TRUE
);

-- Tabla de Categorías
CREATE TABLE categoria (
    id_categoria SERIAL PRIMARY KEY,
    nombre_categoria VARCHAR(100) NOT NULL,
    descripcion TEXT,
    fk_categoria_padre INTEGER REFERENCES categoria(id_categoria)
);

-- Tabla de Pedidos
CREATE TABLE pedido (
    id_pedido SERIAL PRIMARY KEY,
    numero_pedido VARCHAR(20) UNIQUE NOT NULL,
    fk_cliente INTEGER NOT NULL,
    fecha_pedido TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    estado_pedido VARCHAR(20) DEFAULT 'PENDIENTE',
    subtotal DECIMAL(12,2) DEFAULT 0,
    impuestos DECIMAL(12,2) DEFAULT 0,
    descuento DECIMAL(12,2) DEFAULT 0,
    total DECIMAL(12,2) NOT NULL,
    fk_vendedor INTEGER
);

-- Tabla de Detalle de Pedidos (relación M:N entre Pedido y Producto)
CREATE TABLE pedido_detalle (
    id_detalle SERIAL PRIMARY KEY,
    fk_pedido INTEGER NOT NULL,
    fk_producto INTEGER NOT NULL,
    cantidad INTEGER NOT NULL,
    precio_unitario DECIMAL(12,2) NOT NULL,
    descuento_linea DECIMAL(12,2) DEFAULT 0,
    subtotal_linea DECIMAL(12,2) NOT NULL
);

-- Tabla de Vendedores
CREATE TABLE vendedor (
    id_vendedor SERIAL PRIMARY KEY,
    codigo_vendedor VARCHAR(20) UNIQUE NOT NULL,
    nombre VARCHAR(100) NOT NULL,
    apellido VARCHAR(100) NOT NULL,
    email VARCHAR(255) UNIQUE NOT NULL,
    fk_sucursal INTEGER NOT NULL,
    fecha_ingreso DATE NOT NULL,
    activo BOOLEAN DEFAULT TRUE
);

-- Tabla de Sucursales
CREATE TABLE sucursal (
    id_sucursal SERIAL PRIMARY KEY,
    codigo_sucursal VARCHAR(20) UNIQUE NOT NULL,
    nombre_sucursal VARCHAR(100) NOT NULL,
    direccion TEXT NOT NULL,
    fk_ciudad INTEGER NOT NULL,
    telefono VARCHAR(20),
    activa BOOLEAN DEFAULT TRUE
);

-- Claves foráneas para mantener integridad referencial
ALTER TABLE cliente ADD CONSTRAINT fk_cliente_ciudad 
    FOREIGN KEY (fk_ciudad) REFERENCES ciudad(id_ciudad);

ALTER TABLE ciudad ADD CONSTRAINT fk_ciudad_estado 
    FOREIGN KEY (fk_estado) REFERENCES estado(id_estado);

ALTER TABLE estado ADD CONSTRAINT fk_estado_pais 
    FOREIGN KEY (fk_pais) REFERENCES pais(id_pais);

ALTER TABLE producto ADD CONSTRAINT fk_producto_categoria 
    FOREIGN KEY (fk_categoria) REFERENCES categoria(id_categoria);

ALTER TABLE pedido ADD CONSTRAINT fk_pedido_cliente 
    FOREIGN KEY (fk_cliente) REFERENCES cliente(id_cliente);

ALTER TABLE pedido ADD CONSTRAINT fk_pedido_vendedor 
    FOREIGN KEY (fk_vendedor) REFERENCES vendedor(id_vendedor);

ALTER TABLE pedido_detalle ADD CONSTRAINT fk_detalle_pedido 
    FOREIGN KEY (fk_pedido) REFERENCES pedido(id_pedido) ON DELETE CASCADE;

ALTER TABLE pedido_detalle ADD CONSTRAINT fk_detalle_producto 
    FOREIGN KEY (fk_producto) REFERENCES producto(id_producto);

ALTER TABLE vendedor ADD CONSTRAINT fk_vendedor_sucursal 
    FOREIGN KEY (fk_sucursal) REFERENCES sucursal(id_sucursal);

ALTER TABLE sucursal ADD CONSTRAINT fk_sucursal_ciudad 
    FOREIGN KEY (fk_ciudad) REFERENCES ciudad(id_ciudad);
```

#### Ventajas de Bases de Datos OLTP
- **Integridad de datos garantizada**
- **Eliminación de redundancia**
- **Actualizaciones eficientes**
- **Concurrencia optimizada**
- **Espacio de almacenamiento mínimo**

#### Desventajas para Análisis
- **Consultas complejas con múltiples JOINs**
- **Rendimiento lento para reporting**
- **Dificultad para análisis histórico**
- **Bloqueos durante consultas analíticas**

---

### Bases de Datos de Almacén de Datos (Data Warehouse - OLAP)

#### Características Principales
- **Optimizadas para consultas analíticas**: Diseñadas para reporting y análisis
- **Desnormalización controlada**: Reducen JOINs para mejorar rendimiento
- **Datos históricos**: Mantienen información temporal para análisis de tendencias
- **Solo lectura o actualizaciones por lotes**: ETL periódico
- **Esquemas dimensionales**: Modelos estrella, copo de nieve y constelación

#### Conceptos Fundamentales

**Tabla de Hechos (Fact Table)**:
- Contiene métricas cuantificables del negocio
- Claves foráneas hacia tablas de dimensiones
- Medidas aditivas, semi-aditivas y no-aditivas

**Tabla de Dimensiones (Dimension Table)**:
- Contienen atributos descriptivos
- Proporcionan contexto a los hechos
- Jerarquías para drill-down y roll-up

#### Modelo Estrella (Star Schema)

**Características**:
- Tabla de hechos central rodeada de tablas de dimensiones
- Dimensiones desnormalizadas
- Consultas simples con pocos JOINs
- Mejor rendimiento de consultas

**Ejemplo - Ventas en Modelo Estrella**:

```sql
-- Tabla de Hechos: Ventas
CREATE TABLE fact_ventas (
    id_venta BIGSERIAL PRIMARY KEY,
    fk_fecha INTEGER NOT NULL,
    fk_producto INTEGER NOT NULL,
    fk_cliente INTEGER NOT NULL,
    fk_sucursal INTEGER NOT NULL,
    fk_vendedor INTEGER NOT NULL,
    
    -- Métricas (medidas)
    cantidad_vendida INTEGER NOT NULL,
    precio_unitario DECIMAL(12,2) NOT NULL,
    descuento DECIMAL(12,2) DEFAULT 0,
    impuestos DECIMAL(12,2) NOT NULL,
    costo_unitario DECIMAL(12,2) NOT NULL,
    
    -- Métricas calculadas
    venta_bruta DECIMAL(12,2) NOT NULL,
    venta_neta DECIMAL(12,2) NOT NULL,
    margen_bruto DECIMAL(12,2) NOT NULL,
    
    fecha_carga TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Dimensión Fecha (Time Dimension)
CREATE TABLE dim_fecha (
    id_fecha INTEGER PRIMARY KEY,
    fecha_completa DATE NOT NULL,
    año INTEGER NOT NULL,
    trimestre INTEGER NOT NULL,
    mes INTEGER NOT NULL,
    mes_nombre VARCHAR(20) NOT NULL,
    semana_año INTEGER NOT NULL,
    dia_año INTEGER NOT NULL,
    dia_mes INTEGER NOT NULL,
    dia_semana INTEGER NOT NULL,
    dia_semana_nombre VARCHAR(20) NOT NULL,
    es_fin_semana BOOLEAN NOT NULL,
    es_feriado BOOLEAN DEFAULT FALSE,
    nombre_feriado VARCHAR(100)
);

-- Dimensión Producto (desnormalizada)
CREATE TABLE dim_producto (
    id_producto INTEGER PRIMARY KEY,
    codigo_producto VARCHAR(50) NOT NULL,
    nombre_producto VARCHAR(200) NOT NULL,
    descripcion_producto TEXT,
    
    -- Jerarquía de categorías (desnormalizada)
    categoria_nivel1 VARCHAR(100) NOT NULL,
    categoria_nivel2 VARCHAR(100),
    categoria_nivel3 VARCHAR(100),
    
    -- Atributos del producto
    marca VARCHAR(100),
    modelo VARCHAR(100),
    color VARCHAR(50),
    tamaño VARCHAR(50),
    peso DECIMAL(8,2),
    
    precio_lista DECIMAL(12,2) NOT NULL,
    costo_estandar DECIMAL(12,2) NOT NULL,
    
    fecha_lanzamiento DATE,
    estado_producto VARCHAR(20) NOT NULL,
    
    fecha_inicio_vigencia DATE NOT NULL,
    fecha_fin_vigencia DATE,
    registro_activo BOOLEAN DEFAULT TRUE
);

-- Dimensión Cliente (desnormalizada)
CREATE TABLE dim_cliente (
    id_cliente INTEGER PRIMARY KEY,
    codigo_cliente VARCHAR(20) NOT NULL,
    nombre_completo VARCHAR(200) NOT NULL,
    nombre VARCHAR(100) NOT NULL,
    apellido VARCHAR(100) NOT NULL,
    
    -- Información demográfica
    fecha_nacimiento DATE,
    edad_actual INTEGER,
    rango_edad VARCHAR(20),
    genero VARCHAR(10),
    
    -- Información geográfica (desnormalizada)
    pais VARCHAR(100) NOT NULL,
    estado_provincia VARCHAR(100) NOT NULL,
    ciudad VARCHAR(100) NOT NULL,
    codigo_postal VARCHAR(20),
    region_comercial VARCHAR(100),
    
    -- Información de contacto
    email VARCHAR(255),
    telefono VARCHAR(20),
    
    -- Segmentación
    segmento_cliente VARCHAR(50),
    categoria_cliente VARCHAR(20),
    
    fecha_registro DATE NOT NULL,
    fecha_primera_compra DATE,
    fecha_ultima_compra DATE,
    
    fecha_inicio_vigencia DATE NOT NULL,
    fecha_fin_vigencia DATE,
    registro_activo BOOLEAN DEFAULT TRUE
);

-- Dimensión Sucursal
CREATE TABLE dim_sucursal (
    id_sucursal INTEGER PRIMARY KEY,
    codigo_sucursal VARCHAR(20) NOT NULL,
    nombre_sucursal VARCHAR(100) NOT NULL,
    tipo_sucursal VARCHAR(50) NOT NULL,
    
    -- Ubicación (desnormalizada)
    direccion_completa TEXT NOT NULL,
    pais VARCHAR(100) NOT NULL,
    estado_provincia VARCHAR(100) NOT NULL,
    ciudad VARCHAR(100) NOT NULL,
    codigo_postal VARCHAR(20),
    
    -- Clasificación
    region_comercial VARCHAR(100) NOT NULL,
    zona_comercial VARCHAR(100) NOT NULL,
    area_metros2 DECIMAL(8,2),
    clasificacion_tamaño VARCHAR(20),
    
    telefono VARCHAR(20),
    email VARCHAR(255),
    
    fecha_apertura DATE NOT NULL,
    gerente_sucursal VARCHAR(100),
    
    fecha_inicio_vigencia DATE NOT NULL,
    fecha_fin_vigencia DATE,
    registro_activo BOOLEAN DEFAULT TRUE
);

-- Dimensión Vendedor
CREATE TABLE dim_vendedor (
    id_vendedor INTEGER PRIMARY KEY,
    codigo_vendedor VARCHAR(20) NOT NULL,
    nombre_completo VARCHAR(200) NOT NULL,
    nombre VARCHAR(100) NOT NULL,
    apellido VARCHAR(100) NOT NULL,
    
    -- Información profesional
    cargo VARCHAR(100) NOT NULL,
    nivel_vendedor VARCHAR(50),
    equipo_ventas VARCHAR(100),
    supervisor VARCHAR(100),
    
    -- Información demográfica
    edad_actual INTEGER,
    rango_edad VARCHAR(20),
    años_experiencia INTEGER,
    
    -- Información geográfica
    sucursal_asignada VARCHAR(100) NOT NULL,
    region_asignada VARCHAR(100) NOT NULL,
    
    fecha_ingreso DATE NOT NULL,
    fecha_inicio_vigencia DATE NOT NULL,
    fecha_fin_vigencia DATE,
    registro_activo BOOLEAN DEFAULT TRUE
);

-- Índices para optimizar consultas OLAP
CREATE INDEX idx_fact_ventas_fecha ON fact_ventas(fk_fecha);
CREATE INDEX idx_fact_ventas_producto ON fact_ventas(fk_producto);
CREATE INDEX idx_fact_ventas_cliente ON fact_ventas(fk_cliente);
CREATE INDEX idx_fact_ventas_sucursal ON fact_ventas(fk_sucursal);
CREATE INDEX idx_fact_ventas_vendedor ON fact_ventas(fk_vendedor);

-- Índice compuesto para consultas frecuentes
CREATE INDEX idx_fact_ventas_fecha_sucursal ON fact_ventas(fk_fecha, fk_sucursal);
CREATE INDEX idx_fact_ventas_producto_fecha ON fact_ventas(fk_producto, fk_fecha);
```

**Consulta Ejemplo - Modelo Estrella**:

```sql
-- Ventas por región y mes en 2024
SELECT 
    df.año,
    df.mes_nombre,
    ds.region_comercial,
    SUM(fv.venta_neta) as total_ventas,
    SUM(fv.cantidad_vendida) as total_cantidad,
    COUNT(DISTINCT fv.fk_cliente) as clientes_unicos,
    AVG(fv.venta_neta) as venta_promedio
FROM fact_ventas fv
    INNER JOIN dim_fecha df ON fv.fk_fecha = df.id_fecha
    INNER JOIN dim_sucursal ds ON fv.fk_sucursal = ds.id_sucursal
WHERE df.año = 2024
    AND ds.registro_activo = TRUE
GROUP BY df.año, df.mes, df.mes_nombre, ds.region_comercial
ORDER BY df.mes, ds.region_comercial;
```

#### Modelo Copo de Nieve (Snowflake Schema)

**Características**:
- Dimensiones normalizadas en múltiples tablas relacionadas
- Reduce redundancia de datos
- Consultas más complejas con más JOINs
- Mejor para integridad de datos dimensionales

**Ejemplo - Dimensión Producto Normalizada**:

```sql
-- Tabla principal de Producto
CREATE TABLE dim_producto_snow (
    id_producto INTEGER PRIMARY KEY,
    codigo_producto VARCHAR(50) NOT NULL,
    nombre_producto VARCHAR(200) NOT NULL,
    descripcion_producto TEXT,
    fk_categoria INTEGER NOT NULL,
    fk_marca INTEGER NOT NULL,
    modelo VARCHAR(100),
    color VARCHAR(50),
    tamaño VARCHAR(50),
    peso DECIMAL(8,2),
    precio_lista DECIMAL(12,2) NOT NULL,
    costo_estandar DECIMAL(12,2) NOT NULL,
    fecha_lanzamiento DATE,
    estado_producto VARCHAR(20) NOT NULL
);

-- Tabla de Categorías (normalizada)
CREATE TABLE dim_categoria (
    id_categoria INTEGER PRIMARY KEY,
    codigo_categoria VARCHAR(20) NOT NULL,
    nombre_categoria VARCHAR(100) NOT NULL,
    descripcion_categoria TEXT,
    fk_categoria_padre INTEGER REFERENCES dim_categoria(id_categoria),
    nivel_jerarquia INTEGER NOT NULL
);

-- Tabla de Marcas
CREATE TABLE dim_marca (
    id_marca INTEGER PRIMARY KEY,
    codigo_marca VARCHAR(20) NOT NULL,
    nombre_marca VARCHAR(100) NOT NULL,
    descripcion_marca TEXT,
    pais_origen VARCHAR(100),
    año_fundacion INTEGER,
    sitio_web VARCHAR(255)
);

-- Tabla de Jerarquía de Categorías (para navegación)
CREATE TABLE dim_jerarquia_categoria (
    id_jerarquia SERIAL PRIMARY KEY,
    id_categoria INTEGER NOT NULL,
    categoria_nivel1 VARCHAR(100) NOT NULL,
    categoria_nivel2 VARCHAR(100),
    categoria_nivel3 VARCHAR(100),
    categoria_nivel4 VARCHAR(100),
    path_completo VARCHAR(500) NOT NULL,
    nivel_profundidad INTEGER NOT NULL
);

-- Referencias
ALTER TABLE dim_producto_snow ADD CONSTRAINT fk_producto_categoria
    FOREIGN KEY (fk_categoria) REFERENCES dim_categoria(id_categoria);

ALTER TABLE dim_producto_snow ADD CONSTRAINT fk_producto_marca
    FOREIGN KEY (fk_marca) REFERENCES dim_marca(id_marca);
```

**Consulta Ejemplo - Modelo Copo de Nieve**:

```sql
-- Ventas por jerarquía de categorías (requiere más JOINs)
SELECT 
    dm.nombre_marca,
    dc1.nombre_categoria as categoria_principal,
    dc2.nombre_categoria as subcategoria,
    dp.nombre_producto,
    SUM(fv.venta_neta) as total_ventas,
    SUM(fv.cantidad_vendida) as total_cantidad
FROM fact_ventas fv
    INNER JOIN dim_producto_snow dp ON fv.fk_producto = dp.id_producto
    INNER JOIN dim_categoria dc1 ON dp.fk_categoria = dc1.id_categoria
    LEFT JOIN dim_categoria dc2 ON dc1.fk_categoria_padre = dc2.id_categoria
    INNER JOIN dim_marca dm ON dp.fk_marca = dm.id_marca
    INNER JOIN dim_fecha df ON fv.fk_fecha = df.id_fecha
WHERE df.año = 2024
GROUP BY dm.nombre_marca, dc1.nombre_categoria, dc2.nombre_categoria, dp.nombre_producto
ORDER BY total_ventas DESC;
```

#### Modelo Constelación (Galaxy Schema)

**Características**:
- Múltiples tablas de hechos que comparten dimensiones
- Dimensiones conformadas (conformed dimensions)
- Escalabilidad para múltiples procesos de negocio
- Consistencia entre diferentes áreas analíticas

**Ejemplo - Múltiples Hechos**:

```sql
-- Tabla de Hechos: Ventas
CREATE TABLE fact_ventas (
    id_venta BIGSERIAL PRIMARY KEY,
    fk_fecha INTEGER NOT NULL,
    fk_producto INTEGER NOT NULL,
    fk_cliente INTEGER NOT NULL,
    fk_sucursal INTEGER NOT NULL,
    cantidad_vendida INTEGER NOT NULL,
    venta_neta DECIMAL(12,2) NOT NULL,
    costo_total DECIMAL(12,2) NOT NULL,
    margen_bruto DECIMAL(12,2) NOT NULL
);

-- Tabla de Hechos: Inventario (comparte dimensiones)
CREATE TABLE fact_inventario (
    id_inventario BIGSERIAL PRIMARY KEY,
    fk_fecha INTEGER NOT NULL,
    fk_producto INTEGER NOT NULL,
    fk_sucursal INTEGER NOT NULL,
    stock_inicial INTEGER NOT NULL,
    entradas INTEGER DEFAULT 0,
    salidas INTEGER DEFAULT 0,
    stock_final INTEGER NOT NULL,
    costo_promedio DECIMAL(12,2) NOT NULL,
    valor_inventario DECIMAL(12,2) NOT NULL
);

-- Tabla de Hechos: Compras
CREATE TABLE fact_compras (
    id_compra BIGSERIAL PRIMARY KEY,
    fk_fecha INTEGER NOT NULL,
    fk_producto INTEGER NOT NULL,
    fk_proveedor INTEGER NOT NULL,
    fk_sucursal INTEGER NOT NULL,
    cantidad_comprada INTEGER NOT NULL,
    costo_unitario DECIMAL(12,2) NOT NULL,
    costo_total DECIMAL(12,2) NOT NULL
);

-- Dimensión Proveedor (específica para compras)
CREATE TABLE dim_proveedor (
    id_proveedor INTEGER PRIMARY KEY,
    codigo_proveedor VARCHAR(20) NOT NULL,
    nombre_proveedor VARCHAR(200) NOT NULL,
    categoria_proveedor VARCHAR(100),
    pais VARCHAR(100) NOT NULL,
    ciudad VARCHAR(100) NOT NULL,
    contacto_principal VARCHAR(100),
    telefono VARCHAR(20),
    email VARCHAR(255),
    calificacion_proveedor VARCHAR(20),
    fecha_inicio_relacion DATE NOT NULL
);

-- Las dimensiones dim_fecha, dim_producto, dim_sucursal son compartidas
-- entre todas las tablas de hechos (dimensiones conformadas)
```

#### Ventajas y Desventajas de Cada Modelo

| Aspecto | Estrella | Copo de Nieve | Constelación |
|---------|----------|---------------|--------------|
| **Complejidad de Consultas** | Baja | Media-Alta | Media |
| **Rendimiento** | Alto | Medio | Alto |
| **Espacio de Almacenamiento** | Alto | Bajo | Medio |
| **Mantenimiento** | Fácil | Complejo | Medio |
| **Integridad Dimensional** | Baja | Alta | Alta |
| **Flexibilidad** | Baja | Alta | Alta |

---

### Bases de Datos Tipo Data Lake

#### Características Principales
- **Almacenamiento de datos en bruto**: Datos estructurados, semi-estructurados y no estructurados
- **Esquema al momento de lectura**: Schema-on-Read vs Schema-on-Write
- **Escalabilidad masiva**: Petabytes de información
- **Diversidad de formatos**: JSON, XML, CSV, Parquet, Avro, logs, imágenes, videos
- **Procesamiento distribuido**: Hadoop, Spark, cloud-native solutions

#### Arquitectura de Data Lake

**Capas del Data Lake**:

1. **Capa de Ingesta (Data Ingestion)**
   - Batch processing (ETL tradicional)
   - Streaming en tiempo real
   - APIs y conectores

2. **Capa de Almacenamiento (Storage Layer)**
   - Almacenamiento distribuido (HDFS, S3, Azure Data Lake)
   - Particionamiento y organización de datos
   - Gestión de metadatos

3. **Capa de Procesamiento (Processing Layer)**
   - Apache Spark, Hadoop MapReduce
   - Transformaciones y limpieza de datos
   - Machine Learning pipelines

4. **Capa de Análisis (Analytics Layer)**
   - SQL engines (Presto, Athena, BigQuery)
   - BI tools y visualización
   - Machine Learning y AI

#### Estructura de Organización en Data Lake

```
data-lake/
├── raw/                          # Datos en bruto
│   ├── transactional/
│   │   ├── year=2024/
│   │   │   ├── month=01/
│   │   │   │   ├── day=01/
│   │   │   │   │   ├── sales_20240101.json
│   │   │   │   │   ├── customers_20240101.csv
│   │   │   │   │   └── products_20240101.parquet
│   │   │   │   └── day=02/
│   │   │   └── month=02/
│   │   └── year=2025/
│   ├── streaming/
│   │   ├── clickstream/
│   │   ├── iot_sensors/
│   │   └── social_media/
│   ├── external/
│   │   ├── market_data/
│   │   ├── weather/
│   │   └── demographics/
│   └── unstructured/
│       ├── documents/
│       ├── images/
│       └── videos/
├── processed/                    # Datos procesados/limpiados
│   ├── curated/
│   │   ├── customer_360/
│   │   ├── product_catalog/
│   │   └── sales_summary/
│   └── aggregated/
│       ├── daily_metrics/
│       ├── monthly_reports/
│       └── yearly_summaries/
├── sandbox/                      # Área de experimentación
│   ├── data_science/
│   ├── analytics_team/
│   └── temp_projects/
└── archive/                      # Datos archivados
    ├── year=2020/
    ├── year=2021/
    └── year=2022/
```

#### Formatos de Datos en Data Lake

**Formatos Estructurados**:

```sql
-- Ejemplo: Datos de ventas en formato Parquet
-- Esquema inferido al momento de lectura
CREATE EXTERNAL TABLE sales_raw (
    transaction_id STRING,
    customer_id BIGINT,
    product_id BIGINT,
    quantity INT,
    unit_price DECIMAL(10,2),
    total_amount DECIMAL(12,2),
    transaction_date TIMESTAMP,
    store_id INT,
    salesperson_id INT
)
STORED AS PARQUET
LOCATION 's3://data-lake/raw/transactional/sales/'
PARTITIONED BY (
    year INT,
    month INT,
    day INT
);
```

**Formatos Semi-estructurados**:

```json
-- Ejemplo: Datos de clickstream en JSON
{
  "event_id": "evt_20240115_001234",
  "timestamp": "2024-01-15T14:30:45.123Z",
  "user_id": "user_789456",
  "session_id": "sess_abc123def456",
  "event_type": "page_view",
  "page_info": {
    "url": "/products/electronics/smartphones",
    "title": "Smartphones - Electronics Store",
    "category": "electronics",
    "subcategory": "smartphones"
  },
  "user_agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36",
  "geo_location": {
    "country": "Chile",
    "region": "Santiago Metropolitan",
    "city": "Santiago",
    "coordinates": {
      "lat": -33.4489,
      "lon": -70.6693
    }
  },
  "device_info": {
    "device_type": "desktop",
    "browser": "Chrome",
    "os": "Windows 10",
    "screen_resolution": "1920x1080"
  },
  "referrer": "https://google.com/search?q=smartphones",
  "utm_parameters": {
    "source": "google",
    "medium": "cpc",
    "campaign": "electronics_2024",
    "content": "smartphone_ad_v1"
  }
}
```

#### Catálogo de Datos y Metadatos

```sql
-- Tabla de Metadatos para Datasets
CREATE TABLE data_catalog (
    dataset_id UUID PRIMARY KEY,
    dataset_name VARCHAR(255) NOT NULL,
    dataset_description TEXT,
    source_system VARCHAR(100) NOT NULL,
    data_format VARCHAR(50) NOT NULL, -- JSON, Parquet, CSV, Avro, etc.
    storage_location TEXT NOT NULL,
    schema_definition JSONB,
    partition_strategy VARCHAR(100),
    data_classification VARCHAR(50), -- Public, Internal, Confidential, Restricted
    owner_team VARCHAR(100) NOT NULL,
    steward_contact VARCHAR(255),
    
    -- Información temporal
    creation_date TIMESTAMP NOT NULL,
    last_updated TIMESTAMP NOT NULL,
    retention_period_days INTEGER,
    
    -- Métricas de calidad
    quality_score DECIMAL(3,2),
    completeness_percentage DECIMAL(5,2),
    last_quality_check TIMESTAMP,
    
    -- Información de acceso
    access_frequency VARCHAR(20), -- High, Medium, Low
    last_accessed TIMESTAMP,
    access_pattern VARCHAR(50), -- Batch, Streaming, Ad-hoc
    
    tags TEXT[], -- Array de tags para búsqueda
    business_glossary_terms TEXT[],
    
    -- Información de lineage
    upstream_sources TEXT[],
    downstream_consumers TEXT[],
    
    status VARCHAR(20) DEFAULT 'ACTIVE' -- ACTIVE, DEPRECATED, ARCHIVED
);

-- Tabla de Esquemas versionados
CREATE TABLE schema_registry (
    schema_id UUID PRIMARY KEY,
    dataset_id UUID REFERENCES data_catalog(dataset_id),
    schema_version INTEGER NOT NULL,
    schema_definition JSONB NOT NULL,
    compatibility_type VARCHAR(20), -- BACKWARD, FORWARD, FULL, NONE

---

## Bases de Datos No Relacionales (NoSQL)

### Definición
Sistemas de gestión de bases de datos que no siguen el modelo relacional tradicional, diseñados para manejar grandes volúmenes de datos distribuidos.

### Tipos de Bases de Datos NoSQL

#### 1. Bases de Datos de Documentos
**Características**:
- Almacenan documentos (JSON, BSON, XML)
- Esquema flexible
- Consultas por contenido del documento

**Sistemas**: MongoDB, CouchDB, Amazon DocumentDB

**Ejemplo MongoDB**:
```javascript
{
  "_id": ObjectId("..."),
  "nombre": "Juan Pérez",
  "email": "juan@email.com",
  "direccion": {
    "calle": "Av. Principal 123",
    "ciudad": "Santiago",
    "codigo_postal": "8320000"
  },
  "telefonos": ["555-1234", "555-5678"],
  "pedidos": [
    {
      "id_pedido": "P001",
      "fecha": "2024-01-15",
      "total": 150.50
    }
  ]
}
```

**Casos de Uso**:
- Gestión de contenido
- Catálogos de productos
- Perfiles de usuario
- Aplicaciones web

#### 2. Bases de Datos Clave-Valor
**Características**:
- Modelo más simple: clave única → valor
- Alto rendimiento
- Escalabilidad horizontal

**Sistemas**: Redis, Amazon DynamoDB, Riak

**Ejemplo Redis**:
```
SET usuario:1001:nombre "Juan Pérez"
SET usuario:1001:email "juan@email.com"
HSET usuario:1001:config tema "oscuro" idioma "es"
```

**Casos de Uso**:
- Cache de sesiones
- Configuraciones de aplicación
- Carritos de compra
- Contadores en tiempo real

#### 3. Bases de Datos Columnares
**Características**:
- Datos organizados por familias de columnas
- Optimizado para consultas analíticas
- Compresión eficiente

**Sistemas**: Cassandra, HBase, Amazon Redshift

**Ejemplo Cassandra**:
```sql
CREATE TABLE ventas_por_fecha (
    fecha DATE,
    region TEXT,
    producto TEXT,
    cantidad INT,
    precio DECIMAL,
    PRIMARY KEY (fecha, region)
) WITH CLUSTERING ORDER BY (region ASC);
```

**Casos de Uso**:
- Análisis de big data
- Sistemas de logging
- Métricas y monitoreo
- Data warehousing

#### 4. Bases de Datos de Grafos
**Características**:
- Nodos (entidades) y aristas (relaciones)
- Optimizado para consultas de relaciones complejas
- Algoritmos de grafos integrados

**Sistemas**: Neo4j, Amazon Neptune, ArangoDB

**Ejemplo Neo4j (Cypher)**:
```cypher
CREATE (juan:Persona {nombre: "Juan Pérez", edad: 30})
CREATE (maria:Persona {nombre: "María García", edad: 25})
CREATE (empresa:Empresa {nombre: "TechCorp"})
CREATE (juan)-[:TRABAJA_EN {desde: "2020-01-01"}]->(empresa)
CREATE (juan)-[:CONOCE {desde: "2015-06-15"}]->(maria)

// Consultar amigos de amigos
MATCH (p:Persona {nombre: "Juan Pérez"})-[:CONOCE]->(amigo)-[:CONOCE]->(amigoDeAmigo)
RETURN amigoDeAmigo.nombre
```

**Casos de Uso**:
- Redes sociales
- Sistemas de recomendación
- Detección de fraude
- Análisis de redes

### Comparación NoSQL vs SQL

| Aspecto | SQL (Relacional) | NoSQL |
|---------|------------------|-------|
| **Esquema** | Fijo, predefinido | Flexible, dinámico |
| **Escalabilidad** | Vertical (scale-up) | Horizontal (scale-out) |
| **Consistencia** | ACID fuerte | Eventual (BASE) |
| **Consultas** | SQL estándar | APIs específicas |
| **Relaciones** | JOINs complejos | Desnormalización |
| **Casos de Uso** | Transacciones complejas | Big Data, alta velocidad |

### Teorema CAP
En sistemas distribuidos, solo se pueden garantizar dos de estas tres propiedades:
- **Consistencia**: Todos los nodos ven los mismos datos simultáneamente
- **Disponibilidad**: El sistema permanece operativo
- **Tolerancia a Particiones**: El sistema continúa funcionando a pesar de fallos de red

**Clasificación**:
- **CP**: MongoDB, HBase
- **AP**: Cassandra, DynamoDB
- **CA**: Sistemas relacionales tradicionales

---

## Buenas Prácticas y Estándares

### Convenciones de Nomenclatura

#### Tablas
- Usar nombres en singular: `CLIENTE`, no `CLIENTES`
- Usar sustantivos descriptivos
- Prefijos para tablas de tipos específicos:
  - `CAT_`: Catálogos (`CAT_ESTADOS`)
  - `LOG_`: Auditoría (`LOG_TRANSACCIONES`)
  - `TMP_`: Temporales (`TMP_IMPORTACION`)

#### Columnas
- Nombres descriptivos y concisos
- Usar prefijos para identificadores:
  - `id_`: Claves primarias (`id_cliente`)
  - `fk_`: Claves foráneas (`fk_cliente`)
- Usar sufijos para tipos específicos:
  - `_fecha`: Campos de fecha (`fecha_creacion`)
  - `_flag`: Campos booleanos (`activo_flag`)

#### Restricciones
- **Primary Keys**: `PK_[TABLA]`
- **Foreign Keys**: `FK_[TABLA_HIJO]_[TABLA_PADRE]`
- **Unique**: `UK_[TABLA]_[COLUMNAS]`
- **Check**: `CHK_[TABLA]_[CONDICION]`

#### Índices
- **Simples**: `IDX_[TABLA]_[COLUMNA]`
- **Compuestos**: `IDX_[TABLA]_[COLUMNA1]_[COLUMNA2]`
- **Únicos**: `UDX_[TABLA]_[COLUMNAS]`

### Estándares de Diseño

#### Tipos de Datos Recomendados

**PostgreSQL**:
```sql
-- Identificadores
SERIAL, BIGSERIAL (auto-increment)
UUID (identificadores distribuidos)

-- Texto
VARCHAR(n) -- longitud variable limitada
TEXT -- longitud variable ilimitada
CHAR(n) -- longitud fija

-- Números
INTEGER, BIGINT
DECIMAL(precision, scale) -- para dinero
NUMERIC(precision, scale) -- cálculos exactos

-- Fechas
DATE -- solo fecha
TIMESTAMP -- fecha y hora
TIMESTAMPTZ -- fecha y hora con zona horaria

-- Booleanos
BOOLEAN

-- JSON
JSON, JSONB (binario, más eficiente)
```

**SQL Server**:
```sql
-- Identificadores
INT IDENTITY(1,1)
UNIQUEIDENTIFIER (GUID)

-- Texto
NVARCHAR(n) -- Unicode variable
NCHAR(n) -- Unicode fijo
VARCHAR(n) -- ASCII variable

-- Números
INT, BIGINT
DECIMAL(precision, scale)
MONEY -- tipo específico para dinero

-- Fechas
DATE
DATETIME2(precision)
DATETIMEOFFSET -- con zona horaria

-- Booleanos
BIT
```

#### Reglas de Integridad

1. **Integridad de Entidad**
   - Toda tabla debe tener clave primaria
   - Los valores de clave primaria no pueden ser nulos

2. **Integridad Referencial**
   - Las claves foráneas deben referenciar claves primarias existentes
   - Definir acciones para UPDATE y DELETE

3. **Integridad de Dominio**
   - Usar CHECK constraints para validar rangos
   - Definir valores por defecto apropiados

#### Ejemplo de Implementación Completa

```sql
-- Tabla de Clientes
CREATE TABLE cliente (
    id_cliente SERIAL PRIMARY KEY,
    codigo_cliente VARCHAR(20) NOT NULL,
    nombre VARCHAR(100) NOT NULL,
    apellido VARCHAR(100) NOT NULL,
    email VARCHAR(255),
    telefono VARCHAR(20),
    fecha_nacimiento DATE,
    fecha_registro TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    activo BOOLEAN DEFAULT TRUE,
    
    CONSTRAINT uk_cliente_codigo UNIQUE (codigo_cliente),
    CONSTRAINT uk_cliente_email UNIQUE (email),
    CONSTRAINT chk_cliente_email CHECK (email ~* '^[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}$'),
    CONSTRAINT chk_cliente_fecha_nacimiento CHECK (fecha_nacimiento <= CURRENT_DATE)
);

-- Tabla de Pedidos
CREATE TABLE pedido (
    id_pedido SERIAL PRIMARY KEY,
    numero_pedido VARCHAR(20) NOT NULL,
    fk_cliente INTEGER NOT NULL,
    fecha_pedido TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    fecha_entrega DATE,
    estado VARCHAR(20) DEFAULT 'PENDIENTE',
    subtotal DECIMAL(12,2) DEFAULT 0,
    impuestos DECIMAL(12,2) DEFAULT 0,
    total DECIMAL(12,2) DEFAULT 0,
    
    CONSTRAINT uk_pedido_numero UNIQUE (numero_pedido),
    CONSTRAINT fk_pedido_cliente FOREIGN KEY (fk_cliente) 
        REFERENCES cliente(id_cliente) ON DELETE RESTRICT ON UPDATE CASCADE,
    CONSTRAINT chk_pedido_estado CHECK (estado IN ('PENDIENTE', 'PROCESANDO', 'ENVIADO', 'ENTREGADO', 'CANCELADO')),
    CONSTRAINT chk_pedido_totales CHECK (total = subtotal + impuestos),
    CONSTRAINT chk_pedido_fecha_entrega CHECK (fecha_entrega >= fecha_pedido::DATE)
);

-- Índices para optimización
CREATE INDEX idx_cliente_nombre ON cliente(nombre, apellido);
CREATE INDEX idx_cliente_email ON cliente(email);
CREATE INDEX idx_pedido_cliente ON pedido(fk_cliente);
CREATE INDEX idx_pedido_fecha ON pedido(fecha_pedido);
CREATE INDEX idx_pedido_estado ON pedido(estado);

-- Vista para consultas frecuentes
CREATE VIEW vista_pedidos_cliente AS
SELECT 
    p.id_pedido,
    p.numero_pedido,
    p.fecha_pedido,
    p.estado,
    p.total,
    c.nombre,
    c.apellido,
    c.email
FROM pedido p
INNER JOIN cliente c ON p.fk_cliente = c.id_cliente
WHERE c.activo = TRUE;
```

### Documentación y Mantenimiento

#### Control de Versiones
- Usar scripts de migración numerados secuencialmente
- Documentar todos los cambios en el esquema
- Mantener scripts de rollback

#### Respaldo y Recuperación
- Definir estrategias de backup según criticidad
- Probar procedimientos de recuperación regularmente
- Documentar RTO (Recovery Time Objective) y RPO (Recovery Point Objective)

#### Monitoreo y Optimización
- Implementar monitoreo de rendimiento
- Analizar planes de ejecución regularmente
- Mantener estadísticas actualizadas

Este documento debe ser revisado y actualizado periódicamente para incorporar nuevas tecnologías, mejores prácticas y lecciones aprendidas en proyectos específicos.
