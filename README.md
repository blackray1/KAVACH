# KAVACH

🛡️ KAVACH — Smart Workplace Safety & Anonymous Reporting System

> "Because silence should never be the only option."*

KAVACH is a mobile-first React application designed to empower women in the workplace to report harassment anonymously, preserve tamper-proof evidence, and collectively trigger HR alerts — all without ever revealing their identity.

Built for hackathons, presented as a production-ready prototype.

---

📸 Screens

| Splash & Onboarding | Home Dashboard | Whisper Signal |
|---|---|---|
| Animated shield logo, 3-slide intro | SOS button, stats, activity feed | 3-step anonymous incident flow |

| Proof Vault | Safety Score | Circle of Trust |
|---|---|---|
| Blockchain-anchored evidence cards | Company safety ratings | Anonymous peer support feed |

---
✨ Features

🔐 Zero-Knowledge Authentication
- Anonymous Mode** — Firebase anonymous auth generates a random `Shield_XXXXXX` ID. No email, no name, no trace.
- Work Email Mode** — Email is deleted immediately post-login and replaced with an anonymous token.

📡 Whisper Signal (AI-Powered Reporting)
- 3-step guided incident report: *Who → Where → What*
- Signals stored anonymously under department namespace
- **Convergence Algorithm** — When 5+ signals come from the same department within 30 days, an automatic anonymous HR alert is triggered
- Real-time convergence meter with live threshold indicators

🔒 Proof Vault 2.0
- Evidence entries with status badges (Danger / Caution)
- Each entry contains: evidence tags, blockchain hash, emotion log, POSH deadline countdown, witness count
- AES-256 encrypted** storage with Polygon blockchain anchoring (mock for demo)
- One-click Legal Draft** — GPT-4 generates a formal POSH Act 2013 complaint letter with numbered evidence exhibits and legal section references

📊 Company Safety Score
- Algorithmic safety score (0–100) for companies
- Score breakdown: POSH committee status, HR response time, training frequency, open investigations
- Transparent scoring formula displayed in-app
- Search and compare companies

💗 Circle of Trust
- Anonymous peer support feed
- Upvote helpful posts
- Verified Legal Advisor** badge for certified advisors
- Post anonymously — tied only to your Shield ID

---

🛠️ Tech Stack

| Layer | Technology |
|---|---|
| Frontend | React.js (hooks-based, single page) |
| Styling | Tailwind CSS + inline CSS-in-JS |
| Fonts | Cormorant Garamond + Outfit (Google Fonts) |
| Backend | Node.js + Express (REST API) |
| Database | Firebase Realtime Database |
| Auth | Firebase Anonymous Authentication |
| AI | OpenAI GPT-4 API (legal draft generation) |
| Charts | Recharts |
| Blockchain | Polygon (SHA-256 hash anchoring) |

---

🎨 Design System

Theme:Light Baby Pink — warm, approachable, yet serious.

Background   #FDF0F3   Soft blush
Surface      #FFFFFF   White cards
Accent       #D4547A   Deep rose
Border       #F2C9D4   Pink lines
Ink          #2C1A1F   Near-black text
Muted        #C49AAA   Soft mauve
Success      #4A7C6B   Muted teal

- Mobile-first, max-width 430px
- Pill-shaped buttons, 18px card radius
- Cormorant Garamond for all display headings
- Subtle pink gradient headers on every screen


🗂️ Firebase Data Structure

/users/{anonymousToken}
  - createdAt
  - shieldId           → e.g. "Shield_7x9k2m"
  - daysProtected
  - vaultCount

/signals/{department}/{timestamp}
  - role
  - location
  - incidentType
  - anonymousToken     → NOT real identity
  - convergenceCount

/vault/{anonymousToken}/{entryId}
  - title
  - date
  - status             → "danger" | "caution"
  - evidence[]
  - blockchainHash
  - emotionLog
  - poshDeadline       → incidentDate + 90 days
  - witnesses

/companies/{companyId}
  - name, location, sector
  - safetyScore
  - poshCommittee      → boolean
  - hrResponseDays
  - trainingPerYear
  - openInvestigations

/circle/{postId}
  - anonymousToken
  - sector, city
  - message
  - helpfulCount
  - timestamp
  - isVerified         → boolean
⚙️ Safety Score Algorithm
Base score:                    100
Per unresolved complaint:       −5
No POSH committee active:      −10
HR response time > 7 days:      −8
Per annual training session:    +5
🤖 GPT-4 Legal Draft Prompt

When a user clicks **"Generate Legal Draft"**, the following is sent to GPT-4:

System:
  You are a legal expert specializing in India's POSH Act 2013.
  Generate formal legal complaint letters.

User:
  Generate a complaint under POSH Act 2013.
  Incident: {title}
  Date: {date}
  Evidence: {evidenceList}
  Blockchain proof: {hash}

  Format with:
  - To: Internal Complaints Committee
  - Subject line
  - Incident description paragraph
  - Numbered evidence exhibits (Exhibit A, B, C...)
  - Relevant POSH Act sections (3, 9, 11, 13, 17)
  - Closing and signature block


🚀 Getting Started

Prerequisites
- Node.js 18+
- npm or yarn
- Firebase project
- OpenAI API key

Installation

bash
1. Clone the repository
git clone https://github.com/yourusername/kavach.git
cd kavach

2. Install dependencies
npm install

3. Set up environment variables
cp .env.example .env


Environment Variables

env
REACT_APP_FIREBASE_API_KEY=your_key
REACT_APP_FIREBASE_AUTH_DOMAIN=your_domain
REACT_APP_FIREBASE_DATABASE_URL=your_db_url
REACT_APP_FIREBASE_PROJECT_ID=your_project_id
REACT_APP_OPENAI_API_KEY=your_openai_key


Run Locally

bash
npm start
App runs at http://localhost:3000


Build for Production

bash
npm run build

📁 Project Structure

kavach/
├── public/
│   └── index.html
├── src/
│   ├── components/
│   │   ├── Splash.jsx          # Animated splash screen
│   │   ├── Onboarding.jsx      # 3-slide intro
│   │   ├── Login.jsx           # Anonymous + email auth
│   │   ├── Home.jsx            # Dashboard + SOS
│   │   ├── Signal.jsx          # Whisper Signal 3-step flow
│   │   ├── Vault.jsx           # Proof Vault + legal draft
│   │   ├── Score.jsx           # Company safety scores
│   │   ├── Circle.jsx          # Anonymous peer support
│   │   └── Nav.jsx             # Bottom navigation
│   ├── firebase/
│   │   ├── config.js           # Firebase init
│   │   ├── auth.js             # Anonymous auth helpers
│   │   └── db.js               # Realtime DB helpers
│   ├── utils/
│   │   ├── shieldId.js         # Shield ID generator
│   │   ├── convergence.js      # Signal convergence logic
│   │   └── hashProof.js        # SHA-256 blockchain mock
│   ├── App.jsx
│   └── index.js
├── .env.example
├── package.json
└── README.md
🔒 Privacy & Security Principles

- No PII stored** — real email is never written to the database
- Anonymous token only** — all data keyed to `Shield_XXXXXX`
- AES-256 vault encryption** — evidence entries encrypted client-side
- Blockchain anchoring** — SHA-256 hash of `(timestamp + content)` anchored to Polygon for tamper-proof timestamping
- POSH deadline auto-calculation** — `incidentDate + 90 days` per Section 9 of the Act
- Zero-knowledge onboarding** — anonymous auth requires no personal data whatsoever

---

📜 Legal Context

KAVACH is built around **India's Sexual Harassment of Women at Workplace (Prevention, Prohibition and Redressal) Act, 2013** (POSH Act).

Key sections referenced in the app:

| Section | Description |
|---|---|
| Section 2(n) | Definition of sexual harassment |
| Section 3 | Prohibition of sexual harassment |
| Section 9 | Complaint filing — within 3 months of incident |
| Section 11 | ICC inquiry process — 90-day limit |
| Section 12 | Interim relief during inquiry |
| Section 13 | Employer action on ICC recommendations |
| Section 17 | Penalty for disclosure of complainant identity |

---
🏆 Hackathon Context

KAVACH was built as a hackathon prototype demonstrating:

- Anonymous-first architecture for sensitive social impact use cases
- AI-assisted legal document generation
- Collective signal detection (convergence alerting)
- Blockchain proof-of-existence for digital evidence

---

🤝 Contributing

Pull requests are welcome. For major changes, please open an issue first to discuss what you'd like to change.

1. Fork the repo
2. Create your feature branch — `git checkout -b feature/my-feature`
3. Commit your changes — `git commit -m 'Add some feature'`
4. Push to the branch — `git push origin feature/my-feature`
5. Open a Pull Request

---
📄 License

MIT License — see [LICENSE](LICENSE) for details.

---
🌸 Acknowledgements

Built with care for every woman who was told to stay silent.

"Documentation is your power."*

---

<div align="center">
  <strong>KAVACH · Smart Workplace Safety</strong><br/>
  Made with 💗 for social impact
</div>

