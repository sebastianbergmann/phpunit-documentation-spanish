

.. _code-coverage-analysis:

===============================
Análisis de Cobertura de Código
===============================

    *Wikipedia*:

    En ciencias de la computación la cobertura de código es una medida usada
    para describir el grado en que el código fuente de un programa se ha probado
    por medio de un conjunto de pruebas. Un programa con una alta cobertura de
    código ha sido probado más profundamente y tiene una baja probabilidad de
    contener errores en comparación con un programa con una baja cobertura
    de código.

En este capítulo aprenderemos todo sobre la cobertura de código de PHPUnit,
funcionalidad que provee una idea de que parte del código de producción
se ejecuta cuando se hacen las pruebas. La funcionalidad se vale del componente
`php-code-coverage <https://github.com/sebastianbergmann/php-code-coverage>`_,
que a su vez aprovecha la funcionalidad de cobertura de código que provee la
extensión de PHP `Xdebug <https://xdebug.org/>`_.

.. admonition:: Nota

   Xdebug no se distribuye como parte de PHPUnit. Si recibimos una notificación
   mientras ejecutamos las pruebas indicando que el controlador de cobertura de
   código no esta disponible, es posible que Xdebug no este instalado o no este configurado
   apropiadamente. Antes de poder usar la característica de cobertura de código
   en PHPUnit deberíamos leer
   `la guía de instalación de Xdebug <https://xdebug.org/docs/install>`_.

   php-code-coverage también soporta `phpdbg <https://phpdbg.room11.org/ introduction.html>`_
   como fuente alternativa de datos para la cobertura de código.

PHPUnit puede generar un reporte de cobertura de código basado en HTML, como
también en archivos de registro de sucesos basados en XML, con la información de
cobertura de código en varios formatos (Clover, Crap4J, PHPUnit). La información
de cobertura de código se puede reportar como texto (e impresa por STDOUT) y
exportada como código PHP para ser procesado posteriormente.

Podemos revisar :ref:`textui` para ver una lista de comandos que acompañados con
los interruptores adecuados permite controlar la funcionalidad de cobertura de código,
también debemos revisar :ref:`appendixes.configuration.logging` para las
configuraciones relevantes.

.. _code-coverage-analysis.metrics:

Métricas de Software para la Cobertura de Código
################################################

Existen varias métricas de software para medir la cobertura de código:

*Line Coverage*

    La métrica de software *Line Coverage* mide si cada línea ejecutable fue
    ejecutada.

*Function and Method Coverage*

    La métrica de software *Function and Method Coverage* mide si cada función
    o método se ha invocado. php-code-coverage solo considera una función o
    método como cubierto cuando todas sus líneas ejecutables fueron cubiertas.

*Class and Trait Coverage*

    La métrica de software *Class and Trait Coverage* mide si cada método de una
    clase o *trait* fue cubierto. php-code-coverage solo considera una clase o
    *trait* como cubierto cuando todos sus métodos fueron cubiertos.

*Opcode Coverage*

    La métrica de software *Opcode Coverage* mide si cada *código de operación*, «opcode»,
    de una función o método se ha ejecutado mientras se corre la suite de prueba.
    Una línea de código se copila usualmente dentro de más de una linea de
    *código de operación*. La cobertura de línea considera a una línea de código
    cubierta tan pronto como sus *códigos de operación* se ejecutan.

*Branch Coverage*

    La métrica de software *Branch Coverage* mide si la expresión booleana de
    cada estructura de control evalúa ``true`` y ``false`` mientras se ejecuta
    la suite de prueba.

*Path Coverage*

    La métrica de software *Path Coverage* mide si cada una de las rutas posibles
    de ejecución de una función o método fue seguida durante la ejecución de la
    suite de prueba. Una ruta de ejecución es una secuencia única entre un conjunto
    de ramas desde el comienzo de la función o método hasta su salida.

*Change Risk Anti-Patterns (CRAP) Index*

    El *Change Risk Anti-Patterns (CRAP) Index* se calcula en base a la complejidad
    ciclomática y cobertura de código de una unidad de código. El código que no
    es muy complejo y tiene una cobertura de código adecuada tendrá un índice
    CRAP bajo. El índice CRAP se puede bajar escribiendo pruebas y refactorizando
    el código para disminuir su complejidad.

.. admonition:: Nota

   Las métricas de software *Opcode Coverage*, *Branch Coverage* y *Path Coverage*
   aún no están soportadas por php-code-coverage.

.. _code-coverage-analysis.whitelisting-files:

Lista Blanca de Archivos
########################

Es obligatorio configurar una *whitelist* para indicarle a PHPUnit que archivos
de código fuente incluir en el reporte de cobertura de código. Esto se puede
hacer o usando la opción de línea de comandos ``--whitelist`` o desde el
archivo de configuración (ver :ref:`appendixes.configuration.whitelisting-files`).

Los valores de configuración ``addUncoveredFilesFromWhitelist`` y
``processUncoveredFilesFromWhitelist`` se usan para configurar la manera como se
usa la lista blanca:

- ``addUncoveredFilesFromWhitelist="false"`` significa que solo los archivos
  en la lista blanca que por lo menos tienen una línea de código ejecutado
  se incluyen en el reporte de cobertura de código.

- ``addUncoveredFilesFromWhitelist="true"`` (por defecto) significa que todos
  los archivos en la lista blanca se incluyen en el reporte de cobertura de código
  incluso si no fue ejecutada ni una sola línea del archivo.

- ``processUncoveredFilesFromWhitelist="false"`` (por defecto) significa que un
  archivo listado en la lista blanca que no tiene líneas de código ejecutadas
  será agregado al reporte de cobertura de código (si se indica ``addUncoveredFilesFromWhitelist="true"``)
  pero no será cargado por PHPUnit y por lo tanto no se analizará la corrección
  de las líneas de código de información.

- ``processUncoveredFilesFromWhitelist="true"`` significa que un archivo listado
  en la lista blanca que no tiene líneas de código ejecutadas será cargado por
  PHPUnit así que se puede analizar la corrección de las líneas de información
  del código ejecutables.

.. admonition:: Nota

   Por favor observemos que la carga de los archivos de código fuente que se ejecuta
   cuando se configura ``processUncoveredFilesFromWhitelist="true"``, por ejemplo,
   puede causar problemas cuando un archivo de código fuente contiene código
   fuera del alcance de una clase o función.

.. _code-coverage-analysis.ignoring-code-blocks:

Ignorar Bloques de Código
#########################

En ocasiones tenemos bloques de código que no podemos probar y que podríamos
querer ignorar durante el análisis de cobertura de código. PHPUnit permite hacer
esto usando las anotaciones ``@codeCoverageIgnore``, ``@codeCoverageIgnoreStart``
y ``@codeCoverageIgnoreEnd`` como se muestra en
:numref:`code-coverage-analysis.ignoring-code-blocks.examples.Sample.php`.

.. code-block:: php
    :caption: Uso de las anotaciones ``@codeCoverageIgnore``, ``@codeCoverageIgnoreStart`` y ``@codeCoverageIgnoreEnd``
    :name: code-coverage-analysis.ignoring-code-blocks.examples.Sample.php

    <?php
    use PHPUnit\Framework\TestCase;

    /**
     * @codeCoverageIgnore
     */
    class Foo
    {
        public function bar()
        {
        }
    }

    class Bar
    {
        /**
         * @codeCoverageIgnore
         */
        public function foo()
        {
        }
    }

    if (false) {
        // @codeCoverageIgnoreStart
        print '*';
        // @codeCoverageIgnoreEnd
    }

    exit; // @codeCoverageIgnore
    ?>

Las líneas de código ignoradas (marcadas como ignoradas usando las anotaciones)
se cuentan como ejecutadas (si ellas son ejecutables) y no serán señaladas.

.. _code-coverage-analysis.specifying-covered-methods:

Especificar los Métodos de Cobertura
####################################

La anotación ``@covers`` (ver :ref:`appendixes.annotations.covers.tables.annotations`)
se puede usar en el código de prueba para especificar que método(s) un método
de prueba quiere ejecutar. Si se provee, solo la información de cobertura de
código para el o los métodos especificados serán considerados. El
:numref:`code-coverage-analysis.specifying-covered-methods.examples.BankAccountTest.php`
muestra un ejemplo.

.. code-block:: php
    :caption: Prueba que especifica que métodos se quieren cubrir
    :name: code-coverage-analysis.specifying-covered-methods.examples.BankAccountTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class BankAccountTest extends TestCase
    {
        protected $ba;

        protected function setUp()
        {
            $this->ba = new BankAccount;
        }

        /**
         * @covers BankAccount::getBalance
         */
        public function testBalanceIsInitiallyZero()
        {
            $this->assertSame(0, $this->ba->getBalance());
        }

        /**
         * @covers BankAccount::withdrawMoney
         */
        public function testBalanceCannotBecomeNegative()
        {
            try {
                $this->ba->withdrawMoney(1);
            }

            catch (BankAccountException $e) {
                $this->assertSame(0, $this->ba->getBalance());

                return;
            }

            $this->fail();
        }

        /**
         * @covers BankAccount::depositMoney
         */
        public function testBalanceCannotBecomeNegative2()
        {
            try {
                $this->ba->depositMoney(-1);
            }

            catch (BankAccountException $e) {
                $this->assertSame(0, $this->ba->getBalance());

                return;
            }

            $this->fail();
        }

        /**
         * @covers BankAccount::getBalance
         * @covers BankAccount::depositMoney
         * @covers BankAccount::withdrawMoney
         */
        public function testDepositWithdrawMoney()
        {
            $this->assertSame(0, $this->ba->getBalance());
            $this->ba->depositMoney(1);
            $this->assertSame(1, $this->ba->getBalance());
            $this->ba->withdrawMoney(1);
            $this->assertSame(0, $this->ba->getBalance());
        }
    }
    ?>

Además, es posible especificar que una prueba no debe cubrir *ningún* method usando
la anotación ``@coversNothing`` (ver :ref:`appendixes.annotations.coversNothing`).
Esto puede ser útil cuando escribimos pruebas de integración para asegurar que
solo se genera cobertura de código para pruebas unitarias.

.. code-block:: php
    :caption: Una prueba que especifica que ningún método será cubierto
    :name: code-coverage-analysis.specifying-covered-methods.examples.GuestbookIntegrationTest.php

    <?php
    use PHPUnit\DbUnit\TestCase

    class GuestbookIntegrationTest extends TestCase
    {
        /**
         * @coversNothing
         */
        public function testAddEntry()
        {
            $guestbook = new Guestbook();
            $guestbook->addEntry("suzy", "Hello world!");

            $queryTable = $this->getConnection()->createQueryTable(
                'guestbook', 'SELECT * FROM guestbook'
            );

            $expectedTable = $this->createFlatXmlDataSet("expectedBook.xml")
                                  ->getTable("guestbook");

            $this->assertTablesEqual($expectedTable, $queryTable);
        }
    }
    ?>

.. _code-coverage-analysis.edge-cases:

Casos Límite
############

Esta sección muestra interesantes casos límite que producen información de
cobertura de código confusa.

.. code-block:: php
    :name: code-coverage-analysis.edge-cases.examples.Sample.php

    <?php
    use PHPUnit\Framework\TestCase;

    // Because it is "line based" and not statement base coverage
    // one line will always have one coverage status
    if (false) this_function_call_shows_up_as_covered();

    // Due to how code coverage works internally these two lines are special.
    // This line will show up as non executable
    if (false)
        // This line will show up as covered because it is actually the
        // coverage of the if statement in the line above that gets shown here!
        will_also_show_up_as_covered();

    // To avoid this it is necessary that braces are used
    if (false) {
        this_call_will_never_show_up_as_covered();
    }
    ?>

Speeding Up Code Coverage with Xdebug
#####################################

The performance of code coverage data collection with Xdebug 2.6 (and later) can
be significantly improved by delegating whitelist filtering to Xdebug.

In order to do this, the first step is to generate the filter script for Xdebug
using the ``--dump-xdebug-filter`` option:

.. code-block:: bash

    $ phpunit --dump-xdebug-filter build/xdebug-filter.php
    PHPUnit 7.4.0 by Sebastian Bergmann and contributors.

    Runtime:       PHP 7.2.11 with Xdebug 2.6.1
    Configuration: /workspace/project/phpunit.xml

    Wrote Xdebug filter script to build/xdebug-filter.php

Now we can use the ``--prepend`` option to load the Xdebug filter script as early
as possible when we want to generate a code coverage report:

.. code-block:: bash

    $ phpunit --prepend build/xdebug-filter.php --coverage-html build/coverage-report

