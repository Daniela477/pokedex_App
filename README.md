# 🎮 PokeDex App — Despliegue en Azure Static Web Apps

## 📋 Descripción del Proyecto

PokeDex es una aplicación web desarrollada en Angular que permite explorar las especies de Pokémon de la primera y segunda generación. Muestra información detallada sobre sus características, habilidades, tipos y estadísticas, consumiendo datos desde la API pública [PokéAPI](https://pokeapi.co).

- **Tipo de aplicación:** Web (Angular)
- **URL pública:** https://brave-mud-0b171620f7.azurestaticapps.net
- **Repositorio:** https://github.com/Daniela477/pokedex_App
- **Plataforma de despliegue:** Microsoft Azure Static Web Apps

---

## ☁️ Creación de la Cuenta en Azure for Students

### Paso 1 — Acceder al portal de Azure for Students
Se ingresó a [https://azure.microsoft.com/es-es/free/students/](https://azure.microsoft.com/es-es/free/students/) y se hizo clic en **"Empieza gratis"**.

### Paso 2 — Autenticación con correo institucional
Se inició sesión con el correo institucional de la universidad. Azure for Students no requiere tarjeta de crédito; solo verifica que el correo pertenezca a una institución educativa registrada.

### Paso 3 — Verificación estudiantil
Azure validó automáticamente el estado estudiantil mediante el correo institucional y otorgó **$100 USD en créditos gratuitos** válidos por 12 meses.

### Paso 4 — Acceso al portal
Una vez activada la cuenta, se accedió al portal de administración en [https://portal.azure.com](https://portal.azure.com).

---

## 🔒 Seguridad de la Aplicación

La aplicación fue escaneada en [securityheaders.com](https://securityheaders.com) obteniendo una calificación **A+**.

### Cabeceras HTTP implementadas

| Cabecera | Propósito |
|----------|-----------|
| `Content-Security-Policy` | Controla qué recursos puede cargar la app, previniendo ataques XSS |
| `Strict-Transport-Security` | Obliga el uso de HTTPS en todas las conexiones |
| `X-Content-Type-Options` | Evita la detección automática de tipos de archivo (MIME sniffing) |
| `X-Frame-Options` | Evita que la app sea embebida en iframes externos (Clickjacking) |
| `Referrer-Policy` | Minimiza la fuga de información en cabeceras HTTP |
| `Permissions-Policy` | Restringe el acceso a funciones del navegador como cámara y micrófono |

---

## 💭 Reflexión Técnica

### ¿Qué vulnerabilidades previenen los encabezados implementados?

- **Content-Security-Policy** previene ataques de tipo XSS (Cross-Site Scripting), que permiten a atacantes inyectar scripts maliciosos en la página.
- **Strict-Transport-Security** evita ataques de tipo Man-in-the-Middle al forzar conexiones cifradas HTTPS.
- **X-Frame-Options** protege contra Clickjacking, donde un atacante oculta la app dentro de un iframe engañoso.
- **X-Content-Type-Options** evita que el navegador interprete archivos con tipos MIME incorrectos, lo que podría ejecutar código malicioso.

### ¿Qué aprendí sobre la relación entre despliegue y seguridad web?

El despliegue no termina cuando la aplicación es accesible públicamente. La seguridad es una capa adicional e indispensable. Configurar correctamente las cabeceras HTTP es una práctica de bajo costo pero alto impacto que protege tanto a los usuarios como a la organización. Una aplicación desplegada sin cabeceras de seguridad es una aplicación vulnerable por defecto.

### ¿Qué desafíos encontré en el proceso?

- La aplicación Angular requería compilación previa, por lo que fue necesario configurar correctamente el `outputPath` en Azure (`dist/pokedex-angular`).
- El pipeline de CI/CD incluía un paso de Codecov que fallaba por incompatibilidad con la versión de Node.js. Se solucionó simplificando el archivo `pipelines.yml`.
- La política de Content-Security-Policy bloqueaba inicialmente las llamadas a `beta.pokeapi.co` y a Google Fonts. Se corrigió agregando los dominios permitidos explícitamente.
- Para lograr A+ se calculó el hash SHA-256 del script inline generado por Angular y se agregó al CSP en lugar de usar `unsafe-inline`.
- Las imágenes no cargaban porque `environment.prod.ts` tenía la ruta hardcodeada a `/pokedex-angular/assets/images`. Se corrigió a `/assets/images`.

---

## 👩‍💻 Autora

**Daniela Naranjo**  
Ingeniería de Sistemas — Fundación Universitaria Tecnológico Comfenalco  
GitHub: [@Daniela477](https://github.com/Daniela477)
