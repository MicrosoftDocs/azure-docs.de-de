---
title: Tätigen eines Telefonanrufs über Twilio (PHP) | Microsoft Docs
description: Erfahren Sie, wie Sie mit dem Twilio API-Dienst in Azure einen Telefonanruf tätigen und eine SMS-Nachricht senden. Die Beispiele sind für eine PHP-Anwendung vorgesehen.
documentationcenter: php
services: ''
author: georgewallace
ms.assetid: 44e35adc-be06-4700-beee-8c9e2c20c540
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.openlocfilehash: ef0642b26898249d78f5103e8a8f80198887bed9
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95542523"
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-php-application-on-azure"></a>Tätigen eines Telefonanrufs mithilfe von Twilio in einer PHP-Anwendung auf Azure
Das folgende Beispiel zeigt, wie Sie von einer in Azure gehosteten PHP-Webseite einen Anruf über Twilio tätigen können. Die Anwendung fragt den Benutzer nach Werten für den Telefonanruf, wie im folgenden Screenshot gezeigt.

![Azure-Anrufformular mit Twilio und PHP][twilio_php]

Sie benötigen Folgendes, um den Code in diesem Artikel ausführen zu können:

1. Ein Twilio-Konto und ein Authentifizierungs-Token von Ihrer [Twilio-Konsole][twilio_console] abrufen. Sie können Sie die Preise von Twilio unter [https://www.twilio.com/pricing][twilio_pricing] einsehen. Sie können sich unter [https://www.twilio.com/try-twilio][try_twilio] für ein Testkonto anmelden.
2. Rufen Sie die [Twilio-Bibliothek für PHP](https://github.com/twilio/twilio-php) ab, oder installieren Sie sie als PEAR-Paket. Weitere Informationen finden Sie in der [Infodatei](https://github.com/twilio/twilio-php/blob/master/README.md).
3. Installieren Sie das Azure SDK für PHP. 
<!-- For an overview of the SDK and instructions on installing it, see [Set up the Azure SDK for PHP](./app-service/quickstart-php.md) -->

## <a name="create-a-web-form-for-making-a-call"></a>Erstellen eines Webformulars für den Anruf
Der folgende HTML-Code erstellt eine Webseite (**callform.html**) zur Eingabe der Benutzerdaten für den Anruf:

```html
<!DOCTYPE html>
<html>
<head>
  <title>Automated call form</title>
</head>
<body>
  <h1>Automated Call Form</h1>
  <p>Fill in all fields and click <b>Make this call</b>.</p>
  <form action="makecall.php" method="post">
    <table>
      <tr>
        <td>To:</td>
        <td><input name="callTo" size="50" type="text" value=""></td>
      </tr>
      <tr>
        <td>From:</td>
        <td><input name="callFrom" size="50" type="text" value=""></td>
      </tr>
      <tr>
        <td>Call message:</td>
        <td><input name="callText" size="100" type="text" value="Hello. This is the call text. Good bye."></td>
      </tr>
      <tr>
        <td colspan="2"><input type="submit" value="Make this call"></td>
      </tr>
    </table>
  </form><br>
</body>
</html>
```

## <a name="create-the-code-to-make-the-call"></a>Erstellen des Codes für den Anruf
Der folgende Code erstellt **makecall.php**, die aufgerufen wird, wenn der Benutzer das Formular aus **callform.html** übermittelt. Der folgende Code erstellt die Anrufnachricht und führt den Anruf aus. Ersetzen Sie die Platzhalterwerte **$sid** und **$token** im folgenden Code durch Ihr [Twilio-Konto][twilio_console] und Ihr Authentifizierungstoken in der Twilio-Konsole.

```html
<html>
<head><title>Making call...</title></head>
<body>
<p>Your call is being made.</p>

<?php
require_once 'path/to/vendor/autoload.php';

$sid   = "your_account_sid";
$token = "your_authentication_token";

$from_number = $_POST['callFrom']; // Calls must be made from a registered Twilio number.
$to_number   = $_POST['callTo'];
$message     = $_POST['callText'];

$client = new Twilio\Rest\Client($sid, $token);

$call = $client->calls->create(
            $to_number,
            $from_number,
            array('url' => https://twimlets.com/message?Message=' . urlencode($message))
        );

echo "Call status: " . $call->status . "<br />";
echo "URI resource: " . $call->uri . "<br />";
?>
</body>
</html>
```

**makecall.php** führt nicht nur den Aufruf aus, sondern zeigt außerdem auch einige Aufruf-Metadaten an, wie das folgende Bild zeigt. Weitere Informationen zu Anrufmetadaten finden Sie unter [https://www.twilio.com/docs/api/rest/call#instance-properties][twilio_call_properties].

![Azure-Anrufantwort mit Twilio und PHP][twilio_php_response]

## <a name="run-the-application"></a>Ausführen der Anwendung
Der nächste Schritt besteht darin, [Ihre Anwendung mit Git in Azure-Web-Apps](app-service/quickstart-php.md) bereitzustellen (jedoch sind nicht alle Informationen relevant). 

## <a name="next-steps"></a>Nächste Schritte
Dieser Code demonstriert die allgemeinen Funktionen für die Verwendung von Twilio mit PHP in Azure. Bevor Sie dieses Beispiel in einer Produktionsumgebung bereitstellen, sollten Sie einige Funktionen zur Fehlerbehandlung oder andere Features hinzufügen. Beispiel:

* Anstelle eines Web-Formulars könnten Sie Azure-Speicher-Blobs oder eine SQL-Datenbank zum Speichern von Telefonnummern und Anruftexten verwenden. Weitere Informationen zur Verwendung von Azure Storage-Blobs in PHP finden Sie unter [Übertragen von Objekten nach/aus Azure Blob Storage mit PHP][howto_blob_storage_php]. Informationen zur Verwendung von SQL-Datenbanken in PHP finden Sie unter [Abfragen einer Azure SQL-Datenbank mithilfe von PHP][howto_sql_azure_php].
* Der Code in **makecall.php** verwendet eine von Twilio bereitgestellte URL ([https://twimlets.com/message][twimlet_message_url]), die eine Antwort in der Twilio Markup Language (TwiML) zurückgibt, die Twilio mitteilt, wie mit dem Aufruf verfahren werden soll. Das zurückgegebene TwiML kann z. B. ein `<Say>`-Verb enthalten, das dazu führt, dass dem Anrufempfänger ein bestimmter Text vorgesprochen wird. Anstelle der von Twilio bereitgestellten URL können Sie auch mit einem eigenen Dienst auf die Twilio-Anfrage antworten. Weitere Informationen finden Sie unter [Verwenden von Twilio für Telefonie- und SMS-Funktionen in PHP][howto_twilio_voice_sms_php]. Weitere Informationen zu TwiML finden Sie unter [https://www.twilio.com/docs/api/twiml][twiml]. Weitere Informationen zu `<Say>` und anderen Twilio-Verben finden Sie unter [https://www.twilio.com/docs/api/twiml/say][twilio_say].
* Lesen Sie die Twilio-Sicherheitsrichtlinien unter [https://www.twilio.com/docs/security][twilio_docs_security].

Zusätzliche Informationen zu Twilio finden Sie unter [https://www.twilio.com/docs][twilio_docs].

## <a name="see-also"></a>Weitere Informationen
* [Verwenden von Twilio für Telefonie- und SMS-Funktionen in PHP](partner-twilio-php-how-to-use-voice-sms.md)

[twilio_console]: https://www.twilio.com/console
[twilio_pricing]: https://www.twilio.com/pricing
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_api]: https://www.twilio.com/docs/api
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified
[twimlet_message_url]: https://twimlets.com/message
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api_service]: https://api.twilio.com
[build_php_azure_app]: http://azurephp.interoperabilitybridges.com/articles/build-and-deploy-a-windows-azure-php-application
[howto_twilio_voice_sms_php]: partner-twilio-php-how-to-use-voice-sms.md
[howto_blob_storage_php]: ./storage/blobs/storage-quickstart-blobs-php.md
[howto_sql_azure_php]: ./azure-sql/database/connect-query-content-reference-guide.md
[twilio_call_properties]: https://www.twilio.com/docs/api/rest/call#instance-properties
[twilio_docs_security]: https://www.twilio.com/docs/security
[twilio_docs]: https://www.twilio.com/docs
[twilio_say]: https://www.twilio.com/docs/api/twiml/say
[ssl_validation]: http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html
[twilio_php]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPCallForm.jpg
[twilio_php_response]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPMakeCall.jpg
[twilio_php_github]: https://github.com/twilio/twilio-php