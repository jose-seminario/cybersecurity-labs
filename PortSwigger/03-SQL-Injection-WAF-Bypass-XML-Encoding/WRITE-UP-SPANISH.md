# SQL Injection con Bypass de Filtro mediante Codificación XML

**Plataforma:** PortSwigger Web Security Academy
**Categoría:** SQL Injection
**Dificultad:** Practitioner
**Estado:** ✅ Resuelto

## Objetivo

Explotar una vulnerabilidad de SQL Injection en la funcionalidad de verificación de stock para obtener las credenciales del usuario `administrator` y acceder a su cuenta.

## 1. Accediendo al laboratorio

Comencé accediendo al laboratorio de PortSwigger utilizando Microsoft Edge.

La aplicación contiene una funcionalidad de verificación de stock donde podía seleccionar una tienda y comprobar el stock disponible de un producto.

<img width="1600" height="831" alt="image" src="https://github.com/user-attachments/assets/06e31802-96ba-404c-a485-3f17fd97c54d" />

## 2. Probando las peticiones con F12

Al principio, no tenía Burp Suite instalado, así que intenté analizar y modificar las peticiones utilizando las herramientas de desarrollador del navegador.

Usando **F12 → Network**, pude encontrar la petición relacionada con la verificación de stock y modificar valores como el `storeId`.

Esto me permitió entender que el valor se estaba enviando al servidor a través de una petición XML.

<img width="1600" height="830" alt="image" src="https://github.com/user-attachments/assets/64b41dd0-4d5b-428b-877a-d8dcabe87b23" />

## 3. Confirmando el parámetro

Después de modificar la petición, confirmé que la aplicación estaba procesando mi entrada correctamente.

La respuesta cambiaba según el valor que enviaba, lo cual me ayudó a identificar `storeId` como un parámetro interesante para probar.

Esta fue mi primera confirmación de que podía controlar parte de la petición enviada al servidor.

<img width="1600" height="769" alt="image" src="https://github.com/user-attachments/assets/1a617330-1249-4827-9c88-59bf90ee95bd" />

## 4. Intentando completar el SQL Injection

Continué probando el parámetro y finalmente llegué al punto en el que necesitaba obtener información de la tabla `users`.

Intenté usar `UNION SELECT` y también probé usar entidades XML para evadir el filtro.

Sin embargo, trabajar directamente desde F12 se volvió difícil, y no logré que el payload completo devolviera los nombres de usuario y contraseñas como esperaba.

La aplicación también estaba detectando algunos de los intentos de SQL Injection con:

```
Attack detected
```

En este punto, decidí dejar de intentar forzar la solución mediante las herramientas del navegador y utilizar una herramienta dedicada de seguridad web en su lugar.

<img width="875" height="476" alt="image" src="https://github.com/user-attachments/assets/34affb67-bd93-4957-a5dd-51a30fee1e24" />

## 5. Instalando Burp Suite y Hackvertor

Instalé Burp Suite Community Edition y la extensión Hackvertor.

Repetí el mismo proceso que había hecho anteriormente en Edge, pero esta vez utilizando Burp Suite para interceptar y modificar las peticiones HTTP.

La función principal que utilicé fue **Repeater**, que me permitió modificar la petición y volver a enviarla sin tener que repetir las acciones en el navegador.

Esto hizo que el proceso de pruebas fuera mucho más fácil de gestionar.

<img width="1600" height="877" alt="image" src="https://github.com/user-attachments/assets/60b9592f-4d82-481f-b678-09fddfa99d9c" />

## 6. Evadiendo el filtro y obteniendo las credenciales

Usando Burp Suite, pude trabajar con la petición de stock de manera más efectiva.

Probé el enfoque de `UNION SELECT` y confirmé que la aplicación era vulnerable, pero el WAF bloqueaba el payload normal.

Dado que la petición se enviaba en XML, utilicé Hackvertor para codificar el payload de SQL Injection usando entidades XML.

La idea final fue usar `UNION SELECT` para obtener el `username` y el `password` de la tabla `users` y concatenar ambos valores en una sola columna.

La respuesta finalmente devolvió los usuarios de la base de datos, incluyendo:

```
administrator~[password]
```

Esto me dio las credenciales necesarias para acceder a la cuenta de administrador.

<img width="1597" height="888" alt="image" src="https://github.com/user-attachments/assets/273a5c33-fdb1-4d76-9bd0-21cf2eae25d9" />

## 7. Acceso como administrador

Finalmente, utilicé las credenciales obtenidas mediante el SQL Injection para iniciar sesión en la aplicación como `administrator`.

El inicio de sesión fue exitoso y el laboratorio se completó.

<img width="1600" height="684" alt="image" src="https://github.com/user-attachments/assets/15097723-fa10-4f55-90b5-8b9255db8dcd" />

## Lo que aprendí

- Cómo analizar peticiones HTTP utilizando las herramientas de desarrollador del navegador.
- Cómo Burp Suite puede interceptar y modificar peticiones HTTP.
- Cómo Repeater facilita mucho las pruebas de peticiones.
- Cómo puede existir una vulnerabilidad de SQL Injection en un parámetro XML.
- Cómo se puede usar `UNION SELECT` para obtener información de otra tabla.
- Cómo un WAF puede detectar payloads comunes de SQL Injection.
- Cómo la codificación XML puede usarse para evadir el filtro en este laboratorio.
- Cómo concatenar valores cuando solo hay una columna disponible.

Lo más importante que aprendí de este laboratorio fue lo útil que puede ser Burp Suite para las pruebas de seguridad web. Al principio intenté hacer todo con F12, pero cuando las peticiones se volvieron más complejas, fue difícil trabajar de manera eficiente.

Después de usar Burp Suite, entendí que poder interceptar, modificar, reenviar y analizar peticiones HTTP marca una gran diferencia al probar una aplicación web.

## Conclusión

Este laboratorio me permitió practicar SQL Injection de una manera más completa.

No solo aprendí sobre `UNION SELECT` y la extracción de información de otra tabla — también aprendí sobre el proceso de analizar una petición HTTP, modificarla y estudiar la respuesta.

También aprendí a usar Burp Suite y Hackvertor, herramientas que no había usado de esta forma antes.

Todavía estoy aprendiendo, pero este laboratorio me ayudó a entender mucho mejor cómo funcionan las peticiones HTTP y cómo se pueden analizar durante una evaluación de seguridad web.

## Resultado

✅ Laboratorio completado con éxito.
