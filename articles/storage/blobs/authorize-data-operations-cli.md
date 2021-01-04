---
title: Auswählen der Autorisierung des Zugriffs auf Blobdaten mit der Azure CLI
titleSuffix: Azure Storage
description: Geben Sie an, wie Datenvorgänge für Blobdaten mit der Azure CLI autorisiert werden. Sie können Datenvorgänge mit Azure AD-Anmeldeinformationen, dem Kontozugriffsschlüssel oder einem SAS-Token (Shared Access Signature) autorisieren.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/13/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurecli
ms.openlocfilehash: 060bfb6c88bbed8ba12c5b5ebfd2e9617f5abfb2
ms.sourcegitcommit: 295db318df10f20ae4aa71b5b03f7fb6cba15fc3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2020
ms.locfileid: "94637237"
---
# <a name="choose-how-to-authorize-access-to-blob-data-with-azure-cli"></a>Auswählen der Autorisierung des Zugriffs auf Blobdaten mit der Azure CLI

Azure Storage stellt Erweiterungen für die Azure CLI bereit, mit denen Sie angeben können, wie Vorgänge für Blobdaten autorisiert werden sollen. Sie können Datenvorgänge auf folgende Weise autorisieren:

- Mit einem Azure Active Directory (Azure AD)-Sicherheitsprinzipal. Microsoft empfiehlt, die Verwendung von Azure AD-Anmeldeinformationen für mehr Sicherheit und Benutzerfreundlichkeit.
- Mit dem Kontozugriffsschlüssel oder einem SAS-Token (Shared Access Signature).

## <a name="specify-how-data-operations-are-authorized"></a>Angeben, wie Datenvorgänge autorisiert werden

Azure CLI-Befehle zum Lesen und Schreiben von Blobdaten enthalten den optionalen Parameter `--auth-mode`. Legen Sie diesen Parameter fest, um anzugeben, wie ein Datenvorgang autorisiert werden soll:

- Legen Sie den Parameter `--auth-mode` auf `login` fest, um sich mit einem Azure AD-Sicherheitsprinzipal anzumelden (empfohlen).
- Legen Sie den Parameter `--auth-mode` auf den Legacywert `key` fest, damit versucht wird, den Kontozugriffsschlüssel für die Autorisierung abzurufen. Wenn Sie den Parameter `--auth-mode` weglassen, versucht die Azure CLI ebenfalls, den Zugriffsschlüssel abzurufen.

Damit Sie den Parameter `--auth-mode` verwenden können, vergewissern Sie sich, dass mindestens Version 2.0.46 der Azure CLI installiert ist. Führen Sie `az --version` aus, um Ihre installierte Version zu überprüfen.

> [!IMPORTANT]
> Wenn Sie den Parameter `--auth-mode` weglassen oder auf `key`festlegen, versucht die Azure CLI, den Kontozugriffsschlüssel für die Autorisierung zu verwenden. In diesem Fall empfiehlt Microsoft, den Zugriffsschlüssel entweder im Befehl oder in der Umgebungsvariablen **AZURE_STORAGE_KEY** bereitzustellen. Weitere Informationen zu Umgebungsvariablen finden Sie im Abschnitt [Festlegen von Umgebungsvariablen für Autorisierungsparameter](#set-environment-variables-for-authorization-parameters).
>
> Wenn Sie den Zugriffsschlüssel nicht angeben, versucht die Azure CLI, den Azure Storage-Ressourcenanbieter aufzurufen, um den Schlüssel für jeden Vorgang abzurufen. Das Ausführen vieler Datenvorgänge, für die ein Aufruf des Ressourcenanbieters erforderlich ist, kann zu einer Drosselung führen. Weitere Informationen zu Grenzwerten für Ressourcenanbieter finden Sie unter [Skalierbarkeits- und Leistungsziele für den Azure Storage-Ressourcenanbieter](../common/scalability-targets-resource-provider.md).

## <a name="authorize-with-azure-ad-credentials"></a>Autorisieren mit Azure AD-Anmeldeinformationen

Wenn Sie sich mit Azure AD-Anmeldeinformationen bei der Azure-Befehlszeilenschnittstelle anmelden, wird ein OAuth 2.0-Zugriffstoken zurückgegeben. Dieses Token wird dann automatisch von der Befehlszeilenschnittstelle verwendet, um nachfolgende Vorgänge für Blob- oder Queue Storage-Daten zu autorisieren. Für unterstützte Vorgänge müssen Sie mit dem Befehl keinen Kontoschlüssel und kein SAS-Token mehr übergeben.

Sie können einem Azure AD-Sicherheitsprinzipal über die rollenbasierte Zugriffssteuerung (Azure RBAC) Berechtigungen für Blobdaten zuweisen. Weitere Informationen zu Azure-Rollen in Azure Storage finden Sie unter [Verwalten der Zugriffsrechte für Azure Storage-Daten mit Azure RBAC](../common/storage-auth-aad-rbac-portal.md).

### <a name="permissions-for-calling-data-operations"></a>Berechtigungen für das Aufrufen von Datenvorgängen

Die Azure Storage-Erweiterungen werden für Vorgänge mit Blobdaten unterstützt. Welche Vorgänge Sie aufrufen können, hängt von den Berechtigungen des Azure AD-Sicherheitsprinzipals ab, mit dem Sie sich bei der Azure-Befehlszeilenschnittstelle anmelden. Die Berechtigungen für Azure Storage-Container werden über Azure RBAC zugewiesen. Wenn Sie beispielsweise die Rolle **Storage-Blobdatenleser** zugewiesen haben, können Sie Skriptbefehle ausführen, die Daten aus einem Container lesen. Haben Sie die Rolle **Mitwirkender an Storage-Blobdaten** zugewiesen, können Sie Skriptbefehle ausführen, die einen Container oder die darin enthaltenen Daten lesen, schreiben oder löschen.

Einzelheiten zu den Berechtigungen, die für die einzelnen Azure Storage-Vorgänge in einem Container erforderlich sind, finden Sie unter [Aufrufen von Speichervorgängen mit OAuth-Token](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens).  

### <a name="example-authorize-an-operation-to-create-a-container-with-azure-ad-credentials"></a>Beispiel: Autorisieren eines Vorgangs zum Erstellen eines Containers mit Azure AD-Anmeldeinformationen

Im folgenden Beispiel sehen Sie, wie mithilfe Ihrer Azure AD-Anmeldeinformationen über die Azure CLI ein Container erstellt wird. Damit Sie den Container erstellen können, müssen Sie sich bei der Azure CLI anmelden, und Sie benötigen eine Ressourcengruppe und ein Speicherkonto. Informationen zum Erstellen dieser Ressourcen finden Sie unter [Schnellstart: Erstellen, Herunterladen und Auflisten von Blobs mit der Azure-Befehlszeilenschnittstelle](../blobs/storage-quickstart-blobs-cli.md).

1. Weisen Sie sich vor der Erstellung des Containers selbst die Rolle [Mitwirkender an Storage-Blobdaten](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) zu. Obwohl Sie der Kontobesitzer sind, benötigen Sie explizite Berechtigungen, um Datenvorgänge in Ihrem Speicherkonto ausführen zu können. Weitere Informationen zum Zuweisen von Azure-Rollen finden Sie unter [Zuweisen einer Azure-Rolle für den Zugriff auf Blob- und Warteschlangendaten über das Azure-Portal](../common/storage-auth-aad-rbac-portal.md).

    > [!IMPORTANT]
    > Die Azure-Rollenzuweisungen können einige Minuten dauern.

1. Rufen Sie mithilfe des `--auth-mode`-Parameters, für den `login` festgelegt wurde, den Befehl [az storage container create](/cli/azure/storage/container#az-storage-container-create) auf, um den Container mithilfe Ihrer Azure AD-Anmeldeinformationen zu erstellen. Denken Sie daran, die Platzhalterwerte in eckigen Klammern durch Ihre eigenen Werte zu ersetzen:

    ```azurecli
    az storage container create \
        --account-name <storage-account> \
        --name sample-container \
        --auth-mode login
    ```

## <a name="authorize-with-the-account-access-key"></a>Autorisieren mit dem Kontozugriffsschlüssel

Wenn Sie über den Kontoschlüssel verfügen, können Sie jeden beliebigen Azure Storage-Datenvorgang aufrufen. Im Allgemeinen bietet die Verwendung des Kontoschlüssels weniger Sicherheit. Wenn der Kontoschlüssel kompromittiert ist, sind möglicherweise alle Daten in Ihrem Konto gefährdet.

Das folgende Beispiel zeigt das Erstellen eines Containers mit dem Kontozugriffsschlüssel. Geben Sie den Kontoschlüssel an, und legen Sie für den Parameter `--auth-mode` den Wert `key` fest:

```azurecli
az storage container create \
    --account-name <storage-account> \
    --name sample-container \
    --account-key <key>
    --auth-mode key
```

## <a name="authorize-with-a-sas-token"></a>Autorisieren mit einem SAS-Token

Wenn Sie über ein SAS-Token verfügen, können Sie Datenvorgänge aufzurufen, die von der SAS zugelassen werden. Das folgende Beispiel zeigt das Erstellen eines Containers mit einem SAS-Token:

```azurecli
az storage container create \
    --account-name <storage-account> \
    --name sample-container \
    --sas-token <token>
```

## <a name="set-environment-variables-for-authorization-parameters"></a>Festlegen von Umgebungsvariablen für Autorisierungsparameter

Sie können Autorisierungsparameter in Umgebungsvariablen angeben, um zu vermeiden, dass diese in jeden Aufruf eines Azure Storage-Datenvorgangs eingeschlossen werden müssen. In der folgenden Tabelle werden die verfügbaren Umgebungsvariablen beschrieben.

| Umgebungsvariable                  | BESCHREIBUNG                                                                                                                                                                                                                                                                                                                                                                     |
|---------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|    AZURE_STORAGE_ACCOUNT              |    Der Name des Speicherkontos. Diese Variable sollte in Verbindung mit dem Speicherkontoschlüssel oder einem SAS-Token verwendet werden. Ist keines von beiden vorhanden, versucht die Azure CLI, den Speicherkonto-Zugriffsschlüssel mithilfe des authentifizierten Azure AD-Kontos abzurufen. Wenn eine große Anzahl von Befehlen gleichzeitig ausgeführt wird, kann der Drosselungsgrenzwert des Azure Storage-Ressourcenanbieters erreicht werden. Weitere Informationen zu Grenzwerten für Ressourcenanbieter finden Sie unter [Skalierbarkeits- und Leistungsziele für den Azure Storage-Ressourcenanbieter](../common/scalability-targets-resource-provider.md).             |
|    AZURE_STORAGE_KEY                  |    Der Speicherkontoschlüssel. Diese Variable muss in Verbindung mit dem Speicherkontonamen verwendet werden.                                                                                                                                                                                                                                                                          |
|    AZURE_STORAGE_CONNECTION_STRING    |    Eine Verbindungszeichenfolge, die den Speicherkontoschlüssel oder ein SAS-Token enthält. Diese Variable muss in Verbindung mit dem Speicherkontonamen verwendet werden.                                                                                                                                                                                                                       |
|    AZURE_STORAGE_SAS_TOKEN            |    Ein SAS-Token (Shared Access Signature). Diese Variable muss in Verbindung mit dem Speicherkontonamen verwendet werden.                                                                                                                                                                                                                                                            |
|    AZURE_STORAGE_AUTH_MODE            |    Der Autorisierungsmodus, mit dem der Befehl ausgeführt werden soll. Zulässige Werte sind `login` (empfohlen) oder `key`. Wenn Sie `login` angeben, verwendet die Azure CLI Ihre Azure AD-Anmeldeinformationen, um den Datenvorgang zu autorisieren. Wenn Sie den Legacymodus `key` angeben, versucht die Azure CLI, den Kontozugriffsschlüssel abzufragen und den Befehl mit dem Schlüssel zu autorisieren.    |

## <a name="next-steps"></a>Nächste Schritte

- [Verwenden der Azure-Befehlszeilenschnittstelle zum Zuweisen einer Azure-Rolle für den Zugriff auf Blob- und Warteschlangendaten](../common/storage-auth-aad-rbac-cli.md)
- [Autorisieren des Zugriffs auf Blob- und Warteschlangendaten mit verwalteten Identitäten für Azure-Ressourcen](../common/storage-auth-aad-msi.md)