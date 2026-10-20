# Debate 2: Caché, rapidez y confianza

## El problema

Una aplicación consulta repetidamente datos costosos de calcular. Una caché puede reducir latencia y carga, pero una respuesta almacenada puede quedar desactualizada o entregarse a la persona equivocada si se diseña mal.

La pregunta del debate es: **¿debe el sistema priorizar la velocidad de la caché o la corrección y seguridad de cada respuesta?**

## Caos: conservar flexibilidad

**Caos:** «Las solicitudes no son todas iguales. Algunas necesitan información reciente; otras pueden aceptar una respuesta ligeramente antigua. Algunas pueden almacenarse; otras contienen información privada y no deberían compartirse. Si prohibimos toda caché por temor al error, desperdiciamos oportunidades de mejorar.»

**Singularidad:** «La flexibilidad también multiplica los casos que debemos proteger. Una respuesta correcta para una persona podría ser una filtración para otra.»

## Singularidad: una respuesta, un propósito

**Singularidad:** «Cada entrada debe representar una consulta concreta. La clave debe incluir todo lo que cambia la respuesta: recurso, usuario o ámbito de acceso, idioma y versión relevante. Si no podemos definir cuándo dos solicitudes son equivalentes, no deberíamos reutilizar su resultado.»

**Caos:** «Y si incluimos demasiadas diferencias en la clave, casi nunca habrá coincidencias. La caché perderá su utilidad.»

## Orden: políticas comunes y límites

**Orden:** «No necesitamos elegir entre guardar todo y guardar nada. Necesitamos reglas explícitas:

- decidir qué datos se pueden almacenar;
- separar ámbitos de usuarios y permisos;
- definir duración o invalidación;
- no guardar secretos innecesarios;
- verificar autorización antes de entregar datos protegidos;
- medir aciertos, fallos y antigüedad.»

**Singularidad:** «La autorización no debe depender de que una clave haya sido construida correctamente.»

**Orden:** «De acuerdo. La clave reduce colisiones entre contextos, pero no reemplaza la autorización. La aplicación debe comprobar que el solicitante puede acceder al recurso.»

## Intervención técnica

Una política conceptual puede expresarse así:

```text
validar solicitud
comprobar autorización para el recurso
construir clave con el ámbito y las entradas relevantes
si existe una entrada válida: devolverla
si no: calcular, guardar solo si está permitido y devolver
```

La duración adecuada depende del tipo de dato. Para datos que cambian con frecuencia puede ser preferible una duración corta o invalidación explícita. Para información altamente sensible, la decisión segura puede ser no almacenarla en una caché compartida.

## Veredicto

- **Caos** recuerda que distintas solicitudes pueden necesitar distintas políticas.
- **Singularidad** exige que cada resultado reutilizado tenga un propósito y contexto precisos.
- **Orden** define reglas comunes para autorización, caducidad, invalidación y observabilidad.

La caché no es eficiente solo porque responda más rápido. También debe conservar la corrección y evitar exponer datos. La solución es almacenar selectivamente, usar claves apropiadas y mantener autorización independiente de la caché.

Las tres leyes funcionan aquí como una lente de diseño: variedad de condiciones, reglas seguras y resultados delimitados. No garantizan por sí solas que una implementación sea correcta.
