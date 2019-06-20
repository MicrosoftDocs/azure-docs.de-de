---
title: Hinzufügen eines Dienstprinzipals zur Azure Analysis Services-Serveradministratorrolle | Microsoft Docs
description: Hier erfahren Sie, wie Sie einen Automatisierungsdienstprinzipal zur Serveradministratorrolle hinzufügen.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 701be795ca217c4a2dc5a7dbaa3a3717d16c85bc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61024608"
---
# <a name="add-a-service-principal-to-the-server-administrator-role"></a>Hinzufügen eines Dienstprinzipals zur Serveradministratorrolle 

 Um unbeaufsichtigte PowerShell-Tasks zu automatisieren, muss ein Dienstprinzipal auf dem verwalteten Analysis Services-Server über die Rechte eines **Serveradministrators** verfügen. In diesem Artikel wird beschrieben, wie Sie auf einem Azure AS-Server einen Dienstprinzipal zur Serveradministratorrolle hinzufügen.

## <a name="before-you-begin"></a>Voraussetzungen
Bevor Sie diese Aufgabe ausführen, muss ein Dienstprinzipal in Azure Active Directory registriert sein.

[Erstellen eines Dienstprinzipals – Azure-Portal](../active-directory/develop/howto-create-service-principal-portal.md)   
[Erstellen eines Dienstprinzipals – PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="required-permissions"></a>Erforderliche Berechtigungen
Für die Durchführung dieser Aufgabe benötigen Sie die Rechte eines [Serveradministrators](analysis-services-server-admins.md) auf dem Azure AS-Server. 

## <a name="add-service-principal-to-server-administrators-role"></a>Hinzufügen eines Dienstprinzipals zur Serveradministratorrolle

1. Stellen Sie in SSMS eine Verbindung mit Ihrem Azure AS-Server her.
2. Klicken Sie unter **Servereigenschaften** > **Sicherheit** auf **Hinzufügen**.
3. Suchen Sie unter **Benutzer oder Gruppe auswählen** anhand des Namens nach Ihrer registrierten App, und klicken Sie dann auf **Hinzufügen**.

    ![Suchen nach dem Dienstprinzipalkonto](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-picker.png)

4. Überprüfen Sie die ID des Dienstprinzipalkontos, und klicken Sie dann auf **OK**.
    
    ![Suchen nach dem Dienstprinzipalkonto](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-add.png)


> [!NOTE]
> Bei Servervorgängen, für die Azure PowerShell-Cmdlets verwendet werden, muss der Dienstprinzipal mit dem Scheduler außerdem zur Rolle **Besitzer** für die Ressource in der [rollenbasierten Zugriffssteuerung in Azure (Role-Based Access Control, RBAC)](../role-based-access-control/overview.md) gehören. 

## <a name="related-information"></a>Verwandte Informationen

* [Herunterladen des SQL Server PowerShell-Moduls](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [Herunterladen von SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   


