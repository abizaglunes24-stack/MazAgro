# MazAgro
Agronomía y control de parcelas
import React, { useState, useEffect } from 'react';
import { 
  Leaf, 
  Droplets, 
  Sun, 
  Plus, 
  ArrowLeft, 
  Camera, 
  Info,
  Thermometer,
  WifiOff,
  Map as MapIcon,
  BarChart3,
  FileText,
  Activity,
  Layers,
  ArrowUpRight,
  FlaskConical,
  Database,
  MapPin,
  Wind,
  Navigation,
  Mountain,
  Save,
  CheckCircle2,
  TrendingUp,
  ShieldCheck,
  Zap,
  Globe,
  PieChart,
  BookOpen,
  Microscope,
  Search,
  Dna,
  Grape,
  Bug,
  ChevronRight,
  ClipboardList
} from 'lucide-react';

// --- LOGO PERSONALIZADO MAZAGRO (M-Leaf Design) ---
const MazAgroLogo = ({ size = 24, className = "" }) => (
  <svg 
    width={size} 
    height={size} 
    viewBox="0 0 24 24" 
    fill="none" 
    xmlns="http://www.w3.org/2000/svg"
    className={className}
  >
    <path 
      d="M12 2L4.5 20.29C4.21 21.02 4.75 21.82 5.53 21.82H18.47C19.25 21.82 19.79 21.02 19.5 20.29L12 2Z" 
      fill="currentColor" 
      opacity="0.2"
    />
    <path 
      d="M12 2L3 21H7L12 11L17 21H21L12 2Z" 
      fill="currentColor" 
    />
    <path 
      d="M11 13L8 18H11L10 22L13 17H10L11 13Z" 
      fill="#fbbf24"
    />
  </svg>
);

// --- BASE DE DATOS TÉCNICA, CIENTÍFICA Y MERCADO 2025 ---
const MAZAGRO_KNOWLEDGE = {
  crops: {
    "Espárrago": {
      variety: "UC-157 F1",
      stats: { ph: "6.5 - 7.5", irrigation: "Goteo 4L/h", density: "22,000 pl/ha" },
      soilDefaults: { level: "0.5% (Nivelado)", type: "Franco Arenoso" },
      panorama2025: { ranking: "3° Mundial", mainState: "Sonora (58%)", volume: "358k Ton", trend: "Creciente" },
      biocontrol: "Control de Prodiplosis mediante liberación de Trichogramma (SADER/FAO).",
      physiology: "Anoxia radicular crítica en corona y demanda de N en helecho (Taiz & Zeiger).",
      image: "https://images.unsplash.com/photo-1515471209610-dae1c92d8777?auto=format&fit=crop&q=80&w=400"
    },
    "Uva (Vid)": {
      variety: "Red Globe / Cabernet",
      stats: { ph: "5.5 - 7.0", irrigation: "Riego Deficitario Controlado", density: "3,333 pl/ha" },
      soilDefaults: { level: "2.5% Pendiente", type: "Franco Pedregoso" },
      panorama2025: { ranking: "Top 20 Mundial", mainState: "Sonora (75%)", volume: "380k Ton", trend: "Estable" },
      biocontrol: "Técnica del Insecto Estéril (IAEA) contra Mosca de la Fruta.",
      physiology: "Manejo de canopia (VSP) para síntesis de antocianos (Keller/Reynier/Mullins).",
      image: "https://images.unsplash.com/photo-1533435328003-d7480d7a1282?auto=format&fit=crop&q=80&w=400"
    },
    "Pimiento": {
      variety: "Híbrido Indeterminado",
      stats: { ph: "6.0 - 6.8", irrigation: "Pulsos cortos", density: "30,000 pl/ha" },
      soilDefaults: { level: "1.5% Pendiente", type: "Franco Arcilloso" },
      panorama2025: { ranking: "2° Mundial", mainState: "Sinaloa (34%)", volume: "3.1M Ton", trend: "Creciente" },
      biocontrol: "Orius insidiosus para control de trips (SADER).",
      physiology: "Translocación de Ca++ vía xilema para prevenir necrosis apical (Taiz).",
      image: "https://images.unsplash.com/photo-1592841200221-a6898f307bac?auto=format&fit=crop&q=80&w=400"
    }
  },
  academy: [
    { source: "Taiz & Zeiger (2023)", title: "Fisiología Vegetal", concept: "Señalización por ABA y transporte por floema." },
    { source: "Keller (2020)", title: "Science of Grapevines", concept: "Interacción raíz-suelo y dinámica de la canopia." },
    { source: "SADER/SIAP", title: "Panorama 2025", concept: "Ranking de producción nacional y exportaciones." },
    { source: "FAO / IAEA", title: "Manejo Integrado", concept: "Control biológico clásico y Técnica del Insecto Estéril." }
  ]
};

const App = () => {
  // --- PERSISTENCIA DE DATOS CON LOCAL STORAGE ---
  const [lots, setLots] = useState(() => {
    const saved = localStorage.getItem('mazagro_v2_data');
    if (saved) return JSON.parse(saved);
    return [
      { 
        id: 1, name: 'Lote Norte A1', crop: 'Espárrago', area: '2.5 ha', health: 96,
        location: 'Sector Quebrada Alta', coords: '-13.06, -76.35', 
        ...MAZAGRO_KNOWLEDGE.crops["Espárrago"],
        soil: MAZAGRO_KNOWLEDGE.crops["Espárrago"].soilDefaults,
        applications: [
          { date: '2026-02-10', type: 'Bioestimulante', product: 'AminoVigor', dose: '2 L/ha', objective: 'Estrés Térmico' }
        ],
        timeline: [{ date: '2026-02-14', event: 'Sincronización inicial MazAgro' }]
      }
    ];
  });

  useEffect(() => {
    localStorage.setItem('mazagro_v2_data', JSON.stringify(lots));
  }, [lots]);

  // --- ESTADOS DE LA UI ---
  const [view, setView] = useState('dashboard');
  const [selectedLot, setSelectedLot] = useState(null);
  const [activeTab, setActiveTab] = useState('tecnico');
  const [isOffline, setIsOffline] = useState(!navigator.onLine);
  const [weather] = useState({ temp: 24.8, hum: 45, wind: 10, loc: "Fundo Central" });

  const [newLot, setNewLot] = useState({ name: '', crop: 'Espárrago', area: '', location: '' });
  const [newApp, setNewApp] = useState({ type: 'Químico', product: '', dose: '', objective: '' });

  useEffect(() => {
    const handleSync = () => setIsOffline(!navigator.onLine);
    window.addEventListener('online', handleSync);
    window.addEventListener('offline', handleSync);
    return () => { window.removeEventListener('online', handleSync); window.removeEventListener('offline', handleSync); };
  }, []);

  // --- HANDLERS ---
  const saveLot = () => {
    if (!newLot.name) return;
    const info = MAZAGRO_KNOWLEDGE.crops[newLot.crop];
    const lot = { 
      id: Date.now(), ...newLot, health: 100, ...info, 
      soil: info.soilDefaults, coords: '-13.XXX, -76.XXX', 
      applications: [], timeline: [{ date: new Date().toISOString().split('T')[0], event: 'Alta de parcela' }]
    };
    setLots([lot, ...lots]);
    setView('dashboard');
    setNewLot({ name: '', crop: 'Espárrago', area: '', location: '' });
  };

  const saveApp = () => {
    const updated = lots.map(l => l.id === selectedLot.id ? {
      ...l, applications: [{ id: Date.now(), date: new Date().toISOString().split('T')[0], ...newApp }, ...l.applications]
    } : l);
    setLots(updated);
    setSelectedLot(updated.find(l => l.id === selectedLot.id));
    setView('lotDetail');
    setNewApp({ type: 'Químico', product: '', dose: '', objective: '' });
  };

  return (
    <div className="min-h-screen bg-slate-50 font-sans text-slate-900 max-w-lg mx-auto shadow-2xl relative pb-24 overflow-x-hidden">
      
      {/* Indicador de Estado */}
      <div className={`text-white text-[9px] py-1.5 px-4 flex justify-between font-black uppercase tracking-widest sticky top-0 z-50 transition-colors ${isOffline ? 'bg-slate-800' : 'bg-emerald-600'}`}>
        <div className="flex items-center gap-2"><Globe size={10} /> MazAgro Pro | Martínez Azpeitia Systems</div>
        {isOffline && <div className="flex items-center gap-1"><WifiOff size={10} /> Offline Mode</div>}
      </div>

      {/* Header */}
      <header className="bg-slate-900 text-white p-7 rounded-b-[3rem] shadow-xl border-b-4 border-emerald-500/20">
        <div className="flex justify-between items-center">
          {view === 'dashboard' ? (
            <div className="flex items-center gap-3">
              <div className="bg-emerald-500 p-2.5 rounded-2xl rotate-3 shadow-lg shadow-emerald-500/30">
                <MazAgroLogo size={28} className="text-white" />
              </div>
              <h1 className="text-2xl font-black italic tracking-tighter uppercase leading-none">
                MazAgro<span className="text-emerald-500 block text-[8px] not-italic tracking-[0.4em] font-bold">AGRICULTURA DE PRECISIÓN</span>
              </h1>
            </div>
          ) : (
            <button onClick={() => setView(view === 'lotDetail' ? 'dashboard' : 'lotDetail')} className="p-2 bg-white/10 rounded-full">
              <ArrowLeft size={24} />
            </button>
          )}
          <button onClick={() => setView('library')} className="bg-slate-800 p-3 rounded-2xl border border-slate-700">
            <BookOpen size={20} className="text-emerald-400" />
          </button>
        </div>
      </header>

      <main className="p-4">
        {/* --- DASHBOARD --- */}
        {view === 'dashboard' && (
          <div className="space-y-6 animate-in fade-in duration-500">
            <div className="bg-slate-900 rounded-[2.5rem] p-6 text-white shadow-2xl relative overflow-hidden border border-white/5">
              <div className="absolute -right-6 top-[-20px] opacity-10"><Sun size={140} /></div>
              <div className="relative z-10 flex justify-between items-center">
                <div>
                  <p className="text-emerald-400 text-[10px] font-black uppercase tracking-widest mb-1 flex items-center gap-1"><MapPin size={10}/> {weather.loc}</p>
                  <div className="flex items-baseline gap-1">
                    <span className="text-4xl font-black">{weather.temp}°</span>
                    <span className="text-xl font-bold opacity-60">C</span>
                  </div>
                </div>
                <div className="text-right border-l border-white/10 pl-6">
                  <p className="text-[9px] opacity-40 font-black mb-1 uppercase tracking-widest leading-none">Status</p>
                  <p className="text-sm font-black text-emerald-400 flex items-center justify-end gap-1">ÓPTIMO <TrendingUp size={14}/></p>
                </div>
              </div>
            </div>

            <div className="flex justify-between items-center px-2">
              <h2 className="font-black text-slate-800 flex items-center gap-2 uppercase text-[11px] tracking-widest">
                <Layers size={18} className="text-emerald-600" /> Mis Parcelas
              </h2>
              <button onClick={() => setView('addLot')} className="bg-emerald-600 text-white p-2.5 rounded-2xl"><Plus size={20} /></button>
            </div>

            <div className="space-y-4">
              {lots.map(lot => (
                <div key={lot.id} onClick={() => { setSelectedLot(lot); setView('lotDetail'); }}
                  className="bg-white rounded-[2.5rem] p-5 shadow-sm border border-slate-100 flex items-center gap-5 active:scale-95 cursor-pointer">
                  <div className="w-16 h-16 bg-slate-50 rounded-2xl overflow-hidden shrink-0 flex items-center justify-center">
                    {lot.crop === 'Uva (Vid)' ? <Grape className="text-purple-600" size={32} /> : <img src={lot.image} className="w-full h-full object-cover" />}
                  </div>
                  <div className="flex-1 min-w-0">
                    <h3 className="font-black text-slate-800 truncate mb-1">{lot.name}</h3>
                    <div className="flex items-center gap-2">
                      <span className="text-[9px] bg-slate-900 text-white px-2 py-0.5 rounded font-black uppercase tracking-widest">{lot.crop}</span>
                    </div>
                  </div>
                  <ArrowUpRight size={20} className="text-slate-300" />
                </div>
              ))}
            </div>
          </div>
        )}

        {/* --- DETALLE DE LOTE --- */}
        {view === 'lotDetail' && selectedLot && (
          <div className="animate-in fade-in slide-in-from-right-10 duration-500 pb-10">
            <div className="bg-white p-8 rounded-[3rem] shadow-sm border border-slate-100 mb-6">
              <h2 className="text-3xl font-black text-slate-900 leading-none mb-4">{selectedLot.name}</h2>
              <div className="flex items-center gap-2">
                <div className="bg-slate-900 text-white text-[9px] px-3 py-1.5 rounded-full font-black uppercase tracking-widest">
                  <Navigation size={10} className="inline mr-1" /> {selectedLot.coords}
                </div>
                <div className="bg-emerald-50 text-emerald-700 text-[9px] px-3 py-1.5 rounded-full font-black uppercase">
                  {selectedLot.status}
                </div>
              </div>
            </div>

            {/* Tabs */}
            <div className="flex gap-2 mb-6 overflow-x-auto no-scrollbar px-1">
              {[
                { id: 'tecnico', label: 'Técnico', icon: Mountain },
                { id: 'biocontrol', label: 'Sanidad', icon: Bug },
                { id: 'panorama', label: 'SIAP 2025', icon: Globe }
              ].map(tab => (
                <button key={tab.id} onClick={() => setActiveTab(tab.id)}
                  className={`flex-1 flex items-center justify-center gap-2 py-4 rounded-2xl text-[10px] font-black uppercase transition-all shadow-sm border ${
                    activeTab === tab.id ? 'bg-slate-900 text-white' : 'bg-white text-slate-400 border-slate-100'
                  }`}>
                  <tab.icon size={14} /> {tab.label}
                </button>
              ))}
            </div>

            {activeTab === 'tecnico' && (
              <div className="space-y-4 animate-in zoom-in-95">
                <div className="grid grid-cols-2 gap-4">
                  <div className="bg-white p-6 rounded-[2.5rem] border border-slate-100 flex flex-col items-center shadow-sm">
                    <TrendingUp className="text-blue-500 mb-2" size={24}/>
                    <p className="text-[9px] font-black text-slate-400 uppercase tracking-widest">Pendiente</p>
                    <p className="text-sm font-black">{selectedLot.soil.level}</p>
                  </div>
                  <div className="bg-white p-6 rounded-[2.5rem] border border-slate-100 flex flex-col items-center shadow-sm">
                    <Mountain className="text-amber-600 mb-2" size={24}/>
                    <p className="text-[9px] font-black text-slate-400 uppercase tracking-widest">Terreno</p>
                    <p className="text-sm font-black text-center">{selectedLot.soil.type}</p>
                  </div>
                </div>
                <div className="bg-slate-900 text-white p-8 rounded-[3rem] shadow-xl">
                  <h4 className="text-emerald-400 font-black text-[10px] uppercase mb-4 tracking-[0.2em]">Fisiología Académica</h4>
                  <p className="text-xs italic leading-relaxed opacity-90 border-l-2 border-emerald-500 pl-4">
                    "{selectedLot.physiology}"
                  </p>
                </div>
              </div>
            )}

            {activeTab === 'biocontrol' && (
              <div className="space-y-4 animate-in zoom-in-95">
                <div className="bg-emerald-900 text-white p-8 rounded-[3rem] shadow-xl relative overflow-hidden">
                  <h3 className="text-[10px] font-black uppercase tracking-[0.3em] text-emerald-400 mb-6 flex items-center gap-2">
                    <ShieldCheck size={18} /> Protocolo Sanitario
                  </h3>
                  <p className="text-sm leading-relaxed mb-8 italic opacity-95">"{selectedLot.biocontrol}"</p>
                  <button onClick={() => setView('addApp')} className="w-full bg-white text-emerald-900 py-5 rounded-3xl font-black text-xs uppercase shadow-xl">
                    Registrar Aplicación
                  </button>
                </div>
                <div className="space-y-3">
                  {selectedLot.applications.map((app, i) => (
                    <div key={i} className="bg-white p-5 rounded-[2rem] border border-slate-100 shadow-sm flex justify-between items-center relative overflow-hidden">
                      <div className={`absolute left-0 top-0 bottom-0 w-2 ${app.type === 'Químico' ? 'bg-rose-500' : 'bg-emerald-500'}`} />
                      <div className="pl-4">
                        <p className="text-[9px] font-black text-slate-400 uppercase mb-1">{app.date} • {app.type}</p>
                        <h4 className="font-black text-slate-800 text-lg leading-tight">{app.product}</h4>
                        <p className="text-xs text-slate-500">Objetivo: {app.objective}</p>
                      </div>
                      <div className="bg-slate-50 px-4 py-3 rounded-2xl text-sm font-black">{app.dose}</div>
                    </div>
                  ))}
                </div>
              </div>
            )}
            
            {/* Otras pestañas conservan la misma lógica estructural... */}
          </div>
        )}

        {/* --- BIBLIOTECA --- */}
        {view === 'library' && (
          <div className="animate-in slide-in-from-bottom-10 duration-500 pb-10 px-2">
            <h2 className="text-2xl font-black text-slate-900 mb-8 italic uppercase tracking-tighter flex items-center gap-3">
              <Microscope size={24} className="text-emerald-600" /> Biblioteca MazAgro
            </h2>
            <div className="space-y-6">
              {MAZAGRO_DB.academy.map((item, idx) => (
                <div key={idx} className="bg-white p-8 rounded-[3rem] shadow-sm border border-slate-100">
                  <span className="bg-slate-900 text-white text-[8px] px-3 py-1 rounded-full font-black uppercase mb-3 block w-fit">{item.source}</span>
                  <h3 className="font-black text-slate-800 text-lg mb-2 leading-tight">{item.title}</h3>
                  <p className="text-xs text-slate-500 leading-relaxed italic opacity-80">"{item.concept}"</p>
                </div>
              ))}
            </div>
          </div>
        )}

        {/* --- FORMULARIOS --- */}
        {view === 'addLot' && (
          <div className="animate-in slide-in-from-bottom-10 duration-500 pb-10 px-2">
            <h2 className="text-2xl font-black text-slate-900 mb-8 font-serif italic uppercase tracking-tighter">Alta de Parcela</h2>
            <div className="bg-white p-8 rounded-[3rem] border border-slate-100 space-y-6 shadow-sm">
              <input type="text" placeholder="Nombre de Parcela" className="w-full bg-slate-50 border-2 border-slate-50 rounded-2xl py-5 px-6 text-sm font-bold focus:border-emerald-500 outline-none" value={newLot.name} onChange={e => setNewLot({...newLot, name: e.target.value})} />
              <div className="grid grid-cols-2 gap-4">
                <select className="w-full bg-slate-50 border-2 border-slate-50 rounded-2xl py-5 px-4 text-sm font-bold" value={newLot.crop} onChange={e => setNewLot({...newLot, crop: e.target.value})}>
                  {Object.keys(MAZAGRO_DB.crops).map(c => <option key={c} value={c}>{c}</option>)}
                </select>
                <input type="text" placeholder="Superficie (ha)" className="w-full bg-slate-50 border-2 border-slate-50 rounded-2xl py-5 px-6 text-sm font-bold outline-none" value={newLot.area} onChange={e => setNewLot({...newLot, area: e.target.value})} />
              </div>
              <button onClick={handleSaveLot} className="w-full bg-emerald-600 text-white py-6 rounded-[2.5rem] font-black text-lg shadow-xl active:scale-95 transition-all">REGISTRAR EN MAZAGRO</button>
            </div>
          </div>
        )}

        {view === 'addApp' && (
          <div className="animate-in slide-in-from-bottom-10 duration-500 pb-10 px-2">
            <h2 className="text-2xl font-black text-slate-900 mb-8">Nuevo Registro</h2>
            <div className="bg-white p-8 rounded-[3rem] border border-slate-100 shadow-sm space-y-8">
              <div className="grid grid-cols-2 gap-2">
                {['Químico', 'Bioestimulante', 'Fitohormona', 'Control Biológico'].map(t => (
                  <button key={t} onClick={() => setNewApp({...newApp, type: t})} className={`py-4 rounded-2xl text-[10px] font-black border transition-all ${newApp.type === t ? 'bg-slate-900 text-white border-slate-900' : 'bg-slate-50 text-slate-500'}`}>{t}</button>
                ))}
              </div>
              <input type="text" placeholder="Producto" className="w-full bg-slate-50 border-2 border-slate-50 rounded-2xl py-5 px-6 text-sm font-bold focus:border-emerald-500 outline-none" value={newApp.product} onChange={e => setNewApp({...newApp, product: e.target.value})} />
              <input type="text" placeholder="Dosis" className="w-full bg-slate-50 border-2 border-slate-50 rounded-2xl py-5 px-6 text-sm font-bold outline-none" value={newApp.dose} onChange={e => setNewApp({...newApp, dose: e.target.value})} />
              <input type="text" placeholder="Objetivo" className="w-full bg-slate-50 border-2 border-slate-50 rounded-2xl py-5 px-6 text-sm font-bold outline-none" value={newApp.objective} onChange={e => setNewApp({...newApp, objective: e.target.value})} />
              <button onClick={handleSaveApp} className="w-full bg-emerald-600 text-white py-6 rounded-[2.5rem] font-black text-lg shadow-xl active:scale-95 transition-all">GUARDAR REGISTRO</button>
            </div>
          </div>
        )}
      </main>

      {/* Navegación Inferior */}
      <nav className="fixed bottom-0 left-0 right-0 bg-white/95 backdrop-blur-md border-t border-slate-200 p-6 flex justify-around items-center z-40 max-w-lg mx-auto">
        <button onClick={() => setView('dashboard')} className={view === 'dashboard' ? 'text-emerald-600 scale-110' : 'text-slate-300'}><MapIcon size={26} /></button>
        <button onClick={() => setView('addLot')} className="bg-emerald-600 text-white p-5 rounded-3xl -mt-16 shadow-2xl shadow-emerald-500/30 border-4 border-slate-50 active:scale-90 transition-all">
          <MazAgroLogo size={32} />
        </button>
        <button onClick={() => setView('library')} className={view === 'library' ? 'text-emerald-600 scale-110' : 'text-slate-300'}><BookOpen size={26} /></button>
      </nav>
    </div>
  );
};

export default App;
