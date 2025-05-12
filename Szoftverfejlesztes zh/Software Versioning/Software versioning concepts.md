A konfiguráció menedzsment egy fejlődő szoftverrendszerben történő változások kezelésének folyamata.
##### Configuration Management
- Version control
- System building
- Change management
- Release management
### Versioning
###### Definition by SEVOCAB: 
> Egyedi verziónevek vagy verziószámok hozzárendelése szoftver konfiguráció elemek egyedi állapotaihoz, általában egy bizonyos célra, mint például a szoftvertermék egy kiadása egy külső csoport számára vagy pedig egy bizonyos termékvonal azonosítása.

A **verzió** kifejezés egy szoftver konfigurációs elem egyedi és megkülönböztethető állapotait jelenti.
### Version (verzióazonosító)
###### Definition by SEVOCAB: 
>Kiegészítő információ egy konfiguráció elem verzióinak megkülönböztetéséhez.

A verzióazonosítókat általában növekvő sorrendben rendelik hozzá egy szoftver konfigurációs elem különböző verzióihoz. Ez azt jelenti, hogy egy rendezés van értelmezve a verzióazonosítók halmazán.
### Release
Egy **szoftver (rendszer) kiadás (software release, system release)** egy szoftver (szoftverrendszer) egy olyan verziója, melyet elérhetővé tesznek az ügyfelek számára.
###### 2 két fajtája:
- **fő kiadások (major releases)**: jelenetős új funkciókat hoznak.
- **minor kiadások (minor releases):** hibajavításokat, a funkciók terén pedig kisebb továbbfejlesztéseket hoznak
### Changelog
###### Definition by Olivier Lacan: 
>Egy changelog egy olyan állomány, mely egy gondosan összeállított és időrendbe rendezett listát tartalmaz arról, hogy egy projekt egyes verzióiban milyen említésre méltó változások történtek.
###### changelog formátum konvenciók:
- [Keep a Changelog](https://keepachangelog.com/) (Olivier Lacan)
- [Common Changelog](https://common-changelog.org/) (Vincent Weevers)
###### tools:
- conventional-changelog-cli (JavaScript)
- git-cliff (Rust)
- github-changelog-generator (Ruby)
- GitHub: [Automatically generate release notes](https://docs.github.com/en/repositories/releasing-projects-on-github/automatically-generated-release-notes)
### Version Control
*aka. revision control, source control, source code management*
###### Definition by Sommerville: 
>Egy szoftverrendszerben és komponenseiben történő változások kezelésének folyamata, ahol tudható, hogy a komponens/rendszer egyes verzióiban milyen változások történtek, és vissza lehet állítani a komponensek/rendszer korábbi verzióit.
### Version Control Systems
###### Definíció (Sommerville): 
>A verziókezelési folyamatok támogatására fejlesztett szoftvereszközök.
###### A verziókezelő rendszerek két fajtája: 
- centralizált (például Subversion) 
- elosztott (például Git).

Examples for VCSs: Apache Subversion, Fossil, Git, Mercurial
### Related term: Version hell, dependency hell


