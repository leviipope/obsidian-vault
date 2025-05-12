### What is a pattern?
>Minden minta olyan problémát ír le, ami újra és újra felbukkan a környezetünkben, s aztán leírja hozzá a megoldás magját, oly módon, hogy a megoldás milliószor felhasználható legyen, anélkül, hogy valaha is kétszer ugyanúgy csinálnánk.

**Részei:**
- Környezet (Context): Mely helyzetekben fordul elő a probléma. 
- Probléma (Problem): Az adott környezetben ismétlődően felmerülő probléma. 
	- Erő (Force): Olyan szempontot jelent, melyet a megoldás során figyelembe kell venni.
- Megoldás (Solution):  Hogyan oldjuk meg az ismétlődő problémát? Hogyan ellensúlyozzuk ki az erőket? 
	- Egy megoldási sémát ad, nem egy részletes tervet. Mentális építőkocka.

### Architectural patterns, the Model-View-Controller (MVC) architectural pattern
Az architekturális minták szoftverrendszerek alapvető szerkezeti felépítésére adnak sémákat. Ehhez előre definiált alrendszereket biztosítanak, meghatározzák ezek felelősségi köreit, valamint szabályokat és irányelveket tartalmaznak a köztük lévő kapcsolatok szervezésére vonatkozólag. (Példák: hexagonális architektúra, microkernel, ***modell-nézet-vezérlő***, …)

##### Példa architekturális mintára: MVC (2)
- Név: Modell-nézet vezérlő (Model-View-Controller) 
- Környezet: Rugalmas ember-gép felülettel rendelkező interaktív alkalmazások. 
- Probléma: Különösen gyakori az igény a felhasználói felületek változtatására. 
	- Erők:
		- Ugyanaz az információt különböző módon jelenik meg különböző helyeken (például oszlop- vagy kördiagramon). 
		- Az alkalmazás megjelenítésének és viselkedésének azonnal tükröznie kell az adatokon végzett műveleteket. 
		- A felhasználói felület könnyen változtatható kell hogy legyen, akár futásidőben is. 
		- Különböző look and feel szabványok támogatása vagy a felhasználói felület portolása nem érintheti az alkalmazás magjának kódját. 
- Megoldás: Az interaktív alkalmazás három részre osztása: 
	- A modell komponens az adatokat és a funkcionalitást csomagolja be, független a kimenet ábrázolásmódjától vagy az input viselkedésétől. 
	- A nézet komponensek jelenítik meg az információkat a felhasználónak. 
	- A vezérlő fogadja a bemenetet, melyet szolgáltatáskérésekké alakít a modell vagy a nézet felé.

MVC keretrendszerek: Sprint Framework (Java), Sails.js (JS), Laravel (PHP), Django (Python), Ruby (Ruby on Rails)

MVC keretrendszer a JavaFX? Valójában nem az, de megvalósítható a JavaFX segítségével az MVC minta.
### Design patterns, classification of design patterns
- A tervezési minták középszintű minták, kisebb léptékűek az architekturális mintáknál.
- Alkalmazásuknak nincs hatása egy szoftverrendszer alapvető felépítésére, de nagyban meghatározhatják egy alrendszer felépítését.
- Függetlenek egy adott programozási nyelvtől vagy programozási paradigmától.

A minták osztályozása céljuk szerint (GoF): 
- **Létrehozási minták (creational patterns):** az objektumok létrehozásával foglalkoznak. 
- **Szerkezeti minták (structural patterns):** azzal foglalkoznak, hogy hogyan alkotnak osztályok és objektumok nagyobb szerkezeteket. 
- **Viselkedési minták (behavioural patterns):** az osztályok vagy objektumok egymásra hatását valamint a felelősségek elosztását írják le.
### Programming idioms/implementation patterns
- Egy idióma egy programozási nyelvre jellemző alacsony szintű minta. Az idiómák jelentik a legalacsonyabb szintű mintákat.
- Egy idióma leírja, hogy hogyan valósítsuk meg komponensek és kapcsolataik bizonyos vonatkozásait az adott nyelv eszközrendszerével. A legtöbb idióma nyelvspecifikus, létező programozási tapasztalatot hordoznak.
### Anti-patterns, the blob and the spaghetti code anti-patterns
>Egy antiminta pont olyan, mint egy minta, kivéve azt, hogy megoldás helyet valami olyat ad, ami látszólag megoldásnak néz ki, de nem az.

Egy problémára adott általánosan előforduló megoldások, melyek kifejezetten negatív következményekkel járnak.

###### The Blob
- Antiminta neve: a massza (The Blob) ● 
- Más néven: Winnebago, az Isten osztály (The God Class) 
- Leggyakoribb előfordulási szint: alkalmazás 
- Újragyártott megoldás neve: a felelősségek újraosztása 
- Újragyártott megoldás típusa: szoftver 
- Kiváltó okok: lustaság, sietség 
- Kiegyensúlyozatlan erők: a funkcionalitás, a teljesítmény és a bonyolultság kezelése 
- Anekdotaszerű példa: „Ez az osztály az architektúránk szíve.”
- Általános alak:
	- A massza olyan tervezésnél fordul elő, ahol a feldolgozást egyetlen osztály sajátítja ki magának, a többi osztály pedig elsősorban adatokat zár egységbe. 
	- Olyan osztálydiagram jellemzi, mely egyetlen bonyolult vezérlő osztályból és azt körülvevő egyszerű adat osztályokból áll.
- Tünetek és következmények: 
	- Egyetlen osztály nagyszámú attribútummal, művelettel vagy mindkettővel. Általában a massza jelenlétét jelzi egy 60-nál több attribútummal és művelettel rendelkező osztály. 
	- Egymáshoz nem kapcsolódó attribútumok és műveletek bezárása egyetlen osztályba. 
	- Egy ilyen osztály túl bonyolult újrafelhasználáshoz vagy teszteléshez. 
	- Költséges lehet egy ilyen osztály a memóriába való betöltése. Még egyszerű műveletekhez is sok erőforrást használ.
- Tipikus okok: 
	- Az objektumorientált architektúra hiánya. (Bármilyen) architektúra hiánya. 
	- Az architektúra kikényszerítésének hiánya 
	- Túl korlátozott beavatkozás 
	- Kódolt katasztrófa (rossz követelmény specifikáció) 
- Újragyártott megoldás: A megoldás kódújraszervezéssel jár.

###### Spagetti code
- Antiminta neve: spagetti kód (Spaghetti Code) 
- Leggyakoribb előfordulási szint: alkalmazás 
- Újragyártott megoldás neve: kódújraszervezéssel, kódtisztítás 
- Újragyártott megoldás típusa: szoftver 
- Kapcsolódó megoldások: analízis-paralízis, lávafolyás
- Kiváltó okok: tudatlanság, lustaság 
- Kiegyensúlyozatlan erők: a bonyolultság, a változás kezelése 
- Anekdotaszerű példa: „Ó! Micsoda zűrzavar!”, „Ugye tisztában vagy vele, hogy a nyelv egynél több függvényt támogat?”, „Könnyebb újraírni ezt a kódot, mint megpróbálni módosítani.”
- Háttér: Klasszikus, a leghíresebb antiminta, mely egyidős a programozási nyelvekkel.
- Általános alak: 
	- Strukturálatlan, nehezen átlátható programkódként jelenik meg. 
	- Objektumorientált nyelvek esetén kevés osztály jellemzi, melyeknél a metódusok megvalósítása nagyon hosszú.
- Tünetek és következmények: 
	- A metódusok nagyon folyamat-orientáltak, az objektumokat gyakran folyamatoknak nevezik.
	- Kevés kapcsolat van az objektumok között. 
	- Sok a paraméter nélküli metódus, melyek osztályszintű és globális változókat használnak. 
	- Nehéz a kód újrafelhasználása. Sok esetben nem is szempont az újrafelhasználhatóságot.
	- Elvesznek az objektumorientáltság előnyei, nem kerül felhasználásra az öröklődés és a polimorfizmus. 
	- A további karbantartási erőfeszítések csak súlyosbítják a problémát.
	- Költségesebb a létező kódbázis karbantartása, mint egy új megoldás kifejlesztése a semmiből.