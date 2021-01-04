---
title: Verwenden von Twilio für Sprachanrufe und SMS (PHP) | Microsoft Docs
description: Erfahren Sie, wie Sie mit dem Twilio API-Dienst in Azure einen Telefonanruf tätigen und eine SMS-Nachricht senden. Die Codebeispiele wurden in PHP geschrieben.
documentationcenter: php
services: ''
author: georgewallace
ms.assetid: 007f22e3-ac75-4868-8315-da000c2e0dd0
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.openlocfilehash: bf1ab01b39d594002bc5e677ffe6c3049fbb91ce
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95521018"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-php"></a>Verwenden von Twilio für Telefonie- und SMS-Funktionen in PHP
Dieser Leitfaden veranschaulicht die Ausführung allgemeiner Programmierungsaufgaben mit dem Twilio-API-Dienst in Azure. Die Szenarien behandeln das Tätigen eines Telefonanrufs und das Senden einer Kurznachricht (SMS). Weitere Informationen zu Twilio und zur Verwendung von Telefonie und SMS in Ihren Anwendungen finden Sie im Abschnitt [Nächste Schritte](#NextSteps) .

## <a name="what-is-twilio"></a><a id="WhatIs"></a>Was ist Twilio?
Twilio ist ein leistungsstarkes und zukunftsorientiertes Tool für die Unternehmenskommunikation, mit dem Entwickler Telefonie-, VoIP- und Messagingfunktionalitäten in Anwendungen einbetten können. Die gesamte in einer cloudbasierten, globalen Umgebung benötigte Infrastruktur wird virtualisiert und über die Twilio-API-Kommunikationsplattform zur Verfügung gestellt. Anwendungen können einfach erstellt und skaliert werden. Sie profitieren von flexiblen Pay-as-you-go-Preisen und der Zuverlässigkeit der Cloud.

**Twilio Voice** ermöglicht Anwendungen das Tätigen und Entgegennehmen von Telefonanrufen. **Twilio SMS** ermöglicht Ihren Anwendungen das Senden und Empfangen von SMS-Nachrichten. **Twilio Client** ermöglicht Ihnen das Tätigen von VoIP-Anrufen von jedem Telefon, Tablet-PC oder Browser und unterstützt WebRTC.

## <a name="twilio-pricing-and-special-offers"></a><a id="Pricing"></a>Twilio-Preise und -Sonderangebote
Azure-Kunden erhalten ein [Sonderangebot](https://www.twilio.com/azure): eine Twilio-Gutschrift in Höhe von 10 US-Dollar bei einem Upgrade Ihres Twilio-Kontos. Diese Twilio-Gutschrift kann für jede beliebige Twilio-Nutzung ausgegeben werden (eine Gutschrift von 10 US-Dollar reicht für den Versand von 1.000 SMS-Nachrichten oder für bis zu 1.000 eingehende Telefonminuten, abhängig vom Standort der Telefonnummer und dem Nachrichten- oder Anrufziel). Lösen Sie diese Twilio-Gutschrift ein, und beginnen Sie unter [https://ahoy.twilio.com/azure](https://ahoy.twilio.com/azure).

Twilio ist ein Pay-as-you-go-Dienst, also vertragsungebunden. Es fallen keine Einrichtungsgebühren an, und Sie können Ihr Konto jederzeit schließen. Weitere Informationen finden Sie unter [Twilio-Preise][twilio_pricing].

## <a name="concepts"></a><a id="Concepts"></a>Konzepte
Die Twilio-API ist eine RESTful-API, die Telefonie- und SMS-Funktionen für Anwendungen bereitstellt. Clientbibliotheken stehen in mehreren Sprachen zur Verfügung. Eine Liste finden Sie unter [Twilio-API-Bibliotheken][twilio_libraries].

Schlüsselaspekte der Twilio API sind Twilio-Verben und die Twilio Markup Language (TwiML).

### <a name="twilio-verbs"></a><a id="Verbs"></a>Twilio-Verben
Die API verwendet Twilio-Verben; so weist beispielsweise das Verb **&lt;Say&gt;** Twilio an, eine Nachricht in einem Anruf akustisch zu übermitteln.

Nachfolgend finden Sie eine Liste mit Twilio-Verben. Andere Verben und Funktionen werden in der [Dokumentation zur Twilio Markup Language](https://www.twilio.com/docs/api/twiml)erläutert.

* **&lt;Dial:&gt;** Verbindet den Anrufer mit einem anderen Telefon.
* **&lt;Gather:&gt;** Erfasst Ziffern, die über die Telefontasten eingegeben werden.
* **&lt;Hangup:&gt;** Beendet einen Anruf.
* **&lt;Play:&gt;** Gibt eine Audiodatei wieder.
* **&lt;Pause:&gt;** Wartet ohne Ton für eine angegebene Anzahl von Sekunden.
* **&lt;Record:&gt;** Zeichnet die Stimme des Anrufers auf und gibt eine URL einer Datei mit der Aufzeichnung zurück.
* **&lt;Redirect:&gt;** Überträgt die Steuerung eines Anrufs oder einer SMS an die TwiML an einer anderen URL.
* **&lt;Reject:&gt;** Lehnt einen bei Ihrer Twilio-Nummer eingehenden Anruf ab, ohne dass Ihnen Kosten entstehen.
* **&lt;Say:&gt;** Konvertiert Text in Sprache für einen Anruf.
* **&lt;Sms:&gt;** Sendet eine SMS-Nachricht.

### <a name="twiml"></a><a id="TwiML"></a>TwiML
TwiML ist ein Satz XML-basierter Anweisungen auf Grundlage der Twilio-Verben, die Twilio anweisen, wie ein Anruf oder eine SMS verarbeitet werden soll.

Die TwiML im folgenden Beispiel konvertiert den Text **Hello World** in Sprache.

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<Response>
    <Say>Hello World</Say>
</Response>
```

Wenn eine Anwendung die Twilio-API aufruft, ist einer der API-Parameter die URL, die die TwiML-Antwort zurückgibt. Zu Entwicklungszwecken können Sie die von Twilio zur Verfügung gestellten URLs verwenden, um die von Ihren Anwendungen verwendeten TwiML-Antworten bereitzustellen. Sie können auch Ihre eigenen URLs hosten, um TwiML-Antworten zu generieren; eine weitere Option ist die Verwendung des **TwiMLResponse** -Objekts.

Weitere Informationen zu Twilio-Verben, ihren Attributen sowie TwiML finden Sie unter [TwiML][twiml]. Weitere Informationen zur Twilio-API finden Sie unter [Twilio-API][twilio_api].

## <a name="create-a-twilio-account"></a><a id="CreateAccount"></a>Erstellen von Twilio-Konten
Wenn Sie ein Twilio-Konto erstellen möchten, melden Sie sich bei [Try Twilio][try_twilio] an. Sie können mit einem kostenlosen Konto beginnen und später ein Upgrade vornehmen.

Wenn Sie sich für ein Twilio-Konto anmelden, erhalten Sie eine Konto-ID und ein Authentifizierungstoken. Beide Angaben benötigen Sie zum Tätigen von Twilio-API-Anrufen. Bewahren Sie Ihr Authentifizierungstoken sicher auf, um unbefugten Zugriff auf Ihr Konto zu verhindern. Ihre Konto-ID und das Authentifizierungstoken können Sie auf der [Twilio-Kontoseite][twilio_account] in den Feldern **ACCOUNT SID** bzw. **AUTH TOKEN** einsehen.

## <a name="create-a-php-application"></a><a id="create_app"></a>Erstellen einer PHP-Anwendung
PHP-Anwendungen, die den Twilio-Dienst verwenden und in Azure ausgeführt werden, unterscheiden sich nicht von anderen PHP-Anwendungen, die den Twilio-Dienst verwenden. Twilio-Dienste sind REST-basiert und können in PHP auf verschiedene Arten aufgerufen werden. Dieser Artikel konzentriert sich auf die Verwendung von Twilio-Diensten mit der [Twilio-Bibliothek für PHP von GitHub][twilio_php]. Weitere Informationen zur Verwendung der Twilio-Bibliothek für PHP finden Sie unter [https://www.twilio.com/docs/libraries/php][twilio_lib_docs].

Ausführliche Anweisungen zum Erstellen und Bereitstellen von Twilio-/PHP-Anwendungen in Azure finden Sie unter [Tätigen eines Telefonanrufs mithilfe von Twilio in einer PHP-Anwendung auf Azure][howto_phonecall_php].

## <a name="configure-your-application-to-use-twilio-libraries"></a><a id="configure_app"></a>Konfigurieren einer Anwendung für die Verwendung von Twilio-Bibliotheken
Sie können die Anwendung auf zwei verschiedene Arten für die Twilio-Bibliothek in PHP konfigurieren:

1. Sie können die Twilio-Bibliothek für PHP von GitHub ([https://github.com/twilio/twilio-php][twilio_php]) herunterladen und das Verzeichnis **Services** Ihrer Anwendung hinzufügen.
   
    ODER
2. Sie können die Twilio-Bibliothek für PHP als PEAR-Paket installieren. Führen Sie dazu die folgenden Befehle aus:

    ```bash
    $ pear channel-discover twilio.github.com/pear
    $ pear install twilio/Services_Twilio
    ```

Nachdem Sie die Twilio-Bibliothek für PHP installiert haben, können Sie eine **require_once**-Anweisung am Anfang Ihrer PHP-Dateien hinzufügen, um auf die Bibliothek zu verweisen:

```php
require_once 'Services/Twilio.php';
```

Weitere Informationen finden Sie unter [https://github.com/twilio/twilio-php/blob/master/README.md][twilio_github_readme].

## <a name="how-to-make-an-outgoing-call"></a><a id="howto_make_call"></a>Vorgehensweise: Tätigen von ausgehenden Anrufen
Nachfolgend wird erläutert, wie Sie einen ausgehenden Anruf mit der **Services_Twilio**-Klasse tätigen. Dieser Code verwendet eine von Twilio bereitgestellte Website für die Rückgabe der TwiML-Antwort (Twilio Markup Language). Geben Sie Ihre Werte für die Telefonnummern **From** und **To** ein, und stellen Sie sicher, dass Sie die **From**-Telefonnummer für Ihr Twilio-Konto verifizieren, bevor Sie den Code ausführen.

```php
// Include the Twilio PHP library.
require_once 'Services/Twilio.php';

// Library version.
$version = "2010-04-01";

// Set your account ID and authentication token.
$sid = "your_twilio_account_sid";
$token = "your_twilio_authentication_token";

// The number of the phone initiating the call.
$from_number = "NNNNNNNNNNN";

// The number of the phone receiving call.
$to_number = "NNNNNNNNNNN";

// Use the Twilio-provided site for the TwiML response.
$url = "https://twimlets.com/message";

// The phone message text.
$message = "Hello world.";

// Create the call client.
$client = new Services_Twilio($sid, $token, $version);

//Make the call.
try
{
    $call = $client->account->calls->create(
        $from_number, 
        $to_number,
        $url.'?Message='.urlencode($message)
    );
}
catch (Exception $e) 
{
    echo 'Error: ' . $e->getMessage();
}
```

Wie bereits erwähnt, verwendet dieser Code eine von Twilio bereitgestellte Website für die Rückgabe der TwiML-Antwort. Stattdessen können Sie die TwiML-Antwort auch von Ihrer eigenen Website bereitstellen lassen. Weitere Informationen dazu finden Sie unter [Bereitstellen von TwiML-Antworten von der eigenen Website](#howto_provide_twiml_responses).

* **Hinweis**: Informationen zur Problembehandlung bei Validierungsfehlern für das TLS/SSL-Zertifikat finden Sie unter [https://www.twilio.com/docs/api/errors][ssl_validation]. 

## <a name="how-to-send-an-sms-message"></a><a id="howto_send_sms"></a>Vorgehensweise: Senden einer SMS-Nachricht
Das folgende Beispiel zeigt, wie Sie eine SMS-Nachricht mit der **Services_Twilio**-Klasse senden. Die **From** -Nummer wird von Twilio für Testkonten zum Senden von SMS-Nachrichten bereitgestellt. Die **To** -Nummer muss für Ihr Twilio-Konto überprüft werden, bevor Sie den Code ausführen.

```php
// Include the Twilio PHP library.
require_once 'Services/Twilio.php';

// Library version.
$version = "2010-04-01";

// Set your account ID and authentication token.
$sid = "your_twilio_account_sid";
$token = "your_twilio_authentication_token";


$from_number = "NNNNNNNNNNN"; // With trial account, texts can only be sent from your Twilio number.
$to_number = "NNNNNNNNNNN";
$message = "Hello world.";

// Create the call client.
$client = new Services_Twilio($sid, $token, $version);

// Send the SMS message.
try
{
    $client->$client->account->messages->sendMessage($from_number, $to_number, $message);
}
catch (Exception $e) 
{
    echo 'Error: ' . $e->getMessage();
}
```

## <a name="how-to-provide-twiml-responses-from-your-own-website"></a><a id="howto_provide_twiml_responses"></a>Vorgehensweise: Bereitstellen von TwiML-Antworten von der eigenen Website
Wenn Ihre Anwendung einen Anruf an die Twilio-API initiiert, sendet Twilio Ihre Anforderung an eine URL, von der die Rückgabe einer TwiML-Antwort erwartet wird. Das obige Beispiel verwendet die von Twilio bereitgestellte URL [https://twimlets.com/message][twimlet_message_url]. (TwiML wurde für die Verwendung durch Twilio konzipiert, kann jedoch im Browser angezeigt werden. Klicken Sie beispielsweise auf [https://twimlets.com/message][twimlet_message_url], um ein leeres `<Response>` -Element anzuzeigen. Ein weiteres Beispiel: Klicken Sie auf [https://twimlets.com/message?Message%5B0%5D=Hello%20World][twimlet_message_url_hello_world], um ein `<Response>` -Element mit einem `<Say>` -Element anzuzeigen.)

Anstelle der von Twilio bereitgestellten URL können Sie auch Ihre eigene Website für die Rückgabe von HTTP-Antworten erstellen. Sie können die Website in allen Sprachen erstellen, die XML-Antworten zurückgeben. Dieses Beispiel geht davon aus, dass Sie die TwiML-Antwort mit PHP erstellen.

Die folgende PHP-Seite erstellt eine TwiML-Antwort, die beim Anruf **Hello World** sagt.

```xml
<?php    
    header("content-type: text/xml");    
    echo "<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n";
?>
<Response>    
    <Say>Hello world.</Say>
</Response>
```

Wie das vorstehende Beispiel zeigt, handelt es sich bei der TwiML-Antwort einfach um ein XML-Dokument. Die Twilio-Bibliothek für PHP enthält Klassen, die TwiML für Sie generieren. Das folgende Beispiel generiert die gleiche Antwort wie oben, jedoch unter Verwendung der **Services\_Twilio\_Twiml**-Klasse in der Twilio-Bibliothek für PHP:

```php
require_once('Services/Twilio.php');

$response = new Services_Twilio_Twiml();
$response->say("Hello world.");
print $response;
```

Weitere Informationen zu TwiML finden Sie unter [https://www.twilio.com/docs/api/twiml][twiml_reference]. 

Sobald Sie Ihre PHP-Seite mit den TwiML-Antworten eingerichtet haben, können Sie die URL der PHP-Seite an die `Services_Twilio->account->calls->create`-Methode übergeben. Beispiel: Ihre Webanwendung **MyTwiML** liegt in einem unter Azure gehosteten Dienst, und der Name der PHP-Seite lautet **mytwiml.php**. In diesem Fall kann die URL wie im folgenden Codebeispiel gezeigt an **Services_Twilio->account->calls->create** übergeben werden:

```php
require_once 'Services/Twilio.php';

$sid = "your_twilio_account_sid";
$token = "your_twilio_authentication_token";
$from_number = "NNNNNNNNNNN";
$to_number = "NNNNNNNNNNN";
$url = "http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.php";

// The phone message text.
$message = "Hello world.";

$client = new Services_Twilio($sid, $token, "2010-04-01");

try
{
    $call = $client->account->calls->create(
        $from_number, 
        $to_number,
        $url.'?Message='.urlencode($message)
    );
}
catch (Exception $e) 
{
    echo 'Error: ' . $e->getMessage();
}
```

Weitere Informationen zur Verwendung von Twilio in Azure mit PHP finden Sie unter [Tätigen eines Telefonanrufs mithilfe von Twilio in einer PHP-Anwendung auf Azure][howto_phonecall_php].

## <a name="how-to-use-additional-twilio-services"></a><a id="AdditionalServices"></a>Vorgehensweise: Verwenden zusätzlicher Twilio-Dienste
Zusätzlich zu den hier gezeigten Beispielen bietet Twilio webbasierte APIs, mit denen Sie zusätzliche Twilio-Funktionen in Ihrer Azure-Anwendung verwenden können. Ausführliche Informationen finden Sie in der [Twilio API-Dokumentation][twilio_api_documentation].

## <a name="next-steps"></a><a id="NextSteps"></a>Nächste Schritte
Nachdem Sie nun mit den Grundlagen des Twilio-Dienstes vertraut sind, finden Sie unter diesen Links weitere Informationen:

* [Twilio-Sicherheitsrichtlinien][twilio_security_guidelines]
* [Twilio-Anleitungen und Beispielcode][twilio_howtos]
* [Twilio-Schnellstart-Tutorials][twilio_quickstarts] 
* [Twilio auf GitHub][twilio_on_github]
* [Kontakt zum Twilio-Support][twilio_support]

[twilio_php]: https://github.com/twilio/twilio-php
[twilio_lib_docs]: https://www.twilio.com/docs/libraries/php
[twilio_github_readme]: https://github.com/twilio/twilio-php/blob/master/README.md
[ssl_validation]: https://www.twilio.com/docs/api/errors
[twilio_api_service]: https://api.twilio.com
[howto_phonecall_php]: partner-twilio-php-make-phone-call.md
[twilio_voice_request]: https://www.twilio.com/docs/api/twiml/twilio_request
[twilio_sms_request]: https://www.twilio.com/docs/api/twiml/sms/twilio_request
[misc_role_config_settings]: /previous-versions/azure/hh690945(v=azure.100)
[twimlet_message_url]: https://twimlets.com/message
[twimlet_message_url_hello_world]: https://twimlets.com/message?Message%5B0%5D=Hello%20World
[twiml_reference]: https://www.twilio.com/docs/api/twiml
[twilio_pricing]: https://www.twilio.com/pricing
[special_offer]: https://ahoy.twilio.com/azure
[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api]: https://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]:  https://www.twilio.com/user/account
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_api_documentation]: https://www.twilio.com/api
[twilio_security_guidelines]: https://www.twilio.com/docs/security
[twilio_howtos]: https://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: https://www.twilio.com/help/contact
[twilio_quickstarts]: https://www.twilio.com/docs/quickstart