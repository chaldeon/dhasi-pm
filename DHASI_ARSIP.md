# DHASI PLATFORM — Arsip Bahasa Manusia
> Dokumen living ini adalah sumber kebenaran tunggal untuk seluruh platform dhasi.my.id.
> Update setiap kali ada keputusan, perubahan arah, atau sprint selesai.

---

## Metadata Dokumen

| Field | Nilai |
|-------|-------|
| Dibuat | 2026-06-17 |
| Terakhir diupdate | 2026-06-17 |
| Versi | 0.3 — Post Sprint 1 |
| Pemilik | Daniel (personal use) |

---

## 1. Visi Platform

**dhasi.my.id** adalah sandbox pengembangan personal — tempat membangun, menguji, dan mematangkan tools sebelum dipindahkan ke domain produksi yang sesuai.

Filosofi: **Build here → validate → migrate.**

---

## 2. Peta Domain & Modul

```
dhasi.my.id/                ← Homepage portal (LIVE)
dhasi.my.id/project/        ← Project Management Tool (LIVE)
dhasi.my.id/budget/         ← Budget Tracker (Planned)
dhasi.my.id/notes/          ← Notes & Docs (Planned)
dhasi.my.id/time/           ← Time Tracker (Planned)
```

### Status Modul

| Path | Nama | Status |
|------|------|--------|
| dhasi.my.id | Homepage Portal | ✅ Live |
| dhasi.my.id/project | Project Management | ✅ Live (perlu restruktur UI) |
| dhasi.my.id/budget | Budget Tracker | 📋 Planned |
| dhasi.my.id/notes | Notes & Docs | 📋 Planned |
| dhasi.my.id/time | Time Tracker | 📋 Planned |

---

## 3. Infrastruktur & Deploy

| Layer | Pilihan | Catatan |
|-------|---------|---------|
| Repo | GitHub (chaldeon/dhasi-pm) | Public |
| Hosting | Vercel (Hobby) | Auto-deploy setiap push |
| Domain | dhasi.my.id | DNS A record @ → 216.198.79.1 via Rumahweb |
| www | www.dhasi.my.id | CNAME → f54c8aa655afdf82.vercel-dns-017.com |
| DNS Provider | Rumahweb | |

### Struktur Repo
```
dhasi-pm/
├── index.html          ← homepage
├── project/
│   └── index.html      ← PM tool (all-in-one)
├── vercel.json         ← cleanUrls: true
├── robots.txt          ← Disallow: / (tidak diindex Google)
└── ARSIP.md            ← dokumen ini
```

### Pelajaran Deploy (penting!)
- Folder dibuat via Finder Mac → jangan pakai `/` dalam nama folder, bikin jadi `project:` di GitHub
- Selalu buat folder via Terminal atau GitHub web editor
- `vercel.json` dengan `cleanUrls: true` wajib ada agar `/project` bisa diakses
- Vercel Hobby tidak support IP restriction atau password protection berbayar

---

## 4. Tech Stack

| Layer | Pilihan | Catatan |
|-------|---------|---------|
| Frontend | Pure HTML/CSS/JS | All-in-one single file per modul |
| Storage (Sprint 1-2) | localStorage (key: `dhasi_pm`) | Supabase-ready structure |
| Storage (Sprint 3+) | Supabase | Migration dari localStorage |
| Deploy | Vercel | Auto-deploy via GitHub push |

### Design Language
- Mode: Light
- Main color: putih
- Accent: Biru slate (#3b82f6)
- Font: system-ui / sans-serif
- Prinsip: fungsional dulu, cantik kemudian

---

## 5. Data Model (Supabase-Ready)

> Saat ini disimpan di localStorage dengan key `dhasi_pm`.
> Struktur ini dirancang agar mudah dimigrasi ke Supabase nanti.

### Projects
```json
{
  "id": "uuid",
  "name": "string",
  "description": "string (end goal project)",
  "start_date": "date",
  "end_date": "date (deadline)",
  "status": "planning | active | on_hold | completed",
  "created_at": "timestamp",
  "updated_at": "timestamp"
}
```

### Tasks
```json
{
  "id": "uuid",
  "project_id": "uuid",
  "milestone_id": "uuid | null",
  "parent_id": "uuid | null",
  "name": "string",
  "description": "string",
  "start_date": "date",
  "end_date": "date",
  "duration_days": "number",
  "progress_pct": "number (0-100)",
  "status": "not_started | in_progress | completed | on_hold",
  "dependencies": ["task_id"],
  "assigned_to": "string (text bebas Sprint 1, resource_id Sprint 2)",
  "need_to_hire": "boolean (Sprint 2)",
  "planned_cost": "number",
  "actual_cost": "number",
  "is_milestone": "boolean",
  "order": "number",
  "created_at": "timestamp"
}
```

### Resources (Sprint 2)
```json
{
  "id": "uuid",
  "name": "string",
  "type": "person | equipment | material",
  "division": "string",
  "cost_per_day": "number",
  "availability_pct": "number (0-100)"
}
```

### Divisions (Sprint 2)
```json
{
  "id": "uuid",
  "name": "string",
  "members": ["resource_id"]
}
```

---

## 6. Konsep UX & Fitur (Disepakati)

### Cara Pikir Natural User (PM Flow)
```
1. Tentukan END GOAL / deliverable
2. Tentukan MILESTONES (tonggak menuju end goal)
3. Tentukan TASKS per milestone
4. Assign RESOURCES & BUDGET per task
5. Set DEPENDENCIES (paralel vs sequence)
6. Lihat GANTT untuk validasi timeline
7. Monitor RESOURCE UTILIZATION (ada yang nganggur?)
8. Monitor BUDGET (ada yang over?)
```

### Fitur yang Akan Dibangun (Roadmap)

| Fitur | Sprint | Deskripsi |
|-------|--------|-----------|
| Restrukturisasi UI hierarki | Sprint 1b | Tasks nested di bawah Milestone, bukan tabel WBS flat |
| Tooltip ℹ | Sprint 1b | Hover untuk panduan singkat di setiap elemen |
| Guided wizard popup | Sprint 1b | Saat new project: pilih guided atau mandiri |
| Assigned_to text field | Sprint 1b | Tulis nama orang bebas, belum perlu resource management |
| Resource Management | Sprint 2 | Define orang, divisi, cost/hari, availability |
| Resource Gap Analysis | Sprint 2 | Task tanpa assignee → flag ⚠️ + opsi "Need to Hire" |
| Resource Leveling View | Sprint 2 | Per orang: semua task mereka, kapan penuh/kosong |
| Dependencies visual | Sprint 2 | Paralel vs sequence, dependency arrows di Gantt |
| Budget Dashboard | Sprint 3 | Planned vs Actual per task/project, alert over budget |
| Cloudflare Access | Infra | Login Google untuk akses, gratis personal |
| Simple password prompt | Infra | Layer pertama pengaman, localStorage-based |

---

## 7. Security Plan (Personal Sandbox)

3 layer kombinasi (gratis):
1. ✅ `robots.txt` Disallow: / — tidak diindex Google
2. 🔲 Simple password prompt — localStorage-based, menghalangi orang iseng
3. 🔲 Cloudflare Access — login Google, proper auth, gratis personal

---

## 8. Sprint Log

### Sprint 0 — Foundation (2026-06-17)
**Status:** ✅ Selesai
- [x] Keputusan tech stack
- [x] Data model dirancang
- [x] Arsip dokumen dibuat
- [x] Struktur repo diputuskan

---

### Sprint 1 — Core PM Tool (2026-06-17)
**Status:** ✅ Live (dengan catatan)
- [x] Homepage portal (dhasi.my.id)
- [x] PM tool all-in-one single file
- [x] Dashboard multi-project
- [x] WBS task list + sub-task
- [x] Milestone support
- [x] Gantt chart visual + zoom
- [x] Export/Import JSON backup
- [x] Deploy ke Vercel + domain dhasi.my.id
- [x] robots.txt

**Catatan & Pelajaran:**
- File awalnya dipecah (css/, js/) tapi CSS tidak ke-load di Vercel → digabung jadi all-in-one
- Folder `project/` dibuat via Finder → ter-upload sebagai `project:` → fix via Terminal `mv`
- `vercel.json` dengan `cleanUrls: true` wajib untuk serve subfolder
- Repo sempat dihapus dan dibuat ulang 2x saat troubleshoot

---

### Sprint 1b — UX Restruktur (BERIKUTNYA)
**Status:** 🔴 Belum dimulai
**Target:**
- [ ] UI hierarki baru: End Goal → Milestones → Tasks (nested)
- [ ] Tooltip ℹ di setiap elemen penting
- [ ] Popup guided wizard saat new project
- [ ] Field assigned_to (text bebas)
- [ ] Gantt tetap ada tapi tidak prominent
- [ ] Simple password prompt (layer 2 security)

---

### Sprint 2 — Resources & Dependencies (PLANNED)
**Status:** 📋 Direncanakan
- [ ] Resource Management (orang, divisi, cost/hari)
- [ ] Assign resource ke task (dropdown, bukan text bebas)
- [ ] Resource Gap Analysis (⚠️ unassigned + "Need to Hire" flag)
- [ ] Resource Leveling View (per orang, timeline utilization)
- [ ] Dependencies antar task (paralel vs sequence)
- [ ] Cloudflare Access setup

---

### Sprint 3 — Budget & Supabase (PLANNED)
**Status:** 📋 Direncanakan
- [ ] Budget Dashboard (planned vs actual)
- [ ] Alert over budget
- [ ] Supabase migration
- [ ] Auth (email login)

---

## 9. Keputusan & Catatan Penting

| Tanggal | Keputusan | Alasan |
|---------|-----------|--------|
| 2026-06-17 | Personal use dulu | Validasi sebelum komersialisasi |
| 2026-06-17 | localStorage Sprint 1-2, Supabase Sprint 3+ | Cepat, Supabase-ready structure |
| 2026-06-17 | All-in-one HTML (CSS+JS inline) | File eksternal tidak ter-serve di Vercel Hobby |
| 2026-06-17 | 1 repo, path-based (/project/) bukan subdomain | Lebih simpel, 1 Vercel project |
| 2026-06-17 | Light mode, accent biru slate | Personal brand, bukan Madael merah |
| 2026-06-17 | Milestone = Task dengan is_milestone: true | Simplifikasi model |
| 2026-06-17 | UI perlu restruktur: hierarki End Goal → Milestone → Task | UX flat WBS membingungkan user baru |
| 2026-06-17 | assigned_to = text bebas Sprint 1b, resource_id Sprint 2 | Bertahap, tidak over-engineer |
| 2026-06-17 | Resource Gap Analysis: flag ⚠️ + "Need to Hire" | Kebutuhan real: tidak ada yang nganggur, tidak over budget |
| 2026-06-17 | Security: robots.txt + password prompt + Cloudflare Access | 3 layer gratis, bertahap |

---

## 10. Cara Pakai Dokumen Ini

1. **Setiap sprint dimulai:** baca Sprint Log, cek status
2. **Setiap keputusan dibuat:** tambah baris di tabel Keputusan (#9)
3. **Setiap sprint selesai:** update checklist Sprint Log
4. **Simpan di repo GitHub** sebagai `ARSIP.md` di root — commit setiap perubahan

---

*"Build it, use it, understand it — then ship it."*
