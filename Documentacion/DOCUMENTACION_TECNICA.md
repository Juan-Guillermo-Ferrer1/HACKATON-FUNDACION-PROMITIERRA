# 📖 Documentación Técnica - Hackathon IA & Automatización 2026

## Fundación ProMiTierra | Caquetá, Colombia

> Este documento es una guía técnica para los participantes de la Hackathon. Aquí encontrarás todo lo necesario para integrar herramientas de IA, automatización y desplegar tu proyecto en nuestra infraestructura.

---

## 🧠 1. Arquitectura de IA

<p align="center">
  <img src="../.logos/openai.png" height="50"> &nbsp;&nbsp;
  <img src="../.logos/anthropic.png" height="50"> &nbsp;&nbsp;
  <img src="../.logos/ollama.png" height="50">
</p>

### Opciones de Integración

#### Opción A: APIs Externas (OpenAI, Anthropic, Hugging Face)

```python
# Ejemplo con OpenAI (Python)
from openai import OpenAI
import os
from dotenv import load_dotenv

load_dotenv()

client = OpenAI(api_key=os.getenv("OPENAI_API_KEY"))

def generar_respuesta(prompt: str) -> str:
    response = client.chat.completions.create(
        model="gpt-4o",
        messages=[{"role": "user", "content": prompt}]
    )
    return response.choices[0].message.content

# Uso
respuesta = generar_respuesta("¿Cómo puedo optimizar procesos en mi empresa?")
print(respuesta)
```

```javascript
// Ejemplo con OpenAI (Node.js)
const OpenAI = require('openai');
require('dotenv').config();

const client = new OpenAI({ apiKey: process.env.OPENAI_API_KEY });

async function generarRespuesta(prompt) {
  const response = await client.chat.completions.create({
    model: "gpt-4o",
    messages: [{ role: "user", content: prompt }]
  });
  return response.choices[0].message.content;
}
```

#### Opción B: Modelos Locales (Ollama)

```python
# Ejemplo con Ollama
import ollama

def generar_respuesta_local(prompt: str) -> str:
    response = ollama.chat(
        model="llama3",
        messages=[{"role": "user", "content": prompt}]
    )
    return response['message']['content']

# Uso local sin conexión a internet
respuesta = generar_respuesta_local("Explain, like I'm five, how AI works")
print(respuesta)
```

#### Opción C: LangChain (Flujos Avanzados)

```python
# Cadena de IA con memoria y herramientas
from langchain_openai import ChatOpenAI
from langchain.prompts import ChatPromptTemplate
from langchain.tools import Tool
from langchain.agents import AgentExecutor, create_openai_functions_agent

llm = ChatOpenAI(temperature=0.7)

prompt = ChatPromptTemplate.from_template(
    "Eres un asistente útil. Ayúdame a resolver: {input}"
)

# Definir herramientas personalizadas
def buscar_informacion(query: str) -> str:
    # Lógica de búsqueda
    return f"Resultados para: {query}"

tools = [
    Tool.from_function(
        func=buscar_informacion,
        name="buscar_info",
        description="Útil para buscar información específica"
    )
]

agent = create_openai_functions_agent(llm, tools, prompt)
agent_executor = AgentExecutor(agent=agent, tools=tools, verbose=True)

result = agent_executor.invoke({"input": "¿Qué tecnologías usa ProMiTierra?"})
print(result['output'])
```

---

## ⚙️ 2. Automatización de Tareas

<p align="center">
  <img src="../.logos/python.png" height="40"> &nbsp;&nbsp;
  <img src="../.logos/nodejs.png" height="40"> &nbsp;&nbsp;
  <img src="../.logos/n8n.png" height="40">
</p>

### Python Scripts

```python
# automacion_procesos.py
import schedule
import time
import os
from datetime import datetime

def tarea_automatizada():
    """Función que se ejecuta periódicamente"""
    print(f"Ejecutando tarea: {datetime.now()}")
    # Tu lógica aquí
    # - Procesar archivos
    # - Consultar APIs
    # - Generar reportes
    # - Enviar notificaciones

# Programar tareas
schedule.every().hour.do(tarea_automatizada)
schedule.every().day.at("08:00").do(tarea_automatizada)

print("Automatización iniciada...")
while True:
    schedule.run_pending()
    time.sleep(60)
```

### Node.js Scripts

```javascript
// automacion.js
const cron = require('node-cron');

function tareaAutomatizada() {
  console.log(`Ejecutando tarea: ${new Date()}`);
  // Tu lógica aquí
}

// Programar tareas
cron.schedule('0 * * * *', () => {
  tareaAutomatizada();
});

cron.schedule('0 8 * * *', () => {
  tareaAutomatizada();
});

console.log('Automatización iniciada...');
```

### Flujos con n8n (Low-Code)

```yaml
# workflow-n8n.yml
name: Flujo Automatizado
nodes:
  - name: Trigger
    type: n8n-nodes-base.cron
    parameters:
      rule:
        interval:
          - field: hours
            minutesInterval: 1

  - name: ProcesarDatos
    type: n8n-nodes-base.code
    parameters:
      code: |
        const items = $input.all();
        return items.map(item => ({
          json: {
            ...item.json,
            procesado: true,
            fecha: new Date().toISOString()
          }
        }));

  - name: GuardarResultado
    type: n8n-nodes-base.httpRequest
    parameters:
      method: POST
      url: "https://tu-api.com/datos"
```

---

## 🐳 3. Despliegue con Docker y DockPloy

<p align="center">
  <img src="../.logos/docker.png" height="60"> &nbsp;&nbsp;
  <img src="../.logos/dokploy.png" height="60">
</p>

### Dockerfile Estándar

```dockerfile
# Dockerfile
FROM python:3.11-slim

WORKDIR /app

# Instalar dependencias
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Copiar código
COPY . .

# Variables de entorno
ENV PYTHONUNBUFFERED=1

# Puerto
EXPOSE 8000

# Comando de inicio
CMD ["python", "main.py"]
```

### Docker Compose (Multi-Servicio)

```yaml
# docker-compose.yml
version: '3.8'

services:
  app:
    build: .
    ports:
      - "8000:8000"
    env_file:
      - .env
    depends_on:
      - db
      - redis

  db:
    image: postgres:15
    environment:
      POSTGRES_DB: hackathon
      POSTGRES_USER: user
      POSTGRES_PASSWORD: password
    volumes:
      - postgres_data:/var/lib/postgresql/data

  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"

volumes:
  postgres_data:
```

### Archivo de Despliegue para DockPloy

```yaml
# dockploy.yml
name: hackathon-promitierra
version: 1.0

services:
  web:
    build: .
    ports:
      - 8000:8000
    environment:
      - NODE_ENV=production
    env_file:
      - .env
    healthcheck:
      path: /health
      interval: 30s
      timeout: 10s
      retries: 3

  workers:
    command: python worker.py
    replicas: 2
```

###构建 y Desplegar

```bash
# Construir imagen local
docker build -t hackathon-app .

# Probar localmente
docker run -p 8000:8000 --env-file .env hackathon-app

# Desplegar a DockPloy (desde tu servidor)
dockploy deploy -f dockploy.yml
```

---

## 🔐 4. Configuración de Variables de Entorno

### Archivo .env (No subir a Git)

```bash
# .env - Copiar y renombrar este archivo
# IMPORTANTE: Añadir .env a .gitignore

# === API Keys ===

# OpenAI (GPT-4)
OPENAI_API_KEY=sk-tu-clave-aqui

# Anthropic (Claude)
ANTHROPIC_API_KEY=sk-ant-tu-clave-aqui

# Hugging Face
HF_API_KEY=hf_tu_clave_aqui

# === Locales ===

# Ollama (modelos locales)
OLLAMA_BASE_URL=http://localhost:11434
OLLAMA_MODEL=llama3

# === Base de Datos ===

DB_HOST=localhost
DB_PORT=5432
DB_NAME=hackathon_db
DB_USER=usuario
DB_PASSWORD=contraseña_segura

# === Redis ===

REDIS_URL=redis://localhost:6379

# === Aplicación ===

APP_ENV=development
APP_PORT=8000
APP_URL=http://localhost:8000

# === Seguridad ===

SECRET_KEY=tu-clave-secreta-aqui
JWT_SECRET=otro-secreto-aqui
```

### .gitignore

```gitignore
# .gitignore
node_modules/
__pycache__/
*.pyc
.env
.env.local
*.log
dist/
build/
.DS_Store
```

### Cargar Variables en Python

```python
from dotenv import load_dotenv
import os

load_dotenv()  # Carga .env automáticamente

api_key = os.getenv("OPENAI_API_KEY")
if not api_key:
    raise ValueError("OPENAI_API_KEY no está configurada")
```

### Cargar Variables en Node.js

```javascript
require('dotenv').config();

const apiKey = process.env.OPENAI_API_KEY;
if (!apiKey) {
  throw new Error('OPENAI_API_KEY no está configurada');
}
```

---

## 🛠️ 5. Tech Stack Recomendado

<p align="center">
  <img src="../.logos/python.png" height="40"> &nbsp;&nbsp;
  <img src="../.logos/nodejs.png" height="40">
</p>

<p align="center">
  <img src="../.logos/openai.png" height="40"> &nbsp;&nbsp;
  <img src="../.logos/ollama.png" height="40"> &nbsp;&nbsp;
  <img src="../.logos/anthropic.png" height="40">
</p>

<p align="center">
  <img src="../.logos/postgresql.png" height="40"> &nbsp;&nbsp;
  <img src="../.logos/mongodb.png" height="40">
</p>

<p align="center">
  <img src="../.logos/docker.png" height="40"> &nbsp;&nbsp;
  <img src="../.logos/github.png" height="40">
</p>

| Categoría | Recomendado | Alternativo |
|-----------|-------------|-------------|
| **Lenguaje** | Python 3.10+ | Node.js 18+ |
| **Framework API** | FastAPI, Flask | Express.js |
| **IA/LLM** | OpenAI, Anthropic, Ollama | Hugging Face, LangChain |
| **Base de Datos** | PostgreSQL | MongoDB, SQLite |
| **Cola de Tareas** | Celery, Redis | BullMQ |
| **Contenedores** | Docker | Podman |
| **Despliegue** | DockPloy | Railway, Render |

---

## 📚 6. Recursos Adicionales

### Documentación Oficial
- [OpenAI API](https://platform.openai.com/docs)
- [Anthropic Claude](https://docs.anthropic.com)
- [LangChain](https://python.langchain.com)
- [Ollama](https://github.com/ollama/ollama)
- [FastAPI](https://fastapi.tiangolo.com)
- [Docker](https://docs.docker.com)

### Tutorías Disponibles
- Canal de Discord: [Por definir]
- Sesiones de mentoría: [Por definir fecha]
- Documentación interna: docs/

---

## ⚡ Próximos Pasos

1. **Clona este repositorio** o usa la plantilla
2. **Configura tu .env** con las API keys
3. **Desarrolla tu solución** usando IA y/o automatización
4. **Prueba localmente** con Docker
5. **Prepara el despliegue** para DockPloy
6. **Entrega** creando un Issue

---

## ❓ Preguntas

Si tienes dudas técnicas, crea un Issue usando la plantilla "Pregunta / Duda" o consulta el canal oficial de soporte.

---

*¡Éxito en tu proyecto! 🎯*
*Fundación ProMiTierra - Innovación con Sentido Humano*