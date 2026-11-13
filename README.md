# Aplicación Streamlit para el precio de casas en California

La aplicación estima el precio medio de las viviendas de una zona de California.
El usuario introduce dos datos (ingreso medio y número medio de habitaciones); la
app completa el resto con valores por defecto, valida que están las 8 variables del
modelo, consulta el modelo y muestra el precio en dólares.

Se compone de dos servicios: la **API FastAPI** (`app.py`), que carga el modelo
`modelo_california.pkl` y expone `POST /predict`, y la **interfaz Streamlit**
(`streamlit_app.py`), que envía los datos en JSON a la API por HTTP y formatea la
respuesta. Se conectan mediante la URL pública de la API (`API_URL`).

Autores: Alejandro Gracia, Dan Bolocan

## Servicios desplegados en Render

| Servicio | Qué es | URL |
|----------|--------|-----|
| API | FastAPI con el modelo (`app.py`) | https://TU-SERVICIO-API.onrender.com |
| Interfaz | Streamlit (`streamlit_app.py`), que llama a la API | https://TU-SERVICIO-STREAMLIT.onrender.com |

## Cómo ejecutarla en local

```bash
pip install -r requirements.txt
# Terminal 1: API (http://localhost:8000, docs en /docs)
uvicorn app:app --reload
# Terminal 2: interfaz
streamlit run streamlit_app.py
```

En la barra lateral de la interfaz, la URL de la API por defecto es
`http://localhost:8000`.

## Despliegue en Render

| Servicio | Build Command | Start Command |
|----------|---------------|---------------|
| API | `pip install -r requirements.txt` | `uvicorn app:app --host 0.0.0.0 --port $PORT` |
| Interfaz | `pip install -r requirements.txt` | `streamlit run streamlit_app.py --server.port $PORT --server.address 0.0.0.0 --server.headless true` |

La URL de la API se cambia en `streamlit_app.py`, constante `API_URL` (TODO 0):
se comenta la línea de `localhost` y se descomenta la de Render. También se puede
cambiar en la barra lateral de la interfaz.

## Documentación de la API

URL base: <https://TU-SERVICIO-API.onrender.com>

### `POST /predict`

Recibe un JSON con las 8 variables de una zona censal y devuelve el precio medio
estimado de sus viviendas, en cientos de miles de dólares.

**Ejemplo de petición**

```json
{
  "MedInc": 5.0,
  "HouseAge": 29.0,
  "AveRooms": 6.0,
  "AveBedrms": 1.05,
  "Population": 1166.0,
  "AveOccup": 2.8,
  "Latitude": 34.26,
  "Longitude": -118.49
}
```

**Ejemplo de respuesta correcta (200)**

```json
{
  "MedHouseVal": 2.239282093596062
}
```

(Equivale a 223.928 $: la app multiplica por 100.000 y muestra `$223,928`.)

**Errores**

| Código | Cuándo ocurre | Qué muestra la aplicación |
|--------|---------------|---------------------------|
| 422 | Falta un campo o tiene un tipo incorrecto | «La API devolvió un error (código 422). Detalle: ...» con el campo que falta |
| Sin respuesta | La API no está disponible o tarda demasiado | «No se pudo contactar con la API...» con qué comprobar y cuándo reintentar |
