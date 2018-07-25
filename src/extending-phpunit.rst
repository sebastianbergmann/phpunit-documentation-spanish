

.. _extending-phpunit:

================
Extender PHPUnit
================

PHPUnit se puede extender de varias maneras para hacer la escritura de las pruebas
una tarea más fácil y para personalizar los mensajes que se obtienen a partir
de la ejecución de las pruebas. Aquí presentamos los puntos de partida comunes
para extender PHPUnit.

.. _extending-phpunit.PHPUnit_Framework_TestCase:

La Subclase PHPUnit\\Framework\\TestCase
########################################

Una de las maneras más fáciles de extender PHPUnit es
escribir aserciones personalizadas y métodos útiles en una subclase abstracta
de ``PHPUnit\Framework\TestCase`` y derivar nuestros clases de casos de prueba
desde esta clase.

.. _extending-phpunit.custom-assertions:

Escribir aserciones personalizadas
##################################

Cuando escribimos aserciones personalizadas es bueno seguir la manera como
PHPUnit implementa sus propias aserciones. Como podemos ver en el
:numref:`extending-phpunit.examples.Assert.php`, el método ``assertTrue()`` solo
es un envoltorio alrededor de los métodos ``isTrue()`` y ``assertThat()``.
``isTrue()`` crea un objeto con el que hacer la comparación que se pasa al
método ``assertThat()`` para ser evaluado.

.. code-block:: php
    :caption: Los métodos assertTrue() y isTrue() de la clase PHPUnit\Framework\Assert
    :name: extending-phpunit.examples.Assert.php

    <?php
    namespace PHPUnit\Framework;

    use PHPUnit\Framework\TestCase;

    abstract class Assert
    {
        // ...

        /**
         * Asserts that a condition is true.
         *
         * @param  boolean $condition
         * @param  string  $message
         * @throws PHPUnit\Framework\AssertionFailedError
         */
        public static function assertTrue($condition, $message = '')
        {
            self::assertThat($condition, self::isTrue(), $message);
        }

        // ...

        /**
         * Returns a PHPUnit\Framework\Constraint\IsTrue matcher object.
         *
         * @return PHPUnit\Framework\Constraint\IsTrue
         * @since  Method available since Release 3.3.0
         */
        public static function isTrue()
        {
            return new PHPUnit\Framework\Constraint\IsTrue;
        }

        // ...
    }

El :numref:`extending-phpunit.examples.IsTrue.php` muestra como la clase
``PHPUnit\Framework\Constraint\IsTrue`` extiende a la clase base abstracta
``PHPUnit\Framework\Constraint`` para construir los objetos de comparación
(o restricciones).

.. code-block:: php
    :caption: La clase PHPUnit\Framework\Constraint\IsTrue
    :name: extending-phpunit.examples.IsTrue.php

    <?php
    namespace PHPUnit\Framework\Constraint;

    use PHPUnit\Framework\Constraint;

    class IsTrue extends Constraint
    {
        /**
         * Evaluates the constraint for parameter $other. Returns true if the
         * constraint is met, false otherwise.
         *
         * @param mixed $other Value or object to evaluate.
         * @return bool
         */
        public function matches($other)
        {
            return $other === true;
        }

        /**
         * Returns a string representation of the constraint.
         *
         * @return string
         */
        public function toString()
        {
            return 'is true';
        }
    }

La ventaja de implementar los métodos ``assertTrue()`` y ``isTrue()`` de la misma
manera que la clase ``PHPUnit\Framework\Constraint\IsTrue`` está en que ``assertThat()``
automáticamente contabiliza la evaluación de la aserción para incluirla en las
estadísticas. Además, el método ``isTrue()`` se puede usar como un objeto para
la comparación cuando se configuran objetos falsos.

.. _extending-phpunit.PHPUnit_Framework_TestListener:

Implementar PHPUnit\\Framework\\TestListener
############################################

El :numref:`extending-phpunit.examples.SimpleTestListener.php` muestra una
implementación simple de la interfaz ``PHPUnit\Framework\TestListener``.

.. code-block:: php
    :caption: Un escucha, «listener», de pruebas simple
    :name: extending-phpunit.examples.SimpleTestListener.php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\Framework\TestListener;

    class SimpleTestListener implements TestListener
    {
        public function addError(PHPUnit\Framework\Test $test, Exception $e, $time): void
        {
            printf("Error while running test '%s'.\n", $test->getName());
        }

        public function addWarning(PHPUnit\Framework\Test $test, PHPUnit\Framework\Warning $e, float $time): void
        {
            printf("Warning while running test '%s'.\n", $test->getName());
        }

        public function addFailure(PHPUnit\Framework\Test $test, PHPUnit\Framework\AssertionFailedError $e, $time): void
        {
            printf("Test '%s' failed.\n", $test->getName());
        }

        public function addIncompleteTest(PHPUnit\Framework\Test $test, Exception $e, $time): void
        {
            printf("Test '%s' is incomplete.\n", $test->getName());
        }

        public function addRiskyTest(PHPUnit\Framework\Test $test, Exception $e, $time): void
        {
            printf("Test '%s' is deemed risky.\n", $test->getName());
        }

        public function addSkippedTest(PHPUnit\Framework\Test $test, Exception $e, $time): void
        {
            printf("Test '%s' has been skipped.\n", $test->getName());
        }

        public function startTest(PHPUnit\Framework\Test $test): void
        {
            printf("Test '%s' started.\n", $test->getName());
        }

        public function endTest(PHPUnit\Framework\Test $test, $time): void
        {
            printf("Test '%s' ended.\n", $test->getName());
        }

        public function startTestSuite(PHPUnit\Framework\TestSuite $suite): void
        {
            printf("TestSuite '%s' started.\n", $suite->getName());
        }

        public function endTestSuite(PHPUnit\Framework\TestSuite $suite): void
        {
            printf("TestSuite '%s' ended.\n", $suite->getName());
        }
    }

El :numref:`extending-phpunit.examples.ExtendedTestListener.php` muestra como
usar un el «trait» ``PHPUnit\Framework\TestListenerDefaultImplementation``,
que nos permite especificar sola los métodos de interfaz que son interesantes
para nuestro caso de uso, mientras que se proveen implementaciones vacías para
todos los otros.

.. code-block:: php
    :caption: Usar la implementación «trait» por defecto en un escucha de pruebas
    :name: extending-phpunit.examples.ExtendedTestListener.php

    <?php
    use PHPUnit\Framework\TestListener;
    use PHPUnit\Framework\TestListenerDefaultImplementation;

    class ShortTestListener implements TestListener
    {
        use TestListenerDefaultImplementation;

        public function endTest(PHPUnit\Framework\Test $test, $time): void
        {
            printf("Test '%s' ended.\n", $test->getName());
        }
    }

En el :ref:`appendixes.configuration.test-listeners` podemos ver como configurar
PHPUnit para adjuntar nuestro escucha de pruebas a la ejecución de una prueba.

.. _extending-phpunit.PHPUnit_Framework_Test:

Implementar PHPUnit\Framework\Test
##################################

La interfaz ``PHPUnit\Framework\Test`` es pequeña y fácil de implementar.
Por ejemplo, podemos escribir una implementación de ``PHPUnit\Framework\Test``
que sea más simple que ``PHPUnit\Framework\TestCase`` y que ejecuten las
*pruebas dirigidas por datos*.

El :numref:`extending-phpunit.examples.DataDrivenTest.php` muestra una
clase de casos de pruebas dirigida por datos que usa un archivo con valores
separados por comas (CSV). Cada línea del archivo es de la forma ``foo;bar``,
donde el primer valor es el valor esperado y el segundo es el valor real.

.. code-block:: php
    :caption: Una prueba dirigida por datos
    :name: extending-phpunit.examples.DataDrivenTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class DataDrivenTest implements PHPUnit\Framework\Test
    {
        private $lines;

        public function __construct($dataFile)
        {
            $this->lines = file($dataFile);
        }

        public function count()
        {
            return 1;
        }

        public function run(PHPUnit\Framework\TestResult $result = null)
        {
            if ($result === null) {
                $result = new PHPUnit\Framework\TestResult;
            }

            foreach ($this->lines as $line) {
                $result->startTest($this);
                PHP_Timer::start();
                $stopTime = null;

                list($expected, $actual) = explode(';', $line);

                try {
                    PHPUnit\Framework\Assert::assertEquals(
                      trim($expected), trim($actual)
                    );
                }

                catch (PHPUnit\Framework\AssertionFailedError $e) {
                    $stopTime = PHP_Timer::stop();
                    $result->addFailure($this, $e, $stopTime);
                }

                catch (Exception $e) {
                    $stopTime = PHP_Timer::stop();
                    $result->addError($this, $e, $stopTime);
                }

                if ($stopTime === null) {
                    $stopTime = PHP_Timer::stop();
                }

                $result->endTest($this, $stopTime);
            }

            return $result;
        }
    }

    $test = new DataDrivenTest('data_file.csv');
    $result = PHPUnit\TextUI\TestRunner::run($test);

.. code-block:: bash

    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    .F

    Time: 0 seconds

    There was 1 failure:

    1) DataDrivenTest
    Failed asserting that two strings are equal.
    expected string <bar>
    difference      <  x>
    got string      <baz>
    /home/sb/DataDrivenTest.php:32
    /home/sb/DataDrivenTest.php:53

    FAILURES!
    Tests: 2, Failures: 1.

.. _extending-phpunit.TestRunner:

Extender TestRunner
###################

PHPUnit |version| soporta extensiones para TestRunner que se pueden enganchar
a varios eventos durante la ejecución de la prueba. Para más detalles sobre como
registrar extensiones en la configuración XML de PHPUnit podemos ver
:ref:`appendixes.configuration.extensions`.

Cada evento disponible al que la extensión se puede enganchar se representa
con una interfaz que la extensión debe implementar. La lista de eventos
disponibles en PHPUnit |version| se puede ver :ref:`extending-phpunit.hooks`.

.. _extending-phpunit.hooks:

Interfaces de Enganche Disponibles
----------------------------------

- ``AfterIncompleteTestHook``
- ``AfterLastTestHook``
- ``AfterRiskyTestHook``
- ``AfterSkippedTestHook``
- ``AfterSuccessfulTestHook``
- ``AfterTestErrorHook``
- ``AfterTestFailureHook``
- ``AfterTestWarningHook``
- ``BeforeFirstTestHook``
- ``BeforeTestHook``

El :numref:`extending-phpunit.examples.TestRunnerExtension` muestra un ejemplo
para una extensión que implementa las interfaces ``BeforeFirstTestHook`` y
``AfterLastTestHook``.

.. code-block:: php
    :caption: Ejemplo de Extensión para el TestRunner
    :name: extending-phpunit.examples.TestRunnerExtension

    <?php

    namespace Vendor;

    use PHPUnit\Runner\AfterLastTestHook;
    use PHPUnit\Runner\BeforeFirstTestHook;

    final class MyExtension implements BeforeFirstTestHook, AfterLastTestHook
    {
        public function executeAfterLastTest(): void
        {
            // called after the last test has been run
        }

        public function executeBeforeFirstTest(): void
        {
            // called before the first test is being run
        }
    }
