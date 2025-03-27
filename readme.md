# Seguridad MongoDB

Requisitos previos antes de ininciar -> Tener OpenSSL instalado en tu sistema operativo.
## Instalación de OpenSSL
Se instalo desde -> https://slproweb.com/products/Win32OpenSSL.html

Durante la instalación, asegúrarnos de seleccionar la opción "The OpenSSL binaries (/bin) directory" para agregar OpenSSL al PATH del sistema.

Pero en mi caso no se agrego al path asi que se lo agremamos manualmente.

![Agregarlo al Path](imagenes/pathopenssh.png)


# 1. Abilitar la autenticación en MongoDB

### Agrega o modifica la siguiente configuración en la sección security:

security:    
authorization: enabled

![](imagenes/enabledauto.png)

### Guardar los cambios y reiniciar el servicio de MongoDB.

![](imagenes/netstop.png)

# 2. Crear Usuarios con Roles y Permisos Específicos

Ya tengo creado un usuario admin

![](imagenes/crearadmin.png)

### Crear un usuario con permisos de solo lectura

Movernos a la base de datos que deseamos proteger. Por ejemplo, usemos la base de datos "SeguridadDB".

![](imagenes/MoverdeDB.png)

**Creación**:
![](imagenes/creadRead.png)

**Verificar su autorización:**

![Ingresar](imagenes/ingresarRead.png)

**Prueba de lectura:** 

![Prueba de lectura](imagenes/readLectura.png)

**Prueba de escritura:**

![](imagenes/readInsertOne.png)
----------------------------------------------------------------------
### Crear un usuario con permisos de lectura y escritura

![](imagenes/ReadWrite.png)

**Ingresar**

![ingresar](imagenes/LEIngreso.png)


**Prueba de lectura:**

![Prueba Lectura](imagenes/LEfind.png)

**Prueba de Escritura**

![Prueba Escritura](imagenes/LEinsert.png)

# 3. Configurar Conexiones Seguras con SSL/TLS

### Generar Certificados SSL/TLS

![certificado](imagenes/CREARCERTIFICADO.png)

### Combina las claves en un solo archivo:
![](imagenes/combinarlos.png)

## Configurar MongoDB para usar SSL

**Abrir mongod.cfg y agrega/modifica:**

![Modificar el cfg](imagenes/Modificarcfg.png)

**Reinicia MongoDB para aplicar los cambios:**

![Reiniciar MongoDB](imagenes/Mstop.png)

**Aqui me ocurrio un error al reiniciar MongoDB:**

---

### **Error con TLS**
MongoDB no iniciaba debido a dos factores principales:

1. **Falta de cadena de confianza**: MongoDB requiere un archivo CA (`mongodb-ca.pem`) para validar el certificado público (`mongodb.pem`). Sin esta cadena de confianza, MongoDB no puede garantizar la seguridad de las conexiones y por eso no inicia.

2. **Configuración TLS**: MongoDB utiliza como protocolo estándar para conexiones seguras. Cualquier intento de usar configuraciones antiguas relacionadas con **SSL** no es compatible, ya que SSL está obsoleto.

---

### **Cómo se solucionó**
1. **Generación del archivo CA**: Usamos OpenSSL para crear un archivo CA autofirmado (`mongodb-ca.pem`):
   ```bash
   openssl req -new -x509 -days 365 -key C:\mongodb-key.pem -out C:\mongodb-ca.pem
   ```

2. **Actualización del archivo de configuración**: Se ajustó el archivo `mongod.cfg` para incluir los archivos necesarios:

  ![Error del cfg](imagenes/Error1.png)

3. **Reinicio y prueba**: Una vez configurado correctamente, MongoDB se reinició y aceptó conexiones seguras utilizando TLS.

---
Pero ahunque si puedo reiniciar el archivo de configuración ahun asi no me deja iniciar sesion
  ![Error del inicio de sesion](imagenes/erroror.png)
