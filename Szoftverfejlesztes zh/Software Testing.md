### What is software testing?
A szoftvertesztelés célja a szoftverek minőségének megállapítása és a működés közbeni meghibásodási kockázat csökkentése. A szoftvertermékekkel kapcsolatos olyan tevékenységeket jelent, melyek a hibák felfedezésére és a minőség értékelésére irányulnak.
### Terms used to describe a malfunction
- **Tévedés/tévesztés (error/mistake):** rossz eredményt adó emberi tevékenység.
- **Hiba (defect/fault/bug):** tökéletlenség vagy hiányosság egy munkatermékben, melynél nem teljesülnek a követelmények vagy előírások.
- **Meghibásodás (failure):** olyan esemény, melynél egy komponens vagy rendszer nem lát egy megkövetelt funkciót a megszabott határok között.
### Testing principles
1. A tesztelés a hibák jelenlétét mutatja meg, nem a hiányukat 
2. Lehetetlen a kimerítő tesztelés 
3. A korai tesztelés időt és pénzt takarít meg 
4. A hibák csoportosulnak 
5. A tesztek elkopnak (óvakodj a kártevőírtó paradoxontól) 
6. A tesztelés környezetfüggő 
7. A hibamentesség egy tévhit
### Test case (teszteset)
Tesztfeltételek alapján meghatározott előfeltételek, bemenetek, tevékenységek (adott esetben), elvárt eredmények és utófeltételek halmaza.
Tesztfeltétel: Egy komponens vagy rendszer tesztelhető vonatkozás, melyet a tesztelés alapjául választunk.
*Magas szintű teszteset:* Teszteset, mely absztrakt előfeltételekkel, bemeneti adatokkal, elvárt eredményekkel, utófeltételekkel és (adott esetben) lépésekkel rendelkezik. 
*Alacsony szintű teszteset:* Teszteset, mely konkrét előfeltételekkel, bemeneti adatokkal, elvárt eredményekkel, utófeltételekkel és (adott esetben) a lépések részletes leírásával rendelkezik.
### Test data (tesztadatok)
A tesztadatok a tesztvégrehajtáshoz szükséges adatokat jelentik. 
Az ilyen konkrét értékek a használatukra vonatkozó világos útmutatásokkal együtt végrehajtható alacsony szintű tesztesetekké teszik a magas szintű teszteseteket. 
Ugyanaz a magas szintű teszteset különböző tesztadatokat használhat különböző végrehajtásoknál.
### Tesztelési szintek
###### Egységtesztelés/komponens tesztelés (unit testing/component testing)
- A függetlenül tesztelhető komponensekre összpontosít. 
- Az egységtesztelést általában az a fejlesztő végzi, aki a kódot írja, de legalább a tesztelt kódhoz való hozzáférés szükséges. 
- A fejlesztők gyakran egy komponens kódjának megírása után írnak és hajtanak végre egységteszteket. Azonban az automatikus egységtesztek megírása megelőzheti az alkalmazáskód megírását, lásd például a teszt vezérelt fejlesztést (TDD).
###### Komponensintegrációs tesztelés (component/unit integration testing)
- Az integrált komponensek közötti kommunikációra és interfészekre összpontosít. 
- Az egységtesztelés után végzik és általában automatizált. 
- A komponensintegrációs teszteknek magára az integrációra kell koncentrálnia, nem pedig az egyes komponensek működésére.
###### Rendszertesztelés (system testing)
- A rendszer egészének (funkcionális és nem funkcionális) viselkedésére összpontosít. 
- Jellemzően független tesztelők végzik jelentős mértékben specifikációkra támaszkodva.
###### Rendszerintegrációs tesztelés (system integration testing) 
- A rendszerek közötti kommunikációra és interfészekre összpontosít. 
	- Kiterjedhet külső szervezetekkel és általuk szolgáltatott interfészekkel (például webszolgáltatásokkal) való interakciókra.
- A rendszerintegrációs tesztelés általában a tesztelők felelősége. 
- Olyan alkalmas teszkörnyezetre van hozzá szükség, amely lehetőleg hasonló a működtetési környezethez.
###### Elfogadási tesztelés (acceptance testing)
- Annak meghatározására összpontosít, hogy a rendszer kész-e a telepítésre és az ügyfél (végfelhasználó) általi használatra.
- Gyakran az ügyfél vagy a rendszerüzemeltetők felelőssége, de más érintettek is bevonhatók.
- A szoftver kiadása előtt azt néha odaadják potenciális felhasználok egy kis kiválasztott csoportjának kipróbálásra (alfa tesztelés) és/vagy reprezentatív felhasználók egy nagyobb halmazának (béta tesztelés).
	- Alfa tesztelés: 
		- Felhasználók és fejlesztők együtt dolgoznak egy rendszer tesztelésén a fejlesztés közben. 
		- A fejlesztő szervezet telephelyén történik. 
	- Béta tesztelés: 
		- Ügyfelek és felhasználók egy nagyobb csoportja végzi.
		- A felhasználók helyén történik. 
		- Főleg olyan szoftvertermékekhez alkalmazzák, melyeket sok különböző környezetben használnak. 
		- A marketing egy formája is.
### Change-related testing
- Teszteket kell végezni, amikor módosítások történnek egy rendszerben egy hiba kijavításához vagy új funkcionalitás hozzáadásához/létező funkcionalitás módosításához
A változással kapcsolatos tesztelés két fajtája: 
- Megerősítő tesztelés: célja annak megerősítése, hogy az eredeti hiba sikeresen kijavításra került. 
- Regressziós tesztelés: Lehetséges, hogy egy változás a kód egy részében, akár egy javítás vagy másfajta módosítás, véletlenül hatással van a kód más részeinek viselkedésére. A regressziós tesztelés célja a változások által okozott akaratlan mellékhatások érzékelése.
### Good unit tests (FIRST)
Az egységtesztelés (unit testing) a programkomponensek, például a metódusok és az osztályok tesztelésének folyamata.
A jó egységtesztek ismertetőjegyei:
- Gyors (Fast): A tesztek gyorsak kell, hogy legyenek. Gyorsan kell, hogy lefussanak. 
- Független (Independent): A tesztek nem függhetnek egymástól. 
- Megismételhető (Repeatable): A tesztek bármely környezetben megismételhetők kell, hogy legyenek. 
- Önérvényesítő (Self-Validating): A teszteknek logikai kimenete kell, hogy legyen. Vagy átmennek, vagy megbuknak. 
- Jól időzített (Timely): A teszteket kellő időben kell megírni, közvetlenül a tesztelendő kód előtt.
### Structuring unit tests (AAA)
- Elrendez (Arrange): ez a rész felelős a tesztelt rendszer és függőségei egy kívánt állapotba állításáért. 
- Cselekszik (Act): ez a rész szolgál a tesztelt rendszer metódusainak meghívására, az előkészített függőségek átadására és a kimeneti érték elkapására (ha van). 
- Kijelent (Assert): ez a szakasz szolgál a kimenetel ellenőrzésére. A kimenetel ábrázolható a visszatérési értékkel vagy a tesztelt rendszer végső állapotával.
*Alternatív megfogalmazás: Feltéve, hogy-amikor-akkor (Given-When-Then)*
``` Java
@Test 
public void testPairOfMapEntry() { 
// Arrange: 
final HashMap<Integer,String> map = new HashMap<>(); 
map.put(0, "foo"); 
final Entry<Integer,String? entry = map.entrySet().iterator().next(); 
// Act: 
final Pair<Integer,String> pair = MutablePair.of(entry); 
// Assert: 
assertEquals(entry.getKey(), pair.getLeft()); 
assertEquals(entry.getValue(), pair.getRight()); 
}
```
## JUnit
Az xUnit név olyan egységteszt keretrendszerek egy családját jelenti, melyek a SmallTalk programozási nyelvhez készült SUnit egységteszt keretrendszer felépítését követik

JUnit 5 moduláris felépítésű: 
JUnit 5 = JUnit Platform + JUnit Jupiter + JUnit Vintage

Használat az Apache Maven-nel: Adjuk hozzá az org.junit.jupiter:junit-jupiter-engine terméket függőségként a pom.xml-hez:
###### Tesztosztályok és -metódusok
- **Tesztosztály:** bármely felsőszintű osztály, statikus tagosztály vagy @Nested osztály, mely legalább egy tesztmetódust tartalmaz. (Nem lehet absztrakt és egyetlen konstruktora kell, hogy legyen.)
- **Tesztmetódus:** a `@Test`, `@RepeatedTest`, `@ParameterizedTest`, `@TestFactory` vagy `@TestTemplate` annotációval megjelölt bármely példánymetódus.
- **Életciklus metódus:** a `@BeforeAll`, `@AfterAll`, `@BeforeEach` vagy `@AfterEach` annotációval megjelölt bármely metódus.
- nem lehetnek *private*
###### Teszt végrehajtási életciklus
- Alapértelmezésben a JUnit egy új példányt hoz létre minden egyes tesztosztályból az egyes tesztmetódusok végrehajtás előtt, mely lehetővé teszi a tesztmetódusok izoláltan történő végrehajtását. 
- Ez a viselkedés megváltoztatható, az összes tesztmetódus ugyanazon a tesztpéldányon történő végrehajtásához a tesztosztályt a `@TestInstance(Lifecycle.PER_CLASS)` annotációval kell megjelölni.
###### Teszteredmények
- **Siker (success):** amikor a teszt végrehajtásakor minden tényleges eredmény megegyezik a várt végeredményekkel. (passes)
- **Bukás (failure):** amikor a teszt végrehajtásakor a tényleges eredmény nem egyezik meg a várt végeredménnyel. A bukást egy elbukó állítás okozza. (fails)
- **Hiba (error):** amikor a teszt végrehajtásakor egy hiba következik be, mely megakadályozza a befejeződést. A hibát egy váratlan kivétel vagy hiba okozza
### Kódlefedettségi metrikák
Egy kódlefedettségi metrika (röviden lefedettségi metrika) a végrehajtott forráskód mennyiségét méri százalékosan kifejezve egy tesztkészlet futtatásakor

A leggyakrabban használt lefedettségi metrikák az **utasítás lefedettség (statement coverage)** és a **sor lefedettség (line coverage):** 
- Utasítás lefedettség = Végrehajtott utasítások / Összes utasítás száma 
- Sor lefedettség = Végrehajtott kódsorok / Összes sor száma

Az **ág lefedettség (branch coverage)** egy lefedettségi mérték, mely az olyan vezérlési szerkezeteken alapul, mint az `if` és a `switch`.
Ág lefedettség = Végrehajtott ágak / Összes ág száma

**Mi az ésszerű lefedettségi szám? **
- Veszélyes egy bizonyos érték elérésének megcélzása egy lefedettségi metrikánál, mivel könnyen ez válhat a fő céllá.
- Inkább a megfelelő egységtesztelésre kell koncentrálni.
- Ökölszabályok: 
	- Jó, ha egy rendszer fő részeinél nagy a lefedettség. 
	- Nem jó ezt magas szintű követelménnyé tenni.

**összegzés**: A lefedettségi metrikák jó negatív indikátorok, de rossz pozitív indikátorok -> Egy alacsony kódlefedettségi metrika érték azt jelzi, hogy problémák vannak a tesztekkel, azonban egy magas érték nem jelenti azt, hogy a tesztek jó minőségűek.