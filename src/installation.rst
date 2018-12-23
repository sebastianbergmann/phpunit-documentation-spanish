

.. _installation:

==================
Instalar PHPUnit
==================

.. _installation.requirements:

Requisitos
##########

PHPUnit 7.0 requiere PHP 7.1. Se recomienda encarecidamente usar la última versión de PHP.

PHPUnit requiere las extensiones `dom <http://php.net/manual/es/dom.setup.php>`_
y `json <http://php.net/manual/es/json.installation.php>`_ que normalmente están
habilitadas por defecto.

Además, PHPUnit requiere las extensiones `pcre <http://php.net/manual/es/pcre.installation.php>`_,
`reflection <http://php.net/manual/es/reflection.installation.php>`_,
y `spl <http://php.net/manual/es/spl.installation.php>`_.
Estas extensiones estándares se habilitan por defecto y no se pueden desactivar
sin parchear el sistema PHP construido y/o las fuentes en C.

La característica de reporte de cobertura de código requiere las extensiones
`Xdebug <http://xdebug.org/>`_ (2.5.0 o superior) y
`tokenizer <http://php.net/manual/es/tokenizer.installation.php>`_.
La generación de reportes en XML requiere la extensión
`xmlwriter <http://php.net/manual/es/xmlwriter.installation.php>`_.

.. _installation.phar:

PHP Archive (PHAR)
##################

La manera más fácil de obtener PHPUnit es descargar un `PHP Archive (PHAR) <http://php.net/phar>`_ que contiene todas
las dependencias requeridas (así como también algunas opcionales) de PHPUnit empaquetadas en un único archivo.

La extensión `phar <http://php.net/manual/es/phar.installation.php>`_
se necesita para usar PHP Archives (PHAR).

Si la extensión `Suhosin <http://suhosin.org/>`_ está habilitada,
necesitas permitir la ejecución de archivos PHAR en tu
``php.ini``:

.. code-block:: bash

    suhosin.executor.include.whitelist = phar

Para instalar globalmente el PHAR:

.. code-block:: bash

    $  wget https://phar.phpunit.de/phpunit-7.0.phar
    $  chmod +x phpunit-7.0.phar
    $  sudo mv phpunit-7.0.phar /usr/local/bin/phpunit
    $  phpunit --version
    PHPUnit x.y.z by Sebastian Bergmann and contributors.

También podrías usar el archivo PHAR descargado directamente:

.. code-block:: bash

    $  wget https://phar.phpunit.de/phpunit-7.0.phar
    $  php phpunit-7.0.phar --version
    PHPUnit x.y.z by Sebastian Bergmann and contributors.

.. _installation.phar.windows:

Windows
=======

Instalar globalmente el PHAR involucra el mismo procedimiento manual de
`instalar Composer en Windows <https://getcomposer.org/doc/00-intro.md#installation-windows>`_:

#.

   Crea un directorio para binarios PHP; e.g., :file:`C:\\bin`

#.

   Añade ;C:\bin a tu variable de entorno ``PATH``
   (`ayuda relacionada <http://stackoverflow.com/questions/6318156/adding-python-path-on-windows-7>`_)

#.

   Descarga `<https://phar.phpunit.de/phpunit-7.0.phar>`_ y
   guarda el archivo como :file:`C:\\bin\\phpunit.phar`

#.

   Abre una línea de comandos (e.g.,
   presiona :kbd:`Windows`:kbd:`R`
   » escribe cmd
   » :kbd:`ENTER`)

#.

   Crea un script batch envolvente (resultando en
   :file:`C:\\bin\\phpunit.cmd`):

   .. code-block:: bash

       C:\Users\username>  cd C:\bin
       C:\bin>  echo @php "%~dp0phpunit.phar" %* > phpunit.cmd
       C:\bin>  exit

#.

   Abre una nueva línea de comando y confirma que puedes ejecutar PHPUnit
   desde cualquier ruta:

   .. code-block:: bash

       C:\Users\username>  phpunit --version
       PHPUnit x.y.z by Sebastian Bergmann and contributors.

Para los entornos shell Cygwin y/o MingW32 (e.g., TortoiseGit), podrías
saltarte el paso 5. de arriba: simplemente guarda el archivo como
:file:`phpunit` (sin la extensión :file:`.phar`)
y hazla ejecutable usando
``chmod 775 phpunit``.

.. _installation.phar.verification:

Verificar Publicaciones de PHPUnit PHAR
=======================================

Todas las publicaciones de código distribuído por el Proyecto PHPUnit
están firmadas por el release manager para la versión. Firmas 
PGP y SHA1 están disponibles para verificación en `phar.phpunit.de <https://phar.phpunit.de/>`_.

Los siguientes ejemplos detallan cómo funciona la verificación. Iniciemos por
descargar el archivo :file:`phpunit.phar` así como también su
firma PGP desprendida :file:`phpunit.phar.asc`:

.. code-block:: bash

    wget https://phar.phpunit.de/phpunit.phar
    wget https://phar.phpunit.de/phpunit.phar.asc

Queremos verificar el archivo PHAR de PHPUnit (:file:`phpunit.phar`)
contra su firma desprendida (:file:`phpunit.phar.asc`):

.. code-block:: bash

    gpg phpunit.phar.asc
    gpg: Signature made Sat 19 Jul 2014 01:28:02 PM CEST using RSA key ID 6372C20A
    gpg: Can't check signature: public key not found

No tenemos la clave pública del release manager (``6372C20A``) en nuestro sistema 
local. Para proceder con la verificación mecesitamos recuperar la clave pública 
del release manager desde un servidor de claves. Uno de esos servidores 
es :file:`pgp.uni-mainz.de`. Los servidores de clave pública están enlazados juntos, 
así que deberías ser capaz de conectarte a cualquier servidor de claves.

.. code-block:: bash

    gpg --keyserver pgp.uni-mainz.de --recv-keys 0x4AA394086372C20A
    gpg: requesting key 6372C20A from hkp server pgp.uni-mainz.de
    gpg: key 6372C20A: public key "Sebastian Bergmann <sb@sebastian-bergmann.de>" imported
    gpg: Total number processed: 1
    gpg:               imported: 1  (RSA: 1)

Ahora hemos recibido una clave pública para una entidad conocida como 
"Sebastian Bergmann <sb@sebastian-bergmann.de>". Sin embargo, no tenemos una maner de
verificar que esta clave fue creada por la persona conocida como Sebastian
Bergmann. Pero, intentemos verificar la firma de la versión nuevamente.

.. code-block:: bash

    gpg phpunit.phar.asc
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

En este punto, la firma es buena pero no confiamos en esta clave. Una firma 
buena significa que el archivo no ha sido manipulado. Sin embargo, debido a la
naturaleza de la criptografía de clave pública, también necesitas verificar que
la clave ``6372C20A`` fue creada por el Sebastian Bergmann auténtico.

Cualquier atacante puede crear una clave pública y subirla a los servidores 
de clave pública. Ellos puede entonces crear versiones maliciosas firmadas
con esta clave falsa. Entonces, si intentas verificar la firma de esta publicación
corrupta, va a resultar exitosa debido a que la clave no era la clave "auténtica".
Por lo tanto, necesitas validar la autenticidad de esta clave. Aún así, validar la
autenticidad de una clave pública está fuera del ámbito de esta documentación.

Sería prudente crear un script de shell para manejar la instalación de 
PHPUnit que verifique la firma GnuPG antes de ejecutar tu test suite. 
Por ejemplo:

.. code-block:: bash

    #!/usr/bin/env bash
    clean=1 # Delete phpunit.phar after the tests are complete?
    aftercmd="php phpunit.phar --bootstrap bootstrap.php src/tests"
    gpg --fingerprint D8406D0D82947747293778314AA394086372C20A
    if [ $? -ne 0 ]; then
        echo -e "\033[33mDownloading PGP Public Key...\033[0m"
        gpg --recv-keys D8406D0D82947747293778314AA394086372C20A
        # Sebastian Bergmann <sb@sebastian-bergmann.de>
        gpg --fingerprint D8406D0D82947747293778314AA394086372C20A
        if [ $? -ne 0 ]; then
            echo -e "\033[31mCould not download PGP public key for verification\033[0m"
            exit
        fi
    fi

    if [ "$clean" -eq 1 ]; then
        # Let's clean them up, if they exist
        if [ -f phpunit.phar ]; then
            rm -f phpunit.phar
        fi
        if [ -f phpunit.phar.asc ]; then
            rm -f phpunit.phar.asc
        fi
    fi

    # Let's grab the latest release and its signature
    if [ ! -f phpunit.phar ]; then
        wget https://phar.phpunit.de/phpunit.phar
    fi
    if [ ! -f phpunit.phar.asc ]; then
        wget https://phar.phpunit.de/phpunit.phar.asc
    fi

    # Verify before running
    gpg --verify phpunit.phar.asc phpunit.phar
    if [ $? -eq 0 ]; then
        echo
        echo -e "\033[33mBegin Unit Testing\033[0m"
        # Run the testing suite
        `$after_cmd`
        # Cleanup
        if [ "$clean" -eq 1 ]; then
            echo -e "\033[32mCleaning Up!\033[0m"
            rm -f phpunit.phar
            rm -f phpunit.phar.asc
        fi
    else
        echo
        chmod -x phpunit.phar
        mv phpunit.phar /tmp/bad-phpunit.phar
        mv phpunit.phar.asc /tmp/bad-phpunit.phar.asc
        echo -e "\033[31mSignature did not match! PHPUnit has been moved to /tmp/bad-phpunit.phar\033[0m"
        exit 1
    fi

.. _installation.composer:

Composer
########

Si usas `Composer <https://getcomposer.org/>`_ para gestionar las dependencias de tu proyecto,
simplemente agrega una dependencia (en desarrollo) sobre ``phpunit/phpunit`` 
en el archivo ``composer.json`` del proyecto:

.. code-block:: bash

    composer require --dev phpunit/phpunit ^7.0

.. _installation.optional-packages:

Paquetes opcionales
###################

Los siguientes paquetes opcionales están disponibles:

``PHP_Invoker``

    Una clase utilitaria para invocar callables con un tiempo de espera. Este paquete se necesita
    para forzar tiempos de espera de prueba en el modo estricto.

    Este paquete se incluye con la distribución PHAR de PHPUnit. Puede ser instalado 
    via Composer usando el siguiente comando:

    .. code-block:: bash

        composer require --dev phpunit/php-invoker

``DbUnit``

    Porte de DbUnit a PHP/PHPUnit para dar soporte a pruebas con interacción con base de datos.
    
    Este paquete no está incluido en la distribución PHAR de PHPUnit. Puede ser instalado
    via Composer usando el siguiente comando

    .. code-block:: bash

        composer require --dev phpunit/dbunit
