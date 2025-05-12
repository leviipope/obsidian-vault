### Static code analysis
A statikus kódelemzés  a programkód elemzésének olyan folyamata, ahol a kód végrehajtása nem történik meg.
C#: InferSharp, Roslyn Analyzers, Roslynator
C++: Cppcheck
JavaScript: ESLint, JSHLint, JSLint, RSLint
Java: CheckStyle, Error Prone, NullAway, SpotBugs
Python: Prospector, Pylint
### Dont repeat yourself aka. DRY
>Every piece of knowledge must have a single, unambiguous, authoritative representation within a system.

Az ellenkezője a WET. „We enjoy typing”, „write everything twice”, „waste everyone's time”.
###### Az ismétlések fajtái:
- Kényszerített ismétlés (imposed duplication): a környezet láthatólag megköveteli az ismétlést. 
- Nem szándékos ismétlés (inadvertent duplication): a fejlesztők nem veszik észre, hogy információkat duplikálnak. 
- Türelmetlen ismétlés (impatient duplication): a fejlesztők lustaságából fakad, az ismétlés látszik a könnyebb útnak. 
- Fejlesztők közötti ismétlés (interdeveloper duplication): egy csapatban vagy különböző csapatokban többen duplikálnak egy információt.

A **kódismétlés (duplicate code)** azonos (vagy nagyon hasonló) forráskódrész, mely egynél többször fordul elő egy programban. Nem minden kódismétlés információ ismétlés!

A DRY elv megsértése nem mindig kódismétlés formájában jelennek meg. A DRY elv az információk megismétléséről szól. A tudás egy darabkája két teljesen eltérő módon is kifejezhető két különböző helyen.
``` Java
class Line {
Point start; 
Point end; 
double length; // a DRY elv megsértése 
}
```

Az elv megsértése kiküszöbölhető a length adattag egy metódusra való kicserélésével:
``` Java
class Line {
Point start; 
Point end; 

	double length() { 
		return start.distanceTo(end); 
	} 
}
```

### Keep it simple, stupid aka. KISS
- 1960-as évek, amerikai haditengerészet. 
- Kelly Johnson (1910–1990) repülőmérnöknek tulajdonítják a kifejezést. 
- Az egyszerűségre való törekvés: 
	- Leonardo da Vinci (1452–1519): „Az egyszerűség a kifinomultság csúcsa.” 
	- Ludwig Mies van der Rohe (1886–1969): „A kevesebb több.” 
	- Albert Einstein (1879–1955): 
		- „Everything should be made as simple as possible, but not simpler.” 
### You aren't gonna need it aka. YAGNI
- Az extrém programozás (XP) egy alapelve
- "Mindig akkor implementálj valamit, amikor tényleg szükséged van rá, soha ne akkor, amikor csak sejted, hogy kell.”
- Anyagi szempontból is meggondolandó: Mekkora költséggel jár egy lehetőség fejlesztése ami jelenleg nem szükséges?
- A YAGNI csak akkor járható stratégia, ha a kód könnyen változtatható.
### Coupling
Egy szoftvermodul függésének mértéke egy másik szoftvermodultól. Más szóval, a szoftvermodulok közötti csatoltság annak mértéke, hogy mennyire szoros a kapcsolatuk. A csatoltság laza vagy szoros lehet.
- Szoros csatoltság: 
	- A bonyolultságot növeli, mely megnehezíti a kód módosítását, tehát a karbantarthatóságot csökkenti.
	- Az újrafelhasználhatóságot is csökkenti.
- Laza csatoltság:
	- Lehetővé teszi a fejlesztők számára a nyitva zárt elvnek megfelelő kód írását, azaz a kódot kiterjeszthetővé teszi, a kiterjeszthetőség pedig karbantarthatóvá teszi. 
	- Lehetővé teszi a párhuzamos fejlesztést.
### GoF principles
- **Interfészre programozzunk, ne implementációra.**
- **Részesítsük előnyben az objektum-összetételt az öröklődéssel szemben.**
###### objektum-összetétel vs. öröklődés
A két leggyakoribb módszer az újrafelhasználásra az objektumorientált rendszerekben: 
- Öröklődés (fehér dobozos újrafelhasználás) 
- Objektum-összetétel (fekete dobozos újrafelhasználás)

A fehér/fekete dobozos jelző a láthatóságra utal.

Öröklődés előnyei:
- Statikusan, fordítási időben történik, és használata egyszerű, mivel a programozási nyelv közvetlenül támogatja.
Az öröklődés hátrányai: 
- A szülőosztályoktól örökölt megvalósításokat futásidőben nem változtathatjuk meg, mivel az öröklődés már fordításkor eldől. 
- Mivel az öröklődés betekintést enged egy alosztálynak a szülője megvalósításába, gyakran mondják, hogy az öröklődés megszegi az egységbe zárás szabályát.
- Az alosztály megvalósítása annyira kötődik a szülőosztály megvalósításához, hogy a szülő megvalósításában a legkisebb változtatás is az alosztály változását vonja maga után.
- Ha az örökölt megvalósítás bármely szempontból nem felel meg az új feladatnak, arra kényszerülünk, hogy újraírjuk, vagy valami megfelelőbbel helyettesítsük a szülőosztályt. Ez a függőség korlátozza a rugalmasságot, és végül az újrafelhasználhatóságot.

Öröklődés: 
- Körültekintően kell alkalmazni. 
- Nem megfelelő alkalmazása törékeny szoftvert eredményezhet. 
- Olyan osztályokat biztonságos kiterjeszteni, melyeket kifejezetten kiterjesztéshez terveztek és dokumentáltak.

Objektum-összetétel:
- Dinamikusan, futásidőben történik, olyan objektumokon keresztül, amelyek hivatkozásokat szereznek más objektumokra. 
- Szükséges hozzá, hogy az objektumok figyelembe vegyék egymás interfészét.

| előnyei                                                                                                     | hátrányai                                                                                                          |
| ----------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------ |
| Mivel az objektumokat csak az interfészükön keresztül érhetjük el, nem szegjük meg az egységbe zárás elvét. | Az objektum-összetételen alapuló tervezés alkalmazása során több objektumunk lesz (még ha osztályunk kevesebb is). |
| Bármely objektumot lecserélhetünk egy másikra futásidőben, amíg a típusaik egyeznek.                        |                                                                                                                    |
| Az osztályok és osztályhierarchiák kicsik maradnak                                                          |                                                                                                                    |
### SOLID principles
- **S**ingle Responsibility Principle (SRP) – Egyszeres felelősség elve 
- **O**pen/Closed Principle (OCP) – Nyitva zárt elv 
- **L**iskov Substitution Principle (LSP) – Liskov-féle helyettesítési elv 
- **I**nterface Segregation Principle (ISP) – Interfész szétválasztási elv 
- **D**ependency Inversion Principle (DIP) – Függőség megfordítási elv
###### SRP (Egyszeres felelősség elve)
- „A class should have only one reason to change.”
- Egy felelősség egy ok a változásra. Ha egy osztálynak egynél több felelőssége van, akkor egynél több oka van a változásra.
- Egynél több felelősség esetén a felelősségek csatolttá válnak. Egy felelősségben történő változások gyengíthetik vagy gátolhatják az osztály azon képességét, hogy eleget tegyen a többi felelősségének.
- Az elv így fogalmazható újra: Egy modul egy, és csak egyetlen aktornak (emberek egy olyan csoportja, akik azt akarják, hogy a szoftver ugyanúgy változzon) van alárendelve.
###### OCP (Nyitva zárt elv)
A szoftver entitások (osztályok, modulok, függvények, …) legyenek nyitottak a bővítésre, de zártak a módosításra.
- Nyitott a bővítésre: a modul viselkedése kiterjeszthető. 
- Zárt a módosításra: a modul viselkedésének kiterjesztése nem eredményezi a modul forrás- vagy bináris kódjának változását.
###### LSP - Liskov Substitution Principle (Liskov-féle helyettesítési elv )
Ha az S típus a T típus altípusa, nem változhat meg egy program működése, ha benne a T típusú objektumokat S típusú objektumokkal helyettesítjük. 
**S gyerek, T szülő, nem változik a program ha minden szülőt gyerekeire cseréljük**
###### ISP (Interfész szétválasztási elv )
„Classes should not be forced to depend on methods they do not use.”
**Vastag interfész (fat interface):** Az észszerűén szükségesnél több tagfüggvénnyel és baráttal rendelkező interfész.
**Interfész szennyezés (interface pollution):** Egy interfész szennyezése szükségtelen metódusokkal.
###### DIP (Függőség megfordítási elv)
- Magas szintű modulok ne függjenek alacsony szintű moduloktól. Mindkettő absztrakcióktól függjön. 
- Az absztrakciók ne függjenek a részletektől. A részletek függjenek az absztrakcióktól.
- A magas szintű modulok tartalmazzák az alkalmazás üzleti logikáját, ők adják az alkalmazás identitását. Ha ezek a modulok alacsony szintű moduloktól függenek, akkor az alacsony szintű modulokban történő változásoknak közvetlen hatása lehet a magas szintű modulokra, szükségessé tehetik azok változását is. Ez abszurd! A magas szintű modulok azok, melyek meg kellene, hogy határozzák az alacsony szintű modulokat.
- A magas szintű modulokat szeretnénk újrafelhasználni. Az alacsony szintű modulok újrafelhasználásra elég jó megoldást jelentenek a programkönyvtárak.
### Dependency injection
A vezérlés megfordítása (IoC – inversion of control) nevű architekturális minta alkalmazásának egy speciális esete.
*Definition: A függőség befecskendezés olyan szoftvertervezési elvek és minták összessége, melyek [[Object-Oriented Design Principles#Coupling|lazán csatolt kód]] fejlesztését teszik lehetővé.*
- A lazán csatoltság kiterjeszthetővé teszi a kódot, a kiterjeszthetőség pedig karbantarthatóvá.
- **Függőség (dependency):** egy kliens által igényelt szolgáltatást jelent, mely a feladatának ellátásához szükséges. 
- **Függő (dependent):** egy kliens objektum, melynek egy függőségre vagy függőségekre van szüksége a feladatának ellátásához. 
- **Objektum gráf (object graph):** függő objektumok és függőségeik egy összessége. 
- **Befecskendezés (injection):** egy kliens függőségeinek megadását jelenti.
- **DI konténer (DI container):** függőség befecskendezési funkcionalitást nyújtó programkönyvtár. 
	- Az [[Framework|Inversion of Control (IoC) container]] kifejezést is használják rájuk. 
	- A függőség befecskendezés alkalmazható DI konténer nélkül. 
		- Tiszta DI: függőség befecskendezés alkalmazásának gyakorlata DI konténer nélkül.
A függőség befecskendezés objektum gráfok hatékony létrehozásával, ennek mintáival és legjobb gyakorlataival foglalkozik. A DI keretrendszerek lehetővé teszik, hogy a kliensek a függőségeik létrehozását és azok befecskendezését külső kódra bízzák.
**Előnyei:**
- Kiterjeszthetőség 
- Karbantarthatóság 
- Tesztelhetőség: a függőség befecskendezés támogatja az egységtesztelést.