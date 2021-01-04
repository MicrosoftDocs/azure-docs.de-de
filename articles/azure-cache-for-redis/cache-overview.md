---
title: Was ist Azure Cache for Redis?
description: Azure Cache for Redis bietet Funktionen wie cachefremdes Muster, Content Caching, Zwischenspeicherung von Benutzersitzungen, Auftrags- und Nachrichtenwarteschlangen sowie verteilte Transaktionen.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: overview
ms.date: 05/12/2020
ms.openlocfilehash: 225df0dc53a0386bb53576970a1b6330351f4545
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96184126"
---
# <a name="azure-cache-for-redis"></a>Azure Cache for Redis
Azure Cache for Redis bietet einen auf der Software [Redis](https://redis.io/) basierenden In-Memory-Datenspeicher. Redis verbessert die Leistung und Skalierbarkeit einer Anwendung, die intensiven Gebrauch von Back-End-Datenspeichern macht. Mit Redis können große Mengen von Anwendungsanforderungen verarbeitet werden, indem häufig verwendete Daten im Arbeitsspeicher des Servers behalten werden, um schnelle Schreib- und Lesevorgänge zu ermöglichen. Redis stellt eine kritische Datenspeicherlösung mit geringer Wartezeit und hohem Durchsatz für moderne Anwendungen bereit.

Azure Cache for Redis bietet sowohl die Open-Source-Lösung Redis als auch ein kommerzielles Produkt von Redis Labs als verwalteten Dienst. Dadurch erhalten Sie sichere und dedizierte Redis-Serverinstanzen sowie vollständige Redis-API-Kompatibilität. Der Dienst wird von Microsoft betrieben und in Azure gehostet, und jede Anwendung innerhalb oder außerhalb von Azure kann darauf zugreifen.

Azure Cache for Redis kann unter anderem als Cache für verteilte Daten oder Inhalte, als Sitzungsspeicher oder als Nachrichtenbroker verwendet werden. Azure Cache for Redis kann als eigenständige Lösung oder parallel mit einem anderen Azure-Datenbankdienst wie etwa Azure SQL oder Cosmos DB bereitgestellt werden.

## <a name="key-scenarios"></a>Wichtige Szenarien
Azure Cache for Redis verbessert die Anwendungsleistung durch die Unterstützung gängiger Anwendungsarchitekturmuster. Nachfolgend sind einige der am häufigsten verwendeten Muster aufgeführt:

| Muster      | BESCHREIBUNG                                        |
| ------------ | -------------------------------------------------- |
| [Datencache](cache-web-app-cache-aside-leaderboard.md) | Datenbanken sind häufig zu groß, um sie direkt in einen Cache zu laden. Das [cachefremde](/azure/architecture/patterns/cache-aside) Muster wird üblicherweise verwendet, um Daten nur bei Bedarf in den Cache zu laden. Wenn das System Änderungen an den Daten vornimmt, kann es auch den Cache aktualisieren, der dann an andere Clients verteilt wird. Darüber hinaus kann das System ein Ablaufdatum für Daten festlegen oder eine Entfernungsrichtlinie verwenden, um Datenaktualisierungen im Cache auszulösen.|
| [Inhaltscache](cache-aspnet-output-cache-provider.md) | Viele Webseiten werden auf der Grundlage von Vorlagen mit statischen Inhalten wie Kopfzeilen, Fußzeilen und Bannern generiert. Diese statischen Elemente ändern sich in der Regel nur selten. Im Vergleich zu Back-End-Datenspeichern ermöglicht ein In-Memory-Cache einen schnelleren Zugriff auf statische Inhalte. Dieses Muster trägt zur Verringerung der Verarbeitungszeit und der Serverauslastung sowie zur Erhöhung der Reaktionsfähigkeit von Webservern bei. Dadurch benötigen Sie ggf. weniger Server, um die Last zu bewältigen. Zur Unterstützung dieses Musters mit ASP.NET bietet Azure Cache for Redis den Redis-Ausgabecacheanbieter.|
| [Sitzungsspeicher](cache-aspnet-session-state-provider.md) | Dieses Muster wird häufig bei Einkaufswagen und anderen Benutzerverlaufsdaten verwendet, die eine Webanwendung möglicherweise mit Benutzercookies verknüpfen möchte. Die Speicherung von zu vielen Inhalten in einem Cookie kann sich negativ auf die Leistung auswirken, weil die Cookiegröße zunimmt und mit jeder Anforderung übergeben und überprüft wird. Bei einer typischen Lösung wird das Cookie als Schlüssel zum Abfragen der Daten in einer Datenbank verwendet. Die Verwendung eines In-Memory-Cache wie Azure Cache for Redis, um einem Benutzer Informationen zuzuordnen, ist wesentlich schneller als die Interaktion mit einer vollständigen relationalen Datenbank. |
| Einreihen von Aufträgen in eine Warteschlange und Message Queuing | Anwendungen fügen Aufgaben häufig einer Warteschlange hinzu, wenn die Ausführung der mit der Anforderung verbundenen Vorgänge länger dauert. Zeitintensive Vorgänge werden einer Warteschlange hinzugefügt und der Reihe nach verarbeitet – nicht selten von einem anderen Server.  Diese Methode der Verzögerung von Arbeit wird als Aufgaben-Queuing bezeichnet. Azure Cache for Redis bietet eine verteilte Warteschlange, um dieses Muster in Ihrer Anwendung zu ermöglichen.|
| Verteilte Transaktionen | Anwendungen benötigen mitunter eine Reihe von Befehlen für einen Back-End-Datenspeicher, um einen einzelnen atomischen Vorgang auszuführen. Alle Befehle müssen erfolgreich ausgeführt oder jeweils mittels Rollback in den ursprünglichen Zustand zurückversetzt werden. Azure Cache for Redis unterstützt das Ausführen eines Batchs von Befehlen als einzelne [Transaktion](https://redis.io/topics/transactions). |

## <a name="redis-versions"></a>Redis-Versionen

Azure Cache for Redis unterstützt OSS Redis Version 4.x sowie Version 6.0 als Vorschau. Wir haben uns entschieden, Redis 5.0 zu überspringen, um Ihnen die neueste Version zu bieten. Zuvor hat Azure Cache for Redis nur eine einzelne Redis-Version bereitgestellt. Es wird ein neueres Hauptreleaseupgrade und mindestens eine ältere stabile Version bereitgestellt. Sie können [auswählen, welche Version](cache-how-to-version.md) am besten für Ihre Anwendung geeignet ist.


## <a name="service-tiers"></a>Dienstebenen
Azure Cache for Redis ist in folgenden Tarifen verfügbar:

| Tarif | BESCHREIBUNG |
|---|---|
| Basic | Eine OSS Redis Cache-Instanz, die auf einem einzelnen virtuellen Computer ausgeführt wird. Dieser Tarif verfügt über keine Vereinbarung zum Servicelevel (Service-Level Agreement, SLA) und ist ideal für Entwicklungs-/Testworkloads und nicht kritische Workloads. |
| Standard | Eine OSS Redis Cache-Instanz, die auf zwei virtuellen Computern in einer replizierten Konfiguration ausgeführt wird. |
| Premium | OSS Redis Cache-Hochleistungsinstanzen. Dieser Tarif bietet höheren Durchsatz, kürzere Wartezeiten, bessere Verfügbarkeit und mehr Features. Premium-Caches werden im Gegensatz zu Basic- und Standard-Caches auf leistungsfähigeren virtuellen Computern bereitgestellt. |
| Enterprise | Hochleistungscaches mit Redis Enterprise-Software von Redis Labs. Dieser Tarif unterstützt Redis-Module (einschließlich RediSearch, RedisBloom und RedisTimeSeries). Außerdem bietet er eine noch höhere Verfügbarkeit als der Premium-Tarif. |
| Enterprise Flash | Kostengünstige große Caches mit Redis Enterprise-Software von Redis Labs. Dieser Tarif erweitert Redis-Datenspeicher auf nicht flüchtigen Speicher auf einem virtuellen Computer. Dieser Speicher ist günstiger als DRAM. Dadurch verringern sich die Gesamtkosten pro GB Arbeitsspeicher. |

### <a name="feature-comparison"></a>Funktionsvergleiche
Unter [Azure Cache for Redis – Preise](https://azure.microsoft.com/pricing/details/cache/) finden Sie einen detaillierten Vergleich der einzelnen Tarife. In der folgenden Tabelle werden einige der unterstützten Features nach Tarif aufgeführt:

| Funktionsbeschreibung | Basic | Standard | Premium | Enterprise | Enterprise Flash |
| ------------------- | :-----: | :------: | :---: | :---: | :---: |
| [Vereinbarung zum Servicelevel (SLA)](https://azure.microsoft.com/support/legal/sla/cache/v1_0/) |-|✔|✔|✔|✔|
| Datenverschlüsselung |✔|✔|✔|✔|✔|
| [Netzwerkisolation](cache-how-to-premium-vnet.md) |✔|✔|✔|✔|✔|
| [Skalieren](cache-how-to-scale.md) |✔|✔|✔|✔|✔|
| [Zonenredundanz](cache-how-to-zone-redundancy.md) |-|-|✔|✔|✔|
| [Georeplikation](cache-how-to-geo-replication.md) |-|-|✔|-|-|
| [Datenpersistenz](cache-how-to-premium-persistence.md) |-|-|✔|-|-|
| [OSS-Cluster](cache-how-to-premium-clustering.md) |-|-|✔|✔|✔|
| [Module](https://redis.io/modules) |-|-|-|✔|-|
| [Import/Export](cache-how-to-import-export-data.md) |-|-|✔|✔|✔|
| [Geplante Updates](cache-administration.md#schedule-updates) |✔|✔|✔|-|-|

### <a name="choosing-the-right-tier"></a>Auswählen der richtigen Ebene
Berücksichtigen Sie bei der Tarifwahl für Azure Cache for Redis Folgendes:

* **Arbeitsspeicher**: Die Tarife „Basic“ und „Standard“ bieten zwischen 250 MB und 53 GB. Der Premium-Tarif bietet zwischen 6 GB und 1,2 TB. Die Enterprise-Tarife bieten zwischen 12 GB und 14 TB.  Wenn Sie einen Cache im Premium-Tarif erstellen möchten, der größer als 120 GB ist, können Sie das Redis-OSS-Clustering verwenden. Weitere Informationen finden Sie unter [Azure Cache for Redis – Preise](https://azure.microsoft.com/pricing/details/cache/). Weitere Informationen finden Sie unter [Konfigurieren von Clustern für Azure Cache for Redis vom Typ „Premium“](cache-how-to-premium-clustering.md).
* **Netzwerkleistung**: Für Workloads, die einen hohen Durchsatz erfordern, steht im Premium- oder Enterprise-Tarif eine höhere Bandbreite zur Verfügung als im Basic- oder Standard-Tarif. Zudem haben die größeren Caches aufgrund des zugrunde liegenden virtuellen Computers, der den Cache hostet, bei jedem Tarif eine höhere Bandbreite. Weitere Informationen finden Sie unter [Azure Cache for Redis – Leistung](cache-planning-faq.md#azure-cache-for-redis-performance).
* **Durchsatz**: Der Premium-Tarif bietet den maximal verfügbaren Durchsatz. Wenn Cacheserver oder -clients die Bandbreitengrenzwerte erreichen, können Timeouts auf der Clientseite auftreten. Ausführlichere Informationen finden Sie in der unten stehenden Tabelle.
* **Hochverfügbarkeit**: Azure Cache for Redis verfügt über mehrere Optionen für [Hochverfügbarkeit](cache-high-availability.md). Dadurch wird garantiert, dass ein Standard-, Premium- oder Enterprise-Cache gemäß unserer [SLA](https://azure.microsoft.com/support/legal/sla/cache/v1_0/) verfügbar ist. Die SLA deckt nur die Konnektivität zu den Cacheendpunkten ab. Sie bezieht sich dagegen nicht auf Schutz vor Datenverlusten. Es wird empfohlen, das Redis-Feature für Datenpersistenz im Premium-Tarif zu verwenden, um den Schutz vor Datenverlusten zu erhöhen.
* **Datenpersistenz**: Der Tarif "Premium" ermöglicht die Persistenz der Cachedaten in einem Azure Storage-Konto. In anderen Tarifen werden Daten nur im Arbeitsspeicher gespeichert. Probleme mit der zugrunde liegenden Infrastruktur können zu potenziellen Datenverlusten führen. Es wird empfohlen, das Redis-Feature für Datenpersistenz im Premium-Tarif zu verwenden, um den Schutz vor Datenverlusten zu erhöhen. Azure Cache for Redis bietet RDB- und AOF-Optionen (Vorschauversion) bei der Redis-Persistenz. Weitere Informationen finden Sie unter [Konfigurieren von Persistenz für Azure Cache for Redis vom Typ „Premium“](cache-how-to-premium-persistence.md).
* **Netzwerkisolation**: Azure Private Link- und VNET-Bereitstellungen (Virtual Network) bieten eine verbesserte Sicherheit und Datenverkehrsisolation für Azure Cache for Redis. Ein VNET ermöglicht die weitere Einschränkung des Zugriffs über Richtlinien für die Steuerung des Netzwerkzugriffs. Weitere Informationen finden Sie unter [Azure Cache for Redis mit Azure Private Link (Public Preview)](cache-private-link.md) sowie unter [Konfigurieren der Unterstützung virtueller Netzwerke für Azure Cache for Redis vom Typ „Premium“](cache-how-to-premium-vnet.md).
* **Maximale Anzahl von Clientverbindungen**: Der Premium-Tarif bietet die maximale Anzahl von Clients, die eine Verbindung mit Redis herstellen können, mit einer größeren Anzahl an Verbindungen für größere Caches. Clustering erhöht nicht die Anzahl von Verbindungen, die für einen gruppierten Cache verfügbar sind. Weitere Informationen finden Sie unter [Azure Cache for Redis – Preise](https://azure.microsoft.com/pricing/details/cache/).
* **Dedizierter Kern für Redis-Server**: Mit Ausnahme von „C0“ werden von allen Caches dedizierte VM-Kerne ausgeführt.
* **Singlethread-Verarbeitung**: Von Redis wird entwurfsbedingt nur ein einzelner Thread für die Befehlsverarbeitung verwendet. Von Azure Cache for Redis werden auch zusätzliche Kerne für die E/A-Verarbeitung genutzt. Die Nutzung zusätzlicher Kerne trägt zur Verbesserung der Durchsatzleistung bei, auch wenn sich dadurch ggf. keine lineare Skalierung ergibt. Darüber hinaus verfügen größere virtuelle Computer in der Regel über höhere Bandbreitengrenzwerte als kleinere. Dadurch lässt sich eine Sättigung des Netzwerks vermeiden, die Timeouts in Ihrer Anwendung zur Folge hat.
* **Leistungsverbesserungen**: Caches im Premium- oder Enterprise-Tarif werden auf Hardware mit schnelleren Prozessoren bereitgestellt, die im Vergleich zum Basic- oder Standard-Tarif eine bessere Leistung bieten. Caches im Premium-Tarif erreichen einen höheren Durchsatz und geringere Wartezeiten. Weitere Informationen finden Sie unter [Azure Cache for Redis – Leistung](cache-planning-faq.md#azure-cache-for-redis-performance).

Nach der Erstellung können Sie Ihren Cache vom Basic-Tarif auf den Premium-Tarif skalieren. Die Herunterskalierung auf einen niedrigeren Tarif wird nicht unterstützt. Ausführliche Anweisungen zur Skalierung finden Sie unter [How to Scale Azure Cache for Redis](cache-how-to-scale.md) (Skalieren von Azure Cache for Redis) und [How to automate a scaling operation](cache-how-to-scale.md#how-to-automate-a-scaling-operation) (Automatisieren eines Skalierungsvorgangs).

### <a name="enterprise-tier-requirements"></a>Anforderungen für den Enterprise-Tarif

Die Enterprise-Tarife basieren auf Redis Enterprise, einer kommerziellen Redis-Version von Redis Labs. Kunden können eine Lizenz für diese Software über ein Azure Marketplace-Angebot beziehen und bezahlen. Azure Cache for Redis vereinfacht die Lizenzbeschaffung, sodass Sie diesen Schritt nicht separat durchführen müssen. Für den Erwerb im Azure Marketplace müssen folgende Voraussetzungen erfüllt sein:
* Ihr Azure-Abonnement muss über ein gültiges Zahlungsmittel verfügen. Azure-Gutschriften oder kostenlose MSDN-Abonnements werden nicht unterstützt.
* Sie müssen für das Abonnement über die Rolle „Besitzer“ oder „Mitwirkender“ verfügen.
* Ihre Organisation muss [Azure Marketplace-Einkäufe](../cost-management-billing/manage/ea-azure-marketplace.md#enabling-azure-marketplace-purchases) zulassen.
* Bei Verwendung eines privaten Marketplace muss er das Redis Labs Enterprise-Angebot enthalten.

## <a name="next-steps"></a>Nächste Schritte
* [Erstellen einer Azure Cache for Redis-Instanz](quickstart-create-redis.md)
* [Erstellen eines Caches mit Enterprise-Tarif](quickstart-create-redis-enterprise.md)
* [Schnellstart: Verwenden von Azure Cache for Redis mit einer ASP.NET-Web-App](cache-web-app-howto.md)
* [Schnellstart: Verwenden von Azure Cache for Redis mit .NET Core](cache-dotnet-core-quickstart.md)
* [Schnellstart: Verwenden von Azure Cache for Redis mit .NET Framework](cache-dotnet-how-to-use-azure-redis-cache.md)
* [Schnellstart: Verwenden von Azure Cache for Redis mit Node.js](cache-nodejs-get-started.md)
* [Schnellstart: Verwenden von Azure Cache for Redis mit Java](cache-java-get-started.md)
* [Schnellstart: Verwenden von Azure Cache for Redis mit Python](cache-python-get-started.md)