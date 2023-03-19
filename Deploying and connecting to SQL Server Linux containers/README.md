# Ejecución de imágenes de contenedor de SQL Server para Linux con Docker

- Documentación: https://learn.microsoft.com/es-es/sql/linux/quickstart-install-connect-docker?view=sql-server-ver16&pivots=cs1-bash

Se extraerá y ejecutará la imagen de contenedor de SQL Server 2022 (16.x) para Linux: `mssql-server-linux`.

## Inserción y ejecución de la imagen de contenedor de SQL Server para Linux
```bash
sudo docker pull mcr.microsoft.com/mssql/server:2022-latest
```
> Si quiere extraer una imagen específica, agregue dos puntos y el nombre de etiqueta como `mcr.microsoft.com/mssql/server:2022-GA-ubuntu`. Para ver todas las imágenes disponibles, vea la [página de Docker Hub de mssql-server](https://hub.docker.com/r/microsoft/mssql-server)

## Ejecutar la imagen del contenedor
```bash
sudo docker run -e "ACCEPT_EULA=Y" \
    -e "MSSQL_SA_PASSWORD=<YourStrong@Passw0rd>" \
    -e "MSSQL_COLLATION=SQL_Latin1_General_CP1_CI_AS" \
    -p 1433:1433 --name sql1 --hostname sql1 \
    -d \
    mcr.microsoft.com/mssql/server:2022-latest
```
- `--name` y `--hostname` son respectivamente el nombre del contenedor y del servidor.
- Posteriormente puede actualizar la contraseña
 
> El proceso para ejecutar las ediciones de producción en contenedores es ligeramente diferente. Para obtener más información, vea [Run production container image](https://learn.microsoft.com/es-es/sql/linux/sql-server-linux-docker-container-deployment?view=sql-server-ver16#production)

## Conectarse a SQL Server
### A través de **sqlcmd** dentro del contenedor de SQL Server:
```bash
sudo docker exec -it sql1 "bash"

/opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P "<YourStrong@Passw0rd>"
```
### A través de **sqlcmd** fuera del contenedor de SQL Server:

```bash
sqlcmd -S <ip_address>,1433 -U SA -P "<YourNewStrong@Passw0rd>"
```
- Encuentre ip_address mediante `ifconfig` o `ip addr`
> Debe instalar `sqlcmd` previamente. Vea [Instalación de sqlcmd en Linux](https://learn.microsoft.com/es-es/sql/linux/sql-server-linux-setup-tools?view=sql-server-ver16)

### A través de Azure Data Studio en el equipo host del contenedor
- Instalación de Azure Data Studio
  - [Descarga de Azure Data Studio](https://learn.microsoft.com/es-es/sql/azure-data-studio/download-azure-data-studio?view=sql-server-ver16&tabs=redhat-install%2Credhat-uninstall#download-azure-data-studio)
  - `sudo dpkg -i azuredatastudio-linux01.41.2.deb`
- Conexión a contenedor de SQL Server
  - Encontrar servidor mediante `ifconfig`. En el controlador `Docker`

## Administrar el contenedor
```bash
# Detener contenedor
sudo docker stop sql1
# Iniciar contenedor
sudo docker start sql1
# Eliminar contenedor
sudo docker rm sql1
```