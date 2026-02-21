## Pasos para el repositorio

1. Clonar el repositorio 
2. Clonar ```.env.template``` y renombrarlo a ```.env```
3. Ejecutar el comando `docker compose up --build`
---
En caso de que no se realicen cambios en archivos como package*.json, prisma*.ts o dockerfiles incluyendo el docker-compose.yml levantar unicamente los contenedores mediante `docker compose up --watch` o `docker compose up` y seleccionar la opcion Enable Watch en consola



### Pasos para crear los Git Submodules


1. Crear un nuevo repositorio en GitHub
2. Clonar el repositorio en la máquina local
3. Añadir el submodule, donde `repository_url` es la url del repositorio y `directory_name` es el nombre de la carpeta donde quieres que se guarde el sub-módulo (no debe de existir en el proyecto)
```
git submodule add <repository_url> <directory_name>
```
4. Añadir los cambios al repositorio (git add, git commit, git push)
Ej:
```
git add .
git commit -m "Add submodule"
git push
```
5. Inicializar y actualizar Sub-módulos, cuando alguien clona el repositorio por primera vez, debe de ejecutar el siguiente comando para inicializar y actualizar los sub-módulos
```
git submodule update --init --recursive
```
6. Para actualizar las referencias de los sub-módulos
```
git submodule update --remote
```


## Importante
Si se trabaja en el repositorio que tiene los sub-módulos, **primero actualizar y hacer push** en el sub-módulo y **después** en el repositorio principal. 

Si se hace al revés, se perderán las referencias de los sub-módulos en el repositorio principal y tendremos que resolver conflictos.


## Trabajo Correcto con Submódulos

Los submódulos apuntan a **commits específicos**, no a ramas.  
Por eso, al ejecutar:

```bash
git submodule update --init --recursive
```

quedarán en **detached HEAD**.

### Para trabajar correctamente:

1. Entrar al submódulo:
   ```bash
   cd <submodule-directory>
   ```

2. Cambiar a la rama deseada:
   ```bash
   git switch <branch-name>
   ```

3. Realizar cambios y hacer push normalmente.

4. Volver al monorepo y actualizar la referencia:
   ```bash
   cd ..
   git add <submodule-directory>
   git commit -m "chore: update submodule reference"
   git push
   ```

> ❗ No se recomienda trabajar directamente en detached HEAD.