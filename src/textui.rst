

.. _textui:

==============================================
El Ejecutor de Pruebas desde Línea de Comandos
==============================================

El ejecutor de pruebas desde línea de comandos de PHPUnit se puede invocar
con el comando :file:`phpunit`. El siguiente código muestra como ejecutar las
pruebas con el ejecutor de pruebas desde línea de comandos:

.. code-block:: bash

    $ phpunit ArrayTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    ..

    Time: 0 seconds

    OK (2 tests, 2 assertions)

Cuando se ejecuta de la manera que se muestra arriba, el ejecutor de pruebas
desde línea de comandos buscará el archivo fuente :file:`ArrayTest.php` en la
carpeta actual, lo cargará y esperará encontrar la clase de caso de prueba
``ArrayTest``. Y luego se ejecutaran las pruebas de la clase.

Para cada prueba que se ejecuta, la herramienta de línea de comandos imprime
un carácter para indicar el progreso:

``.``

    Se imprime cuando la prueba es exitosa.

``F``

    Se imprime cuando una aserción falla al ejecutarse el método de prueba.

``E``

    Se imprime cuando ocurre un error mientras se ejecuta el método de prueba.

``R``

    Se imprime cuando una prueba se ha marcado como riesgosa (ver
    :ref:`risky-tests`).

``S``

    Se imprime cuando la prueba ha sido omitida (ver
    :ref:`incomplete-and-skipped-tests`).

``I``

    Se imprime cuando la prueba se marca como incompleta o aún no implementada
    (ver :ref:`incomplete-and-skipped-tests`).

PHPUnit distingue entre *fallas* y *errores*. Una falla es una aserción de
PHPUnit violada, como cuando la llamada a ``assertSame()`` falla. Un error
es una excepción inesperada o un error de PHP. A veces esta distinción muestra
ser útil porque los errores tienden a ser más fáciles de corregir que las
fallas. Si tenemos una lista grande de problemas, es mejor resolver
primero los errores y ver si queda alguna falla luego de que todos los errores
están corregidos.

.. _textui.clioptions:

Opciones de la línea de comandos
################################

Vamos a dar una ojeada a las opciones del ejecutor de pruebas desde línea
de comandos en el siguiente código:

.. code-block:: bash

    $ phpunit --help
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    Usage: phpunit [options] UnitTest [UnitTest.php]
           phpunit [options] <directory>

    Code Coverage Options:

      --coverage-clover <file>    Generate code coverage report in Clover XML format.
      --coverage-crap4j <file>    Generate code coverage report in Crap4J XML format.
      --coverage-html <dir>       Generate code coverage report in HTML format.
      --coverage-php <file>       Export PHP_CodeCoverage object to file.
      --coverage-text=<file>      Generate code coverage report in text format.
                                  Default: Standard output.
      --coverage-xml <dir>        Generate code coverage report in PHPUnit XML format.
      --whitelist <dir>           Whitelist <dir> for code coverage analysis.
      --disable-coverage-ignore   Disable annotations for ignoring code coverage.

    Logging Options:

      --log-junit <file>          Log test execution in JUnit XML format to file.
      --log-teamcity <file>       Log test execution in TeamCity format to file.
      --testdox-html <file>       Write agile documentation in HTML format to file.
      --testdox-text <file>       Write agile documentation in Text format to file.
      --testdox-xml <file>        Write agile documentation in XML format to file.
      --reverse-list              Print defects in reverse order

    Test Selection Options:

      --filter <pattern>          Filter which tests to run.
      --testsuite <name,...>      Filter which testsuite to run.
      --group ...                 Only runs tests from the specified group(s).
      --exclude-group ...         Exclude tests from the specified group(s).
      --list-groups               List available test groups.
      --list-suites               List available test suites.
      --test-suffix ...           Only search for test in files with specified
                                  suffix(es). Default: Test.php,.phpt

    Test Execution Options:

      --dont-report-useless-tests Do not report tests that do not test anything.
      --strict-coverage           Be strict about @covers annotation usage.
      --strict-global-state       Be strict about changes to global state
      --disallow-test-output      Be strict about output during tests.
      --disallow-resource-usage   Be strict about resource usage during small tests.
      --enforce-time-limit        Enforce time limit based on test size.
      --disallow-todo-tests       Disallow @todo-annotated tests.

      --process-isolation         Run each test in a separate PHP process.
      --globals-backup            Backup and restore $GLOBALS for each test.
      --static-backup             Backup and restore static attributes for each test.

      --colors=<flag>             Use colors in output ("never", "auto" or "always").
      --columns <n>               Number of columns to use for progress output.
      --columns max               Use maximum number of columns for progress output.
      --stderr                    Write to STDERR instead of STDOUT.
      --stop-on-error             Stop execution upon first error.
      --stop-on-failure           Stop execution upon first error or failure.
      --stop-on-warning           Stop execution upon first warning.
      --stop-on-risky             Stop execution upon first risky test.
      --stop-on-skipped           Stop execution upon first skipped test.
      --stop-on-incomplete        Stop execution upon first incomplete test.
      --fail-on-warning           Treat tests with warnings as failures.
      --fail-on-risky             Treat risky tests as failures.
      -v|--verbose                Output more verbose information.
      --debug                     Display debugging information.

      --loader <loader>           TestSuiteLoader implementation to use.
      --repeat <times>            Runs the test(s) repeatedly.
      --teamcity                  Report test execution progress in TeamCity format.
      --testdox                   Report test execution progress in TestDox format.
      --testdox-group             Only include tests from the specified group(s).
      --testdox-exclude-group     Exclude tests from the specified group(s).
      --printer <printer>         TestListener implementation to use.

    Configuration Options:

      --bootstrap <file>          A "bootstrap" PHP file that is run before the tests.
      -c|--configuration <file>   Read configuration from XML file.
      --no-configuration          Ignore default configuration file (phpunit.xml).
      --no-coverage               Ignore code coverage configuration.
      --no-extensions             Do not load PHPUnit extensions.
      --include-path <path(s)>    Prepend PHP's include_path with given path(s).
      -d key[=value]              Sets a php.ini value.
      --generate-configuration    Generate configuration file with suggested settings.

    Miscellaneous Options:

      -h|--help                   Prints this usage information.
      --version                   Prints the version and exits.
      --atleast-version <min>     Checks that version is greater than min and exits.

``phpunit UnitTest``

    Ejecuta las pruebas que se encuentran en la clase ``UnitTest``. Se espera
    que esta clase este declarada en el archivo fuente :file:`UnitTest.php`.

    ``UnitTest`` debe ser una clase que hereda de ``PHPUnit\Framework\TestCase``
    o una clase que provee un método ``public static suite()`` que regresa un
    objeto ``PHPUnit_Framework_Test``, por ejemplo una instancia de la clase
    ``PHPUnit_Framework_TestSuite``.

``phpunit UnitTest UnitTest.php``

    Ejecuta las pruebas que están en la clase ``UnitTest``. Se espera que esta
    clase este declarada en el archivo fuente que se especifica.

``--coverage-clover``

    Genera un archivo de registro en formato XML con la información de la
    cobertura de código de las pruebas ejecutadas. Ver :ref:`logging` para más
    detalles.

    Nótese que esta funcionalidad solo está disponible cuando las extensiones
    tokenizer y Xdebug están instaladas.

``--coverage-crap4j``

    Genera un reporte de cobertura de código en formato Crap4J. Para más
    detalles se puede ver :ref:`code-coverage-analysis`.

    Nótese que esta funcionalidad solo está disponible cuando las extensiones
    tokenizer y Xdebug están instaladas.

``--coverage-html``

    Genera un reporte de cobertura de código en formato HTML. Ver
    :ref:`code-coverage-analysis` para más detalles.

    Nótese que esta funcionalidad solo está disponible cuando las extensiones
    tokenizer y Xdebug están instaladas.

``--coverage-php``

    Genera un objeto PHP_CodeCoverage serializado con la información de
    cobertura de código.

    Nótese que esta funcionalidad solo está disponible cuando las extensiones
    tokenizer y Xdebug están instaladas.

``--coverage-text``

    Genera un archivo de registro o una salida en línea de comandos en un formato
    legible por humanos con la información de cobertura de código de las pruebas
    ejecutadas. Ver :ref:`logging` para más detalles.

    Nótese que esta funcionalidad solo está disponible cuando las extensiones
    tokenizer y Xdebug están instaladas.

``--log-junit``

    Genera un archivo de sucesos en formato JUnit XML de las pruebas ejecutadas.
    Ver :ref:`logging` para más detalles.

``--testdox-html`` and ``--testdox-text``

    Genera un documento ágil en formato HTML o texto plano de las pruebas que
    se ejecutaron. Ver :ref:`other-uses-for-tests` para más detalles.

``--filter``

    Solo ejecuta las pruebas cuyo nombre coincide con un patrón de expresión
    regular dado. Si el patrón no se encierra entre delimitadores, PHPUnit
    cerrará el patrón dentro de delimitadores ``/``.

    El nombre de las pruebas para hacer coincidir estarán en uno de los
    siguientes formatos:

    ``TestNamespace\TestCaseClass::testMethod``

        El formato de nombre para pruebas por defecto es equivalente a usar
        la constante mágica ``__METHOD__`` dentro el método de prueba.

    ``TestNamespace\TestCaseClass::testMethod with data set #0``

        Cuando una prueba tiene un proveedor de datos, cada iteración sobre los
        datos trae el índice actual añadido al final del nombre de la prueba por
        defecto.

    ``TestNamespace\TestCaseClass::testMethod with data set "my named data"``

        Cuando una prueba tiene un proveedor de datos que usa conjuntos nombrados,
        cada iteración de los datos trae el nombre actual añadido al final del
        nombre de la prueba por defecto. Ver :numref:`textui.examples.TestCaseClass.php`
        para un ejemplo de conjunto de datos nombrados.

        .. code-block:: php
            :caption: Conjunto de datos nombrados
            :name: textui.examples.TestCaseClass.php

            <?php
            use PHPUnit\Framework\TestCase;

            namespace TestNamespace;

            class TestCaseClass extends TestCase
            {
                /**
                 * @dataProvider provider
                 */
                public function testMethod($data)
                {
                    $this->assertTrue($data);
                }

                public function provider()
                {
                    return [
                        'my named data' => [true],
                        'my data'       => [true]
                    ];
                }
            }
            ?>

    ``/path/to/my/test.phpt``

        El nombre de la prueba, para una prueba PHPT, es la ruta del sistema de
        archivos.

    Revisar el ejemplo :numref:`textui.examples.filter-patterns`
    para ver patrones de filtro validos.

    .. code-block:: shell
        :caption: Ejemplos de patrones de filtro
        :name: textui.examples.filter-patterns

        --filter 'TestNamespace\\TestCaseClass::testMethod'
        --filter 'TestNamespace\\TestCaseClass'
        --filter TestNamespace
        --filter TestCaseClase
        --filter testMethod
        --filter '/::testMethod .*"my named data"/'
        --filter '/::testMethod .*#5$/'
        --filter '/::testMethod .*#(5|6|7)$/'

    Ver el :numref:`textui.examples.filter-shortcuts` para algunos atajos
    adicionales que están disponibles para seleccionar proveedores de datos.

    .. code-block:: shell
        :caption: Atajos para filtros
        :name: textui.examples.filter-shortcuts

        --filter 'testMethod#2'
        --filter 'testMethod#2-4'
        --filter '#2'
        --filter '#2-4'
        --filter 'testMethod@my named data'
        --filter 'testMethod@my.*data'
        --filter '@my named data'
        --filter '@my.*data'

``--testsuite``

    Solo ejecuta la suite de prueba cuyo nombre coincide con el patrón dado.

``--group``

    Solo ejecuta las pruebas del o de los grupos especificados. Una prueba se
    puede marcar como perteneciente a un grupo usando la anotación ``@group``.

    La anotación ``@author`` es un alias para ``@group`` que permite filtrar
    las pruebas con base en sus autores.

``--exclude-group``

    Excluye las pruebas de un grupo o grupos especificados. Una prueba se puede
    marcar como perteneciente a un grupo usando la anotación ``@group``.

``--list-groups``

    Lista los grupos de pruebas disponibles.

``--test-suffix``

    Solo busca los archivos de prueba con el o los sufijos especificados.

``--dont-report-useless-tests``

    No reporta las pruebas que no prueban nada. Ver :ref:`risky-tests` para más detalles.

``--strict-coverage``

    Es estricto con la cobertura de código involuntaria. Ver :ref:`risky-tests`
    para más detalles.

``--strict-global-state``

    Es estricto con la manipulación del estado global. Ver :ref:`risky-tests`
    para más detalles.

``--disallow-test-output``

    Es estricto sobre la salida durante las pruebas. Ver :ref:`risky-tests` para más detalles.

``--disallow-todo-tests``

    No ejecuta las pruebas que tienen la anotación ``@todo`` es su bloque de
    documentación.

``--enforce-time-limit``

    Impone un límite de tiempo basado en el tamaño de la prueba. Ver
    :ref:`risky-tests` para detalles.

``--process-isolation``

    Ejecuta cada prueba en un proceso PHP separado.

``--no-globals-backup``

    No respalda ni restaura la variable $GLOBALS. Ver :ref:`fixtures.global-state`
    para más detalles.

``--static-backup``

    Respalda y restaura los atributos estáticos de las clases definidas por el
    usuario. Ver :ref:`fixtures.global-state` para más detalles.

``--colors``

    Usa colores para la salida. En Windows, usamos
    `ANSICON <https://github.com/adoxa/ansicon>`_ o `ConEmu <https://github.com/Maximus5/ConEmu>`_.

    Existen tres valores posibles para esta opción:

    -

      ``never``: nunca mostrar colores en la salida. Este es el valor por defecto
      cuando se usa la opción ``--colors``.

    -

      ``auto``: muestra los colores en la salida a menos que la terminal actual
      no soporte colores o si la salida se canalizada hacia un comando o se
      redirige a un archivo.

    -

      ``always``: siempre muestra colores en la salida incluso cuando la terminal
      no soporta colores o cuando la salida se canaliza hacia un comando o se
      redirige a un archivo.

    Cuando se usa ``--colors`` sin ningún valor se toma la opción ``auto``.

``--columns``

    Define el número de columnas que se usan para la salida de progreso. Si
    ``max`` se define como valor, el número de columnas será el máximo de la
    terminal actual.

``--stderr``

    Opcionalmente imprime en ``STDERR`` en lugar de ``STDOUT``.

``--stop-on-error``

    Se detiene la ejecución frente al primer error.

``--stop-on-failure``

    Se detiene la ejecución frente al primer error o falla.

``--stop-on-risky``

    Se detiene la ejecución frente a la primera prueba riesgosa.

``--stop-on-skipped``

    Se detiene la ejecución frente a la primera prueba omitida.

``--stop-on-incomplete``

    Se detiene la ejecución frente a la primera prueba incompleta.

``--verbose``

    Hace a la información de salida más verbosa, por ejemplo, se muestran los
    nombres de las pruebas incompletas u omitidas.

``--debug``

    Muestra la información de depuración en la salida, tal como el nombre de una
    prueba cuando comienza su ejecución.

``--loader``

    Especifica la implementación de ``PHPUnit_Runner_TestSuiteLoader`` que se
    usa.

    El cargador de la suite de pruebas estándares buscará el archivo fuente en
    la carpeta actual y en cada carpeta que se especifica en la directiva
    de configuración ``include_path`` de PHP. Un nombre de clase como
    ``Project_Package_Class`` se asocia al archivo fuente
    :file:`Project/Package/Class.php`.

``--repeat``

    Ejecutar repetidamente la o las pruebas el número de veces especificado.

``--testdox``

    Reporta el progreso de las pruebas como una documentación ágil. Ver
    :ref:`other-uses-for-tests` para más detalles.

``--printer``

    Especifica la impresora que se usa para generar el resultado. La clase
    impresora debe extender de ``PHPUnit_Util_Printer`` e implementar la interfaz
    ``PHPUnit\Framework\TestListener``.

``--bootstrap``

    Un archivo PHP «bootstrap» se ejecuta antes de las pruebas.

``--configuration``, ``-c``

    Leer la configuración desde el archivo XML. Ver :ref:`appendixes.configuration`
    para más detalles.

    Si :file:`phpunit.xml` o :file:`phpunit.xml.dist` (en este orden) existen
    en la carpeta actual de trabajo y ``--configuration`` *no* se usa, la
    configuración se leerá automáticamente de alguno de estos archivo.

``--no-configuration``

    Ignora los archivos :file:`phpunit.xml` y :file:`phpunit.xml.dist` de la
    capeta de trabajo actual.

``--include-path``

    Añade al comienzo del ``include_path`` de PHP una o varias rutas dadas.

``-d``

    Asigna una valor a la opción de configuración de PHP que se indica.

.. admonition:: Nota

    Nótese que desde la versión 4.8 las opciones se pueden colocar después de los
    argumentos.
