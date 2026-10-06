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

## Da fare (seconda tappa)
- Editor semplice delle stanze del docente (domande → codici dei lucchetti, sfondo), con esporta/importa.
- Altre stanze e avventure.
