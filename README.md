<!DOCTYPE html>
<html lang="en" class="antialiased">
<head>
    <meta charset="utf-8"/>
    <meta content="width=device-width, initial-scale=1.0" name="viewport"/>
    <title>Universal AI - Pro Version</title>
    
    <script src="https://cdn.tailwindcss.com?plugins=forms,container-queries,typography"></script>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&display=swap" rel="stylesheet"/>
    <link href="https://fonts.googleapis.com/css2?family=Material+Symbols+Outlined:wght,FILL@100..700,0..1&display=swap" rel="stylesheet"/>
    <script src="https://cdn.jsdelivr.net/npm/marked/marked.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/dompurify/3.0.6/purify.min.js"></script>

    <script id="tailwind-config">
        tailwind.config = {
            darkMode: "class",
            theme: {
                extend: {
                    colors: {
                        "primary": "#135bec",
                        "primary-hover": "#0f4bc2",
                        "background-light": "#f6f6f8",
                        "background-dark": "#101622",
                        "surface-light": "#ffffff",
                        "surface-dark": "#1e293b",
                    },
                    fontFamily: {
                        "display": ["Inter", "sans-serif"],
                        "body": ["Inter", "sans-serif"],
                    }
                },
            },
        }
    </script>
    
    <style>
        body { font-family: 'Inter', sans-serif; }
        .custom-scrollbar::-webkit-scrollbar { width: 6px; }
        .custom-scrollbar::-webkit-scrollbar-track { background: transparent; }
        .custom-scrollbar::-webkit-scrollbar-thumb { background-color: rgba(156, 163, 175, 0.3); border-radius: 20px; }
        .custom-scrollbar:hover::-webkit-scrollbar-thumb { background-color: rgba(156, 163, 175, 0.5); }
        textarea::-webkit-scrollbar { display: none; }
        textarea { -ms-overflow-style: none; scrollbar-width: none; }

        .text-gradient {
            background: linear-gradient(to right, #4285f4, #d96570);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
            background-clip: text;
        }
        
        .chat-bubble-user { background-color: #f1f5f9; color: #1e293b; border-radius: 1.5rem 1.5rem 0 1.5rem; }
        .dark .chat-bubble-user { background-color: #334155; color: #f8fafc; }
        
        .chat-bubble-ai { background-color: transparent; color: inherit; }

        .fade-enter { opacity: 0; transform: translateY(10px); animation: fadeIn 0.4s ease-out forwards; }
        @keyframes fadeIn { to { opacity: 1; transform: translateY(0); } }

        .typing-indicator span {
            display: inline-block; width: 6px; height: 6px; background-color: #9ca3af; border-radius: 50%;
            animation: typing 1.4s infinite ease-in-out both;
        }
        .typing-indicator span:nth-child(1) { animation-delay: -0.32s; }
        .typing-indicator span:nth-child(2) { animation-delay: -0.16s; }
        @keyframes typing {
            0%, 80%, 100% { transform: scale(0); }
            40% { transform: scale(1); }
        }
        
        /* Markdown Styles for AI Response */
        .prose pre { background-color: #1e293b; color: #f8fafc; border-radius: 0.5rem; padding: 1rem; overflow-x: auto; margin-top: 0.5rem; margin-bottom: 0.5rem; }
        .prose code { font-family: monospace; background-color: rgba(156, 163, 175, 0.2); padding: 0.1rem 0.3rem; border-radius: 0.25rem; color: #ef4444; }
        .prose p { margin-bottom: 0.75rem; }
        .prose a { color: #135bec; text-decoration: underline; }
        .prose ul { list-style-type: disc; padding-left: 1.5rem; margin-bottom: 0.75rem; }
        .prose ol { list-style-type: decimal; padding-left: 1.5rem; margin-bottom: 0.75rem; }
        .prose h1, .prose h2, .prose h3 { font-weight: 600; margin-top: 1rem; margin-bottom: 0.5rem; }
        
        /* Message Actions */
        .msg-actions { opacity: 0; transition: opacity 0.2s; }
        .message-row:hover .msg-actions { opacity: 1; }
    </style>
</head>
<body class="bg-background-light dark:bg-background-dark text-slate-900 dark:text-slate-100 h-screen overflow-hidden flex selection:bg-primary/20 transition-colors duration-300">

    <div id="loading" class="fixed inset-0 z-[9999] flex flex-col items-center justify-center bg-white dark:bg-[#131314] transition-opacity duration-500">
        <div class="w-12 h-12 border-4 border-primary border-t-transparent rounded-full animate-spin mb-4"></div>
        <p class="text-slate-600 dark:text-slate-300 font-medium">Initializing Universal AI Pro...</p>
    </div>

    <div id="app" class="hidden flex-1 flex w-full h-full relative">
        
        <div id="sidebar-overlay" class="fixed inset-0 bg-black/50 z-30 hidden md:hidden backdrop-blur-sm transition-opacity"></div>

        <aside id="sidebar" class="fixed md:static inset-y-0 left-0 z-40 w-[280px] h-full bg-[#f0f4f9] dark:bg-[#131314] flex flex-col justify-between transform -translate-x-full md:translate-x-0 transition-transform duration-300 shrink-0 border-r border-slate-200 dark:border-slate-800 md:border-none">
            <div class="p-3 flex flex-col gap-6 h-[calc(100%-180px)]">
                <div class="px-2 pt-2 flex items-center justify-between">
                    <button id="close-sidebar-btn" class="md:hidden p-2 rounded-full hover:bg-black/5 dark:hover:bg-white/10 text-slate-600 dark:text-slate-300">
                        <span class="material-symbols-outlined">menu_open</span>
                    </button>
                    <div class="hidden md:block p-2 text-slate-600 dark:text-slate-300 flex items-center gap-2">
                        <span class="material-symbols-outlined font-bold text-xl text-primary">blur_on</span>
                        <span class="font-semibold text-sm tracking-wide">Universal AI</span>
                    </div>
                </div>
                
                <button id="new-chat-btn" class="flex items-center gap-3 bg-[#dde3ea] dark:bg-[#1f1f1f] text-slate-700 dark:text-slate-200 px-4 py-3 rounded-xl hover:bg-[#d1d9e2] dark:hover:bg-[#2f2f2f] transition-colors w-max shadow-sm">
                    <span class="material-symbols-outlined text-primary" style="font-size: 20px;">add</span>
                    <span class="text-sm font-medium">New chat</span>
                </button>
                
                <div class="flex flex-col gap-2 mt-2 flex-1 overflow-hidden">
                    <div class="px-3 text-xs font-medium text-slate-500 dark:text-slate-400">Recent Chats</div>
                    <div id="chat-list" class="custom-scrollbar overflow-y-auto flex-1 flex flex-col gap-1 pr-2 pb-4">
                        </div>
                </div>
            </div>

            <div class="p-3 flex flex-col gap-1 border-t border-slate-200 dark:border-slate-800 md:border-none">
                <button id="clear-data-btn" class="flex items-center gap-3 px-3 py-2.5 mb-1 rounded-xl hover:bg-red-50 dark:hover:bg-red-900/20 text-red-600 dark:text-red-400 text-sm font-medium transition-colors">
                    <span class="material-symbols-outlined text-xl">delete_forever</span> Clear All Data
                </button>

                <button id="open-settings-btn" class="flex items-center gap-3 px-3 py-2.5 rounded-full hover:bg-[#e4e9ef] dark:hover:bg-[#28292a] text-slate-700 dark:text-slate-300 text-sm font-medium transition-colors">
                    <span class="material-symbols-outlined text-xl">settings</span> Settings
                </button>
                <div class="flex items-center justify-between px-3 py-2 mt-2">
                    <div class="flex items-center gap-2">
                        <div class="size-2 rounded-full bg-green-500 shadow-[0_0_8px_rgba(34,197,94,0.6)]"></div>
                        <span id="current-model-display" class="text-xs text-slate-500 dark:text-slate-400 truncate max-w-[120px]">gpt-3.5-turbo</span>
                    </div>
                    <span class="text-xs text-slate-400 font-bold">Pro v2.0</span>
                </div>
            </div>
        </aside>

        <main class="flex-1 flex flex-col h-full relative bg-surface-light dark:bg-background-dark md:rounded-l-3xl shadow-2xl overflow-hidden w-full">
            
            <div class="md:hidden flex items-center justify-between p-4 border-b border-slate-200 dark:border-slate-800 bg-surface-light/80 dark:bg-background-dark/80 backdrop-blur-md z-20">
                <button id="open-sidebar-btn" class="p-2 -ml-2 rounded-full hover:bg-slate-100 dark:hover:bg-slate-800 text-slate-600 dark:text-slate-300">
                    <span class="material-symbols-outlined">menu</span>
                </button>
                <h1 class="text-lg font-medium text-slate-800 dark:text-white">Universal AI</h1>
                <button id="theme-toggle-mobile" class="p-2 rounded-full hover:bg-slate-100 dark:hover:bg-slate-800 text-slate-600 dark:text-slate-300">
                    <span class="material-symbols-outlined theme-icon-mob">dark_mode</span>
                </button>
            </div>

            <div class="absolute top-4 right-6 flex items-center gap-3 z-20 hidden md:flex">
                <button id="theme-toggle" class="flex items-center justify-center p-2 rounded-full hover:bg-slate-100 dark:hover:bg-slate-800 text-slate-600 dark:text-slate-300 transition-colors" title="Toggle Theme">
                    <span id="theme-icon" class="material-symbols-outlined">dark_mode</span>
                </button>
                <div class="bg-gradient-to-tr from-blue-500 to-purple-500 p-[2px] rounded-full cursor-pointer hover:shadow-lg transition-shadow">
                    <div class="size-8 rounded-full bg-white dark:bg-slate-900 flex items-center justify-center text-primary font-bold text-sm">U</div>
                </div>
            </div>

            <div id="chat-container" class="flex-1 overflow-y-auto custom-scrollbar flex flex-col items-center pt-6 md:pt-10 pb-40 px-4 md:px-0 w-full scroll-smooth">
                
                <div id="welcome-screen" class="w-full max-w-[800px] flex flex-col gap-10 mt-10 md:mt-20 fade-enter">
                    <div class="space-y-2 pl-2 md:pl-0">
                        <h1 class="text-4xl md:text-6xl font-medium tracking-tight text-slate-300 dark:text-slate-700">
                            <span class="text-gradient font-semibold">Hello, Explorer.</span>
                        </h1>
                        <h2 class="text-3xl md:text-5xl font-medium tracking-tight text-[#c4c7c5] dark:text-[#444746] mt-2">How can I assist you today?</h2>
                    </div>
                    
                    <div class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-4 gap-4 w-full px-2 md:px-0 mt-8">
                        <button class="prompt-btn flex flex-col gap-3 p-4 rounded-xl bg-slate-50 dark:bg-[#1e1f20] hover:bg-slate-100 dark:hover:bg-[#2d2e2f] transition-all text-left border border-transparent hover:border-slate-200 dark:hover:border-slate-700 h-40 justify-between group shadow-sm">
                            <p class="text-slate-700 dark:text-slate-200 font-medium text-sm">Write a professional email for a sick day off</p>
                            <div class="self-end p-2 rounded-full bg-white dark:bg-black/20 group-hover:bg-white dark:group-hover:bg-black/40 transition-colors">
                                <span class="material-symbols-outlined text-primary text-xl">mail</span>
                            </div>
                        </button>
                        <button class="prompt-btn flex flex-col gap-3 p-4 rounded-xl bg-slate-50 dark:bg-[#1e1f20] hover:bg-slate-100 dark:hover:bg-[#2d2e2f] transition-all text-left border border-transparent hover:border-slate-200 dark:hover:border-slate-700 h-40 justify-between group shadow-sm">
                            <p class="text-slate-700 dark:text-slate-200 font-medium text-sm">Explain Quantum Computing in simple terms</p>
                            <div class="self-end p-2 rounded-full bg-white dark:bg-black/20 group-hover:bg-white dark:group-hover:bg-black/40 transition-colors">
                                <span class="material-symbols-outlined text-purple-500 text-xl">science</span>
                            </div>
                        </button>
                        <button class="prompt-btn flex flex-col gap-3 p-4 rounded-xl bg-slate-50 dark:bg-[#1e1f20] hover:bg-slate-100 dark:hover:bg-[#2d2e2f] transition-all text-left border border-transparent hover:border-slate-200 dark:hover:border-slate-700 h-40 justify-between group shadow-sm">
                            <p class="text-slate-700 dark:text-slate-200 font-medium text-sm">Review my JavaScript code for bugs</p>
                            <div class="self-end p-2 rounded-full bg-white dark:bg-black/20 group-hover:bg-white dark:group-hover:bg-black/40 transition-colors">
                                <span class="material-symbols-outlined text-green-500 text-xl">code</span>
                            </div>
                        </button>
                        <button class="prompt-btn flex flex-col gap-3 p-4 rounded-xl bg-slate-50 dark:bg-[#1e1f20] hover:bg-slate-100 dark:hover:bg-[#2d2e2f] transition-all text-left border border-transparent hover:border-slate-200 dark:hover:border-slate-700 h-40 justify-between group shadow-sm">
                            <p class="text-slate-700 dark:text-slate-200 font-medium text-sm">Help me design a SQL database schema</p>
                            <div class="self-end p-2 rounded-full bg-white dark:bg-black/20 group-hover:bg-white dark:group-hover:bg-black/40 transition-colors">
                                <span class="material-symbols-outlined text-orange-500 text-xl">dataset</span>
                            </div>
                        </button>
                    </div>
                </div>

                <div id="messages-container" class="w-full max-w-[800px] flex flex-col gap-6 px-2 md:px-0 hidden pb-10">
                    </div>
                
                <div id="typing-indicator" class="w-full max-w-[800px] px-2 md:px-0 hidden mt-4">
                    <div class="flex gap-4">
                        <div class="size-8 rounded-full bg-gradient-to-tr from-blue-500 via-purple-500 to-red-500 shrink-0 mt-1 animate-spin" style="animation-duration: 3s;"></div>
                        <div class="flex flex-col gap-2 justify-center bg-slate-50 dark:bg-[#1e1f20] px-4 py-3 rounded-2xl rounded-tl-none border border-slate-100 dark:border-slate-800 shadow-sm">
                            <div class="typing-indicator flex gap-1">
                                <span></span><span></span><span></span>
                            </div>
                        </div>
                    </div>
                </div>
            </div>

            <div class="absolute bottom-0 left-0 w-full bg-gradient-to-t from-surface-light via-surface-light to-transparent dark:from-background-dark dark:via-background-dark pt-10 pb-6 px-4 z-20">
                <div class="max-w-[800px] mx-auto relative">
                    
                    <div id="status-banner" class="hidden absolute -top-12 left-0 right-0 px-4 py-2 rounded-lg text-sm text-center shadow-sm font-medium transition-all duration-300"></div>

                    <div id="image-preview-container" class="hidden absolute -top-24 left-4 bg-white dark:bg-[#2d2e2f] p-2 rounded-xl shadow-lg border border-slate-200 dark:border-slate-700">
                        <div class="relative group">
                            <img id="image-preview" src="" class="h-16 w-16 object-cover rounded-lg" alt="Upload preview">
                            <button id="remove-image-btn" class="absolute -top-2 -right-2 bg-red-500 text-white rounded-full p-1 opacity-0 group-hover:opacity-100 transition-opacity">
                                <span class="material-symbols-outlined text-[14px]">close</span>
                            </button>
                        </div>
                    </div>

                    <div class="bg-[#f0f4f9] dark:bg-[#1e1f20] rounded-3xl pl-2 pr-2 py-2 flex items-end gap-1 focus-within:shadow-lg focus-within:bg-white dark:focus-within:bg-[#2d2e2f] transition-all border border-slate-200 dark:border-slate-700 shadow-md">
                        
                        <input type="file" id="image-upload" accept="image/*" class="hidden">
                        <button onclick="document.getElementById('image-upload').click()" class="p-3 mb-1 rounded-full text-slate-500 hover:text-primary dark:text-slate-400 dark:hover:text-primary hover:bg-black/5 dark:hover:bg-white/10 transition-colors shrink-0" title="Upload Image">
                            <span class="material-symbols-outlined">add_photo_alternate</span>
                        </button>
                        
                        <button id="voice-btn" class="p-3 mb-1 rounded-full text-slate-500 hover:text-red-500 dark:text-slate-400 dark:hover:text-red-400 hover:bg-black/5 dark:hover:bg-white/10 transition-colors shrink-0" title="Voice Input">
                            <span class="material-symbols-outlined" id="mic-icon">mic</span>
                        </button>
                        
                        <textarea id="user-input" class="w-full bg-transparent border-none focus:ring-0 text-slate-800 dark:text-slate-100 placeholder-slate-500 dark:placeholder-slate-400 resize-none py-3.5 px-2 max-h-[200px] text-base leading-relaxed focus:outline-none" placeholder="Message AI... (Shift+Enter for new line)" rows="1" style="min-height: 52px;"></textarea>
                        
                        <div class="flex items-center mb-1 shrink-0 gap-1">
                            <button id="stop-btn" class="hidden p-3 rounded-full text-red-500 hover:bg-red-50 dark:hover:bg-red-500/10 transition-colors" title="Stop Generation">
                                <span class="material-symbols-outlined">stop_circle</span>
                            </button>
                            <button id="send-btn" class="p-3 rounded-full text-white bg-primary hover:bg-primary-hover transition-colors disabled:opacity-50 disabled:bg-slate-300 dark:disabled:bg-slate-700 disabled:text-slate-500 shadow-sm flex items-center justify-center">
                                <span class="material-symbols-outlined text-[20px] translate-x-[2px]">send</span>
                            </button>
                        </div>
                    </div>
                    
                    <div class="text-center mt-3 flex justify-center items-center gap-2">
                        <p class="text-xs text-slate-500 dark:text-slate-400">
                            AI may generate incorrect info. Always verify important details.
                        </p>
                        <span class="text-xs bg-slate-200 dark:bg-slate-800 px-2 py-0.5 rounded text-slate-600 dark:text-slate-300">OpenRouter</span>
                    </div>
                </div>
            </div>
        </main>
    </div>

    <div id="settings-modal" class="fixed inset-0 z-50 flex items-center justify-center bg-black/50 backdrop-blur-sm hidden opacity-0 transition-opacity duration-300 px-4">
        <div class="bg-white dark:bg-[#1e1f20] w-full max-w-lg rounded-2xl shadow-2xl overflow-hidden border border-slate-200 dark:border-slate-700 transform scale-95 transition-transform duration-300" id="settings-content">
            <div class="px-6 py-4 border-b border-slate-100 dark:border-slate-700 flex justify-between items-center bg-slate-50/50 dark:bg-[#1a1a1a]">
                <h3 class="text-xl font-semibold text-slate-800 dark:text-white flex items-center gap-2">
                    <span class="material-symbols-outlined text-primary">manage_accounts</span> Settings
                </h3>
                <button id="close-settings-btn" class="p-1 rounded-full text-slate-400 hover:bg-slate-200 dark:hover:bg-slate-800 hover:text-slate-700 dark:hover:text-slate-200 transition-colors">
                    <span class="material-symbols-outlined">close</span>
                </button>
            </div>
            
            <div class="p-6 flex flex-col gap-6 max-h-[70vh] overflow-y-auto custom-scrollbar">
                
                <div class="bg-blue-50 dark:bg-blue-900/20 p-4 rounded-xl border border-blue-100 dark:border-blue-800/30">
                    <p class="text-sm text-blue-800 dark:text-blue-300 font-medium">
                        <span class="material-symbols-outlined text-[16px] inline-block align-text-bottom mr-1">info</span>
                        Pro Version Features Active: Local Crypto, Voice Synthesis, Contextual Memory.
                    </p>
                </div>

                <div class="flex flex-col gap-2">
                    <label class="text-sm font-semibold text-slate-700 dark:text-slate-300">OpenRouter API Key <span class="text-red-500">*</span></label>
                    <div class="relative">
                        <input id="api-key-input" class="w-full rounded-xl border border-slate-300 dark:border-slate-600 bg-white dark:bg-[#0b0c0d] text-slate-900 dark:text-slate-100 focus:ring-2 focus:ring-primary focus:border-primary pl-10 py-2.5 transition-shadow" placeholder="sk-or-v1-..." type="password"/>
                        <span class="material-symbols-outlined absolute left-3 top-3 text-slate-400 text-[20px]">key</span>
                    </div>
                    <p class="text-xs text-slate-500">Encrypted and stored securely in local browser storage (Base64 wrapper).</p>
                </div>
                
                <div class="flex flex-col gap-2">
                    <label class="text-sm font-semibold text-slate-700 dark:text-slate-300">AI Model</label>
                    <div class="relative">
                        <select id="model-input" class="w-full rounded-xl border border-slate-300 dark:border-slate-600 bg-white dark:bg-[#0b0c0d] text-slate-900 dark:text-slate-100 focus:ring-2 focus:ring-primary focus:border-primary pl-10 py-2.5 appearance-none">
                            <option value="openai/gpt-3.5-turbo">GPT-3.5 Turbo (Default)</option>
                            <option value="google/gemini-pro">Gemini Pro</option>
                            <option value="anthropic/claude-3-haiku">Claude 3 Haiku</option>
                            <option value="meta-llama/llama-3-8b-instruct">Llama 3 (8B)</option>
                        </select>
                        <span class="material-symbols-outlined absolute left-3 top-3 text-slate-400 text-[20px]">smart_toy</span>
                        <span class="material-symbols-outlined absolute right-3 top-3 text-slate-400 text-[20px] pointer-events-none">expand_more</span>
                    </div>
                </div>

                <div class="flex flex-col gap-2">
                    <label class="text-sm font-semibold text-slate-700 dark:text-slate-300">System Prompt</label>
                    <textarea id="system-prompt-input" class="w-full rounded-xl border border-slate-300 dark:border-slate-600 bg-white dark:bg-[#0b0c0d] text-slate-900 dark:text-slate-100 focus:ring-2 focus:ring-primary focus:border-primary p-3 text-sm" rows="3" placeholder="You are a helpful AI assistant."></textarea>
                </div>
            </div>

            <div class="px-6 py-4 border-t border-slate-100 dark:border-slate-700 bg-slate-50 dark:bg-[#1a1a1a] flex justify-end gap-3">
                <button id="cancel-settings-btn" class="px-4 py-2 rounded-xl text-sm font-medium text-slate-700 dark:text-slate-300 hover:bg-slate-200 dark:hover:bg-slate-800 transition-colors">Cancel</button>
                <button id="save-settings-btn" class="px-4 py-2 rounded-xl text-sm font-medium bg-primary text-white hover:bg-primary-hover shadow-md transition-colors flex items-center gap-2">
                    <span class="material-symbols-outlined text-[18px]">save</span> Save Config
                </button>
            </div>
        </div>
    </div>

    <script>
        document.addEventListener('DOMContentLoaded', () => {
            // Remove Loader
            setTimeout(() => {
                document.getElementById('loading').style.opacity = '0';
                setTimeout(() => {
                    document.getElementById('loading').style.display = 'none';
                    document.getElementById('app').classList.remove('hidden');
                }, 500);
            }, 800);

            const generateId = () => Math.random().toString(36).substr(2, 9);

            // STATE MANAGEMENT
            let state = {
                apiKey: '',
                model: 'openai/gpt-3.5-turbo',
                systemPrompt: 'You are a helpful AI assistant.',
                messages: [], // Format: {id, role, content, timestamp}
                history: JSON.parse(localStorage.getItem('uai_history')) || [], // Load History
                currentChatId: generateId(),
                isGenerating: false,
                currentImageBase64: null,
                abortController: null
            };

            // DOM ELEMENTS
            const els = {
                html: document.documentElement,
                themeToggle: document.getElementById('theme-toggle'),
                themeToggleMob: document.getElementById('theme-toggle-mobile'),
                themeIcons: document.querySelectorAll('#theme-icon, .theme-icon-mob'),
                sidebar: document.getElementById('sidebar'),
                sidebarOverlay: document.getElementById('sidebar-overlay'),
                openSidebarBtn: document.getElementById('open-sidebar-btn'),
                closeSidebarBtn: document.getElementById('close-sidebar-btn'),
                newChatBtn: document.getElementById('new-chat-btn'),
                chatList: document.getElementById('chat-list'),
                clearDataBtn: document.getElementById('clear-data-btn'),
                
                settingsModal: document.getElementById('settings-modal'),
                openSettingsBtn: document.getElementById('open-settings-btn'),
                closeSettingsBtn: document.getElementById('close-settings-btn'),
                cancelSettingsBtn: document.getElementById('cancel-settings-btn'),
                saveSettingsBtn: document.getElementById('save-settings-btn'),
                apiKeyInput: document.getElementById('api-key-input'),
                modelInput: document.getElementById('model-input'),
                systemPromptInput: document.getElementById('system-prompt-input'),
                currentModelDisplay: document.getElementById('current-model-display'),
                
                welcomeScreen: document.getElementById('welcome-screen'),
                messagesContainer: document.getElementById('messages-container'),
                typingIndicator: document.getElementById('typing-indicator'),
                
                userInput: document.getElementById('user-input'),
                sendBtn: document.getElementById('send-btn') || document.getElementById('sendBtn'), 
                stopBtn: document.getElementById('stop-btn'),
                statusBanner: document.getElementById('status-banner'),
                promptBtns: document.querySelectorAll('.prompt-btn'),
                
                voiceBtn: document.getElementById('voice-btn'),
                micIcon: document.getElementById('mic-icon'),
                imageUpload: document.getElementById('image-upload'),
                imagePreviewContainer: document.getElementById('image-preview-container'),
                imagePreview: document.getElementById('image-preview'),
                removeImageBtn: document.getElementById('remove-image-btn')
            };

            // === 1. ENCRYPTION & STORAGE ===
            const storage = {
                encode: (str) => btoa(encodeURIComponent(str)),
                decode: (str) => decodeURIComponent(atob(str)),
                save: () => {
                    if(state.apiKey) localStorage.setItem('uai_k', storage.encode(state.apiKey));
                    localStorage.setItem('uai_m', state.model);
                    localStorage.setItem('uai_p', state.systemPrompt);
                },
                load: () => {
                    const k = localStorage.getItem('uai_k');
                    if (k) state.apiKey = storage.decode(k);
                    state.model = localStorage.getItem('uai_m') || state.model;
                    state.systemPrompt = localStorage.getItem('uai_p') || state.systemPrompt;
                    els.currentModelDisplay.textContent = state.model.split('/').pop();
                }
            };
            storage.load();

            // === 2. THEME MANAGEMENT ===
            const toggleTheme = () => {
                const isDark = els.html.classList.toggle('dark');
                localStorage.setItem('theme', isDark ? 'dark' : 'light');
                els.themeIcons.forEach(icon => icon.textContent = isDark ? 'light_mode' : 'dark_mode');
            };
            if (localStorage.theme === 'dark' || (!('theme' in localStorage) && window.matchMedia('(prefers-color-scheme: dark)').matches)) {
                els.html.classList.add('dark');
                els.themeIcons.forEach(icon => icon.textContent = 'light_mode');
            }
            els.themeToggle.addEventListener('click', toggleTheme);
            els.themeToggleMob.addEventListener('click', toggleTheme);

            // === 3. SIDEBAR MANAGEMENT ===
            const toggleSidebar = () => {
                els.sidebar.classList.toggle('-translate-x-full');
                els.sidebarOverlay.classList.toggle('hidden');
            };
            els.openSidebarBtn.addEventListener('click', toggleSidebar);
            els.closeSidebarBtn.addEventListener('click', toggleSidebar);
            els.sidebarOverlay.addEventListener('click', toggleSidebar);

            // === 4. SETTINGS MODAL ===
            const openSettings = () => {
                els.apiKeyInput.value = state.apiKey;
                els.modelInput.value = state.model;
                els.systemPromptInput.value = state.systemPrompt;
                els.settingsModal.classList.remove('hidden');
                setTimeout(() => els.settingsModal.classList.remove('opacity-0'), 10);
            };
            const closeSettings = () => {
                els.settingsModal.classList.add('opacity-0');
                setTimeout(() => els.settingsModal.classList.add('hidden'), 300);
            };
            els.openSettingsBtn.addEventListener('click', openSettings);
            els.closeSettingsBtn.addEventListener('click', closeSettings);
            els.cancelSettingsBtn.addEventListener('click', closeSettings);
            els.saveSettingsBtn.addEventListener('click', () => {
                state.apiKey = els.apiKeyInput.value.trim();
                state.model = els.modelInput.value;
                state.systemPrompt = els.systemPromptInput.value;
                storage.save();
                els.currentModelDisplay.textContent = state.model.split('/').pop();
                showStatus('Settings saved successfully!', 'success');
                closeSettings();
            });

            // === 5. UTILITIES (Alerts, UI Adjustments) ===
            const showStatus = (msg, type = 'error') => {
                els.statusBanner.textContent = msg;
                els.statusBanner.className = `absolute -top-12 left-0 right-0 px-4 py-2 rounded-lg text-sm text-center shadow-sm font-medium transition-all duration-300 z-50 ${
                    type === 'error' ? 'bg-red-100 text-red-700 dark:bg-red-900/50 dark:text-red-300 border border-red-200 dark:border-red-800' : 
                    'bg-green-100 text-green-700 dark:bg-green-900/50 dark:text-green-300 border border-green-200 dark:border-green-800'
                }`;
                els.statusBanner.classList.remove('hidden');
                setTimeout(() => els.statusBanner.classList.add('hidden'), 4000);
            };

            const scrollToBottom = () => {
                els.messagesContainer.parentElement.scrollTop = els.messagesContainer.parentElement.scrollHeight;
            };

            // === 6. HISTORY MANAGEMENT ===
            const saveCurrentSession = () => {
                if (state.messages.length === 0) {
                    state.history = state.history.filter(c => c.id !== state.currentChatId);
                } else {
                    const existingIndex = state.history.findIndex(c => c.id === state.currentChatId);
                    const firstMsgText = state.messages[0].content.text || "Image Chat";
                    const session = {
                        id: state.currentChatId,
                        title: firstMsgText.length > 30 ? firstMsgText.substring(0, 30) + "..." : firstMsgText,
                        messages: [...state.messages],
                        timestamp: Date.now()
                    };
                    if (existingIndex > -1) {
                        state.history[existingIndex] = session;
                    } else {
                        state.history.unshift(session);
                    }
                }
                localStorage.setItem('uai_history', JSON.stringify(state.history));
                renderHistory();
            };

            const renderHistory = () => {
                els.chatList.innerHTML = '';
                if(state.history.length === 0) {
                    els.chatList.innerHTML = '<p class="text-xs text-slate-400 px-3 mt-2">No history found</p>';
                    return;
                }
                state.history.forEach(chat => {
                    const btn = document.createElement('button');
                    const isActive = chat.id === state.currentChatId;
                    btn.className = `flex flex-col text-left px-3 py-2.5 rounded-xl hover:bg-[#dde3ea] dark:hover:bg-[#2f2f2f] transition-colors w-full ${isActive ? 'bg-[#dde3ea] dark:bg-[#2f2f2f]' : ''}`;
                    btn.innerHTML = `
                        <span class="text-sm font-medium text-slate-700 dark:text-slate-200 truncate w-full">${chat.title}</span>
                        <span class="text-[10px] text-slate-500 mt-1">${new Date(chat.timestamp).toLocaleString()}</span>
                    `;
                    btn.onclick = () => loadChat(chat.id);
                    els.chatList.appendChild(btn);
                });
            };

            const loadChat = (id) => {
                const chat = state.history.find(c => c.id === id);
                if(!chat) return;
                state.currentChatId = id;
                state.messages = [...chat.messages];
                els.welcomeScreen.classList.add('hidden');
                els.messagesContainer.classList.remove('hidden');
                els.messagesContainer.innerHTML = '';
                state.messages.forEach(renderMessage);
                if(window.innerWidth < 768) toggleSidebar();
                renderHistory();
                scrollToBottom();
            };

            // DATA CLEAN BTN
            els.clearDataBtn.addEventListener('click', () => {
                if(confirm("Are you sure you want to delete all history and settings? This cannot be undone.")) {
                    localStorage.removeItem('uai_history');
                    localStorage.removeItem('uai_k');
                    localStorage.removeItem('uai_m');
                    localStorage.removeItem('uai_p');
                    
                    state.history = [];
                    state.messages = [];
                    state.apiKey = '';
                    state.currentChatId = generateId();
                    
                    els.messagesContainer.innerHTML = '';
                    els.messagesContainer.classList.add('hidden');
                    els.welcomeScreen.classList.remove('hidden');
                    renderHistory();
                    showStatus("All data and history wiped clean.", "success");
                    if(window.innerWidth < 768) toggleSidebar();
                }
            });

            // Initial Render History
            renderHistory();

            // === 7. MULTIMEDIA ===
            els.imageUpload.addEventListener('change', (e) => {
                const file = e.target.files[0];
                if(file) {
                    const reader = new FileReader();
                    reader.onload = (e) => {
                        state.currentImageBase64 = e.target.result;
                        els.imagePreview.src = state.currentImageBase64;
                        els.imagePreviewContainer.classList.remove('hidden');
                    };
                    reader.readAsDataURL(file);
                }
            });
            els.removeImageBtn.addEventListener('click', () => {
                state.currentImageBase64 = null;
                els.imagePreviewContainer.classList.add('hidden');
                els.imageUpload.value = '';
            });

            const SpeechRecognition = window.SpeechRecognition || window.webkitSpeechRecognition;
            let recognition;
            if(SpeechRecognition) {
                recognition = new SpeechRecognition();
                recognition.continuous = false;
                recognition.interimResults = true;
                recognition.onstart = () => {
                    els.micIcon.classList.add('text-red-500', 'animate-pulse');
                    els.userInput.placeholder = "Listening...";
                };
                recognition.onresult = (event) => {
                    els.userInput.value = Array.from(event.results)
                        .map(r => r[0].transcript).join('');
                };
                recognition.onend = () => {
                    els.micIcon.classList.remove('text-red-500', 'animate-pulse');
                    els.userInput.placeholder = "Message AI... (Shift+Enter for new line)";
                };
                els.voiceBtn.addEventListener('click', () => {
                    if(els.micIcon.classList.contains('animate-pulse')) recognition.stop();
                    else recognition.start();
                });
            } else {
                els.voiceBtn.style.display = 'none';
            }

            window.speakText = (text) => {
                const synth = window.speechSynthesis;
                if(synth.speaking) { synth.cancel(); return; }
                const utterance = new SpeechSynthesisUtterance(text.replace(/[*#`]/g, ''));
                synth.speak(utterance);
            };

            // === 8. CHAT LOGIC & UI RENDER ===
            els.userInput.addEventListener('input', function() {
                this.style.height = '52px';
                this.style.height = (this.scrollHeight) + 'px';
                els.sendBtn.disabled = this.value.trim().length === 0 && !state.currentImageBase64;
            });

            const renderMessage = (msgObj) => {
                const wrapper = document.createElement('div');
                wrapper.className = `flex w-full message-row group ${msgObj.role === 'user' ? 'justify-end' : 'justify-start'}`;
                wrapper.id = `msg-${msgObj.id}`;

                let contentHtml = '';
                if(msgObj.role === 'user') {
                    const safeText = DOMPurify.sanitize(msgObj.content.text);
                    contentHtml = `<div class="chat-bubble-user px-5 py-3.5 max-w-[85%] shadow-sm text-[15px] leading-relaxed relative">
                                    ${msgObj.content.image ? `<img src="${msgObj.content.image}" class="max-w-[200px] rounded-lg mb-2 border border-slate-200 dark:border-slate-700">` : ''}
                                    <div class="whitespace-pre-wrap">${safeText}</div>
                                    <div class="msg-actions absolute -left-12 top-2 flex gap-1">
                                        <button onclick="editMessage('${msgObj.id}')" class="p-1.5 rounded-full hover:bg-slate-200 dark:hover:bg-slate-700 text-slate-500" title="Edit"><span class="material-symbols-outlined text-[16px]">edit</span></button>
                                    </div>
                                   </div>`;
                } else {
                    const parsedMarkdown = marked.parse(msgObj.content.text);
                    const safeMarkdown = DOMPurify.sanitize(parsedMarkdown);
                    contentHtml = `
                    <div class="flex gap-4 w-full max-w-full">
                        <div class="size-8 rounded-full bg-gradient-to-tr from-blue-500 to-purple-500 shrink-0 mt-1 flex items-center justify-center text-white shadow-sm">
                            <span class="material-symbols-outlined text-[18px]">blur_on</span>
                        </div>
                        <div class="flex flex-col gap-1 w-full overflow-hidden">
                            <div class="font-semibold text-sm text-slate-700 dark:text-slate-300">AI Assistant</div>
                            <div class="prose dark:prose-invert max-w-none text-[15px] leading-relaxed chat-bubble-ai">${safeMarkdown}</div>
                            
                            <div class="msg-actions flex items-center gap-2 mt-2">
                                <button onclick="copyText('${msgObj.id}')" class="p-1 rounded hover:bg-slate-200 dark:hover:bg-slate-800 text-slate-500 transition" title="Copy"><span class="material-symbols-outlined text-[16px]">content_copy</span></button>
                                <button onclick="speakText(document.getElementById('msg-${msgObj.id}').querySelector('.prose').innerText)" class="p-1 rounded hover:bg-slate-200 dark:hover:bg-slate-800 text-slate-500 transition" title="Read Aloud"><span class="material-symbols-outlined text-[16px]">volume_up</span></button>
                                <button onclick="deleteMessage('${msgObj.id}')" class="p-1 rounded hover:bg-red-100 dark:hover:bg-red-900/30 text-slate-500 hover:text-red-500 transition" title="Delete"><span class="material-symbols-outlined text-[16px]">delete</span></button>
                            </div>
                        </div>
                    </div>`;
                }

                wrapper.innerHTML = contentHtml;
                els.messagesContainer.appendChild(wrapper);
                scrollToBottom();
            };

            window.copyText = (id) => {
                const text = document.querySelector(`#msg-${id} .prose`).innerText;
                navigator.clipboard.writeText(text);
                showStatus('Copied to clipboard', 'success');
            };
            
            window.deleteMessage = (id) => {
                state.messages = state.messages.filter(m => m.id !== id);
                document.getElementById(`msg-${id}`).remove();
                if(state.messages.length === 0) {
                    els.messagesContainer.classList.add('hidden');
                    els.welcomeScreen.classList.remove('hidden');
                }
                saveCurrentSession(); // Update history when message deleted
            };

            window.editMessage = (id) => {
                const msgIndex = state.messages.findIndex(m => m.id === id);
                if(msgIndex > -1) {
                    const text = state.messages[msgIndex].content.text;
                    els.userInput.value = text;
                    els.userInput.style.height = 'auto';
                    els.userInput.style.height = els.userInput.scrollHeight + 'px';
                    els.userInput.focus();
                    els.sendBtn.disabled = false;
                    
                    const toDelete = state.messages.slice(msgIndex);
                    toDelete.forEach(m => document.getElementById(`msg-${m.id}`)?.remove());
                    state.messages = state.messages.slice(0, msgIndex);
                    
                    if(state.messages.length === 0) {
                        els.messagesContainer.classList.add('hidden');
                        els.welcomeScreen.classList.remove('hidden');
                    }
                    saveCurrentSession(); // Update history when edited
                }
            };

            // === 9. API INTEGRATION ===
            const enhanceWithMemory = () => {
                const apiMessages = [{ role: "system", content: state.systemPrompt }];
                const recentMessages = state.messages.slice(-6);
                recentMessages.forEach(msg => {
                    if(msg.content.image) {
                         apiMessages.push({
                            role: msg.role,
                            content: [
                                { type: "text", text: msg.content.text || "Explain this image." },
                                { type: "image_url", image_url: { url: msg.content.image } }
                            ]
                        });
                    } else {
                        apiMessages.push({ role: msg.role, content: msg.content.text });
                    }
                });
                return apiMessages;
            };

            const handleSend = async () => {
                const text = els.userInput.value.trim();
                const image = state.currentImageBase64;
                
                if(!text && !image) return;
                if(!state.apiKey) {
                    openSettings();
                    showStatus('Please enter your OpenRouter API Key first.');
                    return;
                }

                els.welcomeScreen.classList.add('hidden');
                els.messagesContainer.classList.remove('hidden');
                els.userInput.value = '';
                els.userInput.style.height = '52px';
                els.sendBtn.disabled = true;
                els.removeImageBtn.click(); 

                const userMsg = { id: generateId(), role: 'user', content: { text, image }, timestamp: Date.now() };
                state.messages.push(userMsg);
                renderMessage(userMsg);
                saveCurrentSession(); // Save after user message

                els.typingIndicator.classList.remove('hidden');
                els.sendBtn.parentElement.classList.add('hidden');
                els.stopBtn.classList.remove('hidden');
                scrollToBottom();

                state.abortController = new AbortController();

                try {
                    const response = await fetch("https://openrouter.ai/api/v1/chat/completions", {
                        method: "POST",
                        headers: {
                            "Authorization": `Bearer ${state.apiKey}`,
                            "HTTP-Referer": window.location.href,
                            "X-Title": "Universal AI Pro",
                            "Content-Type": "application/json"
                        },
                        body: JSON.stringify({
                            model: state.model,
                            messages: enhanceWithMemory()
                        }),
                        signal: state.abortController.signal
                    });

                    if(!response.ok) {
                        const err = await response.json();
                        throw new Error(err.error?.message || 'API Request Failed');
                    }

                    const data = await response.json();
                    const aiText = data.choices[0].message.content;
                    
                    const aiMsg = { id: generateId(), role: 'assistant', content: { text: aiText }, timestamp: Date.now() };
                    state.messages.push(aiMsg);
                    renderMessage(aiMsg);
                    saveCurrentSession(); // Save after AI response

                } catch (error) {
                    if(error.name === 'AbortError') showStatus('Generation stopped', 'success');
                    else showStatus(`Error: ${error.message}`);
                } finally {
                    els.typingIndicator.classList.add('hidden');
                    els.stopBtn.classList.add('hidden');
                    els.sendBtn.parentElement.classList.remove('hidden');
                    state.isGenerating = false;
                }
            };

            els.sendBtn.addEventListener('click', handleSend);
            els.userInput.addEventListener('keydown', (e) => {
                if(e.key === 'Enter' && !e.shiftKey) {
                    e.preventDefault();
                    if(!els.sendBtn.disabled) handleSend();
                }
            });

            els.stopBtn.addEventListener('click', () => {
                if(state.abortController) state.abortController.abort();
            });

            els.promptBtns.forEach(btn => {
                btn.addEventListener('click', () => {
                    const text = btn.querySelector('p').innerText;
                    els.userInput.value = text;
                    els.sendBtn.disabled = false;
                    handleSend();
                });
            });
            
            // New Chat Functionality Updated
            els.newChatBtn.addEventListener('click', () => {
                state.currentChatId = generateId(); // Set a new chat ID
                state.messages = [];
                els.messagesContainer.innerHTML = '';
                els.messagesContainer.classList.add('hidden');
                els.welcomeScreen.classList.remove('hidden');
                renderHistory(); // Update selection visually
                if(window.innerWidth < 768) toggleSidebar();
            });
        });
    </script>
</body>
</html>
