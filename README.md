# Problema de la mochila - Modelos de Programación - Grupo 1
## Flujo de trabajo
### Entorno de desarrollo local
#### Opción Conga + Colab
Instalar siguiendo las [instrucciones que preparó Sonia](Documents/Configuración%20entorno%20Jupyter%20+%20Colab.pdf) si queremos usar Colab. De este modo tendremos una instalación local de Jupyter en un virtual env, y podremos ejecutar las bibliotecas de Ocean. Podremos usar tanto el editor Jupyter como el editor Colab de Google, o configurar Visual Studio u otro entorno que se pueda conectar al kernel de Jupyter para ejecutar el código.


#### Alternativa mínima: Solo Jupyter
Creamos el virtual env y instalamos todas las dependencias que necesitamos
```
python –m venv modelos_computacion

#ELEGIR UNA DE LAS SIGUIENTES OPCIONES
modelos_computacion\Scripts\activate # en Windows
. modelos_computacion/bin/activate # en sistemas Unix

pip install dwave-ocean-sdk
pip install yfinance
pip install nbdime
pip install nbstripout
pip install tabulate
pip install pandas
pip install matplotlib
pip install ipykernel
pip install networkx
pip install jupyter-networkx
```

Configuramos el acceso a DWAVE
```
dwave auth login --oob
# UNA VEZ TERMINADO EL PROCESO DE LOGIN
dwave config create --auto-token
```

### Descargar el repositorio git

Dentro del directorio de nuestro virtual env y con el virtual env activo (veremos el nombre del virtual env entre paréntesis antes del prompt del sistema). "proyecto_mochila" es el nombre del directorio que se creará para descargar el repositorio

```
git clone https://github.com/soniamerayogonzalez/knapsack_modelos_programacion_grupo_1.git proyecto_mochila 
nbstripout --install
nbdime config-git --enable --global
```

### Arrancando el Jupyter local
Si hemos seguido las instrucciones de Sonia, ya tendremos un Jupyter arrancado (y además aceptará conexiones de Colab). Si no, podemos arrancarlo así (proyecto_mochila es el directorio que se creó antes para descargar el repositorio, si usas otro, cambialó)
```
jupyter notebook proyecto_mochila
```

### Edición de los ficheros
Podemos editar los ficheros de muchas formas. 
- (recomendada) En local, con el servidor Jupyter (localhost:8888)
- Via colab, si hemos seguido las instruciones de Sonia, podemos ejecutar contra el kernel de nuestro jupyter (o del servidor que ha creado Sonia). Pero en ese caso tendremos que trabajar con una copia de los notebooks y luego volver a ponerlos en nuestro directorio para subirlos a git.

### Ejecucion del código
Si hemos hecho todo lo anterior, podremos ejecutar el código sin problema.

### Subiendo los cambios a git
Para subir los cambios a git, trabajaremos con un modelo simplificado (subir directamente a main). Si estás familiarizado con git y lo prefieres, puedes crear tus propios branches y luego hacer los pull request para hacer el merge del branch al branch principal. Pero si no estás muy familiarizado con git, usa el siguiente método:
```
# Antes de empezar a editar nosotros, sincronizamos el repostiorio (es decir, nos descargamos los últimos cambios que otros hayan subido)
git pull
# Hacemos los cambios locales.
# Añadimos los cambios locales. Podemos ir fichero a fichero o añadir todo simplemente añadiendo el directorio raiz:
git add .
# Hacemos commit de los cambios
git commit -m "Descripción de los cambios"
# Aquí hacemos un FETCH para descargar los cambios que otros puedan haber hecho
git fetch origin
# Hacemos el push para meterlo en el repositorio -- Si hay cambios que es necesario mezclar, nos avisará git
git push origin main
```

Si en ese proceso te aparece un conflicto, es porque alguien ha subido cambios en los mismos ficheros que tú estás modificando. La mayoría de las veces, git será capaz de combinar los cambios, y te guiará por el proceso. Si no puede hacerlo, te pedirá que lo hagas tú a mano. En la sección siguiente, en el punto "nbdiff" explico un poco más esto.

### Herramientas útiles.
#### nbstripout
Hemos instalado correctamente nbstripout. Esto hace que antes de subir los notebooks a git, se eliminen todas las celdas de "out", es decir, los resultados de la ejecución. De esa forma se descarga el git y se mantiene limpio, y se evita que aparezcan conflictos y "diffs" muy grandes.

Por si no hubieses instalado nbstripout o no funcionase bien, hay un workflow instalado en el repositorio que detecta automáticamente si hay celdas de salida en los notebooks, y en ese caso te envía un email alertándote. Si usas un flujo de trabajo creando tus propias branches, no podrás hacer el merge hasta que no soluciones eso, pero si trabajas en el branch principal, el merge ya estará hecho (y tendrás que limpiarlo y hacer otro commit).

#### nbdiff
Si tienes que resolver conflictos, el programa nbdiff es muy útil para comparar tu versión con la versión que hay en el repositorio. 

Puedes usarlo para ver los cambios que hay en tu fichero respecto a lo que tenías antes. Simplemente ejecuta en el directorio en el que estés trabajando:
```
nbdiff-web
```

### Flujo de trabajo para resolver conflictos con git
Supongamos que tú has hecho cambios en tu local, pero un compañero ya hizo *push* de sus cambios al `main`. Este es el proceso seguro para sincronizar el trabajo.

#### 1. Guardar tus cambios y descargar los remotos

Antes de traer nada del servidor, asegura tus cambios locales.

1. **Haz commit de tu trabajo actual:**

   ```bash
   git add .
   git commit -m "Mis cambios locales"
   ```

2. **Descarga la información del repositorio remoto:**
   (No la fusiones todavía). Usamos `fetch` en lugar de `pull` para poder inspeccionar primero.

   ```bash
   git fetch origin
   ```

#### 2. Visualizar las diferencias (Antes de fusionar)

Aquí es donde verificas qué cambió tu compañero respecto a tu versión antes de mezclarlo todo.

Para ver la comparación visualmente en el navegador entre tu versión (`HEAD`) y la que acabas de descargar (`origin/main`):

```bash
nbdiff-web HEAD origin/main
```

*Esto abrirá una pestaña en tu navegador mostrando las celdas añadidas, eliminadas o modificadas lado a lado.*

#### 3. Fusionar y Resolver Conflictos

Ahora procedemos a mezclar el código.

1. **Intenta el merge:**

   ```bash
   git merge origin/main
   ```

   * Si todo sale bien, Git lo mezcla automáticamente.
   * **Pero**, si hay conflictos (ambos tocaron la misma celda), Git te detendrá.

2. **Resolver conflictos visualmente:**
   Gracias a la configuración de `nbdime`, solo tienes que ejecutar:

   ```bash
   git mergetool --tool=nbdime
   ```

   Esto lanzará **`nbmerge-web`**.

   * Se abrirá una interfaz de 3 columnas:
     1. **Local** (Tus cambios).
     2. **Base** (El ancestro común, cómo estaba antes).
     3. **Remote** (Los cambios de tu compañero).

   * Abajo verás el resultado final. Podrás elegir qué versión de la celda quedarte o editarla manualmente ahí mismo.

   * Al terminar, dale a "Save" y cierra la herramienta.

#### 4. Hacer el Push final

Una vez resuelto el conflicto, `nbdime` suele guardar el archivo y hacer el `git add` automáticamente.

1. **Finaliza el commit del merge:**
   (Solo necesario si no se hizo automático al cerrar la herramienta de merge).

   ```bash
   git commit -m "Merge realizado y conflictos resueltos"
   ```

2. **Sube todo al servidor:**

   ```bash
   git push origin main
   ```
