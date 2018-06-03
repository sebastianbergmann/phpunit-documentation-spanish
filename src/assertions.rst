

.. _appendixes.assertions:

==========
Aserciones
==========

En este apéndice listamos varios métodos para hacer aserciones que están
disponibles.

.. _appendixes.assertions.static-vs-non-static-usage-of-assertion-methods:

Static vs. Non-Static Usage of Assertion Methods
################################################

Las aserciones de PHPUnit están implementadas en ``PHPUnit\Framework\Assert``.
``PHPUnit\Framework\TestCase`` hereda de ``PHPUnit\Framework\Assert``.

Los métodos para hacer aserciones son declarados estáticos y se pueden invocar
desde cualquier contexto usando ``PHPUnit\Framework\Assert::assertTrue()``,
``$this->assertTrue()`` o ``self::assertTrue()`` en una clase que extiende de
PHPUnit\\Framework\\TestCase.

De hecho, incluso podemos usar envoltorios de funciones globales como ``assertTrue()``
en cualquier contexto (incluyendo clases que extienden de ``PHPUnit\Framework\TestCase``)
cuando hemos incluido (manualmente) el archivo con código fuente
:file:`src/Framework/Assert/Functions.php` que viene con PHPUnit.

Una pregunta común, especialmente de los nuevos desarrolladores de PHPUnit,
es cual es «la manera correcta» de invocar una aserción si usar ``$this->assertTrue()``
o ``self::assertTrue()``. La respuesta corta es: no hay una manera correcta. Y tampoco
hay una manera incorrecta. Es un asunto de preferencias personales.

La mayoría de las personas «se siente segura» usando ``$this->assertTrue()`` porque
el método de prueba se invoca en un objeto de prueba. El hecho de que los métodos
para hacer aserciones se declaran estáticos permite (re)usarlos fuera del ámbito
de un objeto de prueba. Por último, la envoltura de funciones globales permite
a los desarrolladores escribir menos caracteres (``assertTrue()`` en lugar de
``$this->assertTrue()`` o ``self::assertTrue())``.

.. _appendixes.assertions.assertArrayHasKey:

assertArrayHasKey()
###################

``assertArrayHasKey(mixed $key, array $array[, string $message = ''])``

Reporta un error identificado por el ``$message`` si el ``$array`` no tiene la
llave ``$key``.

``assertArrayNotHasKey()`` es el inverso de este método y toma los mismos
argumentos.

.. code-block:: php
    :caption: Uso del método assertArrayHasKey()
    :name: appendixes.assertions.assertArrayHasKey.example

    <?php
    use PHPUnit\Framework\TestCase;

    class ArrayHasKeyTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertArrayHasKey('foo', ['bar' => 'baz']);
        }
    }

.. code-block:: bash

    $ phpunit ArrayHasKeyTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) ArrayHasKeyTest::testFailure
    Failed asserting that an array has the key 'foo'.

    /home/sb/ArrayHasKeyTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertClassHasAttribute:

assertClassHasAttribute()
#########################

``assertClassHasAttribute(string $attributeName, string $className[, string $message = ''])``

Reporta un error identificado por el ``$message`` si ``$className::attributeName`` no existe.

``assertClassNotHasAttribute()`` es el inverso de esta aserción y toma los
mismos argumentos.

.. code-block:: php
    :caption: Uso del método assertClassHasAttribute()
    :name: appendixes.assertions.assertClassHasAttribute.example

    <?php
    use PHPUnit\Framework\TestCase;

    class ClassHasAttributeTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertClassHasAttribute('foo', stdClass::class);
        }
    }

.. code-block:: bash

    $ phpunit ClassHasAttributeTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) ClassHasAttributeTest::testFailure
    Failed asserting that class "stdClass" has attribute "foo".

    /home/sb/ClassHasAttributeTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertArraySubset:

assertArraySubset()
###################

``assertArraySubset(array $subset, array $array[, bool $strict = false, string $message = ''])``

Reporta un error identificado por el ``$message`` si el ``$array`` no contiene
el ``$subset``.

``$strict`` es una bandera usada para comparar la identidad de objetos dentro
de arreglos.

.. code-block:: php
    :caption: Uso del método assertArraySubset()
    :name: appendixes.assertions.assertArraySubset.example

    <?php
    use PHPUnit\Framework\TestCase;

    class ArraySubsetTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertArraySubset(['config' => ['key-a', 'key-b']], ['config' => ['key-a']]);
        }
    }

.. code-block:: bash

    $ phpunit ArraySubsetTest
    PHPUnit 7.0.0 by Sebastian Bergmann.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) Epilog\EpilogTest::testNoFollowOption
    Failed asserting that an array has the subset Array &0 (
        'config' => Array &1 (
            0 => 'key-a'
            1 => 'key-b'
        )
    ).

    /home/sb/ArraySubsetTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertClassHasStaticAttribute:

assertClassHasStaticAttribute()
###############################

``assertClassHasStaticAttribute(string $attributeName, string $className[, string $message = ''])``

Reporta un error identificado por el ``$message`` si ``$className::attributeName``
no existe.

``assertClassNotHasStaticAttribute()`` es el inverso de esta aserción y toma los mismos argumentos.

.. code-block:: php
    :caption: Uso del método assertClassHasStaticAttribute()
    :name: appendixes.assertions.assertClassHasStaticAttribute.example

    <?php
    use PHPUnit\Framework\TestCase;

    class ClassHasStaticAttributeTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertClassHasStaticAttribute('foo', stdClass::class);
        }
    }

.. code-block:: bash

    $ phpunit ClassHasStaticAttributeTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) ClassHasStaticAttributeTest::testFailure
    Failed asserting that class "stdClass" has static attribute "foo".

    /home/sb/ClassHasStaticAttributeTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertContains:

assertContains()
################

``assertContains(mixed $needle, Iterator|array $haystack[, string $message = ''])``

Reporta un error identificado por el ``$message`` si ``$needle`` no es un elemento
de ``$haystack``.

``assertNotContains()`` es el inverso de esta aserción y toma los mismos argumentos.

``assertAttributeContains()`` y ``assertAttributeNotContains()`` son envoltorios
convenientes que usan un atributo ``public``, ``protected`` o ``private`` de una
clase u objeto como el lugar donde buscar, *haystack*.

.. code-block:: php
    :caption: Uso del método assertContains()
    :name: appendixes.assertions.assertContains.example

    <?php
    use PHPUnit\Framework\TestCase;

    class ContainsTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertContains(4, [1, 2, 3]);
        }
    }

.. code-block:: bash

    $ phpunit ContainsTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) ContainsTest::testFailure
    Failed asserting that an array contains 4.

    /home/sb/ContainsTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

``assertContains(string $needle, string $haystack[, string $message = '', boolean $ignoreCase = false])``

Reporta un error identificado por el ``$message`` si ``$needle`` no es una subcadena
de caracteres de ``$haystack``.

Si ``$ignoreCase`` es ``true``, la prueba será insensible a mayúsculas y minúsculas.

.. code-block:: php
    :caption: Uso del método assertContains()
    :name: appendixes.assertions.assertContains.example2

    <?php
    use PHPUnit\Framework\TestCase;

    class ContainsTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertContains('baz', 'foobar');
        }
    }

.. code-block:: bash

    $ phpunit ContainsTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) ContainsTest::testFailure
    Failed asserting that 'foobar' contains "baz".

    /home/sb/ContainsTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. code-block:: php
    :caption: Uso del método assertContains() con $ignoreCase
    :name: appendixes.assertions.assertContains.example3

    <?php
    use PHPUnit\Framework\TestCase;

    class ContainsTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertContains('foo', 'FooBar');
        }

        public function testOK()
        {
            $this->assertContains('foo', 'FooBar', '', true);
        }
    }

.. code-block:: bash

    $ phpunit ContainsTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F.

    Time: 0 seconds, Memory: 2.75Mb

    There was 1 failure:

    1) ContainsTest::testFailure
    Failed asserting that 'FooBar' contains "foo".

    /home/sb/ContainsTest.php:6

    FAILURES!
    Tests: 2, Assertions: 2, Failures: 1.

.. _appendixes.assertions.assertContainsOnly:

assertContainsOnly()
####################

``assertContainsOnly(string $type, Iterator|array $haystack[, boolean $isNativeType = null, string $message = ''])``

Reporta un error identificado por el ``$message`` si ``$haystack`` no contiene
solamente variables del tipo ``$type``.

``$isNativeType`` es una bandera usada para indicar si ``$type`` es un tipo
nativo de PHP o no.

``assertNotContainsOnly()`` es el inverso de esta aserción y toma los mismos argumentos.

``assertAttributeContainsOnly()`` y ``assertAttributeNotContainsOnly()`` son
envoltorios convenientes que usan un atributo ``public``, ``protected`` o
``private`` de una clases u objeto como el lugar donde buscar, *haystack*.

.. code-block:: php
    :caption: Uso del método assertContainsOnly()
    :name: appendixes.assertions.assertContainsOnly.example

    <?php
    use PHPUnit\Framework\TestCase;

    class ContainsOnlyTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertContainsOnly('string', ['1', '2', 3]);
        }
    }

.. code-block:: bash

    $ phpunit ContainsOnlyTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) ContainsOnlyTest::testFailure
    Failed asserting that Array (
        0 => '1'
        1 => '2'
        2 => 3
    ) contains only values of type "string".

    /home/sb/ContainsOnlyTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertContainsOnlyInstancesOf:

assertContainsOnlyInstancesOf()
###############################

``assertContainsOnlyInstancesOf(string $classname, Traversable|array $haystack[, string $message = ''])``

Reporta un error identificado por el ``$message`` si ``$haystack`` no contiene
solamente instancias de la clase ``$classname``.

.. code-block:: php
    :caption: Uso del método assertContainsOnlyInstancesOf()
    :name: appendixes.assertions.assertContainsOnlyInstancesOf.example

    <?php
    use PHPUnit\Framework\TestCase;

    class ContainsOnlyInstancesOfTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertContainsOnlyInstancesOf(
                Foo::class,
                [new Foo, new Bar, new Foo]
            );
        }
    }

.. code-block:: bash

    $ phpunit ContainsOnlyInstancesOfTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) ContainsOnlyInstancesOfTest::testFailure
    Failed asserting that Array ([0]=> Bar Object(...)) is an instance of class "Foo".

    /home/sb/ContainsOnlyInstancesOfTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertCount:

assertCount()
#############

``assertCount($expectedCount, $haystack[, string $message = ''])``

Reporta un error identificado por el ``$message`` si el número de elementos
en ``$haystack`` no es ``$expectedCount``.

``assertNotCount()`` es el inverso de esta aserción y toma los mismos
argumentos.

.. code-block:: php
    :caption: Uso del método assertCount()
    :name: appendixes.assertions.assertCount.example

    <?php
    use PHPUnit\Framework\TestCase;

    class CountTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertCount(0, ['foo']);
        }
    }

.. code-block:: bash

    $ phpunit CountTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) CountTest::testFailure
    Failed asserting that actual size 1 matches expected size 0.

    /home/sb/CountTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertDirectoryExists:

assertDirectoryExists()
#######################

``assertDirectoryExists(string $directory[, string $message = ''])``

Reporta un error identificado por el ``$message`` si la carpeta especificada
por ``$directory`` no existe.

``assertDirectoryNotExists()`` es el inverso de esta aserción y toma los mismos
argumentos.

.. code-block:: php
    :caption: Uso del método assertDirectoryExists()
    :name: appendixes.assertions.assertDirectoryExists.example

    <?php
    use PHPUnit\Framework\TestCase;

    class DirectoryExistsTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertDirectoryExists('/path/to/directory');
        }
    }

.. code-block:: bash

    $ phpunit DirectoryExistsTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) DirectoryExistsTest::testFailure
    Failed asserting that directory "/path/to/directory" exists.

    /home/sb/DirectoryExistsTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertDirectoryIsReadable:

assertDirectoryIsReadable()
###########################

``assertDirectoryIsReadable(string $directory[, string $message = ''])``

Reporta un error identificado por el ``$message`` si la carpeta especificada
en ``$directory`` no es una carpeta o no es legible.

``assertDirectoryNotIsReadable()`` es el inverso de esta aserción y toma los
mismos argumentos.

.. code-block:: php
    :caption: Uso del método assertDirectoryIsReadable()
    :name: appendixes.assertions.assertDirectoryIsReadable.example

    <?php
    use PHPUnit\Framework\TestCase;

    class DirectoryIsReadableTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertDirectoryIsReadable('/path/to/directory');
        }
    }

.. code-block:: bash

    $ phpunit DirectoryIsReadableTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) DirectoryIsReadableTest::testFailure
    Failed asserting that "/path/to/directory" is readable.

    /home/sb/DirectoryIsReadableTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertDirectoryIsWritable:

assertDirectoryIsWritable()
###########################

``assertDirectoryIsWritable(string $directory[, string $message = ''])``

Reporta un error identificado por el ``$message`` si la carpeta especificada en
``$directory`` no es una carpeta o no se puede escribir en ella.

``assertDirectoryNotIsWritable()`` es el inverso de esta aserción y toma los
mismos argumentos.

.. code-block:: php
    :caption: Uso del método assertDirectoryIsWritable()
    :name: appendixes.assertions.assertDirectoryIsWritable.example

    <?php
    use PHPUnit\Framework\TestCase;

    class DirectoryIsWritableTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertDirectoryIsWritable('/path/to/directory');
        }
    }

.. code-block:: bash

    $ phpunit DirectoryIsWritableTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) DirectoryIsWritableTest::testFailure
    Failed asserting that "/path/to/directory" is writable.

    /home/sb/DirectoryIsWritableTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertEmpty:

assertEmpty()
#############

``assertEmpty(mixed $actual[, string $message = ''])``

Reporta un error identificado por el ``$message`` si ``$actual`` no está vacío.

``assertNotEmpty()`` es el inverso de esta aserción y toma los mismos argumentos.

``assertAttributeEmpty()`` y ``assertAttributeNotEmpty()`` son envoltorios
apropiados para ser usados con atributos de tipo ``public``, ``protected`` o
``private`` de un objeto o una clase.

.. code-block:: php
    :caption: Uso del método assertEmpty()
    :name: appendixes.assertions.assertEmpty.example

    <?php
    use PHPUnit\Framework\TestCase;

    class EmptyTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertEmpty(['foo']);
        }
    }

.. code-block:: bash

    $ phpunit EmptyTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) EmptyTest::testFailure
    Failed asserting that an array is empty.

    /home/sb/EmptyTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertEqualXMLStructure:

assertEqualXMLStructure()
#########################

``assertEqualXMLStructure(DOMElement $expectedElement, DOMElement $actualElement[, boolean $checkAttributes = false, string $message = ''])``

Reporta un error identificado por el ``$message`` si la estructura del DOMElement
en ``$actualElement`` no es igual a la estructura XML del DOMElement en
``$expectedElement``.

.. code-block:: php
    :caption: Uso del método assertEqualXMLStructure()
    :name: appendixes.assertions.assertEqualXMLStructure.example

    <?php
    use PHPUnit\Framework\TestCase;

    class EqualXMLStructureTest extends TestCase
    {
        public function testFailureWithDifferentNodeNames()
        {
            $expected = new DOMElement('foo');
            $actual = new DOMElement('bar');

            $this->assertEqualXMLStructure($expected, $actual);
        }

        public function testFailureWithDifferentNodeAttributes()
        {
            $expected = new DOMDocument;
            $expected->loadXML('<foo bar="true" />');

            $actual = new DOMDocument;
            $actual->loadXML('<foo/>');

            $this->assertEqualXMLStructure(
              $expected->firstChild, $actual->firstChild, true
            );
        }

        public function testFailureWithDifferentChildrenCount()
        {
            $expected = new DOMDocument;
            $expected->loadXML('<foo><bar/><bar/><bar/></foo>');

            $actual = new DOMDocument;
            $actual->loadXML('<foo><bar/></foo>');

            $this->assertEqualXMLStructure(
              $expected->firstChild, $actual->firstChild
            );
        }

        public function testFailureWithDifferentChildren()
        {
            $expected = new DOMDocument;
            $expected->loadXML('<foo><bar/><bar/><bar/></foo>');

            $actual = new DOMDocument;
            $actual->loadXML('<foo><baz/><baz/><baz/></foo>');

            $this->assertEqualXMLStructure(
              $expected->firstChild, $actual->firstChild
            );
        }
    }

.. code-block:: bash

    $ phpunit EqualXMLStructureTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    FFFF

    Time: 0 seconds, Memory: 5.75Mb

    There were 4 failures:

    1) EqualXMLStructureTest::testFailureWithDifferentNodeNames
    Failed asserting that two strings are equal.
    --- Expected
    +++ Actual
    @@ @@
    -'foo'
    +'bar'

    /home/sb/EqualXMLStructureTest.php:9

    2) EqualXMLStructureTest::testFailureWithDifferentNodeAttributes
    Number of attributes on node "foo" does not match
    Failed asserting that 0 matches expected 1.

    /home/sb/EqualXMLStructureTest.php:22

    3) EqualXMLStructureTest::testFailureWithDifferentChildrenCount
    Number of child nodes of "foo" differs
    Failed asserting that 1 matches expected 3.

    /home/sb/EqualXMLStructureTest.php:35

    4) EqualXMLStructureTest::testFailureWithDifferentChildren
    Failed asserting that two strings are equal.
    --- Expected
    +++ Actual
    @@ @@
    -'bar'
    +'baz'

    /home/sb/EqualXMLStructureTest.php:48

    FAILURES!
    Tests: 4, Assertions: 8, Failures: 4.

.. _appendixes.assertions.assertEquals:

assertEquals()
##############

``assertEquals(mixed $expected, mixed $actual[, string $message = ''])``

Reporta un error identificado por el ``$message`` si las dos variables
``$expected`` y ``$actual`` no son iguales.

``assertNotEquals()`` es el inverso de esta aserción y toma los mismos argumentos.

``assertAttributeEquals()`` y ``assertAttributeNotEquals()`` son los envoltorios
apropiados cuando se usa atributos ``public``, ``protected`` o ``private`` de
una clase u objeto para el valor real

.. code-block:: php
    :caption: Uso del método assertEquals()
    :name: appendixes.assertions.assertEquals.example

    <?php
    use PHPUnit\Framework\TestCase;

    class EqualsTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertEquals(1, 0);
        }

        public function testFailure2()
        {
            $this->assertEquals('bar', 'baz');
        }

        public function testFailure3()
        {
            $this->assertEquals("foo\nbar\nbaz\n", "foo\nbah\nbaz\n");
        }
    }

.. code-block:: bash

    $ phpunit EqualsTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    FFF

    Time: 0 seconds, Memory: 5.25Mb

    There were 3 failures:

    1) EqualsTest::testFailure
    Failed asserting that 0 matches expected 1.

    /home/sb/EqualsTest.php:6

    2) EqualsTest::testFailure2
    Failed asserting that two strings are equal.
    --- Expected
    +++ Actual
    @@ @@
    -'bar'
    +'baz'

    /home/sb/EqualsTest.php:11

    3) EqualsTest::testFailure3
    Failed asserting that two strings are equal.
    --- Expected
    +++ Actual
    @@ @@
     'foo
    -bar
    +bah
     baz
     '

    /home/sb/EqualsTest.php:16

    FAILURES!
    Tests: 3, Assertions: 3, Failures: 3.

Comparaciones más especializadas se usan para especificar el tipo de argumentos
para ``$expected`` y ``$actual``, ver abajo.

``assertEquals(float $expected, float $actual[, string $message = '', float $delta = 0])``

Reporta un error identificado por el ``$message`` si la diferencia absoluta entre
los números de tipo flotante ``$expected`` y ``$actual`` es mayor que el ``$delta``.
Si la diferencia absoluta entre los número de tipo flotante ``$expected`` y ``$actual``
es menor o *igual a* ``$delta`` entonces la aserción pasa.

Por favor lea "`Todo lo que un Científico de la Computación Debe Saber sobre la
Aritmética de Punto Flotante <http://docs.oracle.com/cd/E19957-01/806-3568/ncg_goldberg.html>`_"
para entender porqué ``$delta`` es necesario.

.. code-block:: php
    :caption: Uso del método assertEquals() con número de punto flotante
    :name: appendixes.assertions.assertEquals.example2

    <?php
    use PHPUnit\Framework\TestCase;

    class EqualsTest extends TestCase
    {
        public function testSuccess()
        {
            $this->assertEquals(1.0, 1.1, '', 0.1);
        }

        public function testFailure()
        {
            $this->assertEquals(1.0, 1.1);
        }
    }

.. code-block:: bash

    $ phpunit EqualsTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    .F

    Time: 0 seconds, Memory: 5.75Mb

    There was 1 failure:

    1) EqualsTest::testFailure
    Failed asserting that 1.1 matches expected 1.0.

    /home/sb/EqualsTest.php:11

    FAILURES!
    Tests: 2, Assertions: 2, Failures: 1.

``assertEquals(DOMDocument $expected, DOMDocument $actual[, string $message = ''])``

Reporta un error identificado por el ``$message`` si la forma canónica no comentada
del documento XML representado por los dos objectos DOMDocument ``$expected`` y
``$actual`` no son iguales.

.. code-block:: php
    :caption: Uso del método assertEquals() con objetos DOMDocument
    :name: appendixes.assertions.assertEquals.example3

    <?php
    use PHPUnit\Framework\TestCase;

    class EqualsTest extends TestCase
    {
        public function testFailure()
        {
            $expected = new DOMDocument;
            $expected->loadXML('<foo><bar/></foo>');

            $actual = new DOMDocument;
            $actual->loadXML('<bar><foo/></bar>');

            $this->assertEquals($expected, $actual);
        }
    }

.. code-block:: bash

    $ phpunit EqualsTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) EqualsTest::testFailure
    Failed asserting that two DOM documents are equal.
    --- Expected
    +++ Actual
    @@ @@
     <?xml version="1.0"?>
    -<foo>
    -  <bar/>
    -</foo>
    +<bar>
    +  <foo/>
    +</bar>

    /home/sb/EqualsTest.php:12

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

``assertEquals(object $expected, object $actual[, string $message = ''])``

Reporta un error identificado por el ``$message`` si los dos objetos ``$expected``
y ``$actual`` no tienen valores de atributos iguales.

.. code-block:: php
    :caption: Uso del método assertEquals() con objetos
    :name: appendixes.assertions.assertEquals.example4

    <?php
    use PHPUnit\Framework\TestCase;

    class EqualsTest extends TestCase
    {
        public function testFailure()
        {
            $expected = new stdClass;
            $expected->foo = 'foo';
            $expected->bar = 'bar';

            $actual = new stdClass;
            $actual->foo = 'bar';
            $actual->baz = 'bar';

            $this->assertEquals($expected, $actual);
        }
    }

.. code-block:: bash

    $ phpunit EqualsTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.25Mb

    There was 1 failure:

    1) EqualsTest::testFailure
    Failed asserting that two objects are equal.
    --- Expected
    +++ Actual
    @@ @@
     stdClass Object (
    -    'foo' => 'foo'
    -    'bar' => 'bar'
    +    'foo' => 'bar'
    +    'baz' => 'bar'
     )

    /home/sb/EqualsTest.php:14

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

``assertEquals(array $expected, array $actual[, string $message = ''])``

Reporta un error identificado por el ``$message`` si los dos arreglos ``$expected``
y ``$actual`` no son iguales.

.. code-block:: php
    :caption: Uso del método assertEquals() con arreglos
    :name: appendixes.assertions.assertEquals.example5

    <?php
    use PHPUnit\Framework\TestCase;

    class EqualsTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertEquals(['a', 'b', 'c'], ['a', 'c', 'd']);
        }
    }

.. code-block:: bash

    $ phpunit EqualsTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.25Mb

    There was 1 failure:

    1) EqualsTest::testFailure
    Failed asserting that two arrays are equal.
    --- Expected
    +++ Actual
    @@ @@
     Array (
         0 => 'a'
    -    1 => 'b'
    -    2 => 'c'
    +    1 => 'c'
    +    2 => 'd'
     )

    /home/sb/EqualsTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertFalse:

assertFalse()
#############

``assertFalse(bool $condition[, string $message = ''])``

Reporta un error identificado por el ``$message`` si ``$condition`` es ``true``.

``assertNotFalse()`` es el inverso de esta aserción y toma los mismos argumentos.

.. code-block:: php
    :caption: Uso del método assertFalse()
    :name: appendixes.assertions.assertFalse.example

    <?php
    use PHPUnit\Framework\TestCase;

    class FalseTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertFalse(true);
        }
    }

.. code-block:: bash

    $ phpunit FalseTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) FalseTest::testFailure
    Failed asserting that true is false.

    /home/sb/FalseTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertFileEquals:

assertFileEquals()
##################

``assertFileEquals(string $expected, string $actual[, string $message = ''])``

Reporta un error identificado por el ``$message`` si the archivo especificado en
``$expected`` no tiene el mismo contenido que el archivo especificado en ``$actual``.

``assertFileNotEquals()`` es el inverso de esta aserción y toma los mismos argumentos.

.. code-block:: php
    :caption: Uso del método assertFileEquals()
    :name: appendixes.assertions.assertFileEquals.example

    <?php
    use PHPUnit\Framework\TestCase;

    class FileEqualsTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertFileEquals('/home/sb/expected', '/home/sb/actual');
        }
    }

.. code-block:: bash

    $ phpunit FileEqualsTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.25Mb

    There was 1 failure:

    1) FileEqualsTest::testFailure
    Failed asserting that two strings are equal.
    --- Expected
    +++ Actual
    @@ @@
    -'expected
    +'actual
     '

    /home/sb/FileEqualsTest.php:6

    FAILURES!
    Tests: 1, Assertions: 3, Failures: 1.

.. _appendixes.assertions.assertFileExists:

assertFileExists()
##################

``assertFileExists(string $filename[, string $message = ''])``

Reporta un error identificado por el ``$message`` si the archivo especificado en
``$filename`` no existe.

``assertFileNotExists()`` es el inverso de esta aserción y toma los mismos argumentos.

.. code-block:: php
    :caption: Uso del método assertFileExists()
    :name: appendixes.assertions.assertFileExists.example

    <?php
    use PHPUnit\Framework\TestCase;

    class FileExistsTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertFileExists('/path/to/file');
        }
    }

.. code-block:: bash

    $ phpunit FileExistsTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) FileExistsTest::testFailure
    Failed asserting that file "/path/to/file" exists.

    /home/sb/FileExistsTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertFileIsReadable:

assertFileIsReadable()
######################

``assertFileIsReadable(string $filename[, string $message = ''])``

Reporta un error identificado por el ``$message`` si el archivo especificado
en ``$filename`` no es un archivo o no es legible.

``assertFileNotIsReadable()`` es el inverso de esta aserción y toma los mismos argumentos.

.. code-block:: php
    :caption: Uso del método assertFileIsReadable()
    :name: appendixes.assertions.assertFileIsReadable.example

    <?php
    use PHPUnit\Framework\TestCase;

    class FileIsReadableTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertFileIsReadable('/path/to/file');
        }
    }

.. code-block:: bash

    $ phpunit FileIsReadableTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) FileIsReadableTest::testFailure
    Failed asserting that "/path/to/file" is readable.

    /home/sb/FileIsReadableTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertFileIsWritable:

assertFileIsWritable()
######################

``assertFileIsWritable(string $filename[, string $message = ''])``

Reporta un error identificado por el ``$message`` si el archivo especificado en
``$filename`` no es un archivo o no se puede escribir en él.

``assertFileNotIsWritable()`` es el inverso de esta aserción y toma los mismos argumentos.

.. code-block:: php
    :caption: Uso del método assertFileIsWritable()
    :name: appendixes.assertions.assertFileIsWritable.example

    <?php
    use PHPUnit\Framework\TestCase;

    class FileIsWritableTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertFileIsWritable('/path/to/file');
        }
    }

.. code-block:: bash

    $ phpunit FileIsWritableTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) FileIsWritableTest::testFailure
    Failed asserting that "/path/to/file" is writable.

    /home/sb/FileIsWritableTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertGreaterThan:

assertGreaterThan()
###################

``assertGreaterThan(mixed $expected, mixed $actual[, string $message = ''])``

Reporta un error identificado por el ``$message`` si el valor de ``$actual``
no es mayor que el valor de ``$expected``.

``assertAttributeGreaterThan()`` es un envoltorio conveniente que usa el atributo
``public``, ``protected`` o ``private`` de una clase u objeto como el valor real..

.. code-block:: php
    :caption: Uso del método assertGreaterThan()
    :name: appendixes.assertions.assertGreaterThan.example

    <?php
    use PHPUnit\Framework\TestCase;

    class GreaterThanTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertGreaterThan(2, 1);
        }
    }

.. code-block:: bash

    $ phpunit GreaterThanTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) GreaterThanTest::testFailure
    Failed asserting that 1 is greater than 2.

    /home/sb/GreaterThanTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertGreaterThanOrEqual:

assertGreaterThanOrEqual()
##########################

``assertGreaterThanOrEqual(mixed $expected, mixed $actual[, string $message = ''])``

Reporta un error identificado por el ``$message`` si el valor de ``$actual``
no es mayor o igual al valor de ``$expected``.

``assertAttributeGreaterThanOrEqual()`` es un envoltorio conveniente que usa el
atributo ``public``, ``protected`` o ``private`` de una clase u objeto como el
valor real.

.. code-block:: php
    :caption: Uso del método assertGreaterThanOrEqual()
    :name: appendixes.assertions.assertGreaterThanOrEqual.example

    <?php
    use PHPUnit\Framework\TestCase;

    class GreatThanOrEqualTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertGreaterThanOrEqual(2, 1);
        }
    }

.. code-block:: bash

    $ phpunit GreaterThanOrEqualTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.25Mb

    There was 1 failure:

    1) GreatThanOrEqualTest::testFailure
    Failed asserting that 1 is equal to 2 or is greater than 2.

    /home/sb/GreaterThanOrEqualTest.php:6

    FAILURES!
    Tests: 1, Assertions: 2, Failures: 1.

.. _appendixes.assertions.assertInfinite:

assertInfinite()
################

``assertInfinite(mixed $variable[, string $message = ''])``

Reporta un error identificado por el ``$message`` si ``$variable`` es not ``INF``.

``assertFinite()`` es el inverso de esta aserción y toma los mismos argumentos.

.. code-block:: php
    :caption: Uso del método assertInfinite()
    :name: appendixes.assertions.assertInfinite.example

    <?php
    use PHPUnit\Framework\TestCase;

    class InfiniteTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertInfinite(1);
        }
    }

.. code-block:: bash

    $ phpunit InfiniteTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) InfiniteTest::testFailure
    Failed asserting that 1 is infinite.

    /home/sb/InfiniteTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertInstanceOf:

assertInstanceOf()
##################

``assertInstanceOf($expected, $actual[, $message = ''])``

Reporta un error identificado por el ``$message`` si ``$actual`` no es una
instancia de ``$expected``.

``assertNotInstanceOf()`` es el inverso de esta aserción y toma los mismos argumentos.

``assertAttributeInstanceOf()`` son ``assertAttributeNotInstanceOf()`` son
envoltorios convenientes que se pueden aplicar a un atributo ``public``,
``protected`` o ``private`` de una clase u objeto.

.. code-block:: php
    :caption: Uso del método assertInstanceOf()
    :name: appendixes.assertions.assertInstanceOf.example

    <?php
    use PHPUnit\Framework\TestCase;

    class InstanceOfTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertInstanceOf(RuntimeException::class, new Exception);
        }
    }

.. code-block:: bash

    $ phpunit InstanceOfTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) InstanceOfTest::testFailure
    Failed asserting that Exception Object (...) is an instance of class "RuntimeException".

    /home/sb/InstanceOfTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertInternalType:

assertInternalType()
####################

``assertInternalType($expected, $actual[, $message = ''])``

Reporta un error identificado por el ``$message`` si ``$actual`` no es del tipo
``$expected``.

``assertNotInternalType()`` es el inverso de esta aserción y toma los mismos argumentos.

``assertAttributeInternalType()`` y ``assertAttributeNotInternalType()`` son
envoltorios convenientes que se pueden aplicar a un atributo ``public``,
``protected`` o ``private`` de una clase u objeto.

.. code-block:: php
    :caption: Uso del método assertInternalType()
    :name: appendixes.assertions.assertInternalType.example

    <?php
    use PHPUnit\Framework\TestCase;

    class InternalTypeTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertInternalType('string', 42);
        }
    }

.. code-block:: bash

    $ phpunit InternalTypeTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) InternalTypeTest::testFailure
    Failed asserting that 42 is of type "string".

    /home/sb/InternalTypeTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertIsReadable:

assertIsReadable()
##################

``assertIsReadable(string $filename[, string $message = ''])``

Reporta un error identificado por el ``$message`` si el archivo o carpeta
especificada en ``$filename`` no se puede leer.

``assertNotIsReadable()`` es el inverso de esta aserción y toma los mismos argumentos.

.. code-block:: php
    :caption: Uso del método assertIsReadable()
    :name: appendixes.assertions.assertIsReadable.example

    <?php
    use PHPUnit\Framework\TestCase;

    class IsReadableTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertIsReadable('/path/to/unreadable');
        }
    }

.. code-block:: bash

    $ phpunit IsReadableTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) IsReadableTest::testFailure
    Failed asserting that "/path/to/unreadable" is readable.

    /home/sb/IsReadableTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertIsWritable:

assertIsWritable()
##################

``assertIsWritable(string $filename[, string $message = ''])``

Reporta un error identificado por el ``$message`` si el archivo o carpeta
especificada en ``$filename`` no se puede escribir.

``assertNotIsWritable()`` es el inverso de esta aserción y toma los mismos argumentos.

.. code-block:: php
    :caption: Uso del método assertIsWritable()
    :name: appendixes.assertions.assertIsWritable.example

    <?php
    use PHPUnit\Framework\TestCase;

    class IsWritableTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertIsWritable('/path/to/unwritable');
        }
    }

.. code-block:: bash

    $ phpunit IsWritableTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) IsWritableTest::testFailure
    Failed asserting that "/path/to/unwritable" is writable.

    /home/sb/IsWritableTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertJsonFileEqualsJsonFile:

assertJsonFileEqualsJsonFile()
##############################

``assertJsonFileEqualsJsonFile(mixed $expectedFile, mixed $actualFile[, string $message = ''])``

Reporta un error identificado por el ``$message`` si el valor de ``$actualFile``
no coincide con el valor de ``$expectedFile``.

.. code-block:: php
    :caption: Uso del método assertJsonFileEqualsJsonFile()
    :name: appendixes.assertions.assertJsonFileEqualsJsonFile.example

    <?php
    use PHPUnit\Framework\TestCase;

    class JsonFileEqualsJsonFileTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertJsonFileEqualsJsonFile(
              'path/to/fixture/file', 'path/to/actual/file');
        }
    }

.. code-block:: bash

    $ phpunit JsonFileEqualsJsonFileTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) JsonFileEqualsJsonFile::testFailure
    Failed asserting that '{"Mascot":"Tux"}' matches JSON string "["Mascott", "Tux", "OS", "Linux"]".

    /home/sb/JsonFileEqualsJsonFileTest.php:5

    FAILURES!
    Tests: 1, Assertions: 3, Failures: 1.

.. _appendixes.assertions.assertJsonStringEqualsJsonFile:

assertJsonStringEqualsJsonFile()
################################

``assertJsonStringEqualsJsonFile(mixed $expectedFile, mixed $actualJson[, string $message = ''])``

Reporta un error identificado por el ``$message`` si el valor de ``$actualJson``
no coincide con el valor de ``$expectedFile``.

.. code-block:: php
    :caption: Uso del método assertJsonStringEqualsJsonFile()
    :name: appendixes.assertions.assertJsonStringEqualsJsonFile.example

    <?php
    use PHPUnit\Framework\TestCase;

    class JsonStringEqualsJsonFileTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertJsonStringEqualsJsonFile(
                'path/to/fixture/file', json_encode(['Mascot' => 'ux'])
            );
        }
    }

.. code-block:: bash

    $ phpunit JsonStringEqualsJsonFileTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) JsonStringEqualsJsonFile::testFailure
    Failed asserting that '{"Mascot":"ux"}' matches JSON string "{"Mascott":"Tux"}".

    /home/sb/JsonStringEqualsJsonFileTest.php:5

    FAILURES!
    Tests: 1, Assertions: 3, Failures: 1.

.. _appendixes.assertions.assertJsonStringEqualsJsonString:

assertJsonStringEqualsJsonString()
##################################

``assertJsonStringEqualsJsonString(mixed $expectedJson, mixed $actualJson[, string $message = ''])``

Reporta un error identificado por el ``$message`` si el valor de ``$actualJson``
no coincide con el valor de ``$expectedJson``.

.. code-block:: php
    :caption: Uso del método assertJsonStringEqualsJsonString()
    :name: appendixes.assertions.assertJsonStringEqualsJsonString.example

    <?php
    use PHPUnit\Framework\TestCase;

    class JsonStringEqualsJsonStringTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertJsonStringEqualsJsonString(
                json_encode(['Mascot' => 'Tux']),
                json_encode(['Mascot' => 'ux'])
            );
        }
    }

.. code-block:: bash

    $ phpunit JsonStringEqualsJsonStringTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) JsonStringEqualsJsonStringTest::testFailure
    Failed asserting that two objects are equal.
    --- Expected
    +++ Actual
    @@ @@
     stdClass Object (
     -    'Mascot' => 'Tux'
     +    'Mascot' => 'ux'
    )

    /home/sb/JsonStringEqualsJsonStringTest.php:5

    FAILURES!
    Tests: 1, Assertions: 3, Failures: 1.

.. _appendixes.assertions.assertLessThan:

assertLessThan()
################

``assertLessThan(mixed $expected, mixed $actual[, string $message = ''])``

Reporta un error identificado por el ``$message`` si el valor de ``$actual`` no
es menor que el valor de ``$expected``.

``assertAttributeLessThan()`` es un envoltorio conveniente que usa el atributo
``public``, ``protected`` o ``private`` de una clase u objeto como el valor real.

.. code-block:: php
    :caption: Uso del método assertLessThan()
    :name: appendixes.assertions.assertLessThan.example

    <?php
    use PHPUnit\Framework\TestCase;

    class LessThanTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertLessThan(1, 2);
        }
    }

.. code-block:: bash

    $ phpunit LessThanTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) LessThanTest::testFailure
    Failed asserting that 2 is less than 1.

    /home/sb/LessThanTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertLessThanOrEqual:

assertLessThanOrEqual()
#######################

``assertLessThanOrEqual(mixed $expected, mixed $actual[, string $message = ''])``

Reporta un error identificado por el ``$message`` si el valor de ``$actual``
no es menor o igual que el valor de ``$expected``.

``assertAttributeLessThanOrEqual()`` es un envoltorio conveniente que usa el
atributo ``public``, ``protected`` o ``private`` de una clase u objeto como el
valor real.

.. code-block:: php
    :caption: Uso del método assertLessThanOrEqual()
    :name: appendixes.assertions.assertLessThanOrEqual.example

    <?php
    use PHPUnit\Framework\TestCase;

    class LessThanOrEqualTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertLessThanOrEqual(1, 2);
        }
    }

.. code-block:: bash

    $ phpunit LessThanOrEqualTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.25Mb

    There was 1 failure:

    1) LessThanOrEqualTest::testFailure
    Failed asserting that 2 is equal to 1 or is less than 1.

    /home/sb/LessThanOrEqualTest.php:6

    FAILURES!
    Tests: 1, Assertions: 2, Failures: 1.

.. _appendixes.assertions.assertNan:

assertNan()
###########

``assertNan(mixed $variable[, string $message = ''])``

Reporta un error identificado por el ``$message`` si ``$variable`` no es ``NAN``.

.. code-block:: php
    :caption: Uso del método assertNan()
    :name: appendixes.assertions.assertNan.example

    <?php
    use PHPUnit\Framework\TestCase;

    class NanTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertNan(1);
        }
    }

.. code-block:: bash

    $ phpunit NanTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) NanTest::testFailure
    Failed asserting that 1 is nan.

    /home/sb/NanTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertNull:

assertNull()
############

``assertNull(mixed $variable[, string $message = ''])``

Reporta un error identificado por el ``$message`` si ``$variable`` no es ``null``.

``assertNotNull()`` es el inverso de esta aserción y toma los mismos argumentos.

.. code-block:: php
    :caption: Uso del método assertNull()
    :name: appendixes.assertions.assertNull.example

    <?php
    use PHPUnit\Framework\TestCase;

    class NullTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertNull('foo');
        }
    }

.. code-block:: bash

    $ phpunit NotNullTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) NullTest::testFailure
    Failed asserting that 'foo' is null.

    /home/sb/NotNullTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertObjectHasAttribute:

assertObjectHasAttribute()
##########################

``assertObjectHasAttribute(string $attributeName, object $object[, string $message = ''])``

Reporta un error identificado por el ``$message`` si ``$object->attributeName``
no existe.

``assertObjectNotHasAttribute()`` es el inverso de esta aserción y toma los mismos argumentos.

.. code-block:: php
    :caption: Uso del método assertObjectHasAttribute()
    :name: appendixes.assertions.assertObjectHasAttribute.example

    <?php
    use PHPUnit\Framework\TestCase;

    class ObjectHasAttributeTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertObjectHasAttribute('foo', new stdClass);
        }
    }

.. code-block:: bash

    $ phpunit ObjectHasAttributeTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) ObjectHasAttributeTest::testFailure
    Failed asserting that object of class "stdClass" has attribute "foo".

    /home/sb/ObjectHasAttributeTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertRegExp:

assertRegExp()
##############

``assertRegExp(string $pattern, string $string[, string $message = ''])``

Reporta un error identificado por el ``$message`` si ``$string`` no coincide con
la expresión regular ``$pattern``.

``assertNotRegExp()`` es el inverso de esta aserción y toma los mismos argumentos.

.. code-block:: php
    :caption: Uso del método assertRegExp()
    :name: appendixes.assertions.assertRegExp.example

    <?php
    use PHPUnit\Framework\TestCase;

    class RegExpTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertRegExp('/foo/', 'bar');
        }
    }

.. code-block:: bash

    $ phpunit RegExpTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) RegExpTest::testFailure
    Failed asserting that 'bar' matches PCRE pattern "/foo/".

    /home/sb/RegExpTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertStringMatchesFormat:

assertStringMatchesFormat()
###########################

``assertStringMatchesFormat(string $format, string $string[, string $message = ''])``

Reporta un error identificado por el ``$message`` si el ``$string`` no coincide con
el formato dado en ``$format``.

``assertStringNotMatchesFormat()`` es el inverso de esta aserción y toma los mismos argumentos.

.. code-block:: php
    :caption: Uso del método assertStringMatchesFormat()
    :name: appendixes.assertions.assertStringMatchesFormat.example

    <?php
    use PHPUnit\Framework\TestCase;

    class StringMatchesFormatTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertStringMatchesFormat('%i', 'foo');
        }
    }

.. code-block:: bash

    $ phpunit StringMatchesFormatTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) StringMatchesFormatTest::testFailure
    Failed asserting that 'foo' matches PCRE pattern "/^[+-]?\d+$/s".

    /home/sb/StringMatchesFormatTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

La cadena de caracteres que designa el formato puede contener los siguientes
sustitutos:

-

  ``%e``: Representa el separador de carpetas, por ejemplo ``/`` en GNU/Linux.

-

  ``%s``: Uno o más de cualquier carácter (o espacio en blanco) excepto el
  carácter de fin de línea.

-

  ``%S``: Cero o más de cualquier carácter (o espacio en blanco) excepto el
  carácter de fin de línea.

-

  ``%a``: Uno o más de cualquier carácter (o espacio en blanco) incluyendo el
  carácter de fin de línea.

-

  ``%A``: Cero o más de cualquier carácter (o espacio en blanco) incluyendo el
  carácter de fin de línea.

-

  ``%w``: Cero o más caracteres de espacio en blanco.

-

  ``%i``: Un valor entero con signo, por ejemplo ``+3142``, ``-3142``.

-

  ``%d``: Un valor entero sin signo, por ejemplo ``123456``.

-

  ``%x``: Uno o más caracteres hexadecimales. Esto es, caracteres en el rango de
  ``0-9``, ``a-f`` y ``A-F``.

-

  ``%f``: Un número de punto flotante, por ejemplo: ``3.142``, ``-3.142``,
  ``3.142E-10`` o ``3.142e+10``.

-

  ``%c``: Un solo carácter de cualquier tipo.

.. _appendixes.assertions.assertStringMatchesFormatFile:

assertStringMatchesFormatFile()
###############################

``assertStringMatchesFormatFile(string $formatFile, string $string[, string $message = ''])``

Reporta un error identificado por el ``$message`` si el ``$string`` no coincide
con el contenido de ``$formatFile``.

``assertStringNotMatchesFormatFile()`` es el inverso de esta aserción y toma los
mismos argumentos.

.. code-block:: php
    :caption: Uso del método assertStringMatchesFormatFile()
    :name: appendixes.assertions.assertStringMatchesFormatFile.example

    <?php
    use PHPUnit\Framework\TestCase;

    class StringMatchesFormatFileTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertStringMatchesFormatFile('/path/to/expected.txt', 'foo');
        }
    }

.. code-block:: bash

    $ phpunit StringMatchesFormatFileTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) StringMatchesFormatFileTest::testFailure
    Failed asserting that 'foo' matches PCRE pattern "/^[+-]?\d+
    $/s".

    /home/sb/StringMatchesFormatFileTest.php:6

    FAILURES!
    Tests: 1, Assertions: 2, Failures: 1.

.. _appendixes.assertions.assertSame:

assertSame()
############

``assertSame(mixed $expected, mixed $actual[, string $message = ''])``

Reporta un error identificado por el ``$message`` si las dos variables ``$expected``
y ``$actual`` no tienen el mismo tipo y valor.

``assertNotSame()`` es el inverso de esta aserción y toma los mismos argumentos.

``assertAttributeSame()`` y ``assertAttributeNotSame()`` son envoltorios convenientes
que usan el atributo ``public``, ``protected`` o ``private`` de una clase u
objeto como el valor real.

.. code-block:: php
    :caption: Uso del método assertSame()
    :name: appendixes.assertions.assertSame.example

    <?php
    use PHPUnit\Framework\TestCase;

    class SameTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertSame('2204', 2204);
        }
    }

.. code-block:: bash

    $ phpunit SameTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) SameTest::testFailure
    Failed asserting that 2204 is identical to '2204'.

    /home/sb/SameTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

``assertSame(object $expected, object $actual[, string $message = ''])``

Reporta un error identificado por el ``$message`` si las dos variables ``$expected``
y ``$actual`` no hacen referencia al mismo objeto.

.. code-block:: php
    :caption: Uso del método assertSame() con objetos
    :name: appendixes.assertions.assertSame.example2

    <?php
    use PHPUnit\Framework\TestCase;

    class SameTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertSame(new stdClass, new stdClass);
        }
    }

.. code-block:: bash

    $ phpunit SameTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) SameTest::testFailure
    Failed asserting that two variables reference the same object.

    /home/sb/SameTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertStringEndsWith:

assertStringEndsWith()
######################

``assertStringEndsWith(string $suffix, string $string[, string $message = ''])``

Reporta un error identificado por el ``$message`` si ``$string`` no termina en
``$suffix``.

``assertStringEndsNotWith()`` es el inverso de esta aserción y toma los mismos argumentos.

.. code-block:: php
    :caption: Uso del método assertStringEndsWith()
    :name: appendixes.assertions.assertStringEndsWith.example

    <?php
    use PHPUnit\Framework\TestCase;

    class StringEndsWithTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertStringEndsWith('suffix', 'foo');
        }
    }

.. code-block:: bash

    $ phpunit StringEndsWithTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 1 second, Memory: 5.00Mb

    There was 1 failure:

    1) StringEndsWithTest::testFailure
    Failed asserting that 'foo' ends with "suffix".

    /home/sb/StringEndsWithTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertStringEqualsFile:

assertStringEqualsFile()
########################

``assertStringEqualsFile(string $expectedFile, string $actualString[, string $message = ''])``

Reporta un error identificado por el ``$message`` si el archivo especificado en
``$expectedFile`` no tiene a ``$actualString`` como su contenido.

``assertStringNotEqualsFile()`` es el inverso de esta aserción y toma los mismos argumentos.

.. code-block:: php
    :caption: Uso del método assertStringEqualsFile()
    :name: appendixes.assertions.assertStringEqualsFile.example

    <?php
    use PHPUnit\Framework\TestCase;

    class StringEqualsFileTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertStringEqualsFile('/home/sb/expected', 'actual');
        }
    }

.. code-block:: bash

    $ phpunit StringEqualsFileTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.25Mb

    There was 1 failure:

    1) StringEqualsFileTest::testFailure
    Failed asserting that two strings are equal.
    --- Expected
    +++ Actual
    @@ @@
    -'expected
    -'
    +'actual'

    /home/sb/StringEqualsFileTest.php:6

    FAILURES!
    Tests: 1, Assertions: 2, Failures: 1.

.. _appendixes.assertions.assertStringStartsWith:

assertStringStartsWith()
########################

``assertStringStartsWith(string $prefix, string $string[, string $message = ''])``

Reporta un error identificado por el ``$message`` si el ``$string`` no comienza
con el ``$prefix``.

``assertStringStartsNotWith()`` es el inverso de esta aserción y toma los mismos argumentos.

.. code-block:: php
    :caption: Uso del método assertStringStartsWith()
    :name: appendixes.assertions.assertStringStartsWith.example

    <?php
    use PHPUnit\Framework\TestCase;

    class StringStartsWithTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertStringStartsWith('prefix', 'foo');
        }
    }

.. code-block:: bash

    $ phpunit StringStartsWithTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) StringStartsWithTest::testFailure
    Failed asserting that 'foo' starts with "prefix".

    /home/sb/StringStartsWithTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertThat:

assertThat()
############

Aserciones más complejas se pueden formular usando las clases ``PHPUnit\Framework\Constraint``.
Ellas se pueden evaluar usando el método ``assertThat()``. El
:numref:`appendixes.assertions.assertThat.example` muestra como las restricciones
``logicalNot()`` y ``equalTo()`` se pueden usar para expresar la aserción
``assertNotEquals()``.

``assertThat(mixed $value, PHPUnit\Framework\Constraint $constraint[, $message = ''])``

Reporta un error identificado por el ``$message`` si el ``$value`` no coincide
con el ``$constraint``.

.. code-block:: php
    :caption: Uso del método assertThat()
    :name: appendixes.assertions.assertThat.example

    <?php
    use PHPUnit\Framework\TestCase;

    class BiscuitTest extends TestCase
    {
        public function testEquals()
        {
            $theBiscuit = new Biscuit('Ginger');
            $myBiscuit  = new Biscuit('Ginger');

            $this->assertThat(
              $theBiscuit,
              $this->logicalNot(
                $this->equalTo($myBiscuit)
              )
            );
        }
    }

La :numref:`appendixes.assertions.assertThat.tables.constraints` muestra las
clases disponibles en ``PHPUnit\Framework\Constraint``.

.. rst-class:: table
.. list-table:: Constraints
    :name: appendixes.assertions.assertThat.tables.constraints
    :header-rows: 1

    * - Constraint
      - Meaning
    * - ``PHPUnit\Framework\Constraint\Attribute attribute(PHPUnit\Framework\Constraint $constraint, $attributeName)``
      - Constraint that applies another constraint to an attribute of a class or an object.
    * - ``PHPUnit\Framework\Constraint\IsAnything anything()``
      - Constraint that accepts any input value.
    * - ``PHPUnit\Framework\Constraint\ArrayHasKey arrayHasKey(mixed $key)``
      - Constraint that asserts that the array has a given key.
    * - ``PHPUnit\Framework\Constraint\TraversableContains contains(mixed $value)``
      - Constraint that asserts that the ``array`` or object that implements the ``Iterator`` interface contains a given value.
    * - ``PHPUnit\Framework\Constraint\TraversableContainsOnly containsOnly(string $type)``
      - Constraint that asserts that the ``array`` or object that implements the ``Iterator`` interface contains only values of a given type.
    * - ``PHPUnit\Framework\Constraint\TraversableContainsOnly containsOnlyInstancesOf(string $classname)``
      - Constraint that asserts that the ``array`` or object that implements the ``Iterator`` interface contains only instances of a given classname.
    * - ``PHPUnit\Framework\Constraint\IsEqual equalTo($value, $delta = 0, $maxDepth = 10)``
      - Constraint that checks if one value is equal to another.
    * - ``PHPUnit\Framework\Constraint\Attribute attributeEqualTo($attributeName, $value, $delta = 0, $maxDepth = 10)``
      - Constraint that checks if a value is equal to an attribute of a class or of an object.
    * - ``PHPUnit\Framework\Constraint\DirectoryExists directoryExists()``
      - Constraint that checks if the directory exists.
    * - ``PHPUnit\Framework\Constraint\FileExists fileExists()``
      - Constraint that checks if the file(name) exists.
    * - ``PHPUnit\Framework\Constraint\IsReadable isReadable()``
      - Constraint that checks if the file(name) is readable.
    * - ``PHPUnit\Framework\Constraint\IsWritable isWritable()``
      - Constraint that checks if the file(name) is writable.
    * - ``PHPUnit\Framework\Constraint\GreaterThan greaterThan(mixed $value)``
      - Constraint that asserts that the value is greater than a given value.
    * - ``PHPUnit\Framework\Constraint\Or greaterThanOrEqual(mixed $value)``
      - Constraint that asserts that the value is greater than or equal to a given value.
    * - ``PHPUnit\Framework\Constraint\ClassHasAttribute classHasAttribute(string $attributeName)``
      - Constraint that asserts that the class has a given attribute.
    * - ``PHPUnit\Framework\Constraint\ClassHasStaticAttribute classHasStaticAttribute(string $attributeName)``
      - Constraint that asserts that the class has a given static attribute.
    * - ``PHPUnit\Framework\Constraint\ObjectHasAttribute hasAttribute(string $attributeName)``
      - Constraint that asserts that the object has a given attribute.
    * - ``PHPUnit\Framework\Constraint\IsIdentical identicalTo(mixed $value)``
      - Constraint that asserts that one value is identical to another.
    * - ``PHPUnit\Framework\Constraint\IsFalse isFalse()``
      - Constraint that asserts that the value is ``false``.
    * - ``PHPUnit\Framework\Constraint\IsInstanceOf isInstanceOf(string $className)``
      - Constraint that asserts that the object is an instance of a given class.
    * - ``PHPUnit\Framework\Constraint\IsNull isNull()``
      - Constraint that asserts that the value is ``null``.
    * - ``PHPUnit\Framework\Constraint\IsTrue isTrue()``
      - Constraint that asserts that the value is ``true``.
    * - ``PHPUnit\Framework\Constraint\IsType isType(string $type)``
      - Constraint that asserts that the value is of a specified type.
    * - ``PHPUnit\Framework\Constraint\LessThan lessThan(mixed $value)``
      - Constraint that asserts that the value is smaller than a given value.
    * - ``PHPUnit\Framework\Constraint\Or lessThanOrEqual(mixed $value)``
      - Constraint that asserts that the value is smaller than or equal to a given value.
    * - ``logicalAnd()``
      - Logical AND.
    * - ``logicalNot(PHPUnit\Framework\Constraint $constraint)``
      - Logical NOT.
    * - ``logicalOr()``
      - Logical OR.
    * - ``logicalXor()``
      - Logical XOR.
    * - ``PHPUnit\Framework\Constraint\PCREMatch matchesRegularExpression(string $pattern)``
      - Constraint that asserts that the string matches a regular expression.
    * - ``PHPUnit\Framework\Constraint\StringContains stringContains(string $string, bool $case)``
      - Constraint that asserts that the string contains a given string.
    * - ``PHPUnit\Framework\Constraint\StringEndsWith stringEndsWith(string $suffix)``
      - Constraint that asserts that the string ends with a given suffix.
    * - ``PHPUnit\Framework\Constraint\StringStartsWith stringStartsWith(string $prefix)``
      - Constraint that asserts that the string starts with a given prefix.

.. _appendixes.assertions.assertTrue:

assertTrue()
############

``assertTrue(bool $condition[, string $message = ''])``

Reporta un error identificado por el ``$message`` si ``$condition`` es ``false``.

``assertNotTrue()`` es el inverso de esta aserción y toma los mismos argumentos.

.. code-block:: php
    :caption: Uso del método assertTrue()
    :name: appendixes.assertions.assertTrue.example

    <?php
    use PHPUnit\Framework\TestCase;

    class TrueTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertTrue(false);
        }
    }

.. code-block:: bash

    $ phpunit TrueTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) TrueTest::testFailure
    Failed asserting that false is true.

    /home/sb/TrueTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertXmlFileEqualsXmlFile:

assertXmlFileEqualsXmlFile()
############################

``assertXmlFileEqualsXmlFile(string $expectedFile, string $actualFile[, string $message = ''])``

Reporta un error identificado por el ``$message`` si the XML document in ``$actualFile``
no es igual al documento XML en ``$expectedFile``.

``assertXmlFileNotEqualsXmlFile()`` es el inverso de esta aserción y toma los mismos argumentos.

.. code-block:: php
    :caption: Uso del método assertXmlFileEqualsXmlFile()
    :name: appendixes.assertions.assertXmlFileEqualsXmlFile.example

    <?php
    use PHPUnit\Framework\TestCase;

    class XmlFileEqualsXmlFileTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertXmlFileEqualsXmlFile(
              '/home/sb/expected.xml', '/home/sb/actual.xml');
        }
    }

.. code-block:: bash

    $ phpunit XmlFileEqualsXmlFileTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.25Mb

    There was 1 failure:

    1) XmlFileEqualsXmlFileTest::testFailure
    Failed asserting that two DOM documents are equal.
    --- Expected
    +++ Actual
    @@ @@
     <?xml version="1.0"?>
     <foo>
    -  <bar/>
    +  <baz/>
     </foo>

    /home/sb/XmlFileEqualsXmlFileTest.php:7

    FAILURES!
    Tests: 1, Assertions: 3, Failures: 1.

.. _appendixes.assertions.assertXmlStringEqualsXmlFile:

assertXmlStringEqualsXmlFile()
##############################

``assertXmlStringEqualsXmlFile(string $expectedFile, string $actualXml[, string $message = ''])``

Reporta un error identificado por el ``$message`` si the XML document in ``$actualXml`` is not equal to the XML document in ``$expectedFile``.

``assertXmlStringNotEqualsXmlFile()`` es el inverso de esta aserción y toma los mismos argumentos.

.. code-block:: php
    :caption: Uso del método assertXmlStringEqualsXmlFile()
    :name: appendixes.assertions.assertXmlStringEqualsXmlFile.example

    <?php
    use PHPUnit\Framework\TestCase;

    class XmlStringEqualsXmlFileTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertXmlStringEqualsXmlFile(
              '/home/sb/expected.xml', '<foo><baz/></foo>');
        }
    }

.. code-block:: bash

    $ phpunit XmlStringEqualsXmlFileTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.25Mb

    There was 1 failure:

    1) XmlStringEqualsXmlFileTest::testFailure
    Failed asserting that two DOM documents are equal.
    --- Expected
    +++ Actual
    @@ @@
     <?xml version="1.0"?>
     <foo>
    -  <bar/>
    +  <baz/>
     </foo>

    /home/sb/XmlStringEqualsXmlFileTest.php:7

    FAILURES!
    Tests: 1, Assertions: 2, Failures: 1.

.. _appendixes.assertions.assertXmlStringEqualsXmlString:

assertXmlStringEqualsXmlString()
################################

``assertXmlStringEqualsXmlString(string $expectedXml, string $actualXml[, string $message = ''])``

Reporta un error identificado por el ``$message`` si the XML document in ``$actualXml`` is not equal to the XML document in ``$expectedXml``.

``assertXmlStringNotEqualsXmlString()`` es el inverso de esta aserción y toma los mismos argumentos.

.. code-block:: php
    :caption: Uso del método assertXmlStringEqualsXmlString()
    :name: appendixes.assertions.assertXmlStringEqualsXmlString.example

    <?php
    use PHPUnit\Framework\TestCase;

    class XmlStringEqualsXmlStringTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertXmlStringEqualsXmlString(
              '<foo><bar/></foo>', '<foo><baz/></foo>');
        }
    }

.. code-block:: bash

    $ phpunit XmlStringEqualsXmlStringTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) XmlStringEqualsXmlStringTest::testFailure
    Failed asserting that two DOM documents are equal.
    --- Expected
    +++ Actual
    @@ @@
     <?xml version="1.0"?>
     <foo>
    -  <bar/>
    +  <baz/>
     </foo>

    /home/sb/XmlStringEqualsXmlStringTest.php:7

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.
