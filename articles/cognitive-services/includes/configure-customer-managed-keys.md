---
title: Verwenden des Azure-Portals zum Konfigurieren von kundenseitig verwalteten Schlüsseln
titleSuffix: Cognitive Services
description: Hier erfahren Sie, wie Sie im Azure-Portal kundenseitig verwaltete Schlüssel mit Azure Key Vault konfigurieren. Mit von Kunden verwalteten Schlüsseln können Sie Zugriffssteuerungen erstellen, rotieren, deaktivieren und widerrufen.
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 05/28/2020
ms.author: egeaney
ms.openlocfilehash: c79846b0a5b675c34e4e7919e9ecd9d591bfefe5
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96356357"
---
## <a name="customer-managed-keys-with-azure-key-vault"></a>Von Kunden verwaltete Schlüssel mit Azure Key Vault

Sie müssen Azure Key Vault verwenden, um kundenseitig verwaltete Schlüssel zu speichern. Sie können entweder Ihre eigenen Schlüssel erstellen und in einem Schlüsseltresor speichern oder mit den Azure Key Vault-APIs Schlüssel generieren. Die Cognitive Services-Ressource und der Schlüsseltresor müssen sich in derselben Region und in demselben Azure Active Directory-Mandanten (Azure AD) befinden, aber sie können sich in verschiedenen Abonnements befinden. Weitere Informationen zum Azure Key Vault finden Sie unter [What is Azure Key Vault? (Was ist der Azure Key Vault?)](../../key-vault/general/overview.md).

Wenn eine neue Cognitive Services-Ressource erstellt wird, wird sie immer mit von Microsoft verwalteten Schlüsseln verschlüsselt. Es ist nicht möglich, zum Zeitpunkt der Ressourcenerstellung kundenseitig verwaltete Schlüssel zu aktivieren. Kundenseitig verwaltete Schlüssel werden in Azure Key Vault gespeichert. Für den Schlüsseltresor müssen Zugriffsrichtlinien bereitgestellt werden, mit denen Schlüsselberechtigungen für die verwaltete Identität erteilt werden, die der Cognitive Services-Ressource zugeordnet ist. Die verwaltete Identität ist erst verfügbar, nachdem die Ressource mit dem erforderlichen Tarif für CMK erstellt wurde.

Durch das Aktivieren von kundenseitig verwalteten Schlüsseln wird auch eine systemseitig zugewiesene [verwaltete Identität](../../active-directory/managed-identities-azure-resources/overview.md) aktiviert. Dies ist eine Funktion von Azure AD. Sobald die systemseitig zugewiesene verwaltete Identität aktiviert ist, wird diese Ressource bei Azure Active Directory registriert. Nach der Registrierung erhält die verwaltete Identität Zugriff auf die Key Vault-Instanz, die bei der Einrichtung des kundenseitig verwalteten Schlüssels ausgewählt wurde. 

> [!IMPORTANT]
> Wenn Sie systemseitig zugewiesene verwaltete Identitäten deaktivieren, wird der Zugriff auf den Schlüsseltresor entfernt, und alle mit den Kundenschlüsseln verschlüsselten Daten sind nicht mehr zugänglich. Alle Features, die von diesen Daten abhängen, funktionieren nicht mehr.

> [!IMPORTANT]
> Verwaltete Identitäten unterstützen derzeit keine verzeichnisübergreifenden Szenarien. Wenn Sie vom Kunden verwaltete Schlüssel im Azure-Portal konfigurieren, wird automatisch eine verwaltete Identität im Hintergrund zugewiesen. Wenn Sie anschließend das Abonnement, die Ressourcengruppe oder die Ressource von einem Azure AD-Verzeichnis in ein anderes Verzeichnis verschieben, wird die der Ressource zugeordnete verwaltete Identität nicht an den neuen Mandanten übertragen, sodass kundenseitig verwaltete Schlüssel möglicherweise nicht mehr funktionieren. Weitere Informationen finden Sie in [Häufig gestellte Fragen und bekannte Probleme mit verwalteten Identitäten für Azure-Ressourcen](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories) unter **Übertragen eines Abonnements zwischen Azure AD-Verzeichnissen**.  

## <a name="configure-azure-key-vault"></a>Konfigurieren von Azure Key Vault

Bei der Verwendung kundenseitig verwalteter Schlüssel müssen im Schlüsseltresor zwei Eigenschaften festgelegt werden: **Vorläufiges Löschen** und **Nicht bereinigen**. Diese Eigenschaften sind standardmäßig nicht aktiviert, können aber entweder mithilfe von PowerShell oder per Azure CLI für einen neuen oder vorhandenen Schlüsseltresor aktiviert werden.

> [!IMPORTANT]
> Wenn Sie die Eigenschaften **Vorläufiges Löschen** und **Nicht Bereinigen** nicht aktiviert haben und Ihren Schlüssel löschen, können Sie die Daten in Ihrer Cognitive Services-Ressource nicht wiederherstellen.

Informationen zum Aktivieren dieser Eigenschaften für einen vorhandenen Schlüsseltresor finden Sie in den Abschnitten **Aktivieren des vorläufigen Löschens** und **Aktivieren des Bereinigungsschutzes** in einem der folgenden Artikel:

- [Verwenden des vorläufigen Löschens mit PowerShell](../../key-vault/general/key-vault-recovery.md)
- [Verwenden des vorläufigen Löschens mit der CLI](../../key-vault/general/key-vault-recovery.md)

Für die Azure Storage-Verschlüsselung werden nur RSA-Schlüssel der Größe 2048 unterstützt. Weitere Informationen zu Schlüsseln finden Sie unter **Key Vault-Schlüssel** in [Informationen zu Schlüsseln, Geheimnissen und Zertifikaten in Azure Key Vault](../../key-vault/general/about-keys-secrets-certificates.md).

## <a name="enable-customer-managed-keys-for-your-resource"></a>Aktivieren von kundenseitig verwalteten Schlüsseln für Ihre Ressource

Um vom Kunden verwaltete Schlüssel im Azure-Portal zu aktivieren, gehen Sie folgendermaßen vor:

1. Navigieren Sie zu Ihrer Cognitive Services-Ressource.
1. Klicken Sie auf dem Blatt **Einstellungen** für Ihre Cognitive Services Ressource auf **Verschlüsselung**. Wählen Sie wie im folgenden Screenshot gezeigt die Option **Von Kunden verwaltete Schlüssel** aus.

    ![Screenshot zur Auswahl der Option „Von Kunden verwaltete Schlüssel“](../media/cognitive-services-encryption/selectcmk.png)

## <a name="specify-a-key"></a>Angeben eines Schlüssels

Nachdem Sie „Von Kunden verwaltete Schlüssel“ aktiviert haben, können Sie einen Schlüssel angeben, der der Cognitive Services-Ressource zugeordnet werden soll.

### <a name="specify-a-key-as-a-uri"></a>Festlegen eines Schlüssels als URI

Gehen Sie wie folgt vor, um einen Schlüssel als URI anzugeben:

1. Um den Schlüssel-URI im Azure-Portal anzuzeigen, navigieren Sie zu Ihrem Schlüsseltresor, und wählen die Einstellung **Schlüssel** aus. Wählen Sie den gewünschten Schlüssel aus, und klicken Sie darauf, um dessen Versionen anzuzeigen. Wählen Sie eine Schlüsselversion aus, um die Einstellungen für diese Version anzuzeigen.
1. Kopieren Sie den Wert im Feld **Schlüsselbezeichner**, das den URI enthält.

    ![Screenshot mit Schlüssel-URI des Schlüsseltresors](../media/cognitive-services-encryption/key-uri-portal.png)

1. Wählen Sie in den Einstellungen unter **Verschlüsselung** für Ihr Speicherkonto die Option **Schlüssel-URI eingeben** aus.
1. Fügen Sie den kopierten URI in das Feld **Schlüssel-URI** ein.

   ![Screenshot zur Eingabe des Schlüssel-URI](../media/cognitive-services-encryption/ssecmk2.png)

1. Geben Sie das Abonnement an, das den Schlüsseltresor enthält.
1. Speichern Sie die Änderungen.

### <a name="specify-a-key-from-a-key-vault"></a>Festlegen eines Schlüssels aus einem Schlüsseltresor

Um einen Schlüssel aus einem Schlüsseltresor anzugeben, müssen Sie zunächst sicherstellen, dass Sie über einen Schlüsseltresor mit einem Schlüssel verfügen. Gehen Sie wie folgt vor, um einen Schlüssel aus einem Schlüsseltresor anzugeben:

1. Wählen Sie die Option **Select from Key Vault** (Aus Schlüsseltresor wählen).
1. Wählen Sie den Schlüsseltresor mit dem Schlüssel aus, den Sie verwenden möchten.
1. Wählen Sie den Schlüssel aus dem Schlüsseltresor aus.

   ![Screenshot mit Option für vom Kunden verwaltete Schlüssel](../media/cognitive-services-encryption/ssecmk3.png)

1. Speichern Sie die Änderungen.

## <a name="update-the-key-version"></a>Aktualisieren der Schlüsselversion

Wenn Sie eine neue Version eines Schlüssels erstellen, aktualisieren Sie die Cognitive Services-Ressource, damit sie die neue Version verwendet. Folgen Sie diesen Schritten:

1. Navigieren Sie zu Ihrer Cognitive Services-Ressource, und zeigen Sie die Einstellungen für die **Verschlüsselung** an.
1. Geben Sie den URI für die neue Schlüsselversion ein. Alternativ können Sie den Schlüsseltresor und den Schlüssel erneut auswählen, um die Version zu aktualisieren.
1. Speichern Sie die Änderungen.

## <a name="use-a-different-key"></a>Verwenden eines anderen Schlüssels

Gehen Sie wie folgt vor, um den für die Verschlüsselung verwendeten Schlüssel zu ändern:

1. Navigieren Sie zu Ihrer Cognitive Services-Ressource, und zeigen Sie die Einstellungen für die **Verschlüsselung** an.
1. Geben Sie den URI für den neuen Schlüssel ein. Alternativ können Sie den Schlüsseltresor und dann einen neuen Schlüssel auswählen.
1. Speichern Sie die Änderungen.

## <a name="rotate-customer-managed-keys"></a>Rotieren von kundenseitig verwalteten Schlüsseln

Sie können einen vom Kunden verwalteten Schlüssel in Azure Key Vault entsprechend Ihren Konformitätsrichtlinien rotieren. Wenn der Schlüssel rotiert wird, müssen Sie die Cognitive Services-Ressource aktualisieren, sodass der neue Schlüssel-URI verwendet wird. Informationen zum Aktualisieren der Ressource für die Verwendung einer neuen Version des Schlüssels im Azure-Portal finden Sie unter [Aktualisieren der Schlüsselversion](#update-the-key-version).

Durch Rotieren des Schlüssels werden die Daten in der Ressource nicht erneut verschlüsselt. Es ist keine weitere Aktion durch den Benutzer erforderlich.

## <a name="revoke-access-to-customer-managed-keys"></a>Widerrufen des Zugriffs auf von Kunden verwaltete Schlüssel

Zum Widerrufen des Zugriffs auf von Kunden verwaltete Schlüssel können Sie PowerShell oder die Azure-Befehlszeilenschnittstelle verwenden. Weitere Informationen finden Sie unter [Azure Key Vault – PowerShell](/powershell/module/az.keyvault//) oder [Azure Key Vault – CLI](/cli/azure/keyvault). Durch das Widerrufen des Zugriffs wird der Zugriff auf alle Daten in der Cognitive Services-Ressource blockiert, da Cognitive Services keinen Zugriff mehr auf den Verschlüsselungsschlüssel hat.

## <a name="disable-customer-managed-keys"></a>Deaktivieren von vom Kunden verwalteten Schlüsseln

Wenn Sie „Von Kunden verwaltete Schlüssel“ deaktivieren, wird Ihre Cognitive Services-Ressource mit von Microsoft verwalteten Schlüsseln verschlüsselt. Führen Sie die folgenden Schritte aus, um vom Kunden verwaltete Schlüssel zu deaktivieren:

1. Navigieren Sie zu Ihrer Cognitive Services-Ressource, und zeigen Sie die Einstellungen für die **Verschlüsselung** an.
1. Deaktivieren Sie das Kontrollkästchen neben der Einstellung **Eigenen Schlüssel verwenden**.