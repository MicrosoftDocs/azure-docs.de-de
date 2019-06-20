---
title: include file
description: include file
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/22/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 0cfa0fdb51969c92e767adfa86a0065d11da56e2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66237746"
---
[Azure Files](../articles/storage/files/storage-files-introduction.md) unterstützt identitätsbasierte Authentifizierung über SMB (Server Message Block) (Vorschau) über [Azure Active Directory (Azure AD) Domain Services](../articles/active-directory-domain-services/overview.md). Ihre in die Domäne eingebundenen virtuellen Windows-Computer (VMs) können mit [Azure AD](../articles/active-directory/fundamentals/active-directory-whatis.md)-Anmeldeinformationen auf Azure-Dateifreigaben zugreifen. 

Azure AD authentifiziert, eine Identität, z. B. einen Benutzer, eine Gruppe oder einen Dienstprinzipal, mittels [rollenbasierter Zugriffssteuerung (RBAC)](../articles/role-based-access-control/overview.md). Sie können benutzerdefinierte RBAC-Rollen definieren, die gängige Berechtigungssätze für den Zugriff auf Azure Files enthalten. Wenn Sie Ihre benutzerdefinierte RBAC-Rolle einer Azure AD-Identität zuweisen, erhält diese Identität Zugriff auf eine Azure-Dateifreigabe gemäß diesen Berechtigungen.

Als Teil der Preview unterstützt Azure Files auch die Beibehaltung, Vererbung und Durchsetzung von [NTFS DACLs](https://technet.microsoft.com/library/2006.01.howitworksntfs.aspx) für alle Dateien und Verzeichnisse in einer Dateifreigabe. Wenn Sie Daten von einer Dateifreigabe in Azure Files kopieren, oder umgekehrt, können Sie angeben, dass NTFS DACLs beibehalten werden sollen. Auf diese Weise können Sie Sicherungsszenarien unter Verwendung von Azure Files implementieren, wobei Ihre NTFS DACLS zwischen Ihrer lokalen Dateifreigabe und Ihrer Clouddateifreigabe erhalten bleiben. 

> [!NOTE]
> - Azure AD-Authentifizierung über SMB wird in der Preview-Version nicht für Linux-VMs unterstützt. Nur Windows Server-VMs werden unterstützt.
> - Azure AD-Authentifizierung über SMB wird nicht für lokale Computer unterstützt, die auf Azure Files zugreifen.
> - Azure AD-Authentifizierung ist nur für Speicherkonten verfügbar, die nach dem 24. September 2018 erstellt wurden.
> - Azure AD-Authentifizierung über SMB und persistente NTFS-Zugriffssteuerungsliste wird auf Azure-Dateifreigaben, die vom Azure-Dateisynchronisierungsdienst verwaltet werden, nicht unterstützt. 
