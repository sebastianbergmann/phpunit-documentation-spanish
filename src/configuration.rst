

.. _appendixes.configuration:

============================
Archivo de Configuración XML
============================

.. _appendixes.configuration.phpunit:

PHPUnit
#######

Los atributos del elemento ``<phpunit>`` se pueden usar para configurar la
funcionalidad del núcleo de PHPUnit.

.. code-block:: bash

    <phpunit
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xsi:noNamespaceSchemaLocation="https://schema.phpunit.de/|version|/phpunit.xsd"
             backupGlobals="true"
             backupStaticAttributes="false"
             <!--bootstrap="/path/to/bootstrap.php"-->
             cacheTokens="false"
             colors="false"
             convertErrorsToExceptions="true"
             convertNoticesToExceptions="true"
             convertWarningsToExceptions="true"
             forceCoversAnnotation="false"
             printerClass="PHPUnit\TextUI\ResultPrinter"
             <!--printerFile="/path/to/ResultPrinter.php"-->
             processIsolation="false"
             stopOnError="false"
             stopOnFailure="false"
             stopOnIncomplete="false"
             stopOnSkipped="false"
             stopOnRisky="false"
             testSuiteLoaderClass="PHPUnit\Runner\StandardTestSuiteLoader"
             <!--testSuiteLoaderFile="/path/to/StandardTestSuiteLoader.php"-->
             timeoutForSmallTests="1"
             timeoutForMediumTests="10"
             timeoutForLargeTests="60"
             verbose="false">
      <!-- ... -->
    </phpunit>

El XML de configuración de arriba corresponde al comportamiento por defecto del
ejecutor de pruebas TextUI documentado en :ref:`textui.clioptions`.

Las opciones adicionales que no están disponibles como opciones desde la línea de
comandos son:

``convertErrorsToExceptions``

    Por defecto PHPUnit instalará un gestor de errores que convierte los
    siguientes errores en excepciones:

    - ``E_WARNING``

    - ``E_NOTICE``

    - ``E_USER_ERROR``

    - ``E_USER_WARNING``

    - ``E_USER_NOTICE``

    - ``E_STRICT``

    - ``E_RECOVERABLE_ERROR``

    - ``E_DEPRECATED``

    - ``E_USER_DEPRECATED``

    Colocamos ``convertErrorsToExceptions`` en ``false`` para desactivar esta
    característica.

``convertNoticesToExceptions``

    Cuando colocamos ``false``, el gestor de errores instalado por
    ``convertErrorsToExceptions`` no convertirá los errores ``E_NOTICE``,
    ``E_USER_NOTICE`` o ``E_STRICT`` en excepciones.

``convertWarningsToExceptions``

    Cuando colocamos ``false``, el gestor de errores instalado por
    ``convertErrorsToExceptions`` no convertirá los errores ``E_WARNING`` o
    ``E_USER_WARNING`` en excepciones.

``forceCoversAnnotation``

    El código de cobertura solo será reportado para pruebas que usan la anotación
    ``@covers`` documentada en :ref:`appendixes.annotations.covers`.

``timeoutForLargeTests``

    Si los límites de tiempo basados en el tamaño de la prueba son forzados
    entonces este atributo designará el tiempo límite para todos las pruebas
    marcadas con ``@large``. Si una prueba no se completa dentro del tiempo límite
    configurado entonces la prueba fallará.

``timeoutForMediumTests``

    Si los límites de tiempo basados en el tamaño de la prueba son forzados
    entonces este atributo designará el tiempo límite para todas las pruebas
    marcadas con ``@medium``. Si una prueba no se completa dentro del tiempo
    límite configurado entonces la prueba fallará.

``timeoutForSmallTests``

    Si los límites de tiempo basados en el tamaño de la prueba son forzados
    entonces este atributo designa el tiempo límite para todas las pruebas que
    no han sido marcadas como ``@medium`` o ``@large``. Si una prueba no se
    completa dentro del tiempo límite configurado, la prueba fallará.

.. _appendixes.configuration.testsuites:

Suites de Pruebas
#################

El elemento ``<testsuites>`` y su o sus hijos ``<testsuite>`` se pueden usar
para construir una suite de prueba compuesta por suites de pruebas y casos de
prueba.

.. code-block:: bash

    <testsuites>
      <testsuite name="My Test Suite">
        <directory>/path/to/*Test.php files</directory>
        <file>/path/to/MyTest.php</file>
        <exclude>/path/to/exclude</exclude>
      </testsuite>
    </testsuites>

Si usamos los atributos ``phpVersion`` y ``phpVersionOperator`` se puede
especificar una determinada versión de PHP. El ejemplo de abajo solo agregará
los archivos :file:`/path/to/\*Test.php` y el archivo :file:`/path/to/MyTest.php`
si la versión de PHP es superior o igual a 5.3.0.

.. code-block:: bash

      <testsuites>
        <testsuite name="My Test Suite">
          <directory suffix="Test.php" phpVersion="5.3.0" phpVersionOperator=">=">/path/to/files</directory>
          <file phpVersion="5.3.0" phpVersionOperator=">=">/path/to/MyTest.php</file>
        </testsuite>
      </testsuites>

El atributo ``phpVersionOperator`` es opcional y por defecto tiene el valor ``>=``.

.. _appendixes.configuration.groups:

Grupos
######

El elemento ``<groups>`` y sus hijos ``<include>``, ``<exclude>`` y ``<group>``
se pueden usar para seleccionar grupos de pruebas marcadas con la anotación
``@group`` (documentada en :ref:`appendixes.annotations.group`) que serán o no
serán ejecutadas.

.. code-block:: bash

    <groups>
      <include>
        <group>name</group>
      </include>
      <exclude>
        <group>name</group>
      </exclude>
    </groups>

La configuración XML de arriba es equivalente a invocar el ejecutor de pruebas
TextUI con las siguientes opciones:

-

  ``--group name``

-

  ``--exclude-group name``

.. _appendixes.configuration.whitelisting-files:

Lista Blanca de Archivos para la Cobertura de Código
####################################################

El elemento ``<filter>`` y sus hijos se pueden usar para configurar la lista
blanca usada para el reporte de cobertura de código.

.. code-block:: bash

    <filter>
      <whitelist processUncoveredFilesFromWhitelist="true">
        <directory suffix=".php">/path/to/files</directory>
        <file>/path/to/file</file>
        <exclude>
          <directory suffix=".php">/path/to/files</directory>
          <file>/path/to/file</file>
        </exclude>
      </whitelist>
    </filter>

.. _appendixes.configuration.logging:

Logging
#######

El elemento ``<logging>`` y su hijo ``<log>`` se puede usar para configurar
el registro de sucesos de la ejecución de la prueba.

.. code-block:: bash

    <logging>
      <log type="coverage-html" target="/tmp/report" lowUpperBound="35"
           highLowerBound="70"/>
      <log type="coverage-clover" target="/tmp/coverage.xml"/>
      <log type="coverage-php" target="/tmp/coverage.serialized"/>
      <log type="coverage-text" target="php://stdout" showUncoveredFiles="false"/>
      <log type="junit" target="/tmp/logfile.xml"/>
      <log type="testdox-html" target="/tmp/testdox.html"/>
      <log type="testdox-text" target="/tmp/testdox.txt"/>
    </logging>

La configuración XML de arriba es equivalente a invocar el ejecutor de pruebas
TextUI con las siguiente opciones:

-

  ``--coverage-html /tmp/report``

-

  ``--coverage-clover /tmp/coverage.xml``

-

  ``--coverage-php /tmp/coverage.serialized``

-

  ``--coverage-text``

-

  ``> /tmp/logfile.txt``

-

  ``--log-junit /tmp/logfile.xml``

-

  ``--testdox-html /tmp/testdox.html``

-

  ``--testdox-text /tmp/testdox.txt``

Los atributos ``lowUpperBound``, ``highLowerBound`` y ``showUncoveredFiles``
no tienen opciones equivalentes en el ejecutor de pruebas TextUI.

-

  ``lowUpperBound``:  Máximo porcentaje de cobertura de código para considerar
  la cobertura como baja, «lowly».

-

  ``highLowerBound``: Mínimo porcentaje de cobertura para que se considere
  como alta, «highly».

-

  ``showUncoveredFiles``: Mostrar todos los archivos de la lista blanca en la
  salida ``--coverage-text`` y no solo los archivos con la información de
  cobertura.

-

  ``showOnlySummary``: Solo mostrar el resumen en la salida ``--coverage-text``.

.. _appendixes.configuration.test-listeners:

«Listeners» de Prueba
#####################

El elemento ``<listeners>`` y su hijo ``<listener>`` se pueden usar para adjuntar
escuchas de prueba, «test listeners», adicionales a la prueba en ejecución.

.. code-block:: bash

    <listeners>
      <listener class="MyListener" file="/optional/path/to/MyListener.php">
        <arguments>
          <array>
            <element key="0">
              <string>Sebastian</string>
            </element>
          </array>
          <integer>22</integer>
          <string>April</string>
          <double>19.78</double>
          <null/>
          <object class="stdClass"/>
        </arguments>
      </listener>
    </listeners>

La configuración XML de arriba corresponde a adjuntar el objeto ``$listener``
(ver abajo) a la ejecución de una prueba:

.. code-block:: bash

    $listener = new MyListener(
        ['Sebastian'],
        22,
        'April',
        19.78,
        null,
        new stdClass
    );

.. _appendixes.configuration.extensions:

Registrar Extensiones de TestRunner
###################################

El elemento ``<extensions>`` y su hijo ``<extension>`` se puede usar para
registrar extensiones de TextRunner personalizadas.

El :numref:`configuration.examples.RegisterExtension` muestra como registrar
una extensión.

.. code-block:: xml
    :caption: Registrar una Extensión de TestRunner
    :name: configuration.examples.RegisterExtension

      <?xml version="1.0" encoding="UTF-8"?>
      <phpunit xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="https://schema.phpunit.de/7.1/phpunit.xsd">
          <extensions>
              <extension class="Vendor\MyExtension"/>
          </extensions>
      </phpunit>

.. _appendixes.configuration.php-ini-constants-variables:

Asignar las configuraciones de PHP INI, Constantes y Variables Globales
#######################################################################

El elemento ``<php>`` y sus hijos se pueden usar para establecer las configuraciones
de PHP, constantes y variables globales. Se puede usar para agregar el
``include_path``.

.. code-block:: bash

    <php>
      <includePath>.</includePath>
      <ini name="foo" value="bar"/>
      <const name="foo" value="bar"/>
      <var name="foo" value="bar"/>
      <env name="foo" value="bar"/>
      <post name="foo" value="bar"/>
      <get name="foo" value="bar"/>
      <cookie name="foo" value="bar"/>
      <server name="foo" value="bar"/>
      <files name="foo" value="bar"/>
      <request name="foo" value="bar"/>
    </php>

La configuración XML de arriba se corresponde con el siguiente código PHP:

.. code-block:: bash

    ini_set('foo', 'bar');
    define('foo', 'bar');
    $GLOBALS['foo'] = 'bar';
    $_ENV['foo'] = 'bar';
    $_POST['foo'] = 'bar';
    $_GET['foo'] = 'bar';
    $_COOKIE['foo'] = 'bar';
    $_SERVER['foo'] = 'bar';
    $_FILES['foo'] = 'bar';
    $_REQUEST['foo'] = 'bar';
