---
title: Zuweisen einer Azure-Rolle für den Datenzugriff über das Azure-Portal
titleSuffix: Azure Storage
description: Hier erfahren Sie, wie Sie mit dem Azure-Portal Berechtigungen für einen Azure Active Directory-Sicherheitsprinzipal mit rollenbasierter Zugriffssteuerung von Azure (Azure Role-Based Access Control, Azure RBAC) zuweisen. Azure Storage unterstützt für die Authentifizierung über Azure AD integrierte und benutzerdefinierte Azure-Rollen.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/07/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 40003db9c3bd2c736f9cedd73b8b7a31a77f625f
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96854579"
---
# <a name="use-the-azure-portal-to-assign-an-azure-role-for-access-to-blob-and-queue-data"></a>Zuweisen einer Azure-Rolle für den Zugriff auf Blob- und Warteschlangendaten über das Azure-Portal

Azure Active Directory (Azure AD) autorisiert Rechte für den Zugriff auf abgesicherte Ressourcen über die [rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC)](../../role-based-access-control/overview.md). Azure Storage bietet eine Reihe in Azure integrierter Rollen mit allgemeinen Berechtigungssätzen für den Zugriff auf Blob- und Warteschlangendaten.

Wenn einem Azure AD-Sicherheitsprinzipal eine Azure-Rolle zugewiesen wird, gewährt ihm Azure Zugriff auf diese Ressourcen. Der Zugriff kann auf die Ebene des Abonnements, der Ressourcengruppe, des Speicherkontos oder eines einzelnen Containers oder einer Warteschlange begrenzt werden. Eine Azure AD-Sicherheitsprinzipal kann ein Benutzer, eine Gruppe, ein Anwendungsdienstprinzipal oder eine [verwaltete Identität für Azure-Ressourcen](../../active-directory/managed-identities-azure-resources/overview.md) sein.

In diesem Artikel wird beschrieben, wie Sie Azure-Rollen über das Azure-Portal zuweisen. Das Azure-Portal bietet eine einfache Benutzeroberfläche für die Zuweisung von Azure-Rollen und die Verwaltung des Zugriffs auf Ihre Speicherressourcen. Sie können Azure-Rollen für Blob- und Warteschlangenressourcen auch mithilfe von Azure-Befehlszeilentools oder Azure Storage-Verwaltungs-APIs zuweisen. Weitere Informationen zu Azure-Rollen für Speicherressourcen finden Sie unter [Authentifizieren des Zugriffs auf Azure-Blobs und -Warteschlangen mit Azure Active Directory](storage-auth-aad.md).

## <a name="azure-roles-for-blobs-and-queues"></a>Azure-Rollen für Blobs und Warteschlangen

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Bestimmen des Ressourcenumfangs

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="assign-azure-roles-using-the-azure-portal"></a>Zuweisen von Azure-Rollen über das Azure-Portal

Nachdem Sie den gewünschten Umfang für eine Rollenzuweisung festgelegt haben, navigieren Sie im Azure-Portal zur entsprechenden Ressource. Zeigen Sie die Einstellungen für die **Zugriffssteuerung (IAM)** für die Ressource an, und befolgen Sie diese Anweisungen zum Verwalten von Rollenzuweisungen:

1. Weisen Sie die entsprechende Azure Storage Azure-Rolle zu, um einem Azure AD-Sicherheitsprinzipal Zugriff zu gewähren.

1. Weisen Sie die Azure Resource Manager-Rolle [Leser](../../role-based-access-control/built-in-roles.md#reader) Benutzern zu, die über das Azure-Portal mit ihren Azure AD-Anmeldeinformationen auf Container oder Warteschlangen zugreifen müssen.

In den folgenden Abschnitten werden diese Schritte ausführlicher beschrieben.

> [!NOTE]
> Wenn Sie ein Azure Storage-Konto erstellen, erhalten Sie nicht automatisch Berechtigungen für den Zugriff auf Daten über Azure AD. Sie müssen sich selbst explizit eine Azure-Rolle für Azure Storage zuweisen. Sie können sie auf Ebene Ihres Abonnements, einer Ressourcengruppe, eines Speicherkontos oder eines Containers oder einer Warteschlange zuordnen.
>
> Bevor Sie sich eine Rolle für den Datenzugriff zuweisen, können Sie bereits über das Azure-Portal auf Daten in Ihrem Speicherkonto zugreifen, da das Azure-Portal auch den Kontoschlüssel für den Datenzugriff nutzen kann. Weitere Informationen finden Sie unter [Auswählen der Autorisierung des Zugriffs auf Blobdaten im Azure-Portal](../blobs/authorize-data-operations-portal.md).

### <a name="assign-an-azure-built-in-role"></a>Zuweisen einer in Azure integrierten Rolle

Bevor Sie einem Sicherheitsprinzipal eine Rolle zuweisen, stellen Sie sicher, dass Sie den Umfang der Berechtigungen berücksichtigen, die Sie erteilen. Legen Sie mithilfe des Abschnitts [Bestimmen des Ressourcenumfangs](#determine-resource-scope) den gewünschten Umfang fest.

Die hier gezeigte Vorgehensweise weist eine auf einen Container begrenzte Rolle zu, aber Sie können die gleichen Schritte ausführen, um eine auf eine Warteschlange begrenzte Rolle zuzuweisen:

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrem Speicherkonto, und zeigen Sie die **Übersicht** für das Konto an.
1. Wählen Sie unter „Dienste“ die Option **Blobs** aus.
1. Navigieren Sie zum Container, dem Sie eine Rolle zuweisen möchten, und zeigen Sie die Einstellungen des Containers an.
1. Wählen Sie **Zugriffssteuerung (IAM)** aus, um Zugriffssteuerungseinstellungen für den Container anzuzeigen. Wählen Sie die Registerkarte **Rollenzuweisungen** aus, um die Liste mit den Rollenzuweisungen anzuzeigen.

    :::image type="content" source="media/storage-auth-aad-rbac-portal/portal-access-control-container.png" alt-text="Screenshot mit Zugriffssteuerungseinstellungen für den Container":::

1. Klicken Sie auf die Schaltfläche **Rollenzuweisung hinzufügen**, um eine neue Rolle hinzuzufügen.
1. Wählen Sie im Fenster **Rollenzuweisung hinzufügen** die Azure Storage-Rolle aus, die Sie zuweisen möchten. Suchen Sie dann den Sicherheitsprinzipal, dem Sie diese Rolle zuweisen möchten.

    :::image type="content" source="media/storage-auth-aad-rbac-portal/add-rbac-role.png" alt-text="Screenshot: Zuweisen einer Azure-Rolle":::

1. Klicken Sie auf **Speichern**. Die Identität, der Sie die Rolle zugewiesen haben, wird unter dieser Rolle angezeigt. Die folgende Abbildung zeigt z.B., dass der hinzugefügte Benutzer nun Leseberechtigungen für Daten im Container *sample-container* hat.

    :::image type="content" source="media/storage-auth-aad-rbac-portal/container-scoped-role.png" alt-text="Screenshot mit einer Liste von Benutzern, denen eine Rolle zugewiesen ist":::

Sie können ähnliche Schritte ausführen, um eine Rolle zuzuweisen, deren Umfang für ein Speicherkonto, eine Ressourcengruppe oder ein Abonnement gilt.

### <a name="assign-the-reader-role-for-portal-access"></a>Zuweisen der Rolle „Leser“ für den Portalzugriff

Wenn Sie einem Sicherheitsprinzipal eine integrierte oder benutzerdefinierte Rolle für Azure Storage zuweisen, erteilen Sie diesem Sicherheitsprinzipal die Berechtigung, Vorgänge auf Daten in Ihrem Speicherkonto anzuwenden. Die integrierten Rollen des Typs **Datenleser** bieten Leserechte für die Daten in einem Container oder einer Warteschlange, während die integrierten Rollen des Typs **Mitwirkender an Daten** Lese-, Schreib- und Löschrechte für einen Container oder eine Warteschlange erteilen. Berechtigungen beziehen sich auf die angegebene Ressource.  
Wenn Sie beispielsweise der Benutzerin Mary die Rolle **Mitwirkender an Storage-Warteschlangendaten** auf der Ebene eines Containers namens **sample-container** zuweisen, erhält Mary Lese-, Schreib- und Löschzugriff auf alle Blobs in diesem Container.

Wenn Mary jedoch ein Blob im Azure-Portal anzeigen möchte, bietet die Rolle **Mitwirkender an Storage-Warteschlangendaten** allein nicht genügend Berechtigungen, um im Portal zum Blob zu navigieren, um ihn anzuzeigen. Zusätzliche Azure AD-Berechtigungen sind erforderlich, um durch das Portal zu navigieren und die anderen Ressourcen anzuzeigen, die dort sichtbar sind.

Wenn Ihre Benutzer auf Blobs im Azure-Portal zugreifen müssen, weisen Sie ihnen auf der Ebene des Speicherkontos oder höher die zusätzliche Azure-Rolle [Leser](../../role-based-access-control/built-in-roles.md#reader) zu. Die Rolle **Leser** ist eine Azure Resource Manager-Rolle, die es Benutzern ermöglicht, Ressourcen im Speicherkonto anzuzeigen, ohne sie ändern zu können. Sie bietet keine Leseberechtigungen für Daten in Azure Storage, sondern nur für Ressourcen zur Kontoverwaltung.

Führen Sie diese Schritte aus, um die Rolle **Leser** so zuzuweisen, dass ein Benutzer im Azure-Portal auf Blobs zugreifen kann. In diesem Beispiel ist die Zuweisung auf das Speicherkonto beschränkt:

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrem Speicherkonto.
1. Wählen Sie **Zugriffssteuerung (IAM)** aus, um Zugriffssteuerungseinstellungen für das Speicherkonto anzuzeigen. Wählen Sie die Registerkarte **Rollenzuweisungen** aus, um die Liste mit den Rollenzuweisungen anzuzeigen.
1. Wählen Sie im Fenster **Rollenzuweisung hinzufügen** die Rolle **Leser** aus. 
1. Wählen Sie im Feld **Zugriff zuweisen zu** die Option **Azure AD-Benutzer, -Gruppe oder -Dienstprinzipal** aus.
1. Suchen Sie den Sicherheitsprinzipal, dem Sie die Rolle zuweisen möchten.
1. Speichern Sie die Rollenzuweisung.

Die Zuweisung der Rolle **Leser** ist nur für Benutzer erforderlich, die über das Azure-Portal auf Blobs oder Warteschlangen zugreifen müssen.

> [!IMPORTANT]
> Die Vorschauversion von Azure Storage-Explorer im Azure-Portal unterstützt nicht die Verwendung von Azure AD-Anmeldeinformationen zum Anzeigen und Ändern von Blob- und Warteschlangendaten. Storage-Explorer im Azure-Portal verwendet immer die Kontoschlüssel für den Zugriff auf Daten. Um Storage-Explorer im Azure-Portal verwenden zu können, muss Ihnen eine Rolle zugewiesen werden, in der **Microsoft.Storage/storageAccounts/listkeys/action** enthalten ist.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu Azure-Rollen für Speicherressourcen finden Sie unter [Authentifizieren des Zugriffs auf Azure-Blobs und -Warteschlangen mit Azure Active Directory](storage-auth-aad.md). 
- Weitere Informationen zur Azure RBAC finden Sie im Artikel [Was ist die rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC)?](../../role-based-access-control/overview.md).
- Informationen zum Zuweisen und Verwalten von Azure-Rollenzuweisungen mit Azure PowerShell, der Azure-Befehlszeilenschnittstelle oder der REST-API finden Sie in diesen Artikeln:
    - [Hinzufügen oder Entfernen von Azure-Rollenzuweisungen mithilfe des Azure PowerShell-Moduls](../../role-based-access-control/role-assignments-powershell.md)
    - [Hinzufügen oder Entfernen von Azure-Rollenzuweisungen mithilfe der Azure-Befehlszeilenschnittstelle](../../role-based-access-control/role-assignments-cli.md)
    - [Hinzufügen oder Entfernen von Rollenzuweisungen mithilfe von Azure RBAC und der REST-API](../../role-based-access-control/role-assignments-rest.md)
- Informationen zum Autorisieren des Zugriffs auf Container und Warteschlangen in Ihren Speicheranwendungen finden Sie unter [Verwenden von Azure AD mit Azure Storage-Anwendungen](storage-auth-aad-app.md).
