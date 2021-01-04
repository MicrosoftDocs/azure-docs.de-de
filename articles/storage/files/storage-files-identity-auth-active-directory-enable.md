---
title: Übersicht – lokale AD DS-Authentifizierung für Azure-Dateifreigaben
description: Erfahren Sie mehr zur Active Directory Domain Services-Authentifizierung (AD DS) für Azure-Dateifreigaben. Dieser Artikel behandelt Supportszenarien und Verfügbarkeit und erläutert, wie die Berechtigungen zwischen Ihrem AD DS und Azure Active Directory funktionieren.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 09/13/2020
ms.author: rogarana
ms.openlocfilehash: 2214dbc9dcbd4ba7728065ee45471e9f94b9e513
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95739994"
---
# <a name="overview---on-premises-active-directory-domain-services-authentication-over-smb-for-azure-file-shares"></a>Übersicht – lokale Active Directory Domain Services-Authentifizierung über SMB für Azure-Dateifreigaben

[Azure Files](storage-files-introduction.md)  unterstützt die identitätsbasierte Authentifizierung per SMB (Server Message Block) über zwei Arten von Domänendiensten: lokales Active Directory Domain Services (AD DS) und Azure Active Directory Domain Services (Azure AD DS). Es wird dringend empfohlen, den Abschnitt [Funktionsweise](./storage-files-active-directory-overview.md#how-it-works) zu lesen, damit Sie für die Authentifizierung den richtigen Domänendienst auswählen. Die Einrichtung unterscheidet sich je nach gewähltem Domänendienst. Diese Artikelreihe behandelt das Aktivieren und Konfigurieren von lokalen AD DS für die Authentifizierung mit Azure-Dateifreigaben.

Wenn Sie noch nicht mit Azure-Dateifreigaben vertraut sind, empfiehlt es sich, unsere [Planhinweisliste](storage-files-planning.md) zu lesen, bevor Sie die folgende Artikelreihe lesen.

## <a name="supported-scenarios-and-restrictions"></a>Unterstützte Szenarien und Einschränkungen

- AD DS-Identitäten, die für die lokale AD DS-Authentifizierung von Azure Files verwendet werden, müssen mit Azure AD synchronisiert sein. Kennworthashsynchronisierung ist optional. 
- Unterstützt von Azure-Dateisynchronisierung verwaltete Azure-Dateifreigaben.
- Unterstützt Kerberos-Authentifizierung mit AD mit RC4-HMAC- und [AES 256-Verschlüsselung](./storage-troubleshoot-windows-file-connection-problems.md#azure-files-on-premises-ad-ds-authentication-support-for-aes-256-kerberos-encryption). Die Unterstützung für die AES 256-Verschlüsselung ist zurzeit auf Speicherkonten beschränkt, deren Namen maximal 15 Zeichen lang sind. AES 128-Kerberos-Verschlüsselung wird noch nicht unterstützt.
- Unterstützt Einmaliges Anmelden.
- Wird nur auf Clients unterstützt, die auf Betriebssystemversionen nach Windows 7 oder Windows Server 2008 R2 ausgeführt werden.
- Wird nur für die AD-Gesamtstruktur unterstützt, in der das Speicherkonto registriert ist. Sie können auf Azure-Dateifreigaben standardmäßig nur mit den AD DS-Anmeldeinformationen einer einzelnen Gesamtstruktur zugreifen. Wenn Sie von einer anderen Gesamtstruktur aus auf Ihre Azure-Dateifreigabe zugreifen müssen, stellen Sie sicher, dass Sie die richtige Gesamtstrukturvertrauensstellung konfiguriert haben. Weitere Informationen finden Sie unter [FAQ](storage-files-faq.md#ad-ds--azure-ad-ds-authentication).
- Unterstützt nicht die Authentifizierung für Computerkonten, die in AD erstellt werden.
- Unterstützt keine Authentifizierung bei NFS-Dateifreigaben (Network File System).

Wenn Sie AD DS für Azure-Dateifreigaben über SMB aktivieren, können Ihre in AD DS eingebundenen Computer Azure-Dateifreigaben mithilfe Ihrer vorhandenen AD DS-Anmeldeinformationen bereitstellen. Diese Funktion kann für eine AD DS-Umgebung aktiviert werden, die entweder mit lokalen Computern oder in Azure gehostet wird.

> [!NOTE]
> Um Sie beim Einrichten der Azure Files-AD-Authentifizierung für gängige Anwendungsfälle zu unterstützen, haben wir zwei Videos mit Schrittanleitungen für die folgenden Szenarien veröffentlicht:
> - [Ersetzen lokaler Dateiserver durch Azure Files (einschließlich Einrichtung eines privaten Links für Dateien und AD-Authentifizierung)](https://sec.ch9.ms/ch9/3358/0addac01-3606-4e30-ad7b-f195f3ab3358/ITOpsTalkAzureFiles_high.mp4)
> - [Verwenden von Azure Files als Profilcontainer für Windows Virtual Desktop (einschließlich Einrichtung der AD-Authentifizierung und FsLogix-Konfiguration)](https://www.youtube.com/embed/9S5A1IJqfOQ)

## <a name="prerequisites"></a>Voraussetzungen 

Bevor Sie die AD DS-Authentifizierung für Azure-Dateifreigaben aktivieren, müssen Sie sicherstellen, dass die folgenden Voraussetzungen erfüllt sind: 

- Sie müssen Ihre [AD DS-Umgebung](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) auswählen und erstellen und über Azure AD Connect mit [Azure AD synchronisieren](../../active-directory/hybrid/how-to-connect-install-roadmap.md). 

    Sie können das Feature für eine neue oder eine vorhandene AD DS-Umgebung aktivieren. Die für den Zugriff verwendeten Identitäten müssen mit Azure AD synchronisiert werden. Der Azure AD-Mandant und die Dateifreigabe, auf die Sie zugreifen möchten, müssen dem gleichen Abonnement zugeordnet sein.

- Binden Sie einen lokalen Computer oder eine Azure-VM in eine lokale AD DS-Domäne ein. Weitere Informationen zum Domänenbeitritt finden Sie unter [Hinzufügen eines Computers zu einer Domäne](/windows-server/identity/ad-fs/deployment/join-a-computer-to-a-domain).

    Wenn Ihr Computer keiner AD DS-Domäne beigetreten ist, können Sie möglicherweise dennoch AD-Anmeldeinformationen für die Authentifizierung verwenden, wenn Ihr Computer über eine Sichtverbindung mit dem AD-Domänencontroller verfügt.

- Wählen Sie ein Azure-Speicherkonto, oder erstellen Sie eines.  Für optimale Leistung wird empfohlen, das Speicherkonto in derselben Region bereitzustellen wie der Client, von dem aus Sie auf die Freigabe zugreifen möchten. [Binden Sie dann die Azure-Dateifreigabe](storage-how-to-use-files-windows.md) mit dem Speicherkontoschlüssel ein. Durch die Einbindung mit dem Speicherkontoschlüssel wird die Konnektivität verifiziert.

    Stellen Sie sicher, dass das Speicherkonto mit den Dateifreigaben nicht bereits für Azure AD DS-Authentifizierung konfiguriert ist. Wenn die Azure Files-Azure AD DS-Authentifizierung für das Speicherkonto aktiviert ist, muss diese vor der Umstellung auf eine lokale AD DS-Authentifizierung deaktiviert werden. Dies bedeutet, dass vorhandene ACLs, die in der Azure AD DS-Umgebung konfiguriert sind, für die ordnungsgemäße Berechtigungserzwingung neu konfiguriert werden müssen.


    Wenn beim Herstellen einer Verbindung mit Azure Files Probleme auftreten, können Sie das [Problembehandlungstool verwenden, das für Azure Files-Einbindungsfehler unter Windows veröffentlicht wurde](https://azure.microsoft.com/blog/new-troubleshooting-diagnostics-for-azure-files-mounting-errors-on-windows/). Außerdem stehen [Anleitungen](./storage-files-faq.md#on-premises-access) zur Umgehung von Szenarien bereit, wenn Port 445 blockiert ist. 


- Nehmen Sie alle relevanten Netzwerkkonfigurationen vor der Aktivierung und Konfiguration der AD DS-Authentifizierung für Ihre Azure-Dateifreigaben vor. Weitere Informationen finden Sie unter [Azure Files – Überlegungen zum Netzwerkbetrieb](storage-files-networking-overview.md).

## <a name="regional-availability"></a>Regionale Verfügbarkeit

Die Azure Files-Authentifizierung mit AD DS ist [in allen öffentlichen Azure- und Gov-Regionen](https://azure.microsoft.com/global-infrastructure/locations/) verfügbar.

## <a name="overview"></a>Übersicht

Wenn Sie Netzwerkkonfigurationen für Ihre Dateifreigabe aktivieren möchten, lesen Sie den Artikel [Netzwerküberlegungen](./storage-files-networking-overview.md), und nehmen Sie entsprechende Konfiguration vor, bevor Sie die AD DS-Authentifizierung aktivieren.

Wenn Sie die AD DS-Authentifizierung für Ihre Azure-Dateifreigaben aktivieren, können Sie sich mit Ihren lokalen AD DS-Anmeldeinformationen bei ihren Azure-Dateifreigaben authentifizieren. Außerdem ermöglicht es eine bessere Verwaltung der Berechtigungen, sodass Sie eine präzise Zugriffssteuerung erzielen. Hierzu müssen die Identitäten mithilfe von AD Connect aus den lokalen AD DS mit Azure AD synchronisiert werden. Sie steuern den Zugriff auf Freigabeebene mit Identitäten, die mit Azure AD synchronisiert werden, während Sie den Zugriff auf Datei-/Freigabebene mit lokalen AD DS-Anmeldeinformationen verwalten.

Führen Sie anschließend die folgenden Schritte aus, um Azure Files für die AD DS-Authentifizierung einzurichten: 

1. [Teil 1: Azure Files-AD DS-Authentifizierung für Ihr Speicherkonto aktivieren](storage-files-identity-ad-ds-enable.md)

1. [Teil 2: Azure AD-Identität (Benutzer, Gruppe oder Dienstprinzipal), die mit der AD-Zielidentität synchronisiert wird, Zugriffsberechtigungen für eine Freigabe zuweisen](storage-files-identity-ad-ds-assign-permissions.md)

1. [Teil 3: Windows ACLs über SMB für Verzeichnisse und Dateien konfigurieren](storage-files-identity-ad-ds-configure-permissions.md)
 
1. [Teil 4: Azure-Dateifreigabe für eine VM einbinden, die Mitglied Ihrer AD DS-Umgebung ist](storage-files-identity-ad-ds-mount-file-share.md)

1. [Aktualisieren Sie das Kennwort für Ihre Speicherkontoidentität in AD DS.](storage-files-identity-ad-ds-update-password.md)

Die folgende Abbildung zeigt den vollständigen Workflow zur Aktivierung der Azure AD-Authentifizierung über SMB für Azure-Dateifreigaben. 

![Files AD-Workflowdiagramm](media/storage-files-active-directory-domain-services-enable/diagram-files-ad.png)

Die für den Zugriff auf Azure-Dateifreigaben verwendeten Identitäten müssen mit Azure AD synchronisiert werden, um über das Modell für die [rollenbasierte Zugriffssteuerung von Azure (Azure Role-Based Access Control, Azure-RBAC)](../../role-based-access-control/overview.md) Dateiberechtigungen auf Freigabeebene zu erzwingen. [DACLs im Windows-Stil](/previous-versions/technet-magazine/cc161041(v=msdn.10)) für Dateien/Verzeichnisse, die von vorhandenen Dateiservern übertragen werden, werden beibehalten und erzwungen. Diese bietet Ihnen nahtlose Integration in die AD DS-Umgebung Ihres Unternehmens. Wenn Sie lokale Dateiserver durch Azure-Dateifreigaben ersetzen, können vorhandene Benutzer ohne Änderungen an den verwendeten Anmeldeinformationen auf Azure-Dateifreigaben von ihren aktuellen SSO-Clients aus zugreifen.  

## <a name="next-steps"></a>Nächste Schritte

Um die lokale AD DS-Authentifizierung für Ihre Azure-Dateifreigabe zu aktivieren, lesen Sie den nächsten Artikel:

[Teil 1: Aktivieren der AD DS-Authentifizierung für Ihr Konto](storage-files-identity-ad-ds-enable.md)
