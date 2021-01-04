---
title: include file
description: include file
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 09/16/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e2a1eb8524c9d9a4b0ae603da3c05fbb20900111
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95995474"
---
SMB Multichannel für Azure-Dateifreigaben weist derzeit die folgenden Einschränkungen auf:
- Kann nur mit lokal redundanten FileStorage-Konten verwendet werden.
- Wird nur für Windows-Clients unterstützt. 
- Maximale Anzahl von Kanälen ist vier.
- SMB Direct wird nicht unterstützt.
- Bei Speicherkonten mit [ identitätsbasierter Authentifizierung](../articles/storage/files/storage-files-active-directory-overview.md) von Windows Server Active Directory-Domänendiensten (AD DS) oder Azure AD DS, die für Azure Files aktiviert sind, könnten SMB-Clients nicht den Datei-Explorer von Windows verwenden, um NTFS-Berechtigungen für Verzeichnisse und Dateien zu konfigurieren.
    - Verwenden Sie stattdessen das Windows-Tool [icacls](/windows-server/administration/windows-commands/icacls) oder den Befehl [Set-ACL](/powershell/module/microsoft.powershell.security/set-acl?view=powershell-7), um Berechtigungen zu konfigurieren.

