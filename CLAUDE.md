# Pixie-Link: Pfad der Weisen — Projekt-Briefing

2D-Mobile-Game im Retro-16-Bit-Stil: Flappy-Bird-artiger Endless-Runner mit Rogue-lite-Elementen, inspiriert vom Zelda-Universum. Alles lebt in einer einzigen Datei: `pixie-link.html` (HTML5 Canvas, kein Framework, keine Dependencies).

## Architektur (bitte beibehalten)

**Strikte Trennung von Run-Logik und Permanent-Logik:**

- **Permanent-Logik** (überlebt jeden Run): `Storage` (localStorage mit In-Memory-Fallback, Key `pixielink_meta`), `SKILL_TREE`, `Meta`-Objekt. `Meta.perks()` ist die EINZIGE Schnittstelle zur Run-Logik — der Run kennt nur aggregierte Werte, nie den Baum selbst.
- **Run-Logik** (pro Run neu): `ROOM_TYPES` (datengetrieben), `RoomDirector` (streamt zufällige Raum-Sequenz), `Run`-Objekt. Rubine fließen erst beim Tod nach `Meta`.

**Raum-System:** 6 Typen in `ROOM_TYPES` (tore, stachel, schacht mit invertierter Gravitation, bloecke, geister/Deku-Dickicht, grotte als Belohnungsraum). Jeder Typ hat `weight`, `minDiff` (Freischaltung über Distanz, diff = dist/6000 gedeckelt auf 1) und `build(x0, len, diff)`. Neuer Raumtyp = neuer Eintrag im Objekt.

**Skillbaum "Tempel der Weisen":** 3 Äste × 3 Nodes mit Leveln, Kosten, Voraussetzungen (`req`). Pfad des Mutes (HP/Schild/Fee-Revive), Pfad der Weisheit (Magnet/Rubin-Wert/Grotten-Chance), Pfad der Kraft (Gravitation/Flap-Kraft/Air-Dash per Doppel-Tipp).

**Items (Wahl vor dem Run):** Nur Mut, Roc-Feder (höherer Flap, sanfter Fall), Bumerang (auto-Wurf, zerstört Blöcke/Dekus), Enterhaken (Halten = Luftanker mit Meter, goldene Kette zur Decke).

## Grafik-Stil (nach Referenz-Artwork des Nutzers)

- Interne Auflösung 320×480, `image-rendering: pixelated`, alles prozedural.
- `SPRITES`: Pixel-Map-Fabrik (String-Arrays → Offscreen-Canvas). Link mit blondem Pony, grüner Mütze, Schild vorn, Schwert am Rücken; Deku-Stachler; Rubin; Herz; Holz-Bumerang; Item-Icons.
- **3 Biome**, zyklisch alle 2200 Einheiten (`biomeAt`): Dungeon (blaue Ziegel, Ketten, Fackeln, Moos), Wald (heller Himmel, Wolken, Parallax-Bäume, Blätterdach-Decke), Lava (Vulkane, Glut-Partikel, Lavasee). Säulen passen sich dem Biom an.
- HUD: Herz-Sprites links, gerahmte Item-Box rechts oben (wie im Referenzbild).

## Loop-Design

Rubine sammeln → Tempel der Weisen (Upgrades) → bessere Überlebenschancen in schwereren Räumen. Schwierigkeit skaliert über Distanz (Tempo + Raum-Parameter).

## Roadmap (offen)

1. GitHub-Anbindung: Repo aktuell halten, Push nach jeder Änderung (Commit-Nachricht deutsch, kurz).
2. Sound (Web Audio API, Chiptune-Stil).
3. Balancing der Skillkosten-Kurve.
4. Optional: echte Sprite-Sheets statt prozeduraler Pixel-Maps.

## Testen

Headless-Smoke-Test hat sich bewährt: Canvas-Stub in Node, Bot-Runs pro Item bis zum Tod, Pixel-Farb-Assertions pro Biom, Skillbaum komplett kaufen, Persistenz prüfen. Bei Änderungen: `node --check` auf den Script-Block + kurzer Bot-Run.
