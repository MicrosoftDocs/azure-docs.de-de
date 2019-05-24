---
title: Azure PowerShell-Skriptbeispiel – Erstellen eines Service Fabric-Clusters | Microsoft-Dokumentation
description: Azure PowerShell-Skriptbeispiel – Erstellen eines Service Fabric-Clusters.
services: service-fabric
documentationcenter: ''
author: aljo-microsoft
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: 0f9c8bc5-3789-4eb3-8deb-ae6e2200795a
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 01/19/2018
ms.author: aljo
ms.custom: mvc
ms.openlocfilehash: 4693117e28f23e4dc94e4edbe96e949080ad2cce
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66161972"
---
# <a name="create-a-service-fabric-cluster"></a>Erstellen von Service Fabric-Clustern

Dieses Beispielskript erstellt einen Service Fabric-Cluster mit fünf Knoten, der mit einem X.509-Zertifikat geschützt wird.  Der Befehl erstellt ein selbstsigniertes Zertifikat und lädt es in einen neuen Key Vault hoch. Das Zertifikat wird außerdem in ein lokales Verzeichnis kopiert.  Legen Sie den *-OS*-Parameter fest, um die Version von Windows oder Linux auszuwählen, in der die Clusterknoten ausgeführt werden.  Passen Sie die Parameter nach Bedarf an.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Installieren Sie bei Bedarf Azure PowerShell mithilfe der Anleitung im [Azure PowerShell-Handbuch](/powershell/azure/overview), und führen Sie dann `Connect-AzAccount` aus, um eine Verbindung mit Azure herzustellen. 

## <a name="sample-script"></a>Beispielskript

[!code-powershell[main](../../../powershell_scripts/service-fabric/create-secure-cluster/create-secure-cluster.ps1 "Create a Service Fabric cluster")]

## <a name="clean-up-deployment"></a>Bereinigen der Bereitstellung 

Nach dem Ausführen des Skriptbeispiels können mit dem folgenden Befehl die Ressourcengruppe, der Cluster und alle zugehörigen Ressourcen entfernt werden.

```powershell
$groupname="mysfclustergroup"
Remove-AzResourceGroup -Name $groupname -Force
```

## <a name="script-explanation"></a>Erläuterung des Skripts

Das Skript verwendet die folgenden Befehle. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Get-Help | Notizen |
|---|---|
| [New-AzServiceFabricCluster](/powershell/module/az.servicefabric/New-azServiceFabricCluster) | Erstellt einen neuen Service Fabric-Cluster. |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Azure PowerShell-Modul finden Sie in der [Azure PowerShell-Dokumentation](/powershell/azure/overview).

Zusätzliche Azure PowerShell-Beispiele für Azure Service Fabric finden Sie unter [Azure PowerShell-Beispiele](../service-fabric-powershell-samples.md).
