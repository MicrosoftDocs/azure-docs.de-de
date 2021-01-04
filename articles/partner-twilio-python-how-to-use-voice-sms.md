---
title: Verwenden von Twilio für Sprachanrufe und SMS (Python) | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mit dem Twilio API-Dienst in Azure einen Telefonanruf tätigen und eine SMS-Nachricht senden. Die Codebeispiele wurden in Python geschrieben.
services: ''
documentationcenter: python
author: georgewallace
ms.assetid: 561bc75b-4ac4-40ba-bcba-48e901f27cc3
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 02/19/2015
ms.author: gwallace
ms.custom: devx-track-python
ms.openlocfilehash: ba93591ade730c4e9c9bdb6a42232e71e10d6469
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000435"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-python"></a>Verwenden von Twilio für Telefonie- und SMS-Funktionen in Python
Dieser Leitfaden veranschaulicht die Ausführung allgemeiner Programmierungsaufgaben mit dem Twilio-API-Dienst in Azure. Die Szenarien behandeln das Tätigen eines Telefonanrufs und das Senden einer Kurznachricht (SMS). Weitere Informationen zu Twilio und zur Verwendung von Telefonie und SMS in Ihren Anwendungen finden Sie im Abschnitt [Nächste Schritte](#NextSteps) .

## <a name="what-is-twilio"></a><a id="WhatIs"></a>Was ist Twilio?
Twilio ist ein leistungsstarkes und zukunftsorientiertes Tool für die Unternehmenskommunikation, mit dem Entwickler Telefonie-, VoIP- und Messagingfunktionalitäten in Anwendungen einbetten können. Die gesamte in einer cloudbasierten, globalen Umgebung benötigte Infrastruktur wird virtualisiert und über die Twilio-API-Kommunikationsplattform zur Verfügung gestellt. Anwendungen können einfach erstellt und skaliert werden. Sie profitieren von flexiblen Pay-as-you-go-Preisen und der Zuverlässigkeit der Cloud.

**Twilio Voice** ermöglicht Anwendungen das Tätigen und Entgegennehmen von Telefonanrufen.
**Twilio SMS** ermöglicht Ihren Anwendungen das Senden und Empfangen von SMS-Nachrichten.
**Twilio Client** ermöglicht Ihnen das Tätigen von VoIP-Anrufen von jedem Telefon, Tablet-PC oder Browser und unterstützt WebRTC.

## <a name="twilio-pricing-and-special-offers"></a><a id="Pricing"></a>Twilio-Preise und -Sonderangebote
Azure-Kunden erhalten ein [Sonderangebot][special_offer]: eine Twilio-Gutschrift in Höhe von 10 US-Dollar bei einem Upgrade Ihres Twilio-Kontos. Diese Twilio-Gutschrift kann für jede beliebige Twilio-Nutzung ausgegeben werden (eine Gutschrift von 10 US-Dollar reicht für den Versand von 1.000 SMS-Nachrichten oder für bis zu 1.000 eingehende Telefonminuten, abhängig vom Standort der Telefonnummer und dem Nachrichten- oder Anrufziel). Lösen Sie diese [Twilio-Gutschrift][special_offer] ein, und legen Sie los.

Twilio ist ein Pay-as-you-go-Dienst, also vertragsungebunden. Es fallen keine Einrichtungsgebühren an, und Sie können Ihr Konto jederzeit schließen. Weitere Informationen finden Sie unter [Twilio-Preise][twilio_pricing].

## <a name="concepts"></a><a id="Concepts"></a>Konzepte
Die Twilio-API ist eine RESTful-API, die Telefonie- und SMS-Funktionen für Anwendungen bereitstellt. Clientbibliotheken stehen in mehreren Sprachen zur Verfügung. Eine Liste finden Sie unter [Twilio-API-Bibliotheken][twilio_libraries].

Schlüsselaspekte der Twilio API sind Twilio-Verben und die Twilio Markup Language (TwiML).

### <a name="twilio-verbs"></a><a id="Verbs"></a>Twilio-Verben
Die API verwendet Twilio-Verben; so weist beispielsweise das Verb **&lt;Say&gt;** Twilio an, eine Nachricht in einem Anruf akustisch zu übermitteln.

Nachfolgend finden Sie eine Liste mit Twilio-Verben. Andere Verben und Funktionen werden in der [Dokumentation zur Twilio Markup Language][twiml]erläutert.

* **&lt;Dial&gt;** : Verbindet den Anrufer mit einem anderen Telefon.
* **&lt;Gather&gt;** : Erfasst Ziffern, die über die Telefontasten eingegeben werden.
* **&lt;Hangup&gt;** : Beendet einen Anruf.
* **&lt;Pause&gt;** : Wartet ohne Ton für eine angegebene Anzahl von Sekunden.
* **&lt;Play&gt;** : Gibt eine Audiodatei wieder.
* **&lt;Queue&gt;** : Hinzufügen an eine Warteschlange von Aufrufern.
* **&lt;Record&gt;** : Zeichnet die Stimme des Anrufers auf und gibt eine URL einer Datei mit der Aufzeichnung zurück.
* **&lt;Redirect&gt;** : Überträgt die Steuerung eines Anrufs oder einer SMS an die TwiML an einer anderen URL.
* **&lt;Reject&gt;** : Lehnt einen bei Ihrer Twilio-Nummer eingehenden Anruf ab, ohne dass Ihnen Kosten entstehen.
* **&lt;Say&gt;** : Konvertiert Text in Sprache für einen Anruf.
* **&lt;Sms&gt;** : Sendet eine SMS-Nachricht.

### <a name="twiml"></a><a id="TwiML"></a>TwiML
TwiML ist ein Satz XML-basierter Anweisungen auf Grundlage der Twilio-Verben, die Twilio anweisen, wie ein Anruf oder eine SMS verarbeitet werden soll.

Die TwiML im folgenden Beispiel konvertiert den Text **Hello World** in Sprache.

```xml
<?xml version="1.0" encoding="UTF-8" ?>
  <Response>
    <Say>Hello World</Say>
  </Response>
```

Wenn eine Anwendung die Twilio-API aufruft, ist einer der API-Parameter die URL, die die TwiML-Antwort zurückgibt. Zu Entwicklungszwecken können Sie die von Twilio zur Verfügung gestellten URLs verwenden, um die von Ihren Anwendungen verwendeten TwiML-Antworten bereitzustellen. Sie können auch Ihre eigenen URLs hosten, um TwiML-Antworten zu generieren; eine weitere Option ist die Verwendung des `TwiMLResponse`-Objekts.

Weitere Informationen zu Twilio-Verben, ihren Attributen sowie TwiML finden Sie unter [TwiML][twiml]. Weitere Informationen zur Twilio-API finden Sie unter [Twilio-API][twilio_api].

## <a name="create-a-twilio-account"></a><a id="CreateAccount"></a>Erstellen von Twilio-Konten
Wenn Sie ein Twilio-Konto erstellen möchten, melden Sie sich bei [Try Twilio][try_twilio] an. Sie können mit einem kostenlosen Konto beginnen und später ein Upgrade vornehmen.

Wenn Sie sich für ein Twilio-Konto anmelden, erhalten Sie eine Konto-SID und ein Authentifizierungstoken. Beide Angaben benötigen Sie zum Tätigen von Twilio-API-Anrufen. Bewahren Sie Ihr Authentifizierungstoken sicher auf, um unbefugten Zugriff auf Ihr Konto zu verhindern. Ihre Konto-SID und das Authentifizierungstoken können Sie in der [Twilio-Konsole][twilio_console] in den Feldern **ACCOUNT SID** bzw. **AUTH TOKEN** einsehen.

## <a name="create-a-python-application"></a><a id="create_app"></a>Erstellen einer Python-Anwendung
Eine Python-Anwendung, die den Twilio-Dienst verwendet und in Azure ausgeführt wird, unterscheidet sich nicht von anderen Python-Anwendungen, die den Twilio-Dienst verwenden. Twilio-Dienste sind REST-basiert und können in Python auf verschiedene Arten aufgerufen werden. Dieser Artikel konzentriert sich auf die Verwendung von Twilio-Diensten mit der [Twilio-Bibliothek für Python von GitHub][twilio_python]. Weitere Informationen zur Verwendung der Twilio-Bibliothek für Python finden Sie unter [https://www.twilio.com/docs/libraries/python][twilio_lib_docs].

Richten Sie [zuerst eine neue Azure-Linux-VM][azure_vm_setup] ein, die als Host für Ihre neue Python-Webanwendung fungiert. Sobald der virtuelle Computer ausgeführt wird, müssen Sie Ihre Anwendung auf einem öffentlichen Port verfügbar machen, wie unten beschrieben.

### <a name="add-an-incoming-rule"></a>Eine eingehende Regel hinzufügen
  1. Navigieren Sie zur Seite „[Netzwerksicherheitsgruppe][Azure_nsg]“.
  2. Wählen Sie die mit Ihrem virtuellen Computer entsprechende Netzwerksicherheitsgruppe.
  3. Hinzufügen einer **ausgehenden Regel** für **port 80**. Achten Sie darauf, dass Sie Eingehende von einer beliebigen Adresse zulassen.

### <a name="set-the-dns-name-label"></a>DNS-Namensbezeichnung eingeben
  1. Navigieren Sie zur Seite [Die öffentlichen IP-Adressen][azure_ips].
  2. Wählen Sie die öffentliche IP-Adresse aus, die Ihrem virtuellen Computer entspricht.
  3. Legen Sie den **DNS-Namensbezeichnung** im Abschnitt **Konfiguration** fest. Bei diesem Beispiel sieht es etwa wie folgt *ihr Domänenname*.centralus.cloudapp.azure.com

Sobald Sie über SSH eine Verbindung mit dem virtuellen Computer herstellen können, können Sie das Web Framework Ihrer Wahl installieren (die beiden am häufigsten bekannten in Python sind [Flask](http://flask.pocoo.org/) und [Django](https://www.djangoproject.com)). Sie können einem der beiden nur durch Ausführen des `pip install`-Befehls installieren.

Bedenken Sie, dass wir den virtuellen Computer zum Zulassen von Datenverkehr nur an Port 80 konfiguriert haben. Stellen Sie sicher, dass die Anwendung für die Verwendung dieses Ports konfiguriert wird.

## <a name="configure-your-application-to-use-twilio-libraries"></a><a id="configure_app"></a>Konfigurieren einer Anwendung für die Verwendung von Twilio-Bibliotheken
Sie können die Anwendung auf zwei verschiedene Arten für die Twilio-Bibliothek für Python konfigurieren:

* Sie können die Twilio-Bibliothek für Python als Pip-Paket installieren. Führen Sie dazu die folgenden Befehle aus:
   
  `$ pip install twilio`

    ODER

* Laden Sie die Twilio-Bibliothek für Python von GitHub ([https://github.com/twilio/twilio-python][twilio_python]) herunter, und installieren Sie sie wie folgt:

  `$ python setup.py install`

Nachdem Sie die Twilio-Bibliothek für Python installiert haben, können Sie es anschließend in Ihre Python-Dateien `import`:

  `import twilio`

Weitere Informationen finden Sie unter [twilio_github_readme](https://github.com/twilio/twilio-python/blob/master/README.md).

## <a name="how-to-make-an-outgoing-call"></a><a id="howto_make_call"></a>Tätigen von ausgehenden Anrufen
Im Folgenden ist dargestellt, wie Sie einen ausgehenden Anruf tätigen. Dieser Code verwendet eine von Twilio bereitgestellte Website für die Rückgabe der TwiML-Antwort (Twilio Markup Language). Geben Sie Ihre Werte für die Telefonnummern **from_number** und **to_number** ein, und stellen Sie sicher, dass Sie die **from_number**-Telefonnummer für Ihr Twilio-Konto verifiziert haben, bevor Sie den Code ausführen.

```python
from urllib.parse import urlencode

# Import the Twilio Python Client.
from twilio.rest import TwilioRestClient

# Set your account ID and authentication token.
account_sid = "your_twilio_account_sid"
auth_token = "your_twilio_authentication_token"

# The number of the phone initiating the call.
# This should either be a Twilio number or a number that you've verified
from_number = "NNNNNNNNNNN"

# The number of the phone receiving call.
to_number = "NNNNNNNNNNN"

# Use the Twilio-provided site for the TwiML response.
url = "https://twimlets.com/message?"

# The phone message text.
message = "Hello world."

# Initialize the Twilio client.
client = TwilioRestClient(account_sid, auth_token)

# Make the call.
call = client.calls.create(to=to_number,
                           from_=from_number,
                           url=url + urlencode({'Message': message}))
print(call.sid)
```

Wie bereits erwähnt, verwendet dieser Code eine von Twilio bereitgestellte Website für die Rückgabe der TwiML-Antwort. Stattdessen können Sie die TwiML-Antwort auch von Ihrer eigenen Website bereitstellen lassen. Weitere Informationen dazu finden Sie unter [Bereitstellen von TwiML-Antworten von der eigenen Website](#howto_provide_twiml_responses).

## <a name="how-to-send-an-sms-message"></a><a id="howto_send_sms"></a>Senden von SMS-Nachrichten
Das folgende Beispiel zeigt, wie Sie eine SMS-Nachricht mit der `TwilioRestClient`-Klasse senden. Die **from_number**-Nummer wird von Twilio für Testkonten zum Senden von SMS-Nachrichten bereitgestellt. Die **to_number**-Nummer muss für Ihr Twilio-Konto überprüft werden, bevor Sie den Code ausführen.

```python
# Import the Twilio Python Client.
from twilio.rest import TwilioRestClient

# Set your account ID and authentication token.
account_sid = "your_twilio_account_sid"
auth_token = "your_twilio_authentication_token"

from_number = "NNNNNNNNNNN"  # With trial account, texts can only be sent from your Twilio number.
to_number = "NNNNNNNNNNN"
message = "Hello world."

# Initialize the Twilio client.
client = TwilioRestClient(account_sid, auth_token)

# Send the SMS message.
message = client.messages.create(to=to_number,
                                    from_=from_number,
                                    body=message)
```

## <a name="how-to-provide-twiml-responses-from-your-own-website"></a><a id="howto_provide_twiml_responses"></a>Bereitstellen von TwiML-Antworten von der eigenen Website
Wenn Ihre Anwendung einen Anruf an die Twilio-API initiiert, sendet Twilio Ihre Anforderung an eine URL, von der die Rückgabe einer TwiML-Antwort erwartet wird. Das obige Beispiel verwendet die von Twilio bereitgestellte URL [https://twimlets.com/message][twimlet_message_url]. (TwiML wurde für die Verwendung durch Twilio konzipiert, kann jedoch im Browser angezeigt werden. Klicken Sie beispielsweise auf [https://twimlets.com/message][twimlet_message_url], um ein leeres `<Response>` -Element anzuzeigen. Ein weiteres Beispiel: Klicken Sie auf [https://twimlets.com/message?Message%5B0%5D=Hello%20World][twimlet_message_url_hello_world], um ein `<Response>` -Element mit einem `<Say>` -Element anzuzeigen.)

Anstelle der von Twilio bereitgestellten URL können Sie auch Ihre eigene Website für die Rückgabe von HTTP-Antworten erstellen. Sie können die Website in allen Sprachen erstellen, die XML-Antworten zurückgeben. Dieses Beispiel geht davon aus, dass Sie die TwiML-Antwort mit Python erstellen.

Die folgenden Beispiele geben eine TwiML-Antwort aus, die beim Anruf **Hello World** sagt.

Mit Flask:

```python
from flask import Response
@app.route("/")
def hello():
    xml = '<Response><Say>Hello world.</Say></Response>'
    return Response(xml, mimetype='text/xml')
```

Mit Django:

```python
from django.http import HttpResponse
def hello(request):
    xml = '<Response><Say>Hello world.</Say></Response>'
    return HttpResponse(xml, content_type='text/xml')
```

Wie das vorstehende Beispiel zeigt, handelt es sich bei der TwiML-Antwort einfach um ein XML-Dokument. Die Twilio-Bibliothek für Python enthält Klassen, die TwiML für Sie generieren. Das folgende Beispiel generiert die gleiche Antwort wie oben, jedoch unter Verwendung des `twiml`-Moduls in der Twilio-Bibliothek für Python:

```python
from twilio import twiml

response = twiml.Response()
response.say("Hello world.")
print(str(response))
```

Weitere Informationen zu TwiML finden Sie unter [https://www.twilio.com/docs/api/twiml][twiml_reference].

Sobald Sie Ihre Python-Anwendung mit den TwiML-Antworten eingerichtet haben, können Sie die URL der Anwendung an die `client.calls.create`-Methode übergeben. Wenn Sie z.B. eine Webanwendung mit dem Namen **MyTwiML** für einen gehosteten Azure-Dienst bereitgestellt haben, können Sie die URL als Webhook verwenden, wie im folgenden Beispiel gezeigt:

```python
from twilio.rest import TwilioRestClient

account_sid = "your_twilio_account_sid"
auth_token = "your_twilio_authentication_token"
from_number = "NNNNNNNNNNN"
to_number = "NNNNNNNNNNN"
url = "http://your-domain-label.centralus.cloudapp.azure.com/MyTwiML/"

# Initialize the Twilio client.
client = TwilioRestClient(account_sid, auth_token)

# Make the call.
call = client.calls.create(to=to_number,
                           from_=from_number,
                           url=url)
print(call.sid)
```

## <a name="how-to-use-additional-twilio-services"></a><a id="AdditionalServices"></a>Verwenden zusätzlicher Twilio-Dienste
Zusätzlich zu den hier gezeigten Beispielen bietet Twilio webbasierte APIs, mit denen Sie zusätzliche Twilio-Funktionen in Ihrer Azure-Anwendung verwenden können. Ausführliche Informationen finden Sie in der [Twilio API-Dokumentation][twilio_api].

## <a name="next-steps"></a><a id="NextSteps"></a>Nächste Schritte
Nachdem Sie nun mit den Grundlagen des Twilio-Dienstes vertraut sind, finden Sie unter diesen Links weitere Informationen:

* [Twilio-Sicherheitsrichtlinien][twilio_security_guidelines]
* [Twilio-Anleitungen und Beispielcode][twilio_howtos]
* [Twilio-Schnellstart-Tutorials][twilio_quickstarts]
* [Twilio auf GitHub][twilio_on_github]
* [Kontakt zum Twilio-Support][twilio_support]

[special_offer]: https://ahoy.twilio.com/azure
[twilio_python]: https://github.com/twilio/twilio-python
[twilio_lib_docs]: https://www.twilio.com/docs/libraries/python
[twilio_github_readme]: https://github.com/twilio/twilio-python/blob/master/README.md

[twimlet_message_url]: https://twimlets.com/message
[twimlet_message_url_hello_world]: https://twimlets.com/message?Message%5B0%5D=Hello%20World
[twiml_reference]: https://www.twilio.com/docs/api/twiml
[twilio_pricing]: https://www.twilio.com/pricing

[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api]: https://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_console]:  https://www.twilio.com/console
[twilio_security_guidelines]: https://www.twilio.com/docs/security
[twilio_howtos]: https://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: https://www.twilio.com/help/contact
[twilio_quickstarts]: https://www.twilio.com/docs/quickstart
