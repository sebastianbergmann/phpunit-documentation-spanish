

.. _logging:

=======
Registro
=======

PHPUnit puede producir una serie de archivos de registro.

.. _logging.xml:

Resultados de Pruebas (XML)
##################

El archivo de registro de resultados de pruebas en XML de PHPUnit está basado en el
utilizado por esta `tarea JUnit para Apache Ant <http://ant.apache.org/manual/Tasks/junit.html>`_. El siguiente ejemplo muestra el archivo XML generado por las pruebas en ``ArrayTest``:

.. code-block:: bash

    <?xml version="1.0" encoding="UTF-8"?>
    <testsuites>
      <testsuite name="ArrayTest"
                 file="/home/sb/ArrayTest.php"
                 tests="2"
                 assertions="2"
                 failures="0"
                 errors="0"
                 time="0.016030">
        <testcase name="testNewArrayIsEmpty"
                  class="ArrayTest"
                  file="/home/sb/ArrayTest.php"
                  line="6"
                  assertions="1"
                  time="0.008044"/>
        <testcase name="testArrayContainsAnElement"
                  class="ArrayTest"
                  file="/home/sb/ArrayTest.php"
                  line="15"
                  assertions="1"
                  time="0.007986"/>
      </testsuite>
    </testsuites>

El siguiente archivo XML fue generado por dos pruebas,
``testFailure`` y ``testError``,
de una clase de pruebas llamada ``FailureErrorTest`` y
muestra cómo se guardarán los fallos y errores.

.. code-block:: bash

    <?xml version="1.0" encoding="UTF-8"?>
    <testsuites>
      <testsuite name="FailureErrorTest"
                 file="/home/sb/FailureErrorTest.php"
                 tests="2"
                 assertions="1"
                 failures="1"
                 errors="1"
                 time="0.019744">
        <testcase name="testFailure"
                  class="FailureErrorTest"
                  file="/home/sb/FailureErrorTest.php"
                  line="6"
                  assertions="1"
                  time="0.011456">
          <failure type="PHPUnit_Framework_ExpectationFailedException">
    testFailure(FailureErrorTest)
    Failed asserting that &lt;integer:2&gt; matches expected value &lt;integer:1&gt;.

    /home/sb/FailureErrorTest.php:8
    </failure>
        </testcase>
        <testcase name="testError"
                  class="FailureErrorTest"
                  file="/home/sb/FailureErrorTest.php"
                  line="11"
                  assertions="0"
                  time="0.008288">
          <error type="Exception">testError(FailureErrorTest)
    Exception:

    /home/sb/FailureErrorTest.php:13
    </error>
        </testcase>
      </testsuite>
    </testsuites>

.. _logging.codecoverage.xml:

Cobertura de Código (XML)
###################

El formato XML para la información sobre cobertura de código producido por PHPUnit está ligeramente basado en el utilizado por `Clover <http://www.atlassian.com/software/clover/>`_. El siguiente ejemplo muestra el registro XML generado por las pruebas en ``BankAccountTest``:

.. code-block:: bash

    <?xml version="1.0" encoding="UTF-8"?>
    <coverage generated="1184835473" phpunit="3.6.0">
      <project name="BankAccountTest" timestamp="1184835473">
        <file name="/home/sb/BankAccount.php">
          <class name="BankAccountException">
            <metrics methods="0" coveredmethods="0" statements="0"
                     coveredstatements="0" elements="0" coveredelements="0"/>
          </class>
          <class name="BankAccount">
            <metrics methods="4" coveredmethods="4" statements="13"
                     coveredstatements="5" elements="17" coveredelements="9"/>
          </class>
          <line num="77" type="method" count="3"/>
          <line num="79" type="stmt" count="3"/>
          <line num="89" type="method" count="2"/>
          <line num="91" type="stmt" count="2"/>
          <line num="92" type="stmt" count="0"/>
          <line num="93" type="stmt" count="0"/>
          <line num="94" type="stmt" count="2"/>
          <line num="96" type="stmt" count="0"/>
          <line num="105" type="method" count="1"/>
          <line num="107" type="stmt" count="1"/>
          <line num="109" type="stmt" count="0"/>
          <line num="119" type="method" count="1"/>
          <line num="121" type="stmt" count="1"/>
          <line num="123" type="stmt" count="0"/>
          <metrics loc="126" ncloc="37" classes="2" methods="4" coveredmethods="4"
                   statements="13" coveredstatements="5" elements="17"
                   coveredelements="9"/>
        </file>
        <metrics files="1" loc="126" ncloc="37" classes="2" methods="4"
                 coveredmethods="4" statements="13" coveredstatements="5"
                 elements="17" coveredelements="9"/>
      </project>
    </coverage>

.. _logging.codecoverage.text:

Cobertura de Código (TEXT)
####################

La cobertura de código en texto plano puede salir por la consola o a un archivo de texto.

El objetivo de este formato es proveer de un visual de la cobertura de código mientras se prueban un grupo pequeño de clases. Para proyectos más grandes esta salida puede ser útil para obtener una visión rápida o conjuntamente con la funcionalidad ``--filter``.
Cuando lo usamos desde la consola será escrito en ``php://stdout``;
esto hara honra a la opción ``--colors``.
Escribir a la consola es la opción por defecto cuando se llama al comando.
Por defecto esto mostrará solo los archivos que tengan al menos una línea cubierta.
Esto solo puede cambiar con la opción XML ``showUncoveredFiles``. Mirar :ref:`appendixes.configuration.logging`.
Por defecto todos los archivos y sus estados de cobertura son mostrados en el informe detallado.
Esto puede cambiar con la opcion XML ``showOnlySummary``.


