

.. _textui:

==============================================
El Ejecutor de Pruebas desde Línea de Comandos
==============================================

El ejecutor de pruebas desde línea de comandos de PHPUnit se puede invocar
con el comando :file:`phpunit`. El siguiente código muestra como ejecutar las
pruebas con el ejecutor de pruebas desde línea de comandos:

.. code-block:: bash

    $ phpunit ArrayTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    ..

    Time: 0 seconds

    OK (2 tests, 2 assertions)

Cuando se ejecuta de la manera que se muestra arriba, el ejecutor de pruebas
desde línea de comandos buscará el archivo fuente :file:`ArrayTest.php` en la
carpeta actual, lo cargará y esperará encontrar la clase de caso de prueba
``ArrayTest``. Y luego se ejecutarán las pruebas de esta clase.

Para cada prueba que se ejecuta, la herramienta de línea de comandos imprime
un carácter para indicar el progreso:

``.``

    Se imprime cuando la prueba es exitosa.

``F``

    Se imprime cuando una aserción falla mientras se ejecuta un método de prueba.

``E``

    Se imprime cuando ocurre un error mientras se ejecuta un método de prueba.

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
de comandos, veamos el siguiente código:

.. code-block:: bash

    $ phpunit --help
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    Usage: phpunit [options] UnitTest [UnitTest.php]
           phpunit [options] <directory>

    Code Coverage Options:

      --coverage-clover <file>    Generate code coverage report in Clover XML format
      --coverage-crap4j <file>    Generate code coverage report in Crap4J XML format
      --coverage-html <dir>       Generate code coverage report in HTML format
      --coverage-php <file>       Export PHP_CodeCoverage object to file
      --coverage-text=<file>      Generate code coverage report in text format
                                  Default: Standard output
      --coverage-xml <dir>        Generate code coverage report in PHPUnit XML format
      --whitelist <dir>           Whitelist <dir> for code coverage analysis
      --disable-coverage-ignore   Disable annotations for ignoring code coverage
      --no-coverage               Ignore code coverage configuration
      --dump-xdebug-filter <file> Generate script to set Xdebug code coverage filter

    Logging Options:

      --log-junit <file>          Log test execution in JUnit XML format to file
      --log-teamcity <file>       Log test execution in TeamCity format to file
      --testdox-html <file>       Write agile documentation in HTML format to file
      --testdox-text <file>       Write agile documentation in Text format to file
      --testdox-xml <file>        Write agile documentation in XML format to file
      --reverse-list              Print defects in reverse order

    Test Selection Options:

      --filter <pattern>          Filter which tests to run
      --testsuite <name,...>      Filter which testsuite to run
      --group ...                 Only runs tests from the specified group(s)
      --exclude-group ...         Exclude tests from the specified group(s)
      --list-groups               List available test groups
      --list-suites               List available test suites
      --list-tests                List available tests
      --list-tests-xml <file>     List available tests in XML format
      --test-suffix ...           Only search for test in files with specified
                                  suffix(es). Default: Test.php,.phpt

    Test Execution Options:

      --dont-report-useless-tests Do not report tests that do not test anything
      --strict-coverage           Be strict about @covers annotation usage
      --strict-global-state       Be strict about changes to global state
      --disallow-test-output      Be strict about output during tests
      --disallow-resource-usage   Be strict about resource usage during small tests
      --enforce-time-limit        Enforce time limit based on test size
      --default-time-limit=<sec>  Timeout in seconds for tests without @small, @medium or @large
      --disallow-todo-tests       Disallow @todo-annotated tests

      --process-isolation         Run each test in a separate PHP process
      --globals-backup            Backup and restore $GLOBALS for each test
      --static-backup             Backup and restore static attributes for each test

      --colors=<flag>             Use colors in output ("never", "auto" or "always")
      --columns <n>               Number of columns to use for progress output
      --columns max               Use maximum number of columns for progress output
      --stderr                    Write to STDERR instead of STDOUT
      --stop-on-defect            Stop execution upon first not-passed test
      --stop-on-error             Stop execution upon first error
      --stop-on-failure           Stop execution upon first error or failure
      --stop-on-warning           Stop execution upon first warning
      --stop-on-risky             Stop execution upon first risky test
      --stop-on-skipped           Stop execution upon first skipped test
      --stop-on-incomplete        Stop execution upon first incomplete test
      --fail-on-warning           Treat tests with warnings as failures
      --fail-on-risky             Treat risky tests as failures
      -v|--verbose                Output more verbose information
      --debug                     Display debugging information

      --loader <loader>           TestSuiteLoader implementation to use
      --repeat <times>            Runs the test(s) repeatedly
      --teamcity                  Report test execution progress in TeamCity format
      --testdox                   Report test execution progress in TestDox format
      --testdox-group             Only include tests from the specified group(s)
      --testdox-exclude-group     Exclude tests from the specified group(s)
      --printer <printer>         TestListener implementation to use

      --resolve-dependencies      Resolve dependencies between tests
      --order-by=<order>          Run tests in order: default|reverse|random|defects|depends
      --random-order-seed=<N>     Use a specific random seed <N> for random order
      --cache-result              Write run result to cache to enable ordering tests defects-first

    Configuration Options:

      --prepend <file>            A PHP script that is included as early as possible
      --bootstrap <file>          A PHP script that is included before the tests run
      -c|--configuration <file>   Read configuration from XML file
      --no-configuration          Ignore default configuration file (phpunit.xml)
      --no-logging                Ignore logging configuration
      --no-extensions             Do not load PHPUnit extensions
      --include-path <path(s)>    Prepend PHP's include_path with given path(s)
      -d key[=value]              Sets a php.ini value
      --generate-configuration    Generate configuration file with suggested settings
      --cache-result-file==<FILE> Specify result cache path and filename

    Miscellaneous Options:

      -h|--help                   Prints this usage information
      --version                   Prints the version and exits
      --atleast-version <min>     Checks that version is greater than min and exits
      --check-version             Check whether PHPUnit is the latest version

``phpunit UnitTest``

    Ejecuta las pruebas que se encuentran en la clase ``UnitTest``. Se espera
    que esta clase este declarada en el archivo fuente :file:`UnitTest.php`.

    ``UnitTest`` debe ser una clase que hereda de ``PHPUnit\Framework\TestCase``
    o una clase que provee un método ``public static suite()`` que regresa un
    objeto ``PHPUnit\Framework\Test``, por ejemplo una instancia de la clase
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

    Solo ejecuta las pruebas cuyo nombre coincide con un patrón dado que está
    basado en una expresión regular. Si el patrón no se encierra entre
    delimitadores, PHPUnit cerrará el patrón dentro de delimitadores ``/``.

    El nombre de la prueba debe estar en uno de los
    siguientes formatos:

    ``TestNamespace\TestCaseClass::testMethod``

        El formato de nombre para pruebas por defecto es equivalente a usar
        la constante mágica ``__METHOD__`` dentro el método de prueba.

    ``TestNamespace\TestCaseClass::testMethod with data set #0``

        Cuando una prueba tiene un proveedor de datos, cada iteración sobre los
        datos trae el índice actual añadido al final del nombre por defecto de
        la prueba.

    ``TestNamespace\TestCaseClass::testMethod with data set "my named data"``

        Cuando una prueba tiene un proveedor de datos que usa conjuntos etiquetados,
        cada iteración de los datos trae el nombre actual añadido al final del
        nombre por defecto de la prueba. Ver :numref:`textui.examples.TestCaseClass.php`
        para un ejemplo de conjunto de datos etiquetados.

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

        El nombre de la prueba para una prueba PHPT es la ruta en el sistema de
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

    Las anotaciones ``@author`` y ``@ticket`` son alias para ``@group`` que
    respectivamente permiten filtrar las pruebas con base en sus autores o en
    su ticket de identificación.

``--exclude-group``

    Excluye las pruebas de un grupo o de los grupos especificados. Una prueba se
    puede marcar como perteneciente a un grupo usando la anotación ``@group``.

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
      no soporte colores o si la salida se canaliza hacia un comando o si se
      redirige a un archivo.

    -

      ``always``: siempre muestra colores en la salida incluso cuando la terminal
      no soporta colores o cuando la salida se canaliza hacia un comando o se
      redirige a un archivo.

    Cuando se usa ``--colors`` sin ningún valor se toma la opción ``auto``.

``--columns``

    Define el número de columnas que se usan para la salida que muestra el
    progreso. Si ``max`` se define con un valor, este número de columnas será el
    máximo de la terminal actual.

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

    Especifica la implementación de ``PHPUnit\Runner\TestSuiteLoader`` que se
    usa.

    El cargador estándar de la suite de pruebas buscará el archivo fuente en
    la carpeta actual y en cada carpeta que se especifica en la directiva
    de configuración ``include_path`` de PHP. Un nombre de clase como
    ``Project_Package_Class`` se pone en correspondencia con un archivo fuente
    como :file:`Project/Package/Class.php`.

``--repeat``

    Ejecutar repetidamente la o las pruebas el número de veces especificado.

``--testdox``

    Reporta el progreso de las pruebas en formato TestDox. Ver
    :ref:`other-uses-for-tests` para más detalles.

``--printer``

    Especifica la impresora que se usa para generar el resultado. La clase
    impresora debe extender de ``PHPUnit\Util\Printer`` e implementar la interfaz
    ``PHPUnit\Framework\TestListener``.

``--bootstrap``

    Un archivo PHP «bootstrap» se ejecuta antes de las pruebas.

``--configuration``, ``-c``

    Lee la configuración desde el archivo XML. Ver :ref:`appendixes.configuration`
    para más detalles.

    Si :file:`phpunit.xml` o :file:`phpunit.xml.dist` (en este orden) existen
    en la carpeta actual de trabajo y ``--configuration`` *no* se usa, la
    configuración se leerá automáticamente de estos archivo.

    Si se especifica una carpeta y si :file:`phpunit.xml` o :file:`phpunit.xml.dist`
    (en este orden) existen en la carpeta, la configuración se leerá automáticamente
    de estos archivos.

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

.. _textui.testdox:

TestDox
#######

La característica TestDox de PHPUnit busca una clase de prueba y a todos sus
métodos de prueba para los convierte desde «camel case» o «snake case» a oraciones:
los métodos de prueba ``testBalanceIsInitiallyZero()`` or ``test_balance_is_initially_zero()``
son convertido en «Balance is initially zero». Si hay varios métodos de prueba cuyos
nombres solo se diferencian en un sufijo de uno o más dígitos, como 
``testBalanceCannotBecomeNegative()`` y ``testBalanceCannotBecomeNegative2()``,
las oraciones "Balance cannot become negative" aparecerá solo una vez, todo esto
suponiendo que todas las pruebas tuvieron éxito.

Veamos que aspecto tiene un documento ágil generado para la clase ``BankAccount``:

.. code-block:: bash

    $ phpunit --testdox BankAccountTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    BankAccount
     ✔ Balance is initially zero
     ✔ Balance cannot become negative

Alternativamente, la documentación ágil se puede generar en formato HTML o texto
plano y guardarlo en un archivo, esto se hace usando los argumentos 
``--testdox-html`` y ``--testdox-text``.

La Documentación Ágil se puede usar para documentar los supuestos que hacemos
sobre los paquetes externos que usamos en nuestro proyecto.
Cuando usamos un paquete externo estamos expuesto a que el paquete no se comporte
de la forma esperada y, además, futuras versiones del paquete pueden cambiar de
una manera sutil y romper nuestro código sin que nos demos cuenta de ello.
Podemos prevenir estos riesgos escribiendo una prueba cada vez que hagamos
una suposición. Si nuestra prueba es exitosa nuestra suposición es correcta.
Si documentamos todas las suposiciones con pruebas, los lanzamientos futuros
de los paquetes externos no serán causa de preocupación: si la prueba es exitosa,
nuestro sistema debería seguir funcionando.
