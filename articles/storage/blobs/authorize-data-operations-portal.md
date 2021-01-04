---
title: Auswählen der Autorisierung des Zugriffs auf Blobdaten im Azure-Portal
titleSuffix: Azure Storage
description: Wenn Sie über das Azure-Portal auf Blobdaten zugreifen, richtet das Portal tatsächlich Anforderungen an Azure Storage. Diese Anforderungen an Azure Storage können entweder mit Ihrem Azure AD-Konto oder Zugriffsschlüssel für das Speicherkonto authentifiziert und autorisiert werden.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 09/08/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: blobs
ms.custom: contperf-fy21q1
ms.openlocfilehash: 8c963f11a34217253f02cb5d116d66cdbf8bcc19
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97033956"
---
# <a name="choose-how-to-authorize-access-to-blob-data-in-the-azure-portal"></a>Auswählen der Autorisierung des Zugriffs auf Blobdaten im Azure-Portal

Wenn Sie über das [Azure-Portal](https://portal.azure.com) auf Blobdaten zugreifen, richtet das Portal tatsächlich Anforderungen an Azure Storage. Eine Anforderung an Azure Storage kann entweder mit Ihrem Azure AD-Konto oder Zugriffsschlüssel für das Speicherkonto autorisiert werden. Das Portal zeigt an, welche Methode Sie verwenden, und ermöglicht Ihnen, zwischen den beiden zu wechseln, sofern Sie die entsprechenden Berechtigungen haben.  

Sie können auch angeben, wie ein einzelner Blob-Uploadvorgang im Azure-Portal autorisiert werden soll. Das Portal verwendet standardmäßig die Methode, die Sie zum Autorisieren von Blob-Uploadvorgängen verwenden. Sie haben jedoch die Möglichkeit, diese Einstellung beim Hochladen eines Blobs zu ändern.

## <a name="permissions-needed-to-access-blob-data"></a>Für den Zugriff auf Blobdaten benötigte Berechtigungen

Je nachdem, wie Sie den Zugriff auf Blobdaten im Azure-Portal autorisieren möchten, benötigen Sie spezielle Berechtigungen. Meist werden diese Berechtigungen über die rollenbasierte Zugriffssteuerung (Azure RBAC) bereitgestellt. Weitere Informationen zu Azure RBAC finden Sie unter [Was ist die rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC)?](../../role-based-access-control/overview.md).

### <a name="use-the-account-access-key"></a>Verwenden des Kontozugriffsschlüssels

Für den Zugriff auf Blobdaten mit dem Kontozugriffsschlüssel muss Ihnen eine Azure-Rolle zugewiesen sein, die die Azure RBAC-Aktion **Microsoft.Storage/storageAccounts/listkeys/action** einschließt. Bei dieser Azure-Rolle kann es sich um eine integrierte oder benutzerdefinierte Rolle handeln. Es folgen integrierte Rollen mit Unterstützung für **Microsoft.Storage/storageAccounts/listkeys/action**:

- Die Azure Resource Manager-Rolle [Besitzer](../../role-based-access-control/built-in-roles.md#owner)
- Die Azure Resource Manager-Rolle [Mitwirkender](../../role-based-access-control/built-in-roles.md#contributor)
- Die Rolle [Speicherkontomitwirkender](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

Wenn Sie versuchen, im Azure-Portal auf Blobdaten zuzugreifen, prüft das Portal zunächst, ob Ihnen eine Rolle mit **Microsoft.Storage/storageAccounts/listkeys/action** zugewiesen ist. Wenn Ihnen eine Rolle mit dieser Aktion zugewiesen wurde, verwendet das Portal den Kontoschlüssel für den Zugriff auf Blobdaten. Wenn Ihnen keine Rolle mit dieser Aktion zugewiesen wurde, versucht das Portal, über Ihr Azure AD-Konto auf Daten zuzugreifen.

> [!NOTE]
> Die zu „Administrator für klassisches Abonnement“ gehörigen Rollen „Dienstadministrator“ und „Co-Administrator“ schließen die Entsprechung der Azure Resource Manager-Rolle [Besitzer](../../role-based-access-control/built-in-roles.md#owner) ein. Die Rolle **Besitzer** schließt alle Aktionen einschließlich **Microsoft.Storage/storageAccounts/listkeys/action** ein, sodass ein Benutzer mit einer dieser Administratorrollen auch mit dem Kontoschlüssel auf Blobdaten zugreifen kann. Weitere Informationen finden Sie unter [Administratorrollen für klassische Abonnements, Azure-Rollen und Azure AD-Rollen](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles).

### <a name="use-your-azure-ad-account"></a>Verwenden des Azure AD-Kontos

Für den Zugriff auf Blobdaten im Azure-Portal über Ihr Azure AD-Konto müssen Sie die beiden folgenden Voraussetzungen erfüllen:

- Ihnen wurde die Azure Resource Manager-Rolle [Leser](../../role-based-access-control/built-in-roles.md#reader) mindestens bis zur Ebene des Speicherkontos oder höher zugewiesen. Die Rolle **Leser** erteilt die am stärksten eingeschränkten Berechtigungen. Eine andere Azure Resource Manager-Rolle, die den Zugriff auf Ressourcen zur Verwaltung von Speicherkonten gewährt, ist jedoch ebenfalls akzeptabel.
- Ihnen wurde entweder eine integrierte oder benutzerdefinierte Rolle zugewiesen, die den Zugriff auf Blobdaten ermöglicht.

Die Zuweisung der Rolle **Leser** oder einer anderen Azure Resource Manager-Rollen ist notwendig, damit der Benutzer die Ressourcen der Speicherkontenverwaltung im Azure-Portal anzeigen und nutzen kann. Die Azure-Rollen, die Zugriff auf Blobdaten gewähren, lassen keinen Zugriff auf Ressourcen zur Verwaltung von Speicherkonten zu. Um im Portal auf Blobdaten zugreifen zu können, benötigt der Benutzer Berechtigungen zum Navigieren durch die Ressourcen des Speicherkontos. Weitere Informationen zu dieser Anforderung finden Sie unter [Zuweisen der Rolle „Leser“ für den Zugriff auf das Portal](../common/storage-auth-aad-rbac-portal.md#assign-the-reader-role-for-portal-access).

Im Folgenden finden Sie die integrierten Rollen, die den Zugriff auf Ihre Blobdaten unterstützen:

- [Besitzer von Speicherblobdaten](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner): Dient zur Steuerung des POSIX-Zugriffs für Azure Data Lake Storage Gen2.
- [Mitwirkender an Speicherblobdaten](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor): Weist Lese-, Schreib- und Löschberechtigungen für Blobs zu.
- [Leser von Speicherblobdaten](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader): Weist ausschließlich Leseberechtigungen für Blobs zu.

Benutzerdefinierte Rollen können verschiedene Kombinationen der von den integrierten Rollen gebotenen Berechtigungen unterstützen. Weitere Informationen zum Erstellen benutzerdefinierter Azure-Rollen finden Sie unter [Benutzerdefinierte Azure-Rollen](../../role-based-access-control/custom-roles.md) und [Grundlegendes zu Rollendefinitionen für Azure-Ressourcen](../../role-based-access-control/role-definitions.md).

> [!IMPORTANT]
> Die Vorschauversion von Azure Storage-Explorer im Azure-Portal unterstützt nicht die Verwendung von Azure AD-Anmeldeinformationen zum Anzeigen und Ändern von Blobdaten. Storage-Explorer im Azure-Portal verwendet immer die Kontoschlüssel für den Zugriff auf Daten. Um Storage-Explorer im Azure-Portal verwenden zu können, muss Ihnen eine Rolle zugewiesen werden, in der **Microsoft.Storage/storageAccounts/listkeys/action** enthalten ist.

## <a name="navigate-to-blobs-in-the-azure-portal"></a>Navigieren zu Blobs im Azure-Portal

Um Blobdaten im Portal anzuzeigen, navigieren Sie zur **Übersicht** Ihres Speicherkontos und klicken auf die Links zu **Blobs**. Alternativ können Sie im Menü zum Abschnitt **Blob-Dienst** navigieren.

:::image type="content" source="media/anonymous-read-access-configure/blob-public-access-portal.png" alt-text="Screenshot der Navigation zu Blobdaten im Azure-Portal":::

## <a name="determine-the-current-authentication-method"></a>Bestimmen der aktuellen Authentifizierungsmethode

Wenn Sie zu einem Container navigieren, zeigt das Azure-Portal an, ob Sie zur Authentifizierung derzeit den Kontozugriffsschlüssel oder Ihr Azure AD-Konto verwenden.

### <a name="authenticate-with-the-account-access-key"></a>Authentifizierung mit dem Kontozugriffsschlüssel

Wenn Sie sich mit dem Kontozugriffsschlüssel authentifizieren, wird im Portal **Zugriffsschlüssel** als Authentifizierungsmethode angegeben:

:::image type="content" source="media/authorize-data-operations-portal/auth-method-access-key.png" alt-text="Screenshot eines Benutzers, der gegenwärtig mit dem Kontoschlüssel auf Container zugreift":::

Um zur Verwendung des Azure AD-Kontos zu wechseln, klicken Sie auf den in der Abbildung hervorgehobenen Link. Wenn Sie über die Ihnen zugewiesenen Azure-Rollen die entsprechenden Berechtigungen haben, können Sie fortfahren. Wenn Ihnen jedoch die benötigten Berechtigungen fehlen, erhalten Sie eine Fehlermeldung wie die folgende:

:::image type="content" source="media/authorize-data-operations-portal/auth-error-azure-ad.png" alt-text="Angezeigter Fehler, wenn das Azure AD-Konto den Zugriff nicht unterstützt":::

Beachten Sie, dass in der Liste keine Blobs enthalten sind, wenn Ihrem Azure AD-Konto die Berechtigung zu ihrer Anzeige fehlt. Klicken Sie auf den Link **Zum Zugriffsschlüssel wechseln**, um erneut den Zugriffsschlüssel für die Authentifizierung zu nutzen.

### <a name="authenticate-with-your-azure-ad-account"></a>Authentifizierung mit dem Azure AD-Konto

Wenn Sie sich mit Ihrem Azure AD-Konto authentifizieren, wird im Portal **Azure AD-Benutzerkonto** als Authentifizierungsmethode angegeben:

:::image type="content" source="media/authorize-data-operations-portal/auth-method-azure-ad.png" alt-text="Screenshot eines Benutzers, der derzeit mit einem Azure AD Konto auf Container zugreift":::

Um zur Verwendung des Zugriffsschlüssels zu wechseln, klicken Sie auf den in der Abbildung hervorgehobenen Link. Wenn Sie Zugriff auf den Kontoschlüssel haben, können Sie fortfahren. Wenn Sie keinen Zugriff auf den Kontoschlüssel haben, erhalten Sie eine Fehlermeldung wie die folgende:

:::image type="content" source="media/authorize-data-operations-portal/auth-error-access-key.png" alt-text="Angezeigter Fehler, wenn Sie keinen Zugriff auf den Kontoschlüssel haben":::

Beachten Sie, dass in der Liste keine Blobs enthalten sind, wenn Sie keinen Zugriff auf die Kontoschlüssel haben. Klicken Sie auf den Link **Zum Azure AD-Benutzerkonto wechseln**, um erneut das Azure AD-Konto für die Authentifizierung zu nutzen.

## <a name="specify-how-to-authorize-a-blob-upload-operation"></a>Angeben, wie ein Blob-Uploadvorgang autorisiert werden soll

Wenn Sie ein Blob aus dem Azure-Portal hochladen, können Sie angeben, ob Sie diesen Vorgang mit dem Kontozugriffsschlüssel oder mit Ihren Azure AD-Anmeldeinformationen authentifizieren und autorisieren möchten. Das Portal verwendet wie unter [Bestimmen der aktuellen Authentifizierungsmethode](#determine-the-current-authentication-method) beschrieben standardmäßig die aktuelle Authentifizierungsmethode.

So geben Sie an, wie ein Blob-Uploadvorgang autorisiert werden soll

1. Navigieren Sie im Azure-Portal zu dem Container, in den Sie ein Blob hochladen möchten.
1. Wählen Sie die Schaltfläche **Hochladen**.
1. Erweitern Sie den Abschnitt **Erweitert**, um die erweiterten Eigenschaften für das Blob anzuzeigen.
1. Geben Sie im Feld **Authentifizierungstyp** an, ob Sie den Uploadvorgang mithilfe Ihres Azure AD-Kontos oder mit dem Kontozugriffsschlüssel autorisieren möchten, wie in der folgenden Abbildung dargestellt:

    :::image type="content" source="media/authorize-data-operations-portal/auth-blob-upload.png" alt-text="Screenshot: Ändern der Autorisierungsmethode beim Hochladen von Blobs":::

## <a name="next-steps"></a>Nächste Schritte

- [Authentifizieren des Zugriffs auf Azure-Blobs und -Warteschlangen mit Azure Active Directory](../common/storage-auth-aad.md)
- [Zuweisen einer Azure-Rolle für den Zugriff auf Blob- und Warteschlangendaten über das Azure-Portal](../common/storage-auth-aad-rbac-portal.md)
- [Verwenden der Azure-Befehlszeilenschnittstelle zum Zuweisen einer Azure-Rolle für den Zugriff auf Blob- und Warteschlangendaten](../common/storage-auth-aad-rbac-cli.md)
- [Verwenden des Azure PowerShell-Moduls zum Zuweisen einer Azure-Rolle für den Zugriff auf Blob- und Warteschlangendaten](../common/storage-auth-aad-rbac-powershell.md)
