# GitAvanzado20Minutos
Recopilación sobre administración avanzada en git.\
Basado en el [repositorio](https://github.com/HerCerM/ManualDefinitivoGit/blob/master/Parte2_Profundizando.md) de [HerCerM](https://github.com/HerCerM)

### Algunos trick shots:

`$ git status [-s | --short]` \
`$ git commit -a -m "mensaje"`

`$ git checkout -b [NewBranch]` \
`Switched to a new branch`

Esto es un atajo para:

`$ git branch [NewBranch]`\
`$ git checkout [NewBranch]`

---
`$ git log [--all] [--oneline] [-<cantidad>] [--first-parent] [<commit>]`


* (`--oneline`) muestra una versión más compacta, sólo desplegando el encabezado del mensaje, los primeros 7 caracteres del hash del commit y la rama en la que fue realizado.
* (`--all`) Ver todos los commits del repositorio (esto es, todos los commits accesibles mediante alguna rama) en lugar a sólo los accesibles a través de HEAD.
* (`-<cantidad>`) Muestra sólo <cantidad> número de commits. Por ejemplo -1 sólo muestra el primer commit del recorrido.
* (`--first-parent`) Recorre el árbol de commits sólo pasando por el primer padre. Esto es muy útil al revisar la historia de master, donde no se suele desear ver commits de ramas fusionadas.
* (`<commit>`) Especificar una posición distinta a HEAD para iniciar el recorrido del árbol. Este argumento toma cualquier referencia resoluble a un commit, véase referencias absolutas y referencias relativas.

`$ git log --oneline -5 --author [value]` \
`$ git shortlog`

---
### 1. El mejor amigo de un directorio vacío: `.gitkeep`

Consideremos el siguiente experimento.
```
$ git init
Initialized empty Git repository in C:/Users/hjcer/temp/.git/

$ mkdir ejemplo1

$ git status
On branch master

No commits yet

# A pesar que el directorio ejemplo1 existe en el working tree, Git
# no lo reconoce, negando la posibilidad de agregarlo al staging
# area y almacenarlo en un commit.
nothing to commit (create/copy files and use "git add" to track)
```
`.git keep`, ¿Acaso un archivo de configuración, como `.gitignore` o `.gitconfig` ? \
lamentablemente no, por lo que no tiene documentación oficial su uso; el prefijo git sugiere que es un archivo de configuración, cuando en realidad no lo es.

Al ser confuso este archivo, en lugar de usar un `.gitkeep` se recomienda utilizar un readme.md explicando la razón de la necesidad de hacer tracking de tal carpeta

---
### 2. Ignorando archivos: `.gitignore`

Para ignorar archivos se requiere un archivo de configuración `.gitignore`, el cual puede tener impacto global o sólo respecto a un repositorio. Los archivos a ignorar se seleccionan utilizando expresiones glob.

Las expresiones glob sirven el propósito de representar archivos mediante wildcards (caracteres especiales), dado un sistema de archivos; mientras que las expresiones regulares representan cadenas de texto, dada una secuencia de caracteres.

#### Notación para expresiones glob
| Símbolo|       Explicación |
| --- | --- |
| * |	Representa cualquier número de caracteres, incluido ninguno, pero no una diagonal. |
| ** |	Representa uno o más directorios, pero no el directorio actual (.) ni el padre (..). |
| ? |	Representa un carácter. |
| [abc] |	Representa cualquier carácter contenido en los corchetes. |
| [a-z] |	Representa cualquier carácter contenido en el intervalo definido por los corchetes. Este intervalo es dependiente de la configuración regional. |

Este archivo define los archivos que Git ignorará. **Archivos tracked no son afectados**. El archivo puede contener comentarios de una línea, los cuales inician con `#`. Para especificar archivos a ignorar, colocar un patrón glob por línea. Si se ignora un directorio, todos sus archivos y subdirectorios también son ignorados. Un repositorio puede tener más de un `.gitignore`, siendo sus patrones glob relativos a la ubicación del archivo.

![img](https://github.com/HerCerM/ManualDefinitivoGit/blob/master/images/ignore_1.png)

Git permite definir un `.gitignore` de impacto global (afecta todos los repositorios del usuario con esta configuración). Primero es necesario crear el archivo manualmente, luego se indica su ubicación en la configuración global.

`$ git config --global core.excludesFile <ruta-.gitignore>`

#### Ignorar archivos tracked
Para ignorar archivos tracked (añadidos al staging environment o existentes en algún commit) primero deben ser olvidados por Git, es decir, cambiar su estado a untracked. Para conseguir esto se utiliza el comando siguiente, donde `<archivo>` acepta expresiones glob.

`$ git rm --cached <archivo>`

---
### 3. Stash

Vamos a presentar un ejemplo simple de ramificar y fusionar, con un flujo de trabajo que se podría presentar en la realidad. Imagina los siguientes pasos:

1. Trabajas en un sitio web.
2. Creas una rama para un nuevo tema sobre el que quieres desarrollar.
3. Realizas cambios en esa rama.

En este momento, recibes una llamada avisándote de un problema crítico que has de resolver. Y sigues los siguientes pasos:

1. Vuelves a la rama de producción original.
2. Creas una nueva rama para el problema crítico y lo resuelves trabajando en ella.
3. Tras las pertinentes pruebas, fusionas (merge) esa rama y la envías (push) a la rama de producción.
4. Vuelves a la rama del tema en que andabas antes de la llamada y continúas tu trabajo.

Cuando se intenta cambiar de rama sin haber primero hecho commit de los cambios del working tree, en ocasiones Git reporta un mensaje de error similar al siguiente:

`error: Your local changes to the following files would be overwritten by checkout:
        foo.txt
Please commit your changes or stash them before you switch branches.
Aborting`

*Si el trabajo en la rama actual aún no está listo para ser persistido en un commit, la solución yace en el stashing. Para crear un stash, existen los comandos* `$ git stash`, `**$ git stash push**` y `$ git stash save`

Al estar desacoplado de las ramas, los stashes pueden ser referidos en cualquier rama. La idea es poder limpiar tanto el working tree como el staging area pero no perder los cambios, almacenándolos en un commit. Esto para poder cambiar de rama aún si no está listo para realizar un commit o en caso que requiera ejecutar `git pull`. Los cambios de este commit pueden ser luego aplicados al trabajar en una rama distinta o en la misma rama.

#### Comandos para la administración de los stashes

`$ git stash list`  Para mostrar todos los stashes

`$ git stash push [-u | -a][-m "mensaje"][<Archivos>]` Crear un nuevo stash

* (`-u`) Incluyendo archivos modificados y untracked.
* (`-a`) Incluyendo archivos modificados, untracked e ignorados.
* (`-m`) Agregar mensaje al stash. Es posible utilizar la misma bandera de mensaje de commit (`-m`) para etiquetar al stash con un mensaje.

🔍 Tip de Hernán. Recuperar stashes perdidos puede ser complicado, por ello recomiendo utilizar `$ git commit` incluso para cambios parciales si estos son muy significativos. Luego siempre es posible realizar un `$ git commit --amend` para terminar de componer el commit.

##### Es posible cambiar de rama teniendo modificaciones uncommitted en el working tree si el cambio * no requiere deshacer * dichas modificaciones.


