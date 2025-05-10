# Servicio IAS en macOS usando Docker

Este proyecto levanta un contenedor macOS utilizando la imagen [`dockur/macos`](https://github.com/dockur/macos) como base para implementar un servicio IAS.

## 📦 Requisitos

- Docker
- Git

## 🚀 Instalación y ejecución

1. Revisa y ajusta el archivo `docker-compose.yml` según tus necesidades.

2. Ejecuta el contenedor:

```console
docker compose up -d
```

Una vez que el contenedor esté en ejecución, **sigue las instrucciones del repositorio original [dockur/macos](https://github.com/dockur/macos)** para acceder a la máquina virtual macOS.

## 🛠️ Compilación del proyecto IASService

### Modificar `Program.cs`

```csharp
WebApplicationOptions options = new() {
    Args = args,
    ContentRootPath = AppContext.BaseDirectory // Solo modifica esta línea
};
```

### Para Mac con procesador Intel (x64):

```console
dotnet publish -c Release -r osx-x64 --self-contained true -p:PublishSingleFile=true -o publish
```

### Para Mac con procesador Apple Silicon (ARM64):

```console
dotnet publish -c Release -r osx-arm64 --self-contained true -p:PublishSingleFile=true -o publish
```

**Nota:** Después de ejecutar el comando `dotnet publish`, **debes comprimir la carpeta `publish` generada en un archivo `.zip`**.

## ⚙️ Configuración del servicio IAS como Launch Agent en macOS

### 1. Crear la carpeta y copiar los archivos del servicio

Primero, necesitas crear la carpeta de destino donde se instalarán los archivos del servicio y luego copiar los archivos necesarios.

Ejecuta los siguientes comandos:

```bash
# Crear la carpeta de instalación si no existe
sudo mkdir -p /opt/iasservice

# Copiar todos los archivos de la carpeta 'publish' al directorio de instalación
sudo cp -R publish/* /opt/iasservice/

# Asignar los permisos de ejecución al archivo
sudo chmod +x /opt/iasservice/IASServiceClientCore
```

### 2. Crear la carpeta `LaunchAgents` (si no existe)

Verifica que el directorio `~/Library/LaunchAgents` exista y créalo si es necesario:

```console
mkdir -p ~/Library/LaunchAgents
```

### 3. Copia el archivo al directorio de `LaunchAgents` del usuario:

```console
cp com.ias.service.plist ~/Library/LaunchAgents/
```

### 4. Carga el servicio con `launchctl`:

Una vez que el archivo `.plist` esté en el lugar adecuado, carga el servicio con:

```console
launchctl load ~/Library/LaunchAgents/com.ias.service.plist
```

### 5. Verifica que el servicio esté corriendo:

```console
launchctl list | grep com.ias.service
```

### 6. Para ver la salida estándar del servicio (stdout):

```console
cat /tmp/iasservice.out.log
```

### 7. Para ver los errores del servicio (stderr):

```console
cat /tmp/iasservice.err.log
```

### 8. Consultar el servicio

Después de cargar el servicio con `launchctl load`, **debes consultar el recurso adecuado de la API en el puerto 3024** para verificar que el servicio está funcionando correctamente.