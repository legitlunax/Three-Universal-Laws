# Principios Evolutivos, Reglas de No-Regresión y Arquitectura Multi-Juego

## 0.1 Separación obligatoria de las APIs del ecosistema

`MainAPI` es la API principal de registro de plugins. `GameTypeAPI` registra motores/tipos de juego de forma independiente. `DatabaseTypeAPI` registra transportes **externos de datos de juego**, sin reemplazar la persistencia MSSQL del propio CMS. `SchemaAdapterAPI` registra adaptadores verificados para cada esquema/emulador (por ejemplo MUEMU, Louis). Cada proveedor debe ser una carpeta removible en `/plugins`; un módulo de funcionalidades consume contratos normalizados y nunca importa clases privadas de otro plugin ni accede a SQL nativo de manera directa. La eliminación física invalida únicamente las capacidades y los perfiles dependientes. Consultar `API_ARCHITECTURE.md` y los cuatro manuales de API. Se trata de una especificación futura, no de código implementado.


Este documento establece las leyes arquitectónicas y el flujo de trabajo obligatorio para evolucionar la plataforma (**ZendaMod-Next**) desde su base actual (enfocada en MU Online) hacia un motor CMS y constructor visual multi-juego (al estilo del clásico *Enjin.com* para **Minecraft, Counter-Strike, Rust, Metin2, Lineage 2, MU Online**, entre otros), garantizando que **ningún cambio pequeño o grande rompa el código existente**.

## 0.0 Ley de ladrillos y artes: los bloques se perfeccionan, las artes son maleables

El usuario fijó esta distinción el 2026-10-20 y la perfeccionó ese mismo día; gobierna
cómo se construye el proyecto: **los ladrillos son bloques de construcción (derivadas
API) y las artes son los productos que se arman con ellos**. No es un apéndice
editorial: es un invariante estructural de las tres leyes.

| Ley | Rol canónico | Invariante arquitectónico |
| --- | --- | --- |
| **Singularity** | **El Ladrillo** | Una responsabilidad verificada, entradas cerradas, ejecución acotada, sin carga ejecutable. Perfeccionado una vez por versión exacta (`id@x.y.z`), ciego a las artes que lo consumen. |
| **Order** | **El Registro y la Compuerta** | Fijación exacta de versiones, esquemas estrictos y RBAC por recurso. Separa la habilitación opcional de la autoridad; manda las brechas a cuarentena en vez de ensanchar un ladrillo en uso. |
| **Chaos** | **El Compositor de Artes** | Da forma y reconfigura artes maleables mediante planes DAG sobre ladrillos existentes. Gobierna la regla de las dos artes emitiendo `capability_gap` con `consuming_crafts`. |

- **Ladrillo = derivada API privada**: una operación Singularity enfocada, gobernada
  por Order y proponible por Chaos, con nombre y versión exacta (`id@x.y.z`),
  entradas cerradas, límites explícitos y ninguna carga ejecutable. Una vez publicada
  una versión, su comportamiento observable es inmutable: si debe cambiar, se publica
  una versión nueva con migración explícita de consumidores.
- **Arte = webshop, AdminCP, live template, rankings, markets, support**: sistemas
  que se arman con ladrillos y cuya forma cambia. Una arte nunca es perfecta ni
  estable; el diseño, las preferencias y la configuración cambian, y esa diferencia
  es intencional.
- **Regla de las dos artes**: la lógica específica permanece privada dentro de una
  arte hasta que una segunda arte necesita la misma primitiva, o hasta que el usuario
  lo exige. Entonces Chaos emite `capability_gap` declarando `consuming_crafts`, el
  contrato cerrado, el dueño sugerido y las pruebas; está prohibido duplicar lógica
  privada entre artes.
- **La configuración solo habilita; jamás autoriza**: en el algoritmo de invocación
  de Order, la verificación de habilitación es un paso propio y anterior a la
  comprobación de permisos. Una preferencia puede encender o apagar una capacidad ya
  construida; nunca concede autoridad, permisos, acceso a MSSQL ni capacidad nativa
  de juego o pago.
- **Terminología**: *ladrillo* y *arte* son los términos canónicos; *taller* se reserva
  para el espacio de autoría y replanificación, nunca para el producto entregado.
- **Adaptabilidad y potencial primero**: el almacén de datos admite una alta diversidad
  de funciones sin que todas estén implementadas hoy; los esquemas se declaran de
  forma extensible, y el ladrillo se diseña para no tener que reescribirse.

Texto canónico por ley: [Singularity §13](docs/api/SINGULARITY.md),
[Order §13](docs/api/ORDER.md), [Chaos §11](docs/api/CHAOS.md). Inventario y ciclo de
vida de un ladrillo: [docs/api/BUILDING_BLOCKS.md](docs/api/BUILDING_BLOCKS.md).
## 0. Principio superior: arquitectura de plugins por carpeta

La meta final **no se cumple** con módulos internos desacoplados ni solo con
bloques GrapesJS. Cada función opcional del CMS será un plugin instalable por
**carpeta completa** en `/plugins/<identificador>/`. Si la carpeta de un plugin
aprobado y compatible aparece, el CMS la descubre e integra mediante contratos;
si desaparece, todas sus rutas, widgets, eventos y menús desaparecen en la
**siguiente petición**, sin romper núcleo ni plugins independientes. Los datos
históricos y configuraciones permanecen en MSSQL. Volver a insertar la misma
versión aprobada puede restaurar la funcionalidad. Nuevos ejecutables se
aprueban antes de activarlos. Dependencias obligatorias ausentes bloquean solo
sus consumidores; las opcionales se degradan sin fatal.

`PLUGIN_SYSTEM.md` prevalece como especificación de este objetivo y distingue
claramente los plugins del constructor visual GrapesJS. **Hoy es una dirección de
arquitectura por implementar, no funcionalidad existente**. La actual separación
`src/Modules/*` / `packages/premium/*` debe migrarse paso a paso sin romper V1-V5,
ni alterar juego/mercado/pagos ni borrar datos.

---

## 1. Filosofía Central: Evolución Aditiva (Open-Closed & Fail-Closed)

El principio rector del sistema es **Abierto a la Extensión, Cerrado a la Modificación Destructiva**:

1. **Inmutabilidad de Contratos Congelados (`V1` a `V{N}`)**:
   - Ninguna interfaz publicada en `src/Contracts/V*` ni sus catálogos/hashes en `contracts/v*/baseline.json` pueden alterarse silenciosamente.
   - **Cambio pequeño**: Si es una mejora interna dentro de un módulo (ej. optimizar una consulta o ajustar una vista privada) que no altera la firma ni el comportamiento prometido por el contrato, se realiza únicamente dentro de su adaptador (`Infrastructure` o `Application`) manteniendo intactos los tests existentes.
   - **Cambio grande**: Si una funcionalidad requiere nuevos parámetros, nuevas capacidades o cambiar el modelo de datos, **nunca modifiques el contrato congelado**. Crea un nuevo puerto versionado (`V{N+1}`) o un puerto específico adicional, mantén el anterior funcionando para los consumidores existentes y migra los consumidores de forma explícita y gradual.
2. **Prohibición de Actualizar Hashes para Ocultar Rupturas**:
   - Está estrictamente prohibido regenerar `contracts/v*/baseline.json` como un "arreglo rápido" ante un fallo de verificación. Un fallo de hash significa que se rompió un contrato público.
3. **Fallo Seguro (*Fail-Closed*) por Defecto**:
   - Cualquier capacidad no verificada, esquema de juego desconocido, paquete premium ausente, licencia inactiva o estado corrupto en base de datos debe denegar la operación de forma limpia y privada, sin recurrir a *fallbacks* silenciosos hacia archivos locales en modo base de datos ni exponer trazas de SQL/credenciales.

---

## 2. Arquitectura Agnóstica Multi-Juego (De MU Online a Múltiples Juegos)

Para que el sistema sirva en el futuro para **Minecraft, Counter-Strike, Rust, Metin2, Lineage 2 y MU Online** sin convertir el código en un bloque monolítico lleno de condicionales (`if ($game === 'minecraft')`), toda evolución debe respetar tres capas de abstracción:

### A. El Core Web es 100% Agnóstico del Juego
Los módulos del núcleo abierto (`Admin`, `Content`, `Community`, `Templates` con GrapesJS, y la identidad web en `Identity`) **no deben conocer detalles binarios ni tablas nativas de ningún juego**.
- El constructor visual **GrapesJS**, el sistema de noticias, los tickets de soporte, las cuentas del sitio web, la auditoría y los permisos operan exclusivamente sobre entidades del sitio web (**Web Data**) y consumen datos del juego únicamente a través de **Proyecciones Normalizadas de Lectura** (`render-context` y contratos públicos).

### B. Abstracción Universal de Conceptos de Juego (`GameDriver` / `GameProfile`)
Cada juego tiene mecánicas distintas, pero a nivel de un CMS web todos comparten conceptos universales que se adaptan mediante **Puertos y Adaptadores**:

| Concepto Universal en el CMS | MU Online (MSSQL) | Metin2 (MySQL) / Lineage 2 (MySQL/MSSQL) | Minecraft (MySQL / RCON / API) | Counter-Strike / Rust (Steam API / RCON / SQL) |
| :--- | :--- | :--- | :--- | :--- |
| **Identidad Vinculada (`AccountReader`)** | `MEMB_INFO` (`memb___id`) | `account.account` / `accounts` | UUID de Mojang / Floodgate / AuthMe | `SteamID64` (`7656119...`) |
| **Avatar / Personaje (`CharacterReader`)** | `Character` (Nivel, Resets, Clase) | `player.player` / `characters` (Nivel, Reino/Raza, PvP) | Perfil de Jugador (Estadísticas, Rango LuckPerms) | Perfil de Servidor (K/D, ELO, Horas, Clan) |
| **Clasificaciones (`RankingReader`)** | Resets, Grand Resets, Gremios, Gens | Nivel, Yang, Gremios, Reinos | Kills, Economía, Tiempo jugado, Factions/Towny | Top Fraggers, Faceit/Premier ELO, Wipes, Clanes |
| **Economía / Monedas (`CurrencyReader`)** | Zen, WCoinC/P, GoblinPoints, Ruud | Yang, Won, Dragon Coins | Vault Economy, Coins, Tokens | Puntos VIP, Skins/Créditos de Tienda |
| **Contenedores / Ítems (`Inventory/Warehouse`)** | Hex Binario (10B, 16B, 20B, 32B) | Tablas relacionales `item` (`vnum`, `sockets`, `attr`) | NBT Data / Comandos de entrega RCON | Comandos RCON / Plugins (Oxide/uMod, CounterStrikeSharp) |
| **Estado del Servidor (`ServerStatusReader`)** | `MEMB_STAT` (`ConnectStat`) + Puerto TCP | Conteo en tabla/puerto de canales (CH1..CH4) | Server List Ping Protocol / Query / RCON | Source A2S_INFO Query / BattleMetrics / RCON |
| **Mutaciones (`GameMutationGateway`)** | Transacción MSSQL + Check Offline + Lock | Transacción SQL con verificación de desconexión | Cola de comandos RCON / Plugin Webhook idempotente | Ejecución RCON / API con clave de idempotencia |

### C. Regla de Encapsulamiento por Juego
1. **Nunca mezcles lógica de un juego en otro**: El decodificador binario de ítems de MU Online (`MuItem`, `ItemBinaryDecoder`), el cálculo de *Resets* o los emblemas 8x8 pertenecen exclusivamente al adaptador/perfil de MU Online.
2. **Desacoplamiento de Topología de Datos**: Mantén la separación lógica en 3 partes (**Web Data**, **Account Data**, **Game Data**).
   - En juegos basados en bases de datos directas (MU, Metin2, Lineage 2), `Game Data` y `Account Data` usan adaptadores SQL (`PDO`).
   - En juegos basados en APIs o consola remota (Minecraft, Rust, Counter-Strike), los mismos puertos (`RankingReader`, `GameMutationGateway`, `ServerStatusReader`) se implementarán mediante adaptadores RCON, Webhooks firmados o bases de datos de plugins sin tocar una sola línea del Core Web ni de las plantillas de GrapesJS.

---

## 3. Aislamiento Físico: Core Libre vs. Paquetes Premium

1. **Distribución futura por carpetas independientes**:
   - El núcleo reducido contiene el arranque, autorización/AdminCP de base,
     contratos, registro de plugins y alojamiento del editor visual gratuito.
   - Todas las funciones opcionales, gratuitas o premium, tendrán **su propia
     carpeta** en `/plugins/<identificador>/`. Ejemplos:
     `/plugins/zenda.news/` y `/plugins/zenda.webshop/`.
   - `src/Modules/*` y `packages/premium/*` son ubicaciones actuales de
     transición. No se borran ni trasladan en masa sin pruebas de equivalencia.
2. **Dependencias y ausencia física**:
   - El núcleo no importa código interno de plugins opcionales. Un plugin tampoco
     importa las clases internas ni las tablas privadas de otro. Usa contratos
     estables con versiones compatibles y dependencia declarada.
   - En la siguiente petición, si una carpeta desaparece, su servicio, rutas,
     menús, widgets y eventos quedan inactivos. Dependientes obligatorios quedan
     bloqueados sin errores fatales; los opcionales deben tolerar la ausencia.
   - `PluginManager` valida carpeta, confianza, compatibilidad, licencia y
     estado autorizado antes de activar el paquete. La ausencia nunca elimina
     datos MSSQL ni transforma un borrado accidental en desinstalación definitiva.

---

## 4. Protocolo Obligatorio para Realizar Cambios (Pequeños o Grandes)

Antes de escribir o modificar cualquier línea de código, tanto desarrolladores humanos como agentes de IA deben ejecutar este ciclo de 5 pasos:

### Paso 1: Clasificación del Cambio
- **Nivel 1 (Cambio Pequeño / Local)**: Corrección de bug, mejora de UI/CSS, refactorización privada o ajuste en un adaptador sin cambiar firmas públicas.
- **Nivel 2 (Cambio Mediano / Extensión Interna)**: Nuevo método en un servicio o puerto interno de un módulo (`Application`/`Domain`), nueva tabla propiedad del sitio web (DDL aditivo numerado) o nueva ruta HTTP dentro de un módulo existente.
- **Nivel 3 (Cambio Grande / Evolutivo)**: Nuevo módulo, extracción a paquete premium, soporte para un nuevo juego/emulador o cambio que afecta cómo se comunican dos módulos (requiere un nuevo contrato `V{N+1}` y registro en `DECISIONS.md`).

### Paso 2: Análisis de Impacto Estático
Antes de editar un archivo existente, ejecuta siempre la herramienta de impacto para identificar qué módulos y suites de pruebas dependen de él:
```powershell
& 'C:/xampp/php/php.exe' scripts/impact.php --changed=ruta/al/Archivo.php
```
- Revisa todos los consumidores directos e indirectos reportados.
- Asegúrate de que ningún consumidor vea alterados los tipos de retorno, las precondiciones ni las invariantes de seguridad.

### Paso 3: Protección de Invariantes de Persistencia y Seguridad
Al escribir el cambio, verifica que se cumplan sin excepción:
- **Autorización primero**: Verificar permisos del servidor (`Authorization`) antes de abrir conexiones SQL perezosas, leer almacenamiento o seleccionar paquetes.
- **Transacciones Propias y CAS (*Compare-And-Swap*)**: Toda escritura en base de datos debe poseer su propia transacción, bloquear el recurso primario primero, validar la revisión esperada (`expectedRevision`), comprobar que las filas afectadas sean exactamente las esperadas y verificar el `commit`.
- **Idempotencia**: Todo comando de mutación o configuración debe registrar y validar su hash canónico y clave de idempotencia para que los reintentos no dupliquen efectos ni reviertan estados posteriores.
- **Separación de Sesiones**: Las sesiones de jugadores del sitio web y las sesiones de administradores (`AdminCP`) están estrictamente separadas; ningún rol de juego o nombre coincidente otorga permisos administrativos.

### Paso 4: Ejecución de la Suite de Verificación Completa
Ningún cambio se considera válido hasta que pase los niveles de verificación del proyecto:
```powershell
& 'C:/xampp/php/php.exe' scripts/verify.php --level=complete
```
- Si agregas una funcionalidad nueva, **debes agregar su prueba unitaria/integración/concurrencia correspondiente** (incluyendo casos negativos: estado corrupto, falta de permisos, paquete ausente, rollback en fallo de commit).

### Paso 5: Registro Documental Honesto
- Actualiza `STATE.md`, `DECISIONS.md` (si es una decisión arquitectónica nueva) y la documentación específica en `docs/` distinguiendo con total honestidad entre:
  - *Implementado y verificado en SQLite/Sandbox*
  - *Implementado y verificado en MSSQL de laboratorio*
  - *Planeado / No implementado*

---
