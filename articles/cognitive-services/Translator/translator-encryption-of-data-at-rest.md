---
title: Übersetzerverschlüsselung für ruhende Daten
titleSuffix: Azure Cognitive Services
description: Microsoft bietet Ihnen die Möglichkeit, Ihre Abonnements für Cognitive Services mit Ihren eigenen Schlüsseln, so genannten kundenseitig verwalteten Schlüsseln (Customer-Managed-Keys, CMK), zu verwalten. In diesem Artikel erfahren Sie mehr über die Datenverschlüsselung im Ruhezustand für den Übersetzer und wie Sie CMK aktivieren und verwalten können.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: ff7b9f86cebc3c2479105d2a52aa92a265f8a1b3
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95016512"
---
# <a name="translator-encryption-of-data-at-rest"></a>Übersetzerverschlüsselung für ruhende Daten

Übersetzer verschlüsselt automatisch Daten, die Sie hochladen, um benutzerdefinierte Übersetzungsmodelle zu erstellen, wenn sie in der Cloud persistent gespeichert werden, und unterstützt Sie so bei der Einhaltung der Sicherheits- und Complianceziele Ihrer Organisation.

## <a name="about-cognitive-services-encryption"></a>Informationen zur Cognitive Services-Verschlüsselung

Daten werden mittels [FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2)-konformer [256-Bit-AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)-Verschlüsselung ver- und entschlüsselt. Verschlüsselung und Entschlüsselung sind transparent, was bedeutet, dass die Verschlüsselung und der Zugriff für Sie verwaltet werden. Ihre Daten werden standardmäßig geschützt, und Sie müssen weder Code noch Anwendungen ändern, um die Verschlüsselung nutzen zu können.

## <a name="about-encryption-key-management"></a>Informationen zur Verwaltung von Verschlüsselungsschlüsseln

Standardmäßig verwendet Ihr Abonnement von Microsoft verwaltete Verschlüsselungsschlüssel. Wenn Sie einen Tarif verwenden, der kundenseitig verwaltete Schlüssel unterstützt, können Sie die Verschlüsselungseinstellungen für Ihre Ressource im Abschnitt **Verschlüsselung** des [Azure-Portals](https://portal.azure.com) sehen, wie in der folgenden Abbildung dargestellt.

![Anzeigen der Verschlüsselungseinstellungen](../media/cognitive-services-encryption/encryptionblade.png)

Für Abonnements, die nur von Microsoft verwaltete Verschlüsselungsschlüssel unterstützen, ist kein **Verschlüsselungsabschnitt** verfügbar.

## <a name="customer-managed-keys-with-azure-key-vault"></a>Von Kunden verwaltete Schlüssel mit Azure Key Vault

Standardmäßig verwendet Ihr Abonnement von Microsoft verwaltete Verschlüsselungsschlüssel. Es gibt auch die Option zum Verwalten Ihres Abonnements mit Ihren eigenen Schlüsseln (kundenseitig verwaltete Schlüssel, CMK). Kundenseitig verwaltete Schlüssel (CMK) bieten größere Flexibilität beim Erstellen, Rotieren, Deaktivieren und Widerrufen von Zugriffssteuerungen. Außerdem können Sie die zum Schutz Ihrer Daten verwendeten Verschlüsselungsschlüssel überwachen. Wenn für Ihr Abonnement CMK konfiguriert ist, wird darüber hinaus die doppelte Verschlüsselung bereitgestellt, mit der eine zweite Schutzebene zur Verfügung steht, während Sie den Verschlüsselungsschlüssel über Ihren Azure Key Vault kontrollieren können.

> [!IMPORTANT]
> Kundenseitig verwaltete Schlüssel sind für alle Tarife für den Übersetzerdienst verfügbar. Wenn Sie die Möglichkeit haben möchten, von Kunden verwaltete Schlüssel zu verwenden, füllen Sie das [Formular zum Anfordern von kundenseitig verwalteten Schlüsseln für den Übersetzerdienst](https://aka.ms/cogsvc-cmk) aus, und reichen Sie es ein. Nach ca. 3–5 Werktagen erhalten Sie eine Rückmeldung zum Status Ihrer Anforderung. Je nach Bedarf können Sie in einer Warteschlange platziert und genehmigt werden, sobald Platz verfügbar ist. Nachdem Ihre Verwendung von CMK mit dem Übersetzerdienst genehmigt wurde, müssen Sie eine neue Übersetzerressource erstellen. Nachdem die Übersetzerressource erstellt wurde, können Sie mit Azure Key Vault Ihre verwaltete Identität einrichten.

Führen Sie die folgenden Schritte aus, um kundenseitig verwaltete Schlüssel für Übersetzer zu aktivieren:

1. Erstellen Sie Ihre neue regionale Translator- oder Cognitive Services-Ressource. Dies funktioniert nicht mit einer globalen Ressource.
2. Aktivieren Sie die verwaltete Identität im Azure-Portal, und fügen Sie Ihre Informationen zum vom Kunden verwalteten Schlüssel hinzu.
3. Erstellen Sie einen neuen Arbeitsbereich im benutzerdefinierten Translator, und ordnen Sie diese Abonnementinformationen zu.

### <a name="enable-customer-managed-keys"></a>Aktivieren von vom Kunden verwalteten Schlüsseln

Sie müssen Azure Key Vault zum Speichern Ihrer vom Kunden verwalteten Schlüssel verwenden. Sie können entweder Ihre eigenen Schlüssel erstellen und in einem Schlüsseltresor speichern oder mit den Azure Key Vault-APIs Schlüssel generieren. Die Cognitive Services-Ressource und der Schlüsseltresor müssen sich in derselben Region und in demselben Azure Active Directory-Mandanten (Azure AD) befinden, aber sie können sich in verschiedenen Abonnements befinden. Weitere Informationen zum Azure Key Vault finden Sie unter [What is Azure Key Vault? (Was ist der Azure Key Vault?)](../../key-vault/general/overview.md).

Eine neue Cognitive Services-Ressource wird immer mit von Microsoft verwalteten Schlüsseln verschlüsselt. Es ist nicht möglich, zum Zeitpunkt der Ressourcenerstellung kundenseitig verwaltete Schlüssel zu aktivieren. Kundenseitig verwaltete Schlüssel werden in Azure Key Vault gespeichert. Für den Schlüsseltresor müssen Zugriffsrichtlinien bereitgestellt werden, mit denen Schlüsselberechtigungen für die verwaltete Identität erteilt werden, die der Cognitive Services-Ressource zugeordnet ist. Die verwaltete Identität ist verfügbar, sobald die Ressource erstellt wurde.

Informationen zum Verwenden von kundenseitig verwalteten Schlüsseln mit Azure Key Vault für die Cognitive Services-Verschlüsselung finden Sie unter:

- [Konfigurieren von kundenseitig verwalteten Schlüsseln mit Key Vault für die Cognitive Services-Verschlüsselung über das Azure-Portal](../Encryption/cognitive-services-encryption-keys-portal.md)

Durch das Aktivieren von kundenseitig verwalteten Schlüsseln wird auch eine systemseitig zugewiesene verwaltete Identität aktiviert. Dies ist eine Funktion von Azure AD. Sobald die systemseitig zugewiesene verwaltete Identität aktiviert ist, wird diese Ressource bei Azure Active Directory registriert. Nach der Registrierung erhält die verwaltete Identität Zugriff auf die Key Vault-Instanz, die bei der Einrichtung des kundenseitig verwalteten Schlüssels ausgewählt wurde. Sie können sich weiter über [verwaltete Identitäten](../../active-directory/managed-identities-azure-resources/overview.md) informieren.

> [!IMPORTANT]
> Wenn Sie systemseitig zugewiesene verwaltete Identitäten deaktivieren, wird der Zugriff auf den Schlüsseltresor entfernt, und alle mit den Kundenschlüsseln verschlüsselten Daten sind nicht mehr zugänglich. Alle Features, die von diesen Daten abhängen, funktionieren nicht mehr. Außerdem wird die Bereitstellung aller Modelle aufgehoben, die Sie bereitgestellt haben. Alle hochgeladenen Daten werden aus dem benutzerdefinierten Translator gelöscht. Wenn die verwalteten Identitäten erneut aktiviert werden, stellen wir das Modell nicht automatisch erneut für Sie bereit.

> [!IMPORTANT]
> Verwaltete Identitäten unterstützen derzeit keine verzeichnisübergreifenden Szenarien. Wenn Sie vom Kunden verwaltete Schlüssel im Azure-Portal konfigurieren, wird automatisch eine verwaltete Identität im Hintergrund zugewiesen. Wenn Sie anschließend das Abonnement, die Ressourcengruppe oder die Ressource von einem Azure AD-Verzeichnis in ein anderes Verzeichnis verschieben, wird die der Ressource zugeordnete verwaltete Identität nicht an den neuen Mandanten übertragen, sodass kundenseitig verwaltete Schlüssel möglicherweise nicht mehr funktionieren. Weitere Informationen finden Sie in [Häufig gestellte Fragen und bekannte Probleme mit verwalteten Identitäten für Azure-Ressourcen](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories) unter **Übertragen eines Abonnements zwischen Azure AD-Verzeichnissen**.  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Speichern von kundenseitig verwalteten Schlüsseln in Azure Key Vault

Wenn Sie kundenseitig verwaltete Schlüssel aktivieren möchten, müssen Sie Ihre Schlüssel in einer Azure Key Vault-Instanz speichern. Sie müssen die Eigenschaften **Vorläufiges Löschen** und **Do Not Purge** (Nicht bereinigen) im Schlüsseltresor aktivieren.

Für die Cognitive Services-Verschlüsselung werden nur RSA-Schlüssel der Größe 2048 unterstützt. Weitere Informationen zu Schlüsseln finden Sie unter **Key Vault-Schlüssel** in [Informationen zu Schlüsseln, Geheimnissen und Zertifikaten in Azure Key Vault](../../key-vault/general/about-keys-secrets-certificates.md).

> [!NOTE]
> Wenn der gesamte Schlüsseltresor gelöscht wird, werden Ihre Daten nicht mehr angezeigt, und die Bereitstellung aller ihrer Modelle wird aufgehoben. Alle hochgeladenen Daten werden aus dem benutzerdefinierten Translator gelöscht. 

### <a name="revoke-access-to-customer-managed-keys"></a>Widerrufen des Zugriffs auf von Kunden verwaltete Schlüssel

Zum Widerrufen des Zugriffs auf von Kunden verwaltete Schlüssel können Sie PowerShell oder die Azure-Befehlszeilenschnittstelle verwenden. Weitere Informationen finden Sie unter [Azure Key Vault – PowerShell](/powershell/module/az.keyvault//) oder [Azure Key Vault – CLI](/cli/azure/keyvault). Durch das Widerrufen des Zugriffs wird der Zugriff auf alle Daten in der Cognitive Services-Ressource blockiert und die Bereitstellung Ihrer Modelle wird aufgehoben, da Cognitive Services keinen Zugriff mehr auf den Verschlüsselungsschlüssel hat. Alle hochgeladenen Daten werden auch aus dem benutzerdefinierten Translator gelöscht.


## <a name="next-steps"></a>Nächste Schritte

* [Weitere Informationen zu Azure Key Vault](../../key-vault/general/overview.md)
