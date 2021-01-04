---
title: Verwenden von Notification Hubs mit Python
description: Erfahren Sie mehr über die Verwendung von Azure Notification Hubs aus einer Python-Anwendung.
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 5640dd4a-a91e-4aa0-a833-93615bde49b4
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: python
ms.devlang: php
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.custom: devx-track-python
ms.openlocfilehash: f81614005a1b0374dc249187c4ff3c920b7c97e9
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2020
ms.locfileid: "92424844"
---
# <a name="how-to-use-notification-hubs-from-python"></a>Verwenden von Notofication Hubs mit Python

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

Sie können von einem Java-/PHP-/Python-/Ruby-Back-End aus über die REST-Schnittstelle für Notification Hubs, die im MSDN-Artikel [REST-APIs für Benachrichtigungshubs](/previous-versions/azure/reference/dn223264(v=azure.100)) beschrieben ist, auf alle Notification Hub-Funktionen zugreifen.

> [!NOTE]
> Dies ist eine beispielhafte Referenzimplementierung für die Implementierung der Benachrichtigungsübermittlung in Python und nicht der offiziell unterstützte Notification Hub Python SDK. Das Beispiel wurde mit Python 3.4 erstellt.

In diesem Artikel lernen Sie Folgendes:

- Erstellen eines REST-Clients für Notification Hubs-Features in Python.
- Senden von Benachrichtigungen über die Python-Schnittstelle an die Notification Hub-REST-APIs.
- Abrufen eines Abbilds der HTTP-REST-Anforderung/Antwort zu Debugging-/Lernzwecken.

Führen Sie die Schritte im Lernprogramm [Erste Schritte mit Notification Hubs](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) aus, und implementieren Sie den Back-End-Teil in Python.

> [!NOTE]
> Das Beispiel ist auf das Senden von Benachrichtigungen begrenzt und enthält keine Registrierungsverwaltung.

## <a name="client-interface"></a>Clientschnittstelle

Über die Hauptclientschnittstelle können dieselben Methoden bereitgestellt werden, die im [.NET Notification Hubs SDK](https://msdn.microsoft.com/library/jj933431.aspx)verfügbar sind. Mithilfe dieser Schnittstelle können Sie alle Tutorials und Beispiele, die derzeit auf dieser Website verfügbar sind und die von der Community im Internet beigesteuert werden, direkt umsetzen.

Den gesamten verfügbaren Code finden Sie im [Beispiel für Python-REST-Wrapper].

So erstellen Sie beispielsweise einen Client:

```python
isDebug = True
hub = NotificationHub("myConnectionString", "myNotificationHubName", isDebug)
```

So senden Sie eine Windows-Popupbenachrichtigung

```python
wns_payload = """<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Hello world!</text></binding></visual></toast>"""
hub.send_windows_notification(wns_payload)
```

## <a name="implementation"></a>Implementierung

Führen Sie, sofern nicht bereits geschehen, das Tutorial [Erste Schritte mit Notification Hubs] bis zum letzten Abschnitt aus, in dem Sie das Back-End implementieren müssen.

Alle Details für das Implementieren eines vollständigen REST-Wrappers finden sich auf [MSDN](/previous-versions/azure/reference/dn530746(v=azure.100)). In diesem Abschnitt wird die Python-Implementierung mit den Hauptschritten beschrieben, die für den Zugriff auf REST-Endpunkte von Notification Hubs und für das Senden von Benachrichtigungen erforderlich sind.

1. Analysieren der Verbindungszeichenfolge
2. Generieren des Authentifizierungstokens
3. Senden einer Benachrichtigung über die HTTP-REST-API

### <a name="parse-the-connection-string"></a>Analysieren der Verbindungszeichenfolge

Dies hier ist die Hauptklasse, die den Client implementiert, dessen Konstruktor die Verbindungszeichenfolge analysiert:

```python
class NotificationHub:
    API_VERSION = "?api-version=2013-10"
    DEBUG_SEND = "&test"

    def __init__(self, connection_string=None, hub_name=None, debug=0):
        self.HubName = hub_name
        self.Debug = debug

        # Parse connection string
        parts = connection_string.split(';')
        if len(parts) != 3:
            raise Exception("Invalid ConnectionString.")

        for part in parts:
            if part.startswith('Endpoint'):
                self.Endpoint = 'https' + part[11:]
            if part.startswith('SharedAccessKeyName'):
                self.SasKeyName = part[20:]
            if part.startswith('SharedAccessKey'):
                self.SasKeyValue = part[16:]
```

### <a name="create-security-token"></a>Erstellen des Sicherheitstokens

Einzelheiten zum Erstellen des Sicherheitstokens finden Sie [hier](/previous-versions/azure/reference/dn495627(v=azure.100)).
Fügen Sie der Klasse `NotificationHub` die folgenden Methoden hinzu, um das Token basierend auf dem URI der aktuellen Anforderung und den Anmeldeinformationen, die aus der Verbindungszeichenfolge extrahiert wurden, zu erstellen.

```python
@staticmethod
def get_expiry():
    # By default returns an expiration of 5 minutes (=300 seconds) from now
    return int(round(time.time() + 300))


@staticmethod
def encode_base64(data):
    return base64.b64encode(data)


def sign_string(self, to_sign):
    key = self.SasKeyValue.encode('utf-8')
    to_sign = to_sign.encode('utf-8')
    signed_hmac_sha256 = hmac.HMAC(key, to_sign, hashlib.sha256)
    digest = signed_hmac_sha256.digest()
    encoded_digest = self.encode_base64(digest)
    return encoded_digest


def generate_sas_token(self):
    target_uri = self.Endpoint + self.HubName
    my_uri = urllib.parse.quote(target_uri, '').lower()
    expiry = str(self.get_expiry())
    to_sign = my_uri + '\n' + expiry
    signature = urllib.parse.quote(self.sign_string(to_sign))
    auth_format = 'SharedAccessSignature sig={0}&se={1}&skn={2}&sr={3}'
    sas_token = auth_format.format(signature, expiry, self.SasKeyName, my_uri)
    return sas_token
```

### <a name="send-a-notification-using-http-rest-api"></a>Senden einer Benachrichtigung über die HTTP-REST-API

Lassen Sie uns zuerst eine Klasse definieren, die eine Benachrichtigung darstellt.

```python
class Notification:
    def __init__(self, notification_format=None, payload=None, debug=0):
        valid_formats = ['template', 'apple', 'fcm',
                         'windows', 'windowsphone', "adm", "baidu"]
        if not any(x in notification_format for x in valid_formats):
            raise Exception(
                "Invalid Notification format. " +
                "Must be one of the following - 'template', 'apple', 'fcm', 'windows', 'windowsphone', 'adm', 'baidu'")

        self.format = notification_format
        self.payload = payload

        # array with keynames for headers
        # Note: Some headers are mandatory: Windows: X-WNS-Type, WindowsPhone: X-NotificationType
        # Note: For Apple you can set Expiry with header: ServiceBusNotification-ApnsExpiry
        # in W3C DTF, YYYY-MM-DDThh:mmTZD (for example, 1997-07-16T19:20+01:00).
        self.headers = None
```

Diese Klasse ist ein Container für einen nativen Benachrichtigungstext oder einen Satz von Eigenschaften einer Benachrichtigungsvorlage sowie ein Satz von Headern, der ein Format (native Plattform oder Vorlage) und plattformspezifische Eigenschaften (wie die Apple-Ablaufeigenschaft und WNS-Header) enthält.

Alle verfügbaren Optionen finden Sie in der Dokumentation der [REST-APIs für Notification Hubs](/previous-versions/azure/reference/dn495827(v=azure.100)) und unter den Formaten der einzelnen Benachrichtigungsplattformen.

Mit dieser Klasse können Sie jetzt die Methoden zum Senden von Benachrichtigungen innerhalb der Klasse `NotificationHub` schreiben.

```python
def make_http_request(self, url, payload, headers):
    parsed_url = urllib.parse.urlparse(url)
    connection = http.client.HTTPSConnection(
        parsed_url.hostname, parsed_url.port)

    if self.Debug > 0:
        connection.set_debuglevel(self.Debug)
        # adding this querystring parameter gets detailed information about the PNS send notification outcome
        url += self.DEBUG_SEND
        print("--- REQUEST ---")
        print("URI: " + url)
        print("Headers: " + json.dumps(headers, sort_keys=True,
                                       indent=4, separators=(' ', ': ')))
        print("--- END REQUEST ---\n")

    connection.request('POST', url, payload, headers)
    response = connection.getresponse()

    if self.Debug > 0:
        # print out detailed response information for debugging purpose
        print("\n\n--- RESPONSE ---")
        print(str(response.status) + " " + response.reason)
        print(response.msg)
        print(response.read())
        print("--- END RESPONSE ---")

    elif response.status != 201:
        # Successful outcome of send message is HTTP 201 - Created
        raise Exception(
            "Error sending notification. Received HTTP code " + str(response.status) + " " + response.reason)

    connection.close()


def send_notification(self, notification, tag_or_tag_expression=None):
    url = self.Endpoint + self.HubName + '/messages' + self.API_VERSION

    json_platforms = ['template', 'apple', 'fcm', 'adm', 'baidu']

    if any(x in notification.format for x in json_platforms):
        content_type = "application/json"
        payload_to_send = json.dumps(notification.payload)
    else:
        content_type = "application/xml"
        payload_to_send = notification.payload

    headers = {
        'Content-type': content_type,
        'Authorization': self.generate_sas_token(),
        'ServiceBusNotification-Format': notification.format
    }

    if isinstance(tag_or_tag_expression, set):
        tag_list = ' || '.join(tag_or_tag_expression)
    else:
        tag_list = tag_or_tag_expression

    # add the tags/tag expressions to the headers collection
    if tag_list != "":
        headers.update({'ServiceBusNotification-Tags': tag_list})

    # add any custom headers to the headers collection that the user may have added
    if notification.headers is not None:
        headers.update(notification.headers)

    self.make_http_request(url, payload_to_send, headers)


def send_apple_notification(self, payload, tags=""):
    nh = Notification("apple", payload)
    self.send_notification(nh, tags)


def send_fcm_notification(self, payload, tags=""):
    nh = Notification("fcm", payload)
    self.send_notification(nh, tags)


def send_adm_notification(self, payload, tags=""):
    nh = Notification("adm", payload)
    self.send_notification(nh, tags)


def send_baidu_notification(self, payload, tags=""):
    nh = Notification("baidu", payload)
    self.send_notification(nh, tags)


def send_mpns_notification(self, payload, tags=""):
    nh = Notification("windowsphone", payload)

    if "<wp:Toast>" in payload:
        nh.headers = {'X-WindowsPhone-Target': 'toast',
                      'X-NotificationClass': '2'}
    elif "<wp:Tile>" in payload:
        nh.headers = {'X-WindowsPhone-Target': 'tile',
                      'X-NotificationClass': '1'}

    self.send_notification(nh, tags)


def send_windows_notification(self, payload, tags=""):
    nh = Notification("windows", payload)

    if "<toast>" in payload:
        nh.headers = {'X-WNS-Type': 'wns/toast'}
    elif "<tile>" in payload:
        nh.headers = {'X-WNS-Type': 'wns/tile'}
    elif "<badge>" in payload:
        nh.headers = {'X-WNS-Type': 'wns/badge'}

    self.send_notification(nh, tags)


def send_template_notification(self, properties, tags=""):
    nh = Notification("template", properties)
    self.send_notification(nh, tags)
```

Diese Methoden senden eine HTTP-POST-Anforderung mit dem ordnungsgemäßen Text und Headern zum Senden der Benachrichtigung an den „/messages“-Endpunkt des Benachrichtigungs-Hubs.

### <a name="using-debug-property-to-enable-detailed-logging"></a>Aktivieren der ausführlichen Protokollierung mithilfe der Debug-Eigenschaft

Durch das Aktivieren der Debug-Eigenschaft während der Initialisierung des Notification Hubs werden detaillierte Protokollinformationen zu HTTP-Anforderung und -Antwort sowie ein detailliertes Sendeergebnis für die Benachrichtigungsnachricht geschrieben.
Die [„TestSend“-Eigenschaft für Notification Hubs](/previous-versions/azure/reference/dn495827(v=azure.100)) gibt ausführliche Informationen zum Sendeergebnis der Benachrichtigung zurück.
Wenn Sie diese Eigenschaft verwenden möchten, initialisieren Sie mit dem folgenden Code:

```python
hub = NotificationHub("myConnectionString", "myNotificationHubName", isDebug)
```

Der HTTP-URL der Sendeanforderung des Notification Hubs wird eine Testabfragezeichenfolge als Ergebnis angehängt.

## <a name="complete-the-tutorial"></a><a name="complete-tutorial"></a>Abschließen des Lernprogramms

Sie können jetzt das Lernprogramm "Erste Schritte" abschließen, indem Sie die Benachrichtigung von einem Python-Back-End aus senden.

Initialisieren Sie Ihren Notification Hubs-Client (ersetzen Sie die Verbindungszeichenfolge und den Hubnamen wie im [Erste Schritte mit Notification Hubs]beschrieben):

```python
hub = NotificationHub("myConnectionString", "myNotificationHubName")
```

Fügen Sie dann den Sendecode je nach mobiler Zielplattform hinzu. Mit diesem Beispiel werden außerdem Methoden auf höherer Ebene hinzugefügt, um das Senden von Benachrichtigungen auf Plattformbasis zu aktivieren, z.B. „send_windows_notification“ (für Windows), „send_apple_notification“ (für Apple) usw.

### <a name="windows-store-and-windows-phone-81-non-silverlight"></a>Windows Store und Windows Phone 8.1 (nicht Silverlight)

```python
wns_payload = """<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Test</text></binding></visual></toast>"""
hub.send_windows_notification(wns_payload)
```

### <a name="windows-phone-80-and-81-silverlight"></a>Windows Phone 8.0 und 8.1 Silverlight

```python
hub.send_mpns_notification(toast)
```

### <a name="ios"></a>iOS

```python
alert_payload = {
    'data':
        {
            'msg': 'Hello!'
        }
}
hub.send_apple_notification(alert_payload)
```

### <a name="android"></a>Android

```python
fcm_payload = {
    'data':
        {
            'msg': 'Hello!'
        }
}
hub.send_fcm_notification(fcm_payload)
```

### <a name="kindle-fire"></a>Kindle Fire

```python
adm_payload = {
    'data':
        {
            'msg': 'Hello!'
        }
}
hub.send_adm_notification(adm_payload)
```

### <a name="baidu"></a>Baidu

```python
baidu_payload = {
    'data':
        {
            'msg': 'Hello!'
        }
}
hub.send_baidu_notification(baidu_payload)
```

Beim Ausführen des Python-Codes sollte eine Benachrichtigung erstellt und auf dem Zielgerät angezeigt werden.

## <a name="examples"></a>Beispiele

### <a name="enabling-the-debug-property"></a>Aktivieren der Eigenschaft `debug`

Wenn Sie beim Initialisieren von „NotificationHub“ das Flag „debug“ aktivieren, werden die detaillierte HTTP-Anforderung und -Antwort sowie das Benachrichtigungsergebnis wie folgt angezeigt. Dabei können Sie erkennen, welche HTTP-Header in der Anforderung übergeben werden und welche HTTP-Antwort vom Notification Hub empfangen wurde:

![Screenshot einer Konsole mit den Details der HTTP-Anforderung und -Antwort sowie den Meldungen mit dem Benachrichtigungsergebnis (rot umrandet).][1]

Das detaillierte Ergebnis des Notification Hubs wird angezeigt, z.B.

- wenn die Nachricht erfolgreich zum Pushbenachrichtigungsdienst gesendet wurde.
    ```xml
    <Outcome>The Notification was successfully sent to the Push Notification System</Outcome>
    ```
- Wenn für eine Pushbenachrichtigung keine Ziele gefunden wurden, wird wahrscheinlich die folgende Ausgabe in der Antwort angezeigt (dies besagt, dass keine Registrierungen zum Übermitteln der Benachrichtigung gefunden wurden, möglicherweise aufgrund nicht übereinstimmender Registrierungs-Tags).
    ```xml
    '<NotificationOutcome xmlns="http://schemas.microsoft.com/netservices/2010/10/servicebus/connect" xmlns:i="https://www.w3.org/2001/XMLSchema-instance"><Success>0</Success><Failure>0</Failure><Results i:nil="true"/></NotificationOutcome>'
    ```

### <a name="broadcast-toast-notification-to-windows"></a>Broadcast-Popupbenachrichtigung an Windows

Beachten Sie die Header, die beim Senden einer Broadcast-Popupbenachrichtigung an einen Windows-Client gesendet werden.

```python
hub.send_windows_notification(wns_payload)
```

![Screenshot einer Konsole mit den Details der HTTP-Anforderung und dem Service Bus-Benachrichtigungsformat sowie den XWNS-Typwerten (rot umrandet).][2]

### <a name="send-notification-specifying-a-tag-or-tag-expression"></a>Senden einer Benachrichtigung mit Angabe eines Tags (oder Tag-Ausdrucks)

Beachten Sie den HTTP-Header des Tags, der der HTTP-Anforderung hinzugefügt wird (im folgenden Beispiel wird die Benachrichtigung nur an Registrierungen mit der „payload“ des Typs „sports“ gesendet).

```python
hub.send_windows_notification(wns_payload, "sports")
```

![Screenshot einer Konsole mit den Details der HTTP-Anforderung, dem Service Bus-Benachrichtigungsformat, dem Service Bus-Benachrichtigungstag sowie den XWNS-Typwerten (rot umrandet).][3]

### <a name="send-notification-specifying-multiple-tags"></a>Senden einer Benachrichtigung mit Angabe mehrerer Tags

Beachten Sie, wie sich der HTTP-Header der Tags ändert, wenn mehrere Tags gesendet werden.

```python
tags = {'sports', 'politics'}
hub.send_windows_notification(wns_payload, tags)
```

![Screenshot einer Konsole mit den Details der HTTP-Anforderung, dem Service Bus-Benachrichtigungsformat, mehreren Service Bus-Benachrichtigungstags sowie den XWNS-Typwerten (rot umrandet).][4]

### <a name="templated-notification"></a>Auf Vorlagen basierende Benachrichtigung

Beachten Sie, dass sich der Format-HTTP-Header ändert und der "payload"-Text als Teil des HTTP-Anforderungstexts gesendet wird:

**Clientseitig (registrierte Vorlage):**

```python
var template = @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(greeting_en)</text></binding></visual></toast>";
```

**Serverseitig (Senden der Nutzlast):**

```python
template_payload = {'greeting_en': 'Hello', 'greeting_fr': 'Salut'}
hub.send_template_notification(template_payload)
```

![Screenshot einer Konsole mit den Details der HTTP-Anforderung, dem Inhaltstyp und den Werten für das Service Bus-Benachrichtigungsformat (rot umrandet).][5]

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben wir gezeigt, wie Sie einen Python-REST-Client für Notification Hubs erstellen. Hier können Sie folgende Aktionen ausführen:

- Laden Sie das vollständige [Beispiel für Python-REST-Wrapper] herunter, das den gesamten Code in diesem Artikel enthält.
- Erfahren Sie mehr über das Markierungsfeature von Notification Hubs im [Lernprogramm zum Übermitteln aktueller Nachrichten]
- Erfahren Sie mehr über das Templates-Feature von Notification Hubs im [Lernprogramm zum Lokalisieren von Nachrichten]

<!-- URLs -->
[Beispiel für Python-REST-Wrapper]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-python
[Erste Schritte mit Notification Hubs]: ./notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Lernprogramm zum Übermitteln aktueller Nachrichten]: ./notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Lernprogramm zum Lokalisieren von Nachrichten]: ./notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md

<!-- Images. -->
[1]: ./media/notification-hubs-python-backend-how-to/DetailedLoggingInfo.png
[2]: ./media/notification-hubs-python-backend-how-to/BroadcastScenario.png
[3]: ./media/notification-hubs-python-backend-how-to/SendWithOneTag.png
[4]: ./media/notification-hubs-python-backend-how-to/SendWithMultipleTags.png
[5]: ./media/notification-hubs-python-backend-how-to/TemplatedNotification.png
