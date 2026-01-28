## Pasos para el repositorio

1. Clonar el repositorio 
2. Clonar ```.env.template``` y renombrarlo a ```.env```
3. Ejecutar el comando `docker compose up --build`
---
En caso de que no se realicen cambios en archivos como package*.json, prisma*.ts o dockerfiles incluyendo el docker-compose.yml levantar unicamente los contenedores mediante `docker compose up --watch` o `docker compose up` y seleccionar la opcion Enable Watch en consola