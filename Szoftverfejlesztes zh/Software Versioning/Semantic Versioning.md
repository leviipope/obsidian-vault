**Az általánosan bevett gyakorlaton alapuló egyszerű szabályok és követelmények a verziószámok kiosztásához és növeléséhez.**
Tetszőleges olyan szoftverhez használható, mely nyilvános API-al rendelkezik.  Az API változásai a verziószámának növelésével kerülnek kifejezésre.
### Why do we need SemVer?
- A függőségkezeléshez pontosan meghatározott verziószámok szükségesek, melyek világos és rugalmas függőség-specifikációkat tesznek lehetővé. 
- Kapcsolódó fogalom: verziópokol (version hell), függőségi pokol (dependency hell)
### SemVer specification
A Szemantikus verziószámozás specifikáció eredeti szerzője **Tom Preston-Werner**, a GitHub egyik társalapítója.
### SemVer normal version numbers
A normál verziószámok X.Y .Z formájúak, ahol X, Y és Z nemnegatív egész számok:
- X: főverzió (major version)
- Y: alverzió (minor version)
- Z: patch verzió.
###### Usage
Növeld,
- a *főverziót*, amikor a korábbi verzióval inkompatibilis módon változik az API
- az *alverziót*, amikor a korábbi verzióval kompatibilis módon vezetünk be új funkcionalitást
- a *patch* verziót, amikor a korábbi verzióval kompatibilis hibajavítások történnek.
###### Details
- Miután kiadásra került egy verziózott csomag, a verzió tartalma nem módosítható. Bármilyen módosítást egy új verzióként kell kiadni.
- A nulla főverzió a kezdeti fejlesztéshez van fenntartva. Itt bármilyen változás történhet, az API nem tekinthető stabilnak.
- A patch verziót 0-ra kell visszaállítani az alverzió növelésekor (például 1.2.3 → 1.3.0)
- A patch verziót és az alverziót 0-ra kell visszaállítani a főverzió növelésekor (például 0.9.15 → 1.0.0).
### Breaking changes
**Visszafelé NEM kompatibilis változás egy nyilvános API-ban.** Az API kliensei számára fordításidejű, szerkesztésidejű vagy futásidejű hibákat okoz.
### pre-release versions
**X . Y . Z - V** formájú, ahol V → 0-9A-Za-z
- A kiadás előtti verziók alacsonyabb precedenciájúak, mint a megfelelő normál verziók. 
- Egy kiadás előtti verzió azt jelzi, hogy a verzió instabil és lehet, hogy nem elégíti ki a megfelelő normál verzió tervezett kompatibilitási követelményeit. 
- Példák: 1.0.0-alpha, 1.0.0-alpha.1, 1.0.0-beta.2
### Determining version precedence
- 1.0.0 < 1.0.1 < 1.2.0 < 1.10.0 < 1.10.1 < 2.0.0
- 1.0.0-alpha < 1.0.0
- 1.0.0-alpha < 1.0.0-alpha.1 < 1.0.0-alpha.beta < 1.0.0-beta < 1.0.0-beta.2 < 1.0.0-beta.11 < 1.0.0-rc.1 < 1.0.0
### SemVer felhasználások és eszközök
###### Csomag ökoszisztémák és csomagkezelők:
- Node.js: npm
- PHP: Composer
- Rust: Cargo
- .NET: NuGet
###### Eszközök:
- composer/semver (PHP)
- GitVersion (C#)
- semver (JavaScript, Python)
- Java SemVer (Java)
### Más verziószámozási sémák
- Calendar Versioning (Ubuntu)
- ZeroVer: 0-based Versioning
- Python
- TEX: a TEX verziószáma a π-hez konvergál, a legutóbbi verzió a 3.141592653 számú. 