# Order API

> **Ley del Orden:** un propósito que ayuda a muchos propósitos.
>
> **Único propósito:** **gobernar la interoperabilidad y ejecución confiable de capacidades mediante contratos verificables**.
>
> **Estado:** especificación de arquitectura `1.0.0`; contrato central de las tres APIs.
> **Perfil de implementación de ZendaMod-Next:** [integración y límites actuales](WEBSITE_INTEGRATION.md). Las operaciones generales siguientes son el diseño objetivo; consultar STATE.md para evidencia real.
>
> **Documentos relacionados:** [Chaos API](CHAOS.md) · [Singularity API](SINGULARITY.md).

## 1. Propósito, regla central y alcance

Order API es la **puerta de entrada gobernada** del sistema. No implementa lógica de negocio especializada ni pretende resolver todos los problemas. Su propósito único es hacer que capacidades independientes puedan descubrirse, verificarse, combinarse y ejecutarse de manera confiable.

El registro, la autorización, la validación, la observabilidad y el control de ejecución son **mecanismos auxiliares de ese propósito único**, no excusas para convertir Order en un objeto gigante que absorba todas las funcionalidades.

### Responsabilidades obligatorias

1. **Registro y descubrimiento:** mantener identificadores estables y versiones de capacidades realmente instaladas y habilitadas.
2. **Verificación de contratos:** comprobar entradas, salidas, permisos, efectos, límites y compatibilidad declarada.
3. **Resolución:** localizar la implementación adecuada mediante ID y versión sin hardcodear URLs, nombres de archivos o proveedores en el llamador.
4. **Autorización:** decidir si un actor autenticado puede ejecutar una capacidad concreta sobre un recurso concreto.
5. **Invocación:** aplicar políticas y delegar la ejecución al adaptador apropiado, normalmente un handler de Singularity.
6. **Orquestación validada:** ejecutar planes acotados y dependencias declaradas, sin interpretar expresiones arbitrarias.
7. **Control de recursos:** aplicar plazos, cuotas, concurrencia, límites de tamaño y cancelación cooperativa cuando exista.
8. **Integridad:** gestionar claves de idempotencia, límites transaccionales y reglas de reintento.
9. **Observabilidad:** correlacionar trazas, métricas, errores y auditorías sin filtrar secretos.
10. **Ciclo de vida:** instalar, habilitar, suspender, actualizar y retirar proveedores sin corromper el núcleo.
11. **Evolución de contratos:** aceptar nuevas capacidades únicamente después de revisión, pruebas y aprobación conforme a políticas.

### Prohibiciones

- No añadir dentro del núcleo reglas particulares de MU Online, comercio, matemáticas, modelos concretos de IA, Metin2 ni otros dominios.
- No evaluar código del usuario como implementación de una capacidad; sin `eval`, SQL arbitrario, carga dinámica desde una URL o shells genéricas.
- No permitir que Chaos, un plugin o un modelo de IA cambien sus propios permisos.
- No convertir el registro de servicios en un mecanismo para descubrir por ensayo y error redes o recursos no autorizados.
- No confiar en el manifiesto de un plugin como prueba de seguridad: describe intenciones, no aislamiento real.

## 2. Mapa arquitectónico

```text
        Entradas conocidas           Objetivos abiertos
                |                          |
                |                      Chaos API
                |                     (planifica)
                |                          |
                +-------------+------------+
                              |
                         ORDER API
             (único punto de acceso gobernado)
                              |
       +----------+-----------+-----------+
       |          |           |           |
    Registry    Policy     Executor    Observability
       |          |           |           |
       +----------+-----------+-----------+
                              |
                Adaptadores por contrato
                              |
                 SINGULARITY HANDLERS
                 /       |        \
             math.add  records.get  auth.verify
                              |
                 Resultados / eventos
                              |
                         ORDER API
                              |
                       Consumidores
```

**Implementación física:** los bloques pueden estar en un mismo proceso. No hace falta introducir HTTP, contenedores o colas para cada operación matemática. Utilizar otros procesos solamente cuando lo justifiquen el aislamiento, la disponibilidad o la carga.

## 3. Contrato canónico de capacidad

Toda capacidad publicable, incluso si se ejecuta dentro de un módulo mayor, necesita un manifiesto validado. Este contrato pertenece a Order; Singularity lo implementa y Chaos lo consulta.

### Ejemplo completo: `math.add`

```json
{
  "contract_schema": "1.0",
  "id": "math.add",
  "version": "1.0.0",
  "owner": "core.math",
  "kind": "singularity",
  "purpose": "Sumar exactamente dos operandos numéricos",
  "input_schema": {
    "type": "object",
    "additionalProperties": false,
    "required": ["a", "b"],
    "properties": {
      "a": {"type": "number"},
      "b": {"type": "number"}
    }
  },
  "output_schema": {
    "type": "object",
    "additionalProperties": false,
    "required": ["value"],
    "properties": {"value": {"type": "number"}}
  },
  "effects": "none",
  "required_permissions": [],
  "idempotent": true,
  "cache_policy": "eligible",
  "timeout_ms": 1000,
  "max_input_bytes": 8192,
  "data_classification": "public"
}
```

**Precisiones de tipos:** el ejemplo de `number` es ilustrativo. Para valores financieros, enteros muy grandes o aritmética decimal exacta, utilizar contratos tipados como `decimal-string` con precisión y reglas de redondeo explícitas. Rechazar `NaN` e infinitos y validar desbordamientos en los lenguajes que lo permitan. JSON no garantiza por sí solo aritmética exacta.

### Campos mínimos y semántica

| Campo | Obligación |
|---|---|
| `id` | Identificador estable `dominio.acción`, único en el registro. |
| `version` | Versión semántica de contrato e implementación publicada. |
| `owner` | Proveedor identificado y autorizado por el administrador. |
| `kind` | Tipo de capacidad (por ejemplo, `singularity` o `adapter`). |
| `purpose` | Responsabilidad funcional legible y comprobable. |
| `input_schema` / `output_schema` | Validaciones estrictas y compatibles entre sí. |
| `effects` | `none`, `read`, `write` o `external`; no se presupone ausencia de efectos. |
| `required_permissions` | Permisos mínimos evaluados por Order; una lista vacía **no anula la autenticación y políticas generales**. |
| `idempotent` | Describe si repetir la solicitud conserva el efecto lógico; no garantiza exactamente-una-vez. |
| `timeout_ms` | Límite superior sujeto al techo del servidor. |
| `data_classification` | Clasificación utilizada para retención, auditoría y exposición. |

Extensiones opcionales versionadas: `resource_policy`, `supported_modes`, `compensation`, `event_types`, `deprecation`, `examples`. Los campos desconocidos del manifiesto se rechazan o se interpretan únicamente si pertenecen a una extensión negociada.

### Reglas de compatibilidad

- Nueva funcionalidad compatible: nueva versión menor `MINOR` si mantiene los contratos vigentes.
- Corrección sin cambio externo: versión de parche `PATCH`.
- Eliminar campos, cambiar su significado o endurecer entradas previamente válidas: versión mayor `MAJOR`, salvo que un contrato preexistente ya lo permitiera.
- Los planes y las artes en producción fijan siempre versiones exactas (id@major.minor.patch); Order jamás resuelve ni actualiza silenciosamente hacia una versión distinta sin migración explícita del consumidor.
- Mantener adaptadores de transición mientras existan consumidores admitidos; probar migraciones y rollback cuando sea posible.

## 4. Contrato de invocación

El actor y los datos de seguridad **se derivan del contexto autenticado del servidor**, no de campos arbitrarios enviados por el cliente.

### Petición pública ilustrativa

```json
{
  "request_id": "request-001",
  "capability": "math.add",
  "version": "1.0.0",
  "args": {"a": 2, "b": 3},
  "idempotency_key": null,
  "constraints": {"deadline_ms": 1500}
}
```

### Respuesta exitosa

```json
{
  "request_id": "request-001",
  "status": "success",
  "capability": "math.add",
  "version": "1.0.0",
  "result": {"value": 5},
  "meta": {"duration_ms": 2, "trace_id": "trace-001"}
}
```

### Respuesta de error

```json
{
  "request_id": "request-001",
  "status": "error",
  "error": {
    "code": "PERMISSION_DENIED",
    "message": "La operación no está autorizada",
    "retryable": false
  },
  "meta": {"trace_id": "trace-001"}
}
```

**Catálogo mínimo de errores:** `INVALID_INPUT`, `AUTHENTICATION_REQUIRED`, `PERMISSION_DENIED`, `CAPABILITY_UNAVAILABLE`, `VERSION_NOT_SUPPORTED`, `CONTRACT_VIOLATION`, `RATE_LIMITED`, `TIMEOUT`, `CONFLICT`, `DEPENDENCY_UNAVAILABLE`, `INTERNAL_ERROR`.

Los mensajes expuestos no deben mostrar consultas SQL, rutas internas, claves, trazas de pila ni datos de otros usuarios.

## 5. Interfaz lógica de Order API

| Operación | Resultado esperado | Requisito central |
|---|---|---|
| `order.list_capabilities` | Lista filtrada por identidad/permisos | no revelar capacidades privadas |
| `order.describe` | Manifiesto público permitido | ID + versión |
| `order.resolve` | Handler autorizado desde registro | sin URL hardcodeada en el consumidor |
| `order.invoke` | Resultado validado de una operación | controles previos y posteriores |
| `order.submit_plan` | Ejecución acotada de un DAG válido | verificaciones por paso |
| `order.extension_propose` | Solicitud de cambio en cuarentena | no instala ni activa |
| `order.extension_approve` | Aprobación administrativa auditada | identidad con permiso explícito |
| `order.events.subscribe` | Suscripción contractual autorizada | control de efectos y carga |

`order.authorize`, `order.audit` y `order.registry.install` suelen ser interfaces **internas o administrativas**, no APIs públicas de libre invocación. La separación evita transformar la capa de seguridad en un servicio que cualquier plugin pueda reconfigurar.

## 6. Algoritmo obligatorio de invocación

```text
order.invoke(petición, contexto_autenticado):
  1. verificar identidad del servidor, ámbito y cuota global
  2. localizar ID + versión exacta (x.y.z) habilitada desde registro confiable
  3. verificar estado de habilitación opcional (la configuración solo enciende o
     apaga; si está apagado -> CAPABILITY_UNAVAILABLE)
  4. comprobar permisos explícitos del actor sobre operación Y recurso/tenant
     (la configuración jamás otorga permisos)
  5. verificar entrada cerrada (esquema estricto, tamaño, tipos, límites de
     dominio, cero carga ejecutable)
  6. verificar efecto permitido (none | read | write | external), plazo e idempotencia
  7. asignar traza y contexto de ejecución de mínimo privilegio al handler de Singularity
  8. ejecutar a través del adaptador apropiado con límites efectivos
  9. validar la salida contra su contrato; filtrar datos protegidos
 10. registrar auditoría/medición mínima y devolver respuesta tipada
```

**Autorización por recurso:** que un usuario pueda invocar `records.get_by_id` no significa que pueda leer cualquier `record_id`. El controlador debe evaluar también propiedad, tenant o regla de acceso al objeto solicitado. El adaptador de datos debe usar consultas parametrizadas y privilegios mínimos. Un manifiesto no reemplaza esas verificaciones.

### Reglas de ejecución de planes

- Un plan es un **DAG** con límite de pasos y profundidad; rechazar ciclos y referencias inexistentes.
- Order valida cada capacidad y permiso al **momento de ejecutar cada paso**, no solamente al iniciar el plan.
- Las referencias entre pasos se resuelven mediante rutas tipadas de un conjunto permitido, jamás con código evaluado.
- Las tareas independientes pueden correr en paralelo, pero se limita concurrencia para evitar agotar recursos.
- Un fallo detiene los pasos dependientes; las acciones ya realizadas no se revierten mágicamente.
- Para procesos con varios efectos se requieren transacciones locales o un patrón *saga* con compensaciones probadas; si no existe compensación, informar el estado parcial.
- Una acción potencialmente irreversible nunca se repite automáticamente tras una respuesta ambigua sin un mecanismo seguro de reconciliación.

## 7. Plugins, APIs y descubrimiento sin rutas rígidas

Estructura de referencia **opcional** (el diseño no depende del lenguaje ni del framework):

```text
app/
  core/
    order/
      contracts/
      registry/
      policy/
      executor/
      lifecycle/
      telemetry/
    chaos/               # motor intercambiable de planificación
  plugins/
    math/
      plugin.json
      capabilities/
        add.json
        divide.json
      handlers/
        ...
    records/
      plugin.json
      capabilities/
        get-by-id.json
      handlers/
        ...
```

### Ciclo de vida

1. **Discover:** el proceso confiable busca manifiestos en directorios autorizados, sin ejecutar código de plugins.
2. **Validate:** comprobar formato, ID, dependencias, versiones, nombres únicos y políticas.
3. **Trust:** verificar procedencia, revisión/aprobación y firmas si existe infraestructura de firma; una firma no prueba ausencia de vulnerabilidades.
4. **Activate:** cargar solamente proveedores aprobados, en el nivel de aislamiento apropiado y con capacidades explícitas.
5. **Operate:** exponer operaciones mediante el registro, medir fallos y limitar su impacto.
6. **Disable / remove:** dejar de resolver sus operaciones y desactivar sus tareas/hook; devolver `CAPABILITY_UNAVAILABLE` a los consumidores afectados.
7. **Preserve:** conservar los datos persistentes del plugin para recuperación; eliminarlos requiere un proceso administrativo de desinstalación/migración aparte.

Eliminar una carpeta **no debe romper el arranque del núcleo**. Los consumidores deben degradarse de manera controlada, no suponer que los datos de ese plugin desaparecieron. No es seguro cargar código PHP/JS/Python no confiable simplemente porque esté dentro de `/plugins`; los módulos con código arbitrario requieren revisión y, para terceros no confiables, aislamiento real fuera del proceso privilegiado.

### Rutas y conectores

- Consumidores piden `order.invoke("math.add", ... )` en lugar de importar clases internas o llamar URLs fijas.
- Las URLs/credenciales reales permanecen en configuración segura del adaptador del proveedor.
- Un proveedor caído genera un error tipado; no se resuelve mediante búsquedas arbitrarias en Internet.
- Un conector específico para MSSQL, MySQL u otros motores oculta su transporte, **sin inventar equivalencia de esquemas** ni eliminar las migraciones necesarias.

## 8. Caché, eventos e integridad

**Caché.** Solo aplicar si el contrato permite cachear y la clave incorpora entradas relevantes, ID y versión de contrato, tenant, visibilidad y contexto de autorización cuando corresponda. La expiración e invalidación deben corresponder a las dependencias reales. No cachear respuestas sensibles en un contexto compartido no autorizado.

**Eventos.** Los eventos llevan `event_id`, `type`, `version`, `occurred_at`, `producer`, `correlation_id` y carga validada. Se publican tras confirmar la transacción relevante, idealmente mediante *transactional outbox*. Los consumidores deben tolerar duplicados y retrasos; entrega *at-least-once* **no** equivale a procesamiento exactamente una vez.

**Idempotencia.** Para escrituras, utilizar claves scoped por actor, tenant, operación y argumentos normalizados, almacenadas en un repositorio duradero. Si una petición llega dos veces, reconciliar y devolver el mismo resultado lógico en vez de ejecutar dos veces una compra. Esto depende del adaptador y almacenamiento; una bandera `idempotent: true` por sí sola no implementa nada.

**Observabilidad.** Métricas por operación y versión, latencia p50/p95/p99, tasa de errores, colas, eventos descartados y rechazos por política. Trazar con IDs de correlación, redacción de secretos y límites de retención. No registrar tokens, contraseñas o datos innecesarios.

## 9. Seguridad no negociable

- Denegar por defecto; autenticación y autorización independientes.
- El contexto de identidad es del servidor y no puede ser falsificado por un plan.
- Operaciones y recursos autorizados con privilegios mínimos; separar administración del uso normal.
- Validación estricta de entrada **y** salida; protección contra inyección en los adaptadores.
- Límites de memoria, concurrencia y tiempo; un timeout lógico no detiene por sí solo un proceso bloqueado: aislar lo que requiera corte forzoso.
- Aislar proveedores no confiables; limitar acceso a red, archivos y credenciales según la infraestructura real.
- Evitar un único punto de fallo mediante diseño apropiado de despliegue (sin exigir distribución prematura).
- Configuración y secretos fuera del código y fuera de los manifiestos públicos.
- Cambios de políticas y permisos con auditoría, pruebas y autorización administrativa explícita.

## 10. Evolución continua: protocolo de nueva API

Si Chaos descubre un requisito que ninguna capacidad satisface:

```text
Chaos: capability_gap
   -> Order: propuesta pendiente + verificación de duplicados
   -> revisión técnica y de seguridad
   -> nuevo manifiesto + handler + pruebas de contrato
   -> pruebas de compatibilidad, rendimiento, fallo y permisos
   -> aprobación de despliegue y migración planificada
   -> registro de ID/versión e integración
   -> Chaos descubre nueva capacidad
```

**La propuesta no es código con permiso de ejecución.** Los agentes pueden generar una implementación candidata en un entorno de desarrollo restringido; la promoción a producción es una decisión independiente que requiere controles de calidad y autorización.

## 11. Criterios mínimos de aceptación

- [ ] Un consumidor invoca por ID y versión, sin conocer la ubicación del handler.
- [ ] Los manifiestos inválidos se aíslan sin derribar todo el sistema.
- [ ] Permisos se verifican por operación y recurso, también durante los planes.
- [ ] Entradas y salidas cumplen contratos; errores son estables y no exponen secretos.
- [ ] Deshabilitar o retirar un plugin no rompe el núcleo ni borra sus datos implícitamente.
- [ ] Un plugin nuevo no se ejecuta ni obtiene privilegios sin aprobación.
- [ ] Reintentos seguros, idempotencia y fallos parciales tienen semántica documentada.
- [ ] Versiones incompatibles no se adoptan en silencio.
- [ ] La vía sin IA puede llamar Order directamente.
- [ ] Se miden rendimiento real y errores bajo carga, en lugar de presuponer eficiencia por modularidad.

## 12. Invariantes de implementación

1. **Order tiene un propósito: interoperabilidad confiable bajo contratos.**
2. **Todas las capacidades ejecutables atraviesan políticas equivalentes de Order**, sea una llamada local u otra remota.
3. **Ni Chaos ni Singularity pueden concederse permisos.**
4. **Nunca se ejecuta código generado como sustituto de una capacidad ausente sin aprobación.**
5. **Versiones, contratos, permisos y transacciones importan más que la organización física de carpetas.**
6. **Los cambios deben preservar compatibilidad explícita o declarar una migración.**

## 13. Ladrillos y artes en el registro

Order es el punto donde la distinción entre **ladrillos** (*building blocks*) y
**artes** (*crafts*) se vuelve gobernable: **el registro de Order custodia los
ladrillos inmutables, mientras que las artes se ensamblan y ejecutan a través de sus
contratos** (ver [principios evolutivos](../../EVOLUTION_PRINCIPLES.md) §0.0 y
[BUILDING_BLOCKS.md](BUILDING_BLOCKS.md)).

1. **El registro es estable a propósito.** Un ladrillo registrado conserva su `id`
   y su versión exacta (`x.y.z`) mientras su comportamiento declarado no cambie.
   Order no reorganiza ni muta el catálogo porque una arte (`webshop`, `AdminCP`,
   plantilla viva, `rankings`, mercado, soporte) cambie de diseño o flujo.
2. **Una arte nueva nunca deforma un ladrillo existente.** Si una arte necesita una
   capacidad que ningún ladrillo actual resuelve, se abre un `capability_gap`
   (justificado cuando dos artes demandan la misma primitiva o por mandato
   explícito de diseño). Se aprueba y registra un ladrillo nuevo con su propio
   contrato y versión; jamás se ensancha un ladrillo en uso para acomodar casos
   particulares de una arte.
3. **La versión exacta es el contrato de estabilidad.** Cada arte o plan invoca
   ladrillos mediante su versión exacta (`dominio.acción@major.minor.patch`). La
   migración de `x@1.0.0` a `x@2.0.0` es una decisión explícita y verificada por
   consumidor; Order nunca sustituye versiones de forma implícita.
4. **La configuración solo habilita; jamás autoriza.** Un interruptor de
   configuración (como `feature.resolve@1.0.0`) únicamente enciende o apaga un
   camino funcional cuyos ladrillos y políticas ya están registrados y protegidos.
   Una preferencia activa no crea autoridad, no agrega permisos, no reemplaza la
   validación de recurso/tenant del handler y no abre acceso a MSSQL ni a
   capacidades nativas de juego o pago. Deshabilitar una preferencia devuelve
   `CAPABILITY_UNAVAILABLE` sin destruir datos ni contratos.
5. **El poder real permanece en las compuertas del núcleo.** La identidad separada
   de administrador del sitio, la autorización *server-side* por operación y
   recurso, la pasarela de mutación auditada y el aislamiento de juego/pago se
   aplican invariablemente a toda arte habilitada.
**Definición final:** Order API es el mecanismo común de contratos verificables,
fijación exacta de versiones y ejecución gobernada que permite que múltiples artes
maleables evolucionen sobre ladrillos inmutables sin acoplamientos directos,
privilegios implícitos ni dependencia obligatoria de IA.