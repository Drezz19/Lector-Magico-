# Lector Mágico Inclusivo

Aplicación web educativa y accesible diseñada para niñas y niños de primaria con baja visión, dislexia o dificultades de aprendizaje visual. El sistema permite fotografiar o subir páginas de libros escolares para transcribir su texto mediante Reconocimiento Óptico de Caracteres (OCR), personalizar la presentación visual del texto y escucharlo mediante narración en voz alta.

- **Sitio en línea:** [https://andresdrezz.pythonanywhere.com](https://andresdrezz.pythonanywhere.com)
- **Asignatura:** Gráficos, Interfaces y Usabilidad — Universidad de Guadalajara
- **Evento / Entrega:** Expodit — 27 de noviembre de 2026
- **Integrantes del equipo:** *(Completar con los nombres de los 3 a 4 integrantes)*

---

## Vínculo con la Agenda 2030 (ODS)

- **ODS 4:** Educación de calidad.
- **Meta 4.5:** Eliminar las disparidades de género en la educación y asegurar el acceso igualitario a todos los niveles de la enseñanza y la formación profesional para las personas vulnerables, incluidas las personas con discapacidad.
- **Aporte del sistema:** Ofrece lectura bimodal gratuita (texto adaptable de alto contraste + síntesis de voz controlada) desde cualquier navegador móvil o de escritorio, promoviendo la autonomía lectora en el aula y en casa.

---

## Vistas y funcionalidades del sistema

El sistema cuenta con **5 vistas conectadas** mediante navegación semántica persistente y un endpoint de procesamiento OCR:

1. **Inicio (`/`)**: Presentación del sistema, accesos directos a las herramientas y vínculo con la ODS 4 (Meta 4.5).
2. **Lector de Fotos y Voz (`/lector`)**:
   - Carga o captura de fotografías de libros (`JPG`, `PNG`, `WebP`, `BMP`, máx. 10 MB).
   - Preprocesamiento digital con Pillow (corrección EXIF, escala de grises, inversión automática de fondos oscuros, reescalado y nitidez) y extracción estructurada con Tesseract OCR (`--psm 3`).
   - Síntesis de voz con `window.speechSynthesis` en español, selección inteligente de voces naturales, control de **Pausar/Continuar** en la palabra exacta (`onboundary`), **Reiniciar**, **Detener**, ajuste de velocidad (**Normal / Lenta**) y modo **Solo voz local (privada)**.
   - Panel de personalización visual: 4 fuentes (`Quicksand`, `Lexend`, `Andika`, `Verdana`), ajuste de tamaño (`A-` / `A+`) y 5 temas cromáticos (Blanco, Crema, Azul suave, Noche y Alto Contraste Negro/Amarillo).
   - Botón **Borrar lectura** para limpiar la pantalla en computadoras escolares compartidas.
3. **Biblioteca Escolar (`/biblioteca`)**: Lecturas infantiles de primaria precargadas y verificadas para practicar lectura y escucha sin requerir cámara.
4. **Ayuda y Contacto (`/ayuda`)**: Guía rápida en 3 pasos, aviso de privacidad orientado a menores y formulario con validación accesible en servidor, resumen de errores enlazado y correo opcional.
5. **Declaración de Accesibilidad (`/accesibilidad`)**: Nivel de conformidad WCAG 2.2 AA, resumen técnico de criterios implementados, limitaciones conocidas con alternativas y canales de contacto.

---

## Accesibilidad (WCAG 2.2 Nivel AA)

- **Paleta de 6 colores verificada:**
  - Papel Crema (Fondo): `#FCF9F2`
  - Azul Tinta (Texto principal): `#102542` (contraste 14.6:1 sobre fondo crema)
  - Azul Educación (Primario): `#1D4E89` (contraste 8.3:1 con blanco)
  - Verde Lectura (Secundario / Éxito): `#1B6B46` (contraste 6.4:1 con blanco)
  - Frambuesa (Acento): `#9E2A5B` (contraste 7.1:1 con blanco)
  - Rojo Alerta (Error): `#B42318` (contraste 6.5:1 con blanco)
- **Navegación y semántica:** Enlace *"Saltar al contenido principal"*, idioma declarado (`lang="es"`), regiones HTML5 (`header`, `nav`, `main`, `footer`), foco visible de doble anillo, áreas táctiles de al menos 44 x 44 px y regiones `aria-live` para avisos de estado y error.
- **Adaptabilidad:** Diseño fluido sin desplazamiento horizontal desde 320 px de ancho hasta escritorio, compatible con zoom al 200 % y 400 %.
- **Independencia del color:** Todos los estados de error, éxito y navegación se comunican mediante texto e iconos consistentes de la familia **Lucide Icons**.

---

## Seguridad y privacidad

- **Procesamiento en memoria:** Las imágenes subidas a `/procesar` nunca se guardan en disco; los metadatos EXIF/GPS se descartan al convertir la imagen para el OCR y la respuesta incluye `Cache-Control: no-store`.
- **Protección de endpoints:** Validación de token **CSRF** y control de tasa (**Rate Limiting**) por IP en `/procesar` y `/ayuda`.
- **Validación estricta de imágenes:** Restricción de extensiones y formatos en Pillow (`JPEG`, `PNG`, `WEBP`, `BMP`) y tope contra bombas de descompresión (`MAX_IMAGE_PIXELS = 25,000,000`).
- **Cabeceras HTTP seguras:** `Content-Security-Policy`, `X-Content-Type-Options: nosniff`, `X-Frame-Options: DENY`, `Referrer-Policy` y `Permissions-Policy`.
- **Protección para menores:** Aviso visible para no fotografiar rostros ni datos personales, correo electrónico opcional con recomendación de supervisión adulta en `/ayuda` y opción de voz estrictamente local (`localService`).

---

## Estructura del repositorio

```text
.
├── flask_app.py               # Aplicación Flask, rutas, OCR, CSRF, rate limiting y validaciones
├── requirements.txt           # Dependencias de Python
├── README.md                  # Documentación del proyecto
├── static/
│   └── styles.css             # Hoja de estilos global (paleta AA/AAA, foco y diseño 320px)
└── templates/
    ├── base.html              # Plantilla maestra con navegación y pie de página persistente
    ├── inicio.html            # Vista 1: Inicio y ODS 4
    ├── lector.html            # Vista 2: Lector OCR, voz y personalización visual
    ├── biblioteca.html        # Vista 3: Biblioteca de lecturas escolares
    ├── ayuda.html             # Vista 4: Guía de uso y formulario accesible validado
    └── accesibilidad.html     # Vista 5: Declaración de accesibilidad WCAG 2.2 AA
```

---

## Instalación y ejecución local

### 1. Requisitos previos
- **Python 3.10 o superior**
- **Tesseract OCR** instalado en el sistema operativo junto con el paquete de idioma español (`spa`):
  - **Ubuntu / Debian:** `sudo apt-get install tesseract-ocr tesseract-ocr-spa`
  - **macOS (Homebrew):** `brew install tesseract tesseract-lang`
  - **Windows:** Instalar desde los binarios oficiales de UB Mannheim seleccionando el idioma *Spanish* y añadir Tesseract al `PATH`.

### 2. Clonar e instalar dependencias
```bash
git clone https://github.com/TU-USUARIO/lector-magico-inclusivo.git
cd lector-magico-inclusivo
python -m venv venv
source venv/bin/activate  # En Windows: venv\Scripts\activate
pip install -r requirements.txt
```

### 3. Variables de entorno recomendadas
```bash
export SECRET_KEY="cambia-esta-clave-por-una-cadena-larga-y-aleatoria"
export FLASK_DEBUG=0
export FORCE_HTTPS=0  # Usar 1 en producción con HTTPS activo
```

### 4. Ejecutar en desarrollo
```bash
python flask_app.py
```
Abrir `http://127.0.0.1:5000` en el navegador.

---

## Notas de despliegue en PythonAnywhere

1. Subir los archivos respetando la estructura dentro de `/home/<usuario>/mysite/`.
2. Configurar en la pestaña **Web** la ruta de archivos estáticos: URL `/static/` apuntando a `/home/<usuario>/mysite/static`.
3. Activar **Force HTTPS** en la sección *Security* de la pestaña **Web**.
4. El archivo `flask_app.py` establece `os.environ['OMP_THREAD_LIMIT'] = '1'` antes de importar Tesseract para evitar bloqueos de hilos en entornos WSGI compartidos.

---

## Licencias y créditos de recursos

- **Flask:** Licencia BSD-3-Clause.
- **Pillow (PIL Fork):** Licencia MIT-CMU.
- **pytesseract / Tesseract OCR:** Licencia Apache 2.0.
- **Lucide Icons (SVG integrados):** Licencia MIT ([https://lucide.dev](https://lucide.dev)).
- **Tipografías (`Quicksand`, `Fredoka`, `Lexend`, `Andika`):** SIL Open Font License (OFL) 1.1.
- **Ilustración SVG de portada y textos de la Biblioteca Escolar:** Creación original del equipo para fines educativos.

---

## Declaración de uso de herramientas de IA generativa

En cumplimiento con las reglas académicas de la asignatura, se declara el uso de herramientas de inteligencia artificial generativa (**Google Gemini**) como apoyo durante el desarrollo de este proyecto en las siguientes actividades:

- **En qué partes se utilizó:** Estructuración modular de la arquitectura en Flask/Jinja2, optimización del preprocesamiento OCR con Pillow y expresiones regulares, implementación de los controles de la Web Speech API, revisión de criterios técnicos WCAG 2.2 AA, análisis de vulnerabilidades de seguridad/privacidad y redacción inicial de la documentación técnica.
- **Cómo se revisó y validó el resultado:** Todo el código generado fue integrado, probado y depurado por el equipo en PythonAnywhere tanto en computadoras de escritorio como en dispositivos móviles, verificando manualmente el reconocimiento de texto, la síntesis de voz, la navegación por teclado, los formularios y los resultados de las auditorías de accesibilidad y pruebas con personas usuarias. El equipo asume la responsabilidad total sobre el sistema y la documentación entregada.