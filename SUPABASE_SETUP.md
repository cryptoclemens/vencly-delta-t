# Supabase Feedback-Integration – Setup-Anleitung

Einmaliger Aufwand: ~10 Minuten. Danach landet jedes Feedback strukturiert in einer
Postgres-Datenbank – und dieselbe Supabase-Instanz kann später auch den **Login**
übernehmen (kein weiterer Dienst nötig).

---

## Schritt 1 – Supabase-Projekt anlegen

1. Öffne [supabase.com](https://supabase.com) und melde dich an (GitHub-Login möglich).
2. **New Project** → Organisation wählen → Name z. B. `vencly-projects`.
3. Starkes Passwort vergeben (nur für DB-Admin-Zugang, nicht für die App).
4. Region: **EU (Frankfurt)** wählen → **Create new project**.
5. Warte ~2 Minuten bis das Projekt bereit ist.

---

## Schritt 2 – Feedback-Tabelle erstellen

Im Supabase-Dashboard: **SQL Editor** → **New query** → folgenden SQL einfügen und ausführen:

```sql
-- Feedback-Tabelle (wird von allen Projekten gemeinsam genutzt)
CREATE TABLE public.feedback (
  id             BIGSERIAL PRIMARY KEY,
  created_at     TIMESTAMPTZ NOT NULL DEFAULT NOW(),
  project        TEXT        NOT NULL,          -- z.B. 'DeltaT', 'Geotherm-Atlas'
  type           TEXT        NOT NULL,          -- '💡 Idee' | '🐛 Bug' | '🙏 Wunsch'
  message        TEXT        NOT NULL,
  name           TEXT,                          -- optional
  version        TEXT,                          -- Auto-generierte App-Version
  inputs_snapshot JSONB,                        -- Snapshot der Eingabewerte (DeltaT)
  user_agent     TEXT
);

-- Nur INSERT erlauben (kein SELECT für anonyme Nutzer → Datenschutz)
ALTER TABLE public.feedback ENABLE ROW LEVEL SECURITY;

CREATE POLICY "Anon darf schreiben"
  ON public.feedback FOR INSERT
  TO anon
  WITH CHECK (true);

-- Optional: Authentifizierte Nutzer (Admin) dürfen alles lesen
CREATE POLICY "Auth darf lesen"
  ON public.feedback FOR SELECT
  TO authenticated
  USING (true);
```

---

## Schritt 3 – API-Credentials holen

1. Im Dashboard: **Project Settings** → **API**
2. Notiere dir:
   - **Project URL** → sieht aus wie `https://abcdefgh.supabase.co`
   - **anon public** Key → langer JWT-String unter „Project API keys"

> ⚠ Den `anon`-Key darf man im Frontend verwenden – er ist nur für
> die erlaubten Operationen (INSERT) nutzbar, da RLS aktiv ist.
> Den `service_role`-Key **niemals** im Frontend einsetzen.

---

## Schritt 4 – Keys in die App eintragen

Öffne **`delta-t.html`** (und/oder **`vencly-project-template.html`**) und ersetze
oben im `<script>`-Block die zwei Platzhalter:

```js
const SUPABASE_URL      = 'https://abcdefgh.supabase.co';   // ← deine URL
const SUPABASE_ANON_KEY = 'eyJhbGciOiJIUzI1NiIsInR5c...';  // ← dein anon key
```

Fertig. Ab sofort wird jedes abgesendete Feedback in Supabase gespeichert.

---

## Feedback ansehen

**Option A – Supabase Table Editor**
Dashboard → **Table Editor** → Tabelle `feedback` → alle Einträge sichtbar,
filterbar, exportierbar als CSV.

**Option B – SQL**
```sql
SELECT created_at, project, type, name, message, version
FROM feedback
ORDER BY created_at DESC
LIMIT 50;
```

**Option C – Supabase Studio (automatisches Dashboard)**
Dashboard → **Reports** oder einfach im Table Editor mit dem Filter-Button arbeiten.

---

## Vorbereitung für Login (Stufe 2)

Wenn du später einen Login ergänzen willst, ist in Supabase bereits alles vorhanden:

```sql
-- Feedback mit User verknüpfen (Migration wenn Auth aktiv)
ALTER TABLE public.feedback
  ADD COLUMN user_id UUID REFERENCES auth.users(id);

-- RLS-Policy anpassen: Nutzer sieht nur eigenes Feedback
DROP POLICY "Auth darf lesen" ON public.feedback;
CREATE POLICY "Nutzer sieht eigenes Feedback"
  ON public.feedback FOR SELECT
  TO authenticated
  USING (user_id = auth.uid());
```

Auth in der App aktivieren: **Authentication → Providers** im Dashboard →
E-Mail/Passwort einschalten. Dann den Supabase JS-Client einbinden:

```html
<script src="https://cdn.jsdelivr.net/npm/@supabase/supabase-js@2/dist/umd/supabase.min.js"></script>
```

```js
const { createClient } = supabase;
const sb = createClient(SUPABASE_URL, SUPABASE_ANON_KEY);

// Login
await sb.auth.signInWithPassword({ email, password });

// Aktuellen Nutzer abrufen
const { data: { user } } = await sb.auth.getUser();
```

---

## Dateien-Übersicht

| Datei | Beschreibung |
|---|---|
| `delta-t.html` | Geothermie-Rechner mit Supabase-Feedback |
| `vencly-project-template.html` | Wiederverwendbares Template mit Supabase-Feedback |
| `SUPABASE_SETUP.md` | Diese Anleitung |
