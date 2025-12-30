<!DOCTYPE html>
<html lang="pt-pt">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Informa | Painel de Demandas</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://unpkg.com/lucide@latest"></script>
    <script type="text/javascript" src="https://cdn.jsdelivr.net/npm/@emailjs/browser@3/dist/email.min.js"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700;800;900&display=swap');
        
        :root {
            --brand-primary: #4f46e5;
            --brand-secondary: #0f172a;
        }

        body { 
            font-family: 'Inter', sans-serif;
            -webkit-font-smoothing: antialiased;
        }

        .glass-effect {
            background: rgba(255, 255, 255, 0.8);
            backdrop-filter: blur(20px);
            border: 1px solid rgba(255, 255, 255, 0.3);
        }

        .custom-card {
            border-radius: 2.5rem;
            transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
        }

        .custom-card:hover {
            transform: translateY(-4px);
            box-shadow: 0 20px 25px -5px rgb(0 0 0 / 0.05), 0 8px 10px -6px rgb(0 0 0 / 0.05);
        }

        .modal-backdrop {
            backdrop-filter: blur(8px);
            background-color: rgba(15, 23, 42, 0.6);
        }

        input, select, textarea {
            transition: all 0.2s ease;
        }

        input:focus, select:focus, textarea:focus {
            transform: scale(1.01);
        }

        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(10px); }
            to { opacity: 1; transform: translateY(0); }
        }
        .animate-fade { animation: fadeIn 0.4s ease-out forwards; }
        
        .custom-scrollbar::-webkit-scrollbar { width: 6px; }
        .custom-scrollbar::-webkit-scrollbar-track { background: transparent; }
        .custom-scrollbar::-webkit-scrollbar-thumb { background: #e2e8f0; border-radius: 10px; }
    </style>
</head>
<body class="bg-[#f8fafc] text-slate-900 min-h-screen">

    <!-- ECRÃ DE LOGIN -->
    <div id="login-screen" class="fixed inset-0 z-[100] bg-slate-950 flex flex-col items-center justify-center p-4">
        <div class="animate-fade bg-slate-900 border border-slate-800 w-full max-w-md rounded-[3rem] p-12 shadow-2xl">
            <div class="flex flex-col items-center mb-10 text-center">
                <div class="bg-indigo-600 p-5 rounded-[2rem] mb-6 shadow-xl shadow-indigo-500/20">
                    <i data-lucide="shield-check" class="text-white w-10 h-10"></i>
                </div>
                <h1 class="text-3xl font-black uppercase text-white tracking-tighter">Informa</h1>
                <p class="text-slate-500 text-xs font-bold uppercase tracking-[0.3em] mt-2">Acesso Restrito</p>
            </div>
            
            <form id="login-form" class="space-y-5">
                <div class="space-y-1">
                    <label class="text-[10px] font-black text-slate-500 uppercase ml-4">Utilizador</label>
                    <input type="text" id="login-user" required placeholder="NOME DE ACESSO" 
                        class="w-full bg-slate-800 border border-slate-700 rounded-2xl px-6 py-4 outline-none focus:ring-2 focus:ring-indigo-500 font-bold uppercase text-white placeholder:text-slate-600">
                </div>
                <div class="space-y-1">
                    <label class="text-[10px] font-black text-slate-500 uppercase ml-4">Palavra-Passe</label>
                    <input type="password" id="login-pass" required placeholder="••••••••" 
                        class="w-full bg-slate-800 border border-slate-700 rounded-2xl px-6 py-4 outline-none focus:ring-2 focus:ring-indigo-500 font-bold text-white placeholder:text-slate-600">
                </div>
                <div id="login-error" class="hidden p-4 bg-red-500/10 text-red-400 text-[10px] font-black rounded-2xl text-center border border-red-500/20 uppercase tracking-widest">
                    Credenciais Inválidas
                </div>
                <button type="submit" id="login-submit-btn" class="w-full bg-indigo-600 hover:bg-indigo-500 text-white font-black py-5 rounded-[1.5rem] transition-all shadow-lg uppercase text-xs tracking-widest mt-4 disabled:opacity-50 disabled:cursor-wait">
                    Entrar no Painel
                </button>
            </form>
        </div>
        <p class="mt-12 text-slate-600 font-bold text-[10px] uppercase tracking-[0.4em]">© 2025 CLX Corporation</p>
    </div>

    <!-- DASHBOARD PRINCIPAL -->
    <div id="app-screen" class="hidden flex-col min-h-screen">
        <header class="glass-effect sticky top-0 z-30 border-b border-slate-200/50">
            <div class="max-w-7xl mx-auto px-6 h-24 flex items-center justify-between">
                <div class="flex items-center gap-4">
                    <div class="bg-indigo-600 p-3 rounded-2xl text-white shadow-lg shadow-indigo-100">
                        <i data-lucide="layers" class="w-6 h-6"></i>
                    </div>
                    <div>
                        <h1 class="font-black text-xl uppercase tracking-tighter text-slate-900">Informa</h1>
                        <p class="text-[10px] font-bold text-slate-400 uppercase tracking-widest">Gestão de Fluxo</p>
                    </div>
                </div>

                <div class="flex items-center gap-6">
                    <div class="text-right hidden md:block">
                        <p id="user-info-name" class="text-sm font-black text-slate-900 uppercase"></p>
                        <p id="user-info-role" class="text-[9px] font-bold text-indigo-500 uppercase tracking-widest"></p>
                    </div>
                    
                    <div class="flex items-center gap-2 bg-slate-100 p-1.5 rounded-[1.5rem]">
                        <button id="btn-admin-panel" class="hidden p-3 hover:bg-white text-slate-600 rounded-xl transition-all hover:shadow-sm">
                            <i data-lucide="settings-2" class="w-5 h-5"></i>
                        </button>
                        <button onclick="handleLogout()" class="p-3 hover:bg-red-500 hover:text-white text-slate-600 rounded-xl transition-all hover:shadow-sm">
                            <i data-lucide="power" class="w-5 h-5"></i>
                        </button>
                    </div>
                </div>
            </div>
        </header>

        <main class="max-w-7xl mx-auto px-6 py-12 w-full flex-grow">
            <div class="flex flex-col md:flex-row justify-between items-end mb-12 gap-8">
                <div class="animate-fade">
                    <h2 id="view-title" class="text-5xl font-[900] text-slate-900 tracking-tight uppercase leading-none">Painel Global</h2>
                    <div class="flex items-center gap-2 mt-4">
                        <span id="view-subtitle" class="bg-indigo-100 text-indigo-700 px-4 py-1.5 rounded-full text-[10px] font-black uppercase tracking-widest">Geral</span>
                        <span class="w-2 h-2 rounded-full bg-slate-300"></span>
                        <p class="text-slate-400 text-[10px] font-bold uppercase tracking-widest">Atualizado agora</p>
                    </div>
                </div>
                
                <button id="btn-new-task" class="hidden bg-slate-900 hover:bg-indigo-600 text-white pl-8 pr-10 py-5 rounded-[2rem] font-black flex items-center gap-4 shadow-2xl transition-all hover:scale-105 active:scale-95 text-[10px] uppercase tracking-[0.2em]">
                    <i data-lucide="plus-circle" class="w-5 h-5"></i> Abrir Demanda
                </button>
            </div>

            <div id="tasks-container" class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-8">
                <div class="col-span-full py-20 text-center opacity-20">
                    <i data-lucide="loader-2" class="w-12 h-12 animate-spin mx-auto mb-4"></i>
                    <p class="font-black uppercase tracking-widest text-xs">A carregar dados...</p>
                </div>
            </div>
        </main>
    </div>

    <!-- MODAL DE NOVA DEMANDA -->
    <div id="modal-task" class="hidden fixed inset-0 z-50 flex items-center justify-center p-6 modal-backdrop">
        <div class="bg-white rounded-[4rem] w-full max-w-2xl p-16 shadow-2xl relative animate-fade">
            <div class="flex justify-between items-start mb-12">
                <div>
                    <h2 class="text-3xl font-black text-slate-900 uppercase tracking-tighter">Criar Demanda</h2>
                    <p class="text-slate-400 text-xs font-bold uppercase tracking-widest mt-1">O colaborador receberá um email</p>
                </div>
                <button onclick="closeModals()" class="bg-slate-100 p-4 rounded-full text-slate-400 hover:bg-red-50 hover:text-red-500 transition-all">
                    <i data-lucide="x" class="w-6 h-6"></i>
                </button>
            </div>

            <form id="task-form" class="space-y-8">
                <div class="space-y-2">
                    <label class="text-[10px] font-black text-slate-400 uppercase ml-2">Título do Pedido</label>
                    <input type="text" id="task-title" required placeholder="O QUE PRECISA DE SER FEITO?" 
                        class="w-full p-6 border-2 border-slate-100 bg-slate-50 rounded-[2rem] font-bold uppercase outline-none focus:border-indigo-500 focus:bg-white">
                </div>

                <div class="space-y-2">
                    <label class="text-[10px] font-black text-slate-400 uppercase ml-2">Detalhes e Instruções</label>
                    <textarea id="task-desc" placeholder="DESCREVA OS DETALHES AQUI..." 
                        class="w-full p-8 border-2 border-slate-100 bg-slate-50 rounded-[2rem] h-40 resize-none outline-none focus:border-indigo-500 focus:bg-white"></textarea>
                </div>

                <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
                    <div class="space-y-2">
                        <label class="text-[10px] font-black text-slate-400 uppercase ml-2">Setor</label>
                        <select id="task-category" class="w-full p-6 border-2 border-slate-100 bg-slate-50 rounded-[2rem] font-black text-xs uppercase appearance-none focus:border-indigo-500 cursor-pointer"></select>
                    </div>
                    <div class="space-y-2">
                        <label class="text-[10px] font-black text-slate-400 uppercase ml-2">Responsável</label>
                        <select id="task-assigned" required class="w-full p-6 border-2 border-slate-100 bg-slate-50 rounded-[2rem] font-black text-xs uppercase appearance-none focus:border-indigo-500 cursor-pointer">
                            <option value="">SELECIONAR COLABORADOR</option>
                        </select>
                    </div>
                </div>

                <div class="pt-10 flex gap-4">
                    <button type="submit" id="task-submit-btn" class="flex-1 bg-indigo-600 hover:bg-indigo-700 text-white font-black py-7 rounded-[2.5rem] uppercase text-xs tracking-[0.3em] shadow-xl shadow-indigo-100 transition-all disabled:opacity-50">
                        Enviar Notificação
                    </button>
                </div>
            </form>
        </div>
    </div>

    <!-- MODAL DE ADMINISTRAÇÃO -->
    <div id="modal-admin" class="hidden fixed inset-0 z-50 flex items-center justify-center p-4 modal-backdrop">
        <div class="bg-white rounded-[4rem] w-full max-w-6xl shadow-2xl overflow-hidden flex flex-col max-h-[90vh] animate-fade">
            <div class="flex justify-between items-center p-12 bg-slate-50 border-b border-slate-100">
                <div>
                    <h2 class="text-4xl font-black uppercase tracking-tighter">Gestão Master</h2>
                    <p class="text-slate-400 text-[10px] font-bold uppercase tracking-[0.4em] mt-2">Configurações de Acesso e Emails</p>
                </div>
                <button onclick="closeModals()" class="bg-white p-5 rounded-full shadow-sm hover:bg-red-500 hover:text-white transition-all">
                    <i data-lucide="x" class="w-8 h-8"></i>
                </button>
            </div>
            
            <div class="p-12 overflow-y-auto flex-1 custom-scrollbar">
                <div class="grid grid-cols-1 lg:grid-cols-3 gap-12">
                    <div class="lg:col-span-1 space-y-8">
                        <div class="bg-slate-50 p-10 rounded-[3rem]">
                            <h3 class="font-black uppercase text-xs tracking-widest mb-8 flex items-center gap-2">
                                <i data-lucide="user-plus" class="w-4 h-4"></i> Novo Utilizador
                            </h3>
                            <form id="admin-user-form" class="space-y-4">
                                <input type="text" id="new-user-login" required placeholder="NOME" class="w-full p-5 bg-white border-2 border-slate-100 rounded-2xl font-bold uppercase text-xs">
                                <input type="text" id="new-user-pass" required placeholder="PALAVRA-PASSE" class="w-full p-5 bg-white border-2 border-slate-100 rounded-2xl font-bold text-xs">
                                <input type="email" id="new-user-email" required placeholder="EMAIL@EXEMPLO.COM" class="w-full p-5 bg-white border-2 border-slate-100 rounded-2xl font-bold text-xs">
                                <select id="new-user-category" class="w-full p-5 bg-white border-2 border-slate-100 rounded-2xl font-bold text-[10px] uppercase"></select>
                                <select id="new-user-role" class="w-full p-5 bg-white border-2 border-slate-100 rounded-2xl font-bold text-[10px] uppercase"></select>
                                <button type="submit" class="w-full bg-slate-900 text-white p-5 rounded-2xl font-black uppercase text-[10px] tracking-widest mt-4">Registar</button>
                            </form>
                        </div>
                    </div>

                    <div class="lg:col-span-2 space-y-6">
                        <h3 class="font-black uppercase text-xs tracking-widest mb-2 flex items-center gap-2">
                            <i data-lucide="users" class="w-4 h-4"></i> Utilizadores Ativos
                        </h3>
                        <div id="admin-users-list" class="grid grid-cols-1 md:grid-cols-2 gap-4"></div>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/11.1.0/firebase-app.js";
        import { 
            getFirestore, collection, query, onSnapshot, addDoc, updateDoc, 
            deleteDoc, doc, serverTimestamp, setDoc, getDoc 
        } from "https://www.gstatic.com/firebasejs/11.1.0/firebase-firestore.js";
        import { getAuth, signInAnonymously, onAuthStateChanged } from "https://www.gstatic.com/firebasejs/11.1.0/firebase-auth.js";

        // CONFIGURAÇÕES DO EMAILJS
        const EMAILJS_PUBLIC_KEY = "5Qjda_NSpbRtPpBmp";
        const EMAILJS_SERVICE_ID = "service_g4zor8g"; // ATUALIZADO
        const EMAILJS_TEMPLATE_ID = "template_dc2l6vd";

        emailjs.init(EMAILJS_PUBLIC_KEY);

        const firebaseConfig = {
            apiKey: "AIzaSyD29RmGmXbB8tcuZXN608eKjlXhr0LKBn0",
            authDomain: "gestor-do-informa.firebaseapp.com",
            projectId: "gestor-do-informa",
            storageBucket: "gestor-do-informa.firebasestorage.app",
            messagingSenderId: "2215345954",
            appId: "1:2215345954:web:3f201a2dda85b15100ef7b"
        };

        const app = initializeApp(firebaseConfig);
        const auth = getAuth(app);
        const db = getFirestore(app);
        const appId = "gestor-informa-demandas";

        const CATEGORIES = ["Meio Ambiente", "Linguagens", "Comunicações", "Edição de Vídeo", "Cultura", "Secretaria", "Esportes", "Presidência", "Informações", "Designer"];
        const ROLES = ["Colaborador", "Estagiário", "Diretor"];
        const MASTER_CONFIG = { login: "CLX", pass: "02072007" };

        let currentUser = null;
        let allTasks = [];
        let allUsers = [];
        let isAuthReady = false;

        // AUTH INITIALIZATION
        const initAuth = async () => {
            try {
                await signInAnonymously(auth);
            } catch (err) {
                console.warn("Auth Configuration Warning: Login anónimo ignorado.", err);
            }
        };

        onAuthStateChanged(auth, (user) => {
            isAuthReady = true;
        });

        window.addEventListener('load', () => { 
            initAuth();
            populateSelects(); 
            lucide.createIcons();
        });

        function populateSelects() {
            const catSelects = [document.getElementById('task-category'), document.getElementById('new-user-category')];
            const roleSelects = [document.getElementById('new-user-role')];
            catSelects.forEach(s => {
                s.innerHTML = '';
                CATEGORIES.forEach(c => s.innerHTML += `<option value="${c}">${c.toUpperCase()}</option>`);
            });
            roleSelects.forEach(s => {
                s.innerHTML = '';
                ROLES.forEach(r => s.innerHTML += `<option value="${r}">${r.toUpperCase()}</option>`);
            });
        }

        async function sendNotificationEmail(taskData) {
            const targetUser = allUsers.find(u => u.login === taskData.assignedTo);
            if (!targetUser || !targetUser.email) return;

            const templateParams = {
                to_email: targetUser.email,
                to_name: targetUser.login,
                from_name: currentUser.login,
                task_title: taskData.title,
                task_category: taskData.category,
                message: taskData.description || "Verifique o painel para mais detalhes."
            };

            try {
                await emailjs.send(EMAILJS_SERVICE_ID, EMAILJS_TEMPLATE_ID, templateParams);
                console.log("Notificação enviada com sucesso!");
            } catch (err) {
                console.error("Falha técnica no envio do email (EmailJS):", err);
                // A demanda foi salva no banco, não vamos alarmar o usuário.
            }
        }

        document.getElementById('login-form').onsubmit = async (e) => {
            e.preventDefault();
            const loginInput = document.getElementById('login-user').value.trim();
            const passInput = document.getElementById('login-pass').value;
            const submitBtn = document.getElementById('login-submit-btn');

            submitBtn.disabled = true;

            try {
                if (loginInput.toUpperCase() === MASTER_CONFIG.login.toUpperCase() && passInput === MASTER_CONFIG.pass) {
                    loginSuccess({ login: MASTER_CONFIG.login.toUpperCase(), category: 'MASTER', role: 'Administrador', isMaster: true });
                    return;
                }

                const userRef = doc(db, 'artifacts', appId, 'public', 'data', 'users', loginInput.toLowerCase());
                const userDoc = await getDoc(userRef);
                
                if (userDoc.exists() && userDoc.data().pass === passInput) {
                    loginSuccess({ ...userDoc.data(), isMaster: false });
                } else {
                    showLoginError();
                }
            } catch (err) {
                console.error("Login Error:", err);
                showLoginError();
            } finally {
                submitBtn.disabled = false;
            }
        };

        function showLoginError() {
            const errorEl = document.getElementById('login-error');
            errorEl.classList.remove('hidden');
            setTimeout(() => errorEl.classList.add('hidden'), 3000);
        }

        function loginSuccess(userData) {
            currentUser = userData;
            document.getElementById('login-screen').classList.add('hidden');
            document.getElementById('app-screen').classList.replace('hidden', 'flex');
            
            document.getElementById('user-info-role').textContent = `${userData.role} • ${userData.category}`;
            document.getElementById('user-info-name').textContent = userData.login;
            
            if (userData.isMaster || userData.category === 'Presidência' || userData.role === 'Diretor') {
                document.getElementById('btn-new-task').classList.remove('hidden');
            }
            if (userData.isMaster) {
                document.getElementById('btn-admin-panel').classList.remove('hidden');
            }
            
            startListeners();
        }

        function startListeners() {
            onSnapshot(collection(db, 'artifacts', appId, 'public', 'data', 'tasks'), (snap) => {
                allTasks = snap.docs.map(d => ({ id: d.id, ...d.data() }))
                    .sort((a, b) => (b.createdAt?.seconds || 0) - (a.createdAt?.seconds || 0));
                renderTasks();
            }, (err) => console.error("Snapshot Task Error:", err));

            onSnapshot(collection(db, 'artifacts', appId, 'public', 'data', 'users'), (snap) => {
                allUsers = snap.docs.map(d => ({ id: d.id, ...d.data() }));
                renderAdminUsers();
                updateTaskAssignedSelect();
            }, (err) => console.error("Snapshot User Error:", err));
        }

        function renderTasks() {
            const container = document.getElementById('tasks-container');
            if (allTasks.length === 0) {
                container.innerHTML = `<div class="col-span-full py-20 text-center text-slate-300 font-bold uppercase tracking-widest text-xs">Nenhuma demanda encontrada</div>`;
                return;
            }

            container.innerHTML = allTasks.map(t => `
                <div class="animate-fade bg-white custom-card p-10 border border-slate-100 shadow-sm relative overflow-hidden">
                    <div class="absolute top-0 right-0 p-6">
                        <span class="text-[9px] font-black bg-slate-100 text-slate-500 px-4 py-2 rounded-full uppercase tracking-tighter">
                            ${t.category}
                        </span>
                    </div>
                    
                    <div class="mb-8">
                        <h3 class="font-black text-xl uppercase tracking-tight text-slate-900 mb-3 leading-tight ${t.status === 'concluída' ? 'line-through text-slate-300' : ''}">
                            ${t.title}
                        </h3>
                        <p class="text-slate-500 text-sm leading-relaxed line-clamp-3">${t.description || 'Sem descrição detalhada.'}</p>
                    </div>

                    <div class="flex items-center justify-between pt-8 border-t border-slate-50">
                        <div class="flex items-center gap-3">
                            <div class="w-8 h-8 rounded-full bg-indigo-600 flex items-center justify-center text-[10px] text-white font-black">
                                ${t.assignedTo ? t.assignedTo.substring(0,2).toUpperCase() : '??'}
                            </div>
                            <span class="text-[10px] font-black uppercase tracking-widest text-slate-400">${t.assignedTo || 'Pendente'}</span>
                        </div>
                        
                        <button onclick="toggleStatus('${t.id}', '${t.status}')" 
                            class="p-4 rounded-2xl transition-all ${t.status === 'concluída' ? 'bg-emerald-500 text-white' : 'bg-slate-50 text-slate-300 hover:bg-emerald-50 hover:text-emerald-500'}">
                            <i data-lucide="${t.status === 'concluída' ? 'check-circle-2' : 'circle'}" class="w-6 h-6"></i>
                        </button>
                    </div>
                </div>
            `).join('');
            lucide.createIcons();
        }

        document.getElementById('task-form').onsubmit = async (e) => {
            e.preventDefault();
            const submitBtn = document.getElementById('task-submit-btn');
            submitBtn.disabled = true;

            const data = {
                title: document.getElementById('task-title').value.trim(),
                description: document.getElementById('task-desc').value.trim(),
                category: document.getElementById('task-category').value,
                assignedTo: document.getElementById('task-assigned').value
            };
            
            try {
                // SALVAR NO FIREBASE PRIMEIRO (MUITO IMPORTANTE)
                await addDoc(collection(db, 'artifacts', appId, 'public', 'data', 'tasks'), { 
                    ...data, 
                    status: 'pendente', 
                    createdAt: serverTimestamp()
                });
                
                // DEPOIS TENTAR O EMAIL (PODE FALHAR SEM QUEBRAR O SISTEMA)
                await sendNotificationEmail(data);
                
                closeModals();
            } catch (err) {
                console.error("Error creating task:", err);
            } finally {
                submitBtn.disabled = false;
            }
        };

        document.getElementById('admin-user-form').onsubmit = async (e) => {
            e.preventDefault();
            const login = document.getElementById('new-user-login').value.trim();
            try {
                await setDoc(doc(db, 'artifacts', appId, 'public', 'data', 'users', login.toLowerCase()), { 
                    login: login, 
                    pass: document.getElementById('new-user-pass').value.trim(),
                    email: document.getElementById('new-user-email').value.trim(),
                    category: document.getElementById('new-user-category').value, 
                    role: document.getElementById('new-user-role').value 
                });
                document.getElementById('admin-user-form').reset();
            } catch (err) {
                console.error("Error creating user:", err);
            }
        };

        function renderAdminUsers() {
            document.getElementById('admin-users-list').innerHTML = allUsers
                .filter(u => u.login.toUpperCase() !== MASTER_CONFIG.login.toUpperCase())
                .map(u => `
                    <div class="p-6 border-2 border-slate-50 rounded-[2rem] bg-white flex justify-between items-center group">
                        <div class="flex items-center gap-4">
                            <div class="bg-slate-100 p-3 rounded-xl text-slate-400 group-hover:bg-indigo-50 group-hover:text-indigo-600 transition-all">
                                <i data-lucide="user" class="w-5 h-5"></i>
                            </div>
                            <div>
                                <p class="font-black text-xs uppercase text-slate-900">${u.login}</p>
                                <p class="text-[9px] text-slate-400 font-bold uppercase tracking-widest">${u.email || 'SEM EMAIL'}</p>
                            </div>
                        </div>
                        <button onclick="delUser('${u.id}')" class="text-slate-200 hover:text-red-500 p-2 transition-all">
                            <i data-lucide="trash-2" class="w-5 h-5"></i>
                        </button>
                    </div>`).join('');
            lucide.createIcons();
        }

        window.toggleStatus = async (id, s) => { 
            await updateDoc(doc(db, 'artifacts', appId, 'public', 'data', 'tasks', id), { 
                status: s === 'concluída' ? 'pendente' : 'concluída' 
            }); 
        };
        
        window.delUser = async (id) => { 
            if(confirm("Deseja mesmo remover este utilizador?")) {
                await deleteDoc(doc(db, 'artifacts', appId, 'public', 'data', 'users', id)); 
            }
        };
        
        window.closeModals = () => {
            document.querySelectorAll('.modal-backdrop').forEach(m => m.classList.add('hidden'));
        };

        window.handleLogout = () => location.reload();

        function updateTaskAssignedSelect() {
            const select = document.getElementById('task-assigned');
            const currentVal = select.value;
            select.innerHTML = '<option value="">SELECIONAR COLABORADOR</option>';
            allUsers.forEach(u => {
                select.innerHTML += `<option value="${u.login}">${u.login.toUpperCase()}</option>`;
            });
            select.value = currentVal;
        }

        document.getElementById('btn-new-task').onclick = () => { 
            document.getElementById('task-form').reset(); 
            document.getElementById('modal-task').classList.remove('hidden'); 
        };
        
        document.getElementById('btn-admin-panel').onclick = () => {
            document.getElementById('modal-admin').classList.remove('hidden'); 
        };

    </script>
</body>
</html>
