# 📄 Documentación del Flujo Automatizado - Clasificación y Gestión de Tickets de Soporte

## 🎯 Objetivo del Flujo
Automatizar el procesamiento de correos entrantes para:

1. Clasificar si son solicitudes de soporte técnico.
2. Extraer y registrar datos relevantes en Google Sheets.
3. Enviar confirmaciones automáticas por correo.
4. Notificar al equipo por Slack (vía Webhook) y registrar el ticket en Trello.

---

## 🔁 Resumen del Flujo

1. **📥 Captura de Correos**
   - **Nodo:** `GET EMAIL` (`gmailTrigger`)
   - Revisa nuevos mensajes en Gmail cada minuto.

2. **🧹 Limpieza y Preparación**
   - **Nodo:** `Clear Data`
   - Extrae campos clave: `Fecha`, `Email`, `Asunto`, `Descripción`, `ID`.

3. **🧠 Clasificación con IA**
   - **Nodo:** `Validation support` (GPT-4o-mini)
   - Determina si el email corresponde a una solicitud de soporte técnico (`true` o `false`).

4. **🔀 Rama Condicional**
   - **Nodo:** `Switch`
   - Divide el flujo en:
     - Soporte Técnico
     - No Soporte Técnico

---

## 🛠️ Rama: Soporte Técnico

5. **📄 Extracción Detallada de Datos**
   - **Nodo:** `Clear Data 2`
   - IA extrae `Fecha`, `Email`, `Asunto`, `Descripción`, `Estado`.
   - Estado por defecto: `Nuevo`, o `Prioritario` si contiene palabras clave como `urgente`, `caído`, `no funciona`.

6. **📊 Registro en Google Sheets**
   - **Nodo:** `Google Sheets`
   - Inserta los datos en la hoja de soporte técnico.

7. **🧪 Evaluación de Prioridad**
   - **Nodo:** `If`
   - Verifica si el estado es `Prioritario`.

8. **📧 Notificaciones y Confirmaciones**
   - **Si es Prioritario:**
     - Email interno → `Email Slack`
     - Confirmación usuario → `Confirmacion Email`
     - Webhook Slack → `HTTP Slack Prioritario`
     - Trello → `Trello`
   - **Si NO es Prioritario:**
     - Confirmación usuario → `Confirmacion Email1`
     - Webhook Slack → `HTTP Slack No - Prioritario`
     - Trello → `Trello1`

---

## ❌ Rama: No Soporte Técnico

9. **📄 Extracción de Datos**
   - **Nodo:** `Clear Data`
   - Se extraen campos relevantes con IA.

10. **📊 Registro en otra hoja de Google Sheets**
    - **Nodo:** `Google Sheets1`
    - Guarda en la hoja de "No - Soporte Técnico".

11. **📧 Confirmación al Remitente**
    - **Nodo:** `Confirmacion Email2`
    - Notifica que fue redirigido a otra área.

12. **📋 Registro en Trello**
    - **Nodo:** `Trello2`

---

## 📡 Integraciones Utilizadas

| Herramienta         | Función Principal                           |
|---------------------|---------------------------------------------|
| **Gmail**           | Captura y envío de correos                  |
| **OpenAI (GPT-4o)** | Clasificación y extracción inteligente      |
| **Google Sheets**   | Registro estructurado de tickets            |
| **Trello**          | Gestión visual de tareas                    |
| **Webhook.site**    | Notificación externa (ej. Slack o logging)  |

---

## ✅ Resultado Esperado

- Los correos se clasifican automáticamente como soporte o no soporte.
- Se notifican al usuario y al equipo según la prioridad.
- Los tickets quedan registrados y organizados en Google Sheets y Trello.

---
