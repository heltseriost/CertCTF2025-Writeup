# CertCTF2025-Writeup
Samtliga lösningar för CertCTF2025

Följer tidslinjen i attacken.

### ***Angriparens IPv4-adress***

*Kategori: Adresser*,  *Poäng: 100*

Vi vet från "scenario.txt" att angriparen har kopplat in en mini-dator på nätverket där skrivaren egentligen sitter, möjligtvis på samma subnät.

Genom att titta på "IPv4 statistics" ser vi att ip-adresser på subnätet "192.168.177.0/24" står för den absoluta majoriteten av trafiken:

<img width="1501" alt="SCR-20250322-noff" src="https://github.com/user-attachments/assets/ca3684ac-77c8-464d-8f36-8bbdf3ca8941" />

Tittar vi i trafiken ser vi att det sker massa trafik udda trafik från "192.168.177.141" bland annat massa arp-request, misslyckade tcp-anslutningar och sen en uppkoppling mot 192.168.177.155 på port 4444. Port 4444 är en klassisk "lyssnarport" "Metasploit framework" och Meterpreter.

Vi kan då räkna ut ganska snabbt att ip-adressen som angriparen använde sig av initialt var "192.168.177.141"

`Svar: 192.168.177.141`.

---

### ***FTP-serverns IPv4-adress***

*Kategori: Adresser*,  *Poäng: 100*

Vi vet ju från "scenario.txt" att filservern är drabbad, i och med anslutningen från 192.168.177.141 till 192.168.177.155 kan vi ganska snabbt lista ut att det är filservern.

Dessutom använder ftp port 21. Filtrerar vi på lyckade anslutningar på port 21 ser vi endast en adress: "192.168.177.155" vilket innebär att den porten bara är öppen på den ip-adressen. Ännu en indikation på att det är filservern.

<img width="1384" alt="SCR-20250322-ntzk" src="https://github.com/user-attachments/assets/1089ea19-ebfc-4181-a086-b7a28f5f86da" />

`Svar: 192.168.177.155`.

---

### ***Domänkontrollantens IPv4-adress***

*Kategori: Adresser*,  *Poäng: 100*

Domänkontrollanten hanterar Windows Active Directorys biljettsystem Kerberos. Filtrerar vi på detta kan vi luska ut att det troligtvis rör sig om adressen 192.168.177.129.

<img width="1389" alt="SCR-20250322-nwdy" src="https://github.com/user-attachments/assets/66707ce8-e958-4be9-91e5-5ce1bf4acdaf" />

filtrerar vi på adressen kan vi dessutom bekräfta detta ännu mer med namnet "DC-01 GENTLEDENT"

<img width="1384" alt="SCR-20250322-nwjf" src="https://github.com/user-attachments/assets/f4e68672-52e0-49f7-999b-43eebc9a3016" />

`Svar: 192.168.177.129`.

---

### ***Angriparens hostname***

*Kategori: Adresser*,  *Poäng: 100*

Vi vet att angriparen är 192.168.177.141. filtrerar vi på dhcp och 192.168.177.141 får vi följande:

<img width="1389" alt="SCR-20250322-nzhi" src="https://github.com/user-attachments/assets/1704e5c3-a8c9-4e35-aa37-9112e442c17e" />

Klickar vi på request-paketet kan vi se hostname  för 192.168.177.141:

<img width="970" alt="SCR-20250322-nzls" src="https://github.com/user-attachments/assets/fa294948-b65d-404b-a80e-345ca57329e9" />

`Svar: kali`.

---

### ***Tidszon***

*Kategori: Utredning av IT-attacken*,  *Poäng: 100*

Har man wireshark inställt på svensk tid (dvs. UTC) behöver man inte fundera så mycket, men har man inte det kan man också klicka på sista paketet och då ser vi tiden svenskt tid (UTC). Avrundar vi neråt till helsekund får vi svaret:

<img width="1326" alt="SCR-20250322-obiy" src="https://github.com/user-attachments/assets/1aef3bc4-071f-4448-9401-5449a53a039f" />

`Svar: 09:42:01`.

---

### ***Initial teknik***

*Kategori: MITRE ATT&CK*,  *Poäng: 100*

Vi ser att det första gör angriparen (192.168.177.141) gör på nätverket är att köra massa arp-request. Udda trafik som troligtvis är för att få en bild av vilka uppkopplade enheter som finns INTERNT på nätverket. En teknik som kallas "Remote System Discovery" ur taktiken Discovery. INTE att förväxla med tekniken "active scanning" som är en del av taktiken Reconnaiscance vilket det inte rör sig om här då vi är inne internt på nätverket.

<img width="1390" alt="SCR-20250322-odlu" src="https://github.com/user-attachments/assets/f1cf173c-4127-4647-a5cc-4d104f729f15" />

Sidan för MITRE ATT&CK-tekniken:

<img width="1486" alt="SCR-20250322-ofco" src="https://github.com/user-attachments/assets/31ea1e2e-7a05-441d-bb46-2f2f3f23002a" />

`Svar: T1018`.

---

### ***Utökad teknik***

*Kategori: MITRE ATT&CK*,  *Poäng: 100*

Vi ser att omedelbart några sekunder efter första tekniken försöker angriparen (192.168.177.141) att etablera massa tcp-anslutningar till de ip-adresser som har svarat på arp-requesten (bland annnat filservern och domänkontrollanten) på massa olika portar. En så kallad "portskanning" vilket är tekniken "Network Service Discovery" ur MITRE ATTACK.

<img width="1390" alt="SCR-20250322-oivr" src="https://github.com/user-attachments/assets/a4485f28-4f2d-47fd-b001-b805798713f7" />

Sidan för MITRE ATT&CK-tekniken:

<img width="1480" alt="SCR-20250322-ojfd" src="https://github.com/user-attachments/assets/eaf5dfdf-4dbb-4371-a0c9-2db7b700938f" />

`Svar: T1046`.

---

### ***Resultat av skanning***

*Kategori: Utredning av IT-attacken*,  *Poäng: 100*

Genom att filtrera på angriparens ip-adress och de lyckade anslutningar (syn,ack) kan vi se de portarna som var öppna. Till exempel port 88 (kerberos), 80 (http) osv.

Vi ser att de ip-addresser som hade öppna portar va 192.168.177.129, 192.168.177.138, 192.168.177.139 och 192.168.177.155. Filtrerar vi sedan på de fyra ip-addressen med "ip.src" kan vi se vilka öppna portar som angriparen hittade på varje ip. Summerar vi dessa och tar bort dubbletter får vi antalet totalt öppna portar som hittades. 10 + 5 + 3 + 3 = 21

<img width="1390" alt="SCR-20250322-oivr" src="https://github.com/user-attachments/assets/a4485f28-4f2d-47fd-b001-b805798713f7" />

<img width="1480" alt="SCR-20250322-ojfd" src="https://github.com/user-attachments/assets/eaf5dfdf-4dbb-4371-a0c9-2db7b700938f" />

`Svar: 21`.

---

### ***Autentiseringsuppgifter 1.0***

*Kategori: MITRE ATT&CK*,  *Poäng: 100*

Vi har redan noterat att kerberos finns i trafiken. I trafiken efter skanningen ser vi att angriparen ber domänkontrollanten om en kerberos-ticket. 

<img width="1387" alt="SCR-20250322-onpf" src="https://github.com/user-attachments/assets/c86aad6c-a60a-43dd-b398-77688ac97ebe" />

<img width="1390" alt="SCR-20250322-onut" src="https://github.com/user-attachments/assets/85bc45c8-ea0d-4cff-8889-b5381de915ef" />

Det rör sig om "AS-REP Roasting". Om "Pre-authentication" är avstängt kan angripare be domänkontrollanten om "tickets" till servicekonton utan att behöva autentisera sig. En ticket de sedan kan försöka knäcka offline för att få tag i lösenordet. Om det lösenordet dessutom är svagt, har man problem.

Sidan för MITRE ATT&CK-subtekniken:

![SCR-20250322-oqny](https://github.com/user-attachments/assets/3ba6fad6-537e-4bef-9ca4-d50edb59218f)

`Svar: T1558.004`.

---

### ***Autentiseringsuppgifter 2.0***

*Kategori: Utredning av IT-attacken*,  *Poäng: 100*

Man kan se att autentiseringsuppgifterna (ticket) skickas krypterat i en "AS-REP". Denna kan vi dessutom plocka ut med olika tekniker för att knäckas offline. 

<img width="1377" alt="SCR-20250322-orkn" src="https://github.com/user-attachments/assets/ca6d0358-5c2a-4fbd-a7dc-85ac6e7a364b" />

`Svar: 2387`.

---

### ***Lösenord***

*Kategori: Utredning av IT-attacken*,  *Poäng: 100*

Vi kan plocka ut kerberosbiljetten och knäcka den med till exempel verktyg som hashcat eller john. Men vi har ju minnesdumpen så då kan vi göra det den snabba vägen. Vi kan dumpa ut hashar med en plugin: "windows.hashdump" i volatility3. Genom att får vi ut nt-hashen för admin-kontot dvs "FTPService" som är: 1be448211e59b6428d01d6fe9dfd8f91

<img width="982" alt="SCR-20250322-oytd" src="https://github.com/user-attachments/assets/60b24f37-8837-4088-901f-9b89d386d0fc" />

Vi kan testa stoppa hashen i Crackstation för att se om det är ett tidigare läckt lösenord, vilket det är! Funkar även att knäcka den med hashcat.

<img width="1420" alt="SCR-20250322-oumg" src="https://github.com/user-attachments/assets/df88c20c-0e8f-4c58-8e1e-b19ea68b72e1" />

`Svar: DentalSurgery528`.


---

### ***Lösenord***

*Kategori: Utredning av IT-attacken*,  *Poäng: 100*

Vi kan dumpa ut hashar med plugin "windows.hashdump" i volatility3 på minnesdumpen. Genom att får vi ut nt-hashen för admin-kontot dvs "FTPService" som är: 1be448211e59b6428d01d6fe9dfd8f91

<img width="982" alt="SCR-20250322-oytd" src="https://github.com/user-attachments/assets/60b24f37-8837-4088-901f-9b89d386d0fc" />

Vi kan testa stoppa hashen i Crackstation för att se om det är ett tidigare läckt lösenord, vilket det är:

<img width="1420" alt="SCR-20250322-oumg" src="https://github.com/user-attachments/assets/df88c20c-0e8f-4c58-8e1e-b19ea68b72e1" />

`Svar: DentalSurgery528`.

---

### ***Angriparens server***

*Kategori: Utredning av IT-attacken*,  *Poäng: 200*

Vi kan se massa dns-paket till ip-addressen "10.245.122.37" i nätverkstrafiken som indikerar "DNS tunneling". En teknik som angripare ofta använde sig av för att oupptäckt skicka data i query-fältet, för till exempel exfiltration eller för kommunikation med en C2-server. I detta fallet ser vi långa strängar med vad som ser ut som hex data med domänen facebook.com. Till exempel: 0.383d06170e7c392808262a3d0f223c2520111352.facebook.com

<img width="1375" alt="SCR-20250322-ptpl" src="https://github.com/user-attachments/assets/e1688689-ffcd-4bd5-8792-53470c76bcf1" />

`Svar: 10.245.122.37`.

---

### ***Dataläckan***

*Kategori: Utredning av IT-attacken*,  *Poäng: 500*

SE WRITEUP PÅ "Angriparens server" OVAN!

Stoppar vi in datan i Cyberchef kan vi misstänka att datan är krypterad:

<img width="1236" alt="SCR-20250323-bbif" src="https://github.com/user-attachments/assets/7b81e732-6629-48a6-ac4e-69058fe514d6" />

Vi kan försöka brute-forcea olika typer av vanliga krypteringsmetoder, men vi kan också försöka luska i minnesdumpen och se om vi kan hitta något där.

Vi har tidigare försökt få ut filen "ConsoleHost_history.txt" för att se powershell-kommandon men den filen verkar inte logga så det går inte..

Men om vi istället bara kör strings och använder grep för "facebook.com" får vi upp lite intressanta grejer: 

<img width="1404" alt="SCR-20250323-bgwl" src="https://github.com/user-attachments/assets/1e1f52cd-e7ba-4e5b-96f1-ceaf8a780a0a" />

Öppnar vi dessutom minnesdumpen i en hex-läsare och söker på texten "facebook.com" så kan vi få fram ett script som kör någon slags XOR-kryptering på filen gd_patient_04.rtf. 

<img width="1500" alt="SCR-20250323-biby" src="https://github.com/user-attachments/assets/17418c93-b222-44d4-8822-e3dc2f74e229" />

<img width="360" alt="SCR-20250323-bieg" src="https://github.com/user-attachments/assets/0e856ea1-739c-4093-b856-606aa42b0332" />

Vi kan lista ut att scriptet gör följande:

1.	Läser en fil (C:\Shared\Patients\gd_patient_04.rtf).
2.	XOR-krypterar filens data med en nyckel ($k som är CatchMeIfUCanLOL i bytes).
3.	Omvandlar krypterad data till en hex-sträng.
4.	Skapar subdomäner baserade på hex-strängen (facebook.com).
5.	Utför DNS-uppslag på varje subdomän för att exfiltrera data.

Vi försöker carva ut filen C:\Shared\Patients\gd_patient_04.rtf men den verkar vara överskriven..

<img width="1355" alt="image" src="https://github.com/user-attachments/assets/061596ce-b9ea-45d0-8261-3122869b9746" />

Då får vi helt enkelt återskapa den.

Vi börjar med att få ut alla querys som har facebook.com i sig då det är de som är intressanta, vi kan använda tshark till det:

<img width="1400" alt="SCR-20250322-pvjp" src="https://github.com/user-attachments/assets/852546a7-fead-4a1e-99ac-fb8526bb800d" />

Vi sparar detta till en fil "dnsdata.txt".

Samma datta verkar skickas flera gånger så vi filtrerat ut unik hexdatan och sätter ihop det till en lång sträng med följande Python-script:

<img width="794" alt="SCR-20250323-brta" src="https://github.com/user-attachments/assets/70167d2c-2eef-4df7-b62f-65365ed6628f" />

<img width="1406" alt="SCR-20250322-pwme" src="https://github.com/user-attachments/assets/e2252994-7f97-4ad8-8cc5-89a28c1dc3f9" />

Nu kan vi då försöka dekryptera hexdatan med den hårdkodade nyckeln som vi hittade "CatchMeIfUCanLOL" i bytes med följande Python-script:

<img width="1505" alt="SCR-20250322-pxgb" src="https://github.com/user-attachments/assets/3f3daf1c-b411-411c-9475-5149f01e492c" />

<img width="1408" alt="SCR-20250322-pxkw" src="https://github.com/user-attachments/assets/faf39b5f-2d31-4184-ba4b-4489b57a7a78" />

Om vi sedan lägger in den hex datan i fil har vi återskapat filen och kan se namnet på den patient vars känsliga data läcktes:



<img width="717" alt="SCR-20250322-pxvy" src="https://github.com/user-attachments/assets/c400ed47-d084-4a61-8c96-0ccde2ea0fe9" />

`Svar: Håkan Kerberosqvist`.

### ***Fobi***

*Kategori: Utredning av IT-attacken*,  *Poäng: 500*

Tittar vi i patientjorunalen för Håkan Kerberosqvist ser vi att han Anitidaefobi. En fobi som innebär att ankor stirrar på en.

<img width="717" alt="SCR-20250322-pxvy" src="https://github.com/user-attachments/assets/ffba74e4-3892-43b1-bfbb-402750414f79" />

<img width="1230" alt="SCR-20250322-pybi" src="https://github.com/user-attachments/assets/6762b54d-b77b-459c-adc9-77084fde04e7" />

---













   






