

.. _test-doubles:

================
Dobles de Prueba
================

Gerard Meszaros introduce el concepto de Dobles de Prueba, en ingles *Test Doubles*,
de la siguiente manera en :ref:`Meszaros2007`:

    *Gerard Meszaros*:

    A veces es lisa y llanamente difícil probar el sistema bajo prueba (SUT)
    porque el sistema depende de otros componentes que no se pueden usar en el
    entorno de pruebas. Esto podría ser porque ellos no están disponibles,
    no regresarán los resultados necesarios para la prueba o porque la ejecución
    de esos componentes tendría efectos secundarios indeseables. En otros casos,
    nuestra estrategia de prueba necesita que nosotros tengamos más control
    o visibilidad del comportamiento interno del SUT.

    Cuando estamos escribiendo una prueba en que no podemos (o elegimos no) usar
    un componente real del que se depende (DOC), podemos reemplazarlo por una
    Doble Prueba.
    El Doble de Prueba no debe comportarse exactamente como el DOC real; él
    solamente debe proveer la misma API que la dependencia real para que así
    el SUT piense que es una dependencia real.

Los métodos ``createMock($type)`` y ``getMockBuilder($type)`` que provee PHPUnit
se pueden usar en una prueba para generar automáticamente un objeto que actúa
como un doble de pruebas del objeto original especificado, de tipo interfaz o
nombre de clase. Este objeto doble de prueba se puede usar en cada contexto donde
un objeto del tipo original se espera o necesita.

El método ``createMock($type)`` regresa un doble de pruebas del objeto del
tipo especificado (interfaz o clase). Por defecto, la creación de este doble de
prueba se realiza usando las mejores prácticas. Los métodos ``__construct()``
y ``__clone()`` de la clase original no se ejecutan y los argumentos pasados
a un método del doble de pruebas no se clonan. Si estas configuraciones
predeterminadas no son las que necesitamos podemos usar el método
``getMockBuilder($type)`` para personalizar la generación del doble de
pruebas usando una interfaz fluida.

Por defecto, todos los métodos de la clases original se reemplazan por una
implementación simulada que solo regresa ``null`` (sin llamar al método original).
Usando el método ``will($this->returnValue())``, por ejemplo, podemos configurar
esas implementaciones simuladas para que regresen un valor cuando sean llamadas.

.. admonition:: Limitaciones: métodos final, private y static

   Nótese que los métodos ``final``, ``private`` y ``static``
   no se pueden esbozar, *stubbed*, o simular *mocked*.
   La funcionalidad de dobles de prueba de PHPUnit ignora a estos métodos
   que mantienen su comportamiento original excepto los métodos ``static``
   que se reemplazan por el método lanzador de excepciones
   ``\PHPUnit\Framework\MockObject\BadMethodCallException``.

.. _test-doubles.stubs:

Esbozos
#######

La practica de remplazar un objeto con un doble de pruebas, que (opcionalmente)
regresa valores de retorno configurados, se llama *stubbing*, quizás bosquejar.
Podemos usar un *stub*, quizás bosquejo o esbozo, para «reemplazar un componente
real del que el SUT depende y así la prueba tiene un punto de control para las
entradas indirectas del SUT. Esto permite probar de manera forzada al SUT por
rutas que de otra manera no se ejecutarían».

El :numref:`test-doubles.stubs.examples.StubTest.php` muestra como esbozar
llamadas a métodos y configurar valores de retorno. Primero usamos el
método ``createMock()``, que provee la clase ``PHPUnit\Framework\TestCase``,
para configurar el esbozo del objeto que se verá como un objeto de la clase
``SomeClass`` (:numref:`test-doubles.stubs.examples.SomeClass.php`). Luego
usamos una `Interfaz Fluida <http://martinfowler.com/bliki/FluentInterface.html>`_
que PHPUnit provee para especificar el comportamiento del esbozo. En esencia,
esto significa que no necesitamos crear varios objetos temporales y después
unirlos. En su lugar, encadenamos las llamadas a los métodos como se muestra
en el ejemplo. Esto lleva a tener un código más legible y «fluido».

.. code-block:: php
    :caption: La clase que queremos esbozar
    :name: test-doubles.stubs.examples.SomeClass.php

    <?php
    class SomeClass
    {
        public function doSomething()
        {
            // Do something.
        }
    }
    ?>

.. code-block:: php
    :caption: Esbozo de una llamada a un método que regresa un valor asignado
    :name: test-doubles.stubs.examples.StubTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class StubTest extends TestCase
    {
        public function testStub()
        {
            // Create a stub for the SomeClass class.
            $stub = $this->createMock(SomeClass::class);

            // Configure the stub.
            $stub->method('doSomething')
                 ->willReturn('foo');

            // Calling $stub->doSomething() will now return
            // 'foo'.
            $this->assertSame('foo', $stub->doSomething());
        }
    }
    ?>

.. admonition:: Limitación: Métodos llamados «method»

   El ejemplo de arriba solo funciona cuando en la clase original no se
   declara un método llamado «método».

   Si la clase original declara un método llamado «method» entonces debemos usar
   ``$stub->expects($this->any())->method('doSomething')->willReturn('foo');``.

«Detrás de bastidores» PHPUnit automáticamente genera una nueva clase PHP que
implementa el comportamiento deseado cuando se usa el método ``createMock()``.

El :numref:`test-doubles.stubs.examples.StubTest2.php` muestra un ejemplo
de como usar la interfaz fluida del *Mock Builder* para configurar la creación
de un doble de pruebas. La configuración de este doble de pruebas usa las
mismas buenas practicas que por defecto usa el método ``createMock()``.

.. code-block:: php
    :caption: La *Mock Builder API* se puede usar para configurar la generación del doble de pruebas de clase
    :name: test-doubles.stubs.examples.StubTest2.php

    <?php
    use PHPUnit\Framework\TestCase;

    class StubTest extends TestCase
    {
        public function testStub()
        {
            // Create a stub for the SomeClass class.
            $stub = $this->getMockBuilder(SomeClass::class)
                         ->disableOriginalConstructor()
                         ->disableOriginalClone()
                         ->disableArgumentCloning()
                         ->disallowMockingUnknownTypes()
                         ->getMock();

            // Configure the stub.
            $stub->method('doSomething')
                 ->willReturn('foo');

            // Calling $stub->doSomething() will now return
            // 'foo'.
            $this->assertSame('foo', $stub->doSomething());
        }
    }
    ?>

Hasta ahora con los ejemplos anteriores regresamos valores simples usando el
método ``willReturn($value)``. Esa sintaxis corta es equivalente a
``will($this->returnValue($value))``. Podemos usar variaciones de esta sintaxis
más larga y obtener un comportamiento más complejo para el esbozo.

A veces queremos regresar como resultado de la llamada al método esbozado uno
de los argumentos del método llamado (sin cambios).
El :numref:`test-doubles.stubs.examples.StubTest3.php` muestra como podemos
hacer esto usando el método ``returnArgument()`` en lugar de ``returnValue()``.

.. code-block:: php
    :caption: Llamada a un método esbozado que regresa uno de sus argumentos
    :name: test-doubles.stubs.examples.StubTest3.php

    <?php
    use PHPUnit\Framework\TestCase;

    class StubTest extends TestCase
    {
        public function testReturnArgumentStub()
        {
            // Create a stub for the SomeClass class.
            $stub = $this->createMock(SomeClass::class);

            // Configure the stub.
            $stub->method('doSomething')
                 ->will($this->returnArgument(0));

            // $stub->doSomething('foo') returns 'foo'
            $this->assertSame('foo', $stub->doSomething('foo'));

            // $stub->doSomething('bar') returns 'bar'
            $this->assertSame('bar', $stub->doSomething('bar'));
        }
    }
    ?>

Cuando se prueba una interfaz fluida, a veces es útil tener un método esbozado
que regresa una referencia al objeto esbozado. El
:numref:`test-doubles.stubs.examples.StubTest4.php` muestra como alcanzar este
objetivo con el método ``returnSelf()``.

.. code-block:: php
    :caption: Esbozar la llamada a un método que regresa un referencia al objeto esbozado
    :name: test-doubles.stubs.examples.StubTest4.php

    <?php
    use PHPUnit\Framework\TestCase;

    class StubTest extends TestCase
    {
        public function testReturnSelf()
        {
            // Create a stub for the SomeClass class.
            $stub = $this->createMock(SomeClass::class);

            // Configure the stub.
            $stub->method('doSomething')
                 ->will($this->returnSelf());

            // $stub->doSomething() returns $stub
            $this->assertSame($stub, $stub->doSomething());
        }
    }
    ?>

Algunos de los métodos esbozados deberían regresar diferentes valores
dependiendo de una lista predefinida de argumentos. Podemos usar el método
``returnValueMap()`` para crear un mapa que asocia argumentos con valores
de retorno. Veamos el ejemplo :numref:`test-doubles.stubs.examples.StubTest5.php`
para un ejemplo.

.. code-block:: php
    :caption: Esbozar la llamada a un método para regresar un valor desde un mapa
    :name: test-doubles.stubs.examples.StubTest5.php

    <?php
    use PHPUnit\Framework\TestCase;

    class StubTest extends TestCase
    {
        public function testReturnValueMapStub()
        {
            // Create a stub for the SomeClass class.
            $stub = $this->createMock(SomeClass::class);

            // Create a map of arguments to return values.
            $map = [
                ['a', 'b', 'c', 'd'],
                ['e', 'f', 'g', 'h']
            ];

            // Configure the stub.
            $stub->method('doSomething')
                 ->will($this->returnValueMap($map));

            // $stub->doSomething() returns different values depending on
            // the provided arguments.
            $this->assertSame('d', $stub->doSomething('a', 'b', 'c'));
            $this->assertSame('h', $stub->doSomething('e', 'f', 'g'));
        }
    }
    ?>

Cuando la llamada a un esbozo de método debe regresar un valor calculado en lugar
de un valor fijado (ver ``returnValue()``) o un argumento sin cambios
(ver ``returnArgument()``), podemos usar el método ``returnCallback()``
para tener un esbozo de método que regresa el resultado de una función o método
de retro llamada. Ver el :numref:`test-doubles.stubs.examples.StubTest6.php`:

.. code-block:: php
    :caption: Esbozar la llamada a un método que regresar un valor desde una retro llamada
    :name: test-doubles.stubs.examples.StubTest6.php

    <?php
    use PHPUnit\Framework\TestCase;

    class StubTest extends TestCase
    {
        public function testReturnCallbackStub()
        {
            // Create a stub for the SomeClass class.
            $stub = $this->createMock(SomeClass::class);

            // Configure the stub.
            $stub->method('doSomething')
                 ->will($this->returnCallback('str_rot13'));

            // $stub->doSomething($argument) returns str_rot13($argument)
            $this->assertSame('fbzrguvat', $stub->doSomething('something'));
        }
    }
    ?>

Una alternativa simple para configurar un método de retro llamada puede ser
especificando una lista de valores deseables de retorno. Podemos hacer esto
con el método ``onConsecutiveCalls()``.
Ver el :numref:`test-doubles.stubs.examples.StubTest7.php`.

.. code-block:: php
    :caption: Esbozar la llamada a un método que regresar una lista de valores en el orden especificado
    :name: test-doubles.stubs.examples.StubTest7.php

    <?php
    use PHPUnit\Framework\TestCase;

    class StubTest extends TestCase
    {
        public function testOnConsecutiveCallsStub()
        {
            // Create a stub for the SomeClass class.
            $stub = $this->createMock(SomeClass::class);

            // Configure the stub.
            $stub->method('doSomething')
                 ->will($this->onConsecutiveCalls(2, 3, 5, 7));

            // $stub->doSomething() returns a different value each time
            $this->assertSame(2, $stub->doSomething());
            $this->assertSame(3, $stub->doSomething());
            $this->assertSame(5, $stub->doSomething());
        }
    }
    ?>

En lugar de regresar un valor, un método esbozado puede además lanzar una
excepción. El :numref:`test-doubles.stubs.examples.StubTest8.php` el método
muestra como usar el método ``throwException()`` para hacer esto.

.. code-block:: php
    :caption: Esbozar la llama a un método para lanzar un excepción
    :name: test-doubles.stubs.examples.StubTest8.php

    <?php
    use PHPUnit\Framework\TestCase;

    class StubTest extends TestCase
    {
        public function testThrowExceptionStub()
        {
            // Create a stub for the SomeClass class.
            $stub = $this->createMock(SomeClass::class);

            // Configure the stub.
            $stub->method('doSomething')
                 ->will($this->throwException(new Exception));

            // $stub->doSomething() throws Exception
            $stub->doSomething();
        }
    }
    ?>

Alternativamente, nosotros mismos podemos escribir un esbozo y mejorar su
diseño a lo largo del camino. Los recursos usados ampliamente se acceden a través
de una sola fachada, *single façade*, por lo que podemos fácilmente reemplazar
el recurso con un esbozo.
Por ejemplo, en lugar de tener llamadas directamente a la base de datos
esparcidas a lo largo del código, podemos tener un solo objeto ``Database``
que implementación de la interfaz ``IDatabase``. Luego, podemos crear un esbozo
de la implementación de ``IDatabase`` y usarla para nuestras pruebas.
Incluso podemos crear una opción para ejecutar las pruebas con el esbozo de
base de datos o una base de datos real, así podemos usar nuestras pruebas
tanto para pruebas locales durante el desarrollo como para la integración de
las pruebas con una base de datos real.

Las funcionalidades que se necesitan esbozar tienden a ser agrupadas en el
mismo objeto con lo que se mejora su cohesion. Al presenta la funcionalidad
en una sola y coherente interfaz podemos reducir el acoplamiento con el resto
del sistema.

.. _test-doubles.mock-objects:

Objetos Falsos
##############

La práctica de reemplazar un objeto con un doble de pruebas que verifica las
expectativas; por ejemplo, al aseverar que un método se ha llamado; tiene el
nombre de *mocking*, quizás simulación o falsificación.

Podemos usar un *objeto falso* «como un punto de observación que se usa para
verificar las salidas indirectas del SUT cuando se está *ejercitando*.
Generalmente el objeto falso incluye además las funcionalidades de la prueba
esbozada puesto que él debe retornar valores al SUT, siempre que
el sistema no ha fallado las pruebas, pero el énfasis está en la verificación
de las salidas indirectas. Por eso, un objeto falso es mucho más que un esbozo
de prueba más algunas aserciones; este se usa de una manera fundamentalmente
diferente» (Gerard Meszaros).

.. admonition:: Limitación: Verificación automática de las expectativas

   Solo los objetos falsos generados dentro del ámbito de una prueba serán
   verificados automáticamente por PHPUnit. Los objetos falsos generados por
   los proveedores de datos, por ejemplo, o inyectados dentro de la prueba
   usando la anotación ``@depends`` no serán verificados automáticamente por
   PHPUnit.

Aquí tenemos un ejemplo: supongamos que queremos probar si el método correcto,
``update()`` en nuestro ejemplo, es llamado por un objeto que observa a otro
objeto. El :numref:`test-doubles.mock-objects.examples.SUT.php` muestra el
código para las clases ``Subject`` y ``Observer`` que son parte del sistema
que se está probando (SUT).

.. code-block:: php
    :caption: Las clases *Subject* y *Observer* que son parte del sistema sometido a prueba (SUT)
    :name: test-doubles.mock-objects.examples.SUT.php

    <?php
    use PHPUnit\Framework\TestCase;

    class Subject
    {
        protected $observers = [];
        protected $name;

        public function __construct($name)
        {
            $this->name = $name;
        }

        public function getName()
        {
            return $this->name;
        }

        public function attach(Observer $observer)
        {
            $this->observers[] = $observer;
        }

        public function doSomething()
        {
            // Do something.
            // ...

            // Notify observers that we did something.
            $this->notify('something');
        }

        public function doSomethingBad()
        {
            foreach ($this->observers as $observer) {
                $observer->reportError(42, 'Something bad happened', $this);
            }
        }

        protected function notify($argument)
        {
            foreach ($this->observers as $observer) {
                $observer->update($argument);
            }
        }

        // Other methods.
    }

    class Observer
    {
        public function update($argument)
        {
            // Do something.
        }

        public function reportError($errorCode, $errorMessage, Subject $subject)
        {
            // Do something
        }

        // Other methods.
    }
    ?>

El :numref:`test-doubles.mock-objects.examples.SubjectTest.php` muestra como
usar un objeto falso para probar la interacción entre los objetos ``Subject``
y ``Observer``.

Primero usamos el método ``getMockBuilder()`` que es provisto por la clase
``PHPUnit\Framework\TestCase`` para configurar un objeto falso para el
``Observer``. Como damos un arreglo como segundo parámetro (opcional) para
el método ``getMock()``, solo el método ``update()`` de la clase ``Observer``
es reemplazada por la implementación falsa.

Como estamos interesados en revisar si se llama a un método y con que
argumentos, introducimos los métodos ``expects()`` y ``with()`` para especificar
como esta interacción debería darse.

.. code-block:: php
    :caption: Probar si un método es llamado y con que argumentos
    :name: test-doubles.mock-objects.examples.SubjectTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class SubjectTest extends TestCase
    {
        public function testObserversAreUpdated()
        {
            // Create a mock for the Observer class,
            // only mock the update() method.
            $observer = $this->getMockBuilder(Observer::class)
                             ->setMethods(['update'])
                             ->getMock();

            // Set up the expectation for the update() method
            // to be called only once and with the string 'something'
            // as its parameter.
            $observer->expects($this->once())
                     ->method('update')
                     ->with($this->equalTo('something'));

            // Create a Subject object and attach the mocked
            // Observer object to it.
            $subject = new Subject('My subject');
            $subject->attach($observer);

            // Call the doSomething() method on the $subject object
            // which we expect to call the mocked Observer object's
            // update() method with the string 'something'.
            $subject->doSomething();
        }
    }
    ?>

El método ``with()`` puede tomar cualquier número de argumentos mientras que
correspondan con el número de argumentos que tienen el método que está siendo
simulado (falsificado). Podemos especificar restricciones más avanzadas que una
simple comparación en los argumentos del método.

.. code-block:: php
    :caption: Probar que un método regresa con un número de argumentos restringidos de diferentes maneras
    :name: test-doubles.mock-objects.examples.SubjectTest2.php

    <?php
    use PHPUnit\Framework\TestCase;

    class SubjectTest extends TestCase
    {
        public function testErrorReported()
        {
            // Create a mock for the Observer class, mocking the
            // reportError() method
            $observer = $this->getMockBuilder(Observer::class)
                             ->setMethods(['reportError'])
                             ->getMock();

            $observer->expects($this->once())
                     ->method('reportError')
                     ->with(
                           $this->greaterThan(0),
                           $this->stringContains('Something'),
                           $this->anything()
                       );

            $subject = new Subject('My subject');
            $subject->attach($observer);

            // The doSomethingBad() method should report an error to the observer
            // via the reportError() method
            $subject->doSomethingBad();
        }
    }
    ?>

El método ``withConsecutive()`` puede tomar cualquier número de arreglos como
argumentos dependiendo de las llamadas que deseamos probar. Cada arreglo
es una lista de restricciones correspondientes a los argumentos del método
que se está simulando, como en ``with()``.

.. code-block:: php
    :caption: Prueba que un método fue llamado dos veces con argumentos específicos.
    :name: test-doubles.mock-objects.examples.with-consecutive.php

    <?php
    use PHPUnit\Framework\TestCase;

    class FooTest extends TestCase
    {
        public function testFunctionCalledTwoTimesWithSpecificArguments()
        {
            $mock = $this->getMockBuilder(stdClass::class)
                         ->setMethods(['set'])
                         ->getMock();

            $mock->expects($this->exactly(2))
                 ->method('set')
                 ->withConsecutive(
                     [$this->equalTo('foo'), $this->greaterThan(0)],
                     [$this->equalTo('bar'), $this->greaterThan(0)]
                 );

            $mock->set('foo', 21);
            $mock->set('bar', 48);
        }
    }
    ?>

La restricción ``callback()`` se puede usar para la verificación de argumentos
más complejos. Esta restricción recibe una retro llamada de PHP como único
argumento. La retro llamada de PHP recibirá el argumento que será verificado
como único argumento y debería retornar ``true`` si el argumento pasa la
verificación y de lo contrario ``false``.

.. code-block:: php
    :caption: Verificación de argumentos más complejos
    :name: test-doubles.mock-objects.examples.SubjectTest3.php

    <?php
    use PHPUnit\Framework\TestCase;

    class SubjectTest extends TestCase
    {
        public function testErrorReported()
        {
            // Create a mock for the Observer class, mocking the
            // reportError() method
            $observer = $this->getMockBuilder(Observer::class)
                             ->setMethods(['reportError'])
                             ->getMock();

            $observer->expects($this->once())
                     ->method('reportError')
                     ->with($this->greaterThan(0),
                            $this->stringContains('Something'),
                            $this->callback(function($subject){
                              return is_callable([$subject, 'getName']) &&
                                     $subject->getName() == 'My subject';
                            }));

            $subject = new Subject('My subject');
            $subject->attach($observer);

            // The doSomethingBad() method should report an error to the observer
            // via the reportError() method
            $subject->doSomethingBad();
        }
    }
    ?>

.. code-block:: php
    :caption: Prueba que el método fue llamado una vez y con un objeto idéntico al que fue llamado
    :name: test-doubles.mock-objects.examples.clone-object-parameters-usecase.php

    <?php
    use PHPUnit\Framework\TestCase;

    class FooTest extends TestCase
    {
        public function testIdenticalObjectPassed()
        {
            $expectedObject = new stdClass;

            $mock = $this->getMockBuilder(stdClass::class)
                         ->setMethods(['foo'])
                         ->getMock();

            $mock->expects($this->once())
                 ->method('foo')
                 ->with($this->identicalTo($expectedObject));

            $mock->foo($expectedObject);
        }
    }
    ?>

.. code-block:: php
    :caption: Crear un objeto falso con la clonación de parámetros habilitada
    :name: test-doubles.mock-objects.examples.enable-clone-object-parameters.php

    <?php
    use PHPUnit\Framework\TestCase;

    class FooTest extends TestCase
    {
        public function testIdenticalObjectPassed()
        {
            $cloneArguments = true;

            $mock = $this->getMockBuilder(stdClass::class)
                         ->enableArgumentCloning()
                         ->getMock();

            // now your mock clones parameters so the identicalTo constraint
            // will fail.
        }
    }
    ?>

Las restricciones, conocidas en ingles como
:ref:`appendixes.assertions.assertThat.tables.constraints`, muestran las
limitaciones que se pueden aplicar a los argumentos del método y en la
:numref:`test-doubles.mock-objects.tables.matchers` se muestran las comparaciones
que están disponibles para especificar el número de invocaciones.

.. rst-class:: table
.. list-table:: Comparadores
    :name: test-doubles.mock-objects.tables.matchers
    :header-rows: 1

    * - Comparador
      - Significado
    * - ``PHPUnit\Framework\MockObject\Matcher\AnyInvokedCount any()``
      - Regresa la coincidencia que resulta cuando el método que se evalúa se
        ejecuta cero o más veces.
    * - ``PHPUnit\Framework\MockObject\Matcher\InvokedCount never()``
      - Regresa la coincidencia que resulta cuando el método que se evalúa
        nunca se ejecuta.
    * - ``PHPUnit\Framework\MockObject\Matcher\InvokedAtLeastOnce atLeastOnce()``
      - Regresa la coincidencia que resulta cuando el método que se evalúa se
        ejecuta al menos una vez.
    * - ``PHPUnit\Framework\MockObject\Matcher\InvokedCount once()``
      - Regresa la coincidencia que resulta cuando el método que se evalúa se
        ejecuta exactamente una vez.
    * - ``PHPUnit\Framework\MockObject\Matcher\InvokedCount exactly(int $count)``
      - Regresa la coincidencia que resulta cuando el método que se evalúa se
        ejecuta exactamente ``$count`` veces.
    * - ``PHPUnit\Framework\MockObject\Matcher\InvokedAtIndex at(int $index)``
      - Regresa la coincidencia que resulta cuando el método que se evalúa se
        invoca dada una variable ``$index``.

.. admonition:: Nota

   El parámetro ``$index`` para el comparador ``at()`` se refiere al índice,
   comenzando en cero, de *todas las invocaciones de métodos* dado un objeto
   simulado. Tenga cuidado cuando ejecute este comparador pues nos puede
   llevar a pruebas frágiles cuando ellas están muy atadas a detalles específicos
   de la implementación.

Como mencionamos al comienzo, cuando los valores predeterminados usados por el
método ``createMock()`` para generar los dobles de pruebas no satisfacen
nuestras necesidades podemos usar el método ``getMockBuilder($type)``
para personalizar la generación de los dobles de prueba usando
una interfaz fluida. Aquí hay una lista con los métodos que provee el
*Mock Builder*:

-

  Se puede llamar al método ``setMethods(array $methods)`` sobre el objeto
  *Mock Builder* para especificar los métodos que serán reemplazados con
  un doble de prueba configurable. El comportamiento de los otros métodos no
  se carga. Si llamamos al método ``setMethods(null)`` ningún método será
  reemplazado.

-

  Se puede llamar al método ``setMethodsExcept(array $methods)`` sobre el
  objeto *Mock Builder* para especificar los métodos que no serán reemplazados
  con un doble de prueba configurable mientras que se reemplazan todos los
  otros métodos. Este método trabaja de forma inversa a ``setMethods()``.

-

  Se puede llamar al método ``setConstructorArgs(array $args)`` para proveer
  un arreglo de parámetros que se pasa al constructor original de la clase
  (que por defecto no se reemplaza con una implementación falsa).

-

  Se puede llamar al método ``setMockClassName($name)`` para especificar un
  nombre de clase para la clase de dobles de prueba generada.

  ``setMockClassName($name)`` can be used to specify a class name for the generated test double class.

-

  Se puede usar el método ``disableOriginalConstructor()`` para inhabilitar
  la llamada al constructor de la clase original.

-

  El método ``disableOriginalClone()`` se puede usar para inhabilitar la llamada
  al constructor clone de la clase original.

-

  El método ``disableAutoload()`` se puede usar para inhabilitar el
  ``__autoload()`` durante la generación de la clase para el doble de pruebas.

.. _test-doubles.prophecy:

Profecía
########

`Prophecy <https://github.com/phpspec/prophecy>`_ es un «extremadamente dogmático
pero muy poderoso y flexible framework de simulación de objetos PHP. Aunque
inicialmente fue creado para satisfacer las necesidades de phpspec2 es lo
suficientemente flexible para usarse dentro de cualquier framework de pruebas
con un mínimo esfuerzo».

PHPUnit tiene soporte incluido para usar *Prophecy* y crear dobles de prueba.
El :numref:`test-doubles.prophecy.examples.SubjectTest.php` muestra como
la misma prueba del ejemplo
:numref:`test-doubles.mock-objects.examples.SubjectTest.php` se puede expresar
usando la filosofía de *Prophecy* de profecías y revelaciones:

.. code-block:: php
    :caption: Probar que un método es llamado una vez y con un argumento específico
    :name: test-doubles.prophecy.examples.SubjectTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class SubjectTest extends TestCase
    {
        public function testObserversAreUpdated()
        {
            $subject = new Subject('My subject');

            // Create a prophecy for the Observer class.
            $observer = $this->prophesize(Observer::class);

            // Set up the expectation for the update() method
            // to be called only once and with the string 'something'
            // as its parameter.
            $observer->update('something')->shouldBeCalled();

            // Reveal the prophecy and attach the mock object
            // to the Subject.
            $subject->attach($observer->reveal());

            // Call the doSomething() method on the $subject object
            // which we expect to call the mocked Observer object's
            // update() method with the string 'something'.
            $subject->doSomething();
        }
    }
    ?>

Es necesario revisar la `documentación <https://github.com/phpspec/prophecy#how-to-use-it>`_
de *Prophecy* para mayores detalles de como crear, configurar y usar esbozos,
espías y simulaciones con este framework alternativo para dobles de pruebas.

.. _test-doubles.mocking-traits-and-abstract-classes:

Simular *Traits* y Clases Abstractas
####################################

El método ``getMockForTrait()`` regresa un objeto falso que usa un *trait*
específico. Todos los métodos abstractos del *trait* dado se simulan. Esto
permite probar métodos concretos de un *trait*.

.. code-block:: php
    :caption: Probar los métodos concretos de un *trait*
    :name: test-doubles.mock-objects.examples.TraitClassTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    trait AbstractTrait
    {
        public function concreteMethod()
        {
            return $this->abstractMethod();
        }

        public abstract function abstractMethod();
    }

    class TraitClassTest extends TestCase
    {
        public function testConcreteMethod()
        {
            $mock = $this->getMockForTrait(AbstractTrait::class);

            $mock->expects($this->any())
                 ->method('abstractMethod')
                 ->will($this->returnValue(true));

            $this->assertTrue($mock->concreteMethod());
        }
    }
    ?>

El método ``getMockForAbstractClass()`` regresa un objeto simulado para una
clase abstracta. Todos los métodos de una clase abstracta se simulan.
Esto permite probar los métodos concretos de una clase abstracta.

.. code-block:: php
    :caption: Probar los métodos concretos de una clase abstracta
    :name: test-doubles.mock-objects.examples.AbstractClassTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    abstract class AbstractClass
    {
        public function concreteMethod()
        {
            return $this->abstractMethod();
        }

        public abstract function abstractMethod();
    }

    class AbstractClassTest extends TestCase
    {
        public function testConcreteMethod()
        {
            $stub = $this->getMockForAbstractClass(AbstractClass::class);

            $stub->expects($this->any())
                 ->method('abstractMethod')
                 ->will($this->returnValue(true));

            $this->assertTrue($stub->concreteMethod());
        }
    }
    ?>

.. _test-doubles.stubbing-and-mocking-web-services:

Esbozar y Simular Servicios Web
###############################

Cuando nuestra aplicación interactúa con servicios web quisiéramos probarlos
sin interactuar realmente con el servicio web. Para hacer el esbozo o
la simulación de un servicio web, se puede usar el método ``getMockFromWsdl()``
exactamente como ``getMock()`` (ver arriba). La única diferencia es que
``getMockFromWsdl()`` regresa un esbozo o simulación basado en una descripción
de servicio web WSDL y ``getMock()`` regresa un esbozo o simulación basado
en una clase o interfaz PHP.

El :numref:`test-doubles.stubbing-and-mocking-web-services.examples.GoogleTest.php`
muestra como ``getMockFromWsdl()`` se puede usar para esbozar, por ejemplo,
el servicio web descrito en :file:`GoogleSearch.wsdl`.

.. code-block:: php
    :caption: Esbozar un servicio web
    :name: test-doubles.stubbing-and-mocking-web-services.examples.GoogleTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class GoogleTest extends TestCase
    {
        public function testSearch()
        {
            $googleSearch = $this->getMockFromWsdl(
              'GoogleSearch.wsdl', 'GoogleSearch'
            );

            $directoryCategory = new stdClass;
            $directoryCategory->fullViewableName = '';
            $directoryCategory->specialEncoding = '';

            $element = new stdClass;
            $element->summary = '';
            $element->URL = 'https://phpunit.de/';
            $element->snippet = '...';
            $element->title = '<b>PHPUnit</b>';
            $element->cachedSize = '11k';
            $element->relatedInformationPresent = true;
            $element->hostName = 'phpunit.de';
            $element->directoryCategory = $directoryCategory;
            $element->directoryTitle = '';

            $result = new stdClass;
            $result->documentFiltering = false;
            $result->searchComments = '';
            $result->estimatedTotalResultsCount = 3.9000;
            $result->estimateIsExact = false;
            $result->resultElements = [$element];
            $result->searchQuery = 'PHPUnit';
            $result->startIndex = 1;
            $result->endIndex = 1;
            $result->searchTips = '';
            $result->directoryCategories = [];
            $result->searchTime = 0.248822;

            $googleSearch->expects($this->any())
                         ->method('doGoogleSearch')
                         ->will($this->returnValue($result));

            /**
             * $googleSearch->doGoogleSearch() will now return a stubbed result and
             * the web service's doGoogleSearch() method will not be invoked.
             */
            $this->assertEquals(
              $result,
              $googleSearch->doGoogleSearch(
                '00000000000000000000000000000000',
                'PHPUnit',
                0,
                1,
                false,
                '',
                false,
                '',
                '',
                ''
              )
            );
        }
    }
    ?>

.. _test-doubles.mocking-the-filesystem:

Simular el Sistema de Archivos
##############################

`vfsStream <https://github.com/mikey179/vfsStream>`_
es un `envoltorio para flujos <http://www.php.net/streams>`_, *stream wrapper*,
para un `sistema de archivos virtual <http://en.wikipedia.org/wiki/Virtual_file_system>`_
que puede ser útil en pruebas unitarias para simular un sistema de archivos
real.

Si usamos `Composer <https://getcomposer.org/>`_ como administración de
dependencias en nuestro proyecto, simplemente agregamos el paquete
``mikey179/vfsStream`` como dependencia en nuestro archivo ``composer.json``
del proyecto. Abajo hay un ejemplo de un archivo ``composer.json`` simplificado
que define las dependencias: PHPUnit 4.6 y vfsStream, en *tiempo de desarrollo*.

.. code-block:: php

    {
        "require-dev": {
            "phpunit/phpunit": "~4.6",
            "mikey179/vfsStream": "~1"
        }
    }

El :numref:`test-doubles.mocking-the-filesystem.examples.Example.php`
muestra una clase que interactúa con el sistema de archivos.

.. code-block:: php
    :caption: Una clase que interactúa con el sistema de archivos
    :name: test-doubles.mocking-the-filesystem.examples.Example.php

    <?php
    use PHPUnit\Framework\TestCase;

    class Example
    {
        protected $id;
        protected $directory;

        public function __construct($id)
        {
            $this->id = $id;
        }

        public function setDirectory($directory)
        {
            $this->directory = $directory . DIRECTORY_SEPARATOR . $this->id;

            if (!file_exists($this->directory)) {
                mkdir($this->directory, 0700, true);
            }
        }
    }?>

Sin un sistema de archivos virtual como vfsStream no podemos probar el método
``setDirectory()`` aislado de influencias externas
(ver :numref:`test-doubles.mocking-the-filesystem.examples.ExampleTest.php`).

.. code-block:: php
    :caption: Probar una clase que interactúa con el sistema de archivos
    :name: test-doubles.mocking-the-filesystem.examples.ExampleTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class ExampleTest extends TestCase
    {
        protected function setUp()
        {
            if (file_exists(dirname(__FILE__) . '/id')) {
                rmdir(dirname(__FILE__) . '/id');
            }
        }

        public function testDirectoryIsCreated()
        {
            $example = new Example('id');
            $this->assertFalse(file_exists(dirname(__FILE__) . '/id'));

            $example->setDirectory(dirname(__FILE__));
            $this->assertTrue(file_exists(dirname(__FILE__) . '/id'));
        }

        protected function tearDown()
        {
            if (file_exists(dirname(__FILE__) . '/id')) {
                rmdir(dirname(__FILE__) . '/id');
            }
        }
    }
    ?>

La estrategia de arriba tiene varias desventajas:

-

  Como con cualquier recurso externo, podría haber problemas de intermitencia
  con el sistema de archivos. Esto hace frágiles a las pruebas que
  interactuaran con el sistema de archivos.

-

  En los métodos ``setUp()`` y ``tearDown()`` debemos asegurarnos que la carpeta
  no existe ni antes ni después de la prueba.

-

  Cuando la ejecución de la prueba termina antes de que el método ``tearDown()``
  es invocado la carpeta permanecerá en el sistema de archivos.

El :numref:`test-doubles.mocking-the-filesystem.examples.ExampleTest2.php`
muestra como vfsStream se puede usar para simular el sistema de archivos en
una prueba para una clase que interactúa con el sistema de archivos.

.. code-block:: php
    :caption: Simular el sistema de archivos para una prueba que interactúa con el sistema de archivos
    :name: test-doubles.mocking-the-filesystem.examples.ExampleTest2.php

    <?php
    use PHPUnit\Framework\TestCase;

    class ExampleTest extends TestCase
    {
        public function setUp()
        {
            vfsStreamWrapper::register();
            vfsStreamWrapper::setRoot(new vfsStreamDirectory('exampleDir'));
        }

        public function testDirectoryIsCreated()
        {
            $example = new Example('id');
            $this->assertFalse(vfsStreamWrapper::getRoot()->hasChild('id'));

            $example->setDirectory(vfsStream::url('exampleDir'));
            $this->assertTrue(vfsStreamWrapper::getRoot()->hasChild('id'));
        }
    }
    ?>

Esto tiene algunas ventajas:

-

  La prueba misma es más concisa.

-

  vfsStream otorga al desarrollador control total sobre la configuración del
  sistema de archivos para el código que se prueba.

-

  Como las operaciones sobre el sistema de archivos no se ejecutan sobre un
  sistema de archivos real, las operaciones de limpieza que se colocan en el
  método ``tearDown()`` no son necesarias.
