# Flujo de trabajo en proyectos Web

## Estructura de proyectos locales

Tener una estructura estándarizada para proyectos locales, ayuda a que se realicen la menor cantidad de cambios posibles (o se anulen) para que cada miembro del equipo pueda trabajar de una manera tal, que no tenga que preocuparse por realizar cambios para adecuar su estrcutura a la de los demás. 

### Servidor local

Una parte fundamental de la estructura y estandarización de proyectos es, tener una misma estructura de URL's para el trabajo en servidores local, una estructura de URL's puede ser la siguiente para que todos los miembros del equipo la cumplan, aunque esto depende de una conciliación entre todos los miembros del mismo. 

~~~
✔️ http://localhost/agencia/project
❌ http://localhost/pr/project
❌ http://localhost/sites/project
❌ http://localhost/project
❌ http://project
~~~

En la medida de lo posible se debería intentar usar el protocolo _**SSL**_, esto debido a que en la actualidad la mayoría de las *URL*'s en Internet usan este protocolo, y muchas veces puede ser problematico el cambiar de un protocolo a otro, una ayuda para crear servidores locales seguros es agregar un certificado autofirmado, [mkcert](https://github.com/FiloSottile/mkcert) puede ayudar en esta tarea. 

### Bases de datos

El uso de base de datos que se propone y que es en su mayoría el común en el desarrollo de softare web es *MySQL/MariaDB*. El uso de esta o cualquier base de datos se debe estandarizar para todos los proyectos, una sugerencia es usar nombres descriptivos y/o alusivos al proyecto:

- Bases de datos tal que: `Cliente/Proyecto`
- Prefijos de tabla estandarizado a todos los proyectos `Proyecto/Cliente_tabla`
- Usuario y contraseña estandarizado al proyecto


## Uso de Git

El uso correcto de ramas en git puede ayudar a mejorar el flujo de trabajo, esto se logra cuando cada desarrollador tiene una rama especifica para desarrollar las características que le fueron asignadas, de esta forma cada miembro del proyecto pueden trabajar sin temor a interferir o arruinar el trabajo de los demás, para posteriormente integrar cada rama a la rama principal. Un recurso para aprender a usar _Git_ (y en el que se basa esta sección) y todas sus posibilidades es el [libro Git](https://git-scm.com/book/es/v2).

Para trabajar en _GIT_, se hace principalmente desde la consola del sistema operativo, aunque también existen diversos [clientes gráficos](https://git-scm.com/downloads/guis)


### Iniciar un repositiorio
La forma más sencilla de comenzar a trabajar con _GIT_ es, crear un directorio nuevo y dentro de el inicializarlo de la siguiente manera:
~~~bash
$ mkdir project
$ cd project
$ git init
~~~

### Ver los archivos modificados
Cuando comenzámos a trabajar con _GIT_, es posible poder ver los archivos que han sido modificacos, agregados o eliminados
~~~bash
$ git status
~~~

### Ver cambios en los archivos
Al estar trabajando, es posible poder ver los cambios que hemos realizados en un archivo, tanto como lo que se ha agregado como eliminado
~~~bash
$ git diff
~~~

### Agregar archivos al área de preparación
~~~bash
$ git add FILE
#Agrega todos los archivos modificados
$ git add .
~~~

### Confirmar los archivos agregados
Después de agregar archivos al área de _"stage"_ (preparación), uno de los siguientes pasos fundamentales es confirmar los cambios.
~~~bash
#Esto confirmará los cambios y abrirá el editor predeterminado
#para escribir un mensaje o descripción acerca de los cambios realizados
$ git commit 

#Podemos confirmar los cambios y escribir un mensaje en la misma lína
$ git commit -m "Cambios realizados"

#Si ya hemos agregado anteriormente los archivos trabajados al área de preparación
#podemos ejecutar el siguiente comando para no tener agregarlos en cada modificación
$ git commit -a -m "Cambios realizados"
~~~

### Ver logs del último commit o cambiarlo
~~~bash
#Ver el último commit agregado y sus cambios
#podemos cambiar -1 por el número de commits que deseemos ver
$ git log -p -1

#Ver descripciones de los cambios en los commits
$ git log --oneline

#Ver los cambios al código utilizando el número de commit
$ git show NumeroCommit

#Agregar archivos/cambios o cambiar el mensaje del último commit
$ git add FILES
$ git commit --amend
~~~

### Sacar archivos del área de preparación
~~~bash
$ git reset HEAD FILE
# o
$ git restore --staged FILE
~~~

### Deshacer cambios a un archivo
~~~bash
$ git checkout -- FILE
# Deshacer todos los cambios de los archivos trabajados
$ git checkout .
~~~

### Etiquetado de versiones
~~~bash
#Etiqueta anotada, información completa sobre la versión
#Omitir "-m" para abrir el editor y poder agregar más información
$ git tag -a v0.0.1 -m "Mensaje sobre la versión"

#Etiqueta ligera, referencia simple al commit
# git tag v0.0.1-l

#Etiquetado tardío
#Para agregar una etiqueta a un commit anterior
$ git tag -a v0.0.2 -m "Mensaje sobre la versión" COMMIT_CHECKSUM

#Mostrar etiquetas
$ git tag
#Mostrar etiqueta especifica
$ git show tag
~~~

### Envíando etiquetas al servidor
Por defecto git no transfiere las etiquetas al hacer un push, hay que hacerlo explicitamente.
~~~bash
#Enviar etiqueta
$ git push origin v0.0.1
#Enviar todas las etiquetas
$ git push origin --tags
~~~

### Borrando etiquetas
Por defecto git no borrará las etiquetas en elservidor, por lo que también debe hacerse explicitamente
~~~bash
#Borrar etiqueta local
$ git tag -d v0.0.1
#Borrar etiqueta remota
$ git push --delete origin v0.0.1
~~~

### Ramas
Las ramas en _GIT_ nos dan la oportunidad de poder trabajar con una "copia" del repositorio sin tener que modificar el origanl
~~~bash
#Agregar nueva rama
$ git branch development
#Cambiar a la nueva rama
$ git checkout development
#Agrega nueva rama y cambia a ella
$ git checkout -b development
~~~

### Agregar una nueva rama al servidor remoto
Si deseamos trabajar con una rama que hayamos creado en otra computadora, esto es posible si la subimos al servidor de origen
~~~bash
#Cambiamos a la rama "development"
$ git checkout development
#Subimos la rama actual al servidor
$ git push origin development
~~~

### Borrado de ramas
Una rama pude ser borrada cuando se ha terminado de trabajar en ella
~~~bash
#Borrar rama localmente
$ git branch -d development
#Borrar rama local con cambios sin confirmar
$ git branch -d -f development
#o
$ git branch -D development
#Borrar rama remotamente
$ git push origin --delete development
~~~

## Haciéndo deploy a un servidor FTP desde Bitbucket

Para cuestiones de integración continua, es posible hacer un `deploy` de la rama principal cada que esta es actualizada (push) a un servidor FTP, para que de esta manera, el servidor de producción o pruebas siempre esté actualizado. Las variables de entorno deben ser configuradas en las opciones del repositorio. El siguiente código se ejecuta solamente cuando se actualiza la rama master y debe ser puesto en la raíz del proyecto en un archivo llamado `bitbucket-pipelines.yml`:
~~~yaml
image: debian
pipelines:
  branches:
    master:
      - step:
          name: Deploy
          deployment: production
          script:
            - apt-get -qq install git-ftp
            - git config git-ftp.url ftp://$SERVER/htdocs/
            - git config git-ftp.user $USER
            - git config git-ftp.password $FTP_PASSWORD
            - git ftp push          	    
# La primera vez que se ejecute el deploy debe usarse "git ftp init" en lugar de "git ftp push"
~~~

## Buenas prácticas 

El uso de buenas prácticas en los proyectos de desarrollo web, nos ayudan enormemente a tener bien estructurado el código, con lo cual ayuda a la legibilidad del mismo, a la rápidez para identificar que hace cada línea, así como para depurar de una manera más eficaz. 

### HTML

_HTML_ es el corazón de la web, no es solamente sobre lo cual estructuramos páginas y sitios de Internet, sino que últimamente ha cobrado mucha importancia con su última actualización (_HTML5_), la cual hace énfasis en la estructura semántica, la cual contribuye ampliamente a la accesibilidad para lectores de pantalla y al indexado en motores de búsqueda. Es por todo esto que se recomienda la siguiente guia de estilos.

### Identación y escritura del código
En general, y no solamente para _HTML_, debería usarse una identación de dos espacios, no combinandose estos en un tabulador, esto para que sea más sencillo manejar el código. Tampoco debería mezclarse tabuladores con espacios. En HTML todas las etiquetas y atributos deben escribirse en minúscilas, aunque es permitido el uso de mayúsculas, esto es descanosejado. 

#### Declaración de tipo de dcumento y header.

Usar estos aspectos es de suma importancia, ya que mediante estos declaramos el tipo de documento que usarémos y los metadatos y utilidades a usar. Aunque el uso de "header" puede ser omitido, esto es desaconsejado. La etiqueta "title", que da título a los ducmentos _HTML_, es obligatoria
~~~html
<!doctype html> <!--declaración del tipo-->
<header> <!--declaración del header-->
	<title>Título del documento</title>
</header>
~~~

#### Declaración de la codificación de caracteres y responsividad


### CSS

CSS tiene diversos métodos de estructuración (OOCSS, ITCSS, BEM), aunque el más popular es BEM, que es el que se propone como método de trabajo.

Uso de estructuración mediante *BEM* (Bloque, Elemento, Modificador) y buenas prácticas para el código CSS.

~~~css
/*Selectores multiples en diferentes líneas*/
.button,
.button:hover,
.button:active {}

/*Uso de colores hexadecimales abreviados cuando sea posible*/
.color {
    color: #444;
}

/*Valores hexadecimales en minúsculas*/
.background {
    background-color: #4c5f3d;
}

/*URL's en comillas simples*/
@import('http://resource.com');

/*Valores sin unidades de medida cuando este sea 0*/
.margins {
    margin: 0 1rem 2rem 0;
}
~~~

Hay que recordar que la metodología *BEM* desaconseja sub elementos, por lo tanto, hay que tener muy en consideración todo aquello que puede ser un bloque y cuales deben ser sus elementos:

~~~html
<nav class="navbar">
	<ul class="menu">
        <li class="menu__item">
            <!- Incorrecto -->
        	<a class="menu__item__link" href="#!">Link</a>
            <!- Correcto -->
        	<a class="menu__link" href="#!">Link</a>
        </li>
    </ul>
</nav>
~~~



### Javascript

La codificación en Javascript se puede llevar a cabo de diversas maneras, hay muchos métodos de buenas prácticas y estructuración, la que se propone en este caso, es una de las más famosas y conocidas en el desarrollo web, que es la [guía de estilos para Javascript](https://github.com/paolocarrasco/javascript-style-guide)  de _Airbnb_.


### React

En el caso de uso de React para desarrollo web, _Airbnb_ también posee una [ guía de estilos para React/JSX](https://github.com/agrcrobles/javascript/tree/master/react).

### Documentación de funciones

Es muy importante el poder documentar las funciones y/o métodos usados en Javascript, React o cualquier lenguaje de programación, esto con la finalidad de no solamente tener claro que hace cada cosa, sino también para recordar en caso de que se deje el código durante mucho tiempo o si se integra un nuevo miembro al equipo, saber el funcionamiento de cada función, método o clase. 

Hay un estandar común para la documentación el cual es el siguiente:

~~~javascript
/** 
 * functionName
 * @param  {string} 	paramName
 * @param  {int} 		paramName
 * @return {component}	value returned
 */
function sayHello(name, age) {
    console.log(`Hello ${name} you are ${age} yo!`);
    //No value returned, then undefined or none.
}

~~~




