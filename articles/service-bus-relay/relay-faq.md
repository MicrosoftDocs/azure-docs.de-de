---
title: Azure Relay – häufig gestellte Fragen | Microsoft-Dokumentation
description: Hier erhalten Sie Antworten auf einige häufig gestellte Fragen zu Azure Relay.
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: 886d2c7f-838f-4938-bd23-466662fb1c8e
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/21/2018
ms.author: spelluru
ms.openlocfilehash: 2433f4b3563cc8b301d1815cccf5ab24406e8662
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66111476"
---
# <a name="azure-relay-faqs"></a>Azure Relay – häufig gestellte Fragen

In diesem Artikel werden einige häufig gestellte Fragen zu [Azure Relay](https://azure.microsoft.com/services/service-bus/) beantwortet. Allgemeine Informationen zu Azure-Preisen und zum Azure-Support finden Sie unter [Häufig gestellte Fragen zum Azure-Support](https://azure.microsoft.com/support/faq/).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="general-questions"></a>Allgemeine Fragen
### <a name="what-is-azure-relay"></a>Was ist Azure Relay?
Der [Azure Relay-Dienst](relay-what-is-it.md) vereinfacht Ihre Hybridanwendungen, indem er Sie dabei unterstützt, Dienste, die sich in einem Unternehmensnetzwerk befinden, auf sichere Weise für die öffentliche Cloud verfügbar zu machen. Sie können die Dienste verfügbar machen, ohne eine Firewallverbindung zu öffnen und ohne grundlegende Änderungen an der Infrastruktur des Unternehmensnetzwerks vornehmen zu müssen.

### <a name="what-is-a-relay-namespace"></a>Was ist ein Relay-Namespace?
Ein [Namespace](relay-create-namespace-portal.md) ist ein Bereichscontainer für die Adressierung von Relay-Ressourcen innerhalb Ihrer Anwendung. Um Relay verwenden zu können, müssen Sie einen Namespace erstellen. Dies ist einer der ersten Schritte.

### <a name="what-happened-to-service-bus-relay-service"></a>Was ist aus dem Service Bus Relay-Dienst geworden?
Der früher „Relay“ genannte Service Bus-Dienst heißt jetzt [WCF-Relay](relay-wcf-dotnet-get-started.md). Sie können den Dienst weiter wie gewohnt verwenden. Der Hybrid Connections-Dienst ist die aktualisierte Version eines Diensts, der aus Azure BizTalk Services übernommen wurde. WCF-Relay und Hybrid Connections werden weiterhin unterstützt.

## <a name="pricing"></a>Preise
In diesem Abschnitt werden einige häufig gestellte Fragen zur Relay-Preisstruktur beantwortet. Allgemeine Informationen zu Azure-Preisen finden Sie unter [Häufig gestellte Fragen zum Azure-Support](https://azure.microsoft.com/support/faq/). Vollständige Informationen zu Relay-Preisen finden Sie unter [Service Bus-Preise][Pricing overview].

### <a name="how-do-you-charge-for-hybrid-connections-and-wcf-relay"></a>Wie werden Hybridverbindungen und WCF-Relay abgerechnet?
Alle Informationen zu den Preisen für Relay finden Sie in der Tabelle [Hybridverbindungen und WCF-Relays][Pricing overview] auf der Seite mit den Preisdetails zu Service Bus. Neben den auf dieser Seite aufgeführten Preisen werden Ihnen die entsprechenden, aus dem Rechenzentrum ausgehenden Datenübertragungen berechnet, mit denen Ihre Anwendung bereitgestellt wird.

### <a name="how-am-i-billed-for-hybrid-connections"></a>Wie werden Hybridverbindungen abgerechnet?
Im Folgenden finden Sie drei Beispielszenarien für die Abrechnung für Hybrid Connections:

*   Szenario 1:
    *   Sie haben einen einzelnen Listener – z.B. eine Instanz des Hybrid Connections-Managers – installiert und führen diesen den gesamten Monat lang kontinuierlich aus.
    *   Sie senden während des Monats 3 GB Daten über die Verbindung. 
    *   Die Gesamtgebühr beträgt 5 USD.
*   Szenario 2:
    *   Sie haben einen einzelnen Listener – z.B. eine Instanz des Hybrid Connections-Managers – installiert und führen diesen den gesamten Monat lang kontinuierlich aus.
    *   Sie senden während des Monats 10 GB Daten über die Verbindung.
    *   Die Gesamtgebühr beträgt 7,50 USD. Diese Summe setzt sich zusammen aus 5 USD für die Verbindung und die ersten 5 GB plus 2,50 USD für die zusätzlichen 5 GB.
*   Szenario 3:
    *   Sie haben zwei Instanzen A und B des Hybrid Connections-Managers installiert und führen diese den gesamten Monat lang kontinuierlich aus.
    *   Sie senden während des Monats 3 GB Daten über Verbindung A.
    *   Sie senden während des Monats 6 GB Daten über Verbindung B.
    *   Die Gesamtgebühr beträgt 10,50 USD. Diese Summe setzt sich zusammen aus 5 USD für Verbindung A plus 5 USD für Verbindung B plus 0,50 USD für das 6. Gigabyte in Verbindung B.

Beachten Sie, dass die Preise in diesen Beispielen nur während des Vorschauzeitraums von Hybrid Connections gelten. Die Preise können sich bei allgemeiner Verfügbarkeit von Hybrid Connections ändern.

### <a name="how-are-hours-calculated-for-relay"></a>Wie werden Stunden für Relay berechnet?

WCF-Relay ist nur in den Namespaces des Tarifs „Standard“ verfügbar. Preise und [Verbindungskontingente](../service-bus-messaging/service-bus-quotas.md) für Relays sind ansonsten unverändert. Dies bedeutet, dass Relays auch weiterhin anhand der Anzahl der Nachrichten (nicht der Vorgänge) und der Relaystunden abgerechnet werden. Weitere Informationen finden Sie in der Tabelle [Hybridverbindungen und WCF-Relays](https://azure.microsoft.com/pricing/details/service-bus/) auf der Seite mit den Preisdetails.

### <a name="what-if-i-have-more-than-one-listener-connected-to-a-specific-relay"></a>Was geschieht, wenn mehrere Listener mit einem bestimmten Relay verbunden sind?
In einigen Fällen können mit einem einzelnen Relay mehrere Listener verbunden sein. Ein Relay wird als „geöffnet“ betrachtet, wenn mindestens ein Relaylistener mit ihm verbunden ist. Das Hinzufügen von Listenern zu einem offenen Relay führt zu zusätzlichen Relaystunden. Die Anzahl von Relaysendern (Clients, die Nachrichten von Relays aufrufen oder an diese senden), die mit dem Relay verbunden sind, hat keine Auswirkung auf die Berechnung der Relaystunden.

### <a name="how-is-the-messages-meter-calculated-for-wcf-relays"></a>Wie wird die Verbrauchseinheit „Nachrichten“ für WCF-Relays berechnet?
(**Dies gilt nur für WCF-Relays. Nachrichten sind in Hybrid Connections kein Kostenfaktor.** )

Im Allgemeinen werden abrechenbare Nachrichten für Relays mit derselben Methode berechnet, die weiter oben für Brokerentitäten (Warteschlangen, Themen und Abonnements) beschrieben wird. Es gibt jedoch einige wichtige Ausnahmen.

Das Senden einer Nachricht an ein Service Bus-Relay wird als vollständiger Sendevorgang an den Relaylistener behandelt, der die Nachricht empfängt. Es wird nicht als Sendevorgang an das Service Bus-Relay mit anschließender Übermittlung an den Relaylistener behandelt. Ein Dienstaufruf vom Typ Anforderung-Antwort (von bis zu 64 KB) für einen Relaylistener führt zu zwei abrechenbaren Nachrichten: eine für die Anforderung und eine für die Antwort (unter der Voraussetzung, dass die Antwort ebenfalls 64 KB oder kleiner ist). Dies unterscheidet sich vom Einsatz einer Warteschlange für die Vermittlung zwischen einem Client und einem Dienst. Wenn Sie eine Warteschlange für die Vermittlung zwischen einem Client und einem Dienst verwenden, erfordert das gleiche Anforderung-Antwort-Muster das Senden einer Anforderungsnachricht an die Warteschlange, gefolgt von einer Nachricht zur Entfernung/Übermittlung von der Warteschlange an den Dienst. Darauf folgt eine Antwortnachricht an eine andere Warteschlange und eine Nachricht zur Entfernung/Übermittlung von dieser Warteschlange an den Client. Unter Annahme der gleichen Nachrichtengrößen (bis zu 64 KB) würde das Muster der Vermittlung per Warteschlange zu vier abrechenbaren Nachrichten führen. Damit würden Ihnen zum Implementieren des gleichen Musters doppelt so viele Nachrichten berechnet wie bei Verwendung von Relays. Natürlich gibt es Vorteile beim Einsatz von Warteschlangen, wenn man dieses Muster verwenden möchte, wie etwa Dauerhaftigkeit und Lastenausgleich. Diese Vorteile könnten die zusätzlichen Kosten rechtfertigen.

Relays, die anhand der WCF-Bindung **netTCPRelay** geöffnet werden, behandeln Nachrichten nicht als einzelne Nachrichten, sondern als Datenstrom, der durch das System fließt. Wenn Sie diese Bindung verwenden, erhalten nur der Sender und der Listener Einblick in das Framing der einzelnen Nachrichten, die gesendet/empfangen werden. Bei Relays, die die **netTCPRelay**-Bindung verwenden, werden zur Ermittlung abrechenbarer Nachrichten alle Daten als Datenstrom behandelt. In diesen Fall berechnet Service Bus die Gesamtmenge der über jedes einzelne Relay gesendeten oder empfangenen Daten für einen Zeitraum von 5 Minuten. Dann wird diese Gesamtmenge durch 64 KB dividiert, um die Anzahl von abrechenbaren Nachrichten für dieses Relay in diesem Zeitraum zu ermitteln.

## <a name="quotas"></a>Kontingente
| Namen des Kontingents | `Scope` |  Notizen | Wert |
| --- | --- | --- | --- |
| Gleichzeitige Listener für ein Relay |Entität |Nachfolgende Anforderungen für zusätzliche Verbindungen werden abgelehnt, und vom aufrufenden Code wird eine Ausnahme empfangen. |25 |
| Gleichzeitige Relayverbindungen für alle Relayendpunkte eines Dienstnamespace |Namespace |- |5\.000 |
| Relayendpunkte pro Dienstnamespace |Namespace |- |10\.000 |
| Nachrichtengröße für [NetOnewayRelayBinding](/dotnet/api/microsoft.servicebus.netonewayrelaybinding)- und [NetEventRelayBinding](/dotnet/api/microsoft.servicebus.neteventrelaybinding)-Relays |Namespace |Eingehende Nachrichten, die diese Kontingente überschreiten, werden abgelehnt, und vom aufrufenden Code wird eine Ausnahme empfangen. |64 KB |
| Nachrichtengröße für [HttpRelayTransportBindingElement](/dotnet/api/microsoft.servicebus.httprelaytransportbindingelement)- und [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding)-Relays |Namespace |Keine Begrenzung der Nachrichtengröße. |Unbegrenzt |

### <a name="does-relay-have-any-usage-quotas"></a>Verfügt Relay über Verwendungskontingente?
Microsoft legt für jeden Clouddienst standardmäßig ein aggregiertes monatliches Nutzungskontingent fest, das abonnementübergreifend für einen Kunden berechnet wird. Wir wissen, dass Ihre Anforderungen diese Limits zuweilen überschreiten können. Wenden Sie sich an unseren Kundendienst, und teilen Sie uns Ihre Anforderungen mit, damit wir diese Werte entsprechend anpassen können. Für Service Bus gelten die folgenden aggregierten Nutzungskontingente:

* 5 Milliarden Nachrichten
* 2 Millionen Relaystunden

Wir behalten uns vor, ein Konto zu deaktivieren, das die monatlichen Nutzungskontingente überschritten hat. Wir informieren allerdings per E-Mail darüber und unternehmen mehrere Versuche, den Kunden zu kontaktieren, ehe wir Maßnahmen ergreifen. Kunden, die diese Kontingente überschreiten, haben die Kosten für die Überschreitung zu tragen.

### <a name="naming-restrictions"></a>Benennungseinschränkungen
Ein Relaynamespace-Name muss zwischen 6 und 50 Zeichen lang sein.

## <a name="subscription-and-namespace-management"></a>Abonnement- und Namespace-Verwaltung
### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>Wie migriere ich einen Namespace zu einem anderen Azure-Abonnement?

Um einen Namespace aus einem Azure-Abonnement in ein anderes zu verschieben, können Sie entweder das [Azure-Portal](https://portal.azure.com) oder PowerShell-Befehle verwenden. Bevor Sie den Namespace in ein anderes Abonnement verschieben können, muss der Namespace bereits aktiv sein. Der Benutzer, der die Befehle ausführt, muss sowohl im Quellabonnement als auch im Zielabonnement Administratorbenutzer sein.

#### <a name="azure-portal"></a>Azure-Portal

Informationen zum Migrieren von Azure Relay-Namespaces aus einem Abonnement in ein anderes finden Sie unter [Verschieben von Ressourcen in eine neue Ressourcengruppe oder ein neues Abonnement](../azure-resource-manager/resource-group-move-resources.md#use-portal). 

#### <a name="powershell"></a>PowerShell

Verwenden Sie die folgende Befehlssequenz, wenn Sie einen Namespace mithilfe von PowerShell aus einem Azure-Abonnement in ein anderes verschieben möchten. Um diesen Vorgang auszuführen, muss der Namespace bereits aktiv sein, und der Benutzer, der die PowerShell-Befehle ausführt, muss sowohl im Quell- als auch im Zielabonnement Administratorbenutzer sein.

```azurepowershell-interactive
# Create a new resource group in the target subscription.
Select-AzSubscription -SubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff'
New-AzResourceGroup -Name 'targetRG' -Location 'East US'

# Move the namespace from the source subscription to the target subscription.
Select-AzSubscription -SubscriptionId 'aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa'
$res = Find-AzResource -ResourceNameContains mynamespace -ResourceType 'Microsoft.ServiceBus/namespaces'
Move-AzResource -DestinationResourceGroupName 'targetRG' -DestinationSubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff' -ResourceId $res.ResourceId
```

## <a name="troubleshooting"></a>Problembehandlung
### <a name="what-are-some-of-the-exceptions-generated-by-azure-relay-apis-and-suggested-actions-you-can-take"></a>Welche Beispiele gibt es für Ausnahmen, die von Azure Relay-APIs generiert werden, und die vorgeschlagenen Aktionen?
Eine Beschreibung häufig auftretender Ausnahmen und der vorgeschlagenen Aktionen, die Sie ausführen können, finden Sie unter [Relay-Ausnahmen][Relay exceptions].

### <a name="what-is-a-shared-access-signature-and-which-languages-can-i-use-to-generate-a-signature"></a>Was ist eine Shared Access Signature, und welche Sprachen kann ich zum Generieren einer Signatur verwenden?
Bei Shared Access Signatures handelt es sich um einen Authentifizierungsmechanismus, der auf sicheren Hashes (SHA-256) oder URIs basiert. Informationen dazu, wie Sie Ihre eigenen Signaturen in Node, PHP, Java, C und C# generieren, finden Sie im Artikel [Service Bus-Authentifizierung mit Shared Access Signatures][Shared Access Signatures].

### <a name="is-it-possible-to-whitelist-relay-endpoints"></a>Ist es möglich, Relayendpunkte in eine Whitelist aufzunehmen?
Ja. Der Relayclient stellt mithilfe vollqualifizierter Domänennamen Verbindungen mit dem Azure Relay-Dienst her. Kunden können einen Eintrag für `*.servicebus.windows.net` für Firewalls hinzufügen, die DNS-Whitelists unterstützen.

## <a name="next-steps"></a>Nächste Schritte
* [Erstellen eines Namespaces](relay-create-namespace-portal.md)
* [Erste Schritte mit .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Erste Schritte mit Node](relay-hybrid-connections-node-get-started.md)

[Pricing overview]: https://azure.microsoft.com/pricing/details/service-bus/
[Relay exceptions]: relay-exceptions.md
[Shared Access Signatures]: ../service-bus-messaging/service-bus-sas.md
