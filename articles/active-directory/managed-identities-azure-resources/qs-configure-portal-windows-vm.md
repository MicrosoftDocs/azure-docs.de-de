---
title: Konfigurieren verwalteter Identitäten mithilfe des Azure-Portals – Azure AD
description: Ausführliche Anweisungen zum Konfigurieren von verwalteten Identitäten für Azure-Ressourcen auf einer Azure-VM über das Azure-Portal
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
ms.date: 12/15/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 78c973537e958b7199c137c81e72091f70b84ddb
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97590986"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-vm-using-the-azure-portal"></a>Konfigurieren von verwalteten Identitäten für Azure-Ressourcen auf einem virtuellen Computer über das Azure-Portal

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Verwaltete Identitäten für Azure-Ressourcen stellen für Azure-Dienste eine automatisch verwaltete Identität in Azure Active Directory bereit. Sie können diese Identität für die Authentifizierung bei jedem Dienst verwenden, der die Azure AD-Authentifizierung unterstützt. Hierfür müssen keine Anmeldeinformationen im Code enthalten sein. 

In diesem Artikel erfahren Sie, wie Sie eine vom System und vom Benutzer zugewiesene verwaltete Identität für einen virtuellen Azure-Computer (VM) über das Azure-Portal aktivieren und deaktivieren. 

## <a name="prerequisites"></a>Voraussetzungen

- Wenn Sie nicht mit verwalteten Identitäten für Azure-Ressourcen vertraut sind, helfen Ihnen die Informationen in der [Übersicht](overview.md) weiter.
- Wenn Sie noch kein Azure-Konto haben, sollten Sie sich [für ein kostenloses Konto registrieren](https://azure.microsoft.com/free/), bevor Sie fortfahren.

## <a name="system-assigned-managed-identity"></a>Systemseitig zugewiesene verwaltete Identität

In diesem Abschnitt erfahren Sie, wie Sie eine vom System zugewiesene verwaltete Identität für einen virtuellen Computer mit dem Azure-Portal aktivieren und deaktivieren.

### <a name="enable-system-assigned-managed-identity-during-creation-of-a-vm"></a>Aktivieren einer vom System zugewiesenen verwalteten Identität beim Erstellen eines virtuellen Computers

Zum Aktivieren der systemseitig zugewiesenen verwalteten Identität auf einem virtuellen Computer während dessen Erstellung benötigt Ihr Konto die Rollenzuweisung [Mitwirkender für virtuelle Computer](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).  Es sind keine weiteren Azure AD-Verzeichnisrollenzuweisungen erforderlich.

- Ändern Sie unter der Registerkarte **Verwaltung** im Abschnitt **Identität** die Einstellung für **Verwaltete Dienstidentität** zu **Ein**.  

![Aktivieren einer systemseitig zugewiesenen Identität beim Erstellen eines virtuellen Computers](./media/msi-qs-configure-portal-windows-vm/enable-system-assigned-identity-vm-creation.png)

Informationen zum Erstellen eines virtuellen Computers finden Sie in den folgenden Schnellstartanleitungen: 

- [Erstellen eines virtuellen Windows-Computers mit dem Azure-Portal](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine) 
- [Erstellen Sie eines virtuellen Linux-Computers mit dem Azure-Portal](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)


### <a name="enable-system-assigned-managed-identity-on-an-existing-vm"></a>Aktivieren einer vom System zugewiesenen verwalteten Identität auf einem vorhandenen virtuellen Computer

Zum Aktivieren der systemseitig zugewiesenen verwalteten Identität auf einem virtuellen Computer, der ursprünglich ohne diese bereitgestellt wurde, benötigt Ihr Konto die Rollenzuweisung [Mitwirkender für virtuelle Computer](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).  Es sind keine weiteren Azure AD-Verzeichnisrollenzuweisungen erforderlich.

1. Melden Sie sich am [Azure-Portal](https://portal.azure.com) mit einem Konto an, das dem Azure-Abonnement zugeordnet ist, das den virtuellen Computer enthält.

2. Navigieren Sie zum gewünschten virtuellen Computer, und wählen Sie **Identität** aus.

3. Wählen Sie unter **Vom System zugewiesen**, **Status** die Option **Ein** aus, und klicken Sie dann auf **Speichern**:

   ![Screenshot der Seite „Identität (Vorschau)“, auf welcher der Status „Vom System zugewiesen“ auf „Ein“ festgelegt ist](./media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade.png)  

### <a name="remove-system-assigned-managed-identity-from-a-vm"></a>Entfernen einer vom System zugewiesenen verwalteten Identität von einem virtuellen Computer

Zum Entfernen der systemseitig zugewiesenen verwalteten Identität von einem virtuellen Computer benötigt Ihr Konto die Rollenzuweisung [Mitwirkender für virtuelle Computer](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).  Es sind keine weiteren Azure AD-Verzeichnisrollenzuweisungen erforderlich.

Bei einem virtuellen Computer, für den die vom System zugewiesene verwaltete Identität nicht mehr benötigt wird, gehen Sie wie folgt vor:

1. Melden Sie sich am [Azure-Portal](https://portal.azure.com) mit einem Konto an, das dem Azure-Abonnement zugeordnet ist, das den virtuellen Computer enthält. 

2. Navigieren Sie zum gewünschten virtuellen Computer, und wählen Sie **Identität** aus.

3. Wählen Sie unter **Vom System zugewiesen**, **Status** die Option **Aus** aus, und klicken Sie dann auf **Speichern**:

   ![Screenshot der Konfigurationsseite](./media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade-disable.png)

## <a name="user-assigned-managed-identity"></a>Benutzerseitig zugewiesene verwaltete Identität

 In diesem Abschnitt erfahren Sie, wie Sie mithilfe des Azure-Portals eine vom Benutzer zugewiesene verwaltete Identität einer VM hinzufügen und von dieser entfernen.

### <a name="assign-a-user-assigned-identity-during-the-creation-of-a-vm"></a>Zuweisen einer vom Benutzer zugewiesenen Identität beim Erstellen eines virtuellen Computers

Für die Zuweisung einer benutzerseitig zugewiesenen Identität zu einem virtuellen Computer benötigt Ihr Konto die Rollenzuweisungen [Mitwirkender für virtuelle Computer](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) und [Operator für verwaltete Identität](../../role-based-access-control/built-in-roles.md#managed-identity-operator). Es sind keine weiteren Azure AD-Verzeichnisrollenzuweisungen erforderlich.

Derzeit unterstützt das Azure-Portal nicht das Zuweisen einer vom System zugewiesenen verwalteten Identität während der Erstellung einer VM. Lesen Sie stattdessen die folgenden Schnellstartartikel, um zunächst einen virtuellen Computer zu erstellen, und fahren Sie dann mit dem nächsten Abschnitt mit Einzelheiten zum Zuweisen einer vom Benutzer zugewiesenen verwalteten Identität zum virtuellen Computer fort:

- [Erstellen eines virtuellen Windows-Computers mit dem Azure-Portal](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Erstellen Sie eines virtuellen Linux-Computers mit dem Azure-Portal](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-vm"></a>Zuweisen einer vom Benutzer zugewiesenen verwalteten Identität zu einem vorhandenen virtuellen Computer

Für die Zuweisung einer benutzerseitig zugewiesenen Identität zu einem virtuellen Computer benötigt Ihr Konto die Rollenzuweisungen [Mitwirkender für virtuelle Computer](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) und [Operator für verwaltete Identität](../../role-based-access-control/built-in-roles.md#managed-identity-operator). Es sind keine weiteren Azure AD-Verzeichnisrollenzuweisungen erforderlich.

1. Melden Sie sich am [Azure-Portal](https://portal.azure.com) mit einem Konto an, das dem Azure-Abonnement zugeordnet ist, das den virtuellen Computer enthält.
2. Navigieren Sie zur gewünschten VM, und klicken Sie auf **Identität**, **Vom Benutzer zugewiesen** und dann auf **\+Hinzufügen**.

   ![Screenshot der Seite „Identität“, auf der „Vom Benutzer zugewiesen“ ausgewählt und die Schaltfläche „Hinzufügen“ hervorgehoben ist](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot1.png)

3. Klicken Sie auf die vom Benutzer zugewiesene Identität, die Sie der VM hinzufügen möchten, und dann auf **Hinzufügen**.

    ![Hinzufügen einer vom Benutzer zugewiesenen verwalteten Identität zu einer VM](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot2.png)

### <a name="remove-a-user-assigned-managed-identity-from-a-vm"></a>Entfernen einer vom Benutzer zugewiesenen verwalteten Identität von einer VM

Für das Entfernen einer benutzerseitig zugewiesenen Identität von einem virtuellen Computer benötigt Ihr Konto die Rollenzuweisung [Mitwirkender für virtuelle Computer](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor). Es sind keine weiteren Azure AD-Verzeichnisrollenzuweisungen erforderlich.

1. Melden Sie sich am [Azure-Portal](https://portal.azure.com) mit einem Konto an, das dem Azure-Abonnement zugeordnet ist, das den virtuellen Computer enthält.
2. Navigieren Sie zur gewünschten VM, und klicken Sie auf **Identität**, **Vom Benutzer zugewiesen**, den Namen der vom Benutzer zugewiesenen verwalteten Identität, die Sie löschen möchten, und dann auf **Entfernen**. (Klicken Sie im Bestätigungsbereich auf **Ja**.)

   ![Entfernen einer vom Benutzer zugewiesenen verwalteten Identität von einer VM](./media/msi-qs-configure-portal-windows-vm/remove-user-assigned-identity-vm-screenshot.png)

## <a name="next-steps"></a>Nächste Schritte

- Gewähren Sie der verwalteten Identität einer Azure-VM über das Azure-Portal [Zugriff auf eine andere Azure-Ressource](howto-assign-access-portal.md).