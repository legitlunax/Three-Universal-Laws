# XAMPP PHP upgrade to 8.2.12

For repeatable manual configuration by operators and future customers, use
[Manual XAMPP / PHP 8.2 setup](installation/XAMPP_8_2.md). It covers GD,
PDO_ODBC/PDO_SQLSRV, the Microsoft ODBC client, other project dependencies,
CLI/Apache checks and troubleshooting without changing the host automatically.

## Verified project continuation (2026-10-20)

`C:/xampp/php/php.exe -v` reports PHP 8.2.12. The base CLI loads PDO_SQLite but
does not load GD/PDO_ODBC; both installed matching DLLs load successfully when
selected explicitly. The project now provides `scripts/verify.ps1` to select
those extensions for verification and its child fixtures through a temporary
PHP_INI_SCAN_DIR, restoring the environment and removing the temporary INI.
No XAMPP php.ini, Apache configuration, service or machine PATH was changed.
This does not certify the Apache runtime, actual ODBC SQL transport or deployment.

Legacy PDO fixture mixed-return overrides carry standalone ReturnTypeWillChange
attributes for PHP 8.2; PHP 7.4 reads those lines as comments. Assertions and the
runner's strict error handler remain active. Product source compatibility remains
PHP 7.4 and Composer's target stays 7.4.33 until a separate compatibility decision.
STATE records actual verification results; old runtime claims in archived history
are historical evidence, not the active interpreter.

Status: the local XAMPP installation was upgraded to PHP 8.2.12. This note documents the runtime change and the XAMPP configuration that must remain consistent for this project.

Important: the environment is running PHP 8.2.12, not 8.2.2. Any documentation or troubleshooting steps must explicitly reference 8.2.12.

## Scope

This change applies to the local development environment used for ZendaMod-Next. It is an operational environment note, not a product feature change.

- Project: ZendaMod-Next
- Local web stack: XAMPP on Windows
- Active PHP version: 8.2.12
- Web server: Apache bundled with XAMPP
- Database compatibility: PHP 8.2.x runtime needs matching extensions and drivers for the project stack

## Why the upgrade was needed

The project was upgraded to PHP 8.2.12 to align the local XAMPP runtime with modern PHP compatibility expectations and to avoid running on older PHP releases that may not support current application dependencies or runtime behavior.

This is especially important for:

- modern Composer dependency compatibility
- current PHP extension support
- parity with the project's documented runtime assumptions
- less ambiguous local debugging / production parity checks

## XAMPP files that matter

The following XAMPP files are the critical configuration points for the PHP 8.2.12 setup.

### 1) Main PHP runtime configuration

File:

- `C:\xampp\php\php.ini`

This is the active PHP configuration file used by the XAMPP Apache module and CLI runtime.

Typical settings that must be reviewed and kept consistent after the upgrade:

- `date.timezone` — set to the local timezone used by the project and environment
- `memory_limit` — keep it high enough for project and dependency runtime needs
- `upload_max_filesize` — confirm it matches the app's upload requirements
- `post_max_size` — must be greater than or equal to the upload limit
- `max_execution_time` — confirm requests, tools, and installers do not fail unexpectedly
- `display_errors` — keep this enabled only for local debugging, disabled for stable runtime
- `error_reporting` — align with PHP 8.2.x behavior and the project's debugging needs
- `extension_dir` — must point to the active XAMPP PHP extensions directory
- `pdo`, `mysqli`, `openssl`, and any project-specific DB extension settings — verify they are loaded for the active runtime

If the project depends on SQL Server or other DB drivers, confirm that the matching PHP 8.2 extension DLLs are installed and enabled in `php.ini`.

### 2) Apache module configuration

File:

- `C:\xampp\apache\conf\httpd.conf`
- `C:\xampp\apache\conf\extra\httpd-xampp.conf`

These files tell Apache which PHP binary and PHP module to load and where the PHP configuration directory is.

Important points to verify:

- Apache is loading the correct PHP 8.2 module for the XAMPP build
- `PHPIniDir` points to `C:/xampp/php`
- the PHP Apache module path is valid for the bundled Apache version
- no stale references remain to an older PHP installation

Example pattern to verify:

```apache
LoadModule php_module "C:/xampp/php/php8apache2_4.dll"
PHPIniDir "C:/xampp/php"
```

If the file paths or module names do not match the installed XAMPP PHP version, Apache will not use the upgraded runtime correctly.

### 3) Windows PATH and runtime resolution

File:

- Windows environment variables / shell PATH

The command prompt and IDE should resolve to the XAMPP PHP install, not an older system-wide PHP installation.

Check:

- `where php`
- `php -v`

The expected output should display PHP 8.2.12 from the XAMPP install.

If another PHP version appears first in the PATH, Apache and local tooling may behave inconsistently.

## Required post-upgrade checks

After the XAMPP PHP upgrade, these checks should be run to confirm the environment is healthy.

### CLI verification

In Windows CMD or PowerShell:

```cmd
php -v
where php
php -m
```

Expected results:

- PHP version displays 8.2.12
- the binary path resolves to `C:\xampp\php\php.exe` or equivalent XAMPP install path
- required extensions are loaded without warnings or missing-module errors

### Apache verification

Restart Apache after any config change:

```cmd
C:\xampp\apache\bin\httpd.exe -k restart
```

Then confirm:

- the local site loads without PHP startup errors
- no `php_module` or `PHPIniDir` warnings are logged
- no missing extension fatal errors appear when the app boots

## Common compatibility issues after PHP 8.2.x upgrade

These are the usual issues to watch for with a local XAMPP upgrade:

- old `php.ini` overrides still present from earlier installations
- stale Apache module references to an older PHP DLL
- missing SQL Server / DB driver extensions for PHP 8.2
- incorrect `extension_dir` path after reinstall or reconfiguration
- conflicting system PATH values that point to another PHP version first
- deprecated PHP 7.x code that fails under PHP 8.2
- opcache or date/time settings creating inconsistent runtime behavior

## Project-specific notes

For this repository, the local XAMPP upgrade should be treated as an environment dependency change. Keep the environment documented and reproducible so that future debugging, installation work, and deployment checks use the same runtime assumptions.

This repository should not rely on hidden runtime state from an older PHP install. If the project needs a PHP extension, it should be explicitly enabled in the XAMPP PHP runtime and documented here.

## Documentation checklist

When modifying the XAMPP PHP configuration, update this document with:

- the exact PHP version in use
- the active XAMPP install path
- the PHP CLI and Apache module versions that are confirmed working
- any extension changes added for the project
- any timezone, memory, upload, or execution-time adjustments made to the runtime
- the exact Apache restart action performed after the config change

## Final status

The development environment is now operating under PHP 8.2.12 via XAMPP. All XAMPP runtime changes relevant to this setup should be treated as environment configuration and remain documented here for future maintenance and troubleshooting.
