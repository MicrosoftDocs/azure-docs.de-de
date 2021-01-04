---
title: Cloud-Apps oder -aktionen in Richtlinien für bedingten Zugriff – Azure Active Directory
description: Was sind Cloud-Apps oder -aktionen in einer Richtlinie für bedingten Zugriff in Azure AD?
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 10/16/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1b3b4da4e21bca421b76f820c04ba68375be5ca0
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93307778"
---
# <a name="conditional-access-cloud-apps-or-actions"></a>Bedingter Zugriff: Cloud-Apps oder -aktionen

Cloud-Apps oder -aktionen sind ein wichtiger Bestandteil einer Richtlinie für bedingten Zugriff. Richtlinien für bedingten Zugriff ermöglichen Administratoren das Zuweisen von Steuerelementen zu bestimmten Anwendungen oder Aktionen.

- Administratoren stehen eine Reihe von Anwendungen zur Auswahl. Dazu zählen integrierte Microsoft-Anwendungen und alle [in Azure AD integrierten Anwendungen](../manage-apps/what-is-application-management.md) (Katalog- und Nicht-Kataloganwendungen) sowie über den [Anwendungsproxy](../manage-apps/what-is-application-proxy.md) veröffentlichte Anwendungen.
- Administratoren haben die Wahl, die Richtlinie nicht auf Basis einer Cloudanwendung, sondern basierend auf einer Benutzeraktion zu definieren. Als einzige Aktion wird „Sicherheitsinformationen registrieren (Vorschauversion)“ unterstützt. Hierbei können durch den bedingten Zugriff Steuerelemente rund um die Funktion [Kombinierte Registrierung von Sicherheitsinformationen](../authentication/howto-registration-mfa-sspr-combined.md) erzwungen werden.

![Definieren einer Richtlinie für bedingten Zugriff und Angeben von Cloud-Apps](./media/concept-conditional-access-cloud-apps/conditional-access-cloud-apps-or-actions.png)

## <a name="microsoft-cloud-applications"></a>Microsoft Cloud-Anwendungen

Viele der vorhandenen Microsoft-Cloudanwendungen sind in der Liste der Anwendungen enthalten, die zur Auswahl stehen. 

Administratoren können den folgenden Cloud-Apps von Microsoft eine Richtlinie für bedingten Zugriff zuweisen. Zu einigen Apps wie Office 365 und Microsoft Azure Management gehören mehrere untergeordnete Apps oder Dienste. Die folgende Liste ist nicht vollständig und kann sich ändern.

- [Office 365](#office-365)
- Azure Analysis Services
- Azure DevOps
- [Azure SQL Database und Azure Synapse Analytics](../../azure-sql/database/conditional-access-configure.md)
- Dynamics CRM Online
- Microsoft Application Insights Analytics
- [Microsoft Azure Information Protection](/azure/information-protection/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)
- [Microsoft Azure Management](#microsoft-azure-management)
- Microsoft Azure-Abonnementverwaltung
- Microsoft Cloud App Security
- Portal für die Zugriffssteuerung auf Microsoft Commerce-Tools
- Authentifizierungsdienst für Microsoft Commerce-Tools
- Microsoft Flow
- Microsoft Forms
- Microsoft Intune
- [Microsoft Intune-Registrierung](/intune/enrollment/multi-factor-authentication)
- Microsoft Planner
- Microsoft PowerApps
- Microsoft-Suche in Bing
- Microsoft StaffHub
- Microsoft Stream
- Microsoft Teams
- Exchange Online
- SharePoint
- Yammer
- Office Delve
- Office Sway
- Outlook-Gruppen
- Power BI-Dienst
- Project Online
- Skype for Business Online
- Virtuelles privates Netzwerk (VPN):
- Windows Defender ATP

### <a name="office-365"></a>Office 365

Microsoft 365 stellt cloudbasierte Dienste für Produktivität und Zusammenarbeit wie Exchange, SharePoint und Microsoft Teams bereit. Microsoft 365-Clouddienste sind umfassend integriert, um nahtlose Funktionen für die Zusammenarbeit zu gewährleisten. Diese Integration kann beim Erstellen von Richtlinien zu Verwirrung führen, da einige Apps wie Microsoft Teams Abhängigkeiten von anderen Apps wie SharePoint oder Exchange aufweisen.

Mit der Office 365-App können Sie gleichzeitig auf all diese Dienste abzielen. Wir empfehlen die Verwendung der neuen Office 365-App, statt auf einzelne Cloud-Apps abzuzielen, um Probleme mit [Dienstabhängigkeiten](service-dependencies.md) zu vermeiden. Eine Ausrichtung auf diese Gruppe von Anwendungen trägt dazu bei, Probleme zu vermeiden, die aufgrund inkonsistenter Richtlinien und Abhängigkeiten auftreten können.

Administratoren können wahlweise bestimmte Apps von der Richtlinie ausschließen, indem sie die Office 365-App einbeziehen und bestimmte Apps ihrer Wahl in der Richtlinie ausschließen.

Die Client-App von Office 365 enthält u.a. die folgenden wichtigen Anwendungen:

   - Microsoft Flow
   - Microsoft Forms
   - Microsoft Stream
   - Microsoft To-Do
   - Microsoft Teams
   - Exchange Online
   - SharePoint Online
   - Microsoft 365-Suchdienst
   - Yammer
   - Office Delve
   - Office Online
   - Office.com
   - OneDrive
   - PowerApps
   - Skype for Business Online
   - Sway

### <a name="microsoft-azure-management"></a>Microsoft Azure Management

Die Microsoft Azure Management-Anwendung enthält mehrere zugrundeliegende Dienste. 

   - Azure-Portal
   - Azure Resource Manager-Anbieter
   - APIs für das klassische Bereitstellungsmodell
   - Azure PowerShell
   - Azure CLI
   - Administratorportal für Visual Studio-Abonnements
   - Azure DevOps
   - Azure Data Factory-Portal

> [!NOTE]
> Die Microsoft Azure Management-Anwendung gilt für Azure PowerShell, die die Azure Resource Manager-API aufruft. Sie gilt nicht für Azure AD PowerShell, die Microsoft Graph aufruft.

## <a name="other-applications"></a>Andere Anwendungen

Neben den Microsoft-Apps können Administratoren jede für Azure AD registrierte Anwendung zu Richtlinien für bedingten Zugriff hinzufügen. Dazu zählen u.a. die folgenden Anwendungen: 

- Über den [Azure AD-Anwendungsproxy](../manage-apps/what-is-application-proxy.md) veröffentlichte Anwendungen
- [Aus dem Katalog hinzugefügte Anwendungen](../manage-apps/add-application-portal.md)
- [Benutzerdefinierte Nicht-Kataloganwendungen](../manage-apps/view-applications-portal.md)
- [Ältere Anwendungen, die über App-Bereitstellungscontroller und -netzwerke veröffentlicht wurden](../manage-apps/secure-hybrid-access.md)
- Anwendungen, die [einmaliges Anmelden mit Kennwort](../manage-apps/configure-password-single-sign-on-non-gallery-applications.md) verwenden

> [!NOTE]
> Da die Richtlinie für bedingten Zugriff die Anforderungen für den Zugriff auf einen Dienst festlegt, können Sie sie nicht auf eine (öffentliche/native) Clientanwendung anwenden. Anders ausgedrückt: Die Richtlinie wird nicht direkt in einer Clientanwendung (öffentlichen/nativen Anwendung) festgelegt, sondern angewendet, wenn ein Client einen Dienst aufruft. Eine Richtlinie, die für den SharePoint-Dienst festgelegt wurde, gilt beispielsweise für die Clients, die SharePoint aufrufen. Eine Richtlinie, die für Exchange festgelegt wurde, gilt für den Zugriffsversuch auf E-Mail mithilfe des Outlook-Clients. Aus diesem Grund stehen Clientanwendungen (öffentliche/native Anwendungen) in der Cloud-Apps-Auswahl nicht zur Auswahl zur Verfügung, und die Option „Bedingter Zugriff“ ist in den Anwendungseinstellungen für die in Ihrem Mandanten registrierte Clientanwendung (öffentliche/native Anwendung) nicht verfügbar. 

## <a name="user-actions"></a>Benutzeraktionen

Benutzeraktionen sind Aufgaben, die von einem Benutzer ausgeführt werden können. Die einzige derzeit unterstützte Aktion ist **Sicherheitsinformationen registrieren**. Dadurch kann eine Richtlinie für bedingten Zugriff erzwungen werden, wenn Benutzer, für die die kombinierte Registrierung aktiviert ist, ihre Sicherheitsinformationen zu registrieren versuchen. Weitere Informationen finden Sie im Artikel [Kombinierte Registrierung von Sicherheitsinformationen](../authentication/concept-registration-mfa-sspr-combined.md).

## <a name="next-steps"></a>Nächste Schritte

- [Bedingter Zugriff: Bedingungen](concept-conditional-access-conditions.md)

- [Allgemeine Richtlinien für bedingten Zugriff](concept-conditional-access-policy-common.md)
- [Abhängigkeiten von Clientanwendungen](service-dependencies.md)
