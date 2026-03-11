import { useState, useEffect, useRef } from "react";

// ─── PALETTE ───────────────────────────────────────────────────────
const C = {
  bg:"#FDF0F3", surf:"#FFFFFF", surfDim:"#FAE4EA", ink:"#2C1A1F",
  sub:"#7A4F5A", dim:"#C49AAA", line:"#F2C9D4",
  accent:"#D4547A", accentL:"#FDEDF1", accentM:"#F0AABF",
  success:"#4A7C6B", successL:"#E8F4F0",
  warn:"#8A6020", warnL:"#FEF5E4",
  danger:"#B91C1C", dangerL:"#FEF2F2",
  info:"#1D4ED8", infoL:"#EFF6FF",
  pink1:"#F9D0DC", pink2:"#F4B8CB", pink3:"#EE94AF",
  escalate:"#7C3AED", escalateL:"#F5F3FF",
  gold:"#B45309", goldL:"#FFFBEB",
};

// ─── HELPERS ───────────────────────────────────────────────────────
const genShieldId = () => {
  const c = "abcdefghijklmnopqrstuvwxyz0123456789";
  return "Shield_" + Array.from({length:6},()=>c[Math.floor(Math.random()*c.length)]).join("");
};
const fmt   = d => new Date(d).toLocaleDateString("en-IN",{day:"numeric",month:"short",year:"numeric"});
const fmtT  = d => new Date(d).toLocaleTimeString("en-IN",{hour:"2-digit",minute:"2-digit"});
const ago   = ts => { const m=Math.floor((Date.now()-ts)/60000); if(m<60)return`${m}m ago`; const h=Math.floor(m/60); if(h<24)return`${h}h ago`; return`${Math.floor(h/24)}d ago`; };
const cdwn  = dl => { const d=dl-Date.now(); if(d<=0)return null; return{h:Math.floor(d/3600000),m:Math.floor((d%3600000)/60000),pct:Math.min(100,(d/259200000)*100)}; };

// ─── INDIAN LAW DATABASE ───────────────────────────────────────────
const LAW_DB = {
  "Inappropriate Comments": {
    ipc: [
      { sec:"IPC § 354A", title:"Sexual Harassment", desc:"Making sexually coloured remarks — punishable up to 3 years." },
      { sec:"IPC § 509",  title:"Word/Gesture Insulting Modesty", desc:"Words or gestures intended to insult the modesty of a woman." },
    ],
    posh: ["Section 2(n)(i)","Section 3(1)"],
    it:   [],
    category:"verbal",
  },
  "Unwanted Contact": {
    ipc: [
      { sec:"IPC § 354",  title:"Assault/Criminal Force on Woman", desc:"Assault with intent to outrage modesty — punishable up to 5 years." },
      { sec:"IPC § 354A", title:"Physical Contact & Advances", desc:"Unwelcome and explicit sexual contact — punishable up to 3 years." },
      { sec:"IPC § 354B", title:"Assault with Intent to Disrobe", desc:"If applicable — punishable 3–7 years." },
    ],
    posh: ["Section 2(n)(ii)","Section 3(2)(i)"],
    it:   [],
    category:"physical",
  },
  "Discrimination": {
    ipc: [
      { sec:"IPC § 166A", title:"Public Servant Disobeying Law", desc:"Applicable if ICC/employer fails to act — punishable up to 2 years." },
    ],
    posh: ["Section 3(2)(v)","Section 19 (Employer duties)","Section 26 (Penalty for non-compliance)"],
    it:   [],
    other:[{ sec:"Labour Law", title:"Equal Remuneration Act 1976", desc:"Prohibits gender-based discrimination in wages and conditions." }],
    category:"systemic",
  },
  "Threats": {
    ipc: [
      { sec:"IPC § 503",  title:"Criminal Intimidation", desc:"Threatening to cause injury to body, reputation or property." },
      { sec:"IPC § 506",  title:"Punishment for Criminal Intimidation", desc:"Punishable up to 7 years if threat is to cause death or grievous hurt." },
      { sec:"IPC § 507",  title:"Anonymous Criminal Intimidation", desc:"Threats through anonymous communication — enhanced punishment." },
    ],
    posh: ["Section 3(2)(iii)","Section 3(2)(iv)"],
    it:   [{ sec:"IT Act § 66A", title:"Online Threats / Menacing Messages", desc:"Sending threatening messages electronically — punishable up to 3 years." }],
    category:"criminal",
  },
  "Unsafe Environment": {
    ipc: [
      { sec:"IPC § 304A", title:"Causing Death by Negligence", desc:"If unsafe environment causes physical harm — applicable to employer." },
    ],
    posh: ["Section 19(c)","Section 19(d)","Section 19(e) (Safe working conditions)","Section 26 (Penalty)"],
    it:   [],
    other:[{ sec:"Factories Act 1948 § 7A", title:"General Duties of Employer", desc:"Duty to ensure safe working environment for all employees." }],
    category:"environmental",
  },
  "Stalking": {
    ipc: [
      { sec:"IPC § 354D", title:"Stalking", desc:"Following a woman, monitoring use of internet/email/social media — punishable up to 5 years for repeat offence." },
    ],
    posh: ["Section 2(n)(iii)","Section 3(1)"],
    it:   [{ sec:"IT Act § 66E", title:"Privacy Violation", desc:"Capturing/transmitting private area images without consent." },
           { sec:"IT Act § 67",  title:"Obscene Electronic Content", desc:"Publishing/transmitting obscene material electronically." }],
    category:"digital",
  },
};

const LEGAL_DRAFT_FULL = (entry, incidentType) => {
  const laws = LAW_DB[incidentType] || LAW_DB["Inappropriate Comments"];
  const today = new Date().toLocaleDateString("en-IN",{day:"numeric",month:"long",year:"numeric"});
  const ipcList  = laws.ipc.map((l,i)=>`  ${i+1}. ${l.sec} — ${l.title}\n     ${l.desc}`).join("\n");
  const poshList = laws.posh.map(s=>`  · ${s}`).join("\n");
  const itList   = laws.it.length ? laws.it.map((l,i)=>`  ${i+1}. ${l.sec} — ${l.title}\n     ${l.desc}`).join("\n") : "  Not applicable to this incident type.";
  const otherList= laws.other?.map((l,i)=>`  ${i+1}. ${l.sec} — ${l.title}\n     ${l.desc}`).join("\n") || "  None.";
  const exhibits = entry.evidence.map((e,i)=>`  Exhibit ${String.fromCharCode(65+i)}: ${e}\n  → AES-256 encrypted · Polygon blockchain hash: ${entry.blockchainHash}`).join("\n");

  return `════════════════════════════════════════════
   KAVACH — FORMAL LEGAL COMPLAINT
   Auto-generated · POSH Act 2013 & Indian Law
════════════════════════════════════════════

TO,
The Presiding Officer / Chairperson,
Internal Complaints Committee (ICC)
[Company / Organisation Name]
[Company Address, City, State — PIN]

DATE: ${today}
COMPLAINT REF: KVH-${entry.blockchainHash.slice(-8).toUpperCase()}

SUBJECT: Formal Complaint of Workplace Sexual Harassment
         under POSH Act 2013, IPC, and IT Act
         Incident: "${entry.title}"

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

PART I — COMPLAINANT DETAILS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Identity: PROTECTED (Anonymous Filing — Kavach Protocol)
Shield Reference: [Auto-assigned on submission]
Blockchain Proof-of-Filing: ${entry.blockchainHash}
Date of Incident: ${entry.date}
Number of Witnesses: ${entry.witnesses}
Emotional Impact Index: ${entry.emotionLog}

Note: Pursuant to Section 16 of the POSH Act 2013, the
identity of the complainant must be kept confidential.
Disclosure attracts penalty under Section 17.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

PART II — INCIDENT DESCRIPTION
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

The complainant experienced workplace harassment
constituting "${incidentType}" on or around ${entry.date}.
The nature of the conduct falls squarely within the
definition of "sexual harassment" under Section 2(n) of
the POSH Act 2013, specifically engaging the provisions
detailed in Part III of this complaint.

The complainant suffered measurable psychological
distress (Emotion Index: ${entry.emotionLog}), establishing
the requisite nexus between the respondent's conduct and
workplace harm under established ICC jurisprudence.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

PART III — APPLICABLE LEGAL PROVISIONS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

A. POSH ACT 2013 — PRIMARY APPLICABILITY
${poshList}

B. INDIAN PENAL CODE (IPC) — CRIMINAL PROVISIONS
${ipcList}

C. INFORMATION TECHNOLOGY ACT 2000 — DIGITAL ELEMENTS
${itList}

D. OTHER APPLICABLE LEGISLATION
${otherList}

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

PART IV — EVIDENCE EXHIBITS
━━━━━━━━━━━━━━━━━━━━━━━━━━━

All evidence is preserved using Kavach Proof Vault:
AES-256 client-side encryption + Polygon blockchain
timestamp anchoring (tamper-evident by design).

${exhibits}
${entry.witnesses>0?`\nWitness Declarations: ${entry.witnesses} corroborating statement(s)\navailable to ICC upon formal request.`:""}

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

PART V — RELIEF SOUGHT
━━━━━━━━━━━━━━━━━━━━━━━

The complainant respectfully requests the ICC to:

1. INTERIM RELIEF (Section 12, POSH Act):
   · Transfer of complainant or respondent pending inquiry
   · Work-from-home accommodation if feasible
   · Restraint order preventing respondent from contacting
     the complainant directly or through third parties

2. INQUIRY (Section 11, POSH Act):
   · Time-bound inquiry completed within 90 days
   · Opportunity to present evidence and witnesses
   · Equitable, non-biased inquiry process

3. FINAL ACTION (Section 13, POSH Act):
   · Written recommendation to employer within 10 days
     of inquiry completion
   · Appropriate disciplinary action against respondent
   · Compensation commensurate with loss suffered

4. CRIMINAL REFERRAL (if applicable):
   · Forward complaint to appropriate law enforcement
     under cited IPC provisions if evidence warrants
   · Referral to Cyber Crime portal for IT Act violations

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

PART VI — DECLARATION
━━━━━━━━━━━━━━━━━━━━━

I, the complainant, declare that the contents of this
complaint are true and correct to the best of my
knowledge. This complaint is submitted in good faith
under Section 9 of the POSH Act 2013, within the
prescribed limitation period.

I understand that:
· False complaints attract action under Section 14
· Identity protection is guaranteed under Section 16
· Retaliation against me is prohibited under Section 17

Yours faithfully,
[Anonymous Complainant — Kavach Protected Identity]

Blockchain Proof of Filing: ${entry.blockchainHash}
Kavach Shield Reference: [Auto-generated on submission]
Timestamp: ${today}

════════════════════════════════════════════
   This draft was auto-generated by Kavach AI.
   Consult a legal advisor before submission.
════════════════════════════════════════════`;
};

// ─── RISK ENGINE ───────────────────────────────────────────────────
const RISK = {
  "Threats":              {level:"MAJOR",score:95,color:C.danger, bg:C.dangerL, label:"Critical Risk",  icon:"🚨",desc:"Threats constitute criminal intimidation under IPC § 506 — grounds for immediate ICC action and police complaint."},
  "Unwanted Contact":     {level:"MAJOR",score:85,color:C.danger, bg:C.dangerL, label:"High Risk",      icon:"🚨",desc:"Physical contact without consent may constitute assault under IPC § 354. Immediate evidence preservation is critical."},
  "Discrimination":       {level:"MAJOR",score:72,color:C.warn,   bg:C.warnL,   label:"Major Risk",     icon:"⚠️",desc:"Gender-based discrimination violates POSH Act and Equal Remuneration Act 1976 — employer liability applies."},
  "Inappropriate Comments":{level:"MINOR",score:55,color:C.warn,  bg:C.warnL,   label:"Moderate Risk",  icon:"⚠️",desc:"Verbal harassment under IPC § 354A / § 509. Pattern documentation significantly strengthens the complaint."},
  "Unsafe Environment":   {level:"MINOR",score:45,color:C.accent, bg:C.accentL, label:"Moderate Risk",  icon:"⚠️",desc:"Systemic unsafe conditions trigger employer liability under POSH Act Section 19 and Factories Act § 7A."},
};

// ─── MOCK DATA ─────────────────────────────────────────────────────
const now = Date.now();

const MOCK_VAULT = [
  {id:"v1",title:"Meeting Room Incident",    date:"2025-01-10",status:"danger", incidentType:"Unwanted Contact",      evidence:["emails","voice memo","location"],        blockchainHash:"0x7f3a9b2c...8c2d",emotionLog:"High Distress",  witnesses:2,poshDeadline:17},
  {id:"v2",title:"WhatsApp Harassment Chain",date:"2025-01-28",status:"danger", incidentType:"Threats",               evidence:["screenshots","timestamps","chat export"], blockchainHash:"0x4e1f8a3d...1b9f",emotionLog:"High Distress",  witnesses:0,poshDeadline:31},
  {id:"v3",title:"Corridor Intimidation",    date:"2025-02-14",status:"caution",incidentType:"Inappropriate Comments",evidence:["CCTV request","witness statement"],       blockchainHash:"0x9c2b7e4a...3d8c",emotionLog:"Medium Distress",witnesses:1,poshDeadline:65},
];

const MOCK_COMPANIES = [
  {id:"c1",name:"TechNova Solutions",  location:"Bengaluru, KA",sector:"IT / Software",    safetyScore:78,reviewCount:142,poshCommittee:true, hrResponseDays:4, trainingPerYear:3,openInvestigations:2,trend:"up",   avgResolutionDays:12,complaintsThisYear:3},
  {id:"c2",name:"Meridian Finance",    location:"Mumbai, MH",   sector:"Banking & Finance",safetyScore:52,reviewCount:89, poshCommittee:true, hrResponseDays:9, trainingPerYear:1,openInvestigations:5,trend:"down", avgResolutionDays:38,complaintsThisYear:8},
  {id:"c3",name:"GreenPath Pharma",    location:"Hyderabad, TS",sector:"Pharmaceuticals",  safetyScore:41,reviewCount:63, poshCommittee:false,hrResponseDays:14,trainingPerYear:0,openInvestigations:8,trend:"down", avgResolutionDays:62,complaintsThisYear:11},
  {id:"c4",name:"Stellar EdTech",      location:"Pune, MH",     sector:"Education / EdTech",safetyScore:89,reviewCount:54,poshCommittee:true, hrResponseDays:2, trainingPerYear:6,openInvestigations:0,trend:"up",   avgResolutionDays:8, complaintsThisYear:1},
];

const MOCK_COMPLAINTS = [
  {id:"c1",title:"Meeting Room Incident",    date:"2025-01-10",status:"hr_overdue",  riskLevel:"MAJOR",incidentType:"Unwanted Contact",      filedAt:now-86400000*4, hrDeadline:now-3600000*5,
   hrMessages:[{from:"hr",text:"Complaint received. Review will begin shortly.",time:now-86400000*3},{from:"victim",text:"Please expedite — I feel unsafe returning to office.",time:now-86400000*2},{from:"hr",text:"Investigator assigned. 48-hour turnaround.",time:now-86400000}],
   timeline:[{label:"Complaint Filed",done:true,time:now-86400000*4},{label:"HR Notified",done:true,time:now-86400000*3},{label:"HR Response (72h)",done:false,overdue:true,time:now-3600000*5},{label:"Investigation",done:false},{label:"Resolution",done:false}]},
  {id:"c2",title:"WhatsApp Harassment Chain",date:"2025-01-28",status:"investigating",riskLevel:"MAJOR",incidentType:"Threats",               filedAt:now-86400000*2, hrDeadline:now+3600000*14,
   hrMessages:[{from:"hr",text:"ICC convened. WFH accommodation approved. Investigation begins Monday.",time:now-86400000},{from:"victim",text:"Thank you. Please keep me updated.",time:now-3600000*18},{from:"hr",text:"Weekly status updates will be sent to your anonymous ID.",time:now-3600000*10}],
   timeline:[{label:"Complaint Filed",done:true,time:now-86400000*2},{label:"HR Notified",done:true,time:now-86400000*2+3600000},{label:"HR Response (72h)",done:true,time:now-86400000},{label:"Investigation",done:true,time:now-3600000*10},{label:"Resolution",done:false}]},
  {id:"c3",title:"Corridor Intimidation",   date:"2025-02-14",status:"filed",       riskLevel:"MINOR",incidentType:"Inappropriate Comments",  filedAt:now-3600000*6,  hrDeadline:now+3600000*66,
   hrMessages:[],
   timeline:[{label:"Complaint Filed",done:true,time:now-3600000*6},{label:"HR Notified",done:false,pending:true},{label:"HR Response (72h)",done:false},{label:"Investigation",done:false},{label:"Resolution",done:false}]},
];

const MOCK_CIRCLE = [
  {id:"p1",shieldId:"Shield_9k2mxz",sector:"IT / Software",city:"Bengaluru",message:"Finally reported last week. It took 3 months documenting everything. POSH committee met within 72h. Stay strong — documentation is your power.",helpfulCount:47,timestamp:now-3600000*2,isVerified:false},
  {id:"p2",shieldId:"Kavach Legal Advisor",sector:"Legal",city:"National",message:"Under IPC § 354A and POSH Act Section 9, complaints must be filed within 3 months. Evidence submitted digitally via Kavach Vault is admissible. DM for free consultation.",helpfulCount:128,timestamp:now-3600000*6,isVerified:true},
  {id:"p3",shieldId:"Shield_4r7wnq",sector:"Finance",city:"Mumbai",message:"Always CC yourself on every email. Forward inappropriate messages to personal email before HR loses them. Seen it happen twice.",helpfulCount:93,timestamp:now-3600000*14,isVerified:false},
  {id:"p4",shieldId:"Shield_2m9kbp",sector:"Healthcare",city:"Delhi",message:"Documenting retaliation was KEY. Retaliation is separately punishable under POSH Section 17. Don't let them silence you.",helpfulCount:61,timestamp:now-86400000,isVerified:false},
];

// ─── GLOBAL STYLES ─────────────────────────────────────────────────
const G = `
  @import url('https://fonts.googleapis.com/css2?family=Cormorant+Garamond:wght@400;500;600;700&family=Outfit:wght@300;400;500;600&display=swap');
  *{box-sizing:border-box;margin:0;padding:0;}
  body{background:#FDF0F3;font-family:'Outfit',sans-serif;color:#2C1A1F;-webkit-tap-highlight-color:transparent;}
  ::-webkit-scrollbar{width:0;}
  @keyframes fadeUp{from{opacity:0;transform:translateY(10px)}to{opacity:1;transform:translateY(0)}}
  @keyframes slideUp{from{opacity:0;transform:translateY(100%)}to{opacity:1;transform:translateY(0)}}
  @keyframes pls{0%,100%{opacity:1}50%{opacity:0.5}}
  @keyframes spinR{to{transform:rotate(360deg)}}
  @keyframes toastIn{from{opacity:0;transform:translateY(-8px)}to{opacity:1;transform:translateY(0)}}
  @keyframes petalF{0%,100%{transform:translateY(0)}50%{transform:translateY(-6px)}}
  @keyframes urgP{0%,100%{box-shadow:0 0 0 0 rgba(185,28,28,0.4)}70%{box-shadow:0 0 0 10px rgba(185,28,28,0)}}
  @keyframes tickA{0%,100%{opacity:1}50%{opacity:0.6}}
  @keyframes barIn{from{width:0}to{width:var(--w)}}
  .fade-up{animation:fadeUp 0.3s ease both;}
  .slide-up{animation:slideUp 0.4s cubic-bezier(0.16,1,0.3,1) both;}
  .pulsing{animation:pls 2.2s ease-in-out infinite;}
  .floating{animation:petalF 3s ease-in-out infinite;}
  .urgent{animation:urgP 1.5s ease-in-out infinite;}
  .tick{animation:tickA 1s ease-in-out infinite;}
  .spin{animation:spinR 0.9s linear infinite;}
  .bar-anim{animation:barIn 1.1s cubic-bezier(0.4,0,0.2,1) forwards;}
  button{cursor:pointer;border:none;outline:none;font-family:'Outfit',sans-serif;}
  input,textarea{font-family:'Outfit',sans-serif;outline:none;}
  input::placeholder,textarea::placeholder{color:#C49AAA;}
`;

// ─── ATOMS ─────────────────────────────────────────────────────────
const Tag = ({children,color=C.sub,bg=C.surfDim,style={}}) => (
  <span style={{background:bg,color,borderRadius:20,padding:"2px 10px",fontSize:11,fontWeight:500,display:"inline-flex",alignItems:"center",gap:3,whiteSpace:"nowrap",...style}}>{children}</span>
);

const Btn = ({children,onClick,variant="primary",disabled,full=true,small=false,style={}}) => {
  const v = {
    primary:  {background:disabled?C.pink1:C.accent,  color:disabled?C.dim:"#fff",  boxShadow:disabled?"none":`0 2px 12px ${C.accentM}88`},
    ghost:    {background:"none",border:`1.5px solid ${C.line}`,color:C.sub},
    outline:  {background:"none",border:`1.5px solid ${disabled?C.line:C.accent}`,color:disabled?C.dim:C.accent},
    soft:     {background:C.accentL,color:C.accent,border:`1px solid ${C.accentM}`},
    danger:   {background:C.danger,color:"#fff",boxShadow:`0 2px 12px ${C.danger}44`},
    escalate: {background:C.escalate,color:"#fff"},
    success:  {background:C.success,color:"#fff"},
    law:      {background:C.goldL,color:C.gold,border:`1.5px solid ${C.gold}44`},
  };
  return <button onClick={disabled?undefined:onClick} style={{width:full?"100%":"auto",padding:small?"8px 14px":"13px 20px",borderRadius:50,fontSize:small?12:14,fontWeight:500,display:"flex",alignItems:"center",justifyContent:"center",gap:8,transition:"all 0.15s",...v[variant],...style}}>{children}</button>;
};

const Card = ({children,style={},onClick}) => (
  <div onClick={onClick} style={{background:C.surf,border:`1.5px solid ${C.line}`,borderRadius:18,overflow:"hidden",cursor:onClick?"pointer":"default",...style}}>{children}</div>
);

const Spinner = () => <div className="spin" style={{width:18,height:18,border:`2px solid ${C.pink1}`,borderTopColor:C.accent,borderRadius:"50%"}}/>;

const Toast = ({msg,type,onClose}) => {
  useEffect(()=>{const t=setTimeout(onClose,3500);return()=>clearTimeout(t);},[]);
  const bdr = type==="error"?C.danger:type==="warn"?C.warn:type==="escalate"?C.escalate:type==="law"?C.gold:C.success;
  return <div style={{position:"fixed",top:16,left:"50%",transform:"translateX(-50%)",zIndex:9999,background:C.surf,border:`1.5px solid ${C.line}`,borderTop:`3px solid ${bdr}`,borderRadius:14,padding:"12px 18px",display:"flex",gap:10,alignItems:"center",animation:"toastIn 0.25s ease",maxWidth:380,minWidth:280,boxShadow:`0 8px 24px ${C.pink1}`}}>
    <div style={{width:7,height:7,borderRadius:"50%",background:bdr,flexShrink:0}}/>
    <span style={{fontSize:13,color:C.ink,flex:1}}>{msg}</span>
    <button onClick={onClose} style={{background:"none",color:C.dim,fontSize:18,lineHeight:1,padding:0}}>×</button>
  </div>;
};

const Shield = ({size=32,outline=false}) => (
  <svg width={size} height={size} viewBox="0 0 32 32" fill="none">
    <path d="M16 3L4 8V16C4 23 9.5 28.5 16 30.5C22.5 28.5 28 23 28 16V8L16 3Z" fill={outline?C.accentL:C.accent} stroke={C.accent} strokeWidth="1.5" strokeLinejoin="round"/>
    {!outline&&<path d="M11 16.5L14.5 20L21 13.5" stroke="white" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round"/>}
    {outline&&<path d="M11 16.5L14.5 20L21 13.5" stroke={C.accent} strokeWidth="2" strokeLinecap="round" strokeLinejoin="round"/>}
  </svg>
);

// ─── SPLASH ────────────────────────────────────────────────────────
const Splash = ({onDone}) => {
  const [v,setV]=useState(false);
  useEffect(()=>{setTimeout(()=>setV(true),80);setTimeout(onDone,2600);},[]);
  return (
    <div style={{height:"100%",display:"flex",flexDirection:"column",alignItems:"center",justifyContent:"center",background:`linear-gradient(160deg,#FFF0F5,#FDE8EE 50%,#F9D8E6 100%)`}}>
      {[{top:"12%",left:"8%",s:18,d:0},{top:"20%",right:"10%",s:14,d:0.4},{bottom:"25%",left:"12%",s:12,d:0.8},{bottom:"18%",right:"8%",s:16,d:1.2}].map((p,i)=>(
        <div key={i} className="floating" style={{position:"absolute",fontSize:p.s,opacity:0.35,animationDelay:`${p.d}s`,...p}}>🌸</div>
      ))}
      <div style={{opacity:v?1:0,transform:v?"translateY(0)":"translateY(14px)",transition:"all 0.7s ease",textAlign:"center",zIndex:1}}>
        <div className="pulsing"><Shield size={56}/></div>
        <h1 style={{fontFamily:"'Cormorant Garamond',serif",fontSize:46,fontWeight:700,color:C.ink,letterSpacing:6,marginTop:18}}>KAVACH</h1>
        <p style={{color:C.dim,fontSize:11,letterSpacing:4,marginTop:5,fontWeight:300}}>SMART WORKPLACE SAFETY</p>
      </div>
      <div style={{position:"absolute",bottom:44,display:"flex",gap:6,opacity:v?1:0,transition:"opacity 0.5s 0.5s"}}>
        {[1,2,3].map(i=><div key={i} style={{width:i===1?28:7,height:3,borderRadius:10,background:i===1?C.accent:C.pink1}}/>)}
      </div>
    </div>
  );
};

// ─── ONBOARDING ────────────────────────────────────────────────────
const Onboarding = ({onDone}) => {
  const [s,setS]=useState(0);
  const slides=[
    {title:"You Are\nNot Alone",body:"Thousands face workplace harassment in silence. Kavach gives you a voice — without revealing who you are.",num:"01",emoji:"🌸"},
    {title:"Evidence &\nLegal Shield",body:"Your vault anchors evidence on blockchain. One tap generates a full legal complaint citing IPC, POSH Act, and IT Act sections.",num:"02",emoji:"⚖️"},
    {title:"72h Rule —\nSystem Escalates",body:"HR must respond in 72 hours. If they don't, Kavach auto-escalates to NCW, Women Helpline 181, and Cyber Crime.",num:"03",emoji:"🛡"},
  ];
  const sl=slides[s];
  return (
    <div style={{height:"100%",display:"flex",flexDirection:"column",background:`linear-gradient(160deg,#FFF5F8,#FDE8EE 100%)`}}>
      <div key={s} className="fade-up" style={{flex:1,padding:"60px 32px 24px",display:"flex",flexDirection:"column",justifyContent:"flex-end"}}>
        <div style={{fontSize:52,marginBottom:20}}>{sl.emoji}</div>
        <div style={{fontFamily:"'Cormorant Garamond',serif",fontSize:12,color:C.dim,letterSpacing:3,marginBottom:16}}>{sl.num} / 03</div>
        <h2 style={{fontFamily:"'Cormorant Garamond',serif",fontSize:36,fontWeight:600,color:C.ink,lineHeight:1.15,marginBottom:18,whiteSpace:"pre-line"}}>{sl.title}</h2>
        <p style={{color:C.sub,fontSize:15,lineHeight:1.75,fontWeight:300}}>{sl.body}</p>
      </div>
      <div style={{padding:"28px 32px 48px"}}>
        <div style={{display:"flex",gap:5,marginBottom:24}}>{slides.map((_,i)=><div key={i} style={{flex:i===s?3:1,height:3,borderRadius:10,background:i===s?C.accent:C.pink1,transition:"all 0.4s"}}/>)}</div>
        {s<2?<Btn onClick={()=>setS(s+1)}>Continue →</Btn>:<Btn onClick={onDone}>Get My Shield ID →</Btn>}
        {s<2&&<button onClick={onDone} style={{width:"100%",padding:12,background:"none",color:C.dim,fontSize:13,marginTop:8}}>Skip</button>}
      </div>
    </div>
  );
};

// ─── LOGIN ─────────────────────────────────────────────────────────
const Login = ({onLogin}) => {
  const [mode,setMode]=useState("anon");
  const [email,setEmail]=useState(""),  [pass,setPass]=useState("");
  const [loading,setLoading]=useState(false);
  const go=()=>{setLoading(true);setTimeout(()=>{onLogin({shieldId:genShieldId(),mode});setLoading(false);},1600);};
  return (
    <div style={{height:"100%",display:"flex",flexDirection:"column",background:`linear-gradient(160deg,#FFF5F8,#FDE8EE 100%)`}}>
      <div style={{padding:"52px 32px 20px"}}>
        <Shield size={40}/>
        <h1 style={{fontFamily:"'Cormorant Garamond',serif",fontSize:38,fontWeight:700,color:C.ink,marginTop:14,letterSpacing:2}}>KAVACH</h1>
        <p style={{color:C.sub,fontSize:14,marginTop:4,fontWeight:300}}>Your identity. Always protected.</p>
      </div>
      <div style={{flex:1,padding:"0 24px 32px",overflowY:"auto"}}>
        <div style={{display:"flex",background:C.pink1+"88",borderRadius:50,padding:4,marginBottom:24,border:`1.5px solid ${C.line}`}}>
          {[["anon","🛡 Anonymous"],["email","✉ Work Email"]].map(([m,l])=>(
            <button key={m} onClick={()=>setMode(m)} style={{flex:1,padding:"9px",borderRadius:50,background:mode===m?C.surf:"none",color:mode===m?C.accent:C.dim,fontSize:13,fontWeight:mode===m?600:400,boxShadow:mode===m?`0 2px 8px ${C.pink2}66`:"none",transition:"all 0.2s"}}>{l}</button>
          ))}
        </div>
        {mode==="anon"?(
          <div className="fade-up">
            <div style={{background:C.successL,borderLeft:`3px solid ${C.success}`,borderRadius:14,padding:"14px 16px",marginBottom:20}}>
              <div style={{color:C.success,fontWeight:600,fontSize:13}}>🔐 Zero-Knowledge Mode</div>
              <div style={{color:C.sub,fontSize:12,marginTop:3,lineHeight:1.5,fontWeight:300}}>No email. No name. No trace. Only a Shield ID.</div>
            </div>
            <Card style={{padding:"20px",marginBottom:24}}>
              <div style={{color:C.dim,fontSize:10,letterSpacing:1.5,marginBottom:8}}>YOUR ANONYMOUS IDENTITY</div>
              <div style={{fontFamily:"'Cormorant Garamond',serif",fontSize:28,fontWeight:600,color:C.accent}}>Shield_••••••</div>
              <div style={{color:C.dim,fontSize:11,marginTop:6}}>Generated on login · Tied to no real identity</div>
            </Card>
            <Btn onClick={go} disabled={loading}>{loading?<><Spinner/>Generating...</>:"Enter Anonymously →"}</Btn>
          </div>
        ):(
          <div className="fade-up">
            {[["EMAIL","email","you@company.com",email,setEmail],["PASSWORD","password","••••••••",pass,setPass]].map(([label,type,ph,val,set])=>(
              <div key={label} style={{marginBottom:16}}>
                <label style={{color:C.dim,fontSize:10,letterSpacing:1.5,display:"block",marginBottom:6}}>{label}</label>
                <input value={val} onChange={e=>set(e.target.value)} type={type} placeholder={ph} style={{width:"100%",background:C.surf,border:`1.5px solid ${C.line}`,borderRadius:12,padding:"13px 16px",color:C.ink,fontSize:14}}/>
              </div>
            ))}
            <div style={{background:C.warnL,borderLeft:`3px solid ${C.warn}`,borderRadius:12,padding:"10px 16px",marginBottom:20,fontSize:12,color:C.warn,lineHeight:1.5}}>Your email is deleted and replaced with a Shield ID.</div>
            <Btn onClick={go} disabled={loading||!email||!pass}>{loading?<><Spinner/>Anonymizing...</>:"Secure Login →"}</Btn>
          </div>
        )}
      </div>
    </div>
  );
};

// ─── BOTTOM NAV (6 tabs, compact) ──────────────────────────────────
const Nav = ({active,onChange}) => {
  const tabs=[{id:"home",icon:"⊙",label:"Home"},{id:"signal",icon:"◎",label:"Signal"},{id:"vault",icon:"⬡",label:"Vault"},{id:"track",icon:"◈",label:"Track"},{id:"score",icon:"★",label:"Score"},{id:"circle",icon:"◉",label:"Circle"}];
  return (
    <div style={{background:C.surf,borderTop:`1.5px solid ${C.line}`,display:"flex",paddingBottom:16,paddingTop:2}}>
      {tabs.map(t=>(
        <button key={t.id} onClick={()=>onChange(t.id)} style={{flex:1,display:"flex",flexDirection:"column",alignItems:"center",gap:2,background:"none",padding:"6px 0",position:"relative"}}>
          {active===t.id&&<div style={{position:"absolute",top:0,left:"50%",transform:"translateX(-50%)",width:24,height:2.5,borderRadius:10,background:C.accent}}/>}
          {t.id==="track"&&<div style={{position:"absolute",top:4,right:"14%",width:6,height:6,borderRadius:"50%",background:C.danger,border:`1.5px solid ${C.surf}`}}/>}
          <span style={{fontSize:15,color:active===t.id?C.accent:C.dim,transition:"color 0.15s",marginTop:4}}>{t.icon}</span>
          <span style={{fontSize:8,fontWeight:500,color:active===t.id?C.accent:C.dim,letterSpacing:0.5}}>{t.label.toUpperCase()}</span>
        </button>
      ))}
    </div>
  );
};

// ─── RISK BADGE ─────────────────────────────────────────────────────
const RiskBadge = ({incidentType,compact=false}) => {
  const r=RISK[incidentType]; if(!r)return null;
  if(compact)return <Tag color={r.color} bg={r.bg}>{r.icon} {r.level}</Tag>;
  return (
    <div style={{background:r.bg,border:`1.5px solid ${r.color}33`,borderLeft:`4px solid ${r.color}`,borderRadius:14,padding:"14px 16px"}}>
      <div style={{display:"flex",justifyContent:"space-between",alignItems:"center",marginBottom:8}}>
        <div style={{display:"flex",alignItems:"center",gap:8}}>
          <span style={{fontSize:20}}>{r.icon}</span>
          <div><div style={{color:r.color,fontWeight:700,fontSize:13}}>{r.label}</div><div style={{color:C.sub,fontSize:11}}>Risk Score {r.score}/100</div></div>
        </div>
        <div style={{background:r.color,borderRadius:20,padding:"3px 10px",fontSize:11,color:"#fff",fontWeight:600}}>{r.score}</div>
      </div>
      <div style={{background:"#fff",borderRadius:10,height:5,overflow:"hidden",marginBottom:10}}><div style={{height:"100%",width:`${r.score}%`,background:r.color,borderRadius:10,transition:"width 0.8s ease"}}/></div>
      <p style={{color:C.sub,fontSize:12,lineHeight:1.6}}>{r.desc}</p>
    </div>
  );
};

// ─── ESCALATION PANEL ──────────────────────────────────────────────
const EscalationPanel = ({showToast}) => (
  <div className="fade-up" style={{background:C.escalateL,border:`2px solid ${C.escalate}44`,borderLeft:`4px solid ${C.escalate}`,borderRadius:16,padding:18,marginBottom:16}}>
    <div style={{color:C.escalate,fontWeight:700,fontSize:14,marginBottom:4}}>🚨 Auto-Escalation Triggered</div>
    <div style={{color:C.sub,fontSize:12,marginBottom:12}}>HR exceeded 72-hour deadline. Forwarding to authorities.</div>
    {[{icon:"📞",label:"Women Helpline",sub:"24/7 Crisis Support",num:"181",color:"#DC2626",bg:C.dangerL},{icon:"🏛",label:"National Commission for Women",sub:"ncwapps.nic.in",color:C.escalate,bg:C.escalateL},{icon:"💻",label:"Cyber Crime Portal",sub:"cybercrime.gov.in",color:C.info,bg:C.infoL}].map((e,i)=>(
      <div key={i} style={{background:e.bg,border:`1px solid ${e.color}33`,borderRadius:12,padding:"10px 14px",display:"flex",alignItems:"center",gap:10,marginBottom:8}}>
        <span style={{fontSize:20}}>{e.icon}</span>
        <div style={{flex:1}}><div style={{fontWeight:600,fontSize:13,color:C.ink}}>{e.label}</div><div style={{color:C.sub,fontSize:11}}>{e.sub}</div></div>
        <button onClick={()=>showToast(`Connecting to ${e.label}...`,"escalate")} style={{background:e.color,color:"#fff",borderRadius:20,padding:"5px 12px",fontSize:11,fontWeight:600,cursor:"pointer"}}>{e.num==="181"?"Call":"Visit"}</button>
      </div>
    ))}
    <div style={{marginTop:8,background:"#fff",borderRadius:10,padding:"8px 12px",fontSize:11,color:C.sub}}>ℹ️ Identity remains protected. Only anonymous reference shared with authorities.</div>
  </div>
);

// ─── COUNTDOWN ─────────────────────────────────────────────────────
const Countdown = ({deadline}) => {
  const [cd,setCd]=useState(cdwn(deadline));
  useEffect(()=>{const t=setInterval(()=>setCd(cdwn(deadline)),60000);return()=>clearInterval(t);},[deadline]);
  if(!cd)return <div className="urgent" style={{background:C.dangerL,border:`1.5px solid ${C.danger}44`,borderRadius:12,padding:"12px 16px",display:"flex",gap:10,alignItems:"center"}}><span style={{fontSize:18}}>🚨</span><div><div style={{color:C.danger,fontWeight:700,fontSize:13}}>HR Deadline Exceeded</div><div style={{color:C.sub,fontSize:11}}>Auto-escalation triggered</div></div></div>;
  const urg=cd.h<6;
  return (
    <div style={{background:urg?C.dangerL:C.warnL,border:`1.5px solid ${urg?C.danger:C.warn}44`,borderRadius:12,padding:"12px 16px"}}>
      <div style={{display:"flex",justifyContent:"space-between",marginBottom:7}}>
        <span style={{color:urg?C.danger:C.warn,fontWeight:600,fontSize:12}}>⏱ HR Response Deadline</span>
        <span className={urg?"tick":""} style={{color:urg?C.danger:C.warn,fontWeight:700,fontSize:13}}>{cd.h}h {cd.m}m left</span>
      </div>
      <div style={{background:"#fff",borderRadius:10,height:5,overflow:"hidden"}}><div style={{height:"100%",width:`${cd.pct}%`,background:urg?C.danger:C.warn,borderRadius:10}}/></div>
      {urg&&<div style={{color:C.danger,fontSize:11,marginTop:5,fontWeight:500}}>⚠️ Under 6 hours before auto-escalation to NCW & 181</div>}
    </div>
  );
};

// ─── TIMELINE ──────────────────────────────────────────────────────
const Timeline = ({steps}) => (
  <div style={{padding:"4px 0"}}>
    {steps.map((s,i)=>(
      <div key={i} style={{display:"flex",gap:12,paddingBottom:i<steps.length-1?16:0}}>
        <div style={{display:"flex",flexDirection:"column",alignItems:"center"}}>
          <div style={{width:28,height:28,borderRadius:"50%",flexShrink:0,background:s.done?C.success:s.overdue?C.danger:s.pending?C.warn:C.surfDim,border:`2px solid ${s.done?C.success:s.overdue?C.danger:s.pending?C.warn:C.line}`,display:"flex",alignItems:"center",justifyContent:"center",fontSize:11,color:s.done?"#fff":s.overdue?"#fff":s.pending?"#fff":C.dim,fontWeight:600}}>
            {s.done?"✓":s.overdue?"!":s.pending?"…":"○"}
          </div>
          {i<steps.length-1&&<div style={{width:2,flex:1,background:s.done?`${C.success}55`:C.line,marginTop:4,minHeight:14}}/>}
        </div>
        <div style={{paddingTop:4}}>
          <div style={{fontWeight:s.done||s.overdue?500:400,fontSize:13,color:s.overdue?C.danger:s.done?C.ink:C.sub}}>{s.label}</div>
          {s.time&&<div style={{color:C.dim,fontSize:11,marginTop:1}}>{fmt(s.time)} · {fmtT(s.time)}</div>}
          {s.overdue&&<div style={{color:C.danger,fontSize:11,fontWeight:500}}>Deadline missed</div>}
          {s.pending&&<div style={{color:C.warn,fontSize:11}}>Awaiting response...</div>}
        </div>
      </div>
    ))}
  </div>
);

// ─── HR CHAT ───────────────────────────────────────────────────────
const HRChat = ({complaint,shieldId,showToast}) => {
  const [msgs,setMsgs]=useState(complaint.hrMessages);
  const [text,setText]=useState("");
  const [sending,setSending]=useState(false);
  const bottomRef=useRef(null);
  useEffect(()=>{bottomRef.current?.scrollIntoView({behavior:"smooth"});},[msgs]);
  const send=()=>{
    if(!text.trim())return;
    setSending(true);
    setMsgs(m=>[...m,{from:"victim",text:text.trim(),time:Date.now()}]);
    setText("");
    setTimeout(()=>{setSending(false);if(complaint.status!=="hr_overdue")setMsgs(m=>[...m,{from:"hr",text:"Thank you. Our ICC officer will review and respond within 24 hours.",time:Date.now()+2000}]);},2000);
    showToast("Message sent anonymously to HR","success");
  };
  return (
    <div style={{display:"flex",flexDirection:"column",height:"100%"}}>
      <div style={{background:C.surfDim,border:`1px solid ${C.line}`,borderRadius:"14px 14px 0 0",padding:"11px 14px",display:"flex",gap:10,alignItems:"center"}}>
        <div style={{width:32,height:32,borderRadius:"50%",background:C.surf,border:`1.5px solid ${C.line}`,display:"flex",alignItems:"center",justifyContent:"center",fontSize:14}}>🏢</div>
        <div><div style={{fontWeight:600,fontSize:13,color:C.ink}}>HR / ICC Committee</div><div style={{color:C.dim,fontSize:11}}>{shieldId}</div></div>
        <div style={{marginLeft:"auto"}}><Tag color={complaint.status==="hr_overdue"?C.danger:C.success} bg={complaint.status==="hr_overdue"?C.dangerL:C.successL}>{complaint.status==="hr_overdue"?"Overdue":"Active"}</Tag></div>
      </div>
      <div style={{flex:1,overflowY:"auto",padding:14,display:"flex",flexDirection:"column",gap:10,background:"#FFF8FA",border:`1px solid ${C.line}`,borderTop:"none"}}>
        {msgs.length===0&&<div style={{textAlign:"center",color:C.dim,fontSize:13,padding:"24px 0",fontWeight:300}}>No messages yet. Send first message to HR.</div>}
        {msgs.map((m,i)=>(
          <div key={i} style={{display:"flex",flexDirection:"column",alignItems:m.from==="victim"?"flex-end":"flex-start"}}>
            <div style={{maxWidth:"78%",padding:"10px 14px",borderRadius:m.from==="victim"?"18px 18px 4px 18px":"18px 18px 18px 4px",background:m.from==="victim"?C.accent:C.surf,color:m.from==="victim"?"#fff":C.sub,border:m.from==="hr"?`1.5px solid ${C.line}`:"none",fontSize:13,lineHeight:1.5}}>{m.text}</div>
            <div style={{color:C.dim,fontSize:10,marginTop:3,paddingLeft:4,paddingRight:4}}>{ago(m.time)}</div>
          </div>
        ))}
        {sending&&<div style={{display:"flex",gap:6,alignItems:"center"}}><Spinner/><span style={{color:C.dim,fontSize:12}}>HR reviewing...</span></div>}
        <div ref={bottomRef}/>
      </div>
      <div style={{border:`1px solid ${C.line}`,borderTop:"none",borderRadius:"0 0 14px 14px",padding:"10px 12px",background:C.surf,display:"flex",gap:8,alignItems:"flex-end"}}>
        <textarea value={text} onChange={e=>setText(e.target.value)} placeholder="Message HR anonymously..." rows={2} style={{flex:1,background:C.surfDim,border:`1.5px solid ${C.line}`,borderRadius:12,padding:"10px 12px",color:C.ink,fontSize:13,resize:"none",lineHeight:1.5}}/>
        <button onClick={send} disabled={!text.trim()} style={{width:38,height:38,borderRadius:"50%",background:text.trim()?C.accent:C.pink1,color:"#fff",fontSize:16,display:"flex",alignItems:"center",justifyContent:"center",flexShrink:0,transition:"all 0.2s",border:"none"}}>↑</button>
      </div>
    </div>
  );
};

// ─── TRACK SCREEN ──────────────────────────────────────────────────
const Track = ({user,showToast}) => {
  const [complaints]=useState(MOCK_COMPLAINTS);
  const [sel,setSel]=useState(null);
  const [view,setView]=useState("timeline");
  const statusMeta={filed:{label:"Filed",color:C.info,bg:C.infoL},hr_notified:{label:"HR Notified",color:C.warn,bg:C.warnL},hr_responded:{label:"Responded",color:C.success,bg:C.successL},hr_overdue:{label:"HR Overdue 🚨",color:C.danger,bg:C.dangerL},investigating:{label:"Investigating",color:C.warn,bg:C.warnL},resolved:{label:"Resolved ✓",color:C.success,bg:C.successL},escalated:{label:"Escalated",color:C.escalate,bg:C.escalateL}};
  const s=sel?complaints.find(c=>c.id===sel):null;
  if(s)return(
    <div style={{height:"100%",display:"flex",flexDirection:"column"}}>
      <div style={{padding:"16px 20px 12px",background:`linear-gradient(160deg,#FFF5F8,#FDE8EE)`,borderBottom:`1.5px solid ${C.line}`}}>
        <button onClick={()=>setSel(null)} style={{background:"none",color:C.accent,fontSize:13,fontWeight:500,display:"flex",alignItems:"center",gap:4,marginBottom:8}}>← Back</button>
        <div style={{display:"flex",justifyContent:"space-between",alignItems:"flex-start"}}>
          <div><div style={{fontFamily:"'Cormorant Garamond',serif",fontSize:17,fontWeight:600,color:C.ink}}>{s.title}</div><div style={{color:C.dim,fontSize:12,marginTop:1}}>Filed {fmt(s.filedAt)}</div></div>
          <Tag color={statusMeta[s.status].color} bg={statusMeta[s.status].bg}>{statusMeta[s.status].label}</Tag>
        </div>
        <div style={{display:"flex",gap:6,marginTop:10}}>{[["timeline","📋 Timeline"],["chat","💬 HR Chat"]].map(([v,l])=><button key={v} onClick={()=>setView(v)} style={{flex:1,padding:"8px",borderRadius:20,background:view===v?C.accent:C.surf,color:view===v?"#fff":C.sub,fontSize:12,fontWeight:view===v?600:400,border:`1.5px solid ${view===v?C.accent:C.line}`,transition:"all 0.2s"}}>{l}</button>)}</div>
      </div>
      <div style={{flex:1,overflow:"hidden",padding:"12px 20px",display:"flex",flexDirection:"column"}}>
        {view==="timeline"?(
          <div style={{flex:1,overflowY:"auto"}}>
            {s.status==="hr_overdue"&&<EscalationPanel showToast={showToast}/>}
            <div style={{marginBottom:14}}><Countdown deadline={s.hrDeadline}/></div>
            <div style={{marginBottom:14}}><div style={{color:C.dim,fontSize:10,letterSpacing:1.5,marginBottom:8}}>RISK ANALYSIS</div><RiskBadge incidentType={s.incidentType}/></div>
            <div style={{color:C.dim,fontSize:10,letterSpacing:1.5,marginBottom:10}}>COMPLAINT TIMELINE</div>
            <Card style={{padding:"16px 20px",marginBottom:14}}><Timeline steps={s.timeline}/></Card>
            {s.status==="hr_overdue"&&<Card style={{padding:16,background:C.escalateL,border:`1.5px solid ${C.escalate}33`}}><div style={{fontWeight:600,fontSize:13,color:C.escalate,marginBottom:8}}>⚖️ What happens now?</div>{["Complaint forwarded to NCW","Women Helpline 181 notified","Cyber Crime portal notified if digital elements exist","Your identity remains fully protected"].map((t,i)=><div key={i} style={{display:"flex",gap:8,marginBottom:5}}><span style={{color:C.escalate,fontSize:11}}>•</span><span style={{color:C.sub,fontSize:12,lineHeight:1.5}}>{t}</span></div>)}</Card>}
          </div>
        ):(
          <div style={{flex:1,overflow:"hidden",display:"flex",flexDirection:"column"}}>
            {s.status==="hr_overdue"&&<div style={{background:C.dangerL,border:`1px solid ${C.danger}33`,borderRadius:10,padding:"8px 12px",marginBottom:10,fontSize:12,color:C.danger,fontWeight:500}}>🚨 HR non-responsive. Messages also forwarded to NCW.</div>}
            <HRChat complaint={s} shieldId={user.shieldId} showToast={showToast}/>
          </div>
        )}
      </div>
    </div>
  );
  return(
    <div style={{height:"100%",display:"flex",flexDirection:"column"}}>
      <div style={{padding:"20px 20px 12px",background:`linear-gradient(160deg,#FFF5F8,#FDE8EE)`}}>
        <h2 style={{fontFamily:"'Cormorant Garamond',serif",fontSize:24,fontWeight:600,color:C.ink}}>Complaint Tracker</h2>
        <p style={{color:C.dim,fontSize:12,marginTop:3,fontWeight:300}}>Status · HR Comms · Escalation</p>
      </div>
      {complaints.some(c=>c.status==="hr_overdue")&&<div style={{margin:"0 20px",marginTop:10,background:C.dangerL,borderLeft:`4px solid ${C.danger}`,borderRadius:12,padding:"10px 14px",display:"flex",gap:10,alignItems:"center"}}><span className="tick" style={{fontSize:18}}>🚨</span><div><div style={{color:C.danger,fontWeight:700,fontSize:12}}>HR Non-Responsive Detected</div><div style={{color:C.sub,fontSize:11}}>1 complaint exceeded 72h · Auto-escalated to NCW & 181</div></div></div>}
      <div style={{flex:1,overflowY:"auto",padding:"12px 20px 24px"}}>
        {complaints.map(c=>{const sm=statusMeta[c.status];const cd=cdwn(c.hrDeadline);const r=RISK[c.incidentType];return(
          <Card key={c.id} style={{marginBottom:12,cursor:"pointer"}} onClick={()=>{setSel(c.id);setView("timeline");}}>
            <div style={{padding:16}}>
              <div style={{display:"flex",justifyContent:"space-between",alignItems:"flex-start",marginBottom:8}}><div style={{flex:1,marginRight:8}}><div style={{fontSize:14,fontWeight:500,color:C.ink}}>{c.title}</div><div style={{color:C.dim,fontSize:11,marginTop:1}}>Filed {fmt(c.filedAt)}</div></div><Tag color={sm.color} bg={sm.bg}>{sm.label}</Tag></div>
              <div style={{display:"flex",gap:6,marginBottom:10,flexWrap:"wrap"}}>{r&&<Tag color={r.color} bg={r.bg}>{r.icon} {r.level}</Tag>}<Tag>{c.incidentType}</Tag></div>
              {c.status==="hr_overdue"?<div style={{background:C.dangerL,border:`1px solid ${C.danger}33`,borderRadius:10,padding:"8px 12px",display:"flex",justifyContent:"space-between"}}><span style={{color:C.danger,fontSize:12,fontWeight:600}}>🚨 72h Exceeded</span><span style={{color:C.danger,fontSize:11}}>Escalated →</span></div>:cd?<div style={{background:cd.h<12?C.dangerL:C.warnL,borderRadius:10,padding:"8px 12px"}}><div style={{display:"flex",justifyContent:"space-between",marginBottom:5}}><span style={{color:cd.h<12?C.danger:C.warn,fontSize:11,fontWeight:500}}>⏱ HR Deadline</span><span style={{color:cd.h<12?C.danger:C.warn,fontSize:11,fontWeight:600}}>{cd.h}h {cd.m}m left</span></div><div style={{background:"#fff",borderRadius:10,height:4,overflow:"hidden"}}><div style={{height:"100%",width:`${cd.pct}%`,background:cd.h<12?C.danger:C.warn,borderRadius:10}}/></div></div>:<div style={{background:C.successL,borderRadius:10,padding:"8px 12px"}}><span style={{color:C.success,fontSize:12,fontWeight:500}}>✓ HR responded in time</span></div>}
              <div style={{display:"flex",justifyContent:"space-between",alignItems:"center",marginTop:10,paddingTop:10,borderTop:`1px solid ${C.line}`}}><span style={{color:C.dim,fontSize:11}}>💬 {c.hrMessages.length} messages with HR</span><span style={{color:C.accent,fontSize:11,fontWeight:500}}>View →</span></div>
            </div>
          </Card>
        );})}
        <div style={{marginTop:4}}>
          <div style={{color:C.dim,fontSize:10,letterSpacing:1.5,marginBottom:10}}>EMERGENCY CONTACTS</div>
          <Card style={{padding:16}}>
            {[{icon:"📞",label:"Women Helpline",num:"181",color:"#DC2626",bg:C.dangerL},{icon:"🏛",label:"National Commission for Women",num:"NCW",color:C.escalate,bg:C.escalateL},{icon:"💻",label:"Cyber Crime Portal",num:"cybercrime.gov.in",color:C.info,bg:C.infoL}].map((e,i)=>(
              <div key={i} style={{display:"flex",alignItems:"center",gap:10,padding:"10px 0",borderBottom:i<2?`1px solid ${C.line}`:"none"}}>
                <span style={{fontSize:18}}>{e.icon}</span>
                <div style={{flex:1}}><div style={{fontSize:13,color:C.ink,fontWeight:500}}>{e.label}</div><div style={{fontSize:11,color:C.dim}}>{e.num}</div></div>
                <button onClick={()=>showToast(`Connecting to ${e.label}...`,"escalate")} style={{background:e.bg,border:`1px solid ${e.color}33`,borderRadius:20,padding:"5px 12px",color:e.color,fontSize:11,fontWeight:600,cursor:"pointer"}}>{e.num==="181"?"Call":"Visit"}</button>
              </div>
            ))}
          </Card>
        </div>
      </div>
    </div>
  );
};

// ─── SCORE SCREEN ──────────────────────────────────────────────────
const Score = ({showToast}) => {
  const [sel,setSel]=useState(MOCK_COMPANIES[0]);
  const [search,setSearch]=useState("");
  const [barW,setBarW]=useState("0%");
  const [tab,setTab]=useState("overview"); // overview | breakdown
  useEffect(()=>{setBarW("0%");setTimeout(()=>setBarW(`${sel.safetyScore}%`),80);},[sel]);
  const clr=s=>s>=75?C.success:s>=50?C.warn:C.danger;
  const filtered=MOCK_COMPANIES.filter(c=>c.name.toLowerCase().includes(search.toLowerCase())||c.location.toLowerCase().includes(search.toLowerCase()));

  const scoreBreakdown = (c) => {
    let score=100, items=[];
    if(!c.poshCommittee){score-=10;items.push({label:"No POSH Committee",delta:-10,color:C.danger});}
    else{items.push({label:"POSH Committee Active",delta:0,color:C.success,note:"✓"});}
    if(c.hrResponseDays>7){const d=Math.min(40,Math.floor((c.hrResponseDays-7)*2));score-=d;items.push({label:`HR Response: ${c.hrResponseDays} days`,delta:-d,color:C.danger});}
    else{items.push({label:`HR Response: ${c.hrResponseDays} days`,delta:0,color:C.success,note:"✓"});}
    const trainBonus=c.trainingPerYear*5;score+=trainBonus;
    items.push({label:`Training: ${c.trainingPerYear}×/year`,delta:trainBonus>0?trainBonus:0,color:trainBonus>0?C.success:C.warn});
    const compPenalty=c.openInvestigations*5;score-=compPenalty;
    items.push({label:`Open Investigations: ${c.openInvestigations}`,delta:-compPenalty,color:c.openInvestigations>3?C.danger:C.warn});
    const resPenalty=c.avgResolutionDays>30?Math.min(20,Math.floor((c.avgResolutionDays-30)/2)):0;if(resPenalty>0){score-=resPenalty;items.push({label:`Avg Resolution: ${c.avgResolutionDays} days`,delta:-resPenalty,color:C.danger});}
    return items;
  };

  return (
    <div style={{height:"100%",overflowY:"auto"}}>
      <div style={{padding:"20px 20px 12px",background:`linear-gradient(160deg,#FFF5F8,#FDE8EE)`}}>
        <h2 style={{fontFamily:"'Cormorant Garamond',serif",fontSize:24,fontWeight:600,color:C.ink}}>Safety Score</h2>
        <p style={{color:C.dim,fontSize:12,marginTop:3,fontWeight:300}}>POSH compliance · HR performance · Transparency</p>
        <input value={search} onChange={e=>setSearch(e.target.value)} placeholder="Search company or location..."
          style={{width:"100%",marginTop:12,background:C.surf,border:`1.5px solid ${C.line}`,borderRadius:12,padding:"11px 16px",color:C.ink,fontSize:14}}/>
      </div>

      {/* Featured card */}
      <div style={{padding:"12px 20px 14px"}}>
        <Card style={{padding:20}}>
          <div style={{display:"flex",justifyContent:"space-between",alignItems:"flex-start",marginBottom:10}}>
            <div style={{flex:1,marginRight:12}}>
              <div style={{fontFamily:"'Cormorant Garamond',serif",fontSize:20,fontWeight:600,color:C.ink}}>{sel.name}</div>
              <div style={{color:C.dim,fontSize:12,marginTop:2}}>{sel.location} · {sel.sector}</div>
              <div style={{display:"flex",gap:6,marginTop:8,flexWrap:"wrap"}}>
                <Tag color={sel.poshCommittee?C.success:C.danger} bg={sel.poshCommittee?C.successL:C.dangerL}>{sel.poshCommittee?"POSH Active":"No POSH ⚠"}</Tag>
                <Tag color={C.sub}>{sel.reviewCount} reviews</Tag>
              </div>
            </div>
            <div style={{textAlign:"right",flexShrink:0}}>
              <div style={{fontFamily:"'Cormorant Garamond',serif",fontSize:48,fontWeight:700,color:clr(sel.safetyScore),lineHeight:1}}>{sel.safetyScore}</div>
              <div style={{color:C.dim,fontSize:10}}>/ 100</div>
            </div>
          </div>

          {/* Score bar */}
          <div style={{background:C.pink1,borderRadius:10,height:8,overflow:"hidden",marginBottom:6}}>
            <div style={{height:"100%",width:barW,background:clr(sel.safetyScore),borderRadius:10,transition:"width 1.1s cubic-bezier(0.4,0,0.2,1)"}}/>
          </div>
          <div style={{display:"flex",justifyContent:"space-between",marginBottom:16}}>
            <span style={{color:C.dim,fontSize:10}}>0</span>
            <span style={{color:clr(sel.safetyScore),fontSize:10,fontWeight:600}}>{sel.safetyScore}/100 · {sel.safetyScore>=75?"GOOD":sel.safetyScore>=50?"AVERAGE":"POOR"}</span>
            <span style={{color:C.dim,fontSize:10}}>100</span>
          </div>

          {/* Sub-tabs */}
          <div style={{display:"flex",gap:6,marginBottom:14}}>
            {[["overview","Overview"],["breakdown","Score Breakdown"]].map(([v,l])=>(
              <button key={v} onClick={()=>setTab(v)} style={{flex:1,padding:"8px",borderRadius:20,background:tab===v?C.accent:C.surfDim,color:tab===v?"#fff":C.sub,fontSize:12,fontWeight:tab===v?600:400,border:`1px solid ${tab===v?C.accent:C.line}`,transition:"all 0.2s"}}>{l}</button>
            ))}
          </div>

          {tab==="overview"?(
            <>
              {[["POSH Committee",sel.poshCommittee?"Active":"Inactive",sel.poshCommittee?C.success:C.danger],
                ["HR Response Time",`${sel.hrResponseDays} days`,sel.hrResponseDays<=7?C.success:C.danger],
                ["Safety Training",`${sel.trainingPerYear}× / year`,sel.trainingPerYear>=2?C.success:C.warn],
                ["Open Investigations",`${sel.openInvestigations} pending`,sel.openInvestigations<=2?C.success:C.danger],
                ["Avg Resolution",`${sel.avgResolutionDays} days`,sel.avgResolutionDays<=14?C.success:sel.avgResolutionDays<=30?C.warn:C.danger],
                ["Complaints This Year",`${sel.complaintsThisYear} filed`,sel.complaintsThisYear<=2?C.success:C.warn],
              ].map(([l,v,c])=>(
                <div key={l} style={{display:"flex",justifyContent:"space-between",padding:"9px 0",borderBottom:`1px solid ${C.line}`}}>
                  <span style={{color:C.sub,fontSize:13}}>{l}</span>
                  <span style={{color:c,fontSize:13,fontWeight:500}}>{v}</span>
                </div>
              ))}
              <div style={{marginTop:12,display:"flex",justifyContent:"space-between",alignItems:"center"}}>
                <span style={{color:C.dim,fontSize:11}}>{sel.reviewCount} anonymous reviews</span>
                <button onClick={()=>showToast("Rating submitted anonymously.","success")} style={{background:C.accentL,border:`1.5px solid ${C.accentM}`,borderRadius:20,padding:"5px 14px",color:C.accent,fontSize:11,fontWeight:500,cursor:"pointer"}}>Rate ★</button>
              </div>
            </>
          ):(
            <>
              <div style={{color:C.dim,fontSize:10,letterSpacing:1,marginBottom:12}}>HOW THIS SCORE IS CALCULATED</div>
              <div style={{display:"flex",justifyContent:"space-between",padding:"8px 12px",background:`${C.success}18`,borderRadius:8,marginBottom:4}}>
                <span style={{fontSize:13,color:C.sub}}>Base Score</span>
                <span style={{color:C.success,fontWeight:600,fontSize:13}}>+100</span>
              </div>
              {scoreBreakdown(sel).map((item,i)=>(
                <div key={i} style={{display:"flex",justifyContent:"space-between",alignItems:"center",padding:"8px 12px",background:item.delta>0?`${C.success}10`:item.delta<0?`${C.danger}10`:C.surfDim,borderRadius:8,marginBottom:4}}>
                  <span style={{fontSize:12,color:C.sub,flex:1,marginRight:8}}>{item.label}</span>
                  <span style={{color:item.color,fontWeight:600,fontSize:13,flexShrink:0}}>
                    {item.delta>0?`+${item.delta}`:item.delta<0?item.delta:item.note||"0"}
                  </span>
                </div>
              ))}
              <div style={{marginTop:8,padding:"10px 12px",background:C.surfDim,borderRadius:8,display:"flex",justifyContent:"space-between"}}>
                <span style={{fontSize:13,fontWeight:600,color:C.ink}}>Final Score</span>
                <span style={{fontFamily:"'Cormorant Garamond',serif",fontSize:20,fontWeight:700,color:clr(sel.safetyScore)}}>{sel.safetyScore}</span>
              </div>
            </>
          )}
        </Card>
      </div>

      {/* Score formula reference */}
      <div style={{padding:"0 20px 14px"}}>
        <Card style={{padding:"16px 20px"}}>
          <div style={{color:C.dim,fontSize:10,letterSpacing:1.5,marginBottom:12}}>SCORING FORMULA</div>
          {[["Base score","+100"],["No POSH committee","−10"],["HR response > 7 days","−2 per extra day"],["Per unresolved complaint","−5"],["Per annual training session","+5"],["Avg resolution > 30 days","−2 per extra week"],].map(([l,v])=>(
            <div key={l} style={{display:"flex",justifyContent:"space-between",padding:"5px 0",fontSize:12}}>
              <span style={{color:C.dim}}>{l}</span><span style={{color:v.startsWith("+")?C.success:C.danger,fontWeight:500}}>{v}</span>
            </div>
          ))}
        </Card>
      </div>

      {/* Other companies */}
      <div style={{padding:"0 20px 24px"}}>
        <div style={{color:C.dim,fontSize:10,letterSpacing:1.5,marginBottom:10}}>ALL COMPANIES</div>
        {filtered.filter(c=>c.id!==sel.id).map(c=>(
          <Card key={c.id} style={{padding:"14px 16px",marginBottom:10,cursor:"pointer",display:"flex",justifyContent:"space-between",alignItems:"center"}} onClick={()=>{setSel(c);setTab("overview");}}>
            <div>
              <div style={{fontSize:14,color:C.ink}}>{c.name}</div>
              <div style={{color:C.dim,fontSize:11,marginTop:1}}>{c.location} · {c.sector}</div>
            </div>
            <div style={{display:"flex",alignItems:"center",gap:8}}>
              <div style={{textAlign:"right"}}>
                <div style={{fontFamily:"'Cormorant Garamond',serif",fontSize:28,fontWeight:700,color:clr(c.safetyScore),lineHeight:1}}>{c.safetyScore}</div>
                <div style={{display:"flex",alignItems:"center",gap:3,justifyContent:"flex-end",marginTop:2}}>
                  <span style={{color:c.trend==="up"?C.success:C.danger,fontSize:11}}>{c.trend==="up"?"↑":"↓"}</span>
                  <span style={{fontSize:10,color:C.dim}}>{c.trend==="up"?"Improving":"Declining"}</span>
                </div>
              </div>
            </div>
          </Card>
        ))}
      </div>
    </div>
  );
};

// ─── VAULT SCREEN ──────────────────────────────────────────────────
const Vault = ({showToast}) => {
  const [exp,setExp]=useState(null);
  const [modal,setModal]=useState(null);
  const [draftText,setDraftText]=useState("");
  const [loading,setLoading]=useState(false);
  const [lawView,setLawView]=useState(null); // view laws panel inside modal

  const genDraft=(e)=>{
    setModal(e);setLoading(true);setDraftText("");setLawView(null);
    setTimeout(()=>{setDraftText(LEGAL_DRAFT_FULL(e,e.incidentType||"Inappropriate Comments"));setLoading(false);},2200);
  };

  return (
    <div style={{height:"100%",display:"flex",flexDirection:"column",position:"relative"}}>
      <div style={{padding:"20px 20px 12px",background:`linear-gradient(160deg,#FFF5F8,#FDE8EE)`}}>
        <h2 style={{fontFamily:"'Cormorant Garamond',serif",fontSize:24,fontWeight:600,color:C.ink}}>Proof Vault</h2>
        <div style={{marginTop:10,background:C.surf,border:`1.5px solid ${C.line}`,borderRadius:12,padding:"10px 16px",display:"flex",gap:8,alignItems:"center"}}>
          <span>🔐</span>
          <span style={{fontSize:11,color:C.sub,fontWeight:300}}>AES-256 + Polygon Blockchain · Only you hold the key</span>
        </div>
      </div>
      <div style={{flex:1,overflowY:"auto",padding:"12px 20px"}}>
        {MOCK_VAULT.map(e=>(
          <Card key={e.id} style={{marginBottom:12,cursor:"pointer"}} onClick={()=>setExp(exp===e.id?null:e.id)}>
            <div style={{padding:16}}>
              <div style={{display:"flex",justifyContent:"space-between",alignItems:"flex-start",marginBottom:8}}>
                <div style={{fontSize:14,fontWeight:500,color:C.ink}}>{e.title}</div>
                <Tag color={e.status==="danger"?C.accent:C.warn} bg={e.status==="danger"?C.accentL:C.warnL}>{e.status==="danger"?"🚨 Danger":"⚠️ Caution"}</Tag>
              </div>
              <div style={{color:C.dim,fontSize:12,marginBottom:8}}>{fmt(e.date)} · {e.witnesses} witness{e.witnesses!==1?"es":""}</div>
              {e.incidentType&&<RiskBadge incidentType={e.incidentType} compact/>}
              <div style={{display:"flex",flexWrap:"wrap",gap:5,marginTop:8,marginBottom:8}}>
                {e.evidence.map(ev=><Tag key={ev} color={C.sub} bg={C.surfDim}>{ev}</Tag>)}
              </div>
              <div style={{display:"flex",justifyContent:"space-between"}}>
                <span style={{fontSize:10,color:C.dim,fontFamily:"monospace"}}>{e.blockchainHash}</span>
                <span style={{fontSize:11,color:e.poshDeadline<30?C.accent:C.sub,fontWeight:e.poshDeadline<30?500:400}}>{e.poshDeadline}d POSH deadline</span>
              </div>
            </div>
            {exp===e.id&&(
              <div className="fade-up" style={{borderTop:`1.5px solid ${C.line}`,padding:"14px 16px",background:C.surfDim}}>
                <div style={{display:"flex",gap:8,marginBottom:12}}>
                  {[["EMOTION",e.emotionLog,e.emotionLog.includes("High")?C.accent:C.sub],["BLOCKCHAIN","✓ Verified",C.success]].map(([l,v,c])=>(
                    <div key={l} style={{flex:1,background:C.surf,border:`1.5px solid ${C.line}`,borderRadius:10,padding:"10px 12px"}}>
                      <div style={{color:C.dim,fontSize:10,letterSpacing:1}}>{l}</div>
                      <div style={{color:c,fontSize:12,fontWeight:500,marginTop:4}}>{v}</div>
                    </div>
                  ))}
                </div>
                <Btn onClick={ev=>{ev.stopPropagation();genDraft(e);}} style={{marginBottom:8}}>⚖ Generate Legal Draft</Btn>
                <div style={{display:"flex",gap:8}}>
                  <Btn variant="ghost" onClick={ev=>ev.stopPropagation()} style={{fontSize:12,padding:"10px",borderRadius:50}}>Submit to ICC</Btn>
                  <Btn variant="ghost" onClick={ev=>ev.stopPropagation()} style={{fontSize:12,padding:"10px",borderRadius:50}}>Edit Entry</Btn>
                </div>
              </div>
            )}
          </Card>
        ))}
        <button onClick={()=>showToast("New entry created!","success")} style={{width:"100%",padding:13,background:"none",border:`2px dashed ${C.pink2}`,borderRadius:14,color:C.dim,fontSize:13,marginBottom:12}}>+ Add New Entry</button>
      </div>

      {/* Legal Draft Modal */}
      {modal&&(
        <div style={{position:"absolute",inset:0,background:"rgba(253,240,243,0.93)",zIndex:100,display:"flex",alignItems:"flex-end"}} onClick={()=>!loading&&setModal(null)}>
          <div className="slide-up" style={{background:C.surf,borderRadius:"20px 20px 0 0",border:`1.5px solid ${C.line}`,padding:20,width:"100%",maxHeight:"88%",display:"flex",flexDirection:"column"}} onClick={e=>e.stopPropagation()}>
            <div style={{display:"flex",justifyContent:"space-between",alignItems:"center",marginBottom:10}}>
              <div>
                <div style={{fontFamily:"'Cormorant Garamond',serif",fontSize:18,fontWeight:600,color:C.ink}}>Legal Complaint Draft</div>
                <div style={{color:C.dim,fontSize:11,marginTop:2}}>POSH Act 2013 · IPC · IT Act · Auto-generated</div>
              </div>
              <button onClick={()=>setModal(null)} style={{background:C.surfDim,border:`1.5px solid ${C.line}`,borderRadius:"50%",width:32,height:32,color:C.sub,fontSize:16,display:"flex",alignItems:"center",justifyContent:"center"}}>×</button>
            </div>

            {loading?(
              <div style={{flex:1,display:"flex",flexDirection:"column",alignItems:"center",justifyContent:"center",gap:14}}>
                <Spinner/>
                <div style={{textAlign:"center"}}>
                  {["Identifying applicable IPC sections","Mapping POSH Act provisions","Cross-referencing IT Act clauses","Generating formal complaint..."].map((t,i)=>(
                    <div key={i} style={{color:i===0?C.gold:C.sub,fontSize:12,padding:"3px 0",opacity:0,animation:`fadeUp 0.4s ease ${i*0.45}s both`}}>{t}</div>
                  ))}
                </div>
              </div>
            ):(
              <>
                {/* Applicable laws quick view */}
                {modal.incidentType&&(()=>{
                  const laws=LAW_DB[modal.incidentType];
                  if(!laws)return null;
                  return(
                    <div style={{background:C.goldL,border:`1.5px solid ${C.gold}33`,borderRadius:12,padding:"10px 14px",marginBottom:10}}>
                      <div style={{display:"flex",justifyContent:"space-between",alignItems:"center",marginBottom:8}}>
                        <div style={{color:C.gold,fontWeight:600,fontSize:12}}>⚖️ Laws Applied in This Draft</div>
                        <button onClick={()=>setLawView(lawView?"hide":"show")} style={{background:"none",color:C.gold,fontSize:11,fontWeight:500}}>{lawView?"Hide":"Show all"}</button>
                      </div>
                      <div style={{display:"flex",flexWrap:"wrap",gap:5}}>
                        {laws.ipc.map(l=><Tag key={l.sec} color={C.gold} bg={C.goldL} style={{border:`1px solid ${C.gold}33`}}>{l.sec}</Tag>)}
                        {laws.posh.slice(0,2).map(s=><Tag key={s} color={C.success} bg={C.successL}>POSH {s.split(" ")[0]} {s.split(" ")[1]}</Tag>)}
                        {laws.it.map(l=><Tag key={l.sec} color={C.info} bg={C.infoL}>{l.sec}</Tag>)}
                      </div>
                      {lawView&&(
                        <div className="fade-up" style={{marginTop:10}}>
                          {laws.ipc.length>0&&<><div style={{color:C.dim,fontSize:10,letterSpacing:1,marginBottom:6}}>IPC SECTIONS</div>{laws.ipc.map(l=><div key={l.sec} style={{marginBottom:8,padding:"8px 10px",background:"#fff",borderRadius:8,borderLeft:`3px solid ${C.gold}`}}><div style={{fontWeight:600,fontSize:12,color:C.gold}}>{l.sec} — {l.title}</div><div style={{fontSize:11,color:C.sub,marginTop:2,lineHeight:1.5}}>{l.desc}</div></div>)}</>}
                          {laws.it.length>0&&<><div style={{color:C.dim,fontSize:10,letterSpacing:1,marginBottom:6,marginTop:8}}>IT ACT SECTIONS</div>{laws.it.map(l=><div key={l.sec} style={{marginBottom:8,padding:"8px 10px",background:"#fff",borderRadius:8,borderLeft:`3px solid ${C.info}`}}><div style={{fontWeight:600,fontSize:12,color:C.info}}>{l.sec} — {l.title}</div><div style={{fontSize:11,color:C.sub,marginTop:2,lineHeight:1.5}}>{l.desc}</div></div>)}</>}
                        </div>
                      )}
                    </div>
                  );
                })()}
                <div style={{flex:1,overflowY:"auto",background:C.surfDim,borderRadius:12,padding:16,fontFamily:"monospace",fontSize:11,color:C.sub,lineHeight:1.8,whiteSpace:"pre-wrap",marginBottom:12}}>{draftText}</div>
                <div style={{display:"flex",gap:8}}>
                  <Btn onClick={()=>{showToast("Complaint submitted to ICC with blockchain proof.","success");setModal(null);}}>Submit to ICC</Btn>
                  <Btn variant="law" onClick={()=>showToast("Draft saved — open in document editor.","law")}>Save Draft</Btn>
                </div>
              </>
            )}
          </div>
        </div>
      )}
    </div>
  );
};

// ─── SIGNAL SCREEN ─────────────────────────────────────────────────
const Signal = ({showToast,onConvergence}) => {
  const [step,setStep]=useState(0);
  const [sel,setSel]=useState({role:null,location:null,incident:null});
  const [loading,setLoading]=useState(false);
  const [done,setDone]=useState(false);
  const [count,setCount]=useState(3);
  const steps=[{label:"Who was involved?",key:"role",opts:["Manager","Senior Leader","Colleague","Client","HR Personnel"]},{label:"Where did it happen?",key:"location",opts:["Meeting Room","Office Floor","WhatsApp","Email","Video Call","Corridor"]},{label:"Type of incident?",key:"incident",opts:["Inappropriate Comments","Unwanted Contact","Discrimination","Threats","Unsafe Environment"]}];
  const pick=(key,val)=>{setSel(s=>({...s,[key]:val}));if(step<2)setTimeout(()=>setStep(s=>s+1),260);};
  const submit=()=>{setLoading(true);setTimeout(()=>{setLoading(false);setDone(true);const n=count+1;setCount(n);onConvergence(n);},2800);};
  const reset=()=>{setStep(0);setSel({role:null,location:null,incident:null});setDone(false);};

  if(loading)return(
    <div style={{height:"100%",display:"flex",flexDirection:"column",alignItems:"center",justifyContent:"center",gap:16,padding:32,background:`linear-gradient(160deg,#FFF5F8,#FDE8EE)`}}>
      <div className="floating"><Shield size={48}/></div>
      <div style={{textAlign:"center",marginTop:8}}>
        {["Encrypting signal","Anonymizing identity","Running IPC risk mapping","Checking convergence patterns"].map((t,i)=>(
          <div key={i} style={{color:i===0?C.accent:C.sub,fontSize:13,fontWeight:i===0?500:300,padding:"4px 0",opacity:0,animation:`fadeUp 0.4s ease ${i*0.55}s both`}}>{t}...</div>
        ))}
      </div>
    </div>
  );

  if(done){
    const risk=RISK[sel.incident];
    const laws=LAW_DB[sel.incident];
    return(
      <div style={{height:"100%",overflowY:"auto",padding:24}} className="fade-up">
        <div style={{textAlign:"center",marginBottom:20}}>
          <div style={{fontSize:48,marginBottom:10}}>🌸</div>
          <h2 style={{fontFamily:"'Cormorant Garamond',serif",fontSize:28,fontWeight:600,color:C.ink}}>Signal Received</h2>
          <p style={{color:C.sub,fontSize:13,marginTop:6,fontWeight:300}}>Encrypted · Anonymous · IPC-mapped</p>
        </div>
        {risk&&<div style={{marginBottom:14}}><div style={{color:C.dim,fontSize:10,letterSpacing:1.5,marginBottom:8}}>RISK ANALYSIS</div><RiskBadge incidentType={sel.incident}/></div>}
        {/* Applicable laws */}
        {laws&&(
          <div style={{marginBottom:14,background:C.goldL,border:`1.5px solid ${C.gold}33`,borderLeft:`4px solid ${C.gold}`,borderRadius:14,padding:14}}>
            <div style={{color:C.gold,fontWeight:600,fontSize:12,marginBottom:8}}>⚖️ Applicable Indian Laws</div>
            {laws.ipc.map(l=><div key={l.sec} style={{marginBottom:6}}><span style={{color:C.gold,fontWeight:600,fontSize:12}}>{l.sec}</span><span style={{color:C.sub,fontSize:12}}> — {l.title}</span></div>)}
            {laws.it.map(l=><div key={l.sec} style={{marginBottom:6}}><span style={{color:C.info,fontWeight:600,fontSize:12}}>{l.sec}</span><span style={{color:C.sub,fontSize:12}}> — {l.title}</span></div>)}
            {laws.posh.slice(0,2).map(s=><div key={s} style={{marginBottom:4}}><span style={{color:C.success,fontWeight:600,fontSize:12}}>POSH {s}</span></div>)}
          </div>
        )}
        {/* Convergence */}
        <Card style={{padding:18,marginBottom:14}}>
          <div style={{display:"flex",justifyContent:"space-between",marginBottom:10}}><span style={{fontSize:13,fontWeight:500}}>Convergence Meter</span><span style={{fontSize:13,color:count>=5?C.accent:C.sub,fontWeight:600}}>{Math.min(count,5)}/5</span></div>
          <div style={{background:C.pink1,borderRadius:10,height:6,overflow:"hidden",marginBottom:12}}><div style={{height:"100%",width:`${Math.min(count/5*100,100)}%`,background:count>=5?C.accent:C.pink3,borderRadius:10,transition:"width 1s ease"}}/></div>
          <div style={{display:"flex",gap:5}}>{[1,2,3,4,5].map(n=><div key={n} style={{flex:1,height:26,borderRadius:8,background:n<=count?(n>=5?C.accent:C.pink2):C.surfDim,display:"flex",alignItems:"center",justifyContent:"center",fontSize:10,color:n<=count?(n>=5?"#fff":C.accent):C.dim,fontWeight:600}}>{n}</div>)}</div>
          {count>=5&&<div style={{marginTop:12,background:C.accentL,border:`1.5px solid ${C.accentM}`,borderRadius:10,padding:"10px 14px"}}><div style={{color:C.accent,fontWeight:600,fontSize:12}}>Convergence Alert Triggered</div><div style={{color:C.sub,fontSize:11}}>Anonymous HR alert dispatched</div></div>}
        </Card>
        {risk?.level==="MAJOR"&&<div style={{background:C.dangerL,border:`1.5px solid ${C.danger}33`,borderLeft:`4px solid ${C.danger}`,borderRadius:14,padding:14,marginBottom:14}}><div style={{fontWeight:600,fontSize:13,color:C.danger,marginBottom:8}}>⚡ Major Incident Protocol</div>{["HR anonymously notified — 72h response required","Evidence priority-flagged for preservation","Auto-escalation to NCW if HR doesn't respond","Criminal complaint possible under cited IPC sections"].map((t,i)=><div key={i} style={{display:"flex",gap:8,marginBottom:5}}><span style={{color:C.danger,fontSize:11}}>•</span><span style={{color:C.sub,fontSize:12,lineHeight:1.5}}>{t}</span></div>)}</div>}
        <Card style={{marginBottom:18}}>{Object.entries(sel).map(([k,v],i,a)=>v&&<div key={k} style={{padding:"11px 16px",display:"flex",justifyContent:"space-between",borderBottom:i<a.length-1?`1px solid ${C.line}`:"none"}}><span style={{color:C.dim,fontSize:13,textTransform:"capitalize"}}>{k}</span><span style={{color:C.sub,fontSize:13}}>{v}</span></div>)}</Card>
        <Btn variant="soft" onClick={reset}>Send Another Signal</Btn>
      </div>
    );
  }

  const curr=steps[step];
  const allDone=sel.role&&sel.location&&sel.incident;
  return(
    <div style={{height:"100%",display:"flex",flexDirection:"column"}}>
      <div style={{padding:"20px 20px 14px",background:`linear-gradient(160deg,#FFF5F8,#FDE8EE)`}}>
        <h2 style={{fontFamily:"'Cormorant Garamond',serif",fontSize:24,fontWeight:600,color:C.ink}}>Whisper Signal</h2>
        <p style={{color:C.dim,fontSize:12,marginTop:3,fontWeight:300}}>Anonymous · Encrypted · IPC Risk-Scored</p>
        <div style={{display:"flex",gap:5,marginTop:14}}>{steps.map((_,i)=><div key={i} style={{flex:1,height:3,borderRadius:10,background:i<=step?C.accent:C.pink1,transition:"background 0.3s"}}/>)}</div>
        <div style={{color:C.dim,fontSize:10,marginTop:5}}>STEP {step+1} OF {steps.length}</div>
      </div>
      {step===2&&sel.incident&&<div className="fade-up" style={{padding:"10px 20px 0"}}><RiskBadge incidentType={sel.incident} compact/></div>}
      <div style={{flex:1,overflowY:"auto",padding:"14px 20px"}}>
        <div key={step} className="fade-up">
          <div style={{fontWeight:500,fontSize:14,marginBottom:14,color:C.ink}}>{curr.label}</div>
          <div style={{display:"grid",gridTemplateColumns:"1fr 1fr",gap:8}}>
            {curr.opts.map(opt=>{
              const r=step===2?RISK[opt]:null;
              return(
                <button key={opt} onClick={()=>pick(curr.key,opt)} style={{padding:"13px 12px",borderRadius:14,textAlign:"left",background:sel[curr.key]===opt?C.accentL:C.surf,border:`1.5px solid ${sel[curr.key]===opt?C.accent:C.line}`,color:sel[curr.key]===opt?C.accent:C.sub,fontSize:12,fontWeight:sel[curr.key]===opt?500:400,transition:"all 0.15s"}}>
                  {opt}{r&&<span style={{display:"block",fontSize:10,color:r.color,marginTop:2,fontWeight:500}}>{r.icon} {r.level}</span>}
                </button>
              );
            })}
          </div>
        </div>
      </div>
      <div style={{padding:"12px 20px 16px",display:"flex",gap:8}}>
        {step>0&&<Btn variant="ghost" onClick={()=>setStep(s=>s-1)} full={false} style={{padding:"13px 20px",borderRadius:50}}>← Back</Btn>}
        {allDone&&<Btn onClick={submit}>Send Signal →</Btn>}
      </div>
    </div>
  );
};

// ─── CIRCLE ────────────────────────────────────────────────────────
const Circle = ({user,showToast}) => {
  const [posts,setPosts]=useState(MOCK_CIRCLE);
  const [text,setText]=useState("");
  const [voted,setVoted]=useState(new Set());
  const vote=id=>{if(voted.has(id))return;setPosts(ps=>ps.map(p=>p.id===id?{...p,helpfulCount:p.helpfulCount+1}:p));setVoted(v=>new Set([...v,id]));};
  const send=()=>{if(!text.trim())return;setPosts(ps=>[{id:`p${Date.now()}`,shieldId:user.shieldId,sector:"IT/Software",city:"Bengaluru",message:text,helpfulCount:0,timestamp:Date.now(),isVerified:false},...ps]);setText("");showToast("Posted anonymously","success");};
  return(
    <div style={{height:"100%",display:"flex",flexDirection:"column"}}>
      <div style={{padding:"20px 20px 14px",background:`linear-gradient(160deg,#FFF5F8,#FDE8EE)`,borderBottom:`1.5px solid ${C.line}`}}>
        <div style={{display:"flex",justifyContent:"space-between",alignItems:"flex-start"}}>
          <div><h2 style={{fontFamily:"'Cormorant Garamond',serif",fontSize:24,fontWeight:600,color:C.ink}}>Circle of Trust</h2><div style={{color:C.dim,fontSize:12,marginTop:2,fontWeight:300}}>Anonymous peer support</div></div>
          <Tag color={C.success} bg={C.successL}>● 2,411 online</Tag>
        </div>
      </div>
      <div style={{flex:1,overflowY:"auto",padding:"12px 20px"}}>
        {posts.map(p=>(
          <Card key={p.id} style={{padding:16,marginBottom:12,borderLeft:p.isVerified?`3px solid ${C.ink}`:"none"}}>
            <div style={{display:"flex",justifyContent:"space-between",alignItems:"flex-start",marginBottom:10}}>
              <div style={{display:"flex",gap:8,alignItems:"center"}}>
                <div style={{width:32,height:32,borderRadius:"50%",background:p.isVerified?C.surfDim:C.accentL,display:"flex",alignItems:"center",justifyContent:"center",fontSize:14}}>{p.isVerified?"⚖️":"🛡"}</div>
                <div>
                  <div style={{display:"flex",alignItems:"center",gap:6}}><span style={{fontSize:13,fontWeight:p.isVerified?600:400,color:C.ink}}>{p.shieldId}</span>{p.isVerified&&<Tag color={C.sub} bg={C.surfDim}>Verified</Tag>}</div>
                  <div style={{fontSize:11,color:C.dim}}>{p.sector} · {p.city}</div>
                </div>
              </div>
              <span style={{fontSize:11,color:C.dim}}>{ago(p.timestamp)}</span>
            </div>
            <p style={{fontSize:13,color:C.sub,lineHeight:1.7,marginBottom:12,fontWeight:300}}>{p.message}</p>
            <div style={{display:"flex",gap:8}}>
              <button onClick={()=>vote(p.id)} style={{display:"flex",alignItems:"center",gap:5,background:voted.has(p.id)?C.successL:C.surfDim,border:`1.5px solid ${voted.has(p.id)?C.success+"55":C.line}`,borderRadius:20,padding:"5px 12px",color:voted.has(p.id)?C.success:C.dim,fontSize:12,cursor:voted.has(p.id)?"default":"pointer"}}>↑ {p.helpfulCount}</button>
              <button onClick={()=>showToast("Reply coming soon.","warn")} style={{flex:1,background:C.surfDim,border:`1.5px solid ${C.line}`,borderRadius:20,padding:"5px 12px",color:C.dim,fontSize:12}}>Reply anonymously</button>
            </div>
          </Card>
        ))}
      </div>
      <div style={{padding:"12px 20px 20px",borderTop:`1.5px solid ${C.line}`,background:C.surf}}>
        <div style={{display:"flex",gap:8,alignItems:"flex-end"}}>
          <textarea value={text} onChange={e=>setText(e.target.value)} placeholder="Share with the Circle..." rows={2} style={{flex:1,background:C.surfDim,border:`1.5px solid ${C.line}`,borderRadius:14,padding:"11px 13px",color:C.ink,fontSize:13,resize:"none",lineHeight:1.5}}/>
          <button onClick={send} disabled={!text.trim()} style={{width:40,height:40,borderRadius:"50%",background:text.trim()?C.accent:C.pink1,color:"#fff",fontSize:16,display:"flex",alignItems:"center",justifyContent:"center",flexShrink:0,transition:"all 0.2s",border:"none"}}>↑</button>
        </div>
        <div style={{color:C.dim,fontSize:11,marginTop:7}}>Posting as {user.shieldId}</div>
      </div>
    </div>
  );
};

// ─── HOME ──────────────────────────────────────────────────────────
const Home = ({user,onNav,showToast,convCount}) => {
  const stats=[{label:"Vault Entries",val:3,icon:"🔒",bg:"#FFF0F5"},{label:"POSH Deadline",val:"17d",icon:"⏳",bg:"#FEF9EC"},{label:"Days Protected",val:47,icon:"🛡",bg:"#F0FAF5"},{label:"Circle Members",val:"2.4K",icon:"💗",bg:"#F5F0FF"}];
  const activity=[{text:"HR Deadline exceeded — NCW & 181 notified",time:"2h ago",urgent:true},{text:"IPC § 354A — Signal risk-mapped",time:"4h ago",urgent:false},{text:"Vault entry locked & blockchain-anchored",time:"1d ago",urgent:false},{text:"Legal Advisor replied in Circle",time:"2d ago",urgent:false}];
  return(
    <div style={{height:"100%",overflowY:"auto"}}>
      <div style={{background:`linear-gradient(160deg,#FFF5F8,#FDE8EE)`,padding:"22px 20px 20px"}}>
        <div style={{display:"flex",justifyContent:"space-between",alignItems:"center",marginBottom:20}}>
          <div><div style={{color:C.dim,fontSize:12,fontWeight:300}}>Welcome back</div><div style={{color:C.accent,fontWeight:600,fontSize:15,marginTop:1}}>{user.shieldId}</div></div>
          <Tag color={C.success} bg={C.successL}>● Protected</Tag>
        </div>
        <div style={{textAlign:"center",padding:"8px 0 4px"}}>
          <button onClick={()=>showToast("SOS sent anonymously to your Circle.","error")} style={{width:114,height:114,borderRadius:"50%",background:C.surf,border:`2.5px solid ${C.accent}`,color:C.accent,fontSize:11,fontWeight:600,letterSpacing:0.8,display:"inline-flex",flexDirection:"column",alignItems:"center",justifyContent:"center",gap:5,boxShadow:`0 0 0 10px ${C.accentL},0 4px 20px ${C.accentM}66`,transition:"all 0.2s"}}>
            <Shield size={28}/><span style={{lineHeight:1.3}}>I FEEL<br/>UNSAFE</span>
          </button>
        </div>
      </div>
      <div style={{margin:"14px 20px 0",background:C.dangerL,border:`1.5px solid ${C.danger}33`,borderLeft:`4px solid ${C.danger}`,borderRadius:12,padding:"10px 14px",display:"flex",gap:10,alignItems:"center",cursor:"pointer"}} onClick={()=>onNav("track")}>
        <span className="tick" style={{fontSize:18}}>🚨</span>
        <div style={{flex:1}}><div style={{color:C.danger,fontWeight:700,fontSize:12}}>HR Non-Responsive · Auto-Escalated</div><div style={{color:C.sub,fontSize:11}}>NCW + Women Helpline 181 notified</div></div>
        <span style={{color:C.danger,fontSize:11}}>Track →</span>
      </div>
      {convCount>=3&&<div style={{margin:"10px 20px 0",background:C.accentL,border:`1.5px solid ${C.accentM}`,borderRadius:12,padding:"10px 14px",display:"flex",gap:10,alignItems:"center"}}><span>⚠️</span><div><div style={{color:C.accent,fontWeight:600,fontSize:12}}>Convergence {convCount>=5?"Triggered":"Building"}</div><div style={{color:C.sub,fontSize:11}}>{convCount}/5 signals from your dept</div></div></div>}
      <div style={{padding:"14px 20px 0",display:"grid",gridTemplateColumns:"1fr 1fr",gap:10}}>
        {stats.map((s,i)=><Card key={i} style={{padding:"16px 14px",background:s.bg}}><div style={{fontSize:20,marginBottom:7}}>{s.icon}</div><div style={{fontFamily:"'Cormorant Garamond',serif",fontSize:28,fontWeight:600,color:C.ink}}>{s.val}</div><div style={{color:C.sub,fontSize:11,marginTop:2}}>{s.label}</div></Card>)}
      </div>
      <div style={{padding:"14px 20px 0"}}>
        <div style={{color:C.dim,fontSize:10,letterSpacing:1.5,marginBottom:10}}>QUICK ACTIONS</div>
        <div style={{display:"grid",gridTemplateColumns:"1fr 1fr",gap:8}}>
          {[{label:"📡 File Signal",screen:"signal"},{label:"🔒 Lock Proof",screen:"vault"},{label:"📋 Track Status",screen:"track"},{label:"★ Safety Score",screen:"score"}].map((a,i)=>(
            <button key={i} onClick={()=>onNav(a.screen)} style={{padding:"11px 8px",background:C.surf,border:`1.5px solid ${C.line}`,borderRadius:12,color:C.sub,fontSize:12,transition:"all 0.15s"}}>{a.label}</button>
          ))}
        </div>
      </div>
      <div style={{padding:"14px 20px 24px"}}>
        <div style={{color:C.dim,fontSize:10,letterSpacing:1.5,marginBottom:10}}>RECENT ACTIVITY</div>
        <Card>{activity.map((a,i)=><div key={i} style={{padding:"11px 16px",display:"flex",justifyContent:"space-between",alignItems:"center",borderBottom:i<activity.length-1?`1px solid ${C.line}`:"none",background:a.urgent?"#FFF5F5":"transparent"}}><div style={{display:"flex",gap:8,alignItems:"center"}}>{a.urgent&&<div style={{width:6,height:6,borderRadius:"50%",background:C.danger,flexShrink:0}}/>}<span style={{fontSize:12,color:a.urgent?C.danger:C.sub}}>{a.text}</span></div><span style={{fontSize:11,color:C.dim,flexShrink:0,marginLeft:8}}>{a.time}</span></div>)}</Card>
      </div>
    </div>
  );
};

// ─── ROOT ──────────────────────────────────────────────────────────
export default function App() {
  const [phase,setPhase]=useState("splash");
  const [tab,setTab]=useState("home");
  const [user,setUser]=useState(null);
  const [toast,setToast]=useState(null);
  const [convCount,setConvCount]=useState(3);
  const showToast=(msg,type="success")=>setToast({msg,type,id:Date.now()});
  const login=u=>{setUser({...u,vaultCount:3,daysProtected:47});setPhase("main");};
  const screens={home:Home,signal:Signal,vault:Vault,track:Track,score:Score,circle:Circle};
  const Active=screens[tab];
  return(
    <>
      <style>{G}</style>
      <div style={{width:"100%",maxWidth:430,margin:"0 auto",height:"100vh",display:"flex",flexDirection:"column",background:C.bg,position:"relative",overflow:"hidden",boxShadow:"0 0 60px rgba(212,84,122,0.08)"}}>
        {toast&&<Toast key={toast.id} msg={toast.msg} type={toast.type} onClose={()=>setToast(null)}/>}
        {phase==="splash"  &&<Splash onDone={()=>setPhase("onboard")}/>}
        {phase==="onboard" &&<Onboarding onDone={()=>setPhase("login")}/>}
        {phase==="login"   &&<Login onLogin={login}/>}
        {phase==="main"    &&(
          <>
            <div style={{flex:1,overflow:"hidden"}} key={tab} className="fade-up"><Active user={user} onNav={setTab} showToast={showToast} convCount={convCount} onConvergence={setConvCount}/></div>
            <Nav active={tab} onChange={setTab}/>
          </>
        )}
      </div>
    </>
  );
}
