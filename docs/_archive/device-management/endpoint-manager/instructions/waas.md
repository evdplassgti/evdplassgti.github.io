# Windows As A Service

## Quality and Feature updates

With the release of Windows 10, Microsoft introduced a new strategy to update and upgrade the operating system during its lifecycle. Before Windows 10, security updates and fixes where offered as separate update packages from which IT Admins could choose which of them were deployed and which were not. Additionally, when devices were not updated for a longer period, for instance when newly deployed from an outdated custom image, a long list of Windows updates must be additionally installed and sometimes with several reboots in between. Windows 1o introduced Quality updates which are inclusive and cumulative, meaning only one update package per month, without options to skip one or more fixes, and the latest Quality update package includes all previous updates (when still relevant). This strategy has the following important advantages:

* All devices with the same patch level are equally configured. This minimizes the risk for issues caused by missing updates or devices configured differently.
* For IT admins and end-users, the strategy is more simple and less time consuming, having to install only one single update package to update the OS with the latest fixes and security updates.

Next to patches or fixes that improve Windows 10 quality, Microsoft also releases so called 'Feature Updates'. These upgrades contain functional improvements and new features that previous would be offered through a new Windows version or Service Pack (Windows XP -> Windows XP SP1 -> Windows XP SP2 -> Windows Vista -> Windows 7 -> Windows 8). Contrary to the approach for previous Windows versions, Feature updates are released more frequently (2 times a year instead of once per 3 or 4 years) and are typically smaller in size. Users can benefit from improvements and new features earlier while the impact and change is smaller then before. Implementation of Feature Updates therefore is quicker and easier to adopt. Note that from the beginning of Feature Update 21H2, feature updates are released annually instead of semi-annual, in the second half of the calendar year. After release, the feature update is supported for 18 months (Pro) or 30 months (Enterprise).

With the update and upgrade strategy for Windows 10, updates are more frequent and smaller in size (Feature update) and using cumulative Quality updates, easier to deploy. Both contribute to stability and adoption of improvements and new features. Just what you can expect from a service, hence Windows as a Service.

## Windows 11

With the release of Windows 10, Microsoft announced there would be no new Windows version released in the future. This 'promise' is not kept as Microsoft released Windows 11 in the fall of 2021. The update strategy for Windows 11 is similar to what we now have for Windows 10: monthly Quality updates and one, single Feature update in the second half of the calendar year. However, the feature update is supported for 24 months (Pro) or 36 months (Enterprise). The upgrade path from Windows 10 to Windows 11 is very much like a feature update for Windows 10 (inplace upgrade), both in user experience as technical approach. Windows 10 is maintained side by side with Windows 11 and is supported till the end of 2025.  

## Deployment of updates

Sogeti Smart Workspace makes use of Windows Update for Business. Updates are downloaded using the Windows Update Service over the internet at the time of release and automatically installed on end user devices. Before an update is released for production by Microsoft, the update is tested thoroughly by Microsoft internally and the Windows Insider community. Feedback and experiences are used to improve the quality and stability before updates are general available. Customers can choose between the following update channels that differ in release moment and support terms. The choice for an update channel is per device and set once so subsequent updates are offered from the selected channel. At this moment in time, the following channels exist, for both Windows 10 and Windows 11:

**Insider channel**. Windows 10 devices ontvangen pre-releases van nieuwe Windows 10 builds. Issues en feedback kunnen daarmee vroegtijdig worden opgemerkt en verzameld met als doel de uiteindelijke productie versie te verbeteren. Het insider channel is primair bedoeld voor test omgevingen, niet voor productie. Zogenaamde early adopters kunnen ervoor kiezen hun device met deze versie in te (laten) richten maar dan voor niet kritische systemen met geen of geringe impact op de bedrijfsvoering wanneer er iets fout gaat. Dit channel ontvangt in de regel frequenter (vaker dan 2 keer per jaar) nieuwe releases.

**Semi-annual channel**. Devices in dit channel ontvangen updates wanneer ze door Microsoft als productie rijpe versie worden vrijgegeven, normaliter tweemaal per jaar. Veruit de meeste computers zullen geconfigureerd worden om gebruik te maken van dit channel.

•	Long Term service release. Dit channel is bedoelt voor bedrijfskritische systemen met een grote impact op de bedrijfsvoering wanneer een upgrade issues veroorzaakt. Voor deze versie worden geen Feature Upgrades uitgegeven maar wel de maandelijkse Quality updates. Eens in de twee tot drie jaar verschijnt een nieuwe versie voor dit channel die met een in-place upgrade kan worden doorgevoerd.  Deze versie heeft tot 10 jaar ondersteuning van Microsoft. Het lijkt aantrekkelijk om deze release te gebruiken omdat er minder (grote) wijzigingen worden aangeboden. Sogeti adviseert in de Smart Workspace oplossing terughoudend te zijn met deze keuze omdat die vernieuwing tegen gaat. Daarnaast biedt deze versie minder functionaliteit, bijvoorbeeld door het ontbreken van ondersteuning van applicaties uit de Microsoft Store (de zogenaamde modern apps). 

Om de impact voor de organisatie te verkleinen, wordt de installatie van updates binnen de organisatie gespreid doorgevoerd. Dat geschiedt met zogenaamde Update Rings. Een Update Ring is een verzameling devices waarvoor naast het service channel het moment en gedrag voor installatie is geconfigureerd. Standaard worden meerdere Update Rings gedefinieerd om zo de installatie van Quality Updates en Feature Upgrades te kunnen spreiden. De eigenschappen voor deze update rings zijn binnen Smart Workspace gelijk, met uitzondering van het moment van installatie. 
Binnen Windows Update for Buisness kan ervoor worden gekozen om het moment van installatie na release uit te stellen, dit wordt ‘deferal’ genoemd. Hoewel het mogelijk is om ook de installatie van Quality Updates uit te stellen, wordt dit binnen Smart Workspace in geen van de update rings toegepast. De praktijk leert dat issues met Quality Updates uitzonderlijk zijn. De verwachtte schade door uitstel van een update, door een vulnerability, schatten we groter in dan die veroorzaakt door issues met de update zelf. 
De volgende drie update rings worden geconfigureerd, allen gebruik makend van het Semi-Annual Channel voor Windows Updates:

•	Pilot : Computers in deze update ring krijgen zowel Quality als Feature Updates direct na release aangeboden door de deferal voor beiden op 0 dagen in te stellen.
•	Test : Computers in deze update ring krijgen Quality Updates direct na release en Feature Updates 15 dagen na release. Dit geeft de organisatie 15 dagen de tijd om eerst in de pilot groep te testen.
•	Productie : Computers in deze update ring krijgen Quality Updates direct na release en Feature Updates 30 dagen na release. Dit geeft de organisatie 30 dagen de tijd om eerst in de pilot groep te testen, waarvan de laatste 15 dagen tevens in de test groep.

Wanneer het fout gaat
In uitzonderlijke gevallen kan het voorkomen dat er issues optreden bij het doorvoeren van updates. Dat zien we dan met name bij Feature Updates. Op dat moment kan aanvullend worden ingegrepen door de installatie van Quality Updates of Feature Updates te pauzeren. Deze pauzering is niet afhankelijk van de deferal en treedt in nadat de computer de pauze instelling heeft ontvangen. De pauzering wordt automatisch na 35 dagen opgeheven, maar kan zowel worden verlengd als eerder worden opgeheven. De pauzering van updates wordt per update ring afzonderlijk geactiveerd of opgeheven.

Optimalisatie van de download
Afhankelijk van het type van de update, Quality Updates of Feature Updates, kan de grootte van de updates oplopen van enkele megabytes tot gigabytes. Om bandbreedte gebruik te verminderen, beschikt Windows 10 over mogelijkheden om de download te optimaliseren, de Delivery Optimization service. Het is nog steeds nodig dat er verbinding is met het internet, maar reeds gedownloade updates zijn via een lokale cache op het device beschikbaar voor andere computers in het netwerk. Het is dan niet nodig dat iedere computer de updates afzonderlijk gaat downloaden. Eigenschappen van deze cache en beschikbare bandbreedte wordt centraal in Smart Workspace geconfigureerd, afhankelijk van het netwerk bij de klant en in overeenstemming met de netwerk specialisten van de klant. Naast Windows Updates, maken ook de applicatie installatie van applicaties via Microsoft Endpoint Manager (Intune) en het automatisch update mechanisme van Office 365 applicaties gebruik van Delivery Optimization.

Aanvullende updates
Naast updates voor het Windows 10 operating system, kan worden geconfigureerd dat ook updates van drivers en andere Microsoft toepassingen worden aangeboden. In de standaard Smart Workspace configuratie worden beide opties geactiveerd.

Updates voor Edge (Chromium) en Office 365 Apps
Zowel de nieuwe, modern versie van Edge als de Office 365 apps maken, net als Windows 10, gebruik van verschillende update channels waarin verschillende versies worden aangeboden. Hoewel de Office 365 apps strikt genomen geen onderdeel uitmaken van het operating systeem, zijn ze normaal gesproken opgenomen in de standaard werkplek. Omdat ook het update mechanisme sterk overeenkomt met die van Windows 10, worden ze in dit informatieblad beschreven. De lifecycle voor overige applicaties is beschreven in het informatieblad ‘Application Lifecycle’.

Voor Edge bestaan de volgende channels:
Channel	Toepassing	Update frequentie
Stable 	Generiek gebruik / productie (SSW standaard)	Eens per 6 weken
Beta	Pre-release om te testen binnen de organisatie	Eens per 6 weken
Dev	Voor planning en ontwikkeling, met name wanneer in-house web applicaties worden gebouwd en onderhouden. Deze versie biedt geen support.	Wekelijks
Canary	Experimenteel, niet geteste of ondersteunde versie	Dagelijks

Het update channel wordt ingesteld bij de installatie van Edge, in Smart Workspace door middel van een installatie package in Endpoint Manager (Intune). De standaard voor generieke gebruikers is de Stable release. Na installatie wordt Edge automatisch bijgewerkt. Daarvoor is geen configuratie of actie nodig. Wanneer naast het Stable channel ook andere channels worden gebruikt, dan zijn daarvoor aanvullende packages in Intune nodig. De groepen in de update rings kunnen gebruikt worden voor verschillende versies van Edge, bijvoorbeeld het aanbieden van de Beta release aan de pilot en evt test gebruikers. Gebruik van Dev of Canary raden we af voor gebruik in productie omdat dit vroege versies betreft die Microsoft ook niet ondersteund. De Dev versie kan eventueel wel worden toegepast voor (web-)ontwikkelaars.

Voor Office 365 bestaan de volgende channels:
Channel	Toepassing	Update frequentie
Current Channel	Gebruik van nieuwe Office features, op het moment dat ze beschikbaar komen	Minimaal eens per maand, in de praktijk meerder keren per maand
Monthly Channel	Eens per maand voorzien van nieuwe Office features op een voorspelbaar moment (patch Tuesday)	Eens per maand, op de tweede dinsdag van de maand
Semi-Annual Enterprise Channel	Voor Enterprise organisaties waar een verstoring of wijziging van Office een grote impact kan hebben (SSW standaard)	Security en nodige updates eens per maand, op de tweede dinsdag van de maand. Nieuwe features worden twee keer per jaar aangeboden.

De keuze voor het update channel is sterk afhankelijk van de organisatie en het gebruik van Office 365 binnen de organisatie. Security updates en fixes worden voor alle versies maandelijks uitgebracht en toegepast. De keuze voor een channel heeft met name impact op de beschikbare features / nieuwe functionaliteit. 
Wanneer gebruikers zo snel mogelijk van nieuwe features gebruik willen maken, dan is het Current Channel de beste keuze. Voor meer voorspelbaarheid en stabiliteit kan beter gekozen worden voor het Monthly Enterprise channel. Tot slot, het Semi-Annual channel biedt de beste mogelijkheid om nieuwe versies eerst goed te testen voor ze breed beschikbaar worden gesteld binnen de organisatie. Omdat dit de meest ‘veilige’ optie is, is dit de standaard keuze in Smart Workspace. De best passende keuze wordt altijd in overeenstemming met de organisatie gemaakt.

Het gebruikte update channel wordt bij de installatie van Office ingesteld. Wijziging is mogelijk maar vereist een herinstallatie / configuratie van Office, mogelijk gecombineerd met een downgrade - wanneer het update channel naar van Current of Monthly naar resp. Monthly of Semi-Annual wordt aangepast - of een upgrade wanneer de omgekeerde weg wordt behandeld. 

Worden alle drie de channels binnen de organisatie gebruikt, dan zijn ook drie installatie packages voor Office benodigd. Deze worden in Microsoft Endpoint Manager (Intune) geconfigureerd maar vereisen geen afzonderlijke packaging activiteiten. Voor deployment kunnen dezelde groepen worden gebruikt die voor de spreiding van Windows Updates worden gebruikt. Wordt gebruik gemaakt van het applicatie portaal van Smart Workspace, dan kan een optie worden aangeboden om gebruikers op een gecontroleerde wijze te laten omschakelen naar een ander update channel. Let er dan wel op dat de controle over ‘Wie heeft Wat’ dan lastiger wordt.

Gebruikers ervaring
Windows zal automatisch controleren of er updates beschikbaar zijn. Dat gebeurt bij het opstarten en daarna periodiek. Wanneer updates beschikbaar zijn – dat is afhankelijk van de instellingen voor uitstel van updates – dan worden deze op de achtergrond automatisch gedownload en geinstalleerd. De gebruiker hoeft hierop geen actie op te ondernemen en het update proces gebruikt de laagste prioriteit om de productiviteit zo min mogelijk te beinvloeden.

 
Wanneer de updates zijn geinstalleerd, krijgt de gebruiker een popup waarin wordt gemeld dat er updates beschikbaar zijn en de vraag om de computer te herstarten om de updates te verwerken. Omdat dit niet perse een geschikt moment hoeft te zijn, heeft de gebruiker de mogelijkheid om de herstart uit te stellen, in de Smart Workspace configuratie tot maximaal 7 dagen. De gebruiker kan ook een moment kiezen waarop de computer automatisch opnieuw start om de update af te ronden. Wanneer de gebruiker de computer in deze periode niet heeft herstart, dan zal de computer automatisch worden opgestart. De gebruiker krijgt hierover een melding. Deze functionaliteit wordt ‘Engaged Restart’ genoemd.

Bij het afsluiten en opnieuw starten van de computer worden de updates daadwerkelijk toegepast. In de regel duurt dit enkele minuten voor Quality Updates. Het verwerkenvan de Feature Update, na download en pre-installatie, duurt in de regel 15-30 minuten. Met name voor de laatste is dat sterk afhankelijk van de leeftijd en aard van de computer.

Het bijwerken van zowel Office als Edge heeft geen merkbare impact voor de gebruiker. Beiden worden automatisch en op de achtergrond bijgewerkt. Met name bij het gebruik van het Current Channel voor de Office 365 apps kan dit tot gevolg hebben dat gebruikers nieuwe features als ‘plotselinge’ wijzigingen ervaren. Goede communicatie en adoptie is daarom noodzakelijk.




