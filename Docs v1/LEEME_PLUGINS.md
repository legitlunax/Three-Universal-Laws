# Objetivo principal: plugins de carpeta para ZendaMod Next

## API principal y proveedores especializados

No son tres bases independientes: `MainAPI` gestiona todos los plugins; `GameTypeAPI` añade tipos de juego, `DatabaseTypeAPI` añade motores externos de datos de juego y `SchemaAdapterAPI` interpreta esquemas/emuladores específicos. Ejemplo: `zenda.game.mu` + `zenda.db.mssql` + `zenda.schema.muemu` + `zenda.rankings`. Todos se colocan en `/plugins`; al quitar uno, solo desaparecen sus capacidades y se bloquean los consumidores obligatorios. **El MSSQL del sitio web no depende del plugin `zenda.db.mssql`**. Los contratos actuales son una especificación, aún no una funcionalidad PHP operativa. Lee `API_ARCHITECTURE.md` y los cuatro manuales de API.


Este paquete actualiza la **documentación del proyecto**, no el PHP del sitio.

**Lo que se busca:** colocar una carpeta `plugins/zenda.webshop/` y tener integración
por API con menús, rutas, eventos, servicios y bloques del editor. Quitar la
carpeta elimina **solo** esas funcionalidades a partir de la próxima petición;
el núcleo, el AdminCP y los demás plugins independientes siguen funcionando.
MSSQL conserva configuración, historiales y datos. Reinstalar la misma carpeta
aprobada y compatible permite recuperar su funcionamiento.

**La condición de seguridad:** un plugin nuevo o modificado no ejecuta PHP hasta
que el administrador lo apruebe. Una vez aprobado, puede volver a instalarse con
solo copiar la carpeta. La dependencia obligatoria ausente se bloquea limpiamente.
Una aplicación PHP no puede interrumpir código que ya está ejecutándose ni aislar
arbitrariamente código malicioso dentro del mismo proceso.

**Dos funciones distintas:** mover una carpeta a `/plugins` es el sistema de
plugins. Arrastrar bloques en GrapesJS es edición visual de páginas. Un plugin
puede registrar nuevos bloques, pero no son la misma operación.

**Lecturas iniciales para Codex/Antigravity:**

1. `STATE.md`, `AGENTS.md` y `PLUGIN_SYSTEM.md`.
2. `PLUGIN_DEVELOPMENT.md` para las reglas de creación.
3. `PLUGIN_IMPLEMENTATION_PROMPT.md` para encargar la implementación.

**Estado real:** los MD se han preparado, pero el loader y los tests de
quitar/poner carpetas **no existen todavía por este solo cambio**. Para que
funcione hay que aplicar estas instrucciones sobre el repositorio PHP real.
