# 🚀 Proceso de Despliegue — PokeDex en Azure Static Web Apps

## 1. Preparación del Repositorio en GitHub

### 1.1 Creación del repositorio
Se creó un repositorio público en GitHub llamado `pokedex_App` desde la interfaz web de [github.com](https://github.com).

### 1.2 Clonación del repositorio
```bash
git clone https://github.com/Daniela477/pokedex_App.git
cd pokedex_App
```

### 1.3 Carga del código fuente
Se copió el código fuente de la aplicación Angular (`pokedex-angular`) dentro de la carpeta clonada, asegurando que el archivo `angular.json` quedara en la raíz del repositorio.

### 1.4 Subida a GitHub
```bash
git add .
git commit -m "Agregar código fuente PokeDex Angular"
git push origin main
```

**Resultado:** 979 objetos subidos exitosamente al repositorio remoto.

---

## 2. Configuración del Despliegue en Azure

### 2.1 Creación del recurso Static Web Apps
Desde el portal [portal.azure.com](https://portal.azure.com) se buscó el servicio **Static Web Apps** y se creó un nuevo recurso con los siguientes parámetros:

| Parámetro | Valor |
|-----------|-------|
| Suscripción | Azure for Students |
| Grupo de recursos | Pokedex-pp_group (nuevo) |
| Nombre | Pokedex-App |
| Plan | Free |
| Región | East US 2 |
| Origen | GitHub |
| Repositorio | Daniela477/pokedex_App |
| Rama | main |
| Preset de compilación | Angular |
| App location | `/` |
| Output location | `dist/pokedex-angular` |

> El `outputPath` fue verificado previamente en el archivo `angular.json`:
> ```bash
> cat angular.json | grep outputPath
> # "outputPath": "dist/pokedex-angular"
> ```

### 2.2 Despliegue automático
Al crear el recurso, Azure generó automáticamente un workflow de GitHub Actions en `.github/workflows/azure-static-web-apps-brave-mud-0b171620f7.yml` que compila y despliega la aplicación en cada push a `main`.

**URL pública generada:** `https://brave-mud-0b171620f7.azurestaticapps.net`

---

## 3. Problemas Encontrados y Soluciones

### ❌ Problema 1: Pipeline de tests fallaba por Codecov
**Error:**
```
TypeError: result.split is not a function
Process completed with exit code 1
```
**Causa:** El archivo `pipelines.yml` intentaba subir reportes a Codecov usando una versión incompatible con Node.js 18.

**Solución:** Se simplificó el archivo `.github/workflows/pipelines.yml` eliminando el paso de Codecov:
```yaml
name: Run test-coverage
on: [push]
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Use Node.js 18.x
        uses: actions/setup-node@v4
        with:
          node-version: 18.x
      - run: npm install
      - run: npm run test:cov -- --watch=false --no-progress --browsers=ChromeHeadlessCI
```

---

### ❌ Problema 2: Error 500 — App no cargaba datos de Pokémon
**Error en consola:**
```
Connecting to 'https://beta.pokeapi.co/graphql/v1beta' violates Content Security Policy
```
**Causa:** La política CSP bloqueaba las llamadas a `beta.pokeapi.co` y a Google Fonts.

**Solución:** Se actualizó el archivo `staticwebapp.config.json` permitiendo los dominios externos necesarios.

---

### ❌ Problema 3: Calificación capped en A por uso de unsafe-inline
**Warning:**
```
This policy contains 'unsafe-inline' which is dangerous in the script-src directive.
```
**Causa:** El `script-src` incluía `'unsafe-inline'`, lo cual es considerado inseguro.

**Solución:** Se eliminó `'unsafe-inline'` del `script-src`, logrando la calificación **A+**.

---

## 4. Configuración Final de Seguridad HTTP

Archivo `staticwebapp.config.json` en la raíz del repositorio:

```json
{
  "globalHeaders": {
    "Content-Security-Policy": "default-src 'self'; script-src 'self'; style-src 'self' 'unsafe-inline' https://fonts.googleapis.com; font-src 'self' https://fonts.gstatic.com; img-src 'self' data: https:; connect-src 'self' https://pokeapi.co https://beta.pokeapi.co",
    "Strict-Transport-Security": "max-age=31536000; includeSubDomains; preload",
    "X-Content-Type-Options": "nosniff",
    "X-Frame-Options": "DENY",
    "Referrer-Policy": "no-referrer",
    "Permissions-Policy": "geolocation=(), microphone=(), camera=()"
  }
}
```

### ✅ Resultado del escaneo en securityheaders.com

- **Calificación obtenida: A+** 🏆
- **Mensaje:** *"Wow, amazing grade!"*
- **Cabeceras verificadas:** Strict-Transport-Security ✅ | Referrer-Policy ✅ | X-Content-Type-Options ✅ | Content-Security-Policy ✅ | X-Frame-Options ✅ | Permissions-Policy ✅

---

## 5. Validaciones Finales

| Validación | Estado |
|-----------|--------|
| App carga desde URL pública | ✅ |
| 151 Pokémon visibles | ✅ |
| Detalle de cada Pokémon funciona | ✅ |
| HTTPS activo | ✅ |
| Sin errores en consola del navegador | ✅ |
| Calificación securityheaders.com | ✅ **A+** |

---

## 6. Historial de Commits

| Commit | Descripción |
|--------|-------------|
| `265e7dd` | Agregar código fuente PokeDex Angular |
| `5d7127d` | Fix pipeline: remover codecov |
| `4088b99` | Agregar cabeceras de seguridad |
| `2b325c0` | Fix CSP: permitir fonts y beta.pokeapi.co |
| `fc659cc` | Corregir extensiones de archivos md |
| `último`  | Fix CSP: remover unsafe-inline para A+ |

---

## 7. Recursos Utilizados

- [Azure Static Web Apps Docs](https://docs.microsoft.com/azure/static-web-apps/)
- [securityheaders.com](https://securityheaders.com)
- [PokéAPI](https://pokeapi.co)
- [OWASP Secure Headers Project](https://owasp.org/www-project-secure-headers/)
