# Inyección SQL — Consultando el Tipo y la Versión de la Base de Datos en MySQL y Microsoft SQL Server

## 📋 Resumen

| | |
|---|---|
| **Categoría** | Inyección SQL |
| **Objetivo** | Aplicación web de e-commerce |
| **Meta** | Obtener el tipo y la versión de la base de datos mediante `UNION SELECT` |
| **Bases de datos involucradas** | MySQL / Microsoft SQL Server |

---

## 1. Introducción al Laboratorio

Este laboratorio se centra en explotar una vulnerabilidad de **Inyección SQL** para obtener información sobre el tipo y la versión de la base de datos.

La aplicación es un sitio web de e-commerce con varias categorías de productos. El objetivo es manipular el parámetro `category` y usar un ataque `UNION SELECT` para obtener la versión de la base de datos.

<img width="1600" height="897" alt="image" src="https://github.com/user-attachments/assets/3237fd4e-822d-4c96-9a20-198bca420e28" />

---

## 2. Interceptando la Solicitud con Burp Suite

Primero, seleccioné una categoría de producto y usé **Burp Suite Proxy** para interceptar la solicitud HTTP generada por la aplicación.

La solicitud contiene el parámetro `category`, que es la entrada que se probará para la Inyección SQL.

Después de interceptar la solicitud, la envié a **Burp Suite Repeater** para poder modificar el parámetro y probar diferentes payloads de Inyección SQL.

<img width="1641" height="941" alt="image" src="https://github.com/user-attachments/assets/5f5ff39e-b73e-45ae-a116-3a3b6a373890" />

---

## 3. Identificando el Contexto de la Base de Datos

Como este laboratorio es muy similar al anterior de Oracle, inicialmente lo abordé con el mismo razonamiento.

Sin embargo, la sintaxis necesaria para la Inyección SQL puede variar según el sistema gestor de base de datos. En el laboratorio anterior, se requería sintaxis específica de Oracle como `FROM dual`, `BANNER` y `v$version`.

Para este laboratorio, la solución utiliza en cambio sintaxis compatible con **MySQL y Microsoft SQL Server**, como `@@version` y el carácter de comentario `#`.

> 💡 **Idea clave:** la misma técnica puede necesitar adaptarse según el motor de base de datos que se esté utilizando.

<img width="1223" height="774" alt="image" src="https://github.com/user-attachments/assets/bada390d-dfbf-4d04-a42a-6aab86fddd8c" />

---

## 4. Confirmando el Número de Columnas

Antes de usar `UNION SELECT` para obtener información, necesitaba determinar cuántas columnas devolvía la consulta original, y cuáles de esas columnas podían contener texto.

**Payload utilizado:**

```sql
' UNION SELECT 'abc','def'#
```

Los valores `abc` y `def` se usaron como datos de prueba, no como información real de la base de datos.

**Respuesta:**

```text
abc
def
```

Esto confirmó que la consulta original devuelve **dos columnas**, y que ambas columnas pueden usarse para mostrar datos de tipo texto.

<img width="1345" height="882" alt="image" src="https://github.com/user-attachments/assets/706e0a1d-eff8-48bb-8481-643002ee20a9" />

> ⚙️ **Concepto:** un `UNION SELECT` debe devolver el mismo número de columnas que la consulta original. Como la consulta original devolvía dos columnas, la consulta inyectada también debía devolver dos columnas.

---

## 5. Obteniendo la Versión de la Base de Datos

Una vez confirmado el número de columnas y su compatibilidad con datos de tipo texto, pude solicitar información sobre la versión de la base de datos.

Para MySQL y Microsoft SQL Server, se puede usar `@@version` para obtener la información de la versión.

La inyección se estructuró de manera que `@@version` se colocó en una columna y `NULL` se usó para la segunda columna, para mantener el número de columnas requerido:

```sql
' UNION SELECT @@version, NULL#
```

**Respuesta:**

```text
8.0.42-0ubuntu0.20.04.1
```

✅ Esto identifica la base de datos como **MySQL 8.0.42**, y la información de versión indica un paquete de Ubuntu 20.04.

<img width="1462" height="891" alt="image" src="https://github.com/user-attachments/assets/b5fd9567-bd3f-4882-ba89-873b1a8e07f4" />

Esto demuestra por qué es importante entender el contexto de la base de datos. En el laboratorio anterior de Oracle, tuve que usar estructuras específicas de Oracle como `v$version` y `BANNER`. Aquí, `@@version` fue el método adecuado.

---

## 6. Laboratorio Completado

El laboratorio se completó con éxito, confirmando que la Inyección SQL pudo explotarse para obtener información de la versión de la base de datos.

<img width="1397" height="626" alt="image" src="https://github.com/user-attachments/assets/3d68952b-98b0-481d-8100-98bd850b329d" />

Este laboratorio fue más sencillo que el anterior porque la metodología general fue muy similar. La principal diferencia fue adaptar la sintaxis SQL al motor de base de datos.

### 🔄 Resumen del Flujo de Trabajo

```text
Interceptar la solicitud
        ↓
Enviarla a Burp Repeater
        ↓
Probar el número de columnas
        ↓
Confirmar que las columnas admiten texto
        ↓
Usar UNION SELECT
        ↓
Consultar la versión de la base de datos
        ↓
Identificar MySQL 8.0.42
        ↓
Laboratorio resuelto ✅
```

### 🗝️ Conclusiones Clave

- `UNION SELECT` requiere el mismo número de columnas que la consulta original.
- Se pueden usar valores de prueba como `abc` y `def` para identificar columnas que aceptan texto.
- `NULL` puede usarse como marcador de posición cuando se requiere una segunda columna pero su valor no es importante.
- La sintaxis específica de cada base de datos importa al explotar una Inyección SQL.
- `@@version` puede usarse tanto en MySQL como en Microsoft SQL Server para obtener la información de versión.
- La misma metodología de Inyección SQL a menudo puede adaptarse a diferentes motores de base de datos cambiando la sintaxis específica de cada uno.
