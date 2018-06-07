

.. _appendixes.annotations:

===========
Anotaciones
===========

Una anotación es una forma especial de meta datos sintácticos que se pueden
agregar al código fuente de algún lenguaje de programación. Aunque PHP no
tiene entre sus características la anotación de código fuente, el uso de
etiquetas como ``@annotation arguments`` en un bloque de documentación se ha
establecido en la comunidad PHP como el modo de anotar el código fuente. En PHP
los bloques de documentación son reflexivos: se puede acceder a ellos por medio
del método ``getDocComment()`` de la «API Reflection» a nivel de función,
clase, método y atributo. Aplicaciones como PHPUnit usan esta información
en tiempo de ejecución para configurar su comportamiento.

.. admonition:: Nota

   Un comentario de documentación en PHP debe comenzar con ``/**`` y terminar
   con ``*/``. Las anotaciones en cualquier otro estilo de comentarios serán
   ignoradas.

Este apéndice muestra toda la variedad de anotaciones soportada por PHPUnit.

.. _appendixes.annotations.author:

@author
#######

La anotación ``@author`` es un alias de la anotación ``@group``
(ver :ref:`appendixes.annotations.group`) y permite filtrar las pruebas en
base a sus autores.

.. _appendixes.annotations.after:

@after
######

La anotación ``@after`` se puede usar para especificar métodos que deben ser
llamados después de todos los métodos de prueba de la clase de casos de prueba.

.. code-block:: php

    use PHPUnit\Framework\TestCase;

    class MyTest extends TestCase
    {
        /**
         * @after
         */
        public function tearDownSomeFixtures()
        {
            // ...
        }

        /**
         * @after
         */
        public function tearDownSomeOtherFixtures()
        {
            // ...
        }
    }

.. _appendixes.annotations.afterClass:

@afterClass
###########

La anotación ``@afterClass`` se puede usar para especificar métodos estáticos
que limpian los ambientes compartidos. Estos métodos se llaman después de que
todos los métodos de prueba de una clase de prueba se han ejecutado.

.. code-block:: php

    use PHPUnit\Framework\TestCase;

    class MyTest extends TestCase
    {
        /**
         * @afterClass
         */
        public static function tearDownSomeSharedFixtures()
        {
            // ...
        }

        /**
         * @afterClass
         */
        public static function tearDownSomeOtherSharedFixtures()
        {
            // ...
        }
    }

.. _appendixes.annotations.backupGlobals:

@backupGlobals
##############

Las operaciones de respaldo y restauración para las variables globales se
pueden desactivar completamente para todas las pruebas de una clase de casos
de prueba, de la siguiente manera:

.. code-block:: php

    use PHPUnit\Framework\TestCase;

    /**
     * @backupGlobals disabled
     */
    class MyTest extends TestCase
    {
        // ...
    }

Además, la anotación ``@backupGlobals`` se puede usar a nivel de método de
prueba. Esto permite una configuración «de grano fino» sobre las operaciones de
respaldo y restauración:

.. code-block:: php

    use PHPUnit\Framework\TestCase;

    /**
     * @backupGlobals disabled
     */
    class MyTest extends TestCase
    {
        /**
         * @backupGlobals enabled
         */
        public function testThatInteractsWithGlobalVariables()
        {
            // ...
        }
    }

.. _appendixes.annotations.backupStaticAttributes:

@backupStaticAttributes
#######################

La anotación ``@backupStaticAttributes`` se puede usar para respaldar todos
los valores de las propiedades estáticas en todas las clases declaradas antes
de cada prueba y restaurarlos después. Esta anotación se puede usar a nivel de
clase de caso de prueba o de método de prueba.

.. code-block:: php

    use PHPUnit\Framework\TestCase;

    /**
     * @backupStaticAttributes enabled
     */
    class MyTest extends TestCase
    {
        /**
         * @backupStaticAttributes disabled
         */
        public function testThatInteractsWithStaticAttributes()
        {
            // ...
        }
    }

.. admonition:: Nota

   La anotación ``@backupStaticAttributes`` está limitada por PHP y en algunas
   circunstancias puede causar que valores estáticos no deseados persistan y se
   filtren en las pruebas siguientes.

   Para más detalles podemos ver :ref:`fixtures.global-state`.

.. _appendixes.annotations.before:

@before
#######

La anotación ``@before`` se puede usar para especificar métodos que se deben
llamar antes de los métodos de prueba de la clase de casos de prueba.

.. code-block:: php

    use PHPUnit\Framework\TestCase;

    class MyTest extends TestCase
    {
        /**
         * @before
         */
        public function setupSomeFixtures()
        {
            // ...
        }

        /**
         * @before
         */
        public function setupSomeOtherFixtures()
        {
            // ...
        }
    }

.. _appendixes.annotations.beforeClass:

@beforeClass
############

La anotación ``@beforeClass`` se puede usar para especificar métodos estáticos
que limpian los ambientes compartidos. Estos métodos se llaman antes de
cualquier método de prueba de una clase de prueba.

.. code-block:: php

    use PHPUnit\Framework\TestCase;

    class MyTest extends TestCase
    {
        /**
         * @beforeClass
         */
        public static function setUpSomeSharedFixtures()
        {
            // ...
        }

        /**
         * @beforeClass
         */
        public static function setUpSomeOtherSharedFixtures()
        {
            // ...
        }
    }

.. _appendixes.annotations.codeCoverageIgnore:

@codeCoverageIgnore*
####################

Las anotaciones ``@codeCoverageIgnore``, ``@codeCoverageIgnoreStart`` y
``@codeCoverageIgnoreEnd`` se pueden usar para excluir líneas de código del
análisis de cobertura de código.

Para conocer el uso de estas anotaciones podemos ver :ref:`code-coverage-analysis.ignoring-code-blocks`.

.. _appendixes.annotations.covers:

@covers
#######

La anotación ``@covers`` se puede usar en el código de prueba para especificar
el o los métodos que queremos probar dentro de un método de prueba :

.. code-block:: php

    /**
     * @covers BankAccount::getBalance
     */
    public function testBalanceIsInitiallyZero()
    {
        $this->assertSame(0, $this->ba->getBalance());
    }

Si esta anotación se usa, solo se considerará la información de cobertura de
código para el o los métodos especificados.

El :numref:`appendixes.annotations.covers.tables.annotations` muestra la
sintaxis de la anotación ``@covers``.

.. rst-class:: table
.. list-table:: Anotaciones que permite especificar que métodos son cubiertos por una prueba
    :name: appendixes.annotations.covers.tables.annotations
    :header-rows: 1

    * - Anotación
      - Descripción
    * - ``@covers ClassName::methodName``
      - Indica que el método de prueba anotado cubre el método especificado.
    * - ``@covers ClassName``
      - Indica que el método de prueba anotado cubre todos los métodos de una
        clase dada.
    * - ``@covers ClassName<extended>``
      - Indica que el método de prueba anotado cubre todos los métodos de una
        clase dada y sus clases o interfaces padre.
    * - ``@covers ClassName::<public>``
      - Indica que el método de prueba anotado cubre todos los métodos públicos
        de una clase dada.
    * - ``@covers ClassName::<protected>``
      - Indica que el método de prueba anotado cubre todos los métodos protegidos
        de una clase dada.
    * - ``@covers ClassName::<private>``
      - Indica que el método de prueba anotado cubre todos los métodos privados
        de una clase dada.
    * - ``@covers ClassName::<!public>``
      - Indica que el método de prueba anotado cubre todos los métodos que no
        son públicos de una clase dada.
    * - ``@covers ClassName::<!protected>``
      - Indica que el método de prueba anotado cubre todos los métodos que no
        son protegidos de una clase dada.
    * - ``@covers ClassName::<!private>``
      - Indica que el método de prueba anotado cubre todos los métodos que nos
        son privados de una clase dada.
    * - ``@covers ::functionName``
      - Indica que el método de prueba anotado cubre la función global especificada.

.. _appendixes.annotations.coversDefaultClass:

@coversDefaultClass
###################

La anotación ``@coversDefaultClass`` se puede usar para especificar un espacio
de nombres o un nombre de clase por defecto. Para que nombres muy largos
no necesiten ser repetido para cada anotación ``@covers``.
Ver :numref:`appendixes.annotations.examples.CoversDefaultClassTest.php`.

.. code-block:: php
    :caption: Usar @coversDefaultClass para acortar las anotaciones
    :name: appendixes.annotations.examples.CoversDefaultClassTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    /**
     * @coversDefaultClass \Foo\CoveredClass
     */
    class CoversDefaultClassTest extends TestCase
    {
        /**
         * @covers ::publicMethod
         */
        public function testSomething()
        {
            $o = new Foo\CoveredClass;
            $o->publicMethod();
        }
    }

.. _appendixes.annotations.coversNothing:

@coversNothing
##############

La anotación ``@coversNothing`` se puede usar en el código de prueba para
especificar que el caso de prueba anotado no se guarde en la información de
cobertura de código.

Esta anotación se puede usar para las pruebas de integración. Para un ejemplo
podemos ver
:ref:`code-coverage-analysis.specifying-covered-methods.examples.GuestbookIntegrationTest.php`

Esta anotación se puede usar a nivel de clase o de método y sobrescribirá
cualquier etiqueta ``@covers``.

.. _appendixes.annotations.dataProvider:

@dataProvider
#############

Un método de prueba puede aceptar argumentos arbitrarios. Estos argumentos
se proveen por uno o más métodos proveedores de datos (``provider()`` en
:ref:`writing-tests-for-phpunit.data-providers.examples.DataTest.php`).
El método proveedor de datos a ser usado se especifica usando la anotación
``@dataProvider``.

Para más detalles podemos ver :ref:`writing-tests-for-phpunit.data-providers`.

.. _appendixes.annotations.depends:

@depends
########

PHPUnit soporta la declaración de dependencia explicitas entre métodos de pruebas.
Estas dependencias no definen el orden en que los métodos de prueba serán
ejecutados pero ellos permiten regresar una instancia del ambiente de pruebas
desde el productor y pasarla al consumidor. El
:ref:`writing-tests-for-phpunit.examples.StackTest2.php` muestra como usar la
anotación ``@depends`` para expresar dependencias entre métodos de prueba.

Ver :ref:`writing-tests-for-phpunit.test-dependencies` para más detalles.

.. _appendixes.annotations.doesNotPerformAssertions:

@doesNotPerformAssertions
#########################

Evita que una prueba que no ejecuta aserciones sea considerado riesgoso.

.. _appendixes.annotations.expectedException:

@expectedException
##################

:ref:`writing-tests-for-phpunit.exceptions.examples.ExceptionTest.php`
shows how to use the ``@expectedException`` annotation to
test whether an exception is thrown inside the tested code.

See :ref:`writing-tests-for-phpunit.exceptions` for more
details.

.. _appendixes.annotations.expectedExceptionCode:

@expectedExceptionCode
######################

La anotación ``@expectedExceptionCode`` en conjunción con ``@expectedException``
permite hacer aserciones sobre el código de error lanzado por una excepción,
esto nos permite seleccionar una excepción en específico.

.. code-block:: php

    use PHPUnit\Framework\TestCase;

    class MyTest extends TestCase
    {
        /**
         * @expectedException     MyException
         * @expectedExceptionCode 20
         */
        public function testExceptionHasErrorCode20()
        {
            throw new MyException('Some Message', 20);
        }
    }

Para probar con facilidad y reducir la duplicidad se puede usar un atajo
para especificar una constante de clase como ``@expectedExceptionCode`` usando
la sintaxis «``@expectedExceptionCode ClassName::CONST``».

.. code-block:: php

    use PHPUnit\Framework\TestCase;

    class MyTest extends TestCase
    {
        /**
          * @expectedException     MyException
          * @expectedExceptionCode MyClass::ERRORCODE
          */
        public function testExceptionHasErrorCode20()
        {
          throw new MyException('Some Message', 20);
        }
    }
    class MyClass
    {
        const ERRORCODE = 20;
    }

.. _appendixes.annotations.expectedExceptionMessage:

@expectedExceptionMessage
#########################

La anotación ``@expectedExceptionMessage`` funciona de manera similar a la
anotación ``@expectedExceptionCode`` y permite hacer una aserción sobre el
mensaje de error de una excepción.

.. code-block:: php

    use PHPUnit\Framework\TestCase;

    class MyTest extends TestCase
    {
        /**
         * @expectedException        MyException
         * @expectedExceptionMessage Some Message
         */
        public function testExceptionHasRightMessage()
        {
            throw new MyException('Some Message', 20);
        }
    }

El mensaje esperado puede ser una sub cadena de caracteres del «Message» de la
excepción. Esta característica puede ser útil para afirmar solo un determinado
nombre o parámetro que está presente en el mensaje de la excepción y no
considerar todo el mensaje de la excepción.

.. code-block:: php

    use PHPUnit\Framework\TestCase;

    class MyTest extends TestCase
    {
         /**
          * @expectedException        MyException
          * @expectedExceptionMessage broken
          */
         public function testExceptionHasRightMessage()
         {
             $param = "broken";
             throw new MyException('Invalid parameter "'.$param.'".', 20);
         }
    }

Para facilitar las pruebas y reducir la duplicidad se puede usar un atajo
para especificar una constate de clase como ``@expectedExceptionMessage``
usando la sintaxis «``@expectedExceptionMessage ClassName::CONST``».
Podemos encontrar un ejemplo en :ref:`appendixes.annotations.expectedExceptionCode`.

.. _appendixes.annotations.expectedExceptionMessageRegExp:

@expectedExceptionMessageRegExp
###############################

El mensaje esperado puede ser especificado con una expresión regular usando la
anotación ``@expectedExceptionMessageRegExp``. Esta etiqueta es útil para
situaciones donde una sub cadena de caracteres no es adecuada para comparar
un mensaje dado.

.. code-block:: php

    use PHPUnit\Framework\TestCase;

    class MyTest extends TestCase
    {
         /**
          * @expectedException              MyException
          * @expectedExceptionMessageRegExp /Argument \d+ can not be an? \w+/
          */
         public function testExceptionHasRightMessage()
         {
             throw new MyException('Argument 2 can not be an integer');
         }
    }

.. _appendixes.annotations.group:

@group
######

Una prueba puede ser etiquetada como perteneciendo a uno o más grupos usando la
anotación ``@group`` de la siguiente manera:

.. code-block:: php

    use PHPUnit\Framework\TestCase;

    class MyTest extends TestCase
    {
        /**
         * @group specification
         */
        public function testSomething()
        {
        }

        /**
         * @group regresssion
         * @group bug2204
         */
        public function testSomethingElse()
        {
        }
    }

La anotación ``@group``` se puede colocar en la clase de prueba. En este caso
todos los métodos de prueba «heredan» de la clase de prueba.

Las pruebas se pueden seleccionar para su ejecución en base a grupos usando las
opciones ``--group`` y ``--exclude-group`` del ejecutor de pruebas en línea de
comandos o usando las directivas respectivas en el archivo de configuración XML.

.. _appendixes.annotations.large:

@large
######

La anotación ``@large`` es una alias para ``@group large``.

Si el paquete ``PHP_Invoker`` está instalado y el modo estricto esta habilitado,
las pruebas largas fallarán si ellas toman más de 60 segundo en ejecución. Este
tiempo límite se configura con el atributo ``timeoutForLargeTests`` en el archivo
de configuración XML.

.. _appendixes.annotations.medium:

@medium
#######

La anotación ``@medium`` es una alias para ``@group medium``. Una prueba media
no debería depender de una prueba marcada como ``@large``-

Si el paquete ``PHP_Invoker`` está instalado y el modo estricto está habilitado,
una prueba mediana fallará si su ejecución tarda más de 10 segundos. Este tiempo
de espera se configura a través del atributo ``timeoutForMediumTests`` en el
archivo de configuración XML.

.. _appendixes.annotations.preserveGlobalState:

@preserveGlobalState
####################

Cuando una prueba se ejecuta en un proceso separado PHPUnit intentará preservar
el estado global a partir del proceso padre mediante la serialización de todas
las globales que están en el proceso padre y luego descerializandolas en el
proceso hijo. Esto puede causar problemas si el proceso padre contiene globales
que no son serializables. Para corregir esto, podemos evitar que PHPUnit
preserve el estado global con la anotación ``@preserveGlobalState``.

.. code-block:: php

    use PHPUnit\Framework\TestCase;

    class MyTest extends TestCase
    {
        /**
         * @runInSeparateProcess
         * @preserveGlobalState disabled
         */
        public function testInSeparateProcess()
        {
            // ...
        }
    }

.. _appendixes.annotations.requires:

@requires
#########

La anotación ``@requires`` se puede usar para saltar pruebas cuando las precondiciones
comunes, como la versión de PHP o las extensiones instaladas no se encuentran.

Una lista completa de posibilidades y ejemplos se puede encontrar en
:ref:`incomplete-and-skipped-tests.requires.tables.api`

.. _appendixes.annotations.runTestsInSeparateProcesses:

@runTestsInSeparateProcesses
############################

Indica que todas las pruebas en la clase de pruebas deben ser ejecutados en un
proceso PHP separado.

.. code-block:: php

    use PHPUnit\Framework\TestCase;

    /**
     * @runTestsInSeparateProcesses
     */
    class MyTest extends TestCase
    {
        // ...
    }

*Nota:* Por defecto, PHPUnit intentará preservar el estado global desde el
proceso padre serializando todas las globales del proceso padre y
deserialzandolas en el proceso hijo. Esto puede causar problemas si el proceso
padre contiene globales que no son serializables. Para información sobre como
corregir esto podemos revisar :ref:`appendixes.annotations.preserveGlobalState`.

.. _appendixes.annotations.runInSeparateProcess:

@runInSeparateProcess
#####################

Indica que la prueba debe ser ejecutada en un proceso PHP separado.

.. code-block:: php

    use PHPUnit\Framework\TestCase;

    class MyTest extends TestCase
    {
        /**
         * @runInSeparateProcess
         */
        public function testInSeparateProcess()
        {
            // ...
        }
    }

*Nota:* Por defecto, PHPUnit intentará preservar el estado global desde el
proceso padre serializando todas las globales del proceso padre y
deserialzandolas en el proceso hijo. Esto puede causar problemas si el proceso
padre contiene globales que no son serializables. Para información sobre como
corregir esto podemos revisar :ref:`appendixes.annotations.preserveGlobalState`.

.. _appendixes.annotations.small:

@small
######

La anotación ``@small`` es un alias para ``@group small``. Una prueba pequeña
no debe depender de una prueba marcada como ``@medium`` o ``@large``.

Si el paquete ``PHP_Invoker`` está instalado y el modo estricto está habilitado,
una prueba «pequeña» fallará si toma más de un segundo en ejecutarse. El tiempo
de espera se configura con el atributo ``timeoutForSmallTests`` en el archivo
de configuración XML.

.. admonition:: Nota

   Para habilitar los límites de tiempo las pruebas tienen que ser anotadas
   explícitamente con ``@small``, ``@medium`` y ``@large``.

.. _appendixes.annotations.test:

@test
#####

Como alternativa a añadir el prefijo ``test`` al nombre del método podemos usar
la anotación ``@test`` en un bloque de documentación de método para marcarlo
como un método de prueba.

.. code-block:: php

    /**
     * @test
     */
    public function initialBalanceShouldBe0()
    {
        $this->assertSame(0, $this->ba->getBalance());
    }

.. _appendixes.annotations.testdox:

@testdox
########

Especifica una descripción alternativa que se usa cuando se genera la documentación
ágil de sentencias.

La anotación ``@testdox`` se pueden aplicar tanto a clases de prueba como a
métodos de prueba.

.. code-block:: php

    /**
     * @testdox A bank account
     */
    class BankAccountTest extends TestCase
    {
        /**
         * @testdox has an initial balance of zero
         */
        public function balanceIsInitiallyZero()
        {
            $this->assertSame(0, $this->ba->getBalance());
        }
    }

.. admonition:: Nota

   Antes de PHPUnit 7.0 (debido a un error en el análisis sintáctico de las
   anotaciones, «parsing»), cuando se usa la anotación ``@testdox`` además se
   activa la anotación ``@test``.

.. _appendixes.annotations.testWith:

@testWith
#########

En lugar de implementar un método como ``@dataProvider`` podemos definir un
conjunto de datos usando la anotación ``@testWith``.

Un conjunto de datos consiste de uno o varios elementos. Para definir un conjunto
de datos con multiples elementos definimos cada elemento en una línea separada.
Cada elemento del conjunto de datos debe ser un arreglo definido en JSON.

Ver :ref:`writing-tests-for-phpunit.data-providers` para aprender más sobre como
pasar un conjunto de datos a una prueba.

.. code-block:: php

    /**
     * @param string    $input
     * @param int       $expectedLength
     *
     * @testWith        ["test", 4]
     *                  ["longer-string", 13]
     */
    public function testStringLength(string $input, int $expectedLength)
    {
        $this->assertSame($expectedLength, strlen($input));
    }

Una representación de un objeto en JSON será convertido a un arreglo asociado.

.. code-block:: php

    /**
     * @param array     $array
     * @param array     $keys
     *
     * @testWith        [{"day": "monday", "conditions": "sunny"}, ["day", "conditions"]]
     */
    public function testArrayKeys($array, $keys)
    {
        $this->assertSame($keys, array_keys($array));
    }

.. _appendixes.annotations.ticket:

@ticket
#######

The ``@ticket`` annotation is an alias for the
``@group`` annotation (see :ref:`appendixes.annotations.group`) and allows to filter tests based
on their ticket ID.

.. _appendixes.annotations.uses:

@uses
#####

La anotación ``@uses`` especifica el código que será ejecutado por una prueba
pero que no deseamos que sea cubierto por la prueba. Un buen ejemplo es un objeto
de valor, «value object», que se necesita para probar una unidad de código.

.. code-block:: php

    /**
     * @covers BankAccount::deposit
     * @uses   Money
     */
    public function testMoneyCanBeDepositedInAccount()
    {
        // ...
    }

Esta anotación es especialmente útil en el modo de cobertura estricto en donde
código cubierto causa involuntariamente que la prueba falle. Para más
información sobre el modo de cobertura estricto podemos ver
:ref:`risky-tests.unintentionally-covered-code`.
