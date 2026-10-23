# Medidas DAX – Análisis de ventas de vehículos
Este documento reúne las principales medidas DAX utilizadas en el dashboard de Power BI.
## 1. Modelado de datos
### Tabla Calendario
Se creó una tabla calendario para facilitar los cálculos temporales en el modelo de Power BI.

```Calendario = 
ADDCOLUMNS(
    CALENDAR(MIN('fact_vehiculos'[fecha_operacion]), MAX('fact_vehiculos'[fecha_operacion])),
    "Año", YEAR([Date]),
    "Mes", FORMAT([Date], "MMM"),
    "MesNum", MONTH([Date]),
    "Trimestre", "T" & FORMAT([Date], "Q")
)
