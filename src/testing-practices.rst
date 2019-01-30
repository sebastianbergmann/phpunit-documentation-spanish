

.. _testing-practices:

======================
Prácticas para Pruebas
======================

    *Erich Gamma*:
    
    Siempre puedes escribir más pruebas. Sin embargo, rápidamente encontrarás
    que sólo una fracción de las pruebas que puedas imaginar es realmente 
    útil. Lo que tú quieres es escribir pruebas fallen aun cuando pienses
    que deberían funcionar, o pruebas pasen exitosamente aunque pienses que
    deberían fallar.
    Otra manera de pensar de ello es en términos de costo/beneficio: tú quieres
    escribir pruebas que te retribuirán información.   


.. _testing-practices.during-development:

Durante el Desarrollo
#####################

Cuando necesitas hacer un cambio a una estructura interna del software en el 
que estás trabajando para hacerlo más fácil de entender y modificarlo sin 
cambiar su comportamiento observable, un suite de pruebas es invaluable a la
hora de aplicar esas llamadas `refactorizaciones <http://martinfowler.com/bliki/DefinitionOfRefactoring.html>`_
de manera segura. De otro modo, podrías notar que el sistema se rompe mientras
estás llevando a cabo la reestructuración.

Las siguientes condiciones te ayudarán a mejorar el código y diseño de tu
proyecto mientras usas pruebas unitarias para verificar que, en efecto, los pasos
de transformación de la refactorización preservan el comportamiento y no 
introducen errores:

#.

   Todas las pruebas unitarias se ejecutan correctamente.

#.

   El código comunica sus principios de diseño.

#.

   El código no contiene redundancias.

#.

   El código contiene el mínimo número de clases y métodos.

Cuando necesitas agregar una nueva funcionalidad al sistema, escribe las pruebas 
primero. Entonces habrás terminado de desarrollar cuando las pruebas se ejecuten.
Esta práctica será discutida en más detalle en el siguiente capítulo.


.. _testing-practices.during-debugging:

Durante la Depuración
#####################

Cuando tienes un reporte de defecto, tu impulso podría ser corregir el defecto lo
más rápido posible. La experiencia muestra que este impulso no te servirá bien; 
es como cuando la corrección del defecto causa otro defecto.

Puedes mantener tu impulso bajo control haciendo lo siguiente:

#.

   Verifica que puedes reproducir el defecto.

#.

   Encuentra la demostración en más pequeña escala del defecto en el código. Por 
   ejemplo, si un número aparece incorrectamente en una salida, encuentra el objeto
   que computa ese número.

#.

   Escribe una prueba automatizada que falle, pero que sea exitosa cuando el defecto 
   esté corregido.

#.

   Corrige el defecto.

Encontrar la reproducción más pequeña confiable del defecto te da la oportunidad de 
examinar realmente la causa del defecto. La prueba que escribas mejorará las 
probabilidades de que cuando corrijas el defecto, realmente lo corrijas, porque la 
nueva prueba reduce la probabilidad de deshacer la corrección con futuros cambios
al código. Todas las pruebas que escribiste antes reducen la probabilidad de causar,
inadvertidamente, un problema diferente.


    *Benjamin Smedberg*:

    Las pruebas unitarias ofrecen muchas ventajas:

    -

      Las pruebas dan a los autores del código y a los revisores la confianza de que 
      esos parches producen los resultados esperados.

    -

      Crear casos de prueba es un buen impulso para que los desarrolladores descubran 
      casos límite.

    -

      Las pruebas proveen una buena manera de capturar regresiones rápidamente y 
      asegurarse de que ninguna de éstas se repita dos veces.

    -

      Las pruebas unitarias proveen ejemplos funcionales de cómo usar una API y puede 
      ayudar significativamente los esfuerzos de documentación.

    En conjunto, las pruebas unitarias integradas hacen más pequeño el costo y el riesgo de 
    cualquier cambio pequeño Permitirá al proyecto hacer \[...] mayores mejoras en su
    arquitectura \[...] de forma rápida y confiable.
