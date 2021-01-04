---
title: Aktivieren der Kennworthashsynchronisierung für Azure AD Domain Services | Microsoft-Dokumentation
description: In diesem Tutorial wird beschrieben, wie Sie die Kennworthashsynchronisierung mit Azure AD Connect in einer per Azure Active Directory Domain Services verwalteten Domäne aktivieren.
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: e83d8941d1be7fd36f53a881a21716252ad01954
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/05/2020
ms.locfileid: "96618347"
---
# <a name="tutorial-enable-password-synchronization-in-azure-active-directory-domain-services-for-hybrid-environments"></a>Tutorial: Aktivieren der Kennwortsynchronisierung in Azure Active Directory Domain Services für Hybridumgebungen

Für Hybridumgebungen kann ein Azure AD-Mandant (Azure Active Directory) konfiguriert werden, um die Synchronisierung mit einer lokalen AD DS-Umgebung (Active Directory Domain Services) per Azure AD Connect durchzuführen. Standardmäßig werden mit Azure AD Connect keine NTLM- (NT LAN Manager) und Kerberos-Legacy-Kennworthashes synchronisiert, die für Azure Active Directory Domain Services (Azure AD DS) benötigt werden.

Zum Verwenden von Azure AD DS mit Konten, die über eine lokale AD DS-Umgebung synchronisiert werden, müssen Sie Azure AD Connect so konfigurieren, dass die für die NTLM- und Kerberos-Authentifizierung erforderlichen Kennworthashes synchronisiert werden. Nach der Azure AD Connect-Konfiguration wird für die Legacy-Kennworthashes über ein Ereignis zur lokalen Kontoerstellung oder Kennwortänderung dann auch die Synchronisierung mit Azure AD durchgeführt.

Sie müssen diese Schritte nicht ausführen, wenn Sie reine Cloudkonten ohne lokale AD DS-Umgebung oder eine *Ressourcengesamtstruktur* verwenden. Für verwaltete Domänen mit einer Ressourcengesamtstruktur werden lokale Kennworthashes nie synchronisiert. Bei der Authentifizierung für lokale Konten werden die Gesamtstruktur-Vertrauensstellungen für Ihre eigenen AD DS-Domänencontroller verwendet.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Gründe für die Verwendung von NTLM- und Kerberos-Legacy-Kennworthashes
> * Vorgehensweise beim Konfigurieren der Synchronisierung von Legacy-Kennworthashes für Azure AD Connect

Wenn Sie kein Azure-Abonnement besitzen, [erstellen Sie ein Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie die folgenden Ressourcen:

* Ein aktives Azure-Abonnement.
    * Wenn Sie kein Azure-Abonnement besitzen, [erstellen Sie ein Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Einen mit Ihrem Abonnement verknüpften Azure Active Directory-Mandanten, der per Azure AD Connect mit einem lokalen Verzeichnis synchronisiert ist.
    * [Erstellen Sie einen Azure Active Directory-Mandanten][create-azure-ad-tenant], oder [verknüpfen Sie ein Azure-Abonnement mit Ihrem Konto][associate-azure-ad-tenant], sofern erforderlich.
    * [Aktivieren Sie bei Bedarf Azure AD Connect für die Kennworthashsynchronisierung][enable-azure-ad-connect].
* Eine verwaltete Azure Active Directory Domain Services-Domäne, die in Ihrem Azure AD-Mandanten aktiviert und konfiguriert ist.
    * [Erstellen und konfigurieren Sie eine verwaltete Azure Active Directory Domain Services-Domäne][create-azure-ad-ds-instance], sofern erforderlich.

## <a name="password-hash-synchronization-using-azure-ad-connect"></a>Kennworthashsynchronisierung mit Azure AD Connect

Azure AD Connect wird verwendet, um Objekte, z. B. Benutzerkonten und -gruppen, einer lokalen AD DS-Umgebung mit einem Azure AD-Mandanten zu synchronisieren. Im Rahmen des Prozesses können Konten per Kennworthashsynchronisierung das gleiche Kennwort in der lokalen AD DS-Umgebung und in Azure AD nutzen.

Um Benutzer in der verwalteten Domäne authentifizieren zu können, benötigt Azure AD DS Kennworthashes in einem Format, das für die Authentifizierung über NTLM und Kerberos geeignet ist. Azure AD speichert erst dann Kennworthashes in dem für die NTLM- oder Kerberos-Authentifizierung erforderlichen Format, wenn Sie Azure AD DS für Ihren Mandanten aktivieren. Aus Sicherheitsgründen speichert Azure AD Kennwörter nicht als Klartext. Daher kann Azure AD diese NTLM- oder Kerberos-Kennworthashes nicht automatisch auf der Grundlage bereits vorhandener Anmeldeinformationen von Benutzern generieren.

Azure AD Connect kann so konfiguriert werden, dass die erforderlichen NTLM- oder Kerberos-Kennworthashes für Azure AD DS synchronisiert werden. Stellen Sie sicher, dass Sie die Schritte zum [Aktivieren von Azure AD Connect für die Kennworthashsynchronisierung][enable-azure-ad-connect] ausgeführt haben. Führen Sie bei einer vorhandenen Instanz von Azure AD Connect einen [Download und ein Update auf die aktuelle Version][azure-ad-connect-download] durch, um sicherzustellen, dass Sie die Legacy-Kennworthashes für NTLM und Kerberos synchronisieren können. Diese Funktionalität ist in frühen Versionen von Azure AD Connect oder im DirSync-Legacy-Tool nicht verfügbar. Azure AD Connect-Version *1.1.614.0* oder höher ist erforderlich.

> [!IMPORTANT]
> Azure AD Connect sollte nur für die Synchronisierung mit lokalen AD DS-Umgebungen installiert und konfiguriert werden. Die Installation von Azure AD Connect in einer verwalteten Azure AD DS-Domäne zur erneuten Synchronisierung von Objekten mit Azure AD wird nicht unterstützt.

## <a name="enable-synchronization-of-password-hashes"></a>Aktivieren der Synchronisierung von Kennworthashes

Nachdem Sie Azure AD Connect installiert und für die Synchronisierung mit Azure AD konfiguriert haben, können Sie nun die Legacy-Kennworthashsynchronisierung für NTLM und Kerberos konfigurieren. Ein PowerShell-Skript wird zum Konfigurieren der erforderlichen Einstellungen verwendet, und anschließend wird eine vollständige Kennwortsynchronisierung mit Azure AD gestartet. Nachdem der Prozess für die Azure AD Connect-Kennworthashsynchronisierung abgeschlossen ist, können sich Benutzer über Azure AD DS an Anwendungen anmelden, indem NTLM- bzw. Kerberos-Legacy-Kennworthashes genutzt werden.

1. Öffnen Sie auf dem Computer, der über die Azure AD Connect-Installation verfügt, im Startmenü die Option **Azure AD Connect > Synchronisierungsdienst**.
1. Wählen Sie die Registerkarte **Connectors** aus. Die Verbindungsinformationen zum Einrichten der Synchronisierung zwischen der lokalen AD DS-Umgebung und Azure AD werden aufgeführt.

    Unter **Typ** wird entweder *Windows Azure Active Directory (Microsoft)* für den Azure AD-Connector oder *Active Directory Domain Services* für den lokalen AD DS-Connector angegeben. Notieren Sie sich die Connectornamen, damit Sie sie im nächsten Schritt im PowerShell-Skript verwenden können.

    ![Auflisten der Connectornamen in Synchronization Service Manager](media/tutorial-configure-password-hash-sync/service-sync-manager.png)

    In diesem Beispielscreenshot werden die folgenden Connectors verwendet:

    * Der Azure AD-Connector hat den Namen *contoso.onmicrosoft.com – AAD*.
    * Der lokale AD DS-Connector hat den Namen *onprem.contoso.com*.

1. Kopieren Sie das folgende PowerShell-Skript, und fügen Sie es auf dem Computer ein, auf dem Azure AD Connect installiert ist. Das Skript löst eine vollständige Kennwortsynchronisierung aus, einschließlich der Legacy-Kennworthashes. Aktualisieren Sie die Variablen `$azureadConnector` und `$adConnector` mit den Connectornamen aus dem vorherigen Schritt.

    Führen Sie dieses Skript in jeder AD-Gesamtstruktur aus, um NTLM- und Kerberos-Kennworthashes des lokalen Kontos mit Azure AD zu synchronisieren.

    ```powershell
    # Define the Azure AD Connect connector names and import the required PowerShell module
    $azureadConnector = "<CASE SENSITIVE AZURE AD CONNECTOR NAME>"
    $adConnector = "<CASE SENSITIVE AD DS CONNECTOR NAME>"
    
    Import-Module "C:\Program Files\Microsoft Azure AD Sync\Bin\ADSync\ADSync.psd1"
    Import-Module "C:\Program Files\Microsoft Azure Active Directory Connect\AdSyncConfig\AdSyncConfig.psm1"

    # Create a new ForceFullPasswordSync configuration parameter object then
    # update the existing connector with this new configuration
    $c = Get-ADSyncConnector -Name $adConnector
    $p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
    $p.Value = 1
    $c.GlobalParameters.Remove($p.Name)
    $c.GlobalParameters.Add($p)
    $c = Add-ADSyncConnector -Connector $c

    # Disable and re-enable Azure AD Connect to force a full password synchronization
    Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $false
    Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $true
    ```

    Je nach Größe Ihres Verzeichnisses in Bezug auf die Anzahl von Konten und Gruppen kann die Synchronisierung der Legacy-Kennworthashes mit Azure AD auch etwas länger dauern. Die Kennwörter werden dann mit der verwalteten Domäne synchronisiert, nachdem die Synchronisierung mit Azure AD erfolgt ist.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial wurde Folgendes beschrieben:

> [!div class="checklist"]
> * Gründe für die Verwendung von NTLM- und Kerberos-Legacy-Kennworthashes
> * Vorgehensweise beim Konfigurieren der Synchronisierung von Legacy-Kennworthashes für Azure AD Connect

> [!div class="nextstepaction"]
> [Funktionsweise der Synchronisierung in einer verwalteten Azure AD Domain Services-Domäne](synchronization.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[enable-azure-ad-connect]: ../active-directory/hybrid/how-to-connect-install-express.md

<!-- EXTERNAL LINKS -->
[azure-ad-connect-download]: https://www.microsoft.com/download/details.aspx?id=47594
