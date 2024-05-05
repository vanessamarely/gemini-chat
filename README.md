# Guia

https://medium.com/@vanessamarely/gu%C3%ADa-para-construir-un-chat-con-la-api-gemini-ai-con-react-ac655702947b

## Guía paso a paso
Configuración del proyecto

Comienza creando un nuevo proyecto de React usando vite:

```npm create vite@latest gemini-chat```

Instala la dependencias del proyecto:

```
cd gemini-chat
npm install
```
Instala la librería de Google Generative AI:

```
npm install @google/generative-ai
```

- Estructura del componente React

Crea un componente(Chat.jsx). Puedes considerar componentes adicionales para modularidad y personalización (piensa en el área de entrada, la visualización de mensajes).

- Obtención de la clave de la API de Gemini AI

Ve a maker suite de google, en la siguiente url: [https://aistudio.google.com/](https://aistudio.google.com/). Crea una API key, de un proyecto que crees en google cloud. Almacena esta clave de forma segura; se recomienda una variable de entorno (archivo .env).

- Establecimiento de la conexión con Gemini AI

Dentro de tu componente Chat principal, importa los módulos necesarios e inicializa la conexión con Gemini:

```
import { GoogleGenerativeAI, HarmBlockThreshold, HarmCategory  } from "@google/generative-ai";

const apiKey = import.meta.env.VITE_API_KEY;  
const genAI = new GoogleGenerativeAI(apiKey);
```

- Configuración de la seguridad y la generación

Adapta la configuración de seguridad y generación para que coincida con los objetivos de tu chat:

```
const safetySettings = [ // Personaliza los umbrales de seguridad
{
    category: HarmCategory.HARM_CATEGORY_HARASSMENT,
    threshold: HarmBlockThreshold.BLOCK_ONLY_HIGH,
  },
]; 
const generationConfig = { // Ajusta el estilo de respuesta
  stopSequences: ["red"],
  maxOutputTokens: 100,
  temperature: 0.5,
  topP: 0.1,
  topK: 16,
}; 
const model = genAI.getGenerativeModel({    
  model: "gemini-pro", 
  generationConfig, 
  safetySettings,  
});
```

- Diseño de la interfaz de usuario (UI) 

Enfócate en crear una UI clara e intuitiva. Utiliza técnicas de estilo (CSS o una biblioteca de estilo) para mejorar la experiencia del usuario. Este es un formulario sencillo para el chat, que sirve de punto de partida.

```
<form className="chat-form" onSubmit={handleSubmit}>
  <textarea
    className="chat-form-text "
    value={message}
    onChange={handleSetMessage}
    placeholder="Enter your prompt here..."
  />
  <button className="chat-form-button" type="submit">
    Generate Text
  </button>
</form>
```

- Lógica del chat

Implementa la lógica del chat, manejando los mensajes del usuario y las respuestas de Gemini. El código proporcionado ofrece un excelente punto de partida.

```
// Inicializar el chat
const chat = model.startChat({
  history: [],
});

// Manejar el scroll 
const scrollToBottom = () => {
  bottomRef.current?.scrollIntoView({ behavior: "smooth" });
};

useEffect(() => {
  if(!loading){
    scrollToBottom();
  }
}, [loading]);

// Historial del chat
const addMessageToHistory = (role, message) => {
  setChatHistory((prevHistory) => [...prevHistory, { role, parts: message }]);
};
const fetchData = async () => { //LLamar a la api
  setLoading(true);
  addMessageToHistory("user", message);
  const result = await chat.sendMessage(message);
  const response = await result.response;

  const text = response.text();
  addMessageToHistory(
    "model",
    text
      .replace(/\n/g, "<br />")
      .replace(/"/g, "")
      .replace(/\*([^*]+)\*/g, "<h3>$1</h3>")
  );
  setMessage("");
  setLoading(false);
};

const handleSubmit = async (event) => { // Manejar el clic del boton
  event.preventDefault();
  fetchData();
};

const handleSetMessage = (event) => { // Manejar el campo de texto
  setMessage(event.target.value);
};
```

- Pruebas y refinamiento

Prueba exhaustivamente tu chat con diversas consultas y temas. Itera en la configuración de seguridad y generación para obtener resultados óptimos.
Consideraciones adicionales

Aprendizaje contextual: Explora formas de “entrenar” al chat en temas específicos, permitiéndole proporcionar respuestas más personalizadas y perspicaces.
Manejo de errores: Incorpora un manejo de errores robusto para gestionar de forma elegante las respuestas inesperadas de la API o los problemas de red.

    
# Stack

React + Vite + Gemini

This template provides a minimal setup to get React working in Vite with HMR and some ESLint rules.

Currently, two official plugins are available:

- [@vitejs/plugin-react](https://github.com/vitejs/vite-plugin-react/blob/main/packages/plugin-react/README.md) uses [Babel](https://babeljs.io/) for Fast Refresh
- [@vitejs/plugin-react-swc](https://github.com/vitejs/vite-plugin-react-swc) uses [SWC](https://swc.rs/) for Fast Refresh
