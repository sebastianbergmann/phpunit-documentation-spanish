

.. _writing-tests-for-phpunit:

============================
Escribir pruebas con PHPUnit
============================

El :numref:`writing-tests-for-phpunit.examples.StackTest.php` muestra como
podemos escribir pruebas usando PHPUnit, esta prueba ejecuta operaciones sobre un
arreglo de PHP. El ejemplo presenta las convenciones básicas y los pasos para
escribir pruebas con PHPUnit:

#.

   Las pruebas para una clase ``Class`` van dentro de una clase ``ClassTest``.

#.

   ``ClassTest`` hereda (en la mayoría de los casos) de ``PHPUnit\Framework\TestCase``.

#.

   Las pruebas son métodos públicos y toman como nombre ``test*``.

   También, podemos usar la anotación de *docblock* ``@test`` en el método para
   marcarlo como un método de prueba.

#.

   Dentro de los métodos de prueba están los métodos de aserción, como ``assertSame()``
   (ver :ref:`appendixes.assertions`), que se usan para determinar o aseverar si
   el valor real coincide con el valor esperado.

.. code-block:: php
    :caption: Probando operaciones sobre un arreglo con PHPUnit
    :name: writing-tests-for-phpunit.examples.StackTest.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class StackTest extends TestCase
    {
        public function testPushAndPop(): void
        {
            $stack = [];
            $this->assertSame(0, count($stack));

            array_push($stack, 'foo');
            $this->assertSame('foo', $stack[count($stack)-1]);
            $this->assertSame(1, count($stack));

            $this->assertSame('foo', array_pop($stack));
            $this->assertSame(0, count($stack));
        }
    }

|
    *Martin Fowler*:

    Siempre que estés intentando escribir algo en una sentencia ``print`` o
    en una expresión depuradora, escribe en su lugar una prueba.

.. _writing-tests-for-phpunit.test-dependencies:

Dependencia de Pruebas
######################

    *Adrian Kuhn et. al.*:

    Las pruebas unitarias se escriben principalmente como una buena práctica
    para ayudar a los desarrolladores a identificar y corregir errores,
    refactorizar el código y como documentación de la parte del software
    bajo prueba. Para alcanzar estos beneficios, las pruebas unitarias deberían
    idealmente cubrir todos los posibles caminos en un programa. Una prueba
    unitaria cubre generalmente una ruta de acción específica de un método o
    función. Sin embargo un método de prueba no es necesariamente una entidad
    encapsulada e independiente. A veces existen dependencias implícitas entre
    los métodos de prueba, dependencias escondidas en el escenario de
    implementación de una prueba.

PHPUnit soporta la declaración de dependencias explícitas entre métodos de
prueba. Estas dependencias no definen el orden en que los métodos de pruebas
deben ser ejecutados pero ellas permiten retornar una instancia con
los elementos necesarios para una prueba, pasándolos desde un productor hasta los
consumidores.

-

  Un productor es un método de prueba que ofrece a la parte del software bajo
  prueba un valor de retorno.

-

  Un consumidor es un método de prueba que depende de uno o más productores y
  de sus valores de retorno.

El :numref:`writing-tests-for-phpunit.examples.StackTest2.php` muestra como usar
la anotación ``@depends`` para expresar dependencias entre métodos de prueba.

.. code-block:: php
    :caption: Usar la anotación ``@depends`` para expresar dependencias
    :name: writing-tests-for-phpunit.examples.StackTest2.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class StackTest extends TestCase
    {
        public function testEmpty(): array
        {
            $stack = [];
            $this->assertEmpty($stack);

            return $stack;
        }

        /**
         * @depends testEmpty
         */
        public function testPush(array $stack): array
        {
            array_push($stack, 'foo');
            $this->assertSame('foo', $stack[count($stack)-1]);
            $this->assertNotEmpty($stack);

            return $stack;
        }

        /**
         * @depends testPush
         */
        public function testPop(array $stack): void
        {
            $this->assertSame('foo', array_pop($stack));
            $this->assertEmpty($stack);
        }
    }

En el ejemplo anterior, la primera prueba ``testEmpty()`` crea un nuevo
arreglo y asevera que esta vacío. Como resultado la prueba regresa un elemento.
La segunda prueba ``testPush()`` depende de ``testEmpty()``, en este caso,
se pasa como argumento el resultado de la prueba de la que depende. Finalmente,
``testPop()`` depende de ``testPush()``.

.. admonition:: Nota

   Por defecto el valor de retorno brindado por un productor se pasa «como está» a su
   consumidor. Esto significa que cuando un productor regresa un
   objeto, se pasa una referencia del objeto al consumidor. En lugar de una
   referencia es posible pasar o (a) una copia (profunda) con la anotación
   ``@depends clone`` o (b) una clonación (superficial normal), basada en la palabra
   clave de PHP ``clone``, con la anotación ``@depends shallowClone``.

Para localizar defectos rápidamente, queremos concentrar nuestra atención sobre
las pruebas fallidas relevantes. Por esta razón PHPUnit omite la ejecución de
una prueba cuando la prueba de la que depende falla. Esto mejora la detección
de los defectos, aprovechando las dependencias entre pruebas, como se muestra en
:numref:`writing-tests-for-phpunit.examples.DependencyFailureTest.php`.

.. code-block:: php
    :caption: Aprovechar las dependencias entre pruebas
    :name: writing-tests-for-phpunit.examples.DependencyFailureTest.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class DependencyFailureTest extends TestCase
    {
        public function testOne(): void
        {
            $this->assertTrue(false);
        }

        /**
         * @depends testOne
         */
        public function testTwo(): void
        {
        }
    }

.. parsed-literal::

    $ phpunit --verbose DependencyFailureTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    FS

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) DependencyFailureTest::testOne
    Failed asserting that false is true.

    /home/sb/DependencyFailureTest.php:6

    There was 1 skipped test:

    1) DependencyFailureTest::testTwo
    This test depends on "DependencyFailureTest::testOne" to pass.

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1, Skipped: 1.

Una prueba puede tener más de una anotación ``@depends``. PHPUnit no cambia el
orden en que las pruebas son ejecutadas, es necesario que las dependencias de
una prueba puedan ser encontradas antes de que la prueba sea ejecutada.

Una prueba que tiene más de una anotación ``@depends`` obtendrá como primer
argumento el resultado del primer productor, como segundo argumento el resultado
del segundo productor y así sucesivamente.
Ver :numref:`writing-tests-for-phpunit.examples.MultipleDependencies.php`

.. code-block:: php
    :caption: Prueba con multiples dependencias
    :name: writing-tests-for-phpunit.examples.MultipleDependencies.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class MultipleDependenciesTest extends TestCase
    {
        public function testProducerFirst(): string
        {
            $this->assertTrue(true);

            return 'first';
        }

        public function testProducerSecond(): string
        {
            $this->assertTrue(true);

            return 'second';
        }

        /**
         * @depends testProducerFirst
         * @depends testProducerSecond
         */
        public function testConsumer(string $a, string $b): void
        {
            $this->assertSame('first', $a);
            $this->assertSame('second', $b);
        }
    }

.. parsed-literal::

    $ phpunit --verbose MultipleDependenciesTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    ...

    Time: 0 seconds, Memory: 3.25Mb

    OK (3 tests, 4 assertions)

.. _writing-tests-for-phpunit.data-providers:

Proveedores de Datos
####################

Un método de prueba puede aceptar argumentos arbitrarios. Estos argumentos son
provistos por un método proveedor de datos (``additionProvider()`` en el
:numref:`writing-tests-for-phpunit.data-providers.examples.DataTest.php`).
El método proveedor de datos que queremos usar se especifica con la anotación
``@dataProvider``.

Un método proveedor de datos debe ser ``public`` y retornar una arreglo de
arreglos o un objeto que implementa la interfaz ``Iterator`` y produce un
arreglo en cada paso de la iteración. Para cada arreglo que es parte de la
colección se llama al método de prueba y el contenido del arreglo constituye
sus argumentos.

.. code-block:: php
    :caption: Usar un proveedor de datos que regresa un arreglo de arreglos
    :name: writing-tests-for-phpunit.data-providers.examples.DataTest.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class DataTest extends TestCase
    {
        /**
         * @dataProvider additionProvider
         */
        public function testAdd(int $a, int $b, int $expected): void
        {
            $this->assertSame($expected, $a + $b);
        }

        public function additionProvider(): array
        {
            return [
                [0, 0, 0],
                [0, 1, 1],
                [1, 0, 1],
                [1, 1, 3]
            ];
        }
    }

.. parsed-literal::

    $ phpunit DataTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    ...F

    Time: 0 seconds, Memory: 5.75Mb

    There was 1 failure:

    1) DataTest::testAdd with data set #3 (1, 1, 3)
    Failed asserting that 2 is identical to 3.

    /home/sb/DataTest.php:9

    FAILURES!
    Tests: 4, Assertions: 4, Failures: 1.

Cuando se usa un gran número de datos es útil colocar una cadena de caracteres
como llave en lugar de la numeración por defecto. La salida será más verbosa y
contendrá el nombre del conjunto de datos que hizo fallar la prueba.

.. code-block:: php
    :caption: Usar un proveedor de datos con un conjunto de datos etiquetado
    :name: writing-tests-for-phpunit.data-providers.examples.DataTest1.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class DataTest extends TestCase
    {
        /**
         * @dataProvider additionProvider
         */
        public function testAdd(int $a, int $b, int $expected): void
        {
            $this->assertSame($expected, $a + $b);
        }

        public function additionProvider(): array
        {
            return [
                'adding zeros'  => [0, 0, 0],
                'zero plus one' => [0, 1, 1],
                'one plus zero' => [1, 0, 1],
                'one plus one'  => [1, 1, 3]
            ];
        }
    }

.. parsed-literal::

    $ phpunit DataTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    ...F

    Time: 0 seconds, Memory: 5.75Mb

    There was 1 failure:

    1) DataTest::testAdd with data set "one plus one" (1, 1, 3)
    Failed asserting that 2 is identical to 3.

    /home/sb/DataTest.php:9

    FAILURES!
    Tests: 4, Assertions: 4, Failures: 1.

.. admonition:: Nota

    Con la anotación :ref:`appendixes.annotations.testdox` podemos hacer que la salida sea más
    abundante definiendo una oración y usando los nombres de los parámetros como comodín
    (``$a``, ``$b`` y ``$expected`` en el ejemplo de arriba). Además,  siempre que el conjunto
    de datos tenga nombre podemos hace referencia al nombre del conjunto de datos con ``$_dataName``.

.. code-block:: php
    :caption: Usar un proveedor de datos que regresa un objeto Iterador
    :name: writing-tests-for-phpunit.data-providers.examples.DataTest2.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class DataTest extends TestCase
    {
        /**
         * @dataProvider additionProvider
         */
        public function testAdd(int $a, int $b, int $expected): void
        {
            $this->assertSame($expected, $a + $b);
        }

        public function additionProvider(): CsvFileIterator
        {
            return new CsvFileIterator('data.csv');
        }
    }

.. parsed-literal::

    $ phpunit DataTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    ...F

    Time: 0 seconds, Memory: 5.75Mb

    There was 1 failure:

    1) DataTest::testAdd with data set #3 ('1', '1', '3')
    Failed asserting that 2 is identical to 3.

    /home/sb/DataTest.php:11

    FAILURES!
    Tests: 4, Assertions: 4, Failures: 1.

.. code-block:: php
    :caption: La clase CsvFileIterator
    :name: writing-tests-for-phpunit.data-providers.examples.CsvFileIterator.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class CsvFileIterator implements Iterator
    {
        private $file;
        private $key = 0;
        private $current;

        public function __construct(string $file)
        {
            $this->file = fopen($file, 'r');
        }

        public function __destruct()
        {
            fclose($this->file);
        }

        public function rewind(): void
        {
            rewind($this->file);

            $this->current = fgetcsv($this->file);

            if (is_array($this->current)) {
                $this->current = array_map('intval', $this->current);
            }

            $this->key = 0;
        }

        public function valid(): bool
        {
            return !feof($this->file);
        }

        public function key(): int
        {
            return $this->key;
        }

        public function current(): array
        {
            return $this->current;
        }

        public function next(): void
        {
            $this->current = fgetcsv($this->file);

            if (is_array($this->current)) {
                $this->current = array_map('intval', $this->current);
            }

            $this->key++;
        }
    }

Cuando un prueba recibe una entrada tanto desde un método proveedor
de datos ``@dataProvider`` como desde una o más pruebas de las que depende
(``@depends``), los argumentos del proveedor de datos regresarán antes que los
argumentos de las pruebas de las que depende. Los argumentos que se toman de las
pruebas que se marcan como dependencias serán los mismo para cada conjunto de datos.
Ver :numref:`writing-tests-for-phpunit.data-providers.examples.DependencyAndDataProviderCombo.php`

.. code-block:: php
    :caption: Combinación de @depends y @dataProvider en una misma prueba
    :name: writing-tests-for-phpunit.data-providers.examples.DependencyAndDataProviderCombo.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class DependencyAndDataProviderComboTest extends TestCase
    {
        public function provider(): array
        {
            return [['provider1'], ['provider2']];
        }

        public function testProducerFirst(): string
        {
            $this->assertTrue(true);

            return 'first';
        }

        public function testProducerSecond(): string
        {
            $this->assertTrue(true);

            return 'second';
        }

        /**
         * @depends testProducerFirst
         * @depends testProducerSecond
         * @dataProvider provider
         */
        public function testConsumer(): void
        {
            $this->assertSame(
                ['provider1', 'first', 'second'],
                func_get_args()
            );
        }
    }

.. parsed-literal::

    $ phpunit --verbose DependencyAndDataProviderComboTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    ...F

    Time: 0 seconds, Memory: 3.50Mb

    There was 1 failure:

    1) DependencyAndDataProviderComboTest::testConsumer with data set #1 ('provider2')
    Failed asserting that two arrays are identical.
    --- Expected
    +++ Actual
    @@ @@
    Array &0 (
    -    0 => 'provider1'
    +    0 => 'provider2'
         1 => 'first'
         2 => 'second'
    )
    /home/sb/DependencyAndDataProviderComboTest.php:32

    FAILURES!
    Tests: 4, Assertions: 4, Failures: 1.

.. code-block:: php
    :caption: Usar multiples proveedores de datos para una sola prueba
    :name: writing-tests-for-phpunit.data-providers.examples2.DataTest.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class DataTest extends TestCase
    {
        /**
         * @dataProvider additionWithNonNegativeNumbersProvider
         * @dataProvider additionWithNegativeNumbersProvider
         */
        public function testAdd(int $a, int $b, int $expected): void
        {
            $this->assertSame($expected, $a + $b);
        }

        public function additionWithNonNegativeNumbersProvider(): array
        {
            return [
                [0, 1, 1],
                [1, 0, 1],
                [1, 1, 3]
            ];
        }

        public function additionWithNegativeNumbersProvider(): array
        {
            return [
                [-1, 1, 0],
                [-1, -1, -2],
                [1, -1, 0]
            ];
        }
     }

.. parsed-literal::

    $ phpunit DataTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    ..F...                                                              6 / 6 (100%)

    Time: 0 seconds, Memory: 5.75Mb

    There was 1 failure:

    1) DataTest::testAdd with data set #3 (1, 1, 3)
    Failed asserting that 2 is identical to 3.

    /home/sb/DataTest.php:12

    FAILURES!
    Tests: 6, Assertions: 6, Failures: 1.

.. admonition:: Nota

   Cuando una prueba depende de otra prueba que usa un proveedor de datos, la
   prueba dependiente será ejecutada solo cuando la prueba de la que depende es
   exitosa para al menos un elemento del conjunto de datos. El resultado de una
   prueba que usa proveedores de datos no puede ser inyectado dentro de una
   prueba dependiente.

.. admonition:: Nota

   Todos los proveedores de datos son ejecutados antes de la llamada al método
   estático ``setUpBeforeClass()`` y de la primera llamada al método ``setUp()``.
   Por esta razón no es posible tener acceso a ninguna variable creada en estos
   métodos desde el proveedor de datos. Esto es necesario para que PHPUnit sea capaz
   de contar el numero total de pruebas.

.. _writing-tests-for-phpunit.exceptions:

Probar Excepciones
##################

El :numref:`writing-tests-for-phpunit.exceptions.examples.ExceptionTest.php`
muestra como usar el método ``expectException()`` para probar si una excepción
es lanzada por el código que se está probando.

.. code-block:: php
    :caption: Usar el método expectException()
    :name: writing-tests-for-phpunit.exceptions.examples.ExceptionTest.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class ExceptionTest extends TestCase
    {
        public function testException(): void
        {
            $this->expectException(InvalidArgumentException::class);
        }
    }

.. parsed-literal::

    $ phpunit ExceptionTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) ExceptionTest::testException
    Failed asserting that exception of type "InvalidArgumentException" is thrown.

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

Además del método ``expectException()`` existen los métodos
``expectExceptionCode()``, ``expectExceptionMessage()`` y
``expectExceptionMessageMatches()`` para establecer una predicción sobre las
excepciones lanzadas por el código que se está probando.

.. admonition:: Nota

   Nótese que ``expectExceptionMessage()`` asevera que el mensaje real
   (``$actual``) contiene el mensaje esperado (``$expected``) y no ejecuta una
   comparación exacta de cadenas de caracteres.

.. _writing-tests-for-phpunit.errors:

Probar los errores, avisos y notificaciones de PHP
##################################################

Con la configuración por defecto de PHPUnit los errores, avisos y notificaciones
de PHP que se disparan durante la ejecución de una prueba se convierten en
excepciones. Usando estas excepciones se puede, por ejemplo, esperar que una prueba
disparé un error, una aviso o una notificación de PHP, como se muestra en
:numref:`writing-tests-for-phpunit.exceptions.examples.ErrorTest.php`.

.. admonition:: Nota

   La configuración en tiempo de ejecución de PHP ``error_reporting`` puede
   limitar los errores que PHPUnit convertirá en excepciones. Si tenemos
   problemas con esta característica, debemos asegurarnos de que PHP no está
   configurado para eliminar los tipos de errores que estamos probando.

.. code-block:: php
    :caption: Esperar errores, avisos y notificaciones de PHP
    :name: writing-tests-for-phpunit.exceptions.examples.ErrorTest.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class ErrorTest extends TestCase
    {
        public function testDeprecationCanBeExpected(): void
        {
            $this->expectDeprecation();

            // Optionally test that the message is equal to a string
            $this->expectDeprecationMessage('foo');

            // Or optionally test that the message matches a regular expression
            $this->expectDeprecationMessageMatches('/foo/');

            \trigger_error('foo', \E_USER_DEPRECATED);
        }

        public function testNoticeCanBeExpected(): void
        {
            $this->expectNotice();

            // Optionally test that the message is equal to a string
            $this->expectNoticeMessage('foo');

            // Or optionally test that the message matches a regular expression
            $this->expectNoticeMessageMatches('/foo/');

            \trigger_error('foo', \E_USER_NOTICE);
        }

        public function testWarningCanBeExpected(): void
        {
            $this->expectWarning();

            // Optionally test that the message is equal to a string
            $this->expectWarningMessage('foo');

            // Or optionally test that the message matches a regular expression
            $this->expectWarningMessageMatches('/foo/');

            \trigger_error('foo', \E_USER_WARNING);
        }

        public function testErrorCanBeExpected(): void
        {
            $this->expectError();

            // Optionally test that the message is equal to a string
            $this->expectErrorMessage('foo');

            // Or optionally test that the message matches a regular expression
            $this->expectErrorMessageMatches('/foo/');

            \trigger_error('foo', \E_USER_ERROR);
        }
    }

Cuando la prueba depende de funciones «built-in» de PHP que podrían lanzan
errores, como ``fopen()``,
puede ser útil usar la supresión de errores mientras se prueba. Esto permite
revisar los valores retornados que sin la supresión de las notificaciones
llevaría a un excepción lanzada por el gestor de errores de PHPUnit.

.. code-block:: php
    :caption: Probar valores de retorno de un código que usa errores de PHP
    :name: writing-tests-for-phpunit.exceptions.examples.TriggerErrorReturnValue.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class ErrorSuppressionTest extends TestCase
    {
        public function testFileWriting(): void
        {
            $writer = new FileWriter;

            $this->assertFalse(@$writer->write('/is-not-writeable/file', 'stuff'));
        }
    }

    final class FileWriter
    {
        public function write($file, $content)
        {
            $file = fopen($file, 'w');

            if ($file === false) {
                return false;
            }

            // ...
        }
    }

.. parsed-literal::

    $ phpunit ErrorSuppressionTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    .

    Time: 1 seconds, Memory: 5.25Mb

    OK (1 test, 1 assertion)

Sin la supresión de errores la prueba fallaría reportando
``fopen(/is-not-writeable/file): failed to open stream: No such file or directory``.

.. _writing-tests-for-phpunit.output:

Probar Salidas
##############

A veces deseamos aseverar que la ejecución de un método genera la
salida prevista (por ejemplo, con ``echo`` o ``print``). La clase
``PHPUnit\Framework\TestCase`` usa la característica
`Funciones de Control
de Salida <http://www.php.net/manual/es/ref.outcontrol.php>`_
de PHP para proporcionar la funcionalidad que se necesita para esta tarea.

El :numref:`writing-tests-for-phpunit.output.examples.OutputTest.php`
muestra como usar el método ``expectOutputString()`` para establecer la salida
prevista. Si la salida prevista no se genera entonces la prueba se contará como un
fallo.

.. code-block:: php
    :caption: Probar la salida de una función o método
    :name: writing-tests-for-phpunit.output.examples.OutputTest.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class OutputTest extends TestCase
    {
        public function testExpectFooActualFoo(): void
        {
            $this->expectOutputString('foo');

            print 'foo';
        }

        public function testExpectBarActualBaz(): void
        {
            $this->expectOutputString('bar');

            print 'baz';
        }
    }

.. parsed-literal::

    $ phpunit OutputTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    .F

    Time: 0 seconds, Memory: 5.75Mb

    There was 1 failure:

    1) OutputTest::testExpectBarActualBaz
    Failed asserting that two strings are equal.
    --- Expected
    +++ Actual
    @@ @@
    -'bar'
    +'baz'

    FAILURES!
    Tests: 2, Assertions: 2, Failures: 1.

La :numref:`writing-tests-for-phpunit.output.tables.api`
muestra los métodos que se pueden usar para probar la salida

.. rst-class:: table
.. list-table:: Métodos para probar la salida
    :name: writing-tests-for-phpunit.output.tables.api
    :header-rows: 1

    * - Método
      - Propósito
    * - ``void expectOutputRegex(string $regularExpression)``
      - Define la salida esperada para coincidir con una ``$regularExpression``.
    * - ``void expectOutputString(string $expectedString)``
      - Define la salida esperada igual a ``$expectedString``.
    * - ``bool setOutputCallback(callable $callback)``
      - Define una función de retro llamada que se usa, por ejemplo, para
        normalizar la salida real.
    * - ``string getActualOutput()``
      - Trae la salida real.

.. admonition:: Nota

   Una prueba que emite una salida fallará en el modo estricto.

.. _writing-tests-for-phpunit.error-output:

Salida de Error
###############

Siempre que una prueba falla PHPUnit intenta proveer la mayor cantidad
de información de contexto que sea posible y que pueda ayudar a identificar el
problema.

.. code-block:: php
    :caption: Salida de error generada cuando la comparación entre arreglos falla
    :name: writing-tests-for-phpunit.error-output.examples.ArrayDiffTest.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class ArrayDiffTest extends TestCase
    {
        public function testEquality(): void
        {
            $this->assertSame(
                [1, 2,  3, 4, 5, 6],
                [1, 2, 33, 4, 5, 6]
            );
        }
    }

.. parsed-literal::

    $ phpunit ArrayDiffTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.25Mb

    There was 1 failure:

    1) ArrayDiffTest::testEquality
    Failed asserting that two arrays are identical.
    --- Expected
    +++ Actual
    @@ @@
     Array (
         0 => 1
         1 => 2
    -    2 => 3
    +    2 => 33
         3 => 4
         4 => 5
         5 => 6
     )

    /home/sb/ArrayDiffTest.php:7

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

En este ejemplo solo uno de los valores del arreglo es diferente, los otros
valores se muestran para dar el contexto y señalar donde ocurrió el error.

Cuando la salida generada es demasiado larga de leer, PHPUnit la separará
y mostrará unas pocas lineas de información alrededor de cada diferencia.

.. code-block:: php
    :caption: Salida de error cuando falla la comparación entre arreglos muy largos
    :name: writing-tests-for-phpunit.error-output.examples.LongArrayDiffTest.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class LongArrayDiffTest extends TestCase
    {
        public function testEquality(): void
        {
            $this->assertSame(
                [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 2,  3, 4, 5, 6],
                [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 2, 33, 4, 5, 6]
            );
        }
    }

.. parsed-literal::

    $ phpunit LongArrayDiffTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.25Mb

    There was 1 failure:

    1) LongArrayDiffTest::testEquality
    Failed asserting that two arrays are identical.
    --- Expected
    +++ Actual
    @@ @@
         11 => 0
         12 => 1
         13 => 2
    -    14 => 3
    +    14 => 33
         15 => 4
         16 => 5
         17 => 6
     )

    /home/sb/LongArrayDiffTest.php:7

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _writing-tests-for-phpunit.error-output.edge-cases:

Casos Límites
=============

Cuando una comparación falla PHPUnit crea una representación textual
de los valores de entrada y los compara. Debido a esta implementación,
un *diff* puede mostrar más problemas de los que realmente existen.

Esto solo sucede cuando se usa ``assertEquals()`` u otras funciones de comparación
«débil» sobre arreglos u objetos.

.. code-block:: php
    :caption: Caso límite al generar la diferencia cuando se usa comparación débil
    :name: writing-tests-for-phpunit.error-output.edge-cases.examples.ArrayWeakComparisonTest.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class ArrayWeakComparisonTest extends TestCase
    {
        public function testEquality(): void
        {
            $this->assertEquals(
                [1, 2, 3, 4, 5, 6],
                ['1', 2, 33, 4, 5, 6]
            );
        }
    }

.. parsed-literal::

    $ phpunit ArrayWeakComparisonTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.25Mb

    There was 1 failure:

    1) ArrayWeakComparisonTest::testEquality
    Failed asserting that two arrays are equal.
    --- Expected
    +++ Actual
    @@ @@
     Array (
    -    0 => 1
    +    0 => '1'
         1 => 2
    -    2 => 3
    +    2 => 33
         3 => 4
         4 => 5
         5 => 6
     )

    /home/sb/ArrayWeakComparisonTest.php:7

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

En este ejemplo la diferencia en el primer índice, entre ``1`` y ``'1'``, se
reporta aunque ``assertEquals()`` considera a estos valores como iguales.
