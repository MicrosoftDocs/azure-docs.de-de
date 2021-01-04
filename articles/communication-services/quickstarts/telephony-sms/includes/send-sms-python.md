---
title: include file
description: include file
services: azure-communication-services
author: danieldoolabh
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 09/03/2020
ms.topic: include
ms.custom: include file
ms.author: dadoolab
ms.openlocfilehash: a24d9531b7b2d2d2f31eec275da7db7e48b9c74a
ms.sourcegitcommit: 4c89d9ea4b834d1963c4818a965eaaaa288194eb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/04/2020
ms.locfileid: "96615943"
---
Steigen Sie in Azure Communication Services ein, indem Sie die Python-Clientbibliothek für SMS von Communication Services nutzen, um SMS-Nachrichten zu senden.

Im Rahmen dieser Schnellstartanleitung fallen in Ihrem Azure-Konto ggf. geringfügige Kosten im Centbereich an.

<!--**TODO: update all these reference links as the resources go live**

[API reference documentation](../../../references/overview.md) | [Library source code](#todo-sdk-repo) | [Package (PiPy)](#todo-nuget) | [Samples](#todo-samples)--> 

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- [Python](https://www.python.org/downloads/) 2.7, 3.5 oder höher.
- Eine aktive Communication Services-Ressource und eine Verbindungszeichenfolge. [Erstellen Sie eine Communication Services-Ressource.](../../create-communication-resource.md)
- Eine für SMS-Nachrichten geeignete Telefonnummer. [Beziehen Sie eine Telefonnummer.](../get-phone-number.md)

### <a name="prerequisite-check"></a>Prüfen der Voraussetzungen

- Führen Sie in einem Terminal- oder Befehlsfenster den Befehl `python --version` aus, um sich zu vergewissern, dass Python installiert ist.
- Melden Sie sich zum Anzeigen der Telefonnummern für Ihre Communication Services-Ressource beim [Azure-Portal](https://portal.azure.com/) an, suchen Sie nach Ihrer Communication Services-Ressource, und öffnen Sie im linken Navigationsbereich die Registerkarte **Telefonnummern**.

## <a name="setting-up"></a>Einrichten

### <a name="create-a-new-python-application"></a>Erstellen einer neuen Python-Anwendung

Öffnen Sie Ihr Terminal- oder Befehlsfenster, erstellen Sie ein neues Verzeichnis für Ihre App, und navigieren Sie zu diesem Verzeichnis.

```console
mkdir sms-quickstart && cd sms-quickstart
```

Erstellen Sie mithilfe eines Text-Editors eine Datei mit dem Namen **send-sms.py** im Stammverzeichnis des Projekts, und fügen Sie die Struktur für das Programm hinzu (einschließlich einer einfachen Ausnahmebehandlung). In den folgenden Abschnitten wird dieser Datei der gesamte Quellcode für diese Schnellstartanleitung hinzugefügt.

```python
import os
from azure.communication.sms import PhoneNumber
from azure.communication.sms import SendSmsOptions
from azure.communication.sms import SmsClient

try:
    # Quickstart code goes here
except Exception as ex:
    print('Exception:')
    print(ex)
```

### <a name="install-the-package"></a>Installieren des Pakets

Installieren Sie im Anwendungsverzeichnis mithilfe des Befehls `pip install` das Python-Paket der Clientbibliothek für SMS von Azure Communication Services.

```console
pip install azure-communication-sms --pre
```

## <a name="object-model"></a>Objektmodell

Die folgenden Klassen und Schnittstellen werden für einige der wichtigsten Features der Python-Clientbibliothek für SMS von Azure Communication Services verwendet:

| Name                                  | Beschreibung                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| SmsClient | Diese Klasse ist für alle SMS-Funktionen erforderlich. Sie instanziieren sie mit Ihren Abonnementinformationen und verwenden sie zum Senden von SMS-Nachrichten. |
| SendSmsOptions | Diese Klasse bietet Optionen zum Konfigurieren von Zustellberichten. Ist „enable_delivery_report“ auf „True“ festgelegt, wird bei erfolgreicher Zustellung ein Ereignis ausgegeben. |

## <a name="authenticate-the-client"></a>Authentifizieren des Clients

Instanziieren Sie einen SMS-Client (**SmsClient**) mit Ihrer Verbindungszeichenfolge. Im folgenden Code wird die Verbindungszeichenfolge für die Ressource aus einer Umgebungsvariablen namens `COMMUNICATION_SERVICES_CONNECTION_STRING` abgerufen. Informationen zur Verwaltung der Verbindungszeichenfolge Ihrer Ressource finden Sie [hier](../../create-communication-resource.md#store-your-connection-string).

```python
# This code demonstrates how to fetch your connection string
# from an environment variable.
connection_string = os.getenv('COMMUNICATION_SERVICES_CONNECTION_STRING')

# Create the SmsClient object which will be used to send SMS messages
sms_client = SmsClient.from_connection_string(connection_string)
```

## <a name="send-an-sms-message"></a>Senden einer SMS

Senden Sie eine SMS-Nachricht durch Aufrufen der Methode „Send“. Fügen Sie in **send-sms.py** am Ende des `try`-Blocks den folgenden Code hinzu:

```python

# calling send() with sms values
sms_response = sms_client.send(
        from_phone_number=PhoneNumber("<leased-phone-number>"),
        to_phone_numbers=[PhoneNumber("<to-phone-number>")],
        message="Hello World via SMS",
        send_sms_options=SendSmsOptions(enable_delivery_report=True)) # optional property

```

Ersetzen Sie `<leased-phone-number>` durch eine für SMS geeignete Telefonnummer, die Ihrer Communication Services-Instanz zugeordnet ist, und ersetzen Sie `<to-phone-number>` durch die Telefonnummer, an die Sie eine Nachricht senden möchten. 

Der Parameter `send_sms_options` ist ein optionaler Parameter zum Konfigurieren von Zustellberichten. Dies ist in Szenarien hilfreich, in denen Ereignisse ausgegeben werden sollen, wenn SMS-Nachrichten zugestellt wurden. Informationen zum Konfigurieren von Zustellberichten für SMS-Nachrichten finden Sie in der Schnellstartanleitung [Behandeln von SMS-Ereignissen](../handle-sms-events.md).

## <a name="run-the-code"></a>Ausführen des Codes

Führen Sie die Anwendung mit dem Befehl `python` aus dem Anwendungsverzeichnis aus.

```console
python send-sms.py
```
