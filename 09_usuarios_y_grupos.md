# Usuarios y grupos

**Christian Robles**

## Introducción

En Linux, la administración de **usuarios y grupos** es fundamental para la
seguridad y organización del sistema.

- **Usuarios** → representan cuentas individuales que pueden iniciar sesión
  y ejecutar procesos.  
- **Grupos** → permiten organizar usuarios y asignar permisos colectivos.  

Cada archivo y proceso está asociado a un usuario y a un grupo, lo que
determina quién puede acceder y qué acciones puede realizar.

---

## Comandos principales

- `whoami` → muestra el usuario actual.  
- `id` → muestra información del usuario y sus grupos.  
- `adduser` / `useradd` → crea un nuevo usuario.  
- `passwd` → cambia la contraseña de un usuario.  
- `groupadd` → crea un nuevo grupo.  
- `usermod` → modifica la configuración de un usuario (ejemplo: añadirlo a un
  grupo).  
- `deluser` / `userdel` → elimina un usuario.  
- `groups` → muestra los grupos a los que pertenece un usuario.  

---

## Ejemplos prácticos

### 1. Ver usuario actual

```bash
whoami
```

**Resultado esperado:**

```
usuario
```

---

### 2. Ver información de usuario y grupos

```bash
id
```

**Resultado esperado:**

```
uid=1000(usuario) gid=1000(usuario) groups=1000(usuario),27(sudo),1001(staff)
```

---

### 3. Crear un nuevo usuario

```bash
sudo adduser juan
```

**Resultado esperado:**

```
Adding user `juan' ...
Adding new group `juan' (1002) ...
Adding new user `juan' (1002) with group `juan' ...
Enter new UNIX password:
```

Se crea el usuario `juan` con su grupo principal.

---

### 4. Cambiar contraseña

```bash
sudo passwd juan
```

**Resultado esperado:**

```
Enter new UNIX password:
Retype new UNIX password:
passwd: password updated successfully
```

---

### 5. Crear un grupo

```bash
sudo groupadd proyecto
```

**Resultado esperado:**

El grupo `proyecto` se crea en el sistema.

---

### 6. Añadir un usuario a un grupo

```bash
sudo usermod -aG proyecto juan
groups juan
```

**Resultado esperado:**

```
juan : juan proyecto
```

El usuario `juan` ahora pertenece al grupo `proyecto`.

---

### 7. Eliminar un usuario

```bash
sudo deluser juan
```

**Resultado esperado:**

```
Removing user `juan' ...
Done.
```

---

## Reto de práctica

1. Crea un usuario `ana`.  
2. Crea un grupo `equipo`.  
3. Añade `ana` al grupo `equipo`.  
4. Verifica con `id ana` que pertenece al grupo.  
5. Elimina el usuario `ana`.  

**Resultado esperado:**

```
uid=1002(ana) gid=1002(ana) groups=1002(ana),1003(equipo)
Removing user `ana' ...
Done.
```

---

### 8. Opciones avanzadas de `useradd` y `usermod`

- **`useradd -m -s /bin/bash juan`** → crea usuario con directorio home y shell
  específico.  
- **`usermod -d /nuevo_home juan`** → cambia el directorio home de un usuario.  
- **`usermod -L juan`** → bloquea la cuenta de un usuario.  
- **`usermod -U juan`** → desbloquea la cuenta.  

> **Nota:** estas opciones permiten configurar usuarios de manera más precisa.

---

### 9. Opciones avanzadas de grupos

- **`groupdel grupo`** → elimina un grupo.  
- **`gpasswd -a usuario grupo`** → añade un usuario a un grupo.  
- **`gpasswd -d usuario grupo`** → elimina un usuario de un grupo.  

> **Nota:** `gpasswd` se usa mucho para gestionar grupos sin modificar
  directamente archivos del sistema.

---

### 10. Archivos de configuración clave

- **`/etc/passwd`** → lista usuarios y sus configuraciones básicas.  
- **`/etc/group`** → lista grupos y sus miembros.  
- **`/etc/shadow`** → almacena contraseñas encriptadas.  


> **Nota:** los usuarios avanzados suelen revisar estos archivos directamente
  para diagnóstico y administración avanzada.

---

### 11. Recomendaciones

- Usar usuarios y grupos para aplicar el **principio de menor privilegio**.  
- Evitar usar siempre `root`; crear usuarios específicos para tareas.  
- Configurar grupos para proyectos, permitiendo colaboración sin comprometer
  seguridad.  
- Conocer y revisar archivos de configuración para entender cómo el sistema
  gestiona cuentas.  

---

### 12. Ejemplo integrado de nivel avanzado

```bash
sudo useradd -m -s /bin/bash carlos
sudo passwd carlos
sudo groupadd desarrollo
sudo gpasswd -a carlos desarrollo
id carlos
```

**Resultado esperado:**

```
uid=1004(carlos) gid=1004(carlos) groups=1004(carlos),1005(desarrollo)
```

Interpretación:

- Se crea el usuario `carlos` con su directorio home y shell Bash.  
- Se le asigna una contraseña.  
- Se crea el grupo `desarrollo`.  
- Se añade `carlos` al grupo `desarrollo`.  

---

## Conclusión

Para tener un un buen nivel en usuarios y grupos es necesario dominar:

- Opciones avanzadas de `useradd` y `usermod`.  
- Gestión de grupos con `gpasswd`.  
- Archivos de configuración clave (`/etc/passwd`, `/etc/group`, `/etc/shadow`).  
- Filosofía de seguridad: aplicar menor privilegio y usar grupos para
  colaboración.

