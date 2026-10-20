# Debate 3: Registros, privacidad y memoria del sistema

## El problema

Un servicio falla de forma intermitente. El equipo necesita información para descubrir la causa, pero sus registros pueden contener datos personales, credenciales u otra información sensible.

La pregunta es: **¿cuánto debería recordar un sistema para poder aprender de sus errores sin poner en riesgo a sus usuarios?**

## Caos: observar lo inesperado

**Caos:** «Los fallos no siempre ocurren donde los esperamos. Si registramos muy poco, no podremos descubrir patrones nuevos ni entender condiciones raras. Necesitamos observar el comportamiento real, no solo el que anticipamos.»

**Singularidad:** «Registrar todo crea una colección enorme de datos sensibles y ruido. Más información no garantiza mejores explicaciones.»

## Singularidad: registrar el hecho necesario

**Singularidad:** «Cada evento registrado debe responder una pregunta operacional concreta: qué operación falló, en qué etapa y con qué resultado. No hace falta guardar contraseñas, tokens, cuerpos completos de solicitudes ni datos personales si no son necesarios.»

**Caos:** «¿Y si el detalle que descartas resulta ser justo el que revela un fallo desconocido?»

## Orden: una memoria gobernada

**Orden:** «La respuesta no es ni amnesia total ni memoria ilimitada. Definamos un esquema de eventos, niveles de detalle, controles de acceso y plazos de retención. Enmascaremos o excluyamos datos sensibles. Para investigar casos excepcionales, podemos habilitar diagnósticos temporales y controlados, con autorización y auditoría.»

**Singularidad:** «Y los registros deben distinguir hechos de interpretaciones. Un mensaje de error no debería afirmar una causa que el sistema no comprobó.»

## Intervención técnica

Un evento útil puede incluir:

```text
timestamp
operation_name
request_id
outcome
error_code
duration_ms
```

Debe excluir secretos y datos que no sean necesarios para operar o investigar. Un identificador de correlación ayuda a conectar eventos de una misma operación sin copiar su contenido privado. Los registros también requieren límites de acceso, protección contra alteraciones y una política de retención.

## Veredicto

- **Caos** impulsa la observación de situaciones no previstas.
- **Singularidad** limita cada registro a los datos necesarios para una pregunta concreta.
- **Orden** gobierna formato, acceso, protección y retención.

Un sistema seguro no necesita recordarlo todo para aprender. Necesita conservar evidencia suficiente y confiable, durante el tiempo adecuado, con acceso limitado. La observabilidad debe ayudar a corregir el sistema sin convertir los registros en una fuente evitable de exposición.

La analogía con la memoria tiene límites: los registros no son la experiencia completa del sistema, sino una selección diseñada de eventos.
