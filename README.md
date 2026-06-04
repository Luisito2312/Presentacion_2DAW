# Mi Memoria de Prácticas: Desarrollo y Refactorización de Integraciones para WordPress (AutomatorWP & GamiPress)

¡Hola! En este repositorio he subido todo el trabajo de desarrollo, auditoría y refactorización de plugins que he llevado a cabo durante mi periodo de prácticas de Formación Profesional Dual en la empresa. 

A continuación os detallo la memoria de mis actividades, cómo me he organizado semana a semana, las herramientas que he utilizado, la relación del trabajo con mis estudios y mi valoración personal de la experiencia.

---

## 🏢 1. Datos Generales de la Empresa
* **Empresa:** [TSUNOA GAMES - Creadores de GamiPress y AutomatorWP]
* **Sector:** Desarrollo de Software, WordPress Core, Automatización y Gamificación.
* **Actividad Principal:** Creación y mantenimiento de extensiones, integraciones y complementos avanzados para el ecosistema de automatización de WordPress (con un catálogo activo de más de 300 plugins).

---

## 👤 2. Actividad y Responsabilidades por Alumno

### Alumno: **[Luis Godino Wallace]**
* **Periodo de Estancia:** [02/02/2026] a [26/05/2026]
* **Puesto:** Desarrollador Backend WordPress Junior / Auditor de Código
* **Mi Responsabilidad Principal:** 
  Me he encargado de realizar la auditoría técnica de plugins heredados (*legacy*), refactorizar su código siguiendo el estándar de calidad y arquitectura de la empresa (Singleton y modularidad), integrar APIs de terceros y erradicar la deuda técnica (código basura y malas prácticas).

### 🔗 Vídeo de la Exposición
* **Enlace al vídeo:** [🎥 Ver Exposición de Prácticas - Luis Godino Wallace](https://enlace-al-video.com)

---

## 🛠️ 3. Resumen Técnico de los Plugins que he Refactorizado / Desarrollado

Durante mis prácticas, he trabajado directamente sobre los siguientes plugins de integración. El objetivo principal ha sido transformarlos desde su estado original (que solía ser inoperante, desestructurado o con mala puntuación en las auditorías iniciales de la empresa) hasta cumplir al 100% el "Estándar de Oro" de Tsunoa Games, que toma como referencia el plugin de `LatePoint`:

1. **AutomatorWP - Asana Integration (`automatorwp-asana`)**
   * **Estado Inicial:** Tenía una estructura procedimental básica, la lógica de las acciones estaba metida entera en un único archivo, las cadenas de texto no estaban preparadas para traducción (i18n) y no había ningún control de requisitos del sistema.
   * **Mejoras que he realizado:** Refactoricé completamente el plugin para implementar la clase Singleton principal (`AutomatorWP_Asana`). Separé la lógica de la acción *"Create task in Asana"* en su propio archivo modular (`includes/actions/create-task.php`), añadí soporte de traducción (i18n), implementé la función de validación de seguridad `meets_requirements()` y registré el icono vectorial corporativo (`.svg`).

2. **AutomatorWP - Google Sheets Integration (`automatorwp-google-sheets`)** *(Originalmente alojado en la carpeta `automatorwp-constant-contact`)*
   * **Estado Inicial:** El plugin original era una mezcla extraña e inservible. Tenía archivos e inicialización de Constant Contact, pero la acción `add-row.php` intentaba llamar a funciones de Google Sheets que ni siquiera existían en el código, sin autenticación ni endpoints funcionales.
   * **Mejoras que he realizado:** Hice una reestructuración completa del plugin. Renombré la carpeta, el archivo principal y las clases para llamarse `AutomatorWP_Google_Sheets`. Eliminé toda la API residual de Constant Contact y desarrollé desde cero el núcleo de conexión con Google Sheets API v4 usando OAuth 2.0 en `functions.php` (`get_spreadsheets()`, `get_worksheets()`), además de sanear adecuadamente todas las variables de entrada.

3. **AutomatorWP - FluentSMTP Integration (`automatorwp-fluentsmtp`)**
   * **Estado Inicial:** Tenía problemas de carga tardía de traducciones, los avisos en el dashboard se duplicaban molestando al administrador y no contaba con soporte de tags ni historial de logs en las automatizaciones.
   * **Mejoras que he realizado:** Unifiqué el patrón Singleton de la clase principal. Añadí disparadores nativos para los eventos de envío de correo exitoso y fallido (`trigger-email-delivery-success.php` y `trigger-error-in-email-delivery.php`), configuré las etiquetas dinámicas para que los usuarios puedan usar variables como `Email To` y `Email Subject` en sus automatizaciones de AutomatorWP, y conecté todo a la base de datos de logs usando `log_meta()` y `log_fields()`.

4. **AutomatorWP - MetForm Integration (`automatorwp-metform`)**
   * **Estado Inicial:** Lógica procedimental metida en closures anónimas dentro de los ganchos de carga de WordPress.
   * **Mejoras que he realizado:** Encapsulé todo el código en la clase final Singleton `AutomatorWP_MetForm`, implementé el cargador de avisos de administración (`admin_notices()`) y reescribí el verificador seguro para que no cargue el plugin a menos que tanto MetForm como AutomatorWP estén activos.

5. **AutomatorWP - Ontraport Integration (`automatorwp-ontraport`)**
   * **Estado Inicial:** Contenía muchísimo código basura. Al parecer, usaron una integración antigua de Trello como plantilla y se olvidaron de limpiar los archivos. Había 9 acciones inútiles (como crear tarjetas de Trello o añadir checklist) que no servían para nada en Ontraport.
   * **Mejoras que he realizado:** Hice una limpieza profunda de deuda técnica. Borré los 9 archivos inútiles de la carpeta `includes/actions/` y recorté en más de un 70% el peso de `functions.php` y `ajax-functions.php`, dejando el código limpio y enfocado exclusivamente en las dos funciones reales de Ontraport: crear y eliminar contactos en el CRM.

6. **AutomatorWP - PressPrimer Integration (`automatorwp-pressprimer`)**
   * **Estado Inicial:** Los disparadores no registraban etiquetas dinámicas para el usuario y no guardaban historial en el log de AutomatorWP.
   * **Mejoras que he realizado:** Refactoricé el archivo principal a Singleton, mejoré la carga de traducción en el arranque y añadí etiquetas dinámicas en el trigger de completar cuestionario (`quiz_name` y `quiz_score`), permitiendo que queden guardadas en el historial con `log_meta()` y `log_fields()`.

7. **AutomatorWP - Appointment Hour Booking (`automatorwp-appointment-hour-booking`)**
   * **Mejoras que he realizado:** Creé y adapté toda la estructura de la integración de reservas siguiendo el patrón Singleton de la empresa. Creé los disparadores para reservas completadas por usuarios (`user-booking-made`), por invitados (`guest-booking-made`) y para cancelaciones (`user-cancelled-booking`), asegurando que solo funcione si el plugin base de reservas está activo.

8. **GamiPress - Referrals (`gamipress-referrals`)**
   * **Mejoras que he realizado:** Adapté el plugin para ser compatible con versiones recientes de GamiPress, implementé el almacenamiento en caché de recuentos de referidos en la metadata de usuario para mejorar la velocidad de carga de la base de datos, y optimicé las integraciones de venta para WooCommerce y Easy Digital Downloads.

---

## 📅 4. Mi Temporalización por Semanas

| Semana | Bloque de Actividades | Plugins en los que he Trabajado |
| :---: | :--- | :--- |
| **Semanas 1 - 2** | **Inducción y Auditoría:** Aprendí el estándar corporativo tomando `LatePoint` como guía. Realicé mis primeras auditorías de deuda técnica y sostenibilidad en los plugins de Asana y Metform. | `automatorwp-asana`, `automatorwp-metform` |
| **Semanas 3 - 4** | **Refactorización Básica:** Migré el código de procedimental a Singleton. Escribí los métodos de instancia y modularicé las acciones dentro del directorio `/actions/`. | `automatorwp-asana`, `automatorwp-metform` |
| **Semanas 5 - 6** | **Trazabilidad y Traducciones:** Estandaricé el registro de tags y los logs de auditoría de automatizaciones. Añadí soporte de traducciones e implementé avisos de administración no duplicados. | `automatorwp-pressprimer`, `automatorwp-fluentsmtp` |
| **Semanas 7 - 8** | **Limpieza de Deuda Técnica:** Encontré y borré el código copiado de Trello en Ontraport. Eliminé los 9 archivos inservibles y optimicé el archivo de funciones para aligerar el plugin. | `automatorwp-ontraport` |
| **Semanas 9 - 10** | **Desarrollo de Integración API:** Reorganicé el plugin híbrido de Google Sheets. Escribí el flujo de OAuth 2.0 y la conexión con su API v4 para añadir filas a hojas de cálculo. | `automatorwp-constant-contact` (Google Sheets) |
| **Semana 11** | **Triggers de Reserva y Ajustes:** Construí los disparadores para Appointment Hour Booking y apliqué optimizaciones de caché de referidos en GamiPress Referrals. | `automatorwp-appointment-hour-booking`, `gamipress-referrals` |
| **Semana 12** | **Pruebas y Documentación:** Hice pruebas de integración, verifiqué que las traducciones funcionaran bien, preparé los informes de cumplimiento y redacté la documentación final. | Todos los plugins trabajados |

---

## 🛠️ 5. Herramientas que he Utilizado
* **Lenguajes:** PHP (Orientado a Objetos, namespaces), JavaScript, HTML5, CSS y SVG.
* **Entorno Local y Servidor:** LocalWP, XAMPP, PhpStorm y VS Code.
* **APIs y Pruebas:** Postman, Google Cloud Console (gestión de credenciales de OAuth 2.0), Asana Developer Dashboard y Ontraport API.
* **Control de Versiones:** Git, GitHub y Subversion (SVN) para enviar actualizaciones a WordPress.org.
* **Depuración:** Query Monitor, WP_DEBUG y WP-CLI.

---

## 🎓 6. Relación con los Módulos de mis Estudios (DAW / DAM)

Así es como he aplicado lo aprendido en clase al trabajo diario en la empresa:

* **Desarrollo Web en Entorno Servidor (DWES):**
  * He aplicado patrones de diseño como el Singleton para evitar múltiples instancias de un mismo plugin.
  * He consumido APIs REST de terceros usando las funciones nativas de WordPress (`wp_remote_post` y `wp_remote_get`).
  * He configurado y comprendido flujos de autenticación OAuth 2.0 (Google Sheets).
* **Desarrollo Web en Entorno Cliente (DWEC):**
  * He gestionado la carga condicional de scripts (`wp_enqueue_script`) para mantener el backend rápido, y he manejado respuestas y callbacks de AJAX en WordPress.
* **Diseño de Interfaces Web (DIW):**
  * He integrado iconos vectoriales (.svg) optimizados para el menú de administración y he estructurado los avisos (`admin_notices`) usando los estilos CSS nativos de WordPress.
* **Despliegue de Aplicaciones Web (DAW) / Bases de Datos:**
  * He trabajado con Git para organizar mi código en ramas (Feature Branching).
  * He optimizado y realizado consultas y guardado de datos dinámicos (`log_meta`) en la base de datos de WordPress (tablas de metadatos).
* **Sistemas de Gestión Empresarial (SGE):**
  * He programado la interconexión automática de flujos empresariales integrando WordPress con herramientas externas de gestión y CRM (Google Sheets, Asana y Ontraport).

---

## 📝 7. Mi Valoración Personal de la Experiencia
Para mí, estas prácticas en Tsunoa Games están siendo una experiencia genial. Trabajar directamente sobre una base de código real que usan miles de usuarios me ha hecho ver la verdadera importancia de escribir un código limpio, estructurado y, sobre todo, sostenible.

A nivel técnico, me ha servido mucho no solo para programar cosas nuevas, sino para aprender a leer código de otros, auditarlo y refactorizarlo. Descubrir problemas como la deuda técnica en Ontraport y resolverlos me ha dado mucha soltura. Además, he aprendido cómo trabaja un equipo profesional para coordinar el mantenimiento de más de 300 complementos gracias a estándares estrictos. Es un aprendizaje que me va a venir de lujo para mi futuro como desarrollador.
