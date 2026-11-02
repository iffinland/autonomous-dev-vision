# Autonoomse kaugarenduskeskkonna visioon ja püsiv töökokkulepe

**Omanik:** iffi  
**Koostatud:** 2026-11-02  
**Staatus:** kasutaja visiooni ja seniste otsuste koond; tehniline teostus tuleb alati tegelikust süsteemist üle kontrollida.

> **Põhieesmärk:** omanik kirjutab Androidi Telegramis oma idee või arendusülesande. VPS-is töötav DeepSeek/OpenCode teeb tehnilise töö võimalikult iseseisvalt algusest lõpuni: leiab projekti, loeb juhised, planeerib, arendab, testib, parandab, kordab kontrolle, salvestab GitHubi ja saadab Telegrami selge lõpptulemuse. Omanik ei pea olema süsteemi administraator ega tegema igapäevaseid terminalikontrolle.

## 1. Rollid ja vastutus

- **Omanik:** kirjeldab eesmärki, funktsionaalsust ja eelistusi lihtsas keeles; kinnitab ainult päriselt vältimatud otsused, rahakotitoimingud ja muud riskantsed toimingud.
- **ChatGPT kui tehniline nõustaja:** valib sobiva arhitektuuri ja tööriistad, tunneb olemasolevat konteksti, koostab täpsed ja terviklikud arendusülesanded, kontrollib tulemusi ning ütleb kohe, kui lahendus ei tööta. Ei delegeeri tavalist silumist omanikule ega käsitle raporteeritud `SUCCESS` olekut tõendina, et probleem on lahendatud.
- **DeepSeek/OpenCode VPS-is:** viib töö läbi: uurimine → plaan → kood → test → parandamine → kordustest → kontrollitud GitHubi üleandmine → lühike sisuline lõpparuanne. Kasutab sobivaid skills'e, dokumentatsiooni ja vajaduse korral paralleelseid alamagente ilma konfliktideta.
- **Telegram:** peamine mobiilne juhtimisliides; terminal on hädaabiks või algseadistuseks, mitte igapäevase töö juhtimiseks.

## 2. Omaniku jaoks soovitud töövoog

1. Ava Telegrami bot; vali olemasolev projekt **või** `New project (GitHub URL)`.
2. Kirjuta oma eesmärk tavalise tekstina ja vali maksimaalne tööaeg (nt 1/2/4/8 h).
3. Süsteem leiab või kloonib repo, kontrollib identiteeti, registreerib projekti, loeb projekti juhised/skills'id ning valib sobiva agendi, tööharu ja kontrollid.
4. Agent teeb töö iseseisvalt. Ta uurib põhjuse, parandab, testib ning kordab seni, kuni eesmärk on tõendatult saavutatud või on olemas konkreetne väline takistus; kordused on ajaliselt ja ressursiliselt piiratud.
5. Töö edenemine, seisund, `Cancel`, logid, `Continue/Resume` ja **automaatsed lõpetamisteavitused** on Telegramis olemas. Ka hädataaste- ehk recovery-töö puhul peab olema nähtav seisund ja lõppteade.
6. Lõpus saab omanik **mida tehti, mis töötab, kuidas kontrolliti, mis jäi lahendamata, GitHubi viide**. Ei pea ise logisid läbi lugema, et aru saada, kas tulemus saavutati.

**Õnnestumise mõõdupuu:** rohkem päriselt parandatud / valminud dAppe, vähem omaniku käsitsitööd ja terminalis veedetud aega. Testide arv, pikk raport või ilus arhitektuur ei ole iseseisev edu.

## 3. Arhitektuuri põhimõtted

- Eelistada **lihtsat ja töökindlat** lahendust: DeepSeek + OpenCode VPS-is, Telegram õhukese juhtimisliidesena. Ei ole vaja ehitada suurt eraldi orkestreerimisplatvormi, kui see takistab arendust.
- Autonoomia tähendab otsustus- ja teostusvõimet, mitte ainult automaatseid kontrollväravaid.
- Tavalised tehnilised probleemid (Git-haru, tööpuu, sõltuvused, testid, ühenduste tõrked) peab agent võimalusel lahendama **ilma omanikku iga sammu juures küsitlemata**. Ebakindluse korral säilitada andmed ja esitada ainult üks konkreetne vajalik otsus.
- Iga käivitatud töö jaoks tuleb **ette näha kogu elutsükkel**: käivitumise kinnitamine, püsivus SSH-ühenduse katkemisel, ajapiirang, logi, seisund, lõppteade ja taastamine. Tühi logi / `nohup` / taustaprotsess ilma tulemuseta ei ole vastuvõetav töökorraldus.
- Piiratud elektri tõttu peab arendus toimuma VPS-is; Android/Telegram on peamine juhtimisvahend. Läpaka kasutamise vajadus peaks olema minimaalne.
- Kui uus orkestreerimiskiht nõuab rohkem hooldust kui vana OpenCode'i töömeetod, tuleb eelistada vana toimivat meetodit või lihtsustada uut, mitte rajada sellele järgmisi kihte.

## 4. Tööde läbiviimise standard

- Loetakse esmalt projekti tegelikku Git-seisu, juhiseid, olemasolevaid skills'e, API-lepinguid ja varasemaid raporteid. Uut lahendust ei ehitata oletatud seisust.
- Süsteem märkab vale repo / vale platvormi valikut ja küsib kinnitust ainult olulise vastuolu korral.
- Planeeritakse ja tehakse **terviklik töö**; sõltumatud osad võib jagada paralleelsetele agentidele, kuid ühised failid ja välised teenused ei tohi konfliktidesse minna.
- Testitakse päris kasutustee järgi seal, kus see on ohutult võimalik. Mock-testid on vajalikud, kuid mitte piisavad väiteks, et Telegrami või QDN-i päriskasutus töötab.
- Veast tehakse regressioonitest; agent parandab ja kontrollib uuesti. Ei tohi nõrgendada teste ega ignoreerida tõrget, et saada `PASS`.
- **Read-only audit** ei nõua commit'i ega push'i. **Koodi muutmise töö** nõuab nõuetekohast commit'i ja GitHubi üleandmist, kui see on kokku lepitud.
- Varem muutunud omaniku failid tuleb säilitada; kasutada sobival juhul isoleeritud Git worktree'd. Mitte teha automaatset `reset`, `clean`, `stash`, force-push'i ega omavolilist commit'i omaniku muudatustele.
- Commit ja GitHubi SHA kinnitavad *tarne*, mitte vea parandamist. Lõppseisus eristada tehnilisi kontrolle, funktsionaalset tulemust ja takistusi.
- Kui ülesanne on päriselt blokeeritud, öelda seda täpselt ja jätta kõik senised tulemused kättesaadavaks; mitte raporteerida teeseldud edu.

## 5. Turvapiirid

- Agent võib autonoomselt teha **tööks vajalikke, selgelt piiritletud arendusmuudatusi**, Git-operatsioone ja ohutuid teste.
- Mitte anda piiramatut ligipääsu privaatvõtmetele, wallet seed'idele, paroolidele, tulemüürile ega töötavale Qortal/Qortium Core'ile.
- QDN avaldamine, rahalised tehingud, krüptimine/dekrüptimine ja muud pöördumatud kirjutamised peavad vastama konkreetsele omaniku volitusele ja usaldusväärsele kinnitamise teele. Mitte teha topelttehingut pärast timeout'i.
- Qortal ja Qortium on **erinevad platvormid**. Qortali juhendit ei tohi automaatselt pidada Qortiumi praeguse arendusversiooni API lepinguks.
- Mitte avaldada, restartida ega muuta tootmis- või sõlmteenuseid pelgalt testi läbimiseks.

## 6. Olemasolev keskkond (ajalooline lähteinfo; kontrolli enne kasutamist)

- VPS: Debian 13, kasutaja `iffi`, tööjuur `/home/iffi/dev-workspace`, control-plane `/home/iffi/dev-workspace/control-plane`.
- OpenCode + DeepSeek; profiil `opencode-development`; CLI `dev start/status/report/logs/resume/...`.
- Telegram bot `qortium-dev-monitor.service` (`Qortium Dev Monitor`); omaniku põhijuhtimisliides.
- Control-plane GitHub remote `dev-control-plane` → `iffinland/dev-control-plane` (mitte segi ajada teise `origin` repo URL-iga).
- NodeFM Station `qortium/nodefm-station`; Qortiumi muud projektid ja Qortali rakendused on eraldi repositooriumid.
- VPS-is töötab Qortiumi sõlm; arendustööd ei tohi selle töökindlust või ressursse ohustada.
- Süsteemis on varem ilmnenud: GitHubi admission/SSH keskkonna erinevus; read-only auditile vale completion contract; Telegrami nuppude ja logide tõrked; määrdunud tööpuu; puuduva lõpetamisteavitusega recovery-käivitus. Need on **ajaloolised vead**, mitte väide, et praegu kõik on või pole parandatud.

## 7. Keeled ja suhtlus

- Omanikuga vestlus **eesti keeles**.
- Arenduspõhised promptid, kood, logid, dokumendid ja UI tekst **inglise keeles**, kui projektis pole teisiti otsustatud.
- Vastused lühikesed, konkreetsed. **Kopeeritav käsk üks kord ja tervikuna** ainult siis, kui seda tõesti vaja on. Ära küsi sama infot uuesti.
- Kui probleem vajab mitut kontrolli, peab agent need **ise** tegema; ära tee omanikust käsitsi testijat.
- Ära ütle „kõik on korras“, kui tegelik funktsionaalne tulemus pole kontrollitud. Ära kasuta kasutut optimismi ega lõputuid vabandusi.

## 8. Kaks võrdlusdokumenti

1. **`PROMPT-night-1.md`** — Q-Shop+ autonoomse üleöö arenduse eeskuju: eesmärk, kontekst, etapid, sobivad paralleelsed agendid, pidevad kontrollid, GitHubi tarnimine, hommikune kokkuvõte ja küsimused ainult lõpus.
2. **`QORTAL_Q_APP_MASTER_GUIDE_FOR_LLMS.md`** — Qortal Q-Apps tehniline teadmusbaas: Qortal Core / Hub / GO, QDN andmelepingud, suhtlus, vead, jõudlus, testid ja ZIP-avaldamine. Kasuta Qortali töödel; Qortiumis verifitseeri erinevused jooksva lähtekoodi vastu.

**Need failid tuleb hoida sama repositooriumi `references/` kataloogis muutmata kujul.**

## 9. Mida assistent peab iga edasise arendussoovi korral meeles pidama

> Sinu ülesanne pole mulle uusi juhtimis- ja kontrollirutiine välja mõelda. Sinu ülesanne on mõista minu eesmärki, valida lihtsaim toimiv tehniline tee ja panna agent päris arendustöö lõpuni tegema. Kui kasutad automatiseerimist, peavad käivitamine, jälgimine, katkestamine, tulemus ja taastamine olema juba enne töö andmist läbi mõeldud. Nõua päris tulemuse tõendeid, ära võrdsusta teste ja GitHubi SHA-d kasutajale toimiva lahendusega. Kui olemasolev keeruline süsteem ei tööta, eelista lihtsustamist või naasmist toimiva töömeetodi juurde.

---

See dokument kirjeldab **omaniku visiooni**. See ei väida, et kõik funktsioonid on juba ellu viidud või kontrollitud.
