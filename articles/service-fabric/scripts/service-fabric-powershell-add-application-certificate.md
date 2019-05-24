---
title: Skriptbeispiel von Azure PowerShell – Hinzufügen eines Anwendungszertifikats zu einem Cluster | Microsoft-Dokumentation
description: Skriptbeispiel von Azure PowerShell – Hinzufügen eines Anwendungszertifikats zu einem Cluster
services: service-fabric
documentationcenter: ''
author: aljo-microsoft
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 01/18/2018
ms.author: aljo
ms.custom: mvc
ms.openlocfilehash: 313a1b7e4f0109a0a5cb94214918d97c25b5cf5d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66153849"
---
# <a name="add-an-application-certificate-to-a-service-fabric-cluster"></a>Fügen Sie ein Anwendungszertifikat zu einem Service Fabric-Cluster hinzu

In diesem Beispielskript wird ein selbst signiertes Zertifikat im angegebenen Azure Key Vault erstellt, und es wird auf allen Knoten des Service Fabric-Clusters installiert. Das Zertifikat wird außerdem in einen lokalen Ordner heruntergeladen. Der Name des heruntergeladenen Zertifikats ist identisch mit dem Namen des Zertifikats im Schlüsseltresor. Passen Sie die Parameter nach Bedarf an.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Installieren Sie bei Bedarf Azure PowerShell mithilfe der Anleitung im [Azure PowerShell-Handbuch](/powershell/azure/overview), und führen Sie dann `Connect-AzAccount` aus, um eine Verbindung mit Azure herzustellen. 

## <a name="sample-script"></a>Beispielskript

[!code-powershell[main](../../../powershell_scripts/service-fabric/add-application-certificate/add-new-application-certificate.ps1 "Add an application certificate to a cluster")]

## <a name="script-explanation"></a>Erläuterung des Skripts

Das Skript verwendet die folgenden Befehle: Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Get-Help | Notizen |
|---|---|
| [Add-AzServiceFabricApplicationCertificate](/powershell/module/az.servicefabric/Add-azServiceFabricApplicationCertificate) | Fügen Sie den VM-Skalierungsgruppen, die den Cluster bilden, ein neues Anwendungszertifikat hinzu.  |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Azure PowerShell-Modul finden Sie in der [Azure PowerShell-Dokumentation](/powershell/azure/overview).

Zusätzliche Azure PowerShell-Beispiele für Azure Service Fabric finden Sie unter [Azure PowerShell-Beispiele](../service-fabric-powershell-samples.md).
