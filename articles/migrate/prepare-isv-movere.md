---
title: Vorbereiten von Azure Migrate für die Arbeit mit einem ISV-Tool/Movere
description: In diesem Artikel wird beschrieben, wie Sie Azure Migrate für die Arbeit mit einem ISV-Tool oder Movere vorbereiten, sowie die ersten Schritte bei der Verwendung des Tools.
author: ms-psharma
ms.author: panshar
ms.manager: abhemraj
ms.topic: how-to
ms.date: 06/10/2020
ms.openlocfilehash: 1716db0476169e12822b3f47f7199bf6e2c4ee92
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96753772"
---
# <a name="prepare-to-work-with-an-isv-tool-or-movere"></a>Vorbereiten der Verwendung eines ISV-Tools oder von Movere

Wenn Sie ein [ISV-Tool](migrate-services-overview.md#isv-integration) oder Movere einem Azure Migrate-Projekt hinzugefügt haben, gibt es ein paar durchzuführende Schritte, bevor Sie das Tool verknüpfen und Daten an Azure Migrate senden. 

## <a name="check-azure-ad-permissions"></a>Überprüfen der Azure AD-Berechtigungen

Ihr Azure-Benutzerkonto benötigt die folgenden Berechtigungen:

- Berechtigung zum Registrieren einer Azure Active Directory-App (Azure AD) bei Ihrem Azure-Mandanten
- Berechtigung zum Zuordnen einer Rolle zur Azure AD-App auf Abonnementebene


### <a name="set-permissions-to-register-an-azure-ad-app"></a>Festlegen von Berechtigungen zum Registrieren einer Azure AD-App

1. Überprüfen Sie in Azure AD die Rolle Ihr Konto.
2. Wenn Sie die Benutzerrolle besitzen, wählen Sie im linken Bereich **Benutzereinstellungen** aus, und überprüfen Sie, ob Benutzer Anwendungen registrieren können. Wenn die Einstellung auf **Ja** festgelegt ist, können aller Benutzer im Azure AD-Mandanten eine App registrieren. Wenn die Einstellung auf **Nein** festgelegt ist, können nur Administratorbenutzer Apps registrieren.   
3. Wenn Sie keine Berechtigungen besitzen, kann ein Administratorbenutzer Ihrem Benutzerkonto die Rolle [Anwendungsadministrator](../active-directory/roles/permissions-reference.md#application-administrator) zuweisen, damit Sie die App registrieren können.
4. Nachdem das Tool mit Azure Migrate verknüpft wurde, kann der Administrator die Rolle aus Ihrem Konto entfernen.

### <a name="set-permissions-to-assign-a-role-to-an-azure-ad-app"></a>Festlegen von Berechtigungen zum Zuweisen einer Rolle zu einer Azure AD-App
 
In Ihrem Azure-Abonnement muss Ihr Konto über **Microsoft.Authorization/*/Write**-Zugriff verfügen, um einer Azure AD-App eine Rolle zuzuweisen. 

1. Öffnen Sie im Azure-Portal **Abonnements**.
2. Wählen Sie das relevante Abonnement aus. Wenn es nicht angezeigt wird, wählen Sie den Filter **globale Abonnements** aus. 
3. Wählen Sie **My permissions** (Meine Berechtigungen). Wählen Sie anschließend **Klicken Sie hier, um die vollständigen Zugangsdaten für dieses Abonnement anzuzeigen** aus.
4. Überprüfen Sie in **Rollenzuweisungen** > **Ansicht** die Berechtigungen. Wenn Ihr Konto keine Berechtigungen besitzt, bitten Sie den Abonnementadministrator, Sie der Rolle [Benutzerzugriffsadministrator](../role-based-access-control/built-in-roles.md#user-access-administrator) oder [Besitzer](../role-based-access-control/built-in-roles.md#owner) hinzuzufügen.

## <a name="allow-access-to-urls"></a>Zulassen des Zugriffs auf URLs

Erlauben Sie für ISV-Tools und den Azure-Datenbank-Migrations-Assistenten den Zugriff auf die in der Tabelle zusammengefassten öffentlichen Cloud-URLs. Wenn Sie einen URL-basierten Proxy für die Internetverbindung verwenden, stellen Sie sicher, dass der Proxy alle CNAME-Einträge auflöst, die beim Abrufen der URLs empfangen werden. 

**URL** | **Details**
--- | ---
*.portal.azure.com  | Navigieren Sie zum Azure-Portal. 
*.windows.net<br/> *.msftauth.net<br/> *.msauth.net <br/> *.microsoft.com<br/> *.live.com   | Melden Sie sich bei Ihrem Azure-Abonnement an. 
*.microsoftonline.com<br/> *.microsoftonline-p.com | Erstellen von Azure Active Directory-Apps (AD) für die Kommunikation zwischen der Appliance und Azure Migrate. 
management.azure.com | Aufrufen von Azure Resource Manager für das Azure Migrate-Projekt.
*.servicebus.windows.net | Kommunikation zwischen der Appliance und EventHub zum Senden der Nachrichten.


## <a name="start-using-the-tool"></a>Beginnen mit der Verwendung des Tools

1. Wenn Sie noch keine Lizenz oder eine kostenlose Testversion für das Tool haben, wählen Sie im Tooleintrag in Azure Migrate in **Registrieren** die Option **Weitere Informationen** aus.
2. Befolgen Sie im Tool die Anweisungen, um eine Verknüpfung zwischen dem Tool und dem Azure Migrate-Projekt herzustellen und Daten an Azure Migrate zu senden.

## <a name="next-steps"></a>Nächste Schritte

Befolgen Sie die Anweisungen Ihres ISV oder von Movere, um Daten an Azure Migrate zu senden.

   
