---
title: Registrierungsverwaltung
description: In diesem Thema wird erläutert, wie Geräte bei Notification Hubs registriert werden, um Pushbenachrichtigungen zu empfangen.
services: notification-hubs
documentationcenter: .net
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: fd0ee230-132c-4143-b4f9-65cef7f463a1
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.author: jowargo
ms.date: 04/08/2019
ms.openlocfilehash: 5a70eec15003a1f75a80740f269f6df3523012a8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64685393"
---
# <a name="registration-management"></a>Registrierungsverwaltung

## <a name="overview"></a>Übersicht

In diesem Thema wird erläutert, wie Geräte bei Notification Hubs registriert werden, um Pushbenachrichtigungen zu empfangen. Nach einem allgemeinen Überblick über Registrierungen werden in diesem Thema die beiden Hauptmuster zum Registrieren von Geräten vorgestellt: die direkte Registrierung beim Notification Hub über das Gerät und die Registrierung über ein Anwendungs-Back-End.

## <a name="what-is-device-registration"></a>Was ist die Geräteregistrierung?

Die Geräteregistrierung bei einem Notification Hub erfolgt mithilfe einer **Registrierung** oder **Installation**.

### <a name="registrations"></a>Registrierungen

Bei einer Registrierung wird das PNS-Handle (Platform Notification Service) für ein Gerät Tags und ggf. einer Vorlage zugeordnet. Das PNS-Handle könnte einem ChannelURI, einem Gerätetoken oder einer FCM-Registrierungs-ID entsprechen. Tags werden verwendet, um Benachrichtigungen an die richtige Gruppe von Gerätehandles weiterzuleiten. Weitere Informationen finden Sie unter [Weiterleitung und Tagausdrücke](notification-hubs-tags-segment-push-message.md). Vorlagen werden verwendet, um Transformationen pro Registrierung zu implementieren. Weitere Informationen finden Sie unter [Vorlagen](notification-hubs-templates-cross-platform-push-messages.md).

> [!NOTE]
> Azure Notification Hubs unterstützt maximal 60 Tags pro Gerät.

### <a name="installations"></a>Installationen

Eine Installation ist eine erweiterte Registrierung, die einen Behälter von Eigenschaften umfasst, die sich auf Pushvorgänge beziehen. Dies ist der neueste und beste Ansatz zum Registrieren Ihrer Geräte. Bisher wird er jedoch noch nicht vom clientseitigen .NET SDK ([Notification Hub-SDK für Back-End-Vorgänge](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)) unterstützt.  Wenn Sie die Registrierung über das Client-Gerät selbst durchführen, müssen Sie daher die [Notification Hubs-REST-API](https://docs.microsoft.com/rest/api/notificationhubs/create-overwrite-installation) zur Unterstützung von Installationen verwenden. Wenn Sie einen Back-End-Dienst verwenden, sollten Sie auch das [Notification Hub-SDK für Back-End-Vorgänge](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)verwenden können.

Im Folgenden sind die wichtigsten Vorteile bei der Verwendung von Installationen beschrieben:

- Das Erstellen oder Aktualisieren einer Installation ist vollständig idempotent. Sie können eine Installation wiederholen, ohne sich um doppelte Registrierungen sorgen zu müssen.
- Das Installationsmodell unterstützt ein spezielles Tagformat (`$InstallationId:{INSTALLATION_ID}`), das das direkte Senden von Benachrichtigungen an das spezifische Gerät ermöglicht. Wenn der Code der App beispielsweise eine Installations-ID von `joe93developer` für dieses spezifische Gerät zulässt, kann ein Entwickler dieses Gerät als Ziel zum Senden einer Benachrichtigung an das `$InstallationId:{joe93developer}`-Tag verwenden. So können Sie ein spezifisches Gerät als Ziel verwenden, ohne zusätzlichen Code schreiben zu müssen.
- Mithilfe von Installationen können Sie zudem Registrierungsteilupdates durchführen. Das Teilupdate einer Installation wird mit einer PATCH-Methode unter Verwendung des [JSON-Patch-Standards](https://tools.ietf.org/html/rfc6902)angefordert. Dies ist nützlich, wenn Sie Tags für die Registrierung aktualisieren möchten. Sie müssen nicht die gesamte Registrierung auflösen und dann alle vorherigen Tags erneut senden.

Eine Installation kann folgende Eigenschaften enthalten. Eine vollständige Liste der Installationseigenschaften finden Sie unter [Erstellen oder Überschreiben einer Installation mit der REST-API](https://docs.microsoft.com/rest/api/notificationhubs/create-overwrite-installation) oder [Installationseigenschaften](https://docs.microsoft.com/dotnet/api/microsoft.azure.notificationhubs.installation).

```json
// Example installation format to show some supported properties
{
    installationId: "",
    expirationTime: "",
    tags: [],
    platform: "",
    pushChannel: "",
    ………
    templates: {
        "templateName1" : {
            body: "",
            tags: [] },
        "templateName2" : {
            body: "",
            // Headers are for Windows Store only
            headers: {
                "X-WNS-Type": "wns/tile" }
            tags: [] }
    },
    secondaryTiles: {
        "tileId1": {
            pushChannel: "",
            tags: [],
            templates: {
                "otherTemplate": {
                    bodyTemplate: "",
                    headers: {
                        ... }
                    tags: [] }
            }
        }
    }
}
```

> [!NOTE]
> Standardmäßig laufen Registrierungen und Installationen nicht ab.

Registrierungen und Installationen müssen ein gültiges PNS-Handle für jedes Gerät bzw. jeden Kanal enthalten. Da PNS-Handles nur in einer Client-App auf dem Gerät abgerufen werden können, besteht ein Muster darin, sich direkt auf dem Gerät mit der Client-App zu registrieren. Andererseits können tagbezogene Sicherheitsaspekte und Geschäftslogik die Verwaltung der Geräteregistrierung im App-Back-End erforderlich machen.

### <a name="templates"></a>Vorlagen

Wenn Sie [Vorlagen](notification-hubs-templates-cross-platform-push-messages.md)verwenden möchten, sollte die Geräteinstallation auch alle Vorlagen, die dem jeweiligen Gerät zugeordnet sind, in einem JSON-Format enthalten (siehe Beispiel oben). Mithilfe der Vorlagennamen können unterschiedliche Vorlagen problemlos auf dasselbe Gerät abzielen.

Jeder Vorlagenname ist einem Vorlagentext und einer optionalen Gruppe von Tags zugeordnet. Darüber hinaus kann jede Plattform zusätzliche Vorlageneigenschaften aufweisen. Für den Windows Store (mit WNS) und Windows Phone 8 (mit MPNS) kann die Vorlage einen zusätzlichen Satz von Headern enthalten. Bei APNs können Sie eine Ablaufeigenschaft auf eine Konstante oder auf einen Vorlagenausdruck festlegen. Eine vollständige Liste der Installationseigenschaften finden Sie im Thema [Erstellen oder Überschreiben einer Installation mit REST](https://docs.microsoft.com/rest/api/notificationhubs/create-overwrite-installation) .

### <a name="secondary-tiles-for-windows-store-apps"></a>Sekundäre Kacheln für Windows Store-Apps

Für Windows Store-Clientanwendungen ist das Senden von Benachrichtigungen an sekundäre Kacheln und an die primäre Kachel identisch. Dies wird auch in Installationen unterstützt. Sekundäre Kacheln verfügen über einen unterschiedlichen ChannelUri, der vom SDK in der Client-App transparent behandelt wird.

Das SecondaryTiles-Wörterbuch verwendet dieselbe TileId, die zum Erstellen des SecondaryTiles-Objekts in der Windows Store-App verwendet wird. Wie beim primären ChannelUri können sich ChannelUris sekundärer Kacheln jederzeit ändern. Damit die Installationen im Notification Hub aktuell bleiben, müssen sie vom Gerät mit den aktuellen ChannelUris der sekundären Kacheln aktualisiert werden.

## <a name="registration-management-from-the-device"></a>Registrierungsverwaltung über das Gerät

Wenn die Geräteregistrierung über Client-Apps verwaltet wird, ist das Back-End nur für das Senden von Benachrichtigungen verantwortlich. Client-Apps sorgen dafür, dass PNS-Handles auf dem neuesten Stand bleiben, und registrieren Tags. Dieses Muster wird in der folgenden Abbildung veranschaulicht.

![](./media/notification-hubs-registration-management/notification-hubs-registering-on-device.png)

Zuerst ruft das Gerät das PNS-Handle aus dem PNS ab und registriert sich dann direkt beim Notification Hub. Wenn die Registrierung erfolgreich verläuft, kann das App-Back-End eine zielgerichtete Benachrichtigung an diese Registrierung senden. Weitere Informationen zum Senden von Benachrichtigungen finden Sie unter [Weiterleitung und Tagausdrücke](notification-hubs-tags-segment-push-message.md).

In diesem Fall verwenden Sie nur Lauschrechte, um über das Gerät auf die Notification Hubs zuzugreifen. Weitere Informationen finden Sie unter [Sicherheit](notification-hubs-push-notification-security.md).

Die Registrierung über das Gerät ist die einfachste Methode, birgt aber auch Nachteile:

- Eine Client-App kann ihre Tags nur aktualisieren, wenn die App aktiv ist. Angenommen, ein Benutzer verfügt über zwei Geräte, die Tags im Zusammenhang mit Sportmannschaften registrieren. Wenn sich das erste Gerät für ein zusätzliches Tag (z. B. Borussia Dortmund) registriert, empfängt das zweite Gerät erst Benachrichtigungen zu Borussia Dortmund, wenn die App auf dem zweiten Gerät ein zweites Mal ausgeführt wird. Allgemeiner ausgedrückt bedeutet dies, dass die Verwaltung von Tags möglichst über das Back-End erfolgen sollte, wenn Tags für mehrere Geräte gelten.
- Da Apps gehackt werden können, muss die Registrierung mit besonderer Sorgfalt auf bestimmte Tags beschränkt werden (wie im Abschnitt „Sicherheit auf Tagebene“ erläutert).

### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-an-installation"></a>Beispielcode zur Registrierung bei einem Notification Hub über ein Gerät mithilfe einer Installation

Momentan wird dieser Vorgang nur bei Verwendung der [Notification Hubs-REST-API](https://docs.microsoft.com/rest/api/notificationhubs/create-overwrite-installation)unterstützt.

Die Installation kann auch mit der PATCH-Methode unter Verwendung des [JSON-Patch-Standards](https://tools.ietf.org/html/rfc6902) aktualisiert werden.

```
class DeviceInstallation
{
    public string installationId { get; set; }
    public string platform { get; set; }
    public string pushChannel { get; set; }
    public string[] tags { get; set; }
}

private async Task<HttpStatusCode> CreateOrUpdateInstallationAsync(DeviceInstallation deviceInstallation,
        string hubName, string listenConnectionString)
{
    if (deviceInstallation.installationId == null)
        return HttpStatusCode.BadRequest;

    // Parse connection string (https://msdn.microsoft.com/library/azure/dn495627.aspx)
    ConnectionStringUtility connectionSaSUtil = new ConnectionStringUtility(listenConnectionString);
    string hubResource = "installations/" + deviceInstallation.installationId + "?";
    string apiVersion = "api-version=2015-04";

    // Determine the targetUri that we will sign
    string uri = connectionSaSUtil.Endpoint + hubName + "/" + hubResource + apiVersion;

    //=== Generate SaS Security Token for Authorization header ===
    // See, https://msdn.microsoft.com/library/azure/dn495627.aspx
    string SasToken = connectionSaSUtil.getSaSToken(uri, 60);

    using (var httpClient = new HttpClient())
    {
        string json = JsonConvert.SerializeObject(deviceInstallation);

        httpClient.DefaultRequestHeaders.Add("Authorization", SasToken);

        var response = await httpClient.PutAsync(uri, new StringContent(json, System.Text.Encoding.UTF8, "application/json"));
        return response.StatusCode;
    }
}

var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

string installationId = null;
var settings = ApplicationData.Current.LocalSettings.Values;

// If we have not stored an installation id in application data, create and store as application data.
if (!settings.ContainsKey("__NHInstallationId"))
{
    installationId = Guid.NewGuid().ToString();
    settings.Add("__NHInstallationId", installationId);
}

installationId = (string)settings["__NHInstallationId"];

var deviceInstallation = new DeviceInstallation
{
    installationId = installationId,
    platform = "wns",
    pushChannel = channel.Uri,
    //tags = tags.ToArray<string>()
};

var statusCode = await CreateOrUpdateInstallationAsync(deviceInstallation, 
                    "<HUBNAME>", "<SHARED LISTEN CONNECTION STRING>");

if (statusCode != HttpStatusCode.Accepted)
{
    var dialog = new MessageDialog(statusCode.ToString(), "Registration failed. Installation Id : " + installationId);
    dialog.Commands.Add(new UICommand("OK"));
    await dialog.ShowAsync();
}
else
{
    var dialog = new MessageDialog("Registration successful using installation Id : " + installationId);
    dialog.Commands.Add(new UICommand("OK"));
    await dialog.ShowAsync();
}
```

### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-a-registration"></a>Beispielcode zur Registrierung bei einem Notification Hub über ein Gerät mithilfe einer Registrierung

Durch diese Methoden wird eine Registrierung für das Gerät erstellt oder aktualisiert, auf dem sie aufgerufen werden. Dies bedeutet, dass zum Aktualisieren des Handles oder der Tags die gesamte Registrierung überschrieben werden muss. Wie bereits erwähnt, sind Registrierungen temporär. Daher sollten die aktuellen Tags, die ein bestimmtes Gerät benötigt, immer an einem zuverlässigen Ort gespeichert sein.

```
// Initialize the Notification Hub
NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(listenConnString, hubName);

// The Device id from the PNS
var pushChannel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

// If you are registering from the client itself, then store this registration id in device
// storage. Then when the app starts, you can check if a registration id already exists or not before
// creating.
var settings = ApplicationData.Current.LocalSettings.Values;

// If we have not stored a registration id in application data, store in application data.
if (!settings.ContainsKey("__NHRegistrationId"))
{
    // make sure there are no existing registrations for this push handle (used for iOS and Android)    
    string newRegistrationId = null;
    var registrations = await hub.GetRegistrationsByChannelAsync(pushChannel.Uri, 100);
    foreach (RegistrationDescription registration in registrations)
    {
        if (newRegistrationId == null)
        {
            newRegistrationId = registration.RegistrationId;
        }
        else
        {
            await hub.DeleteRegistrationAsync(registration);
        }
    }

    newRegistrationId = await hub.CreateRegistrationIdAsync();

    settings.Add("__NHRegistrationId", newRegistrationId);
}

string regId = (string)settings["__NHRegistrationId"];

RegistrationDescription registration = new WindowsRegistrationDescription(pushChannel.Uri);
registration.RegistrationId = regId;
registration.Tags = new HashSet<string>(YourTags);

try
{
    await hub.CreateOrUpdateRegistrationAsync(registration);
}
catch (Microsoft.WindowsAzure.Messaging.RegistrationGoneException e)
{
    settings.Remove("__NHRegistrationId");
}
```

## <a name="registration-management-from-a-backend"></a>Registrierungsverwaltung über ein Back-End

Zum Verwalten von Registrierungen über das Back-End muss zusätzlicher Code geschrieben werden. Bei jedem Start muss die App auf dem Gerät das aktualisierte PNS-Handle (zusammen mit den Tags und Vorlagen) für das Back-End bereitstellen, und das Back-End muss dieses Handle im Notification Hub aktualisieren. Dieses Verfahren wird in der folgenden Abbildung veranschaulicht.

![](./media/notification-hubs-registration-management/notification-hubs-registering-on-backend.png)

Die Vorteile der Verwaltung von Registrierungen über das Back-End liegen darin, dass Tags für Registrierungen selbst dann geändert werden können, wenn die entsprechende App auf dem Gerät inaktiv ist, und dass die Client-App authentifiziert werden kann, bevor ihrer Registrierung ein Tag hinzugefügt wird.

### <a name="example-code-to-register-with-a-notification-hub-from-a-backend-using-an-installation"></a>Beispielcode zur Registrierung bei einem Notification Hub über ein Back-End mithilfe einer Installation

Das Clientgerät ruft sein PNS-Handle und die relevanten Installationseigenschaften wie zuvor ab und ruft eine benutzerdefinierte API im Back-End auf, die die Registrierung ausführen und Tags autorisieren kann usw. Das Back-End kann das [Notification Hub SDK für Back-End-Vorgänge](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) nutzen.

Die Installation kann auch mit der PATCH-Methode unter Verwendung des [JSON-Patch-Standards](https://tools.ietf.org/html/rfc6902) aktualisiert werden.

```
// Initialize the Notification Hub
NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(listenConnString, hubName);

// Custom API on the backend
public async Task<HttpResponseMessage> Put(DeviceInstallation deviceUpdate)
{

    Installation installation = new Installation();
    installation.InstallationId = deviceUpdate.InstallationId;
    installation.PushChannel = deviceUpdate.Handle;
    installation.Tags = deviceUpdate.Tags;

    switch (deviceUpdate.Platform)
    {
        case "mpns":
            installation.Platform = NotificationPlatform.Mpns;
            break;
        case "wns":
            installation.Platform = NotificationPlatform.Wns;
            break;
        case "apns":
            installation.Platform = NotificationPlatform.Apns;
            break;
        case "fcm":
            installation.Platform = NotificationPlatform.Fcm;
            break;
        default:
            throw new HttpResponseException(HttpStatusCode.BadRequest);
    }


    // In the backend we can control if a user is allowed to add tags
    //installation.Tags = new List<string>(deviceUpdate.Tags);
    //installation.Tags.Add("username:" + username);

    await hub.CreateOrUpdateInstallationAsync(installation);

    return Request.CreateResponse(HttpStatusCode.OK);
}
```

### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-a-registration-id"></a>Beispielcode zur Registrierung bei einem Notification Hub über ein Gerät mithilfe einer Registrierungs-ID

Über das App-Back-End können Sie grundlegende CRUDS-Vorgänge für Registrierungen ausführen. Beispiel:

```
var hub = NotificationHubClient.CreateClientFromConnectionString("{connectionString}", "hubName");

// create a registration description object of the correct type, e.g.
var reg = new WindowsRegistrationDescription(channelUri, tags);

// Create
await hub.CreateRegistrationAsync(reg);

// Get by id
var r = await hub.GetRegistrationAsync<RegistrationDescription>("id");

// update
r.Tags.Add("myTag");

// update on hub
await hub.UpdateRegistrationAsync(r);

// delete
await hub.DeleteRegistrationAsync(r);
```

Die Nebenläufigkeit zwischen Registrierungsupdates muss vom Back-End behandelt werden. Service Bus unterstützt die Steuerung für optimistische Nebenläufigkeit für die Registrierungsverwaltung. Auf der HTTP-Ebene wird dies durch die Verwendung von ETag für Registrierungsverwaltungsvorgänge implementiert. Dieses Feature wird von Microsoft-SDKs, die eine Ausnahme auslösen, wenn ein Update aus Gründen der Nebenläufigkeit abgelehnt wird, transparent verwendet. Das Back-End ist dafür verantwortlich, diese Ausnahmen zu behandeln und das Update zu wiederholen, sofern erforderlich.
