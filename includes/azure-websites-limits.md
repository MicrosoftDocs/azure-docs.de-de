---
author: rothja
ms.service: app-service
ms.topic: include
ms.date: 03/04/2020
ms.author: jroth
ms.openlocfilehash: 800dc50f82fa47228f1a88a143c5b515168812a6
ms.sourcegitcommit: 3e8058f0c075f8ce34a6da8db92ae006cc64151a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92755583"
---
| Resource | Kostenlos | Shared | Basic | Standard | Premium (v3) | Isolated </th> |
| --- | --- | --- | --- | --- | --- | --- |
| [Web-Apps, mobile Apps oder API-Apps](https://azure.microsoft.com/services/app-service/) pro [Azure App Service-Plan](../articles/app-service/overview-hosting-plans.md)<sup>1</sup> |10 |100 |Unbegrenzt<sup>2</sup> |Unbegrenzt<sup>2</sup> |Unbegrenzt<sup>2</sup> |Unbegrenzt<sup>2</sup>|
| [App Service-Plan](../articles/app-service/overview-hosting-plans.md) |10 pro Region |10 pro Ressourcengruppe |100 pro Ressourcengruppe |100 pro Ressourcengruppe |100 pro Ressourcengruppe |100 pro Ressourcengruppe|
| Serverinstanztyp |Shared |Shared |Dediziert<sup>3</sup> |Dediziert<sup>3</sup> |Dediziert<sup>3</sup></p> |Dediziert<sup>3</sup>|
| [Aufskalieren](../articles/app-service/manage-scale-up.md) (Instanzen maximal) |1 freigegebene |1 freigegebene |3 dedizierte<sup>3</sup> |10 dedizierte<sup>3</sup> |30 dedizierte<sup>3</sup>|100 dedizierte<sup>4</sup>|
| Speicher<sup>5</sup> |1 GB<sup>5</sup> |1 GB<sup>5</sup> |10 GB<sup>5</sup> |50 GB<sup>5</sup> |250 GB<sup>5</sup> <br/><br/> Wenn Sie mehr als 250 GB benötigen, erstellen Sie eine Supportanfrage. |1 TB<sup>5</sup> <br/><br/> Das verfügbare Speicherkontingent beträgt 999 GB. |
| CPU-Zeit (5 Minuten)<sup>6</sup> |3 Minuten |3 Minuten |Unbegrenzt, normal zu [bezahlen](https://azure.microsoft.com/pricing/details/app-service/)</a> |Unbegrenzt, normal zu [bezahlen](https://azure.microsoft.com/pricing/details/app-service/)</a> |Unbegrenzt, normal zu [bezahlen](https://azure.microsoft.com/pricing/details/app-service/)</a> |Unbegrenzt, normal zu [bezahlen](https://azure.microsoft.com/pricing/details/app-service/)</a>|
| CPU-Zeit (Tag)<sup>6</sup> |60 Minuten |240 Minuten |Unbegrenzt, normal zu [bezahlen](https://azure.microsoft.com/pricing/details/app-service/)</a> |Unbegrenzt, normal zu [bezahlen](https://azure.microsoft.com/pricing/details/app-service/)</a> |Unbegrenzt, normal zu [bezahlen](https://azure.microsoft.com/pricing/details/app-service/)</a> |Unbegrenzt, normal zu [bezahlen](https://azure.microsoft.com/pricing/details/app-service/)</a> |
| Arbeitsspeicher (1 Stunde) |1\.024 MB pro App Service-Plan |1\.024 MB pro App |– |– |– |– |
| Bandbreite |165 MB |Unbegrenzt, Preise für [Dateiübertragungen](https://azure.microsoft.com/pricing/details/data-transfers/) gelten |Unbegrenzt, Preise für [Dateiübertragungen](https://azure.microsoft.com/pricing/details/data-transfers/) gelten |Unbegrenzt, Preise für [Dateiübertragungen](https://azure.microsoft.com/pricing/details/data-transfers/) gelten |Unbegrenzt, Preise für [Dateiübertragungen](https://azure.microsoft.com/pricing/details/data-transfers/) gelten |Unbegrenzt, Preise für [Dateiübertragungen](https://azure.microsoft.com/pricing/details/data-transfers/) gelten |
| Anwendungsarchitektur |32 Bit |32 Bit |32 Bit/64 Bit |32 Bit/64 Bit |32 Bit/64 Bit |32 Bit/64 Bit |
| Websockets pro Instanz<sup>7</sup> |5 |35 |350 |Unbegrenzt |Unbegrenzt |Unbegrenzt |
| IP-Verbindungen | 600 | 600 | Hängt von der Instanzgröße ab<sup>8</sup> | Hängt von der Instanzgröße ab<sup>8</sup> | Hängt von der Instanzgröße ab<sup>8</sup> | 16.000 |
| Gleichzeitige [Debuggerverbindungen](../articles/app-service/troubleshoot-dotnet-visual-studio.md) pro Anwendung |1 |1 |1 |5 |5 |5 |
| App Service-Zertifikate pro Abonnement<sup>9</sup>| Nicht unterstützt | Nicht unterstützt |10 |10 |10 |10 |
| Benutzerdefinierte Domänen pro App</a> |0 (nur Unterdomäne azurewebsites.net)|500 |500 |500 |500 |500 |
| benutzerdefinierte Domäne [SSL-Unterstützung](../articles/app-service/configure-ssl-certificate.md) |Nicht unterstützt, Platzhalterzertifikat für „\*.azurewebsites.net“ standardmäßig verfügbar|Nicht unterstützt, Platzhalterzertifikat für „\*.azurewebsites.net“ standardmäßig verfügbar|Unbegrenzte Anzahl von SNI SSL-Verbindungen |Unbegrenzte Anzahl von SNI SSL-Verbindungen und 1 IP-SSL-Verbindung inbegriffen |Unbegrenzte Anzahl von SNI SSL-Verbindungen und 1 IP-SSL-Verbindung inbegriffen | Unbegrenzte Anzahl von SNI SSL-Verbindungen und 1 IP-SSL-Verbindung inbegriffen|
| Hybridverbindungen | | | 5 pro Plan | 25 pro Plan | 200 pro App | 200 pro App |
| [Virtual Network-Integration](../articles/app-service/web-sites-integrate-with-vnet.md) | | |   |  X |  X  |  X  |
| Integrierter Lastenausgleich | |X |X |X |X |X<sup>10</sup> |
| [Zugriffsbeschränkungen](../articles/app-service/networking-features.md#access-restrictions) | 512 Regeln pro App | 512 Regeln pro App | 512 Regeln pro App | 512 Regeln pro App | 512 Regeln pro App | 512 Regeln pro App |
| [Always On](../articles/app-service/configure-common.md) | | |X |X |X |X |
| [Geplante Sicherungen](../articles/app-service/manage-backup.md) | | | | Geplante Sicherungen alle 2 Stunden, höchstens 12 Sicherungen pro Tag (manuell + geplant) | Geplante Sicherungen jede Stunde, höchstens 50 Sicherungen pro Tag (manuell + geplant) | Geplante Sicherungen jede Stunde, höchstens 50 Sicherungen pro Tag (manuell + geplant) |
| [Automatische Skalierung](../articles/app-service/manage-scale-up.md) | | | |X |X |X |
| [WebJobs](../articles/app-service/webjobs-create.md)<sup>11</sup> |X |X |X |X |X |X |
| [Endpunktüberwachung](../articles/app-service/web-sites-monitor.md) | | |X |X |X |X |
| [Stagingslots](../articles/app-service/deploy-staging-slots.md) pro App| | | |5 |20 |20 |
| [Test-in-Produktion](../articles/app-service/deploy-staging-slots.md#route-traffic)| | | |X |X |X |
| [Diagnoseprotokolle](../articles/app-service/troubleshoot-diagnostic-logs.md) | X | X | X | X | X | X |
| Kudu | X | X | X | X | X | X |
| [Authentifizierung und Autorisierung](../articles/app-service/overview-authentication-authorization.md) | X | X | X | X | X | X |
| [Von App Service verwaltete Zertifikate (Public Preview)](https://azure.microsoft.com/updates/secure-your-custom-domains-at-no-cost-with-app-service-managed-certificates-preview/)<sup>12</sup> | |  | X | X | X | X |
| SLA | |  |99,95 %|99,95 %|99,95 %|99,95 %|  

<sup>1</sup>Sofern nicht anderweitig vermerkt, gelten Apps- und Speicherkontingente gemäß dem App Service-Plan.  
<sup>2</sup>Die tatsächliche Anzahl der Apps, die Sie auf diesen Computern hosten können, hängt von der Aktivität der Apps, der Größe der Computerinstanzen und der entsprechenden Ressourcenauslastung ab.  
<sup>3</sup>Dedizierte Instanzen können unterschiedliche Größen aufweisen. Weitere Informationen finden Sie unter [App Service – Preise](https://azure.microsoft.com/pricing/details/app-service/).  
<sup>4</sup> Weitere sind auf Anfrage möglich.  
<sup>5</sup>Die Speicherbegrenzung ist die gesamte Inhaltsgröße aller Apps im selben App Service-Plan. Die gesamte Inhaltsgröße aller Apps für alle App Service-Pläne in einer einzelnen Ressourcengruppe und Region kann 500 GB nicht überschreiten.  
<sup>6</sup>Diese Ressourcen werden durch physische Ressourcen auf den dedizierten Instanzen (die Instanzengröße und die Anzahl der Instanzen) beschränkt.  
<sup>7</sup>Wenn Sie eine App im Basic-Tarif auf zwei Instanzen skalieren, stehen Ihnen 350 gleichzeitige Verbindungen für jede der beiden Instanzen zur Verfügung. Beim Tarif „Standard“ und höheren Tarifen gelten keine theoretischen Grenzwerte für Websockets, die Anzahl von Websockets kann jedoch durch andere Faktoren eingeschränkt sein. Die maximal zulässige Anzahl von gleichzeitigen Anforderungen (definiert durch `maxConcurrentRequestsPerCpu`) lautet wie folgt: 7.500 pro kleiner VM, 15.000 pro mittlerer VM (7.500 × 2 Kerne) und 75.000 pro großer VM (18.750 × 4 Kerne).  
<sup>8</sup>Die maximalen IP-Verbindungen sind pro Instanz und abhängig von der Instanzgröße: 1.920 pro B1/S1/P1V3-Instanz, 3.968 pro B2/S2/P2V3-Instanz, 8.064 pro B3/S3/P3V3-Instanz.  
<sup>9</sup>Die App Service Certificate-Kontingentgrenze pro Abonnement kann über eine Supportanfrage bis auf eine Obergrenze von 200 erhöht werden.  
<sup>10</sup>Für SKUs vom Typ „App Service (isoliert)“ kann ein interner Lastausgleich (Internal Load Balancing, ILB) mit Azure Load Balancer vorgenommen werden, sodass keine öffentliche Verbindung mit dem Internet erforderlich ist. Aus diesem Grund müssen einige Features eines isolierten App Service mit internem Lastausgleich auf Computern ausgeführt werden, die über Direktzugriff auf den Endpunkt des ILB-Netzwerks verfügen.  
<sup>11</sup>Sie können benutzerdefinierte ausführbare Dateien und/oder Skripts bei Bedarf, nach Zeitplan oder fortwährend als Hintergrundaufgabe in Ihrer App Service-Instanz ausführen. Für die fortlaufende WebJobs-Ausführung ist „Immer bereit“ erforderlich. Es gibt keine vordefinierte Grenze für die Anzahl der WebJobs, die in einer App Service-Instanz ausgeführt werden können. Es gibt praktische Grenzwerte, die davon abhängen, was der Anwendungscode auszuführen versucht.

<sup>12</sup>Domänen ohne das Präfix „www“ werden nicht unterstützt. Es werden nur Standardzertifikate ausgegeben (Platzhalterzertifikate sind nicht verfügbar). Nur auf ein kostenloses Zertifikat pro benutzerdefinierter Domäne beschränkt.
