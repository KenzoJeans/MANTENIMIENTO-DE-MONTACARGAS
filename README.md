# Mantenimiento de Montacargas – Kenzo Jeans

Tablero interactivo para consultar el historial de mantenimiento de los montacargas. Muestra un **modelo 3D del montacargas** con pines sobre cada componente: al hacer clic en un pin se despliegan los reportes de mantenimiento de esa zona, con operario, fecha, estado y fotos de evidencia.

## Propósito

- Reemplazar el registro en papel de los mantenimientos por reportes digitales.
- Consultar el historial de cada componente de forma visual e intuitiva.
- Tener a la vista el horómetro actual, el total de mantenimientos y el porcentaje de mantenimiento preventivo.
- Guardar evidencia fotográfica asociada a cada intervención.

## Cómo fluye la información

```
Formulario de Google  →  Hoja de cálculo (Google Sheets)  →  Tablero (Streamlit + modelo 3D)
```

1. El operario o técnico reporta el mantenimiento en el formulario de Google (con fotos opcionales, que se guardan en Drive).
2. Las respuestas llegan a la hoja de cálculo.
3. El tablero lee la hoja como CSV. Los datos se actualizan solos cada 30 segundos, o al pulsar **"🔄 Actualizar ahora"**.

El ID de la hoja de cálculo está definido en la variable `ID_HOJA` del código.

## Columnas que espera la hoja

Los encabezados deben coincidir con estos nombres:

| Columna | Uso |
|---|---|
| `Marca temporal` | Orden de los reportes (del más reciente al más antiguo) |
| `FECHA` | Fecha mostrada en cada reporte |
| `NOMBRE DEL OPERARIO` | Quién realizó el mantenimiento |
| `COMPONENTE` | Zona del montacargas (define en qué pin aparece) |
| `NOMBRE DE LA PIEZA` | Título del reporte |
| `ESTADO` | Estado del componente o tipo de mantenimiento |
| `DESCRIPCION DEL MANTENIMIENTO` | Detalle del trabajo realizado |
| `HOROMETRO` | Lectura del horómetro |
| `EVIDENCIA DEL MANTENIMIENTO` | Enlaces a las fotos en Drive |

> **Importante:** si se cambia el texto de una pregunta en Google Forms, el encabezado de la hoja cambia también y hay que actualizarlo en el código. Para la columna de fotos se hace en la constante `COLUMNA_FOTO`.

## Cómo se interpreta la información

**Componentes.** El texto de la columna `COMPONENTE` se asigna a uno de 10 pines por palabra clave (sin importar tildes ni mayúsculas):

| Pin | Palabras clave |
|---|---|
| Llantas y ruedas | llanta, rueda |
| Mástil y cadenas elevadoras | mastil, cadena |
| Horquillas / uñas | horquilla |
| Sistema hidráulico | hidraulico |
| Motor | motor |
| Sistema de frenos | freno |
| Dirección | direccion |
| Chasis / estructura / techo protector | chasis, estructura, techo |
| Luces, bocina y alarma | luces, bocina, alarma |
| Horómetro y tablero | horometro, tablero |

También se reconocen los valores del formulario anterior en inglés (`wheel`, `mast`, `fork`, etc.), para no perder reportes históricos. Un valor que no coincida con ninguno **no aparece en ningún pin**.

**Estado.** Se marca con color según el texto:

- 🔴 contiene `CRIT` o `MALO`
- 🟡 contiene `ALER` o `REVIS`
- 🟢 cualquier otro valor

**Indicadores.**

- **Horómetro actual:** es el **valor máximo** registrado (el horómetro solo sube, así que no depende del orden de las fechas). Se leen solo los dígitos del valor.
- **Mantenimientos:** total de reportes en la hoja.
- **% Preventivo:** proporción de reportes cuyo `ESTADO` contiene `PREV`.

**Fotos.** Los enlaces de Drive de la columna de evidencia se convierten en miniaturas, clicables para ver la foto completa. Solo se aceptan enlaces que empiecen por `https://drive.google.com`. Las personas que consultan el tablero deben poder ver esos archivos en Drive, de lo contrario la miniatura no carga.

## Qué ofrece el tablero

- **Visor 3D** con pines que pulsan; se puede rotar, acercar y mover. Al elegir un componente, la cámara se acerca a su pin.
- **Selector manual** de componente (alternativa a hacer clic en el pin).
- **Panel de historial** con los reportes del componente elegido.
- **Tabla resumen de evidencias** con fecha, operario, componente, estado, horómetro y primera foto de cada reporte.
- Aviso en pantalla si no se pudo leer la hoja de cálculo.

## Cómo agregar un componente nuevo

El componente se define en cuatro lugares del archivo principal:

1. `REGLAS_COMPONENTE_NUEVO`: palabras clave para reconocerlo.
2. La lista de opciones del selector (`<select id="selector-componente">`).
3. El diccionario `titulosAlternativos` (título cuando aún no tiene reportes).
4. Una llamada a `agregarPin3D(...)` con su posición en el modelo.

Si el formulario cambia sus opciones, se actualizan estos cuatro puntos.

## Modelo 3D

- **Archivo:** `static/forklift_low_poly.glb`. Sin este archivo el visor no se muestra.
- Las posiciones de los pines son **estimaciones proporcionales** al tamaño del modelo. Si un pin queda mal ubicado, se ajustan los multiplicadores en las llamadas a `agregarPin3D`.
- **Origen y licencia del modelo:** `[completar]`

## Tecnologías y dependencias

Python con Streamlit y Pandas (requiere Python 3.10 o superior). El visor usa Three.js r128, cargado desde internet (cdnjs y jsDelivr), por lo que el equipo que consulta el tablero necesita conexión a internet.

> Completar: confirmar que `requirements.txt` incluya `streamlit` y `pandas`.

## Ejecución y despliegue

> Completar:

- **Archivo principal:** `[nombre del archivo, p. ej. app.py]`
- **Instalar dependencias:** `pip install -r requirements.txt`
- **Ejecutar en local:** `streamlit run [archivo].py`
- **Despliegue:** Streamlit Cloud, cuenta `[completar]`, URL `[completar]`
- **Formulario de reporte (enlace de respuesta):** `[completar]`
- **Carpeta de Drive con las fotos:** `[completar]`

## Responsables

- **Dueño del proceso (mantenimiento):** `[área / cargo]`
- **Mantenimiento técnico del tablero:** `[nombre / cargo]`
- **Cuentas (GitHub, Streamlit, Google):** `[a nombre de quién]`
