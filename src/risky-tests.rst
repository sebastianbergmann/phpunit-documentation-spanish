

.. _risky-tests:

=================
Pruebas Riesgosas
=================

PHPUnit puede ejecutar revisiones adicionales, que documentamos más abajo,
mientras se ejecutan las pruebas.

.. _risky-tests.useless-tests:

Pruebas Inútiles
################

PHPUnit es por defecto estricto con las pruebas que no prueban nada. Esta
revisión se puede desactivar con la opción de línea de comandos
``--dont-report-useless-tests``
o colocando ``beStrictAboutTestsThatDoNotTestAnything="false"`` en el
archivo de configuración XML de PHPUnit.

Una prueba que no ejecuta una aserción se marcará como riesgosa cuando esta
revisión está habilitada. Expectativas sobre objetos falsos o
anotaciones como @expectedException se cuentan como aserciones.

.. _risky-tests.unintentionally-covered-code:

Cobertura Involuntaria de Código
################################

PHPUnit puede ser estricto con el código cubierto involuntariamente. Esta
revisión se puede activar usando la opción ``--strict-coverage`` en la línea
de comandos o colocando ``beStrictAboutCoversAnnotation="true"`` en el archivo
de configuración XML de PHPUnit.

Una prueba que tiene la anotación @covers y ejecuta código que no está marcado
con las anotaciones @covers o @uses será marcada como riesgosa si
esta revisión está activada.

.. _risky-tests.output-during-test-execution:

Salida Durante la Ejecución de la Prueba
########################################

PHPUnit puede ser estricto con las salidas durante las pruebas. Esta revisión
se puede habilitar desde la línea de comandos con la opción
``--disallow-test-output`` o colocando ``beStrictAboutOutputDuringTests="true"``
en el archivo de configuración XML de PHPUnit.

Una prueba que emite una salida, por ejemplo, invocando *print* en el código
de prueba o en el código probado será marcada como riesgosa si esta revisión
esta habilitada.

.. _risky-tests.test-execution-timeout:

Tiempo de Espera de Ejecución de la Prueba
##########################################

Se puede colocar un tiempo límite de ejecución para una prueba si el paquete
``PHP_Invoker`` está instalado y la extensión ``pcntl`` está disponible.
La obligación de tener un tiempo límite se puede activar desde la línea
de comandos con la opción ``--enforce-time-limit`` o colocando
``enforceTimeLimit="true"`` en el archivo de configuración XML de
PHPUnit.

Una prueba con la anotación ``@large`` fallará si toma más de 60 segundos
en ejecutarse. El tiempo de espera se puede configurar con el atributo
``timeoutForLargeTests`` en el archivo de configuración XML de PHPUnit.

Una prueba con la anotación ``@medium`` fallará si demora más de 10 segundos
en ejecutarse. Este tiempo de espera se puede configurar con el atributo
``timeoutForMediumTests`` en el archivo de configuración XML de PHPUnit.

Una prueba con la anotación ``@small`` fallará si toma
más de 1 segundo en ejecutarse. Este tiempo límite se puede configurar con
el atributo ``timeoutForSmallTests`` en el archivo de configuración XML.

.. admonition:: Nota

   Las pruebas se deben marcar explícitamente con la anotación ``@small``,
   ``@medium`` o ``@large`` para habilitar los límites de ejecución.


.. _risky-tests.global-state-manipulation:

Manipulación del Estado Global
##############################

PHPUnit puede ser estricto con las pruebas que manipulan el estado global.
Esta revisión se puede habilitar usando ``--strict-global-state``
desde la línea de comandos o colocando
``beStrictAboutChangesToGlobalState="true"`` en el archivo de configuración
XML de PHPUnit.
