# Evaluación de brechas: arquitectura objetivo y prácticas empresariales

**Fecha de revisión:** 19 de octubre de 2026  
**Alcance:** revisión documental y contraste con fuentes públicas primarias. No es
una auditoría de seguridad, prueba de carga, certificación ni validación de un
despliegue de producción.

## Conclusión ejecutiva

La dirección plugin-first está bien planteada en varios fundamentos difíciles:
un kernel que no depende de plugins opcionales, límites entre APIs, contratos
versionados, MSSQL del sitio independiente de los proveedores de juego,
descubrimiento sin ejecutar código, aprobación explícita, retención de datos al
quitar carpetas y controles estrictos para mutaciones nativas.

Pero la arquitectura futura es todavía **una especificación**, no una plataforma
empresarial operativa. La revisión del estado del proyecto muestra que la
ejecución/verificación física de migraciones del sitio está pendiente (paso 03)
y que todavía falta la primera integración MainAPI con ciclo HTTP de añadir,
quitar y reponer un plugin (paso 04). Las APIs ejecutables de juego, transporte
y esquema tampoco están implementadas.

Las brechas de mayor impacto para producción son:

1. El runtime comprobado es PHP 7.4.33, una rama fuera de soporte.
2. No existe aún una prueba de ciclo de vida de plugins ejecutables por HTTP y
   workers. Un plugin PHP en el mismo proceso es código confiable, no código
   aislado.
3. La inspección física, instalación y recuperación de migraciones MSSQL aún no
   están completas ni verificadas en producción.
4. No hay objetivos operativos acordados y medidos para disponibilidad,
   latencia, RPO/RTO, restauración, monitoreo y respuesta a incidentes.
5. El diagrama objetivo no especifica el despliegue real, las zonas de confianza,
   la cadena de suministro de paquetes ni el comportamiento multi-servidor.

Por tanto, conviene mantener el monolito modular PHP como forma inicial y
concentrarse en evidencia vertical completa antes de ampliar la matriz de juegos
o añadir infraestructura distribuida. “Empresarial” no exige microservicios ni
Kubernetes por sí mismos; exige que los requisitos de calidad, seguridad y
operación sean verificables para el contexto real.

## Marcos usados para la comparación

| Marco / fuente | Qué evalúa | Aplicación a ZendaMod Next |
|---|---|---|
| [C4 Model](https://c4model.com/) | Contexto, contenedores, componentes y código; también vistas dinámicas y de despliegue. Recomienda usar solo los niveles que aporten valor; contexto y contenedores suelen bastar. | El árbol HTML es claro para navegar la jerarquía, pero no muestra actores, límites de confianza, protocolos, instancias desplegadas ni secuencias del ciclo de vida. |
| [AWS Well-Architected](https://docs.aws.amazon.com/wellarchitected/latest/framework/the-pillars-of-the-framework.html) y [Azure Well-Architected](https://learn.microsoft.com/en-us/azure/well-architected/) | Seguridad, confiabilidad, excelencia operacional, rendimiento, costos y sostenibilidad (AWS); Azure organiza pilares cercanos y ofrece guías de evaluación por carga. | El objetivo representa sobre todo estructura funcional; faltan requisitos medibles y diseño de operación/despliegue para esos atributos. |
| [CNCF Platforms White Paper](https://tag-app-delivery.cncf.io/whitepapers/platforms/) | Plataforma como producto para usuarios identificados, con interfaces coherentes, autoservicio, gobernanza y menor carga cognitiva. | El host debe servir a dos grupos: operadores de sitios y autores/distribuidores de plugins; falta cerrar su flujo de desarrollo, aprobación, actualización, diagnóstico y recuperación. |
| [NIST SSDF 1.1](https://csrc.nist.gov/Projects/ssdf) y [OWASP ASVS](https://owasp.org/www-project-application-security-verification-standard/) | Prácticas de desarrollo seguro basadas en riesgo y requisitos verificables de seguridad de aplicaciones. | Aportan un marco para convertir reglas de seguridad ya documentadas en controles y pruebas versionados, sin presentar una lista genérica como certificación. |
| [SLSA 1.1](https://slsa.dev/spec/v1.1/) | Integridad de compilación y artefactos, procedencia verificable y controles de la plataforma de build. | El inventario/hash del paquete ayuda a detectar cambios, pero por sí solo no prueba quién publicó el plugin ni cómo fue compilado. |
| [Google SRE: Service Level Objectives](https://sre.google/sre-book/service-level-objectives/) | Define SLI medibles (por ejemplo, latencia, error, disponibilidad) y SLO con objetivos explícitos. | Antes de dimensionar tolerancia a fallos y escalado, el producto necesita SLO, presupuesto de errores y recuperación objetivo apropiados a su despliegue. |
| [DORA: Continuous Integration](https://dora.dev/capabilities/continuous-integration/) | Integración frecuente, automatización y retroalimentación rápida sobre cambios. | Los verificadores y suites existentes son una buena base; deben quedar obligatoriamente conectados a la publicación y promoción de versiones. |
| [PHP: Supported Versions](https://www.php.net/supported-versions.php) | Ventanas oficiales de soporte y fin de vida por rama de PHP. | La versión del runtime es una dependencia crítica de seguridad y debe ser una condición explícita de soporte/despliegue. |

## Brechas priorizadas

“Diseñado” significa que una regla aparece en la arquitectura o sus documentos;
no significa que haya sido implementada, probada en HTTP o verificada en
producción.

| Prioridad | Área | Estado observado | Riesgo / diferencia con la práctica | Qué cerrar y evidencia de salida |
|---|---|---|---|---|
| **P0 · antes de producción** | Runtime PHP | `STATE.md` registra PHP 7.4.33. La rama 7.4 terminó soporte en 2022; la página oficial indica que las ramas fuera de soporte pueden quedar expuestas a vulnerabilidades sin parche. | Un diseño seguro no compensa un runtime sin correcciones de seguridad. | Definir la versión PHP soportada para producción y actualizar/validar el código en una rama actualmente soportada. Mantener pruebas de compatibilidad si 7.4 continúa como objetivo offline, sin depender de 7.4 en producción. Ejecutar suites y despliegue de prueba sobre la versión objetivo. |
| **P0 · puerta del plugin runtime** | Ejecución y confianza de plugins | Hay descubrimiento de manifiestos, huellas, aprobación y revisión de planes; no hay carga ejecutable ni rutas plugin HTTP. La especificación reconoce que PHP in-process no es un sandbox. | Un archivo aprobado puede ejecutar con los privilegios del proceso web. Hashes prueban igualdad de bytes con una referencia, no identidad del publicador, seguridad del código o aislamiento. | Definir explícitamente el modelo de amenaza: plugins revisados/trusted-code únicamente, o proceso separado con API estrecha para código no confiable. Establecer identidad de publicador, firma/verificación, procedencia/build reproducible según nivel de riesgo, revocación y actualización segura. Probar que nada ejecutable se carga antes de autorización. |
| **P0 · estado y persistencia** | Migraciones MSSQL e instalación | El paso 03 todavía requiere inspección física del esquema, estado instalado comprobable, ejecución ligada a aprobación, manejo de interrupciones y aprovisionamiento guardado. El paso 04 (slice MainAPI HTTP) sigue pendiente. | Revisar un plan no prueba el esquema instalado ni que una migración haya terminado. Una instalación ambigua puede producir drift, doble ejecución o estado falso. | Completar primero el inspector de huella física y rechazo de objetos inesperados; después persistir estado instalado y diseñar ejecución/reconciliación con aprobación fresca, concurrencia, postcondiciones, commit ambiguo y auditoría. Validar con SQL Server real en fixture propio y pruebas de fallo/reintento/race. No cambiar el receipt “unverified” por una etiqueta. |
| **P0 · puerta de producción** | Operación y recuperación | La evidencia MSSQL es acotada a laboratorios/fixtures; no se registran SLO/RPO/RTO de producto. `/health` es liveness constante, no readiness. Backups/restores y despliegue productivo permanecen sin validar. | No se puede afirmar confiabilidad o continuidad sin objetivos medibles, restauración probada y diagnóstico de fallos reales. | Acordar SLI/SLO de disponibilidad, p95/p99, errores y operaciones críticas; establecer RPO/RTO por datos del sitio; probar backup y restore en topología unificada y separada; distinguir liveness/readiness/dependency health; crear alertas, runbooks y ensayos de recuperación. |
| **P1 · antes de multi-instancia** | Despliegue y consistencia de plugins | El destino promete que la presencia física controla disponibilidad en cada request/worker. El plan menciona workers y OPcache, pero no existe aún una vista de despliegue que fije cómo se distribuyen carpetas y versiones en cada instancia. | Dos instancias pueden ver paquetes/versiones distintos; caché de opcode o workers persistentes pueden conservar código que ya no está vigente. Una copia parcial puede exponer un paquete incompleto. | Añadir diagrama C4 de despliegue por entorno. Definir artefacto inmutable, staging fuera del directorio escaneado, promoción atómica, sincronización multi-nodo, invalidación de OPcache/worker generation, rollback y política de disponibilidad durante despliegue. Probar dos workers y luego dos instancias si la topología las usa. |
| **P1 · seguridad de extensiones** | Permisos y límites de fallo | MainAPI y scopes son diseño; las reglas prohíben que el manifest se auto-otorgue permisos. La integración efectiva de rutas, AdminCP, eventos y jobs no existe. | Un error fatal, `exit`, bucle o consumo excesivo dentro de PHP puede afectar todo el proceso, aunque las rutas estén bien aisladas. Ver el menú oculto no constituye autorización. | Probar permisos en handlers, aislamiento de namespaces/rutas, rollback de registro parcial, timeout/limites y fallos explícitos. Mantener plugins en proceso solo si se consideran código confiable. Para código no confiable, usar proceso aislado o no permitir instalación. |
| **P1 · cadena de suministro** | Distribución de paquetes | Se valida un inventario SHA-256 del paquete; no se registra evidencia de una cadena de build/procedencia equivalente a SLSA ni un proceso de actualizaciones/revocación de publicadores completo. | Sustitución en origen, dependencia maliciosa o build comprometido puede producir bytes íntegros pero no confiables. | Especificar paquete reproducible/versionado, publicador y firma, atestación de build/procedencia, inventario de dependencias, análisis de vulnerabilidades, política de soporte, revocación y respuesta a CVE. El nivel de SLSA debe ser proporcional a la distribución y amenaza; no declarar un nivel sin cumplir sus requisitos. |
| **P1 · operación observable** | Métricas, logs y auditoría | Hay reglas de redacción de secretos/auditoría en varias áreas, pero el diagrama no define observabilidad común del kernel/plugin host ni indicadores de salud y latencia por contribución. | Sin correlación no se puede identificar si falló el kernel, un plugin, MSSQL, un proveedor de juego o un job; tampoco demostrar SLO ni acotar incidentes. | Definir logs estructurados redacted, request/correlation IDs, métricas de rutas y plugin, fallos de dependencia/registro/migración y jobs; definir retención, alertas y runbooks. Evitar PII, credenciales y blobs nativos en telemetría. |
| **P1 · datos y transacciones** | Topología y efectos entre bases | La arquitectura permite unificar o separar datos web/cuenta/juego. Los guardrails de mutaciones y pagos están especificados, pero el runtime de juego/pago y su reconciliación multi-sistema siguen ausentes. | Una transacción MSSQL no es una transacción distribuida ni sincroniza el servidor MU activo. Errores entre pago, estado web y entrega nativa pueden dejar operaciones pendientes. | Para cada flujo, declarar autoridad de datos, invariantes, idempotency key, estado pendiente, outbox/reconciliación/compensación y dueño de cada retry. Mantener gate nativo cerrado hasta pruebas reales de propiedad/offline/lock/recheck y recuperación. |
| **P1 · gobernanza de interfaces** | APIs y compatibilidad | Cuatro APIs están separadas conceptualmente; son especificaciones, no SDKs. V1–V5 permanecen congeladas. | Sin reglas probadas de deprecación, compatibilidad de versiones, dependencias, selección de proveedores y errores tipados, los paquetes pueden bloquear el sitio o romperse al actualizar el kernel. | Congelar cada SDK de forma aditiva, publicar matriz de compatibilidad kernel/API/plugin/PHP, probar colisiones/ciclos/proveedores ambiguos y mantener adaptadores para consumidores estáticos hasta demostrar equivalencia. |
| **P1 · ciclo de entrega** | CI y barreras de release | Existen scripts de verificación, suites amplias y documentación de límites; la protección de ramas/revisores no está configurada según el estado del proyecto. | Un comando disponible no impide publicar un artefacto si el flujo de CI o las reglas de merge no lo exigen. | Ejecutar fast/affected/complete en CI según alcance, incluir matriz del runtime soportado, arquitectura/contratos/paquetes, escaneo de dependencias y artefactos. Exigir checks y revisión en la plataforma Git antes de llamar a CI barrera de merge. |
| **P2 · alcance y producto** | Matriz de compatibilidad MU | La visión incluye Season 1–19+, varios emuladores, esquemas y datos binarios; la evidencia disponible es sintética/offline y no certifica compatibilidad universal. | “Autodetección universal” no es un criterio verificable sin una matriz por versión/esquema, muestras permitidas, capacidades y límites de soporte. | Convertir el objetivo en una matriz de versiones/emulador/capacidad con pruebas de lectura conocidas, incertidumbre explícita y soporte `unavailable` para layouts no verificados. Empezar por un adaptador MU documentado; no intentar todos los juegos/emuladores en la primera entrega. |
| **P2 · documentación de arquitectura** | Vistas y audiencias | Los diagramas actual/objetivo son árboles navegables más un flowchart Mermaid. | El treeview enseña jerarquía, pero no fronteras de seguridad, relaciones runtime, despliegue ni estado de una operación compleja. | Conservarlo como índice ejecutivo y añadir vistas C4 pequeñas: Contexto y Contenedores; una dinámica para añadir/quitar plugin y otra para migración; despliegue por entorno con trust boundaries. Mantener “actual” y “target” separados y fecharlos. |

## Qué ya está bien encaminado

- La arquitectura evita depender de código legacy en runtime y conserva módulos
  como islas transicionales hasta migrarlos con evidencia.
- Las responsabilidades de juego, motor de base de datos, esquema/emulador y
  persistencia MSSQL del sitio están diferenciadas.
- La ausencia de carpeta debe revocar código aunque la base guarde aprobación;
  la remoción no debe borrar datos.
- Los manifiestos se descubren como datos y no deben ejecutar PHP. La aprobación
  de código se separa de la revisión/ejecución de migraciones.
- Las escrituras de juego, pagos, plantillas y restablecimiento del sitio tienen
  barreras explícitas; las pruebas sintéticas no se presentan como integración
  real.
- Los contratos y suites existentes dan una base valiosa para evolucionar sin
  reemplazar todo por microservicios.

Esto es buen diseño preventivo, pero los estándares mencionados se aplican a
resultados y evidencias; tener la política escrita no demuestra que el control
funcione bajo carga, concurrencia, despliegue o recuperación.

## Orden recomendado para cerrar las brechas

1. **Compatibilidad segura del entorno:** decidir el PHP de producción actualmente
   soportado; ejecutar el proyecto en esa versión y dejar explícita cualquier
   compatibilidad 7.4 que solo se conserve para pruebas/offline.
2. **Terminar el paso 03 tal como marca `STATE.md`:** inspección física del esquema
   MSSQL, estado instalado confiable, ejecución/reconciliación de migraciones y
   aprovisionamiento autorizado. No mezclar esto con activar plugins.
3. **Construir el primer slice MainAPI (paso 04):** plugin read-only independiente,
   servidor HTTP ya activo, añadir → aprobar → ruta funciona → quitar carpeta →
   siguiente request deja de servirla → reponer versión aprobada; kernel,
   AdminCP y plugin independiente siguen funcionando. Repetir en workers
   independientes y conservar configuración.
4. **Cerrar el plano de control y operación:** UI AdminCP, permisos, auditoría,
   observabilidad, SLO, despliegue, actualización/rollback, backups y restore.
5. **Agregar providers uno a uno:** primero DatabaseType solo lectura, después
   GameType MU, luego un SchemaAdapter verificado y finalmente un consumidor como
   rankings. Cada proveedor debe probar su propia ausencia sin tumbar el kernel.
6. **Migrar capacidades y habilitar efectos sensibles por separado:** demostrar
   equivalencia de módulos, comercio/pagos, escrituras nativas, GrapesJS e
   instalación/reset en sus propios gates; no activarlos solo porque el SDK o
   el diagrama los incluya.

**No se recomienda ahora:** cambiar a microservicios, añadir colas obligatorias,
Kubernetes o multi-región sin SLO, carga y necesidad operativa que los justifiquen.
Primero se debe lograr una plataforma monolítica modular, observable, actualizable
y recuperable con fallos bien delimitados.

Para sugerencias concretas de diseño, TDD, pruebas, fitness functions, migraciones
evolutivas y confiabilidad, ver
[Métodos de ingeniería recomendados](ENGINEERING_METHODS.md). Son propuestas,
no controles ya implantados.

La [síntesis contrastada de la revisión externa](EXTERNAL_ARCHITECTURE_REVIEW.md)
separa hallazgos confirmados de recomendaciones que requieren evidencia o
decisión del propietario.

## Vista arquitectónica que falta

Al próximo diagrama de arquitectura convendría añadir estas tres vistas (además
del árbol de componentes):

1. **Contexto y contenedores:** administrador, visitante, kernel PHP, plugins,
   MSSQL del sitio, datos de cuenta/juego, Drupal, almacenamiento de assets y
   servicios externos; señalar conexiones, credenciales y límites de confianza.
2. **Dinámica:** aprobación/activación y migración autorizada; request durante
   remoción física; recuperación tras fallo/commit ambiguo.
3. **Despliegue:** local/offline, staging y producción; proxy/TLS, web nodes,
   directorio inmutable de plugins, worker(s), MSSQL, secretos y backups; describir
   qué se comparte y cómo se promueve/retira una versión.

## Fuentes consultadas

- [AWS Well-Architected Framework: seis pilares](https://docs.aws.amazon.com/wellarchitected/latest/framework/the-pillars-of-the-framework.html)
- [Azure Well-Architected Framework](https://learn.microsoft.com/en-us/azure/well-architected/)
- [C4 Model: diagramas estáticos y de apoyo](https://c4model.com/diagrams), [dinámicos](https://c4model.com/diagrams/dynamic), [despliegue](https://c4model.com/diagrams/deployment)
- [CNCF Platforms White Paper](https://tag-app-delivery.cncf.io/whitepapers/platforms/)
- [NIST SP 800-218 / SSDF 1.1](https://csrc.nist.gov/Projects/ssdf)
- [OWASP ASVS](https://owasp.org/www-project-application-security-verification-standard/)
- [SLSA 1.1](https://slsa.dev/spec/v1.1/)
- [Google SRE: Service Level Objectives](https://sre.google/sre-book/service-level-objectives/)
- [DORA: Continuous Integration](https://dora.dev/capabilities/continuous-integration/)
- [PHP supported versions](https://www.php.net/supported-versions.php)

La comparación refleja las páginas oficiales consultadas en la fecha indicada;
los marcos se usan como guías de evaluación, no como una certificación de
ZendaMod Next.
