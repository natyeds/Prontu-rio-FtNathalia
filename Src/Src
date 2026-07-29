import React, { useState, useEffect, useMemo } from 'react';
import { createClient } from '@supabase/supabase-js';
import {
  Home, Users, Calendar, ClipboardList, DollarSign, Search, LogOut, Plus, Edit2, Trash2,
  Printer, ChevronLeft, ChevronRight, Cake, Clock, X, Copy, ArrowLeft, User, Save,
  CheckCircle2, XCircle, MinusCircle, FileText, Stethoscope, ActivitySquare, Camera,
  Brain, Wind, HeartPulse, Bone
} from 'lucide-react';

/* ============================= BRAND TOKENS ============================= */
const C = {
  olive: '#4A5A46',
  sage: '#7D8A71',
  tan: '#DCC8B2',
  cream: '#F7F5F0',
  gold: '#C49A6C',
  oliveDark: '#384535',
  ink: '#2E3630',
};
const FONT_DISPLAY = "'Cormorant Garamond', serif";
const FONT_BODY = "'Montserrat', sans-serif";

const ORTHO_TESTS = {
  'Ombro': ['Neer', 'Hawkins-Kennedy', 'Jobe', 'Patte', 'Lift Off', 'Speed', 'Yergason', 'Apreensão', 'Sulco'],
  'Cotovelo': ['Cozen', 'Mill', 'Golfer', 'Tinel'],
  'Punho e Mão': ['Phalen', 'Phalen reverso', 'Tinel', 'Finkelstein', 'Froment'],
  'Coluna Cervical': ['Spurling', 'Distração Cervical', 'Valsalva'],
  'Coluna Lombar': ['Lasègue', 'Slump', 'Patrick (FABER)', 'Gaenslen'],
  'Quadril': ['FABER', 'FADIR', 'Trendelenburg', 'Thomas', 'Ober'],
  'Joelho': ['Lachman', 'Gaveta Anterior', 'Gaveta Posterior', 'Pivot Shift', 'McMurray', 'Apley', 'Estresse em Valgo', 'Estresse em Varo', 'Clarke', 'Patelar'],
  'Tornozelo': ['Gaveta Anterior', 'Talar Tilt', 'Thompson'],
};

const OXFORD_SCALE = ['0 - Sem contração', '1 - Esboço', '2 - Movimento sem gravidade', '3 - Movimento contra gravidade', '4 - Vence resistência parcial', '5 - Normal'];

/* ---- Configuração das avaliações clínicas ---- */
const EVAL_TYPES = {
  ortopedica: { label: 'Ortopédica', icon: 'Bone', color: '#4A5A46' },
  neurologica: { label: 'Neurológica', icon: 'Brain', color: '#7D8A71' },
  respiratoria: { label: 'Respiratória / Cardiorrespiratória', icon: 'Wind', color: '#C49A6C' },
  gerontologia: { label: 'Gerontologia', icon: 'HeartPulse', color: '#B08968' },
};

const NEURO_FIELDS = [
  ['anamnese', 'Anamnese'], ['nivelConsciencia', 'Nível de consciência'], ['glasgow', 'Escala de Glasgow'],
  ['tono', 'Tônus'], ['espasticidade', 'Espasticidade (Escala de Ashworth)'], ['reflexos', 'Reflexos'],
  ['coordenacao', 'Coordenação'], ['marcha', 'Marcha'], ['sensibilidade', 'Sensibilidade'],
  ['equilibrio', 'Equilíbrio'], ['funcaoMotora', 'Função motora'], ['paresCranianos', 'Pares cranianos'],
  ['controlePostural', 'Controle postural'],
];
const NEURO_SCALES = ['Berg', 'TUG', 'Romberg', 'Mini BESTest', 'Escala de Fugl-Meyer', 'Rankin', 'Barthel', 'Brunnstrom'];

const RESP_FIELDS = [
  ['anamnese', 'Anamnese'], ['historicoRespiratorio', 'Histórico respiratório'],
  ['pa', 'PA'], ['fc', 'FC'], ['fr', 'FR'], ['spo2', 'SpO₂'], ['temperatura', 'Temperatura'],
  ['ausculta', 'Ausculta pulmonar'], ['padraoRespiratorio', 'Padrão respiratório'],
  ['expansibilidade', 'Expansibilidade torácica'], ['tosse', 'Tosse'], ['secrecao', 'Secreção'],
  ['oxigenoterapia', 'Oxigenoterapia'], ['ventilacaoMecanica', 'Ventilação mecânica'],
];
const RESP_SCALES = ['Borg', 'MRC', 'NYHA', 'Caminhada de 6 minutos', 'Shuttle Walk Test', 'Pico de Fluxo', 'Manovacuometria'];

const GERO_FIELDS = [
  ['anamnese', 'Anamnese'], ['cognicao', 'Cognição'], ['humor', 'Humor'], ['mobilidade', 'Mobilidade'],
  ['equilibrio', 'Equilíbrio'], ['riscoQueda', 'Risco de queda'], ['avds', 'AVDs'], ['aivds', 'AIVDs'],
  ['nutricao', 'Nutrição'], ['dor', 'Dor'], ['incontinencia', 'Incontinência'],
];
const GERO_SCALES = ['Mini Mental', 'MoCA', 'Katz', 'Lawton', 'Berg', 'TUG', 'Edmonton', 'GDS', 'Morse'];

const PAY_METHODS = ['Pix', 'Dinheiro', 'Cartão', 'Convênio'];
const STATUS_LIST = ['confirmado', 'aguardando', 'realizado', 'cancelado'];
const STATUS_COLOR = {
  confirmado: '#7D8A71', aguardando: '#C49A6C', realizado: '#4A5A46', cancelado: '#B5645A',
};

/* ============================= SUPABASE ============================= */
const SUPABASE_URL = 'https://wjfakrmwzkhqiaaspkda.supabase.co';
const SUPABASE_PUBLISHABLE_KEY = 'sb_publishable_tZKcsSJDcfDcREoaptc4lA_777h9cxR';

export const supabase = createClient(SUPABASE_URL, SUPABASE_PUBLISHABLE_KEY, {
  auth: {
    persistSession: true,
    autoRefreshToken: true,
    detectSessionInUrl: true,
  },
});

const DB_TABLES = {
  patients: 'patients',
  appointments: 'appointments',
  evolutions: 'evolutions',
  evaluations: 'evaluations',
  financial: 'financial',
};

async function dbLoad(key) {
  const { data, error } = await supabase
    .from(DB_TABLES[key])
    .select('id,data')
    .order('created_at', { ascending: true });

  if (error) throw error;
  return (data || []).map(row => ({ ...(row.data || {}), id: row.id }));
}

async function dbUpsert(key, item, userId) {
  const { error } = await supabase
    .from(DB_TABLES[key])
    .upsert({
      id: item.id,
      user_id: userId,
      data: item,
    }, { onConflict: 'id' });

  if (error) throw error;
}

async function dbRemove(key, id) {
  const { error } = await supabase
    .from(DB_TABLES[key])
    .delete()
    .eq('id', id);

  if (error) throw error;
}

async function dbLoadProfile(userId) {
  const { data, error } = await supabase
    .from('profiles')
    .select('avatar_data')
    .eq('id', userId)
    .maybeSingle();

  if (error) throw error;
  return data?.avatar_data || null;
}

async function dbSaveProfile(userId, avatarData) {
  const { error } = await supabase
    .from('profiles')
    .upsert({
      id: userId,
      avatar_data: avatarData,
      updated_at: new Date().toISOString(),
    }, { onConflict: 'id' });

  if (error) throw error;
}

const uid = () => Date.now().toString(36) + Math.random().toString(36).slice(2, 8);

function calcAge(dob) {
  if (!dob) return '';
  const b = new Date(dob + 'T00:00:00');
  if (isNaN(b)) return '';
  const t = new Date();
  let age = t.getFullYear() - b.getFullYear();
  const m = t.getMonth() - b.getMonth();
  if (m < 0 || (m === 0 && t.getDate() < b.getDate())) age--;
  return age;
}
function fmtDate(d) {
  if (!d) return '';
  const dt = new Date(d + 'T00:00:00');
  if (isNaN(dt)) return d;
  return dt.toLocaleDateString('pt-BR');
}
function resizePhoto(file, maxSize = 260) {
  return new Promise((resolve, reject) => {
    const reader = new FileReader();
    reader.onload = () => {
      const img = new Image();
      img.onload = () => {
        const scale = Math.min(1, maxSize / Math.max(img.width, img.height));
        const w = Math.round(img.width * scale), h = Math.round(img.height * scale);
        const canvas = document.createElement('canvas');
        canvas.width = w; canvas.height = h;
        canvas.getContext('2d').drawImage(img, 0, 0, w, h);
        resolve(canvas.toDataURL('image/jpeg', 0.85));
      };
      img.onerror = reject;
      img.src = reader.result;
    };
    reader.onerror = reject;
    reader.readAsDataURL(file);
  });
}
function todayISO() { return new Date().toISOString().slice(0, 10); }
function nowTime() { return new Date().toTimeString().slice(0, 5); }

/* ============================= SHARED UI ============================= */
function GlobalStyle() {
  return (
    <style>{`
      @import url('https://fonts.googleapis.com/css2?family=Cormorant+Garamond:wght@400;500;600;700&family=Montserrat:wght@300;400;500;600;700&display=swap');
      * { box-sizing: border-box; }
      body { margin: 0; }
      .fade-in { animation: fadeIn .35s ease; }
      @keyframes fadeIn { from { opacity: 0; transform: translateY(6px);} to { opacity:1; transform:none; } }
      ::-webkit-scrollbar { width: 8px; height: 8px; }
      ::-webkit-scrollbar-thumb { background: ${C.tan}; border-radius: 8px; }
      input, select, textarea { font-family: ${FONT_BODY}; }
      input:focus, select:focus, textarea:focus, button:focus-visible { outline: 2px solid ${C.gold}; outline-offset: 1px; }
      @media print {
        .no-print { display: none !important; }
        .print-area { display: block !important; }
      }
    `}</style>
  );
}

function Btn({ children, onClick, variant = 'primary', size = 'md', icon: Icon, type = 'button', style, disabled }) {
  const base = {
    fontFamily: FONT_BODY, fontWeight: 600, border: 'none', borderRadius: 10, cursor: disabled ? 'not-allowed' : 'pointer',
    display: 'inline-flex', alignItems: 'center', gap: 8, transition: 'all .15s ease', letterSpacing: 0.2,
    opacity: disabled ? 0.5 : 1,
  };
  const sizes = { sm: { padding: '7px 12px', fontSize: 13 }, md: { padding: '11px 18px', fontSize: 14 }, lg: { padding: '14px 26px', fontSize: 15 } };
  const variants = {
    primary: { background: C.olive, color: C.cream },
    gold: { background: C.gold, color: '#fff' },
    outline: { background: 'transparent', color: C.olive, border: `1.5px solid ${C.olive}` },
    ghost: { background: 'transparent', color: C.olive },
    danger: { background: 'transparent', color: '#B5645A', border: '1.5px solid #B5645A22' },
  };
  return (
    <button type={type} disabled={disabled} onClick={onClick} style={{ ...base, ...sizes[size], ...variants[variant], ...style }}
      onMouseEnter={e => { if (!disabled) e.currentTarget.style.transform = 'translateY(-1px)'; }}
      onMouseLeave={e => { e.currentTarget.style.transform = 'none'; }}>
      {Icon && <Icon size={size === 'sm' ? 15 : 17} />}{children}
    </button>
  );
}

function Field({ label, children, full }) {
  return (
    <div style={{ gridColumn: full ? '1 / -1' : undefined, display: 'flex', flexDirection: 'column', gap: 6 }}>
      <label style={{ fontSize: 12, fontWeight: 600, color: C.sage, textTransform: 'uppercase', letterSpacing: 0.5 }}>{label}</label>
      {children}
    </div>
  );
}
const inputStyle = { padding: '10px 12px', borderRadius: 8, border: `1.5px solid #E4DDD1`, fontSize: 14, color: C.ink, background: '#fff', width: '100%' };

function Input(props) { return <input {...props} style={{ ...inputStyle, ...(props.style || {}) }} />; }
function TextArea(props) { return <textarea {...props} style={{ ...inputStyle, resize: 'vertical', minHeight: 70, ...(props.style || {}) }} />; }
function Select({ children, ...props }) { return <select {...props} style={{ ...inputStyle, ...(props.style || {}) }}>{children}</select>; }

function Card({ children, style }) {
  return <div style={{ background: '#fff', borderRadius: 16, padding: 22, boxShadow: '0 2px 14px rgba(74,90,70,0.07)', border: '1px solid #EFEAE1', ...style }}>{children}</div>;
}
function SectionTitle({ children, sub }) {
  return (
    <div style={{ marginBottom: 16 }}>
      <h3 style={{ fontFamily: FONT_DISPLAY, fontSize: 24, color: C.olive, margin: 0, fontWeight: 600 }}>{children}</h3>
      {sub && <p style={{ fontSize: 13, color: C.sage, margin: '4px 0 0' }}>{sub}</p>}
    </div>
  );
}
function Badge({ children, color }) {
  return <span style={{ fontSize: 11, fontWeight: 700, padding: '3px 10px', borderRadius: 20, background: `${color}22`, color, textTransform: 'capitalize' }}>{children}</span>;
}
function Modal({ title, onClose, children, width = 640 }) {
  return (
    <div style={{ position: 'fixed', inset: 0, background: 'rgba(46,54,48,0.45)', zIndex: 50, display: 'flex', alignItems: 'center', justifyContent: 'center', padding: 16 }} onClick={onClose}>
      <div className="fade-in" onClick={e => e.stopPropagation()} style={{ background: C.cream, borderRadius: 18, width: '100%', maxWidth: width, maxHeight: '90vh', overflowY: 'auto', padding: 26, boxShadow: '0 20px 60px rgba(0,0,0,0.25)' }}>
        <div style={{ display: 'flex', justifyContent: 'space-between', alignItems: 'center', marginBottom: 18 }}>
          <h3 style={{ fontFamily: FONT_DISPLAY, fontSize: 24, color: C.olive, margin: 0 }}>{title}</h3>
          <button onClick={onClose} style={{ background: 'none', border: 'none', cursor: 'pointer', color: C.sage }}><X size={22} /></button>
        </div>
        {children}
      </div>
    </div>
  );
}

/* ============================= LOGIN ============================= */
function LoginScreen({ auth, onLogin, onSignUp, onResetPassword, photo, onChangePhoto }) {
  const [mode, setMode] = useState(auth ? 'login' : 'signup');
  const [email, setEmail] = useState(auth?.email || '');
  const [pass, setPass] = useState('');
  const [pass2, setPass2] = useState('');
  const [remember, setRemember] = useState(true);
  const [err, setErr] = useState('');
  const [info, setInfo] = useState('');

  const submit = async () => {
    setErr('');
    setInfo('');
    const cleanEmail = email.trim().toLowerCase();

    if (!cleanEmail) {
      setErr('Informe seu e-mail.');
      return;
    }

    if (mode === 'reset') {
      const result = await onResetPassword(cleanEmail);
      if (result?.error) setErr(result.error);
      else setInfo('Se o e-mail estiver cadastrado, você receberá um link para redefinir a senha.');
      return;
    }

    if (pass.length < 6) {
      setErr('A senha precisa ter pelo menos 6 caracteres.');
      return;
    }

    if (mode === 'signup') {
      if (pass !== pass2) {
        setErr('As senhas não coincidem.');
        return;
      }
      const result = await onSignUp(cleanEmail, pass);
      if (result?.error) setErr(result.error);
      else if (result?.needsEmailConfirmation) {
        setInfo('Conta criada. Confira seu e-mail para confirmar o cadastro antes de entrar.');
        setMode('login');
        setPass('');
        setPass2('');
      }
      return;
    }

    const result = await onLogin(cleanEmail, pass, remember);
    if (result?.error) setErr(result.error);
  };

  return (
    <div style={{ minHeight: '100vh', background: `linear-gradient(150deg, ${C.cream}, #EFE7D9)`, display: 'flex', alignItems: 'center', justifyContent: 'center', fontFamily: FONT_BODY, padding: 20 }}>
      <div className="fade-in" style={{ width: '100%', maxWidth: 420 }}>
        <div style={{ textAlign: 'center', marginBottom: 30 }}>
          <label style={{ width: 72, height: 72, margin: '0 auto 14px', borderRadius: '50%', background: C.olive, display: 'flex', alignItems: 'center', justifyContent: 'center', position: 'relative', cursor: 'pointer', overflow: 'hidden' }}>
            {photo ? <img src={photo} alt="Foto de perfil" style={{ width: '100%', height: '100%', objectFit: 'cover' }} /> : <ActivitySquare color={C.gold} size={32} />}
            <span style={{ position: 'absolute', bottom: 0, left: 0, right: 0, background: 'rgba(0,0,0,0.45)', display: 'flex', alignItems: 'center', justifyContent: 'center', padding: '3px 0' }}>
              <Camera size={12} color="#fff" />
            </span>
            <input type="file" accept="image/*" style={{ display: 'none' }} onChange={e => e.target.files[0] && onChangePhoto(e.target.files[0])} />
          </label>
          <h1 style={{ fontFamily: FONT_DISPLAY, fontSize: 34, color: C.olive, margin: 0, fontWeight: 600 }}>Nathalia Emilly</h1>
          <p style={{ letterSpacing: 3, fontSize: 11, color: C.gold, fontWeight: 600, margin: '4px 0 0' }}>FISIOTERAPEUTA</p>
          <p style={{ fontSize: 12, color: C.sage, marginTop: 10 }}>MOVIMENTO · EQUILÍBRIO · CIÊNCIA · CUIDADO</p>
        </div>
        <Card>
          <SectionTitle>{mode === 'login' ? 'Entrar' : mode === 'reset' ? 'Redefinir senha' : 'Criar acesso'}</SectionTitle>
          <div style={{ display: 'flex', flexDirection: 'column', gap: 14 }}>
            <Field label="E-mail"><Input type="email" value={email} onChange={e => setEmail(e.target.value)} placeholder="voce@clinica.com" /></Field>
            {mode !== 'reset' && (
              <Field label={mode === 'login' ? 'Senha' : 'Nova senha'}>
                <Input type="password" value={pass} onChange={e => setPass(e.target.value)} placeholder="••••••••" />
              </Field>
            )}
            {mode === 'signup' && (
              <Field label="Confirmar senha"><Input type="password" value={pass2} onChange={e => setPass2(e.target.value)} placeholder="••••••••" /></Field>
            )}
            {mode === 'login' && (
              <label style={{ display: 'flex', alignItems: 'center', gap: 8, fontSize: 13, color: C.ink }}>
                <input type="checkbox" checked={remember} onChange={e => setRemember(e.target.checked)} /> Permanecer conectado
              </label>
            )}
            {err && <div style={{ color: '#B5645A', fontSize: 13 }}>{err}</div>}
            {info && <div style={{ color: C.olive, fontSize: 13 }}>{info}</div>}
            <Btn onClick={submit} size="lg" style={{ justifyContent: 'center', marginTop: 4 }}>
              {mode === 'login' ? 'Entrar' : mode === 'reset' ? 'Enviar link de redefinição' : 'Criar conta'}
            </Btn>
            {mode === 'login' ? (
              <div style={{ display: 'flex', flexDirection: 'column', gap: 5 }}>
                <button onClick={() => { setMode('reset'); setErr(''); setInfo(''); }} style={{ background: 'none', border: 'none', color: C.sage, fontSize: 12.5, cursor: 'pointer', textAlign: 'center' }}>Esqueci minha senha</button>
                <button onClick={() => { setMode('signup'); setErr(''); setInfo(''); }} style={{ background: 'none', border: 'none', color: C.olive, fontSize: 12.5, cursor: 'pointer', textAlign: 'center', fontWeight: 600 }}>Criar uma conta</button>
              </div>
            ) : (
              <button onClick={() => { setMode('login'); setErr(''); setInfo(''); }} style={{ background: 'none', border: 'none', color: C.sage, fontSize: 12.5, cursor: 'pointer', textAlign: 'center' }}>Voltar ao login</button>
            )}
          </div>
        </Card>
        <p style={{ textAlign: 'center', fontSize: 11, color: C.sage, marginTop: 16 }}>Área protegida · dados sincronizados com Supabase</p>
      </div>
    </div>
  );
}

/* ============================= LAYOUT / SIDEBAR ============================= */
const NAV = [
  { key: 'dashboard', label: 'Início', icon: Home },
  { key: 'patients', label: 'Pacientes', icon: Users },
  { key: 'agenda', label: 'Agenda', icon: Calendar },
  { key: 'financeiro', label: 'Financeiro', icon: DollarSign },
];

function Sidebar({ view, setView, onLogout, onSearch, searchTerm, photo, onChangePhoto }) {
  return (
    <div className="no-print" style={{ width: 240, minWidth: 240, background: C.olive, color: C.cream, display: 'flex', flexDirection: 'column', padding: '26px 18px', height: '100vh', position: 'sticky', top: 0 }}>
      <div style={{ display: 'flex', alignItems: 'center', gap: 10, marginBottom: 30, padding: '0 6px' }}>
        <label style={{ width: 38, height: 38, borderRadius: '50%', background: C.gold, display: 'flex', alignItems: 'center', justifyContent: 'center', flexShrink: 0, position: 'relative', cursor: 'pointer', overflow: 'hidden' }} title="Alterar foto de perfil">
          {photo ? <img src={photo} alt="Foto de perfil" style={{ width: '100%', height: '100%', objectFit: 'cover' }} /> : <ActivitySquare size={19} color="#fff" />}
          <input type="file" accept="image/*" style={{ display: 'none' }} onChange={e => e.target.files[0] && onChangePhoto(e.target.files[0])} />
        </label>
        <div>
          <div style={{ fontFamily: FONT_DISPLAY, fontSize: 19, fontWeight: 600, lineHeight: 1.1 }}>Nathalia Emilly</div>
          <div style={{ fontSize: 9.5, letterSpacing: 1.5, color: C.tan }}>FISIOTERAPEUTA</div>
        </div>
      </div>

      <div style={{ position: 'relative', marginBottom: 22 }}>
        <Search size={15} style={{ position: 'absolute', left: 10, top: 11, color: C.tan }} />
        <input value={searchTerm} onChange={e => onSearch(e.target.value)} placeholder="Buscar paciente..."
          style={{ width: '100%', background: 'rgba(255,255,255,0.1)', border: '1px solid rgba(255,255,255,0.15)', borderRadius: 9, padding: '9px 10px 9px 32px', color: '#fff', fontSize: 13 }} />
      </div>

      <nav style={{ display: 'flex', flexDirection: 'column', gap: 4, flex: 1 }}>
        {NAV.map(n => {
          const active = view === n.key;
          return (
            <button key={n.key} onClick={() => setView(n.key)} style={{
              display: 'flex', alignItems: 'center', gap: 12, padding: '11px 12px', borderRadius: 10, border: 'none', cursor: 'pointer',
              background: active ? C.gold : 'transparent', color: active ? '#fff' : C.tan, fontFamily: FONT_BODY, fontWeight: 600, fontSize: 14, textAlign: 'left', transition: 'all .15s',
            }}>
              <n.icon size={17} /> {n.label}
            </button>
          );
        })}
      </nav>
      <button onClick={onLogout} style={{ display: 'flex', alignItems: 'center', gap: 10, background: 'none', border: 'none', color: C.tan, cursor: 'pointer', fontSize: 13, padding: '10px 12px' }}>
        <LogOut size={16} /> Sair
      </button>
    </div>
  );
}

/* ============================= DASHBOARD ============================= */
function Dashboard({ patients, appointments, evolutions, goPatient, setView }) {
  const today = todayISO();
  const todays = appointments.filter(a => a.date === today).sort((a, b) => a.time.localeCompare(b.time));
  const attendedToday = todays.filter(a => a.status === 'realizado').length;
  const upcoming = appointments.filter(a => a.date > today).sort((a, b) => (a.date + a.time).localeCompare(b.date + b.time)).slice(0, 5);
  const pendingEvol = todays.filter(a => a.status === 'realizado' && !evolutions.some(e => e.patientId === a.patientId && e.date === today));
  const thisMonth = new Date().getMonth();
  const birthdays = patients.filter(p => p.dataNascimento && new Date(p.dataNascimento + 'T00:00:00').getMonth() === thisMonth);

  const stat = (label, val, icon, color) => (
    <Card style={{ flex: 1, minWidth: 170 }}>
      <div style={{ display: 'flex', justifyContent: 'space-between', alignItems: 'flex-start' }}>
        <div>
          <div style={{ fontSize: 12, color: C.sage, fontWeight: 600, textTransform: 'uppercase', letterSpacing: 0.4 }}>{label}</div>
          <div style={{ fontFamily: FONT_DISPLAY, fontSize: 36, color: C.olive, fontWeight: 700 }}>{val}</div>
        </div>
        <div style={{ width: 40, height: 40, borderRadius: 12, background: `${color}1c`, display: 'flex', alignItems: 'center', justifyContent: 'center' }}>
          {React.createElement(icon, { size: 19, color })}
        </div>
      </div>
    </Card>
  );

  return (
    <div className="fade-in" style={{ display: 'flex', flexDirection: 'column', gap: 20 }}>
      <div>
        <h1 style={{ fontFamily: FONT_DISPLAY, fontSize: 32, color: C.olive, margin: 0 }}>Bem-vinda, Dra. Nathalia</h1>
        <p style={{ color: C.sage, margin: '4px 0 0', fontSize: 14 }}>{new Date().toLocaleDateString('pt-BR', { weekday: 'long', day: 'numeric', month: 'long' })}</p>
      </div>
      <div style={{ display: 'flex', gap: 16, flexWrap: 'wrap' }}>
        {stat('Pacientes cadastrados', patients.length, Users, C.olive)}
        {stat('Atendidos hoje', attendedToday, CheckCircle2, C.sage)}
        {stat('Agenda de hoje', todays.length, Calendar, C.gold)}
        {stat('Evoluções pendentes', pendingEvol.length, ClipboardList, '#B5645A')}
      </div>

      <div style={{ display: 'flex', gap: 20, flexWrap: 'wrap' }}>
        <Card style={{ flex: 2, minWidth: 320 }}>
          <SectionTitle sub="Atendimentos marcados para hoje">Agenda do dia</SectionTitle>
          {todays.length === 0 && <p style={{ color: C.sage, fontSize: 14 }}>Nenhum atendimento hoje.</p>}
          <div style={{ display: 'flex', flexDirection: 'column', gap: 8 }}>
            {todays.map(a => {
              const p = patients.find(pp => pp.id === a.patientId);
              return (
                <div key={a.id} onClick={() => p && goPatient(p.id)} style={{ display: 'flex', justifyContent: 'space-between', alignItems: 'center', padding: '10px 12px', borderRadius: 10, background: C.cream, cursor: 'pointer' }}>
                  <div style={{ display: 'flex', alignItems: 'center', gap: 10 }}>
                    <Clock size={15} color={C.sage} /><b style={{ fontSize: 14 }}>{a.time}</b>
                    <span style={{ fontSize: 14 }}>{p ? p.nome : '—'}</span>
                  </div>
                  <Badge color={STATUS_COLOR[a.status]}>{a.status}</Badge>
                </div>
              );
            })}
          </div>
        </Card>

        <Card style={{ flex: 1, minWidth: 260 }}>
          <SectionTitle>Próximos atendimentos</SectionTitle>
          <div style={{ display: 'flex', flexDirection: 'column', gap: 10 }}>
            {upcoming.length === 0 && <p style={{ color: C.sage, fontSize: 14 }}>Nada agendado à frente.</p>}
            {upcoming.map(a => {
              const p = patients.find(pp => pp.id === a.patientId);
              return (
                <div key={a.id} style={{ fontSize: 13.5 }}>
                  <b>{fmtDate(a.date)}</b> · {a.time} — {p ? p.nome : '—'}
                </div>
              );
            })}
          </div>
        </Card>
      </div>

      <div style={{ display: 'flex', gap: 20, flexWrap: 'wrap' }}>
        <Card style={{ flex: 1, minWidth: 260 }}>
          <SectionTitle>Evoluções pendentes</SectionTitle>
          {pendingEvol.length === 0 && <p style={{ color: C.sage, fontSize: 14 }}>Tudo em dia.</p>}
          {pendingEvol.map(a => {
            const p = patients.find(pp => pp.id === a.patientId);
            return (
              <div key={a.id} onClick={() => p && goPatient(p.id, 'evolucoes')} style={{ cursor: 'pointer', fontSize: 13.5, padding: '6px 0' }}>
                • {p ? p.nome : '—'} (atendido hoje, sem evolução registrada)
              </div>
            );
          })}
        </Card>
        <Card style={{ flex: 1, minWidth: 260 }}>
          <SectionTitle sub="Aniversariantes do mês">
            <Cake size={18} style={{ verticalAlign: 'middle', marginRight: 6, color: C.gold }} />Aniversariantes
          </SectionTitle>
          {birthdays.length === 0 && <p style={{ color: C.sage, fontSize: 14 }}>Nenhum este mês.</p>}
          {birthdays.map(p => (
            <div key={p.id} style={{ fontSize: 13.5, padding: '4px 0' }}>🎂 {p.nome} — {new Date(p.dataNascimento + 'T00:00:00').toLocaleDateString('pt-BR', { day: '2-digit', month: 'long' })}</div>
          ))}
        </Card>
      </div>
    </div>
  );
}

/* ============================= PATIENTS LIST ============================= */
function PatientsList({ patients, goPatient, addPatient, searchTerm }) {
  const [showForm, setShowForm] = useState(false);
  const filtered = useMemo(() => {
    const t = searchTerm.trim().toLowerCase();
    if (!t) return patients;
    return patients.filter(p => p.nome?.toLowerCase().includes(t) || p.telefone?.includes(t) || p.cpf?.includes(t));
  }, [patients, searchTerm]);

  return (
    <div className="fade-in">
      <div style={{ display: 'flex', justifyContent: 'space-between', alignItems: 'center', marginBottom: 20 }}>
        <div>
          <h1 style={{ fontFamily: FONT_DISPLAY, fontSize: 30, color: C.olive, margin: 0 }}>Pacientes</h1>
          <p style={{ color: C.sage, margin: '2px 0 0', fontSize: 13.5 }}>{patients.length} cadastrados</p>
        </div>
        <Btn icon={Plus} onClick={() => setShowForm(true)}>Novo paciente</Btn>
      </div>
      <div style={{ display: 'grid', gridTemplateColumns: 'repeat(auto-fill, minmax(250px, 1fr))', gap: 14 }}>
        {filtered.map(p => (
          <Card key={p.id} style={{ cursor: 'pointer' }}>
            <div onClick={() => goPatient(p.id)}>
              <div style={{ display: 'flex', alignItems: 'center', gap: 12 }}>
                <div style={{ width: 44, height: 44, borderRadius: '50%', background: C.tan, display: 'flex', alignItems: 'center', justifyContent: 'center', fontFamily: FONT_DISPLAY, fontSize: 19, color: C.olive, fontWeight: 700 }}>
                  {p.nome?.[0]?.toUpperCase() || '?'}
                </div>
                <div>
                  <div style={{ fontWeight: 600, fontSize: 15, color: C.ink }}>{p.nome}</div>
                  <div style={{ fontSize: 12.5, color: C.sage }}>{calcAge(p.dataNascimento)} anos · {p.convenio || 'Particular'}</div>
                </div>
              </div>
              <div style={{ marginTop: 10, fontSize: 12.5, color: C.sage }}>{p.telefone}</div>
            </div>
          </Card>
        ))}
        {filtered.length === 0 && <p style={{ color: C.sage }}>Nenhum paciente encontrado.</p>}
      </div>
      {showForm && <PatientFormModal onClose={() => setShowForm(false)} onSave={p => { addPatient(p); setShowForm(false); }} />}
    </div>
  );
}

function PatientFormModal({ onClose, onSave, initial }) {
  const [f, setF] = useState(initial || {
    nome: '', dataNascimento: '', sexo: '', cpf: '', rg: '', telefone: '', whatsapp: '', email: '',
    endereco: '', profissao: '', convenio: '', carteirinha: '', responsavel: '',
    diagnostico: '', cid: '', medicoSolicitante: '', queixaPrincipal: '', hda: '', hp: '', medicamentos: '', cirurgias: '', alergias: '',
  });
  const set = (k, v) => setF(prev => ({ ...prev, [k]: v }));
  const grid = { display: 'grid', gridTemplateColumns: '1fr 1fr', gap: 14 };

  return (
    <Modal title={initial ? 'Editar paciente' : 'Novo paciente'} onClose={onClose} width={760}>
      <SectionTitle>Dados pessoais</SectionTitle>
      <div style={grid}>
        <Field label="Nome" full><Input value={f.nome} onChange={e => set('nome', e.target.value)} /></Field>
        <Field label="Data de nascimento"><Input type="date" value={f.dataNascimento} onChange={e => set('dataNascimento', e.target.value)} /></Field>
        <Field label="Sexo"><Select value={f.sexo} onChange={e => set('sexo', e.target.value)}><option value="">Selecione</option><option>Feminino</option><option>Masculino</option><option>Outro</option></Select></Field>
        <Field label="CPF"><Input value={f.cpf} onChange={e => set('cpf', e.target.value)} /></Field>
        <Field label="RG"><Input value={f.rg} onChange={e => set('rg', e.target.value)} /></Field>
        <Field label="Telefone"><Input value={f.telefone} onChange={e => set('telefone', e.target.value)} /></Field>
        <Field label="WhatsApp"><Input value={f.whatsapp} onChange={e => set('whatsapp', e.target.value)} /></Field>
        <Field label="E-mail"><Input value={f.email} onChange={e => set('email', e.target.value)} /></Field>
        <Field label="Endereço" full><Input value={f.endereco} onChange={e => set('endereco', e.target.value)} /></Field>
        <Field label="Profissão"><Input value={f.profissao} onChange={e => set('profissao', e.target.value)} /></Field>
        <Field label="Responsável"><Input value={f.responsavel} onChange={e => set('responsavel', e.target.value)} /></Field>
        <Field label="Convênio"><Input value={f.convenio} onChange={e => set('convenio', e.target.value)} /></Field>
        <Field label="Nº carteirinha"><Input value={f.carteirinha} onChange={e => set('carteirinha', e.target.value)} /></Field>
      </div>
      <div style={{ marginTop: 20 }}><SectionTitle>Dados clínicos</SectionTitle></div>
      <div style={grid}>
        <Field label="Diagnóstico médico"><Input value={f.diagnostico} onChange={e => set('diagnostico', e.target.value)} /></Field>
        <Field label="CID"><Input value={f.cid} onChange={e => set('cid', e.target.value)} /></Field>
        <Field label="Médico solicitante" full><Input value={f.medicoSolicitante} onChange={e => set('medicoSolicitante', e.target.value)} /></Field>
        <Field label="Queixa principal" full><TextArea value={f.queixaPrincipal} onChange={e => set('queixaPrincipal', e.target.value)} /></Field>
        <Field label="História da doença atual" full><TextArea value={f.hda} onChange={e => set('hda', e.target.value)} /></Field>
        <Field label="História pregressa" full><TextArea value={f.hp} onChange={e => set('hp', e.target.value)} /></Field>
        <Field label="Medicamentos"><TextArea value={f.medicamentos} onChange={e => set('medicamentos', e.target.value)} /></Field>
        <Field label="Cirurgias"><TextArea value={f.cirurgias} onChange={e => set('cirurgias', e.target.value)} /></Field>
        <Field label="Alergias" full><Input value={f.alergias} onChange={e => set('alergias', e.target.value)} /></Field>
      </div>
      <div style={{ display: 'flex', justifyContent: 'flex-end', gap: 10, marginTop: 22 }}>
        <Btn variant="outline" onClick={onClose}>Cancelar</Btn>
        <Btn icon={Save} onClick={() => { if (!f.nome) { alert('Informe o nome do paciente.'); return; } onSave({ ...f, id: f.id || uid() }); }}>Salvar</Btn>
      </div>
    </Modal>
  );
}

/* ============================= PATIENT DETAIL ============================= */
function PatientDetail({ patient, patients, updatePatient, deletePatient, appointments, evolutions, saveEvolution, deleteEvolution, evaluations, saveEvaluation, back, initialTab }) {
  const [tab, setTab] = useState(initialTab || 'dados');
  const [editing, setEditing] = useState(false);
  const [evalFormType, setEvalFormType] = useState(null); // 'ortopedica' | 'neurologica' | 'respiratoria' | 'gerontologia' | null
  const [showDecl, setShowDecl] = useState(false);

  const patAppointments = appointments.filter(a => a.patientId === patient.id).sort((a, b) => (b.date + b.time).localeCompare(a.date + a.time));
  const realizados = patAppointments.filter(a => a.status === 'realizado');
  const patEvolutions = evolutions.filter(e => e.patientId === patient.id).sort((a, b) => (b.date + b.time).localeCompare(a.date + a.time));
  const patEvals = evaluations.filter(e => e.patientId === patient.id).sort((a, b) => b.date.localeCompare(a.date));

  const tabs = [
    { key: 'dados', label: 'Dados' },
    { key: 'avaliacoes', label: 'Avaliações' },
    { key: 'evolucoes', label: 'Evoluções' },
    { key: 'atendimentos', label: 'Atendimentos' },
  ];

  return (
    <div className="fade-in">
      <button onClick={back} className="no-print" style={{ display: 'flex', alignItems: 'center', gap: 6, background: 'none', border: 'none', color: C.sage, cursor: 'pointer', fontSize: 13, marginBottom: 14, padding: 0 }}>
        <ArrowLeft size={15} /> Voltar
      </button>
      <div className="no-print" style={{ display: 'flex', justifyContent: 'space-between', alignItems: 'center', marginBottom: 18, flexWrap: 'wrap', gap: 10 }}>
        <div style={{ display: 'flex', alignItems: 'center', gap: 14 }}>
          <div style={{ width: 54, height: 54, borderRadius: '50%', background: C.tan, display: 'flex', alignItems: 'center', justifyContent: 'center', fontFamily: FONT_DISPLAY, fontSize: 24, color: C.olive, fontWeight: 700 }}>
            {patient.nome?.[0]?.toUpperCase()}
          </div>
          <div>
            <h1 style={{ fontFamily: FONT_DISPLAY, fontSize: 28, color: C.olive, margin: 0 }}>{patient.nome}</h1>
            <p style={{ margin: '2px 0 0', fontSize: 13, color: C.sage }}>{calcAge(patient.dataNascimento)} anos · {patient.telefone} · {patient.convenio || 'Particular'}</p>
          </div>
        </div>
        <div style={{ display: 'flex', gap: 8 }}>
          <Btn size="sm" variant="outline" icon={FileText} onClick={() => setShowDecl(true)}>Declaração</Btn>
          <Btn size="sm" variant="outline" icon={Printer} onClick={() => window.print()}>Imprimir prontuário</Btn>
          <Btn size="sm" variant="outline" icon={Edit2} onClick={() => setEditing(true)}>Editar</Btn>
          <Btn size="sm" variant="danger" icon={Trash2} onClick={() => { if (confirm('Excluir paciente e todos os seus dados?')) { deletePatient(patient.id); back(); } }}>Excluir</Btn>
        </div>
      </div>

      <div className="no-print" style={{ display: 'flex', gap: 6, marginBottom: 18, borderBottom: `1.5px solid #E4DDD1` }}>
        {tabs.map(t => (
          <button key={t.key} onClick={() => setTab(t.key)} style={{
            padding: '10px 16px', background: 'none', border: 'none', cursor: 'pointer', fontFamily: FONT_BODY, fontWeight: 600, fontSize: 13.5,
            color: tab === t.key ? C.olive : C.sage, borderBottom: tab === t.key ? `2.5px solid ${C.gold}` : '2.5px solid transparent', marginBottom: -1.5,
          }}>{t.label}</button>
        ))}
      </div>

      {tab === 'dados' && (
        <div className="print-area" style={{ display: 'grid', gridTemplateColumns: '1fr 1fr', gap: 18 }}>
          <Card>
            <SectionTitle>Dados pessoais</SectionTitle>
            <DL items={[['CPF', patient.cpf], ['RG', patient.rg], ['Nascimento', fmtDate(patient.dataNascimento)], ['Sexo', patient.sexo],
            ['WhatsApp', patient.whatsapp], ['E-mail', patient.email], ['Endereço', patient.endereco], ['Profissão', patient.profissao],
            ['Convênio', patient.convenio], ['Carteirinha', patient.carteirinha], ['Responsável', patient.responsavel]]} />
          </Card>
          <Card>
            <SectionTitle>Dados clínicos</SectionTitle>
            <DL items={[['Diagnóstico', patient.diagnostico], ['CID', patient.cid], ['Médico solicitante', patient.medicoSolicitante],
            ['Queixa principal', patient.queixaPrincipal], ['HDA', patient.hda], ['HP', patient.hp], ['Medicamentos', patient.medicamentos],
            ['Cirurgias', patient.cirurgias], ['Alergias', patient.alergias]]} />
          </Card>
        </div>
      )}

      {tab === 'avaliacoes' && (
        <div>
          <div className="no-print" style={{ display: 'flex', flexWrap: 'wrap', gap: 8, marginBottom: 16 }}>
            {Object.entries(EVAL_TYPES).map(([key, cfg]) => (
              <Btn key={key} size="sm" variant="outline" icon={Plus} onClick={() => setEvalFormType(key)}>Nova {cfg.label}</Btn>
            ))}
          </div>
          {patEvals.length === 0 && <p style={{ color: C.sage }}>Nenhuma avaliação registrada.</p>}
          <div style={{ display: 'flex', flexDirection: 'column', gap: 14 }}>
            {patEvals.map(ev => ev.tipo === 'neurologica' ? <GenericEvalView key={ev.id} ev={ev} fieldsConfig={NEURO_FIELDS} />
              : ev.tipo === 'respiratoria' ? <GenericEvalView key={ev.id} ev={ev} fieldsConfig={RESP_FIELDS} />
              : ev.tipo === 'gerontologia' ? <GenericEvalView key={ev.id} ev={ev} fieldsConfig={GERO_FIELDS} />
              : <OrthoEvalView key={ev.id} ev={ev} />)}
          </div>
          {evalFormType === 'ortopedica' && <OrthoEvalForm patient={patient} onClose={() => setEvalFormType(null)} onSave={ev => { saveEvaluation({ ...ev, tipo: 'ortopedica' }); setEvalFormType(null); }} />}
          {evalFormType === 'neurologica' && <GenericEvalForm tipo="neurologica" fieldsConfig={NEURO_FIELDS} scalesConfig={NEURO_SCALES} patient={patient} onClose={() => setEvalFormType(null)} onSave={ev => { saveEvaluation(ev); setEvalFormType(null); }} />}
          {evalFormType === 'respiratoria' && <GenericEvalForm tipo="respiratoria" fieldsConfig={RESP_FIELDS} scalesConfig={RESP_SCALES} patient={patient} onClose={() => setEvalFormType(null)} onSave={ev => { saveEvaluation(ev); setEvalFormType(null); }} />}
          {evalFormType === 'gerontologia' && <GenericEvalForm tipo="gerontologia" fieldsConfig={GERO_FIELDS} scalesConfig={GERO_SCALES} patient={patient} onClose={() => setEvalFormType(null)} onSave={ev => { saveEvaluation(ev); setEvalFormType(null); }} />}
        </div>
      )}

      {tab === 'evolucoes' && (
        <EvolutionsTab patient={patient} evolutions={patEvolutions} save={saveEvolution} del={deleteEvolution} />
      )}

      {tab === 'atendimentos' && (
        <Card>
          <SectionTitle>Controle de atendimentos</SectionTitle>
          <p style={{ fontSize: 15, color: C.ink, marginBottom: 14 }}>Paciente realizou <b>{realizados.length}</b> atendimento(s).</p>
          <table style={{ width: '100%', borderCollapse: 'collapse', fontSize: 13.5 }}>
            <thead><tr style={{ textAlign: 'left', color: C.sage, fontSize: 11.5, textTransform: 'uppercase' }}>
              <th style={{ padding: 8 }}>Data</th><th>Horário</th><th>Status</th><th>Observações</th>
            </tr></thead>
            <tbody>
              {patAppointments.map(a => (
                <tr key={a.id} style={{ borderTop: '1px solid #EFEAE1' }}>
                  <td style={{ padding: 8 }}>{fmtDate(a.date)}</td><td>{a.time}</td>
                  <td><Badge color={STATUS_COLOR[a.status]}>{a.status}</Badge></td><td>{a.observacoes}</td>
                </tr>
              ))}
              {patAppointments.length === 0 && <tr><td colSpan={4} style={{ padding: 8, color: C.sage }}>Nenhum atendimento registrado.</td></tr>}
            </tbody>
          </table>
        </Card>
      )}

      {showDecl && <DeclarationModal patient={patient} onClose={() => setShowDecl(false)} />}
      {editing && <PatientFormModal initial={patient} onClose={() => setEditing(false)} onSave={p => { updatePatient(p); setEditing(false); }} />}
    </div>
  );
}

function DL({ items }) {
  return (
    <div style={{ display: 'flex', flexDirection: 'column', gap: 9 }}>
      {items.filter(([, v]) => v).map(([k, v]) => (
        <div key={k}><span style={{ fontSize: 11.5, fontWeight: 700, color: C.sage, textTransform: 'uppercase', letterSpacing: 0.4 }}>{k}</span>
          <div style={{ fontSize: 14, color: C.ink }}>{v}</div></div>
      ))}
      {items.every(([, v]) => !v) && <p style={{ color: C.sage, fontSize: 13 }}>Sem informações preenchidas.</p>}
    </div>
  );
}

/* ---------- Evoluções ---------- */
function EvolutionsTab({ patient, evolutions, save, del }) {
  const [form, setForm] = useState(null);

  const startNew = (copyPrev) => {
    const prev = evolutions[0];
    setForm({
      id: uid(), patientId: patient.id, date: todayISO(), time: nowTime(),
      descricao: copyPrev ? prev?.descricao || '' : '', tecnicas: copyPrev ? prev?.tecnicas || '' : '',
      resposta: copyPrev ? prev?.resposta || '' : '', conduta: copyPrev ? prev?.conduta || '' : '', observacoes: '',
    });
  };

  return (
    <div>
      <div className="no-print" style={{ display: 'flex', justifyContent: 'flex-end', gap: 8, marginBottom: 12 }}>
        {evolutions[0] && <Btn variant="outline" icon={Copy} onClick={() => startNew(true)}>Copiar última evolução</Btn>}
        <Btn icon={Plus} onClick={() => startNew(false)}>Nova evolução</Btn>
      </div>
      {form && (
        <Card style={{ marginBottom: 16 }}>
          <SectionTitle sub={`${fmtDate(form.date)} às ${form.time}`}>Nova evolução</SectionTitle>
          <div style={{ display: 'flex', flexDirection: 'column', gap: 12 }}>
            <Field label="Descrição do atendimento"><TextArea value={form.descricao} onChange={e => setForm({ ...form, descricao: e.target.value })} /></Field>
            <Field label="Técnicas realizadas"><TextArea value={form.tecnicas} onChange={e => setForm({ ...form, tecnicas: e.target.value })} /></Field>
            <Field label="Resposta do paciente"><TextArea value={form.resposta} onChange={e => setForm({ ...form, resposta: e.target.value })} /></Field>
            <Field label="Conduta"><TextArea value={form.conduta} onChange={e => setForm({ ...form, conduta: e.target.value })} /></Field>
            <Field label="Observações"><TextArea value={form.observacoes} onChange={e => setForm({ ...form, observacoes: e.target.value })} /></Field>
            <div style={{ display: 'flex', justifyContent: 'flex-end', gap: 8 }}>
              <Btn variant="outline" onClick={() => setForm(null)}>Cancelar</Btn>
              <Btn icon={Save} onClick={() => { save(form); setForm(null); }}>Salvar evolução</Btn>
            </div>
          </div>
        </Card>
      )}
      <div style={{ display: 'flex', flexDirection: 'column', gap: 12 }}>
        {evolutions.map(e => (
          <Card key={e.id}>
            <div style={{ display: 'flex', justifyContent: 'space-between' }}>
              <b style={{ color: C.olive, fontFamily: FONT_DISPLAY, fontSize: 18 }}>{fmtDate(e.date)} · {e.time}</b>
              <button className="no-print" onClick={() => { if (confirm('Excluir esta evolução?')) del(e.id); }} style={{ background: 'none', border: 'none', color: '#B5645A', cursor: 'pointer' }}><Trash2 size={15} /></button>
            </div>
            <div style={{ fontSize: 13.5, marginTop: 8, display: 'flex', flexDirection: 'column', gap: 6 }}>
              {e.descricao && <div><b>Descrição:</b> {e.descricao}</div>}
              {e.tecnicas && <div><b>Técnicas:</b> {e.tecnicas}</div>}
              {e.resposta && <div><b>Resposta do paciente:</b> {e.resposta}</div>}
              {e.conduta && <div><b>Conduta:</b> {e.conduta}</div>}
              {e.observacoes && <div><b>Observações:</b> {e.observacoes}</div>}
            </div>
          </Card>
        ))}
        {evolutions.length === 0 && !form && <p style={{ color: C.sage }}>Nenhuma evolução registrada ainda.</p>}
      </div>
    </div>
  );
}

/* ---------- Avaliação Ortopédica ---------- */
function OrthoEvalForm({ patient, onClose, onSave }) {
  const [f, setF] = useState({
    id: uid(), patientId: patient.id, date: todayISO(),
    anamnese: '', inspecao: '', palpacao: '', evaDor: 5,
    admRows: [{ articulacao: '', movimento: '', ativo: '', passivo: '' }],
    forcaRows: [{ musculo: '', grau: OXFORD_SCALE[5] }],
    specialTests: {}, planoTerapeutico: '',
  });
  const setTest = (joint, test, val) => setF(prev => ({ ...prev, specialTests: { ...prev.specialTests, [joint]: { ...(prev.specialTests[joint] || {}), [test]: val } } }));

  const addRow = (key, blank) => setF(prev => ({ ...prev, [key]: [...prev[key], blank] }));
  const setRow = (key, i, field, val) => setF(prev => { const rows = [...prev[key]]; rows[i] = { ...rows[i], [field]: val }; return { ...prev, [key]: rows }; });
  const delRow = (key, i) => setF(prev => ({ ...prev, [key]: prev[key].filter((_, idx) => idx !== i) }));

  return (
    <Modal title="Avaliação Ortopédica" onClose={onClose} width={860}>
      <div style={{ display: 'flex', flexDirection: 'column', gap: 16 }}>
        <Field label="Data"><Input type="date" value={f.date} onChange={e => setF({ ...f, date: e.target.value })} style={{ maxWidth: 200 }} /></Field>
        <Field label="Anamnese" full><TextArea value={f.anamnese} onChange={e => setF({ ...f, anamnese: e.target.value })} /></Field>
        <Field label="Inspeção" full><TextArea value={f.inspecao} onChange={e => setF({ ...f, inspecao: e.target.value })} /></Field>
        <Field label="Palpação" full><TextArea value={f.palpacao} onChange={e => setF({ ...f, palpacao: e.target.value })} /></Field>
        <Field label={`Dor (EVA): ${f.evaDor}/10`} full><input type="range" min={0} max={10} value={f.evaDor} onChange={e => setF({ ...f, evaDor: Number(e.target.value) })} /></Field>

        <div>
          <SectionTitle sub="Amplitude de movimento / Goniometria">ADM e Goniometria</SectionTitle>
          {f.admRows.map((r, i) => (
            <div key={i} style={{ display: 'grid', gridTemplateColumns: '1fr 1fr 1fr 1fr auto', gap: 8, marginBottom: 8 }}>
              <Input placeholder="Articulação" value={r.articulacao} onChange={e => setRow('admRows', i, 'articulacao', e.target.value)} />
              <Input placeholder="Movimento" value={r.movimento} onChange={e => setRow('admRows', i, 'movimento', e.target.value)} />
              <Input placeholder="Ativo (°)" value={r.ativo} onChange={e => setRow('admRows', i, 'ativo', e.target.value)} />
              <Input placeholder="Passivo (°)" value={r.passivo} onChange={e => setRow('admRows', i, 'passivo', e.target.value)} />
              <button onClick={() => delRow('admRows', i)} style={{ background: 'none', border: 'none', color: '#B5645A', cursor: 'pointer' }}><X size={16} /></button>
            </div>
          ))}
          <Btn size="sm" variant="outline" icon={Plus} onClick={() => addRow('admRows', { articulacao: '', movimento: '', ativo: '', passivo: '' })}>Adicionar linha</Btn>
        </div>

        <div>
          <SectionTitle sub="Escala Oxford (0–5)">Força muscular</SectionTitle>
          {f.forcaRows.map((r, i) => (
            <div key={i} style={{ display: 'grid', gridTemplateColumns: '1fr 1fr auto', gap: 8, marginBottom: 8 }}>
              <Input placeholder="Músculo / grupo muscular" value={r.musculo} onChange={e => setRow('forcaRows', i, 'musculo', e.target.value)} />
              <Select value={r.grau} onChange={e => setRow('forcaRows', i, 'grau', e.target.value)}>{OXFORD_SCALE.map(o => <option key={o}>{o}</option>)}</Select>
              <button onClick={() => delRow('forcaRows', i)} style={{ background: 'none', border: 'none', color: '#B5645A', cursor: 'pointer' }}><X size={16} /></button>
            </div>
          ))}
          <Btn size="sm" variant="outline" icon={Plus} onClick={() => addRow('forcaRows', { musculo: '', grau: OXFORD_SCALE[5] })}>Adicionar linha</Btn>
        </div>

        <div>
          <SectionTitle sub="Marque o resultado de cada teste por articulação">Testes especiais</SectionTitle>
          {Object.entries(ORTHO_TESTS).map(([joint, tests]) => (
            <details key={joint} style={{ marginBottom: 8, background: '#fff', borderRadius: 10, border: '1px solid #EFEAE1', padding: '10px 14px' }}>
              <summary style={{ cursor: 'pointer', fontWeight: 600, color: C.olive, fontFamily: FONT_BODY }}>{joint}</summary>
              <div style={{ marginTop: 10, display: 'flex', flexDirection: 'column', gap: 8 }}>
                {tests.map(t => {
                  const val = f.specialTests[joint]?.[t] || '';
                  return (
                    <div key={t} style={{ display: 'flex', justifyContent: 'space-between', alignItems: 'center', gap: 10, flexWrap: 'wrap' }}>
                      <span style={{ fontSize: 13.5 }}>{t}</span>
                      <div style={{ display: 'flex', gap: 6 }}>
                        {[['positivo', 'Positivo', '#B5645A'], ['negativo', 'Negativo', C.sage], ['nao_realizado', 'N/R', '#B8AF9E']].map(([v, lab, col]) => (
                          <button key={v} onClick={() => setTest(joint, t, v)} style={{
                            fontSize: 11.5, padding: '4px 9px', borderRadius: 20, cursor: 'pointer', fontWeight: 700,
                            border: `1.5px solid ${col}`, background: val === v ? col : 'transparent', color: val === v ? '#fff' : col,
                          }}>{lab}</button>
                        ))}
                      </div>
                    </div>
                  );
                })}
              </div>
            </details>
          ))}
        </div>

        <Field label="Plano terapêutico" full><TextArea value={f.planoTerapeutico} onChange={e => setF({ ...f, planoTerapeutico: e.target.value })} /></Field>

        <div style={{ display: 'flex', justifyContent: 'flex-end', gap: 10 }}>
          <Btn variant="outline" onClick={onClose}>Cancelar</Btn>
          <Btn icon={Save} onClick={() => onSave(f)}>Salvar avaliação</Btn>
        </div>
      </div>
    </Modal>
  );
}

function OrthoEvalView({ ev }) {
  const positives = [];
  Object.entries(ev.specialTests || {}).forEach(([joint, tests]) => Object.entries(tests).forEach(([t, v]) => { if (v === 'positivo') positives.push(`${t} (${joint})`); }));
  return (
    <Card>
      <b style={{ fontFamily: FONT_DISPLAY, color: C.olive, fontSize: 19 }}>Avaliação Ortopédica — {fmtDate(ev.date)}</b>
      <div style={{ fontSize: 13.5, marginTop: 8, display: 'flex', flexDirection: 'column', gap: 6 }}>
        {ev.anamnese && <div><b>Anamnese:</b> {ev.anamnese}</div>}
        {ev.inspecao && <div><b>Inspeção:</b> {ev.inspecao}</div>}
        {ev.palpacao && <div><b>Palpação:</b> {ev.palpacao}</div>}
        <div><b>EVA dor:</b> {ev.evaDor}/10</div>
        {positives.length > 0 && <div><b>Testes positivos:</b> {positives.join(', ')}</div>}
        {ev.planoTerapeutico && <div><b>Plano terapêutico:</b> {ev.planoTerapeutico}</div>}
      </div>
    </Card>
  );
}

/* ---------- Avaliações Neurológica / Respiratória / Gerontologia (formulário genérico) ---------- */
function GenericEvalForm({ tipo, fieldsConfig, scalesConfig, patient, onClose, onSave }) {
  const initFields = {}; fieldsConfig.forEach(([k]) => { initFields[k] = ''; });
  const initScales = {}; scalesConfig.forEach(s => { initScales[s] = ''; });
  const [f, setF] = useState({ id: uid(), patientId: patient.id, tipo, date: todayISO(), fields: initFields, scales: initScales, planoTerapeutico: '' });
  const setField = (k, v) => setF(prev => ({ ...prev, fields: { ...prev.fields, [k]: v } }));
  const setScale = (s, v) => setF(prev => ({ ...prev, scales: { ...prev.scales, [s]: v } }));

  return (
    <Modal title={`Avaliação ${EVAL_TYPES[tipo].label}`} onClose={onClose} width={780}>
      <div style={{ display: 'flex', flexDirection: 'column', gap: 14 }}>
        <Field label="Data"><Input type="date" value={f.date} onChange={e => setF({ ...f, date: e.target.value })} style={{ maxWidth: 200 }} /></Field>
        {fieldsConfig.map(([k, label]) => (
          <Field key={k} label={label} full><TextArea value={f.fields[k]} onChange={e => setField(k, e.target.value)} /></Field>
        ))}
        <div>
          <SectionTitle sub="Registre o resultado ou pontuação de cada escala/teste aplicado">Escalas e testes</SectionTitle>
          <div style={{ display: 'grid', gridTemplateColumns: '1fr 1fr', gap: 10 }}>
            {scalesConfig.map(s => (
              <Field key={s} label={s}><Input value={f.scales[s]} onChange={e => setScale(s, e.target.value)} placeholder="Resultado / pontuação" /></Field>
            ))}
          </div>
        </div>
        <Field label="Plano terapêutico" full><TextArea value={f.planoTerapeutico} onChange={e => setF({ ...f, planoTerapeutico: e.target.value })} /></Field>
        <div style={{ display: 'flex', justifyContent: 'flex-end', gap: 10 }}>
          <Btn variant="outline" onClick={onClose}>Cancelar</Btn>
          <Btn icon={Save} onClick={() => onSave(f)}>Salvar avaliação</Btn>
        </div>
      </div>
    </Modal>
  );
}

function GenericEvalView({ ev, fieldsConfig }) {
  const filledFields = fieldsConfig.filter(([k]) => ev.fields?.[k]);
  const filledScales = Object.entries(ev.scales || {}).filter(([, v]) => v);
  return (
    <Card>
      <div style={{ display: 'flex', alignItems: 'center', gap: 8 }}>
        <Badge color={EVAL_TYPES[ev.tipo].color}>{EVAL_TYPES[ev.tipo].label}</Badge>
        <b style={{ fontFamily: FONT_DISPLAY, color: C.olive, fontSize: 19 }}>{fmtDate(ev.date)}</b>
      </div>
      <div style={{ fontSize: 13.5, marginTop: 8, display: 'flex', flexDirection: 'column', gap: 6 }}>
        {filledFields.map(([k, label]) => <div key={k}><b>{label}:</b> {ev.fields[k]}</div>)}
        {filledScales.length > 0 && <div><b>Escalas/testes:</b> {filledScales.map(([s, v]) => `${s}: ${v}`).join(' · ')}</div>}
        {ev.planoTerapeutico && <div><b>Plano terapêutico:</b> {ev.planoTerapeutico}</div>}
      </div>
    </Card>
  );
}

/* ---------- Declaração de Comparecimento ---------- */
function DeclarationModal({ patient, onClose }) {
  const [d, setD] = useState({ date: todayISO(), time: nowTime(), duracao: '50 minutos' });
  return (
    <Modal title="Declaração de Comparecimento" onClose={onClose} width={560}>
      <div className="no-print" style={{ display: 'flex', flexDirection: 'column', gap: 12, marginBottom: 18 }}>
        <Field label="Data"><Input type="date" value={d.date} onChange={e => setD({ ...d, date: e.target.value })} /></Field>
        <Field label="Horário"><Input type="time" value={d.time} onChange={e => setD({ ...d, time: e.target.value })} /></Field>
        <Field label="Duração"><Input value={d.duracao} onChange={e => setD({ ...d, duracao: e.target.value })} /></Field>
        <Btn icon={Printer} onClick={() => window.print()}>Gerar / Imprimir PDF</Btn>
      </div>
      <div className="print-area" style={{ background: '#fff', padding: 28, borderRadius: 10, border: `1px solid ${C.tan}`, fontFamily: FONT_BODY }}>
        <div style={{ textAlign: 'center', marginBottom: 24 }}>
          <div style={{ fontFamily: FONT_DISPLAY, fontSize: 24, color: C.olive, fontWeight: 700 }}>Nathalia Emilly</div>
          <div style={{ fontSize: 11, letterSpacing: 2, color: C.gold }}>FISIOTERAPEUTA</div>
        </div>
        <h4 style={{ textAlign: 'center', color: C.olive, fontFamily: FONT_DISPLAY, fontSize: 20 }}>DECLARAÇÃO DE COMPARECIMENTO</h4>
        <p style={{ fontSize: 14.5, lineHeight: 1.8, color: C.ink }}>
          Declaro para os devidos fins que o(a) paciente <b>{patient.nome}</b>, CPF {patient.cpf || '—'}, compareceu a este
          consultório de fisioterapia no dia <b>{fmtDate(d.date)}</b>, às <b>{d.time}</b>, com duração de <b>{d.duracao}</b>,
          para atendimento fisioterapêutico.
        </p>
        <p style={{ marginTop: 40, textAlign: 'center', fontSize: 14 }}>_______________________________</p>
        <p style={{ textAlign: 'center', fontSize: 13, color: C.sage }}>Dra. Nathalia Emilly — Fisioterapeuta</p>
      </div>
    </Modal>
  );
}

/* ============================= AGENDA ============================= */
function Agenda({ patients, appointments, saveAppointment, deleteAppointment, goPatient }) {
  const [mode, setMode] = useState('semana');
  const [ref, setRef] = useState(new Date());
  const [modal, setModal] = useState(null);

  const shift = (n) => {
    const d = new Date(ref);
    if (mode === 'dia') d.setDate(d.getDate() + n);
    else if (mode === 'semana') d.setDate(d.getDate() + n * 7);
    else d.setMonth(d.getMonth() + n);
    setRef(d);
  };

  const dayList = (date) => appointments.filter(a => a.date === date.toISOString().slice(0, 10)).sort((a, b) => a.time.localeCompare(b.time));

  return (
    <div className="fade-in">
      <div style={{ display: 'flex', justifyContent: 'space-between', alignItems: 'center', marginBottom: 18, flexWrap: 'wrap', gap: 10 }}>
        <h1 style={{ fontFamily: FONT_DISPLAY, fontSize: 30, color: C.olive, margin: 0 }}>Agenda</h1>
        <div style={{ display: 'flex', gap: 8, alignItems: 'center' }}>
          <div style={{ display: 'flex', background: '#fff', borderRadius: 10, border: '1px solid #E4DDD1', overflow: 'hidden' }}>
            {['dia', 'semana', 'mes'].map(m => (
              <button key={m} onClick={() => setMode(m)} style={{ padding: '8px 14px', border: 'none', cursor: 'pointer', background: mode === m ? C.olive : 'transparent', color: mode === m ? '#fff' : C.ink, fontFamily: FONT_BODY, fontWeight: 600, fontSize: 12.5, textTransform: 'capitalize' }}>{m}</button>
            ))}
          </div>
          <button onClick={() => shift(-1)} style={{ background: '#fff', border: '1px solid #E4DDD1', borderRadius: 8, cursor: 'pointer', padding: 8 }}><ChevronLeft size={16} /></button>
          <button onClick={() => shift(1)} style={{ background: '#fff', border: '1px solid #E4DDD1', borderRadius: 8, cursor: 'pointer', padding: 8 }}><ChevronRight size={16} /></button>
          <Btn icon={Plus} onClick={() => setModal({ id: uid(), patientId: patients[0]?.id || '', date: ref.toISOString().slice(0, 10), time: '09:00', status: 'confirmado', observacoes: '' })}>Novo</Btn>
        </div>
      </div>

      {mode === 'dia' && (
        <Card>
          <SectionTitle>{ref.toLocaleDateString('pt-BR', { weekday: 'long', day: 'numeric', month: 'long' })}</SectionTitle>
          <AppointmentRows list={dayList(ref)} patients={patients} onEdit={setModal} onDelete={deleteAppointment} goPatient={goPatient} />
        </Card>
      )}

      {mode === 'semana' && (
        <div style={{ display: 'grid', gridTemplateColumns: 'repeat(7, 1fr)', gap: 12 }}>
          {Array.from({ length: 7 }).map((_, i) => {
            const d = new Date(ref); d.setDate(d.getDate() - d.getDay() + i);
            const list = dayList(d);
            return (
              <Card key={i} style={{ padding: 14 }}>
                <div style={{ fontSize: 11.5, color: C.sage, textTransform: 'uppercase', fontWeight: 700 }}>{d.toLocaleDateString('pt-BR', { weekday: 'short' })}</div>
                <div style={{ fontFamily: FONT_DISPLAY, fontSize: 22, color: C.olive, fontWeight: 700, marginBottom: 8 }}>{d.getDate()}</div>
                <div style={{ display: 'flex', flexDirection: 'column', gap: 6 }}>
                  {list.map(a => {
                    const p = patients.find(pp => pp.id === a.patientId);
                    return (
                      <div key={a.id} onClick={() => setModal(a)} style={{ fontSize: 11.5, padding: '5px 7px', borderRadius: 7, background: `${STATUS_COLOR[a.status]}18`, color: STATUS_COLOR[a.status], cursor: 'pointer', fontWeight: 600 }}>
                        {a.time} {p?.nome?.split(' ')[0]}
                      </div>
                    );
                  })}
                </div>
              </Card>
            );
          })}
        </div>
      )}

      {mode === 'mes' && (
        <MonthGrid ref={ref} appointments={appointments} onDayClick={(d) => { setMode('dia'); setRef(d); }} />
      )}

      {modal && <AppointmentModal appt={modal} patients={patients} onClose={() => setModal(null)}
        onSave={a => { saveAppointment(a); setModal(null); }}
        onDelete={() => { deleteAppointment(modal.id); setModal(null); }} />}
    </div>
  );
}

function MonthGrid({ ref: refDate, appointments, onDayClick }) {
  const year = refDate.getFullYear(), month = refDate.getMonth();
  const first = new Date(year, month, 1);
  const startOffset = first.getDay();
  const daysInMonth = new Date(year, month + 1, 0).getDate();
  const cells = [];
  for (let i = 0; i < startOffset; i++) cells.push(null);
  for (let d = 1; d <= daysInMonth; d++) cells.push(new Date(year, month, d));

  return (
    <Card>
      <SectionTitle>{refDate.toLocaleDateString('pt-BR', { month: 'long', year: 'numeric' })}</SectionTitle>
      <div style={{ display: 'grid', gridTemplateColumns: 'repeat(7,1fr)', gap: 6 }}>
        {['D', 'S', 'T', 'Q', 'Q', 'S', 'S'].map((d, i) => <div key={i} style={{ textAlign: 'center', fontSize: 11, color: C.sage, fontWeight: 700 }}>{d}</div>)}
        {cells.map((d, i) => {
          if (!d) return <div key={i} />;
          const iso = d.toISOString().slice(0, 10);
          const count = appointments.filter(a => a.date === iso).length;
          return (
            <div key={i} onClick={() => onDayClick(d)} style={{ cursor: 'pointer', borderRadius: 10, padding: '10px 6px', textAlign: 'center', background: count ? `${C.gold}18` : C.cream, border: iso === todayISO() ? `1.5px solid ${C.gold}` : '1px solid transparent' }}>
              <div style={{ fontSize: 13, fontWeight: 600, color: C.ink }}>{d.getDate()}</div>
              {count > 0 && <div style={{ fontSize: 10, color: C.gold, fontWeight: 700 }}>{count} agend.</div>}
            </div>
          );
        })}
      </div>
    </Card>
  );
}

function AppointmentRows({ list, patients, onEdit, onDelete, goPatient }) {
  if (list.length === 0) return <p style={{ color: C.sage }}>Nenhum atendimento.</p>;
  return (
    <div style={{ display: 'flex', flexDirection: 'column', gap: 8 }}>
      {list.map(a => {
        const p = patients.find(pp => pp.id === a.patientId);
        return (
          <div key={a.id} style={{ display: 'flex', justifyContent: 'space-between', alignItems: 'center', padding: '10px 12px', background: C.cream, borderRadius: 10, flexWrap: 'wrap', gap: 8 }}>
            <div onClick={() => p && goPatient(p.id)} style={{ cursor: 'pointer', display: 'flex', alignItems: 'center', gap: 10 }}>
              <Clock size={14} color={C.sage} /><b>{a.time}</b><span>{p?.nome}</span><Badge color={STATUS_COLOR[a.status]}>{a.status}</Badge>
            </div>
            <div style={{ display: 'flex', gap: 6 }}>
              <button onClick={() => onEdit(a)} style={{ background: 'none', border: 'none', color: C.olive, cursor: 'pointer' }}><Edit2 size={15} /></button>
              <button onClick={() => onDelete(a.id)} style={{ background: 'none', border: 'none', color: '#B5645A', cursor: 'pointer' }}><Trash2 size={15} /></button>
            </div>
          </div>
        );
      })}
    </div>
  );
}

function AppointmentModal({ appt, patients, onClose, onSave, onDelete }) {
  const [f, setF] = useState(appt);
  const repeatNextWeek = () => {
    const d = new Date(f.date + 'T00:00:00'); d.setDate(d.getDate() + 7);
    onSave({ ...f, id: appt.id }); // save current first
    onSave({ ...f, id: uid(), date: d.toISOString().slice(0, 10) });
  };
  return (
    <Modal title={patients.length ? 'Atendimento' : 'Cadastre um paciente primeiro'} onClose={onClose} width={480}>
      {patients.length === 0 ? <p style={{ color: C.sage }}>Cadastre ao menos um paciente antes de criar atendimentos.</p> : (
        <div style={{ display: 'flex', flexDirection: 'column', gap: 12 }}>
          <Field label="Paciente"><Select value={f.patientId} onChange={e => setF({ ...f, patientId: e.target.value })}>{patients.map(p => <option key={p.id} value={p.id}>{p.nome}</option>)}</Select></Field>
          <div style={{ display: 'flex', gap: 12 }}>
            <Field label="Data"><Input type="date" value={f.date} onChange={e => setF({ ...f, date: e.target.value })} /></Field>
            <Field label="Horário"><Input type="time" value={f.time} onChange={e => setF({ ...f, time: e.target.value })} /></Field>
          </div>
          <Field label="Status">
            <Select value={f.status} onChange={e => setF({ ...f, status: e.target.value })}>{STATUS_LIST.map(s => <option key={s} value={s}>{s}</option>)}</Select>
          </Field>
          <Field label="Observações"><TextArea value={f.observacoes} onChange={e => setF({ ...f, observacoes: e.target.value })} /></Field>
          <div style={{ display: 'flex', justifyContent: 'space-between', marginTop: 6, flexWrap: 'wrap', gap: 8 }}>
            <Btn variant="danger" size="sm" icon={Trash2} onClick={onDelete}>Excluir</Btn>
            <div style={{ display: 'flex', gap: 8 }}>
              <Btn variant="outline" size="sm" icon={Copy} onClick={repeatNextWeek}>Repetir +7 dias</Btn>
              <Btn size="sm" icon={Save} onClick={() => onSave(f)}>Salvar</Btn>
            </div>
          </div>
        </div>
      )}
    </Modal>
  );
}

/* ============================= FINANCEIRO ============================= */
function Financeiro({ patients, financial, saveEntry, deleteEntry }) {
  const [showForm, setShowForm] = useState(false);
  const [filter, setFilter] = useState('mes');

  const inRange = (dateStr) => {
    const d = new Date(dateStr + 'T00:00:00'); const now = new Date();
    if (filter === 'dia') return dateStr === todayISO();
    if (filter === 'semana') { const start = new Date(now); start.setDate(now.getDate() - now.getDay()); return d >= start; }
    if (filter === 'mes') return d.getMonth() === now.getMonth() && d.getFullYear() === now.getFullYear();
    if (filter === 'ano') return d.getFullYear() === now.getFullYear();
    return true;
  };
  const filtered = financial.filter(f => inRange(f.data)).sort((a, b) => b.data.localeCompare(a.data));
  const total = filtered.reduce((s, f) => s + Number(f.valor || 0), 0);
  const byMethod = PAY_METHODS.map(m => ({ m, v: filtered.filter(f => f.formaPagamento === m).reduce((s, f) => s + Number(f.valor || 0), 0) }));

  const exportCSV = () => {
    const rows = [['Data', 'Paciente', 'Valor', 'Forma de pagamento'], ...filtered.map(f => {
      const p = patients.find(pp => pp.id === f.patientId);
      return [fmtDate(f.data), p?.nome || '', f.valor, f.formaPagamento];
    })];
    const csv = rows.map(r => r.join(';')).join('\n');
    const blob = new Blob([csv], { type: 'text/csv;charset=utf-8;' });
    const url = URL.createObjectURL(blob);
    const a = document.createElement('a'); a.href = url; a.download = `financeiro_${filter}.csv`; a.click();
  };

  return (
    <div className="fade-in">
      <div style={{ display: 'flex', justifyContent: 'space-between', alignItems: 'center', marginBottom: 18, flexWrap: 'wrap', gap: 10 }}>
        <h1 style={{ fontFamily: FONT_DISPLAY, fontSize: 30, color: C.olive, margin: 0 }}>Financeiro</h1>
        <div style={{ display: 'flex', gap: 8 }}>
          <Select value={filter} onChange={e => setFilter(e.target.value)} style={{ width: 140 }}>
            <option value="dia">Hoje</option><option value="semana">Semana</option><option value="mes">Mês</option><option value="ano">Ano</option>
          </Select>
          <Btn variant="outline" onClick={exportCSV}>Exportar CSV</Btn>
          <Btn variant="outline" icon={Printer} onClick={() => window.print()}>Exportar PDF</Btn>
          <Btn icon={Plus} onClick={() => setShowForm(true)}>Lançar recebimento</Btn>
        </div>
      </div>

      <div style={{ display: 'flex', gap: 16, flexWrap: 'wrap', marginBottom: 18 }}>
        <Card style={{ flex: 1, minWidth: 200 }}>
          <div style={{ fontSize: 12, color: C.sage, fontWeight: 600, textTransform: 'uppercase' }}>Faturamento no período</div>
          <div style={{ fontFamily: FONT_DISPLAY, fontSize: 32, color: C.olive, fontWeight: 700 }}>R$ {total.toFixed(2).replace('.', ',')}</div>
        </Card>
        {byMethod.map(({ m, v }) => (
          <Card key={m} style={{ flex: 1, minWidth: 150 }}>
            <div style={{ fontSize: 12, color: C.sage, fontWeight: 600 }}>{m}</div>
            <div style={{ fontFamily: FONT_DISPLAY, fontSize: 22, color: C.gold, fontWeight: 700 }}>R$ {v.toFixed(2).replace('.', ',')}</div>
          </Card>
        ))}
      </div>

      <Card className="print-area">
        <SectionTitle>Lançamentos</SectionTitle>
        <table style={{ width: '100%', borderCollapse: 'collapse', fontSize: 13.5 }}>
          <thead><tr style={{ textAlign: 'left', color: C.sage, fontSize: 11.5, textTransform: 'uppercase' }}>
            <th style={{ padding: 8 }}>Data</th><th>Paciente</th><th>Valor</th><th>Forma</th><th className="no-print"></th>
          </tr></thead>
          <tbody>
            {filtered.map(f => {
              const p = patients.find(pp => pp.id === f.patientId);
              return (
                <tr key={f.id} style={{ borderTop: '1px solid #EFEAE1' }}>
                  <td style={{ padding: 8 }}>{fmtDate(f.data)}</td><td>{p?.nome || '—'}</td>
                  <td>R$ {Number(f.valor).toFixed(2).replace('.', ',')}</td><td>{f.formaPagamento}</td>
                  <td className="no-print"><button onClick={() => deleteEntry(f.id)} style={{ background: 'none', border: 'none', color: '#B5645A', cursor: 'pointer' }}><Trash2 size={14} /></button></td>
                </tr>
              );
            })}
            {filtered.length === 0 && <tr><td colSpan={5} style={{ padding: 10, color: C.sage }}>Nenhum lançamento no período.</td></tr>}
          </tbody>
        </table>
      </Card>

      {showForm && <EntryModal patients={patients} onClose={() => setShowForm(false)} onSave={e => { saveEntry(e); setShowForm(false); }} />}
    </div>
  );
}

function EntryModal({ patients, onClose, onSave }) {
  const [f, setF] = useState({ id: uid(), patientId: patients[0]?.id || '', valor: '', formaPagamento: 'Pix', data: todayISO() });
  return (
    <Modal title="Lançar recebimento" onClose={onClose} width={440}>
      <div style={{ display: 'flex', flexDirection: 'column', gap: 12 }}>
        <Field label="Paciente"><Select value={f.patientId} onChange={e => setF({ ...f, patientId: e.target.value })}>{patients.map(p => <option key={p.id} value={p.id}>{p.nome}</option>)}</Select></Field>
        <Field label="Valor (R$)"><Input type="number" step="0.01" value={f.valor} onChange={e => setF({ ...f, valor: e.target.value })} /></Field>
        <Field label="Forma de pagamento"><Select value={f.formaPagamento} onChange={e => setF({ ...f, formaPagamento: e.target.value })}>{PAY_METHODS.map(m => <option key={m}>{m}</option>)}</Select></Field>
        <Field label="Data"><Input type="date" value={f.data} onChange={e => setF({ ...f, data: e.target.value })} /></Field>
        <div style={{ display: 'flex', justifyContent: 'flex-end', gap: 8 }}>
          <Btn variant="outline" onClick={onClose}>Cancelar</Btn>
          <Btn icon={Save} onClick={() => { if (!f.patientId || !f.valor) { alert('Selecione o paciente e informe o valor.'); return; } onSave(f); }}>Salvar</Btn>
        </div>
      </div>
    </Modal>
  );
}

/* ============================= APP ROOT ============================= */
export default function App() {
  const [loading, setLoading] = useState(true);
  const [auth, setAuth] = useState(null);
  const [authed, setAuthed] = useState(false);
  const [view, setView] = useState('dashboard');
  const [selectedPatient, setSelectedPatient] = useState(null);
  const [patientTab, setPatientTab] = useState('dados');
  const [searchTerm, setSearchTerm] = useState('');

  const [patients, setPatients] = useState([]);
  const [appointments, setAppointments] = useState([]);
  const [evolutions, setEvolutions] = useState([]);
  const [evaluations, setEvaluations] = useState([]);
  const [financial, setFinancial] = useState([]);
  const [profilePhoto, setProfilePhoto] = useState(null);

  const loadUserData = async (userId) => {
    const [pats, appts, evols, evals, finances, photo] = await Promise.all([
      dbLoad('patients'),
      dbLoad('appointments'),
      dbLoad('evolutions'),
      dbLoad('evaluations'),
      dbLoad('financial'),
      dbLoadProfile(userId),
    ]);
    setPatients(pats);
    setAppointments(appts);
    setEvolutions(evols);
    setEvaluations(evals);
    setFinancial(finances);
    setProfilePhoto(photo);
  };

  const changePhoto = async (file) => {
    if (!auth?.id) return;
    try {
      const dataUrl = await resizePhoto(file);
      await dbSaveProfile(auth.id, dataUrl);
      setProfilePhoto(dataUrl);
    } catch (e) {
      console.error('Erro ao salvar foto de perfil', e);
      alert(`Não foi possível salvar a foto: ${e.message || e}`);
    }
  };

  useEffect(() => {
    let mounted = true;

    const init = async () => {
      try {
        const { data: { session } } = await supabase.auth.getSession();
        if (!mounted) return;

        if (session?.user) {
          setAuth(session.user);
          setAuthed(true);
          await loadUserData(session.user.id);
        } else {
          setAuth(null);
          setAuthed(false);
        }
      } catch (e) {
        console.error('Erro ao inicializar Supabase', e);
        alert(`Não foi possível carregar os dados do Supabase. Verifique se as tabelas/RLS foram criadas.\\n\\n${e.message || e}`);
      } finally {
        if (mounted) setLoading(false);
      }
    };

    init();

    const { data: { subscription } } = supabase.auth.onAuthStateChange((event, session) => {
      if (!mounted) return;
      setAuth(session?.user || null);
      setAuthed(!!session?.user);
      if (event === 'SIGNED_OUT') {
        setPatients([]);
        setAppointments([]);
        setEvolutions([]);
        setEvaluations([]);
        setFinancial([]);
        setProfilePhoto(null);
        setSelectedPatient(null);
      }
    });

    return () => {
      mounted = false;
      subscription.unsubscribe();
    };
  }, []);

  const upsert = async (key, list, item, setter) => {
    if (!auth?.id) return;
    try {
      await dbUpsert(key, item, auth.id);
      const exists = list.some(x => x.id === item.id);
      const next = exists ? list.map(x => x.id === item.id ? item : x) : [...list, item];
      setter(next);
    } catch (e) {
      console.error(`Erro ao salvar ${key}`, e);
      alert(`Não foi possível salvar.\\n\\n${e.message || e}`);
    }
  };

  const remove = async (key, list, id, setter) => {
    try {
      await dbRemove(key, id);
      setter(list.filter(x => x.id !== id));
    } catch (e) {
      console.error(`Erro ao excluir ${key}`, e);
      alert(`Não foi possível excluir.\\n\\n${e.message || e}`);
    }
  };

  const handleLogin = async (email, password) => {
    const { data, error } = await supabase.auth.signInWithPassword({ email, password });
    if (error) return { error: error.message };
    try {
      await loadUserData(data.user.id);
      setAuth(data.user);
      setAuthed(true);
      return {};
    } catch (e) {
      await supabase.auth.signOut();
      return { error: `Login realizado, mas não foi possível carregar seus dados: ${e.message || e}` };
    }
  };

  const handleSignUp = async (email, password) => {
    const { data, error } = await supabase.auth.signUp({
      email,
      password,
      options: { data: { nome: 'Nathalia Emilly' } },
    });
    if (error) return { error: error.message };

    if (data.session?.user) {
      try {
        await loadUserData(data.session.user.id);
        setAuth(data.session.user);
        setAuthed(true);
        return {};
      } catch (e) {
        return { error: `Conta criada, mas houve um erro ao preparar seus dados: ${e.message || e}` };
      }
    }

    return { needsEmailConfirmation: true };
  };

  const handleResetPassword = async (email) => {
    const redirectTo = window.location.origin + window.location.pathname;
    const { error } = await supabase.auth.resetPasswordForEmail(email, { redirectTo });
    return error ? { error: error.message } : {};
  };

  if (loading) return <div style={{ minHeight: '100vh', display: 'flex', alignItems: 'center', justifyContent: 'center', background: C.cream, fontFamily: FONT_BODY, color: C.olive }}><GlobalStyle />Carregando...</div>;

  if (!authed) {
    return (
      <>
        <GlobalStyle />
        <LoginScreen
          auth={auth}
          photo={profilePhoto}
          onChangePhoto={changePhoto}
          onLogin={handleLogin}
          onSignUp={handleSignUp}
          onResetPassword={handleResetPassword}
        />
      </>
    );
  }

  const goPatient = (id, tab) => { setSelectedPatient(id); setPatientTab(tab || 'dados'); setView('patientDetail'); };
  const patient = patients.find(p => p.id === selectedPatient);

  return (
    <div style={{ display: 'flex', fontFamily: FONT_BODY, background: C.cream, minHeight: '100vh', color: C.ink }}>
      <GlobalStyle />
      <Sidebar view={view} setView={(v) => { setView(v); setSelectedPatient(null); }} searchTerm={searchTerm} onSearch={(t) => { setSearchTerm(t); setView('patients'); }}
        photo={profilePhoto} onChangePhoto={changePhoto}
        onLogout={async () => { await supabase.auth.signOut(); }} />
      <div style={{ flex: 1, padding: '28px 34px', overflowX: 'hidden' }}>
        {view === 'dashboard' && <Dashboard patients={patients} appointments={appointments} evolutions={evolutions} goPatient={goPatient} setView={setView} />}
        {view === 'patients' && <PatientsList patients={patients} searchTerm={searchTerm}
          goPatient={goPatient} addPatient={(p) => upsert('patients', patients, p, setPatients)} />}
        {view === 'patientDetail' && patient && (
          <PatientDetail patient={patient} patients={patients}
            updatePatient={(p) => upsert('patients', patients, p, setPatients)}
            deletePatient={(id) => remove('patients', patients, id, setPatients)}
            appointments={appointments} evolutions={evolutions}
            saveEvolution={(e) => upsert('evolutions', evolutions, e, setEvolutions)}
            deleteEvolution={(id) => remove('evolutions', evolutions, id, setEvolutions)}
            evaluations={evaluations} saveEvaluation={(e) => upsert('evaluations', evaluations, e, setEvaluations)}
            back={() => setView('patients')} initialTab={patientTab} />
        )}
        {view === 'agenda' && <Agenda patients={patients} appointments={appointments}
          saveAppointment={(a) => upsert('appointments', appointments, a, setAppointments)}
          deleteAppointment={(id) => remove('appointments', appointments, id, setAppointments)} goPatient={goPatient} />}
        {view === 'financeiro' && <Financeiro patients={patients} financial={financial}
          saveEntry={(e) => upsert('financial', financial, e, setFinancial)}
          deleteEntry={(id) => remove('financial', financial, id, setFinancial)} />}
      </div>
    </div>
  );
}


