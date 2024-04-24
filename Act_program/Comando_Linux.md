# Linea de comando Linux

En esta oportunidad, se trata de comprender y utilizar la línea de comandos de Linux, lo que es crucial para manipular eficientemente el sistema operativo subyacente, automatizar tareas repetitivas y gestionar recursos de manera eficiente. Además, facilita el desarrollo de habilidades para la escritura de scripts y la implementación de sistemas, siendo esencial en entornos de computación distribuida.

## ¿Qué es "the Shell"?
Un programa que recibe comandos del teclado y los envía al sistema operativo para su ejecución. Antiguamente era la única interfaz en sistemas tipo Unix. Hoy, coexisten con interfaces gráficas. En Linux, el shell principal es bash (Bourne Again SHell), una versión mejorada del shell original. Otros shells disponibles incluyen ksh, tcsh y zsh.
### Probando el teclado
Lo primero que deberíamos ver es un símbolo del shell que contiene nuestro nombre de usuario y el nombre de la máquina seguido de un signo de dólar. 
En esta oportunidad usaremos un simulador(https://cocalc.com/). Se ve como:
```bash
~$
```
Escribimos algunos caracteres sin sentido y presionamos la tecla Intro. Deberíamos haber recibido un mensaje de error
```bash
~$ asdfaf
bash: asdfaf: command not found
```
#### No estás operando como root, ¿verdad?
Si el indicador de shell termina en "#" en lugar de "$", estás operando como superusuario, lo que implica privilegios administrativos. Esto puede ser peligroso, ya que puedes alterar archivos críticos del sistema. A menos que sea estrictamente necesario, evita operar como superusuario para prevenir errores costosos o daños en el sistema.

## Navegación

En esta lección, presentaremos nuestros primeros tres comandos: pwd(imprimir directorio de trabajo), cd (cambiar directorio) y ls(listar archivos y directorios). 
### PWD
Entro de ese directorio, podemos ver sus archivos y la ruta a su directorio principal y los subdirectorios del directorio en el que nos encontramos.

```bash
~$ pwd
/home/user
```
La mayoría de los sistemas, el directorio de inicio se llamará /home/nombre_usuario, pero puede ser cualquier cosa según los caprichos del administrador del sistema.
Para enumerar los archivos en el directorio de trabajo, usamos el **ls** comando.
```bash
~$ ls
2024-04-24-file-1.term

#En otro terminal 
localhost:~$ ls
bench.py    hello.c     hello.js    readme.txt
```

### CD

Usamos el comando "cd", seguido de la ruta del directorio deseado. Las rutas pueden ser absolutas o relativas. Una ruta absoluta comienza desde el directorio raíz y sigue la estructura del árbol hasta el directorio deseado. Por ejemplo, "/usr/bin" representa el directorio "bin" dentro del directorio "usr", partiendo desde la raíz del sistema ("/").Comenzamos con:
```bash
~$  cd /usr/bin

/usr/bin$  pwd
/usr/bin

/usr/bin$ ls
 2to3-2.7                                    git-rscp                                 
 411toppm                                     git-scp                                 
 4ti2-circuits                                git-sed                                  
 4ti2-genmodel                                git-setup                              
 4ti2-gensymm                                 git-shell                               
 4ti2-graver                                  git-show-merged-branches                
 4ti2-groebner                                git-show-tree                           git-show-unmerged-branches,....etc.               
```
Ahora digamos que queremos cambiar el directorio de trabajo a /usr/bincuyo padre es /usr.

```bash
/usr/bin$ cd /usr/
/usr$ pwd
/usr
```

O, con una ruta de acceso relativa:

```bash
/usr$ cd ..
/$ pwd
/
```
Podemos cambiar el directorio de trabajo de **_/usr_** a **_/usr/bin_** de dos formas diferentes.

```bash
/$ cd /usr/bin
/usr/bin$ pwd
/usr/bin
/usr/bin$
```
Otra forma de ruta de acceso:
```bash
/$ cd ./bin
/bin$ pwd
/bin
```
En la mayoría de los casos, podemos omitir el "./".
```bash
/$ cd bin
/bin$ 
```
## Looking Around

Un directorio de trabajo a otro, haremos un recorrido por nuestro sistema Linux y, en el camino, aprenderemos algunas cosas sobre lo que lo motiva. Pero antes de comenzar, debemos conocer algunas herramientas. Como: ls(lista de archivos y directorios), less(ver archivos de texto) y file(clasificar el contenido de un archivo).

### ls
Se utiliza para enumerar el contenido de un directorio.Se puede utilizar de varias maneras diferentes.
Lista los archivos en el directorio de trabajo:
```bash
/$ ls
bin     data  etc  home  lib32  libx32  opt   sbin     srv  tmp  var
cocalc  dev   ext  lib   lib64  local   proc  secrets  sys  usr 
```
### ls /bin
Enumera los archivos en el /bin directorio.
```bash
/$ ls /bin
 2to3-2.7                                     git-rscp                                 pgmkernel
 411toppm                                     git-scp                                  pgmnoise
 4ti2-circuits                                git-sed                                  pgmnorm
 4ti2-genmodel                                git-setup                                pgmoil
 4ti2-gensymm                                 git-shell                                pgmramp
```
### Ls -l 
Enumera los archivos en directorio de trabajo en formato largo
```bash
/$ ls -l
total 16
drwxr-xr-x   1 root root 108618 Mar 28 09:28 bin
drwxr-xr-x   8 root root   4096 Apr 15 21:00 cocalc
drwxrwxrwt   2 root root     40 Apr 24 05:18 data
drwxr-xr-x   5 root root    360 Apr 24 05:18 dev
drwxr-xr-x   1 root root   5082 Mar 28 10:27 etc
drwxr-xr-x   1 root root    324 Apr  9 07:17 ext
drwxr-xr-x   1 root root   4096 Apr 24 05:18 home
drwxr-xr-x   1 root root  17628 Mar 28 09:09 lib
drwxr-xr-x   1 root root   1582 Jan 30 10:17 lib32
drwxr-xr-x   1 root root     40 Jan 30 10:17 lib64
drwxr-xr-x   1 root root   1674 Jan 30 10:17 libx32
drwxr-xr-x   2 root root   4096 Apr 15 20:40 local
drwxr-xr-x   1 root root     58 Dec 28  2022 opt
dr-xr-xr-x 913 root root      0 Apr 24 05:18 proc
drwxr-xr-x   1 root root   8242 Mar 28 09:13 sbin
drwxr-xr-x   4 root root   4096 Apr 24 05:18 secrets
drwxr-xr-x   1 root root      0 May 31  2022 srv
dr-xr-xr-x  13 root root      0 Apr 24 05:18 sys
drwxrwxrwt   4 root root     80 Apr 24 06:16 tmp
drwxr-xr-x   1 root root    190 Dec  8  2022 usr
drwxr-xr-x   1 root root    104 Jul 25  2022 var
```

### ls -l /etc /bin
Enumera los archivos en el /bin directorio y /etc directorio en formato largo:
```bash
~$ ls - /etc /bin
ls: cannot access '-': No such file or directory
/bin:
 2to3-2.7                                     git-rscp                                 pgmkernel
 411toppm                                     git-scp                                  pgmnoise
 4ti2-circuits                                git-sed                                  pgmnorm
 4ti2-genmodel                                git-setup                                pgmoil
/etc:
ImageMagick-6                  e2scrub.conf     joe                  openal             skel
LatexMk                        emacs            kernel               openmpi            slurm
ModemManager                   emboss           ld.so.cache          opt                snmp
```
### ls -la ..
Los archivos (aquellos con nombres que comienzan con un punto, normalmente están oculto) en el directorio principal al de trabajo en formato largo.

```bash
~$ ls -la ..
total 10
drwxr-xr-x 1 root root 4096 Apr 24 05:18 .
drwxr-xr-x 1 root root 4096 Apr 24 05:18 ..
drwxr-xr-x 4 user user   12 Apr 24 05:43 user
```

## Manipulación de Archivos
La tabla a continuación enumera algunos lugares interesantes para explorar. Para cada uno de los directorios enumerados a continuación, haz lo siguiente: Cambia al directorio, usó del ls para listar el contenido del directorio.Si hay un archivo interesante, usa el comando file para determinar su contenido. Para archivos de texto, usa less para verlos.

### CP
Progrma que copia archivos y directorios. Copia un único archivo: 

```bash
~$ cp file1 file2
```
Otra forma para que se pueda utilizar para copiar varios archivos a un directorio diferente:

```bash
~$ cp file... directory
```
### mv
Manipulación de archivos implica reubicar o renombrar archivos y directorios según sea necesario. Esto puede implicar mover archivos a diferentes directorios o cambiar sus nombres.

```bash
~$ mv filename1 filename2
```
Mover archivos a un directorio diferente: 
```bash
~$ mv file... directory
```
### rm
Sirve para eliminar archivos y directorios
```bash
~$ rm file...
```
Además, usando la opción recursiva(-r), tambien se puede usar para eliminar directorios:
```bash
~$ rm -r directory...
```
### mkdir
Se utiliza para crear directorios. Para usarlo, se escribe: 

```bash
~$ mkdir directory...
```

### tenemos otros comandos
Los archivos en el directorio de trabajo actual con nombres de terminen con los caracteres ".txt" a text_files.
```bash
~$ cp *.txt text_files
```
El subdirectorio dir1 y los archivos ".bank" en el directorio principal del trabajo actual a un directorio llamado dir2
```bash
~$ mv dir1 ../*.bak dir2
```
ELiminacion de todos los archivos actual que terminen con el caracter "~".
```bash
~$ rm *~
```

## Comando type
Es un shell incorporado que muestra el tipo de ejecución del shell.Ejemplo:
```bash
~$ type type
type is a shell builtin

~$ type ls
ls is aliased to `ls --color=auto`

~$ type cp
cp is hashed (/usr/bin/cp)
```

## Which
Es común en servidores grandes que en sistemas de escritorio. Para determinar la ubicación exacta de un ejecutable dado, se utiliza el comando 'which'. Ejemplo del comando y resultado:

```bash
~$ which ls
/usr/bin/ls
```

## help
Usamos el comando siguiendo del nombre del shell incorporado. Opcionalmente, podemos agregar la opción -m para cambiar el formato de salida.

```bash
~$ help -m cd
NAME
    cd - Change the shell working directory.

SYNOPSIS
    cd [-L|[-P [-e]] [-@]] [dir]

DESCRIPTION
    Change the shell working directory.
    
    Change the current directory to DIR.  The default DIR is the value of the
    HOME shell variable.
    
    The variable CDPATH defines the search path for the directory containing
    DIR.  Alternative directory names in CDPATH are separated by a colon (:).
    A null directory name is the same as the current directory.  If DIR begins
    with a slash (/), then CDPATH is not used.
    
    If the directory is not found, and the shell option `cdable_vars' is set,
    the word is assumed to be  a variable name.  If that variable has a value,
    its value is used for DIR.
    
    Options:
      -L        force symbolic links to be followed: resolve symbolic
                links in DIR after processing instances of `..'
      -P        use the physical directory structure without following
                symbolic links: resolve symbolic links in DIR before
                processing instances of `..'
      -e        if the -P option is supplied, and the current working
                directory cannot be determined successfully, exit with
                a non-zero status
      -@        on systems that support it, present a file with extended
                attributes as a directory containing the file attributes
    
    The default is to follow symbolic links, as if `-L' were specified.
    `..' is processed by removing the immediately previous pathname component
    back to a slash or the beginning of DIR.
    
    Exit Status:
    Returns 0 if the directory is changed, and if $PWD is set successfully when
    -P is used; non-zero otherwise.

SEE ALSO
    bash(1)

IMPLEMENTATION
    GNU bash, version 5.1.16(1)-release (x86_64-pc-linux-gnu)
    Copyright (C) 2020 Free Software Foundation, Inc.
    License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
```

## --help
Se muestra una descripción de la sintaxis y las opciones admitidas del comando.

```bash
~$ help -m cd
NAME
    cd - Change the shell working directory.

SYNOPSIS
    cd [-L|[-P [-e]] [-@]] [dir]

DESCRIPTION
    Change the shell working directory.
    
    Change the current directory to DIR.  The default DIR is the value of the
    HOME shell variable.
    
    The variable CDPATH defines the search path for the directory containing
    DIR.  Alternative directory names in CDPATH are separated by a colon (:).
    A null directory name is the same as the current directory.  If DIR begins
    with a slash (/), then CDPATH is not used.
    
    If the directory is not found, and the shell option `cdable_vars' is set,
    the word is assumed to be  a variable name.  If that variable has a value,
    its value is used for DIR.
    
    Options:
      -L        force symbolic links to be followed: resolve symbolic
                links in DIR after processing instances of `..'
      -P        use the physical directory structure without following
                symbolic links: resolve symbolic links in DIR before
                processing instances of `..'
      -e        if the -P option is supplied, and the current working
                directory cannot be determined successfully, exit with
                a non-zero status
      -@        on systems that support it, present a file with extended
                attributes as a directory containing the file attributes
    
    The default is to follow symbolic links, as if `-L' were specified.
    `..' is processed by removing the immediately previous pathname component
    back to a slash or the beginning of DIR.
    
    Exit Status:
    Returns 0 if the directory is changed, and if $PWD is set successfully when
    -P is used; non-zero otherwise.

SEE ALSO
    bash(1)

IMPLEMENTATION
    GNU bash, version 5.1.16(1)-release (x86_64-pc-linux-gnu)
    Copyright (C) 2020 Free Software Foundation, Inc.
    License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>

~$ mkdir --help
Usage: mkdir [OPTION]... DIRECTORY...
Create the DIRECTORY(ies), if they do not already exist.

Mandatory arguments to long options are mandatory for short options too.
  -m, --mode=MODE   set file mode (as in chmod), not a=rwx - umask
  -p, --parents     no error if existing, make parent directories as needed
  -v, --verbose     print a message for each created directory
  -Z                   set SELinux security context of each created directory
                         to the default type
      --context[=CTX]  like -Z, or if CTX is specified then set the SELinux
                         or SMACK security context to CTX
      --help     display this help and exit
      --version  output version information and exit

GNU coreutils online help: <https://www.gnu.org/software/coreutils/>
Report any translation bugs to <https://translationproject.org/team/>
Full documentation <https://www.gnu.org/software/coreutils/mkdir>
or available locally via: info '(coreutils) mkdir invocation'
```

## man
Las páginas de manual contienen el título, la sintaxis del comando, una descripción y las opciones disponibles.  Para ver la página de manual del comando 'ls', se usa 'man ls'. En sistemas Linux, 'man' utiliza 'less' para mostrar las páginas, permitiendo el uso de comandos familiares de 'less'.
```bash
~$ man ls
LS(1)                                                User Commands                                               LS(1)

NAME
       ls - list directory contents

SYNOPSIS
       ls [OPTION]... [FILE]...

DESCRIPTION
       List  information  about  the FILEs (the current directory by default).  Sort entries alphabetically if none of
       -cftuvSUX nor --sort is specified.

       Mandatory arguments to long options are mandatory for short options too.

       -a, --all
              do not ignore entries starting with .

       -A, --almost-all
              do not list implied . and ..

       --author
              with -l, print the author of each file

       -b, --escape
              print C-style escapes for nongraphic characters

       --block-size=SIZE
              with -l, scale sizes by SIZE when printing them; e.g., '--block-size=M'; see SIZE format below

       -B, --ignore-backups
              do not list implied entries ending with ~

       -c     with -lt: sort by, and show, ctime (time of last modification of file status information); with -l: show
              ctime and sort by name; otherwise: sort by ctime, newest first

       -C     list entries by columns

       --color[=WHEN]
              colorize the output; WHEN can be 'always' (default if omitted), 'auto', or 'never'; more info below, etc.
```
## Redirección de E/S

### Standard Output
El comando dirige su contenido a la pantalla, para redigir la salida estándar a un archivo con carácter ">".
```bash
~$ ls > file_list.txt
```
Fue redirigido al archivo, no apareciendo los resultados en la pantalla. Otra dorma seria ">>", los resultados se agregan al final del archivo que sea más largo cada vez que se repite el comando.
```bash
~$ ls >> file_list.txt
```
## Standard Input

El comando "sort" se utiliza para ordenar líneas de texto en un archivo de entrada y mostrar el resultado ordenado en la salida estándar.
```bash
~$ sort < file_list.txt
2024-04-24-file-1.term
2024-04-24-file-1.term
file_list.txt
file_list.txt

~$ sort < file_list.txt > sorted_file_list.txt 
```
El único requisito es que los operadores de redirección (los "<" y ">") deben aparecer después de las otras opciones y argumentos del comando.

# Pipelines
Esto se logra con el operador "|", lo que permite procesar y manipular datos de manera eficiente. Por ejemplo, al ejecutar "ls | less", la salida de "ls" se pasa como entrada a "less", permitiendo ver el resultado de "ls" página por página.
```bash
~$ ls -l | less
total 3
-rw-r--r-- 1 user user  0 Apr 24 07:04 2024-04-24-file-1.term
-rw-r--r-- 1 user user 74 Apr 24 07:29 file_list.txt
-rw-r--r-- 1 user user 74 Apr 24 07:38 sorted_file_list.txt
(END)
```

Otros comandos como para la muestra de una lista de directorios, ordenados de mayor a menor.
```bash
~$ du | sort -nr
698     .
488     ./.snapshots
202     ./.snapshots/2024-04-24-071220
201     ./.snapshots/2024-04-24-063522
79      ./.snapshots/2024-04-24-055843
7       ./.snapshots/2024-04-24-052151
2       ./.ssh
2       ./.snapshots/2024-04-24-071220/.ssh
2       ./.snapshots/2024-04-24-063522/.ssh
2       ./.snapshots/2024-04-24-055843/.ssh
2       ./.snapshots/2024-04-24-052151/.ssh
1       ./.snapshots/2024-04-24-071220/.snapshots
1       ./.snapshots/2024-04-24-063522/.snapshots
1       ./.snapshots/2024-04-24-055843/.snapshots
1       ./.snapshots/2024-04-24-052151/.snapshots
```

EL otro comando, muestra el número total de archivos de trabajo actual y todos sus subdirectorios.
```bash
~$ find . -type f -print | wc -l
35
```

## Expansión
El comando "echo" es un comando integrado en el shell que imprime su argumento de texto en la salida estándar.
```bash
~$ echo this is a test
this is a test
```
El shell expande el “*” a otra cosa (en este caso, los nombres de los archivos en el directorio de trabajo actual) antes de echoejecutar el comando.
```bash
~$ echo *
2024-04-24-file-1.term file_list.txt ort -nrdu sorted_file_list.txt
```
### Pathname Expansion
Utilizamos varios como: 

* Se utiliza para listar los archivos y directorios en el directorio actual
```bash
~$ ls
 2024-04-24-file-1.term   file_list.txt  'ort -nrdu'   sorted_file_list.txt
```
*  Imprime en la salida estándar todos los nombres de archivos y directorios en el directorio actual que comiencen con la letra "D".
```bash
~$ echo D*
D*
```
* Esta vez, se utiliza para hacer coincidir cualquier número de caracteres antes de la "s".
```bash
~$ echo *s
*s
```
tambien tenemos como ejemplo: 
```bash
~$ echo [[:upper:]]*
[[:upper:]]*
```
* Más allá de nuestro directorio de inicio:
```bash
~$ echo /usr/*/share
/usr/local/share
```

### Tilde expansion 
El carácter tilde ("~") se usa al principio de una palabra, se expande en el nombre del directorio de inicio del usuario especificado, o si no se especifica ningún usuario, en el directorio de inicio del usuario actual. 
```bash
~$ echo ~
/home/user

~$ echo ~foo
~foo
```
### Arithmetic Expansion
Utilizamos el comando "echo $" ára está forma de calcular:
```bash
~$ echo $((2 + 2))
4
~$ echo $(($((5**2)) * 3))
75
```
Tambien podemos poner texto:
```bash
~$ echo with $((5%2)) left over.
with 1 left over.
~$ echo Five divided by two equals $((5/2))
Five divided by two equals 2
```
### Brace Expansion 
En está nos permite crear múltiples cadenas de texto a partir de un patrón que contiene llaves. Esto es útil para crear listas de archivos o directorios de manera rápida y eficiente. 
```bash
~$ echo Front-{A,B,C}-Back
Front-A-Back Front-B-Back Front-C-Back
~$ echo Number_{1..5}
Number_1 Number_2 Number_3 Number_4 Number_5
~$ echo {Z..A}
Z Y X W V U T S R Q P O N M L K J I H G F E D C B A
~$ echo a{A{1,2},B{3,4}}b
aA1b aA2b aB3b aB4b
```
### Double Quotes
Excepto para los caracteres "$", "\", y "`". Esto evita la división de palabras y la expansión de rutas, pero permite la expansión de parámetros y la sustitución de comandos.
```bash
~$ ls -l two words.txt
ls: cannot access 'two': No such file or directory
ls: cannot access 'words.txt': No such file or directory
~$ ls -l "two words.txt"
ls: cannot access 'two words.txt': No such file or directory
~$ mv "two words.txt" two_words.txt
mv: cannot stat 'two words.txt': No such file or directory

~$ echo "$USER $((2+2)) $(cal)"
user 4      April 2024       
Su Mo Tu We Th Fr Sa  
    1  2  3  4  5  6  
 7  8  9 10 11 12 13  
14 15 16 17 18 19 20  
21 22 23 24 25 26 27  
28 29 30  
```
Otro ejemplo:
```bash
~$ echo this is a     test
this is a test
~$ echo "this is a     test"
this is a     test
~$ echo $(cal)
April 2024 Su Mo Tu We Th Fr Sa 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20 21 22 23 24 25 26 27 28 29 30
~$ echo "$(cal)"
     April 2024       
Su Mo Tu We Th Fr Sa  
    1  2  3  4  5  6  
 7  8  9 10 11 12 13  
14 15 16 17 18 19 20  
21 22 23 24 25 26 27  
28 29 30  
```

### Single Quotes
Podemos lograr precediéndolo con una barra invertida, conocida como carácter de escape. Usualmente se utiliza entre comillas dobles para evitar ciertas expansiones.
```bash
~$  echo "The balance for user $USER is: \$5.00"
The balance for user user is: $5.00
~$ mv bad\&filename good_filename
mv: cannot stat 'bad&filename': No such file or directory
```
## Convertirse en superusuario por un corto tiempo
Se úede utilizar en aquellos casos en los que necesita ser superusuario para un número reducido de tareas.

```bash
~$ sudo -i

╔═══════════════════════════════════════════════════════════════════╗
║  ⚠           STOP: YOUR ARE NOT AN ADMINISTRATOR               ⚠  ║
╠═══════════════════════════════════════════════════════════════════╣
║ It is not possible to gain elevated privileges – neither via      ║
║ sudo nor any other mechanism. CoCalc projects run in heavily      ║
║ restricted containers for security reasons!                       ║
╟───────────────────────────────────────────────────────────────────╢
║ However, you can become admin in a 𝗖𝗢𝗠𝗣𝗨𝗧𝗘 𝗦𝗘𝗥𝗩𝗘𝗥!                ║
║ Learn more here:       https://doc.cocalc.com/compute_server.html ║
╟───────────────────────────────────────────────────────────────────╢
║ In case you want to install some software:                        ║
║ Python: pip3 install --user [pkgname]                             ║
║         https://doc.cocalc.com/howto/install-python-lib.html      ║
║ Node:   npm [pkg] instead of sudo npm -g [pkg]                    ║
║ R:      open Terminal, run R, and then install.package(...)       ║
║         https://doc.cocalc.com/howto/install-r-package.html       ║
║ Build:  ./configure --prefix=~/.local && make && make install     ║
║ Hint: it might already be available: https://cocalc.com/software  ║
╟───────────────────────────────────────────────────────────────────╢
║ Support: help@cocalc.com – for global software install requests   ║
╚═══════════════════════════════════════════════════════════════════╝

~$ sudo some_command

╔═══════════════════════════════════════════════════════════════════╗
║  ⚠           STOP: YOUR ARE NOT AN ADMINISTRATOR               ⚠  ║
╠═══════════════════════════════════════════════════════════════════╣
║ It is not possible to gain elevated privileges – neither via      ║
║ sudo nor any other mechanism. CoCalc projects run in heavily      ║
║ restricted containers for security reasons!                       ║
╟───────────────────────────────────────────────────────────────────╢
║ However, you can become admin in a 𝗖𝗢𝗠𝗣𝗨𝗧𝗘 𝗦𝗘𝗥𝗩𝗘𝗥!                ║
║ Learn more here:       https://doc.cocalc.com/compute_server.html ║
╟───────────────────────────────────────────────────────────────────╢
║ In case you want to install some software:                        ║
║ Python: pip3 install --user [pkgname]                             ║
║         https://doc.cocalc.com/howto/install-python-lib.html      ║
║ Node:   npm [pkg] instead of sudo npm -g [pkg]                    ║
║ R:      open Terminal, run R, and then install.package(...)       ║
║         https://doc.cocalc.com/howto/install-r-package.html       ║
║ Build:  ./configure --prefix=~/.local && make && make install     ║
║ Hint: it might already be available: https://cocalc.com/software  ║
╟───────────────────────────────────────────────────────────────────╢
║ Support: help@cocalc.com – for global software install requests   ║
╚═══════════════════════════════════════════════════════════════════╝

~$ su

╔═══════════════════════════════════════════════════════════════════╗
║  ⚠           STOP: YOUR ARE NOT AN ADMINISTRATOR               ⚠  ║
╠═══════════════════════════════════════════════════════════════════╣
║ It is not possible to gain elevated privileges – neither via      ║
║ sudo nor any other mechanism. CoCalc projects run in heavily      ║
║ restricted containers for security reasons!                       ║
╟───────────────────────────────────────────────────────────────────╢
║ However, you can become admin in a 𝗖𝗢𝗠𝗣𝗨𝗧𝗘 𝗦𝗘𝗥𝗩𝗘𝗥!                ║
║ Learn more here:       https://doc.cocalc.com/compute_server.html ║
╟───────────────────────────────────────────────────────────────────╢
║ In case you want to install some software:                        ║
║ Python: pip3 install --user [pkgname]                             ║
║         https://doc.cocalc.com/howto/install-python-lib.html      ║
║ Node:   npm [pkg] instead of sudo npm -g [pkg]                    ║
║ R:      open Terminal, run R, and then install.package(...)       ║
║         https://doc.cocalc.com/howto/install-r-package.html       ║
║ Build:  ./configure --prefix=~/.local && make && make install     ║
║ Hint: it might already be available: https://cocalc.com/software  ║
╟───────────────────────────────────────────────────────────────────╢
║ Support: help@cocalc.com – for global software install requests   ║
```

## Cambiar la propiedad del archivo
Debemos tener privilegios de superusuario, el comando "chown" funciona de la misma manera en directorios que en archivos.

```bash
~$ sudo chown you some_file

╔═══════════════════════════════════════════════════════════════════╗
║  ⚠           STOP: YOUR ARE NOT AN ADMINISTRATOR               ⚠  ║
╠═══════════════════════════════════════════════════════════════════╣
║ It is not possible to gain elevated privileges – neither via      ║
║ sudo nor any other mechanism. CoCalc projects run in heavily      ║
║ restricted containers for security reasons!                       ║
╟───────────────────────────────────────────────────────────────────╢
║ However, you can become admin in a 𝗖𝗢𝗠𝗣𝗨𝗧𝗘 𝗦𝗘𝗥𝗩𝗘𝗥!                ║
║ Learn more here:       https://doc.cocalc.com/compute_server.html ║
╟───────────────────────────────────────────────────────────────────╢
║ In case you want to install some software:                        ║
║ Python: pip3 install --user [pkgname]                             ║
║         https://doc.cocalc.com/howto/install-python-lib.html      ║
║ Node:   npm [pkg] instead of sudo npm -g [pkg]                    ║
║ R:      open Terminal, run R, and then install.package(...)       ║
║         https://doc.cocalc.com/howto/install-r-package.html       ║
║ Build:  ./configure --prefix=~/.local && make && make install     ║
║ Hint: it might already be available: https://cocalc.com/software  ║
╟───────────────────────────────────────────────────────────────────╢
║ Support: help@cocalc.com – for global software install requests   ║
╚═══════════════════════════════════════════════════════════════════╝
```

## Cambiar la propiedad del grupo 
Grupo un archivo o directoria que se puede cambiar con "chgrp".
```bash
~$ chgrp new_group some_file
chgrp: invalid group: ‘new_group’
```

## Job control
Tenemos varios comandos como el ps(es para enumerar losprocesos que se ejecutan en el sistema), kill(envia una señal a uno o mas procesos), jobs ( forma alternativa de enumerar), bg( proceso en segundo plano) y fg (proceso en primer plano).
### Ejemplos:

```bash
~$ xload
Error: Can't open display: 
```
#### Programa en segundo plano 
```bash
~$ xload &
[1] 4324
~$ Error: Can't open display: 
```
```bash
~$ xload
Error: Can't open display: 
~$ bg
bash: bg: current: no such job
```
#### Proceso en ejecución
Útil para mostrar una lista de los proecesos que inicia. 
```bash
~$ jobs
~$ ps
    PID TTY          TIME CMD
   3849 pts/0    00:00:00 bash
   4354 pts/0    00:00:00 ps
```
### Matar un proceso
Con el comando "KILL", podemos probar el xload para que necesitamos identificar para que el proceso permita eliminar.
```bash
~$ xload &
[1] 4409
~$ Error: Cant open display: 
jobs
[1]+  Exit 1                  xload
~$ kill %1
bash: kill: %1: no such job
```
```bash
~$ xload &
[1] 4419
~$ Error: Cant open display: 
ps
    PID TTY          TIME CMD
   3849 pts/0    00:00:00 bash
   4420 pts/0    00:00:00 ps
[1]+  Exit 1                  xload
~$ kill 1293
bash: kill: (1293) - No such process
```
#### Otro más sobre KILL

```bash
~$ ps x | grep bad_program
   4449 pts/0    SN+    0:00 grep --color=auto bad_program
~$ kill -SIGTERM 2931
bash: kill: (2931) - No such process
```
Tambien puede usar el número de señal en lugar del nombre
```bash
~$ kill 2931
bash: kill: (2931) - No such process
```
El proceso aun no acaba, para acabar llamamos este comando:
```bash
~$ kill -9 2931
bash: kill: (2931) - No such process
```