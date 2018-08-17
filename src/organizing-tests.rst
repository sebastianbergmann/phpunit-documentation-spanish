

.. _organizing-tests:

=====================
Organizar las Pruebas
=====================

Uno de los objetivos de PHPUnit es que las pruebas se puedan
combinar: queremos ser capaces de ejecutar cualquier cantidad o
combinación de pruebas, por ejemplo, todas las pruebas de un proyecto
entero o las pruebas para todas las clases de un componente que es parte
de un proyecto o solamente probar una clase.

PHPUnit soporta diferentes maneras de organizar y componer las pruebas dentro
de una suite de prueba. Este capítulo muestra los enfoques que se usan comúnmente.

.. _organizing-tests.filesystem:

Componer una Suite de Prueba Usando el Sistema de Archivos
##########################################################

Probablemente la manera más fácil de componer una suite de prueba es tener
todos los archivos fuente con los casos de prueba en una carpeta de prueba.
PHPUnit puede descubrir automáticamente y ejecutar las pruebas recorriendo
recursivamente el directorio de prueba.

Vamos a dar un vistazo a la suite de prueba de la biblioteca
`sebastianbergmann/money <http://github.com/sebastianbergmann/money/>`_.
Al ver la estructura de la carpeta del proyecto nos damos cuenta que
las clases de casos de prueba en la carpeta :file:`tests` son un espejo
del paquete y de la estructura de clases del Sistema Bajo Prueba, en ingles
*System Under Test (SUT)*, en la carpeta :file:`src`:

.. code-block:: bash

    src                                 tests
    `-- Currency.php                    `-- CurrencyTest.php
    `-- IntlFormatter.php               `-- IntlFormatterTest.php
    `-- Money.php                       `-- MoneyTest.php
    `-- autoload.php

Para ejecutar todas las pruebas de la biblioteca solo necesitamos apuntar el
ejecutor de pruebas en línea de comandos a la carpeta donde están las pruebas:

.. code-block:: bash

    $ phpunit --bootstrap src/autoload.php tests
    PHPUnit |version|.0 by Sebastian Bergmann.

    .................................

    Time: 636 ms, Memory: 3.50Mb

    OK (33 tests, 52 assertions)

.. admonition:: Nota

   Si apuntamos el ejecutor de pruebas en línea de comandos a la carpeta, él
   buscará los archivos :file:`*Test.php`.

Para ejecutar solamente las pruebas que están en la clase de prueba ``CurrencyTest``
del archivo :file:`tests/CurrencyTest.php`, podemos usar el siguiente comando:

.. code-block:: bash

    $ phpunit --bootstrap src/autoload.php tests/CurrencyTest
    PHPUnit |version|.0 by Sebastian Bergmann.

    ........

    Time: 280 ms, Memory: 2.75Mb

    OK (8 tests, 8 assertions)

Para un control de tipo granular sobre que pruebas ejecutar podemos usar la
opción ``--filter``:

.. code-block:: bash

    $ phpunit --bootstrap src/autoload.php --filter testObjectCanBeConstructedForValidConstructorArgument tests
    PHPUnit |version|.0 by Sebastian Bergmann.

    ..

    Time: 167 ms, Memory: 3.00Mb

    OK (2 test, 2 assertions)

.. admonition:: Nota

   Una desventaja de este abordaje es que no tenemos control sobre el orden
   en que las pruebas se ejecutan. Esto puede causar problemas con las pruebas
   que tienen dependencias, ver :ref:`writing-tests-for-phpunit.test-dependencies`.
   En la siguiente sección veremos como podemos dar un orden de ejecución
   explicito usando un archivo de configuración XML.

.. _organizing-tests.xml-configuration:

Componer una Suite de Prueba con una Configuración XML
######################################################

El archivo de configuración XML de PHPUnit (:ref:`appendixes.configuration`)
se puede usar, además, para componer una suite de prueba. El
:numref:`organizing-tests.xml-configuration.examples.phpunit.xml`
muestra un archivo :file:`phpunit.xml` que agregará todas las clases que
se encuentran en los archivos :file:`*Test.php` luego de recorrer recursivamente
la carpeta :file:`tests`.

.. code-block:: php
    :caption: Componer una Suite de Prueba con Configuración XML
    :name: organizing-tests.xml-configuration.examples.phpunit.xml

    <phpunit bootstrap="src/autoload.php">
      <testsuites>
        <testsuite name="money">
          <directory>tests</directory>
        </testsuite>
      </testsuites>
    </phpunit>

Si :file:`phpunit.xml` o :file:`phpunit.xml.dist` (en este orden) existen
en la carpeta de trabajo actual y la opción ``--configuration`` *no* se usa,
la configuración será leída automáticamente del aquellos archivos.

Se puede hacer explícito el orden en que las pruebas se ejecutan:

.. code-block:: php
    :caption: Componer una Suite de Prueba con Configuración XML
    :name: organizing-tests.xml-configuration.examples.phpunit.xml2

    <phpunit bootstrap="src/autoload.php">
      <testsuites>
        <testsuite name="money">
          <file>tests/IntlFormatterTest.php</file>
          <file>tests/MoneyTest.php</file>
          <file>tests/CurrencyTest.php</file>
        </testsuite>
      </testsuites>
    </phpunit>
