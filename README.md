# Retail-app
/* === File: package.json === */
{
  "name": "retail-tracker",
  "version": "1.0.0",
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "preview": "vite preview"
  },
  "dependencies": {
    "@supabase/supabase-js": "^2.0.0",
    "react": "^18.0.0",
    "react-dom": "^18.0.0",
    "tesseract.js": "^4.0.2"
  },
  "devDependencies": {
    "vite": "^4.0.0"
  }
}

/* === File: vite.config.js === */
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';

export default defineConfig({
  plugins: [react()],
});

/* === File: .env === */
VITE_SUPABASE_URL=https://your-project.supabase.co
VITE_SUPABASE_KEY=your-anon-key

/* === File: index.html === */
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Retail Tracker</title>
  </head>
  <body>
    <div id="root"></div>
    <script type="module" src="/src/main.jsx"></script>
  </body>
</html>

/* === File: src/main.jsx === */
import React from 'react';
import ReactDOM from 'react-dom/client';
import App from './App';
import './index.css';

ReactDOM.createRoot(document.getElementById('root')).render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
);

/* === File: src/App.jsx === */
import React, { useState } from 'react';
import { createClient } from '@supabase/supabase-js';
import Tesseract from 'tesseract.js';

const supabase = createClient(import.meta.env.VITE_SUPABASE_URL, import.meta.env.VITE_SUPABASE_KEY);

export default function App() {
  const [note, setNote] = useState('');
  const [file, setFile] = useState(null);
  const [status, setStatus] = useState('');

  const handleVoiceInput = () => {
    const recognition = new webkitSpeechRecognition();
    recognition.lang = 'en-US';
    recognition.onresult = (event) => {
      setNote(event.results[0][0].transcript);
    };
    recognition.start();
  };

  const handleImageUpload = async () => {
    if (!file) return;
    setStatus('Processing image...');
    const result = await Tesseract.recognize(file, 'eng');
    setNote(result.data.text);
    setStatus('Text extracted from image!');
  };

  const handleSave = async () => {
    const { error } = await supabase.from('transactions').insert([{ content: note }]);
    if (error) {
      alert('Error saving!');
    } else {
      setNote('');
      setStatus('Saved successfully!');
    }
  };

  return (
    <div className="min-h-screen bg-gray-100 p-4 text-center">
      <h1 className="text-3xl font-bold mb-4">Retail Voice/Photo Income Tracker</h1>

      <div className="flex flex-col items-center gap-4">
        <textarea
          value={note}
          onChange={(e) => setNote(e.target.value)}
          placeholder="Enter or dictate income/expense details..."
          className="w-full max-w-md p-2 border rounded"
          rows={4}
        />

        <div className="flex gap-2">
          <button onClick={handleVoiceInput} className="bg-blue-500 text-white px-4 py-2 rounded hover:bg-blue-600">
            Use Voice
          </button>

          <input type="file" accept="image/*" onChange={(e) => setFile(e.target.files[0])} />
          <button onClick={handleImageUpload} className="bg-green-500 text-white px-4 py-2 rounded hover:bg-green-600">
            Extract from Image
          </button>
        </div>

        <button onClick={handleSave} className="bg-purple-600 text-white px-6 py-2 rounded hover:bg-purple-700 mt-4">
          Save Entry
        </button>

        <p className="text-sm text-gray-500 mt-2">{status}</p>
      </div>
    </div>
  );
}

/* === File: src/index.css === */
@tailwind base;
@tailwind components;
@tailwind utilities;














/* === File: README.md === */
# Retail Tracker App

A lightweight AI-powered tool to help small business owners track income and expenses using voice or image input.

## Features
- Speech-to-text input (Web Speech API)
- Image OCR (Tesseract.js)
- Real-time storage (Supabase)
- Clean, responsive UI (TailwindCSS)

## Setup

1. Clone the repo:
```bash
git clone https://github.com/yourusername/retail-tracker.git
cd retail-tracker
```
2. Install dependencies:
```bash
npm install
```
3. Set up `.env` file with your Supabase credentials.
4. Run locally:
```bash
npm run dev
```

## Deployment
Use Vercel, Netlify, or Firebase Hosting for quick deployment.

