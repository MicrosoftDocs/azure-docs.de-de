---
title: Verwenden von Twilio für Sprachanrufe und SMS (Java) | Microsoft Docs
description: Erfahren Sie, wie Sie mit dem Twilio API-Dienst in Azure einen Telefonanruf tätigen und eine SMS-Nachricht senden. Die Codebeispiele wurden in Java geschrieben.
services: ''
documentationcenter: java
author: georgewallace
ms.assetid: f3508965-5527-4255-9d51-5d5f926f4d43
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.custom: devx-track-java
ms.openlocfilehash: e7ff925534a9cab6523097f84a8ae199e9cb83eb
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100548725"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-java"></a>Verwenden von Twilio für Telefonie- und SMS-Funktionen in Java
Dieser Leitfaden veranschaulicht die Ausführung allgemeiner Programmierungsaufgaben mit dem Twilio-API-Dienst in Azure. Die Szenarien behandeln das Tätigen eines Telefonanrufs und das Senden einer Kurznachricht (SMS). Weitere Informationen zu Twilio und zur Verwendung von Telefonie und SMS in Ihren Anwendungen finden Sie im Abschnitt [Nächste Schritte](#NextSteps) .

## <a name="what-is-twilio"></a><a id="WhatIs"></a>Was ist Twilio?
Twilio ist eine Webservice-API für Telefonie, mit deren Hilfe Sie Ihre existierenden Websprachen und -Fertigkeiten für die Erstellung von Telefonie- und SMS-Anwendungen nutzen können. Twilio ist ein externer Dienst (kein Feature von Azure und kein Microsoft-Produkt).

**Twilio Voice** ermöglicht Anwendungen das Tätigen und Entgegennehmen von Telefonanrufen. **Twilio SMS** ermöglicht Ihren Anwendungen das Senden und Empfangen von SMS-Nachrichten. **Twilio Client** ermöglicht Sprachkommunikation in Ihren Anwendungen über existierende Internetverbindungen, inklusive mobiler Verbindungen.

## <a name="twilio-pricing-and-special-offers"></a><a id="Pricing"></a>Twilio-Preise und -Sonderangebote
Informationen zu Twilio-Preisen finden Sie unter [Twilio-Preise][twilio_pricing]. Azure-Kunden erhalten als [Sonderangebot][special_offer] kostenlos ein Guthaben von 1.000 SMS oder 1.000 eingehenden Sprachminuten. Besuchen Sie [https://ahoy.twilio.com/azure][special_offer], um sich für dieses Angebot zu registrieren und weitere Informationen zu erhalten.

## <a name="concepts"></a><a id="Concepts"></a>Konzepte
Die Twilio-API ist eine RESTful-API, die Telefonie- und SMS-Funktionen für Anwendungen bereitstellt. Clientbibliotheken stehen in mehreren Sprachen zur Verfügung. Eine Liste finden Sie unter [Twilio-API-Bibliotheken][twilio_libraries].

Schlüsselaspekte der Twilio API sind Twilio-Verben und die Twilio Markup Language (TwiML).

### <a name="twilio-verbs"></a><a id="Verbs"></a>Twilio-Verben
Die API verwendet Twilio-Verben; so weist beispielsweise das Verb **&lt;Say&gt;** Twilio an, eine Nachricht in einem Anruf akustisch zu übermitteln.

Nachfolgend finden Sie eine Liste mit Twilio-Verben.

* **&lt;Dial&gt;** : Verbindet den Anrufer mit einem anderen Telefon.
* **&lt;Gather&gt;** : Erfasst Ziffern, die über die Telefontasten eingegeben werden.
* **&lt;Hangup&gt;** : Beendet einen Anruf.
* **&lt;Play&gt;** : Gibt eine Audiodatei wieder.
* **&lt;Queue&gt;** : Hinzufügen an eine Warteschlange von Aufrufern.
* **&lt;Pause&gt;** : Wartet ohne Ton für eine angegebene Anzahl von Sekunden.
* **&lt;Record&gt;** : Zeichnet die Stimme des Anrufers auf und gibt eine URL einer Datei mit der Aufzeichnung zurück.
* **&lt;Redirect&gt;** : Überträgt die Steuerung eines Anrufs oder einer SMS an die TwiML an einer anderen URL.
* **&lt;Reject&gt;** : Lehnt einen bei Ihrer Twilio-Nummer eingehenden Anruf ab, ohne dass Ihnen Kosten entstehen.
* **&lt;Say&gt;** : Konvertiert Text in Sprache für einen Anruf.
* **&lt;Sms&gt;** : Sendet eine SMS-Nachricht.

### <a name="twiml"></a><a id="TwiML"></a>TwiML
TwiML ist ein Satz XML-basierter Anweisungen auf Grundlage der Twilio-Verben, die Twilio anweisen, wie ein Anruf oder eine SMS verarbeitet werden soll.

Die TwiML im folgenden Beispiel konvertiert den Text **Hello World!** in die Sprache.

```xml
    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World!</Say>
    </Response>
```

Wenn eine Anwendung die Twilio-API aufruft, ist einer der API-Parameter die URL, die die TwiML-Antwort zurückgibt. Zu Entwicklungszwecken können Sie die von Twilio zur Verfügung gestellten URLs verwenden, um die von Ihren Anwendungen verwendeten TwiML-Antworten bereitzustellen. Sie können auch Ihre eigenen URLs hosten, um TwiML-Antworten zu generieren; eine weitere Option ist die Verwendung des **TwiMLResponse** -Objekts.

Weitere Informationen zu Twilio-Verben, ihren Attributen sowie TwiML finden Sie unter [TwiML][twiml]. Weitere Informationen zur Twilio-API finden Sie unter [Twilio-API][twilio_api].

## <a name="create-a-twilio-account"></a><a id="CreateAccount"></a>Erstellen von Twilio-Konten
Wenn Sie ein Twilio-Konto erstellen möchten, melden Sie sich bei [Try Twilio][try_twilio] an. Sie können mit einem kostenlosen Konto beginnen und später ein Upgrade vornehmen.

Wenn Sie sich für ein Twilio-Konto anmelden, erhalten Sie eine Konto-ID und ein Authentifizierungstoken. Beide Angaben benötigen Sie zum Tätigen von Twilio-API-Anrufen. Bewahren Sie Ihr Authentifizierungstoken sicher auf, um unbefugten Zugriff auf Ihr Konto zu verhindern. Ihre Konto-ID und das Authentifizierungstoken können Sie in der [Twilio-Konsole][twilio_console] in den Feldern **ACCOUNT SID** bzw. **AUTH TOKEN** einsehen.

## <a name="create-a-java-application"></a><a id="create_app"></a>Erstellen einer Java-Anwendung
1. Laden Sie das Twilio-JAR herunter und fügen Sie es zu Ihrem Java-Buildpfad und zu Ihrer WAR-Bereitstellungs-Assembly hinzu. Unter [https://github.com/twilio/twilio-java][twilio_java] können Sie die GitHub-Quellen herunterladen und Ihr eigenes JAR erstellen oder ein bereits erstelltes JAR herunterladen (mit oder ohne Abhängigkeiten).
2. Stellen Sie sicher, dass der **cacerts** -Zertifikatspeicher Ihres SDK das Zertifikat der Equifax-Zertifizierungsstelle mit dem MD5-Fingerabdruck 67:CB:9D:C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 enthält (die Seriennummer ist 35:DE:F4:CF und der SHA1-Fingerabdruck ist D2:32:09:AD:23:D3:14:23:21:74:E4:0D:7F:9D:62:13:97:86:63:3A). Dies ist das Zertifizierungsstellenzertifikat für den [https://api.twilio.com][twilio_api_service]-Dienst, der beim Verwenden der Twilio-APIs aufgerufen wird.

Ausführliche Informationen zur Verwendung der Twilio-Clientbibliothek für Java finden Sie unter [Tätigen eines Telefonanrufs mithilfe von Twilio in einer Java-Anwendung auf Azure][howto_phonecall_java].

## <a name="configure-your-application-to-use-twilio-libraries"></a><a id="configure_app"></a>Konfigurieren einer Anwendung für die Verwendung von Twilio-Bibliotheken
Sie können **import** -Anweisungen am Anfang Ihrer Quelldateien für die Twilio-Pakete oder Klassen hinzufügen, die Sie in Ihrer Anwendung verwenden möchten.

Für Java-Quelldateien:

```java
    import com.twilio.*;
    import com.twilio.rest.api.*;
    import com.twilio.type.*;
    import com.twilio.twiml.*;
```

Für Java Server Page (JSP)-Quelldateien:

```java
    import="com.twilio.*"
    import="com.twilio.rest.api.*"
    import="com.twilio.type.*"
    import="com.twilio.twiml.*"
```

Je nach verwendeten Twilio-Paketen oder Klassen sehen Ihre **import** -Anweisungen möglicherweise unterschiedlich aus.

## <a name="how-to-make-an-outgoing-call"></a><a id="howto_make_call"></a>Tätigen von ausgehenden Anrufen
Nachfolgend wird erläutert, wie Sie einen ausgehenden Anruf mit der **Anruf**-Klasse tätigen. Dieser Code verwendet eine von Twilio bereitgestellte Website für die Rückgabe der TwiML-Antwort (Twilio Markup Language). Geben Sie Ihre Werte für die Telefonnummern **from** und **to** ein, und stellen Sie sicher, dass Sie die **from**-Telefonnummer für Ihr Twilio-Konto verifizieren, bevor Sie den Code ausführen.

```java
    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    String accountSID = "your_twilio_account_SID";
    String authToken = "your_twilio_authentication_token";

    // Initialize the Twilio client.
    Twilio.init(accountSID, authToken);

    // Use the Twilio-provided site for the TwiML response.
    URI uri = new URI("https://twimlets.com/message" +
            "?Message%5B0%5D=Hello%20World%21");

    // Declare To and From numbers
    PhoneNumber to = new PhoneNumber("NNNNNNNNNN");
    PhoneNumber from = new PhoneNumber("NNNNNNNNNN");

    // Create a Call creator passing From, To and URL values
    // then make the call by executing the create() method
    Call.creator(to, from, uri).create();
```

Weitere Informationen zu den an die **Call.Create**-Methode übergebenen Parametern finden Sie unter [https://www.twilio.com/docs/api/rest/making-calls][twilio_rest_making_calls].

Wie bereits erwähnt, verwendet dieser Code eine von Twilio bereitgestellte Website für die Rückgabe der TwiML-Antwort. Stattdessen können Sie die TwiML-Antwort auch von Ihrer eigenen Website bereitstellen lassen. Weitere Informationen finden Sie unter [How to Provide TwiML Responses in a Java Application on Azure](#howto_provide_twiml_responses) (in englischer Sprache).

## <a name="how-to-send-an-sms-message"></a><a id="howto_send_sms"></a>Senden von SMS-Nachrichten
Das folgende Beispiel zeigt, wie Sie eine SMS-Nachricht mit der **Nachricht**-Klasse senden. Die **from**-Nummer **4155992671** wird von Twilio für Testkonten zum Senden von SMS-Nachrichten bereitgestellt. Die **to**-Nummer muss für Ihr Twilio-Konto überprüft werden, bevor Sie den Code ausführen.

```java
    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    String accountSID = "your_twilio_account_SID";
    String authToken = "your_twilio_authentication_token";

    // Initialize the Twilio client.
    Twilio.init(accountSID, authToken);

    // Declare To and From numbers and the Body of the SMS message
    PhoneNumber to = new PhoneNumber("+14159352345"); // Replace with a valid phone number for your account.
    PhoneNumber from = new PhoneNumber("+14158141829"); // Replace with a valid phone number for your account.
    String body = "Where's Wallace?";

    // Create a Message creator passing From, To and Body values
    // then send the SMS message by calling the create() method
    Message sms = Message.creator(to, from, body).create();
```

Weitere Informationen zu den an die **Message.Create**-Methode übergebenen Parametern finden Sie unter [https://www.twilio.com/docs/api/rest/sending-sms][twilio_rest_sending_sms].

## <a name="how-to-provide-twiml-responses-from-your-own-website"></a><a id="howto_provide_twiml_responses"></a>Bereitstellen von TwiML-Antworten von der eigenen Website
Wenn Ihre Anwendung einen Anruf an die Twilio-API initiiert – z.B. über die **CallCreator.create**-Methode – sendet Twilio Ihre Anforderung an eine URL, von der die Rückgabe einer TwiML-Antwort erwartet wird. Das obige Beispiel verwendet die von Twilio bereitgestellte URL [https://twimlets.com/message][twimlet_message_url]. (TwiML wurde für die Verwendung durch Webdienste konzipiert, kann jedoch im Browser angezeigt werden. Klicken Sie z.B. auf [https://twimlets.com/message][twimlet_message_url], um ein leeres **&lt; Response&gt;** -Element anzuzeigen. Ein weiteres Beispiel: Klicken Sie auf [https://twimlets.com/message?Message%5B0%5D=Hello%20World%21][twimlet_message_url_hello_world], um ein **&lt; Response&gt;** -Element mit einem **&lt; Say&gt;** -Element anzuzeigen.)

Anstelle der von Twilio bereitgestellten URL können Sie auch Ihre eigene URL-Website für die Rückgabe von HTTP-Antworten erstellen. Sie können die Website in allen Sprachen erstellen, die HTTP-Antworten zurückgeben. Dieses Beispiel geht davon aus, dass Sie die URL in einer JSP-Seite hosten.

Die folgende JSP-Seite erstellt eine TwiML-Antwort, die **Hello World!** beim Anruf sagt.

```xml
    <%@ page contentType="text/xml" %>
    <Response>
        <Say>Hello World!</Say>
    </Response>
```

Die folgende JSP-Seite erstellt eine TwiML-Antwort, die zunächst etwas Text spricht, mehrere Pausen enthält, und anschließend Informationen über die Version der Twilio-API und den Azure-Rollennamen spricht.

```xml
    <%@ page contentType="text/xml" %>
    <Response>
        <Say>Hello from Azure!</Say>
        <Pause></Pause>
        <Say>The Twilio API version is <%= request.getParameter("ApiVersion") %>.</Say>
        <Say>The Azure role name is <%= System.getenv("RoleName") %>.</Say>
        <Pause></Pause>
        <Say>Good bye.</Say>
    </Response>
```

Der Parameter **ApiVersion** ist nur in Twilio-Sprachanforderungen verfügbar (nicht in SMS-Anforderungen). Wie Sie die verfügbaren Anforderungsparameter für Twilio-Telefonie- und SMS-Anforderungen anzeigen, erfahren Sie jeweils unter <https://www.twilio.com/docs/api/twiml/twilio_request> und <https://www.twilio.com/docs/api/twiml/sms/twilio_request>. Die Umgebungsvariable **RoleName** ist als Teil einer Azure-Bereitstellung verfügbar. (Informationen zum Hinzufügen eigener Umgebungsvariablen, die mit **System.getenv** abgerufen werden können, finden Sie im Abschnitt zu Umgebungsvariablen unter [Azure-Rolleneigenschaften][misc_role_config_settings].)

Sobald Sie Ihre JSP-Seite mit den TwiML-Antworten eingerichtet haben, können Sie die URL der JSP-Seite an die **Call.creator**-Methode übergeben. Beispiel: Ihre Webanwendung „MyTwiML“ liegt in einem unter Azure gehosteten Dienst, und der Name der JSP-Seite lautet „mytwiml.jsp“. In diesem Fall kann die URL wie im folgenden Codebeispiel gezeigt an **Call.creator** übergeben werden:

```java
    // Declare To and From numbers and the URL of your JSP page
    PhoneNumber to = new PhoneNumber("NNNNNNNNNN");
    PhoneNumber from = new PhoneNumber("NNNNNNNNNN");
    URI uri = new URI("http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.jsp");

    // Create a Call creator passing From, To and URL values
    // then make the call by executing the create() method
    Call.creator(to, from, uri).create();
```

Eine weitere Option zum Antworten mit TwiML ist die **VoiceResponse**-Klasse, die im Paket **com.twilio.twiml** verfügbar ist.

Weitere Informationen über die Verwendung von Twilio in Azure mit Java finden Sie unter [Tätigen eines Telefonanrufs mithilfe von Twilio in einer Java-Anwendung auf Azure][howto_phonecall_java].

## <a name="how-to-use-additional-twilio-services"></a><a id="AdditionalServices"></a>Verwenden zusätzlicher Twilio-Dienste
Zusätzlich zu den hier gezeigten Beispielen bietet Twilio webbasierte APIs, mit denen Sie zusätzliche Twilio-Funktionen in Ihrer Azure-Anwendung verwenden können. Ausführliche Informationen finden Sie in der [Twilio API-Dokumentation][twilio_api_documentation].

## <a name="next-steps"></a><a id="NextSteps"></a>Nächste Schritte
Nachdem Sie nun mit den Grundlagen des Twilio-Dienstes vertraut sind, finden Sie unter diesen Links weitere Informationen:

* [Twilio Security Guidelines (Twilio-Sicherheitsrichtlinien)][twilio_security_guidelines]
* [Twilio-Anleitungen und Beispielcode][twilio_howtos]
* [Twilio-Schnellstart-Tutorials][twilio_quickstarts]
* [Twilio auf GitHub][twilio_on_github]
* [Kontakt zum Twilio-Support][twilio_support]

[twilio_java]: https://github.com/twilio/twilio-java
[twilio_api_service]: https://api.twilio.com
[howto_phonecall_java]: partner-twilio-java-phone-call-example.md
[misc_role_config_settings]: /previous-versions/azure/hh690945(v=azure.100)
[twimlet_message_url]: https://twimlets.com/message
[twimlet_message_url_hello_world]: https://twimlets.com/message?Message%5B0%5D=Hello%20World%21
[twilio_rest_making_calls]: https://www.twilio.com/docs/api/rest/making-calls
[twilio_rest_sending_sms]: https://www.twilio.com/docs/api/rest/sending-sms
[twilio_pricing]: https://www.twilio.com/pricing
[special_offer]: https://ahoy.twilio.com/azure
[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api]: https://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_console]:  https://www.twilio.com/console
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified#
[twilio_api_documentation]: https://www.twilio.com/docs
[twilio_security_guidelines]: https://www.twilio.com/docs/security
[twilio_howtos]: https://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: https://www.twilio.com/help/contact
[twilio_quickstarts]: https://www.twilio.com/docs/quickstart
