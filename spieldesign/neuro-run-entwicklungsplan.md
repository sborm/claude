# NEURO-RUN — Mehrstufiger Entwicklungsplan

Grundlage: `NR-2026-CORE-SPEC` (Game Design Document). Dieser Plan übersetzt die Spezifikation
in eine reale, gestufte Produktentwicklung — vom validierbaren Prototyp bis zum
Cross-Platform-Launch. Er verändert keine der beschriebenen Mechaniken inhaltlich; er ordnet
sie nach technischer Abhängigkeit, Risiko und Aufwand.

## Realistische Grundannahme zuerst

Die Spec beschreibt ein AAA-Cross-Platform-Produkt (UE5/Unity, natives Gyro, OS-Overlay,
Kamera-KI, Server-Sync, 120 FPS). Das ist **kein Wochenend-Prototyp**, sondern ein Projekt in
der Größenordnung eines kommerziellen Indie-bis-Midcore-Studios (realistisch: 6–18 Monate mit
einem kleinen Team, je nach Scope-Disziplin). Der Plan ist deshalb bewusst in Phasen
geschnitten, die jeweils für sich funktionieren und testbar sind — nicht als Etappen auf dem
Weg zu einem einzigen großen Release.

**Engine-Empfehlung:** Unity statt UE5. Begründung: bessere Mobile-Toolchain (iOS/Android),
etablierte Wege für Gyro-Input, Widgets und In-App-Käufe-Infrastruktur (auch wenn hier keine
verwendet werden), kleinere Team-Einstiegshürde, günstigere Iterationszyklen für ein
Solo-/Kleinteam-Projekt. UE5 lohnt sich erst, wenn die Fidelity-Anforderungen aus Abschnitt 5
(Fluid-Shading, volumetrische Partikel) tatsächlich zum limitierenden Faktor werden.

---

## Phase 0 — Validierung vor Produktion (2–4 Wochen)

Ziel: Beweisen, dass der Core Loop motivational trägt, **bevor** in 3D-Assets, Engine-Setup
oder native Sensorik investiert wird.

| Arbeitspaket | Ergebnis |
|---|---|
| Klickbarer Web-Prototyp des Focus Run (Tunnel-Optik, blau/rot-Tore, Plasma-Timer, NLP-ähnliches Eingabefeld) | Testbares Gefühl für den 120-Sekunden-Loop, ohne Engine-Investition |
| 5–10 Testpersonen mit ADHS-Diagnose, angeleitetes Testing | Reales Signal: trägt der Loop, oder kippt er in Stress/Vermeidung? |
| Interne Metrik-Definition | Was zählt als „funktioniert"? (z. B. Wiederkehrrate an Tag 3/7, nicht nur Session-Länge) |
| Rechtliche Kurzprüfung der Formulierung „Medizinisches Produktivitäts-Ökosystem" | App-Stores (Apple/Google) und viele Jurisdiktionen regulieren Gesundheits-Claims eng, besonders bei Zielgruppe Minderjährige. Klärung: Wellness-Framing statt Medizinprodukt-Framing, sofern keine klinische Studie/Zulassung geplant ist. |

**Go/No-Go-Kriterium:** Ohne ein funktionierendes Signal aus Phase 0 lohnt sich keine
Investition in Phase 1.

---

## Phase 1 — MVP: Ein Plattform, ein Loop (6–10 Wochen)

Ziel: Die kleinste Version, die die Kern-Feedback-Schleife (Realwelt → Plasma → Focus Run →
Belohnung) tatsächlich schließt.

**Scope:**
- Eine Plattform zuerst (Mobile, da Gyro + Widget dort am sinnvollsten sind — PC-Overlay kommt
  erst in Phase 3).
- Quick-Input: Freitext-Eingabe mit einfacher Heuristik statt vollem NLP (Schlüsselwort-/
  Längen-basierte Klassen-Einstufung reicht für den MVP; echtes NLP-Modell ist ein separates
  Arbeitspaket).
- Focus Run: Gyro-Steuerung, prozeduraler Tunnel, Fokus-Tore vs. Impuls-Tore, Plasma-Verbrauch,
  Graustufen-Fehlreaktion.
- Kein Boss-System, kein Metropolis-Metagame, kein PC-Overlay in diesem Schnitt.
- Einfaches, lineares Reward-Feedback (keine Rarity-Rollen) — Rarity-Loot ist bewusst auf
  Phase 2 verschoben, weil variable Belohnungen das höchste Suchtpotenzial im gesamten Konzept
  tragen und zuerst isoliert getestet werden sollten, nicht direkt im MVP mit allem anderen
  vermischt.

**Technik-Stack:** Unity (Mobile), lokale Persistenz (kein Server nötig für MVP), Test Flight /
Play Console Internal Track für Testgruppen.

**Exit-Kriterium:** Nutzer schließen den Loop mehrfach über mindestens 2 Wochen freiwillig ab.

---

## Phase 2 — Adaptive Engine, Boss-System, Rarity-Loot (8–12 Wochen)

Ziel: Die eskalierenden Mechaniken aus Abschnitt 4 hinzufügen — jetzt einzeln messbar, weil
Phase 1 die Baseline liefert.

**Arbeitspakete:**
- Adaptive Engine (Reaktionszeit- und Alltagsverhalten-Tracking → Schwierigkeitsanpassung).
- Apex-Metamorphose / Boss-Fights nach 48h.
- Kamera-Verifikation: **echte KI-Verifikation eines „aufgeräumten Schreibtischs" ist ein
  ungelöstes Computer-Vision-Problem** (subjektiv, kontextabhängig). Realistischer Zwischenschritt:
  Foto-Zeitstempel + einfache Heuristik (Bildveränderung ggü. Vorher-Foto) statt echter
  Zustandsbewertung, mit Option auf manuelles Selbst-Commitment als Fallback. Ein trainiertes
  Modell ist ein eigenes R&D-Arbeitspaket mit offenem Ausgang, keine gesicherte Lieferung.
- Rarity-basiertes Loot-System.
- Metropolis-Metagame (Gebäude, Skilltree, Dopamin-Menü für reale Belohnungen).

**Messpflicht in dieser Phase:** Da hier die Mechaniken mit dem höchsten
Abhängigkeits-/Erschöpfungsrisiko live gehen (Totalsperre, Bedrohung, Zufallsbelohnung in
Kombination), sollte ab Phase 2 ein Kill-Switch pro Mechanik existieren (serverseitiges
Feature-Flag), um einzelne Systeme ohne App-Update abschalten zu können, falls reale
Nutzungsdaten Erschöpfungs- statt Aktivierungsmuster zeigen.

---

## Phase 3 — Cross-Platform-Erweiterung (8–14 Wochen)

Ziel: Die in der Spec beschriebene Systemintegration — jetzt als eigenständige
Engineering-Spur, weil sie andere Technologien braucht als das Mobile-Spiel selbst.

| Komponente | Technologie | Abhängigkeit |
|---|---|---|
| PC-Desktop-Overlay (Taskbar-Hero) | Electron/Tauri (Windows) bzw. natives macOS-Overlay — eigene Codebasis, nicht die Unity-Mobile-App | Braucht Backend-Sync aus Phase 1 |
| Mobile-Widget (iOS WidgetKit / Android App Widgets) | Native pro Plattform | Braucht stabile lokale Datenschicht |
| Server-Sync (Plasma-Stand, Fortschritt, Cross-Device) | Backend (z. B. Supabase/Firebase für MVP-Tempo, später eigener Service) | Voraussetzung für alles in dieser Phase |
| Ablenkungs-Erkennung (blockierte URLs am Desktop) | OS-spezifische Hooks, datenschutzrechtlich zu prüfen (lokal verarbeiten, nichts an Server senden) | Eigenständiges Datenschutz-Review |

---

## Phase 4 — Visuelle & Audio-Fidelity („AAA-Politur") (Laufend, parallelisierbar)

Erst hier lohnt sich die volle Investition in Abschnitt 5 (Fluid-Shader, volumetrische Partikel,
Beat-Matching-Audio, FOV-Stretch-Overdrive). Parallel zu Phase 2/3 vorbereitbar
(Asset-Produktion, Audio-Komposition in 130–140 BPM-Varianten, Shader-Prototyping), aber erst
integrieren, wenn der Loop aus Phase 1–2 nachweislich trägt — sonst wird Zeit in Polish
investiert, bevor klar ist, ob das Fundament funktioniert.

---

## Phase 5 — Testing, Compliance, Launch (4–8 Wochen)

- Kontrollierte Playtests mit realer ADHS-Zielgruppe, mit Einverständniserklärung angesichts
  der Intensität der Mechaniken (Boss-Bedrohung, Totalsperre).
- App-Store-Review-Vorbereitung: Gesundheits-Claims, Altersfreigabe, Datenschutz bei
  minderjährigen Nutzern (z. B. COPPA/GDPR-K-relevante Anforderungen je nach Zielmarkt).
- Phasenweiser Rollout statt Full-Launch, mit aktiven Kill-Switches aus Phase 2.
- Monitoring-Dashboard: Wiederkehrrate, Abbruchrate nach Boss-Fights, Session-Länge über Zeit —
  als frühes Warnsystem für „Krisenfunktionalität statt nachhaltiger Aktivierung" (das Risiko,
  das im ursprünglichen Verhaltensprofil für Urgency-getriebene Systeme beschrieben ist).

---

## Grobe Zeitachse (sequenziell, bei kleinem Team teils parallelisierbar)

```
Phase 0  ██ (2–4 Wochen)
Phase 1    ██████ (6–10 Wochen)
Phase 2          ████████ (8–12 Wochen)
Phase 3                  ██████████ (8–14 Wochen)
Phase 4  ░░░░░░░░░░░░░░░░░░░░░░░░░░ (parallel ab Phase 2)
Phase 5                            ████ (4–8 Wochen)
```

Gesamt realistisch: **7–12 Monate** bis zu einem stabilen Cross-Platform-Launch, abhängig von
Teamgröße. Mit einer Einzelperson eher am oberen Ende oder länger, insbesondere für Phase 3
(native Overlay-/Widget-Entwicklung auf zwei OS-Familien) und die KI-Kamera-Verifikation in
Phase 2.

---

## Offene Risiken, die den Zeitplan verschieben können

1. **Kamera-KI-Verifikation** ist das am wenigsten gesicherte Arbeitspaket — plane einen
   einfacheren Fallback von Anfang an ein, statt darauf zu warten.
2. **Gesundheits-Framing** kann bei App-Store-Review oder rechtlicher Prüfung zu
   Nachbesserungen an Texten/Positionierung zwingen — früh klären, nicht erst vor Launch.
3. **Boss-/Sperr-Mechaniken** können in echten Nutzungsdaten anders wirken als im Design gedacht
   (Erschöpfung statt Aktivierung) — deshalb die Kill-Switches aus Phase 2, nicht erst als
   Post-Launch-Notlösung.
