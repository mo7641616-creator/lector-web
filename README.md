<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>AI Suite - Manuel</title>

    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Phosphor Icons -->
    <script src="https://unpkg.com/@phosphor-icons/web"></script>
    <!-- Google Fonts -->
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&family=Poppins:wght@400;500;600&display=swap" rel="stylesheet">
    <!-- PDF.js -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/pdf.js/2.16.105/pdf.min.js"></script>

    <style>
        /* Estilos base para la interfaz de usuario */
        body { font-family: 'Inter', sans-serif; background-color: #0f172a; color: #e2e8f0; }
        
        /* Estilos para la transición de pestañas */
        .tab-content { display: none; animation: fadeIn 0.3s ease-in-out; }
        .tab-content.active { display: block; }
        
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }

        /* Estilo para el botón de navegación activo */
        .nav-btn.active { background-color: #3b82f6; color: white; box-shadow: 0 0 15px rgba(59, 130, 246, 0.5); }
        
        /* Estilo para el texto en proceso de lectura */
        .highlighted-chunk { background-color: #1d4ed8; color: white; box-shadow: 0 0 10px rgba(59, 130, 246, 0.5); border-radius: 4px; }

        /* Estilo para el modal */
        .modal {
            display: none;
            position: fixed;
            z-index: 1000;
            left: 0;
            top: 0;
            width: 100%;
            height: 100%;
            overflow: auto;
            background-color: rgba(0,0,0,0.4);
            backdrop-filter: blur(5px);
        }
        .modal-content {
            background-color: #1f2937;
            margin: 15% auto;
            padding: 20px;
            border-radius: 12px;
            border: 1px solid #374151;
            width: 80%;
            max-width: 500px;
            animation: slideIn 0.3s ease-out;
        }
        @keyframes slideIn { from { transform: translateY(-50px); opacity: 0; } to { transform: translateY(0); opacity: 1; } }
        
        /* Estilo específico para el texto de la historia */
        .story-output-box {
            white-space: pre-wrap; /* Mantiene saltos de línea y espacios de la IA */
            line-height: 1.75;
            max-height: 600px; /* Límite de altura para no ocupar toda la pantalla */
        }

        /* Estilo Slider tipo YouTube */
        input[type=range].youtube-slider {
            -webkit-appearance: none;
            width: 100%;
            background: transparent;
        }
        input[type=range].youtube-slider::-webkit-slider-thumb {
            -webkit-appearance: none;
            height: 16px;
            width: 16px;
            border-radius: 50%;
            background: #ef4444; /* Rojo tipo YouTube */
            cursor: pointer;
            margin-top: -6px;
            box-shadow: 0 0 5px rgba(0,0,0,0.5);
        }
        input[type=range].youtube-slider::-webkit-slider-runnable-track {
            width: 100%;
            height: 4px;
            cursor: pointer;
            background: #4b5563;
            border-radius: 2px;
        }
        input[type=range].youtube-slider:focus::-webkit-slider-runnable-track {
            background: #6b7280;
        }

        input[type="number"]::-webkit-inner-spin-button, 
        input[type="number"]::-webkit-outer-spin-button { 
            -webkit-appearance: none; 
            margin: 0; 
        }
    </style>
</head>
<body class="flex h-screen overflow-hidden">

    <!-- BARRA LATERAL (Menú de Opciones) -->
    <aside class="w-20 md:w-64 bg-gray-900 border-r border-gray-800 flex flex-col justify-between transition-all duration-300">
        <div>
            <div class="h-20 flex items-center justify-center border-b border-gray-800">
                <h1 class="hidden md:block text-xl font-bold bg-clip-text text-transparent bg-gradient-to-r from-blue-400 to-purple-500">AI Suite</h1>
                <i class="ph-fill ph-robot md:hidden text-3xl text-blue-500"></i>
            </div>
            <nav class="mt-6 flex flex-col gap-2 px-2">
                <button onclick="switchTab('pdf-tool', this)" class="nav-btn active flex items-center gap-3 p-3 rounded-lg text-gray-400 hover:bg-gray-800 hover:text-white transition-all">
                    <i class="ph-fill ph-file-pdf text-xl"></i>
                    <span class="hidden md:block font-medium">PDF a Texto/Resumen</span>
                </button>
                <button onclick="switchTab('translator-tool', this)" class="nav-btn flex items-center gap-3 p-3 rounded-lg text-gray-400 hover:bg-gray-800 hover:text-white transition-all">
                    <i class="ph-fill ph-translate text-xl"></i>
                    <span class="hidden md:block font-medium">Traductor Universal</span>
                </button>
                <button onclick="switchTab('book-recommender-tool', this)" class="nav-btn flex items-center gap-3 p-3 rounded-lg text-gray-400 hover:bg-gray-800 hover:text-white transition-all">
                    <i class="ph-fill ph-book-open text-xl"></i>
                    <span class="hidden md:block font-medium">Libros Inteligente</span>
                </button>
                <button onclick="switchTab('book-extender-tool', this)" class="nav-btn flex items-center gap-3 p-3 rounded-lg text-gray-400 hover:bg-gray-800 hover:text-white transition-all">
                    <i class="ph-fill ph-pencil-line text-xl"></i>
                    <span class="hidden md:block font-medium">Extensión de Libros IA</span>
                </button>
                <button onclick="switchTab('reader-tool', this)" class="nav-btn flex items-center gap-3 p-3 rounded-lg text-gray-400 hover:bg-gray-800 hover:text-white transition-all">
                    <i class="ph-fill ph-book-open-text text-xl"></i>
                    <span class="hidden md:block font-medium">Lector Inteligente (TTS)</span>
                </button>
                <!-- CineFinder Eliminado -->
            </nav>
        </div>
        <div class="p-4 border-t border-gray-800 text-center">
            <p class="text-xs text-gray-500 hidden md:block">Hola, Manuel</p>
        </div>
    </aside>

    <!-- ÁREA PRINCIPAL -->
    <main class="flex-1 bg-gray-900 overflow-y-auto relative">
        <div class="p-6 md:p-10 max-w-6xl mx-auto relative z-10">

            <!-- 1. PDF A TEXTO / RESUMEN -->
            <div id="pdf-tool" class="tab-content active">
                <h2 class="text-3xl font-bold mb-6 text-white">Convertidor PDF a Texto y Resumidor IA</h2>
                <div class="bg-gray-800 rounded-2xl p-8 border border-gray-700 shadow-xl">
                    <div id="drop-zone-pdf" class="border-2 border-dashed border-gray-600 rounded-xl p-8 text-center cursor-pointer hover:border-blue-500 hover:bg-gray-700/50 transition-all">
                        <i class="ph ph-file-pdf text-4xl text-gray-400 mb-2"></i>
                        <p class="text-gray-300">Arrastra tu PDF o haz clic aquí</p>
                        <input type="file" id="pdf-input" class="hidden" accept=".pdf">
                    </div>
                    <textarea id="pdf-output" readonly class="w-full mt-6 h-64 bg-gray-900 border border-gray-700 rounded-lg p-4 text-gray-300 focus:ring-2 focus:ring-blue-500" placeholder="El texto extraído aparecerá aquí..."></textarea>
                    <div class="flex gap-4 mt-4">
                        <button onclick="downloadPdfText()" class="bg-green-600 hover:bg-green-700 text-white px-6 py-2 rounded-lg font-medium transition-colors">Descargar .txt</button>
                        <button onclick="summarizePdfText()" class="bg-blue-600 hover:bg-blue-700 text-white px-6 py-2 rounded-lg font-medium transition-colors">Resumir con IA</button>
                    </div>
                    <div id="pdf-summary-area" class="mt-6 p-4 bg-gray-900/50 rounded-lg text-gray-300 hidden"></div>
                </div>
            </div>

            <!-- 2. TRADUCTOR UNIVERSAL -->
            <div id="translator-tool" class="tab-content">
                <h2 class="text-3xl font-bold mb-6 text-white">Traductor Universal de Archivos (Para Libros Largos)</h2>
                <div class="bg-gray-800 rounded-2xl p-8 border border-gray-700 shadow-xl">
                    <p class="text-gray-400 mb-4">Carga un archivo (.txt o .pdf). La IA lo traducirá por fragmentos, sin límite de tamaño (¡ideal para libros!).</p>
                    
                    <!-- Controles de Carga -->
                    <div class="flex flex-wrap gap-4 items-center mb-6 bg-gray-900/50 p-4 rounded-xl">
                        <label for="trans-file" class="cursor-pointer bg-purple-600 hover:bg-purple-700 text-white px-4 py-2 rounded-lg flex items-center gap-2 font-medium transition-colors">
                            <i class="ph ph-file-arrow-up"></i> Cargar Archivo (.txt o .pdf)
                        </label>
                        <input type="file" id="trans-file" class="hidden" accept=".txt,.pdf">
                        <span id="trans-status" class="text-sm text-gray-400">Ningún archivo cargado</span>
                        <button id="btn-run-translation" onclick="runFileTranslation()" class="ml-auto bg-green-600 hover:bg-green-700 text-white px-6 py-2 rounded-lg font-medium transition-colors disabled:opacity-50" disabled>
                            Traducir y Descargar
                        </button>
                    </div>

                    <!-- Visualizadores -->
                    <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
                        <textarea id="trans-input" class="w-full h-80 bg-gray-900 border border-gray-700 rounded-xl p-5 text-white placeholder-gray-500 focus:ring-2 focus:ring-purple-500 resize-none" placeholder="Texto Original (Muestra de inicio)..." readonly></textarea>
                        <textarea id="trans-output" class="w-full h-80 bg-gray-900 border border-gray-700 rounded-xl p-5 text-white resize-none" placeholder="Traducción al Español Latino..." readonly></textarea>
                    </div>
                </div>
            </div>

            <!-- 3. RECOMENDADOR Y BUSCADOR DE LIBROS (COMBINADO) -->
            <div id="book-recommender-tool" class="tab-content">
                <h2 class="text-3xl font-bold mb-6 text-white flex items-center gap-2">
                    <i class="ph-fill ph-book-open text-blue-400"></i>
                    Libros: Recomendador y Buscador Inteligente
                </h2>
                <div class="bg-gray-800 rounded-2xl p-8 border border-gray-700 shadow-xl space-y-10">

                    <!-- SECCIÓN 1: RECOMENDADOR POR GUSTOS -->
                    <div class="border-b border-gray-700 pb-8">
                        <h3 class="text-xl font-semibold text-white mb-4">1. Recomendador Personalizado (Libros Reales)</h3>
                        <p class="text-gray-400 mb-4">Dime qué tipo de libros te gustan, qué temas te interesan o qué has leído últimamente para obtener recomendaciones.</p>
                        <div class="flex gap-2">
                            <input type="text" id="recom-query" placeholder="Ej: 'Fantasía épica con dragones y un poco de romance.'" class="w-full px-5 py-3 bg-gray-900 border border-gray-700 rounded-lg text-white focus:border-blue-500 outline-none">
                            <button onclick="getRecommendations()" class="bg-blue-600 text-white px-6 py-3 rounded-lg hover:bg-blue-700 font-bold flex items-center gap-2 whitespace-nowrap">
                                <i class="ph ph-wand"></i> Recomendar
                            </button>
                        </div>
                        <div id="recom-result" class="mt-8 text-gray-300">
                            <!-- Los resultados de la recomendación aparecerán aquí -->
                        </div>
                    </div>
                    
                    <!-- SECCIÓN 2: BUSCADOR INTELIGENTE POR TÍTULO -->
                    <div class="border-b border-gray-700 pb-8">
                         <h3 class="text-xl font-semibold text-white mb-4">2. Corrector de Título y Búsqueda PDF</h3>
                         <p class="text-gray-400 mb-4">Escribe un título (aunque esté mal escrito) y la IA lo corrige, luego busca el PDF directamente.</p>
                         <div class="flex gap-2">
                             <input type="text" id="book-search-query" placeholder="Ej: 'Cen años de soleda'" class="w-full px-5 py-3 bg-gray-900 border border-gray-700 rounded-lg text-white focus:border-purple-500 outline-none">
                             <button onclick="searchBookByTitle()" class="bg-purple-600 text-white px-6 py-3 rounded-lg hover:bg-purple-700 font-bold flex items-center gap-2 whitespace-nowrap">
                                 <i class="ph ph-magnifying-glass"></i> Buscar PDF
                             </button>
                         </div>
                         <div id="book-search-result" class="mt-8 hidden">
                            <!-- Los resultados de la búsqueda de título aparecerán aquí -->
                         </div>
                    </div>

                    <!-- SECCIÓN 3: GENERADOR DE HISTORIAS IA -->
                    <div>
                        <h3 class="text-xl font-semibold text-white mb-4">3. Generador de Historias con IA</h3>
                        <p class="text-gray-400 mb-4">Especifica los géneros, personajes y el tono que deseas.</p>
                        <div class="space-y-4">
                            <textarea id="story-styles" placeholder="Ej: 'Cyberpunk, detective cínico en Neo-Tokyo, busca un implante de memoria perdido. Tono oscuro y filosófico. Final inesperado.'" class="w-full h-24 px-5 py-3 bg-gray-900 border border-gray-700 rounded-lg text-white focus:border-green-500 outline-none resize-none"></textarea>
                            
                            <div class="flex items-center gap-4">
                                <label class="text-gray-400">Capítulos a Generar:</label>
                                <input type="number" id="num-chapters" value="10" min="1" class="w-20 px-3 py-2 bg-gray-900 border border-gray-700 rounded-lg text-white text-center">
                                <button onclick="generateStory()" id="btn-generate-story" class="ml-auto bg-green-600 text-white px-6 py-3 rounded-lg hover:bg-green-700 font-bold flex items-center gap-2">
                                    <i class="ph ph-sparkle"></i> Generar Historia
                                </button>
                            </div>
                            
                            <div class="flex items-center gap-4 pt-4 border-t border-gray-700" id="story-controls-extension" style="display: none;">
                                <label class="text-gray-400">Capítulos a Añadir:</label>
                                <input type="number" id="num-chapters-extension" value="5" min="1" class="w-20 px-3 py-2 bg-gray-900 border border-gray-700 rounded-lg text-white text-center">
                                <button onclick="makeStoryLonger()" id="btn-make-longer" class="bg-red-600 text-white px-4 py-2 rounded-lg hover:bg-red-700 font-medium transition-colors flex items-center gap-2">
                                    <i class="ph ph-plus-circle"></i> Hazla más larga
                                </button>
                                <button onclick="regenerateStory()" class="bg-yellow-600 text-white px-4 py-2 rounded-lg hover:bg-yellow-700 font-medium transition-colors flex items-center gap-2">
                                    <i class="ph ph-arrows-clockwise"></i> Generar otra con mismos estilos
                                </button>
                            </div>
                        </div>
                        
                        <div id="story-result-container" class="mt-8 hidden">
                            <div class="flex flex-wrap gap-3 mb-4">
                                <button onclick="downloadStory()" class="story-ctrl-btn bg-gray-700 text-white px-4 py-2 rounded-lg hover:bg-gray-600 font-medium text-sm transition-colors flex items-center gap-2">
                                    <i class="ph ph-download"></i> Descargar Historia (.txt)
                                </button>
                            </div>
                            <div id="story-output" class="story-output-box p-4 bg-gray-900 border border-gray-700 rounded-lg text-gray-300 min-h-[200px] overflow-auto">
                                <!-- Historia generada -->
                            </div>
                            <p id="story-status" class="text-sm text-gray-500 mt-2"></p>
                        </div>
                    </div>

                </div>
            </div>

            <!-- 4. EXTENSIÓN DE LIBROS IA (NUEVA PESTAÑA) -->
            <div id="book-extender-tool" class="tab-content">
                <h2 class="text-3xl font-bold mb-6 text-white flex items-center gap-2">
                    <i class="ph-fill ph-pencil-line text-yellow-400"></i>
                    Extensión de Libros IA
                </h2>
                <div class="bg-gray-800 rounded-2xl p-8 border border-gray-700 shadow-xl">
                    <p class="text-gray-400 mb-4">Carga un archivo (.txt o .pdf) y la IA continuará la trama, agregando nuevos capítulos.</p>
                    
                    <!-- Controles de Carga -->
                    <div class="flex flex-wrap gap-4 items-center mb-6 bg-gray-900/50 p-4 rounded-xl">
                        <label for="extender-file" class="cursor-pointer bg-yellow-600 hover:bg-yellow-700 text-white px-4 py-2 rounded-lg flex items-center gap-2 font-medium transition-colors">
                            <i class="ph ph-file-arrow-up"></i> Cargar Archivo (.txt o .pdf)
                        </label>
                        <input type="file" id="extender-file" class="hidden" accept=".txt,.pdf">
                        <span id="extender-status" class="text-sm text-gray-400">Ningún archivo cargado</span>
                    </div>

                    <div class="flex items-center gap-4 pt-4 border-t border-gray-700">
                        <label class="text-gray-400">Capítulos a Añadir:</label>
                        <input type="number" id="extender-num-chapters" value="10" min="1" class="w-20 px-3 py-2 bg-gray-900 border border-gray-700 rounded-lg text-white text-center">
                        <button id="btn-run-extension" onclick="extendBook()" class="ml-auto bg-pink-600 hover:bg-pink-700 text-white px-6 py-3 rounded-lg font-bold transition-colors disabled:opacity-50" disabled>
                            <i class="ph ph-plus-square"></i> Extender Libro
                        </button>
                    </div>

                    <textarea id="extender-input" class="w-full mt-6 h-40 bg-gray-900 border border-gray-700 rounded-xl p-5 text-white placeholder-gray-500 focus:ring-2 focus:ring-yellow-500 resize-none" placeholder="Texto Original Cargado (Muestra)..." readonly></textarea>
                    
                    <div id="extender-result-container" class="mt-8 hidden">
                        <h4 class="text-lg font-semibold text-white mb-3">Extensión Generada:</h4>
                        <div class="flex flex-wrap gap-3 mb-4">
                            <button onclick="downloadExtension()" id="btn-download-extension" class="bg-green-600 text-white px-4 py-2 rounded-lg hover:bg-green-700 font-medium text-sm transition-colors flex items-center gap-2">
                                <i class="ph ph-download"></i> Descargar extensión de historia (.txt)
                            </button>
                            <button onclick="regenerateExtension()" id="btn-regenerate-extension" class="bg-yellow-600 text-white px-4 py-2 rounded-lg hover:bg-yellow-700 font-medium text-sm transition-colors flex items-center gap-2">
                                <i class="ph ph-arrows-clockwise"></i> No me ha gustado, genera una extensión diferente
                            </button>
                        </div>
                        <div id="extender-output" class="story-output-box p-4 bg-gray-900 border border-gray-700 rounded-lg text-gray-300 min-h-[150px] overflow-auto">
                            <!-- Extensión generada -->
                        </div>
                    </div>
                </div>
            </div>


            <!-- 5. LECTOR INTELIGENTE (TTS) -->
            <div id="reader-tool" class="tab-content">
                <h2 class="text-3xl font-bold mb-6 text-white">Lector Inteligente (Análisis y Limpieza)</h2>
                <div class="bg-gray-800/80 backdrop-blur rounded-2xl p-6 border border-gray-700">
                    <p class="text-gray-400 mb-4 text-sm"><i class="ph-fill ph-sparkle text-yellow-400"></i> Este lector analiza el contenido y elimina texto irrelevante.</p>
                    
                    <!-- Controles Superiores -->
                    <div class="flex flex-wrap gap-4 items-center mb-6 bg-gray-900/50 p-4 rounded-xl">
                        <label for="reader-file" class="cursor-pointer bg-blue-600 hover:bg-blue-700 text-white px-4 py-2 rounded-lg flex items-center gap-2">
                            <i class="ph ph-upload"></i> Cargar Archivo (.txt o .pdf)
                        </label>
                        <input type="file" id="reader-file" class="hidden" accept=".txt,.pdf">
                        <span id="reader-status" class="text-sm text-gray-400">Ningún archivo cargado</span>
                        
                        <div class="ml-auto flex items-center gap-3">
                             <select id="reader-voice" class="bg-gray-800 border border-gray-600 text-sm rounded p-2 text-white max-w-[150px]"></select>
                             <label class="text-sm text-gray-400 whitespace-nowrap">Velocidad:</label>
                             <input type="range" id="reader-speed" min="0.5" max="2" step="0.1" value="1" class="w-24 accent-blue-500" oninput="updateReaderDuration()">
                        </div>
                    </div>
                    
                    <!-- Barra de Progreso y Tiempo (Estilo YouTube) -->
                    <div class="mb-6 px-2">
                        <div class="flex items-center justify-between text-xs text-gray-400 mb-1">
                            <span id="time-current">00:00</span>
                            <span id="time-total">00:00</span>
                        </div>
                        <div class="relative w-full h-4 flex items-center">
                            <input type="range" id="reader-timeline" min="0" max="100" value="0" class="youtube-slider" oninput="scrubTimeline()" onchange="scrubTimeline()">
                        </div>
                        <div class="flex justify-end mt-2 items-center gap-2">
                            <span class="text-xs text-gray-500">Ir a tiempo (mm:ss):</span>
                            <input type="text" id="jump-time-input" placeholder="09:00" class="bg-gray-800 border border-gray-700 rounded text-xs p-1 w-16 text-white text-center" onkeydown="handleTimeJump(event)">
                        </div>
                    </div>

                    <!-- Controles de Reproducción -->
                    <div class="flex justify-center gap-4 mb-6">
                        <button onclick="readerRewind('reader')" class="p-3 bg-gray-700 rounded-full hover:bg-gray-600"><i class="ph-fill ph-rewind text-xl"></i></button>
                        <button onclick="toggleReader('reader')" id="btn-play-pause-reader" class="p-4 bg-blue-500 text-white rounded-full hover:bg-blue-600 shadow-lg hover:scale-105 transition-transform"><i class="ph-fill ph-play text-2xl"></i></button>
                        <button onclick="readerStop('reader')" class="p-3 bg-gray-700 rounded-full hover:bg-gray-600"><i class="ph-fill ph-stop text-xl"></i></button>
                        <button onclick="readerForward('reader')" class="p-3 bg-gray-700 rounded-full hover:bg-gray-600"><i class="ph-fill ph-fast-forward text-xl"></i></button>
                    </div>

                    <!-- Visualizador de Texto -->
                    <div id="reader-text-box" class="h-96 overflow-y-auto bg-gray-900 border border-gray-700 rounded-xl p-6 text-lg leading-relaxed text-gray-300 font-serif select-text" onmouseup="checkSelectionForReading()">
                        <p class="text-center text-gray-600 italic mt-10">El texto limpio y analizado aparecerá aquí... (Selecciona cualquier parte para leer desde ahí)</p>
                    </div>
                </div>
            </div>

            <!-- CineFinder eliminado -->
            
            <!-- MODAL de Carga para Traducción -->
            <div id="loading-modal" class="modal">
                <div class="modal-content text-center">
                    <i class="ph ph-translate text-4xl text-purple-500 mb-4"></i>
                    <h3 class="text-xl font-semibold text-white mb-2">Procesando Archivo Largo</h3>
                    <p id="modal-status-text" class="text-gray-400 mb-4">Fragmento 0/0 en proceso...</p>
                    <div class="w-full bg-gray-700 rounded-full h-4 overflow-hidden">
                        <div id="modal-progress-bar" class="h-4 bg-purple-500 transition-all duration-300" style="width: 0%;"></div>
                    </div>
                </div>
            </div>


        </div>
    </main>

    <script>
        // --- API KEY UNIFICADA ---
        // INSERTA TU API KEY AQUÍ ABAJO
        const GENERAL_API_KEY = 'AIzaSyAiAOMHI6SaLtJdeaKBx83vTeDw2xi21Dw'; 
        const MAX_CHARS_PER_CHUNK = 8000; // Límite por llamada a la API
        
        // --- ESTADO GLOBAL DEL GENERADOR DE HISTORIAS ---
        let currentStoryPrompt = '';
        let currentStory = '';
        let currentExtensionText = '';
        let currentChapterCount = 10;
        let lastFullStory = ''; 

        // --- ESTADO GLOBAL DEL TTS ---
        const ttsState = {
            'reader': { 
                chunks: [], 
                chunkTimes: [], // Tiempos acumulados de inicio de cada chunk en segundos
                totalEstimatedSeconds: 0,
                currentChunk: 0, 
                isPlaying: false, 
                utter: null, 
                statusId: 'reader-status', 
                textBoxId: 'reader-text-box', 
                voiceId: 'reader-voice', 
                speedId: 'reader-speed', 
                buttonId: 'btn-play-pause-reader' 
            }
        };

        // ==========================================
        // LÓGICA DE INTERFAZ Y UTILERÍAS
        // ==========================================
        function switchTab(tabId, button) {
            // AL CAMBIAR DE PESTAÑA: Pausar pero NO reiniciar a cero.
            Object.keys(ttsState).forEach(id => readerPause(id));

            document.querySelectorAll('.tab-content').forEach(el => el.classList.remove('active'));
            document.querySelectorAll('.nav-btn').forEach(el => el.classList.remove('active'));
            document.getElementById(tabId).classList.add('active');
            button.classList.add('active');
        }

        // Función centralizada para llamar a la API
        async function callGemini(prompt, systemPrompt = null, isJson = false, temp = 0.8) {
            if(!GENERAL_API_KEY) {
                alert("Falta la API Key. Por favor edita el archivo HTML e inserta tu clave.");
                return null;
            }
            const payload = { 
                contents: [{ parts: [{ text: prompt }] }],
                generationConfig: {
                    temperature: temp 
                }
            };
            if (systemPrompt) {
                payload.systemInstruction = { parts: [{ text: systemPrompt }] };
            }

            let apiUrl = `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-preview-09-2025:generateContent?key=${GENERAL_API_KEY}`;
            const headers = {'Content-Type': 'application/json'};

            if (isJson) {
                payload.generationConfig.responseMimeType = "application/json";
                payload.generationConfig.responseSchema = {
                    type: "ARRAY",
                    items: {
                        type: "OBJECT",
                        properties: {
                            "title": { "type": "STRING" },
                            "synopsis": { "type": "STRING" },
                            "author": { "type": "STRING" } // AÑADIDO: Campo autor
                        },
                        required: ["title", "synopsis", "author"]
                    }
                };
            }

            try {
                for (let attempt = 0; attempt < 3; attempt++) {
                    const response = await fetch(apiUrl, {
                        method: 'POST',
                        headers: headers,
                        body: JSON.stringify(payload)
                    });
                    
                    if (response.ok) {
                        const data = await response.json();
                        const textResult = data.candidates?.[0]?.content?.parts?.[0]?.text;
                        return textResult; 
                    }

                    if (response.status === 429 && attempt < 2) {
                        const delay = Math.pow(2, attempt) * 1000 + (Math.random() * 1000); 
                        await new Promise(resolve => setTimeout(resolve, delay));
                        continue;
                    }
                    
                    throw new Error(`Error HTTP: ${response.status}`);
                }

            } catch(e) { 
                console.error("Error al llamar a Gemini:", e);
                return null;
            }
            return null;
        }

        // Función para extraer texto de PDF
        async function extractTextFromPDF(file) {
             const { pdfjsLib } = globalThis;
             pdfjsLib.GlobalWorkerOptions.workerSrc = `https://cdnjs.cloudflare.com/ajax/libs/pdf.js/2.16.105/pdf.worker.min.js`;

             const arrayBuffer = await file.arrayBuffer();
             const pdf = await pdfjsLib.getDocument(arrayBuffer).promise;
             let text = '';
             for(let i=1; i<=pdf.numPages; i++){
                 const page = await pdf.getPage(i);
                 const content = await page.getTextContent();
                 text += content.items.map(s => s.str).join(' ') + '\n';
             }
             return text;
        }

        // Función de descarga
        function downloadFile(text, originalFileName) {
            const blob = new Blob([text], { type: 'text/plain;charset=utf-8' });
            const url = URL.createObjectURL(blob);
            const a = document.createElement('a');
            
            a.download = originalFileName;
            a.href = url;
            
            document.body.appendChild(a);
            a.click();
            document.body.removeChild(a);
            URL.revokeObjectURL(url);
        }
        
        function downloadTranslation(text, originalFileName, suffix = '_extraido') {
            const baseName = originalFileName ? originalFileName.replace(/\.(txt|pdf)$/i, '') : 'archivo';
            downloadFile(text, `${baseName}${suffix}.txt`);
        }
        
        function splitTextIntoChunks(text, maxChars) {
            const chunks = [];
            let i = 0;
            while (i < text.length) {
                let end = Math.min(i + maxChars, text.length);
                
                if (end < text.length) {
                    let lastSpace = text.lastIndexOf(' ', end);
                    if (lastSpace > i) {
                        end = lastSpace;
                    }
                }
                
                chunks.push(text.substring(i, end));
                i = end + 1; 
            }
            return chunks;
        }


        // ==========================================
        // 1. LÓGICA PDF A TEXTO / RESUMEN
        // ==========================================
        let currentPdfName = '';

        document.getElementById('drop-zone-pdf').addEventListener('click', () => document.getElementById('pdf-input').click());
        document.getElementById('pdf-input').addEventListener('change', async (e) => {
            const file = e.target.files[0];
            if(!file) return;
            currentPdfName = file.name;
            const outputArea = document.getElementById('pdf-output');
            outputArea.value = "Procesando...";
            document.getElementById('pdf-summary-area').classList.add('hidden');
            
            try {
                const text = await extractTextFromPDF(file);
                outputArea.value = text;
            } catch (e) {
                outputArea.value = "Error al leer el PDF. Asegúrate de que no esté cifrado.";
                console.error("PDF Read Error:", e);
            }
        });

        function downloadPdfText() {
            const text = document.getElementById('pdf-output').value;
            if(!text || text.includes("Procesando")) return;
            downloadTranslation(text, currentPdfName || 'texto_extraido', '_extraido');
        }

        async function summarizePdfText() {
            const text = document.getElementById('pdf-output').value;
            if(!text || text.includes("Procesando")) return;
            
            const div = document.getElementById('pdf-summary-area');
            div.classList.remove('hidden');
            div.innerHTML = "<i class='ph ph-spinner animate-spin'></i> Generando resumen...";
            
            const textToProcess = text.substring(0, 8000);
            const prompt = "Genera un resumen completo y bien estructurado del siguiente texto, en español latinoamericano. Hazlo en un tono formal y profesional. El texto es: " + textToProcess;
            const summary = await callGemini(prompt, null, false, 0.2); 
            
            if (summary) {
                div.innerHTML = `<strong>Resumen de IA:</strong><br>${summary.replace(/\n/g, '<br>')}`;
            } else {
                div.innerHTML = "Error: No se pudo generar el resumen. Verifica la API Key.";
            }
        }


        // ==========================================
        // 2. LÓGICA TRADUCTOR UNIVERSAL (ILIMITADO)
        // ==========================================
        let currentTransFile = null;
        let fullTextToTranslate = '';

        const transFileEl = document.getElementById('trans-file');
        const transStatusEl = document.getElementById('trans-status');
        const transInputEl = document.getElementById('trans-input');
        const transOutputEl = document.getElementById('trans-output');
        const btnRunTransEl = document.getElementById('btn-run-translation');
        const loadingModalEl = document.getElementById('loading-modal');
        const modalProgressBarEl = document.getElementById('modal-progress-bar');
        const modalStatusTextEl = document.getElementById('modal-status-text');

        transFileEl.addEventListener('change', async (e) => {
            currentTransFile = e.target.files[0];
            if (!currentTransFile) {
                transStatusEl.textContent = 'Ningún archivo cargado';
                transInputEl.value = '';
                btnRunTransEl.disabled = true;
                return;
            }

            transStatusEl.textContent = `${currentTransFile.name} (Procesando...)`;
            transInputEl.value = 'Extrayendo texto del archivo...';
            transOutputEl.value = '';
            btnRunTransEl.disabled = true;

            try {
                if (currentTransFile.type === "application/pdf") {
                    fullTextToTranslate = await extractTextFromPDF(currentTransFile);
                } else { 
                    fullTextToTranslate = await currentTransFile.text();
                }

                const displayMaxText = fullTextToTranslate.substring(0, 1000) + (fullTextToTranslate.length > 1000 ? '... [Texto completo cargado: ' + fullTextToTranslate.length + ' caracteres]' : ''); 
                transInputEl.value = displayMaxText;
                transStatusEl.textContent = `${currentTransFile.name} (${fullTextToTranslate.length} caracteres). Listo para traducir.`;
                btnRunTransEl.disabled = false;

            } catch (error) {
                console.error("Error al procesar archivo para traducción:", error);
                transStatusEl.textContent = 'Error al leer el archivo.';
                transInputEl.value = 'Error al leer el contenido del archivo.';
                btnRunTransEl.disabled = true;
            }
        });

        async function runFileTranslation() {
            if (!currentTransFile || btnRunTransEl.disabled || !fullTextToTranslate) return;

            const textChunks = splitTextIntoChunks(fullTextToTranslate, MAX_CHARS_PER_CHUNK);
            const totalChunks = textChunks.length;
            let translatedText = [];

            transOutputEl.value = "Iniciando traducción de libro largo...";
            btnRunTransEl.disabled = true;
            
            loadingModalEl.style.display = 'block';
            document.getElementById('modal-status-text').textContent = "Iniciando traducción...";

            const systemPrompt = `Actúa como un traductor profesional. Tu tarea es traducir el texto proporcionado al **Español Latinoamericano** de manera natural y fluida. Mantén la estructura original (párrafos, saltos de línea). Dado que esto es parte de un documento más grande, **no añadas introducciones, explicaciones ni resúmenes**.`;


            for (let i = 0; i < totalChunks; i++) {
                const chunk = textChunks[i];
                const progress = Math.round(((i + 1) / totalChunks) * 100);

                modalProgressBarEl.style.width = `${progress}%`;
                modalStatusTextEl.textContent = `Fragmento ${i + 1}/${totalChunks} en proceso... (Traduciendo...)`;

                const prompt = `Traduce el siguiente fragmento de texto al Español Latino: "${chunk}"`;
                
                const translation = await callGemini(prompt, systemPrompt, false, 0.2); 

                if (translation) {
                    translatedText.push(translation);
                    if (i === 0) transOutputEl.value = translation + '\n\n... (Traduciendo el resto del libro)';
                } else {
                    translatedText.push(`\n\n[ERROR DE TRADUCCIÓN EN FRAGMENTO ${i + 1}]`);
                    console.error(`Error en la traducción del fragmento ${i + 1}`);
                }
            }

            loadingModalEl.style.display = 'none';
            btnRunTransEl.disabled = false;
            
            const finalTranslation = translatedText.join('\n\n');
            transOutputEl.value = finalTranslation.substring(0, 1000) + (finalTranslation.length > 1000 ? '... [Traducción completa ensamblada: ' + finalTranslation.length + ' caracteres]' : '');
            transStatusEl.textContent = '¡Traducción completa! Descargando archivo...';
            
            downloadTranslation(finalTranslation, currentTransFile.name, '_traducido');

            transStatusEl.textContent = 'Traducción descargada.';
        }

        // ==========================================
        // 3. LÓGICA RECOMENDADOR Y GENERADOR DE HISTORIAS
        // ==========================================

        // --- SECCIÓN 1: RECOMENDADOR POR GUSTOS ---
        async function getRecommendations() {
            const query = document.getElementById('recom-query').value;
            const resDiv = document.getElementById('recom-result');
            if(!query) return;

            resDiv.innerHTML = `<p class="text-center text-blue-400"><i class='ph ph-spinner animate-spin mr-2'></i> Buscando 5 joyas literarias que coincidan perfectamente con tu estilo...</p>`;

            // MODIFICADO: Solicitud de campo Autor
            const systemPrompt = `Eres un experto curador de libros. Basado en la descripción de estilo, género o gusto del usuario, busca y genera de 5 recomendaciones de libros **reales** y populares que coincidan perfectamente con ese estilo. La respuesta DEBE ser un arreglo JSON que contenga solo objetos con las propiedades "title", "synopsis", "author".`;
            
            const prompt = `Basado en mis gustos: "${query}", recomiéndame 5 libros.`;
            const jsonResult = await callGemini(prompt, systemPrompt, true, 0.2); 

            if (!jsonResult) {
                resDiv.innerHTML = `<p class="text-center text-red-400">Error: No se pudo generar la recomendación. Revisa la API Key en la consola.</p>`;
                return;
            }

            let recommendations;
            try {
                recommendations = JSON.parse(jsonResult);
            } catch (e) {
                console.error("Error al parsear JSON de la IA:", e);
                resDiv.innerHTML = `<p class="text-center text-red-400">Error: La IA no devolvió el formato JSON esperado.</p>`;
                return;
            }

            let html = '<div class="space-y-6">';
            recommendations.forEach((book, index) => {
                // MODIFICADO: Búsqueda con Título + Autor + filetype:pdf
                const searchLink = `https://www.google.com/search?q=${encodeURIComponent(book.title)}+${encodeURIComponent(book.author)}+filetype:pdf`;
                html += `
                    <div class="p-4 border border-gray-700 rounded-lg bg-gray-900/50">
                        <h4 class="text-xl font-semibold text-blue-400 mb-1">${index + 1}. ${book.title}</h4>
                        <p class="text-sm text-gray-200 font-medium mb-1">Por: ${book.author}</p>
                        <p class="text-sm text-gray-400 mb-3">${book.synopsis}</p>
                        <a href="${searchLink}" target="_blank" class="text-sm text-red-400 hover:text-red-300 font-medium flex items-center gap-1">
                            <i class="ph ph-file-pdf"></i> Buscar PDF (con Autor)
                        </a>
                    </div>
                `;
            });
            html += '</div>';
            resDiv.innerHTML = html;
        }

        // --- SECCIÓN 2: BUSCADOR INTELIGENTE POR TÍTULO ---
        async function searchBookByTitle() {
            const query = document.getElementById('book-search-query').value;
            const resDiv = document.getElementById('book-search-result');
            if(!query) return;

            resDiv.innerHTML = "<i class='ph ph-spinner animate-spin'></i> Corrigiendo título y buscando enlaces PDF...";
            resDiv.classList.remove('hidden');

            const correctionPrompt = `Corrige el título del libro "${query}". Solo devuelve el título corregido (máximo 10 palabras, sin explicación).`;
            const correctTitle = await callGemini(correctionPrompt, null, false, 0.2); 
            
            if (!correctTitle) {
                resDiv.innerHTML = `<p class="text-red-400">Error al corregir el título. Por favor, verifica la API Key.</p>`;
                return;
            }
            
            const title = correctTitle.trim();
            // MODIFICADO: Simplificado para mostrar búsqueda PDF directa
            const searchPDF = `https://www.google.com/search?q=${encodeURIComponent(title)}+filetype:pdf`;

            resDiv.innerHTML = `
                <p class="text-gray-400 text-sm mb-4">Título Corregido por IA: <strong class="text-white">${title}</strong></p>
                <div class="flex flex-col gap-3">
                    <a href="${searchPDF}" target="_blank" class="flex items-center justify-center p-4 bg-red-600 text-white rounded-lg hover:bg-red-700 font-bold transition-colors text-lg shadow-lg">
                        <i class="ph ph-file-pdf text-2xl mr-3"></i> Buscar PDF en Google
                    </a>
                    <p class="text-center text-gray-500 text-xs mt-2">Búsqueda directa de archivos PDF</p>
                </div>
            `;
        }
        
        // --- SECCIÓN 3: GENERADOR DE HISTORIAS IA ---

        function updateChapterCount() {
            const input = document.getElementById('num-chapters');
            currentChapterCount = parseInt(input.value) || 10;
            if (currentChapterCount < 1) {
                currentChapterCount = 1;
                input.value = 1;
            }
        }

        document.getElementById('num-chapters').addEventListener('change', updateChapterCount);

        async function generateStory(isRegenerate = false, isExtension = false) {
            const stylesInput = document.getElementById('story-styles');
            const resultContainer = document.getElementById('story-result-container');
            const outputDiv = document.getElementById('story-output');
            const statusEl = document.getElementById('story-status');
            const btnGenerateStory = document.getElementById('btn-generate-story');
            const extensionControls = document.getElementById('story-controls-extension');
            const chaptersToAdd = parseInt(document.getElementById('num-chapters-extension').value) || 5;

            if (!isRegenerate && !isExtension) {
                if (!stylesInput.value) {
                    outputDiv.innerHTML = `<p class="text-red-400">Por favor, especifica los estilos para la historia.</p>`;
                    resultContainer.classList.remove('hidden');
                    return;
                }
                currentStoryPrompt = stylesInput.value;
                updateChapterCount(); 
            } else if (isRegenerate && !currentStoryPrompt) {
                 outputDiv.innerHTML = `<p class="text-red-400">Por favor, especifica estilos primero para poder regenerar.</p>`;
                 return;
            }

            resultContainer.classList.remove('hidden');
            outputDiv.innerHTML = `<p class="text-center text-green-400"><i class='ph ph-spinner animate-spin mr-2'></i> Generando historia con ${currentChapterCount} capítulos...</p>`;
            statusEl.textContent = `Generando historia: ${currentStoryPrompt.substring(0, 50)}...`;
            
            document.querySelectorAll('#story-controls-extension button').forEach(btn => btn.disabled = true);
            btnGenerateStory.disabled = true;

            let systemPrompt, prompt;
            let finalChapterCount = currentChapterCount;
            
            if (isExtension) {
                finalChapterCount = currentChapterCount + chaptersToAdd;
                systemPrompt = `Eres un escritor de ficción que continúa una historia. Tu tarea es tomar la historia ya escrita, continuar la narración y añadir ${chaptersToAdd} nuevos capítulos. El total de capítulos debe ser ${finalChapterCount}. Mantén el estilo, tono y estructura de la historia original. Asegúrate de que los nuevos capítulos se integren perfectamente y que el último capítulo se sienta como una conclusión natural o un gancho para más. Mantén el formato de capítulos y títulos.`;
                prompt = `Continúa y extiende la siguiente historia, escrita con los estilos: "${currentStoryPrompt}". Añade ${chaptersToAdd} capítulos. La historia actual es:\n\n${currentStory}`;
            } else {
                systemPrompt = `Eres un talentoso escritor de ficción. Tu tarea es escribir una historia original en español basada en el estilo, género y temas proporcionados. La historia DEBE estar estructurada en ${currentChapterCount} capítulos. Cada capítulo debe tener un título claro. NO añadas introducciones ni conclusiones fuera de la historia.`;
                prompt = `Escribe una historia con los siguientes estilos: ${currentStoryPrompt}`;
            }
            
            const storyText = await callGemini(prompt, systemPrompt, false, 0.8);

            document.querySelectorAll('#story-controls-extension button').forEach(btn => btn.disabled = false);
            btnGenerateStory.disabled = false;

            if (storyText) {
                currentStory = storyText;
                currentChapterCount = finalChapterCount;
                document.getElementById('num-chapters').value = currentChapterCount;

                outputDiv.innerHTML = currentStory; 
                statusEl.textContent = `Historia generada con ${currentChapterCount} capítulos.`;
                extensionControls.style.display = 'flex';
            } else {
                outputDiv.innerHTML = `<p class="text-center text-red-400">Error al generar la historia. Verifica la API Key.</p>`;
                statusEl.textContent = `Error en la generación.`;
                if(isExtension) currentChapterCount -= chaptersToAdd;
            }
        }

        function regenerateStory() {
            if (!currentStoryPrompt) return;
            // Regenera una nueva historia con los mismos estilos y el capítulo actual
            generateStory(true, false);
        }

        function downloadStory() {
            if (!currentStory) {
                document.getElementById('story-status').textContent = 'No hay historia para descargar.';
                return;
            }
            const filename = `historia_generada_${new Date().toISOString().substring(0, 10)}.txt`;
            downloadFile(currentStory, filename);
            document.getElementById('story-status').textContent = 'Historia descargada con éxito.';
        }

        function makeStoryLonger() {
            if (!currentStory || !currentStoryPrompt) {
                document.getElementById('story-status').textContent = 'Genera una historia primero.';
                return;
            }
            // Llama a la función para extender la historia
            generateStory(false, true);
        }


        // ==========================================
        // 4. LÓGICA EXTENSIÓN DE LIBROS IA
        // ==========================================
        let currentExtenderFile = null;
        let fullTextToExtend = '';
        
        const extenderFileEl = document.getElementById('extender-file');
        const extenderStatusEl = document.getElementById('extender-status');
        const extenderInputEl = document.getElementById('extender-input');
        const btnRunExtensionEl = document.getElementById('btn-run-extension');
        const extenderOutputEl = document.getElementById('extender-output');
        const extenderResultContainerEl = document.getElementById('extender-result-container');
        const extenderNumChaptersEl = document.getElementById('extender-num-chapters');

        extenderFileEl.addEventListener('change', async (e) => {
            currentExtenderFile = e.target.files[0];
            if (!currentExtenderFile) {
                extenderStatusEl.textContent = 'Ningún archivo cargado';
                extenderInputEl.value = '';
                btnRunExtensionEl.disabled = true;
                return;
            }

            extenderStatusEl.textContent = `${currentExtenderFile.name} (Procesando...)`;
            extenderInputEl.value = 'Extrayendo texto del archivo...';
            btnRunExtensionEl.disabled = true;

            try {
                if (currentExtenderFile.type === "application/pdf") {
                    fullTextToExtend = await extractTextFromPDF(currentExtenderFile);
                } else { 
                    fullTextToExtend = await currentExtenderFile.text();
                }

                // Usamos solo los últimos 8000 caracteres para el contexto de la extensión
                const lastContext = fullTextToExtend.slice(-8000);
                
                const displayMaxText = fullTextToExtend.substring(0, 1000) + (fullTextToExtend.length > 1000 ? '... [Texto completo cargado: ' + fullTextToExtend.length + ' caracteres]' : ''); 
                extenderInputEl.value = displayMaxText;
                extenderStatusEl.textContent = `${currentExtenderFile.name} (${fullTextToExtend.length} caracteres). Último contexto (${lastContext.length} chars) listo para extender.`;
                btnRunExtensionEl.disabled = false;

            } catch (error) {
                console.error("Error al procesar archivo para extensión:", error);
                extenderStatusEl.textContent = 'Error al leer el archivo.';
                extenderInputEl.value = 'Error al leer el contenido del archivo.';
                btnRunExtensionEl.disabled = true;
            }
        });

        async function extendBook(isRegenerate = false) {
            if (btnRunExtensionEl.disabled || !fullTextToExtend) return;

            const chaptersToAppend = parseInt(extenderNumChaptersEl.value) || 10;
            const lastContext = fullTextToExtend.slice(-8000); // Contexto de los últimos 8000 caracteres
            
            extenderOutputEl.innerHTML = `<p class="text-center text-pink-400"><i class='ph ph-spinner animate-spin mr-2'></i> Generando extensión de ${chaptersToAppend} capítulos...</p>`;
            extenderResultContainerEl.classList.remove('hidden');

            btnRunExtensionEl.disabled = true;
            document.getElementById('btn-download-extension').disabled = true;
            document.getElementById('btn-regenerate-extension').disabled = true;

            const systemPrompt = `Eres un escritor profesional encargado de continuar una obra literaria. Tu tarea es analizar el final del texto proporcionado (que es el contexto más reciente de la trama) y escribir una extensión orgánica de ${chaptersToAppend} nuevos capítulos que continúen la historia. El estilo, el tono y los personajes deben ser idénticos al original. La extensión debe comenzar inmediatamente después del último párrafo del texto proporcionado. La extensión debe estar estructurada en capítulos con títulos claros. NO añadas introducciones, explicaciones ni conclusiones externas.`;

            const prompt = `Continúa la siguiente obra literaria agregando ${chaptersToAppend} capítulos nuevos. Este es el final del texto que debes continuar: "${lastContext}"`;
            
            const extensionText = await callGemini(prompt, systemPrompt, false, 0.8);

            btnRunExtensionEl.disabled = false;
            document.getElementById('btn-download-extension').disabled = false;
            document.getElementById('btn-regenerate-extension').disabled = false;

            if (extensionText) {
                currentExtensionText = extensionText;
                extenderOutputEl.innerHTML = currentExtensionText;
                lastFullStory = fullTextToExtend + "\n\n" + currentExtensionText; // Guardar la historia completa (Original + Extensión)
                extenderStatusEl.textContent = `Extensión de ${chaptersToAppend} capítulos generada y lista para descargar.`;
            } else {
                extenderOutputEl.innerHTML = `<p class="text-center text-red-400">Error al generar la extensión. Intenta de nuevo.</p>`;
                extenderStatusEl.textContent = `Error en la generación de extensión.`;
            }
        }

        function regenerateExtension() {
            if (!fullTextToExtend) {
                extenderStatusEl.textContent = 'Carga un archivo primero para generar una extensión.';
                return;
            }
            extendBook(true);
        }

        function downloadExtension() {
            if (!lastFullStory) {
                extenderStatusEl.textContent = 'Genera una extensión primero.';
                return;
            }
            const filename = currentExtenderFile ? currentExtenderFile.name.replace(/\.(txt|pdf)$/i, '') + '_EXTENDIDO.txt' : 'historia_extendida.txt';
            downloadFile(lastFullStory, filename);
            extenderStatusEl.textContent = 'Historia extendida (Original + Nuevo) descargada con éxito.';
        }


        // ==========================================
        // 5. LÓGICA LECTOR INTELIGENTE (ANALIZAR Y LIMPIAR + TIMELINE)
        // ==========================================

        async function prepareTextForSpeech(rawText) {
            const statusEl = document.getElementById(ttsState['reader'].statusId);
            statusEl.textContent = 'Analizando y limpiando texto con IA...';

            let systemPrompt = `Tu tarea es actuar como un editor de audio experto. Debes preparar el texto para ser leído en voz alta.
            REGLAS DE ORO (ESTRICTAS):
            1. ELIMINA COMPLETAMENTE los caracteres '#' y '*'.
            2. Detecta y ELIMINA frases irrelevantes (ej: "escanear para descargar", "visite nuestro sitio", "página X").
            3. Inserta '<break time="500ms"/>' en pausas naturales.
            4. No resumas.
            5. SALIDA: Texto limpio en español.`;

            const prompt = rawText.substring(0, 8000); 
            
            const cleanText = await callGemini(prompt, systemPrompt, false, 0.2); 
            
            if (cleanText) {
                 statusEl.textContent = 'Texto limpio y analizado. Calculando duración...';
                 return cleanText;
            } else {
                 statusEl.textContent = 'Fallo en la limpieza IA. Usando texto original.';
                 return rawText; 
            }
        }
        
        function formatTime(seconds) {
            const m = Math.floor(seconds / 60);
            const s = Math.floor(seconds % 60);
            return `${m.toString().padStart(2, '0')}:${s.toString().padStart(2, '0')}`;
        }
        
        function updateReaderDuration() {
            const state = ttsState['reader'];
            const speed = parseFloat(document.getElementById(state.speedId).value) || 1.0;
            
            // Re-estimar duración total
            // Promedio palabras por minuto normal ~150 palabras
            // Palabras totales:
            const allWords = state.chunks.join(' ').split(/\s+/).length;
            const baseSeconds = (allWords / 150) * 60; // Duración a velocidad 1.0
            state.totalEstimatedSeconds = baseSeconds / speed;
            
            // Recalcular tiempos de chunks
            state.chunkTimes = [];
            let accumulatedTime = 0;
            state.chunks.forEach(chunk => {
                state.chunkTimes.push(accumulatedTime);
                const chunkWords = chunk.split(/\s+/).length;
                const chunkDuration = (chunkWords / 150) * 60 / speed;
                accumulatedTime += chunkDuration;
            });

            // Actualizar UI
            document.getElementById('time-total').textContent = formatTime(state.totalEstimatedSeconds);
            updateTimelineUI();
        }

        function updateTimelineUI() {
            const state = ttsState['reader'];
            if(state.chunks.length === 0) return;

            // Tiempo actual basado en el chunk actual
            const currentTime = state.chunkTimes[state.currentChunk] || 0;
            document.getElementById('time-current').textContent = formatTime(currentTime);
            
            // Slider (0-100) -> Mapear tiempo actual a porcentaje
            const percentage = (currentTime / state.totalEstimatedSeconds) * 100;
            document.getElementById('reader-timeline').value = percentage || 0;
        }

        function scrubTimeline() {
            const slider = document.getElementById('reader-timeline');
            const state = ttsState['reader'];
            const percent = parseFloat(slider.value);
            const targetTime = (percent / 100) * state.totalEstimatedSeconds;
            
            // Buscar el chunk más cercano a este tiempo
            let closestChunk = 0;
            for(let i=0; i<state.chunkTimes.length; i++) {
                if (state.chunkTimes[i] <= targetTime) {
                    closestChunk = i;
                } else {
                    break;
                }
            }
            
            document.getElementById('time-current').textContent = formatTime(targetTime);
            
            // Solo saltar si el cambio es intencional (onchange) o si pausado
            if(Math.abs(closestChunk - state.currentChunk) > 0) {
                 jumpReader('reader', closestChunk);
            }
        }
        
        function handleTimeJump(event) {
            if(event.key === 'Enter') {
                const input = event.target.value.trim(); // Formato mm:ss
                const parts = input.split(':');
                if(parts.length === 2) {
                    const minutes = parseInt(parts[0]);
                    const seconds = parseInt(parts[1]);
                    if(!isNaN(minutes) && !isNaN(seconds)) {
                        const totalSeconds = (minutes * 60) + seconds;
                        const state = ttsState['reader'];
                        // Mapear segundos a slider %
                        const percent = (totalSeconds / state.totalEstimatedSeconds) * 100;
                        const slider = document.getElementById('reader-timeline');
                        slider.value = Math.min(100, Math.max(0, percent));
                        scrubTimeline(); // Forzar salto
                    }
                }
            }
        }

        // Detectar selección de texto para leer
        function checkSelectionForReading() {
            const selection = window.getSelection();
            if (selection.toString().length > 0) {
                const anchorNode = selection.anchorNode;
                // Buscar el elemento padre que sea un span de chunk
                let parent = anchorNode.parentElement;
                if (parent && parent.id && parent.id.startsWith('reader-chunk-')) {
                    const index = parseInt(parent.id.replace('reader-chunk-', ''));
                    jumpReader('reader', index);
                }
            }
        }

        // Manejador de evento change
        document.getElementById(`reader-file`).addEventListener('change', async (e) => {
            const file = e.target.files[0];
            if(!file) return;

            const mode = 'reader';
            const state = ttsState[mode];
            const outputContainer = document.getElementById(state.textBoxId);
            outputContainer.innerHTML = "<p class='text-center text-blue-500 italic mt-10'>Cargando, analizando y limpiando texto...</p>";
            
            // Reiniciar estado solo al cargar NUEVO archivo
            readerStop(mode); 

            let rawText = "";
            try {
                    if(file.type === "application/pdf") {
                        rawText = await extractTextFromPDF(file);
                    } else {
                        rawText = await file.text();
                    }
            } catch (e) {
                    outputContainer.innerHTML = "<p class='text-center text-red-500 italic mt-10'>Error al leer el archivo.</p>";
                    return;
            }
            
            const processedText = await prepareTextForSpeech(rawText);

            state.chunks = processedText.split(/<break time="500ms"\/>/g).flatMap(chunk => 
                chunk.match(/[^.!?]+[.!?]+(\s|$)/g) || [chunk]
            ).filter(c => c.trim().length > 0);

            if (state.chunks.length === 0) { 
                outputContainer.innerHTML = "<p class='text-center text-red-500 italic mt-10'>La IA determinó que no había contenido narrativo válido o hubo un error.</p>";
                return;
            }
            
            // Calcular tiempos iniciales
            updateReaderDuration();
            
            state.currentChunk = 0;
            outputContainer.innerHTML = state.chunks.map((c,i) => {
                const displayText = c.trim(); 
                return `<span id="${mode}-chunk-${i}" class="transition-colors duration-300 p-1 rounded hover:bg-gray-800 cursor-pointer text-justify inline-block mr-1" onclick="jumpReader('${mode}', ${i})">${displayText}</span>`
            }).join(' '); 
            
            document.getElementById(state.statusId).textContent = `Listo. Duración aprox: ${formatTime(state.totalEstimatedSeconds)}`;
            updateTimelineUI();
        });


        const synth = window.speechSynthesis;
        function loadVoices() {
            const voices = synth.getVoices();
            ['reader'].forEach(mode => {
                const sel = document.getElementById(`${mode}-voice`);
                if (!sel) return;
                sel.innerHTML = '';
                const esVoices = voices.filter(v => v.lang.startsWith('es')).sort((a, b) => a.name.localeCompare(b.name));
                
                esVoices.forEach(v => {
                    const opt = document.createElement('option');
                    opt.text = v.name;
                    opt.value = v.name;
                    sel.appendChild(opt);
                });
                
                if(sel.options.length === 0) {
                    const opt = document.createElement('option'); opt.text = "Voz por Defecto"; sel.appendChild(opt);
                } else {
                    sel.selectedIndex = 0; 
                }
            });
        }
        if(speechSynthesis.onvoiceschanged !== undefined) speechSynthesis.onvoiceschanged = loadVoices;
        loadVoices(); 


        function speakChunk(mode) {
            const state = ttsState[mode];
            // Si llegamos al final, detener
            if(state.currentChunk >= state.chunks.length) { 
                state.isPlaying = false; 
                updateBtn(mode); 
                return; 
            }
            
            // Highlight visual
            document.querySelectorAll(`#${state.textBoxId} span`).forEach(s => s.classList.remove('highlighted-chunk'));
            const el = document.getElementById(`${mode}-chunk-${state.currentChunk}`);
            if(el) {
                el.classList.add('highlighted-chunk');
                el.scrollIntoView({behavior: "smooth", block: "center"});
            }
            
            updateTimelineUI();

            const textToSpeak = state.chunks[state.currentChunk];
            state.utter = new SpeechSynthesisUtterance(textToSpeak); 

            state.utter.rate = document.getElementById(state.speedId).value;
            const vName = document.getElementById(state.voiceId).value;
            const voice = synth.getVoices().find(v => v.name === vName);
            if(voice) state.utter.voice = voice;

            state.utter.onend = (event) => {
                // Solo avanzamos automáticamente si NO hemos pausado manualmente
                if(state.isPlaying) {
                    state.currentChunk++;
                    speakChunk(mode);
                }
            };
            
            state.utter.onerror = (e) => {
                console.error("TTS Error:", e);
                // Si es un error real y no una cancelación manual
                if (e.error !== 'interrupted' && e.error !== 'canceled') {
                     state.currentChunk++;
                     if(state.isPlaying) speakChunk(mode);
                }
            }

            synth.speak(state.utter);
        }

        // FUNCIÓN MEJORADA: Alternar Pausa/Play con cancelación para evitar bugs
        function toggleReader(mode) {
            const state = ttsState[mode];
            if(state.chunks.length === 0) { 
                document.getElementById(state.statusId).textContent = 'Carga un archivo primero.';
                return;
            }

            if(state.isPlaying) {
                // ESTADO: Poniendo en PAUSA
                // Estrategia: Cancelar el audio actual. 
                // Al cancelar, NO avanzamos el chunk (controlado en onend).
                readerPause(mode);
            } else {
                // ESTADO: Dando PLAY
                // Estrategia: Empezar a hablar desde el chunk actual.
                state.isPlaying = true;
                speakChunk(mode);
            }
            updateBtn(mode);
        }

        // Nueva función solo para PAUSAR sin reiniciar
        function readerPause(mode) {
            const state = ttsState[mode];
            state.isPlaying = false; // Marcar como pausado ANTES de cancelar
            synth.cancel(); // Detiene el audio inmediatamente
            updateBtn(mode);
        }

        // Función de STOP total (Reinicia al principio)
        function readerStop(mode) { 
            const state = ttsState[mode];
            state.isPlaying = false; 
            synth.cancel(); 
            state.currentChunk = 0; // Reiniciar al inicio
            updateBtn(mode); 
            document.querySelectorAll(`#${state.textBoxId} span`).forEach(s => s.classList.remove('highlighted-chunk'));
            updateTimelineUI();
        }

        function readerForward(mode) { 
            const state = ttsState[mode];
            synth.cancel(); 
            state.currentChunk = Math.min(state.chunks.length - 1, state.currentChunk + 1); 
            if(state.isPlaying) speakChunk(mode); 
            else {
                updateTimelineUI();
                const el = document.getElementById(`${mode}-chunk-${state.currentChunk}`);
                if(el) el.scrollIntoView({behavior: "smooth", block: "center"});
                // Highlight manual si está pausado
                document.querySelectorAll(`#${state.textBoxId} span`).forEach(s => s.classList.remove('highlighted-chunk'));
                if(el) el.classList.add('highlighted-chunk');
            }
        }

        function readerRewind(mode) { 
            const state = ttsState[mode];
            synth.cancel(); 
            state.currentChunk = Math.max(0, state.currentChunk - 1); 
            if(state.isPlaying) speakChunk(mode); 
            else {
                updateTimelineUI();
                const el = document.getElementById(`${mode}-chunk-${state.currentChunk}`);
                if(el) el.scrollIntoView({behavior: "smooth", block: "center"});
                // Highlight manual si está pausado
                document.querySelectorAll(`#${state.textBoxId} span`).forEach(s => s.classList.remove('highlighted-chunk'));
                if(el) el.classList.add('highlighted-chunk');
            }
        }

        function jumpReader(mode, i) { 
            const state = ttsState[mode];
            if (i < 0 || i >= state.chunks.length) return;
            synth.cancel(); 
            state.currentChunk = i; 
            state.isPlaying = true; 
            updateBtn(mode); 
            speakChunk(mode); 
        }
        
        function updateBtn(mode) {
            const state = ttsState[mode];
            const btn = document.getElementById(state.buttonId);
            if (!btn) return;
            btn.innerHTML = state.isPlaying ? '<i class="ph-fill ph-pause text-2xl"></i>' : '<i class="ph-fill ph-play text-2xl"></i>';
        }


        // Inicialización al cargar la página
        document.addEventListener('DOMContentLoaded', () => {
            // Asegurar que la primera pestaña esté activa al inicio
            switchTab('pdf-tool', document.querySelector('.nav-btn'));
        });

    </script>
</body>
</html>
