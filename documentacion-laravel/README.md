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
| <img src="https://skillicons.dev/icons?i=php" width="32"/> | PHP | 8.0 o superior | `8.3.28` (Laravel 13 exige PHP 8.3 o superior) |
| <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/composer/composer-original.svg" width="32"/> | Composer | Última versión estable | `Última versión estable` |
| <img src="https://skillicons.dev/icons?i=laravel" width="32"/> | Laravel | Laravel Installer o `composer create-project` | `13.x (v13.36.0)`, creado con `composer create-project` |
| <img src="https://skillicons.dev/icons?i=apache" width="32"/> | Servidor web | Apache o Nginx | `Apache 2.4.65` |
| 🖥️ | Entorno local | XAMPP, WampServer o Laragon | `WampServer 64-bit` |
| <img src="https://skillicons.dev/icons?i=mysql" width="32"/> | Base de datos | MySQL o MariaDB funcionando | `MySQL 8.4.7` (incluido en WampServer) |
| <img src="https://skillicons.dev/icons?i=vscode" width="32"/> | Editor de código | Visual Studio Code (recomendado) | `Visual Studio Code` |
| <img src="https://skillicons.dev/icons?i=nodejs" width="32"/> | Node.js / NPM | Solo si fue necesario | `Node.js v24.21.0 / npm 11.19.0` (necesarios para compilar los assets con Vite) |
| <img src="https://skillicons.dev/icons?i=windows" width="32"/> | Sistema operativo | n/a | `Windows` |

### 1.2 Instalación de dependencias y configuración del `.env`

```bash
git clone https://github.com/1RVINNGG/LARAVEL.git
cd LARAVEL
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
DB_DATABASE=prueba
DB_USERNAME=root
DB_PASSWORD=
```

### 1.3 Flujo de comandos utilizado

Paquete de autenticación elegido: **`Laravel/UI` (con Bootstrap)**

```bash
cd C:\wamp64\www
composer create-project laravel/laravel PRUEBA
cd PRUEBA
php artisan config:clear
php artisan migrate
composer require laravel/ui
php artisan ui bootstrap --auth
npm install
npm run dev
```

Para levantar el proyecto:

```bash
composer run dev
# alternativa en dos terminales:
php artisan serve
npm run dev
```

Dependencias de autenticación:

| Gestor | Paquete | Para qué sirve |
|--------|---------|----------------|
| Composer | `laravel/ui` | Genera las rutas, controladores y vistas de login y registro |
| npm | `bootstrap`, `vite` | Estilos y compilación de recursos |

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

Flujo del registro:

```
/register (vista Blade)
   -> RegisterController (app/Http/Controllers/Auth)
   -> Modelo User (app/Models/User.php)
   -> Tabla users (MySQL, base "prueba")
```

### Comandos de migraciones

```bash
php artisan migrate
php artisan migrate:status
```

### Código implementado

**`routes/web.php`**: `Auth::routes()` registra las rutas de login, registro, logout y recuperación de contraseña; `/home` es la página interna.

```php
Route::get('/', function () {
    return view('welcome');
});

Auth::routes();

Route::get('/home', [App\Http\Controllers\HomeController::class, 'index'])->name('home');
```

**`app/Http/Controllers/Auth/RegisterController.php`**: valida los datos del formulario y crea el usuario con la contraseña encriptada.

```php
protected function validator(array $data)
{
    return Validator::make($data, [
        'name' => ['required', 'string', 'max:255'],
        'email' => ['required', 'string', 'email', 'max:255', 'unique:users'],
        'password' => ['required', 'string', 'min:8', 'confirmed'],
    ]);
}

protected function create(array $data)
{
    return User::create([
        'name' => $data['name'],
        'email' => $data['email'],
        'password' => Hash::make($data['password']),
    ]);
}
```

**`app/Http/Controllers/HomeController.php`**: el middleware `auth` bloquea el acceso a quien no haya iniciado sesión.

```php
public function __construct()
{
    $this->middleware('auth');
}

public function index()
{
    return view('home');
}
```

**`app/Models/User.php`**: conecta la aplicación con la tabla `users` y define qué campos se pueden asignar en masa.

```php
protected $fillable = [
    'name',
    'email',
    'password',
];
```

**`resources/views/auth/login.blade.php`**: vista Blade del formulario de inicio de sesión (generada por `laravel/ui`, con campos `email` y `password` protegidos con `@csrf`).

```blade
<form method="POST" action="{{ route('login') }}">
    @csrf
    <input id="email" type="email" name="email" value="{{ old('email') }}" required autofocus>
    <input id="password" type="password" name="password" required>
    <button type="submit">Login</button>
</form>
```

---

## 3. Resultado del laboratorio

### Formulario de registro

![Formulario de registro](docs/registro.png)

### Dashboard tras iniciar sesión

![Panel con sesión iniciada](docs/dashboard.png)

### Usuarios guardados en MySQL (phpMyAdmin)

![Tabla users en phpMyAdmin](docs/phpmyadmin_users.png)

### Estructura del proyecto

![Estructura del proyecto](docs/estructura.png)

Rutas disponibles (acceso en http://127.0.0.1:8000):

| Ruta | Descripción |
|------|-------------|
| `/register` | Formulario de registro |
| `/login` | Inicio de sesión |
| `/home` | Dashboard (solo con sesión iniciada) |

---

## 4. Base de datos

### 4.1 Entorno

| Elemento | Valor |
|----------|-------|
| Motor | MySQL 8.4.7 (WampServer) |
| Herramienta de administración | phpMyAdmin |
| Base de datos | `prueba` |
| Host y puerto | `127.0.0.1` : `3306` |

### 4.2 Cómo interviene Laravel

- **`.env`:** guarda las credenciales y el tipo de conexión; Laravel las lee desde `config/database.php`.
- **Migraciones:** crean las tablas con un solo comando en cualquier equipo.
- **Modelo `User`:** conecta la aplicación con la tabla `users`.

### 4.3 Comandos para crear las tablas

```sql
CREATE DATABASE prueba CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
```

```bash
php artisan migrate
```

Tablas que quedaron creadas: `users`, `password_reset_tokens`, `sessions`, `cache`, `cache_locks`, `jobs`, `job_batches`, `failed_jobs` y `migrations`.

### 4.4 Respaldo (backup)

Copia guardada en el repositorio: `database/backup/prueba.sql`

```bash
mysqldump -u root -p prueba > database/backup/prueba.sql
```

Para restaurarlo:

```bash
mysql -u root -p prueba < database/backup/prueba.sql
```

---

## 5. Dificultades y soluciones

### Dificultad 1: Laravel instalaba la versión 9 y fallaba

Al ejecutar `laravel new PRUEBA` aparecía: `laravel/laravel's latest version requires php ^8.3 which is not satisfied by your platform`. Composer intentaba instalar Laravel 9 y bloqueaba la instalación por avisos de seguridad. La terminal usaba PHP 8.0.30.

**Solución:** cambiar la versión de PHP en WampServer (PHP > Versión > 8.3.28) y forzarla en la terminal:

```powershell
$env:Path = "C:\wamp64\bin\php\php8.3.28;" + $env:Path
php -v
```

### Dificultad 2: La terminal volvía a PHP 8.0.30

Al abrir una terminal nueva aparecía: `Your Composer dependencies require a PHP version ">= 8.3.0". You are running 8.0.30.`

**Solución:** repetir el comando de PATH en cada terminal nueva, o dejarlo permanente agregando `C:\wamp64\bin\php\php8.3.28` al inicio del `Path` en las variables de entorno de Windows.

### Dificultad 3: El comando `laravel` no se reconocía

El instalador global quedaba instalado, pero Windows no encontraba el comando.

**Solución:** crear el proyecto directamente con Composer: `composer create-project laravel/laravel PRUEBA`.

### Dificultad 4: `npm` y `npx` no se reconocían

Al correr `npm install` y `composer run dev` aparecía que `npm` y `npx` no eran comandos válidos, porque Node.js no estaba instalado y luego su ruta no estaba en el PATH de la terminal.

**Solución:** instalar Node.js (LTS) desde nodejs.org con la opción *Add to PATH* y reiniciar VS Code. Si persiste: `$env:Path = "C:\Program Files\nodejs;" + $env:Path`.

### Dificultad 5: Laravel usaba SQLite en lugar de MySQL

Laravel 13 crea el proyecto con `DB_CONNECTION=sqlite` y las variables de MySQL vienen comentadas, por lo que las primeras migraciones corrieron sobre SQLite (`php artisan migrate` respondía `Nothing to migrate`) y después `db:show` indicaba `Unknown database 'laravel'`.

**Solución:** en el `.env` se cambió `DB_CONNECTION` a `mysql`, se descomentaron y completaron `DB_HOST`, `DB_PORT`, `DB_DATABASE=prueba`, `DB_USERNAME` y `DB_PASSWORD`, sin espacios sobrantes. Luego se ejecutó `php artisan config:clear` y `php artisan migrate`, y las tablas se crearon correctamente en la base `prueba`.

### Dificultad 6: Error `Specified key was too long; max key length is 1000 bytes`

Al migrar fallaba la creación de índices en `users` y `failed_jobs`, porque MySQL creaba las tablas con el motor MyISAM.

**Solución:** configurar el motor InnoDB en `config/database.php` (`'engine' => 'InnoDB'`) y agregar en `AppServiceProvider.php`:

```php
use Illuminate\Support\Facades\Schema;

public function boot(): void
{
    Schema::defaultStringLength(191);
}
```

Después se ejecutó `php artisan migrate:fresh` y las migraciones terminaron en DONE.

### Dificultad 7: phpMyAdmin mostraba 404 en `localhost/phpmyadmin`

La ruta de phpMyAdmin cambia según la versión instalada en WAMP.

**Solución:** entrar desde el menú de WAMP: *Database management > phpMyAdmin*.

---

## 6. Referencias

1. Laravel. *Authentication*. https://laravel.com/docs/authentication
2. Laravel. *Database: Migrations*. https://laravel.com/docs/migrations
3. Laravel. *laravel/ui*. https://github.com/laravel/ui
4. Bootstrap. *Documentation*. https://getbootstrap.com/docs/
5. Composer. *Documentation*. https://getcomposer.org/

---

## 7. Fecha de ejecución del laboratorio

`20 de octubre de 2026`

---

## 8. Entrega

- **Fecha de entrega:** 16 de octubre de 2026
- **Forma de entrega:** repositorio en GitHub enlazado en la plataforma académica, con hoja de presentación.

---

Este laboratorio ha sido desarrollado por el estudiante de la **Universidad Tecnológica de Panamá**:

**Nombre:** Irving S. Cruz
**Curso:** Desarrollo Web
**Instructor del Laboratorio:** Irina Fong
