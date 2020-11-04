TODO intro

### Definities
TODO

### Informatiemodellen

TODO intro

Een informatiemodel beschrijft een werkelijkheid. We onderscheiden vier niveaus in variërend van een zo getrouw mogelijke beschrijving van die werkelijkheid tot een specificatie van de wijze van vastlegging van die werkelijkheid in een database of uitwisselformaat [MIM TODO] [NEN3610 TODO] :

**Niveau 1:** een model van begrippen waarin de werkelijkheid wordt beschreven door middel van de daarin gehanteerde begrippen en de relaties tot elkaar.

**Niveau 2:** een conceptueel informatiemodel waarin de werkelijkheid wordt beschreven door middel van de informatie die voor dit domein relevant is, onafhankelijk van de het ontwerp en de implementatie in systemen. Het geeft een zo getrouw mogelijke beschrijving van die werkelijkheid en is in natuurlijke taal geformuleerd.

**Niveau 3:** een logisch informatie- of gegevensmodel waarin de werkelijkheid wordt beschreven door middel van de representatie van de informatie in de systemen en de uitwisseling tussen systemen en gebruikers. Een logisch informatiemodel is implementatie onafhankelijk en kan in naar meerdere technische modellen worden geïmplementeerd.

**Niveau 4:** een fysiek of technisch gegevens- of datamodel waarin de werkelijkheid wordt beschreven door middel van de structuur en eigenschappen van de technologie die wordt gebruikt bij de opslag of uitwisseling. Een fysiek of technisch datamodel is afhankelijk van de gekozen techniek of tooling die wordt gebruikt en kan daarvoor geoptimaliseerd zijn.

### Design Philosophy

TODO: welke service design philosophy / (REST (hateoas), "RESTful", GraphQl, SOAP, ...) past bij welke behoefte.

<aside class="note">
  Waarschijnlijk een algemener hoofdstuk nodig. Maar, heeft wel raakvlakken met informatiearchitectuur.
</aside>

#### REST

Representational state transfer (REST) is een architectuurstijl met een set aan beperkingen om webservices te ontwerpen. De voordelen hiervan zijn:
* Snellere koppelingen
* Makkelijker om te beheren
* Er hoeven minder gegevens te worden verzonden
* Onafhankelijk van soort gegevens

De principes van REST zijn:
* **Client-server** - Door de gebruikersinterface en gegevensopslag te scheiden ontstaat er flexibiliteit voor gebruikersinterfaces, platformen en schaalbaarheid.
* **Stateless** - Elk verzoek van de cliënt naar de server bevat alle informatie die nodig is om het request af te handelen. Er kan geen gebruik worden gemaakt van informatie die op de server is opgeslagen
* **Mogelijkheid tot cache** - Het goed inrichten van de cache leidt tot een vermindering van het aantal gegevensverzoeken
* **Gelaagd systeem** - Het is voor de cliënt niet mogelijk om te herleiden met welke laag van de server er wordt gecommuniceerd. Hierdoor wordt de schaalbaarheid en beveiliging verbeterd.
* **Uniforme interface** - Deze valt uiteen in vier verschillende deelprincipes
  * De gegevens op de server staan conceptueel los van hoe zij worden gepresenteerd via de API
  * De opgevraagde (meta-)gegevens door de cliënt zijn voldoende om de staat van de bron aan te passen of te verwijderen
  * Elk bericht bevat voldoende informatie over hoe het bericht moet worden verwerkt
  * "Hypermedia as the engine of application state" (HATEOAS) is toegepast. Op basis van de gegevens van het respons kan de cliënt doorlinken naar andere gegevens. Door naast de gegevens ook een link mee te sturen van de bron kan een computer zelfstandig verkennen welke informatie beschikbaar is.

<aside class="issue">
Referenties nodig
</aside>

### Aanbevelingen
TODO introductie. Koppelen aan typologie.

#### System API

**Aanbeveling 1: Verbindt het resource ontwerp met een bestaand informatiemodel**
Bij het aanbieden van data via een System API is het van groot belang om de verbinding met een informatiemodel te hebben en deze verbinding ook te beschrijven en te publiceren bij de API documentatie.
* Dit bevordert begrip bij afnemers, zodat zij de ruwe data goed kunnen interpreteren.
* Dit houdt de API beheersbaar en zorgt dat de API gemakkelijker kan mee-evolueren met het informatielandschap
* Wanneer een System API informatie uit een stelsel van gegevensbronnen ontsluit bevordert dit interoperabiliteit in het stelsel.

Het directe informatiemodel van een System API is een (of mogelijk meerdere, afhankelijk van uitwisselformaat) niveau 4 informatiemodel(len). Het is namelijk een model van de uitwisseling van gegevens in een concreet uitwisselformaat. Een voorbeeld hiervan is een OAS document. [OAS TODO].
Het is echter belangrijk om de verbinding met een niveau 3 model uit te drukken, omdat dit een implementatie-onafhankelijk model is, wat begrip, maar ook interoperabiliteit, bevordert. Het bevordert interoperabiliteit met andere niveau 3 informatiemodellen in een stelsel, maar biedt ook één overkoepelend informatiemodel wanneer er sprake is van gegevensuitwisseling conform verschillende niveau 4 informatiemodellen gebaseerd op hetzelfde niveau 3 informatiemodel.

#### Convenience API
TODO

#### Proces API
TODO