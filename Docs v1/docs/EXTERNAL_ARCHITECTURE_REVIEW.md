# Síntesis y verificación de la revisión arquitectónica externa

**Fecha:** 19 de octubre de 2026  
**Origen:** respuesta de una IA externa pegada por el propietario del proyecto.  
**Estado:** síntesis revisada; sus recomendaciones no son decisiones aprobadas ni
evidencia de implementación.

## Procedencia y límites

La respuesta recibida ofrece un análisis amplio de plugins, migraciones, MU,
persistencia, pagos, pruebas, CI y diagramas. Sin embargo, el propio texto
declara que no realizó búsqueda web en vivo. Sus referencias `[cite: n]` apuntan
a algunos documentos internos o quedan como marcadores, no como citas web
verificables. Además, el revisor indica que solo recibió diez documentos y que
reconstruyó otros estados desde resúmenes.

Por ello:

- Se contrastaron los hallazgos importantes con el `STATE.md` actual y con la
  documentación pertinente del repositorio.
- Las conclusiones sobre estado se consideran fiables solo cuando coinciden con
  esas fuentes canónicas.
- Recomendaciones que dependen de detalles específicos de MU, infraestructura
  local, autorizaciones de producto o integraciones externas quedan como
  hipótesis/decisiones pendientes.
- El informe no es auditoría, aprobación de diseño, certificación ni prueba de
  preparación para producción.
- La valoración del informe de que el proyecto supera “la media” de productos
  similares no tiene un conjunto de comparación ni métricas y no se adopta como
  conclusión verificada.

## Síntesis

La conclusión central del informe coincide con el estado del proyecto: hay
fundaciones de persistencia, contratos, seguridad y descubrimiento que han
recibido pruebas offline y de laboratorio acotadas, pero ZendaMod Next todavía
no es un CMS con plugins ejecutables. El paso 03 (estado/migración de plugins)
sigue incompleto y el paso 04 (ciclo HTTP MainAPI add/remove/readd) no se ha
entregado.

La mejor recomendación estructural es continuar con un **monolito modular PHP**
y límites explícitos, cerrar primero los gates ya definidos y evitar
microservicios/colas obligatorias sin necesidad medida. No se debe convertir la
lista futura de capacidades MU en una promesa de compatibilidad universal.

## Hallazgos contrastados

| ID | Tema | Evaluación del informe | Contraste y disposición |
|---|---|---|---|
| R1 | Migraciones del kernel/plugins, paso 03 | No hay todavía verificación física completa, ejecución aprobada y reconciliación de interrupciones. | **Confirmado** por el encabezado de [STATE.md](../STATE.md): existe un modelo lógico acotado, no inspección física ni ejecución de migraciones. Mantener como gate antes de completar instalación de plugins; no activar código por el hecho de revisar un plan. |
| R2 | MainAPI ejecutable y ciclo de vida, paso 04 | Faltan SDK/runtime y pruebas HTTP/workers de agregar, quitar y reponer. | **Confirmado** por [API_ARCHITECTURE.md](../API_ARCHITECTURE.md), [PLUGIN_SYSTEM.md](../PLUGIN_SYSTEM.md) y el estado. Descubrimiento data-only no equivale a activación. Construir el primer corte vertical, pequeño y read-only, después de las dependencias de paso 03 que exija ese corte. |
| R3 | PHP 7.4.33 | No es una base adecuada para producción nueva al estar fuera del soporte oficial. | **Confirmado como brecha de política y riesgo**: el runtime verificado del proyecto es PHP 7.4.33. El objetivo PHP de producción requiere decisión del propietario y comprobación del ciclo oficial vigente cuando se seleccione; no adoptar automáticamente PHP 8.2/8.3 solo porque el informe lo sugiere. |
| R4 | Cobertura MSSQL/ODBC | La evidencia real cubre familias concretas, no todo el sitio ni todos los almacenes. | **Confirmado con alcance acotado** en [STATE.md](../STATE.md), [MSSQL_LAB.md](MSSQL_LAB.md) y [MSSQL_HTTP.md](MSSQL_HTTP.md). La evidencia previa incluye rutas/DDL concretos; no generalizarla a DDL 005–011, a TLS estricto de producción ni a composición completa por defecto. Planificar pruebas reales por adaptador y por uso productivo requerido. |
| R5 | Despliegue de carpetas y consistencia entre workers | Staging, promoción atómica, OPcache y frescura de workers deben probarse. | **Riesgo válido y diseño en parte especificado**, pero no es runtime ya demostrado. [PLUGIN_SYSTEM.md](../PLUGIN_SYSTEM.md) y [PLUGIN_RUNTIME_MIGRATION.md](PLUGIN_RUNTIME_MIGRATION.md) ya requieren presencia física fresca, ausencia de caché revivida, workers independientes y tratamiento de copias incompletas. No asumir que `opcache_invalidate()` resuelve por sí sola distribución multi-nodo o workers persistentes. |
| R6 | Escrituras nativas, bloqueo y desconexión MU | El informe propone `MEMB_STAT.ConnectStat = 0`, tiempo de gracia `DisConnectTM` y lock SQL de cuenta. | **Gate de seguridad confirmado; detalles de implementación no aceptados como universales.** [DATABASE.md](../DATABASE.md), [GAME_TYPE_API.md](../GAME_TYPE_API.md) y [CHANGELOG.md](../CHANGELOG.md) documentan que las heurísticas/checks anteriores no probaron sincronización suficiente; el mercado nativo sigue deshabilitado. Los nombres/semántica de columnas, período de gracia, bloqueo distribuido y coordinación real deben quedar en un adaptador y diseño MU verificado, no copiarse como regla para todos los emuladores. |
| R7 | Recuperación de contraseñas SQL y outbox | La recuperación pública está deshabilitada en modo DB mientras falta entrega segura; propone un worker/outbox. | **Estado confirmado; propuesta razonable, incompleta en detalle.** [STATE.md](../STATE.md) dice que el modo DB devuelve 503 y no hay delivery/outbox configurado. Un outbox podría dar atomicidad al enqueue, pero “guardar solo el hash del token en el outbox” no basta para que el worker construya un enlace válido. El diseño debe resolver entrega secreta de forma protegida, expiración, revocación, reintentos y redacción antes de implementarse. |
| R8 | Auditoría administrativa separada del cambio | Algunas operaciones mantienen el append de auditoría HTTP en commit separado; falla de auditoría podría dejar mutación sin rastro. | **Confirmado como gap de casos específicos**, no como afirmación de que toda auditoría sea externa. [STATE.md](../STATE.md) registra que el audit HTTP de credenciales está separado y que comandos administrativos durables con auditoría atómica siguen pendientes. Diseñar por comando/almacén y probar rollback; distinguir el journal obligatorio de la telemetría HTTP general. |
| R9 | Pagos y efectos en juego | El cobro y la entrega nativa no deben tratarse como una única transacción distribuida; hacen falta estados idempotentes y reconciliación. | **Alineado con invariantes**, aunque no implementado. [AGENTS.md](../AGENTS.md), [INVARIANTS.md](../INVARIANTS.md) y [MARKETPLACE.md](MARKETPLACE.md) mantienen fulfillment/escrituras deshabilitados. No fijar todavía estados o ledger definitivos sin contrato, proveedor, propietario de datos y política de reintentos. |
| R10 | “Autodetección universal” de MU | Cambiar la promesa por una matriz versionada de adaptadores y capacidades verificadas. | **Recomendación aceptada como criterio de producto**, no implementación. La propia documentación declara que Season 1–19+, codecs, ownership y esquemas universales no están certificados. Cada adaptación debe indicar versión, pruebas/fixtures, lecturas soportadas y capacidades denegadas; el desconocido queda `UNAVAILABLE`, sin heurística autorizando writes. |
| R11 | Recuperación/instalador/reset | Permanecen en especificación y deben afectar únicamente datos web incluso en DB unificada. | **Confirmado** por [INSTALLATION_LIFECYCLE.md](INSTALLATION_LIFECYCLE.md). No hay permiso para ejecutar un reset ni borrar datos. La propuesta de probar topología unificada es apropiada cuando exista implementación, usando fixtures propios. |
| R12 | Separación premium/free y GrapesJS | La separación de Webshop/VIP es parcial; otras responsabilidades comerciales y editor visual siguen pendientes. | **Confirmado** por [STATE.md](../STATE.md), [ARCHITECTURE.md](../ARCHITECTURE.md) y [TEMPLATE_SYSTEM.md](../TEMPLATE_SYSTEM.md). Migrar por paquete con equivalencia; el catálogo o licencia no habilita fulfillment. |
| R13 | CI y protección de ramas | Tener verificador no asegura que el host de Git exija su aprobación. | **Pendiente de gobernanza del repositorio**, como documenta [AGENTS.md](../AGENTS.md). No afirmar que el CI bloquee merges hasta activar y verificar las reglas del hosting. |
| R14 | Drupal vs. noticias nativas | El informe señala posible tensión entre Drupal externo y un plugin `zenda.news`. | **Decisión de producto abierta, pero no contradicción probada.** La integración actual lee artículos publicados desde Drupal; los requisitos dicen que la configuración del sitio debe persistir en MSSQL. La base/configuración interna de la instalación Drupal es una frontera diferente. No se debe inspeccionar/ejecutar el sitio legacy para resolverlo. Aclarar si Drupal sigue siendo proveedor editorial opcional, si se desea autoría nativa, o ambas mediante paquetes separados. |

## Recomendaciones que conviene conservar

1. Completar el paso 03 pendiente en `STATE.md`; no reinterpretar receipts no
   verificados como instalación comprobada.
2. Implementar paso 04 como primer plugin pequeño y read-only. La prueba debe
   correr con el servidor HTTP ya iniciado, verificar add/approve/remove/readd,
   un plugin independiente, AdminCP y datos retenidos; añadir procesos/workers
   cuando la superficie runtime lo soporte.
3. Mantener plugins PHP en proceso como código confiable aprobado. Si el producto
   necesita ejecutar extensiones no confiables, definir una frontera real de
   proceso antes de prometer aislamiento; autoloaders y permisos de API no
   pueden sandboxear `exit`, bucles infinitos ni acceso arbitrario al proceso.
4. Mantener gates separados para DatabaseType, GameType y SchemaAdapter; empezar
   por lecturas limitadas y adaptadores explícitamente verificados.
5. Incorporar análisis estático, fitness functions y pruebas generativas de
   forma gradual sobre reglas críticas (propiedad de código, contratos, parsing,
   límites, CAS, idempotencia, permisos y ausencia de fallback).
6. Acordar objetivos operativos y ensayar backup/restore, pero no seleccionar
   cifras de disponibilidad/RPO/RTO sin conocer despliegue y necesidades del
   producto.
7. Sustituir términos absolutos (“bugless”, “universal”, “instantáneo”,
   “aislado”, “atómico entre sistemas”) por matrices y criterios observables.

## Correcciones y cautelas al informe externo

- **No usar las prioridades P0/P1 como clasificación oficial todavía.** Las
  escrituras MU están deshabilitadas; son un gate crítico antes de habilitarlas,
  no un defecto de una función actualmente expuesta. El bloqueo de producción
  debe reflejar el alcance de lanzamiento que el propietario realmente elija.
- El ejemplo `MEMB_STAT` y la ventana `DisConnectTM` procede de un modelo MU
  específico. Debe validarse con el adaptador de esquema seleccionado y la
  autoridad del servidor; no garantiza que una transacción SQL o lock por sí
  sola sincronice la memoria del GameServer/DataServer.
- La recomendación de ejecutar una “suite 001–013 completa” no debe convertirse
  en una meta indiscriminada. Verificar cada DDL/store que se pretenda instalar
  y habilitar, con SQL Server real y grants mínimos. El estado instalado del
  paso 03 es una necesidad aparte.
- Un outbox no puede entregar un token a partir de su hash únicamente. El
  secreto de entrega y los datos sensibles del mensaje requieren un diseño
  específico de protección, caducidad y eliminación.
- La cuestión Drupal debe separar la configuración/conexión de Drupal que
  Zenda guarda para el sitio, del almacenamiento interno que Drupal utiliza.
  Confirmar la arquitectura autorizada sin ejecutar ni alterar la instalación
  legacy.
- El informe externo menciona que se recibieron solo algunos documentos. Antes
  de tomar cualquier recomendación de implementación, volver a contrastarla con
  `STATE.md`, `AGENTS.md`, la documentación normativa y los consumidores reales.

## Preguntas de producto que siguen abiertas

- ¿Qué rama PHP soportada será obligatoria para instalaciones productivas, y
  debe conservarse PHP 7.4 solo para compatibilidad de transición/offline?
- ¿Se distribuirá únicamente código plugin de publicadores confiables, o se
  requiere aislamiento de proceso para terceros?
- ¿Cuál es la política comercial concreta para cifrado/licencias de paquetes
  premium? No añadir un loader al kernel abierto sin decisión.
- ¿Noticias se editarán en Drupal, en un plugin nativo, o se soportarán ambos
  modelos como paquetes/proveedores independientes?
- ¿Qué evidencia y mecanismo autoritativo probarán que una cuenta MU está
  desconectada y que el servidor de juego no conserva cambios en memoria antes
  de permitir futuras mutaciones?
- ¿Qué objetivos de disponibilidad, latencia, pérdida de datos y recuperación
  son necesarios para cada instalación y tamaño de servidor?

Estas preguntas no bloquean el siguiente paso autorizado ya descrito por
`STATE.md`; deben resolverse antes de las fases a las que afecten.

## Trazabilidad

El informe externo y esta síntesis son insumos de revisión, no un segundo estado
de implementación. La fuente canónica de avance sigue siendo
[STATE.md](../STATE.md). La evaluación previa de fuentes públicas se mantiene en
[ARCHITECTURE_GAP_ASSESSMENT.md](ARCHITECTURE_GAP_ASSESSMENT.md); la respuesta
externa pegada no añadió fuentes web verificadas.
