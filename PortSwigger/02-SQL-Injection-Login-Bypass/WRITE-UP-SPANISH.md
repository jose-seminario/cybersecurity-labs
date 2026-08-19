# SQL Injection - Bypass de Login
**Plataforma:** PortSwigger Web Security Academy  
**Categoría:** SQL Injection  
**Dificultad:** Apprentice  
**Estado:** ✅ Resuelto
## Objetivo
Explotar una vulnerabilidad de SQL Injection en la funcionalidad de login para acceder a la cuenta `administrator` sin conocer su contraseña.
## 1. Accediendo al Login
Comencé accediendo al laboratorio y navegando a **My account**, donde estaba disponible la funcionalidad de login.
<img width="1600" height="767" alt="image" src="https://github.com/user-attachments/assets/5690fc7c-1e42-487d-a5e7-41fccb29faed" />
## 2. Probando el Login
Identifiqué el campo de nombre de usuario como el punto de inyección.
Utilicé la siguiente entrada:
`administrator' --`
Para la contraseña, ingresé un valor aleatorio.
La idea era cerrar el valor del nombre de usuario y usar `--` para comentar el resto de la consulta SQL, incluyendo la verificación de la contraseña.
<img width="1600" height="771" alt="image" src="https://github.com/user-attachments/assets/8aed5db5-31b0-4948-af1f-ba18581cf469" />
## 3. Acceso como Administrador
El login fue exitoso aunque no conocía la contraseña del administrador.
La aplicación mostró la cuenta como:
**administrator**
<img width="1600" height="770" alt="image" src="https://github.com/user-attachments/assets/bc386a7d-5f9e-45bc-be52-b92744086a18" />
## Lo que aprendí
- Cómo el SQL Injection puede modificar la lógica de una consulta de login.
- Cómo `'` puede cerrar el valor del nombre de usuario.
- Cómo `--` puede comentar el resto de la consulta SQL.
- Cómo esto puede permitir evadir una verificación de login cuando la entrada del usuario se maneja de forma insegura.
## Resultado
✅ Laboratorio completado con éxito.
