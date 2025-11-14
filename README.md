🚀 Convertidor de Divisas

Trabajo Práctico – Aplicaciones Web (Vue.js)

Aplicación web simple y rápida para convertir monedas en tiempo real, desarrollada con Vue.js 3 y consumiendo la API pública de ExchangeRate-API.

🌎 Demo Online

🔗 https://convertidor-divisas-vue.netlify.app/

🧩 Descripción

Este proyecto permite convertir entre distintas monedas de manera inmediata, mostrar la tasa utilizada y mantener la interfaz siempre actualizada.
Fue construido como ejercicio práctico para reforzar conceptos de Vue.js, consumo de APIs y despliegue web.

✨ Características Principales

Conversión entre USD, EUR, ARS, GBP, BRL, MXN, CLP, JPY

Consulta en tiempo real a una API REST de tasas de cambio

Botón para intercambiar monedas (swap)

Conversión automática al modificar datos

Diseño responsive y limpio

Animaciones suaves para resultados y mensajes

Muestra fecha y hora de la última actualización

Construido con Vite para un desarrollo rápido

🛠 Tecnologías Utilizadas
Tecnología	Uso
Vue.js 3	Framework principal
Vite	Entorno de desarrollo y build
ExchangeRate-API	Tasas de cambio
CSS3	Estilo y animaciones
Netlify	Hosting del proyecto
📦 Instalación y Uso
1️⃣ Clonar el repositorio
git clone https://github.com/TU-USUARIO/convertidor-divisas.git
cd convertidor-divisas

2️⃣ Instalar dependencias
npm install

3️⃣ Ejecutar en modo desarrollo
npm run dev


Abrir en el navegador:
👉 http://localhost:5173

4️⃣ Compilar para producción
npm run build


Los archivos finales se generan en la carpeta dist/.

📁 Estructura del Proyecto
convertidor-divisas/
├── src/
│   ├── assets/          # Imágenes y recursos
│   ├── App.vue          # Componente principal
│   └── main.js          # Archivo de entrada
├── public/              # Archivos estáticos
├── dist/                # Build final (generado)
├── package.json         # Dependencias y scripts
└── README.md            # Este archivo

🧠 Conceptos de Vue.js Aplicados

Data binding (v-model)

Eventos (click, change, keyup)

Condicionales y transiciones

Fetch API para consumir servicios externos

Componentes básicos

Ciclo de vida (mounted)

🌐 API Utilizada: ExchangeRate-API

Endpoint:

https://api.exchangerate-api.com/v4/latest/{monedaBase}


No requiere autenticación

Devuelve un objeto con tasas actualizadas

Ideal para proyectos educativos y demostraciones

👤 Autor

Manu Fina – Tecnicatura Superior en Desarrollo de Software

🧑‍💻 Repositorio: https://github.com/TU-USUARIO/convertidor-divisas

🌐 Demo: https://convertidor-divisas-vue.netlify.app/
