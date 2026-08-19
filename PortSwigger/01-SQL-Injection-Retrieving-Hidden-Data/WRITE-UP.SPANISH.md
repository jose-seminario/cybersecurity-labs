# SQL Injection - Obteniendo Datos Ocultos
**Plataforma:** PortSwigger Web Security Academy  
**Categoría:** SQL Injection  
**Dificultad:** Apprentice  
**Estado:** ✅ Resuelto
## Objetivo
Explotar un SQL Injection en el filtro de categoría de productos para obtener productos no publicados.
## 1. Acceso Inicial
Comencé accediendo al laboratorio e inspeccionando las categorías de productos disponibles.
<img width="1600" height="763" alt="image" src="https://github.com/user-attachments/assets/86e9ed09-1f3a-498e-a541-fac19f1174b4" />
## 2. Identificando el Punto de Inyección
Seleccioné la categoría **Gifts** y noté que la categoría se reflejaba directamente en la URL:
`/filter?category=Gifts`
<img width="1134" height="497" alt="image" src="https://github.com/user-attachments/assets/f5a1a163-93c1-41ca-94a4-96ebdcf7736c" />
Esto indicaba que el parámetro `category` era un posible punto de inyección.
## 3. SQL Injection
Modifiqué el parámetro `category` para alterar la consulta SQL.
La inyección utilizada fue:
`'+OR+1=1--`
<img width="1127" height="531" alt="image" src="https://github.com/user-attachments/assets/95cbc60b-f7a2-4f5a-88de-b59e9e8e2024" />
La aplicación mostró entonces el mensaje de éxito del laboratorio, confirmando que el SQL Injection permitió obtener los datos ocultos.
## Lo que aprendí
- Cómo identificar un posible punto de SQL Injection.
- Cómo `OR 1=1` puede hacer que una condición sea siempre verdadera.
- Cómo `--` puede comentar el resto de una consulta SQL.
- Cómo el SQL Injection puede usarse para obtener datos ocultos.
## Resultado
✅ Laboratorio completado con éxito.
