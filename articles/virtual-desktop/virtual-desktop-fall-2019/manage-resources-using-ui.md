---
title: Bereitstellen eines Verwaltungstools mit einer Azure Resource Manager-Vorlage – Azure
description: Hier erfahren Sie, wie Sie ein Benutzeroberflächentool zum Verwalten von Windows Virtual Desktop-Ressourcen (klassisch) mit einer Azure Resource Manager-Vorlage installieren.
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f96365bcdf64d19dc0b894f2f1230233b3137bc7
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94842650"
---
# <a name="deploy-a-windows-virtual-desktop-classic-management-tool-with-an-azure-resource-manager-template"></a>Bereitstellen eines Windows Virtual Desktop-Verwaltungstools (klassisch) mit einer Azure Resource Manager-Vorlage

>[!IMPORTANT]
>Dieser Inhalt gilt für Windows Virtual Desktop (klassisch). Der Dienst unterstützt keine Windows Virtual Desktop-Objekte in Azure Resource Manager.

Die Anweisungen in diesem Artikel beschreiben, wie die Benutzeroberfläche mit einer Azure Resource Manager-Vorlage bereitgestellt wird.

## <a name="important-considerations"></a>Wichtige Hinweise

Da für die App die Einwilligung für die Interaktion mit Windows Virtual Desktop erforderlich ist, unterstützt dieses Tool keine Business-to-Business-Szenarien (B2B). Das Verwaltungstool muss für jedes Abonnement des Azure Active Directory-Mandanten (AAD) separat bereitgestellt werden.

Dieses Verwaltungstool ist ein Beispiel. Microsoft stellt wichtige Sicherheits- und Qualitätsupdates zur Verfügung. Der [Quellcode steht auf GitHub zur Verfügung](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy). Kunden und Partnern wird empfohlen, das Tool an ihre Geschäftsanforderungen anzupassen.

Die folgenden Browser sind mit dem Verwaltungstool kompatibel:
- Google Chrome 68 oder höher
- Microsoft Edge 40.15063 oder höher
- Mozilla Firefox 52.0 oder höher
- Safari 10 oder höher (nur macOS)

## <a name="what-you-need-to-deploy-the-management-tool"></a>Was Sie zum Bereitstellen des Verwaltungstools benötigen

Bevor Sie das Verwaltungstool bereitstellen, benötigen Sie einen Azure Active Directory (Azure AD)-Benutzer, um eine App-Registrierung zu erstellen und die Verwaltungsbenutzeroberfläche bereitzustellen. Für diesen Benutzer gilt Folgendes:

- Der Benutzer muss Azure AD Multi-Factor Authentication (MFA) deaktiviert haben.
- Er muss über die Berechtigung zum Erstellen von Ressourcen in Ihrem Azure-Abonnement verfügen.
- Er muss über die Berechtigung zum Erstellen einer Azure AD-Anwendung verfügen. Führen Sie die folgenden Schritte aus, um zu überprüfen, ob der Benutzer über die erforderlichen Berechtigungen verfügt, indem Sie die Anweisungen unter [Erforderliche Berechtigungen](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app) befolgen.

Nachdem Sie das Verwaltungstool bereitgestellt und konfiguriert haben, sollten Sie einen Benutzer auffordern, die Verwaltungsbenutzeroberfläche aufzurufen, um sicherzustellen, dass alles funktioniert. Der Benutzer, der die Verwaltungsbenutzeroberfläche aufruft, muss über eine Rollenzuweisung verfügen, die es ihm erlaubt, den Windows Virtual Desktop-Mandanten anzuzeigen oder zu bearbeiten.

## <a name="deploy-the-management-tool"></a>Bereitstellen des Verwaltungstools

Stellen Sie zunächst sicher, dass eine Einwilligung für die Server- und Client-Apps vorliegt, indem Sie zur [Windows Virtual Desktop-Seite für die Einwilligung](https://rdweb.wvd.microsoft.com) für die dargestellte Azure Active Directory-Instanz (AAD) wechseln.

Gehen Sie wie folgt vor, um die Azure Ressource Manager-Vorlage bereitzustellen:

1. Wechseln Sie zur GitHub-Seite [Azure RDS-Templates](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy).
2. Stellen Sie die Vorlage in Azure bereit.
    - Wenn Sie die Bereitstellung unter einem Enterprise-Abonnement durchführen, scrollen Sie nach unten, und wählen Sie **Bereitstellung in Azure** aus.
    - Gehen Sie für die Bereitstellung in Azure folgendermaßen vor, wenn Sie die Bereitstellung unter einem Cloud Solution Provider-Abonnement durchführen:
        1. Scrollen Sie nach unten, klicken Sie mit der rechten Maustaste auf **In Azure bereitstellen**, und wählen Sie dann **Link-Adresse kopieren**.
        2. Öffnen Sie einen Text-Editor, z. B. Editor, und fügen Sie den Link ein.
        3. Geben Sie direkt nach <https://portal.azure.com/> und vor dem Hashtag (#) ein at-Zeichen (@) gefolgt vom Namen der Mandantendomäne ein. Beispiel für das Format: <https://portal.azure.com/@Contoso.onmicrosoft.com#create/>.
        4. Melden Sie sich am Azure-Portal als Benutzer mit den Berechtigungen „Administrator/Mitwirkender“ am Cloud Solution Provider-Abonnement an.
        5. Fügen Sie den Link, den Sie in den Text-Editor kopiert haben, in die Adressleiste ein.
3. Gehen Sie folgendermaßen vor, wenn Sie die Parameter eingeben:
    - Wählen Sie für den **isServicePrincipal**-Parameter die Option **false** aus.
    - Geben Sie als Anmeldeinformationen Ihre Azure AD-Anmeldeinformationen ein, wobei Sie die mehrstufige Authentifizierung deaktivieren. Diese Anmeldeinformationen werden verwendet, um die Azure AD-Anwendung und die Azure-Ressourcen zu erstellen. Weitere Informationen finden Sie unter [Was Sie zum Bereitstellen des Verwaltungstools benötigen](#what-you-need-to-deploy-the-management-tool).
    - Verwenden Sie für **applicationName** einen eindeutigen Namen für die App, die in Ihrer Azure Active Directory-Instanz registriert wird. Dieser Name wird auch für die Web-App-URL verwendet. Sie können beispielsweise einen Namen wie „Apr3UX“ verwenden.
4. Akzeptieren Sie nach Angabe der Parameter die Geschäftsbedingungen, und klicken Sie auf **Kaufen**.

## <a name="provide-consent-for-the-management-tool"></a>Erteilen der Einwilligung für das Verwaltungstool

Nach der Ausführung der GitHub-Azure Resource Manager-Vorlage wird im Azure-Portal eine Ressourcengruppe mit zwei App Services und einem App Service-Plan angezeigt.

Bevor Sie sich anmelden und das Verwaltungstool verwenden, müssen Sie die Einwilligung für die neue Azure AD-Anwendung erteilen, die mit dem Verwaltungstool verknüpft ist. Durch die Einwilligung kann das Verwaltungstool Windows Virtual Desktop-Verwaltungsaufrufe im Namen des Benutzers senden, der derzeit bei dem Tool angemeldet ist.

> [!div class="mx-imgBorder"]
> ![Screenshot, in dem bereitgestellte Berechtigungen nach erfolgter Einwilligung für das Benutzeroberflächen-Verwaltungstool gezeigt werden](../media/management-ui-delegated-permissions.png)

Um zu ermitteln, welchen Benutzer Sie für die Anmeldung bei dem Tool verwenden können, öffnen Sie die [Seite für die Benutzereinstellungen Ihrer Azure Active Directory-Instanz](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/), und notieren Sie den Wert für **Benutzer können Apps den Zugriff auf Unternehmensdaten in ihrem Namen gestatten**.

> [!div class="mx-imgBorder"]
> ![Screenshot, in dem angezeigt wird, ob Benutzer die Einwilligung für Anwendungen nur für ihren Benutzer erteilen können](../media/management-ui-user-consent-allowed.png)

- Wenn der Wert auf **Ja** festgelegt ist, können Sie sich mit einem Benutzerkonto bei Azure Active Directory anmelden und die Einwilligung nur für diesen Benutzer erteilen. Wenn Sie sich jedoch später mit einem anderen Benutzerkonto beim Verwaltungstool anmelden, müssen Sie die gleiche Einwilligung erneut erteilen.
- Wenn der Wert auf **Nein** festgelegt ist, müssen Sie sich als globaler Administrator bei Azure Active Directory anmelden und eine Administratoreinwilligung für alle Benutzer im Verzeichnis erteilen. Für keine anderen Benutzer wird eine Zustimmungsaufforderung angezeigt.


Nachdem Sie entschieden haben, welchen Benutzer Sie für die Erteilung der Einwilligung verwenden, gehen Sie folgendermaßen vor, um die Einwilligung für das Tool zu erteilen:

1. Navigieren Sie zu Ihren Azure-Ressourcen, wählen Sie die Azure App Services-Ressource mit dem Namen aus, den Sie in der Vorlage angegeben haben (z. B. Apr3UX), und navigieren Sie zu der damit verknüpften URL, z. B. `https://rdmimgmtweb-210520190304.azurewebsites.net`.
2. Melden Sie sich mit dem entsprechenden Azure Active Directory-Benutzerkonto an.
3. Wenn Sie sich als globaler Administrator authentifiziert haben, können Sie nun das Kontrollkästchen **Zustimmung im Namen Ihrer Organisation** aktivieren. Wählen Sie **Akzeptieren** aus, um die Einwilligung zu erteilen.

   > [!div class="mx-imgBorder"]
   > ![Screenshot, in dem die vollständige Einwilligungsseite gezeigt wird, die der Benutzer oder Administrator anzeigt](../media/management-ui-consent-page.png)

Damit gelangen Sie nun zum Verwaltungstool.

## <a name="use-the-management-tool"></a>Verwenden des Verwaltungstools

Nach der Erteilung der Einwilligung für die Organisation oder einen bestimmten Benutzer können Sie jederzeit auf das Verwaltungstool zugreifen.

Führen Sie die folgenden Schritte aus, um das Tool zu starten:

1. Wählen Sie die Azure App Services-Ressource mit dem Namen aus, den Sie in der Vorlage angegeben haben (z. B. Apr3UX), und navigieren Sie zu der damit verknüpften URL, z. B `https://rdmimgmtweb-210520190304.azurewebsites.net`.
2. Melden Sie sich mit Ihren Windows Virtual Desktop-Anmeldeinformationen an.
3. Wählen Sie bei der Aufforderung zum Auswählen einer Mandantengruppe in der Dropdownliste den Eintrag **Default Tenant Group** (Standardmandantengruppe) aus.
4. Wenn Sie eine **Standardmandantengruppe** auswählen, sollte links im Fenster ein Menü angezeigt werden. Suchen Sie in diesem Menü nach dem Namen Ihrer Mandantengruppe, und wählen Sie sie aus.

  > [!NOTE]
  > Wenn Sie eine benutzerdefinierte Mandantengruppe festgelegt haben, geben Sie den Namen manuell ein, anstatt eine Auswahl in der Dropdownliste zu treffen.

## <a name="report-issues"></a>Melden von Problemen

Falls Probleme mit dem Verwaltungstool oder mit anderen Windows Virtual Desktop-Tools auftreten, sollten Sie die Anleitung unter [Azure Resource Manager-Vorlagen für Remotedesktopdienste](https://github.com/Azure/RDS-Templates/blob/master/README.md) befolgen, um diese auf GitHub zu melden.

## <a name="next-steps"></a>Nächste Schritte

Sie haben erfahren, wie Sie das Verwaltungstool bereitstellen und eine Verbindung damit herstellen. Im nächsten Schritt erfahren Sie nun, wie Sie mithilfe der Azure Service-Hilfe Dienstprobleme und Integritätsempfehlungen überwachen. Weitere Informationen finden Sie in unserem [Tutorial zum Einrichten von Dienstwarnungen](set-up-service-alerts-2019.md).