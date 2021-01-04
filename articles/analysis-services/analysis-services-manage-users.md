---
title: Authentifizierung und Benutzerberechtigungen in Azure Analysis Services | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Azure Analysis Services Azure Active Directory (Azure AD) zur Identitätsverwaltung und Benutzerauthentifizierung verwendet.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 551bae56565140da3754e74a23b1cc18087f1171
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96487438"
---
# <a name="authentication-and-user-permissions"></a>Authentifizierung und Benutzerberechtigungen

Azure Analysis Services verwendet Azure Active Directory (Azure AD) zur Identitätsverwaltung und Benutzerauthentifizierung. Jeder Benutzer, der einen Azure Analysis Services-Server erstellt, verwaltet oder eine Verbindung mit diesem herstellt, muss über eine gültige Benutzeridentität in einem [Azure AD-Mandanten](../active-directory/fundamentals/active-directory-whatis.md) im selben Abonnement verfügen.

Azure Analysis Services unterstützt die [Azure AD B2B-Zusammenarbeit](../active-directory/external-identities/what-is-b2b.md). Mit B2B können Benutzer außerhalb einer Organisation als Gastbenutzer in ein Azure AD-Verzeichnis eingeladen werden. Gäste können Benutzer aus einem anderen Azure AD-Mandantenverzeichnis oder mit einer gültigen E-Mail-Adresse sein. Wenn ein Benutzer eingeladen wurde und die von Azure per E-Mail gesendete Einladung annimmt, wird die Benutzeridentität dem Mandantenverzeichnis hinzugefügt. Diese Identitäten können Sicherheitsgruppen oder als Mitglieder einer Serveradministrator- oder Datenbankrolle hinzugefügt werden.

![Architektur der Azure Analysis Services-Authentifizierung](./media/analysis-services-manage-users/aas-manage-users-arch.png)

## <a name="authentication"></a>Authentication

In allen Clientanwendungen und Tools werden eine oder mehrere Analysis Services-[Clientbibliotheken](/analysis-services/client-libraries?view=azure-analysis-services-current&preserve-view=true) (AMO, MSOLAP, ADOMD) für die Verbindung mit einem Server verwendet. 

Alle drei Clientbibliotheken unterstützen den interaktiven Azure AD-Ablauf sowie nicht interaktive Authentifizierungsmethoden. Die beiden nicht interaktiven Methoden – die Active Directory-Kennwortauthentifizierung und die integrierte Active Directory-Authentifizierung – können in Anwendungen eingesetzt werden, die AMOMD und MSOLAP verwenden. Bei diesen beiden Methoden werden niemals Popupdialogfelder angezeigt.

Clientanwendungen wie Excel und Power BI Desktop und Tools wie SSMS und Analysis Services-Projekterweiterungen für Visual Studio installieren die aktuellen Versionen der Bibliotheken, wenn sie auf das neueste Release aktualisiert werden. Power BI Desktop, SSMS und Analysis Services-Projekterweiterungen werden monatlich aktualisiert. Excel wird [mit Microsoft 365 aktualisiert](https://support.microsoft.com/office/when-do-i-get-the-newest-features-for-microsoft-365-da36192c-58b9-4bc9-8d51-bb6eed468516). Microsoft 365-Updates erfolgen seltener, und manche Organisationen verwenden den verzögerten Kanal, d. h., dass Updates bis zu drei Monate verzögert werden.

Abhängig von den verwendeten Clientanwendungen oder Tools können sich die Art der Authentifizierung und der Anmeldevorgang unterscheiden. Jede Anwendung unterstützt möglicherweise unterschiedliche Features für die Verbindung mit Clouddiensten wie Azure Analysis Services.

Power BI Desktop, Visual Studio und SSMS unterstützen die universelle Active Directory-Authentifizierung, eine interaktive Methode, die auch die Azure AD Multi-Factor Authentication (MFA) unterstützt. Azure AD MFA hilft beim Schützen des Zugriffs auf Daten und Anwendungen und stellt gleichzeitig ein einfaches Anmeldeverfahren bereit. Es bietet eine sichere Authentifizierung über verschiedene einfache Überprüfungsoptionen (Telefonanruf, SMS, Smartcard mit PIN oder Benachrichtigung in einer mobilen App). Bei der interaktiven MFA mit Azure AD kann ein Popupdialogfeld zur Überprüfung geöffnet werden. **Universelle Authentifizierung wird empfohlen**.

Wenn Sie sich über ein Windows-Konto bei Azure anmelden und universelle Authentifizierung nicht aktiviert oder verfügbar ist (Excel), ist [Active Directory-Verbunddienste (AD FS)](/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs) erforderlich. Mit den Verbunddiensten werden Azure AD- und Microsoft 365-Benutzer über lokale Anmeldeinformationen authentifiziert und können auf Azure-Ressourcen zugreifen.

### <a name="sql-server-management-studio-ssms"></a>SQL Server Management Studio (SSMS)

Azure Analysis Services-Server unterstützen Verbindungen über [SSMS V17.1](/sql/ssms/download-sql-server-management-studio-ssms) und höher mithilfe der Windows-Authentifizierung, Active Directory-Kennwortauthentifizierung und der universellen Active Directory-Authentifizierung. Im Allgemeinen empfiehlt sich die Verwendung der universellen Active Directory-Authentifizierung aus folgenden Gründen:

*  Interaktive und nicht interaktive Authentifizierungsmethoden werden unterstützt.

*  Einladungen von Azure B2B-Gastbenutzern in den Azure AS-Mandanten werden unterstützt. Bei der Herstellung einer Verbindung mit einem Server müssen Gastbenutzer die universelle Active Directory-Authentifizierung auswählen.

*  Multi-Factor Authentication (MFA) wird unterstützt. Azure AD MFA schützt den Zugriff auf Daten und Anwendungen durch eine Reihe von Überprüfungsoptionen: Telefonanruf, Textnachricht, Smartcard mit PIN oder Benachrichtigung über eine mobile App. Bei der interaktiven MFA mit Azure AD kann ein Popupdialogfeld zur Überprüfung geöffnet werden.

### <a name="visual-studio"></a>Visual Studio

Visual Studio stellt die Verbindung mit Azure Analysis Services mithilfe der universellen Active Directory-Authentifizierung mit MFA-Unterstützung her. Benutzer werden bei der ersten Bereitstellung aufgefordert, sich bei Azure anzumelden. Benutzer müssen sich bei Azure mit einem Konto mit Serveradministratorberechtigungen für den Server anmelden, auf dem sie die Bereitstellung durchführen. Bei der ersten Anmeldung bei Azure wird ein Token zugewiesen. Das Token wird für zukünftige Verbindungswiederherstellungen im In-Memory-Cache zwischengespeichert.

### <a name="power-bi-desktop"></a>Power BI Desktop

Power BI Desktop stellt die Verbindung mit Azure Analysis Services mithilfe der universellen Active Directory-Authentifizierung mit MFA-Unterstützung her. Benutzer werden bei der ersten Verbindung aufgefordert, sich bei Azure anzumelden. Benutzer müssen sich bei Azure mit einem Konto anmelden, das in einer Serveradministrator- oder Datenbankrolle enthalten ist.

### <a name="excel"></a>Excel

Excel-Benutzer können eine Verbindung mit einem Server über ein Windows-Konto, eine Organisations-ID (E-Mail-Adresse) oder eine externe E-Mail-Adresse herstellen. Externe E-Mail-Identitäten müssen in Azure AD als Gastbenutzer vorhanden sein.

## <a name="user-permissions"></a>Benutzerberechtigungen

**Serveradministratoren** sind spezifisch für eine Azure Analysis Services-Serverinstanz definiert. Sie stellen eine Verbindung mit Tools wie dem Azure-Portal, SSMS und Visual Studio her, um bestimmte Aufgaben durchzuführen, z. B. Hinzufügen von Datenbanken oder Verwalten von Benutzerrollen. Standardmäßig wird der Benutzer, der den Server erstellt, automatisch als Analysis Services-Serveradministrator hinzugefügt. Andere Administratoren können über das Azure-Portal oder über SSMS hinzugefügt werden. Serveradministratoren müssen über ein Konto im Azure AD-Mandanten im selben Abonnement verfügen. Weitere Informationen finden Sie unter [Verwalten von Serveradministratoren](analysis-services-server-admins.md). 

**Datenbankbenutzer** stellen über Clientanwendungen wie Excel oder Power BI eine Verbindung mit Modelldatenbanken her. Benutzer müssen Datenbankrollen hinzugefügt werden. Datenbankrollen definieren die Berechtigungen „Administrator“, „Verarbeiten“ oder „Lesen“ für eine Datenbank. Dabei ist zu beachten, dass Datenbankbenutzer in einer Rolle mit Administratorberechtigungen nicht mit Serveradministratoren identisch sind. Standardmäßig sind Serveradministratoren jedoch auch immer Datenbankadministratoren. Weitere Informationen finden Sie unter [Verwalten von Datenbankrollen und Benutzern](analysis-services-database-users.md).

**Azure-Ressourcenbesitzer**: Ressourcenbesitzer verwalten Ressourcen für ein Azure-Abonnement. Ressourcenbesitzer können über **Zugriffssteuerung** im Azure-Portal oder mit Azure Resource Manager-Vorlagen Azure AD-Benutzeridentitäten zu den Rollen „Besitzer“ oder „Mitwirkender“ innerhalb eines Abonnements hinzufügen. 

![Access Control im Azure-Portal](./media/analysis-services-manage-users/aas-manage-users-rbac.png)

Rollen auf dieser Ebene gelten für Benutzer oder Konten, die sie zum Ausführen von Aufgaben im Portal oder mithilfe von Azure Resource Manager-Vorlagen benötigen. Weitere Informationen finden Sie unter [Rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC)](../role-based-access-control/overview.md). 

## <a name="database-roles"></a>Datenbankrollen

 Für ein tabellarisches Modell definierte Rollen sind Datenbankrollen. Das bedeutet, dass die Rollen Mitglieder mit Azure AD-Benutzern und -Sicherheitsgruppen enthalten, die über die spezifischen Berechtigungen verfügen, mit denen die Aktionen definiert werden, die diese Mitglieder in einer Modelldatenbank ausführen können. Eine Datenbankrolle wird als separates Objekt in der Datenbank erstellt und gilt nur für die Datenbank, in der diese Rolle erstellt wird.   
  
 Wenn Sie ein neues Projekt für tabellarische Modelle erstellen, weist das Modellprojekt standardmäßig keine Rollen auf. Rollen können im Dialogfeld „Rollen-Manager“ in Visual Studio definiert werden. Werden Rollen während des Modellprojektentwurfs definiert, werden sie nur auf die Arbeitsbereichsdatenbank des Modells angewendet. Bei der Bereitstellung des Modells werden die gleichen Rollen auf das bereitgestellte Modell angewendet. Nach der Bereitstellung eines Modells können Server- und Datenbankadministratoren Rollen und Mitglieder über SSMS verwalten. Weitere Informationen finden Sie unter [Verwalten von Datenbankrollen und Benutzern](analysis-services-database-users.md).
  
## <a name="next-steps"></a>Nächste Schritte

[Verwalten des Zugriffs auf Ressourcen mit Azure Active Directory-Gruppen](../active-directory/fundamentals/active-directory-manage-groups.md)   
[Verwalten von Datenbankrollen und Benutzern](analysis-services-database-users.md)  
[Verwalten von Serveradministratoren](analysis-services-server-admins.md)  
[Rollenbasierte Zugriffssteuerung von Azure (Azure-RBAC)](../role-based-access-control/overview.md)