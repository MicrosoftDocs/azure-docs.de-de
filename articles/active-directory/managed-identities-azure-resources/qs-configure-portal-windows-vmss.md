---
title: Konfigurieren verwalteter Identitäten in VM-Skalierungsgruppen – Azure AD
description: Ausführliche Anweisungen zum Konfigurieren von verwalteten Identitäten für Azure-Ressourcen in einer VM-Skalierungsgruppe über das Azure-Portal
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/20/2018
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 43b986b6bb25dff1227247b1d249ce565553877a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95997408"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-virtual-machine-scale-set-using-the-azure-portal"></a>Konfigurieren von verwalteten Identitäten für Azure-Ressourcen in einer VM-Skalierungsgruppe über das Azure-Portal

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Verwaltete Identitäten für Azure-Ressourcen stellen für Azure-Dienste eine automatisch verwaltete Identität in Azure Active Directory bereit. Sie können diese Identität für die Authentifizierung bei jedem Dienst verwenden, der die Azure AD-Authentifizierung unterstützt. Hierfür müssen keine Anmeldeinformationen im Code enthalten sein. 

In diesem Artikel erfahren Sie, wie Sie mit PowerShell die folgenden Vorgänge für verwaltete Identitäten für Azure-Ressourcen in einer VM-Skalierungsgruppe ausführen können:

- Wenn Sie nicht mit verwalteten Identitäten für Azure-Ressourcen vertraut sind, helfen Ihnen die Informationen in der [Übersicht](overview.md) weiter.
- Wenn Sie noch kein Azure-Konto haben, sollten Sie sich [für ein kostenloses Konto registrieren](https://azure.microsoft.com/free/), bevor Sie fortfahren.
- Um die Verwaltungsvorgänge in diesem Artikel auszuführen, benötigt Ihr Konto die folgenden Azure-Rollenzuweisungen:

    > [!NOTE]
    > Es sind keine weiteren Azure AD-Verzeichnisrollenzuweisungen erforderlich.

    - [Mitwirkender für virtuelle Computer](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) zum Aktivieren und Entfernen einer vom System zugewiesenen verwalteten Identität in einer VM-Skalierungsgruppe

## <a name="system-assigned-managed-identity"></a>Systemseitig zugewiesene verwaltete Identität

In diesem Abschnitt erfahren Sie, wie Sie die vom System zugewiesene verwaltete Identität über das Azure-Portal aktivieren und deaktivieren.

### <a name="enable-system-assigned-managed-identity-during-creation-of-a-virtual-machine-scale-set"></a>Aktivieren der vom System zugewiesenen verwalteten Identität bei der Erstellung einer VM-Skalierungsgruppe

Derzeit unterstützt das Azure-Portal keine Aktivierung einer vom System zugewiesenen verwalteten Identität während der Erstellung einer VM-Skalierungsgruppe. Lesen Sie stattdessen den folgenden Schnellstartartikel zu VM-Skalierungsgruppen, um zunächst eine VM-Skalierungsgruppe zu erstellen, und fahren Sie dann mit dem nächsten Abschnitt mit Einzelheiten zur Aktivierung einer vom System zugewiesenen verwalteten Identität für eine VM-Skalierungsgruppe fort:

- [Erstellen einer VM-Skalierungsgruppe im Azure-Portal](../../virtual-machine-scale-sets/quick-create-portal.md)  

### <a name="enable-system-assigned-managed-identity-on-an-existing-virtual-machine-scale-set"></a>Aktivieren einer vom System zugewiesenen verwalteten Identität in einer vorhandenen VM-Skalierungsgruppe

So aktivieren Sie eine vom System zugewiesene verwaltete Identität in einer VM-Skalierungsgruppe, die ursprünglich ohne sie bereitgestellt wurde

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) mit einem Konto an, das dem Azure-Abonnement zugeordnet ist, das die VM-Skalierungsgruppe enthält.

2. Navigieren Sie zur gewünschten VM-Skalierungsgruppe.

3. Wählen Sie unter **Vom System zugewiesen**, **Status** die Option **Ein** aus, und klicken Sie dann auf **Speichern**:

   ![Screenshot der Seite „Identität (Vorschau)“, auf der „Vom System zugewiesen“ ausgewählt, der Status auf „Ein“ eingestellt und die Schaltfläche „Speichern“ hervorgehoben ist](./media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png) 

### <a name="remove-system-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Entfernen einer vom System zugewiesenen verwalteten Identität aus einer VM-Skalierungsgruppe

Wenn für die VM-Skalierungsgruppe keine vom System zugewiesene verwaltete Identität mehr benötigt wird, gehen Sie wie folgt vor:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) mit einem Konto an, das dem Azure-Abonnement zugeordnet ist, das die VM-Skalierungsgruppe enthält. Stellen Sie außerdem sicher, dass Ihr Konto zu einer Rolle gehört, die Ihnen Schreibberechtigungen für die VM-Skalierungsgruppe erteilt.

2. Navigieren Sie zur gewünschten VM-Skalierungsgruppe.

3. Wählen Sie unter **Vom System zugewiesen**, **Status** die Option **Aus** aus, und klicken Sie dann auf **Speichern**:

   ![Screenshot der Konfigurationsseite](./media/msi-qs-configure-portal-windows-vmss/disable-windows-vmss-portal-configuration-blade.png)

## <a name="user-assigned-managed-identity"></a>Benutzerseitig zugewiesene verwaltete Identität

In diesem Abschnitt erfahren Sie, wie Sie im Azure-Portal eine vom Benutzer zugewiesene verwaltete Identität einer VM-Skalierungsgruppe hinzufügen und aus dieser entfernen.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Zuweisen einer vom Benutzer zugewiesenen verwalteten Identität bei der Erstellung einer VM-Skalierungsgruppe

Derzeit unterstützt das Azure-Portal nicht das Zuweisen einer vom Benutzer zugewiesenen verwalteten Identität während der Erstellung einer VM-Skalierungsgruppe. Lesen Sie stattdessen den folgenden Schnellstartartikel zu VM-Skalierungsgruppen, um zunächst eine VM-Skalierungsgruppe zu erstellen, und fahren Sie dann mit dem nächsten Abschnitt mit Einzelheiten zur Zuweisung einer vom Benutzer zugewiesenen verwalteten Identität fort:

- [Erstellen einer VM-Skalierungsgruppe im Azure-Portal](../../virtual-machine-scale-sets/quick-create-portal.md)

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-virtual-machine-scale-set"></a>Zuweisen einer vom Benutzer zugewiesenen verwalteten Identität zu einer vorhandenen VM-Skalierungsgruppe

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) mit einem Konto an, das dem Azure-Abonnement zugeordnet ist, das die VM-Skalierungsgruppe enthält.
2. Navigieren Sie zur gewünschten VM-Skalierungsgruppe, und klicken Sie auf **Identität**, **Vom Benutzer zugewiesen** und dann auf **\+Hinzufügen**.

   ![Screenshot der Seite „Identität“, auf der „Vom Benutzer zugewiesen“ ausgewählt und die Schaltfläche „Hinzufügen“ hervorgehoben ist](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vmss-screenshot1.png)

3. Klicken Sie auf die vom Benutzer zugewiesene Identität, die Sie der VM-Skalierungsgruppe hinzufügen möchten, und dann auf **Hinzufügen**.
   
   ![Hinzufügen einer vom Benutzer zugewiesenen Identität zu VM-Skalierungsgruppen](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot2.png)

### <a name="remove-a-user-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Entfernen einer vom Benutzer zugewiesenen verwalteten Identität aus einer VM-Skalierungsgruppe

1. Melden Sie sich am [Azure-Portal](https://portal.azure.com) mit einem Konto an, das dem Azure-Abonnement zugeordnet ist, das den virtuellen Computer enthält.
2. Navigieren Sie zur gewünschten VM-Skalierungsgruppe, und klicken Sie auf **Identität**, **Vom Benutzer zugewiesen**, den Namen der vom Benutzer zugewiesenen verwalteten Identität, die Sie löschen möchten, und dann auf **Entfernen**. (Klicken Sie im Bestätigungsbereich auf **Ja**.)

   ![Entfernen einer vom Benutzer zugewiesenen Identität aus einer VM-Skalierungsgruppe](./media/msi-qs-configure-portal-windows-vm/remove-user-assigned-identity-vmss-screenshot.png)


## <a name="next-steps"></a>Nächste Schritte

- Gewähren Sie der verwalteten Identität einer Azure-VM-Skalierungsgruppe über das Azure-Portal den [Zugriff auf eine andere Azure-Ressource](howto-assign-access-portal.md).