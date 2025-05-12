Egy programkonstrukcióra vonatkozó metaadat, melynek nincs közvetlen hatása a programvégrehajtásra.
Az annotációk gépi feldolgozásra alkalmasak, fordítási vagy futási időben érhetők el.
Dokumentációs célokat is szolgálnak, nagyon tömör dokumentációs formának tekinthetők.
#### Possible use cases
**Információk szolgáltatása a fordítónak:** például tekintsen el bizonyos figyelmeztetésektől, jelezzen bizonyos hibákat. (pl. @Deprecated és @Override)

**Kódgenerálás:** Dagger, Jakarta XML Binding (JAXB), Project Lombok

**Futásidejű feldolgozás:** bizonyos annotációkhoz hozzá lehet férni végrehajtási időben.
Egységtesztelés: JUnit 
Perzisztencia: Jackson, Jakarta Persistence (JPA), Jakarta XML Binding (JAXB), Jdbi 
HTTP kliensek: Feign Retrofit

**Futásidejű feldolgozás (folytatás):** bizonyos annotációkhoz hozzá lehet férni végrehajtási időben.
Függőség befecskendezés: Guice, Spring Framework 
Alkalmazás keretrendszerek: Micronaut Framework Spring Framework
### Syntax
Egy annotációt a következők alkotnak: 
- Egy annotáció interfész neve 
- Opcionálisan egy olyan lista, melyet vesszővel elválasztott elem-érték párok alkotnak 
	- A listát () karakterek között kell megadni. 
- Az annotációt az annotáció interfész annotációjának mondjuk.
- Az annotáció interfész határozza meg a használható elem-érték párokat. 
	- Nem kötelező az alapértelmezett értékkel rendelkező elemek megadása. 
- Az elem-érték párok sorrendje nem lényeges. 
	- Az elem-érték párokat abban a sorrendben szokás egy annotációban megadni, melyben az annotáció interfész deklarációjában is deklarálásra kerülnek az elemek.
- Ha egy elem típusa egy tömb típus, akkor az értéket egy tömb inicializáló kifejezés kell, hogy szolgáltassa. 
	- Kivéve azt az esetet, amikor az érték egy egyelemű tömb, ilyenkor elhagyható a kapcsos zárójelpár. 
	- Ekvivalens például az alábbi két annotáció: 
		- `@Target({ElementType.METHOD})` 
		- `@Target(ElementType.METHOD)`
### Kinds of annotations
- Közönséges annotáció: 
``` Java
@XmlElement(name = "creator", 
	namespace = "http://purl.org/dc/terms/", 
	required = true)
```
- Egyelemű annotáció: 
``` Java
@SuppressWarnings(value = "unchecked")
@SuppressWarnings("unchecked") 
  
@Target(value = {ElementType.FIELD, ElementType.METHOD}) 
@Target({ElementType.FIELD, ElementType.METHOD}) 
  ```
- Jelölő annotáció: ha nincs megadva egyetlen elem-érték pár sem, akkor elhagyhatók a () karakterek. 
``` Java
@NotNull
@NotNull()
```
### Where  annotations may appear?
