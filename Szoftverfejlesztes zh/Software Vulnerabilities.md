Definition by CVE Glossary: A flaw in a software, firmware, hardware, or service component resulting from an imperfection that can be exploited in a way that negatively affects the confidentiality, integrity, or availability of the affected component or components.
### 0-day vulnerability
A vulnerability that the vendors are not aware of. 
A **zero-day exploit** (zero-day attack) is the exploitation of a zero-day vulnerability. (e.g. Log4Shell)
### Vulnerability database
A vulnerability database is a special database for collecting and managing information about vulnerabilities found in computer systems.
### CVE
- A Gyakori Sebezhetőségek és Kitettségek (Common Vulnerabilities and Exposures, CVE) program küldetése a nyilvánosságra hozott kiberbiztonsági sebezhetőségek azonosítása, meghatározása és katalogizálása. A katalógusban minden egyes sebezhetőséghez egy CVE rekord van. A sebezhetőségeket a CVE Programmal együttműködő szervezetek fedezik fel és publikálják.
- Egy nemzetközi kiberbiztonsági közösségi munka, melyet az Egyesült Államok Belbiztonsági Minisztériuma szponzorál.
- A CVE nem egy sebezhetőségi adatbázis. Egységes azonosítók egy listája közismert kiberbiztonsági sebezhetőségekhez. Lehetővé teszi az információ technológiai és kiberbiztonsági szakértők számára, hogy ugyanarra a problémára hivatkozzanak. Tehát inkább egy szótár, mint adatbázis.
**Fogalomtár:**
- **CVE azonosító (CVE ID)**: A CVE Program által kiosztott egyedi alfanumerikus azonosító. Minden egyes azonosító egy bizonyos sebezhetőségre hivatkozik. 
- **CVE rekord:** Egy CVE azonosítóhoz tartozó sebezhetőséget leíró adatok. Ezek az adatok ember és gép által olvasható formátumban állnak rendelkezésre (HTML, JSON). 
- **CVE lista**: A CVE Program által azonosított vagy számára bejelentett CVE rekordok katalógusa.
### NDE
- A Nemzeti Sebezhetőségi Adatbázis (National Vulnerability Database, NVD) a NIST által karbantartott, a CVE listára épülő és azzal teljesen szinkronizált sebezhetőségi adatbázis. 
- A CVE-hez hasonlóan az NVD-t is az Egyesült Államok Belbiztonsági Minisztériuma szponzorálja.
- Az NVD adatok egy web API-n keresztül érhetők el.
###### CVE és NVD kapcsolata
- Az NVD a CVE rekordokat olyan információkkal egészíti ki, mint például a hibajavítási információk vagy súlyossági pontszámok. 
  Az NVD a Common Vulnerability Scoring System (CVSS) (Egységes Sebezhetőség Pontozási Rendszer) segítségével rendel egy súlyossági pontszámot minden egyes sebezhetőséghez (0 és 10 között).
- Az NVD fejlett keresési lehetőségeket is biztosít, mint például a keresés operációs rendszer szerint vagy a sebezhetőség típus alapján. 