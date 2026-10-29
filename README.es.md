# Laboratorio de OpenTofu para el aula: infraestructura como código con plan, apply y state

[English](README.md) | [Français](README.fr.md) | [日本語](README.ja.md) | [Español](README.es.md) | [Deutsch](README.de.md)

[![check](https://github.com/mitsurutech/lab-tofu-lab/actions/workflows/check.yml/badge.svg)](https://github.com/mitsurutech/lab-tofu-lab/actions/workflows/check.yml)

Un **laboratorio de OpenTofu** listo para usar para enseñar infraestructura como código, de la [plataforma de aprendizaje MitsuruTech](https://mitsurutech.co.uk/es/learning), donde los centros educativos dan a cada alumno sus propias máquinas y su terminal en el navegador. Cada alumno recibe una **VM** de Ubuntu con **OpenTofu** instalado y tres carpetas de archivos al estilo Terraform. Escribe un pequeño proyecto con **variables**, **salidas** (outputs), **count** y **for_each**, lee un **plan** tras un cambio, mira el **state** y **arregla un proyecto roto**. No se instala nada en el ordenador del alumno, y OpenTofu es libre y gratuito.

## Qué hace el alumno

El laboratorio deja tres carpetas en `~/tofu-lab` y un `README.md` en la carpeta personal que explica qué hacer en seis partes y no da ninguna respuesta:

1. `hello`: un primer `tofu init`, `tofu plan` y `tofu apply`, y el archivo de estado que queda.
2. `hello`: añadir una variable, cambiar la salida y ver si un cambio es una actualización o un reemplazo.
3. `campus`: escribir un proyecto desde cero. Tres variables con valor por defecto, un recurso `terraform_data` por cada nombre de servidor creado con `for_each`, uno por cada aula creado con `count`, un recurso de resumen que lee los dos y dos salidas. `tofu fmt` no debe cambiar los archivos.
4. `campus`: cambiar la lista de servidores y decir, solo con el plan, qué se destruirá, qué se creará y qué se dejará igual.
5. `hello`: leer el state con `tofu state list`, borrarlo y ver cómo el plan quiere crearlo todo de nuevo.
6. `fix`: un proyecto con tres errores (un valor del tipo equivocado, una variable que no existe, dos recursos que se esperan el uno al otro para siempre), que se encuentran con `tofu validate`. Cuando está arreglado, `tofu apply -var pods=7` debe informar de 7 pods.

Una lección básica de la plataforma, OpenTofu Basics, también se trabaja en este laboratorio.

## Cómo se califica

El laboratorio tiene una **comprobación**, un script que califica una VM entregada sin que el profesor tenga que leerla. Se ejecuta sobre una copia de la máquina guardada, tras un arranque limpio. Copia las carpetas `campus` y `fix` del alumno a otro sitio, de modo que su propio state no cuenta, y rechaza cualquier proveedor (provider) o provisioner. Ejecuta `tofu validate` y `tofu fmt -check`. Para `campus`, después **elige valores que nadie ha visto** (un nombre de sitio aleatorio, sus propios servidores y aulas), hace plan y apply, y revisa el state: un servidor por cada nombre con el sitio y el nombre como entrada, las aulas numeradas desde uno, una salida `summary` que lee un recurso en lugar de estar escrita a mano, y la salida `server_names`. Quita un servidor y añade dos, vuelve a hacer el plan y exige que solo se destruya el primero y solo se creen los dos. Aplica, exige que no quede nada por cambiar y destruye. Por último, `fix` tiene que validar, aplicarse con `pods=7`, conservar sus tres recursos y seguir la variable. La primera línea que imprime dice qué no está bien.

Lo que no hace: nunca llega a una nube real, porque desde el laboratorio no se puede llegar a ninguna, y un alumno con root en su propia VM aún podría manipular el propio OpenTofu. Es una comprobación para el aula, no una prueba.

## Qué contiene el archivo

`tofu-lab.lab.yaml` es una **definición de laboratorio de VM** completa, exactamente lo que ejecuta la plataforma:

| Clave | Qué es |
| --- | --- |
| `cpus`, `memory`, `disk` | el tamaño de la VM de cada alumno: 1 CPU, 1024 MB y 10 GB |
| `open` | los puertos que se abren en el navegador: ninguno, el laboratorio es el terminal |
| `setup` | un script que se ejecuta una vez, como root, la primera vez que arranca la VM de cada alumno: instala algunas herramientas del archivo de Ubuntu, descarga OpenTofu 1.13.2 de su versión publicada en GitHub, lo comprueba con un SHA-256 fijo y lo instala; después escribe las tres carpetas, la herramienta `tofulab` (`sudo tofulab install` y `sudo tofulab reset`) y el README |
| `check` | el script de calificación, que se ejecuta como root sobre una copia del disco guardado tras un arranque limpio; su estado de salida es el resultado y `0` significa aprobado |

## Usarlo en la plataforma

**Para ponerlo como tarea a una clase:**

1. En su módulo, añada una tarea y elija la tarjeta **Laboratorio de VM Ubuntu**.
2. Pulse el ejemplo de OpenTofu, o pegue el contenido de `tofu-lab.lab.yaml`. Usar un ejemplo reemplaza el texto, y Deshacer recupera el suyo.
3. Publique y pruebe primero su propia copia. El primer arranque tarda un minuto o dos mientras se descarga OpenTofu. Cada alumno tiene su propia VM, que se conserva entre sesiones.
4. Asígnelo como cualquier tarea. Cuando se entregue, ejecute la comprobación en la fila de un alumno de la lista de la clase: arranca una copia de su máquina guardada, que no se vuelve a guardar, y muestra si ha pasado y qué ha impreso.

La [página de este laboratorio](https://mitsurutech.co.uk/es/help/lab-tofu-lab) del manual, su [guía de laboratorios de VM Ubuntu](https://mitsurutech.co.uk/es/help/labs) y su [página de laboratorios de ejemplo](https://mitsurutech.co.uk/es/help/sample-labs) cuentan el resto.

¿Aún no tiene una cuenta para su centro? [El primer año es gratis](https://mitsurutech.co.uk/es/pricing) hasta 50 alumnos, y [registrarse](https://mitsurutech.co.uk/es/signup) solo pide una dirección de correo.

## Comprobar el archivo

En cada push, `.github/workflows/check.yml` comprueba que el archivo es un YAML válido con las cinco claves de arriba y que `setup` y `check` caben cada uno en el límite de 16 KB de la plataforma. No arranca ninguna VM: eso se hace en las máquinas de la plataforma.

## Lo que no funciona en un laboratorio

La VM llega al archivo de paquetes de Ubuntu, PyPI, npm y GitHub, y a nada más. Por eso **no se puede descargar ningún proveedor y nunca se crea nada real**: AWS, Docker y los demás vienen de un registro al que la VM no llega. El laboratorio usa el único recurso integrado en OpenTofu, `terraform_data`, que solo guarda un valor y lo devuelve; las variables, las salidas, `count`, `for_each`, las dependencias, plan, apply, destroy y el state son totalmente reales. El README del alumno muestra el bloque de recurso de un proveedor como texto para leer, y el laboratorio nunca lo ejecuta. El propio OpenTofu se descarga de GitHub, adonde la VM de un alumno llega solo con una cuenta gratuita: la VM de un simple visitante no puede instalarlo, y entonces la comprobación dice que falta OpenTofu.

## Acerca de

Hecho por [Mitsuru Technologies](https://mitsurutech.co.uk/es) para la [plataforma de aprendizaje MitsuruTech](https://mitsurutech.co.uk/es/learning): Python, Java, Go, Node, SQL y Linux en el navegador para escuelas y centros educativos, con laboratorios de contenedores, laboratorios de VM, aplicaciones web desplegadas desde GitHub y cuestionarios que se corrigen solos. Pruebe código sin cuenta en el [entorno de pruebas](https://mitsurutech.co.uk/es/playground).

Más laboratorios del mismo sitio: [lab-netlab](https://github.com/mitsurutech/lab-netlab), [lab-juice-shop](https://github.com/mitsurutech/lab-juice-shop), [lab-dvwa](https://github.com/mitsurutech/lab-dvwa), [lab-webgoat](https://github.com/mitsurutech/lab-webgoat), [lab-mysql-phpmyadmin](https://github.com/mitsurutech/lab-mysql-phpmyadmin), [lab-git-gitea](https://github.com/mitsurutech/lab-git-gitea), [lab-sample](https://github.com/mitsurutech/lab-sample), [lab-sdn-lab](https://github.com/mitsurutech/lab-sdn-lab), [lab-reverse-lab](https://github.com/mitsurutech/lab-reverse-lab), [lab-formal-lab](https://github.com/mitsurutech/lab-formal-lab), [lab-hdl-lab](https://github.com/mitsurutech/lab-hdl-lab), [lab-k3s-lab](https://github.com/mitsurutech/lab-k3s-lab), [lab-ansible-roles](https://github.com/mitsurutech/lab-ansible-roles) y [lab-ansible-basics](https://github.com/mitsurutech/lab-ansible-basics).

## Licencia

Esta obra está bajo la licencia [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/). Puede compartirla y adaptarla para aprender y enseñar, siempre que reconozca a Mitsuru Technologies Ltd, no la use con fines comerciales y comparta lo que cree a partir de ella con la misma licencia. El texto completo está en el archivo `LICENSE`.

[OpenTofu](https://opentofu.org) (MPL-2.0) se descarga de la versión publicada por el propio proyecto en GitHub cuando arranca una VM; aquí no se distribuye nada de él.
