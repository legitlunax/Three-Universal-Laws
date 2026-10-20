# Debate 4: Cambios, compatibilidad y futuro

## El problema

Un equipo quiere modificar una API utilizada por muchas aplicaciones. La nueva versión simplificaría el diseño, pero algunos clientes todavía dependen del comportamiento anterior.

La pregunta es: **¿es mejor rediseñar con libertad o conservar compatibilidad aunque el sistema mantenga partes antiguas?**

## Caos: permitir nuevos caminos

**Caos:** «Las necesidades cambian. Si todo cambio debe preservar cada decisión pasada, la API se vuelve una acumulación de excepciones. Debemos poder probar una forma mejor, incluso si altera el sistema.»

**Singularidad:** «Un cambio amplio puede romper clientes que no controlamos. La libertad de quien publica no elimina el costo que soportan quienes integran.»

## Singularidad: contratos pequeños y estables

**Singularidad:** «Cada operación pública debe tener un propósito claro y un contrato que diga qué acepta y qué garantiza. Si una operación cambia, sus consumidores necesitan una transición explícita.»

**Caos:** «Un contrato demasiado rígido puede congelar un error y llamar estabilidad a la falta de evolución.»

## Orden: evolucionar sin sorpresas innecesarias

**Orden:** «Compatibilidad no significa inmovilidad. Podemos medir el uso real, introducir una versión nueva, documentar la migración, advertir antes de retirar la antigua y probar ambas durante la transición. Si existe un riesgo urgente de seguridad, la prioridad puede ser distinta y debemos comunicarlo claramente.»

**Singularidad:** «Entonces la decisión depende del impacto y de las garantías que ya prometimos.»

**Orden:** «Sí. Una API interna experimental y una interfaz pública crítica no tienen las mismas obligaciones.»

## Intervención técnica

Una evolución controlada puede seguir este proceso:

1. definir qué cambia y qué consumidores podrían verse afectados;
2. comprobar el uso mediante métricas que respeten la privacidad;
3. publicar el contrato nuevo y una guía de migración;
4. mantener compatibilidad durante un periodo definido, cuando sea viable;
5. probar casos antiguos y nuevos;
6. retirar la versión anterior con aviso, salvo que un riesgo urgente requiera acelerar el cambio.

No es necesario mantener para siempre todo comportamiento histórico. Sí conviene conocer qué promesas existen antes de romperlas.

## Veredicto

- **Caos** permite explorar diseños y responder a necesidades emergentes.
- **Singularidad** mantiene operaciones y contratos comprensibles.
- **Orden** organiza la transición y limita daños a los sistemas dependientes.

El futuro no se protege congelando el presente. Se protege haciendo cambios deliberados, verificables y comunicados. Aun así, no existe una estrategia única: la compatibilidad apropiada depende de los riesgos, los usuarios y la naturaleza del sistema.
