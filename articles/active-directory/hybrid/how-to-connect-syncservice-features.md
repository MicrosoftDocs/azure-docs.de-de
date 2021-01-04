---
title: Features und Konfiguration des Azure AD Connect-Synchronisierungsdiensts | Microsoft Docs
description: Beschreibt die dienstseitigen Features des Azure AD Connect-Synchronisierungsdiensts.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 213aab20-0a61-434a-9545-c4637628da81
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/18/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 261ab5d0f039705a2566b7c28ff4c06778bb661a
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94410537"
---
# <a name="azure-ad-connect-sync-service-features"></a>Features des Azure AD Connect-Synchronisierungsdiensts

Das Synchronisierungsfeature von Azure AD Connect besteht aus zwei Komponenten:

* Der lokalen Komponente **Azure AD Connect-Synchronisierung** , die auch als **Synchronisierungsmodul** bezeichnet wird.
* Dem Service in Azure AD, der auch als **Azure AD Connect-Synchronisierungsdienst**

In diesem Thema wird erläutert, wie die folgenden Features des **Azure AD Connect-Synchronisierungsdiensts** funktionieren und wie Sie diese mit Windows PowerShell konfigurieren können.

Diese Einstellungen werden vom [Azure Active Directory-Modul für Windows PowerShell](/previous-versions/azure/jj151815(v=azure.100))konfiguriert. Laden Sie es herunter, und installieren Sie es separat von Azure AD Connect. Die in diesem Thema dokumentierten Cmdlets wurden in der [Version von März 2016 (Build 9031.1)](https://social.technet.microsoft.com/wiki/contents/articles/28552.microsoft-azure-active-directory-powershell-module-version-release-history.aspx#Version_9031_1)eingeführt. Wenn Sie nicht über die in diesem Thema dokumentierten Cmdlets verfügen oder diese nicht zu den gleichen Ergebnissen führen, stellen Sie sicher, dass Sie die neueste Version ausführen.

Führen Sie zum Anzeigen der Konfiguration in Ihrem Azure AD-Verzeichnis `Get-MsolDirSyncFeatures`aus.  
![Get-MsolDirSyncFeatures – Ergebnis](./media/how-to-connect-syncservice-features/getmsoldirsyncfeatures.png)

Viele dieser Einstellungen können nur von Azure AD Connect geändert werden.

Folgende Einstellungen können durch `Set-MsolDirSyncFeature`konfiguriert werden:

| DirSyncFeature | Comment |
| --- | --- |
| [EnableSoftMatchOnUpn](#userprincipalname-soft-match) |Ermöglicht Objekten den Beitritt zu userPrincipalName zusätzlich zur primären SMTP-Adresse. |
| [SynchronizeUpnForManagedUsers](#synchronize-userprincipalname-updates) |Ermöglicht dem Synchronisierungsmodul die Aktualisierung des userPrincipalName-Attributs für verwaltete/lizenzierte Benutzer (keine Verbundbenutzer). |

Nachdem Sie ein Feature aktiviert haben, kann es nicht wieder deaktiviert werden.

> [!NOTE]
> Seit dem 24. August 2016 ist das Feature *Resilienz bei doppelten Attributen* für neue Azure AD-Verzeichnisse standardmäßig aktiviert. Dieses Feature wird auch für Verzeichnisse freigegeben und aktiviert, die vor diesem Datum erstellt wurden. Sie erhalten eine E-Mail-Benachrichtigung, wenn dieses Feature für Ihr Verzeichnis aktiviert wird.
> 
> 

Die folgenden Einstellungen werden von Azure AD Connect konfiguriert und können von `Set-MsolDirSyncFeature` nicht geändert werden:

| DirSyncFeature | Comment |
| --- | --- |
| DeviceWriteback |[Azure AD Connect: Geräterückschreiben aktivieren](how-to-connect-device-writeback.md) |
| DirectoryExtensions |[Azure AD Connect-Synchronisierung: Verzeichniserweiterungen](how-to-connect-sync-feature-directory-extensions.md) |
| [DuplicateProxyAddressResiliency<br/>DuplicateUPNResiliency](#duplicate-attribute-resiliency) |Ermöglicht es, ein Attribut unter Quarantäne zu stellen, falls es sich um ein Duplikat eines anderen Objekts handelt, damit nicht der Export des gesamten Objekts mit einem Fehler abgebrochen wird. |
| Kennworthashsynchronisierung |[Implementieren der Kennworthashsynchronisierung mit der Azure AD Connect-Synchronisierung](how-to-connect-password-hash-synchronization.md) |
|Passthrough-Authentifizierung|[Benutzeranmeldung mit der Azure Active Directory-Passthrough-Authentifizierung](how-to-connect-pta.md)|
| UnifiedGroupWriteback |Gruppenrückschreiben|
| UserWriteback |Derzeit nicht unterstützt. |

## <a name="duplicate-attribute-resiliency"></a>Resilienz bei doppelten Attributen

Damit die Bereitstellung von Objekten mit doppelten UPN-/proxyAddresses-Werten nicht mit einem Fehler abgebrochen wird, wird das doppelte Attribut unter Quarantäne gestellt, und ein temporärer Wert wird zugewiesen. Wenn der Konflikt gelöst ist, wird der temporäre UPN-Wert automatisch auf den richtigen Wert gesetzt. Weitere Informationen finden Sie unter [Identitätssynchronisierung und Resilienz bei doppelten Attributen](how-to-connect-syncservice-duplicate-attribute-resiliency.md).

## <a name="userprincipalname-soft-match"></a>UserPrincipalName-„Soft Match“

Wenn dieses Feature aktiviert ist, wird ein „Soft Match“ für den UPN sowie für die [primäre SMTP-Adresse](https://support.microsoft.com/kb/2641663)aktiviert, die immer aktiviert ist. Ein „Soft Match“ wird verwendet, um vorhandene Cloudbenutzer in Azure AD mit lokalen Benutzern abzugleichen.

Dieses Feature ist besonders hilfreich, wenn Sie lokale AD-Konten mit vorhandenen in der Cloud erstellten Konten abgleichen müssen und Exchange Online nicht verwenden. In diesem Szenario gibt es im Allgemeinen keinen Grund dafür, das SMTP-Attribut in der Cloud festzulegen.

Dieses Feature ist standardmäßig für neu erstellte Azure AD-Verzeichnisse aktiviert. Sie können ermitteln, ob das Feature aktiviert ist, indem Sie Folgendes ausführen:  

```powershell
Get-MsolDirSyncFeatures -Feature EnableSoftMatchOnUpn
```

Wenn dieses Feature für Ihr Azure AD-Verzeichnis nicht aktiviert ist, können Sie es aktivieren, indem Sie Folgendes ausführen:  

```powershell
Set-MsolDirSyncFeature -Feature EnableSoftMatchOnUpn -Enable $true
```

## <a name="synchronize-userprincipalname-updates"></a>Synchronisieren von userPrincipalName-Updates

Früher wurden Updates des UserPrincipalName-Attributs mithilfe des Synchronisierungsdiensts vom lokalen Standort blockiert, sofern nicht die beiden folgenden Bedingungen erfüllt waren:

* Der Benutzer ist verwaltet (kein Verbundbenutzer).
* Dem Benutzer wurde keine Lizenz zugewiesen.

> [!NOTE]
> Seit März 2019 ist das Synchronisieren von UPN-Änderungen für Verbundbenutzerkonten zulässig.
> 

Durch die Aktivierung dieses Features kann die Synchronisierungsengine den userPrincipalName aktualisieren, wenn dieser lokal geändert wird und Sie die Kennworthashsynchronisierung oder Pass-Through-Authentifizierung verwenden.

Dieses Feature ist standardmäßig für neu erstellte Azure AD-Verzeichnisse aktiviert. Sie können ermitteln, ob das Feature aktiviert ist, indem Sie Folgendes ausführen:  

```powershell
Get-MsolDirSyncFeatures -Feature SynchronizeUpnForManagedUsers
```

Wenn dieses Feature für Ihr Azure AD-Verzeichnis nicht aktiviert ist, können Sie es aktivieren, indem Sie Folgendes ausführen:  

```powershell
Set-MsolDirSyncFeature -Feature SynchronizeUpnForManagedUsers -Enable $true
```

Nach Aktivierung dieses Features bleiben vorhandene userPrincipalName-Werte unverändert. Nach der nächsten lokalen Änderung des userPrincipalName-Attributs wird der UPN im Rahmen der normalen Deltasynchronisierung der Benutzer aktualisiert.  

## <a name="see-also"></a>Weitere Informationen

* [Azure AD Connect-Synchronisierung](how-to-connect-sync-whatis.md)
* [Integrieren Ihrer lokalen Identitäten in Azure Active Directory](whatis-hybrid-identity.md)