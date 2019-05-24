---
title: Includedatei
description: Includedatei
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 07/08/2018
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: 359347e41264711a6ac0fa4d2dd0c3633590e917
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66159886"
---
Dieser Fehler kann auftreten, wenn der Zugriff auf den Azure-Dateisynchronisierungsdienst vom Server aus nicht verfügbar ist. Sie können diesen Fehler in folgenden Schritte beheben:

1. Stellen Sie sicher, dass der Windows-Dienst `FileSyncSvc.exe` nicht von der Firewall blockiert wird.
2. Stellen Sie sicher, dass Port 443 für ausgehende Verbindungen mit dem Azure-Dateisynchronisierungsdienst geöffnet ist. Hierfür können Sie das `Test-NetConnection`-Cmdlet verwenden. Die URL für den `<azure-file-sync-endpoint>`-Platzhalter unten finden Sie im Dokument [Proxy- und Firewall-Einstellungen der Azure-Dateisynchronisierung](../articles/storage/files/storage-sync-files-firewall-and-proxy.md#firewall). 

    ```powershell
    Test-NetConnection -ComputerName <azure-file-sync-endpoint> -Port 443
    ```

3. Stellen Sie sicher, dass die Proxykonfiguration wie erwartet festgelegt ist. Verwenden Sie dazu das `Get-StorageSyncProxyConfiguration`-Cmdlet. Weitere Informationen zur Proxykonfiguration für die Azure-Dateisynchronisierung finden Sie unter [Proxy- und Firewall-Einstellungen der Azure-Dateisynchronisierung](../articles/storage/files/storage-sync-files-firewall-and-proxy.md#firewall).

    ```powershell
    $agentPath = "C:\Program Files\Azure\StorageSyncAgent"
    Import-Module "$agentPath\StorageSync.Management.ServerCmdlets.dll"
    Get-StorageSyncProxyConfiguration
    ```
    
4. Wenden Sie sich an Ihren Netzwerkadministrator, um weitere Unterstützung zur Problembehandlung bei Netzwerkverbindungen zu erhalten.