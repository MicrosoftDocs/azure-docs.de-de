---
title: Häufig gestellte Fragen (FAQ) zu Azure Service Bus | Microsoft-Dokumentation
description: Dieser Artikel bietet Antworten auf einige häufig gestellte Fragen (FAQ) zu Azure Service Bus.
ms.topic: article
ms.date: 09/16/2020
ms.openlocfilehash: acd741101928f5a2dfd72eab1598af6e4556a3d1
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "96022137"
---
# <a name="azure-service-bus---frequently-asked-questions-faq"></a>Häufig gestellte Fragen (FAQ) zu Azure Service Bus

In diesem Artikel werden einige häufig gestellte Fragen zu Microsoft Azure Service Bus behandelt. Sie können auch [Häufig gestellte Fragen zum Azure-Support](https://azure.microsoft.com/support/faq/) aufrufen, wenn Sie nach allgemeinen Informationen zu Azure-Preisen und zum Support suchen.


## <a name="general-questions-about-azure-service-bus"></a>Allgemeine Fragen zu Azure Service Bus
### <a name="what-is-azure-service-bus"></a>Was ist Azure Service Bus?
[Azure Service Bus](service-bus-messaging-overview.md) ist eine asynchrone Cloudplattform für Messaging, mit der Sie Daten zwischen entkoppelten Systemen senden können. Microsoft stellt dieses Feature als Dienst bereit, sodass Sie keine eigene Hardware hosten müssen, um es verwenden zu können.

### <a name="what-is-a-service-bus-namespace"></a>Was ist ein Service Bus-Namespace?
Ein [Namespace](service-bus-create-namespace-portal.md) ist ein Bereichscontainer für die Adressierung von Service Bus-Ressourcen innerhalb Ihrer Anwendung. Die Namespace-Erstellung ist für die Verwendung von Service Bus erforderlich und ist einer der ersten Schritte.

### <a name="what-is-an-azure-service-bus-queue"></a>Was ist eine Azure Service Bus-Warteschlange?
Eine [Service Bus-Warteschlange](service-bus-queues-topics-subscriptions.md) ist eine Entität, in der Nachrichten gespeichert werden. Warteschlangen sind nützlich, wenn Sie über mehrere Anwendungen oder mehrere Teile einer verteilten Anwendung verfügen, die miteinander kommunizieren müssen. Die Warteschlange ähnelt einem Verteilzentrum, in dem mehrere Produkte (Nachrichten) eingehen und von diesem Standort aus dann weiterverschickt werden.

### <a name="what-are-azure-service-bus-topics-and-subscriptions"></a>Was sind Azure Service Bus-Themen und -Abonnements?
Eine Thema kann als Warteschlange visualisiert werden, und bei Verwendung mehrerer Abonnements ergibt sich ein umfassenderes Messagingmodell. Im Wesentlichen handelt es sich um ein Tool für die 1:n-Kommunikation. Bei diesem Modell vom Typ „Veröffentlichen/Abonnieren“ (*Pub/Sub*) kann für eine Anwendung, die eine Nachricht an ein Thema mit mehreren Abonnements sendet, erreicht werden, dass die Nachricht von mehreren Anwendungen empfangen wird.

### <a name="what-is-a-partitioned-entity"></a>Was ist eine partitionierte Entität?
Eine herkömmliche Warteschlange oder ein Thema werden von einem einzelnen Nachrichtenbroker verarbeitet und in einem Nachrichtenspeicher gespeichert. [Partitionierte Warteschlangen und Themen](service-bus-partitioning.md) werden nur auf der Basic- und Standard-Messagingebene unterstützt. Sie werden von mehreren Nachrichtenbrokern verarbeitet und in mehreren Nachrichtenspeichern gespeichert. Dies bedeutet, dass der Gesamtdurchsatz einer partitionierten Warteschlange oder eines Themas nicht mehr durch die Leistung eines einzelnen Nachrichtenbrokers oder Nachrichtenspeichers beschränkt wird. Außerdem führt ein vorübergehender Ausfall eines Nachrichtenspeichers nicht dazu, dass eine partitionierte Warteschlange oder ein Thema nicht mehr verfügbar ist.

Bei der Verwendung von partitionierten Entitäten ist keine Sortierung gewährleistet. Wenn eine Partition nicht verfügbar ist, ist es trotzdem möglich, Nachrichten zu senden und von anderen Partitionen zu empfangen.

 Partitionierte Entitäten werden in der [Premium-SKU](service-bus-premium-messaging.md) nicht mehr unterstützt. 

### <a name="where-does-azure-service-bus-store-customer-data"></a><a name="in-region-data-residency"></a>Wo speichert Azure Service Kundendaten?
Azure Service Bus speichert Kundendaten. Diese Daten werden von Service Bus automatisch in einer einzigen Region gespeichert, sodass dieser Dienst in der Region automatisch die Anforderungen an Datenresidenz erfüllt, einschließlich der im [Trust Center](https://azuredatacentermap.azurewebsites.net/) angegebenen Anforderungen.

### <a name="what-ports-do-i-need-to-open-on-the-firewall"></a>Welche Ports muss ich in der Firewall öffnen? 
Sie können die folgenden Protokolle mit Azure Service Bus verwenden, um Nachrichten zu senden und zu empfangen:

- Advanced Message Queuing Protocol 1.0 (AMQP)
- Hypertext Transfer Protocol 1.1 mit TLS (HTTPS)

In der folgenden Tabelle finden Sie die ausgehenden TCP-Ports, die Sie öffnen müssen, um diese Protokolle für die Kommunikation mit Azure Service Bus verwenden zu können:

| Protocol | Port | Details | 
| -------- | ----- | ------- | 
| AMQP | 5671 | AMQP mit TLS. Weitere Informationen finden Sie im [AMQP 1.0 in Azure Service Bus und Event Hubs – Protokollleitfaden](service-bus-amqp-protocol-guide.md). | 
| HTTPS | 443 | Dieser Port wird für die HTTP/REST-API und für AMQP-over-WebSockets verwendet. |

Der HTTPS-Port ist in der Regel für die ausgehende Kommunikation auch dann erforderlich, wenn AMQP über Port 5671 verwendet wird, da mehrere Verwaltungsvorgänge, die von den Client-SDKs ausgeführt werden, und der Abruf von Token aus Azure Active Directory (falls verwendet) über HTTPS erfolgen. 

Die offiziellen Azure-SDKs verwenden im Allgemeinen das AMQP-Protokoll zum Senden von Nachrichten an und Empfangen von Nachrichten von Service Bus. 

[!INCLUDE [service-bus-websockets-options](../../includes/service-bus-websockets-options.md)]

Das ältere WindowsAzure.ServiceBus-Paket für .NET Framework verfügt über eine Option zur Verwendung von Legacy-SBMP (Service Bus Messaging Protocol), das auch als „NetMessaging“ bezeichnet wird. Dieses Protokoll verwendet die TCP-Ports 9350 bis 9354. Der Standardmodus für dieses Paket besteht darin, automatisch zu erkennen, ob diese Ports für die Kommunikation verfügbar sind. wenn dies nicht der Fall ist, wechselt die Verbindung zu WebSockets mit TLS über Port 443. Sie können diese Einstellung überschreiben und diesen Modus erzwingen, indem Sie `Https` [ConnectivityMode](/dotnet/api/microsoft.servicebus.connectivitymode?view=azure-dotnet) auf die Einstellung [`ServiceBusEnvironment.SystemConnectivity`](/dotnet/api/microsoft.servicebus.servicebusenvironment.systemconnectivity?view=azure-dotnet) festlegen, die global auf die Anwendung angewendet wird.

### <a name="what-ip-addresses-do-i-need-to-add-to-allow-list"></a>Welche IP-Adressen muss ich in die Zulassungsliste aufnehmen?
Um die richtigen IP-Adressen zu ermitteln, die Sie in die Zulassungsliste für Ihre Verbindungen aufnehmen sollten, führen Sie die folgenden Schritte aus:

1. Führen Sie den folgenden Befehl an einer Eingabeaufforderung aus: 

    ```
    nslookup <YourNamespaceName>.servicebus.windows.net
    ```
2. Notieren Sie sich die IP-Adresse, die in `Non-authoritative answer` zurückgegeben werden. 

Wenn Sie **Zonenredundanz** für Ihren Namespace verwenden, müssen Sie einige zusätzliche Schritte durchführen: 

1. Führen Sie zunächst nslookup für den Namespace aus.

    ```
    nslookup <yournamespace>.servicebus.windows.net
    ```
2. Notieren Sie sich den Namen im Abschnitt **non-authoritative answer** (nicht autorisierende Antwort), der in einem der folgenden Formate vorliegt: 

    ```
    <name>-s1.cloudapp.net
    <name>-s2.cloudapp.net
    <name>-s3.cloudapp.net
    ```
3. Führen Sie den Befehl „nslookup“ für jeden Namen mit den Suffixen s1, s2 und s3 aus, um die IP-Adressen aller drei Instanzen zu erhalten, die in drei Verfügbarkeitszonen ausgeführt werden. 

    > [!NOTE]
    > Die vom `nslookup`-Befehl zurückgegebene IP-Adresse ist keine statische IP-Adresse. Allerdings bleibt sie konstant, bis die zugrunde liegende Bereitstellung gelöscht oder in einen anderen Cluster verschoben wird.

### <a name="where-can-i-find-the-ip-address-of-the-client-sendingreceiving-messages-tofrom-a-namespace"></a>Wo finde ich die IP-Adresse des Clients, der Nachrichten an einen Namespace sendet bzw. von diesem empfängt? 
Die IP-Adressen von Clients, die Nachrichten an einen Namespace senden oder von diesem empfangen, werden von uns nicht protokolliert. Generieren Sie die Schlüssel neu, damit die Authentifizierung aller vorhandenen Clients nicht mehr funktioniert, und überprüfen Sie die Einstellungen der [rollenbasierten Zugriffssteuerung in Azure (Azure RBAC)](authenticate-application.md#azure-built-in-roles-for-azure-service-bus), um sicherzustellen, dass nur zulässige Benutzer oder Anwendungen Zugriff auf den Namespace haben. 

Wenn Sie einen **Premium**-Namespace verwenden, schränken Sie den Zugriff auf den Namespace mithilfe von [IP-Filtern](service-bus-ip-filtering.md), [Dienstendpunkten für virtuelle Netzwerke](service-bus-service-endpoints.md) und [privaten Endpunkten](private-link-service.md) ein. 

## <a name="best-practices"></a>Bewährte Methoden
### <a name="what-are-some-azure-service-bus-best-practices"></a>Gibt es Beispiele für bewährte Azure Service Bus-Methoden?
Siehe [Bewährte Methoden für Leistungsoptimierungen mithilfe von Service Bus][Best practices for performance improvements using Service Bus] – In diesem Artikel wird beschrieben, wie Sie die Leistung beim Austauschen von Nachrichten optimieren können.

### <a name="what-should-i-know-before-creating-entities"></a>Was muss ich vor der Erstellung von Entitäten wissen?
Die folgenden Eigenschaften einer Warteschlange und eines Themas sind unveränderlich. Berücksichtigen Sie diese Einschränkung beim Bereitstellen Ihrer Entitäten, da diese Eigenschaften nicht geändert werden können, ohne eine neue Ersatzentität zu erstellen.

* Partitionierung
* Sitzungen
* Duplikaterkennung
* Express-Entität

## <a name="pricing"></a>Preise
In diesem Abschnitt werden einige häufig gestellte Fragen zur Service Bus-Preisstruktur beantwortet.

Im Artikel [Service Bus – Preise und Abrechnung](https://azure.microsoft.com/pricing/details/service-bus/) werden die Abrechnungseinheiten in Service Bus erläutert. Detailliertere Informationen zu den Service Bus-Preisoptionen finden Sie unter [Service Bus – Preise](https://azure.microsoft.com/pricing/details/service-bus/).

Sie können auch [Häufig gestellte Fragen zum Azure-Support](https://azure.microsoft.com/support/faq/) aufrufen, wenn Sie nach allgemeinen Azure-Preisinformationen suchen. 

### <a name="how-do-you-charge-for-service-bus"></a>Wie werden die Kosten für Service Bus berechnet?
Vollständige Informationen zu Service Bus-Preisen finden Sie unter [Service Bus-Preise][Pricing overview]. Neben den aufgeführten Preisen werden Ihnen die damit verbundenen aus dem Rechenzentrum ausgehenden Datenübertragungen berechnet, mit denen Ihre Anwendung bereitgestellt wird.

### <a name="what-usage-of-service-bus-is-subject-to-data-transfer-what-isnt"></a>Bei welcher Nutzung von Service Bus werden Daten übertragen? Bei welcher ist dies nicht der Fall?
Jede Datenübertragung innerhalb der jeweiligen Azure-Region ist ebenso wie jede eingehende Datenübertragung kostenfrei. Für die Datenübertragung außerhalb einer Region gelten Gebühren für ausgehenden Datenverkehr, die [hier](https://azure.microsoft.com/pricing/details/bandwidth/) aufgeführt sind.

### <a name="does-service-bus-charge-for-storage"></a>Fallen für Service Bus Speicherkosten an?
Nein. Für Service Bus fallen keine Speicherkosten an. Es gibt allerdings ein Kontingent, das die Höchstmenge von Daten begrenzt, die pro Warteschlange/Thema beibehalten werden kann. Weitere Informationen finden Sie im nächsten Abschnitt.

### <a name="i-have-a-service-bus-standard-namespace-why-do-i-see-charges-under-resource-group-system"></a>Ich habe einen Service Bus Standard-Namespace. Warum werden Gebühren unter der Ressourcengruppe „$system“ angezeigt?
Vor Kurzem wurden die Abrechnungskomponenten für Azure Service Bus aktualisiert. Aufgrund dieser Änderung werden, wenn Sie über einen Service Bus Standard-Namespace verfügen, möglicherweise Zeilenelemente für die Ressource „/subscriptions/<Azure-Abonnement-ID>/resourceGroups/$system/providers/Microsoft.ServiceBus/namespaces/$system“ unter der Ressourcengruppe „$system“ angezeigt.

Diese Gebühren stellen die Grundgebühr für jedes Azure-Abonnement dar, das einen Service Bus Standard-Namespace bereitgestellt hat. 

Beachten Sie, dass es sich hierbei nicht um neue Gebühren handelt, d. h., sie bestanden auch im vorherigen Abrechnungsmodell. Die einzige Änderung besteht darin, dass sie jetzt unter „$system“ aufgeführt sind. Dies erfolgt aufgrund von Einschränkungen im neuen Abrechnungssystem, bei denen die Gebühren auf Abonnementebene, die nicht an eine bestimmte Ressource gebunden sind, unter der Ressourcen-ID „$system“ gruppiert werden.

## <a name="quotas"></a>Kontingente

Eine Liste mit Service Bus-Grenzwerten und -Kontingenten finden Sie unter [Übersicht über Service Bus-Kontingente][Quotas overview].

### <a name="how-to-handle-messages-of-size--1-mb"></a>Wie werden Nachrichten mit einer Größe über 1 MB behandelt?
Service Bus-Messagingdienste (Warteschlangen und Themen/Abonnements) ermöglichen Anwendungen, Nachrichten mit einer Größe bis zu 256 KB (Standard-Tarif) oder 1 MB (Premium-Tarif) zu senden. Wenn Ihre Nachrichten eine Größe über 1 MB aufweisen, verwenden Sie das [in diesem Blogbeitrag](https://www.serverless360.com/blog/deal-with-large-service-bus-messages-using-claim-check-pattern) beschriebene Anspruchsüberprüfungsmuster.

## <a name="troubleshooting"></a>Problembehandlung
### <a name="why-am-i-not-able-to-create-a-namespace-after-deleting-it-from-another-subscription"></a>Warum kann ich keinen Namespace erstellen, nachdem ich ihn aus einem anderen Abonnement gelöscht habe? 
Wenn Sie einen Namespace aus einem Abonnement löschen, warten Sie vier Stunden, bevor Sie ihn mit dem gleichen Namen in einem anderen Abonnement erstellen. Sie erhalten andernfalls unter Umständen die folgende Fehlermeldung: `Namespace already exists`. 

### <a name="what-are-some-of-the-exceptions-generated-by-azure-service-bus-apis-and-their-suggested-actions"></a>Welche Beispiele gibt es für Ausnahmen, die von Azure Service Bus-APIs generiert werden, und die vorgeschlagenen Aktionen?
Eine Liste der möglichen Service Bus-Ausnahmen finden Sie in der [Übersicht über Ausnahmen][Exceptions overview].

### <a name="what-is-a-shared-access-signature-and-which-languages-support-generating-a-signature"></a>Was ist eine Shared Access Signature, und welche Sprachen unterstützen die Generierung einer Signatur?
SAS (Shared Access Signatures) sind ein Authentifizierungsmechanismus, der auf sicheren Hashes (SHA-256) oder URIs basiert. Informationen dazu, wie Sie Ihre eigenen Signaturen in Node.js, PHP, Java, Python und C# generieren, finden Sie im Artikel [Shared Access Signatures][Shared Access Signatures].

## <a name="subscription-and-namespace-management"></a>Abonnement- und Namespace-Verwaltung
### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>Wie migriere ich einen Namespace zu einem anderen Azure-Abonnement?

Sie können einen Namespace entweder über das [Azure-Portal](https://portal.azure.com) oder unter Verwendung von PowerShell-Befehlen von einem Azure-Abonnement in ein anderes verschieben. Der Namespace muss bereits aktiv sein, um diesen Vorgang durchführen zu können. Der Benutzer, der die Befehle ausführt, muss sowohl im Quellabonnement als auch im Zielabonnement Administrator sein.

#### <a name="portal"></a>Portal

Wenn Sie Service Bus-Namespaces mithilfe des Azure-Portals auf ein anderes Abonnement übertragen möchten, folgen Sie den [hier](../azure-resource-manager/management/move-resource-group-and-subscription.md#use-the-portal) aufgeführten Anweisungen. 

#### <a name="powershell"></a>PowerShell

Mit der folgenden PowerShell-Befehlssequenz wird ein Namespace aus einem Azure-Abonnement in ein anderes verschoben. Um diesen Vorgang auszuführen, muss der Namespace bereits aktiv sein und der Benutzer, der die PowerShell-Befehle ausführt, muss sowohl im Quell- als auch im Zielabonnement Administratorrechte besitzen.

```powershell
# Create a new resource group in target subscription
Select-AzSubscription -SubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff'
New-AzResourceGroup -Name 'targetRG' -Location 'East US'

# Move namespace from source subscription to target subscription
Select-AzSubscription -SubscriptionId 'aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa'
$res = Find-AzResource -ResourceNameContains mynamespace -ResourceType 'Microsoft.ServiceBus/namespaces'
Move-AzResource -DestinationResourceGroupName 'targetRG' -DestinationSubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff' -ResourceId $res.ResourceId
```

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu Service Bus finden Sie in den folgenden Artikeln:

* [Einführung in Azure Service Bus Premium (Blogbeitrag)](https://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
* [Einführung in Azure Service Bus Premium (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
* [Übersicht über Service Bus](service-bus-messaging-overview.md)
* [Erste Schritte mit Service Bus-Warteschlangen](service-bus-dotnet-get-started-with-queues.md)

[Best practices for performance improvements using Service Bus]: service-bus-performance-improvements.md
[Best practices for insulating applications against Service Bus outages and disasters]: service-bus-outages-disasters.md
[Pricing overview]: https://azure.microsoft.com/pricing/details/service-bus/
[Quotas overview]: service-bus-quotas.md
[Exceptions overview]: service-bus-messaging-exceptions.md
[Shared Access Signatures]: service-bus-sas.md
