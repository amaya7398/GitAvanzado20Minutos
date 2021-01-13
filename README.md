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
### 1. Stash

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

*Si el trabajo en la rama actual aún no está listo para ser persistido en un commit, la solución yace en el stashing. Para crear un stash, existen los comandos* `$ git stash`, `$ git stash push` y `$ git stash save`

#### Comandos para la administración de los stashes

`$ git stash list`  Para mostrar todos los stashes

`$ git stash push [-u | -a][-m "mensaje"][<Archivos>]`

* (`-u`) Incluyendo archivos modificados y untracked.
* (`-a`) Incluyendo archivos modificados, untracked e ignorados.
* (`-m`) Agregar mensaje al stash. Es posible utilizar la misma bandera de mensaje de commit (`-m`) para etiquetar al stash con un mensaje.

🔍 Tip de Hernán. Recuperar stashes perdidos puede ser complicado, por ello recomiendo utilizar `$ git commit` incluso para cambios parciales si estos son muy significativos. Luego siempre es posible realizar un `$ git commit --amend` para terminar de componer el commit.

###### Es posible cambiar de rama teniendo modificaciones uncommitted en el working tree si el cambio * no requiere deshacer * dichas modificaciones.


