# Proyecto Final POO: Estimación de Medidas Corporales (MoveNet)

Aplicación web prototipo para la estimación aproximada de medidas antropométricas y talla de ropa. Utiliza la detección de pose por Inteligencia Artificial (IA) en el navegador para analizar la figura del usuario a través de la cámara del dispositivo.

El código base fue desarrollado para un proyecto final de **Programación Orientada a Objetos (POO)** con énfasis en la integración de tecnologías de visión artificial en el cliente (basado en el concepto Mtaylor).

##  Características

* **Detección de Pose en Tiempo Real:** Utiliza el modelo **MoveNet (TensorFlow.js)** para identificar los 17 puntos clave del cuerpo y superponer un esqueleto de referencia en el video.
* **Validación de Calidad:** Comprueba en vivo si el **cuerpo completo** está visible, si la figura es detectada con alta confianza y si la grabación tiene calidad suficiente para el análisis.
* **Estimación Antropométrica:** Calcula medidas clave como Altura, Hombros, Pecho, Cintura y Cadera basándose en la distancia entre los keypoints detectados.
* **Recomendación de Talla:** Sugiere una talla de ropa aproximada (S, M, L, XL) utilizando la medida estimada del pecho.
* **Gestión de Usuarios:** Permite registrar y seleccionar un usuario activo para guardar y cargar las últimas mediciones realizadas en la sesión.

## Tecnologías

| Componente | Tecnología | Propósito |
| :--- | :--- | :--- |
| **Frontend** | HTML5, CSS3, JavaScript | Estructura, Estilos, Lógica del lado del cliente. |
| **IA/Visión** | **TensorFlow.js** | Marco para ejecutar modelos de Machine Learning en el navegador. |
| **Modelo** | **MoveNet (SINGLEPOSE_LIGHTNING)** | Detección rápida y ligera de pose humana. |
| **Media** | `MediaRecorder` API | Captura y grabación de video en formato WebM. |

## Instalación y Ejecución

Al ser un proyecto de un solo archivo HTML que carga sus dependencias vía CDN, la instalación es muy sencilla.

1.  **Descarga:** Guarda el código proporcionado en un archivo llamado `index.html`.
2.  **Ejecución:** Abre el archivo `index.html` directamente en tu navegador web (Chrome, Firefox, Edge, Safari).

### Requisitos

* **Navegador Moderno:** Debe soportar WebGL y las APIs de MediaDevices (`getUserMedia`).
* **Permisos:** Se debe otorgar permiso de acceso a la cámara.
* **Conexión a Internet:** Necesaria para cargar las librerías de TensorFlow.js y MoveNet desde las CDN.

## Guía de Uso

1.  **Selección de Usuario:**
    * Ingresa un nombre en el campo "Ingresa tu nombre".
    * Haz clic en **Registrar / Seleccionar**. Esto habilita la cámara.
2.  **Iniciar Cámara:**
    * Presiona **Iniciar cámara**. Espera a que cargue el modelo de IA.
    * Si el encuadre no es correcto, usa el botón **Cambiar cámara** para alternar entre las cámaras (si estás en un móvil).
3.  **Validación en Vivo:**
    * Posiciónate frente a la cámara de manera que el indicador de **Cuerpo Completo** esté en verde. Debes estar de pies, con los tobillos y la cabeza visibles.
4.  **Grabación:**
    * Haz clic en **Grabar** y mantente quieto y bien posicionado durante unos segundos.
    * Haz clic en **Detener**. 
5.  **Análisis:**
    * El sistema analizará la calidad del video. Si el **Resultado Final** es **ÓPTIMO**, las **Mediciones Corporales Estimadas** y la **Talla recomendada** se mostrarán y se guardarán para tu usuario.

## Consideraciones sobre la Precisión

Es fundamental recalcar que las mediciones obtenidas son **aproximadas** debido a las limitaciones del modelo y la falta de un objeto de referencia real:

* **Escalamiento Fijo:** La relación píxel/centímetro se calcula asumiendo una altura corporal de **170 cm** para establecer la escala inicial. Si la altura real del usuario es diferente a 170 cm, todas las medidas serán inexactas.
* **Estimación Proporcional:** Medidas como Pecho y Cintura no se miden directamente sino que se estiman usando proporciones estándar a partir de las distancias de Hombros y Caderas.

Para mejorar la precisión en una versión futura, se recomienda:
1.  Permitir al usuario introducir su altura real.
2.  Integrar la detección y medición de un objeto de referencia conocido (ej. una tarjeta o regla) dentro del encuadre.

---
*Desarrollado como Proyecto Final POO.*
