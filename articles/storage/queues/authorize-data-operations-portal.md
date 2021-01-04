---
title: Auswählen der Autorisierung des Zugriffs auf Warteschlangendaten im Azure-Portal
titleSuffix: Azure Storage
description: Wenn Sie über das Azure-Portal auf Warteschlangendaten zugreifen, werden vom Portal im Hintergrund Anforderungen an Azure Storage gesendet. Diese Anforderungen an Azure Storage können entweder mit Ihrem Azure AD-Konto oder Zugriffsschlüssel für das Speicherkonto authentifiziert und autorisiert werden.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 09/08/2020
ms.author: tamram
ms.reviewer: ozguns
ms.subservice: queues
ms.custom: contperf-fy21q1
ms.openlocfilehash: 68ac9cd5e89617a820cba9a1d6c61890e50a56a7
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97031741"
---
# <a name="choose-how-to-authorize-access-to-queue-data-in-the-azure-portal"></a>Auswählen der Autorisierung des Zugriffs auf Warteschlangendaten im Azure-Portal

Wenn Sie über das [Azure-Portal](https://portal.azure.com) auf Warteschlangendaten zugreifen, werden vom Portal im Hintergrund Anforderungen an Azure Storage gesendet. Eine Anforderung an Azure Storage kann entweder mit Ihrem Azure AD-Konto oder Zugriffsschlüssel für das Speicherkonto autorisiert werden. Das Portal zeigt an, welche Methode Sie verwenden, und ermöglicht Ihnen, zwischen den beiden zu wechseln, sofern Sie die entsprechenden Berechtigungen haben.

## <a name="permissions-needed-to-access-queue-data"></a>Für den Zugriff auf Warteschlangendaten benötigte Berechtigungen

Je nachdem, wie Sie den Zugriff Warteschlangendaten im Azure-Portal autorisieren möchten, benötigen Sie spezielle Berechtigungen. Meist werden diese Berechtigungen über die rollenbasierte Zugriffssteuerung (Azure RBAC) bereitgestellt. Weitere Informationen zu Azure RBAC finden Sie unter [Was ist die rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC)?](../../role-based-access-control/overview.md).

### <a name="use-the-account-access-key"></a>Verwenden des Kontozugriffsschlüssels

Für den Zugriff auf Warteschlangendaten mit dem Kontozugriffsschlüssel muss Ihnen eine Azure-Rolle zugewiesen sein, die die Azure RBAC-Aktion **Microsoft.Storage/storageAccounts/listkeys/action** umfasst. Bei dieser Azure-Rolle kann es sich um eine integrierte oder benutzerdefinierte Rolle handeln. Es folgen integrierte Rollen mit Unterstützung für **Microsoft.Storage/storageAccounts/listkeys/action**:

- Die Azure Resource Manager-Rolle [Besitzer](../../role-based-access-control/built-in-roles.md#owner)
- Die Azure Resource Manager-Rolle [Mitwirkender](../../role-based-access-control/built-in-roles.md#contributor)
- Die Rolle [Speicherkontomitwirkender](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

Wenn Sie versuchen, im Azure-Portal auf Warteschlangendaten zuzugreifen, wird vom Portal zunächst überprüft, ob Ihnen eine Rolle mit **Microsoft.Storage/storageAccounts/listkeys/action** zugewiesen ist. Wenn Ihnen eine Rolle mit dieser Aktion zugewiesen wurde, wird im Portal der Kontoschlüssel für den Zugriff auf Warteschlangendaten verwendet. Wenn Ihnen keine Rolle mit dieser Aktion zugewiesen wurde, versucht das Portal, über Ihr Azure AD-Konto auf Daten zuzugreifen.

> [!NOTE]
> Die zu „Administrator für klassisches Abonnement“ gehörigen Rollen „Dienstadministrator“ und „Co-Administrator“ schließen die Entsprechung der Azure Resource Manager-Rolle [Besitzer](../../role-based-access-control/built-in-roles.md#owner) ein. Die Rolle **Besitzer** schließt alle Aktionen einschließlich **Microsoft.Storage/storageAccounts/listkeys/action** ein, sodass ein Benutzer mit einer dieser Administratorrollen auch mit dem Kontoschlüssel auf Warteschlangendaten zugreifen kann. Weitere Informationen finden Sie unter [Administratorrollen für klassische Abonnements, Azure-Rollen und Azure AD-Rollen](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles).

### <a name="use-your-azure-ad-account"></a>Verwenden des Azure AD-Kontos

Für den Zugriff auf Warteschlangendaten im Azure-Portal über Ihr Azure AD-Konto müssen Sie die beiden folgenden Voraussetzungen erfüllen:

- Ihnen wurde die Azure Resource Manager-Rolle [Leser](../../role-based-access-control/built-in-roles.md#reader) mindestens bis zur Ebene des Speicherkontos oder höher zugewiesen. Die Rolle **Leser** erteilt die am stärksten eingeschränkten Berechtigungen. Eine andere Azure Resource Manager-Rolle, die den Zugriff auf Ressourcen zur Verwaltung von Speicherkonten gewährt, ist jedoch ebenfalls akzeptabel.
- Ihnen wurde entweder eine integrierte oder benutzerdefinierte Rolle zugewiesen, die den Zugriff auf Warteschlangendaten ermöglicht.

Die Zuweisung der Rolle **Leser** oder einer anderen Azure Resource Manager-Rollen ist notwendig, damit der Benutzer die Ressourcen der Speicherkontenverwaltung im Azure-Portal anzeigen und nutzen kann. Die Azure-Rollen, die Zugriff auf Warteschlangendaten gewähren, ermöglichen keinen Zugriff auf Ressourcen zur Verwaltung von Speicherkonten. Um im Portal auf Warteschlangendaten zugreifen zu können, benötigt der Benutzer Berechtigungen zum Navigieren durch die Ressourcen des Speicherkontos. Weitere Informationen zu dieser Anforderung finden Sie unter [Zuweisen der Rolle „Leser“ für den Zugriff auf das Portal](../common/storage-auth-aad-rbac-portal.md#assign-the-reader-role-for-portal-access).

Im Folgenden die integrierten Rollen, die den Zugriff auf Ihre Warteschlangendaten unterstützen:

- [Mitwirkender an Storage-Warteschlangendaten](../../role-based-access-control/built-in-roles.md#storage-queue-data-contributor): Weist Lese-, Schreib- und Löschberechtigungen für Warteschlangen zu.
- [Storage-Warteschlangendatenleser](../../role-based-access-control/built-in-roles.md#storage-queue-data-reader): Weist ausschließlich Leseberechtigungen für Warteschlangen zu.

Benutzerdefinierte Rollen können verschiedene Kombinationen der von den integrierten Rollen gebotenen Berechtigungen unterstützen. Weitere Informationen zum Erstellen benutzerdefinierter Azure-Rollen finden Sie unter [Benutzerdefinierte Azure-Rollen](../../role-based-access-control/custom-roles.md) und [Grundlegendes zu Rollendefinitionen für Azure-Ressourcen](../../role-based-access-control/role-definitions.md).

Das Auflisten von Warteschlangen wird von der Rolle „Administrator für klassisches Abonnement“ nicht unterstützt. Um Warteschlangen auflisten zu können, muss einem Benutzer die Azure Resource Manager-Rolle **Leser**, die Rolle **Storage-Warteschlangendatenleser** oder die Rolle **Mitwirkender an Storage-Warteschlangendaten** zugewiesen sein.

> [!IMPORTANT]
> Die Vorschauversion des Storage-Explorers im Azure-Portal unterstützt nicht die Verwendung von Azure AD-Anmeldeinformationen zum Anzeigen und Ändern von Warteschlangendaten. Storage-Explorer im Azure-Portal verwendet immer die Kontoschlüssel für den Zugriff auf Daten. Um Storage-Explorer im Azure-Portal verwenden zu können, muss Ihnen eine Rolle zugewiesen werden, in der **Microsoft.Storage/storageAccounts/listkeys/action** enthalten ist.

## <a name="navigate-to-queues-in-the-azure-portal"></a>Navigieren zu Warteschlangen im Azure-Portal

Um Warteschlangendaten im Portal anzuzeigen, navigieren Sie zur **Übersicht** Ihres Speicherkontos und klicken auf die Links für **Warteschlangen**. Alternativ können Sie im Menü zu den Abschnitten **Warteschlangendienst** navigieren.

:::image type="content" source="media/authorize-data-operations-portal/queue-access-portal.png" alt-text="Screenshot, der zeigt, wie Sie im Azure-Portal zu Warteschlangendaten navigieren":::

## <a name="determine-the-current-authentication-method"></a>Bestimmen der aktuellen Authentifizierungsmethode

Wenn Sie zu einer Warteschlange navigieren, wird im Azure-Portal angezeigt, ob Sie zur Authentifizierung derzeit den Kontozugriffsschlüssel oder Ihr Azure AD-Konto verwenden.

### <a name="authenticate-with-the-account-access-key"></a>Authentifizierung mit dem Kontozugriffsschlüssel

Wenn Sie sich mit dem Kontozugriffsschlüssel authentifizieren, wird im Portal **Zugriffsschlüssel** als Authentifizierungsmethode angegeben:

:::image type="content" source="media/authorize-data-operations-portal/auth-method-access-key.png" alt-text="Screenshot eines Benutzers, der gegenwärtig mit dem Kontoschlüssel auf Warteschlangen zugreift":::

Um zur Verwendung des Azure AD-Kontos zu wechseln, klicken Sie auf den in der Abbildung hervorgehobenen Link. Wenn Sie über die Ihnen zugewiesenen Azure-Rollen die entsprechenden Berechtigungen haben, können Sie fortfahren. Wenn Ihnen jedoch die benötigten Berechtigungen fehlen, erhalten Sie eine Fehlermeldung wie die folgende:

:::image type="content" source="media/authorize-data-operations-portal/auth-error-azure-ad.png" alt-text="Angezeigter Fehler, wenn das Azure AD-Konto den Zugriff nicht unterstützt":::

Beachten Sie, dass in der Liste keine Warteschlangen enthalten sind, wenn Ihrem Azure AD-Konto die Anzeigeberechtigung fehlt. Klicken Sie auf den Link **Zum Zugriffsschlüssel wechseln**, um erneut den Zugriffsschlüssel für die Authentifizierung zu nutzen.

### <a name="authenticate-with-your-azure-ad-account"></a>Authentifizierung mit dem Azure AD-Konto

Wenn Sie sich mit Ihrem Azure AD-Konto authentifizieren, wird im Portal **Azure AD-Benutzerkonto** als Authentifizierungsmethode angegeben:

:::image type="content" source="media/authorize-data-operations-portal/auth-method-azure-ad.png" alt-text="Screenshot eines Benutzers, der gegenwärtig mit dem Azure AD-Konto auf Warteschlangen zugreift":::

Um zur Verwendung des Zugriffsschlüssels zu wechseln, klicken Sie auf den in der Abbildung hervorgehobenen Link. Wenn Sie Zugriff auf den Kontoschlüssel haben, können Sie fortfahren. Wenn Sie jedoch keinen Zugriff auf den Kontoschlüssel haben, wird im Azure-Portal eine Fehlermeldung angezeigt.

Warteschlangen werden nicht im Portal aufgeführt, wenn Sie keinen Zugriff auf die Kontoschlüssel haben. Klicken Sie auf den Link **Zum Azure AD-Benutzerkonto wechseln**, um erneut das Azure AD-Konto für die Authentifizierung zu nutzen.

## <a name="next-steps"></a>Nächste Schritte

- [Authentifizieren des Zugriffs auf Azure-Blobs und -Warteschlangen mit Azure Active Directory](../common/storage-auth-aad.md)
- [Zuweisen einer Azure-Rolle für den Zugriff auf Blob- und Warteschlangendaten über das Azure-Portal](../common/storage-auth-aad-rbac-portal.md)
- [Verwenden der Azure-Befehlszeilenschnittstelle zum Zuweisen einer Azure-Rolle für den Zugriff auf Blob- und Warteschlangendaten](../common/storage-auth-aad-rbac-cli.md)
- [Verwenden des Azure PowerShell-Moduls zum Zuweisen einer Azure-Rolle für den Zugriff auf Blob- und Warteschlangendaten](../common/storage-auth-aad-rbac-powershell.md)
