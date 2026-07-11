# 🚀 Automatizacion de subida de parciales con marca de agua con n8n

Este repositorio contiene un flujo de automatización desarrollado en **n8n** diseñado para optimizar, estandarizar y brandear la recepción y distribución de material académico (exámenes parciales) para la comunidad estudiantil de **Byte** (Facultad de Informática - UNLP).

El sistema automatiza todo el ciclo de vida del archivo: recibe imágenes o PDFs mediante un Bot de **Telegram**, aplica la marca de agua correspondiente de forma dinámica, organiza el almacenamiento en **Google Drive** según la materia/año, y notifica en tiempo real en los canales específicos de **Discord**.

---

## 📸 Arquitectura del Flujo (n8n)

*Próximamente: Colocá acá una captura de pantalla de tu flujo visual en n8n para que entre por los ojos al entrar al repositorio.*

---

## 🛠️ Características Principales

- **Procesamiento Híbrido de Archivos:** Lógica condicional que discrimina si el archivo entrante es una imagen o un documento PDF.
- **Estampado Dinámico de PDFs (`pdf-lib`):** Inyección de código JavaScript personalizado para procesar PDFs página por página, calculando mediante factor de escala la resolución y posición para centrar el logo al 90% del ancho de la hoja sin deformarlo.
- **Ruteo Dinámico de Almacenamiento:** Parseo del *caption* del mensaje (`Materia-Año-Parcial`) en JavaScript para buscar e indexar el ID de destino en un diccionario de carpetas de Google Drive.
- **Notificaciones Multiplataforma:** Integración paralela con la API de Discord para alertar a los estudiantes en el canal correcto adjuntando el archivo ya brandeado.
- **Infraestructura Self-Hosted:** Todo el entorno corre de manera local y eficiente dentro de un contenedor Docker[cite: 1].

---

## 💻 Detalles Técnicos y Código

### Tecnologías Utilizadas
*   **n8n** (Workflow Automation Tool)[cite: 1]
*   **Node.js / JavaScript** (Nodos de código personalizado)[cite: 1]
*   **pdf-lib** (Manipulación de documentos PDF en memoria)[cite: 1]
*   **Docker** (Containerización)[cite: 1]
*   **Telegram Bot API, Google Drive API & Discord API**[cite: 1]

### Lógica de Ruteo (Snippet de Ejemplo)
El flujo utiliza un diccionario en formato clave-valor para mapear las siglas de las materias con sus respectivas carpetas y canales[cite: 1]. Si el formato ingresado por el usuario es correcto, procesa el binario sin perder el contexto en n8n[cite: 1]:

```javascript
// Ejemplo conceptual de la lógica utilizada en los nodos de código
const BaseDatos = {
  "Cadp-1-2026": "1QiD7tdqOnmsSj0X5G3BQupf8LI14xDRQ",
  "M3-2-2026": "1Mfn0mxVru4Uq8iuCu7UuwndUj-sxYR_y"
};

const captionOriginal = $('Disparador de imagen ').first().json.message.caption;
const nombreArchivo = captionOriginal.split('-').slice(0, 3).join('-');

const idDestino = BaseDatos[nombreArchivo];
if (idDestino) {
  $input.item.json.id_carpeta_drive = idDestino;
  return $input.item;
} else {
  throw new Error("La ruta ingresada no existe en la base de datos.");
}
