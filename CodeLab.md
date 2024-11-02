author: Ángel Alberto Martínez Sánchez
summary: Resumen del CodeLab
id:Proyecto 1.2
categories: codelab,markdown
environments: Web
status: Published

# Seguridad del Arranque en Debian

# Índice

1. [Introducción](#introducción)
2. [Ocultación del Menú de Arranque](#ocultación-del-menú-de-arranque)
3. [Configuración de Contraseña de Arranque](#configuración-de-contraseña-de-arranque)
4. [Copia de Seguridad de la Configuración](#copia-de-seguridad-de-la-configuración)
5. [Otras Opciones de Seguridad](#otras-opciones-de-seguridad)	
6. [Conclusión](#conclusión)

## Introducción
La seguridad del arranque es un aspecto crítico en la administración de sistemas operativos, especialmente en entornos donde la protección de datos y la integridad del sistema son prioritarias. Un arranque seguro no solo previene accesos no autorizados, sino que también protege contra modificaciones maliciosas que podrían comprometer la estabilidad y la seguridad de todo el sistema.

Esta guía tiene como objetivo proporcionar un enfoque detallado para bastionar el proceso de arranque en un sistema Debian, abordando aspectos clave como la ocultación del menú de GRUB, la configuración de contraseñas, la creación de copias de seguridad y otras medidas de seguridad adicionales.



## Ocultación del Menú de Arranque

Ocultar el menú de GRUB  es esencial para prevenir que un atacante acceda a opciones de recuperación o modifique parámetros de arranque. Primero, abre una terminal y edita el archivo de configuración de GRUB con el siguiente comando:
	
	sudo nano /etc/default/grub

En este archivo, busca o añade las líneas “GRUB_TIMEOUT=0” y “GRUB_TIMEOUT_STYLE=hidden”. Esto significa que no habrá tiempo de espera para mostrar el menú y este estará oculto. Guarda los cambios presionando Ctrl+X, luego Y, y finalmente Enter.

Para aplicar estos cambios, actualiza la configuración de GRUB con:

	sudo update-grub

Reinicia tu sistema para verificar que el menú no se muestre.

![Descripción de la imagen](Imagen1.PNG)

## Configuración de Contraseña de Arranque.

Establecer una contraseña en GRUB es crucial para evitar modificaciones no autorizadas. Para hacerlo, primero genera un hash de la contraseña:

	grub-mkpasswd-pbkdf2

Introduce y confirma tu contraseña. El sistema generará un hash que deberás copiar.

![Descripción de la imagen](Imagen2.PNG)

A continuación, edita el archivo “/etc/grub.d/40_custom”:

	sudo nano /etc/grub.d/40_custom

Añade las siguientes líneas al final del archivo:

	set superusers=”admin”
	password_pbkdf2 admin [hash_generado]

Reemplaza “[hash_generado]” con el hash que obtuviste anteriormente. Guarda los cambios y cierra el editor.

Actualizar GRUB nuevamente con:
	
	sudo update-grub

Reinicia tu sistema para confirmar que la contraseña ahora es requerida para modificar las opciones de arranque.


![Descripción de la imagen](Imagen3.PNG)


## Copia de Seguridad de la Configuración.

Es fundamental tener copias de seguridad de la configuración del arranque para poder restaurarla en caso de problemas. Para crear una copia de seguridad, primero copia los archivos relevantes:

	sudo cp /boot/grub/grub.cfg /boot/grub/grub.cfg.backup
    sudo cp /etc/default/grub /etc/default/grub.backup

Luego, puedes crear un archivo comprimido que contenga todos los archivos importantes:

    sudo tar -czvf grub_config_backup.tar.gz /boot/grub/grub.cfg /etc/default/grub /etc/grub.d/

Asegúrate de mover esta copia a un lugar seguro, como un dispositivo externo o un servicio en la nube.

## Otras Opciones de Seguridad

Deshabilitar el Arranque desde Dispositivos Externos
Para evitar que un atacante inicie desde un dispositivo USB o similar, accede a la BIOS/UEFI durante el arranque (normalmente presionando F2, F12 o Del). Busca la sección donde puedes modificar el orden de arranque y desactiva o mueve los dispositivos externos al final de la lista. Guarda los cambios antes de salir.


### Activar Secure Boot

Si tu hardware lo permite, activa Secure Boot en la BIOS/UEFI. Esta opción previene que software no firmado arranque en tu sistema. Asegúrate de que Debian sea compatible con Secure Boot antes de activarlo.

### Cifrado de Disco Completo

Si estás instalando Debian por primera vez, selecciona la opción de cifrado completo durante la instalación. Si ya tienes Debian instalado, considera usar LUKS para cifrar particiones existentes. Este proceso es más complejo y puede requerir copias de seguridad previas.

## Conclusión

Implementar medidas adecuadas para bastionar el arranque en un sistema Debian es fundamental para proteger tanto los datos como la integridad del sistema operativo. Al ocultar el menú de GRUB, establecer contraseñas seguras y realizar copias de seguridad regulares, se puede mitigar significativamente el riesgo de accesos no autorizados y modificaciones maliciosas. Además, considerar opciones como deshabilitar el arranque desde dispositivos externos y activar Secure Boot proporciona capas adicionales de seguridad. Al seguir esta guía, estarás mejor preparado para mantener tu sistema Debian seguro y protegido frente a amenazas potenciales.
