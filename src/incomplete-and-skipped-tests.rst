

.. _incomplete-and-skipped-tests:

=============================
Omisión y Pruebas Incompletas
=============================

.. _incomplete-and-skipped-tests.incomplete-tests:

Pruebas Incompletas
###################

Cuando estamos trabajando con una nueva clase para un caso de prueba, podríamos
querer escribir un método de prueba vacío y de esta manera mantener una lista 
con las pruebas que debemos escribir, en este caso podríamos comenzar de la 
siguiente manera:

.. code-block:: php

    public function testSomething()
    {
    }

El problema con
los métodos de prueba vacíos es que son interpretados como exitosos por
el framework PHPUnit. Este error en la interpretación lleva a que el reporte
de la prueba sea inútil, porque no podemos ver si la prueba fue realmente
exitosa o solo no está implementada. Llamar a ``$this->fail()`` en un
método de prueba no implementado no nos ayudará, pues, la prueba será
interpretada como una falla. Lo que sería tan incorrecto como interpretar
una prueba no implementada como exitosa.

Si pensamos en una luz verde para una prueba exitosa y una luz roja para una
prueba fallida, necesitamos una luz adicional de color amarillo para marcar
una prueba como incompleta o aún no implementada.
``PHPUnit\Framework\IncompleteTest`` es una interfaz de marcado para indicar
un excepción que lanza el método de prueba cuando la prueba está
incompleta o actualmente no está implementada.
La clase ``PHPUnit\Framework\IncompleteTestError`` es la implementación estándar
de esta interfaz.

El :numref:`incomplete-and-skipped-tests.incomplete-tests.examples.SampleTest.php`
muestra una clase para un caso de prueba, ``SampleTest``, que contiene el
método de prueba ``testSomething()``. Mediante el llamado al método
``markTestIncomplete()`` (que lanza automáticamente una excepción
``PHPUnit\Framework\IncompleteTestError``) en el método de prueba, conseguimos
marcar la prueba como incompleta.

.. code-block:: php
    :caption: Marcar una prueba como incompleta
    :name: incomplete-and-skipped-tests.incomplete-tests.examples.SampleTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class SampleTest extends TestCase
    {
        public function testSomething()
        {
            // Optional: Test anything here, if you want.
            $this->assertTrue(true, 'This should already work.');

            // Stop here and mark this test as incomplete.
            $this->markTestIncomplete(
              'This test has not been implemented yet.'
            );
        }
    }

Una prueba incompleta se denota con una ``I`` en la salida del ejecutor de
pruebas en línea de comandos de PHPUnit, como se muestra en el siguiente
ejemplo:

.. code-block:: bash

    $ phpunit --verbose SampleTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    I

    Time: 0 seconds, Memory: 3.95Mb

    There was 1 incomplete test:

    1) SampleTest::testSomething
    This test has not been implemented yet.

    /home/sb/SampleTest.php:12
    OK, but incomplete or skipped tests!
    Tests: 1, Assertions: 1, Incomplete: 1.

En la :numref:`incomplete-and-skipped-tests.incomplete-tests.tables.api`
se muestra la API para marcar las pruebas como incompletas.

.. rst-class:: table
.. list-table:: API para Pruebas Incompletas
    :name: incomplete-and-skipped-tests.incomplete-tests.tables.api
    :header-rows: 1

    * - Método
      - Propósito
    * - ``void markTestIncomplete()``
      - Marca la prueba actual como incompleta.
    * - ``void markTestIncomplete(string $message)``
      - Marca la prueba actual como incompleta usando un mensaje, ``$message``,
        explicativo.

.. _incomplete-and-skipped-tests.skipping-tests:

Omitir las Pruebas
##################

No todas la pruebas se pueden ejecutar en cualquier entorno. Consideremos por
ejemplo, una capa de abstracción de base de datos que tiene varios controladores
para los diferentes sistemas de base de datos soportados. Las pruebas para
un controlador MySQL se pueden ejecutar solamente, por supuesto, si un servidor
MySQL está disponible.

El :numref:`incomplete-and-skipped-tests.skipping-tests.examples.DatabaseTest.php`
muestra una clase para un caso de prueba, ``DatabaseTest``, que contiene el
método de prueba, ``testConnection()``. En el método modelo ``setUp()`` de la
clase de caso de prueba revisamos si la extensión MySQLi está disponible y
usamos el método ``markTestSkipped()`` para saltar la prueba si no está
disponible la extensión.

.. code-block:: php
    :caption: Omitir la prueba
    :name: incomplete-and-skipped-tests.skipping-tests.examples.DatabaseTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class DatabaseTest extends TestCase
    {
        protected function setUp()
        {
            if (!extension_loaded('mysqli')) {
                $this->markTestSkipped(
                  'The MySQLi extension is not available.'
                );
            }
        }

        public function testConnection()
        {
            // ...
        }
    }

Una prueba que se omite se denota con una ``S`` en la salida del ejecutor de
pruebas en línea de comandos de PHPUnit, como se muestra en el siguiente
ejemplo:

.. code-block:: bash

    $ phpunit --verbose DatabaseTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    S

    Time: 0 seconds, Memory: 3.95Mb

    There was 1 skipped test:

    1) DatabaseTest::testConnection
    The MySQLi extension is not available.

    /home/sb/DatabaseTest.php:9
    OK, but incomplete or skipped tests!
    Tests: 1, Assertions: 0, Skipped: 1.

En la :numref:`incomplete-and-skipped-tests.skipped-tests.tables.api`
se muestra la API para omitir pruebas.

.. rst-class:: table
.. list-table:: API para omitir pruebas
    :name: incomplete-and-skipped-tests.skipped-tests.tables.api
    :header-rows: 1

    * - Método
      - Propósito
    * - ``void markTestSkipped()``
      - Marca la prueba actual como omitida.
    * - ``void markTestSkipped(string $message)``
      - Marca la prueba actual como omitida usando un mensaje, ``$message``, de
        explicación.

.. _incomplete-and-skipped-tests.skipping-tests-using-requires:

Omitir pruebas usando @requires
###############################

A parte de los métodos de arriba es posible usar la anotación ``@requires``
para expresar precondiciones comunes para un caso de prueba.

.. rst-class:: table
.. list-table:: Posibles usos para @requires
    :name: incomplete-and-skipped-tests.requires.tables.api
    :header-rows: 1

    * - Tipo
      - Posibles Valores
      - Ejemplos
      - Otros Ejemplos
    * - ``PHP``
      - Cualquier identificador de versión de PHP
      - @requires PHP 5.3.3
      - @requires PHP 7.1-dev
    * - ``PHPUnit``
      - Cualquier identificador de versión de PHPUnit
      - @requires PHPUnit 3.6.3
      - @requires PHPUnit 4.6
    * - ``OS``
      - Una expresión regular que coincida con `PHP_OS <http://php.net/manual/es/reserved.constants.php#constant.php-os>`_
      - @requires OS Linux
      - @requires OS WIN32|WINNT
    * - ``OSFAMILY``
      - Cualquier valor de `Familia de SO <http://php.net/manual/es/reserved.constants.php#constant.php-os-family>`_
      - @requires OSFAMILY Solaris
      - @requires OSFAMILY Windows
    * - ``function``
      - Cualquier parámetro valido para `function_exists <http://php.net/function_exists>`_
      - @requires function imap_open
      - @requires function ReflectionMethod::setAccessible
    * - ``extension``
      - Cualquier nombre de extensión junto con un identificador de versión opcional
      - @requires extension mysqli
      - @requires extension redis 2.2.0

.. code-block:: php
    :caption: Omitir casos de pruebas usando @requires
    :name: incomplete-and-skipped-tests.skipping-tests.examples.DatabaseClassSkippingTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    /**
     * @requires extension mysqli
     */
    class DatabaseTest extends TestCase
    {
        /**
         * @requires PHP 5.3
         */
        public function testConnection()
        {
            // Test requires the mysqli extension and PHP >= 5.3
        }

        // ... All other tests require the mysqli extension
    }

Si estamos usando una sintaxis que no copila con una determinada versión de PHP
es conveniente revisar la dependencia de versiones en la configuración xml
que se incluye en :ref:`appendixes.configuration.testsuites`.
