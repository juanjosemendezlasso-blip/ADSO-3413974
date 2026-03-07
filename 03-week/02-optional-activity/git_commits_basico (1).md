# 📦 Manual básico de commits con Git (GitHub)

## 1️⃣ Inicializar un repositorio

Crear un repositorio Git en tu carpeta actual:

``` bash
git init
```

Esto crea la carpeta oculta:

    .git

donde Git guarda todo el historial.

------------------------------------------------------------------------

## 2️⃣ Ver estado del repositorio

Para ver qué archivos cambiaron:

``` bash
git status
```

Te muestra:

-   archivos nuevos
-   archivos modificados
-   archivos listos para commit

------------------------------------------------------------------------

## 3️⃣ Añadir archivos al staging

Antes de hacer commit debes **agregar archivos al staging area**.

Agregar un archivo:

``` bash
git add archivo.txt
```

Agregar todos los archivos:

``` bash
git add .
```

------------------------------------------------------------------------

## 4️⃣ Crear un commit

Guardar cambios en el historial:

``` bash
git commit -m "mensaje del commit"
```

Ejemplo:

``` bash
git commit -m "Agrega sistema de colisiones"
```

------------------------------------------------------------------------

## 5️⃣ Ver historial de commits

``` bash
git log
```

Versión corta:

``` bash
git log --oneline
```

Ejemplo:

    a82f91c agrega colisiones
    c11b82f primer commit

------------------------------------------------------------------------

## 6️⃣ Conectar el repo con GitHub

Primero crea el repositorio en GitHub.

Luego conecta tu repo local:

``` bash
git remote add origin https://github.com/usuario/repositorio.git
```

Ver repos remotos:

``` bash
git remote -v
```

------------------------------------------------------------------------

## 7️⃣ Subir commits a GitHub

Primer push:

``` bash
git push -u origin main
```

Luego solo:

``` bash
git push
```

------------------------------------------------------------------------

## 8️⃣ Descargar cambios del repo

``` bash
git pull
```

Esto hace:

    fetch + merge

------------------------------------------------------------------------

## 9️⃣ Clonar un repositorio

Descargar un repo de GitHub:

``` bash
git clone https://github.com/usuario/repositorio.git
```

------------------------------------------------------------------------

## 🔟 Ver cambios en archivos

``` bash
git diff
```

Te muestra qué cambió.

------------------------------------------------------------------------

## 11️⃣ Quitar archivo del staging

``` bash
git reset archivo.txt
```

------------------------------------------------------------------------

## 12️⃣ Cambiar mensaje del último commit

``` bash
git commit --amend
```

------------------------------------------------------------------------

## ⚡ Flujo básico de trabajo

El ciclo típico es:

``` bash
git status
git add .
git commit -m "descripcion del cambio"
git push
```

------------------------------------------------------------------------

## 🧠 Regla simple para recordar

    modificar código
    ↓
    git add
    ↓
    git commit
    ↓
    git push

------------------------------------------------------------------------

## 🚀 Comando útil para programadores

Agregar y hacer commit en una línea:

``` bash
git commit -am "mensaje"
```

Esto funciona **solo con archivos ya trackeados**.
