---
title: include file
description: include file
services: azure-communication-services
author: tomaschladek
manager: nmurav
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: tchladek
ms.openlocfilehash: 472129be5baa865365b49894b705d84c23e9cd04
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97506295"
---
## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Python](https://www.python.org/downloads/) 2.7, 3.5 oder höher.
- Eine aktive Communication Services-Ressource und eine Verbindungszeichenfolge. [Erstellen Sie eine Communication Services-Ressource.](../create-communication-resource.md)

## <a name="setting-up"></a>Einrichten

### <a name="create-a-new-python-application"></a>Erstellen einer neuen Python-Anwendung

1. Öffnen Sie Ihr Terminal- oder Befehlsfenster, erstellen Sie ein neues Verzeichnis für Ihre App, und navigieren Sie zu diesem Verzeichnis.

   ```console
   mkdir access-tokens-quickstart && cd access-tokens-quickstart
   ```

1. Erstellen Sie mithilfe eines Text-Editors eine Datei mit dem Namen **issue-access-tokens.py** im Stammverzeichnis des Projekts, und fügen Sie die Struktur für das Programm hinzu (einschließlich einer einfachen Ausnahmebehandlung). In den folgenden Abschnitten wird dieser Datei der gesamte Quellcode für diese Schnellstartanleitung hinzugefügt.

   ```python
   import os
   from azure.communication.administration import CommunicationIdentityClient

   try:
      print('Azure Communication Services - Access Tokens Quickstart')
      # Quickstart code goes here
   except Exception as ex:
      print('Exception:')
      print(ex)
   ```

### <a name="install-the-package"></a>Installieren des Pakets

Installieren Sie im Anwendungsverzeichnis mithilfe des Befehls `pip install` das Python-Paket der Azure Communication Services-Clientbibliothek „Administration“.

```console
pip install azure-communication-administration
```

## <a name="authenticate-the-client"></a>Authentifizieren des Clients

Instanziieren Sie einen Kommunikationsidentitätsclient (`CommunicationIdentityClient`) mit Ihrer Verbindungszeichenfolge. Im folgenden Code wird die Verbindungszeichenfolge für die Ressource aus einer Umgebungsvariablen namens `COMMUNICATION_SERVICES_CONNECTION_STRING` abgerufen. Informationen zur Verwaltung der Verbindungszeichenfolge Ihrer Ressource finden Sie [hier](../create-communication-resource.md#store-your-connection-string).

Fügen Sie diesen Code im `try`-Block hinzu:

```python
# This code demonstrates how to fetch your connection string
# from an environment variable.
connection_string = os.environ['COMMUNICATION_SERVICES_CONNECTION_STRING']

# Instantiate the identity client
client = CommunicationIdentityClient.from_connection_string(connection_string)
```

## <a name="create-an-identity"></a>Erstellen einer Identität

Von Azure Communication Services wird ein einfaches Identitätsverzeichnis gepflegt. Verwenden Sie die Methode `create_user`, um in dem Verzeichnis einen neuen Eintrag mit einer eindeutigen `Id` zu erstellen. Speichern Sie die empfangene Identität mit einer Zuordnung zu den Benutzern Ihrer Anwendung. Beispielsweise, indem Sie sie in der Datenbank des Anwendungsservers speichern. Die Identität ist später erforderlich, um Zugriffstoken auszustellen.

```python
identity = client.create_user()
print("\nCreated an identity with ID: " + identity.identifier + ":")
```

## <a name="issue-access-tokens"></a>Ausstellen von Zugriffstoken

Verwenden Sie die Methode `issue_token`, um ein Zugriffstoken für eine bereits vorhandene Communication Services-Identität auszustellen. Der Parameter `scopes` definiert einen Satz primitiver Elemente, die dieses Zugriffstoken autorisieren. Weitere Informationen finden Sie in der [Liste der unterstützten Aktionen](../../concepts/authentication.md). Eine neue Instanz des Parameters `communicationUser` kann basierend auf der Zeichenfolgendarstellung der Azure Communication Service-Identität generiert werden.

```python
# Issue an access token with the "voip" scope for an identity
token_result = client.issue_token(identity, ["voip"])
expires_on = token_result.expires_on.strftime('%d/%m/%y %I:%M %S %p')
print("\nIssued an access token with 'voip' scope that expires at " + expires_on + ":")
print(token_result.token)
```

Zugriffstoken sind kurzlebige Anmeldeinformationen, die erneut ausgestellt werden müssen. Wenn dies nicht der Fall ist, kann die Benutzerumgebung Ihrer Anwendung unterbrochen werden. Die Antworteigenschaft `expires_on` gibt die Lebensdauer des Zugriffstokens an.

## <a name="refresh-access-tokens"></a>Zugriffstoken für die Aktualisierung

Verwenden Sie zum Aktualisieren eines Zugriffstokens das `CommunicationUser`-Objekt für die erneute Ausstellung:

```python  
# Value existingIdentity represents identity of Azure Communication Services stored during identity creation
identity = CommunicationUser(existingIdentity)
token_result = client.issue_token( identity, ["voip"])
```

## <a name="revoke-access-tokens"></a>Widerrufen von Zugriffstoken

In einigen Fällen können Sie Zugriffstoken explizit widerrufen. Beispielsweise dann, wenn der Benutzer einer Anwendung das Kennwort ändert, das für die Authentifizierung beim Dienst verwendet wird. Die Methode `revoke_tokens` erklärt alle aktiven Zugriffstoken für ungültig, die für die Identität ausgestellt wurden.

```python  
client.revoke_tokens(identity)
print("\nSuccessfully revoked all access tokens for identity with ID: " + identity.identifier)
```

## <a name="delete-an-identity"></a>Löschen einer Identität

Wird eine Identität gelöscht, werden alle aktiven Zugriffstoken widerrufen, und für die Identität können keine Zugriffstoken mehr ausgestellt werden. Außerdem werden alle gespeicherten Inhalte entfernt, die der Identität zugeordnet sind.

```python
client.delete_user(identity)
print("\nDeleted the identity with ID: " + identity.identifier)
```

## <a name="run-the-code"></a>Ausführen des Codes

Navigieren Sie an einer Konsoleneingabeaufforderung zum Verzeichnis mit der Datei *issue-access-token.py*, und führen Sie anschließend den folgenden Befehl vom Typ `python` aus, um die App auszuführen.

```console
python ./issue-access-token.py
```
