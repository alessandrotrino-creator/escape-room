# CLAUDE.md — Escape room «Il laboratorio scomparso»

Progetto **separato** dai «Giochi multitouch» (richiesta del docente del 05/10/2026: gioco a parte, cartella `Desktop\Escape room`, gruppo «Escape room» nel menu laterale di Claude). Stesso docente: matematica e scienze, scuola secondaria di primo grado, lingua italiana, nessun git/node sul PC (i file si caricano dal sito di GitHub).

## Richieste del docente (intervista del 05/10/2026)
- Per tutte e tre le classi; durata regolabile; **tante sfide**, da proporre lezione per lezione; anche in **cooperazione a più mani** sul multitouch; qualche **gioco di abilità a tema**.
- Due modi di giocare: **classe intera** (cooperazione) e **due squadre** (chi esce prima oppure più punti, a scelta).
- Stanze per argomento **e** avventure pronte; enigmi: lucchetti a codice, oggetti da trovare, puzzle da comporre, esperimenti simulati, più «altre cose accattivanti»; **tutorial con una stanza di esempio** per spiegare il gioco alla classe.
- Cooperazione: mani contemporanee, ruoli diversi (biglietti), ritmo e sincronia, abilità a tema.
- Ambientazione: laboratorio misterioso (il professor Rovelli è scomparso). Indizi: decide il docente (costano punti / tempo / gratis / nessuno). Editor semplice per creare stanze proprie (seconda tappa).
- Argomenti delle prime 12 stanze scelti da Claude: mat 1ª–2ª frazioni, potenze, aree; mat 3ª equazioni, relativi e piano cartesiano, solidi; sci 1ª–2ª materia e calore, cellula, corpo umano; sci 3ª elettricità, genetica, Terra e Universo.

## Struttura di `index.html` (un solo file, come gli altri giochi)
- Schermate `#setup`, `#play` (uno o due `.board` + `.mid` con tempo, pausa, fine), `#end`.
- Ogni stanza: 3 enigmi (stazioni) che danno ciascuno una cifra con un simbolo (● ▲ ■, `SYMS`), poi **la porta**: si inseriscono le cifre nell'ordine dei simboli (`G.order[ri]`). Cifre e ordine sono uguali per le due squadre (`G.digits`, `G.gen` per indice di stanza: stessi enigmi per entrambe).
- `ROOMS[id] = {n, topic, area (m12/m3/s12/s3), col, intro, deco (SVG 200×120), gen(l)}` con `l` 1–3; `gen` restituisce sempre 3 enigmi `{type, title, q, hint:[3], sol, …}`. Le stanze nuove si incollano al posto del segnaposto `/*@@STANZE@@*/`.
- Tipi (`PZ`): `lock` (tastierino, `ans`, `alts`, `unit`, `fig`), `choice` (`opts` da `opts4`/`nopts`), `order` (`items` nell'ordine giusto, `from`/`to`/`labels`), `match` (`pairs`), `find` (`items [{h,ok}]`, `why`), `dial` (esperimento: `min,max,step,ans,tol,fmt,draw(v)`), cooperazione: `hold` (maniglie da tenere premute insieme; alla LIM si accendono e poi «Attiva»), `roles` (4 biglietti leggibili solo tenendoli premuti; alla LIM uno alla volta), `rhythm` (`theme` cuore/polmoni/pompa/ingranaggio, `bpm`, `need`, `two`), `sync` (n ingranaggi da toccare insieme entro 350 ms; alla LIM diventa ritmo), `path` (trascinare `sprite` goccia/elettrone/globulo/molecola/cometa lungo un percorso a tappe senza uscire; niente scorciatoie: si controlla solo il tratto corrente).
- Contesto dei tipi: `c = {d, el, B, lim, sig (AbortSignal per i listener), ok(), err(msg, soft), say(), loop(fn)}`; `loop` usa requestAnimationFrame, ma un timer se la pagina è nascosta (prove automatiche).
- Squadre: `B.team` 0 = classe intera, 1/2 = metà dello schermo, `'L'` = LIM a due squadre su un solo tabellone con turni (`G.turn`; un errore passa il turno e azzera gli errori dell'enigma per chi subentra).
- Punti: enigma = max(2, 10 − 2·errori − 3·indizi se gli indizi costano punti); porta +5. Errore = blocco 2 s (non alla LIM né nel tutorial); indizio «a tempo» = blocco 15 s. «Salta» (tenuto premuto 2 s dal docente) dà la cifra senza punti.
- Tutorial: stanza `prova` con il «coach» (`COACH`, `coach()`), avviato da «▶ Prova la stanza di esempio»; non registra.
- Avventure pronte in `ADV` (le stanze che mancano risultano «in arrivo»).
- Registro di classe: blocco comune `REG` copiato da `giochi multitouch\duello.html` (stessa chiave `gm-registro`, quindi sullo stesso sito GitHub le partite compaiono anche in `registro.html` dei giochi multitouch); unica differenza: il link «Registro e statistiche» punta all'indirizzo assoluto del registro sul sito. `gioco:'escape'`; una voce per enigma risolto, saltato o lasciato a metà. Per evitare conflitti la barra del ritmo si chiama `.rprog` (`.rbar` è del registro).
- Prove: `window.__escTest` (P, G, ROOMS, PZ, ADV, getGen, solve, start…). Verificati nel browser il tutorial completo, lucchetto, provette, leve a più mani, percorso (anche l'uscita dal bordo), ingranaggi, ritmo, LIM a turni, registro.

## Stato al 06/10/2026 (fine prima tappa)
- 12 stanze + `prova`, tutte in `index.html`: frazioni e cellula (Claude); materia, corpo, elettricita, genetica, universo (aiutante scienze); potenze, aree, equazioni, relativi, solidi (aiutante matematica). Verificate generando 300 stanze per livello ciascuna (0 errori: risposte presenti e non doppie, niente NaN, tipi validi) e giocando per intero l'avventura «Fuga dal laboratorio».
- Attenzione quando si aggiungono stanze: il segnaposto `/*@@STANZE@@*/` non deve comparire dentro un commento del testo incollato (chiuderebbe il commento: è già successo due volte).
- Note degli aiutanti: dati arrotondati da libro (gittata 70 mL, 0,5 L d'aria per respiro, asse terrestre 23,5°, Luna a 384 000 km); i numeri grandi nelle scienze usano spazi (`sp()` locale) invece del punto di `N()`; ritmo «polmoni» lento (16–20 respiri al minuto).

## Stato all'08/10/2026 (seconda tappa, prima parte)
- **22 stanze**: in più divisibilita, angoli, proporzioni (m12); probabilita, cerchio, letterale (m3); piante, ecosistemi (s12); forze, terra (s3). Avventure nuove in `ADV` (con `lv` = livello consigliato, che si imposta toccando l'avventura): numeri, cantiere, giungla, pianeta, scommessa, maratona.
- **Varianti in più**: `ROOMS.id.more(l)` restituisce ≥ 6 enigmi con `slot` 1 (lucchetto/conoscenza), 2 (prova), 3 (squadra). `pickEnigmas()` chiama `gen` 8 volte + `more` 2 volte, raggruppa per firma `stanza|livello|tipo|titolo` e sceglie prima le firme mai viste o viste da più tempo (`localStorage['escape-seen']`). **Il titolo identifica la variante**: varianti nuove = titoli nuovi.
- **Impostazioni nuove** in `P`: `lvl` 0 = Crescente (`lvlOf(ri)`), `nEn` 3/4/5 enigmi per stanza, `doors` ('varie'|'cifre'), `scenes` ('varie'|'lab'), `skill` (giochi di abilità).
- **Porte** (`DOORS`, una per stanza a rotazione, `G.dk[ri]`): simboli (classica), cassaforte (manopola a giri ↻/↺), lettere (parola segreta, `PAROLE`), fili (cavi nelle prese), stelle (coordinate su un cielo), pozione (gocce nel calderone), grimaldello (abilità: tempismo sui perni). Ogni porta ha `make(l, m)`, `tok(D, i)` (il «pezzo» dato dall'enigma i), `badge`, `render(c, B, D)`. Apertura con animazione dei battenti (`cinema()`), coriandoli, raggi sul pezzo trovato.
- **Ambientazioni** (`SCENES`, `G.scene[ri]`): lab, cantina umida, cripta, soffitta, serra, osservatorio, officina, grotta. SVG con animazioni SMIL (gocce, torce, pipistrelli, ingranaggi, vapore, stelle). `SCENE_PREF` lega alcuni argomenti a un posto (60%).
- **Abilità**: `skillize()` trasforma a volte `find` in `catch` (bolle che salgono) e `match` in `memory` (carte coperte).
- Prova automatica: `scratchpad` della sessione con `inject.js` (tutte le stanze × livelli × 3/4/5 enigmi, tutte le porte renderizzate): 0 errori.

## Stato al 18/10/2026: stanza da esplorare (prototipo)
- Ispirazione: «Wolf Escape» di una collega (3D in prima persona con Three.js). Copia di sicurezza prima del lavoro: `_backup/index-2026-10-18-prima-della-stanza-3D.html`.
- **Impostazione «La stanza»** (`P.view`): `auto` = 3D per la classe intera e per la LIM a turni, disegnata (panorama) quando le squadre dividono lo schermo; `pano` = sempre disegnata; `classica` = riquadri come prima (solo allora compare «Ambientazione»). Se WebGL o Three.js mancano si passa da soli al panorama (`X3FAIL`).
- Codice: sezione `ESPLORA` prima dei pulsanti finali. Si aggancia a `enterRoom` (`exEnter`), `renderScene` (`exRefresh`), `solve` (fumetto di festa), `wrong`/`useHint` (faccina `exFace`), `stopAll` (`exDispose`). Stato sul tabellone: `B.x = {host, W, mode:'3d'|'pano', r3, pv}`; `B.el.dataset.w` = pelle del luogo (`lab`, `pizza`, `cell`) usata dal CSS dei pannelli.
- **Three.js r128** da cdnjs, caricato solo quando serve (`loadThree`). Stanza: `x3Room`/`x3Scene`; oggetti degli enigmi per tipo in `PROP3` (cassaforte, provette, lavagna, schedario, lente, esperimento, leve, bacheca, cuore, tubo, ingranaggi, vasca di bolle, carte), posti in `SLOT3`; porta `x3Door` (o `x3Pore` nella cellula); cartellini `x3LabelDraw`. Si trascina per guardare, si tocca il pavimento per camminare, si tocca un oggetto per avvicinarsi e aprire l'enigma; frecce/WASD al PC; pulsanti ↺ ▲ ↻ per la LIM. Con un enigma aperto la stanza si ridisegna 1 volta su 8.
- **Panorama** (`pvRoom`): SVG 2400×600 largo 4 volte l'altezza, si trascina; disegni in `PVART.lab/pizza/cell`; oggetti = `ICON[type]` sopra tavoli/piattaforme.
- **Luoghi** (`WORLDS`): `frazioni` → «La pizzeria delle frazioni» (prof.ssa Fettucci), `cellula` → «Dentro la cellula» (dott. Vetrini e l'ameba Gelatina); tutte le altre stanze e il tutorial → laboratorio colorato con il `col` della stanza (Ottavia, assistente di Rovelli). In 3D: `X3W.lab/pizza/cell`. Per aggiungere un luogo: voce in `WORLDS` (cast, skin, intro, facts, eventuali `door`/`round`), `X3W[skin]`, `PVART[skin]`, CSS `.board[data-w="skin"] .panel…`, `FLOW[skin]`.
- **Personaggi** (`CAST`, `chrSVG(c, mood, viewBox)`): SVG 200×320 con parti animate dal CSS (`.eyes`, `.hair`, `.arm`, `.bodyb`, `.mouth`, `.pet`); umori `ok`/`oops`/`think`. Parlano con `exTalk` (fumetto in basso a sinistra; toccarli nella stanza dà curiosità `facts`).
- **Gioco di abilità nuovo** `flow` (nastro delle pizze / vescicole vaganti / nastro trasportatore): `skillize(e, id)` trasforma a volte un «cerca» in `flow` quando la visuale non è classica.
- **Enigmi nuovi** (in `more`, blocco prima del segnaposto delle stanze): frazioni → il conto della pizzeria, le pizze in vetrina (pizze disegnate, anche equivalenti), le pizze più che a metà; cellula → mitosi, procarioti, schedario del microscopio, organuli della cellula animale. Verificati 2400 enigmi generati: 0 errori.
- Limite già presente anche nella versione classica: sul telefono in orizzontale il tastierino non sta tutto nel pannello.
- **Direzione artistica (richiesta del docente, 18/10/2026)**: è un'escape room per ragazzi di 12-14 anni → misteriosa, cupa, a tratti industrial/punk, sotterranei, pioggia e buio, colori spenti con luci al neon; personaggi in **stile manga** (non da bambini piccoli); battute asciutte e un po' inquietanti. Mantenere questo tono in ogni luogo nuovo.
  - Luoghi attuali: «il sotterraneo di Rovelli» (`lab`: cemento, tubi che perdono, neon che ronzano, finestrella con pioggia e fulmini, porta blindata con spia), «La pizzeria clandestina» (`pizza`: cantina, insegna al neon rossa, grata con pioggia, forno), «Dentro la cellula» (`cell`: buio rosso scuro, organuli bioluminescenti, luce che batte come un cuore).
  - Strumenti 3D: `grime()` e `TX.concrete/metal/wet/hazard/neon/window`; `K.pipe`, `K.steam`, `K.rain`, `K.puddle`, `K.neonLight`, `K.flashes` (lampi). La spia sopra la porta diventa verde quando la porta si può aprire.
  - Atmosfera: `AMB` (pioggia sintetizzata, battito nella cellula, tuono ai fulmini; solo con «Suoni: Sì»), `exStorm` (fulmine ogni 9-23 s nei luoghi con pioggia). Con la visuale non classica tutto il gioco passa al tema scuro (`data-theme="dark"`, `#play.xdark`).

- **Tutte le 22 stanze hanno il loro luogo** (18/10/2026). Oltre a sotterraneo, pizzeria e cellula ci sono 8 ambienti (`WSKIN`, voci create in fondo a `WORLDS` con nome, intro e 3 curiosità per stanza):
  - `archive` archivio allagato (Minerva Calcagno): potenze, equazioni, letterale;
  - `foundry` fonderia/officina (Bruna Vulcano): aree, angoli, materia, forze;
  - `crypt` cripta/sala anatomica (dott. Osvaldo Necchi): divisibilità, corpo;
  - `casino` bisca clandestina (Asso Ferretti): proporzioni, probabilità;
  - `cave` miniera, stanza rotonda (Dante «Ruggine» Ferrari): relativi, solidi, terra;
  - `green` serra abbandonata con la pianta carnivora (Edera Lombardi): piante, ecosistemi, genetica;
  - `obs` osservatorio con la cupola aperta, stanza rotonda (Selene Moretti): cerchio, universo;
  - `power` centrale in blackout con bobine di Tesla (Elettra Zappa): elettricità.
  - Ogni ambiente ha: `X3W[skin]` (3D; stanze rotonde con `x3Round`), `PVART[skin]` (disegno per le squadre), CSS dei pannelli con il colore `--pk`, `FLOW[skin]` (titolo e moto `belt`/`float` del gioco del nastro), suono in `AMB` (pioggia nei luoghi `STORMY`, ronzio in fonderia e bisca, vento e gocce in cripta e miniera, battito nella cellula).
  - `chrSVG` ora ha anche acconciature `lunghi`, `rasato`, `corti`, `chignon`; cappelli `casco`, `cilindro`, `saldatore`; `mask`, `beard`, `occhiaie`, `foglie`, `sparks`; attrezzi `libro`, `martello`, `bisturi`, `carta`, `piccone`, `foglia`, `cannocchiale`, `tester`.
  - Richiesta del docente: **cupo sì, ma si deve capire**. Ogni oggetto degli enigmi ha davanti una luce chiara sua; se un ambiente risulta troppo buio si alzano `amb`/`hemi` e si abbassa la nebbia nel `return` del builder (la cripta è già stata schiarita così). Gli arredi grandi vanno messi dove si vedono dalla partenza (0; 3,6) guardando verso la porta.
  - Verifica: tutte le 22 stanze aperte in 3D e in panorama senza errori; giro completo (enigmi → porta → stanza successiva) provato.

## Stanza speciale: la sala giochi (storia dei videogiochi, 18/10/2026)
- `ROOMS.videogiochi` (area `x` = «Stanze speciali», avventura `gameover` «Game over?»), luogo `arcade` «La sala giochi abbandonata» con Nico «Gettone» Arcadi (cappellino, cuffie, joystick). Cabinati 3D con schermi animati (`x3Cab`, `cabAnim`), pioggia sulla vetrina, bip delle macchine in `AMB`.
- Enigmi: 1 di storia (`VG.know`: anni trascorsi, pixel, bit = potenze di 2, tetramini, Jumpman, fantasmi di Pac-Man, linea del tempo di giochi e console, autori) + 2 **minigiochi** (`type:'arcade'`, `game`, `lv`; `VG.game`).
- Minigiochi in `ARC` (motore comune `PZ.arcade`: console d'epoca `cons`, pulsanti a più mani, tastiera, trascinamento, strisciata, tocco; perdere una vita non toglie punti): `pong` (cooperativo a due racchette; alla LIM la destra è automatica), `invaders`, `pacman`, `kong`, `tetris` (su console portatile), `arkanoid`, `bubble`, `pang` (Amiga), `boulder` (Commodore 64), `platform` (NES), `runner` (Mega Drive), `racer` (PlayStation, pseudo-3D). Ogni gioco ha `orig` = scheda «Il gioco originale» (titolo, casa, piattaforma, anno, curiosità) mostrata sopra la console. I nomi sono descrittivi e la grafica è originale: i titoli veri compaiono solo come informazione storica.
- Verifica: ~3000 enigmi generati senza errori; ogni minigioco vinto a tutti e tre i livelli con un giocatore automatico (simulazione con `__escTest.ARC[k].step`); console provate su PC, telefono verticale e squadre. Nota per le prove: con il pannello di anteprima nascosto `requestAnimationFrame` rallenta, quindi i minigiochi vanno simulati chiamando `step` a mano.

## Da fare
- (fatto) Stanza sulla storia dei videogiochi con miniprove in stile giochi classici (richiesta del 18/10/2026).

## Da fare (seconda tappa)
- Editor semplice delle stanze del docente (domande → codici dei lucchetti, sfondo), con esporta/importa.
- Altre stanze e avventure.
