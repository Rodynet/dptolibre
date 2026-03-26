import React, { useState, useEffect } from 'react';
import { 
  ShieldCheck, Shield, TrendingUp, Award, Building2, Building, 
  Users, FileText, Wallet, Gift, Bell, Plus, Search, 
  LayoutDashboard, Settings, CheckCircle2, Clock, AlertCircle, 
  MoreVertical, QrCode, Download, Eye, X, ChevronRight, 
  ArrowRight, ArrowLeft, Camera, Video, Zap, MessageSquare, 
  Gavel, Share2, MousePointer2, Landmark, Droplets, Flame,
  CreditCard, Banknote, UserCheck, Star, Sparkles, Filter, 
  History, Info, Fingerprint, PieChart, BadgePercent, Maximize2
} from 'lucide-react';

// --- COMPONENTES AUXILIARES ---

const StatusBadge = ({ status, type = "admin" }) => {
  const styles = {
    "Listo para Procesar": "bg-emerald-100 text-emerald-700 border-emerald-200",
    "Validando Legajo": "bg-amber-50 text-amber-600 border-amber-200",
    "Alquilada": "bg-emerald-100 text-emerald-700",
    "Vacante": "bg-slate-100 text-slate-500",
    "En Firma": "bg-indigo-100 text-indigo-700",
    "Pendiente": "bg-amber-100 text-amber-700",
    "Pagado": "bg-emerald-100 text-emerald-700"
  };
  return (
    <span className={`text-[9px] font-black uppercase px-2 py-1 rounded-full border ${styles[status] || "bg-slate-100 text-slate-500"}`}>
      {status}
    </span>
  );
};

// --- MOCK DATA GLOBAL ---

const MOCK_DATA = {
  admin: {
    health: 94,
    escrow: 12540800,
    fees: 840500,
    attention: [
      { id: 1, type: 'veredicto', title: 'Legajo Verificado: Juan Pérez', desc: 'IA confirma solvencia 3 a 1. Inquilino y Garantes firmaron.', action: 'DAR OK FINAL' },
      { id: 2, type: 'ticket', title: 'Dueño no autoriza: Caseros 1230', desc: 'Presupuesto de $15.000 pendiente hace 48hs.', action: 'PUSH URGENCIA' },
      { id: 3, type: 'inventario', title: 'Inventario Pendiente: Belgrano 880', desc: 'El inquilino cargó 10 fotos. Requiere tu veredicto.', action: 'REVISAR' }
    ]
  },
  tenant: {
    name: "Juan Pérez",
    score: 820,
    miles: 4500,
    bank: "Banco Galicia",
    bankBalance: 150000,
    blindedBalance: 405430,
    nextRent: 450000,
    address: "Caseros 1230, 4B"
  },
  owner: {
    name: "Roberto Daer",
    netBalance: 760400,
    autoYield: 12450,
    properties: [
      { id: 1, address: "Caseros 1230, 4B", status: "Alquilada", tenant: "Juan Pérez", score: 820 }
    ]
  }
};

// --- APP PRINCIPAL ---

export default function BlindatoApp() {
  const [profile, setProfile] = useState('Admin'); // 'Admin', 'Inquilino', 'Propietario'
  const [view, setView] = useState('Home');
  const [showWizard, setShowWizard] = useState(null); // 'blinding', 'inventory', 'payment', 'verdict'
  const [wizardStep, setWizardStep] = useState(1);

  // --- RENDERIZADO DE PERFILES ---

  const renderAdminWeb = () => (
    <div className="flex h-screen bg-slate-50 font-sans text-slate-900 overflow-hidden w-full">
      {/* Sidebar Admin */}
      <aside className="w-72 bg-slate-900 text-white flex flex-col p-6 z-20 shadow-2xl">
        <div className="flex items-center gap-3 mb-10 px-2">
          <div className="w-10 h-10 bg-indigo-500 rounded-xl flex items-center justify-center text-white font-black text-xl">B</div>
          <div>
            <h1 className="font-black tracking-tighter text-xl leading-none">BLINDATO</h1>
            <p className="text-[10px] text-indigo-400 font-bold uppercase tracking-widest mt-1">Real Estate OS</p>
          </div>
        </div>
        <nav className="flex-1 space-y-2">
          {['Dashboard', 'Inquilinos', 'Dueños', 'Propiedades', 'Contratos', 'Tesorería', 'Tickets'].map(item => (
            <button 
              key={item} 
              onClick={() => setView(item)}
              className={`w-full flex items-center gap-3 p-4 rounded-xl font-bold transition-all ${view === item ? 'bg-indigo-600 text-white shadow-lg' : 'text-slate-400 hover:bg-slate-800'}`}
            >
              {item === 'Dashboard' && <LayoutDashboard size={20} />}
              {item === 'Inquilinos' && <Users size={20} />}
              {item === 'Dueños' && <ShieldCheck size={20} />}
              {item === 'Propiedades' && <Building2 size={20} />}
              {item === 'Contratos' && <FileText size={20} />}
              {item === 'Tesorería' && <Wallet size={20} />}
              {item === 'Tickets' && <MessageSquare size={20} />}
              <span>{item}</span>
            </button>
          ))}
        </nav>
        <div className="mt-auto p-4 bg-slate-800/50 rounded-2xl border border-slate-700">
           <div className="flex items-center gap-3">
              <img src="https://api.dicebear.com/7.x/avataaars/svg?seed=Admin" className="w-8 h-8 rounded-lg bg-slate-700" alt="Admin" />
              <div>
                <p className="text-xs font-bold">Inmobiliaria Raíces</p>
                <button onClick={() => setProfile('Inquilino')} className="text-[9px] text-indigo-400 font-black uppercase">Cambiar Perfil</button>
              </div>
           </div>
        </div>
      </aside>

      {/* Main Content Admin */}
      <main className="flex-1 flex flex-col overflow-hidden">
        <header className="bg-white border-b border-slate-100 p-6 flex justify-between items-center shadow-sm">
          <div className="flex items-center gap-4">
             <button className="bg-indigo-600 text-white px-6 py-3 rounded-2xl font-black text-sm flex items-center gap-2 hover:bg-indigo-700 transition-all shadow-lg shadow-indigo-100">
                <Plus size={20} /> NUEVA PROPIEDAD
             </button>
             <div className="h-8 w-[1px] bg-slate-100 mx-2"></div>
             <div className="flex items-center gap-2">
                <div className="w-3 h-3 rounded-full bg-emerald-500 animate-pulse"></div>
                <span className="text-xs font-black text-slate-400 uppercase tracking-widest">Torre de Control Activa</span>
             </div>
          </div>
          <div className="flex items-center gap-6">
            <div className="relative">
              <Search className="absolute left-3 top-3 text-slate-300" size={18} />
              <input type="text" placeholder="Buscador universal..." className="pl-10 pr-4 py-3 bg-slate-50 border-none rounded-2xl text-sm w-80" />
            </div>
            <Bell size={24} className="text-slate-400 cursor-pointer" />
          </div>
        </header>

        <div className="flex-1 overflow-y-auto p-8 space-y-8">
          {/* Dashboard Section */}
          <section className="grid grid-cols-4 gap-6">
            <div className="bg-white p-6 rounded-[2.5rem] border border-slate-100 shadow-sm flex flex-col items-center">
                <div className="relative w-24 h-24 mb-4">
                  <svg className="w-full h-full transform -rotate-90">
                    <circle cx="50%" cy="50%" r="45%" strokeWidth="8" fill="transparent" className="text-slate-100" stroke="currentColor" />
                    <circle cx="50%" cy="50%" r="45%" strokeWidth="8" fill="transparent" strokeDasharray="251" strokeDashoffset={251 - (251 * 92) / 100} className="text-emerald-500" stroke="currentColor" style={{ strokeLinecap: 'round' }} />
                  </svg>
                  <div className="absolute inset-0 flex items-center justify-center font-black text-xl">92%</div>
                </div>
                <p className="text-[10px] font-black uppercase text-slate-400">Salud de Cartera</p>
            </div>
            <div className="bg-slate-900 p-6 rounded-[2.5rem] text-white flex flex-col justify-between shadow-xl">
                <p className="text-[10px] font-black uppercase opacity-50">Recaudación Total</p>
                <h3 className="text-2xl font-black">$12.5M</h3>
                <p className="text-[10px] text-emerald-400 font-bold">+12.4k Intereses Hoy</p>
            </div>
            <div className="bg-white p-6 rounded-[2.5rem] border border-slate-100 shadow-sm flex flex-col justify-between">
                <p className="text-[10px] font-black uppercase text-slate-400">Honorarios Disponibles</p>
                <h3 className="text-2xl font-black text-indigo-600">$840.5k</h3>
                <button className="text-[9px] font-black bg-indigo-50 text-indigo-600 py-2 rounded-xl uppercase">Invertir Saldo</button>
            </div>
            <div className="bg-white p-6 rounded-[2.5rem] border border-slate-100 shadow-sm flex flex-col justify-between">
                <p className="text-[10px] font-black uppercase text-slate-400">Liquidaciones</p>
                <h3 className="text-2xl font-black">14 Dueños</h3>
                <button className="bg-slate-900 text-white py-2 rounded-xl text-[9px] font-black uppercase">Pagar Todo</button>
            </div>
          </section>

          {/* Torre de Control - Veredictos */}
          <section className="space-y-4">
            <h2 className="text-2xl font-black text-slate-800 uppercase tracking-tighter">Valla de Atención (Juez)</h2>
            <div className="grid grid-cols-1 gap-4">
              {MOCK_DATA.admin.attention.map(item => (
                <div key={item.id} className="bg-white p-6 rounded-[2rem] border-l-[10px] border-indigo-500 border-y border-r border-slate-100 flex items-center justify-between hover:shadow-md transition-all">
                  <div className="flex items-center gap-6">
                    <div className="w-12 h-12 bg-slate-50 rounded-2xl flex items-center justify-center text-indigo-600">
                      {item.type === 'veredicto' ? <Gavel size={24} /> : item.type === 'ticket' ? <MessageSquare size={24} /> : <Camera size={24} />}
                    </div>
                    <div>
                      <h4 className="font-black text-slate-800">{item.title}</h4>
                      <p className="text-sm text-slate-500">{item.desc}</p>
                    </div>
                  </div>
                  <button 
                    onClick={() => setShowWizard(item.type === 'veredicto' ? 'verdict' : 'inventory')}
                    className="bg-slate-900 text-white px-8 py-3 rounded-2xl font-black text-xs uppercase tracking-widest hover:bg-indigo-600 transition-all shadow-xl shadow-slate-200"
                  >
                    {item.action}
                  </button>
                </div>
              ))}
            </div>
          </section>
        </div>
      </main>
    </div>
  );

  const renderMobileFrame = (content) => (
    <div className="flex items-center justify-center min-h-screen bg-slate-200 p-10 font-sans">
      <div className="w-full max-w-[400px] h-[800px] bg-white rounded-[3rem] shadow-2xl overflow-hidden relative border-[8px] border-slate-900 flex flex-col">
        {/* Notch simulation */}
        <div className="absolute top-0 left-1/2 -translate-x-1/2 w-40 h-7 bg-slate-900 rounded-b-3xl z-[100] flex items-center justify-center gap-2">
            <div className="w-2 h-2 rounded-full bg-slate-800"></div>
            <div className="w-12 h-1 bg-slate-800 rounded-full"></div>
        </div>
        
        {/* Top Bar Fixa Mobile */}
        <header className="bg-white px-6 pt-10 pb-4 flex justify-between items-center border-b border-slate-50 z-50">
          <div onClick={() => setView('Profile')} className="flex items-center gap-3 cursor-pointer">
            <div className="w-10 h-10 rounded-full border-2 border-indigo-500 overflow-hidden shadow-sm">
              <img src={`https://api.dicebear.com/7.x/avataaars/svg?seed=${profile}`} alt="User" />
            </div>
            <div className="flex flex-col">
              <span className="text-xs font-black text-slate-800 leading-none">{MOCK_DATA.tenant.name}</span>
              <span className="text-[9px] font-bold text-indigo-600 uppercase mt-1">Inquilino Platinum</span>
            </div>
          </div>
          <div className="flex items-center gap-3">
             {profile === 'Inquilino' && <ShieldCheck className="text-emerald-500" size={24} />}
             <Bell size={20} className="text-slate-400" />
          </div>
        </header>

        {/* Dynamic Mobile Content */}
        <div className="flex-1 overflow-y-auto pb-24 bg-slate-50">
          {content}
        </div>

        {/* Bottom Nav Mobile */}
        <nav className="bg-white px-4 py-4 flex justify-around items-center border-t border-slate-100 z-50 shadow-[0_-10px_20px_rgba(0,0,0,0.02)]">
          {[
            { id: 'Home', icon: Home },
            { id: 'Admin', icon: FileText },
            { id: 'Wallet', icon: Wallet },
            { id: 'Millaje', icon: Gift }
          ].map(item => (
            <button 
              key={item.id} 
              onClick={() => setView(item.id)}
              className={`flex flex-col items-center gap-1 transition-all ${view === item.id ? 'text-indigo-600 scale-110' : 'text-slate-400'}`}
            >
              <item.icon size={22} strokeWidth={view === item.id ? 2.5 : 2} />
              <span className="text-[9px] font-black uppercase tracking-tighter">{item.id}</span>
            </button>
          ))}
        </nav>

        {/* Modals & Wizards */}
        {showWizard === 'blinding' && renderBlindingWizard()}
        {showWizard === 'inventory' && renderInventoryWizard()}
        {showWizard === 'payment' && renderPaymentFlow()}
        {showWizard === 'verdict' && renderVerdictDetail()}
      </div>
    </div>
  );

  const renderTenantHome = () => (
    <div className="p-5 space-y-6 animate-in fade-in duration-500">
      {/* 2.1 Score & Miles */}
      <div className="flex justify-around items-center bg-white p-6 rounded-[2.5rem] shadow-sm border border-slate-100">
         <div className="flex flex-col items-center text-center">
            <div className="w-16 h-16 rounded-full border-4 border-emerald-500 flex items-center justify-center font-black text-emerald-600">{MOCK_DATA.tenant.score}</div>
            <span className="text-[9px] font-black uppercase mt-2 text-slate-400 tracking-widest">Rental Score</span>
         </div>
         <div className="w-[1px] h-10 bg-slate-100"></div>
         <div className="flex flex-col items-center text-center">
            <div className="w-16 h-16 rounded-full border-4 border-amber-400 flex items-center justify-center font-black text-amber-600">{MOCK_DATA.tenant.miles}</div>
            <span className="text-[9px] font-black uppercase mt-2 text-slate-400 tracking-widest">Millas</span>
         </div>
      </div>

      {/* 2.2 Banner de Billetera */}
      <div className="bg-slate-900 rounded-[2.5rem] p-6 text-white shadow-xl relative overflow-hidden active:scale-95 transition-transform" onClick={() => setView('Wallet')}>
         <ShieldCheck size={100} className="absolute right-[-20px] bottom-[-20px] text-white/5" />
         <div className="flex justify-between items-start mb-6">
            <div>
               <p className="text-[10px] font-black uppercase text-indigo-400 tracking-widest">Saldo en Galicia</p>
               <h3 className="text-2xl font-black">${MOCK_DATA.tenant.bankBalance.toLocaleString()}</h3>
            </div>
            <button className="bg-indigo-600 p-2 rounded-xl"><Plus size={16} /></button>
         </div>
         <div className="bg-white/10 p-3 rounded-2xl flex items-center justify-between border border-white/5">
            <span className="text-[9px] font-black uppercase opacity-60">Alquiler Blindado</span>
            <span className="text-xs font-black text-emerald-400">+${MOCK_DATA.tenant.blindedBalance.toLocaleString()}</span>
         </div>
      </div>

      {/* 2.3 Tarjetas de Estado */}
      <div className="space-y-4">
         <div className="bg-white p-5 rounded-3xl border border-slate-100 flex justify-between items-center shadow-sm">
            <div>
               <span className="text-[9px] font-black bg-emerald-100 text-emerald-700 px-2 py-0.5 rounded-full uppercase">Faltan 4 días</span>
               <h4 className="font-black text-slate-800 mt-2 text-sm">{MOCK_DATA.tenant.address}</h4>
               <p className="text-xl font-black text-slate-600 mt-1">${MOCK_DATA.tenant.nextRent.toLocaleString()}</p>
            </div>
            <button onClick={() => setShowWizard('payment')} className="bg-slate-900 text-white text-[10px] font-black px-5 py-3 rounded-xl uppercase tracking-widest">Pagar</button>
         </div>

         <div className="bg-indigo-600 p-6 rounded-[2.5rem] text-white shadow-xl relative overflow-hidden" onClick={() => setShowWizard('blinding')}>
            <Zap className="absolute right-[-10px] top-[-10px] text-white/10" size={80} />
            <h4 className="text-[10px] font-black uppercase tracking-widest opacity-80 mb-4">Misión: Blindar Mayo</h4>
            <div className="flex justify-between text-[10px] font-black mb-2 uppercase">
                <span>Progreso de Resguardo</span>
                <span>45%</span>
            </div>
            <div className="h-3 bg-white/20 rounded-full overflow-hidden mb-4">
                <div className="h-full bg-emerald-400" style={{width: '45%'}}></div>
            </div>
            <button className="w-full bg-white text-indigo-600 font-black py-3 rounded-2xl text-[10px] uppercase tracking-widest">+ Blindar ahora</button>
         </div>

         <div className="bg-white p-5 rounded-3xl border border-slate-100 flex items-center gap-4">
            <div className="w-10 h-10 bg-slate-50 text-slate-400 rounded-xl flex items-center justify-center"><History size={20}/></div>
            <div>
               <h4 className="text-xs font-black text-slate-800 uppercase">Mayo / Junio 2026</h4>
               <p className="text-[10px] text-amber-500 font-bold uppercase">Sin blindaje futuro detectado</p>
            </div>
            <ChevronRight className="ml-auto text-slate-200" />
         </div>
      </div>
    </div>
  );

  const renderProfile = () => (
    <div className="flex-1 bg-white flex flex-col h-full animate-in slide-in-from-bottom duration-300 z-[200] absolute inset-0">
      <header className="p-8 flex justify-between items-start border-b border-slate-50">
        <div className="flex items-center gap-4">
          <div className="w-16 h-16 rounded-full border-2 border-indigo-500 p-1">
            <img src={`https://api.dicebear.com/7.x/avataaars/svg?seed=${profile}`} alt="User" className="w-full h-full rounded-full bg-slate-100" />
          </div>
          <div>
            <h1 className="text-xl font-black text-slate-800">{MOCK_DATA.tenant.name}</h1>
            <p className="text-xs text-slate-500 font-medium tracking-tight">ID: 20-30444555-9</p>
          </div>
        </div>
        <button onClick={() => setView('Home')} className="p-2 bg-slate-100 rounded-full text-slate-400">
          <X size={24} />
        </button>
      </header>
      <div className="flex-1 overflow-y-auto px-6 py-8 space-y-8">
        <section>
          <h3 className="text-[10px] font-black uppercase text-slate-400 tracking-widest mb-4 ml-1">Mis Roles Activos</h3>
          <div className="space-y-4">
            {['Inquilino', 'Propietario', 'Admin'].map((r) => (
              <div key={r} onClick={() => { setProfile(r); setView('Home'); }} className={`p-4 rounded-2xl border-2 transition-all cursor-pointer ${profile === r ? 'border-indigo-600 bg-indigo-50/50 shadow-sm' : 'border-slate-50 bg-white'}`}>
                <div className="flex justify-between items-center">
                  <div className="flex items-center gap-3">
                    {r === 'Inquilino' ? <Users size={18}/> : r === 'Propietario' ? <Shield size={18}/> : <Landmark size={18}/>}
                    <span className="font-black text-sm text-slate-800 uppercase tracking-tighter">{r}</span>
                  </div>
                  {profile === r && <span className="text-[9px] font-black bg-indigo-600 text-white px-2 py-0.5 rounded-full uppercase tracking-widest">Activo</span>}
                </div>
              </div>
            ))}
          </div>
        </section>
        <button className="w-full border-2 border-dashed border-slate-200 p-6 rounded-[2rem] flex flex-col items-center gap-2 text-slate-400">
          <PlusCircle size={28} />
          <span className="text-[10px] font-black uppercase tracking-widest">Agregar Propiedad / Rol</span>
        </button>
        <section className="space-y-3 pb-20">
          <h3 className="text-[10px] font-black uppercase text-slate-400 tracking-widest mb-4 ml-1">Configuración</h3>
          <button className="w-full flex items-center justify-between p-5 bg-slate-50 rounded-2xl border border-slate-100"><span className="text-xs font-black text-slate-700 uppercase tracking-tighter">Cambiar Contraseña</span><ChevronRight size={16} className="text-slate-300"/></button>
          <button className="w-full flex items-center justify-between p-5 bg-red-50 rounded-2xl border border-red-100"><span className="text-xs font-black text-red-600 uppercase tracking-tighter">Cerrar Sesión</span><ArrowRight size={16} className="text-red-300"/></button>
        </section>
      </div>
    </div>
  );

  // --- WIZARDS & OVERLAYS ---

  const renderBlindingWizard = () => (
    <div className="absolute inset-0 bg-slate-900 z-[200] flex flex-col animate-in slide-in-from-bottom duration-300 overflow-hidden">
        <header className="p-8 flex justify-between items-center text-white border-b border-white/5">
            <h3 className="text-[10px] font-black uppercase tracking-widest text-indigo-400">Motor de Blindaje</h3>
            <button onClick={() => setShowWizard(null)} className="p-2 bg-white/10 rounded-full"><X size={20}/></button>
        </header>
        <main className="flex-1 p-8 overflow-y-auto">
            {wizardStep === 1 ? (
                <div className="space-y-8 h-full flex flex-col justify-center animate-in fade-in">
                    <div className="text-center">
                        <p className="text-indigo-400 font-black text-[10px] uppercase tracking-widest mb-4">¿Cuánto quieres resguardar?</p>
                        <div className="text-5xl font-black text-white">$450.000</div>
                        <p className="text-white/40 text-xs mt-4">Monto total de tu próximo alquiler</p>
                    </div>
                    <div className="bg-white/5 border border-white/10 p-6 rounded-3xl flex items-center justify-between">
                        <div className="flex items-center gap-4">
                            <div className="w-10 h-10 bg-indigo-500/20 text-indigo-400 rounded-xl flex items-center justify-center"><Building size={20}/></div>
                            <div>
                                <p className="text-[9px] font-black text-white/50 uppercase">Desde tu banco</p>
                                <p className="text-sm font-bold text-white tracking-tight">Banco Galicia (*** 8901)</p>
                            </div>
                        </div>
                        <ChevronRight size={18} className="text-white/20" />
                    </div>
                    <button onClick={() => setWizardStep(2)} className="mt-auto bg-indigo-600 text-white font-black py-5 rounded-[2rem] text-lg active:scale-95 transition-transform shadow-2xl shadow-indigo-500/20">VER MIS BENEFICIOS <ArrowRight className="inline ml-2" size={20}/></button>
                </div>
            ) : (
                <div className="space-y-6 animate-in slide-in-from-right duration-300 h-full flex flex-col">
                    <div className="flex items-center gap-3">
                        <div className="p-2 bg-emerald-500 rounded-xl text-slate-900"><ShieldCheck size={24} /></div>
                        <h2 className="text-2xl font-black text-white uppercase tracking-tight">Tu Potenciador</h2>
                    </div>
                    <div className="space-y-4 flex-1">
                        <div className="bg-white/5 border border-white/10 rounded-3xl p-5 flex items-center gap-4">
                            <TrendingUp className="text-emerald-400" size={24}/>
                            <div>
                                <p className="text-[9px] font-black text-white/50 uppercase">Rendimiento Mensual</p>
                                <p className="text-lg font-black text-emerald-400">+$5.430,00</p>
                            </div>
                        </div>
                        <div className="grid grid-cols-2 gap-4">
                            <div className="bg-white/5 border border-white/10 rounded-3xl p-5">
                                <Award className="text-indigo-400 mb-2" size={24} />
                                <p className="text-[9px] font-black text-white/50 uppercase">Score</p>
                                <p className="text-lg font-black text-white">+25 pts</p>
                            </div>
                            <div className="bg-white/5 border border-white/10 rounded-3xl p-5">
                                <Sparkles className="text-amber-400 mb-2" size={24} />
                                <p className="text-[9px] font-black text-white/50 uppercase">Millas</p>
                                <p className="text-lg font-black text-white">4.500</p>
                            </div>
                        </div>
                        <div className="bg-indigo-600/30 border border-indigo-500/50 rounded-[2.5rem] p-6 text-white relative overflow-hidden">
                            <Building className="absolute right-[-10px] bottom-[-10px] text-white/5" size={80} />
                            <h4 className="font-black text-xs uppercase mb-3 tracking-widest opacity-80">Camino a tu Hipoteca</h4>
                            <div className="flex justify-between text-[10px] font-black mb-1">
                                <span>Progreso</span>
                                <span>12%</span>
                            </div>
                            <div className="h-2 bg-white/20 rounded-full overflow-hidden">
                                <div className="h-full bg-white" style={{width: '12%'}}></div>
                            </div>
                        </div>
                    </div>
                    <button onClick={() => {setShowWizard(null); setWizardStep(1);}} className="mt-auto bg-emerald-500 text-slate-900 font-black py-5 rounded-[2rem] text-lg shadow-2xl shadow-emerald-500/20 uppercase tracking-widest">CONFIRMAR BLINDAJE</button>
                </div>
            )}
        </main>
    </div>
  );

  const renderInventoryWizard = () => (
    <div className="absolute inset-0 bg-white z-[200] flex flex-col animate-in slide-in-from-bottom duration-300">
        <header className="p-6 border-b border-slate-100 flex justify-between items-center">
            <div>
                <h3 className="text-lg font-black text-slate-800 uppercase tracking-tighter leading-none">Bitácora de Estado</h3>
                <p className="text-[9px] font-bold text-slate-400 uppercase mt-1">Evidencia Inmutable</p>
            </div>
            <button onClick={() => setShowWizard(null)} className="p-2 bg-slate-50 rounded-full text-slate-400"><X size={20}/></button>
        </header>
        <main className="flex-1 overflow-y-auto p-5 pb-24">
            <div className="bg-indigo-600 rounded-[2.5rem] p-8 text-white shadow-xl mb-8 relative overflow-hidden">
                <Camera className="absolute right-[-10px] top-[-10px] text-white/10" size={100} />
                <h3 className="text-xl font-black mb-2">Check-in de Propiedad</h3>
                <p className="text-xs font-medium opacity-80">Documenta el estado actual junto al Administrador. Esto protege tu depósito legalmente.</p>
            </div>
            <div className="space-y-4">
                {[
                    { title: "Grifería Cocina", status: "Capturado", icon: Droplets, color: "text-blue-500" },
                    { title: "Paredes Living", status: "Pendiente", icon: Building, color: "text-slate-300" }
                ].map((item, i) => (
                    <div key={i} className="bg-white p-5 rounded-3xl border border-slate-100 flex items-center justify-between shadow-sm">
                        <div className="flex items-center gap-4">
                            <div className="w-12 h-12 bg-slate-50 rounded-2xl flex items-center justify-center"><item.icon size={24} className={item.color}/></div>
                            <div>
                                <h4 className="text-sm font-black text-slate-800 uppercase leading-none">{item.title}</h4>
                                <p className={`text-[10px] font-bold uppercase mt-1 ${item.status === 'Pendiente' ? 'text-amber-500' : 'text-emerald-500'}`}>{item.status}</p>
                            </div>
                        </div>
                        {item.status === 'Pendiente' ? <button className="bg-indigo-600 text-white p-2 rounded-xl shadow-lg"><Camera size={18}/></button> : <CheckCircle2 size={24} className="text-emerald-500" />}
                    </div>
                ))}
            </div>
        </main>
        <div className="absolute bottom-0 w-full p-6 bg-white border-t border-slate-100">
            <button className="w-full bg-slate-900 text-white font-black py-4 rounded-2xl text-[11px] uppercase tracking-[0.2em]">Cerrar Acta Presencial</button>
        </div>
    </div>
  );

  const renderVerdictDetail = () => (
    <div className="absolute inset-0 bg-white z-[300] flex flex-col animate-in slide-in-from-right duration-300">
        <header className="p-8 bg-slate-900 text-white flex justify-between items-center">
            <div className="flex items-center gap-4">
                <div className="w-12 h-12 bg-indigo-600 rounded-xl flex items-center justify-center"><Zap size={24}/></div>
                <div>
                    <p className="text-[9px] font-black uppercase text-indigo-400">Veredicto IA</p>
                    <h2 className="text-xl font-black">Apto con Seguro</h2>
                </div>
            </div>
            <button onClick={() => setShowWizard(null)} className="p-2 bg-white/10 rounded-full"><X size={20}/></button>
        </header>
        <main className="flex-1 overflow-y-auto p-8 space-y-8">
            <div className="flex items-center justify-between p-6 bg-slate-50 rounded-3xl">
                <div>
                    <h4 className="font-black text-lg">Marcos Galperin</h4>
                    <p className="text-[10px] text-slate-400 font-bold uppercase tracking-widest">ID: 20-30444555-9</p>
                </div>
                <div className="text-center">
                    <p className="text-[9px] font-black text-slate-400 uppercase">Score</p>
                    <p className="text-xl font-black text-indigo-600">890</p>
                </div>
            </div>
            <div className="bg-indigo-50 p-6 rounded-3xl border border-indigo-100 relative">
                <h4 className="text-[10px] font-black text-indigo-900 uppercase tracking-widest mb-3">Ratio de Solvencia</h4>
                <div className="flex items-end gap-2 mb-2">
                    <span className="text-4xl font-black text-indigo-900">3.2x</span>
                    <span className="text-[10px] font-bold text-indigo-600 mb-1">Apto</span>
                </div>
                <div className="h-2 bg-indigo-200 rounded-full overflow-hidden">
                    <div className="h-full bg-indigo-600" style={{width: '80%'}}></div>
                </div>
            </div>
            <div className="bg-emerald-50 p-8 rounded-[2.5rem] border border-emerald-100 text-center">
                <h4 className="font-black text-emerald-900 text-lg mb-2">Oportunidad de Venta</h4>
                <p className="text-xs text-emerald-700 font-medium mb-6">El inquilino no tiene aval físico. Ofrece la **Garantía Blindato** para cerrar hoy mismo.</p>
                <button className="w-full bg-emerald-500 text-slate-900 font-black py-4 rounded-2xl text-[11px] uppercase tracking-widest shadow-xl shadow-emerald-500/20 active:scale-95 transition-all">VENDER GARANTÍA (+$15.000)</button>
            </div>
        </main>
        <footer className="p-8 border-t border-slate-100 flex gap-4">
            <button className="flex-1 bg-slate-900 text-white font-black py-4 rounded-2xl text-[10px] uppercase tracking-widest">DAR OK FINAL</button>
            <button className="flex-1 bg-slate-100 text-slate-400 font-black py-4 rounded-2xl text-[10px] uppercase tracking-widest">RECHAZAR</button>
        </footer>
    </div>
  );

  const renderPaymentFlow = () => (
    <div className="absolute inset-0 bg-slate-50 z-[250] flex flex-col animate-in slide-in-from-bottom duration-300">
        <header className="p-6 bg-white border-b border-slate-100 flex justify-between items-center">
            <h2 className="text-lg font-black text-slate-800 uppercase tracking-tighter">Abonar Alquiler</h2>
            <button onClick={() => setShowWizard(null)} className="p-2 bg-slate-50 rounded-full text-slate-400"><X size={20}/></button>
        </header>
        <main className="flex-1 p-6 space-y-6 overflow-y-auto">
            <div className="bg-slate-900 p-8 rounded-[2.5rem] text-white shadow-2xl relative overflow-hidden">
                <div className="flex justify-between items-start mb-2">
                    <p className="text-[10px] font-black uppercase opacity-60 tracking-widest">Periodo Abril 2026</p>
                    <div className="bg-emerald-500/20 px-2 py-1 rounded-lg text-emerald-400 text-[9px] font-black uppercase">Próximo Vencimiento</div>
                </div>
                <h1 className="text-4xl font-black">$450.000</h1>
            </div>
            <div className="space-y-4">
                <button className="w-full p-5 bg-white border-2 border-indigo-600 rounded-3xl flex items-center justify-between shadow-xl shadow-indigo-100">
                    <div className="flex items-center gap-4">
                        <div className="w-12 h-12 bg-indigo-600 text-white rounded-2xl flex items-center justify-center"><Building size={24}/></div>
                        <div className="text-left">
                            <p className="font-black text-sm text-slate-800">Usa mi Blindaje</p>
                            <p className="text-[10px] text-indigo-600 font-bold uppercase">Disponible: $405.430</p>
                        </div>
                    </div>
                    <CheckCircle2 size={24} className="text-indigo-600" />
                </button>
                <button className="w-full p-5 bg-white border border-slate-100 rounded-3xl flex items-center justify-between opacity-60">
                    <div className="flex items-center gap-4">
                        <div className="w-12 h-12 bg-slate-100 text-slate-400 rounded-2xl flex items-center justify-center"><Banknote size={24}/></div>
                        <div className="text-left">
                            <p className="font-black text-sm text-slate-800">Mi Banco (Galicia)</p>
                            <p className="text-[10px] text-slate-400 font-bold uppercase">Saldo: $150.000</p>
                        </div>
                    </div>
                </button>
            </div>
            <div className="bg-indigo-50 p-6 rounded-3xl border border-indigo-100 flex gap-4">
                <TrendingUp size={24} className="text-indigo-600 shrink-0" />
                <p className="text-[11px] text-indigo-700 font-bold leading-relaxed">
                    Si pagas con tu banco, <span className="underline italic">tu blindaje actual saltará a Mayo</span>, garantizando tu score y ahorrándote intereses el próximo mes.
                </p>
            </div>
        </main>
        <footer className="p-6 bg-white border-t border-slate-100">
            <button onClick={() => setShowWizard(null)} className="w-full bg-indigo-600 text-white font-black py-5 rounded-[2rem] shadow-xl text-[11px] uppercase tracking-[0.2em]">Confirmar Pago</button>
        </footer>
    </div>
  );

  // --- NAVEGACIÓN GLOBAL ---

  if (profile === 'Admin') return renderAdminWeb();
  
  const MobileContent = () => {
    switch(view) {
        case 'Home': return renderTenantHome();
        case 'Profile': return renderProfile();
        default: return (
            <div className="flex flex-col items-center justify-center h-full p-10 text-center opacity-30">
                <div className="w-20 h-20 bg-slate-200 rounded-full flex items-center justify-center mb-4"><Settings size={40}/></div>
                <h3 className="text-xl font-black uppercase tracking-tighter">Sección {view}</h3>
                <p className="text-sm">Contenido en desarrollo bajo el estándar V42.0</p>
            </div>
        );
    }
  };

  return renderMobileFrame(<MobileContent />);
}
