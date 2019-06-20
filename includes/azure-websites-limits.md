---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: ce64047fd7490106790ea8bb1ad7963d82a87c24
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178468"
---
| Resource | Kostenlos | Shared | Basic | Standard | Premium (v2) | Isolated </th> |
| --- | --- | --- | --- | --- | --- | --- |
| [Web-Apps, mobile Apps oder API-Apps](https://azure.microsoft.com/services/app-service/) pro [Azure App Service-Plan](../articles/app-service/overview-hosting-plans.md)<sup>1</sup> |10 |100 |Unbegrenzt<sup>2</sup> |Unbegrenzt<sup>2</sup> |Unbegrenzt<sup>2</sup> |Unbegrenzt<sup>2</sup>|
| [App Service-Plan](../articles/app-service/overview-hosting-plans.md) |10 pro Region |10 pro Ressourcengruppe |100 pro Ressourcengruppe |100 pro Ressourcengruppe |100 pro Ressourcengruppe |100 pro Ressourcengruppe|
| Serverinstanztyp |Shared |Shared |Dediziert<sup>3</sup> |Dediziert<sup>3</sup> |Dediziert<sup>3</sup></p> |Dediziert<sup>3</sup>|
| [Horizontales Hochskalieren](../articles/app-service/web-sites-scale.md) (Instanzen maximal) |1 freigegebene |1 freigegebene |3 dedizierte<sup>3</sup> |10 dedizierte<sup>3</sup> |20 dedizierte<sup>3</sup>|100 dedizierte<sup>4</sup>|
| Speicher<sup>5</sup> |1 GB<sup>5</sup> |1 GB<sup>5</sup> |10 GB<sup>5</sup> |50 GB<sup>5</sup> |250 GB<sup>5</sup></p> |1 TB<sup>5</sup>|
| CPU-Zeit (5 Minuten)<sup>6</sup> |3 Minuten |3 Minuten |Unbegrenzt, normal zu [bezahlen](https://azure.microsoft.com/pricing/details/app-service/)</a> |Unbegrenzt, normal zu [bezahlen](https://azure.microsoft.com/pricing/details/app-service/)</a> |Unbegrenzt, normal zu [bezahlen](https://azure.microsoft.com/pricing/details/app-service/)</a> |Unbegrenzt, normal zu [bezahlen](https://azure.microsoft.com/pricing/details/app-service/)</a>|
| CPU-Zeit (Tag)<sup>6</sup> |60 Minuten |240 Minuten |Unbegrenzt, normal zu [bezahlen](https://azure.microsoft.com/pricing/details/app-service/)</a> |Unbegrenzt, normal zu [bezahlen](https://azure.microsoft.com/pricing/details/app-service/)</a> |Unbegrenzt, normal zu [bezahlen](https://azure.microsoft.com/pricing/details/app-service/)</a> |Unbegrenzt, normal zu [bezahlen](https://azure.microsoft.com/pricing/details/app-service/)</a> |
| Arbeitsspeicher (1 Stunde) |1\.024 MB pro App Service-Plan |1\.024 MB pro App |– |– |– |– |
| Bandbreite |165 MB |Unbegrenzt, Preise für [Dateiübertragungen](https://azure.microsoft.com/pricing/details/data-transfers/) gelten |Unbegrenzt, Preise für [Dateiübertragungen](https://azure.microsoft.com/pricing/details/data-transfers/) gelten |Unbegrenzt, Preise für [Dateiübertragungen](https://azure.microsoft.com/pricing/details/data-transfers/) gelten |Unbegrenzt, Preise für [Dateiübertragungen](https://azure.microsoft.com/pricing/details/data-transfers/) gelten |Unbegrenzt, Preise für [Dateiübertragungen](https://azure.microsoft.com/pricing/details/data-transfers/) gelten |
| Anwendungsarchitektur |32 Bit |32 Bit |32 Bit/64 Bit |32 Bit/64 Bit |32 Bit/64 Bit |32 Bit/64 Bit |
| Websockets pro Instanz<sup>7</sup> |5 |35 |350 |Unbegrenzt |Unbegrenzt |Unbegrenzt |
| Gleichzeitige [Debuggerverbindungen](../articles/app-service/troubleshoot-dotnet-visual-studio.md) pro Anwendung |1 |1 |1 |5 |5 |5 |
| App Service-Zertifikate pro Abonnement<sup>10</sup>| Nicht unterstützt | Nicht unterstützt |10 |10 |10 |10 |
| Benutzerdefinierte Domänen pro App</a> |0 (nur Unterdomäne azurewebsites.net)|500 |500 |500 |500 |500 |
| benutzerdefinierte Domäne [SSL-Unterstützung](../articles/app-service/app-service-web-tutorial-custom-ssl.md) |Nicht unterstützt, Platzhalterzertifikat für „*.azurewebsites.net“ standardmäßig verfügbar|Nicht unterstützt, Platzhalterzertifikat für „*.azurewebsites.net“ standardmäßig verfügbar|Unbegrenzte Anzahl von SNI SSL-Verbindungen |Unbegrenzte Anzahl von SNI SSL-Verbindungen und 1 IP-SSL-Verbindung inbegriffen |Unbegrenzte Anzahl von SNI SSL-Verbindungen und 1 IP-SSL-Verbindung inbegriffen | Unbegrenzte Anzahl von SNI SSL-Verbindungen und 1 IP-SSL-Verbindung inbegriffen|
| Integrierter Lastenausgleich | |X |X |X |X |X<sup>9</sup> |
| [Immer aktiviert](../articles/app-service/configure-common.md) | | |X |X |X |X |
| [Geplante Sicherungen](../articles/app-service/manage-backup.md) | | | | Geplante Sicherungen alle 2 Stunden, höchstens 12 Sicherungen pro Tag (manuell + geplant) | Geplante Sicherungen jede Stunde, höchstens 50 Sicherungen pro Tag (manuell + geplant) | Geplante Sicherungen jede Stunde, höchstens 50 Sicherungen pro Tag (manuell + geplant) |
| [Automatische Skalierung](../articles/app-service/web-sites-scale.md) | | | |X |X |X |
| [WebJobs](../articles/app-service/webjobs-create.md)<sup>8</sup> |X |X |X |X |X |X |
| [Azure Scheduler](https://azure.microsoft.com/services/scheduler/) -Unterstützung: | |X |X |X |X |X |
| [Endpunktüberwachung](../articles/app-service/web-sites-monitor.md) | | |X |X |X |X |
| [Stagingslots](../articles/app-service/deploy-staging-slots.md) | | | |5 |20 |20 |
| SLA | |  |99,9 % |99,95 %|99,95 %|99,95 %|  

<sup>1</sup>Sofern nicht anderweitig vermerkt, gelten Apps- und Speicherkontingente gemäß dem App Service-Plan.  
<sup>2</sup>Die tatsächliche Anzahl der Apps, die Sie auf diesen Computern hosten können, hängt von der Aktivität der Apps, der Größe der Computerinstanzen und der entsprechenden Ressourcenauslastung ab.  
<sup>3</sup>Dedizierte Instanzen können unterschiedliche Größen aufweisen. Weitere Informationen finden Sie unter [App Service – Preise](https://azure.microsoft.com/pricing/details/app-service/).  
<sup>4</sup> Weitere sind auf Anfrage möglich.  
<sup>5</sup>Die Speicherbegrenzung ist die gesamte Inhaltsgröße aller Apps im gleichen App Service-Plan.  
<sup>6</sup>Diese Ressourcen werden durch physische Ressourcen auf den dedizierten Instanzen (die Instanzengröße und die Anzahl der Instanzen) beschränkt.  
<sup>7</sup>Wenn Sie eine App im Basic-Tarif auf zwei Instanzen skalieren, stehen Ihnen 350 gleichzeitige Verbindungen für jede der beiden Instanzen zur Verfügung.  
<sup>8</sup>Sie können benutzerdefinierte ausführbare Dateien und/oder Skripts bei Bedarf, nach Zeitplan oder fortwährend als Hintergrundaufgabe in Ihrer App Service-Instanz ausführen. Für die fortlaufende WebJobs-Ausführung ist „Immer bereit“ erforderlich. Für geplante WebJobs ist Azure Scheduler Free oder Standard erforderlich. Es gibt keine vordefinierte Grenze für die Anzahl der WebJobs, die in einer App Service-Instanz ausgeführt werden können. Es gibt praktische Grenzwerte, die davon abhängen, was der Anwendungscode auszuführen versucht.  
<sup>9</sup> Für isolierte App Service-SKUs kann ein interner Lastausgleich (Internal Load Balancing, ILB) mit Azure Load Balancer durchgeführt werden, sodass keine öffentliche Verbindung mit dem Internet erforderlich ist. Aus diesem Grund müssen einige Features eines isolierten App Service mit internem Lastausgleich auf Computern ausgeführt werden, die über Direktzugriff auf den Endpunkt des ILB-Netzwerks verfügen.  
<sup>10</sup> Die App Service Certificate-Kontingentgrenze pro Abonnement kann über eine Supportanfrage bis auf eine Obergrenze von 200 erhöht werden.  