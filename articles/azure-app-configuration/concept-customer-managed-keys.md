---
title: Verwenden von kundenseitig verwalteten Schlüsseln zum Verschlüsseln Ihrer Konfigurationsdaten
description: Verschlüsseln Ihrer Konfigurationsdaten mithilfe von kundenseitig verwalteten Schlüsseln
author: AlexandraKemperMS
ms.author: alkemper
ms.date: 07/28/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 46a1e99a7bc75efa85fcb5eff649c14af5abd17b
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96930499"
---
# <a name="use-customer-managed-keys-to-encrypt-your-app-configuration-data"></a>Verwenden von kundenseitig verwalteten Schlüsseln zum Verschlüsseln Ihrer App Configuration-Daten
Azure App Configuration [verschlüsselt vertrauliche ruhende Informationen](../security/fundamentals/encryption-atrest.md). Die Verwendung von kundenseitig verwalteten Schlüsseln bietet einen verbesserten Datenschutz, da Sie so Ihre Verschlüsselungsschlüssel verwalten können.  Wenn die Verschlüsselung mit verwalteten Schlüsseln verwendet wird, werden alle vertraulichen Informationen in App Configuration mit einem vom Benutzer bereitgestellten Azure Key Vault-Schlüssel verschlüsselt.  Dadurch kann der Verschlüsselungsschlüssel bei Bedarf rotiert werden.  Außerdem bietet sich dadurch die Möglichkeit, den Zugriff von Azure App Configuration auf vertrauliche Informationen zu widerrufen, indem der Zugriff der App Configuration-Instanz auf den Schlüssel widerrufen wird.

## <a name="overview"></a>Übersicht 
Azure App Configuration verschlüsselt vertrauliche ruhende Informationen mit einem 256-Bit-AES-Verschlüsselungsschlüssel, der von Microsoft bereitgestellt wird. Jede App Configuration-Instanz verfügt über einen eigenen vom Dienst verwalteten Verschlüsselungsschlüssel, der zum Verschlüsseln vertraulicher Informationen verwendet wird. Vertrauliche Informationen umfassen die Werte, die sich in Schlüssel-Wert-Paaren befinden.  Wenn die Funktion für kundenseitig verwaltete Schlüssel aktiviert ist, verwendet App Configuration eine verwaltete Identität, die der App Configuration-Instanz zugewiesen ist, um sich bei Azure Active Directory zu authentifizieren. Die verwaltete Identität ruft dann Azure Key Vault auf und umschließt den Verschlüsselungsschlüssel der App Configuration-Instanz. Der umschlossene Verschlüsselungsschlüssel wird dann gespeichert, und der nicht umschlossene Verschlüsselungsschlüssel wird eine Stunde lang in App Configuration zwischengespeichert. App Configuration aktualisiert die nicht umschlossene Version des Verschlüsselungsschlüssels der App Configuration-Instanz stündlich. Hierdurch wird die Verfügbarkeit unter normalen Betriebsbedingungen sichergestellt. 

>[!IMPORTANT]
> Wenn die der App Configuration-Instanz zugewiesene Identität nicht mehr autorisiert ist, den Verschlüsselungsschlüssel der Instanz zu entpacken, oder wenn der verwaltete Schlüssel endgültig gelöscht wird, können vertrauliche Informationen, die in der App Configuration-Instanz gespeichert sind, nicht mehr entschlüsselt werden. Die Verwendung der Funktion [vorläufiges Löschen](../key-vault/general/soft-delete-overview.md) von Azure Key Vault verringert die Chance für ein versehentliches Löschen Ihres Verschlüsselungsschlüssels.

Wenn Benutzer die Funktion für kundenseitig verwaltete Schlüssel in ihrer Azure App Configuration-Instanz aktivieren, kontrollieren sie damit die Fähigkeit des Diensts, auf ihre vertraulichen Informationen zuzugreifen. Der verwaltete Schlüssel dient als Stammverschlüsselungsschlüssel. Ein Benutzer kann den Zugriff seiner App Configuration-Instanz auf seinen verwalteten Schlüssel widerrufen, indem er seine Schlüsseltresor-Zugriffsrichtlinie ändert. Wenn dieser Zugriff widerrufen wird, verliert App Configuration innerhalb von einer Stunde die Fähigkeit, Benutzerdaten zu entschlüsseln. Zu diesem Zeitpunkt untersagt die App Configuration-Instanz jeglichen Zugriffsversuch. Diese Situation kann wiederhergestellt werden, indem Sie dem Dienst erneut Zugriff auf den verwalteten Schlüssel gewähren.  Innerhalb von einer Stunde ist App Configuration unter normalen Bedingungen dann in der Lage, Benutzerdaten zu entschlüsseln.

>[!NOTE]
>Alle Azure App Configuration-Daten werden für bis zu 24 Stunden in einer isolierten Sicherung gespeichert. Dies schließt den nicht umschlossenen Verschlüsselungsschlüssel ein. Diese Daten stehen dem Dienst oder Dienstteam nicht sofort zur Verfügung. Im Falle einer Notfallwiederherstellung widerruft sich Azure App Configuration selbst erneut von den verwalteten Schlüsseldaten.

## <a name="requirements"></a>Requirements (Anforderungen)
Die folgenden Komponenten sind erforderlich, um die Funktion für kundenseitig verwaltete Schlüssel für Azure App Configuration erfolgreich zu aktivieren:
- Azure App Configuration-Instanz im Standard-Tarif
- Azure Key Vault mit aktivierten Funktionen für vorläufiges Löschen und Bereinigungsschutz
- Ein RSA- oder RSA-HSM-Schlüssel innerhalb von Key Vault
    - Der Schlüssel darf nicht abgelaufen sein, er muss aktiviert sein, und für ihn müssen beide Funktionen aktiviert sein: Umschließen und Entpacken.

Sobald diese Ressourcen konfiguriert sind, bleiben noch zwei Schritte, um Azure App Configuration die Verwendung des Key Vault-Schlüssels zu gestatten:
1. Zuweisen einer verwalteten Identität zur Azure App Configuration-Instanz
2. Gewähren Sie der Identität die Berechtigungen `GET`, `WRAP` und `UNWRAP` in der Zugriffsrichtlinie des Ziel-Key Vaults.

## <a name="enable-customer-managed-key-encryption-for-your-azure-app-configuration-instance"></a>Aktivieren der kundenseitig verwalteten Schlüsselverschlüsselung für Ihre Azure App Configuration-Instanz
Zu Beginn benötigen Sie eine ordnungsgemäß konfigurierte Azure App Configuration-Instanz. Wenn Sie noch keine App Configuration-Instanz zur Verfügung haben, folgen Sie einer dieser Schnellstartanleitungen, um eine einzurichten:
- [Erstellen einer ASP.NET Core-App mit Azure App Configuration](quickstart-aspnet-core-app.md)
- [Erstellen einer .NET Core-App mit Azure App Configuration](quickstart-dotnet-core-app.md)
- [Erstellen einer .NET Framework-App mit Azure App Configuration](quickstart-dotnet-app.md)
- [Erstellen einer Java Spring-App mit Azure App Configuration](quickstart-java-spring-app.md)

>[!TIP]
> Azure Cloud Shell ist eine kostenlose interaktive Shell, mit der Sie die Befehlszeilenanweisungen in diesem Artikel ausführen können.  Für sie sind allgemeine Azure-Tools einschließlich des .NET Core SDK vorinstalliert. Wenn Sie bei Ihrem Azure-Abonnement angemeldet sind, starten Sie [Azure Cloud Shell](https://shell.azure.com) über shell.azure.com.  Weitere Informationen zu Azure Cloud Shell finden Sie in der [Dokumentation](../cloud-shell/overview.md).

### <a name="create-and-configure-an-azure-key-vault"></a>Erstellen und Konfigurieren einer Azure Key Vault-Instanz
1. Erstellen Sie eine Azure Key Vault-Instanz mithilfe der Azure CLI.  Beachten Sie, dass sowohl `vault-name` als auch `resource-group-name` vom Benutzer bereitgestellt werden und eindeutig sein müssen.  Wir verwenden `contoso-vault` und `contoso-resource-group` in diesen Beispielen.

    ```azurecli
    az keyvault create --name contoso-vault --resource-group contoso-resource-group
    ```
    
1. Aktivieren Sie vorläufiges Löschen und Bereinigungsschutz für die Key Vault-Instanz. Ersetzen Sie die Namen der Key Vault-Instanz (`contoso-vault`) und Ressourcengruppe (`contoso-resource-group`), die Sie in Schritt 1 erstellt haben.

    ```azurecli
    az keyvault update --name contoso-vault --resource-group contoso-resource-group --enable-purge-protection --enable-soft-delete
    ```
    
1. Erstellen Sie einen Key Vault-Schlüssel. Stellen Sie einen eindeutigen `key-name` für diesen Schlüssel bereit, und ersetzen Sie die Namen der in Schritt 1 erstellten Key Vault-Instanz (`contoso-vault`). Geben Sie an, ob Sie `RSA`- oder `RSA-HSM`-Verschlüsselung bevorzugen.

    ```azurecli
    az keyvault key create --name key-name --kty {RSA or RSA-HSM} --vault-name contoso-vault
    ```
    
    In der Ausgabe dieses Befehls wird die Schlüssel-ID ("kid") für den generierten Schlüssel angezeigt.  Notieren Sie sich die Schlüssel-ID, um sie später in dieser Übung zu verwenden.  Die Schlüssel-ID hat folgendes Format: `https://{my key vault}.vault.azure.net/keys/{key-name}/{Key version}`.  Die Schlüssel-ID enthält drei wichtige Komponenten:
    1. Key Vault-URI: `https://{my key vault}.vault.azure.net
    1. Key Vault-Schlüsselname: {Key Name}
    1. Key Vault-Schlüsselversion: {Key version}

1. Erstellen Sie eine systemseitig zugewiesene verwaltete Identität mit der Azure-CLI, wobei Sie die Namen Ihrer App Configuration-Instanz und der Ressourcengruppe ersetzen, die Sie in den vorherigen Schritten verwendet haben. Die verwaltete Identität wird verwendet, um auf den verwalteten Schlüssel zuzugreifen. Wir verwenden `contoso-app-config`, um den Namen einer App Configuration-Instanz zu illustrieren:
    
    ```azurecli
    az appconfig identity assign --name contoso-app-config --resource-group contoso-resource-group --identities [system]
    ```
    
    Die Ausgabe dieses Befehls enthält die Prinzipal-ID ("principalId") und Mandanten-ID ("tenandId") der systemseitig zugewiesenen Identität.  Diese IDs werden verwendet, um der Identität Zugriff auf den verwalteten Schlüssel zu gewähren.

    ```json
    {
    "principalId": {Principal Id},
    "tenantId": {Tenant Id},
    "type": "SystemAssigned",
    "userAssignedIdentities": null
    }
    ```

1. Die verwaltete Identität der Azure App Configuration-Instanz benötigt Zugriff auf den Schlüssel, um die Schlüsselüberprüfung, Ver- und Entschlüsselung durchzuführen. Die spezifische Gruppe von Aktionen, auf die sie Zugriff benötigt, umfasst : `GET`, `WRAP` und `UNWRAP` für Schlüssel.  Für das Gewähren des Zugriffs ist die Prinzipal-ID der verwalteten Identität der App Configuration-Instanz erforderlich. Dieser Wert wurde im vorherigen Schritt abgerufen. Er wird unten als `contoso-principalId` angezeigt. Gewähren Sie Berechtigung für den verwalteten Schlüssel mit dem folgenden Befehl:

    ```azurecli
    az keyvault set-policy -n contoso-vault --object-id contoso-principalId --key-permissions get wrapKey unwrapKey
    ```

1. Sobald die Azure App Configuration-Instanz auf den verwalteten Schlüssel zugreifen kann, können wir die Funktion für kundenseitig verwaltete Schlüssel mithilfe der Azure CLI im Dienst aktivieren. Rufen Sie die folgenden Eigenschaften ab, die Sie während der Schritte zur Schlüsselerstellung aufgezeichnet hatten: `key name` `key vault URI`.

    ```azurecli
    az appconfig update -g contoso-resource-group -n contoso-app-config --encryption-key-name key-name --encryption-key-version key-version --encryption-key-vault key-vault-Uri
    ```

Ihre Azure App Configuration-Instanz ist jetzt für die Verwendung eines in Azure Key Vault gespeicherten, kundenseitig verwalteten Schlüssels konfiguriert.

## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel haben Sie Ihre Azure App Configuration-Instanz für die Verwendung eines kundenseitig verwalteten Schlüssels für die Verschlüsselung konfiguriert.  Erfahren Sie, wie Sie [Ihren Dienst in verwaltete Azure-Identitäten integrieren](howto-integrate-azure-managed-service-identity.md).
