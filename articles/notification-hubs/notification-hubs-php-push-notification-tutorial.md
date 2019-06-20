---
title: Verwenden von Notification Hubs mit PHP
description: Erfahren Sie, wie Sie Azure Notification Hubs über ein PHP-Back-End verwenden.
services: notification-hubs
documentationcenter: ''
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 0156f994-96d0-4878-b07b-49b7be4fd856
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: php
ms.devlang: php
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: 054edaf321d90015840fd84e1697fca742fd7e1e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60872176"
---
# <a name="how-to-use-notification-hubs-from-php"></a>Verwenden von Notification Hubs von PHP aus

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

Sie können wie im MSDN-Thema [REST-APIs für Notification Hubs](https://msdn.microsoft.com/library/dn223264.aspx) beschrieben mithilfe der Notification Hubs-REST-Schnittstelle über ein Java-, PHP- oder Ruby-Back-End auf alle Features von Notification Hubs zugreifen.

In diesem Thema wird Folgendes erläutert:

* Erstellen eines REST-Clients für Notification Hubs-Features in PHP
* Führen Sie die Schritte im [Tutorial zu den ersten Schritten](notification-hubs-ios-apple-push-notification-apns-get-started.md) für die mobile Plattform Ihrer Wahl aus, und implementieren Sie den Back-End-Teil in PHP.

## <a name="client-interface"></a>Clientschnittstelle

Die Hauptschnittstelle des Clients kann dieselben Methoden bereitstellen, die im [.NET Notification Hubs SDK](https://msdn.microsoft.com/library/jj933431.aspx) verfügbar sind. Damit können Sie alle Tutorials und Beispiele, die aktuell auf dieser Website erhältlich sind und von der Community im Internet beigetragen werden, direkt umsetzen.

Sie finden den gesamten Code im [PHP REST-Wrapper-Beispiel].

So erstellen Sie beispielsweise einen Client:

    ```php
    $hub = new NotificationHub("connection string", "hubname");
    ```

So senden Sie eine native iOS-Benachrichtigung:

    ```php
    $notification = new Notification("apple", '{"aps":{"alert": "Hello!"}}');
    $hub->sendNotification($notification, null);
    ```

## <a name="implementation"></a>Implementierung

Führen Sie, sofern nicht bereits geschehen, das [Erste-Schritte-Lernprogramm] bis zum letzten Abschnitt aus, in dem Sie das Back-End implementieren müssen.
Sie können auch den Code aus dem [PHP REST-Wrapper-Beispiel] verwenden und direkt mit dem Abschnitt [Abschließen des Tutorials](#complete-tutorial) fortfahren.

Alle Details für das Implementieren eines vollständigen REST-Wrappers finden sich auf [MSDN](https://msdn.microsoft.com/library/dn530746.aspx). In diesem Abschnitt beschreiben wir die PHP-Implementierung der Hauptschritte, die für den Zugriff auf REST-Endpunkte von Notification Hubs erforderlich sind:

1. Analysieren der Verbindungszeichenfolge
2. Generieren des Authentifizierungstokens
3. Durchführen des HTTP-Aufrufs

### <a name="parse-the-connection-string"></a>Analysieren der Verbindungszeichenfolge

Hier ist die Hauptklasse, die den Client implementiert, dessen Konstruktor die Verbindungszeichenfolge analysiert:

    ```php
    class NotificationHub {
        const API_VERSION = "?api-version=2013-10";

        private $endpoint;
        private $hubPath;
        private $sasKeyName;
        private $sasKeyValue;

        function __construct($connectionString, $hubPath) {
            $this->hubPath = $hubPath;

            $this->parseConnectionString($connectionString);
        }

        private function parseConnectionString($connectionString) {
            $parts = explode(";", $connectionString);
            if (sizeof($parts) != 3) {
                throw new Exception("Error parsing connection string: " . $connectionString);
            }

            foreach ($parts as $part) {
                if (strpos($part, "Endpoint") === 0) {
                    $this->endpoint = "https" . substr($part, 11);
                } else if (strpos($part, "SharedAccessKeyName") === 0) {
                    $this->sasKeyName = substr($part, 20);
                } else if (strpos($part, "SharedAccessKey") === 0) {
                    $this->sasKeyValue = substr($part, 16);
                }
            }
        }
    }
    ```

### <a name="create-a-security-token"></a>Erstellen eines Sicherheitstokens

Informationen zum [Erstellen eines SAS-Sicherheitstokens](https://docs.microsoft.com/previous-versions/azure/reference/dn495627(v=azure.100)#create-sas-security-token) finden Sie in der Azure-Dokumentation.

Fügen Sie der `NotificationHub`-Klasse die `generateSasToken`-Methode hinzu, um das Token basierend auf dem URI der aktuellen Anforderung und den aus der Verbindungszeichenfolge extrahierten Anmeldeinformationen zu erstellen.

    ```php
    private function generateSasToken($uri) {
        $targetUri = strtolower(rawurlencode(strtolower($uri)));

        $expires = time();
        $expiresInMins = 60;
        $expires = $expires + $expiresInMins * 60;
        $toSign = $targetUri . "\n" . $expires;

        $signature = rawurlencode(base64_encode(hash_hmac('sha256', $toSign, $this->sasKeyValue, TRUE)));

        $token = "SharedAccessSignature sr=" . $targetUri . "&sig="
                    . $signature . "&se=" . $expires . "&skn=" . $this->sasKeyName;

        return $token;
    }
    ```

### <a name="send-a-notification"></a>Senden einer Benachrichtigung

Zuerst definieren wir eine Klasse, die eine Benachrichtigung darstellt.

    ```php
    class Notification {
        public $format;
        public $payload;

        # array with keynames for headers
        # Note: Some headers are mandatory: Windows: X-WNS-Type, WindowsPhone: X-NotificationType
        # Note: For Apple you can set Expiry with header: ServiceBusNotification-ApnsExpiry in W3C DTF, YYYY-MM-DDThh:mmTZD (for example, 1997-07-16T19:20+01:00).
        public $headers;

        function __construct($format, $payload) {
            if (!in_array($format, ["template", "apple", "windows", "fcm", "windowsphone"])) {
                throw new Exception('Invalid format: ' . $format);
            }

            $this->format = $format;
            $this->payload = $payload;
        }
    }
    ```

Diese Klasse ist ein Container für einen nativen Benachrichtigungstext oder ein Satz von Eigenschaften einer Benachrichtigungsvorlage sowie ein Satz von Headern, die ein Format (native Plattform oder Vorlage) und plattformspezifische Eigenschaften (wie die Apple-Ablaufeigenschaft und WNS-Header) enthalten.

Alle verfügbaren Optionen finden Sie in der Dokumentation der [REST-APIs für Notification Hubs](https://msdn.microsoft.com/library/dn495827.aspx) und unter den Formaten der einzelnen Benachrichtigungsplattformen.

Mit dieser Klasse können wir jetzt die Methoden zum Senden von Benachrichtigungen in der `NotificationHub`-Klasse schreiben:

    ```php
    public function sendNotification($notification, $tagsOrTagExpression="") {
        if (is_array($tagsOrTagExpression)) {
            $tagExpression = implode(" || ", $tagsOrTagExpression);
        } else {
            $tagExpression = $tagsOrTagExpression;
        }

        # build uri
        $uri = $this->endpoint . $this->hubPath . "/messages" . NotificationHub::API_VERSION;
        $ch = curl_init($uri);

        if (in_array($notification->format, ["template", "apple", "fcm"])) {
            $contentType = "application/json";
        } else {
            $contentType = "application/xml";
        }

        $token = $this->generateSasToken($uri);

        $headers = [
            'Authorization: '.$token,
            'Content-Type: '.$contentType,
            'ServiceBusNotification-Format: '.$notification->format
        ];

        if ("" !== $tagExpression) {
            $headers[] = 'ServiceBusNotification-Tags: '.$tagExpression;
        }

        # add headers for other platforms
        if (is_array($notification->headers)) {
            $headers = array_merge($headers, $notification->headers);
        }

        curl_setopt_array($ch, array(
            CURLOPT_POST => TRUE,
            CURLOPT_RETURNTRANSFER => TRUE,
            CURLOPT_SSL_VERIFYPEER => FALSE,
            CURLOPT_HTTPHEADER => $headers,
            CURLOPT_POSTFIELDS => $notification->payload
        ));

        // Send the request
        $response = curl_exec($ch);

        // Check for errors
        if($response === FALSE){
            throw new Exception(curl_error($ch));
        }

        $info = curl_getinfo($ch);

        if ($info['http_code'] <> 201) {
            throw new Exception('Error sending notification: '. $info['http_code'] . ' msg: ' . $response);
        }
    } 
    ```

Die obigen Methoden senden eine HTTP-POST-Anforderung mit dem korrekten Text und Headern an den `/messages`-Endpunkt des Notification Hub, um die Benachrichtigung zu senden.

## <a name="complete-tutorial"></a>Abschließen des Lernprogramms

Sie können jetzt das Tutorial zu den ersten Schritten abschließen, indem Sie die Benachrichtigung von einem PHP-Back-End senden.

Initialisieren Sie Ihren Notification Hubs-Client (ersetzen Sie die Verbindungszeichenfolge und den Hubnamen wie im [Erste-Schritte-Lernprogramm]beschrieben):

    ```php
    $hub = new NotificationHub("connection string", "hubname");
    ```

Fügen Sie dann den Sendecode je nach mobiler Zielplattform hinzu.

### <a name="windows-store-and-windows-phone-81-non-silverlight"></a>Windows Store und Windows Phone 8.1 (nicht Silverlight)

    ```php
    $toast = '<toast><visual><binding template="ToastText01"><text id="1">Hello from PHP!</text></binding></visual></toast>';
    $notification = new Notification("windows", $toast);
    $notification->headers[] = 'X-WNS-Type: wns/toast';
    $hub->sendNotification($notification, null);
    ```

### <a name="ios"></a>iOS

    ```php
    $alert = '{"aps":{"alert":"Hello from PHP!"}}';
    $notification = new Notification("apple", $alert);
    $hub->sendNotification($notification, null);
    ```

### <a name="android"></a>Android

    ```php
    $message = '{"data":{"msg":"Hello from PHP!"}}';
    $notification = new Notification("fcm", $message);
    $hub->sendNotification($notification, null);
    ```

### <a name="windows-phone-80-and-81-silverlight"></a>Windows Phone 8.0 und 8.1 Silverlight

    ```php
    $toast = '<?xml version="1.0" encoding="utf-8"?>' .
                '<wp:Notification xmlns:wp="WPNotification">' .
                   '<wp:Toast>' .
                        '<wp:Text1>Hello from PHP!</wp:Text1>' .
                   '</wp:Toast> ' .
                '</wp:Notification>';
    $notification = new Notification("windowsphone", $toast);
    $notification->headers[] = 'X-WindowsPhone-Target : toast';
    $notification->headers[] = 'X-NotificationClass : 2';
    $hub->sendNotification($notification, null);
    ```

### <a name="kindle-fire"></a>Kindle Fire

    ```php
    $message = '{"data":{"msg":"Hello from PHP!"}}';
    $notification = new Notification("adm", $message);
    $hub->sendNotification($notification, null);
    ```

Beim Ausführen des PHP-Codes sollte jetzt eine Benachrichtigung erstellt werden, die auf dem Zielgerät angezeigt wird.

## <a name="next-steps"></a>Nächste Schritte

In diesem Thema haben wir gezeigt, wie Sie einen einfachen Java-REST-Client für Notification Hubs erstellen. Mögliche nächste Schritte:

* Laden Sie das vollständige [PHP REST-Wrapper-Beispiel]herunter, das den gesamten vorstehenden Code enthält.
* Erfahren Sie mehr über das Tagging-Feature von Notification Hubs im [Breaking News tutorial]
* Lernen Sie im [Benachrichtigen von Benutzern-Lernprogramm], wie Sie Pushbenachrichtigungen an einzelne Benutzer senden.

Weitere Informationen finden Sie außerdem im [PHP Developer Center](https://azure.microsoft.com/develop/php/).

[PHP REST-Wrapper-Beispiel]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-php
[Erste-Schritte-Lernprogramm]: https://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
