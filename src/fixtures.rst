

.. _fixtures:

=========
Ambientes
=========

Una de las partes que consume más tiempo cuando se hacen pruebas, es la parte
en que se escribe el código que construye un mundo en un estado conocido desde
donde comenzar y al que se debe regresar cuando la prueba se completa.
Este estado conocido se llama *ambiente*, en ingles *fixture*, de la prueba.

En el ejemplo :ref:`writing-tests-for-phpunit.examples.StackTest.php`, el ambiente es
simplemente un arreglo que se guarda en la variable ``$stack``. Sin embargo,
la mayoría del tiempo el ambiente será más complejo que un simple arreglo y la
cantidad de código necesario para construirlo crecerá con su complejidad.
El contenido real de la prueba se pierde entre el ruidoso trajinar de la construcción
del ambiente. Este problema empeora aún más cuando escribimos varias pruebas con
ambientes parecidos. Sin la ayuda de un framework de pruebas tendríamos que
duplicar el código que construye el ambiente para cada prueba que se escribe.

PHPUnit puede compartir el código de configuración. Antes de que se ejecute un
método de prueba un método modelo llamado ``setUp()`` se invoca. En el método
``setUp()`` es donde creamos el objeto contra el que probaremos. Una vez que el
método de prueba terminó de ejecutarse, tanto si fue exitoso como fallido, otro
método modelo llamado ``tearDown()`` se invoca. En el método ``tearDown()`` es
con el que limpiamos los objetos contra los que hemos probado.

En el ejemplo :ref:`writing-tests-for-phpunit.examples.StackTest2.php` usamos
la relación productor-consumidor entre las pruebas para compartir un ambiente.
Esto no siempre es deseable o incluso posible. En el
:numref:`fixtures.examples.StackTest.php` se muestra como podemos escribir
las pruebas para la clase ``StackTest`` de una manera que no se reusa el
ambiente sino el código que lo crea. Primero, declaramos una variable
de instancia, ``$stack``, que usaremos en lugar de la variable del
método local. Luego delegamos la creación del ``array`` para el ambiente
al método ``setUp()``. Finalmente, removemos el código redundante
de los métodos de prueba y usamos la variable de instancia introducida
recientemente, ``$this->stack``, en lugar de la variable del método local
``$stack`` con el método de aserción ``assertSame()``.

.. code-block:: php
    :caption: Usar setUp() para crear el ambiente para la clase StackTest
    :name: fixtures.examples.StackTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class StackTest extends TestCase
    {
        protected $stack;

        protected function setUp()
        {
            $this->stack = [];
        }

        public function testEmpty()
        {
            $this->assertTrue(empty($this->stack));
        }

        public function testPush()
        {
            array_push($this->stack, 'foo');
            $this->assertSame('foo', $this->stack[count($this->stack)-1]);
            $this->assertFalse(empty($this->stack));
        }

        public function testPop()
        {
            array_push($this->stack, 'foo');
            $this->assertSame('foo', array_pop($this->stack));
            $this->assertTrue(empty($this->stack));
        }
    }
    ?>

Los métodos modelo ``setUp()`` y ``tearDown()`` se ejecutan una vez para cada
método de prueba (y en instancias nuevas) de la clase de casos de prueba.

Además, los métodos modelo ``setUpBeforeClass()`` y ``tearDownAfterClass()``
se llaman respectivamente antes de que la primera prueba de la clase de casos de
prueba se ejecute y después de que la última prueba de la clase de casos de
prueba se haya ejecutado.

El ejemplo de abajo muestra todos los métodos modelo que están disponibles
en la clase de casos de prueba.

.. code-block:: php
    :caption: Ejemplo que muestra todos los métodos modelo disponibles
    :name: fixtures.examples.TemplateMethodsTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class TemplateMethodsTest extends TestCase
    {
        public static function setUpBeforeClass()
        {
            fwrite(STDOUT, __METHOD__ . "\n");
        }

        protected function setUp()
        {
            fwrite(STDOUT, __METHOD__ . "\n");
        }

        protected function assertPreConditions()
        {
            fwrite(STDOUT, __METHOD__ . "\n");
        }

        public function testOne()
        {
            fwrite(STDOUT, __METHOD__ . "\n");
            $this->assertTrue(true);
        }

        public function testTwo()
        {
            fwrite(STDOUT, __METHOD__ . "\n");
            $this->assertTrue(false);
        }

        protected function assertPostConditions()
        {
            fwrite(STDOUT, __METHOD__ . "\n");
        }

        protected function tearDown()
        {
            fwrite(STDOUT, __METHOD__ . "\n");
        }

        public static function tearDownAfterClass()
        {
            fwrite(STDOUT, __METHOD__ . "\n");
        }

        protected function onNotSuccessfulTest(Exception $e)
        {
            fwrite(STDOUT, __METHOD__ . "\n");
            throw $e;
        }
    }
    ?>

.. code-block:: bash

    $ phpunit TemplateMethodsTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    TemplateMethodsTest::setUpBeforeClass
    TemplateMethodsTest::setUp
    TemplateMethodsTest::assertPreConditions
    TemplateMethodsTest::testOne
    TemplateMethodsTest::assertPostConditions
    TemplateMethodsTest::tearDown
    .TemplateMethodsTest::setUp
    TemplateMethodsTest::assertPreConditions
    TemplateMethodsTest::testTwo
    TemplateMethodsTest::tearDown
    TemplateMethodsTest::onNotSuccessfulTest
    FTemplateMethodsTest::tearDownAfterClass

    Time: 0 seconds, Memory: 5.25Mb

    There was 1 failure:

    1) TemplateMethodsTest::testTwo
    Failed asserting that <boolean:false> is true.
    /home/sb/TemplateMethodsTest.php:30

    FAILURES!
    Tests: 2, Assertions: 2, Failures: 1.

.. _fixtures.more-setup-than-teardown:

Más setUp() que tearDown()
############################

Los métodos ``setUp()`` y ``tearDown()`` son bien simétricos en la teoría pero
no en la práctica. En la practica, solo necesitamos implementar ``tearDown()``
si asignamos recursos externos como archivos o *sockets* en el ``setUp()``.
Si nuestro ``setUp()`` solo crea objetos de PHP planos, podemos ignorar en
general el ``tearDown()``. Sin embargo, si creamos muchos objetos en nuestro
``setUp()``, podríamos querer usar el método ``unset()`` para restablecer las
variables que apunta a esos objetos con un ``tearDown()`` para que se puedan
recolectar como basura. La recolección de basura de los objetos de los casos
de prueba no es previsible.

.. _fixtures.variations:

Variaciones
###########

¿Que paso cuando tenemos dos pruebas con configuraciones ligeramente diferentes?
Existen dos posibilidades:

-

  Si el código de configuración, ``setUp()``, difiere solo ligeramente movemos
  el código que difiere del ``setUp()`` al método de prueba.

-

  Si realmente tenemos una configuración, ``setUp()``, diferente necesitamos una
  clase de caso de prueba diferente. Creamos una nueva clase que tenga la
  diferencia de configuración.

.. _fixtures.sharing-fixture:

Compartir el Ambiente
#####################

Existen varias buenas razones para compartir los ambientes entre pruebas, pero
en la mayoría de los casos la necesidad de compartir un ambiente entre pruebas
radica en un problema de diseño no resulto.

Un buen ejemplo de un ambiente que tiene sentido compartir a través de varias
pruebas es la conexión a base de datos: iniciamos sesión en la base de datos
una vez y reusamos la conexión a la base de datos en lugar de crear una nueva
conexión para cada prueba. Esto hace a las pruebas mucho más rápidas.

El :numref:`fixtures.sharing-fixture.examples.DatabaseTest.php` usa los métodos
modelo ``setUpBeforeClass()`` y ``tearDownAfterClass()`` para respectivamente
conectarse a la base de datos antes de la primera prueba del caso de pruebas
y para desconectarse de la base de datos después de la última prueba del
caso de pruebas.

.. code-block:: php
    :caption: Compartir el ambiente de prueba entre el conjunto de pruebas
    :name: fixtures.sharing-fixture.examples.DatabaseTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class DatabaseTest extends TestCase
    {
        protected static $dbh;

        public static function setUpBeforeClass()
        {
            self::$dbh = new PDO('sqlite::memory:');
        }

        public static function tearDownAfterClass()
        {
            self::$dbh = null;
        }
    }
    ?>

Nunca es suficiente decir que compartir ambientes entre pruebas
reduce el costo de las pruebas. El problema subyacente de diseño es que
los objetos no están suficientemente desacoplados. Alcanzaremos mejores
resultados resolviendo el problema de diseño subyacente y luego escribiendo
pruebas usando esbozos (ver :ref:`test-doubles`), que creando
dependencias entre pruebas en tiempo de ejecución e ignorando la oportunidad
de mejorar el diseño.

.. _fixtures.global-state:

Estado Global
#############

`Es difícil probar código que usa instancias únicas de objetos (singletons). <http://googletesting.blogspot.com/2008/05/tott-using-dependancy-injection-to.html>`_
Lo mismo es verdad para el código que usa variables globales. Generalmente,
el código que queremos probar está fuertemente acoplado con las variables
globales y no podemos controlar su creación. Un problema adicional está en el
hecho de que un cambio en la variable global para una prueba podría romper
otra prueba.

En PHP las variables globales funcional de esta manera:

-

  Una variable global ``$foo = 'bar';`` se almacena como ``$GLOBALS['foo'] = 'bar';``.

-

  La variable ``$GLOBALS`` es una variable *super-global*.

-

  Las variables super-globales son variables integradas que siempre están
  disponibles en todos los ámbitos.

-

  En el ámbito de una función o método, podemos acceder a la variable global
  ``$foo`` directamente accediendo a ``$GLOBALS['foo']`` o usando
  ``global $foo;`` para crear una variable local con una referencia a la
  variable global.

Además de las variables globales, los atributos estáticos de clases son también
parte del estado global.

Antes de la versión 6, por defecto, PHPUnit ejecutaba las pruebas de una manera que
los cambios de las variables globales y super-globales (``$GLOBALS``,
``$_ENV``, ``$_POST``,
``$_GET``, ``$_COOKIE``,
``$_SERVER``, ``$_FILES``,
``$_REQUEST``) no afectaban a otras pruebas.

Desde la versión 5, PHPUnit no ejecuta por defecto esta operación de respaldo y
restauración para las variables globales y super-globales.
Esto se puede activar usando la opción ``--globals-backup``
o agregando ``backupGlobals="true"`` en el
archivo de configuración XML.

Usando la opción ``--static-backup`` o agregando
``backupStaticAttributes="true"`` en el archivo de configuración, conseguimos
que este aislamiento se puede extender a los atributos
estáticos de clase.

.. admonition:: Nota

   Las operaciones de respaldo y restauración para todas las variables y
   atributos estáticos de clase usan ``serialize()`` y
   ``unserialize()``.

   Los objetos de algunas clases (por ejemplo, ``PDO``) no se pueden serializar
   y la operación de respaldo se romperá cuando un objeto de este tipo se
   guarde, por ejemplo, en el arreglo ``$GLOBALS``.

La anotación ``@backupGlobals`` sobre la que se discute en el apéndice
:ref:`appendixes.annotations.backupGlobals` se puede usar para controlar las
operaciones de respaldo y restauración de variables globales. Además, podemos
proveer una lista negra de variables globales que deben ser excluidas de las
operaciones de respaldo y recuperación de la siguiente manera:

.. code-block:: php

    class MyTest extends TestCase
    {
        protected $backupGlobalsBlacklist = ['globalVariable'];

        // ...
    }

.. admonition:: Nota

   Definir la propiedad ``$backupGlobalsBlacklist`` dentro
   del método ``setUp()`` no tiene efecto.

La anotación ``@backupStaticAttributes`` que se discute en el apéndice
:ref:`appendixes.annotations.backupStaticAttributes`
se puede usar para hacer un respaldo de todos los valores de las propiedades
estáticas de todas las clases declaradas antes de cada prueba para restaurarlas
después de la prueba.

Esta anotación procesa todas las clases, no solo la clase de prueba, que se
declaran en el momento en que una prueba comienza. Esto solo aplica a las
propiedades estáticas de clases y no a las variables estáticas que están dentro
de funciones.

.. admonition:: Nota

   La operación ``@backupStaticAttributes`` se ejecuta antes de un método de
   prueba, pero solo si esta activado. Si el valor estático fue cambiado por
   la ejecución previa de una prueba que no tenía la ``@backupStaticAttributes``
   activado, entonces el valor será respaldado y restaurado -- no el valor por
   defecto declarado originalmente. PHP no registra el valor por defecto
   declarada originalmente de ninguna variable estática.

   Los mismo aplica con propiedades estáticas de clases que sean declaradas o
   cargadas recientemente dentro de una prueba. Como sus valores son desconocidos las
   pruebas no pueden redefinir después de su ejecución los valores por defecto
   declarados originalmente. Cualquiera valor que es colocado pasará a las otras
   pruebas.

   Para las pruebas unitarias, se recomienda redefinir explícitamente los valores
   de la propiedades estáticas dentro de la prueba en nuestro código de ``setUp()``
   (e idealmente también en el ``tearDown()``, para no afectar las pruebas que
   se ejecuten posteriormente).

Podemos proveer una lista negra de los atributos estáticos que deben ser
excluidos de las operaciones de respaldo y la restauración:

.. code-block:: php

    class MyTest extends TestCase
    {
        protected $backupStaticAttributesBlacklist = [
            'className' => ['attributeName']
        ];

        // ...
    }

.. admonition:: Nota

   Colocar la propiedad ``$backupStaticAttributesBlacklist`` dentro del método
   ``setUp()`` no tiene efecto.
