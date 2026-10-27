# Medidas DAX – Análisis de ventas de vehículos
Este documento reúne las principales medidas DAX utilizadas en el dashboard de Power BI.
## 1. Modelado de datos
### Tabla Calendario
Se creó una tabla calendario para facilitar los cálculos temporales en el modelo de Power BI.
```
Calendario = 
ADDCOLUMNS(
    CALENDAR(MIN('fact_vehiculos'[fecha_operacion]), MAX('fact_vehiculos'[fecha_operacion])),
    "Año", YEAR([Date]),
    "Mes", FORMAT([Date], "MMM"),
    "MesNum", MONTH([Date]),
    "Trimestre", "T" & FORMAT([Date], "Q")
)
```
## 2. Medidas DAX
### Ventas
Ventas USD = SUM(fact_vehiculos[precio_venta_USD])
### Unidades Vendidas
Unidades Vendidas = COUNTROWS(fact_vehiculos)
### Configuraciones Totales
Configuraciones Totales = COUNT(dim_configuracion[id_configuracion])
### Costo
Costo USD = SUM(fact_vehiculos[costo_USD])
### Margen USD
Margen USD = [Ventas USD] - [Costo USD]
### Precio Promedio USD
Precio Promedio USD = AVERAGE(fact_vehiculos[precio_venta_USD])
### Margen por Unidad USD
Margen por Unidad USD = DIVIDE([Margen USD], [Unidades Vendidas])
### Margen % = DIVIDE([Margen USD], [Ventas USD])
Margen % = DIVIDE([Margen USD], [Ventas USD])
### % Mix Margen
```
DIVIDE(
    [Margen USD],
    CALCULATE([Margen USD], ALL(dim_configuracion))
)
```
### % Mix Unidades
```
DIVIDE(
    [Unidades Vendidas],
    CALCULATE([Unidades Vendidas], ALL(dim_configuracion))
)
```
### Ranking Config Margen
```
Ranking Config Margen = 
RANKX(
    ALLSELECTED(dim_configuracion[id_configuracion]),
    [Margen USD],
    ,
    DESC
)
```
### Promedio Margen Unidad Top15
```
Promedio Margen Unidad Top15 = 
AVERAGEX(
    FILTER(
        ALLSELECTED(dim_configuracion[id_configuracion]),
        [Ranking Config Margen] <= 15
    ),
    CALCULATE([Margen por Unidad USD])
)
```
### Promedio Unidades Top15
```
Promedio Unidades Top15 = 
AVERAGEX(
    FILTER(
        ALLSELECTED(dim_configuracion[id_configuracion]),
        [Ranking Config Margen] <= 15
    ),
    CALCULATE([Unidades Vendidas])
)
```
### Color Cuadrante
```
Color Cuadrante = 
VAR LimiteUnidades = [Promedio Unidades Top15]  
VAR LimiteMargen = [Promedio Margen Unidad Top15] 
RETURN
IF(
    [Unidades Vendidas] < LimiteUnidades && [Margen por Unidad USD] < LimiteMargen, 
    1,
    IF([Unidades Vendidas] >= LimiteUnidades && [Margen por Unidad USD] >= LimiteMargen, 2,
    IF([Unidades Vendidas] >= LimiteUnidades && [Margen por Unidad USD] < LimiteMargen, 3,4))
)
```
