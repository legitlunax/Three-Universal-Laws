# Diagramas de arquitectura

Estos diagramas resumen el estado documentado de ZendaMod Next al 19 de octubre
de 2026. El primero describe la implementación actual y sus límites; el segundo
es una visión objetivo, no una promesa de que esas capacidades ya existan.
`STATE.md` continúa siendo la fuente de verdad del progreso.

Para una vista visual en árbol expandible, abrir
[ARCHITECTURE_DIAGRAMS.html](ARCHITECTURE_DIAGRAMS.html) en el navegador.
Esta versión Markdown conserva los diagramas Mermaid como alternativa.

## Arquitectura actual

```mermaid
flowchart TB
    Visitor["Visitante / jugador / staff"]
    Web["HTTP webroot: public/"]
    Entry["public/index.php"]
    Bootstrap["Bootstrap y composición estática<br/>src/Bootstrap"]
    Router["Rutas, middleware y controladores<br/>PHP 7.4"]
    Contracts["Contratos congelados V1–V5<br/>src/Contracts"]
    Catalog["Política y catálogo estáticos<br/>architecture/*.json"]

    Visitor --> Web --> Entry --> Bootstrap --> Router
    Bootstrap --> Contracts
    Bootstrap --> Catalog

    subgraph Features["Capacidades actuales: cortes parciales / sandbox offline"]
        Identity["Identity<br/>sesiones web separadas, cuentas y perfiles"]
        Content["Content<br/>anuncios y lectura opcional de Drupal"]
        Community["Community<br/>tickets de soporte"]
        GameData["GameData<br/>datos MU sintéticos, guías, eventos y mercado"]
        Admin["Admin<br/>configuración y herramientas web"]
        Templates["Templates<br/>JSON canónico y renderizado de solo lectura"]
        Premium["packages/premium/<br/>Webshop y VIP: catálogos / previews sintéticos"]
    end
    Router --> Identity
    Router --> Content
    Router --> Community
    Router --> GameData
    Router --> Admin
    Router --> Templates
    Router --> Premium
    Identity -. "puertos y contratos declarados" .-> Contracts
    Content -.-> Contracts
    Community -.-> Contracts
    GameData -.-> Contracts
    Templates -.-> Contracts
    Premium -.-> Contracts

    subgraph Persistence["Persistencia web (no es la base de datos del juego)"]
        WebMSSQL["Tablas propias del sitio en MSSQL<br/>adaptadores por familia; integración no uniforme"]
        Offline["Archivo / SQLite<br/>prototipos y fixtures offline"]
        Rule["Modo DB no debe caer silenciosamente<br/>a archivos; producción aún no certificada"]
    end
    Admin --> WebMSSQL
    Identity --> WebMSSQL
    Community --> WebMSSQL
    GameData --> WebMSSQL
    Templates --> WebMSSQL
    Premium --> WebMSSQL
    Features -. "algunos caminos sandbox" .-> Offline
    WebMSSQL -.-> Rule
    Offline -.-> Rule

    subgraph PluginBase["Cimientos de plugins ya presentes, aún no runtime"]
        Discovery["Descubrimiento de paquetes<br/>manifiestos / huellas: solo datos"]
        Approval["Aprobación y habilitación<br/>almacenamiento / revisión de planes"]
        NoRuntime["Sin carga ejecutable ni registro<br/>de rutas/plugin HTTP o workers"]
    end
    Bootstrap --> Discovery
    Discovery --> Approval --> NoRuntime

    Drupal["Drupal opcional<br/>lectura anónima de noticias"]
    Content -. "solo lectura cuando se configura" .-> Drupal
    GameDB["Bases de cuenta/juego MU<br/>sin integración nativa activa"]
    GameData -. "no conectado para operaciones nativas" .-> GameDB

    Safety["Escrituras nativas MU: deshabilitadas<br/>Pagos: deshabilitados<br/>Publicación de plantillas: deshabilitada"]
    GameData --> Safety
    Premium --> Safety
    Templates --> Safety
```

### Cómo leer el estado actual

- El punto de entrada real es `public/index.php`; la composición y las rutas son
  estáticas, no ensambladas por plugins independientes.
- Existen capas por módulo y contratos V1–V5. Los estados de `architecture/`
  describen el plan completo y no significan que todos los módulos estén
  habilitados en producción.
- La carpeta `src/Bootstrap/Plugins` valida paquetes/manifiestos sin ejecutar su
  PHP; el kernel también tiene políticas y almacenamiento de aprobación/revisión.
  Todavía no hay el ciclo de vida HTTP de añadir, quitar y volver a añadir un
  plugin ejecutable.
- Hay adaptadores preparados para persistencia web MSSQL y pruebas SQL acotadas,
  pero no equivalen a una instalación de producción certificada ni a conexión
  con las bases nativas de MU. Archivo/SQLite se conserva como prototipo offline,
  no como fallback silencioso del modo DB.
- La integración de Drupal es de lectura de noticias. Webshop/VIP y varias
  proyecciones de MU son sintéticas; no realizan entregas nativas.

## Arquitectura objetivo al completar la migración

```mermaid
flowchart TB
    User["Jugador / staff"]
    HTTP["HTTP"]
    Public["public/"]
    Kernel["Kernel confiable<br/>router · auth web · permisos · AdminCP base"]
    MainAPI["MainAPI / PluginHost<br/>API versionada"]
    User --> HTTP --> Public --> Kernel
    Kernel --> MainAPI

    subgraph PluginLifecycle["Resolución por request / worker"]
        Folders["plugins/<vendor>.<slug>/<br/>carpetas independientes"]
        Scan["Descubrimiento de hijos directos<br/>manifest y huellas; sin ejecutar código"]
        Gate["Confianza · aprobación · compatibilidad<br/>dependencias · permisos · licencia"]
        Registry["Registro inmutable y acotado<br/>por request"]
        Folders --> Scan --> Gate --> Registry
    end
    MainAPI --> Scan
    Gate -->|"solo paquetes aprobados"| Registry
    Registry --> Routes["Rutas / servicios"]
    Registry --> AdminExt["Extensiones AdminCP"]
    Registry --> Events["Eventos / comandos"]
    Registry --> Jobs["Jobs / workers"]
    Registry --> Widgets["Widgets de plantillas<br/>GrapesJS"]

    subgraph Providers["Proveedores opcionales bajo plugins/"]
        GameAPI["GameTypeAPI<br/>familia de juego"]
        DBAPI["DatabaseTypeAPI<br/>transporte externo de datos de juego"]
        SchemaAPI["SchemaAdapterAPI<br/>layout / emulador verificado"]
        MU["Proveedor MU Online"]
        MSSQLGame["Proveedor de transporte MSSQL de juego"]
        MUEMU["Adaptador MUEMU"]
        Louis["Adaptador Louis"]
        GameAPI --> MU
        DBAPI --> MSSQLGame
        SchemaAPI --> MUEMU
        SchemaAPI --> Louis
    end
    Registry --> GameAPI
    Registry --> DBAPI
    Registry --> SchemaAPI

    subgraph FeaturePlugins["Funciones instalables; ejemplos del destino"]
        Free["Plugins gratuitos<br/>news · rankings · perfiles · soporte"]
        Paid["Paquetes premium separados<br/>webshop · VIP · inventario · mercado"]
        More["Otros plugins<br/>guilds · Castle Siege · contenido"]
    end
    Registry --> Free
    Registry --> Paid
    Registry --> More
    Free -->|"puertos de lectura normalizados"| GameAPI
    Paid -->|"puertos tipados y gateway protegido"| GameAPI
    More --> GameAPI

    subgraph Stores["Datos y persistencia separados"]
        WebDB["MSSQL del sitio, propiedad del kernel<br/>configuración · admin web · aprobaciones · auditoría<br/>migraciones revisadas · datos retenidos"]
        AccountDB["Datos de cuentas del juego<br/>opcional / perfil de realm"]
        GameDB["Datos de juego<br/>opcional / perfil de realm"]
        Topology["Topología configurable<br/>unificada o bases separadas"]
        WebDB --- Topology
        AccountDB --- Topology
        GameDB --- Topology
    end
    Kernel --> WebDB
    Registry --> WebDB
    MSSQLGame --> AccountDB
    MSSQLGame --> GameDB

    Profile["Perfil explícito por realm<br/>juego + transporte + esquema verificado"]
    MU --> Profile
    MSSQLGame --> Profile
    MUEMU --> Profile
    Louis --> Profile
    Profile -->|"capacidad de lectura normalizada"| Free

    Mutation["Gateway de mutación nativa<br/>solo tras ownership + offline + lock<br/>recheck + idempotencia + auditoría"]
    Paid --> Mutation
    Mutation --> GameDB
    Payment["Pasarela / fulfillment<br/>callback verificado + replay protection<br/>transición auditada"]
    Paid --> Payment
    Payment --> WebDB

    Remove["Al quitar carpeta:<br/>contribuciones desaparecen al siguiente request;<br/>datos MSSQL permanecen"]
    Restore["Al restaurar versión aprobada:<br/>revalidar y reanudar datos retenidos"]
    Folders --> Remove --> Restore --> Gate
    Empty["El kernel y AdminCP arrancan<br/>aunque plugins/ esté vacío"]
    Kernel --> Empty
```

### Propiedades del destino

- Cada plugin entra por **MainAPI**. `GameTypeAPI`, `DatabaseTypeAPI` y
  `SchemaAdapterAPI` son contratos separados: juego, motor/transporte y esquema
  de emulador no son intercambiables.
- El MSSQL del sitio pertenece al kernel y no depende del proveedor opcional de
  base de datos del juego. Los datos del sitio, cuentas y juego pueden compartir
  una base o estar separados; el perfil de realm define la combinación.
- La presencia física determina si el código está disponible; la aprobación y
  configuración se conservan en MSSQL. Quitar código revoca sus contribuciones,
  no borra sus datos. El kernel debe funcionar con `plugins/` vacío.
- Ningún plugin de funciones consulta directamente tablas nativas del juego:
  usa capacidades/puertos normalizados. Lectura no concede autorización de
  escritura. Las mutaciones y pagos siguen sus respectivos controles de
  seguridad, idempotencia y auditoría.
- La transición será gradual: los módulos estáticos y paquetes premium actuales
  permanecen hasta migrarlos y probar equivalencia. El objetivo no se alcanza
  simplemente por tener esta especificación.
