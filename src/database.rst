

.. _database:

=====================
Probar Bases de Datos
=====================

Muchos ejemplos, para principiantes y usuarios intermedios, de pruebas unitarias
en cualquier lenguaje de programación sugieren que es absolutamente fácil
probar la lógica de la aplicación con pruebas simples. Pero para aplicaciones
centradas en base de datos esto está muy lejos de la realidad. Basta comenzar
a usar WordPress, TYPO3 o Symfony con Doctrine o Propel, por ejemplo, para
experimentar rápidamente considerables problemas con PHPUnit
porque la base de datos está estrechamente vinculada con estas bibliotecas.


.. admonition:: Nota

   Debemos asegurarnos de tener instalada la extensión de PHP ``pdo`` y la
   extensión específica para la base de datos tal como ``pdo_mysql``. De lo
   contrario los ejemplos que se muestran abajo no van a funcionar.

Probablemente conoces el escenario, por tu trabajo diario y por tus proyectos,
en el que queremos colocar en acción nuestras habilidades (nuevas o no) con
PHPUnit y quedamos atrapados con uno de los siguientes problemas:

#.

   El método que queremos probar ejecuta una operación JOIN muy grande y
   usa los datos para calcular algunos resultados importantes.

#.

   Nuestra lógica de negocio ejecuta una mezcla de sentencias SELECT,
   INSERT, UPDATE y DELETE

#.

   Necesitamos asignar datos de prueba a (muy probablemente) más de dos tablas
   con el objeto de tener datos iniciales razonables para los métodos que queremos
   probar.

La extensión DbUnit simplifica considerablemente la configuración de la base
de datos para las pruebas y permite verificar el contenido de una base de datos
después de ejecutar una serie de operaciones.

.. _database.supported-vendors-for-database-testing:

Proveedores Soportados para las Pruebas de la Base de Datos
###########################################################

Actualmente DbUnit soporta MySQL, PostgreSQL, Oracle y SQLite. Si se integra
`Zend Framework <http://framework.zend.com>`_ o
`Doctrine 2 <http://www.doctrine-project.org>`_
DbUnit puede acceder a otros sistemas de base de datos como IBM DB2 o
Microsoft SQL Server.

.. _database.difficulties-in-database-testing:

Dificultades al Probar Bases de Datos
#####################################

Existe una buena razón para que todos los ejemplos de pruebas unitarias
no incluyan interacciones con la base de datos: esos tipos de pruebas son
tanto complejas de configurar como de mantener. Mientras se prueba contra
la base de datos necesitamos tener cuidado con las siguientes variables:

-

  El esquema y las tablas de la base de datos.

-

  Insertar las filas necesarias para las pruebas en esas tablas.

-

  Verificar el estado de la base de datos después de ejecutar las pruebas.

-

  Limpiar la base de datos para cada nueva prueba.

Como muchas APIs de base de datos, como PDO, MySQLi o ICI8, son incomodas
de usar y de escritura verbosa, hacer estos pasos manualmente es
definitivamente una pesadilla.

Probar el código debe ser lo más corto y preciso posible por varias razones:

-

  No queremos modificar una considerable cantidad de código de prueba
  por pequeños cambios en el código de producción.

-

  Queremos ser capaces de leer y entender el código de prueba fácilmente,
  incluso meses después de escribirlo.

Además, no debemos olvidar que la base de datos es esencialmente una variable
global de entrada para nuestro código. Dos pruebas de nuestro conjunto de pruebas
se pueden ejecutar contra la misma base de datos, posiblemente usando los datos
varias veces. Fallas en la primera prueba puede fácilmente afectar el resultado
de las siguientes pruebas haciendo nuestra experiencia con las pruebas muy
difícil. La limpieza, uno de los paso anteriores, tiene mucha
importancia para resolver el problema de «una base de datos como una
entrada global».

Cuando se prueban bases de datos, DbUnit ayuda a simplificar todos estos
problemas de una manera elegante

En lo que PHPUnit no puede ayudar es con el hecho de que las pruebas a bases
de datos son mucho más lentas comparadas con las pruebas que no usan bases
de datos. Dependiendo del cantidad de las interacciones con la base de datos
las pruebas podrían demorar una cantidad de tiempo considerable. Sin embargo,
si la cantidad de datos usados para cada prueba se mantiene pequeña y probamos
tanto código como sea posible con pruebas que no interactuaran con la
base de datos, podemos fácilmente terminar en menos de un minuto un conjunto
grande de pruebas.

El conjunto de pruebas del proyecto `Doctrine 2 <http://www.doctrine-project.org>`_',
por ejemplo, tiene actualmente cerca de 1000 pruebas
donde casi la mitad de ellas acceden a la base de datos, su ejecución contra
una base de datos MySQL en una computadora de escritorio estándar es de 15
segundos.

.. _database.the-four-stages-of-a-database-test:

Las cuatro etapas de las pruebas con base de datos
##################################################

En su libro sobre Patrones de Prueba xUnit Gerard Meszaros lista las cuatro
etapas de una prueba unitaria:

#.

   Configurar el ambiente (fixture).

#.

   Ejercitar el Sistema Bajo Prueba.

#.

   Verificar los resultados.

#.

   Desmontar (Teardown).

    *¿Que es un ambiente (Fixture)?*

    Un ambiente describe el estado inicial en que está nuestra aplicación y
    su base de datos cuando se ejecuta la prueba.

Probar la base de datos exige que se utilice al menos una configuración
y un desmontaje para limpiar y escribir los datos iniciales dentro de las
tablas. La extensión de base de datos tiene una buena razón para revertir
los cuatro etapas de una prueba de base de datos, el siguiente flujo de
trabajo se ejecuta en cada prueba:

.. _database.clean-up-database:

1. Limpiar la Base de Datos
===========================

Como siempre existe una primera prueba que se ejecuta contra la base de
datos y no sabemos exactamente si ya existen datos en las tablas.
PHPUnit va a ejecutar un TRUNCATE contra todas las tablas especificadas
para redefinir sus estados a vacío.

.. _database.set-up-fixture:

2. Configurar el ambiente
=========================

PHPUnit va a iterar sobre todas las filas de ambientación especificadas y las
insertará en sus respectivas tablas.

.. _database.run-test-verify-outcome-and-teardown:

3–5. Ejecutar la Prueba, Verificar el resultado y Desmontar
===========================================================

Después de redefinir la base de datos y cargarla con su estado inicial
la verdadera prueba es ejecutada por PHPUnit. Esta parte de la prueba
no necesita de ningún conocimiento sobre la Extensión de Base de Datos
por lo que podemos seguir y probar cualquier cosa que queramos con nuestro
código.

Nuestras pruebas usan una aserción especial llamada ``assertDataSetsEqual()``
para fines de verificación, sin embargo es totalmente opcional. Esta
característica se explica en la sección «Aserciones en Bases de Datos».

.. _database.configuration-of-a-phpunit-database-testcase:

Configuración de un Caso de Prueba de una Base de Datos
#######################################################

Generalmente cuando se usa PHPUnit nuestros casos de prueba extenderán de
la clase ``PHPUnit\Framework\TestCase`` de la siguiente manera:

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;

    class MyTest extends TestCase
    {
        public function testCalculate()
        {
            $this->assertSame(2, 1 + 1);
        }
    }
    ?>

Si queremos probar código que trabaja con la Extensión de Base de Datos
la configuración es un poco más compleja y debemos extender de una *TestCase*
abstracta diferente e implementando los métodos abstractos
``getConnection()`` y ``getDataSet()``:

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\DbUnit\TestCaseTrait;

    class MyGuestbookTest extends TestCase
    {
        use TestCaseTrait;

        /**
         * @return PHPUnit\DbUnit\Database\Connection
         */
        public function getConnection()
        {
            $pdo = new PDO('sqlite::memory:');
            return $this->createDefaultDBConnection($pdo, ':memory:');
        }

        /**
         * @return PHPUnit\DbUnit\DataSet\IDataSet
         */
        public function getDataSet()
        {
            return $this->createFlatXMLDataSet(dirname(__FILE__).'/_files/guestbook-seed.xml');
        }
    }
    ?>

.. _database.implementing-getconnection:

Implementando getConnection()
=============================

Para permitir que las funcionalidades de limpieza y carga de datos funcione,
la Extensión de Base de Datos de PHPUnit necesita acceder a una conexión
de base de datos abstraída del proveedor a través de la biblioteca PDO.
Es importante notar que nuestra aplicación no necesita estar basada en
PDO para usar la extensión de base de datos de PHPUnit, la conexión
solo se usa para limpiar y aplicar la configuración.

En el ejemplo anterior creamos una conexión SQL en memoria que
y le pasamos el método ``createDefaultDBConnection`` que envuelve
la instancia PDO y un segundo parámetro (el nombre de la base de datos)
en una capa de abstracción muy simple para conexiones de base de datos del tipo
``PHPUnit\DbUnit\Database\Connection``.

La sección «Usar la API de Conexión de Base de Data» explica la API de esta
interfaz y como podemos hacer el mejor uso de ella.

.. _database.implementing-getdataset:

Implementando getDataSet()
==========================

El método ``getDataSet()`` define como debe ser el estado inicial de la base de
datos antes de que cada prueba sea ejecutada. El estado de la base de datos es
abstraído a través de los concepto DataSet (Conjunto de Datos) y DataTable (Tabla
de Datos) que son representados por las interfaces
``PHPUnit\DbUnit\DataSet\IDataSet`` y
``PHPUnit\DbUnit\DataSet\IDataTable``. La siguiente sección describe
en detalles como estos conceptos trabajan y que beneficios trae su uso en las
pruebas de base de datos.

Para la implementación solo necesitamos saber que el método ``getDataSet()``
se llama una vez durante el ``setUp()`` para traer el conjunto de datos de la
ambientación e insertarlos en la base de datos. En el ejemplo estamos usando
el método de fábrica ``createFlatXMLDataSet($filename)`` que representa un
conjunto de datos por medio de una representación XML.

.. _database.what-about-the-database-schema-ddl:

¿Y que pasa con el Esquema de Base de Datos (DDL)?
==================================================

PHPUnit asume que el esquema de base de datos con todas sus tablas, lanzadores,
secuencias y vistas están creadas antes de que la prueba sea ejecutada. Esto
significa que como desarrolladores debemos asegurar que la base de datos está
correctamente configurada antes de ejecutar el paquete de pruebas.

Existen varias maneras para alcanzar esta pre-condición de las pruebas con bases
de datos.

#.

   Si estamos usando una base de datos persistente (no SQLite en memoria) podemos
   con facilidad configurar la base de datos una solo vez con herramientas
   como phpMyAdmin para MySQL y reusar la base de datos en cada ejecución de
   una prueba.

#.

   Si usamos bibliotecas como
   `Doctrine 2 <http://www.doctrine-project.org>`_ o
   `Propel <http://www.propelorm.org/>`_
   podemos usar sus APIs para crear una sola vez el esquema de base de datos
   que necesitamos antes de ejecutar las pruebas. Podemos usar las capacidades
   de `Configuración y Bootstrap de PHPUnit's <textui.html>`_ para ejecutar ese
   código cada vez que nuestras pruebas sean ejecutadas.

.. _database.tip-use-your-own-abstract-database-testcase:

Consejo: Usemos nuestro propio Caso Abstracto de Prueba de Base de Datos
========================================================================

Del ejemplo previo de implementación podemos ver fácilmente que el método
``getConnection()`` es muy estático y podría usarse en diferentes casos de prueba
de base de datos. Además, para mantener el buen rendimiento de nuestras pruebas
y la carga sobre la base de datos baja podemos refactorizar un poco el código
para obtener un caso de prueba abstracto genérico para nuestra aplicación, y que
aún nos permita especificar datos de ambientación diferentes para cada
caso de prueba:

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\DbUnit\TestCaseTrait;

    abstract class MyApp_Tests_DatabaseTestCase extends TestCase
    {
        use TestCaseTrait;

        // only instantiate pdo once for test clean-up/fixture load
        static private $pdo = null;

        // only instantiate PHPUnit\DbUnit\Database\Connection once per test
        private $conn = null;

        final public function getConnection()
        {
            if ($this->conn === null) {
                if (self::$pdo == null) {
                    self::$pdo = new PDO('sqlite::memory:');
                }
                $this->conn = $this->createDefaultDBConnection(self::$pdo, ':memory:');
            }

            return $this->conn;
        }
    }
    ?>

Este código tiene la conexión a la base de datos incrustada en la conexión PDO.
PHPUnit tiene otra característica importante que podría hacer este caso de
prueba incluso más genérico. Si usamos la
`Configuración XML <appendixes.configuration.html#appendixes.configuration.php-ini-constants-variables>`_
podemos hacer la conexión a base de datos configurable para cada ejecución de una prueba.
Primero vamos a crear el archivo «phpunit.xml» en nuestra carpeta tests/ de
la aplicación para que se vea de la siguiente manera:

.. code-block:: bash

    <?xml version="1.0" encoding="UTF-8" ?>
    <phpunit>
        <php>
            <var name="DB_DSN" value="mysql:dbname=myguestbook;host=localhost" />
            <var name="DB_USER" value="user" />
            <var name="DB_PASSWD" value="passwd" />
            <var name="DB_DBNAME" value="myguestbook" />
        </php>
    </phpunit>

Ahora podemos modificar nuestro caso de prueba de la siguiente manera:

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\DbUnit\TestCaseTrait;

    abstract class Generic_Tests_DatabaseTestCase extends TestCase
    {
        use TestCaseTrait;

        // only instantiate pdo once for test clean-up/fixture load
        static private $pdo = null;

        // only instantiate PHPUnit\DbUnit\Database\Connection once per test
        private $conn = null;

        final public function getConnection()
        {
            if ($this->conn === null) {
                if (self::$pdo == null) {
                    self::$pdo = new PDO( $GLOBALS['DB_DSN'], $GLOBALS['DB_USER'], $GLOBALS['DB_PASSWD'] );
                }
                $this->conn = $this->createDefaultDBConnection(self::$pdo, $GLOBALS['DB_DBNAME']);
            }

            return $this->conn;
        }
    }
    ?>

Ahora podemos ejecutar el paquete de pruebas de base de datos usando diferentes
configuraciones desde la interfaz de línea de comandos:

.. code-block:: bash

    $ user@desktop> phpunit --configuration developer-a.xml MyTests/
    $ user@desktop> phpunit --configuration developer-b.xml MyTests/

La posibilidad de ejecutar las pruebas de base de datos contra diferentes
base de datos es muy importante si estamos programando en una computadora
de desarrollo. Si varios desarrolladores ejecutan las pruebas de base de datos
contra la misma conexión de base de datos podemos fácilmente experimentar
fallas en las pruebas a causa de una condición de carrera.

.. _database.understanding-datasets-and-datatables:

Entendiendo los Conjuntos de Datos y las Tablas de Datos
########################################################

Los Conjuntos de Datos (DataSets) y las Tablas de Datos (DataTables) son conceptos
centrales de la Extensión de Base de Datos de PHPUnit. Deberías intentar entender
estos conceptos simples para dominar las pruebas de base de datos con PHPUnit. Los
DataSet y los DataTable son una capa de abstracción en torno a las tablas, filas
y columnas de nuestra base de datos. Una API simple que oculta el contenido
subyacente de la base de datos en una estructura de tipo objeto y que además permite
implementar otras fuentes de datos que no son una base de datos.

Esta abstracción es necesaria para comparar el contenido actual de la base de
datos con el contenido esperado. Por ejemplo, las expectativas se pueden
representar con archivos XML, YAML o CSV; o con un arreglo PHP. Las interfaces
DataSet (Conjunto de Datos) y DataTable (Tablas de Datos) permiten comparar
estas fuentes conceptualmente diferentes, emulando el almacenamiento en una base
de datos relacional y con un abordaje sistemáticamente similar.

El flujo de trabajo para las aserciones de base de datos en nuestras pruebas consiste
en tres simples pasos:

-

  Especificar una o más tablas de nuestra base de datos por el nombre de la tabla
  (conjunto de datos real).

-

  Especificar el conjunto de datos esperado en nuestro formato preferido
  (YAML, XML, ...).

-

  Afirmar que ambas representaciones de datos son iguales.

Las aserciones no son los únicos casos de uso para los Conjuntos de Datos y las
Tablas de Datos de la Extensión para Bases de Datos de PHPUnit.
Como se muestra en la sección anterior ellos además describen el contenido
inicial de una base de datos. Estamos obligados a definir un conjunto de datos
para la ambientación del Caso de Prueba de la Base de Datos, que luego
se usa para:

-

  Borrar todas las filas de las tablas especificadas en el conjunto de datos.

-

  Escribir todas las columnas en las tablas de datos de la base de datos.

.. _database.available-implementations:

Implementaciones Disponibles
============================

Existen tres diferentes tipos de Conjunto de Datos y Tablas de Datos:

-

  Conjuntos de Datos y Tablas de Datos basadas en archivo.

-

  Conjuntos de Datos y Tablas de Datos basadas en consulta.

-

  Filtros y Composición de Conjuntos de Datos y Tablas de Datos.

Los Conjuntos de Datos y Tablas de Datos basados en archivo se usan generalmente
para la ambientación inicial y para describir los estados esperados de la base
de datos.

.. _database.flat-xml-dataset:

Conjunto de Datos con XML Plano
-------------------------------

El conjunto de datos más común es el llamado XML Plano. Es un formato xml muy
simple donde una etiqueta dentro del nodo raíz ``<dataset>`` representa exactamente
una fila en la base de datos. El nombre de la etiqueta es igual a la tabla en la
que se inserta la fila y un atributo representa una columna. Un ejemplo para una
aplicación simple de libro de visitas podría ser el siguiente:

.. code-block:: bash

    <?xml version="1.0" ?>
    <dataset>
        <guestbook id="1" content="Hello buddy!" user="joe" created="2010-04-24 17:15:23" />
        <guestbook id="2" content="I like it!" user="nancy" created="2010-04-26 12:14:20" />
    </dataset>

Es obviamente fácil de escribir. Aquí ``<guestbook>`` es el nombre de la tabla
donde dos filas se insertan cada una con cuatro columnas «id», «content», «user»
y «created» con sus respectivos valores.

Sin embargo, esta simplicidad tiene un costo.

En el ejemplo anterior no es obvio como podríamos especificar una tabla vacía.
Podemos insertar una etiqueta sin atributos con el nombre de la tabla vacía.
Un archivo xml plano para una tabla que representa un libro de visitas vacío
podría ser el siguiente:

.. code-block:: bash

    <?xml version="1.0" ?>
    <dataset>
        <guestbook />
    </dataset>

La manipulación de valores NULL con xml plano es tedioso. En casi todas las bases
de datos, excepto Oracle, un valor NULL es diferente a una cadena de caracteres
con valor vacío, además, es algo que resulta difícil de describir en un formato
xml plano. Podemos representar valores NULL omitiendo el atributo que especifica
la fila. Si nuestro libro de visitas permite entradas anónimas representadas
por el valor NULL en la columna de usuario, un estado hipotético de la tabla
que representa el libro de visitas podría ser el siguiente:

.. code-block:: bash

    <?xml version="1.0" ?>
    <dataset>
        <guestbook id="1" content="Hello buddy!" user="joe" created="2010-04-24 17:15:23" />
        <guestbook id="2" content="I like it!" created="2010-04-26 12:14:20" />
    </dataset>

En este caso la segunda entrada es publicada anónimamente. Sin embargo, esto
deja un problema serio para el reconocimiento de columnas. Durante las aserciones
de igualdad de conjunto de datos, cada conjunto de datos debe especificar que
columnas pertenecen a una tabla. Si un atributo es NULL para todas las filas
de la tabla de datos, ¿como podría saber la Extensión de Base de Datos que la
columna debe ser parte de la tabla?

El conjunto de datos en xml plano hace una suposición crucial, a partir del
atributo de la primera fila definida de una tabla, se definen las columnas de
esta tabla. En el ejemplo anterior esto significaría que «id», «content», «user»
y «created» son columnas de la tabla del libro de visitas. Para la segunda fila
donde «user» no se define, un NULL se insertaría en la base de datos.

Cuando la primera entrada del libro de visitas se borra solo las columnas «id»,
«content» y «created» serán columnas de la tabla del libro de visitas porque
«user» no está especificado.

Para usar eficazmente el conjunto de datos con un XML Plano cuando los valores
NULL son importantes la primera columna de cada tabla no debe contener ningún
valor nulo y solo las filas siguientes pueden omitir atributos. Esto puede ser
molesto ya que el orden de las filas es un importante factor para las aserciones
de base de datos.

Además, si especificamos solo un subconjunto de columnas de la tabla en el conjunto
de datos XML Plano todos los valores omitidos se colocarán en su valor por defecto.
Esto puede traer errores si una de las columnas omitidas se define como
«NOT NULL DEFAULT NULL».

En conclusión podemos decir que los conjuntos de datos en XML Plano solo se pueden
usar si no necesitamos valores NULL.

Podemos crear una instancia del conjunto de datos xml plano dentro de nuestro
Caso de Prueba de Base de Datos llamando al método ``createFlatXmlDataSet($filename)``:

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\DbUnit\TestCaseTrait;

    class MyTestCase extends TestCase
    {
        use TestCaseTrait;

        public function getDataSet()
        {
            return $this->createFlatXmlDataSet('myFlatXmlFixture.xml');
        }
    }
    ?>

.. _database.xml-dataset:

Conjunto de Datos XML
---------------------

Existe otro conjunto de datos XML más estructurado, que es un poco más detallado
de escribir pero evita los problemas con los valores NULL del conjunto de datos
XML Plano. Dentro de la raíz ``<dataset>`` podemos especificar etiquetas ``<table>``,
``<column>``, ``<row>``, ``<value>`` y ``<null />``. Un conjunto de datos equivalente
al libro de visitas con XML plano definido anteriormente se ve de la siguiente
manera:

.. code-block:: bash

    <?xml version="1.0" ?>
    <dataset>
        <table name="guestbook">
            <column>id</column>
            <column>content</column>
            <column>user</column>
            <column>created</column>
            <row>
                <value>1</value>
                <value>Hello buddy!</value>
                <value>joe</value>
                <value>2010-04-24 17:15:23</value>
            </row>
            <row>
                <value>2</value>
                <value>I like it!</value>
                <null />
                <value>2010-04-26 12:14:20</value>
            </row>
        </table>
    </dataset>

Toda ``<table>`` definida tiene un nombre y requiere una definición de todas las
columnas con sus nombres. Esta puede contener cero o cualquier número de elementos
``<row>`` anidados. No definir un elemento ``<row>`` significa que la tabla está
vacía. Las etiquetas ``<value>`` y ``<null />`` se deben especificar en el orden
en que se especificaron los elementos ``<column>``. La etiqueta ``<null />``
obviamente significa que el valor es NULL.

Podemos crear una instancia del conjunto de datos xml desde dentro de nuestro
Caso de Prueba de Base de Datos llamando al método ``createXmlDataSet($filename)``:

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\DbUnit\TestCaseTrait;

    class MyTestCase extends TestCase
    {
        use TestCaseTrait;

        public function getDataSet()
        {
            return $this->createXMLDataSet('myXmlFixture.xml');
        }
    }
    ?>

.. _database.mysql-xml-dataset:

Conjunto de Datos XML MySQL
---------------------------

Este nuevo formato es específico para `servidor de base de datos MySQL <http://www.mysql.com>`_.
El soporte para este formato se añadió en PHPUnit 3.5. Los archivos en este
formato se pueden generar usando la herramiento
`mysqldump <http://dev.mysql.com/doc/refman/5.0/en/mysqldump.html>`_.
A diferencia de los conjuntos de datos CSV, que ``mysqldump`` también soporta,
un solo archivo en este formato pueden contener datos de varias tablas. Podemos
crear un archivo en este formato invocando el comando ``mysqldump`` de la
siguiente forma:

.. code-block:: bash

    $ mysqldump --xml -t -u [username] --password=[password] [database] > /path/to/file.xml

Este archivo se puede usar en nuestro Caso de Prueba de Base de Datos llamando
al método ``createMySQLXMLDataSet($filename)``:

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\DbUnit\TestCaseTrait;

    class MyTestCase extends TestCase
    {
        use TestCaseTrait;

        public function getDataSet()
        {
            return $this->createMySQLXMLDataSet('/path/to/file.xml');
        }
    }
    ?>

.. _database.yaml-dataset:

Conjunto de Datos YAML
----------------------

También podemos usar el conjunto de datos YAML para el ejemplo del libro de visitas:

.. code-block:: bash

    guestbook:
      -
        id: 1
        content: "Hello buddy!"
        user: "joe"
        created: 2010-04-24 17:15:23
      -
        id: 2
        content: "I like it!"
        user:
        created: 2010-04-26 12:14:20

Este formato es simple, conveniente Y soluciona el problema de los valores NULL
que tendría un conjunto de datos similar representado con un XML Plano. Un valor
NULL en YAML es solamente el nombre de la columna sin ningún valor especificado.
Una cadena de caracteres vacía se especifica como ``column1: ""``.

El conjunto de datos YAML no tiene actualmente un método fábrica para el Caso de
Prueba de Base de Datos, por lo que debemos instanciarlo manualmente:

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\DbUnit\TestCaseTrait;
    use PHPUnit\DbUnit\DataSet\YamlDataSet;

    class YamlGuestbookTest extends TestCase
    {
        use TestCaseTrait;

        protected function getDataSet()
        {
            return new YamlDataSet(dirname(__FILE__)."/_files/guestbook.yml");
        }
    }
    ?>

.. _database.csv-dataset:

Conjunto de Datos CSV
---------------------

Otro conjunto de datos basado en archivo se basa en archivos CSV. Cada tabla del
conjunto de datos se representa con un archivo CSV. Para nuestro ejemplo de libro
de visitas podemos definir el archivo guestbook-table.csv:

.. code-block:: bash

    id,content,user,created
    1,"Hello buddy!","joe","2010-04-24 17:15:23"
    2,"I like it!","nancy","2010-04-26 12:14:20"

A pesar de ser muy conveniente para editar con Excel o LibreOffice, no podemos
especificar valores NULL con el conjunto de datos CSV. Una columna vacía llevará
a que el valor vacío por defecto de la base de datos se inserte en la columna.

Podemos crear un Conjunto de Datos CSV llamando:

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\DbUnit\TestCaseTrait;
    use PHPUnit\DbUnit\DataSet\CsvDataSet;

    class CsvGuestbookTest extends TestCase
    {
        use TestCaseTrait;

        protected function getDataSet()
        {
            $dataSet = new CsvDataSet();
            $dataSet->addTable('guestbook', dirname(__FILE__)."/_files/guestbook.csv");
            return $dataSet;
        }
    }
    ?>

.. _database.array-dataset:

Conjunto de Datos en Arreglo
----------------------------

No existe (aún) un Conjunto de Datos basado en Arreglos en la Extensión de Base
de Datos de PHPUnit, pero podemos implementar con facilidad uno propio. Nuestro
ejemplo de libro de visitas se vera de la siguiente manera:

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\DbUnit\TestCaseTrait;

    class ArrayGuestbookTest extends TestCase
    {
        use TestCaseTrait;

        protected function getDataSet()
        {
            return new MyApp_DbUnit_ArrayDataSet(
                [
                    'guestbook' => [
                        [
                            'id' => 1,
                            'content' => 'Hello buddy!',
                            'user' => 'joe',
                            'created' => '2010-04-24 17:15:23'
                        ],
                        [
                            'id' => 2,
                            'content' => 'I like it!',
                            'user' => null,
                            'created' => '2010-04-26 12:14:20'
                        ],
                    ],
                ]
            );
        }
    }
    ?>

Un Conjunto de Datos con PHP tiene obvias ventajas sobre todo los otros conjuntos
de datos basados en archivos:

-

  Los Arreglos de PHP pueden obviamente manejar valores ``NULL``.

-

  No necesitaremos agregar archivos para las aserciones y podemos especificarlas
  directamente en el Caso de Prueba.

Para este conjunto de datos; como para los anteriores Conjuntos de Datos XML Plano,
CSV y YAML; las llaves de la primera fila especificada definen los nombres de las
columnas de la tabla, en el caso anterior estas serán ser «id», «content»,
«user» y «created».

La implementación para este Conjunto de Datos basado en Arreglos es simple y
directo:

.. code-block:: php

    <?php

    use PHPUnit\DbUnit\DataSet\AbstractDataSet;
    use PHPUnit\DbUnit\DataSet\DefaultTableMetaData
    use PHPUnit\DbUnit\DataSet\DefaultTable
    use PHPUnit\DbUnit\DataSet\DefaultTableIterator

    class MyApp_DbUnit_ArrayDataSet extends AbstractDataSet
    {
        /**
         * @var array
         */
        protected $tables = [];

        /**
         * @param array $data
         */
        public function __construct(array $data)
        {
            foreach ($data AS $tableName => $rows) {
                $columns = [];
                if (isset($rows[0])) {
                    $columns = array_keys($rows[0]);
                }

                $metaData = new DefaultTableMetaData($tableName, $columns);
                $table = new DefaultTable($metaData);

                foreach ($rows AS $row) {
                    $table->addRow($row);
                }
                $this->tables[$tableName] = $table;
            }
        }

        protected function createIterator($reverse = false)
        {
            return new DefaultTableIterator($this->tables, $reverse);
        }

        public function getTable($tableName)
        {
            if (!isset($this->tables[$tableName])) {
                throw new InvalidArgumentException("$tableName is not a table in the current database.");
            }

            return $this->tables[$tableName];
        }
    }
    ?>

.. _database.query-sql-dataset:

Conjunto de Datos basados en Consultas SQL
------------------------------------------

Para las aserciones de base de datos no solo necesitamos conjuntos de datos basados
en archivo sino también conjuntos de datos basados en Consultas SQL que contengan
el contenido real de la base de datos. En este caso es que los Conjuntos de Datos
basados en Consultas toman protagonismo:

.. code-block:: php

    <?php
    $ds = new PHPUnit\DbUnit\DataSet\QueryDataSet($this->getConnection());
    $ds->addTable('guestbook');
    ?>

Agregar una tabla usando su nombre es una manera implícita de definir los datos
de un tabla, que es equivalente a la siguiente consulta:

.. code-block:: php

    <?php
    $ds = new PHPUnit\DbUnit\DataSet\QueryDataSet($this->getConnection());
    $ds->addTable('guestbook', 'SELECT * FROM guestbook');
    ?>

Podemos usar este método para especificar consultas arbitrarias sobre nuestras
tablas y, por ejemplo, restringir las filas, las columnas o agregar clausulas
``ORDER BY``:

.. code-block:: php

    <?php
    $ds = new PHPUnit\DbUnit\DataSet\QueryDataSet($this->getConnection());
    $ds->addTable('guestbook', 'SELECT id, content FROM guestbook ORDER BY created DESC');
    ?>

En la sección sobre Aserciones de Base de Datos mostraremos algunos detalles más
sobre como hacer uso del Conjunto de Datos en base a Consultas.

.. _database.database-db-dataset:

Conjunto de Datos basados en Base de Datos
------------------------------------------

Luego de acceder a una Conexión de Prueba podemos automáticamente crear un Conjunto
de Datos que consiste en todas las tablas, con su contenido, de la base de datos
que se especifica como segundo parámetro del método Fábrica de Conexiones.

Podemos crear un conjunto de datos para toda la base de datos como se muestra
en ``testGuestbook()`` o restringirlo a un conjunto de tablas, especificándolas
por su nombre con una lista blanca como se muestra en el método ``testFilteredGuestbook()``.

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\DbUnit\TestCaseTrait;

    class MySqlGuestbookTest extends TestCase
    {
        use TestCaseTrait;

        /**
         * @return PHPUnit\DbUnit\Database\Connection
         */
        public function getConnection()
        {
            $database = 'my_database';
            $user = 'my_user';
            $password = 'my_password';
            $pdo = new PDO('mysql:...', $user, $password);
            return $this->createDefaultDBConnection($pdo, $database);
        }

        public function testGuestbook()
        {
            $dataSet = $this->getConnection()->createDataSet();
            // ...
        }

        public function testFilteredGuestbook()
        {
            $tableNames = ['guestbook'];
            $dataSet = $this->getConnection()->createDataSet($tableNames);
            // ...
        }
    }
    ?>

.. _database.replacement-dataset:

Replacement DataSet
-------------------

Hemos hablado sobre los problemas con los valores nulos cuando usamos Conjuntos
de Datos como XML Plano y CSV, pero hay una solución algo complicada para ambos
casos que nos permite ponerlos a trabajar con valores NULL.

El Conjunto de Datos de Reemplazo es un decorador para un conjunto de datos existente
que permite remplazar el valor de cualquier columna del conjunto de datos por otro
valor. Para tener nuestro ejemplo de libro de visitas trabajando con valores
NULL modificamos el archivo de la siguiente manera:

.. code-block:: bash

    <?xml version="1.0" ?>
    <dataset>
        <guestbook id="1" content="Hello buddy!" user="joe" created="2010-04-24 17:15:23" />
        <guestbook id="2" content="I like it!" user="##NULL##" created="2010-04-26 12:14:20" />
    </dataset>

Luego, envolvemos el Conjunto de Datos XML Plano con el Conjunto de Datos de
Remplazo:

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\DbUnit\TestCaseTrait;

    class ReplacementTest extends TestCase
    {
        use TestCaseTrait;

        public function getDataSet()
        {
            $ds = $this->createFlatXmlDataSet('myFlatXmlFixture.xml');
            $rds = new PHPUnit\DbUnit\DataSet\ReplacementDataSet($ds);
            $rds->addFullReplacement('##NULL##', null);
            return $rds;
        }
    }
    ?>

.. _database.dataset-filter:

Filtro de Conjunto de Datos
---------------------------

Si tenemos un archivo de ambientación muy grande podemos usar el Filtro de Conjunto
de Datos para crear una lista blanco o negra de tablas y columnas que contendrá
un subconjunto de datos. Esto es especialmente útil, cuando se combina con el
Conjunto de Datos basado en Base de Datos, para filtrar las columnas del conjunto
de datos.

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\DbUnit\TestCaseTrait;

    class DataSetFilterTest extends TestCase
    {
        use TestCaseTrait;

        public function testIncludeFilteredGuestbook()
        {
            $tableNames = ['guestbook'];
            $dataSet = $this->getConnection()->createDataSet();

            $filterDataSet = new PHPUnit\DbUnit\DataSet\DataSetFilter($dataSet);
            $filterDataSet->addIncludeTables(['guestbook']);
            $filterDataSet->setIncludeColumnsForTable('guestbook', ['id', 'content']);
            // ..
        }

        public function testExcludeFilteredGuestbook()
        {
            $tableNames = ['guestbook'];
            $dataSet = $this->getConnection()->createDataSet();

            $filterDataSet = new PHPUnit\DbUnit\DataSet\DataSetFilter($dataSet);
            $filterDataSet->addExcludeTables(['foo', 'bar', 'baz']); // only keep the guestbook table!
            $filterDataSet->setExcludeColumnsForTable('guestbook', ['user', 'created']);
            // ..
        }
    }
    ?>

.. admonition:: Nota

    No podemos usar ambos filtros de columnas, exclusión e inclusión, sobre la
    misma tabla, solo es posible sobre diferentes tablas. Ademas, solo es posible
    la lista blanca de tablas o la negra, no ambas.

.. _database.composite-dataset:

Conjunto de Datos Compuesto
---------------------------

El Conjunto de Datos compuesto es muy útil para agregar varios conjuntos de datos
que ya existen dentro de un solo conjunto de datos. Cuando varios conjuntos de
datos pertenecen a la misma tabla, las filas se añaden en el orden especificado.
Por ejemplo si tenemos dos conjuntos de datos *fixture1.xml*:

.. code-block:: bash

    <?xml version="1.0" ?>
    <dataset>
        <guestbook id="1" content="Hello buddy!" user="joe" created="2010-04-24 17:15:23" />
    </dataset>

y *fixture2.xml*:

.. code-block:: bash

    <?xml version="1.0" ?>
    <dataset>
        <guestbook id="2" content="I like it!" user="##NULL##" created="2010-04-26 12:14:20" />
    </dataset>

Usando el Conjunto de Datos Compuesto podemos agregar ambos archivos a la ambientación:

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\DbUnit\TestCaseTrait;

    class CompositeTest extends TestCase
    {
        use TestCaseTrait;

        public function getDataSet()
        {
            $ds1 = $this->createFlatXmlDataSet('fixture1.xml');
            $ds2 = $this->createFlatXmlDataSet('fixture2.xml');

            $compositeDs = new PHPUnit\DbUnit\DataSet\CompositeDataSet();
            $compositeDs->addDataSet($ds1);
            $compositeDs->addDataSet($ds2);

            return $compositeDs;
        }
    }
    ?>

.. _database.beware-of-foreign-keys:

Precauciones con las Llaves Foráneas
====================================

La Extensión de Base de Datos de PHPUnit inserta las columnas en la base de datos
en el orden en que ellas se especifican en nuestra ambientación. Si nuestro
esquema de base de datos usa llaves foráneas debemos especificar las tablas en
un orden que no cause que las restricciones de llave foránea fallen.

.. _database.implementing-your-own-datasetsdatatables:

Implementar nuestro propio Conjunto de Datos/Tablas de Datos
============================================================

Para entender el interior de los Conjuntos de Datos y Tablas de Datos, vamos a
dar un vistazo a la interfaz de un Conjunto de Datos. Se puede saltar esta parte
si no planeamos implementar nuestro propio Conjunto de Datos o Tabla de Datos.

.. code-block:: php

    <?php
    namespace PHPUnit\DbUnit\DataSet;

    interface IDataSet extends IteratorAggregate
    {
        public function getTableNames();
        public function getTableMetaData($tableName);
        public function getTable($tableName);
        public function assertEquals(IDataSet $other);

        public function getReverseIterator();
    }
    ?>

La interfaz pública es usada internamente por la aserción ``assertDataSetsEqual()``
en el Caso de Prueba de Base de Datos para revisar la calidad del conjunto de datos.
De la interfaz ``IteratorAggregate`` la clase IDataSet hereda el método
``getIterator()`` para iterar sobre todas las tablas del conjunto de datos. El
iterador reverso permite a PHPUnit truncar tablas en el orden opuesto al que ellas
fueron creadas para satisfacer las restricciones de llave foránea.

Dependiendo de la implementación, se toman diferentes enfoques para agregar
instancias de tabla a un conjunto de datos. Por ejemplo, las tablas se agregan
internamente durante la construcción del archivo fuente en todos los Conjuntos de
Datos basados en archivo, tales como ``YamlDataSet``, ``XmlDataSet`` o
``FlatXmlDataSet``.

Una tabla también se representa con la siguiente interfaz:

.. code-block:: php

    <?php
    interface ITable
    {
        public function getTableMetaData();
        public function getRowCount();
        public function getValue($row, $column);
        public function getRow($row);
        public function assertEquals(ITable $other);
    }
    ?>

Con la excepción del método ``getTableMetaData()``, el código anterior es bastante
autoexplicativo. Los métodos usados son todos requeridos para las diferentes
aserciones de la Extensión de la Base de Datos que se explican en el siguiente
capítulo. El método ``getTableMetaData()`` debe regresar una implementación de la
interfaz ``PHPUnit\DbUnit\DataSet\ITableMetaData``, que describe la
estructura de la tabla. La información que posee es:

-

  El nombre de la tabla.

-

  Un arreglo con los nombres de las columnas de la tabla, ordenada por su aparición
  en el conjunto de resultados.

-

  Un arreglo de las columnas que son llave primaria.

Esta interfaz tiene además una aserción que revisa si dos instancias de los
Metadatos de la Tabla son iguales entre si, que es usado por la aserción
de igualdad de conjunto de datos.

.. _database.the-connection-api:

Usar la API de Conexión de Base de Datos
########################################

Existen tres métodos interesantes en la interfaz de Conexión que debe regresar
el método ``getConnection()`` en el Caso de Prueba de Base de Datos:

.. code-block:: php

    <?php
    namespace PHPUnit\DbUnit\Database;

    interface Connection
    {
        public function createDataSet(Array $tableNames = NULL);
        public function createQueryTable($resultName, $sql);
        public function getRowCount($tableName, $whereClause = NULL);

        // ...
    }
    ?>

#.

   El método ``createDataSet()`` crea un Conjunto de Datos basado en Base de
   Datos (DB) como se describe en la sección de implementaciones de Conjunto de
   Datos.

   .. code-block:: php

       <?php
       use PHPUnit\Framework\TestCase;
       use PHPUnit\DbUnit\TestCaseTrait;

       class ConnectionTest extends TestCase
       {
           use TestCaseTrait;

           public function testCreateDataSet()
           {
               $tableNames = ['guestbook'];
               $dataSet = $this->getConnection()->createDataSet();
           }
       }
       ?>

#.

   El método ``createQueryTable()`` se puede usar para crear instancias de
   una «Consulta a Tabla» (QueryTable), dado el nombre de una tabla y una
   consulta SQL.

   .. code-block:: php

       <?php
       use PHPUnit\Framework\TestCase;
       use PHPUnit\DbUnit\TestCaseTrait;

       class ConnectionTest extends TestCase
       {
           use TestCaseTrait;

           public function testCreateQueryTable()
           {
               $tableNames = ['guestbook'];
               $queryTable = $this->getConnection()->createQueryTable('guestbook', 'SELECT * FROM guestbook');
           }
       }
       ?>

#.

   El método ``getRowCount()`` es una manera conveniente de acceder al número de
   filas de una tabla, opcionalmente filtrado por una clausula «where». Este
   método se puede usar con una aserción simple de igualdad:

   .. code-block:: php

       <?php
       use PHPUnit\Framework\TestCase;
       use PHPUnit\DbUnit\TestCaseTrait;

       class ConnectionTest extends TestCase
       {
           use TestCaseTrait;

           public function testGetRowCount()
           {
               $this->assertSame(2, $this->getConnection()->getRowCount('guestbook'));
           }
       }
       ?>

.. _database.database-assertions-api:

API de Aserciones de Base de Datos
##################################

Una herramienta de pruebas como la Extensión de Base de Datos debe proveer
algunas aserciones que podemos usar para revisar el estado actual
de la base de datos, las tablas y la cantidad de filas de una tabla.
En esta sección se describe estas funcionalidades detalladamente:

.. _database.asserting-the-row-count-of-a-table:

Aseverar el número de filas de una Tabla
========================================

A menudo es útil revisar si una tabla contiene una cantidad específica de filas.
Podemos fácilmente conseguir esto sin código de enlace adicional usando la API
de conexión. Supongamos que queremos revisar si después de insertar una fila
en nuestro libro de visitas no solo tenemos las dos entradas iniciales, las que
nos han acompañado en todos los ejemplos anteriores, sino tres:

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\DbUnit\TestCaseTrait;

    class GuestbookTest extends TestCase
    {
        use TestCaseTrait;

        public function testAddEntry()
        {
            $this->assertSame(2, $this->getConnection()->getRowCount('guestbook'), "Pre-Condition");

            $guestbook = new Guestbook();
            $guestbook->addEntry("suzy", "Hello world!");

            $this->assertSame(3, $this->getConnection()->getRowCount('guestbook'), "Inserting failed");
        }
    }
    ?>

.. _database.asserting-the-state-of-a-table:

Aseverar el Estado de una Tabla
===============================

La aseveración anterior es útil pero seguramente queremos revisar el contenido
real de una tabla para verificar que todos los valores se escribieron correctamente
en las columnas. Esto se puede lograr con una aserción de tabla.

Para esto definiremos una instancia de Consulta de Tabla que obtiene
su contenido del nombre de una tabla y una consulta SQL, y luego la compara con
un Conjunto de Datos basado en Archivos o Arreglos:

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\DbUnit\TestCaseTrait;

    class GuestbookTest extends TestCase
    {
        use TestCaseTrait;

        public function testAddEntry()
        {
            $guestbook = new Guestbook();
            $guestbook->addEntry("suzy", "Hello world!");

            $queryTable = $this->getConnection()->createQueryTable(
                'guestbook', 'SELECT * FROM guestbook'
            );
            $expectedTable = $this->createFlatXmlDataSet("expectedBook.xml")
                                  ->getTable("guestbook");
            $this->assertTablesEqual($expectedTable, $queryTable);
        }
    }
    ?>

Ahora debemos escribir el archivo XML Plano *expectedBook.xml* para esta aserción:

.. code-block:: bash

    <?xml version="1.0" ?>
    <dataset>
        <guestbook id="1" content="Hello buddy!" user="joe" created="2010-04-24 17:15:23" />
        <guestbook id="2" content="I like it!" user="nancy" created="2010-04-26 12:14:20" />
        <guestbook id="3" content="Hello world!" user="suzy" created="2010-05-01 21:47:08" />
    </dataset>

Sin embargo esta aserción solo se cumple durante un segundo, *2010–05–01 21:47:08*.
Las fechas representan un problema especial para las pruebas de base de datos,
podemos evitar estas fallas omitiendo la columna «created» en la aserción.

El archivo XML Plano *expectedBook.xml* ajustado debe verse de la siguiente manera
para que la aserción pase la prueba:

.. code-block:: bash

    <?xml version="1.0" ?>
    <dataset>
        <guestbook id="1" content="Hello buddy!" user="joe" />
        <guestbook id="2" content="I like it!" user="nancy" />
        <guestbook id="3" content="Hello world!" user="suzy" />
    </dataset>

Debemos arreglar la llamada a la Consulta de Tabla:

.. code-block:: php

    <?php
    $queryTable = $this->getConnection()->createQueryTable(
        'guestbook', 'SELECT id, content, user FROM guestbook'
    );
    ?>

.. _database.asserting-the-result-of-a-query:

Aseverar el Resultado de una Consulta
=====================================

Además podemos aseverar el resultado de una consulta compleja con el enfoque
de Consulta de Tabla, solamente especificando el resultado de la consulta y
comparándola con un conjunto de datos:

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\DbUnit\TestCaseTrait;

    class ComplexQueryTest extends TestCase
    {
        use TestCaseTrait;

        public function testComplexQuery()
        {
            $queryTable = $this->getConnection()->createQueryTable(
                'myComplexQuery', 'SELECT complexQuery...'
            );
            $expectedTable = $this->createFlatXmlDataSet("complexQueryAssertion.xml")
                                  ->getTable("myComplexQuery");
            $this->assertTablesEqual($expectedTable, $queryTable);
        }
    }
    ?>

.. _database.asserting-the-state-of-multiple-tables:

Aseverar el Estado de Varias Tablas
===================================

Sin dudas podemos aseverar el estado de multiples tablas de una sola vez,
comparando un conjunto de datos basados en una consulta contra un conjunto de
datos basados en archivos. Existen dos maneras diferentes para las aserciones
de Conjunto de Datos.

#.

   Podemos usar el Conjunto de Datos de Base de Datos (DB)
   y compararlo con un Conjunto de Datos basado en Archivos.

   .. code-block:: php

       <?php
       use PHPUnit\Framework\TestCase;
       use PHPUnit\DbUnit\TestCaseTrait;

       class DataSetAssertionsTest extends TestCase
       {
           use TestCaseTrait;

           public function testCreateDataSetAssertion()
           {
               $dataSet = $this->getConnection()->createDataSet(['guestbook']);
               $expectedDataSet = $this->createFlatXmlDataSet('guestbook.xml');
               $this->assertDataSetsEqual($expectedDataSet, $dataSet);
           }
       }
       ?>

#.

   Podemos construir nuestro propio Conjunto de Datos:

   .. code-block:: php

       <?php
       use PHPUnit\Framework\TestCase;
       use PHPUnit\DbUnit\TestCaseTrait;
       use PHPUnit\DbUnit\DataSet\QueryDataSet;

       class DataSetAssertionsTest extends TestCase
       {
           use TestCaseTrait;

           public function testManualDataSetAssertion()
           {
               $dataSet = new QueryDataSet();
               $dataSet->addTable('guestbook', 'SELECT id, content, user FROM guestbook'); // additional tables
               $expectedDataSet = $this->createFlatXmlDataSet('guestbook.xml');

               $this->assertDataSetsEqual($expectedDataSet, $dataSet);
           }
       }
       ?>

.. _database.frequently-asked-questions:

Preguntas y Respuestas Comunes
##############################

.. _database.will-phpunit-re-create-the-database-schema-for-each-test:

¿PHPUnit (re)creará el esquema de base de datos para cada prueba?
=================================================================

No, PHPUnit necesita que todos los objetos de la base de datos
estén disponibles cuando las pruebas comiencen. La base de datos,
tablas, secuencias, lanzadores y vistas se deben crear antes de
ejecutar el conjunto de pruebas.

`Doctrine 2 <http://www.doctrine-project.org>`_ o
`eZ Components <http://www.ezcomponents.org>`_ tienen
poderosas herramientas que permiten crear el esquema de base
de datos desde una estructura de datos predefinida. Sin embargo,
ellas deben estar enlazadas a la extensión de PHPUnit para
permitir la recreación automática de la base de datos antes
de ejecutar el paquete de pruebas completo.

Como cada prueba limpia completamente la base de datos no necesitamos
recrear la base de datos para ejecutar cada prueba. Una base de datos
disponible permanentemente funciona perfectamente.

.. _database.am-i-required-to-use-pdo-in-my-application-for-the-database-extension-to-work:

¿Estoy obligado a usar PDO en mi aplicación para que la Extensión de Base de Datos funcione?
============================================================================================

No, PDO solo es obligatorio para limpiar y configurar el ambiente y
para las aserciones. Podemos usar cualquier abstracción de base de
datos dentro de nuestro código.

.. _database.what-can-i-do-when-i-get-a-too-much-connections-error:

¿Que puedo hacer cuando recibo un Error «Too much Connections»?
===============================================================

Si no guardamos la instancia PDO que se crea con el método
``getConnection()`` del Caso de Prueba el número de conexiones
a la base de datos se incrementará una o más veces por cada prueba
de base de datos. La configuración por defecto de MySQL solo permite
100 conexiones concurrentes y otros proveedores también tienen un
límite máximo de conexiones.

La subsección «Usemos nuestro propio Caso Abstracto de Prueba de Base
de Datos» muestra como podemos prevenir que este error suceda usando
una sola instancia PDO, guardada en la caché, para todos nuestras pruebas.

.. _database.how-to-handle-null-with-flat-xml-csv-datasets:

¿Como lidiar con valores NULL en los Conjuntos de Datos XML Plano y CSV?
========================================================================

No lo hagas. En su lugar, deberíamos usar el Conjunto de Datos XML
o el YAML.
