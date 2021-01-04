---
title: Verwalten von SQL Server-VMs in Azure im Azure-Portal | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie im Azure-Portal auf die Ressource „Virtuelle SQL-Computer“ für eine in Azure gehostete SQL Server-VM zugreifen.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 68c7805136a7361a64a6ff6dfbb9e7d910b2ea9b
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97357941"
---
# <a name="manage-sql-server-vms-in-azure-by-using-the-azure-portal"></a>Verwalten von SQL Server-VMs in Azure über das Azure-Portal
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Im [Azure-Portal](https://portal.azure.com) ist die Ressource [**Virtuelle SQL-Computer**](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) ein unabhängiger Verwaltungsdienst zum Verwalten von SQL Server auf Azure-VMs. Sie können damit alle SQL Server-VMs gleichzeitig anzeigen und Einstellungen für SQL Server ändern: 

![Ressource „Virtuelle SQL-Computer“](./media/manage-sql-vm-portal/sql-vm-manage.png)


## <a name="remarks"></a>Bemerkungen

- Es wird empfohlen, die Ressource [**Virtuelle SQL-Computer**](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) zum Anzeigen und Verwalten Ihrer SQL Server-VMs in Azure zu verwenden. Derzeit unterstützt die Ressource **SQL-VMs** jedoch noch nicht die Verwaltung von SQL Server-VMs, deren [Support abgelaufen](sql-server-2008-extend-end-of-support.md) ist. Um Einstellungen für Ihre SQL Server-VMs mit abgelaufenem Support zu verwalten, verwenden Sie stattdessen die veraltete [Registerkarte „SQL Server-Konfiguration“](#access-the-sql-server-configuration-tab). 
- Die Ressource **SQL-VMs** ist nur für SQL Server-VMs verfügbar, die [mit der SQL-IaaS-Agent-Erweiterung registriert](sql-agent-extension-manually-register-single-vm.md) wurden. 


## <a name="access-the-sql-virtual-machines-resource"></a>Zugreifen auf die Ressource „SQL-VMs“
Führen Sie zum Zugreifen auf die Ressource **SQL-VMs** folgende Schritte aus:

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com). 
1. Wählen Sie **Alle Dienste** aus. 
1. Geben Sie in das Suchfeld **SQL-VMs** ein.
1. (Optional): Wählen Sie das Sternsymbol neben **SQL-VMs** aus, um diese Option Ihrem Menü **Favoriten** hinzuzufügen. 
1. Wählen Sie **Virtuelle SQL-Computer** aus. 

   ![Ermitteln von virtuellen SQL Server-Computern in allen Diensten](./media/manage-sql-vm-portal/sql-vm-search.png)

1. Im Portal werden alle SQL Server-VMs aufgelistet, die im Abonnement verfügbar sind. Wählen Sie die zu verwaltende VM aus, um die Ressource **SQL-VMs** zu öffnen. Verwenden Sie das Suchfeld, wenn Ihre SQL Server-VM nicht angezeigt wird. 

   ![Alle verfügbaren SQL Server-VMs](./media/manage-sql-vm-portal/all-sql-vms.png)

   Durch Auswählen der SQL Server-VM wird die Ressource **SQL-VMs** geöffnet: 


   ![Anzeigen der Ressource „Virtuelle SQL-Computer“](./media/manage-sql-vm-portal/sql-vm-resource.png)

> [!TIP]
> Die Ressource **Virtuelle SQL-Computer** ist für SQL Server-Einstellungen vorgesehen. Wählen Sie im Feld **Virtueller Computer** den Namen des virtuellen Computers aus, um VM-spezifische Einstellungen zu öffnen, die nicht ausschließlich für SQL Server gelten. 

## <a name="access-the-sql-server-configuration-tab"></a>Zugreifen auf die Registerkarte „SQL Server-Konfiguration“
Die Registerkarte **SQL Server-Konfiguration** ist veraltet. Sie stellt im Moment jedoch die einzige Möglichkeit dar, um SQL Server-VMs mit [abgelaufenem Support](sql-server-2008-extend-end-of-support.md) sowie SQL Server-VMs zu verwalten, die nicht mit der [SQL-IaaS-Agent-Erweiterung](sql-agent-extension-manually-register-single-vm.md) registriert wurden.

Navigieren Sie zum Zugreifen auf die veraltete Registerkarte **SQL Server-Konfiguration** zur Ressource **Virtuelle Computer**. Führen Sie die folgenden Schritte durch:

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com). 
1. Wählen Sie **Alle Dienste** aus. 
1. Geben Sie in das Suchfeld **virtuelle Computer** ein.
1. (Optional): Wählen Sie das Sternsymbol neben **Virtuelle Computer** aus, um diese Option Ihrem Menü **Favoriten** hinzuzufügen. 
1. Wählen Sie **Virtuelle Computer** aus. 

   ![Suchen virtueller Computer](./media/manage-sql-vm-portal/vm-search.png)

1. Im Portal werden alle virtuellen Computer im Abonnement aufgelistet. Wählen Sie die zu verwaltende VM aus, um die Ressource **Virtuelle Computer** zu öffnen. Verwenden Sie das Suchfeld, wenn Ihre SQL Server-VM nicht angezeigt wird. 
1. Wählen Sie im Bereich **Einstellungen** die Option **SQL Server-Konfiguration** aus, um Ihre SQL Server-VM zu verwalten. 

   ![SQL Server-Konfiguration](./media/manage-sql-vm-portal/sql-vm-configuration.png)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Artikeln: 

* [Übersicht über SQL Server auf einem virtuellen Windows-Computer](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Häufig gestellte Fragen zu SQL Server auf virtuellen Windows-Computern](frequently-asked-questions-faq.md)
* [Preisinformationen für SQL Server auf virtuellen Windows-Computern](pricing-guidance.md)
* [SQL Server auf Windows-VMs – Versionshinweise](doc-changes-updates-release-notes.md)


