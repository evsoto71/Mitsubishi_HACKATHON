# Guía de Integración Frontend: Sistema “Expediente Único”

**Hackathon Mitsubishi Electric**

## 1. Resumen de la Arquitectura

El objetivo de la interfaz es capturar los datos de un incidente en la línea de producción, enviarlos a nuestro motor de IA y renderizar la respuesta estructurada (8D, Ishikawa, 5 Porqués) en un documento interactivo y exportable.

- **Stack Recomendado:** React.js, Tailwind CSS.
- **Patrón de UI:** Layout de 2 columnas (Izquierda: Formulario — Derecha: Visualización de Documento).
- **Tiempo de respuesta esperado:** 5 a 10 segundos (procesamiento de IA).

## 2. Especificaciones de la API (Backend)

La API ya está configurada con políticas CORS abiertas (`*`) para permitir peticiones desde localhost durante el desarrollo.

- **Endpoint:** `https://api.ledsc.online/webhook/crear-expediente`
- **Método:** `POST`
- **Headers requeridos:** `Content-Type: application/json`

### 2.1. Estructura del Payload (Cuerpo de la Petición)

El formulario de React debe construir y enviar un objeto JSON con los siguientes campos exactos:

| Campo JSON | Tipo de Input en UI | Ejemplo de Valor |
|---|---|---|
| `fecha_inicio` | `date` | `"2026-10-07"` |
| `id_item` | `text` | `"ITEM-8472"` |
| `no_version_incidente` | `text` | `"ACC-2026-V1"` |
| `descripcion_incidente` | `textarea` | `"La banda transportadora B se detuvo..."` |
| `responsable` | `text` | `"Carlos Ramírez"` |
| `colaboradores` | `text` | `"María Hernández"` |
| `posible_causa` | `textarea` | `"Falta de lubricación en rodamientos"` |

### 2.2. Estructura de la Respuesta (200 OK)

El servidor devolverá un objeto JSON donde cada valor es una cadena de texto formateada con sintaxis Markdown.

| Clave JSON | Descripción del Contenido |
|---|---|
| `borrador_8d` | Estructura del equipo y definición del problema (Formato 8D). |
| `ishikawa` | Categorización de causas raíz (Mano de obra, Máquina, etc.). |
| `cinco_porques` | Cadena causal lógica iterativa. |
| `campos_faltantes` | Advertencias de la IA sobre datos omitidos en el reporte. |

## 3. Fases de Desarrollo Frontend

### 3.1. Fase 1: Estado y Formulario (Lado Izquierdo)

1. Crea un estado local para el formulario (`useState` o `react-hook-form`).
2. Implementa la función de `onSubmit` que prevenga la recarga de la página (`e.preventDefault()`).
3. **Estado crítico de carga:** Crea un estado `isLoading`. Cuando la petición inicie, bloquea el botón de **“Analizar”** y muestra un spinner con el mensaje: **“Analizando incidente con Gemini AI...”**.

### 3.2. Fase 2: Renderizado de Resultados (Lado Derecho)

1. Diseña un contenedor que simule una hoja de papel (fondo blanco, sombra, bordes redondeados, padding amplio).
2. Instala y utiliza la librería `react-markdown` para interpretar la respuesta de la API. No inyectes el texto directamente, ya que contiene asteriscos y saltos de línea de Markdown.
3. Mapea las 4 claves del JSON devuelto en secciones visuales.

### 3.3. Fase 3: Edición y Exportación (Funciones Core)

Para la demostración ante los jueces, el sistema debe ser interactivo:

1. **Modo Edición:** Agrega un botón de **“Editar Documento”** que cambie el componente `react-markdown` por un `<textarea>` donde el usuario pueda corregir los textos generados por la IA.
2. **Exportación a PDF:** Instala `html2pdf.js` o `react-to-pdf`. Agrega un botón primario de **“Exportar Reporte a PDF”** que capture el contenedor del documento (lado derecho) y lo descargue al equipo local.

## 4. Dependencias Recomendadas

Ejecuta esto en tu terminal para inicializar las herramientas sugeridas:

```bash
npm install react-markdown html2pdf.js axios
```

## 5. Notas Críticas para la Demostración

- **Manejo de Errores:** Si la API devuelve un error, asegúrate de apagar el estado `isLoading` y mostrar un Toast/Alerta amigable en pantalla para no dejar la UI congelada.
- **Mocks de Emergencia:** Mantén un JSON de respuesta “falso” (*mock*) guardado en el código fuente de React. Si el internet falla durante el pitch del hackathon, puedes desconectar el `fetch` y renderizar el mock para asegurar que el jurado vea la interfaz funcionando.
