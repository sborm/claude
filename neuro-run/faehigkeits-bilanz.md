# NEURO-RUN — Fähigkeits-Bilanz (Stufe 5)

Ehrliche Bestandsaufnahme des Web-Prototyps aus den Stufen 1–4: was davon ist eine echte,
funktionierende Implementierung — und was ist eine bewusste Näherung an eine Mechanik, die in
dieser Sandbox (kein Spiele-Engine, kein Mobilgerät, kein App-Store-Zugang) nicht nativ gebaut
werden kann. Diese Unterscheidung war von Anfang an Teil des Plans (siehe
`spieldesign/neuro-run-entwicklungsplan.md`, Abschnitt „Realistische Grundannahme").

## Echt und verifiziert

| Mechanik | Status | Verifiziert durch |
|---|---|---|
| Task-Klassifikation (Klasse 1/2/3) | Echte Keyword-/Längen-Heuristik, live im Code | Gezielte Eingaben, klassenkorrekte Plasma-Gains bestätigt |
| Plasma-Ökonomie (Drain, Pause-Fenster, Boss-2x) | Echte zeitbasierte Zustandsmaschine | Playwright-Zeitmessung: Drainrate ~1/s normal, ~2,2/s im Boss |
| Tor-/Wand-Kollision | Echte Projektionsgeometrie + Toleranzfenster | Pixelgenau gezielte Treffer: Blau=Erfolg, Rot=Fail, je einzeln erzwungen und bestätigt |
| Adaptive Engine | Echte gleitende Erfolgsquote (letzte 8 Ergebnisse) steuert Spawn-Rhythmus/Toleranz | Skill-Wert vor/nach Serien von Treffern/Fehlversuchen verglichen |
| Boss-Zeitschwelle (Apex-Metamorphose) | Echte Zeitstempel-Logik, 48h-Prinzip (Demo-Override via `?apexMs=`) | Erzwungener Apex-Task löste Boss nach konfigurierter Schwelle zuverlässig aus |
| Kamera-Aufnahme | Echter `getUserMedia`-Zugriff, echtes Frame-Capture | Mit echtem Fake-Videogerät in Playwright durchlaufen |
| Rarity-Loot & Metropolis | Echte Buchhaltung (Counts, Baukosten, Freischaltung), persistiert | Bau einer Akademie zog Blueprint korrekt ab, Zustand blieb nach Reload erhalten |
| Kill-Switches (4 Mechaniken) | Echte Laufzeit-Umschaltung, sofort wirksam | Jede einzeln deaktiviert und Wirkung im laufenden Spiel bestätigt (u. a. Boss trotz überfälligem Apex-Task blieb bei deaktiviertem Schalter aus) |
| PWA (Manifest + Service Worker) | Echt registriert und aktiviert | Über echten lokalen HTTP-Server: Service Worker „activated", Cache-Inhalt geprüft, **Offline-Reload tatsächlich funktionsfähig** |
| Persistenz | Echtes `localStorage` | Übersteht Reload, von der Desktop-Overlay-Seite live mitgelesen |
| Sound | Echte prozedurale Web-Audio-Synthese | Keine externen Audiodateien nötig, läuft offline |

## Bewusst genähert — und warum

- **Gyro-Steuerung** nutzt die echte `DeviceOrientation`-API (kein Mock), ist aber gröber und
  weniger präzise kalibriert als native Sensor-Fusion in Unity/UE5, und verlangt auf iOS einen
  expliziten Freigabe-Tap statt automatischem Zugriff.
- **„NLP"-Eingabe** ist eine reine Keyword-/Längen-Heuristik, kein trainiertes Sprachmodell. Für
  den Spielloop ausreichend, aber nicht das in der Spec beschriebene NLP.
- **Kamera-„Verifikation"** nimmt ein echtes Foto auf, bewertet dessen Inhalt aber **nicht** —
  keine Objekterkennung, keine Einschätzung „ist der Schreibtisch aufgeräumt". Die Bestätigung
  läuft über einen bewussten Tap, nicht über vorgetäuschte KI. Das steht auch direkt im Kamera-
  Dialog des Prototyps.
- **Desktop-Overlay** ist ein Browser-Popup-Fenster, das ausschließlich seinen **eigenen**
  Fokus-/Blur-Zustand kennt. Es kann keine andere Anwendung, keinen anderen Tab und keine echte
  OS-Taskleiste beobachten oder einnehmen — das ist aus einer Webseite heraus technisch nicht
  möglich (Browser-Sicherheitsmodell), nicht nur eine fehlende Funktion.
- **Ablenkungs-Erkennung** (blockierte URLs in anderen Tabs/Programmen) ist nicht implementiert
  und kann von einer Webseite aus grundsätzlich nicht beobachtet werden.
- **3D-Fidelity**: Canvas2D-Pseudo-3D-Projektion statt eines echten 3D-Engine-Renderings — keine
  echten Shader, Normal-Maps oder Physik-Engine wie in UE5/Unity.
- **Sound** ist synthetisiert, kein komponierter oder lizenzierter Track.
- **Kein Server-Sync**: Fortschritt lebt nur in einem Browser/Gerät. Keine Cross-Device-
  Synchronisation, wie sie Phase 3 des Entwicklungsplans vorsieht.
- **Keine App-Store-Distribution**: kein Code-Signing, kein natives Binary, keine
  Installer-Pakete, keine Altersfreigabe/Health-Claims-Prüfung durch einen echten Store.

## Einordnung in den Entwicklungsplan

Dieser Prototyp deckt inhaltlich ab, was **Phase 0 (Validierung)** und **Phase 1 (MVP)** des
`neuro-run-entwicklungsplan.md` vorsahen — plus Teile von Phase 2 (Adaptive Engine, Boss,
Rarity-Loot, Metropolis) und einen ersten, ehrlichen Schritt in Richtung Phase 3 (PWA-
Installierbarkeit). Was in der Studio-Roadmap als eigene Phasen offen bleibt und **nicht** durch
weitere Arbeit an diesem Web-Prototyp entsteht, sondern echte native Entwicklung außerhalb dieser
Umgebung braucht:

- Echte Computer-Vision für die Kamera-Verifikation (Phase 2, offener Ausgang laut Roadmap).
- Native OS-Widgets und echte Taskbar-Einbettung (Phase 3).
- Server-Sync über Geräte hinweg (Phase 3).
- App-Store-Review, Health-Claims-Prüfung, Altersfreigabe (Phase 5).
- Kontrollierte Playtests mit echten Nutzer:innen und Monitoring auf Erschöpfungs- statt
  Aktivierungsmuster (Phase 5) — das gilt unabhängig vom Technik-Stack und lässt sich mit diesem
  Prototyp bereits informell beginnen.

## Fazit

Alles, was in diesem Prototyp als „funktioniert" bezeichnet wird, wurde tatsächlich ausgeführt
und mit Playwright verifiziert — nicht nur behauptet. Alles, was genähert ist, ist im Code und in
diesem Dokument als solches gekennzeichnet, mit der technischen Begründung, warum es in einer
Browser-Sandbox nicht anders geht. Der Weg von hier zu der in `NR-2026-CORE-SPEC` beschriebenen
nativen AAA-App ist der in `neuro-run-entwicklungsplan.md` beschriebene — dieser Prototyp beweist,
dass der Kern-Loop tatsächlich spielbar funktioniert, bevor in native Entwicklung investiert wird.
