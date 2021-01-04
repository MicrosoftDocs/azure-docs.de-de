---
title: Azure Service Bus -Zugriffssteuerung mit SAS (Shared Access Signatures)
description: Übersicht über Service Bus-Zugriffssteuerung mithilfe von Shared Access Signatures, Informationen zur SAS-Autorisierung mit Azure Service Bus.
ms.topic: article
ms.date: 11/03/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: f71320613682f7d4b9f3b706845e68f581b3dc10
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93339409"
---
# <a name="service-bus-access-control-with-shared-access-signatures"></a>Service Bus-Zugriffssteuerung mit Shared Access Signatures

*Shared Access Signatures* (SAS) sind die primären Sicherheitsmechanismen für Service Bus-Messaging. Dieser Artikel beschreibt SAS, ihre Funktionsweise und die plattformunabhängige Verwendung.

SAS schützt den Zugriff auf Service Bus basierend auf Autorisierungsregeln. Diese werden für einen Namespace oder eine Messagingentität (Relay, Warteschlange oder Thema) konfiguriert. Eine Autorisierungsregel hat einen Namen, ist mit bestimmten Rechten verknüpft und enthält ein Paar kryptografischer Schlüssel. Sie verwenden den Namen und den Schlüssel der Regel über das Service Bus SDK oder in Ihrem eigenen Code, um ein SAS-Token zu generieren. Ein Client kann dann das Token an Service Bus übergeben, um die Autorisierung für den angeforderten Vorgang zu bestätigen.

> [!NOTE]
> Azure Service Bus unterstützt das Autorisieren des Zugriffs auf einen Service Bus-Namespace und dessen Entitäten mithilfe von Azure Active Directory (Azure AD). Das Autorisieren von Benutzern oder Anwendungen mithilfe eines von Azure AD zurückgegebenen OAuth 2.0-Tokens bietet mehr Sicherheit und Benutzerfreundlichkeit als die Autorisierung per SAS (Shared Access Signature). Mit Azure AD ist es nicht erforderlich, Token in Ihrem Code zu speichern und potenzielle Sicherheitsrisiken einzugehen.
>
> Microsoft empfiehlt die Verwendung von Azure AD für Ihre Azure Service Bus-Anwendungen, wenn dies möglich ist. Weitere Informationen finden Sie in den folgenden Artikeln:
> - [Authentifizieren und Autorisieren einer Anwendung mit Azure Active Directory für den Zugriff auf Azure Service Bus-Entitäten](authenticate-application.md).
> - [Authentifizieren einer verwalteten Identität mit Azure Active Directory für den Zugriff auf Azure Service Bus-Ressourcen](service-bus-managed-service-identity.md)

## <a name="overview-of-sas"></a>Übersicht über SAS

Shared Access Signatures sind ein anspruchsbasierter Autorisierungsmechanismus mit einfachen Token. Mit SAS werden Schlüssel nie über das Netzwerk übergeben. Schlüssel werden verwendet, um Informationen kryptografisch zu signieren, die später vom Dienst überprüft werden können. SAS kann wie ein Schema aus Benutzername und Kennwort verwendet werden, bei dem der Client unmittelbar den Namen einer Autorisierungsregel und einen passenden Schlüssel besitzt. SAS kann auch ähnlich wie ein Verbundsicherheitsmodell verwendet werden, bei dem der Client ein zeitlich begrenztes und signierte Zugriffstoken von einem Sicherheitstokendienst erhält, ohne den Signaturschlüssel überhaupt zu besitzen.

SAS-Authentifizierung in Service Bus wird mit benannten [SAS-Autorisierungsregeln](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule), denen Zugriffsrechte zugeordnet sind, und einem Paar aus primären und sekundären kryptografischen Schlüsseln konfiguriert. Die Schlüssel sind 256-Bit-Werte in Base64-Darstellung. Sie können Regeln auf Namespaceebene für Service Bus-[Relays](../azure-relay/relay-what-is-it.md), -[Warteschlangen](service-bus-messaging-overview.md#queues) und -[Themen](service-bus-messaging-overview.md#topics) konfigurieren.

Das [Shared Access Signature](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider)-Token enthält den Namen der ausgewählten Autorisierungsregel, den URI der Ressource, auf die zugegriffen werden muss, einen Ablaufwert und eine kryptografische HMAC-SHA256-Signatur, die mithilfe des primären oder sekundären Kryptografieschlüssels für die ausgewählte Autorisierungsregel über diese Felder berechnet wird.

## <a name="shared-access-authorization-policies"></a>SAS-Autorisierungsrichtlinien

Alle Service Bus-Namespaces und Service Bus-Entitäten verfügen über eine SAS-Autorisierungsrichtlinie, die aus Regeln besteht. Die Richtlinie auf Namespaceebene gilt für alle Entitäten innerhalb des Namespace, unabhängig von ihrer jeweiligen Richtlinienkonfiguration.

Jede Regel einer Autorisierungsrichtlinie erfordert drei Angaben: **Name** , **Bereich** und **Rechte**. Der **Name** ist ein eindeutiger Name innerhalb des Bereichs. Der Bereich ist der URI der betreffenden Ressource. Für einen Service Bus-Namespace wird als Bereich der vollqualifizierte Domänenname (Fully Qualified Domain Name, FQDN) angegeben, z.B. `https://<yournamespace>.servicebus.windows.net/`.

Die durch die Richtlinienregel gewährten Rechte können eine Kombination aus Folgendem sein:

* Senden: Gewährt das Recht zum Senden von Nachrichten an die Entität
* Lauschen: Gewährt das Recht zum Lauschen auf (Relay) oder zum Empfangen (Warteschlangen, Abonnements) und zur zugehörigen Nachrichtenverarbeitung
* Verwalten: Gewährt das Recht zum Verwalten der Topologie des Namespace, einschließlich Erstellen und Löschen von Entitäten

Das Recht „Verwalten“ enthält die Rechte „Senden“ und „Empfangen“.

Eine Namespace- oder Entitätenrichtlinie kann bis zu 12 SAS-Autorisierungsregeln aufweisen und bietet so Platz für drei Sätze von Regeln, die jeweils die grundlegenden Rechte und die Kombination aus „Senden“ und „Lauschen“ abdecken. Mit dieser Einschränkung wird verdeutlicht, dass der SAS-Richtlinienspeicher nicht als Benutzer- oder Dienstkontospeicher vorgesehen ist. Wenn Ihre Anwendung Zugriff auf Service Bus basierend auf Benutzer- oder Dienstidentitäten gewähren muss, sollte sie einen Sicherheitstokendienst implementieren, der nach einer Authentifizierungs- und Zugriffsprüfung SAS-Token ausstellt.

Einer Autorisierungsregel wird ein *Primärschlüssel* und ein *Sekundärschlüssel* zugewiesen. Hierbei handelt es sich um kryptografisch starke Schlüssel. Achten Sie darauf, dass sie Ihnen nicht abhanden kommen: Sie sind immer im [Azure-Portal][Azure portal] verfügbar. Sie können einen beliebigen der generierten Schlüssel verwenden und die Schlüssel jederzeit erneut generieren. Wenn Sie einen Schlüssel in der Richtlinie neu erstellen oder ändern, sind alle zuvor basierend auf diesem Schlüssel ausgestellten Token sofort ungültig. Ausgehende Verbindungen, die basierend auf solchen Token erstellt wurden, funktionieren allerdings weiterhin, bis das Token abgelaufen ist.

Wenn Sie einen Service Bus-Namespace erstellen, wird automatisch eine Richtlinie namens **RootManageSharedAccessKey** für den Namespace erstellt. Diese Richtlinie hat die Berechtigung „Verwalten“ für den gesamten Namespace. Es wird empfohlen, dass Sie diese Regel wie ein administratives **Stammkonto** behandeln und nicht in Ihrer Anwendung verwenden. Sie können auf der Registerkarte **Konfigurieren** für den Namespace im Portal, mit PowerShell oder über die Azure CLI weitere Richtlinienregeln erstellen.

## <a name="best-practices-when-using-sas"></a>Bewährte Methoden bei Verwendung von SAS
Wenn Sie Shared Access Signatures in Ihren Anwendungen verwenden, müssen Sie sich der potenziellen Risiken bewusst sein:

- Offengelegte SAS können von allen Personen, in deren Besitz sie gelangen, verwendet werden. Dies kann die Sicherheit Ihrer Event Hubs-Ressourcen gefährden.
- Wenn die SAS einer Clientanwendung abläuft und die Anwendung keine neue SAS von Ihrem Dienst abrufen kann, wird die Funktionsweise der Anwendung unter Umständen eingeschränkt.

Mit den folgenden Empfehlungen für die Verwendung von Shared Access Signatures können Sie diese Risiken verringern:

- **Sorgen Sie dafür, dass die Clients die SAS bei Bedarf automatisch erneuern müssen** : Die Clients sollten ihre SAS rechtzeitig vor der Ablaufzeit erneuern, um Zeit für Wiederholungsversuche zuzulassen, falls der entsprechende Dienst nicht verfügbar sein sollte. Falls Ihre SAS für eine kleine Anzahl sofortiger und kurzfristiger Vorgänge gilt, die normalerweise innerhalb des Ablaufzeitraums abgeschlossen werden, ist dies unter Umständen nicht notwendig, da die SAS nicht erneuert werden müssen. Wenn Ihre Clients jedoch immer wieder Anfragen über die SAS stellen, müssen Sie sich mit dem Ablaufmechanismus auseinander setzen. Dabei müssen Sie zwischen der Notwendigkeit der Kurzlebigkeit einer SAS (wie zuvor beschrieben) und der Sicherstellung einer rechtzeitigen Anforderung der Erneuerung durch den Client abwägen. So verhindern Sie, dass die SAS vor einer erfolgreichen Erneuerung abläuft.
- **Gehen Sie bei der SAS-Startzeit mit Bedacht vor** : Wenn Sie die Startzeit für SAS auf **jetzt** festlegen, können aufgrund von Zeitunterschieden zwischen unterschiedlichen Computern in den ersten Minuten Probleme auftreten. Üblicherweise sollten Sie als Startzeit eine Uhrzeit angeben, die mindestens 15 Minuten in der Vergangenheit liegt. Sie können auch so vorgehen, dass Sie keine Startzeit festlegen, damit die Aktivierung immer sofort erfolgt. Dasselbe gilt auch für die Ablaufzeit. Beachten Sie, dass es für alle Anforderungen in beiden Richtungen zu Uhrabweichungen von bis zu 15 Minuten kommen kann. 
- **Geben Sie die Ressource, auf die zugegriffen werden soll, exakt an** : Aus Sicherheitsgründen sollten Benutzer nur die minimal erforderlichen Berechtigungen erhalten. Wenn ein Benutzer nur Lesezugriff auf eine einzige Entität benötigt, dann geben Sie auch nur Lesezugriff auf diese Entität, und nicht Lese-/Schreib-/Löschzugriff auf alle Entitäten. So lässt sich auch der Schaden verringern, wenn eine SAS kompromittiert wurde, weil die SAS dem Angreifer weniger Angriffsmöglichkeiten bietet.
- **Verwenden Sie SAS nicht immer** : Manchmal überwiegen die Risiken eines bestimmten Vorgangs für Ihre Event Hubs-Instanz gegenüber den Vorzügen von SAS. Erstellen Sie für Vorgänge dieser Art einen Dienst auf der mittleren Ebene, der in Ihre Event Hubs-Instanz schreibt, nachdem die Überprüfung der Geschäftsregeln, die Authentifizierung und die Überwachung durchgeführt wurden.
- **Verwenden Sie immer HTTPs** : Verwenden Sie zum Erstellen oder Verteilen einer SAS immer HTTPS. Wenn eine SAS über HTTP weitergegeben und abgefangen wird, kann ein Angreifer diese mit einem Man-in-the-Middle-Angriff auslesen, anschließend im Namen des Benutzers verwenden und somit unter Umständen sensible Daten gefährden oder böswillig beschädigen.

## <a name="configuration-for-shared-access-signature-authentication"></a>Konfiguration für SAS-Authentifizierung (Shared Access Signature)

Sie können die Regel [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) für Service Bus-Namespaces, -Warteschlangen oder -Themen konfigurieren. Das Konfigurieren einer [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) für ein Service Bus-Abonnement wird zurzeit nicht unterstützt, aber Sie können Regeln, die für einen Namespace oder ein Thema konfiguriert wurden, auch zum Sichern des Zugriffs auf Abonnements verwenden. Ein funktionierendes Beispiel, das dieses Verfahren veranschaulicht, finden Sie unter [Using Shared Access Signature (SAS) authentication with Service Bus Subscriptions](https://code.msdn.microsoft.com/Using-Shared-Access-e605b37c) (Verwenden der SAS-Authentifizierung mit Service Bus-Abonnements).

![SAS](./media/service-bus-sas/service-bus-namespace.png)

In dieser Abbildung gelten die Autorisierungsregeln *manageRuleNS* , *sendRuleNS* und *listenRuleNS* sowohl für die Warteschlange Q1 als auch für das Thema T1. *listenRuleQ* und *sendRuleQ* gelten nur für Warteschlange Q1, und *sendRuleT* gilt nur für Thema T1.

## <a name="generate-a-shared-access-signature-token"></a>Generieren eines SAS-Tokens

Jeder Client mit Zugriff auf den Namen einer Autorisierungsregel und einen von deren Signaturschlüsseln kann ein SAS-Token generieren. Zur Tokengenerierung wird eine Zeichenfolge im folgenden Format erstellt:

```
SharedAccessSignature sig=<signature-string>&se=<expiry>&skn=<keyName>&sr=<URL-encoded-resourceURI>
```

* **`se`** : Tokenablaufwert. Ganze Zahl, die die Sekunden seit der Epoche `00:00:00 UTC` am 1. Januar 1970 (UNIX-Epoche) darstellt, als Ablaufdatum des Tokens.
* **`skn`** : Name der Autorisierungsregel.
* **`sr`** : URI der Ressource, auf die zugegriffen wird.
* **`sig`** : Signatur.

`signature-string` ist der SHA-256-Hash, der über den Ressourcen-URI berechnet wird (der im vorherigen Abschnitt beschriebene **Bereich** ), und eine Zeichenfolgendarstellung des Tokenablaufwerts, getrennt durch LF.

Die Berechnung des Hashs ähnelt dem folgenden Pseudocode und gibt einen 256-Bit-/32-Byte-Hashwert zurück.

```
SHA-256('https://<yournamespace>.servicebus.windows.net/'+'\n'+ 1438205742)
```

Das Token enthält die Werte ohne Hash, sodass der Empfänger den Hash mit den gleichen Parametern neu berechnen und überprüfen kann, ob der Aussteller im Besitz eines gültigen Signaturschlüssels ist.

Der Ressourcen-URI ist der vollständige URI der Service Bus-Ressource, auf die der Zugriff beansprucht wird. Beispiel: `http://<namespace>.servicebus.windows.net/<entityPath>` oder `sb://<namespace>.servicebus.windows.net/<entityPath>`, also `http://contoso.servicebus.windows.net/contosoTopics/T1/Subscriptions/S3`. 

**Der URI muss als [Prozentwert codiert](/dotnet/api/system.web.httputility.urlencode) sein.**

Die zum Signieren verwendete SAS-Autorisierungsregel muss für die durch diesen URI angegebene Entität oder eines seiner hierarchisch übergeordneten Elemente konfiguriert werden. Beispiel: `http://contoso.servicebus.windows.net/contosoTopics/T1` oder `http://contoso.servicebus.windows.net` im vorherigen Beispiel.

Ein SAS-Token ist für alle Ressourcen mit dem Präfix `<resourceURI>` gültig, das in `signature-string` verwendet wird.

> [!NOTE]
> Beispiele zum Generieren eines SAS-Tokens unter Verwendung verschiedener Programmiersprachen finden Sie unter [Generieren eines SAS-Tokens](/rest/api/eventhub/generate-sas-token). 

## <a name="regenerating-keys"></a>Neugenerieren von Schlüsseln

Es wird empfohlen, die im [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) -Objekt verwendeten Schlüssel in regelmäßigen Abständen neu zu generieren. Die Primär- und Sekundärschlüsselslots sind vorhanden, damit Sie Schlüssel schrittweise rotieren können. Wenn Ihre Anwendung im Allgemeinen den Primärschlüssel verwendet, können Sie den Primärschlüssel in den Sekundärschlüsselslot kopieren und erst dann den Primärschlüssel erneut generieren. Der neue Primärschlüsselwert kann dann in den Clientanwendungen konfiguriert werden, die ununterbrochenen Zugriff mit dem alten Primärschlüssel im sekundären Slot haben. Sobald alle Clients aktualisiert wurden, können Sie den Sekundärschlüssel erneut generieren, um schließlich den alten Primärschlüssel außer Kraft zu setzen.

Wenn Sie wissen oder vermuten, dass ein Schlüssel gefährdet ist, und Sie die Schlüssel widerrufen müssen, können Sie sowohl den [PrimaryKey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) als auch den [SecondaryKey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) eines [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule)-Objekts neu generieren und so durch neue Schlüssel ersetzen. Durch dieses Verfahren werden alle Token, die mit den alten Schlüsseln signiert wurden, für ungültig erklärt.

## <a name="shared-access-signature-authentication-with-service-bus"></a>SAS-Authentifizierung bei Service Bus

Die im Folgenden beschriebenen Szenarien umfassen die Konfiguration von Autorisierungsregeln, das Generieren von SAS-Token und die Clientautorisierung.

Ein vollständiges praktisches Beispiel für eine Service Bus-Anwendung, die die Konfiguration veranschaulicht und die SAS-Autorisierung verwendet, finden Sie unter [SAS-Authentifizierung bei Service Bus](https://code.msdn.microsoft.com/Shared-Access-Signature-0a88adf8). Ein Beispiel, das die Verwendung von in Namespaces oder Themen konfigurierten SAS-Autorisierungsregeln zum Absichern von Service Bus-Abonnements veranschaulicht, finden Sie hier: [Using Shared Access Signature (SAS) authentication with Service Bus Subscriptions](https://code.msdn.microsoft.com/Using-Shared-Access-e605b37c)(in englischer Sprache).

## <a name="access-shared-access-authorization-rules-on-an-entity"></a>Zugreifen auf SAS-Autorisierungsregeln für eine Entität

Mit .NET Framework-Bibliotheken in Service Bus können Sie auf ein [Microsoft.ServiceBus.Messaging.SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule)-Objekt, das in einer Service Bus-Warteschlange oder einem Service Bus-Thema konfiguriert wurde, über die [AuthorizationRules](/dotnet/api/microsoft.servicebus.messaging.authorizationrules)-Sammlung der entsprechenden [QueueDescription](/dotnet/api/microsoft.servicebus.messaging.queuedescription)- oder [TopicDescription](/dotnet/api/microsoft.servicebus.messaging.topicdescription)-Objekte zugreifen.

Der folgende Code zeigt, wie Sie Autorisierungsregeln für eine Warteschlange hinzufügen.

```csharp
// Create an instance of NamespaceManager for the operation
NamespaceManager nsm = NamespaceManager.CreateFromConnectionString(
    <connectionString> );
QueueDescription qd = new QueueDescription( <qPath> );

// Create a rule with send rights with keyName as "contosoQSendKey"
// and add it to the queue description.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoSendKey",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] { AccessRights.Send }));

// Create a rule with listen rights with keyName as "contosoQListenKey"
// and add it to the queue description.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoQListenKey",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] { AccessRights.Listen }));

// Create a rule with manage rights with keyName as "contosoQManageKey"
// and add it to the queue description.
// A rule with manage rights must also have send and receive rights.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoQManageKey",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] {AccessRights.Manage, AccessRights.Listen, AccessRights.Send }));

// Create the queue.
nsm.CreateQueue(qd);
```

## <a name="use-shared-access-signature-authorization"></a>Verwenden der Shared Access Signature-Authentifizierung

Anwendungen, die das Azure .NET SDK mit den .NET-Bibliotheken von Service Bus nutzen, können die SAS-Autorisierung über die [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) -Klasse verwenden. Der folgende Code veranschaulicht die Verwendung des Tokenanbieters zum Senden von Nachrichten an eine Service Bus-Warteschlange. Als Alternative zur hier dargestellten Verwendung können Sie auch ein zuvor ausgestelltes Token an die Factorymethode des Tokenanbieters übergeben.

```csharp
Uri runtimeUri = ServiceBusEnvironment.CreateServiceUri("sb",
    <yourServiceNamespace>, string.Empty);
MessagingFactory mf = MessagingFactory.Create(runtimeUri,
    TokenProvider.CreateSharedAccessSignatureTokenProvider(keyName, key));
QueueClient sendClient = mf.CreateQueueClient(qPath);

//Sending hello message to queue.
BrokeredMessage helloMessage = new BrokeredMessage("Hello, Service Bus!");
helloMessage.MessageId = "SAS-Sample-Message";
sendClient.Send(helloMessage);
```

Sie können zudem den Tokenanbieter direkt zum Ausstellen von Token für die Übergabe an andere Clients verwenden.

Verbindungszeichenfolgen können einen Regelnamen ( *SharedAccessKeyName* ) und einen Regelschlüssel ( *SharedAccessKey* ) oder ein zuvor ausgestelltes Token ( *SharedAccessSignature* ) enthalten. Wenn diese in der Verbindungszeichenfolge vorhanden sind, die an eine Konstruktor- oder eine Factorymethode übergeben wird, die eine Verbindungszeichenfolge akzeptiert, wird der SAS-Tokenanbieter automatisch erstellt und aufgefüllt.

Beachten Sie, dass Sie zum Verwenden der SAS-Autorisierung mit Service Bus Relays SAS-Schlüssel nutzen können, die für den Service Bus-Namespace konfiguriert sind. Wenn Sie ein Relay explizit im Namespaceobjekt ([NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) mit [RelayDescription](/dotnet/api/microsoft.servicebus.messaging.relaydescription)) erstellen, können Sie die SAS-Regeln nur für dieses jeweilige Relay festlegen. Zum Verwenden der SAS-Autorisierung mit Service Bus-Abonnements können Sie SAS-Schlüssel nutzen, die für einen Service Bus-Namespace oder ein Thema konfiguriert sind.

## <a name="use-the-shared-access-signature-at-http-level"></a>Verwenden einer Shared Access Signature (auf HTTP-Ebene)

Nachdem Sie nun mit der SAS-Erstellung für beliebige Entitäten in Service Bus vertraut sind, können Sie einen Vorgang vom Typ „HTTP POST“ ausführen:

```http
POST https://<yournamespace>.servicebus.windows.net/<yourentity>/messages
Content-Type: application/json
Authorization: SharedAccessSignature sr=https%3A%2F%2F<yournamespace>.servicebus.windows.net%2F<yourentity>&sig=<yoursignature from code above>&se=1438205742&skn=KeyName
ContentType: application/atom+xml;type=entry;charset=utf-8
```

Das funktioniert überall. Sie können SAS für eine Warteschlange, ein Thema oder ein Abonnement erstellen.

Wenn Sie einem Absender oder Client ein SAS-Token zuweisen, verfügt er nicht direkt über den Schlüssel, und er kann den Hash nicht umkehren und so den Schlüssel ermitteln. Dadurch haben Sie die Kontrolle darüber, worauf er wie lange Zugriff hat. Vergessen Sie nicht: Wenn Sie den Primärschlüssel in der Richtlinie ändern, werden alle auf deren Grundlage erstellten Shared Access Signatures ungültig.

## <a name="use-the-shared-access-signature-at-amqp-level"></a>Verwenden der Shared Access Signature (auf AMQP-Ebene)

Im vorherigen Abschnitt wurde gezeigt, wie Sie das SAS-Token mit einer HTTP POST-Anforderung zum Senden von Daten an den Service Bus verwenden. Wie Sie wissen, können Sie mit dem AMQP (Advanced Message Queue Protocol) auf Service Bus zugreifen, das in zahlreichen Szenarien aus Leistungsgründen bevorzugt verwendet wird. Die Verwendung des SAS-Tokens mit AMQP wird im Dokument [AMQP Claim-Based Security Version 1.0](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc) beschrieben, einem seit 2013 gültigen Arbeitsentwurf, der jedoch heute von Azure unterstützt wird.

Bevor Sie beginnen, Daten an Service Bus zu senden, muss der Herausgeber das SAS-Token in einer AMQP-Nachricht an einen ordnungsgemäß definierten AMQP-Knoten mit dem Namen **$cbs** senden (dieser kann als spezielle Warteschlange betrachtet werden, die vom Dienst zum Abrufen und Überprüfen aller SAS-Token verwendet wird). Der Herausgeber muss das Feld **ReplyTo** in der AMQP-Nachricht angeben. Hierbei handelt es sich um den Knoten, den der Dienst für die Antwort an den Herausgeber mit dem Ergebnis der Tokenüberprüfung verwendet (einfaches Anforderungs-/Antwortmuster zwischen Herausgeber und Dienst). Dieser spontan erstellte Antwortknoten beschäftigt sich mit der dynamischen Erstellung von Remoteknoten (siehe AMQP 1.0-Spezifikation). Nachdem die Gültigkeit des SAS-Tokens überprüft wurde, kann der Verleger mit dem Senden von Daten an den Dienst beginnen.

Die folgenden Schritte zeigen, wie das SAS-Token mit dem AMQP-Protokoll über die [AMQP.NET Lite](https://github.com/Azure/amqpnetlite)-Bibliothek gesendet wird. Dies ist hilfreich, wenn Sie das offizielle Service Bus SDK bei der Entwicklung in C\# nicht verwenden können (z. B. bei WinRT, .NET Compact Framework, .NET Micro Framework und Mono). Dank dieser nützlichen Bibliothek können Sie nachvollziehen, wie die anspruchsbasierte Sicherheit auf AMQP-Ebene funktioniert. Wie sie auf HTTP-Ebene funktioniert, haben Sie bereits gesehen (HTTP POST-Anforderung und SAS-Token werden im Autorisierungsheader gesendet). Wenn Sie nicht so genau über AMQP Bescheid wissen müssen, können Sie für .NET Framework-Anwendungen das offizielle Service Bus-SDK, das die erforderlichen Schritte für Sie ausführt verwenden.

### <a name="c35"></a>C&#35;

```csharp
/// <summary>
/// Send claim-based security (CBS) token
/// </summary>
/// <param name="shareAccessSignature">Shared access signature (token) to send</param>
private bool PutCbsToken(Connection connection, string sasToken)
{
    bool result = true;
    Session session = new Session(connection);

    string cbsClientAddress = "cbs-client-reply-to";
    var cbsSender = new SenderLink(session, "cbs-sender", "$cbs");
    var cbsReceiver = new ReceiverLink(session, cbsClientAddress, "$cbs");

    // construct the put-token message
    var request = new Message(sasToken);
    request.Properties = new Properties();
    request.Properties.MessageId = Guid.NewGuid().ToString();
    request.Properties.ReplyTo = cbsClientAddress;
    request.ApplicationProperties = new ApplicationProperties();
    request.ApplicationProperties["operation"] = "put-token";
    request.ApplicationProperties["type"] = "servicebus.windows.net:sastoken";
    request.ApplicationProperties["name"] = Fx.Format("amqp://{0}/{1}", sbNamespace, entity);
    cbsSender.Send(request);

    // receive the response
    var response = cbsReceiver.Receive();
    if (response == null || response.Properties == null || response.ApplicationProperties == null)
    {
        result = false;
    }
    else
    {
        int statusCode = (int)response.ApplicationProperties["status-code"];
        if (statusCode != (int)HttpStatusCode.Accepted && statusCode != (int)HttpStatusCode.OK)
        {
            result = false;
        }
    }

    // the sender/receiver may be kept open for refreshing tokens
    cbsSender.Close();
    cbsReceiver.Close();
    session.Close();

    return result;
}
```

Die `PutCbsToken()`-Methode empfängt die *Verbindung* (von der [AMQP .NET Lite-Bibliothek](https://github.com/Azure/amqpnetlite) bereitgestellte AMQP-Verbindungsklasseninstanz), die die TCP-Verbindung mit dem Dienst darstellt, und den *sasToken* -Parameter, bei dem es sich um das zu sendende SAS-Token handelt.

> [!NOTE]
> Beim Erstellen der Verbindung muss der **SASL-Authentifizierungsmechanismus auf ANONYMOUS festgelegt** werden (nicht auf den Standardmechanismus PLAIN mit Benutzername und Kennwort – dieser wird verwendet, wenn Sie das SAS-Token nicht senden müssen).
>
>

Im nächsten Schritt erstellt der Herausgeber zwei AMQP-Links zum Senden des SAS-Tokens und zum Empfangen der Antwort (Ergebnis der Tokenüberprüfung) vom Dienst.

Die AMQP-Nachricht enthält eine Reihe von Eigenschaften und mehr Informationen als eine einfache Nachricht. Das SAS-Token ist der Text der Nachricht (mit dem entsprechenden Konstruktor). Als **ReplyTo** -Eigenschaft wird der Knotenname zum Empfangen des Überprüfungsergebnisses über den Empfängerlink festgelegt. (Sie können den Namen nach Bedarf ändern, der Link wird vom Dienst dynamisch erstellt.) Anhand der letzten drei Anwendungseigenschaften/benutzerdefinierten Eigenschaften gibt der Dienst an, welche Art von Vorgang ausgeführt werden soll. Wie in der CBS-Entwurfsspezifikation beschrieben, müssen hier der **Vorgangsname** („put-token“), der **Tokentyp** (in diesem Fall `servicebus.windows.net:sastoken`) und der **„Name“ der Zielgruppe** angegeben werden, für die das Token gilt (die gesamte Entität).

Nach dem Senden des SAS-Tokens über den Senderlink muss der Herausgeber die Antwort über den Empfängerlink lesen. Bei der Antwort handelt es sich um eine einfache AMQP-Nachricht mit einer Anwendungseigenschaft namens **status-code** , die dieselben Werte wie ein HTTP-Statuscode enthalten kann.

## <a name="rights-required-for-service-bus-operations"></a>Erforderliche Rechte für Service Bus-Vorgänge

Die folgende Tabelle zeigt die Zugriffsrechte, die für verschiedene Vorgänge für Service Bus-Ressourcen erforderlich sind.

| Vorgang | Erforderlicher Anspruch | Anspruchsbereich |
| --- | --- | --- |
| **Namespace** | | |
| Konfigurieren einer Autorisierungsregel für einen Namespace |Verwalten |Jede Namespaceadresse |
| **Dienstregistrierung** | | |
| Aufzählen privater Richtlinien |Verwalten |Jede Namespaceadresse |
| Starten des Lauschvorgangs an einem Namespace |Lauschen |Jede Namespaceadresse |
| Senden von Nachrichten an einen Listener in einem Namespace |Send |Jede Namespaceadresse |
| **Warteschlange** | | |
| Erstellen einer Warteschlange |Verwalten |Jede Namespaceadresse |
| Löschen einer Warteschlange |Verwalten |Beliebige gültige Warteschlangenadresse |
| Aufzählen von Warteschlangen |Verwalten |/$Resources/Queues |
| Abrufen der Warteschlangenbeschreibung |Verwalten |Beliebige gültige Warteschlangenadresse |
| Konfigurieren einer Autorisierungsregel für eine Warteschlange |Verwalten |Beliebige gültige Warteschlangenadresse |
| Senden in die Warteschlange |Send |Beliebige gültige Warteschlangenadresse |
| Empfangen von Nachrichten aus einer Warteschlange |Lauschen |Beliebige gültige Warteschlangenadresse |
| Verwerfen oder Abschließen von Nachrichten nach dem Empfang der Nachricht im Peek/Lock-Modus |Lauschen |Beliebige gültige Warteschlangenadresse |
| Zurückstellen einer Nachricht für den späteren Abruf |Lauschen |Beliebige gültige Warteschlangenadresse |
| Platzieren einer Nachricht in die Warteschlange für unzustellbare Nachrichten |Lauschen |Beliebige gültige Warteschlangenadresse |
| Abrufen des einer Nachrichtenwarteschlangensitzung zugeordneten Status |Lauschen |Beliebige gültige Warteschlangenadresse |
| Festlegen des einer Nachrichtenwarteschlangensitzung zugeordneten Status |Lauschen |Beliebige gültige Warteschlangenadresse |
| Planen einer Nachricht für spätere Zustellung, beispielsweise [ScheduleMessageAsync()](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync#Microsoft_Azure_ServiceBus_QueueClient_ScheduleMessageAsync_Microsoft_Azure_ServiceBus_Message_System_DateTimeOffset_) |Lauschen | Beliebige gültige Warteschlangenadresse
| **Thema** | | |
| Erstellen eines Themas |Verwalten |Jede Namespaceadresse |
| Löschen eines Themas |Verwalten |Beliebige gültige Themenadresse |
| Auflisten von Themen |Verwalten |/$Resources/Topics |
| Abrufen der Themenbeschreibung |Verwalten |Beliebige gültige Themenadresse |
| Konfigurieren einer Autorisierungsregel für ein Thema |Verwalten |Beliebige gültige Themenadresse |
| Senden an das Thema |Send |Beliebige gültige Themenadresse |
| **Abonnement** | | |
| Erstellen eines Abonnements |Verwalten |Jede Namespaceadresse |
| Löschen eines Abonnements |Verwalten |../myTopic/Subscriptions/mySubscription |
| Aufzählen von Abonnements |Verwalten |../myTopic/Subscriptions |
| Abrufen der Abonnementbeschreibung |Verwalten |../myTopic/Subscriptions/mySubscription |
| Verwerfen oder Abschließen von Nachrichten nach dem Empfang der Nachricht im Peek/Lock-Modus |Lauschen |../myTopic/Subscriptions/mySubscription |
| Zurückstellen einer Nachricht für den späteren Abruf |Lauschen |../myTopic/Subscriptions/mySubscription |
| Platzieren einer Nachricht in die Warteschlange für unzustellbare Nachrichten |Lauschen |../myTopic/Subscriptions/mySubscription |
| Abrufen des einer Themensitzung zugeordneten Status |Lauschen |../myTopic/Subscriptions/mySubscription |
| Festlegen des einer Themensitzung zugeordneten Status |Lauschen |../myTopic/Subscriptions/mySubscription |
| **Regeln** | | |
| Erstellen einer Regel |Verwalten |../myTopic/Subscriptions/mySubscription |
| Löschen einer Regel |Verwalten |../myTopic/Subscriptions/mySubscription |
| Aufzählen von Regeln |Verwalten oder Lauschen |../myTopic/Subscriptions/mySubscription/Rules

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Service Bus Messaging finden Sie in folgenden Themen.

* [Service Bus-Warteschlangen, -Themen und -Abonnements](service-bus-queues-topics-subscriptions.md)
* [Verwenden von Service Bus-Warteschlangen](service-bus-dotnet-get-started-with-queues.md)
* [Verwenden von Service Bus-Themen und -Abonnements](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[Azure portal]: https://portal.azure.com