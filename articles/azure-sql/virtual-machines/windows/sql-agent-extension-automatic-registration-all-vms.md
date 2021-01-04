---
title: Automatische Registrierung von VMs mit der SQL-IaaS-Agent-Erweiterung
description: Hier erfahren Sie, wie Sie im Azure-Portal die automatische Registrierungsfunktion aktivieren, um alle bisherigen und zukünftigen SQL Server-VMs mit der SQL-IaaS-Agent-Erweiterung zu registrieren.
author: MashaMSFT
ms.author: mathoma
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/07/2020
ms.openlocfilehash: 1ef7943586123a1870ed9a2d0c21aa8b5fd38c1c
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359998"
---
# <a name="automatic-registration-with-sql-iaas-agent-extension"></a>Automatische Registrierung von VMs mit der SQL-IaaS-Agent-Erweiterung
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Aktivieren Sie die automatische Registrierungsfunktion im Azure-Portal, um alle aktuellen und zukünftigen SQL Server-Instanzen auf virtuellen Azure-Computern (Virtual Machines, VMs) mit der [SQL-IaaS-Agent-Erweiterung](sql-server-iaas-agent-extension-automate-management.md) automatisch im Modus „Lightweight“ zu registrieren. 

In diesem Artikel erfahren Sie, wie Sie die automatische Registrierungsfunktion aktivieren. Alternativ können Sie [eine einzelne VM](sql-agent-extension-manually-register-single-vm.md) oder [mehrere Ihrer VMs in einem Massenvorgang](sql-agent-extension-manually-register-vms-bulk.md) mit der SQL-IaaS-Agent-Erweiterung registrieren. 

## <a name="overview"></a>Übersicht

Wenn Sie Ihre SQL Server-VM mit der [SQL-IaaS-Agent-Erweiterung](sql-server-iaas-agent-extension-automate-management.md) registrieren, können Sie den vollen Funktionsumfang nutzen. 

Wenn die automatische Registrierung aktiviert ist, wird täglich ein Auftrag ausgeführt, um zu ermitteln, ob SQL Server auf allen nicht registrierten virtuellen Computern im Abonnement installiert ist. Hierzu werden die Binärdateien der Erweiterung für den SQL-IaaS-Agent auf den virtuellen Computer kopiert. Anschließend wird einmalig ein Hilfsprogramm ausgeführt, um nach der SQL Server-Registrierungsstruktur zu suchen. Wenn die SQL Server-Struktur erkannt wird, erfolgt die Registrierung des virtuellen Computers mit der Erweiterung im Modus „Lightweight“. Ist in der Registrierung keine SQL Server-Struktur vorhanden, werden die Binärdateien entfernt.

Nach der Aktivierung der automatischen Registrierung für ein Abonnement werden alle aktuellen und zukünftigen VMs, auf denen SQL Server installiert ist, mit der SQL-IaaS-Agent-Erweiterung im Modus „Lightweight“ registriert. Sie müssen weiterhin [manuell auf den Modus „Vollständige Verwaltbarkeit“ aktualisieren](sql-agent-extension-manually-register-single-vm.md#upgrade-to-full), um den vollständigen Fuktionssatz nutzen zu können. 

> [!IMPORTANT]
> Die Erweiterung für den SQL-IaaS-Agent sammelt Daten ausschließlich, um Kunden bei der Verwendung von SQL Server in Azure Virtual Machines optionale Vorteile zu bieten. Microsoft verwendet diese Daten ohne vorherige Zustimmung des Kunden nicht für Lizenzierungsüberprüfungen. Weitere Informationen finden Sie unter [Ergänzende Datenschutzbestimmungen zu SQL Server](/sql/sql-server/sql-server-privacy#non-personal-data).

## <a name="prerequisites"></a>Voraussetzungen

Um Ihre SQL Server-VM mit der Erweiterung registrieren zu können, benötigen Sie Folgendes: 

- Ein [Azure-Abonnement](https://azure.microsoft.com/free/) und mindestens Berechtigungen der [Rolle „Mitwirkender“](../../../role-based-access-control/built-in-roles.md#all).
- Ein in der öffentlichen Cloud oder in der Azure Government-Cloud bereitgestelltes Azure-Ressourcenmodell vom Typ [Virtueller Computer mit Windows Server 2008 R2 (oder höher)](../../../virtual-machines/windows/quick-create-portal.md) mit [SQL Server](https://www.microsoft.com/sql-server/sql-server-downloads). Windows Server 2008 wird nicht unterstützt. 


## <a name="enable"></a>Aktivieren

Führen Sie die folgenden Schritte aus, um im Azure-Portal die automatische Registrierung Ihrer virtuellen SQL Server-Computer zu aktivieren:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Navigieren Sie zur Ressourcenseite [**Virtuelle SQL-Computer**](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines). 
1. Wählen Sie **Automatische SQL Server-VM-Registrierung** aus, um die Seite **Automatische Registrierung** zu öffnen. 

   :::image type="content" source="media/sql-agent-extension-automatic-registration-all-vms/automatic-registration.png" alt-text="„Automatische SQL Server-VM-Registrierung“ auswählen, um die Seite „Automatische Registrierung“ zu öffnen":::

1. Wählen Sie im Dropdownmenü Ihr Abonnement aus. 
1. Lesen Sie die Nutzungsbedingungen, und wählen Sie, wenn Sie zustimmen, **Ich stimme zu** aus. 
1. Wählen Sie **Registrieren** aus, um die Funktion zu aktivieren und alle aktuellen und zukünftigen SQL Server-VMs mit der SQL-IaaS-Agent-Erweiterung automatisch zu registrieren. Dadurch wird der SQL Server-Dienst auf keiner der VMs neu gestartet. 

## <a name="disable"></a>Deaktivieren

Verwenden Sie die [Azure CLI](/cli/azure/install-azure-cli) oder [Azure PowerShell](/powershell/azure/install-az-ps), um die automatische Registrierungsfunktion zu deaktivieren. Wenn die automatische Registrierungsfunktion deaktiviert ist, müssen SQL Server-VMs, die dem Abonnement hinzugefügt werden, mit der SQL-IaaS-Agent-Erweiterung manuell registriert werden. Eine Deaktivierung der Funktion führt nicht dazu, dass die Registrierung vorhandener SQL Server-VMs aufgehoben wird, die bereits registriert wurden.



# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Führen Sie den folgenden Befehl aus, um die automatische Registrierung mit Azure CLI zu deaktivieren: 

```azurecli-interactive
az feature unregister --namespace Microsoft.SqlVirtualMachine --name BulkRegistration
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Führen Sie den folgenden Befehl aus, um die automatische Registrierung mit Azure PowerShell zu deaktivieren: 

```powershell-interactive
Unregister-AzProviderFeature -FeatureName BulkRegistration -ProviderNamespace Microsoft.SqlVirtualMachine
```

---

## <a name="enable-for-multiple-subscriptions"></a>Aktivieren für mehrere Abonnements

Sie können die automatische Registrierungsfunktion mit PowerShell für mehrere Azure-Abonnements aktivieren. 

Gehen Sie dazu folgendermaßen vor:

1. Speichern Sie [dieses Skript](https://github.com/microsoft/tigertoolbox/blob/master/AzureSQLVM/RegisterSubscriptionsToSqlVmAutomaticRegistration.ps1) in einer `.ps1`-Datei, z. B. `EnableBySubscription.ps1`. 
1. Navigieren Sie in einem administrativen Eingabeaufforderungs- oder PowerShell-Fenster zu dem Speicherort, in dem Sie das Skript gespeichert haben. 
1. Stellen Sie eine Verbindung mit Azure her `az login`.
1. Führen Sie das Skript aus, wobei Sie die Abonnement-IDs (SubscriptionIds) wie folgt als Parameter übergeben:   
   `.\EnableBySubscription.ps1 -SubscriptionList SubscriptionId1,SubscriptionId2`

   Beispiel: 

   ```console
   .\EnableBySubscription.ps1 -SubscriptionList a1a1a-aa11-11aa-a1a1-a11a111a1,b2b2b2-bb22-22bb-b2b2-b2b2b2bb
   ```

Fehler bei der Registrierung werden in der Datei `RegistrationErrors.csv` gespeichert, die sich in dem Verzeichnis befindet, in dem Sie das `.ps1`-Skript gespeichert und ausgeführt haben. 

## <a name="next-steps"></a>Nächste Schritte

Aktualisieren Sie den Verwaltbarkeitsmodus auf [Vollständig](sql-agent-extension-manually-register-single-vm.md#upgrade-to-full), um alle von der SQL-IaaS-Agent-Erweiterung bereitgestellten Funktionen nutzen zu können. 
