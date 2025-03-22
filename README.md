# CertCTF2025-Writeup
Samtliga lösningar för CertCTF2025

Följer tidslinjen i attacken.

### ***Angriparens IPv4-adress***

*Kategori: Adresser*,  *Poäng: 100*

Vi vet från "scenario.txt" att angriparen har kopplat in en mini-dator på nätverket där skrivaren egentligen sitter, möjligtvis på samma subnät.

Genom att titta på "IPv4 statistics" ser vi att ip-adresser på subnätet "192.168.177.0/24" står för den absoluta majoriteten av trafiken:

<img width="1501" alt="SCR-20250322-noff" src="https://github.com/user-attachments/assets/ca3684ac-77c8-464d-8f36-8bbdf3ca8941" />

Tittar vi i trafiken ser vi att det sker massa trafik udda trafik från "192.168.177.141" bland annat massa arp-request, misslyckade tcp-anslutningar och sen en uppkoppling mot 192.168.177.155 på port 4444. Port 4444 är en klassisk port för lyssnarport "Metasploit framework"

Vi kan då räkna ut ganska snabbt att ip-adressen som angriparen använde sig av initialt var "192.168.177.141"

`Svar: 192.168.177.141`.

---

### ***FTP-serverns IPv4-adress***

*Kategori: Adresser*,  *Poäng: 100*

Ftp brukar använda port 21. Filtrerar vi på lyckade anslutningar på port 21 ser vi en adress: "192.168.177.155" vilket innebär att den porten är öppen på den ip-adressen.

<img width="1384" alt="SCR-20250322-ntzk" src="https://github.com/user-attachments/assets/1089ea19-ebfc-4181-a086-b7a28f5f86da" />

Vi vet ju från "scenario.txt" att filservern är drabbad, samt i och med anslutningen från 192.168.177.141 till 192.168.177.155 kan vi ganska snabbt lista ut att det är filservern.

`Svar: 192.168.177.155`.

---

### ***Domänkontrollantens IPv4-adress***

*Kategori: Adresser*,  *Poäng: 100*

Domänkontrollanten hanterar Active Directorys inbyggda biljettsystem Kerberos. Filtrerar vi på detta kan vi luska ut att det troligtvis rör sig om adressen 192.168.177.129.

<img width="1389" alt="SCR-20250322-nwdy" src="https://github.com/user-attachments/assets/66707ce8-e958-4be9-91e5-5ce1bf4acdaf" />

filtrerar vi på adressen kan vi dessutom bekräfta detta ännu mer med namnet "DC-01 GENTLEDENT"

<img width="1384" alt="SCR-20250322-nwjf" src="https://github.com/user-attachments/assets/f4e68672-52e0-49f7-999b-43eebc9a3016" />


`Svar: 192.168.177.129`.

---

### ***Angriparens hostname***

*Kategori: Adresser*,  *Poäng: 100*

Vi vet att angriparen är 192.168.177.141. filtrerar vi på dhcp och 192.168.177.141 får vi följande:

<img width="1389" alt="SCR-20250322-nzhi" src="https://github.com/user-attachments/assets/1704e5c3-a8c9-4e35-aa37-9112e442c17e" />

Klickar vi på request-paketet kan vi se host name för 192.168.177.141:

<img width="970" alt="SCR-20250322-nzls" src="https://github.com/user-attachments/assets/fa294948-b65d-404b-a80e-345ca57329e9" />

`Svar: kali`.

---

### ***Tidszon***

*Kategori: Utredning av IT-attacken*,  *Poäng: 100*

Har man wireshark inställt på svensk tid (dvs. UTC) behöver man inte fundera så mycket, men har man inte det kan man också klicka på sista paketet och då ser vi tiden svenskt tid (UTC). Avrundar vi neråt till helsekund får vi svaret:

<img width="1326" alt="SCR-20250322-obiy" src="https://github.com/user-attachments/assets/1aef3bc4-071f-4448-9401-5449a53a039f" />


`Svar: 09:42:01`.

### ***Initial teknik***

*Kategori: MITRE ATT&CK*,  *Poäng: 100*

Vi ser att det första gör angriparen (192.168.177.141) gör på nätverket är att köra massa arp-request för att få en bild av vilka uppkopplade enheter som finns på INTERNT på nätverket. En teknik som kallas "Remote System Discovery" ur taktiken Discovery. INTE att förväxla med tekniken "active scanning" som är en del av taktiken Reconnaiscance vilket det inte rör sig om här då vi är inne internt på nätverket.

<img width="1390" alt="SCR-20250322-odlu" src="https://github.com/user-attachments/assets/f1cf173c-4127-4647-a5cc-4d104f729f15" />

Sidan för MITRE ATT&CK-tekniken:

<img width="1486" alt="SCR-20250322-ofco" src="https://github.com/user-attachments/assets/31ea1e2e-7a05-441d-bb46-2f2f3f23002a" />

`Svar: T1018`.

---


