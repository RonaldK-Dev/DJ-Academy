# CLAUDE.md — DJ Academy

> Kontext für eine neue Session. Stand: Juli 2026 (v0.1).

## Projektübersicht

**DJ Academy** ist Ronalds persönliche DJ-Lern-App — das App-Gegenstück zum Vault-Projekt
`D:\second-brain\second-brain\02 Projekte\DJ Academy AI.md` (dort: Vision, Curriculum, Abgrenzung zum Bereich "DJ & Streaming").

Zweck: den Lernweg vom Anfänger zum Twitch-DJ sichtbar machen — Level abhaken, Skills einschätzen, Übungs-Sessions loggen.

**Reales Setup:** Pioneer DDJ-FLX4, Rekordbox, Twitch-Kanal mit Affiliate-Status. Genres: Techno, House, RnB.

## Tech Stack

Wie BBQ-Lab (siehe `../BBQ-Lab/CLAUDE.md` für die ausführlichen Konventionen):

- **Single-File-App**: komplette App in `index.html` (HTML + CSS + Vanilla JS), kein Framework, kein Build, kein npm
- **localStorage** als einzige Persistenz (Key `dj-academy`) — kein Backend in v0.1
- **PWA**: `manifest.json` + `sw.js` (Stale-While-Revalidate, Cache `dj-academy-v1` — bei Shell-Änderungen hochzählen)
- **Google Fonts**: Bebas Neue (Headlines), DM Sans (Body)
- **Farbschema** (Ronalds Wunsch, kein "AI-Look" mit Cyan/Magenta): Gold `--gold:#f5c518` als Hauptfarbe, Orange `--orange:#ff7a1a` und Rot `--red:#ef3a3a` (Verläufe/Akzente), tiefes warmes Near-Black `--bg:#0a0806`. Lila wurde auf Ronalds Feedback wieder entfernt.
- **Design-Sprache (v0.4, inspiriert von academy.djsoundaustria.com):** Glasmorphismus — Karten sind halbtransparent (`--glass`) mit `backdrop-filter:blur(16px)`, Sidebar/Nav mit `blur(20px)`. Ambient-Glow über `body::before` (fixierte radiale Gold/Orange/Rot-Gradients). Große Ecken (22px Karten, 99px Pills/Buttons/Bars). Großer Hero mit Bebas-Neue-Headline `clamp(46px,11vw,76px)`, Eyebrow (letter-spacing .28em) und Corner-Glow. Hover: Karten/Buttons heben sich (`translateY`) + Glow. Bebas Neue auch für `.stat-num` und Section-`h2` (uppercase).
- **Layout**: Mobile = Ein-Spalten-Feed (in `.wrap`, max 560px, zentriert) mit Bottom-Nav (bis 900px); Desktop `@media(min-width:900px)` = fixe Sidebar links (260px, Logo via `nav::before`, Header versteckt) + Karten-Grid (`auto-fill minmax(380px,1fr)`), Hero spannt via `.card:has(.hero),.hero` die volle Breite. Header + main liegen in `<div class="wrap">`, nav ist fixed daneben.
- **Dev-Server**: Python http.server, **Port 4323**, via `D:\second-brain\Projekte\.claude\launch.json` (Name: `DJ-Academy`)

## Architektur

- Daten `D` (persistiert): `{done:{"<level>-<topicIdx>":true}, skills:{name:0..100}, journal:[{id,date,text}]}`
- UI-State `S` (flüchtig): `{view:"dash"|"path"|"lesson"|"journal", open:{levelIdx:bool}, journalText, lesson:{li,i}|null}`
- `set(p)` → merge in `S` → `render()` leert `#root` und baut neu; Daten-Änderungen gehen über `save()` (localStorage)
- Konstanten: `LEVELS` (9 Level) und `SKILLS` (6 Selbsteinschätzungs-Skills)
- **Themen-Struktur:** jedes `topic` ist ein Objekt `{t:"Titel", l?:{why,viz?,ex?,task,hw}}`. Fehlt `l`, gibt es (noch) keine Lektion — Thema ist dann nur abhakbar. Lektions-Inhalte aktuell für **Level 0 (5) + Level 1 (6)** ausgeschrieben, Rest sind Titel-Platzhalter. Insgesamt 43 Themen.
- Lektions-Felder: `why` (Erklärung), `viz` (ASCII-Visualisierung, monospace `<pre class="viz">`, optional), `ex` (In der Praxis, optional), `task` (Übung), `hw` (Hausaufgabe). Inhalte sind auf Ronalds reales Setup zugeschnitten (FLX4, Rekordbox, JBL PartyBox, Techno/House/RnB) und konsistent zu den Vault-Guides.
- Abgeleitete Werte: `levelProgress(li)`, `totalProgress()`, `currentLevel()` (= erstes Level < 100%)

## Views

1. **Dashboard** (`dash`): großer Hero mit aktuellem Level, Gesamt-Fortschritt, "Nächste Schritte" (nächste 3 offene Themen — Klick öffnet die Lektion bzw. hakt Titel-Platzhalter ab), Skill-Slider
2. **Lernpfad** (`path`): alle 9 Level als aufklappbare Karten. Pro Thema: Checkbox (`.box`, `stopPropagation` → nur abhaken) + Titel + Chevron `›` bei Themen mit Lektion (Klick auf Zeile → Lektion)
3. **Lektion** (`lesson`): Zurück-Link, Hero mit Thema, Sektionen (💡 Erklärung / 📊 Visualisierung / 🎧 In der Praxis / 🎛️ Übung / 📝 Hausaufgabe), "Als erledigt markieren"-Button (grün wenn done). Single-Column max 820px auf Desktop (`main.lesson-view`). Nav-Highlight bleibt auf Lernpfad
4. **Journal** (`journal`): Übungs-Session erfassen (Textarea, Datum automatisch), Log mit Löschen (confirm)

## Konventionen

- 2-Space-Indent, doppelte Anführungszeichen, `function`-Deklarationen top-level
- CSS im `<style>`-Tag, eine Regel pro Zeile; Inputs min. 16px font-size (iOS-Zoom)
- User-facing Texte **Deutsch**
- Commits auf Deutsch mit Begründung + Co-Authored-By Claude
- Verifikation im Browser-Preview (Port 4323), Mobile (375×812) und Desktop prüfen

## Aktueller Stand (v0.5)

✅ Lernpfad mit 9 Leveln / 43 Themen, Fortschritts-Tracking, Skills, Journal, PWA-Shell, SW-Offline-Cache
✅ Live auf GitHub Pages: https://ronaldk-dev.github.io/DJ-Academy/
✅ Modernes Glasmorphismus-Design mit Ambient-Glow, großem Hero, Hover-Effekten (v0.4)
✅ **Lektions-System (v0.5):** klickbare Themen öffnen echte Lektionen (Erklärung/Viz/Praxis/Übung/Hausaufgabe). Level 0 + 1 voll ausgeschrieben (11 Lektionen)

### Offene Punkte / Ideen
- **Lektions-Inhalte für Level 2–8 nachfüllen** (aktuell nur Titel-Platzhalter). Struktur steht, es fehlt nur der Content pro `topic.l`
- PNG-Icons (aktuell nur `icon.svg` — reicht für Chromium, iOS-Homescreen-Icon fehlt)
- Set-Analyse-Bereich (Feedback zu aufgenommenen Mixen)
- Optional später: Supabase-Sync wie BBQ-Lab, wenn Multi-Device gebraucht wird

## User-Präferenzen

- Immer Deutsch, duzen, kurz und ergebnisorientiert
- Entscheidungen als klickbare Optionen anbieten
- Ehrliches Feedback erwünscht, kein Ja-Sagen
