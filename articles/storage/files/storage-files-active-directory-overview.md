---
title: Übersicht zur Azure Active Directory-Authentifizierung über SMB für Azure Files (Vorschau) – Azure Storage
description: Azure Files unterstützt identitätsbasierte Authentifizierung über SMB (Server Message Block) (Vorschau) über Azure Active Directory (Azure AD) Domain Services. Ihre in die Domäne eingebundenen virtuellen Windows-Computer (VMs) können dann mit Azure AD-Anmeldeinformationen auf Azure-Dateifreigaben zugreifen.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 09/19/2018
ms.author: rogarana
ms.openlocfilehash: ad8ddf7e9e324bbcc48f15c95870a24fe7476828
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237766"
---
# <a name="overview-of-azure-active-directory-authentication-over-smb-for-azure-files-preview"></a>Übersicht zur Azure Active Directory-Authentifizierung über SMB für Azure Files (Vorschau)
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Wie Sie die Azure AD-Authentifizierung für Azure Files über SMB aktivieren, erfahren Sie unter [Aktivieren der Azure Active Directory-Authentifizierung über SMB für Azure Files (Vorschau)](storage-files-active-directory-enable.md).

## <a name="glossary"></a>Glossar 
Es ist hilfreich, einige wichtige Begriffe im Zusammenhang mit Azure AD-Authentifizierung über SMB für Azure Files zu verstehen:

-   **Azure Active Directory (Azure AD)**  
    Azure Active Directory (Azure AD) ist der mehrinstanzenfähige cloudbasierte Verzeichnis- und Identitätsverwaltungsdienst von Microsoft. Azure AD kombiniert grundlegende Verwaltungsdienste, Zugriffsverwaltung für Anwendungen und Identitätsgovernance in einer einzigen Lösung. Weitere Informationen finden Sie unter [Was ist Azure Active Directory?](../../active-directory/fundamentals/active-directory-whatis.md).

-   **Azure AD Domain Services**  
    Azure AD Domain Services stellen verwaltete Domänendienste bereit, z.B. Domänenbeitritt, Gruppenrichtlinie, LDAP und Kerberos/NTLM-Authentifizierung. Diese Dienste sind vollständig kompatibel mit Windows Server Active Directory. Weitere Informationen finden Sie unter [Azure Active Directory-Domänendienste (AD)](../../active-directory-domain-services/overview.md).

-   **Rollenbasierte Zugriffssteuerung in Azure (Role-Based Access Control, RBAC)**  
    Die rollenbasierte Access Control in Azure (RBAC) ermöglicht eine präzise Zugriffsverwaltung für Azure. Mit RBAC können Sie den Zugriff auf Ressourcen verwalten, indem Sie Benutzern die Mindestberechtigungen zum Ausführen ihrer Aufgaben erteilen. Weitere Informationen zu Rollen finden Sie unter [Was ist die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC)?](../../role-based-access-control/overview.md).

-   **Kerberos-Authentifizierung**

    Kerberos ist ein Authentifizierungsprotokoll, das zum Überprüfen der Identität eines Benutzers oder Hosts verwendet wird. Weitere Informationen zu Kerberos finden Sie unter [Kerberos-Authentifizierung: Übersicht](https://docs.microsoft.com/windows-server/security/kerberos/kerberos-authentication-overview).

-  **Server Message Block-Protokoll (SMB)**  
    SMB ist ein Netzwerkprotokoll zur Dateifreigabe nach Industriestandard. SMB ist auch bekannt als Common Internet File System oder CIFS. Weitere Informationen zu SMB finden Sie unter [Microsoft SMB-Protokoll und CIFS-Protokoll (Übersicht)](https://docs.microsoft.com/windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview).

## <a name="advantages-of-azure-ad-authentication"></a>Vorteile der Azure AD-Authentifizierung
Azure AD über SMB für Azure Files bietet mehrere Vorteile gegenüber der Verwendung der Authentifizierung mit gemeinsam verwendetem Schlüssel:

-   **Erweitern der herkömmlichen Dateifreigabe auf Identitätsbasis in die Cloud mit Azure AD**  
    Wenn Sie Ihre Anwendung mit „Lift & Shift“ in die Cloud verschieben und dabei herkömmliche Dateiserver mit Azure Files ersetzen möchten, dann sollten Sie Ihre Anwendung mit Azure AD zum Zugriff auf Dateidaten authentifizieren. Azure Files unterstützt das Verwenden von Azure AD-Anmeldeinformationen von in die Domäne eingebundenen virtuellen Computern aus per SMB zum Zugriff auf Dateifreigaben, Verzeichnisse oder Dateien. Sie können auch wahlweise alle Ihre lokalen Active Directory-Objekte mit Azure AD synchronisieren, um Benutzernamen, Kennwörter und andere Gruppenzuweisungen beizubehalten.

-   **Erzwingen einer differenzierten Zugriffssteuerung auf Azure-Dateifreigaben**  
    Mit Azure AD-Authentifizierung über SMB können Sie Berechtigungen für eine bestimmte Identität auf Freigabe-, Verzeichnis- oder Dateiebene gewähren. Nehmen wir beispielsweise an, dass Sie über mehrere Teams verfügen, die eine einzelne Azure-Dateifreigabe für die Projektzusammenarbeit verwenden. Sie können allen Teams Zugriff auf nicht vertrauliche Verzeichnisse erteilen und dabei den Zugriff auf Verzeichnisse, die vertrauliche finanzielle Daten enthalten, auf Ihr Finanzteam beschränken. 

-   **Sichern von ACLs zusammen mit Ihren Daten**  
    Sie können Azure Files verwenden, um Ihre vorhandenen lokalen Dateifreigaben zu sichern. Azure Files behält Ihre ACLs zusammen mit Ihren Daten bei, wenn Sie eine Dateifreigabe in Azure Files über SMB sichern.

## <a name="how-it-works"></a>So funktioniert's
Azure Files verwendet Azure AD Domain Services zur Unterstützung der Kerberos-Authentifizierung mit Azure AD-Anmeldeinformationen von in die Domäne eingebundenen virtuellen Computern aus. Bevor Sie Azure AD mit Azure Files verwenden können, müssen Sie zuerst Azure AD Domain Services aktivieren und von den VMs aus, von denen aus Sie auf Dateidaten zugreifen möchten, der Domäne beitreten. Ihr in die Domäne eingebundener virtueller Computer muss sich im selben virtuellen Netzwerk (VNET) wie Azure AD Domain Services befinden. 

Wenn eine Identität, die mit einer auf einem virtuellen Computer ausgeführten Anwendung verknüpft ist, versucht, in Azure Files auf Daten zuzugreifen, wird die Anforderung zur Authentifizierung der Identität an Azure AD Domain Services gesendet. Wenn die Authentifizierung erfolgreich ist, gibt Azure AD Domain Services ein Kerberos-Token zurück. Die Anwendung sendet eine Anforderung mit dem Kerberos-Token, und Azure Files verwendet dieses Token zur Autorisierung der Anforderung. Azure Files empfängt nur das Token und behält Azure AD-Anmeldeinformationen nicht bei.

![Screenshot mit Diagramm der Azure AD-Authentifizierung über SMB](media/storage-files-active-directory-overview/azure-active-directory-over-smb-for-files-overview.png)

### <a name="enable-azure-ad-authentication-over-smb"></a>Aktivieren der Azure AD-Authentifizierung per SMB
Sie können die Azure AD-Authentifizierung über SMB für Azure Files auf Ihren neuen und vorhandenen Speicherkonten, die nach dem 24. September 2018 erstellt wurden, aktivieren. 

Stellen Sie vor dem Aktivieren der Azure AD-Authentifizierung über SMB sicher, dass Azure AD Domain Services für den primären Azure AD-Mandanten bereitgestellt wurde, mit dem Ihr Speicherkonto verknüpft ist. Wenn Sie Azure AD Domain Services noch nicht eingerichtet haben, befolgen Sie die schrittweise Anleitung in [Aktivieren von Azure Active Directory Domain Services mithilfe des Azure-Portals](../../active-directory-domain-services/create-instance.md).

Die Bereitstellung von Azure AD Domain Services dauert normalerweise 10 bis 15 Minuten. Sobald Azure AD Domain Services bereitgestellt ist, können Sie die Azure AD-Authentifizierung für Azure Files über SMB aktivieren. Weitere Informationen finden Sie unter [Aktivieren der Azure Active Directory-Authentifizierung über SMB für Azure Files (Vorschau)](storage-files-active-directory-enable.md). 

### <a name="configure-share-level-permissions-for-azure-files"></a>Konfigurieren von Freigabeberechtigungen für Azure Files
Sobald die Azure AD-Authentifizierung aktiviert ist, können Sie benutzerdefinierte RBAC-Rollen für Azure AD-Identitäten konfigurieren und Zugriffsrechte auf alle Dateifreigaben im Speicherkonto zuweisen.

Wenn eine Anwendung, die auf einem in die Domäne eingebundenen virtuellen Computer ausgeführt wird, versucht, eine Azure-Dateifreigabe einzubinden, oder auf ein Verzeichnis oder eine Datei zuzugreifen, werden die Azure AD-Anmeldeinformationen der Anwendung überprüft, um sicherzustellen, dass die entsprechenden Freigabe- und NTFS-Berechtigungen vorliegen. Weitere Informationen zur Konfiguration von Freigabeberechtigungen finden Sie unter [Aktivieren der Azure Active Directory-Authentifizierung über SMB für Azure Files (Vorschau)](storage-files-active-directory-enable.md).

### <a name="configure-directory--or-file-level-permissions-for-azure-files"></a>Konfigurieren von Berechtigungen auf Verzeichnis- oder Dateiebene für Azure Files 
Azure Files erzwingt standardmäßige NTFS-Dateiberechtigungen auf Verzeichnis- und Dateiebene einschließlich des Stammverzeichnisses. Die Konfiguration von Berechtigungen auf Verzeichnis- oder Dateiebene wird nur über SMB unterstützt. Binden Sie die gewünschte Dateifreigabe von Ihrem virtuellen Computer aus ein, und konfigurieren Sie Berechtigungen mithilfe des Windows-Befehsl [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls) oder [Set-ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl). 

> [!NOTE]
> Konfigurieren von NTFS-Berechtigungen über den Windows-Datei-Explorer wird in der Vorschau nicht unterstützt.

### <a name="use-the-storage-account-key-for-superuser-permissions"></a>Verwenden des Speicherkontoschlüssels für Superuserberechtigungen 
Ein Benutzer, der den Speicherkontoschlüssel besitzt, kann mit Superuserberechtigungen auf Azure Files zugreifen. Superuserberechtigungen übertreffen alle auf Freigabeebene mit RBAC konfigurierten und von Azure AD erzwungenen Einschränkungen der Zugriffssteuerung. Superuserberechtigungen sind erforderlich, um eine Azure-Dateifreigabe einzubinden. 

> [!IMPORTANT]
> Vermeiden Sie im Rahmen der Best Practices für Sicherheit die Freigabe Ihrer Speicherkontoschlüssel, und nutzen Sie Azure AD-Berechtigungen, wann immer möglich.

### <a name="preserve-directory-and-file-acls-for-data-import-to-azure-file-shares"></a>Beibehalten von Verzeichnis- und Dateizugriffssteuerungslisten für den Datenimport in Azure-Dateifreigaben
Azure AD-Authentifizierung über SMB unterstützt das Beibehalten von Verzeichnis oder Datei-ACLs beim Kopieren von Daten in Azure-Dateifreigaben. In der Vorschauversion können Sie die ACLs für ein Verzeichnis oder eine Datei in Azure Files kopieren. Beispielsweise können Sie [robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) mit dem Flag `/copy:s` verwenden, um sowohl die Daten als auch die ACLs in eine Azure-Dateifreigabe zu kopieren.

## <a name="pricing"></a>Preise
Für das Aktivieren der Azure AD-Authentifizierung über SMB in Ihrem Speicherkonto fällt keine zusätzliche Servicegebühr an. Weitere Informationen zu den Preisen finden Sie auf den Seiten [Azure Files – Preise](https://azure.microsoft.com/pricing/details/storage/files/) und [Azure Active Directory Domain Services – Preise ](https://azure.microsoft.com/pricing/details/active-directory-ds/).

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu Azure Files und zur Azure AD-Authentifizierung über SMB finden Sie in diesen Ressourcen:

- [Einführung in Azure Files](storage-files-introduction.md)
- [Aktivieren der Azure Active Directory-Authentifizierung über SMB für Azure Files (Vorschau)](storage-files-active-directory-enable.md)
- [Häufig gestellte Fragen](storage-files-faq.md)
