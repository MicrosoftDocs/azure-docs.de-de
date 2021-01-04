---
title: Verwenden von kundenseitig verwalteten Schlüsseln (Bring-Your-Own-Key, BYOK) mit der REST-API
description: Verwenden Sie in diesem Tutorial vom Kunden verwaltete Schlüssel (Bring-Your-Own-Key, BYOK) mit einem Azure Media Services-Speicherkonto.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: tutorial
ms.date: 10/18/2020
ms.openlocfilehash: c8a5b682e2ac4879d2181bdb069cf554bad512d9
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498284"
---
# <a name="tutorial-use-customer-managed-keys-or-byok-with-media-services-rest-api"></a>Tutorial: Verwenden von kundenseitig verwalteten Schlüsseln (BYOK) mit der Media Services-REST-API

Mit der API „2020-05-01“ können Sie einen kundenseitig verwalteten RSA-Schlüssel mit einem Media Services-Konto verwenden, das eine systemseitig verwaltete Identität aufweist. Dieses Tutorial enthält eine Postman-Sammlung und -Umgebung zum Senden von REST-Anforderungen an Azure-Dienste. Dies sind die verwendeten Dienste:

- Azure Active Directory-Anwendungsregistrierung (Azure AD) für Postman
- Microsoft Graph-API
- Azure Storage
- Azure Key Vault
- Azure Media Services

In diesem Tutorial erfahren Sie, wie Sie Postman für die folgenden Zwecke nutzen:

> [!div class="checklist"]
> - Abrufen von Token für die Verwendung mit Azure-Diensten.
> - Erstellen einer Ressourcengruppe und eines Speicherkontos.
> - Erstellen eines Media Services-Kontos mit einer vom System verwalteten Identität.
> - Erstellen eines Schlüsseltresors zum Speichern eines vom Kunden verwalteten RSA-Schlüssels.
> - Aktualisieren des Media Services-Kontos für die Verwendung des RSA-Schlüssels mit dem Speicherkonto.
> - Verwenden von Variablen in Postman.

Falls Sie noch nicht über ein Azure-Abonnement verfügen, können Sie ein [kostenloses Testkonto](https://azure.microsoft.com/free/) erstellen.

## <a name="prerequisites"></a>Voraussetzungen

1. Registrieren Sie einen Dienstprinzipal mit den entsprechenden Berechtigungen.
1. Installieren Sie [Postman](https://www.postman.com).
1. Laden Sie die Postman-Sammlung für dieses Tutorial unter [Azure-Beispiele: media-services-customer-managed-keys-byok](https://github.com/Azure-Samples/media-services-customer-managed-keys-byok) herunter.

### <a name="register-a-service-principal-with-the-needed-permissions"></a>Registrieren Sie einen Dienstprinzipal mit den erforderlichen Berechtigungen

1. [Erstellen Sie einen Dienstprinzipal](../../active-directory/develop/howto-create-service-principal-portal.md).
1. Gehen Sie zu [Option 2: Erstellen eines neuen Anwendungsgeheimnisses](../../active-directory/develop/howto-create-service-principal-portal.md#authentication-two-options), um das Geheimnis des Dienstprinzipals abzurufen.

   > [!IMPORTANT]
   >Kopieren und speichern Sie das Geheimnis zur späteren Verwendung. Nachdem Sie die Geheimnisseite im Portal verlassen haben, können Sie nicht mehr auf das Geheimnis zugreifen.

1. Weisen Sie dem Dienstprinzipal Berechtigungen zu, wie im folgenden Screenshot dargestellt:

   :::image type="complex" source="./media/tutorial-byok/service-principal-permissions-1.png" alt-text="Screenshot der erforderlichen Berechtigungen für den Dienstprinzipal.":::
   Die Berechtigungen sind nach Dienst, Name der Berechtigung, Typ und dann Beschreibung aufgelistet. Azure Key Vault: Benutzeridentitätswechsel, delegiert, Vollzugriff auf Azure Key Vault. Azure-Dienstverwaltung: Benutzeridentitätswechsel, delegiert, Zugriff auf die Azure-Dienstverwaltung als Organisationsbenutzer. Azure Storage: Benutzeridentitätswechsel, delegiert, Zugriff auf Azure Storage. Media Services: Benutzeridentitätswechsel, delegiert, Zugriff auf Media Services. Microsoft Graph: User.Read, delegiert, Anmelden und Benutzerprofil lesen.
   :::image-end:::

### <a name="install-postman"></a>Installieren von Postman

Wenn Sie Postman noch nicht für die Verwendung mit Azure installiert haben, können Sie es unter [postman.com](https://www.postman.com/) abrufen.

### <a name="download-and-import-the-collection"></a>Herunterladen und Importieren der Sammlung

Laden Sie die Postman-Sammlung für dieses Tutorial unter [Azure-Beispiele: media-services-customer-managed-keys-byok](https://github.com/Azure-Samples/media-services-customer-managed-keys-byok) herunter.

## <a name="install-the-postman-collection-and-environment"></a>Installieren der Postman-Sammlung und -Umgebung

1. Führen Sie Postman aus.
1. Wählen Sie **Importieren** aus.
1. Wählen Sie **Upload files** (Dateien hochladen) aus.
1. Navigieren Sie zu dem Ort, an dem Sie die Sammlungs- und Umgebungsdateien gespeichert haben.
1. Wählen Sie die Sammlungs- und Umgebungsdateien aus.
1. Klicken Sie auf **Öffnen**. Es wird eine Warnung angezeigt, dass die Dateien nicht als API sondern als Sammlungen importiert werden. Diese Warnung braucht Sie nicht zu beunruhigen. Es ist das gewünschte Verhalten.

Die Sammlung wird nun in Ihren Sammlungen als BYOK angezeigt. Außerdem werden die Umgebungsvariablen in Ihren Umgebungen angezeigt.

### <a name="understand-the-rest-api-requests-in-the-collection"></a>Informationen zu den REST-API-Anforderungen in der Sammlung

Die Sammlung stellt die folgenden REST-API-Anforderungen zur Verfügung.

> [!NOTE]
>
>- Die Anforderungen müssen in der angegebenen Reihenfolge gesendet werden.
>- Für die meisten Anforderungen bestehen Testskripts, mit denen dynamisch globale Variablen für die nächste Anforderung in der Abfolge erstellt werden.
>- Sie brauchen globale Variablen nicht manuell zu erstellen.

In Postman werden diese Variablen in eckigen Klammern eingeschlossen angezeigt. Beispiel: `{{bearerToken}}`.

1. Abrufen eines Azure AD-Tokens: Der Test legt die globale Variable **bearerToken** fest.
2. Abrufen eines Microsoft Graph-Tokens: Der Test legt die globale Variable **graphToken** fest.
3. Abrufen von Details zu Dienstprinzipalen: Der Test legt die globale Variable **servicePrincipalObjectId** fest.
4. Erstellen eines Speicherkontos: Der Test legt die globale Variable **storageAccountId** fest.
5. Erstellen eines Media Services-Kontos mit einer vom System verwalteten Identität: Der Test legt die globale Variable **principalid** fest.
6. Erstellen eines Schlüsseltresors, um Zugriff auf den Dienstprinzipal zu erteilen: Der Test legt die globale Variable **keyVaultId** fest.
7. Abrufen eines Schlüsseltresortokens: Der Test legt die globale Variable **keyVaultToken** fest.
8. Erstellen des RSA-Schlüssels im Schlüsseltresor: Der Test legt die globale Variable **keyId** fest.
9. Aktualisieren des Media Services-Kontos für die Verwendung des Schlüssels mit dem Speicherkonto: Für diese Anforderung ist kein Testskript vorhanden.

## <a name="define-environment-variables"></a>Definieren von Umgebungsvariablen

1. Wählen Sie die Umgebungs-Dropdownliste aus, um zu der von Ihnen heruntergeladenen Umgebung zu wechseln.
1. Richten Sie Ihre Umgebungsvariablen in Postman ein. Sie werden ebenfalls als in eckige Klammern eingeschlossene Variablen verwendet. Beispiel: `{{tenantId}}`.

    - **tenantId**: Ihre Mandanten-ID.
    - **servicePrincipalId**: Die ID des Dienstprinzipals, den Sie mit Ihrer bevorzugten Methode einrichten, im Portal, an der CLI usw.
    - **servicePrincipalSecret**: Das für den Dienstprinzipal erstellte Geheimnis.
    - **Abonnement**: Ihre Abonnement-ID.
    - **storageName**: Der Name, den Sie Ihrem Speicher geben möchten.
    - **accountName**: Der Name des Media Service-Kontos, den Sie verwenden möchten.
    - **keyVaultName**: Der Schlüsseltresorname, den Sie verwenden möchten.
    - **resourceLocation**: Der Standort **CentralUs** oder wo immer Sie Ihre Ressourcen platzieren möchten. Diese Sammlung wurde nur mit **CentralUs** (USA, Mitte) getestet.
    - **resourceGroup**: Der Name der Ressourcengruppe.

    Die folgenden Ressourcen stellen die Norm für das Arbeiten mit Azure-Ressourcen dar. Es gibt daher keinen Grund, sie zu ändern.

    - **armResource**: `https://management.core.windows.net`
    - **graphResource**: `https://graph.windows.net/`
    - **keyVaultResource**: `https://vault.azure.net`
    - **armEndpoint**: `management.azure.com`
    - **graphEndpoint**: `graph.windows.net`
    - **aadEndpoint**: `login.microsoftonline.com`
    - **keyVaultDomainSuffix**: `vault.azure.net`

## <a name="send-the-requests"></a>Senden der Anforderungen

Nachdem Sie Ihre Umgebungsvariablen definiert haben, können Sie die Anforderungen nacheinander in der zuvor dargestellten Reihenfolge ausführen. Alternativ können Sie das Ausführungsmodul von Postman verwenden, um die Sammlung auszuführen.

## <a name="change-the-key"></a>Ändern des Schlüssels

Media Services erkennt Änderungen des Schlüssels automatisch. Erstellen Sie eine weitere Schlüsselversion für den gleichen Schlüssel, um diesen Vorgang zu testen. Media Services sollte den Schlüssel in weniger als 15 Minuten erkennen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die von Ihnen erstellten Ressourcen nicht weiter verwenden möchten und *sie Ihnen nicht mehr in Rechnung gestellt werden sollen*, löschen Sie sie.

## <a name="next-steps"></a>Nächste Schritte

Fahren Sie mit dem nächsten Artikel fort, und lernen Sie das...
> [!div class="nextstepaction"]
> [Codieren einer Remotedatei anhand einer URL und Streamen des Videos über REST](stream-files-tutorial-with-rest.md)