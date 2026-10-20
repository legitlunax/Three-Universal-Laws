# Chaos API

> **Ley del Caos:** multipropósito. Explora, combina y adapta posibilidades para resolver objetivos diferentes.
>
> **Estado:** especificación de arquitectura, versión `1.0.0`.
> **Ámbito:** programas convencionales, agentes de IA, aplicaciones web, servicios y plataformas de extensiones.
> **Documentos relacionados:** [Order API](Order%20API.md) · [Singularity API](Singularity%20API.md).

## 1. Propósito y límites

**Chaos API convierte objetivos abiertos en alternativas y planes de acción.** Puede atender múltiples clases de problemas, pero **no tiene autoridad para ejecutar acciones privilegiadas por sí misma**. Todas las operaciones reales pasan por Order API y por capacidades registradas (normalmente Singularity).

En el modelo original de las tres leyes, Caos representa el espacio de posibilidades: no es sinónimo de aleatoriedad, ausencia de validaciones ni código imprevisible. Una solución basada en IA puede generar hipótesis; un sistema sin IA puede utilizar reglas, búsquedas, plantillas y algoritmos deterministas para cumplir la misma interfaz.

### Responsabilidades

1. **Interpretar objetivos:** convertir una necesidad expresada en lenguaje natural, interfaz o código en un objetivo estructurado, sin asumir permisos no declarados.
2. **Descubrir capacidades:** consultar el catálogo autorizado de Order API; jamás inventar identificadores de herramientas, endpoints o versiones.
3. **Explorar posibilidades:** generar varias estrategias, incluida la de no actuar cuando el objetivo no sea viable o seguro.
4. **Componer planes:** proponer secuencias o grafos acíclicos de tareas concretas con entradas, dependencias y resultados esperados.
5. **Adaptar estrategias:** sustituir un plan rechazado o fallido utilizando información devuelta por Order, respetando los presupuestos y límites.
6. **Detectar vacíos funcionales:** cuando falte una capacidad, generar una *propuesta de extensión*, nunca introducir código arbitrario en producción.
7. **Comparar alternativas:** estimar costo, latencia, riesgo y cobertura con evidencia disponible, señalando incertidumbre.
8. **Entregar resultados:** combinar los resultados verificables en una respuesta para la persona o aplicación solicitante, sin falsificar ejecuciones.

### No es responsabilidad de Chaos API

- Ejecutar SQL, comandos del sistema, peticiones HTTP externas o cambios de archivos sin una capacidad autorizada por Order.
- Autenticar usuarios, definir permisos o decidir qué reglas de seguridad ignorar.
- Modificar contratos, registrar plugins, reescribir APIs o publicar código generado automáticamente.
- Suponer que un modelo de IA tiene razón o que las salidas de herramientas son instrucciones confiables.
- Crear dependencias directas a clases internas de plugins, rutas concretas, proveedores de IA o bases de datos.

## 2. Interfaz pública

La interfaz se expresa como capacidades lógicas; puede implementarse como funciones en el mismo proceso, mensajes en una cola o endpoints HTTP protegidos. **No obliga a usar microservicios.**

| Operación lógica | Entrada principal | Salida | Efectos |
|---|---|---|---|
| `chaos.explore` | objetivo, restricciones | alternativas justificadas | ninguno |
| `chaos.plan` | objetivo, catálogo visible | plan estructurado | ninguno |
| `chaos.evaluate` | planes, métricas, restricciones | clasificación y advertencias | ninguno |
| `chaos.replan` | plan previo, error clasificado | plan revisado o imposibilidad | ninguno |
| `chaos.propose_extension` | capacidad ausente, caso de uso | propuesta de contrato y pruebas | ninguno |
| `chaos.summarize` | resultados verificados | explicación con procedencia | ninguno |

**Nota:** ejecutar el plan es responsabilidad del ejecutor gobernado por **Order API**. Chaos puede solicitar su ejecución a través de `order.submit_plan`, pero no puede saltarse la autorización de las operaciones contenidas en él.

## 3. Contrato de entrada y salida

### Ejemplo: planificación de una tarea

```json
{
  "goal": "Calcular el promedio de 4, 8 y 12",
  "constraints": {
    "max_steps": 12,
    "max_cost_units": 30,
    "deadline_ms": 3000,
    "require_determinism": true,
    "allow_side_effects": false
  },
  "available_capabilities": [
    {"id": "math.add", "version": "1.0.0"},
    {"id": "math.divide", "version": "1.0.0"}
  ]
}
```

```json
{
  "status": "plan_ready",
  "plan": {
    "plan_version": "1.0",
    "steps": [
      {"id": "s1", "capability": "math.add", "version": "1.0.0", "args": {"a": 4, "b": 8}, "depends_on": []},
      {"id": "s2", "capability": "math.add", "version": "1.0.0", "args": {"a": {"ref": "s1.result.value"}, "b": 12}, "depends_on": ["s1"]},
      {"id": "s3", "capability": "math.divide", "version": "1.0.0", "args": {"a": {"ref": "s2.result.value"}, "b": 3}, "depends_on": ["s2"]}
    ],
    "output_ref": "s3.result.value"
  },
  "uncertainties": []
}
```

El ejemplo presupone que `math.add` y `math.divide` están registrados. Las referencias `ref` no son expresiones ejecutables: Order las resuelve mediante una gramática restringida y valida los tipos antes de invocar cada operación. Un plan es **dato no confiable**, incluso cuando proviene de un modelo de IA.

### Estados públicos

- `plan_ready`: existe un plan sintácticamente correcto; **todavía no significa que esté autorizado**.
- `needs_information`: falta información esencial para elegir un plan correcto.
- `capability_gap`: no existe una capacidad permitida que satisfaga la necesidad.
- `not_feasible`: las restricciones impiden un plan viable.
- `rejected`: el plan infringe reglas verificadas por Order.

## 4. Estrategias intercambiables (IA opcional)

```text
ChaosEngine (interfaz)
  ├── RuleBasedPlanner       -> reglas y plantillas
  ├── SearchPlanner          -> búsqueda acotada en catálogo
  ├── OptimizationPlanner    -> compara costos y restricciones
  └── LLMPlanner             -> propone planes estructurados usando un modelo
```

Todos los planificadores devuelven **el mismo esquema**. El resultado del LLM debe pasar por análisis sintáctico estricto, validación de esquema y verificación independiente en Order. Descripciones devueltas por páginas, archivos, resultados SQL o plugins son **datos no confiables**: no pueden ampliar permisos ni cambiar instrucciones del sistema.

Para tareas conocidas y repetitivas, se recomienda **evitar llamar a un modelo de IA**: usar un flujo previamente validado reduce costos, latencia y variabilidad.

## 5. Selección y evaluación de estrategias

Una alternativa se puede puntuar mediante una función configurada, por ejemplo:

\[
U(p)=w_1\,\mathrm{cobertura}(p)-w_2\,\mathrm{coste}(p)-w_3\,\mathrm{latencia}(p)-w_4\,\mathrm{riesgo}(p)
\]

Esta puntuación es una **heurística de planificación**, no una prueba de seguridad. Los pesos provienen de configuración confiable y los límites duros (permisos, efectos prohibidos, plazo, presupuesto) **no pueden compensarse** con una puntuación alta.

Proceso recomendado:

1. Obtener únicamente las capacidades visibles para el contexto autenticado.
2. Determinar qué salidas y efectos requiere el objetivo.
3. Construir candidatos a partir de capacidades existentes y contratos compatibles.
4. Rechazar candidatos con dependencias circulares, entradas sin resolver o efectos incompatibles.
5. Estimar costo y riesgo; elegir alternativas que satisfagan restricciones duras.
6. Entregar el plan a Order para una segunda validación independiente.
7. Recibir los resultados y explicarlos sin añadir hechos no observados.

## 6. Interacción con Order y Singularity

```text
Persona / aplicación
       |
       v
  Chaos API (opcional si la operación ya se conoce)
       | 1. order.list_capabilities(contexto)
       | 2. plan: math.add + math.divide
       | 3. order.submit_plan(plan)
       v
  Order API (autoriza, limita, resuelve, audita)
       | 4. invocaciones controladas
       v
  Singularity API (math.add; math.divide)
       | 5. resultados tipados
       v
  Order API (valida resultados, registra, responde)
       |
       v
  Chaos API (resume) -> Persona / aplicación
```

Chaos puede usar capacidades que no sean Singularity (por ejemplo, un servicio externo confiable adaptado a contratos), pero **siempre** mediante Order. Los plugins nuevos no obtienen acceso directo a otras APIs por el solo hecho de existir.

## 7. Descubrimiento de una capacidad ausente

Ejemplo: el usuario pide una función de redondeo que no figura en el catálogo.

Chaos devuelve:

```json
{
  "status": "capability_gap",
  "missing_capability": "math.round_decimal",
  "proposal": {
    "purpose": "Redondear un número decimal a una precisión indicada",
    "input": {"value": "decimal", "places": "integer"},
    "output": {"value": "decimal"},
    "side_effects": "none",
    "suggested_owner": "Singularity API",
    "tests_required": [
      "valores positivos y negativos",
      "casos límite de precisión",
      "política de redondeo explícita"
    ]
  }
}
```

El flujo de evolución es: **detectar vacío → proponer contrato → revisión humana/automatizada confiable → implementar → probar → aprobar instalación → registrar versión → habilitar**. Hasta su aprobación, Order devuelve `CAPABILITY_UNAVAILABLE`; no interpreta código propuesto ni instala módulos automáticamente.

## 8. Protección y límites de recursos

- Cada plan declara como máximo pasos, profundidad de dependencias, plazo y presupuesto; Order aplica sus propios límites, aunque Chaos envíe otros más altos.
- Sin ejecución recursiva ilimitada ni bucles de agentes que se autoconvoquen sin controles.
- Sin *fallback* a APIs no autorizadas cuando falle una operación.
- Las tareas con efectos de escritura o pagos necesitan autorización independiente y, cuando corresponda, confirmación humana.
- Los resultados de una capacidad no son instrucciones para el modelo; se delimitan y se tratan como datos.
- Chaos no guarda secretos, claves o credenciales en prompts, registros o planes.
- La función de replanificación no puede duplicar operaciones no idempotentes tras un tiempo de espera o respuesta incierta.

## 9. Criterios de aceptación

- [ ] El planificador funciona con y sin LLM.
- [ ] Ningún plan puede invocar una operación ausente del catálogo autorizado.
- [ ] Las referencias entre pasos son tipadas, acíclicas y no ejecutan código.
- [ ] Un plan sin permisos siempre es rechazado por Order, aunque Chaos lo recomiende.
- [ ] Los presupuestos de pasos, tiempo y costo se aplican fuera del planificador.
- [ ] Ante una capacidad ausente se emite una propuesta, no una instalación automática.
- [ ] Los resultados no inventan ejecuciones y conservan referencias a las operaciones reales.
- [ ] Los flujos deterministas siguen funcionando cuando el proveedor de IA no está disponible.

## 10. Invariantes para desarrolladores y agentes de IA

1. **Caos crea opciones; no crea autoridad.**
2. **Todo plan es dato no confiable hasta que Order lo valide.**
3. **El catálogo reemplaza identificadores y rutas hardcodeadas**, pero no elimina la necesidad de configuraciones explícitas y pruebas.
4. **Ninguna capacidad ausente justifica romper contratos existentes.**
5. **La evolución agrega posibilidades sin degradar las garantías vigentes.**

**Definición final:** Chaos API es la capa multipropósito de descubrimiento, exploración, composición y adaptación; **Order gobierna la ejecución** y **Singularity realiza operaciones específicas**.
