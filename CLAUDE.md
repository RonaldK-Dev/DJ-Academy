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
- **Farbschema** (Ronalds Wunsch, kein "AI-Look" mit Cyan/Magenta): Gold `--gold:#f5c518` als Hauptfarbe, Orange `--orange:#ff7a1a` und Rot `--red:#ef2d56` (Verläufe/Akzente), warmer dunkler Hintergrund `#0d0a08`. Lila wurde auf Ronalds Feedback wieder entfernt.
- **Layout**: Mobile = Ein-Spalten-Feed mit Bottom-Nav (bis 900px); Desktop `@media(min-width:900px)` = fixe Sidebar links (240px, Logo via `nav::before`, Header versteckt) + Karten-Grid (`auto-fill minmax(400px,1fr)`), Hero-Karte spannt via `.card:has(.hero)` die volle Breite
- **Dev-Server**: Python http.server, **Port 4323**, via `D:\second-brain\Projekte\.claude\launch.json` (Name: `DJ-Academy`)

## Architektur

- Daten `D` (persistiert): `{done:{"<level>-<topicIdx>":true}, skills:{name:0..100}, journal:[{id,date,text}]}`
- UI-State `S` (flüchtig): `{view:"dash"|"path"|"journal", open:{levelIdx:bool}, journalText}`
- `set(p)` → merge in `S` → `render()` leert `#root` und baut neu; Daten-Änderungen gehen über `save()` (localStorage)
- Konstanten: `LEVELS` (9 Level mit Themen-Arrays, angepasst ans reale Setup) und `SKILLS` (6 Selbsteinschätzungs-Skills)
- Abgeleitete Werte: `levelProgress(li)`, `totalProgress()`, `currentLevel()` (= erstes Level < 100%)

## Views

1. **Dashboard** (`dash`): aktueller Level (Badge), Level- und Gesamt-Fortschritt, "Nächste Schritte" (nächste 3 offene Themen, klickbar = abhaken), Skill-Slider
2. **Lernpfad** (`path`): alle 9 Level als aufklappbare Karten mit abhakbaren Themen; komplette Level grün markiert
3. **Journal** (`journal`): Übungs-Session erfassen (Textarea, Datum automatisch), Log mit Löschen (confirm)

## Konventionen

- 2-Space-Indent, doppelte Anführungszeichen, `function`-Deklarationen top-level
- CSS im `<style>`-Tag, eine Regel pro Zeile; Inputs min. 16px font-size (iOS-Zoom)
- User-facing Texte **Deutsch**
- Commits auf Deutsch mit Begründung + Co-Authored-By Claude
- Verifikation im Browser-Preview (Port 4323), Mobile (375×812) und Desktop prüfen

## Aktueller Stand (v0.1)

✅ Lernpfad mit 9 Leveln / 44 Themen, Fortschritts-Tracking, Skills, Journal, PWA-Shell, SW-Offline-Cache

### Offene Punkte / Ideen
- PNG-Icons (aktuell nur `icon.svg` — reicht für Chromium, iOS-Homescreen-Icon fehlt)
- Lektions-Inhalte in der App (Erklärung/Übung/Hausaufgabe pro Thema) statt nur Checkliste
- Set-Analyse-Bereich (Feedback zu aufgenommenen Mixen)
- Optional später: Supabase-Sync wie BBQ-Lab, wenn Multi-Device gebraucht wird

## User-Präferenzen

- Immer Deutsch, duzen, kurz und ergebnisorientiert
- Entscheidungen als klickbare Optionen anbieten
- Ehrliches Feedback erwünscht, kein Ja-Sagen
