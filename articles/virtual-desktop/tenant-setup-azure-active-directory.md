---
title: Erstellen eines Mandanten in Windows Virtual Desktop (Vorschauversion) – Azure
description: Es wird beschrieben, wie Sie in Azure Active Directory Windows Virtual Desktop-Mandanten (Vorschauversion) einrichten.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 2c9682746201306f1b99a04462819618225caa11
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66164263"
---
# <a name="tutorial-create-a-tenant-in-windows-virtual-desktop-preview"></a>Tutorial: Erstellen eines Mandanten in Windows Virtual Desktop (Vorschauversion)

Die Erstellung eines Mandanten in Windows Virtual Desktop (Vorschauversion) ist der erste Schritt zur Entwicklung Ihrer Lösung für die Desktopvirtualisierung. Ein Mandant umfasst eine Gruppe mit mindestens einem Hostpool. Jeder Hostpool besteht aus mehreren Sitzungshosts, die als virtuelle Computer in Azure ausgeführt und für den Windows Virtual Desktop-Dienst registriert werden. Darüber hinaus umfasst jeder Hostpool mindestens eine App-Gruppe, die verwendet wird, um Remotedesktop- und Remoteanwendungsressourcen für Benutzer zu veröffentlichen. Mit einem Mandanten können Sie Hostpools und App-Gruppen erstellen, Benutzer zuweisen und Verbindungen über den Dienst herstellen.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Gewähren von Azure Active Directory-Berechtigungen für den Windows Virtual Desktop-Dienst
> * Zuweisen der Anwendungsrolle „TenantCreator“ für einen Benutzer in Ihrem Azure Active Directory-Mandanten
> * Erstellen eines Windows Virtual Desktop-Mandanten

Sie benötigen Folgendes, um Ihren Windows Virtual Desktop-Mandanten einzurichten:

* Die Mandanten-ID von [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) für Windows Virtual Desktop-Benutzer.
* Ein globales Administratorkonto im Azure Active Directory-Mandanten.
   * Dies gilt auch für CSP-Organisationen (Cloud Solution Provider), die einen Windows Virtual Desktop-Mandanten für ihre Kunden erstellen. Wenn Sie eine CSP-Organisation sind, müssen Sie sich als globaler Administrator der Azure Active Directory-Instanz des Kunden anmelden können.
   * Das Administratorkonto muss aus dem Azure Active Directory-Mandanten stammen, in dem Sie den Windows Virtual Desktop-Mandanten erstellen möchten. Azure Active Directory B2B-Konten (Gastkonten) werden bei diesem Prozess nicht unterstützt.
   * Das Administratorkonto muss ein Geschäfts-, Schul- oder Unikonto sein.
* Ein Azure-Abonnement

## <a name="grant-azure-active-directory-permissions-to-the-windows-virtual-desktop-preview-service"></a>Gewähren von Azure Active Directory-Berechtigungen für den Windows Virtual Desktop-Dienst (Vorschauversion)

Überspringen Sie diesen Abschnitt, falls Sie für diese Azure Active Directory-Instanz bereits Berechtigungen für Windows Virtual Desktop gewährt haben.

Durch das Gewähren von Berechtigungen für den Windows Virtual Desktop-Dienst kann dieser Ihre Azure Active Directory-Instanz zur Erledigung von Verwaltungs- und Endbenutzeraufgaben abfragen.

Gewähren Sie die Dienstberechtigungen wie folgt:

1. Öffnen Sie einen Browser, und stellen Sie eine Verbindung mit der [Windows Virtual Desktop-Seite für die Einwilligung](https://rdweb.wvd.microsoft.com) her.
2. Geben Sie unter **Consent Option** > **Server App** (Einwilligungsoption > Server-App) den Namen des Azure Active Directory-Mandanten oder die Verzeichnis-ID ein, und wählen Sie anschließend **Submit** (Absenden).
        Für Cloud Solution Provider-Kunden ist die ID die Microsoft-ID des Kunden aus dem Partnerportal. Für Enterprise-Kunden befindet sich die ID unter **Azure Active Directory** > **Eigenschaften** > **Verzeichnis-ID**.
3. Melden Sie sich auf der Windows Virtual Desktop-Seite für die Einwilligung mit dem Konto eines globalen Administrators an. Wenn Sie für die Organisation Contoso arbeiten, lautet Ihr Kontoname beispielsweise admin@contoso.com oder admin@contoso.onmicrosoft.com.  
4. Wählen Sie **Akzeptieren** aus.
5. Warten Sie eine Minute.
6. Navigieren Sie zurück zur [Windows Virtual Desktop-Seite für die Einwilligung](https://rdweb.wvd.microsoft.com).
7. Navigieren Sie zu **Consent Option** > **Client App** (Einwilligungsoption > Client-App), geben Sie den gleichen Azure Active Directory-Mandantennamen bzw. die Verzeichnis-ID ein, und wählen Sie anschließend **Submit** (Übermitteln) aus.
8. Melden Sie sich (wie in Schritt 3) auf der Seite für die Windows Virtual Desktop-Einwilligung als globaler Administrator an.
9. Wählen Sie **Akzeptieren** aus.

## <a name="assign-the-tenantcreator-application-role-to-a-user-in-your-azure-active-directory-tenant"></a>Zuweisen der Anwendungsrolle „TenantCreator“ für einen Benutzer in Ihrem Azure Active Directory-Mandanten

Wenn die Anwendungsrolle „TenantCreator“ einem Azure Active Directory-Benutzer zugewiesen wird, kann der jeweilige Benutzer einen Windows Virtual Desktop-Mandanten erstellen, der der Azure Active Directory-Instanz zugeordnet ist. Sie müssen Ihr globales Administratorkonto verwenden, um die Rolle „TenantCreator“ zuzuweisen.

Weisen Sie die Anwendungsrolle „TenantCreator“ über Ihr globales Administratorkonto zu:

1. Öffnen Sie einen Browser, und stellen Sie über Ihr globales Administratorkonto eine Verbindung mit dem [Azure-Portal](https://portal.azure.com) her.
   - Wenn Sie mehrere Azure Active Directory-Mandanten verwenden, empfiehlt es sich, eine private Browsersitzung zu öffnen, die URLs zu kopieren und auf der Adressleiste einzufügen.
2. Suchen Sie über die Suchleiste des Azure-Portals nach **Unternehmensanwendungen**, und wählen Sie den Eintrag aus, der unter der Kategorie **Dienste** angezeigt wird.
3. Suchen Sie innerhalb von **Unternehmensanwendungen** nach **Windows Virtual Desktop**. Die beiden Anwendungen, für die Sie im vorherigen Abschnitt die Einwilligung erteilt haben, werden angezeigt. Wählen Sie von diesen beiden Apps **Windows Virtual Desktop** aus.
        ![Screenshot: Suchergebnisse für „Windows Virtual Desktop“ in den Unternehmensanwendungen. Die App „Windows Virtual Desktop“ ist hervorgehoben.](media/tenant-enterprise-app.png)
4. Wählen Sie **Benutzer und Gruppen**. Unter Umständen wird der Administrator, der die Zustimmung für die Anwendung erteilt hat, bereits mit der zugewiesenen Rolle **Standardzugriff** aufgeführt. Das reicht für die Erstellung eines Windows Virtual Desktop-Mandanten jedoch nicht aus. Führen Sie die weiteren Schritte dieser Anleitung aus, um einem Benutzer die Rolle **TenantCreator** hinzuzufügen.
        ![Screenshot: Benutzer und Gruppen, die zur Verwaltung der Unternehmensanwendung „Windows Virtual Desktop“ zugewiesen sind. Der Screenshot zeigt nur eine einzelne Zuweisung für Standardzugriff.](media/tenant-default-access.png)
5. Wählen Sie **Benutzer hinzufügen** und anschließend auf dem Blatt **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
6. Suchen Sie nach einem Benutzerkonto, das zum Erstellen Ihres Windows Virtual Desktop-Mandanten verwendet wird. Der Einfachheit halber kann dies das globale Administratorkonto sein.

    ![Screenshot: Auswählen eines Benutzers, der als „TenantCreator“ hinzugefügt werden soll.](media/tenant-assign-user.png)

   > [!NOTE]
   > Sie müssen einen Benutzer (oder eine Gruppe mit einem Benutzer) auswählen, der aus diesem Azure Active Directory stammt. Sie können keinen Gastbenutzer (B2B) und keinen Dienstprinzipal auswählen.

7. Wählen Sie das Benutzerkonto, die Schaltfläche **Auswählen** und anschließend **Zuweisen** aus.
8. Vergewissern Sie sich auf der Seite **Windows Virtual Desktop – Benutzer und Gruppen**, dass für den Benutzer, der den Windows Virtual Desktop-Mandanten erstellt, ein neuer Eintrag mit der zugewiesenen Rolle **TenantCreator** angezeigt wird.
        ![Screenshot: Benutzer und Gruppen, die zur Verwaltung der Unternehmensanwendung „Windows Virtual Desktop“ zugewiesen sind. Auf dem Screenshot ist nun ein zweiter Eintrag eines Benutzers zu sehen, der der Rolle „TenantCreator“ zugewiesen ist.](media/tenant-tenant-creator-added.png)

Bevor Sie mit der Erstellung Ihres Windows Virtual Desktop-Mandanten fortfahren, benötigen Sie zwei Informationen:
- ID Ihres Azure Active Directory-Mandanten (oder die **Verzeichnis-ID**)
- ID Ihres Azure-Abonnements

So ermitteln Sie die ID Ihres Azure Active Directory-Mandanten (oder der **Verzeichnis-ID**):
1. Suchen Sie in der gleichen Azure-Portalsitzung über die Suchleiste nach **Azure Active Directory**, und wählen Sie den Eintrag aus, der unter der Kategorie **Dienste** angezeigt wird.
        ![Screenshot: Suchergebnisse für „Azure Active Directory“ im Azure-Portal. Das Suchergebnis unter „Dienste“ ist hervorgehoben.](media/tenant-search-azure-active-directory.png)
2. Scrollen Sie nach unten zu **Eigenschaften**, und wählen Sie die Option aus.
3. Suchen Sie nach der **Verzeichnis-ID**, und wählen Sie das Symbol für die Zwischenablage aus. Fügen Sie sie an einem gut erreichbaren Ort ein, um sie später als **AadTenantId** verwenden zu können.
        ![Screenshot: Azure Active Directory-Eigenschaften. Der Mauszeiger zeigt auf das Symbol, mit dem die Verzeichnis-ID in die Zwischenablage kopiert werden kann.](media/tenant-directory-id.png)

So ermitteln Sie Ihre Azure-Abonnement-ID:
1. Suchen Sie in der gleichen Azure-Portalsitzung über die Suchleiste nach **Abonnements**, und wählen Sie den Eintrag aus, der unter der Kategorie **Dienste** angezeigt wird.
        ![Screenshot: Suchergebnisse für „Azure Active Directory“ im Azure-Portal. Das Suchergebnis unter „Dienste“ ist hervorgehoben.](media/tenant-search-subscription.png)
2. Wählen Sie das Azure-Abonnement aus, das Sie für den Empfang von Benachrichtigungen des Windows Virtual Desktop-Diensts verwenden möchten.
3. Zeigen Sie auf den Wert der **Abonnement-ID**, bis ein Symbol für die Zwischenablage angezeigt wird. Wählen Sie das Symbol für die Zwischenablage aus, und fügen Sie den Wert an einem gut erreichbaren Ort ein, um ihn später als **AzureSubscriptionId** verwenden zu können.
        ![Screenshot: Eigenschaften des Azure-Abonnements. Der Mauszeiger zeigt auf das Symbol, mit dem die Abonnement-ID in die Zwischenablage kopiert werden kann.](media/tenant-subscription-id.png)

## <a name="create-a-windows-virtual-desktop-preview-tenant"></a>Erstellen eines Windows Virtual Desktop-Mandanten (Vorschauversion)

Nachdem Sie nun die Windows Virtual Desktop-Dienstberechtigungen zum Abfragen der Azure Active Directory-Instanz gewährt und die Rolle „TenantCreator“ einem Benutzerkonto zugewiesen haben, können Sie einen Windows Virtual Desktop-Mandanten erstellen.

Zunächst müssen Sie das [Windows Virtual Desktop-Modul herunterladen und importieren](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview), um es in Ihrer PowerShell-Sitzung verwenden zu können.

Melden Sie sich mit dem TenantCreator-Benutzerkonto an Windows Virtual Desktop an, indem Sie dieses Cmdlet verwenden:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Erstellen Sie anschließend einen neuen Windows Virtual Desktop-Mandanten, der dem Azure Active Directory-Mandanten zugeordnet ist:

```powershell
New-RdsTenant -Name <TenantName> -AadTenantId <DirectoryID> -AzureSubscriptionId <SubscriptionID>
```

Die Werte in Klammern sollten durch die Werte ersetzt werden, die für Ihre Organisation und den Mandanten relevant sind. Der für Ihren neuen Windows Virtual Desktop-Mandanten ausgewählte Name muss global eindeutig sein. Angenommen, Sie sind der „TenantCreator“ von Windows Virtual Desktop für die Organisation Contoso. Das auszuführende Cmdlet sieht dann wie folgt aus:

```powershell
New-RdsTenant -Name Contoso -AadTenantId 00000000-1111-2222-3333-444444444444 -AzureSubscriptionId 55555555-6666-7777-8888-999999999999
```

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie Ihren Mandanten erstellt haben, müssen Sie in Azure Active Directory einen Dienstprinzipal erstellen und ihm innerhalb von Windows Virtual Desktop eine Rolle zuweisen. Der Dienstprinzipal ermöglicht die erfolgreiche Bereitstellung des Azure Marketplace-Angebots für Windows Virtual Desktop zur Erstellung eines Hostpools. Weitere Informationen zu Hostpools finden Sie im Tutorial zum Erstellen eines Hostpools in Windows Virtual Desktop.

> [!div class="nextstepaction"]
> [Erstellen von Dienstprinzipalen und Rollenzuweisungen mit PowerShell](./create-service-principal-role-powershell.md)
