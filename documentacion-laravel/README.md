<div align="center">

# Universidad Tecnológica de Panamá

**Facultad de Ingeniería de Sistemas Computacionales**
**Campus Víctor Levis Sasso**

### Desarrollo Web
## Laboratorio #2: Implementación del Login en Laravel

Unidad I: Modelo Vista Controlador (MVC) | Tema: Patrón MVC
Lenguaje: PHP | II Semestre 2026
Instructora: Ing. Irina Fong

<img src="https://skillicons.dev/icons?i=php,laravel,mysql,apache,bootstrap,vite,nodejs,git,vscode" alt="Tecnologías del laboratorio" />

</div>

---

## Contenido

1. [Requisitos previos](#1-requisitos-previos)
2. [Introducción](#2-introducción)
3. [Resultado del laboratorio](#3-resultado-del-laboratorio)
4. [Base de datos](#4-base-de-datos)
5. [Dificultades y soluciones](#5-dificultades-y-soluciones)
6. [Referencias](#6-referencias)
7. [Fecha de ejecución](#7-fecha-de-ejecución-del-laboratorio)
8. [Entrega](#8-entrega)

---

## 1. Requisitos previos

### 1.1 Ecosistema de desarrollo

| Icono | Tecnología | Requisito | Versión que usé |
|:-----:|------------|-----------|-----------------|
| <img src="https://skillicons.dev/icons?i=php" width="32"/> | PHP | 8.0 o superior | `[COMPLETAR: resultado de php -v]` |
| <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/composer/composer-original.svg" width="32"/> | Composer | Última versión estable | `[COMPLETAR: composer -V]` |
| <img src="https://skillicons.dev/icons?i=laravel" width="32"/> | Laravel | Laravel Installer o `composer create-project` | `[COMPLETAR: php artisan --version]` |
| <img src="https://skillicons.dev/icons?i=apache" width="32"/> | Servidor web | Apache o Nginx | `[COMPLETAR]` |
| 🖥️ | Entorno local | XAMPP, WampServer o Laragon | `[COMPLETAR: cuál usaste]` |
| <img src="https://skillicons.dev/icons?i=mysql" width="32"/> | Base de datos | MySQL o MariaDB funcionando | `[COMPLETAR]` |
| <img src="https://skillicons.dev/icons?i=vscode" width="32"/> | Editor de código | Visual Studio Code (recomendado) | `[COMPLETAR]` |
| <img src="https://skillicons.dev/icons?i=nodejs" width="32"/> | Node.js / NPM | Solo si fue necesario | `[COMPLETAR: node -v y npm -v, o dejar en blanco si no los usaste]` |
| <img src="https://skillicons.dev/icons?i=windows" width="32"/> | Sistema operativo | n/a | `[COMPLETAR]` |

### 1.2 Instalación de dependencias y configuración del `.env`

```bash
git clone [COMPLETAR: URL de tu repositorio]
cd [COMPLETAR: carpeta del proyecto]
composer install
npm install
cp .env.example .env        # en Windows: copy .env.example .env
php artisan key:generate
```

Variables de conexión que configuré en el archivo `.env`:

```env
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=[COMPLETAR: nombre de tu base de datos]
DB_USERNAME=[COMPLETAR]
DB_PASSWORD=
```

### 1.3 Flujo de comandos utilizado

Paquete de autenticación elegido: **`[COMPLETAR: Laravel/UI o Breeze]`**

Opción Laravel/UI:

```bash
composer require laravel/ui
php artisan ui bootstrap --auth
npm install && npm run dev
```

Opción Breeze:

```bash
composer require laravel/breeze --dev
php artisan breeze:install
```

> Borra la opción que no usaste y deja solo tus comandos reales, en el orden en que los ejecutaste.

Dependencias de autenticación:

| Gestor | Paquete | Para qué sirve |
|--------|---------|----------------|
| Composer | `[COMPLETAR: laravel/ui o laravel/breeze]` | Genera las rutas, controladores y vistas de login y registro |
| npm | `[COMPLETAR: bootstrap, vite, etc.]` | Estilos y compilación de recursos |

---

## 2. Introducción

### Objetivo

Construir un módulo de autenticación en Laravel aplicando el patrón MVC: registro de usuarios, inicio y cierre de sesión, con los datos guardados en MySQL y una página interna que solo se puede ver con la sesión iniciada.

### Las carpetas del patrón MVC en Laravel

| Parte | Carpeta | Qué hace |
|-------|---------|----------|
| Rutas | `routes/web.php` | Define qué URL responde cada controlador o vista |
| Controladores | `app/Http/Controllers/` | Reciben la petición, validan datos y devuelven una respuesta |
| Modelos | `app/Models/` | Representan las tablas de la base de datos (Eloquent) |
| Vistas | `resources/views/` | Plantillas Blade con el HTML que ve el usuario |
| Migraciones | `database/migrations/` | Describen las tablas en código para poder recrearlas |

### Comandos de migraciones

```bash
php artisan migrate
php artisan migrate:status
```

### Código implementado

`[COMPLETAR: pega aquí los fragmentos importantes de TU proyecto, por ejemplo]`

- `routes/web.php`
- Controlador de login / registro
- Modelo `User`
- Una vista (`login.blade.php`)

Cada fragmento va en un bloque de código con una línea que explique qué hace.

---

## 3. Resultado del laboratorio

`[COMPLETAR: sube tus capturas a docs/capturas/ y ajusta los nombres]`

![Pantalla de login](docs/capturas/login.png)

![Pantalla de registro](docs/capturas/registro.png)

![Panel con sesión iniciada](docs/capturas/home.png)

---

## 4. Base de datos

### 4.1 Entorno

| Elemento | Valor |
|----------|-------|
| Motor | `[COMPLETAR]` |
| Herramienta de administración | `[COMPLETAR: phpMyAdmin u otra]` |
| Base de datos | `[COMPLETAR]` |
| Host y puerto | `127.0.0.1` : `3306` |

### 4.2 Cómo interviene Laravel

- **`.env`:** guarda las credenciales y el tipo de conexión; Laravel las lee desde `config/database.php`.
- **Migraciones:** crean las tablas con un solo comando en cualquier equipo.
- **Modelo `User`:** conecta la aplicación con la tabla `users`.

### 4.3 Comandos para crear las tablas

```sql
CREATE DATABASE [COMPLETAR: nombre] CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
```

```bash
php artisan migrate
```

Tablas que quedaron creadas: `[COMPLETAR: revisa en phpMyAdmin]`

### 4.4 Respaldo (backup)

Copia guardada en el repositorio: `database/backup/[COMPLETAR].sql`

```bash
mysqldump -u [usuario] -p [nombre_de_la_base] > database/backup/[COMPLETAR].sql
```

Para restaurarlo:

```bash
mysql -u [usuario] -p [nombre_de_la_base] < database/backup/[COMPLETAR].sql
```

---

## 5. Dificultades y soluciones

Escribe aquí los problemas que realmente tuviste.

- **Dificultad 1:** `[COMPLETAR]`
  - **Solución:** `[COMPLETAR]`
- **Dificultad 2:** `[COMPLETAR]`
  - **Solución:** `[COMPLETAR]`
- **Dificultad 3 (si la hubo):** `[COMPLETAR]`
  - **Solución:** `[COMPLETAR]`

---

## 6. Referencias

Deja solo las fuentes que de verdad consultaste (mínimo tres):

1. Laravel. *Authentication*. https://laravel.com/docs/authentication
2. Laravel. *Database: Migrations*. https://laravel.com/docs/migrations
3. Laravel. *laravel/ui*. https://github.com/laravel/ui
4. Bootstrap. *Documentation*. https://getbootstrap.com/docs/
5. `[COMPLETAR: otra fuente, video o tutorial que hayas usado]`

---

## 7. Fecha de ejecución del laboratorio

`[COMPLETAR: día en que realizaste la práctica]`

---

## 8. Entrega

- **Fecha de entrega:** 16 de octubre de 2026
- **Forma de entrega:** repositorio en GitHub enlazado en la plataforma académica, con hoja de presentación.
- **Repositorio:** `[COMPLETAR: enlace]`

---

Este laboratorio ha sido desarrollado por el estudiante de la **Universidad Tecnológica de Panamá**:

**Nombre:** Irving S. Cruz
**Curso:** Desarrollo Web
**Instructor del Laboratorio:** Irina Fong
