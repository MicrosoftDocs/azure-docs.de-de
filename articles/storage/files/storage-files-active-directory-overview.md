---
title: 'Übersicht: identitätsbasierte Autorisierung mit Azure Files'
description: Azure Files unterstützt die identitätsbasierte Authentifizierung mit SMB (Server Message Block) über Azure Active Directory Domain Services (AD DS) und Active Directory. Ihre in die Domäne eingebundenen virtuellen Windows-Computer (VMs) können dann mit Azure AD-Anmeldeinformationen auf Azure-Dateifreigaben zugreifen.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 05/29/2020
ms.author: rogarana
ms.openlocfilehash: 0493b66928d944e251e6fe4abda4f1d3d8fb7e23
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94695893"
---
# <a name="overview-of-azure-files-identity-based-authentication-options-for-smb-access"></a>Übersicht über die Optionen der identitätsbasierten Authentifizierung mit Azure Files für den SMB-Zugriff
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Informationen dazu, wie Sie die Authentifizierung mit lokalen Active Directory Domain Services für Azure-Dateifreigaben aktivieren, finden Sie unter [Aktivieren der Authentifizierung mit lokalen Active Directory Domain Services über SMB für Azure-Dateifreigaben](storage-files-identity-auth-active-directory-enable.md).

Informationen zum Aktivieren der Azure AD DS-Authentifizierung für Azure-Dateifreigaben finden Sie unter [Aktivieren der Azure Active Directory Domain Services-Authentifizierung in Azure Files](storage-files-identity-auth-active-directory-domain-service-enable.md).

## <a name="glossary"></a>Glossar 
Sie sollten die folgenden Begriffe im Zusammenhang mit der Azure AD Domain Services-Authentifizierung über SMB für Azure-Dateifreigaben kennen:

-   **Kerberos-Authentifizierung**

    Kerberos ist ein Authentifizierungsprotokoll, das zum Überprüfen der Identität eines Benutzers oder Hosts verwendet wird. Weitere Informationen zu Kerberos finden Sie unter [Kerberos-Authentifizierung: Übersicht](/windows-server/security/kerberos/kerberos-authentication-overview).

-  **Server Message Block-Protokoll (SMB)**

    SMB ist ein Netzwerkprotokoll zur Dateifreigabe nach Industriestandard. SMB ist auch bekannt als Common Internet File System oder CIFS. Weitere Informationen zu SMB finden Sie unter [Microsoft SMB-Protokoll und CIFS-Protokoll (Übersicht)](/windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview).

-   **Azure Active Directory (Azure AD)**

    Azure Active Directory (Azure AD) ist der mehrinstanzenfähige cloudbasierte Verzeichnis- und Identitätsverwaltungsdienst von Microsoft. Azure AD kombiniert grundlegende Verwaltungsdienste, Zugriffsverwaltung für Anwendungen und Identitätsgovernance in einer einzigen Lösung. In Azure AD eingebundene virtuelle Windows-Computer (VMs) können mit Ihren Azure AD-Anmeldeinformationen nicht auf Azure-Dateifreigaben zugreifen. Weitere Informationen finden Sie unter [Was ist Azure Active Directory?](../../active-directory/fundamentals/active-directory-whatis.md).

-   **Azure Active Directory Domain Services (Azure AD DS)**

    Azure AD DS stellt verwaltete Domänendienste bereit, z. B. den Domänenbeitritt, Gruppenrichtlinien, LDAP und die Kerberos/NTLM-Authentifizierung. Diese Dienste sind vollständig kompatibel mit Active Directory Domain Services. Weitere Informationen finden Sie unter [Azure Active Directory Domain Services](../../active-directory-domain-services/overview.md).

- **Lokale Active Directory Domain Services (AD DS)**

    Durch die Integration von lokalen Active Directory Domain Services (AD DS) und Azure Files werden Methoden zum Speichern von Verzeichnisdaten bereitgestellt und die Daten Netzwerkbenutzern und Administratoren zur Verfügung gestellt. Sicherheit ist in AD DS über die Anmeldeauthentifizierung und die Zugriffssteuerung für Objekte im Verzeichnis integriert. Mit einer einzigen Netzwerkanmeldung können Administratoren Verzeichnisdaten und die Organisation im gesamten Netzwerk verwalten, und autorisierte Netzwerkbenutzer können überall im Netzwerk auf Ressourcen zugreifen. AD DS wird häufig von Unternehmen in lokalen Umgebungen eingeführt, und AD DS-Anmeldeinformationen werden als Identität für die Zugriffssteuerung verwendet. Weitere Informationen finden Sie unter [Active Directory Domain Services – Übersicht](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview).

-   **Rollenbasierte Zugriffssteuerung von Azure (Azure RBAC)**

    Die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) in Azure ermöglicht eine präzise Zugriffsverwaltung für Azure. Mit Azure RBAC können Sie den Zugriff auf Ressourcen verwalten, indem Sie Benutzern die Mindestberechtigungen zum Ausführen ihrer Aufgaben erteilen. Weitere Informationen zu Azure RBAC finden Sie unter [Was ist die rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC)?](../../role-based-access-control/overview.md).

## <a name="common-use-cases"></a>Gängige Anwendungsfälle

Die identitätsbasierte Authentifizierung und Unterstützung für Windows-ACLs in Azure Files eignet sich besonders für die folgenden Anwendungsfälle:

### <a name="replace-on-premises-file-servers"></a>Ersatz für lokale Dateiserver

Das als veraltet Markieren und Ersetzen weit verteilter lokaler Dateiserver ist ein häufiges Problem, auf das jedes Unternehmen bei der IT-Modernisierung stößt. Hier sind Azure-Dateifreigaben mit der Authentifizierung mit lokalen AD DS am besten geeignet, sofern Sie die Daten zu Azure Files migrieren können. Eine komplette Migration ermöglicht es Ihnen, die Vorteile der Hochverfügbarkeit und Skalierbarkeit zu nutzen und gleichzeitig clientseitige Änderungen zu minimieren. Sie bietet eine nahtlose Migration für Endbenutzer, sodass sie weiterhin mit denselben Anmeldeinformationen und über ihre vorhandenen, in die Domäne eingebundenen Computer auf ihre Daten zugreifen können.

### <a name="lift-and-shift-applications-to-azure"></a>Lift & Shift von Anwendungen in Azure

Wenn Sie Anwendungen per Lift & Shift in die Cloud verlagern, soll das Authentifizierungsmodell für Ihre Daten beibehalten werden. Da wir die identitätsbasierte Zugriffssteuerung auf Azure-Dateifreigaben ausweiten, entfällt die Notwendigkeit, Ihre Anwendung auf moderne Authentifizierungsmethoden umzustellen und die Cloudeinführung zu beschleunigen. Azure-Dateifreigaben bieten die Möglichkeit, Azure AD DS oder lokale AD DS für die Authentifizierung zu integrieren. Wenn Sie vorhaben, vollumfänglich cloudnativ zu werden, und minimalen Aufwand für die Verwaltung von Cloudinfrastrukturen betreiben möchten, ist Azure AD DS als vollständig verwalteter Domänendienst besser geeignet. Wenn Sie vollständige Kompatibilität mit den AD DS-Funktionen benötigen, sollten Sie in Erwägung ziehen, Ihre AD DS-Umgebung durch das Selbsthosting von Domänencontrollern auf VMs in die Cloud zu erweitern. In beiden Fällen bieten wir Ihnen die Flexibilität, die Domänendienste entsprechend Ihren Geschäftsanforderungen auszuwählen.

### <a name="backup-and-disaster-recovery-dr"></a>Sicherung und Notfallwiederherstellung

Wenn Sie Ihren primären Dateispeicher lokal betreiben, können Azure-Dateifreigaben einen idealen Speicher für die Sicherung oder Notfallwiederherstellung darstellen, um die Geschäftskontinuität zu verbessern. Sie können Azure-Dateifreigaben verwenden, um Ihre Daten von vorhandenen Dateiservern zu sichern und dabei Windows-DACLs beizubehalten. Für Notfallwiederherstellungsszenarien können Sie eine Authentifizierungsoption konfigurieren, um bei einem Failover die ordnungsgemäße Durchsetzung der Zugriffssteuerung zu unterstützen.

## <a name="supported-scenarios"></a>Unterstützte Szenarios

In der folgenden Tabelle werden die unterstützten Authentifizierungsszenarien für Azure-Dateifreigaben für Azure AD DS und lokale AD DS zusammengefasst. Es wird empfohlen, den Domänendienst auszuwählen, den Sie für Ihre Clientumgebung für die Integration mit Azure Files eingeführt haben. Wenn Sie AD DS bereits lokal oder in Azure eingerichtet haben und Ihre Geräte in die AD-Domäne eingebunden sind, sollten Sie sich für die Authentifizierung von Azure-Dateifreigaben mit AD DS entscheiden. Wenn Sie Azure AD DS bereits eingeführt haben, sollten Sie dies auch für die Authentifizierung von Azure-Dateifreigaben verwenden.


|Azure AD DS-Authentifizierung  | Lokale Azure AD DS-Authentifizierung  |
|---------|---------|
|Ihre in Azure AD DS eingebundenen Windows-Computer können mit Azure AD-Anmeldeinformationen über SMB auf Azure-Dateifreigaben zugreifen.     |In lokale AD DS oder Azure AD DS eingebundene Windows-Computer können mit Anmeldeinformationen für lokale AD DS, die über SMB mit Azure AD synchronisiert werden, auf Azure-Dateifreigaben zugreifen. Ihr Client muss über eine Sichtverbindung zu Ihrem AD DS verfügen.        |

### <a name="restrictions"></a>Beschränkungen

- Die Authentifizierung mit Azure AD DS und lokalen AD DS unterstützt keine Authentifizierung für Computerkonten. Stattdessen können Sie die Verwendung eines Dienstanmeldekontos in Erwägung ziehen.
- Für in Azure AD eingebundene Geräte oder Azure AD registrierte Geräte werden weder die Azure AD DS-Authentifizierung noch die lokale AD DS Authentifizierung unterstützt.
- Azure-Dateifreigaben unterstützen nur die identitätsbasierte Authentifizierung bei einem der folgenden Domänendienst, entweder bei [Azure Active Directory Domain Services (Azure AD DS)](#azure-ad-ds) oder einer [lokalen Active Directory Domain Services-Instanz (AD DS)](#ad-ds).
- Mit dem Netzwerkdateisystem (Network File System, NFS), das sich in der Vorschau befindet, wird keine der identitätsbasierten Authentifizierungsmethoden unterstützt.

## <a name="advantages-of-identity-based-authentication"></a>Vorteile der identitätsbasierten Authentifizierung
Die identitätsbasierte Authentifizierung für Azure Files bietet mehrere Vorteile gegenüber der Authentifizierung mit einem gemeinsam verwendeten Schlüssel:

-   **Erweitern des herkömmlichen, identitätsbasierten Dateifreigabezugriffs mit lokalen AD DS und Azure AD DS auf die Cloud**  
    Wenn Sie Ihre Anwendung per Lift & Shift in die Cloud migrieren und dabei herkömmliche Dateiserver durch Azure-Dateifreigaben ersetzen möchten, sollten Sie Ihre Anwendung mit Anmeldeinformationen für lokale AD DS oder Azure AD DS für den Zugriff auf Dateidaten authentifizieren lassen. Azure Files unterstützen Anmeldeinformationen für lokale AD DS oder Azure AD DS für den Zugriff auf Azure-Dateifreigaben über SMB von VMs aus, die in eine Domäne von lokalen AD DS oder von Azure AD DS eingebunden sind.

-   **Erzwingen einer differenzierten Zugriffssteuerung auf Azure-Dateifreigaben**  
    Sie können einer bestimmten Identität Berechtigungen auf der Freigabe-, Verzeichnis- oder Dateiebene zuweisen. Nehmen wir beispielsweise an, dass Sie über mehrere Teams verfügen, die eine einzelne Azure-Dateifreigabe für die Projektzusammenarbeit verwenden. Sie können allen Teams Zugriff auf nicht vertrauliche Verzeichnisse erteilen und dabei den Zugriff auf Verzeichnisse, die vertrauliche finanzielle Daten enthalten, auf Ihr Finanzteam beschränken. 

-   **Sichern von Windows-ACLs (auch als NTFS bezeichnet) zusammen mit Ihren Daten**  
    Sie können Azure-Dateifreigaben verwenden, um Ihre vorhandenen lokalen Dateifreigaben zu sichern. Azure Files behält Ihre ACLs zusammen mit Ihren Daten bei, wenn Sie eine Dateifreigabe auf Azure-Dateifreigaben über SMB sichern.

## <a name="how-it-works"></a>Funktionsweise

Azure-Dateifreigaben nutzen das Kerberos-Protokoll für die Authentifizierung mit lokalen AD DS- oder Azure AD DS-Instanzen. Wenn eine Identität, die mit einem Benutzer oder mit einer auf einem Client ausgeführten Anwendung verknüpft ist, versucht, auf Daten in Azure-Dateifreigaben zuzugreifen, wird die Anforderung zur Authentifizierung der Identität an den Domänendienst (entweder AD DS oder Azure AD DS) gesendet. Wenn die Authentifizierung erfolgreich ist, wird ein Kerberos-Token zurückgegeben. Der Client sendet eine Anforderung mit dem Kerberos-Token, und dieses Token wird von den Azure-Dateifreigaben zur Autorisierung der Anforderung verwendet. Azure-Dateifreigaben erhalten nur das Kerberos-Token, nicht die Anmeldeinformationen für den Zugriff.

Bevor Sie die identitätsbasierte Authentifizierung für Azure-Dateifreigaben aktivieren können, müssen Sie zunächst Ihre Domänenumgebung einrichten.

### <a name="ad-ds"></a>AD DS

Für die Authentifizierung mit lokalen AD DS müssen Sie Ihren AD-Domänencontroller einrichten und Ihre Computer oder VMs in die Domäne einbinden. Sie können Ihre Domänencontroller auf Azure-VMs oder lokal hosten. In jedem Fall müssen Ihre in die Domäne eingebundenen Clients über eine Sichtverbindung zu Ihrem Domänendienst verfügen, d. h. sie müssen sich im Unternehmensnetzwerk oder im virtuellen Netzwerk (VNET) Ihres Domänendiensts befinden.

Das folgende Diagramm stellt die lokale AD DS-Authentifizierung für Azure-Dateifreigaben über SMB dar. Die lokale AD DS-Instanz muss mithilfe Azure AD Connect-Synchronisierung mit Azure AD synchronisiert werden. Nur Hybridbenutzer, die sowohl in der lokalen AD DS-Instanz als auch in Azure AD vorhanden sind, können authentifiziert und für den Zugriff auf Azure-Dateienfreigaben autorisiert werden. Dies liegt daran, dass die Berechtigung auf Freigabeebene mit der in Azure AD dargestellten Identität konfiguriert ist, wo die Berechtigung auf Verzeichnis-/Dateibene damit in AD DS erzwungen wird. Stellen Sie sicher, dass Sie die Berechtigungen mit demselben Hybridbenutzer ordnungsgemäß konfigurieren.

:::image type="content" source="media/storage-files-active-directory-overview/Files-on-premises-AD-DS-Diagram.png" alt-text="Diagramm der lokalen AD DS-Authentifizierung für Azure-Dateifreigaben über SMB":::

### <a name="azure-ad-ds"></a>Azure AD DS

Bei der Azure AD DS-Authentifizierung sollten Sie Azure AD Domain Services aktivieren und die VMs, über die auf Dateidaten zugegriffen werden soll, in die Domäne einbinden. Ihre in die Domäne eingebundene VM muss sich im selben virtuellen Netzwerk (VNET) wie Azure AD DS befinden. 

Das folgende Diagramm stellt den Workflow für Azure AD DS-Authentifizierung für Azure-Dateifreigaben über SMB dar. Es folgt einem ähnlichen Muster wie die lokale AD DS-Authentifizierung für Azure-Dateifreigaben. Es gibt zwei wesentliche Unterschiede:

- Zunächst müssen Sie die Identität nicht in Azure AD DS erstellen, um das Speicherkonto darzustellen. Dies wird vom Aktivierungsprozess im Hintergrund vorgenommen.

- Zweitens können alle in Azure AD vorhandenen Benutzer authentifiziert und autorisiert werden. Dies können ausschließlich Cloud- oder Hybridbenutzer sein. Die Synchronisierung von Azure AD mit Azure AD DS wird von der Plattform verwaltet, ohne dass eine Benutzerkonfiguration erforderlich ist. Der Client muss zwar mit Azure AD DS in die Domäne eingebunden werden, kann jedoch nicht Azure AD verknüpft oder registriert werden. 

:::image type="content" source="media/storage-files-active-directory-overview/Files-Azure-AD-DS-Diagram.png" alt-text="Diagramm":::

### <a name="enable-identity-based-authentication"></a>Aktivieren der identitätsbasierten Authentifizierung

Sie können die identitätsbasierte Authentifizierung mit Azure AD DS oder lokalen AD DS für Azure-Dateifreigaben in Ihren neuen und vorhandenen Speicherkonten aktivieren. Nur ein Domänendienst kann für die Authentifizierung des Dateizugriffs im Speicherkonto verwendet werden. Dies betrifft dann alle Dateifreigaben im Konto. Eine ausführliche Anleitung zum Einrichten Ihrer Dateifreigaben für die Authentifizierung mit Azure AD DS finden Sie in unserem Artikel [Aktivieren der Azure Active Directory Domain Services-Authentifizierung für Azure Files](storage-files-identity-auth-active-directory-domain-service-enable.md), und eine Anleitung für lokale AD DS finden Sie in unserem weiteren Artikel [Aktivieren der Authentifizierung mit lokalen Active Directory Domain Services über SMB für Azure-Dateifreigaben](storage-files-identity-auth-active-directory-enable.md).

### <a name="configure-share-level-permissions-for-azure-files"></a>Konfigurieren von Freigabeberechtigungen für Azure Files

Sobald die Authentifizierung mit Azure AD DS oder lokalen AD DS aktiviert ist, können Sie integrierte Azure-Rollen für Azure AD-Identitäten verwenden oder benutzerdefinierte Rollen konfigurieren und allen Dateifreigaben in Ihren Speicherkonten Zugriffsrechte zuweisen. Mit der zugewiesenen Berechtigung kann die gewährte Identität ausschließlich auf die Freigabe zugreifen, aber nicht auf andere Elemente – auch nicht auf das Stammverzeichnis. Sie müssen immer noch separat Berechtigungen auf Verzeichnis- oder Dateiebene für Azure-Dateifreigaben konfigurieren.

### <a name="configure-directory-or-file-level-permissions-for-azure-files"></a>Konfigurieren von Berechtigungen auf Verzeichnis- oder Dateiebene für Azure Files

Azure-Dateifreigaben erzwingen Windows-Standarddateiberechtigungen sowohl auf Verzeichnis- als auch auf Dateiebene, einschließlich des Stammverzeichnisses. Die Konfiguration von Berechtigungen auf Verzeichnis- oder Dateiebene wird über SMB und REST unterstützt. Binden Sie die gewünschte Dateifreigabe von Ihrer VM aus ein, und konfigurieren Sie Berechtigungen mit dem Datei-Explorer unter Windows mit Windows-[icacls](/windows-server/administration/windows-commands/icacls) oder mit dem Befehl [Set-ACL](/powershell/module/microsoft.powershell.security/get-acl?view=powershell-6).

### <a name="use-the-storage-account-key-for-superuser-permissions"></a>Verwenden des Speicherkontoschlüssels für Superuserberechtigungen

Ein Benutzer, der den Speicherkontoschlüssel besitzt, kann mit Superuserberechtigungen auf Azure-Dateifreigaben zugreifen. Superuserberechtigungen umgehen alle Einschränkungen der Zugriffssteuerung.

> [!IMPORTANT]
> Die empfohlene bewährte Sicherheitsmaßnahme besteht darin, Ihre Speicherkontoschlüssel nicht weiterzugeben und nach Möglichkeit die identitätsbasierte Authentifizierung zu nutzen.

### <a name="preserve-directory-and-file-acls-when-importing-data-to-azure-file-shares"></a>Beibehalten von Verzeichnis- und Datei-Zugriffssteuerungslisten beim Importieren von Daten in Azure-Dateifreigaben

Azure Files unterstützt das Beibehalten von Verzeichnis- oder Datei-Zugriffssteuerungslisten beim Kopieren von Daten in Azure-Dateifreigaben. Sie können Zugriffssteuerungslisten für ein Verzeichnis oder eine Datei mit der Azure-Dateisynchronisierung oder gängigen Dateiverschiebungs-Toolsets in Azure-Dateifreigaben kopieren. Beispielsweise können Sie [robocopy](/windows-server/administration/windows-commands/robocopy) mit dem Flag `/copy:s` verwenden, um sowohl die Daten als auch die Zugriffssteuerungslisten in eine Azure-Dateifreigabe zu kopieren. Zugriffssteuerungslisten werden standardmäßig beibehalten. Sie müssen die identitätsbasierte Authentifizierung für Ihr Speicherkonto dazu nicht aktivieren.

## <a name="pricing"></a>Preise
Für das Aktivieren der identitätsbasierten Authentifizierung über SMB in Ihrem Speicherkonto fällt keine zusätzliche Servicegebühr an. Weitere Informationen zu den Preisen finden Sie unter [Azure Files – Preise](https://azure.microsoft.com/pricing/details/storage/files/) und [Azure AD Domain Services – Preise](https://azure.microsoft.com/pricing/details/active-directory-ds/).

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu Azure Files und zur identitätsbasierten Authentifizierung über SMB finden Sie in diesen Ressourcen:

- [Planung für eine Azure Files-Bereitstellung](storage-files-planning.md)
- [Aktivieren der Authentifizierung mit lokalen Active Directory Domain Services über SMB für Azure-Dateifreigaben](storage-files-identity-auth-active-directory-enable.md)
- [Aktivieren der Azure Active Directory Domain Services-Authentifizierung in Azure Files](storage-files-identity-auth-active-directory-domain-service-enable.md)
- [Häufig gestellte Fragen](storage-files-faq.md)