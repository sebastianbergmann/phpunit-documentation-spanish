

.. _extending-phpunit:

=================
Extending PHPUnit
=================

PHPUnit can be extended in various ways to make the writing of tests
easier and customize the feedback you get from running tests. Here are
common starting points to extend PHPUnit.

.. _extending-phpunit.PHPUnit_Framework_TestCase:

Subclass PHPUnit\\Framework\\TestCase
#####################################

Write custom assertions and utility methods in an abstract subclass of
``PHPUnit\Framework\TestCase`` and derive your test case
classes from that class. This is one of the easiest ways to extend
PHPUnit.

.. _extending-phpunit.custom-assertions:

Write custom assertions
#######################

When writing custom assertions it is the best practice to follow how
PHPUnit's own assertions are implemented. As you can see in
:numref:`extending-phpunit.examples.Assert.php`, the
``assertTrue()`` method is just a wrapper around the
``isTrue()`` and ``assertThat()`` methods:
``isTrue()`` creates a matcher object that is passed on to
``assertThat()`` for evaluation.

.. code-block:: php
    :caption: The assertTrue() and isTrue() methods of the PHPUnit_Framework_Assert class
    :name: extending-phpunit.examples.Assert.php

    <?php
    use PHPUnit\Framework\TestCase;

    abstract class PHPUnit_Framework_Assert
    {
        // ...

        /**
         * Asserts that a condition is true.
         *
         * @param  boolean $condition
         * @param  string  $message
         * @throws PHPUnit_Framework_AssertionFailedError
         */
        public static function assertTrue($condition, $message = '')
        {
            self::assertThat($condition, self::isTrue(), $message);
        }

        // ...

        /**
         * Returns a PHPUnit_Framework_Constraint_IsTrue matcher object.
         *
         * @return PHPUnit_Framework_Constraint_IsTrue
         * @since  Method available since Release 3.3.0
         */
        public static function isTrue()
        {
            return new PHPUnit_Framework_Constraint_IsTrue;
        }

        // ...
    }?>

:numref:`extending-phpunit.examples.IsTrue.php` shows how
``PHPUnit_Framework_Constraint_IsTrue`` extends the
abstract base class for matcher objects (or constraints),
``PHPUnit_Framework_Constraint``.

.. code-block:: php
    :caption: The PHPUnit_Framework_Constraint_IsTrue class
    :name: extending-phpunit.examples.IsTrue.php

    <?php
    use PHPUnit\Framework\TestCase;

    class PHPUnit_Framework_Constraint_IsTrue extends PHPUnit_Framework_Constraint
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
    }?>

The effort of implementing the ``assertTrue()`` and
``isTrue()`` methods as well as the
``PHPUnit_Framework_Constraint_IsTrue`` class yields the
benefit that ``assertThat()`` automatically takes care of
evaluating the assertion and bookkeeping tasks such as counting it for
statistics. Furthermore, the ``isTrue()`` method can be
used as a matcher when configuring mock objects.

.. _extending-phpunit.PHPUnit_Framework_TestListener:

Implement PHPUnit\\Framework\\TestListener
##########################################

:numref:`extending-phpunit.examples.SimpleTestListener.php`
shows a simple implementation of the ``PHPUnit\Framework\TestListener``
interface.

.. code-block:: php
    :caption: A simple test listener
    :name: extending-phpunit.examples.SimpleTestListener.php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\Framework\TestListener;

    class SimpleTestListener implements TestListener
    {
        public function addError(PHPUnit_Framework_Test $test, Exception $e, $time)
        {
            printf("Error while running test '%s'.\n", $test->getName());
        }

        public function addFailure(PHPUnit_Framework_Test $test, PHPUnit_Framework_AssertionFailedError $e, $time)
        {
            printf("Test '%s' failed.\n", $test->getName());
        }

        public function addIncompleteTest(PHPUnit_Framework_Test $test, Exception $e, $time)
        {
            printf("Test '%s' is incomplete.\n", $test->getName());
        }

        public function addRiskyTest(PHPUnit_Framework_Test $test, Exception $e, $time)
        {
            printf("Test '%s' is deemed risky.\n", $test->getName());
        }

        public function addSkippedTest(PHPUnit_Framework_Test $test, Exception $e, $time)
        {
            printf("Test '%s' has been skipped.\n", $test->getName());
        }

        public function startTest(PHPUnit_Framework_Test $test)
        {
            printf("Test '%s' started.\n", $test->getName());
        }

        public function endTest(PHPUnit_Framework_Test $test, $time)
        {
            printf("Test '%s' ended.\n", $test->getName());
        }

        public function startTestSuite(PHPUnit_Framework_TestSuite $suite)
        {
            printf("TestSuite '%s' started.\n", $suite->getName());
        }

        public function endTestSuite(PHPUnit_Framework_TestSuite $suite)
        {
            printf("TestSuite '%s' ended.\n", $suite->getName());
        }
    }
    ?>

:numref:`extending-phpunit.examples.BaseTestListener.php`
shows how to subclass the ``PHPUnit_Framework_BaseTestListener``
abstract class, which lets you specify only the interface methods that
are interesting for your use case, while providing empty implementations
for all the others.

.. code-block:: php
    :caption: Using base test listener
    :name: extending-phpunit.examples.BaseTestListener.php

    <?php
    use PHPUnit\Framework\TestCase;

    class ShortTestListener extends PHPUnit_Framework_BaseTestListener
    {
        public function endTest(PHPUnit_Framework_Test $test, $time)
        {
            printf("Test '%s' ended.\n", $test->getName());
        }
    }
    ?>

In :ref:`appendixes.configuration.test-listeners` you can see
how to configure PHPUnit to attach your test listener to the test
execution.

.. _extending-phpunit.PHPUnit_Framework_Test:

Implement PHPUnit_Framework_Test
################################

The ``PHPUnit_Framework_Test`` interface is narrow and
easy to implement. You can write an implementation of
``PHPUnit_Framework_Test`` that is simpler than
``PHPUnit\Framework\TestCase`` and that runs
*data-driven tests*, for instance.

:numref:`extending-phpunit.examples.DataDrivenTest.php`
shows a data-driven test case class that compares values from a file
with Comma-Separated Values (CSV). Each line of such a file looks like
``foo;bar``, where the first value is the one we expect
and the second value is the actual one.

.. code-block:: php
    :caption: A data-driven test
    :name: extending-phpunit.examples.DataDrivenTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class DataDrivenTest implements PHPUnit_Framework_Test
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

        public function run(PHPUnit_Framework_TestResult $result = null)
        {
            if ($result === null) {
                $result = new PHPUnit_Framework_TestResult;
            }

            foreach ($this->lines as $line) {
                $result->startTest($this);
                PHP_Timer::start();
                $stopTime = null;

                list($expected, $actual) = explode(';', $line);

                try {
                    PHPUnit_Framework_Assert::assertEquals(
                      trim($expected), trim($actual)
                    );
                }

                catch (PHPUnit_Framework_AssertionFailedError $e) {
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
    $result = PHPUnit_TextUI_TestRunner::run($test);
    ?>

.. code-block:: bash

    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

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


