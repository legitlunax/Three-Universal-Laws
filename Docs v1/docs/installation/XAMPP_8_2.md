# Manual XAMPP / PHP 8.2 setup for ZendaMod Next

Reviewed against the local PHP runtime and linked vendor documentation on 2026-10-20.

For operators and future customers using Windows. Examples assume XAMPP is
installed at `C:/xampp`; substitute your own installation directory.
These instructions describe environment preparation. Consult [STATE](../../STATE.md)
for implemented website features and [installation lifecycle](../INSTALLATION_LIFECYCLE.md)
for the planned installer. Installing extensions does not enable unfinished features.

## 1. Identify the PHP installation before editing

Open PowerShell and run:

```powershell
& 'C:/xampp/php/php.exe' -v
& 'C:/xampp/php/php.exe' --ini
& 'C:/xampp/php/php.exe' -m
& 'C:/xampp/php/php.exe' -i | Select-String 'Thread Safety|Architecture|Compiler|extension_dir'
```

Record the PHP version, x64/x86 architecture, thread-safety mode and loaded INI
path. This project's observed CLI is **PHP 8.2.12, x64, thread safe (ZTS), VS2019**;
customers must inspect their own build. PHP DLLs must match the runtime build,
including PHP major/minor, architecture and TS/NTS mode.
[PHP's Windows extension guide](https://www.php.net/manual/en/install.pecl.windows.php)
explains those matching rules.

The expected INI here is `C:/xampp/php/php.ini`. Back it up outside the webroot
before editing. In XAMPP Control Panel, Apache -> Config -> PHP (php.ini) opens
the Apache-side configuration; compare its path with the CLI result.

## 2. Enable GD, PDO_ODBC and the project baseline

Open the identified `php.ini` in a text editor. Find the existing extension lines
and remove their leading semicolon. Keep exactly one active line per extension;
do not paste duplicates. Confirm that `extension_dir` points to the matching
installation's extension directory, for example:

```ini
extension_dir="C:/xampp/php/ext"

extension=gd
extension=pdo_odbc
extension=pdo_sqlite
extension=curl
extension=openssl
```

The corresponding files normally include `php_gd.dll`, `php_pdo_odbc.dll`,
`php_pdo_sqlite.dll`, `php_curl.dll` and `php_openssl.dll` in that directory.
If a file is missing, obtain a matching complete PHP/XAMPP distribution or the
official extension package; uncommenting a line cannot install its DLL.

GD uses `php_gd.dll` on PHP 8; old instructions mentioning `php_gd2.dll` target
older PHP. See [PHP GD installation](https://www.php.net/manual/en/image.installation.php).
PDO is normally built into Windows PHP; enable the needed database drivers
rather than adding a nonexistent `extension=pdo` line.
See [PHP PDO installation](https://www.php.net/manual/en/pdo.installation.php).

| Component | Required for this project / when to enable |
| --- | --- |
| `gd` | Image raster decoding and PNG/JPEG/WebP template tests; check codec support below |
| `PDO` + `pdo_sqlite` | Offline verification and SQLite development fixtures; SQLite is not production website configuration storage |
| `pdo_odbc` | Explicit ODBC website transport and full offline ODBC-profile fixtures; actual SQL also needs the Windows ODBC driver |
| `pdo_sqlsrv` | Website SQL transport when using the default sqlsrv driver; separate Microsoft extension, described in section 4 |
| `curl` + `openssl` | Drupal HTTP/HTTPS reads and TLS support |
| `dom` / `libxml` | Drupal processed-HTML parsing; usually already included in this Windows build |
| `iconv` | Website Unicode/UTF-16 wire conversion; verify it is present |
| `json`, `tokenizer` | Declared Composer/tooling requirements; usually built in |
| `session`, `hash`, `filter`, `SPL` | Website sessions, fingerprints, validation and core PHP facilities; usually built in |
| `fileinfo`, `mbstring` | Recommended for the broader hosting/plugin ecosystem; not new mandatory core requirements introduced by this guide |
| `intl`, `zip`, `mysqli`, `pdo_mysql` | Optional for separately installed products/features that actually require them; enabling them does not replace website MSSQL |

If already present in `php -m`, leave built-in components alone. For recommended
shared extensions, uncomment `extension=fileinfo` / `extension=mbstring` only if
you need them and the matching DLL exists. Do not enable every available extension.
The isolated modern Drupal installation has its own runtime requirements; do not
load Drupal into Zenda's PHP process. See [Drupal integration](../DRUPAL_NEWS.md).

## 3. Apply and verify the changes

Save the INI. In XAMPP Control Panel, stop Apache and start it again so web
requests read the new configuration. A new CLI invocation reads its INI afresh.
If changes appear ineffective, compare the actual loaded INI in both contexts.
[Apache Friends documents the INI/restart procedure](https://www.apachefriends.org/faq_windows.html).

```powershell
& 'C:/xampp/php/php.exe' --ri gd
& 'C:/xampp/php/php.exe' --ri PDO_ODBC
& 'C:/xampp/php/php.exe' --ri PDO
& 'C:/xampp/php/php.exe' -m
```

Expected: GD enabled, PNG/JPEG/WebP supported, PDO_ODBC present, and `odbc` plus
`sqlite` in PDO's available drivers. `sqlsrv` appears only after section 4's
separate PDO_SQLSRV extension is loaded.

For a compact CLI check, paste this whole PowerShell block:

```powershell
@'
<?php
$required = ['json','tokenizer','PDO','pdo_sqlite','gd','curl','openssl','dom','iconv','session','hash','filter'];
$missing = array_values(array_filter($required, static function ($name) { return !extension_loaded($name); }));
echo json_encode(['php' => PHP_VERSION, 'missing_baseline' => $missing,
    'pdo_drivers' => PDO::getAvailableDrivers(),
    'gd_formats' => extension_loaded('gd') ? gd_info() : null], JSON_PRETTY_PRINT), PHP_EOL;
'@ | & 'C:/xampp/php/php.exe'
```

This check does not connect to SQL Server or change website data. Check
PDO_ODBC separately as above when selecting ODBC.

To verify **Apache**, use XAMPP's existing localhost PHP information screen if
available. If it is absent, temporarily place this minimal diagnostic in a
separate localhost-only diagnostic directory, outside this project's source:

```php
<?php
if (!in_array($_SERVER['REMOTE_ADDR'] ?? '', ['127.0.0.1','::1'], true)) {
    http_response_code(403); exit;
}
header('Content-Type: application/json');
echo json_encode(['php' => PHP_VERSION, 'sapi' => PHP_SAPI,
    'ini' => php_ini_loaded_file(), 'gd' => extension_loaded('gd'),
    'pdo_odbc' => extension_loaded('pdo_odbc'),
    'pdo_sqlsrv' => extension_loaded('pdo_sqlsrv')]);
```

Open it only through `localhost`, confirm the INI/version/extensions and remove
the diagnostic afterward. Do not add a permanent phpinfo endpoint to the CMS.

## 4. Prepare Microsoft SQL Server connectivity

There are two different installation layers:

| Layer | Role |
| --- | --- |
| PHP `pdo_odbc` or `pdo_sqlsrv` | Gives PHP the chosen PDO transport |
| Microsoft ODBC Driver 18 for SQL Server | Windows database client used for SQL Server connectivity |

Install the matching Microsoft ODBC Driver from the
[official Microsoft download](https://learn.microsoft.com/en-us/sql/connect/odbc/download-odbc-driver-for-sql-server).
Install any Microsoft Visual C++ runtime prerequisites requested by those
packages from Microsoft's [supported Visual C++ Redistributable page](https://learn.microsoft.com/en-us/cpp/windows/latest-supported-vc-redist).
Match the required architecture; do not download individual missing runtime DLLs
from unrelated sites.
For a 64-bit PHP process, verify that the 64-bit driver is available:

```powershell
Get-OdbcDriver -Name 'ODBC Driver 18 for SQL Server' | Select-Object Name,Platform
```

`extension=odbc` is the separate procedural PHP ODBC interface; it does not
substitute for `pdo_odbc`. Likewise, `sqlsrv` and `pdo_sqlsrv` are different
PHP extensions. Zenda's PDO connection factory needs `pdo_sqlsrv` for its sqlsrv
selection, not just the procedural `sqlsrv` extension.

For **PDO_SQLSRV**, download a Microsoft PHP driver package compatible with
your PHP version. Match the current
[Microsoft compatibility matrix](https://learn.microsoft.com/en-us/sql/connect/php/system-requirements-for-the-php-sql-driver).
At this guide's review, the 5.12 table includes PHP 8.2; the 5.13 table starts
with PHP 8.3. Do not assume the newest package includes PHP 8.2 binaries.
For this observed PHP 8.2 x64 TS build, the corresponding example filename is
`php_pdo_sqlsrv_82_ts.dll` from the **64-bit** package; other builds need their own match.

Place the matching DLL in the configured extension directory and add its actual
filename once to `php.ini`, for example:

```ini
extension=php_pdo_sqlsrv_82_ts.dll
```

Restart Apache and confirm `php --ri pdo_sqlsrv` plus PDO driver `sqlsrv`.
Follow [Microsoft's loading instructions](https://learn.microsoft.com/en-us/sql/connect/php/loading-the-php-sql-driver)
for the driver dependencies. Do not rename a DLL for PHP 8.3 or 7.4 to make it
appear compatible with PHP 8.2.

In this repository, `ZENDA_SETTINGS_DRIVER` defaults to `sqlsrv`; `odbc` must be
selected explicitly through private deployment configuration. Loading both PHP
extensions does not select a driver or create an automatic fallback. Driver
installation alone proves neither database permissions nor whole-site MSSQL
compatibility. Existing verified stores and remaining limits are recorded in
[DATABASE](../../DATABASE.md) and [STATE](../../STATE.md).

XAMPP's bundled MariaDB is not the required website MSSQL database. Obtain the
SQL Server target separately. Keep website credentials private, use a restricted
runtime principal and validated TLS certificates/hostnames; do not disable
certificate validation to resolve a production connection failure. Do not run
migrations or connection probes against an existing customer's MU/account data.

## 5. Website paths and local settings

This project's only supported document root is `ZendaMod-Next/public/`; the parent
source root intentionally denies HTTP access. For local Apache preview, adapt a
VirtualHost such as this to your actual directory. Back up the Apache files,
place the block in `C:/xampp/apache/conf/extra/httpd-vhosts.conf`, and ensure the
existing `Include conf/extra/httpd-vhosts.conf` directive in `httpd.conf` is active
exactly once:

```apache
Listen 127.0.0.1:8092
<VirtualHost 127.0.0.1:8092>
    ServerName localhost
    DocumentRoot "C:/xampp/htdocs/ZendaWeb/ZendaMod-Next/public"
    <Directory "C:/xampp/htdocs/ZendaWeb/ZendaMod-Next/public">
        AllowOverride All
        Require local
    </Directory>
</VirtualHost>
```

Use an unoccupied port, add this Listen directive only once, and open
`http://127.0.0.1:8092`. Enable Apache's existing `rewrite_module` if needed for
the supplied public `.htaccess` rules by uncommenting its existing
`LoadModule rewrite_module modules/mod_rewrite.so` line in `httpd.conf`.
Run `C:/xampp/apache/bin/httpd.exe -t`
before restarting. See [Apache VirtualHost examples](https://httpd.apache.org/docs/2.4/vhosts/examples.html)
and [AllowOverride](https://httpd.apache.org/docs/2.4/mod/core.html#allowoverride).
The application currently validates Host as `localhost`, `127.0.0.1` or `[::1]`;
custom customer hostnames/public access need a separately reviewed deployment change.

Alternatively, run the supported loopback preview from the project root:

```powershell
& 'C:/xampp/php/php.exe' -S 127.0.0.1:8091 -t public public/router.php
```

Keep sessions, uploads, logs and sandbox state outside the webroot with access
limited to the actual service account. Do not use broad Everyone/full-control
permissions to fix a write error. See [sandbox setup](../SANDBOX.md).
For the current image policy's maximum of 2 MiB, these are example environment
values to review manually, not mandatory overrides for every customer:

```ini
default_charset="UTF-8"
date.timezone="UTC"
file_uploads=On
upload_max_filesize=2M
post_max_size=4M
memory_limit=256M
max_execution_time=30
log_errors=On
display_errors=Off
display_startup_errors=Off
```

Set `post_max_size` above `upload_max_filesize` to leave room for the multipart
form. PHP memory/time limits must also fit the selected hosting and image sizes.
See [PHP upload/core settings](https://www.php.net/manual/en/ini.core.php#ini.post-max-size)
and [error logging settings](https://www.php.net/manual/en/errorfunc.configuration.php#ini.display-errors).
Select a writable private log location; inspect logs when public error display
is off. If setting `upload_tmp_dir` or `session.save_path`, create those private
directories first and grant access only to the service account that needs them.

These PHP/environment settings do not grant upload permission, expand the
application allowlist or replace authoritative website configuration in MSSQL.
For the full developer suite, CLI policy must permit `exec`/`proc_open` for lint
and isolated HTTP/worker fixtures; that does not require exposing shell tools to
web users or loosening a customer's web PHP policy.

## 6. Run project verification

After manual extension activation:

```powershell
& 'C:/xampp/php/php.exe' scripts/verify.php --level=fast
& 'C:/xampp/php/php.exe' scripts/verify.php --level=complete
```

For developers whose CLI still lacks the installed GD/PDO_ODBC extensions,
`scripts/verify.ps1 -Level complete` temporarily loads them only for verification
and child fixtures, restores the environment and removes its temporary INI.
It is not a substitute for configuring Apache for customers. Ordinary verification
uses synthetic offline fixtures; actual SQL lab tests are separate and opt-in.

## Troubleshooting and rollback

| Symptom | Check / action |
| --- | --- |
| `imagecreatetruecolor` undefined | Confirm GD in the failing process's actual INI, not just another terminal |
| Missing PNG/JPEG/WebP support | Inspect `php --ri gd`; use a matching build with these codecs |
| `could not find driver` | Inspect PDO's driver list; check the configured transport's PHP extension |
| ODBC driver not found / IM002 | Verify Windows ODBC Driver 18, its architecture and the selected driver name |
| `Unable to load dynamic library` | Verify extension_dir, actual DLL, matching PHP/TS/x64 build and dependent Microsoft runtime libraries |
| `Module ... is already loaded` | Remove duplicate active entries; also inspect additional INI files reported by `php --ini` |
| CLI works but Apache fails | Compare loaded INI, PHP build, extension directory and service-account environment; restart Apache |
| TLS certificate/hostname error | Correct certificate trust and hostname; keep strict application TLS enabled |
| Apache will not start after editing | Read `C:/xampp/apache/logs/error.log`, revert the added line or restore your INI backup, test config and restart |
| Script execution blocked by local policy | Follow your organization's script policy or use configured PHP directly; no global policy bypass is required |

Keep a customer setup record containing versions, build/architecture, enabled
extensions, selected transport and test outcomes. Exclude credentials, complete
phpinfo dumps and player data. Enabling modules is a dependency check, not
approval to activate native writes, commerce or unpublished templates.

XAMPP's default distribution is intended for development, as
[Apache Friends explains](https://www.apachefriends.org/faq_windows.html).
This is a customer preparation guide, not a production deployment certification.
