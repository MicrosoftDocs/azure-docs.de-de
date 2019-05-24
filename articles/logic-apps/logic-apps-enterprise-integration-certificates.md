---
title: 'Schützen von B2B-Nachrichten mit Zertifikaten: Azure Logic Apps | Microsoft-Dokumentation'
description: Hinzufügen von Zertifikaten zum Schützen von B2B-Nachrichten in Azure Logic Apps mit dem Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, LADocs
manager: jeconnoc
ms.assetid: 4cbffd85-fe8d-4dde-aa5b-24108a7caa7d
ms.suite: integration
ms.topic: article
ms.date: 08/17/2018
ms.openlocfilehash: 38bc1615c0849a33ddfa5790a66fc05d681ce339
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66167161"
---
# <a name="secure-b2b-messages-with-certificates"></a>Schützen von B2B-Nachrichten mit Zertifikaten

Wenn B2B-Kommunikation vertraulich sein soll, können Sie sie für Ihre Unternehmensintegrations-Apps, insbesondere Logik-Apps, schützen, indem Sie Ihrem Integrationskonto Zertifikate hinzufügen. Zertifikate sind digitale Dokumente, mit denen die Identität der Teilnehmer an einer elektronischen Kommunikation überprüft wird und die zur Sicherung der Kommunikation auf folgende Arten dienen:

* Verschlüsseln des Nachrichteninhalts
* Digitales Signieren von Nachrichten 

In Ihren Unternehmensintegrations-Apps können Sie die folgenden Zertifikate verwenden:

* [Öffentliche Zertifikate](https://en.wikipedia.org/wiki/Public_key_certificate), die Sie über eine öffentliche [Zertifizierungsstelle (ZS)](https://en.wikipedia.org/wiki/Certificate_authority) im Internet erwerben müssen, für die jedoch keine Schlüssel erforderlich sind 

* Private Zertifikate oder [*selbstsignierte Zertifikate*](https://en.wikipedia.org/wiki/Self-signed_certificate), die Sie selbst erstellen und ausstellen, für die jedoch zusätzlich private Schlüssel erforderlich sind 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="upload-a-public-certificate"></a>Hochladen eines öffentlichen Zertifikats

Um ein *öffentliches Zertifikat* in Logik-Apps mit B2B-Funktionen verwenden zu können, müssen Sie das Zertifikat zuerst in Ihr Integrationskonto hochladen. Nachdem Sie die Eigenschaften in den von Ihnen erstellten [Vereinbarungen](logic-apps-enterprise-integration-agreements.md) definiert haben, steht das Zertifikat zum Schützen Ihrer B2B-Nachrichten zur Verfügung.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie im Azure-Hauptmenü die Option **Alle Ressourcen** aus. Geben Sie im Suchfeld den Namen Ihres Integrationskontos ein, und wählen Sie dann das gewünschte Integrationskonto aus.

   ![Suchen und Auswählen Ihres Integrationskontos](media/logic-apps-enterprise-integration-certificates/select-integration-account.png)  

2. Klicken Sie unter **Komponenten** auf die Kachel **Zertifikate**.

   ![„Zertifikate“ auswählen](media/logic-apps-enterprise-integration-certificates/add-certificates.png)

3. Wählen Sie unter **Zertifikate** die Option **Hinzufügen** aus. Geben Sie unter **Zertifikat hinzufügen** die Details für Ihr Zertifikat an. Wählen Sie **OK** aus, wenn Sie fertig sind.

   | Eigenschaft | Wert | BESCHREIBUNG | 
   |----------|-------|-------------|
   | **Name** | <*certificate-name*> (Zertifikatname) | Der Name Ihres Zertifikats (in diesem Beispiel „PublicCert“) | 
   | **Zertifikattyp** | Öffentlich | Der Typ Ihres Zertifikats |
   | **Certificate** | <*certificate-file-name*> (Name der Zertifikatdatei) | Klicken Sie neben dem Feld **Zertifikat** auf das Ordnersymbol, um die Zertifikatdatei zu suchen und auszuwählen, die Sie hochladen möchten. |
   ||||

   ![Klicken auf „Hinzufügen“, Angeben der Zertifikatdetails](media/logic-apps-enterprise-integration-certificates/public-certificate-details.png)

   Azure lädt das Zertifikat nach dem Überprüfen Ihrer Auswahl hoch.

   ![Anzeigen des neuen Zertifikats in Azure](media/logic-apps-enterprise-integration-certificates/new-public-certificate.png) 

## <a name="upload-a-private-certificate"></a>Hochladen eines privaten Zertifikats

Um ein *privates Zertifikat* in Logik-Apps mit B2B-Funktionen verwenden zu können, müssen Sie das Zertifikat zuerst in Ihr Integrationskonto hochladen. Außerdem benötigen Sie einen privaten Schlüssel, den Sie zuerst zu [Azure Key Vault](../key-vault/key-vault-get-started.md) hinzufügen müssen. 

Nachdem Sie die Eigenschaften in den von Ihnen erstellten [Vereinbarungen](logic-apps-enterprise-integration-agreements.md) definiert haben, steht das Zertifikat zum Schützen Ihrer B2B-Nachrichten zur Verfügung.

> [!NOTE]
> Stellen Sie bei privaten Zertifikaten sicher, dass Sie ein entsprechendes öffentliches Zertifikat hinzufügen, das in der [AS2-Vereinbarung](logic-apps-enterprise-integration-as2.md) in den **Sende- und Empfangseinstellungen** für das Signieren und Verschlüsseln von Nachrichten angezeigt wird.

1. [Fügen Sie Ihren privaten Schlüssel zu Azure Key Vault hinzu](../key-vault/certificate-scenarios.md#import-a-certificate), und geben Sie einen **Schlüsselnamen** an.
   
2. Autorisieren Sie Azure Logic Apps für die Ausführung von Vorgängen für Azure Key Vault. Verwenden Sie zum Erteilen des Zugriffs auf den Logic Apps-Dienstprinzipal den PowerShell-Befehl [Set-AzureKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy). Beispiel:

   `Set-AzKeyVaultAccessPolicy -VaultName 'TestcertKeyVault' -ServicePrincipalName 
   '7cd684f4-8a78-49b0-91ec-6a35d38739ba' -PermissionsToKeys decrypt, sign, get, list`
 
3. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie im Azure-Hauptmenü die Option **Alle Ressourcen** aus. Geben Sie im Suchfeld den Namen Ihres Integrationskontos ein, und wählen Sie dann das gewünschte Integrationskonto aus.

   ![Suchen Ihres Integrationskontos](media/logic-apps-enterprise-integration-certificates/select-integration-account.png) 

4. Klicken Sie unter **Komponenten** auf die Kachel **Zertifikate**.  

   ![Kachel „Zertifikate“ auswählen](media/logic-apps-enterprise-integration-certificates/add-certificates.png)

5. Wählen Sie unter **Zertifikate** die Option **Hinzufügen** aus. Geben Sie unter **Zertifikat hinzufügen** die Details für Ihr Zertifikat an. Wählen Sie **OK** aus, wenn Sie fertig sind.

   | Eigenschaft | Wert | BESCHREIBUNG | 
   |----------|-------|-------------|
   | **Name** | <*certificate-name*> (Zertifikatname) | Der Name Ihres Zertifikats (in diesem Beispiel „privateCert“) | 
   | **Zertifikattyp** | Private | Der Typ Ihres Zertifikats |
   | **Certificate** | <*certificate-file-name*> (Name der Zertifikatdatei) | Klicken Sie neben dem Feld **Zertifikat** auf das Ordnersymbol, um die Zertifikatdatei zu suchen und auszuwählen, die Sie hochladen möchten. | 
   | **Ressourcengruppe** | <*integration-account-resource-group*> (Ressourcengruppe des Integrationskontos) | Die Ressourcengruppe Ihres Integrationskontos (in diesem Beispiel „MyResourceGroup“) | 
   | **Schlüsseltresor** | <*key-vault-name*> (Name des Schlüsseltresors) | Der Name Ihres Azure-Schlüsseltresors |
   | **Schlüsselname** | <*key-name*> | Der Name Ihres Schlüssels |
   ||||

   ![Klicken auf „Hinzufügen“, Angeben der Zertifikatdetails](media/logic-apps-enterprise-integration-certificates/private-certificate-details.png)

   Azure lädt das Zertifikat nach dem Überprüfen Ihrer Auswahl hoch.

   ![Anzeigen des neuen Zertifikats in Azure](media/logic-apps-enterprise-integration-certificates/new-private-certificate.png) 

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen einer B2B-Vereinbarung](logic-apps-enterprise-integration-agreements.md)
