---
title: Verwalten einer vom Benutzer zugewiesenen verwalteten Identität über das Azure-Portal
description: Enthält eine Schritt-für-Schritt-Anleitung zum Erstellen, Auflisten, Löschen und Zuweisen einer vom Benutzer zugewiesenen verwalteten Identität.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/16/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 18a15b8039322fc5e43a2b9dfed8a9bd3fc8b5fb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60441653"
---
# <a name="create-list-delete-or-assign-a-role-to-a-user-assigned-managed-identity-using-the-azure-portal"></a>Erstellen, Auflisten, Löschen oder Zuweisen einer Rolle zu einer vom Benutzer zugewiesenen verwalteten Identität über das Azure-Portal

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Verwaltete Identitäten für Azure-Ressourcen stellen für Azure-Dienste eine verwaltete Identität in Azure Active Directory bereit. Sie können diese Identität verwenden, um sich bei Diensten mit Unterstützung für die Azure AD-Authentifizierung zu authentifizieren, ohne dass Anmeldeinformationen in Ihren Code eingebettet werden müssen. 

In diesem Artikel erfahren Sie, wie Sie im Azure-Portal eine vom Benutzer zugewiesene verwaltete Identität erstellen, auflisten, löschen oder zuweisen.

## <a name="prerequisites"></a>Voraussetzungen

- Wenn Sie nicht mit verwalteten Identitäten für Azure-Ressourcen vertraut sind, helfen Ihnen die Informationen in der [Übersicht](overview.md) weiter. **Machen Sie sich den [Unterschied zwischen einer vom System und einer vom Benutzer zugewiesenen verwalteten Identität](overview.md#how-does-it-work)** bewusst.
- Wenn Sie noch kein Azure-Konto haben, sollten Sie sich [für ein kostenloses Konto registrieren](https://azure.microsoft.com/free/), bevor Sie fortfahren.

## <a name="create-a-user-assigned-managed-identity"></a>Erstellen einer benutzerseitig zugewiesenen verwalteten Identität

Ihrem Konto muss die Rolle [Mitwirkender für verwaltete Identität](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) zugewiesen sein, damit eine benutzerseitig zugewiesene verwaltete Identität erstellt werden kann.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) mit einem Konto an, das dem Azure-Abonnement zugeordnet ist, um die vom Benutzer zugewiesene verwaltete Identität zu erstellen.
2. Geben Sie *Verwaltete Identitäten* im Suchfeld ein, und klicken Sie unter **Dienste** auf **Verwaltete Identitäten**.
3. Klicken Sie auf **Hinzufügen**, und geben Sie im Bereich **Vom Benutzer zugewiesene verwaltete Identität erstellen** in den folgenden Feldern Werte ein:
   - **Ressourcenname:** Dies ist der Name für die vom Benutzer zugewiesene verwaltete Identität, z.B. „UAI1“.
   - **Abonnement**: Wählen Sie das Abonnement aus, in dem die vom Benutzer zugewiesene verwaltete Identität erstellt werden soll.
   - **Ressourcengruppe:** Erstellen Sie für die vom Benutzer zugewiesene Identität eine neue Ressourcengruppe, oder wählen Sie **Vorhandene verwenden** aus, um die vom Benutzer zugewiesene verwaltete Identität in einer vorhandenen Ressourcengruppe zu erstellen.
   - **Standort:** Wählen Sie einen Standort zum Bereitstellen der vom Benutzer zugewiesenen verwalteten Identität aus, z.B. **USA, Westen**.
4. Klicken Sie auf **Create**.

![Erstellen einer benutzerseitig zugewiesenen verwalteten Identität](./media/how-to-manage-ua-identity-portal/create-user-assigned-managed-identity-portal.png)

## <a name="list-user-assigned-managed-identities"></a>Auflisten der vom Benutzer zugewiesenen verwalteten Identitäten

Ihrem Konto muss die Rolle [Operator für verwaltete Identität](/azure/role-based-access-control/built-in-roles#managed-identity-operator) oder [Mitwirkender für verwaltete Identität](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) zugewiesen sein, damit eine benutzerseitig zugewiesene verwaltete Identität aufgelistet/gelesen werden kann.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) mit einem Konto an, das dem Azure-Abonnement zugeordnet ist, um die vom Benutzer zugewiesenen verwalteten Identitäten aufzulisten.
2. Geben Sie *Verwaltete Identitäten* im Suchfeld ein, und klicken Sie unter „Dienste“ auf **Verwaltete Identitäten**.
3. Eine Liste der vom Benutzer zugewiesenen verwalteten Identitäten für Ihr Abonnement wird zurückgegeben.  Durch Klicken auf den entsprechenden Namen können Sie die Details der vom Benutzer zugewiesenen verwalteten Identität anzeigen.

![Auflisten der vom Benutzer zugewiesenen verwalteten Identitäten](./media/how-to-manage-ua-identity-portal/list-user-assigned-managed-identity-portal.png)

## <a name="delete-a-user-assigned-managed-identity"></a>Löschen einer vom Benutzer zugewiesenen verwalteten Identität

Ihrem Konto muss die Rolle [Mitwirkender für verwaltete Identität](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) zugewiesen sein, damit eine benutzerseitig zugewiesene verwaltete Identität gelöscht werden kann.

Durch das Löschen wird eine vom Benutzer zugewiesene Identität nicht von der VM oder Ressource entfernt, der sie zugewiesen wurde.  Informationen zum Entfernen einer vom Benutzer zugewiesenen Identität von einem virtuellen Computer finden Sie unter [Entfernen einer vom Benutzer zugewiesenen verwalteten Identität von einer VM](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#remove-a-user-assigned-managed-identity-from-a-vm).

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) mit einem Konto an, das dem Azure-Abonnement zugeordnet ist, um eine vom Benutzer zugewiesene verwaltete Identität zu löschen.
2. Wählen Sie die vom Benutzer zugewiesene verwaltete Identität aus, und klicken Sie auf **Löschen**.
3. Wählen Sie im Bestätigungsfeld **Ja** aus.

![Löschen einer vom Benutzer zugewiesenen verwalteten Identität](./media/how-to-manage-ua-identity-portal/delete-user-assigned-managed-identity-portal.png)

## <a name="assign-a-role-to-a-user-assigned-managed-identity"></a>Zuweisen einer Rolle zu einer vom Benutzer zugewiesenen verwalteten Identität 

Ihrem Konto muss die Rolle [Benutzerzugriffsadministrator](/azure/role-based-access-control/built-in-roles#user-access-administrator) zugewiesen sein, damit einer benutzerseitig zugewiesenen verwalteten Identität eine Rolle zugewiesen werden kann.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) mit einem Konto an, das dem Azure-Abonnement zugeordnet ist, um die vom Benutzer zugewiesenen verwalteten Identitäten aufzulisten.
2. Geben Sie *Verwaltete Identitäten* im Suchfeld ein, und klicken Sie unter „Dienste“ auf **Verwaltete Identitäten**.
3. Eine Liste der vom Benutzer zugewiesenen verwalteten Identitäten für Ihr Abonnement wird zurückgegeben.  Wählen Sie die vom Benutzer zugewiesene verwaltete Identität aus, der Sie eine Rolle zuweisen möchten.
4. Wählen Sie **Zugriffssteuerung (IAM)** und anschließend **Rollenzuweisung hinzufügen** aus.

   ![Vom Benutzer zugewiesene verwaltete Identität – Start](./media/how-to-manage-ua-identity-portal/assign-role-screenshot1.png)

5. Konfigurieren Sie auf dem Blatt „Rollenzuweisung hinzufügen“ die folgenden Werte, und klicken Sie dann auf **Speichern**:
   - **Rolle**: Zuzuweisende Rolle
   - **Zugriff zuweisen zu**: Ressource, der die vom Benutzer zugewiesene verwaltete Identität zugewiesen wird
   - **Auswählen**: Mitglied, dem der Zugriff zugewiesen wird
   
   ![Vom Benutzer zugewiesene verwaltete Identität – IAM](./media/how-to-manage-ua-identity-portal/assign-role-screenshot2.png)  
