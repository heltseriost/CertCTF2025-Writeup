# CertCTF2025-Writeup
Samtliga lösningar för CertCTF2025

Följer tidslinjen i attacken.

### ***Angriparens IPv4-adress***

Fråga: Vilken IPv4-adress använde angriparen sig av initialt i attacken?

*Kategori: Adresser*,  *Poäng: 100*

Vi vet från "scenario.txt" att angriparen har kopplat in en mini-dator på nätverket där skrivaren egentligen sitter, möjligtvis på samma subnät.

Genom att titta på "IPv4 statistics" ser vi att ip-adresser på subnätet "192.168.177.0/24" står för den absoluta majoriteten av trafiken:

<img width="1501" alt="SCR-20250322-noff" src="https://github.com/user-attachments/assets/ca3684ac-77c8-464d-8f36-8bbdf3ca8941" />

Tittar vi initial på trafiken ser vi att det sker massa trafik udda trafik från "192.168.177.141" bland annat massa arp-request, misslyckade tcp-anslutningar och sen en uppkoppling mot 192.168.177.155 på port 4444 vilket är misstänkt. Port 4444 är en bland annat en klassisk "lyssnarport" för "Metasploit framework" och Meterpreter. 

https://www.cbtnuggets.com/common-ports/what-is-port-4444

Vi kan då räkna ut ganska snabbt att ip-adressen som angriparen använde sig av initialt var "192.168.177.141"

`Svar: 192.168.177.141`

---

### ***FTP-serverns IPv4-adress***

Fråga: Vilken IPv4-adress hade FTP-servern?

*Kategori: Adresser*,  *Poäng: 100*

Vi vet från "scenario.txt" att filservern är drabbad, i och med anslutningen från 192.168.177.141 till 192.168.177.155 kan vi ganska snabbt lista ut att det är filservern.

Dessutom använder ftp port 21. Filtrerar vi på lyckade anslutningar på port 21 ser vi endast en adress: "192.168.177.155" vilket innebär att den porten bara är öppen på den ip-adressen. Ännu en indikation på att det är filservern.

<img width="1384" alt="SCR-20250322-ntzk" src="https://github.com/user-attachments/assets/1089ea19-ebfc-4181-a086-b7a28f5f86da" />

`Svar: 192.168.177.155`

---

### ***Domänkontrollantens IPv4-adress***

Fråga: Vilken IPv4-adress hade domänkontrollanten?

*Kategori: Adresser*,  *Poäng: 100*

Domänkontrollanten hanterar Windows Active Directorys biljettsystem Kerberos. Filtrerar vi på detta kan vi luska ut att det troligtvis rör sig om adressen 192.168.177.129.

<img width="1389" alt="SCR-20250322-nwdy" src="https://github.com/user-attachments/assets/66707ce8-e958-4be9-91e5-5ce1bf4acdaf" />

filtrerar vi på adressen kan vi dessutom bekräfta detta ännu mer med namnet "DC-01 GENTLEDENT"

<img width="1384" alt="SCR-20250322-nwjf" src="https://github.com/user-attachments/assets/f4e68672-52e0-49f7-999b-43eebc9a3016" />

`Svar: 192.168.177.129`

---

### ***Angriparens hostname***

Fråga: Vad var angriparens hostname?

*Kategori: Adresser*,  *Poäng: 100*

Vi vet att angriparen är 192.168.177.141. filtrerar vi på dhcp och 192.168.177.141 får vi följande:

<img width="1389" alt="SCR-20250322-nzhi" src="https://github.com/user-attachments/assets/1704e5c3-a8c9-4e35-aa37-9112e442c17e" />

Klickar vi på request-paketet kan vi se hostname för 192.168.177.141:

<img width="970" alt="SCR-20250322-nzls" src="https://github.com/user-attachments/assets/fa294948-b65d-404b-a80e-345ca57329e9" />

`Svar: kali`

---

### ***Tidszon***

Fråga: Vilken lokal tid (svensk tid) anlände sista paketet i inspelningen av nätverkstrafiken? Avrunda neråt till hel sekund!

*Kategori: Utredning av IT-attacken*,  *Poäng: 100*

Har man wireshark inställt på svensk tid (dvs. CET) behöver man inte fundera så mycket, men har man inte det kan man också klicka på sista paketet och då ser vi tiden svenskt tid (CET). Avrundar vi neråt till helsekund får vi svaret:

<img width="1326" alt="SCR-20250322-obiy" src="https://github.com/user-attachments/assets/1aef3bc4-071f-4448-9401-5449a53a039f" />

`Svar: 09:42:01`

---

### ***Initial teknik***

Fråga: Vad var den absolut första tekniken ur MITRE ATT&CK som angriparen använde sig av på nätverket? Svara med ID-numret.

*Kategori: MITRE ATT&CK*,  *Poäng: 100*

Vi ser att det första gör angriparen (192.168.177.141) gör på nätverket är att köra massa arp-request. Udda trafik som troligtvis är för att få en bild av vilka uppkopplade enheter som finns INTERNT på nätverket. En teknik som kallas "Remote System Discovery" ur taktiken Discovery. INTE att förväxla med tekniken "active scanning" som är en del av taktiken Reconnaissance vilket det inte rör sig om här då vi är inne internt på nätverket och redan har etableras access.

<img width="1390" alt="SCR-20250322-odlu" src="https://github.com/user-attachments/assets/f1cf173c-4127-4647-a5cc-4d104f729f15" />

Sidan för MITRE ATT&CK-tekniken:

<img width="1486" alt="SCR-20250322-ofco" src="https://github.com/user-attachments/assets/31ea1e2e-7a05-441d-bb46-2f2f3f23002a" />

`Svar: T1018`

---

### ***Utökad teknik***

Fråga: Omedelbart (cirka 5-6 sekunder) efter tekniken som användes i utmaningen Initial teknik använder angriparen en annan teknik ur MITRE ATT&CK. Vilken teknik var det?

*Kategori: MITRE ATT&CK*,  *Poäng: 100*

Vi ser att omedelbart några sekunder efter första tekniken försöker angriparen (192.168.177.141) att etablera massa tcp-anslutningar till de ip-adresser som har svarat på arp-requesten (bland annnat filservern och domänkontrollanten) på massa olika portar. En så kallad "portskanning" vilket är tekniken "Network Service Discovery" ur MITRE ATT&CK.

<img width="1390" alt="SCR-20250322-oivr" src="https://github.com/user-attachments/assets/a4485f28-4f2d-47fd-b001-b805798713f7" />

Sidan för MITRE ATT&CK-tekniken:

<img width="1480" alt="SCR-20250322-ojfd" src="https://github.com/user-attachments/assets/eaf5dfdf-4dbb-4371-a0c9-2db7b700938f" />

`Svar: T1046`

---

### ***Resultat av skanning***

Fråga: Hur många öppna portar hittade angriparen totalt på Gentle Dentals nätverk med sin skanning från utmaningen Utökad teknik?

*Kategori: Utredning av IT-attacken*,  *Poäng: 100*

Genom att filtrera på angriparens ip-adress och de lyckade anslutningar (syn,ack) kan vi se de portarna som var öppna. Till exempel port 88 (kerberos), 80 (http) osv.

Vi ser att de ip-adresser som hade öppna portar va 192.168.177.129, 192.168.177.138, 192.168.177.139 och 192.168.177.155.

<img width="1391" alt="SCR-20250322-taak" src="https://github.com/user-attachments/assets/12be263e-615f-4d3b-8533-64e793046fcb" />

Filtrerar vi sedan på de fyra ip-adresser genom att lägga till "ip.src" på filret för respektive ip-adress kan vi se vilka öppna portar som angriparen hittade på respektive ip. Summerar vi dessa och tar bort dubbletter får vi antalet totalt öppna portar som hittades. 10 + 5 + 3 + 3 = 21

`Svar: 21`

---

### ***Autentiseringsuppgifter 1.0***

Fråga: Angriparen lyckades få tag i autentiseringsuppgifter. Vilken subteknik ur MITRE ATT&CK användes? Svara med ID-numret för subtekniken.

*Kategori: MITRE ATT&CK*,  *Poäng: 100*

Vi har redan noterat att kerberos finns i trafiken. I trafiken efter skanningen ser vi att angriparen ber domänkontrollanten om en kerberos-ticket. 

<img width="1387" alt="SCR-20250322-onpf" src="https://github.com/user-attachments/assets/c86aad6c-a60a-43dd-b398-77688ac97ebe" />

<img width="1390" alt="SCR-20250322-onut" src="https://github.com/user-attachments/assets/85bc45c8-ea0d-4cff-8889-b5381de915ef" />

Det rör sig om "AS-REP Roasting". Om "Pre-authentication" är avstängt kan angripare be domänkontrollanten om "tickets" till servicekonton utan att behöva autentisera sig. En ticket de sedan kan försöka knäcka offline för att få tag i lösenordet. Om det lösenordet dessutom är svagt, har man problem.

Sidan för MITRE ATT&CK-subtekniken:

![SCR-20250322-oqny](https://github.com/user-attachments/assets/3ba6fad6-537e-4bef-9ca4-d50edb59218f)

`Svar: T1558.004`

---

### ***Autentiseringsuppgifter 2.0***

Fråga: I vilket paket i nätverkstrafiken fick angriparen tag i autentiseringsuppgifterna från utmaningen "Autentiseringsuppgifter 1.0"?

*Kategori: Utredning av IT-attacken*,  *Poäng: 100*

Man kan se att autentiseringsuppgifterna (ticket) skickas krypterat i en "AS-REP".

<img width="1377" alt="SCR-20250322-orkn" src="https://github.com/user-attachments/assets/ca6d0358-5c2a-4fbd-a7dc-85ac6e7a364b" />

`Svar: 2387`

---

### ***Lösenord***

Fråga: Vilket lösenord använde angriparen för att logga in i utmaningen Obehörig inloggning

*Kategori: Utredning av IT-attacken*,  *Poäng: 100*

Vi kan dumpa ut hashar med plugin "windows.hashdump" i volatility3 på minnesdumpen. Genom detta får vi ut nt-hashen för admin-kontot dvs "FTPService" som är: 1be448211e59b6428d01d6fe9dfd8f91

<img width="982" alt="SCR-20250322-oytd" src="https://github.com/user-attachments/assets/60b24f37-8837-4088-901f-9b89d386d0fc" />

Vi kan använda verktyg som hashcat eller john för knäcka hashen men vi kan också testa att stoppa in den i Crackstation för att se om det är ett tidigare läckt lösenord, vilket det är:

<img width="1420" alt="SCR-20250322-oumg" src="https://github.com/user-attachments/assets/df88c20c-0e8f-4c58-8e1e-b19ea68b72e1" />

`Svar: DentalSurgery528`

---

### ***Obehörig inloggning***

Fråga: Angriparen lyckades logga in på en av datorerna på något sätt. Vilket Logon ID tillhör den obehöriga inloggningen?

*Kategori: Utredning av IT-attacken*,  *Poäng: 100*

Inloggningar kan vi se i loggarna, i Security.evtx-loggen. Det ger event-ID: 4624

Här finns det betydligt bättre verktyg att analysera loggar om man har Windows. Jag har Mac så jag kör python-evtx och kollar på loggarna i XML-format. Här får vi tänka på tidsformatet. Loggarna är i UTC och inte CET alltså då en timme tidigare så det måste vi justera och förstå att det ska läggas till en timme för att justera till svensk tid. Den första loggen som dyker upp är en inloggning från angriparen (192.168.177.141) ca 09:24:59 (Svensk tid) då med logon-ID 0x00000000000ffcab, alltså 0xffcab.

<img width="672" alt="bild4" src="https://github.com/user-attachments/assets/bf9cdf5a-5401-421b-96df-b3cf1595a0ba" />

Vi kan bekräfta detta också genom att jämföra tiden med inloggningen som syns i nätverkstrafiken:

<img width="1373" alt="SCR-20250322-nlvv" src="https://github.com/user-attachments/assets/309e1b93-d7ff-4ab0-866a-d98a52172ef6" />

Här ifrån vet vi nu att angriparen har lyckats ta sig in på filservern och är nu inloggad på den datorn.

`Svar: 0xffcab eller 0x00000000000ffcab`

---

### ***Fortsatt åtkomst***

Fråga: Persistence är en taktik som angripare ofta använder för att försöka bevara sin åtkomst. Vilken första persistence subteknik ur MITRE ATT&CK använde sig angriparen av?

*Kategori: MITRE ATT&CK*,  *Poäng: 100*

Angriparen vill bevara sin åtkomst på något sätt vilket tyder på att han är inloggad på en annan dator (filserverns dator). Vi vet att angriparen loggade in på FTP-servern (192.168.177.155) från 192.168.177.141 ungefär kl 09:24:59 (svensk tid). 

Följer vi loggarna kort efter inloggningen ser vi att 40 sekunder senare 09:25:40 (svensk tid) skapas ett nytt konto som heter svcUpdate genom Event ID 4720. Ett vanligt sätt för angripare att snabbt försöka etablera framtida åtkomst är att skapa konton, här med ett namn som kanske smälter in. Här gäller det att vara lite noga med detaljerna. Det vi ser är att domänen som kontot skapas på är FTPSERVICE, vilket kanske inte höjer ögonbryn om man inte tänker på att den riktiga domänen är gentledental. Detta betyder att svcUpdate är ett lokalt skapat konto på datorn och inte ett konto via domänen för företaget. Söker vi på "MITRE local account persistence" hittar vi T1136.001 som beskriver detta mycket väl. 

<img width="558" alt="localaccount" src="https://github.com/user-attachments/assets/dabb333c-ce55-42f2-80f7-80b91ec02a6b" />

Man kan läsa om detta på Microsofts hemsida: https://learn.microsoft.com/en-us/previous-versions/windows/it-pro/windows-10/security/threat-protection/auditing/event-4720

Sidan på MITRE ATT&CK för subtekniken:

<img width="1261" alt="SCR-20250323-qlld" src="https://github.com/user-attachments/assets/40b5f385-3a37-4706-81f9-ae50655ec47e" />

`Svar: T1136.001`

---

### ***Windows Defender***

Fråga: Vilken MpPreference parameter ändrade angriparen för Windows Defender Antivirus?

*Kategori: Utredning av IT-attacken*,  *Poäng: 100*

Vi vet att det rör Windows Defender då kan vi kolla på loggarna för Windows Defender. 09:26:22 (svensk tid) ändrar angriparen så att Windows Defender Antivirus inte scannar sökvägarna C:\Windows\System32\Temp och C:\Windows. Antagligen för att undvika att eventuella skadliga filer som är tänkta att placeras där scannas.

<img width="852" alt="excludepath1" src="https://github.com/user-attachments/assets/de2a2a92-0521-4f71-b2e2-37b6cc03c5e2" />

<img width="837" alt="excludepath2" src="https://github.com/user-attachments/assets/5c4a440d-b273-41c9-abe4-17f49df92a32" />

MpPreference parametern blir då "ExclusionPath".

Här är ett bra blogginlägg som beskriver denna vanliga teknik för AV-evasion:

https://www.huntress.com/blog/you-can-run-but-you-cant-hide-defender-exclusions

`Svar: ExclusionPath`

---

### ***Nedladdning 1.0***

Fråga: Angriparen lyckades ladda ner filer. Hur många paket innehöll den fullständiga strömmen där angriparen laddade ner filer?

*Kategori: Utredning av IT-attacken*,  *Poäng: 100*

### ***Nedladdning 2.0***

Fråga: Vad hette den binära filen som angriparen laddade ner? Svara med namn och filändelse.

*Kategori: Utredning av IT-attacken*,  *Poäng: 100*

Svar för både Nedladdning 1.0 och Nedladdning 2.0 då de hänger lite ihop.

IP-addressen 192.168.177.141 är en mini-dator med kali som vi listade ut i "Angriparens hostname". På en Kali-dator har man massa verktyg men nu när angriparen är inloggad på FTP-servern (192.168.177.155) kan man tänka sig att han vill ladda ner filer, verktyg och annat för att kunna utföra attacker.
 
Vi hittar ingen http trafik i nätverkstrafiken så antingen har angriparen en server med "fejkad" https eller så laddar angriparen ner från internet från någon domän.

filtrerar vi på dns för att se domäner i trafiken får vi upp lite olika bland annat microsoft-update domäner, kanske inte jätteintressant. MEN en domän som är intressant är github (api.github.com och raw.githubusercontent.com). Angripare använder ibland Github för "Malware hosting". 

Vi får fram två tcp-strömmar med github genom filtret "frame contains github"

<img width="1388" alt="SCR-20250323-mtct" src="https://github.com/user-attachments/assets/defbd94b-2a8b-4664-947f-bec11ae1b8ed" />

Första strömmen "1997" med api.github.com innehåller trafik för api-interaktionen:

<img width="1388" alt="SCR-20250323-mtjn" src="https://github.com/user-attachments/assets/61ddd6ce-c091-4173-ab7e-bbc2aea7cf24" />

medan strömmen "1998" verkar innehålla en stor mängd data som laddas ner, strömmen innehåller 100 paket:

<img width="1387" alt="SCR-20250323-molq" src="https://github.com/user-attachments/assets/ef04dab1-9305-464a-b368-68ba8ff1fca3" />

<img width="132" alt="SCR-20250323-mopm" src="https://github.com/user-attachments/assets/fa9033cc-2c62-45bd-8d81-e5743d2266e4" />

Vi vet nu att angriparen laddar ner filer från "raw.githubusercontent.com".

Det finns en fil som loggar powershell som hetter "ConsoleHost_history.txt" så den kan vi försöka carva ut ur minnet med volatility3. Men den verkar tyvärr inte logga eller så är den överskriven..

<img width="1406" alt="SCR-20250322-ppmo" src="https://github.com/user-attachments/assets/ba8f4265-1fa9-4748-9760-303af61201d1" />

Om vi däremot kör `strings` på minnesdumpen eller öppnar den i en hex-läsare och söker efter texten "raw.githubusercontent.com" eller "api.github.com" kan vi hitta följande:

<img width="1494" alt="SCR-20250323-myyq" src="https://github.com/user-attachments/assets/c0dff97c-45e1-407c-8f46-303cb35c7609" />

Det laddas ner en fil från en privat Github-repo med en api-token. Filen verkar heta cscapi.dll och är 93959 bytes. Vilket verkar stämma ganska väl överens med datamängden som vi såg tidigare i tcp-strömmen "1998". Kollar vi upp Githubsidan får vi detta:

<img width="1246" alt="SCR-20250322-pqkv" src="https://github.com/user-attachments/assets/baa8a033-28e0-4bc6-b1fa-5f2456dacfdc" />


`Svar Nedladdning 1.0: 100`
`Svar Nedladdning 2.0: cscapi.dll`

---

### ***Utnyttjande 1.0***

Fråga: Vilken subteknik ur MITRE ATT&CK användes av angriparen för att utnyttja den binära filen som laddades ner i utmaningen Nedladdning 2.0? Svara med ID-numret

*Kategori: MITRE ATT&CK*,  *Poäng: 100*

### ***Utnyttjande 2.0***

Fråga: Vilket process-id hade den process som utnyttjade angriparens binära fil från utmaningen Untyttjande 1.0?

Format för svaret: 1234

*Kategori: Utredning av IT-attacken*,  *Poäng: 100*

### ***Registerändring***

Fråga: Det finns ett Registernyckelvärde som angriparen ändrade till "0" för att kunna UTNYTTJA den nedladdade binära filen. Vad var namnet? Svara med namnet, exkludera sökvägen.

*Kategori: Utredning av IT-attacken*,  *Poäng: 100*

Här kommer svar för Utnyttjande 1.0, Utnyttjande 2.0 och Registerändring!

Dessa tre utmaningar hör lite ihop.

Vi vet nu att den binära filen är "cscapi.dll", det är en vanlig legitim dll i Windows som är sårbar att kapa för DLL Hijacking bland annat genom explorer.exe. En snabb googling visar detta:

<img width="1318" alt="SCR-20250322-prrh" src="https://github.com/user-attachments/assets/502b5554-ff09-4fe0-9dab-aff4597d56ba" />

Använder vi volatility3 med plugin: windows.dlllist och använder `grep` för cscapi.dll kan vi se vilka dll-filer med det namnet som har laddats in och av vilken process. Vi ser här att några processer laddar in den legitima C:\Windows\System32\cscapi.dll runt 09:19 (svensk tid). Men explorer.exe laddar in C:\Windows\cscapi.dll vid 09:43:01 (svensk tid). Process-id för exlorer.exe är: 4112. 

<img width="1399" alt="SCR-20250322-psus" src="https://github.com/user-attachments/assets/34ad439c-fdf3-41ff-b3ed-bcda2fc96c62" />


Vi vet ju att angriparen sedan tidigare har exluderat Windows Defender Antivirus att scanna C:\Windows.

Troligtvis har angriparen då placerat sin egna skadliga cscapi.dll här genom att ladda ner den till eller flyttat den till C:\Windows för att få explorer.exe att ladda in angriparens dll-fil istället för den legitima. 

Windows-processer laddar in dll-filer från C:\Windows\System32 därefter i sökvägen som processen startas. Det reglereras av registernycklen: "SafeDllSearchMode".

<img width="1412" alt="SCR-20250323-rlkd" src="https://github.com/user-attachments/assets/9a4987b5-0af0-4e34-b094-56c725abc1eb" />


explorer.exe är en av få processer på Windows som körs genom C:\Windows. 

Detta innebär om angriparen placerar in sin "cscapi.dll" och sätter värdet av "SafeDllSearchMode" till 0 kommer explorer.exe ladda in C:\Windows\cscapi.dll och därmed angriparens fil när processen startas. Explorer startas automatiskt för de flesta datorer vid uppstart och är en process som såklart körs väldigt ofta vilket innebär att det är en smart process för angripare att utnyttja för Persistence.

Ändringen av registernyckeln syns också om man söker efter "SafeDllSearchMode" i minnesdumpen och går att i princip bekräfta på så vis:

<img width="1443" alt="SCR-20250323-rnvu" src="https://github.com/user-attachments/assets/340a7bad-5253-4836-9104-f1aac3964ac1" />

Denna teknik kallas "DLL Search Order Hijacking". ID-nummer för MITRE ATT&CK-subtekniken: T1574.001

<img width="1428" alt="SCR-20250323-nwyi" src="https://github.com/user-attachments/assets/cd0c4300-f433-45f7-86ae-e7a0bb54a18b" />

`Svar Utnyttjande 1.0: T1574.001`

`Svar Utnyttjande 2.0: 4112`

`Svar Registerändring: SafeDllSearchMode`

---

### ***Binära filens meddelande***

Fråga: Den binära filen i utmaningen Nedladdning 2.0 innehöll ett meddelande. Återge detta meddelande

*Kategori: Utredning av IT-attacken*,  *Poäng: 100*

Vi kan carva ut den binära filen "cscapi.dll" med volatility3 och analysera den med reversing-verktyg eller köra den i virtuell Windowsmiljö med rundll32.exe om vi vill. Jag har Mac så jag kör statisk analys med radare2 för enkelhetens skull.

Jag körde här plugins: "windows.filescan" (för att hitta den virtuella adressen) och "windows.dumpfiles" (för att dumpa ut filen). 

Sen analyserar jag den med radare2 och ser att den ger ett notifikations-fönster med en textsträng som är tänkt att "simulera" en bakdörr:

<img width="1398" alt="SCR-20250322-psey" src="https://github.com/user-attachments/assets/e39d7f54-0536-4558-873a-72e3acf9deee" />

<img width="1385" alt="SCR-20250322-psmd" src="https://github.com/user-attachments/assets/d9d64b09-8a79-4c7e-8809-d12d4c0bf5d5" />

f0r_3th1c4l_purp0535_th15_d03s_n0t_c0nt41n_4n_3ncrypt3d_p4yl04d_f0r_4_b4ckd00r

`Svar: f0r_3th1c4l_purp0535_th15_d03s_n0t_c0nt41n_4n_3ncrypt3d_p4yl04d_f0r_4_b4ckd00r`

---

### ***Angriparens server***

Fråga: Vilken IPv4-adress hade angriparens egna server?

*Kategori: Utredning av IT-attacken*,  *Poäng: 200*

### ***Dataläckan***

Fråga: Angriparen misstänkts ha stulit känslig information om en patient. Vad heter denna personen? Svara med för-och efternamn.

*Kategori: Utredning av IT-attacken*,  *Poäng: 500*

Vi kan se massa dns-trafik till ip-adressen "10.245.122.37" i nätverkstrafiken som indikerar "DNS tunneling". En teknik som angripare ofta använder sig av för att "maskerat" skicka data i query-fältet, för till exempel exfiltration av data eller för kommunikation med en C2-server. I detta fallet ser vi långa strängar med vad som ser ut som hex data med domänen facebook.com. Till exempel: 0.383d06170e7c392808262a3d0f223c2520111352.facebook.com

<img width="1375" alt="SCR-20250322-ptpl" src="https://github.com/user-attachments/assets/e1688689-ffcd-4bd5-8792-53470c76bcf1" />

Stoppar vi in datan i Cyberchef kan vi misstänka att datan är krypterad:

![SCR-20250323-bbif](https://github.com/user-attachments/assets/7b81e732-6629-48a6-ac4e-69058fe514d6)

Vi kan försöka brute-forcea olika typer av vanliga krypteringsmetoder, men vi kan också försöka luska i minnesdumpen och se om vi kan hitta något där.

Vi har tidigare försökt få ut filen "ConsoleHost_history.txt" för att se powershell-kommandon men den filen verkar inte logga så det går inte..

Men om vi istället bara kör `strings` och använder `grep` för "facebook.com" får vi upp lite intressanta grejer: 

<img width="1404" alt="SCR-20250323-bgwl" src="https://github.com/user-attachments/assets/13cfacaa-2fc4-4dd3-962b-196740fb106f" />

Öppnar vi dessutom minnesdumpen i en hex-läsare och söker på texten "facebook.com" så kan vi få fram ett script som kör någon slags XOR-kryptering på filen `gd_patient_04.rtf`.

![SCR-20250323-biby](https://github.com/user-attachments/assets/17418c93-b222-44d4-8822-e3dc2f74e229)

![SCR-20250323-bieg](https://github.com/user-attachments/assets/0e856ea1-739c-4093-b856-606aa42b0332)

Vi kan lista ut att scriptet gör följande:

1. Läser en fil (`C:\Shared\Patients\gd_patient_04.rtf`).
2. XOR-krypterar filens data med en nyckel (`$k` som är `CatchMeIfUCanLOL` i bytes).
3. Omvandlar krypterad data till en hex-sträng.
4. Skapar subdomäner baserade på hex-strängen och facebook.com.
5. Utför DNS-uppslag på varje subdomän för att exfiltrera data.

Vi försöker carva ut filen `C:\Shared\Patients\gd_patient_04.rtf` men den verkar vara överskriven..

<img width="1406" alt="SCR-20250322-ppmo" src="https://github.com/user-attachments/assets/cd95efcb-c356-4a2c-b1cd-691a827cf0b9" />

Då får vi helt enkelt återskapa den.

Vi börjar med att få ut alla querys som har "facebook.com" i sig då det är de som är intressanta. Vi kan använda `tshark` till det:

![SCR-20250322-pvjp](https://github.com/user-attachments/assets/852546a7-fead-4a1e-99ac-fb8526bb800d)

Vi sparar detta till en fil `dnsdata.txt`.

Samma data verkar skickas flera gånger så vi filtrerar ut unik hexdata och sätter ihop det till en lång sträng med följande Python-script:

![SCR-20250323-brta](https://github.com/user-attachments/assets/70167d2c-2eef-4df7-b62f-65365ed6628f)

<img width="1406" alt="SCR-20250322-pwme" src="https://github.com/user-attachments/assets/a2ad6bf6-588d-479f-9526-bf303b80e331" />

Nu kan vi då försöka dekryptera hexdatan med den hårdkodade nyckeln som vi hittade (`CatchMeIfUCanLOL`) i bytes med följande Python-script:

![SCR-20250322-pxgb](https://github.com/user-attachments/assets/3f3daf1c-b411-411c-9475-5149f01e492c)

<img width="1408" alt="SCR-20250322-pxkw" src="https://github.com/user-attachments/assets/56df6b87-8e1a-4d49-8433-6fc932695fc6" />

Om vi sedan lägger in den hex-datan i en fil har vi återskapat filen och kan se namnet på den patient vars känsliga data läcktes:

![SCR-20250322-pxvy](https://github.com/user-attachments/assets/c400ed47-d084-4a61-8c96-0ccde2ea0fe9)

`Svar Angriparens server: 10.245.122.37`
`Svar Dataläckan: Håkan Kerberosqvist`

---

### ***Fobi***

Fråga: Vilket djur fruktar personen från utmaningen Dataläckan? Svara i plural.

*Kategori: Utredning av IT-attacken*,  *Poäng: 500*

Tittar vi i patientjournalerna för Håkan Kerberosqvist ser vi att han lider av Anitidaefobi. En fobi som innebär att ankor stirrar på en.

![SCR-20250322-pxvy](https://github.com/user-attachments/assets/ffba74e4-3892-43b1-bfbb-402750414f79)

![SCR-20250322-pybi](https://github.com/user-attachments/assets/6762b54d-b77b-459c-adc9-77084fde04e7)

`Svar: Ankor`

---

### ***Ransomware 1.0 - Nyckeln***

*Kategori: Utpressning*,  *Poäng: 500*

Denna uppgiften var lite misslyckad då vi i efterhand insåg att det "primtalet" som vi valde visade sig inte vara ett primtal då det är delbart med t.ex 3, oops.. Vilket gör att krypteringen går att knäcka ganska enkelt och det finns flera möjliga privata nycklar.

Men tanken var iallafall att man skulle lösa uppgiften enligt följande logik:

Vi har fått en fil med en publik nyckel: 506395958. Tittar vi på den krypterade datan ser vi par med nummer. Kollar vi på Wikipedia över asymmetrisk kryptering kan vi hitta ElGamal-kryptering vilket stämmer väl överenes med vår chiffertext i formatet (c1, c2).

<img width="793" alt="SCR-20250322-qins" src="https://github.com/user-attachments/assets/7ab8896c-d721-4ef1-96c4-dacd5cb80501" />

<img width="1207" alt="SCR-20250323-kzwu" src="https://github.com/user-attachments/assets/2109c6ba-f05b-41be-9c2d-f78dcfc138f0" />

<img width="749" alt="SCR-20250323-lacg" src="https://github.com/user-attachments/assets/fc7f30df-8928-4eb7-8df0-09307507df92" />

ElGamal är en asymmetrisk krypteringsalgoritm som använder en publik och en privat nyckel. ElGamal genererar nycklar genom det diskreta logaritmproblemet. En säker krypteringsalgoritm som gör att angriparen inte kan beräkna den privata nyckeln OM allt är gjort på rätt sätt. 

Ett problem är om primtalet är för litet, I detta fallet kan vi se i Ghidra i funktionen "eg_enc" att det är alldeles för litet, endast 30-bit. Då kan vi räkna ut den privata nyckeln genom en matematisk attack. Då vi vet generatorn "2", Primtalet "1073741847" och den publika nyckeln "506395958"

<img width="1273" alt="SCR-20250322-qiul" src="https://github.com/user-attachments/assets/46aac0a6-77d8-4a87-ac4b-9a196c509c3b" />

<img width="793" alt="SCR-20250323-krbj" src="https://github.com/user-attachments/assets/4099a397-442b-4949-80de-4b135e520d36" />

Detta tar cirka 5-15 minuter beroende på datorns prestanda:

<img width="575" alt="SCR-20250323-kvvg" src="https://github.com/user-attachments/assets/8a5f828e-c7ac-4517-8431-51f4e752c3e6" />

`Svar: 177370085 eller 713906975`

---


### ***Ransomware 2.0 - Textsträngen***

*Kategori: Utpressning*,  *Poäng: 500*

Med den privata nyckeln som vi hittat: 177370085 kan vi sedan knäcka krypteringen och få ut den känsliga datan med följande Python-script:

<img width="1218" alt="SCR-20250322-rhsb" src="https://github.com/user-attachments/assets/1656ec0e-d249-47f8-aa91-afa2925a8ace" />

<img width="806" alt="SCR-20250322-rjss" src="https://github.com/user-attachments/assets/289983ce-a6bf-4a7f-a6cb-e6aa17f3e174" />

`Svar: Pelle_Svanslos_Har_Svans`

---
