# AgentKit Fintech Assistant

**AgentKit Fintech** es un agente financiero inteligente desarrollado con las herramientas de **OpenAI Agent Builder** y el **ChatKit Starter Pack**, que analiza datos de clientes, productos y transacciones para ofrecer recomendaciones financieras personalizadas, simulaciones de inversión y alertas de riesgo crediticio.

![Flujo del Agente](./Documents/Workflow.png)

## Descripción general

El asistente utiliza tres archivos estructurados en formato CSV/TXT para simular una base de datos bancaria:

- `clientes.csv` → Información del cliente (segmento, ingresos, score_credito, etc.)
- `productos.csv` → Catálogo de productos financieros (CDTs, créditos, cuentas, etc.)
- `transacciones.csv` → Movimientos financieros por cliente (fecha, tipo, monto, días_mora, etc.)

Estos archivos se cargan en un **Vector Store** dentro de la **OpenAI Platform**, permitiendo que el agente los consulte dinámicamente a través de la herramienta **File Search**.

![Vector Store](./Documents/Vector%20Store.png)

## Configuración del Agente

### Contexto

```text
Eres un asesor financiero que puede buscar información en los archivos `clientes.txt`, `productos.txt` y `transacciones.txt`
que contienen los datos de los clientes, sus productos financieros y sus transacciones.

Tu objetivo es responder a solicitudes del usuario sobre análisis de clientes o decisiones financieras.

Pasos:
1. Usa la tool “Fintech” (File Search) para encontrar información relevante sobre los archivos.
2. Calcula métricas simples:
   - Atrasos: cantidad de transacciones con días_mora > 0
   - Max mora: máximo de días_mora
   - Gasto crédito: suma de montos donde tipo = "cargo_credito"
   - Depósitos: suma de montos donde tipo = "deposito"
   - n_tx: cantidad total de transacciones
   - Diversidad MCC: número de códigos MCC distintos
3. Con base en las métricas, recomienda una acción:
   - Si hay atrasos → plan de pagos.
   - Si el cliente tiene alto score y muchos depósitos → sugerir inversión.
   - Si tiene gastos altos → control de crédito.
   - Si tiene ingresos bajos y score bajo → asesoría de riesgo.

Devuelve una respuesta en texto natural, como asesor financiero, incluyendo la recomendación y los datos relevantes.
```

### Modelo
![Configuración del Agente](./Documents/Agent%20Configuration.png)

## Funcionalidades principales

### 1. Análisis del cliente
- Identifica automáticamente al cliente por su ID o contexto.
- Resume indicadores clave de comportamiento (score_credito, ingresos, mora, etc.).
- Calcula métricas de los últimos 90 días:
  - Atrasos (`dias_mora > 0`)
  - Depósitos (`tipo = deposito`)
  - Gastos con tarjeta (`tipo = cargo_credito`)
  - Diversidad de comercios (conteo de `mcc` distintos)

### 2. Recomendación de acción
A partir del análisis, el agente puede sugerir una de las siguientes acciones:

| Acción | Descripción |
|--------|--------------|
| **oferta_inversion** | Recomienda productos de inversión (CDTs, fondos, etc.) |
| **plan_pagos** | Sugiere un plan de pago por moras o retrasos |
| **retencion_fee_waiver** | Ofrece exoneraciones o beneficios de fidelización |
| **ninguna** | Si no se detecta oportunidad o riesgo |

### 3. Integración conversacional
El agente está disponible a través de **ChatKit**, permitiendo interacción en lenguaje natural.  
Ejemplo:

> 💬 “Tengo unos ahorros y quiero invertirlos.”  
> 🤖 “Puedo ofrecerte un CDT a 90 días con una tasa del 9.8%. ¿Deseas más detalles?”

---

## Instalación y uso

### Clonar repositorio

```bash
git clone https://github.com/Tomaslopera/AgentKit_Fintech.git
```

### Configurar Entorno

```bash
cd openai-chatkit-starter-app
```

> Crea un archivo .env local

```bash
OPENAI_API_KEY=sk-....
NEXT_PUBLIC_CHATKIT_WORKFLOW_ID=wf_68eaaf66f66c8190b8f94dfdf8cd72a705b0937215d781bd
NEXT_PUBLIC_CHATKIT_WORKFLOW_VERSION=production
```

### Ejecutar

```bash
cd openai-chatkit-starter-app
npm install
npm run dev
```

## Casos de Uso

### Análisis individual
> “Muéstrame el resumen del cliente 3.”

> “¿Cuánto ha depositado el cliente 2 en los últimos 90 días?”

### Recomendaciones de inversión
> “Quiero invertir mis ahorros.”

> “¿Qué producto financiero me recomiendas según mi perfil?”

### Alertas y riesgo
> “¿Qué clientes tienen moras mayores a 30 días?”

> “Identifica clientes con score_credito bajo y gasto alto.”
