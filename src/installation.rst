

.. _installation:

==================
Instalar PHPUnit
==================

.. _installation.requirements:

Requisitos
##########

PHPUnit |version| requiere PHP 7.3. Se recomienda encarecidamente usar la última
versión de PHP.

PHPUnit requiere las extensiones `dom <http://php.net/manual/es/dom.setup.php>`_
y `json <http://php.net/manual/es/json.installation.php>`_ que normalmente están
habilitadas por defecto.

Además, PHPUnit requiere las extensiones `pcre <http://php.net/manual/es/pcre.installation.php>`_,
`reflection <http://php.net/manual/es/reflection.installation.php>`_,
y `spl <http://php.net/manual/es/spl.installation.php>`_.
Estas extensiones estándares se habilitan por defecto y no se pueden desactivar
sin parchear el sistema PHP construido y/o las fuentes en C.

La característica de reporte de cobertura de código requiere las extensiones
`Xdebug <http://xdebug.org/>`_ (2.7.0 o superior) y
`tokenizer <http://php.net/manual/es/tokenizer.installation.php>`_.
La generación de reportes en XML requiere la extensión
`xmlwriter <http://php.net/manual/es/xmlwriter.installation.php>`_.

.. _installation.phar:

PHP Archive (PHAR)
##################

La manera más fácil de obtener PHPUnit es descargar un `PHP Archive (PHAR) <http://php.net/phar>`_
que contiene todas las dependencias requeridas (así como también algunas opcionales)
de PHPUnit empaquetadas en un único archivo.

La extensión `phar <http://php.net/manual/es/phar.installation.php>`_
se necesita para usar PHP Archives (PHAR).

Si la extensión `Suhosin <http://suhosin.org/>`_ está habilitada,
será necesario permitir la ejecución de archivos PHAR modificando el archivo
``php.ini``:

.. code-block:: bash

    suhosin.executor.include.whitelist = phar

El archivo PHAR de PHPUnit se puede usar inmediatamente después de descargado:

.. parsed-literal::

    $ wget https://phar.phpunit.de/phpunit-|version|.phar
    $ php phpunit-|version|.phar --version
    PHPUnit x.y.z by Sebastian Bergmann and contributors.

Es una práctica común ejecutar directamente el archivo PHAR:

.. parsed-literal::

    $ wget https://phar.phpunit.de/phpunit-|version|.phar
    $ chmod +x phpunit-|version|.phar
    $ ./phpunit-|version|.phar --version
    PHPUnit x.y.z by Sebastian Bergmann and contributors.


.. _installation.phar.verification:

Verificar Lanzamientos de PHPUnit PHAR
======================================

Todas las versiones de código distribuido por el Proyecto PHPUnit
están firmadas por el «release manager» designado para el lanzamiento. Firmas
PGP y SHA1 están disponibles para verificación en `phar.phpunit.de <https://phar.phpunit.de/>`_.

El siguiente ejemplo detalla cómo funciona la verificación de un lanzamiento.
Iniciemos por descargar el archivo :file:`phpunit.phar` así como también su
firma PGP :file:`phpunit.phar.asc`:

.. parsed-literal::

    $ wget https://phar.phpunit.de/phpunit-|version|.phar
    $ wget https://phar.phpunit.de/phpunit-|version|.phar.asc

Queremos verificar el archivo PHAR de PHPUnit (:file:`phpunit-x.y.phar`)
contra su firma (:file:`phpunit-x.y.phar.asc`):

.. parsed-literal::

    $ gpg phpunit-|version|.phar.asc
    gpg: Signature made Sat 19 Jul 2014 01:28:02 PM CEST using RSA key ID 6372C20A
    gpg: Can't check signature: public key not found

No tenemos la llave pública del «release manager» (``6372C20A``) en nuestro sistema
local. Para continuar con la verificación necesitamos recuperar la llave pública
del «release manager» desde un servidor de llaves. Uno de esos servidores
es :file:`pgp.uni-mainz.de`. Los servidores de llaves públicas están enlazados
entre ellos, así que es posible conectarte a cualquier servidor de llaves.

.. parsed-literal::

    $ curl --silent https://sebastian-bergmann.de/gpg.asc | gpg --import
    gpg: key 4AA394086372C20A: 452 signatures not checked due to missing keys
    gpg: /root/.gnupg/trustdb.gpg: trustdb created
    gpg: key 4AA394086372C20A: public key "Sebastian Bergmann <sb@sebastian-bergmann.de>" imported
    gpg: Total number processed: 1
    gpg:               imported: 1
    gpg: no ultimately trusted keys found

Ahora hemos recibido una llave pública para una entidad conocida como
"Sebastian Bergmann <sb@sebastian-bergmann.de>". Sin embargo, no tenemos
manera de verificar que esta llave fue creada por la persona conocida como
Sebastian Bergmann. Pero, intentemos verificar nuevamente la firma del lanzamiento.

.. parsed-literal::

    $ gpg phpunit-|version|.phar.asc
    gpg: Signature made Sat 19 Jul 2014 01:28:02 PM CEST using RSA key ID 6372C20A
    gpg: Good signature from "Sebastian Bergmann <sb@sebastian-bergmann.de>"
    gpg:                 aka "Sebastian Bergmann <sebastian@php.net>"
    gpg:                 aka "Sebastian Bergmann <sebastian@thephp.cc>"
    gpg:                 aka "Sebastian Bergmann <sebastian@phpunit.de>"
    gpg:                 aka "Sebastian Bergmann <sebastian.bergmann@thephp.cc>"
    gpg:                 aka "[jpeg image of size 40635]"
    gpg: WARNING: This key is not certified with a trusted signature!
    gpg:          There is no indication that the signature belongs to the owner.
    Primary key fingerprint: D840 6D0D 8294 7747 2937  7831 4AA3 9408 6372 C20A

En este punto, la firma es buena pero no confiamos en esta llave. Una firma
buena significa que el archivo no ha sido manipulado. Sin embargo, debido a la
naturaleza de la criptografía de llave pública, también necesitas verificar que
la llave ``6372C20A`` fue creada por el auténtico Sebastian Bergmann.

Cualquier atacante puede crear una llave pública y subirla a los servidores
de llave pública. Entonces, ellos puede crear versiones maliciosas firmadas
con esta llave falsa. Si intentas verificar la firma de esta versión
corrupta, va a resultar exitosa a pesar de que la llave no era la llave «auténtica».
Por lo tanto, necesitas validar la autenticidad de esta llave. Sin embargo, validar la
autenticidad de una clave pública está fuera del ámbito de esta documentación.

La verificación manual de la autenticidad e integridad del archivo PHAR
de PHPUnit usando GPG es tedioso. Este es el objetivo detrás de la creación
PHIVE (PHAR Installation and Verification Environment). Se puede leer sobre
PHIVE en su `página web <https://phar.io/>`_

.. _installation.composer:

Composer
########

Si usas `Composer <https://getcomposer.org/>`_ para gestionar las dependencias
de tu proyecto simplemente agrega ``phpunit/phpunit`` como dependencia (en modo de desarrollo)
en el archivo ``composer.json`` del proyecto:

.. parsed-literal::

    composer require --dev phpunit/phpunit ^\ |version|

.. _installation.global:

Instalación Global
##################

Ten en cuenta que no se recomienda instalar PHPUnit globalmente, por ejemplo,
``/usr/bin/phpunit`` o ``/usr/local/bin/phpunit``.

Por el contrario, PHPUnit debe ser gestionado como una dependencia local del
proyecto.

Las dos formas más comunes de hacer esto es, o colocando el archivo PHAR con
la versión especifica de PHPUnit que necesitamos en la carpeta ``tools`` del
proyecto (que es gestionada por PHIVE) o, en el caso de que usemos Composer,
agregando la versión especifica de PHPUnit que necesitamos en el archivo
``composer.json`` del proyecto.

Webserver
#########

PHPUnit es un *framework* para escribir pruebas y posee una herramienta en
línea de comandos para ejecutarlas. Escribir y ejecutar pruebas es una actividad
de la etapa de desarrollo. No hay razón para instalar PHPUnit en un servidor
web.

**Si subimos PHPUnit a un servidor web nuestro proceso de desarrollo estará roto.
Además, si nuestra carpeta** ``vendor`` **es accesible a través de nuestro
servidor web entonces nuestro proceso de desarrollo también estará roto**.

Observemos que si subimos PHPUnit a un servidor web «cosas malas» pueden pasar.
`Estás advertido. <https://thephp.cc/news/2020/02/phpunit-a-security-risk>`_
