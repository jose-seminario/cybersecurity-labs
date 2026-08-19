# Ataque de SQL Injection — Consultando el Tipo y Versión de la Base de Datos en Oracle

> **Laboratorio:** PortSwigger Web Security Academy — SQL injection attack, querying the database type and version on Oracle
> **Categoría:** SQL Injection
> **Base de datos objetivo:** Oracle 11g Express Edition

---

## 1. Inicio del laboratorio

El objetivo de este laboratorio es explotar una vulnerabilidad de **SQL Injection** para conseguir que la aplicación consulte información sobre la base de datos Oracle y nos muestre su versión.

Al iniciar el laboratorio se presenta una tienda web con diferentes categorías de productos. La aplicación utiliza el parámetro `category` para filtrar los productos.

<img width="1600" height="776" alt="image" src="https://github.com/user-attachments/assets/c43e9c55-df85-4b4d-bf2a-53269b382ba9" />

---

## 2. Interceptar la petición con Burp Suite

Primero activamos **Intercept** en Burp Suite para poder capturar y modificar las peticiones HTTP que realiza el navegador.

Al seleccionar una categoría de productos, Burp Suite intercepta la petición correspondiente.

<img width="593" height="227" alt="image" src="https://github.com/user-attachments/assets/f71025c8-4e28-4ca9-b511-6ef153b655bb" />

La petición contiene el parámetro:

```text
category=Tech+gifts
```

Este parámetro será nuestro punto de entrada para comprobar si la aplicación es vulnerable a SQL Injection.

---

## 3. Enviar la petición a Repeater

Una vez interceptada la petición `GET`, hacemos clic derecho sobre ella y seleccionamos **Send to Repeater**.

Esto nos permite trabajar con la misma petición y modificar el parámetro `category` varias veces sin tener que repetir todo el proceso desde el navegador.

<img width="1378" height="557" alt="image" src="https://github.com/user-attachments/assets/a06a3a30-65c0-437e-8795-7180f1949178" />

En Repeater podemos modificar directamente el valor del parámetro y observar cómo cambia la respuesta del servidor.

---

## 4. Determinar el número de columnas de la consulta

Para utilizar un ataque mediante `UNION SELECT`, primero necesitamos conocer **cuántas columnas devuelve la consulta SQL original**.

Esto es importante porque la consulta que agreguemos mediante `UNION` debe devolver el mismo número de columnas que la consulta original.

En este laboratorio estamos trabajando con **Oracle**, por lo que también debemos tener en cuenta su sintaxis particular, incluyendo el uso de `FROM dual`.

La prueba utilizada fue:

```sql
'+UNION+SELECT+'abc','def'+FROM+dual--
```

Aquí estamos intentando devolver dos valores:

```text
abc → columna 1
def → columna 2
```

Si la aplicación muestra ambos valores, podemos confirmar que nuestra consulta `UNION SELECT` es compatible con **dos columnas** y que ambas pueden mostrar datos de texto.

---

## 5. Confirmar las dos columnas

Después de enviar la petición, la respuesta del servidor devuelve los valores:

```text
abc
def
```

Esto confirma que la consulta original devuelve **dos columnas** y que podemos utilizar ambas para devolver datos de tipo texto.

<img width="620" height="670" alt="image" src="https://github.com/user-attachments/assets/4cf6435c-6b69-4123-93cc-ac6c078eccc6" />

Es importante entender que `abc` y `def` son solamente valores de prueba. No estamos buscando todavía información de la base de datos; los utilizamos para comprobar que las dos columnas funcionan correctamente.

Además, `FROM dual` es necesario en este contexto porque **DUAL es una tabla especial de Oracle** que permite realizar un `SELECT` sin tener que consultar una tabla de datos real.

---

## 6. Obtener información sobre la versión de Oracle

Una vez confirmado que tenemos dos columnas, podemos utilizar el `UNION SELECT` para solicitar información real de la base de datos.

En Oracle, la vista:

```text
v$version
```

contiene información relacionada con la versión del sistema de base de datos.

Dentro de ella utilizamos la columna:

```text
BANNER
```

que contiene la información que queremos recuperar.

La inyección utilizada es:

```sql
'+UNION+SELECT+BANNER,+NULL+FROM+v$version--
```

**Desglose de la consulta:**

| Fragmento | Función |
|---|---|
| `UNION SELECT` | Combina nuestra consulta con la original |
| `BANNER` | Información de versión que queremos recuperar |
| `NULL` | Rellena la segunda columna necesaria para mantener el mismo número de columnas |
| `FROM v$version` | Indica de dónde obtener `BANNER` |
| `--` | Comenta el resto de la consulta original |

<img width="1225" height="703" alt="image" src="https://github.com/user-attachments/assets/f0469b0c-ab4f-4a8b-9590-a8637882f53a" />

Como resultado, la aplicación muestra información de Oracle, incluyendo:

```text
Oracle Database 11g Express Edition
Release 11.2.0.2.0 - 64bit Production
```

<img width="1214" height="802" alt="image" src="https://github.com/user-attachments/assets/1b2741ce-d605-4959-b642-3ec46489d516" />

Esto demuestra que conseguimos utilizar la vulnerabilidad de SQL Injection para consultar información de la base de datos.

**Nota:** `v$version` **no se utiliza para todas las bases de datos**. Es una característica de Oracle. Otros motores utilizan diferentes consultas para obtener su versión.

---

## 7. Laboratorio completado

Finalmente, la aplicación reconoce que se obtuvo la información solicitada y muestra el mensaje:

> **"Congratulations, you solved the lab!"**

<img width="1467" height="611" alt="image" src="https://github.com/user-attachments/assets/4eadf5ff-03d1-40a8-afac-522e100fac46" />

---

## Conclusión

En este laboratorio aprendí a utilizar una vulnerabilidad de **SQL Injection mediante `UNION SELECT`** para consultar información de una base de datos Oracle.

**Proceso seguido:**

```text
Interceptar la petición
        ↓
Enviar a Burp Repeater
        ↓
Identificar el número de columnas
        ↓
Comprobar que ambas columnas aceptan texto
        ↓
Utilizar UNION SELECT
        ↓
Consultar BANNER desde v$version
        ↓
Obtener la versión de Oracle
        ↓
Laboratorio completado
```

**Idea principal aprendida:** antes de utilizar `UNION SELECT`, debemos conocer el **número de columnas que devuelve la consulta original** y determinar cuáles pueden utilizarse para mostrar el tipo de información que queremos recuperar.
