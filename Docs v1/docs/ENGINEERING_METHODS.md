# Métodos de ingeniería recomendados para ZendaMod Next

**Tipo de documento:** recomendaciones para futuras decisiones e
implementaciones. No afirma que estas prácticas ya estén adoptadas ni autoriza
instalar herramientas, ampliar contratos o habilitar integraciones.

## Propósito

No existe una arquitectura ni una técnica que produzca software “sin bugs”.
La meta práctica es reducir defectos mediante límites explícitos, feedback
temprano y pruebas que cubran riesgos reales; detectar fallos y poder recuperarse
de ellos sin dañar datos ni afectar componentes independientes.

Estas sugerencias se adaptan a lo que ya exige el proyecto: PHP, transición
gradual desde módulos estáticos, futuro host de plugins, contratos V1–V5
protegidos, website MSSQL propio y límites de seguridad para datos MU/pagos.
Consultar [STATE.md](../STATE.md) y la
[evaluación de brechas](ARCHITECTURE_GAP_ASSESSMENT.md) para el estado actual.

## Diseños recomendados

### 1. Mantener un monolito modular con puertos y adaptadores

Usar un kernel PHP y módulos/plugins dentro del proceso como primera topología.
Mantener Domain/Application separados de SQL, HTTP, filesystem y SDKs externos;
concentrar ensamblado en Bootstrap; hacer que cada propietario consuma
contratos, no internals de otro módulo/paquete.

Esto corresponde a Ports and Adapters (hexagonal): el dominio se prueba sin
depender de UI o base de datos y los adaptadores intercambiables implementan
puertos del dueño. Ya coincide con las capas del proyecto y con su preferencia
por una composición central. Aplicar DDD de forma selectiva: bounded contexts y
lenguaje del dominio para identidad web, ciclo de plugins, migraciones y
operaciones MU; no crear agregados, bus de eventos o servicios distribuidos
donde no haya una regla de negocio que los necesite.

**No introducir microservicios por moda.** Considerar aislar un plugin en otro
proceso únicamente si hace falta un límite de privilegios, aislamiento de fallos
o despliegue independiente que PHP in-process no puede ofrecer, y después de
definir su protocolo, timeouts, identidad y observabilidad.

Fuente: [Hexagonal Architecture / Ports and Adapters](https://alistair.cockburn.us/hexagonal-architecture/).

### 2. Usar contratos explícitos y decisiones arquitectónicas pequeñas

Cada contrato público debe tener dueño, versión, capacidades, errores, permisos
y estrategia de evolución. Mantener los contratos congelados existentes; los
nuevos SDK MainAPI/GameType/DatabaseType/SchemaAdapter deben nacer como
superficies aditivas distintas. Para interacciones dentro del mismo kernel, una
prueba provider/consumer del contrato puede ser suficiente; reservar Pact u
otra infraestructura de contract testing para fronteras reales de mensajes o
HTTP entre procesos, no incorporarla automáticamente a cada llamada PHP.

Para decisiones que cambian límites, persistencia, seguridad o distribución,
mantener ADR breves: contexto, decisión, alternativas, consecuencias positivas
y negativas, status y evidencia de revisión. Los números/decisiones existentes
pueden ser el índice; evitar un segundo ledger de progreso.

Fuentes: [Pact: contract testing](https://docs.pact.io/) y
[Documenting Architecture Decisions (ADR)](https://cognitect.com/blog/2011/11/15/documenting-architecture-decisions).

### 3. Convertir invariantes en “fitness functions” automatizadas

Las reglas arquitectónicas más importantes deberían romper el build al
incumplirse, no depender solo de revisión manual. Extender gradualmente
`scripts/verify.php`, `scripts/impact.php` y las pruebas de dueño para comprobar:

- ningún módulo importa internals de otro owner;
- core/contracts nunca dependen de paquetes premium;
- PHP ejecutable de plugins no se descubre ni carga sin paquete presente,
  compatible y aprobado;
- los providers no se eligen por orden accidental y ningún driver sustituye el
  MSSQL propio del sitio;
- colisiones, ciclos, permisos o registros parciales rechazan una contribución
  sin dejar rutas/superficies residuales;
- ninguna escritura nativa ocurre sin el gateway y guardrails requeridos;
- cada cambio a un contrato congelado se detecta sin refrescar baselines;
- las rutas y vistas continúan escapando contenido y denegando actores no
  autorizados.

Una fitness function debe ser medible y corresponder a una cualidad o regla
valiosa, no ser otro test que simplemente repite la implementación. Si una regla
no se puede automatizar, documentar el responsable y revisión manual pendiente.

Fuente: [Thoughtworks: Fitness function-driven development](https://www.thoughtworks.com/insights/articles/fitness-function-driven-development).

## Métodos para reducir defectos

### 4. TDD y pruebas por riesgo, no por cantidad

Para cada comportamiento crítico, empezar por casos observables (red), hacer el
cambio mínimo (green) y refactorizar conservando las pruebas. Priorizar invariantes
y denegaciones antes que una cobertura porcentual arbitraria.

Mantener una pirámide práctica, no rígida:

1. **Pruebas rápidas de dominio** para validación, transiciones, canonicalización,
   permisos, límites de schema y cálculo puro.
2. **Pruebas de adaptadores** para SQL/ODBC, lectura/escritura, locks, encoding,
   filesystem y serialización, usando fixtures aislados y dueños explícitos.
3. **Pruebas de sistema** para las pocas rutas verticales de alto riesgo: login
   AdminCP, import/instalación autorizada, add/remove/readd de plugin, y
   operaciones fallidas/reintentadas.
4. **Pruebas de navegador** cuando exista editor GrapesJS real o flujos visuales;
   no sustituirlas por snapshots sintéticos.

Para APIs/plugin lifecycle probar en cada cambio: permitido, no autorizado,
ausente, corrupto, obsoleto, duplicado, concurrencia, fallo antes/después de
commit, reintento y restauración. La ausencia de integración SQL real o navegador
debe quedar como “no verificada”, nunca como pass.

TDD y el test pyramid son guías, no una orden de que todos los casos deban ser
tests unitarios: una prueba de concurrencia requiere procesos/conexiones reales
del fixture, y un lifecycle de rutas requiere HTTP.

Fuentes: [Test-Driven Development](https://martinfowler.com/bliki/TestDrivenDevelopment.html)
y [The Practical Test Pyramid](https://martinfowler.com/articles/practical-test-pyramid.html).

### 5. Añadir pruebas generativas y mutation testing donde la lógica lo justifique

- **Generación/property-based y fuzzing controlado:** explorar muchos inputs
  límites/inválidos para manifiestos JSON, perfil SQL, blobs binarios MU,
  canonicalización y decodificadores; verificar propiedades como
  “parsear-canonicalizar-cargar produce el mismo digest” y “input truncado nunca
  se acepta”. Empezar con generadores deterministas sencillos. Adoptar una
  dependencia PHP solo después de revisar compatibilidad, mantenimiento,
  licencia y reproducibilidad; no agregarla como requisito global por defecto.
- **Mutation testing:** cambiar temporalmente operadores/condiciones y comprobar
  que las pruebas fallan. Usarlo de forma focalizada en autorización, CAS,
  replay, montos, límites y reglas fail-closed para descubrir asserts débiles.
  No perseguir 100% en todo el proyecto ni permitir que un score sustituya una
  revisión de los invariantes.

### 6. Usar análisis estático de forma incremental

Evaluar PHPStan (u otra herramienta mantenida compatible con la matriz elegida)
para tipos, ramas imposibles, símbolos inexistentes y errores de llamadas.
Comenzar midiendo el baseline actual, fijar un nivel sin nuevos errores y subirlo
gradualmente por owner. Analizar código propio y mantener fuera vendor/artefactos.
Ejecutar sobre el PHP objetivo de producción y conservar pruebas de compatibilidad
separadas si se mantiene una versión mínima antigua.

No instalar ni cambiar dependencias como parte de esta recomendación; primero
revisar `composer.json`, el runtime soportado y el resultado piloto.

Fuente: [PHPStan: getting started](https://phpstan.org/user-guide/getting-started).

## Métodos para persistencia, plugins y operación

### 7. Evolucionar esquemas con compatibilidad y recuperación

Mantener migraciones como datos/operaciones restringidas, explícitas y aprobadas.
Aplicar la idea expand/contract solo en una secuencia validada: añadir estructura
compatible, desplegar lectores/escritores compatibles, verificar/backfill y
retirar elementos antiguos mediante una migración posterior aprobada. Una
migración de base de datos y una publicación de código no son atómicas por
defecto; debe existir camino de forward recovery y prueba de interrupción.

Para Zenda, esta recomendación queda subordinada al modelo de operaciones seguro
ya iniciado: no habilita SQL/PHP arbitrario, escritura a tablas MU ni ampliación
automática de la allowlist. Definir y probar la huella física real antes de
confiar en el estado guardado.

Fuente: [Evolutionary Database Design](https://martinfowler.com/articles/evodb.html).

### 8. Hacer idempotentes los efectos externos y resolver “dual writes”

Cuando una operación futura tenga que confirmar estado web y además notificar
otro sistema, no hacer dos escrituras independientes con supuesto de atomicidad.
Si el problema aplica, una tabla outbox escrita en la misma transacción puede
publicar el evento después del commit; el consumidor debe tolerar duplicados y
usar idempotencia. Si hay escrituras coordinadas en varios almacenes, diseñar una
saga/reconciliación con estados visibles, compensaciones seguras y auditoría.

Esto no sustituye el lock/offline/recheck del servidor MU ni convierte una
transacción SQL en sincronización con el proceso del juego. Aplicar solo a flujos
que realmente cruzan esas fronteras, como notificaciones o futuras pasarelas.

Fuente: [AWS Prescriptive Guidance: Transactional Outbox](https://docs.aws.amazon.com/prescriptive-guidance/latest/cloud-design-patterns/transactional-outbox.html).

### 9. Tratar plugins como artefactos de software con una cadena de confianza

El pipeline recomendado para paquetes ejecutables:

1. autor y versión identificables;
2. análisis/lint/tests de paquete y reglas de arquitectura;
3. dependencias inventariadas y revisadas;
4. artefacto reproducible o al menos build controlado con procedencia verificable;
5. firma/verificación según política del distribuidor;
6. revisión de permisos, capacidades y migraciones por separado;
7. aprobación del digest exacto;
8. staging fuera del directorio activo, promoción atómica y prueba add/remove;
9. monitoreo, revocación y respuesta a versiones vulnerables.

El hash del inventario debe atar bytes a la aprobación, pero no certificar autor,
seguridad o procedencia por sí solo. Código PHP de terceros que se ejecuta
in-process debe ser de confianza explícita; aprobación no es sandbox. Código no
confiable requiere aislamiento real fuera del proceso o no se instala.

Fuentes: [NIST SSDF](https://csrc.nist.gov/Projects/ssdf),
[OWASP ASVS](https://owasp.org/www-project-application-security-verification-standard/)
y [SLSA](https://slsa.dev/spec/v1.1/).

### 10. Medir lo que se promete y ensayar fallos

Antes de declarar “alta disponibilidad”, elegir SLI/SLO con responsables:
disponibilidad de rutas esenciales, percentiles de latencia, tasa de errores,
tiempo de recuperación, pérdida de datos permitida y demora máxima de workers.
Definir cómo medirlos, qué alertas provocan y qué se hace cuando el presupuesto de
errores se consume.

Hacer pruebas de fallo controladas y fixtures no productivos para: SQL lento/no
disponible, expiración TLS, paquete removido o modificado, worker con snapshot
viejo, crash entre commit y ack, disco lleno, backup restaurado y despliegue
incompleto. Incorporar observabilidad de request/plugin/realm sin registrar
credenciales, perfiles de conexión, datos personales ni blobs del juego.

Fuentes: [Google SRE: Service Level Objectives](https://sre.google/sre-book/service-level-objectives/)
y [DORA: Continuous Integration](https://dora.dev/capabilities/continuous-integration/).

## Orden sugerido para ZendaMod Next

1. **Ahora:** terminar paso 03 (inspección física y estado/migración verificable);
   decidir el runtime PHP de producción soportado.
2. **Primer corte vertical:** implementar paso 04 con un plugin pequeño read-only
   y demostrar HTTP add/approve/remove/readd, ausencia segura, AdminCP y plugin
   independiente en dos workers. No empezar migrando toda la web.
3. **Endurecimiento del host:** fitness functions, análisis estático incremental,
   pipeline de artefactos, controles server-side y pruebas de fallo del lifecycle.
4. **Operabilidad:** SLO/RPO/RTO, despliegue/rollback, backups y restore con
   MSSQL; registrar evidencia precisa por topología y driver.
5. **Providers y migraciones:** implementar DatabaseType en lectura, después
   GameType MU, SchemaAdapter verificado y un consumidor; validar contrato y
   ausencia de cada provider.
6. **Capacidades sensibles:** migrar módulos en cortes pequeños y solo habilitar
   pagos, mutaciones nativas, publicación GrapesJS o reset cuando cada uno tenga
   revisión de seguridad, pruebas reales apropiadas y reconciliación probada.

No adoptar las diez prácticas de una vez ni convertir las herramientas en fines
propios. Elegir un riesgo, acordar el criterio de salida, automatizar la evidencia
y seguir al siguiente. El progreso es la reducción demostrable de riesgo y
tiempo de recuperación, no el número de patrones, servicios o herramientas.

## Fuentes de métodos consultadas

- [Hexagonal Architecture](https://alistair.cockburn.us/hexagonal-architecture/)
- [Thoughtworks: Fitness function-driven development](https://www.thoughtworks.com/insights/articles/fitness-function-driven-development)
- [Martin Fowler: Test-Driven Development](https://martinfowler.com/bliki/TestDrivenDevelopment.html)
- [Martin Fowler: The Practical Test Pyramid](https://martinfowler.com/articles/practical-test-pyramid.html)
- [Cognitect: Documenting Architecture Decisions](https://cognitect.com/blog/2011/11/15/documenting-architecture-decisions)
- [PHPStan user guide](https://phpstan.org/user-guide/getting-started)
- [Martin Fowler: Evolutionary Database Design](https://martinfowler.com/articles/evodb.html)
- [AWS: Transactional Outbox](https://docs.aws.amazon.com/prescriptive-guidance/latest/cloud-design-patterns/transactional-outbox.html)
- [NIST SSDF](https://csrc.nist.gov/Projects/ssdf)
- [OWASP ASVS](https://owasp.org/www-project-application-security-verification-standard/)
- [SLSA specification](https://slsa.dev/spec/v1.1/)
- [Google SRE: Service Level Objectives](https://sre.google/sre-book/service-level-objectives/)
- [DORA: Continuous Integration](https://dora.dev/capabilities/continuous-integration/)
- [PHP supported versions](https://www.php.net/supported-versions.php)
