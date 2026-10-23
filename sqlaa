use cars_evaluation;

-- Creamos todas las tablas
CREATE TABLE dim_estado_vehiculo (
    id_estado_vehiculo INT PRIMARY KEY AUTO_INCREMENT,
    estado_vehiculo VARCHAR(20) NOT NULL UNIQUE
);

CREATE TABLE dim_canal_venta (
    id_canal_venta INT PRIMARY KEY AUTO_INCREMENT,
    canal_venta VARCHAR(50) NOT NULL UNIQUE
);

CREATE TABLE dim_segmento (
    id_segmento INT PRIMARY KEY AUTO_INCREMENT,
    segmento VARCHAR(50) NOT NULL UNIQUE
);

CREATE TABLE dim_configuracion (
    id_configuracion INT PRIMARY KEY AUTO_INCREMENT,
    nivel_mantenimiento VARCHAR(20) NOT NULL,
    numero_puertas VARCHAR(10) NOT NULL,
    numero_personas VARCHAR(10) NOT NULL,
    tamaño_maletero VARCHAR(20) NOT NULL,
    seguridad VARCHAR(20) NOT NULL,
    UNIQUE (nivel_mantenimiento, numero_puertas, numero_personas, tamaño_maletero, seguridad)
);

CREATE TABLE fact_vehiculos (
    id_fact INT PRIMARY KEY AUTO_INCREMENT,
    fecha_operacion DATE NOT NULL,
    rango_precio VARCHAR(20) NOT NULL,
    id_estado_vehiculo INT NOT NULL,
    id_canal_venta INT NOT NULL,
    id_segmento INT NOT NULL,
    id_configuracion INT NOT NULL,
    precio_venta_USD DECIMAL(10,2) NOT NULL,
    costo_USD DECIMAL(10,2) NOT NULL,

    CONSTRAINT fk_fact_estado
        FOREIGN KEY (id_estado_vehiculo)
        REFERENCES dim_estado_vehiculo(id_estado_vehiculo),

    CONSTRAINT fk_fact_canal
        FOREIGN KEY (id_canal_venta)
        REFERENCES dim_canal_venta(id_canal_venta),

    CONSTRAINT fk_fact_segmento
        FOREIGN KEY (id_segmento)
        REFERENCES dim_segmento(id_segmento),

    CONSTRAINT fk_fact_configuracion
        FOREIGN KEY (id_configuracion)
        REFERENCES dim_configuracion(id_configuracion)
);

-- Insertamos los valores en las tablas

INSERT INTO dim_estado_vehiculo (estado_vehiculo)
SELECT DISTINCT estado_vehiculo
FROM ventas;

INSERT INTO dim_canal_venta (canal_venta)
SELECT DISTINCT canal_venta
FROM ventas;

INSERT INTO dim_segmento (segmento)
SELECT DISTINCT segmento
FROM ventas;

INSERT INTO dim_configuracion (
    nivel_mantenimiento,
    numero_puertas,
    numero_personas,
    tamaño_maletero,
    seguridad
)
SELECT DISTINCT
    nivel_mantenimiento,
    numero_puertas,
    numero_personas,
    tamaño_maletero,
    seguridad
FROM ventas;

INSERT INTO fact_vehiculos (
    fecha_operacion,
    rango_precio,
    id_estado_vehiculo,
    id_canal_venta,
    id_segmento,
    id_configuracion,
    precio_venta_USD,
    costo_USD
)
SELECT
    v.fecha_operacion,
    v.rango_precio,
    de.id_estado_vehiculo,
    dc.id_canal_venta,
    ds.id_segmento,
    dconf.id_configuracion,
    round(v.`precio_venta(USD)`,2),
    round(v.`costo(USD)`,2)
FROM ventas v
JOIN dim_estado_vehiculo de
    ON v.estado_vehiculo = de.estado_vehiculo
JOIN dim_canal_venta dc
    ON v.canal_venta = dc.canal_venta
JOIN dim_segmento ds
    ON v.segmento = ds.segmento
JOIN dim_configuracion dconf
    ON v.nivel_mantenimiento = dconf.nivel_mantenimiento
   AND v.numero_puertas = dconf.numero_puertas
   AND v.numero_personas = dconf.numero_personas
   AND v.tamaño_maletero = dconf.tamaño_maletero
   AND v.seguridad = dconf.seguridad;
