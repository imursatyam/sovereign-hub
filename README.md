# Sovereign Hub — Private Capital Syndicate Platform

> A full-stack, multi-role investment marketplace connecting Entrepreneurs and Investors through structured deal-making, real-time communication, and automated financial tracking.

**Live Demo:** [sovereign-hub.vercel.app](https://sovereign-hub.vercel.app)

---

## What is Sovereign Hub?

Sovereign Hub is a Shark Tank-inspired platform where:
- **Founders (Entrepreneurs)** submit business proposals, showcase their team, set funding goals, and track milestones
- **Sharks (Investors)** browse verified proposals, submit formal term sheets with investment amount and equity terms, and deploy capital
- **Admins** verify user identities, audit proposals, and manage the platform via an Executive Terminal

The platform handles the full investment lifecycle — from proposal submission to deal negotiation, acceptance, and transaction logging.

---

## Features

### Bilateral Marketplace
- Dual-interface system with separate dashboards for Founders and Investors
- Founders submit proposals with funding goals, equity offers, team details, and traction data
- Investors browse, filter, and audit proposals before committing capital

### Structured Negotiation Engine
- Investors submit formal term sheets specifying investment amount and equity percentage
- Founders can accept, decline, or counter each offer
- All negotiation history is preserved per deal

### Neural Performance Index (Success Score)
- A PostgreSQL PL/pgSQL trigger automatically calculates each proposal's viability score
- Score is based on milestone completion rate, funding percentage achieved, and business status
- Updates dynamically on every new data entry — no manual refresh needed

### Forensic Ledger
- Every capital deployment is recorded in `funding_logs` — an append-only transaction table
- Provides a full auditable history of all funding events
- Entrepreneurs and investors can view their respective transaction records via RLS-enforced SELECT policies

### Real-time Neural Handshake Chat
- Built-in messaging system using Supabase Realtime WebSockets
- Chat rooms are scoped per deal — messages from different offers never mix
- Live unread message counts and instant deal-update notifications

### Milestone & Traction Tracking
- Founders can log execution milestones and revenue data
- Each update improves their proposal's Neural Performance Index score
- Investors who backed a proposal receive notifications on every update

### Executive Terminal (Admin Panel)
- Central command for platform moderators
- Verify/unverify user entities, audit proposals, manage support inquiries
- Broadcast platform-wide alerts to all users

---

## Tech Stack

| Layer | Technology |
|---|---|
| Frontend | HTML5, CSS3, JavaScript (ES6+) |
| Backend / Database | Supabase, PostgreSQL |
| Database Logic | PL/pgSQL Triggers, Functions |
| Security | Row-Level Security (RLS) Policies, RBAC |
| Real-time | Supabase Realtime (WebSockets) |
| Deployment | Vercel |

---

## Database Architecture

### Tables
- `users` — All platform users with role (`entrepreneur`, `investor`, `admin`) and verification status
- `proposals` — Business proposals with funding goals, equity, status, and success score
- `offers` — Investor term sheets linked to proposals (investment amount + equity)
- `deals` — Accepted offers that become confirmed deals
- `funding_logs` — Immutable transaction ledger of all capital deployments
- `chat_rooms` — One room per entrepreneur-investor pair
- `messages` — Chat messages with read/unread tracking
- `notifications` — System and deal notifications per user
- `milestones` — Founder execution roadmaps
- `updates` — Founder traction updates sent to investors
- `reviews` — Post-deal feedback
- `support_inquiries` — User support tickets

### Key Security Implementation

**Row-Level Security (RLS)** is enabled on all 13 tables. Example policies:
- Entrepreneurs can only view/edit their own proposals
- Investors can only view their own sent offers and funding logs
- Admin role verified via `is_admin()` function using `auth.uid()` — prevents privilege escalation

**Admin Verification Function:**
```sql
CREATE OR REPLACE FUNCTION is_admin()
RETURNS boolean AS $$
BEGIN
  RETURN EXISTS (
    SELECT 1 FROM public.users
    WHERE id = auth.uid() AND role = 'admin'
  );
END;
$$ LANGUAGE plpgsql SECURITY DEFINER SET search_path = public;
```

**Auto-scoring Trigger:**
```sql
CREATE TRIGGER trigger_update_funding_total
AFTER INSERT ON funding_logs
FOR EACH ROW EXECUTE FUNCTION update_proposal_funding_total();
```

---

## Pages

| Page | Purpose |
|---|---|
| `index.html` | Landing page |
| `auth.html` | Login / Registration |
| `dashboard.html` | Role-based main dashboard |
| `proposals.html` | Browse all proposals |
| `submit-proposal.html` | Founder proposal submission |
| `proposal-details.html` | Full proposal view with offer submission |
| `edit-proposal.html` | Founder proposal editor |
| `offer-detail.html` | Investor offer tracking |
| `deals.html` | Confirmed deals overview |
| `chat.html` | Real-time deal messaging |
| `funding-history.html` | Transaction ledger view |
| `entrepreneur-portfolio.html` | Founder's portfolio |
| `investor-portfolio.html` | Investor's portfolio |
| `profile.html` | User profile management |
| `user-profile.html` | Public user profiles |
| `admin.html` | Executive Terminal (Admin only) |
| `support-info.html` | Support centre |
| `setup.html` | Initial platform configuration |
| `diagnostic.html` | System diagnostics |

---

## Setup & Local Development

### Prerequisites
- A [Supabase](https://supabase.com) account and project
- [Vercel](https://vercel.com) account (for deployment)

### Steps

1. **Clone the repository**
```bash
git clone https://github.com/imursatyam/sovereign-hub.git
cd sovereign-hub
```

2. **Set up the database**
   - Open your Supabase project SQL editor
   - Run the schema from `db.txt` to create all tables, triggers, functions, and RLS policies

3. **Configure Supabase credentials**
   - In each JS file, locate the Supabase initialisation block
   - Replace the `SUPABASE_URL` and `SUPABASE_ANON_KEY` with your own project credentials

4. **Deploy or run locally**
   - Open `index.html` directly in a browser for local testing
   - Or deploy to Vercel by connecting your GitHub repository

---

## Project Structure

```
sovereign-hub/
├── css/                    # Stylesheets
├── js/                     # JavaScript modules
├── index.html              # Landing page
├── auth.html               # Authentication
├── dashboard.html          # Main dashboard
├── admin.html              # Admin terminal
├── proposals.html          # Proposal marketplace
├── chat.html               # Real-time messaging
├── funding-history.html    # Transaction ledger
├── db.txt                  # Full database schema (SQL)
└── ...                     # Additional pages
```

---

## Author

**Satyam Rajesh Vishwakarma**
B.Sc. Computer Science — Asmita College, Thane
CGPA: 9.65 | Final Year Project (2026)

- GitHub: [github.com/imursatyam](https://github.com/imursatyam)
- Email: sboy60127@gmail.com
- Live: [sovereign-hub.vercel.app](https://sovereign-hub.vercel.app)

---

## License

This project is for educational and portfolio purposes.
