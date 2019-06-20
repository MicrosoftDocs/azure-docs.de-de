---
title: 'Azure Active Directory Domain Services: Bereitstellen eines Azure Active Directory-Anwendungsproxys | Microsoft-Dokumentation'
description: Verwenden eines Azure AD-Anwendungsproxys in durch Azure Active Directory Domain Services verwalteten Domänen
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
manager: daveba
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: mstephen
ms.openlocfilehash: 202d33c95742f951b57e68f491f139aef068767c
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66245335"
---
# <a name="deploy-azure-ad-application-proxy-on-an-azure-ad-domain-services-managed-domain"></a>Bereitstellen eines Azure AD-Anwendungsproxys in einer durch Azure AD Domain Services verwalteten Domäne
Mit dem Azure Active Directory-Anwendungsproxy (AD) können Sie Remotemitarbeiter unterstützen, indem Sie lokale Anwendungen so veröffentlichen, dass über das Internet auf sie zugegriffen werden kann. Mit Azure AD Domain Services können Sie jetzt lokal ausgeführte Legacyanwendungen per Lift & Shift zu Azure Infrastrukturdiensten migrieren. Anschließend können Sie diese Anwendungen über den Azure AD-Anwendungsproxy veröffentlichen, um Benutzern in Ihrer Organisation einen sicheren Remotezugriff bereitzustellen.

Wenn Sie mit dem Azure AD-Anwendungsproxy noch nicht vertraut sind, finden Sie weitere Informationen zu diesem Feature in folgendem Artikel: [Bereitstellen von sicherem Remotezugriff auf lokale Anwendungen](../active-directory/manage-apps/application-proxy.md).

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Voraussetzungen
Um die in diesem Artikel beschriebenen Aufgaben auszuführen, benötigen Sie Folgendes:

1. Ein gültiges **Azure-Abonnement**.
2. Ein **Azure AD-Verzeichnis** – entweder synchronisiert mit einem lokalen Verzeichnis oder als reines Cloud-Verzeichnis
3. Für die Verwendung des Azure AD-Anwendungsproxys ist eine **Basic- oder Premium-Lizenz für Azure AD** erforderlich.
4. **Azure AD Domain Services** müssen für das Azure AD-Verzeichnis aktiviert sein. Wenn dies noch nicht der Fall ist, führen Sie alle Aufgaben im Leitfaden [Erste Schritte](create-instance.md)aus.

<br>

## <a name="task-1---enable-azure-ad-application-proxy-for-your-azure-ad-directory"></a>Aufgabe 1: Aktivieren des Azure AD-Anwendungsproxys für Ihr Azure AD-Verzeichnis
Führen Sie die folgenden Schritte aus, um den Azure AD-Anwendungsproxy für Ihr Azure AD-Verzeichnis zu aktivieren.

1. Melden Sie sich als Administrator beim [Azure-Portal](https://portal.azure.com) an.

2. Klicken Sie auf **Azure Active Directory**, um die Verzeichnisübersicht anzuzeigen. Klicken Sie auf **Unternehmensanwendungen**.

    ![Auswählen des Azure AD-Verzeichnisses](./media/app-proxy/app-proxy-enable-start.png)
3. Klicken Sie auf **Anwendungsproxy**. Wenn Sie nicht über ein Basic- oder Premium-Abonnement für Azure AD verfügen, wird Ihnen eine Option zum Aktivieren einer Testversion angezeigt. Wählen Sie für **Anwendungsproxy aktivieren?** die Option **Aktivieren** aus, und klicken Sie auf **Speichern**.

    ![Aktivieren des Anwendungsproxys](./media/app-proxy/app-proxy-enable-proxy-blade.png)
4. Um den Connector herunterzuladen, klicken Sie auf die Schaltfläche **Connector**.

    ![Herunterladen des Connectors](./media/app-proxy/app-proxy-enabled-download-connector.png)
5. Akzeptieren Sie auf der Downloadseite die Lizenzbedingungen und die Datenschutzerklärung, und klicken Sie auf die Schaltfläche **Herunterladen**.

    ![Bestätigen des Downloads](./media/app-proxy/app-proxy-enabled-confirm-download.png)


## <a name="task-2---provision-domain-joined-windows-servers-to-deploy-the-azure-ad-application-proxy-connector"></a>Aufgabe 2: Bereitstellen von in die Domäne eingebundenen Windows Server-Instanzen zum Bereitstellen des Azure AD-Anwendungsproxy-Connectors
Sie benötigen in die Domäne eingebundene virtuelle Windows Server-Computer, auf denen Sie den Azure AD-Anwendungsproxy-Connector installieren können. Bei einigen Anwendungen können Sie mehrere Server bereitstellen, auf denen der Connector installiert ist. Diese Bereitstellungsoption bietet Ihnen eine größere Verfügbarkeit und hilft bei der Verarbeitung größerer Authentifizierungslasten.

Stellen Sie die Connectorserver in demselben virtuellen Netzwerk (oder in einem virtuellen verbundenen Netzwerk/Peernetzwerk) bereit, in dem Sie Ihre verwaltete Azure AD Domain Services-Domäne aktiviert haben. Ebenso müssen die Server, die die über den Anwendungsproxy veröffentlichten Anwendungen hosten, in demselben virtuellen Azure-Netzwerk installiert sein.

Um Connectorserver bereitzustellen, führen Sie die im folgenden Artikel beschriebenen Schritte aus: [Einbinden eines virtuellen Windows-Computers in eine verwaltete Domäne](active-directory-ds-admin-guide-join-windows-vm.md).


## <a name="task-3---install-and-register-the-azure-ad-application-proxy-connector"></a>Aufgabe 3: Installieren und Registrieren des Azure AD-Anwendungsproxy-Connectors
Zuvor haben Sie einen virtuellen Windows Server-Computer bereitgestellt und in die verwaltete Domäne eingebunden. In dieser Aufgabe installieren Sie den Azure AD-Anwendungsproxy-Connector auf diesem virtuellen Computer.

1. Kopieren Sie das Connectorinstallationspaket auf den virtuellen Computer, auf dem Sie den Azure AD-Webanwendungsproxy-Connector installieren.

2. Führen Sie **AADApplicationProxyConnectorInstaller.exe** auf dem virtuellen Computer aus. Akzeptieren Sie die Softwarelizenzbedingungen.

    ![Akzeptieren der Bedingungen für die Installation](./media/app-proxy/app-proxy-install-connector-terms.png)
3. Während der Installation werden Sie aufgefordert, den Connector beim Anwendungsproxy Ihres Azure AD-Verzeichnisses zu registrieren.
   * Geben Sie Ihre **globalen Azure AD-Administratoranmeldeinformationen** ein. Ihr globaler Administratorenmandant kann von Ihren Microsoft Azure-Anmeldeinformationen abweichen.
   * Das zum Registrieren des Connectors verwendete Administratorkonto muss demselben Verzeichnis angehören, in dem Sie auch den Anwendungsproxydienst aktiviert haben. Wenn die Mandantendomäne also beispielsweise „contoso.com“ lautet, muss sich der Administrator als admin@contoso.com oder mit einem anderen gültigen Aliasnamen in dieser Domäne anmelden.
   * Falls auf dem Server, auf dem Sie den Connector installieren, die Option „Verstärkte Sicherheitskonfiguration für IE“ aktiviert ist, wird der Registrierungsbildschirm möglicherweise blockiert. Befolgen Sie die Anweisungen in der Fehlermeldung, um den Zugriff zuzulassen. Stellen Sie sicher, dass die erweiterte Sicherheit von Internet Explorer deaktiviert ist.
   * Falls die Connectorregistrierung nicht erfolgreich war, helfen Ihnen die Informationen unter [Problembehandlung von Anwendungsproxys](../active-directory/manage-apps/application-proxy-troubleshoot.md)weiter.

     ![Connector installiert](./media/app-proxy/app-proxy-connector-installed.png)
4. Um sicherzustellen, dass der Connector ordnungsgemäß funktioniert, führen Sie die Problembehandlung für den Azure AD-Anwendungsproxy-Connector aus. Nach dem Ausführen der Problembehandlung sollte ein Bericht zur erfolgreichen Installation angezeigt werden.

    ![Problembehandlung mit Erfolg](./media/app-proxy/app-proxy-connector-troubleshooter.png)
5. Der neu installierte Connector wird auf der Anwendungsproxyseite in Ihrem Azure AD-Verzeichnis angezeigt.

    ![](./media/app-proxy/app-proxy-connector-page.png)

> [!NOTE]
> Sie können sich entschließen, Connectors auf mehreren Servern zu installieren, um für die Authentifizierung von Anwendungen über den Azure AD-Anwendungsproxy Hochverfügbarkeit zu gewährleisten. Führen Sie die oben aufgeführten Schritte zur Installation des Connectors auf weiteren Servern durch, die in Ihre verwaltete Domäne eingebunden sind.
>
>

## <a name="next-steps"></a>Nächste Schritte
Sie haben den Azure AD-Anwendungsproxy eingerichtet und in Ihre verwaltete Azure AD Domain Services-Domäne integriert.

* **Migrieren Ihrer Anwendungen zu virtuellen Azure-Computern:** Sie können Ihre Anwendungen per Lift & Shift von lokalen Servern zu virtuellen Azure-Computern migrieren, die in die verwaltete Domäne eingebunden sind. Auf diese Weise können Sie die Infrastrukturkosten einsparen, die beim lokalen Ausführen von Servern anfallen.

* **Veröffentlichen von Anwendungen mit dem Azure AD-Anwendungsproxy:** Veröffentlichen Sie Anwendungen, die auf Ihren virtuellen Azure-Computern ausgeführt werden, über den Azure AD-Anwendungsproxy. Weitere Informationen finden Sie unter [Veröffentlichen von Anwendungen mit Azure AD-Anwendungsproxy](../active-directory/manage-apps/application-proxy-publish-azure-portal.md).


## <a name="deployment-note---publish-iwa-integrated-windows-authentication-applications-using-azure-ad-application-proxy"></a>Bereitstellungshinweis: Veröffentlichen von IWA-Anwendungen (integrierte Windows-Authentifizierung) mit dem Azure AD-Anwendungsproxy
Aktivieren Sie einmaliges Anmelden bei Ihren Anwendungen über die integrierte Windows-Authentifizierung (IWA), indem Sie einem Anwendungsproxy-Connector die Berechtigung erteilen, die Identität von Benutzern anzunehmen und Token in deren Namen zu senden und zu empfangen. Konfigurieren Sie die eingeschränkte Kerberos-Delegierung (KCD) für den Connector, um die erforderlichen Berechtigungen für den Zugriff auf Ressourcen in der verwalteten Domäne zu erteilen. Verwenden Sie für erhöhte Sicherheit den Mechanismus der ressourcenbasierten KCD in verwalteten Domänen.


### <a name="enable-resource-based-kerberos-constrained-delegation-for-the-azure-ad-application-proxy-connector"></a>Aktivieren der ressourcenbasierten eingeschränkten Kerberos-Delegierung für den Azure AD-Anwendungsproxy-Connector
Der Azure-Anwendungsproxy-Connector sollte für die eingeschränkte Kerberos-Delegierung (KCD) konfiguriert werden, damit er Benutzeridentitäten in der verwalteten Domäne annehmen kann. In einer verwalteten Azure AD Domain Services-Domäne besitzen Sie keine Domänenadministratorberechtigungen. Aus diesem Grund **kann die herkömmliche KCD auf Kontoebene in einer verwalteten Domäne nicht konfiguriert werden**.

Verwenden Sie die ressourcenbasierte KCD entsprechend der Beschreibung in diesem [Artikel](deploy-kcd.md).

> [!NOTE]
> Sie müssen der Gruppe „AAD DC-Administratoren“ angehören, um die verwaltete Domäne über AD-PowerShell-Cmdlets verwalten zu können.
>
>

Verwenden Sie das PowerShell-Cmdlet „Get-ADComputer“ zum Abrufen der Einstellungen für den Computer, auf dem der Azure AD-Anwendungsproxy-Connector installiert ist.
```powershell
$ConnectorComputerAccount = Get-ADComputer -Identity contoso100-proxy.contoso100.com
```

Verwenden Sie danach das Cmdlet „Set-ADComputer“, um für den Ressourcenserver die ressourcenbasierte KCD einzurichten.
```powershell
Set-ADComputer contoso100-resource.contoso100.com -PrincipalsAllowedToDelegateToAccount $ConnectorComputerAccount
```

Wenn Sie mehrere Anwendungsproxy-Connectors in Ihrer verwalteten Domäne bereitgestellt haben, müssen Sie die ressourcenbasierte KCD für jede dieser Connectorinstanzen konfigurieren.


## <a name="related-content"></a>Verwandte Inhalte
* [Azure AD-Domänendienste – Leitfaden zu den ersten Schritten](create-instance.md)
* [Konfigurieren der eingeschränkten Kerberos-Delegierung in einer verwalteten Domäne](deploy-kcd.md)
* [Übersicht über die eingeschränkte Kerberos-Delegierung](https://technet.microsoft.com/library/jj553400.aspx)
