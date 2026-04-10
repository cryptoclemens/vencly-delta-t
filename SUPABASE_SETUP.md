# Supabase Feedback-Integration – Setup-Anleitung

Einmaliger Aufwand: ~10 Minuten. Danach landet jedes Feedback strukturiert in einer
Postgres-Datenbank – und dieselbe Supabase-Instanz kann später auch den **Login**
übernehmen (kein weiterer Dienst nötig).

> **Status:** Diese Anleitung ist für DeltaT aktuell (Stand März 2026) und wurde mit einem echten Projekt getestet.

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

> **Alternative (ohne SQL):** Im **Table Editor** → **New Table** → Spalten einzeln anlegen und RLS manuell aktivieren. Der SQL-Weg ist schneller und reproduzierbarer.

---

## Schritt 3 – API-Credentials holen

1. Im Dashboard: **Project Settings** → **API**
2. Notiere dir:
   - **Project URL** → sieht aus wie `https://abcdefgh.supabase.co`
   - **Publishable key** → unter „API Keys" (neues Format: `sb_publishable_...`)
     *(In älteren Supabase-Projekten heißt dieser Key noch „anon public" und sieht
     wie ein langer JWT-String aus – `eyJ...`. Beide Formate funktionieren gleich.)*

> ⚠ Den **Publishable key** (früher: `anon`-Key) darf man im Frontend verwenden –
> er ist durch RLS auf die erlaubten Operationen (INSERT) beschränkt.
> Den `service_role`-Key / **Secret key** **niemals** im Frontend einsetzen.

---

## Schritt 4 – Keys in die App eintragen

Trage deine Credentials in **`config.js`** ein (die Datei liegt im Projekt-Ordner,
sie ist gitignored und wird nie committed):

```js
window.VENCLY_CONFIG = {
  supabaseUrl:     'https://abcdefgh.supabase.co',    // ← deine Project URL
  supabaseAnonKey: 'sb_publishable_...',               // ← dein Publishable key
};
```

Fertig. Ab sofort wird jedes abgesendete Feedback in Supabase gespeichert.

**Verifizieren:**
1. `delta-t.html` im Browser öffnen
2. Unten in der App rechts das Feedback-Panel aufklappen (Tab „Feedback")
3. Typ wählen (Idee / Bug / Wunsch), Text eintippen, „Feedback senden" klicken
4. Bei Erfolg: grüne Bestätigung „✅ Gespeichert in Supabase!"
5. In Supabase: **Table Editor → feedback** → der neue Eintrag sollte sichtbar sein

Wenn stattdessen „Dev-Modus" angezeigt wird: `config.js` fehlt oder wird nicht geladen.

---

## Schritt 5 – Deployment auf GitHub Pages

Für das automatische Deployment gibst du die Credentials als **GitHub Secrets** an:

1. Repo auf GitHub öffnen → **Settings → Secrets and variables → Actions**
2. **New repository secret** → folgende zwei anlegen:
   ```
   SUPABASE_URL      = https://abcdefgh.supabase.co
   SUPABASE_ANON_KEY = sb_publishable_...
   ```
3. Push nach `main` → GitHub Actions erstellt `config.js` zur Build-Zeit und deployt zu Pages

Die Workflow-Datei ist in `.github/workflows/deploy.yml`.

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

**Option C – Filter auf ein Projekt**
```sql
SELECT created_at, type, name, message, inputs_snapshot
FROM feedback
WHERE project = 'DeltaT'
ORDER BY created_at DESC;
```

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

## Troubleshooting

| Problem | Ursache | Lösung |
|---|---|---|
| "Dev-Modus" wird angezeigt | `config.js` fehlt oder Platzhalter | Datei anlegen und echte Keys eintragen |
| `401 Unauthorized` beim Senden | Falscher Key oder RLS-Policy fehlt | Key prüfen, RLS-Policies aus Schritt 2 ausführen |
| `42501 new row violates row-level security` | INSERT-Policy fehlt | SQL aus Schritt 2 nochmal ausführen |
| Keys im Browser sichtbar | ✅ Das ist ok | Publishable-Keys sind für Frontend-Nutzung gedacht (RLS schützt die Daten) |

---

## Dateien-Übersicht

| Datei | Beschreibung |
|---|---|
| `delta-t.html` | Geothermie-Rechner mit Supabase-Feedback |
| `config.js` | **Gitignored** – enthält deine Supabase-Credentials |
| `config.example.js` | Vorlage für `config.js` |
| `vencly-project-template.html` | Wiederverwendbares Template mit Supabase-Feedback |
| `SUPABASE_SETUP.md` | Diese Anleitung |
