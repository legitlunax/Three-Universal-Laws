# Singularity API

> **Ley de la Singularidad:** un único propósito y ninguna responsabilidad adicional.
>
> **Propósito:** definir y ejecutar operaciones especializadas con entradas, resultados, permisos y efectos delimitados.
>
> **Estado:** especificación de arquitectura `1.0.0`.
> **Documentos relacionados:** [Chaos API](Chaos%20API.md) · [Order API](Order%20API.md).

## 1. Definición

En esta arquitectura, Singularity API es el **conjunto de contratos y handlers de propósito único**. No significa que deba existir un microservicio por función. Una misma biblioteca o plugin puede contener muchas singularidades independientes, cada una definida por su **única responsabilidad observable**.

Por ejemplo, `math.add` suma dos valores. No redondea, no busca resultados en la web, no modifica usuarios y no elige qué herramienta ejecutar. Por el contrario, un módulo `math` puede publicar `math.add`, `math.divide` y `math.round_decimal` como tres capacidades separadas.

**Relación con las otras leyes:** Chaos propone distintas soluciones; Order decide si es válido ejecutarlas y cómo; Singularity ejecuta un comportamiento concreto y devuelve un resultado verificable. Singularity también funciona sin Chaos cuando un programa conoce de antemano la operación que necesita.

## 2. Responsabilidades de cada singularidad

1. **Declarar su propósito único** mediante un ID inmutable por contrato (`dominio.acción`) y una descripción operacional.
2. **Definir entradas y salidas** con tipos estrictos, campos obligatorios, límites y errores especificados.
3. **Ejecutar exactamente su operación** sin coordinar otras capacidades ni tomar decisiones de política global.
4. **Declarar y respetar efectos secundarios** (`none`, `read`, `write`, `external`) y permisos mínimos.
5. **Validar condiciones de dominio** incluso si Order realizó validación de esquema (defensa en profundidad).
6. **Gestionar recursos propios** de forma controlada: consultas parametrizadas, límites de datos, operaciones acotadas.
7. **Admitir pruebas independientes**, incluyendo casos normales, extremos, errores y concurrencia cuando corresponda.
8. **Devolver datos estructurados** y errores tipados, nunca excepciones internas o mensajes que filtren secretos.
9. **Mantener compatibilidad de contrato** o publicar una versión mayor cuando cambie su comportamiento observable.

### Lo que Singularity no debe hacer

- Planificar objetivos abiertos o inventar otras capacidades.
- Descubrir plugins, autorizarse a sí misma o modificar el registro de Order.
- Hacer llamadas arbitrarias a otras funciones registradas para ocultar un flujo multipropósito.
- Interpretar texto o código proporcionado por el usuario como órdenes del sistema.
- Obtener privilegios o acceso a red, disco, datos o secretos que no necesite.
- Compartir estado mutable global entre tenants sin aislamiento y controles de concurrencia.

## 3. Granularidad correcta: ni gigante ni microscópica

Una singularidad debe responder claramente a **una pregunta de negocio o computación**. No es obligatorio dividir cada línea de código en una API.

| Diseño | ¿Admisible? | Motivo |
|---|---|---|
| `math.add(a,b)` | Sí | una operación matemática concreta |
| `records.get_by_id(id)` | Sí, con reglas explícitas | una consulta delimitada; requiere autorización por registro |
| `auth.verify_signature(message, signature, key_id)` | Sí | una verificación criptográfica delimitada |
| `user.update_profile(user_id, fields)` | Puede serlo | un cambio de dominio coherente y autorizado |
| `universal.execute_anything(code)` | No | ejecución arbitraria, propósito indefinido y peligroso |
| `database.run_sql(query)` expuesto a plugins | No | rompe el límite de operación y facilita accesos indebidos |
| `commerce.process_anything(...)` | No | esconde responsabilidades diferentes bajo un nombre genérico |
| `math.add.validate_integer()` como servicio remoto separado | Normalmente no | sobrefragmentación sin beneficio; es un detalle interno |

**Regla de diseño:** separar capacidades cuando tengan permisos, efectos, contratos, tasas de cambio o necesidades de escalado diferentes. Mantener helpers privados dentro del handler cuando forman parte del mismo propósito.

## 4. Contrato de operación

Todas las capacidades publicadas por Singularity usan el **manifiesto canónico de Order API**. Los siguientes campos no son optativos en su comportamiento:

```json
{
  "contract_schema": "1.0",
  "id": "math.divide",
  "version": "1.0.0",
  "owner": "core.math",
  "kind": "singularity",
  "purpose": "Dividir un número por otro, rechazando divisor cero",
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

Este contrato no oculta reglas matemáticas: especifica cómo se comunican las piezas. Para operar con moneda, precisión decimal o enteros arbitrariamente grandes, usar un contrato diferente con representación exacta y semántica documentada.

### Contrato de ejecución interno

```text
Handler.run(input_validado, execution_context_limitado)
  -> success(output_tipado)
  -> error(codigo_estable, retryable)
```

`execution_context_limitado` puede contener una identidad de recurso ya autorizada, plazo efectivo, identificador de traza y acceso explícito a dependencias permitidas. **No** debe contener el token administrador global o una conexión privilegiada universal.

## 5. Ejemplo implementable: matemáticas

Este ejemplo usa Python estándar con números de precisión flotante ordinaria; no debe emplearse para importes monetarios exactos.

```python
import math


class DomainError(ValueError):
    pass


def _finite_number(value):
    # bool hereda de int en Python, pero no es un operando numérico válido aquí.
    if isinstance(value, bool) or not isinstance(value, (int, float)):
        raise DomainError("INVALID_NUMBER")
    try:
        converted = float(value)
    except (OverflowError, ValueError):
        raise DomainError("INVALID_NUMBER") from None
    if not math.isfinite(converted):
        raise DomainError("INVALID_NUMBER")
    return converted


def math_add(args: dict) -> dict:
    """Único propósito: sumar exactamente dos operandos finitos."""
    a = _finite_number(args["a"])
    b = _finite_number(args["b"])
    result = a + b
    if not math.isfinite(result):
        raise DomainError("NUMERIC_OVERFLOW")
    return {"value": result}


def math_divide(args: dict) -> dict:
    """Único propósito: dividir dos operandos finitos."""
    a = _finite_number(args["a"])
    b = _finite_number(args["b"])
    if b == 0:
        raise DomainError("DIVISION_BY_ZERO")
    result = a / b
    if not math.isfinite(result):
        raise DomainError("NUMERIC_OVERFLOW")
    return {"value": result}
```

Cada función tiene un propósito propio. El código supone que Order comprueba las claves obligatorias y el esquema antes de invocarla. La integración debe mapear `DomainError` a respuestas tipadas, no exponer excepciones sin procesar. La conversión a `float` puede perder precisión, por lo que no se debe declarar exactitud decimal.

### Flujo matemático compuesto

Solicitud: «promedio de 4, 8 y 12».

```text
Chaos (o un programa determinista) propone:
  s1 -> math.add(4, 8)  = 12
  s2 -> math.add(12, 12) = 24
  s3 -> math.divide(24, 3) = 8

Order comprueba contratos, permisos, dependencias, límites y resultados.
Singularity realiza s1, s2, s3 como operaciones separadas.
```

**Importante:** Singularity **no** implementa un «promediador universal» a escondidas para coordinar otras operaciones, salvo que `math.mean` se publique como una capacidad independiente con su propio contrato y propósito específico.

## 6. Ejemplo implementable: consulta segura

Este ejemplo recoge el patrón de búsqueda por clave primaria del proyecto y diferencia **validar una entrada** de **autorizar el acceso al registro**.

```python
def records_get_by_id(connection, record_id: int):
    """Único propósito: recuperar una nota por clave primaria."""
    if isinstance(record_id, bool) or not isinstance(record_id, int) or record_id <= 0:
        raise ValueError("INVALID_RECORD_ID")

    return connection.execute(
        "SELECT id, title, body FROM notes WHERE id = ?",
        (record_id,),
    ).fetchone()
```

**Condiciones indispensables:**

- Order debe autenticar, autorizar y delimitar el recurso/tenant para el actor.
- La consulta real debe aplicar el filtro de acceso/tenant en el nivel adecuado, o usar políticas de seguridad de filas confiables; no basta con que el llamador declare que ya verificó permisos.
- El ejemplo usa una sintaxis de parámetros soportada por algunos controladores; cada adaptador empleará la de su driver.
- `notes.id` debe estar indexado o ser clave primaria para que exista la ventaja esperada.
- El handler devuelve un registro o `null` conforme a contrato; nunca expone una conexión SQL al consumidor.

### Diseño más seguro en sistemas multi-tenant

Preferir una consulta que incluya explícitamente el límite de tenant, con identidad derivada del contexto autenticado:

```python
def records_get_for_tenant(connection, record_id: int, tenant_id: int):
    if (
        isinstance(record_id, bool)
        or not isinstance(record_id, int)
        or record_id <= 0
        or isinstance(tenant_id, bool)
        or not isinstance(tenant_id, int)
        or tenant_id <= 0
    ):
        raise ValueError("INVALID_IDENTIFIER")

    return connection.execute(
        "SELECT id, title, body FROM notes WHERE id = ? AND tenant_id = ?",
        (record_id, tenant_id),
    ).fetchone()
```

`tenant_id` debe provenir del contexto de confianza; esta consulta no sustituye permisos adicionales por propietario o rol si la política los exige.

## 7. Interacción con las otras APIs

```text
            Chaos API
      (elige entre alternativas)
                 |
       plan/solicitud de capacidad
                 v
             Order API
   autoriza -> resuelve -> valida
                 |
                 v
          Singularity API
          [handler específico]
                 |
           resultado tipado
                 v
             Order API
    verifica salida + audita
                 |
        consumidor / Chaos
```

**Invariantes de llamadas:**

- Un handler no llama directamente a otro handler por ruta de archivo o URL.
- Si necesita otra capacidad, se recomienda rediseñar la responsabilidad o solicitar una invocación explícita mediada por Order, controlada en profundidad y con política de dependencia. Evitar ciclos.
- Un consumidor que conoce el ID puede invocar Order sin usar Chaos.
- Un handler de Singularity nunca decide autorizar una operación global; sí verifica sus propias precondiciones de dominio.

## 8. Estados, efectos, errores y reintentos

- **Operaciones puras:** mismas entradas válidas → mismo resultado bajo la misma versión y semántica numérica; son buenas candidatas a caché.
- **Lecturas:** pueden cambiar con el estado; los permisos y el tenant forman parte de la semántica, aunque la operación no modifique datos.
- **Escrituras:** requieren límite transaccional, permisos sobre el objeto y una estrategia explícita de idempotencia.
- **Efectos externos:** pagos, emails y APIs remotas requieren reconciliación ante resultados ambiguos y no garantizan rollback.
- **Errores de dominio:** `DIVISION_BY_ZERO`, `INVALID_RECORD_ID` y similares pueden mapearse a `INVALID_INPUT` con un subcódigo estable.
- **Errores operativos:** timeout o dependencia caída tienen políticas de reintento limitadas y solo si resultan seguros para esa operación.

No es correcto prometer que «single purpose» elimina fallos: una función especializada mal programada también puede contener vulnerabilidades o ser lenta.

## 9. Seguridad y mantenimiento

1. **Mínimo privilegio:** cada handler recibe únicamente servicios y secretos indispensables.
2. **Validación doble:** esquema en Order y precondiciones de dominio dentro de la operación.
3. **Interfaces cerradas:** no permitir argumentos que cambien arbitrariamente tablas, rutas, expresiones o módulos ejecutables.
4. **Aislamiento:** si el plugin contiene código de terceros no confiable, ejecutar fuera del proceso privilegiado con recursos acotados.
5. **Determinismo cuando corresponda:** mantener explícitos dependencias de tiempo, localidad, precisión y versiones.
6. **Trazabilidad:** registrar ID y versión de la capacidad, latencia y códigos de error; omitir datos sensibles.
7. **Compatibilidad:** hacer *contract tests*; nuevas firmas incompatibles se publican como versiones mayores.
8. **Desactivación limpia:** cuando el plugin desaparece, Order deja de resolverlo y el resto del sistema conserva su integridad.
9. **Estado limitado:** inyectar dependencias controladas en lugar de mantener conexiones globales mutables.

## 10. Criterios de aceptación por capacidad

Una capacidad solo se considera lista si cumple:

- [ ] El propósito puede enunciarse en una oración sin «y también hace...».
- [ ] Posee ID, versión, dueño y manifiesto conformes con Order.
- [ ] Entradas y salidas son verificables por esquema.
- [ ] Efectos, permisos, límites de recursos y errores están definidos.
- [ ] No usa SQL concatenado con entradas del usuario ni ejecuta código arbitrario.
- [ ] Un permiso de capacidad no concede automáticamente acceso a todo recurso.
- [ ] Posee pruebas positivas, negativas, límites y, si corresponde, concurrencia.
- [ ] El contrato conserva compatibilidad o publica una nueva versión mayor.
- [ ] Es reemplazable sin exigir al consumidor conocimiento de su implementación interna.
- [ ] Se puede deshabilitar sin interrumpir capacidades no relacionadas.

## 11. Plantilla para nuevas singularidades

```yaml
contract_schema: "1.0"
id: "domain.verb"
version: "1.0.0"
owner: "approved.provider"
kind: "singularity"
purpose: "Un único comportamiento observable"
input_schema: { type: object, additionalProperties: false }
output_schema: { type: object, additionalProperties: false }
effects: "none"  # none | read | write | external
required_permissions: []
idempotent: true
cache_policy: "never"  # never | eligible
# Agregar timeout_ms, max_input_bytes y data_classification.
# Usar JSON Schema válido en los manifiestos de producción.
```

El ejemplo YAML es una **plantilla conceptual**, no un manifiesto completo listo para habilitar. Los valores reales se eligen a partir del riesgo y comportamiento medidos.

## 12. Invariantes para código generado por IA

1. **Cada singularidad ejecuta una responsabilidad definida.**
2. **No se salta Order para alcanzar otra capacidad o datos privilegiados.**
3. **No crea contratos nuevos unilateralmente en tiempo de ejecución.**
4. **No inventa valores, resultados ni éxitos frente a errores.**
5. **No declara idempotencia, seguridad o precisión que no estén implementadas y probadas.**
6. **Las funcionalidades nuevas se agregan como capacidades versionadas, no como excepciones ad hoc en funciones existentes.**

**Definición final:** Singularity API no intenta ser universal. Cada operación hace **una cosa**, de manera explícita y comprobable. El sistema global obtiene amplitud al combinar muchas operaciones mediante Order, con o sin planificación de Chaos.
