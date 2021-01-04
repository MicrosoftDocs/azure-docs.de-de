---
title: Verwenden der mehrstufigen Azure Active Directory-Authentifizierung
description: Azure SQL-Datenbank, Azure SQL Managed Instance und Azure Synapse Analytics unterstützen Verbindungen in SQL Server Management Studio (SSMS) mithilfe der universellen Active Directory-Authentifizierung.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
ms.custom: seoapril2019, has-adal-ref, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 09/28/2020
tags: azure-synapse
ms.openlocfilehash: 9afad44bcf67478a81e75c17d0ff8ffc6d8c65aa
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94841128"
---
# <a name="using-multi-factor-azure-active-directory-authentication"></a>Verwenden der mehrstufigen Azure Active Directory-Authentifizierung
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Azure SQL-Datenbank, Azure SQL Managed Instance und Azure Synapse Analytics unterstützen Verbindungen mit [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) mithilfe der *universellen Azure Active Directory-Authentifizierung mit mehrstufiger Authentifizierung*. In diesem Artikel werden die Unterschiede zwischen den verschiedenen Authentifizierungsoptionen sowie die Einschränkungen bei Verwendung der universellen Authentifizierung in Azure Active Directory (Azure AD) für Azure SQL-Datenbank erörtert.

**Herunterladen der aktuellen Version von SSMS:** Laden Sie die neueste Version von SSMS unter [Herunterladen von SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) auf den Clientcomputer herunter. 

[!INCLUDE[ssms-connect-azure-ad](../includes/ssms-connect-azure-ad.md)]


Verwenden Sie für alle Features in diesem Artikel mindestens die Version 17.2 vom Juli 2017. Das letzte Verbindungsdialogfeld sollte ungefähr wie folgt aussehen:

  ![Screenshot des Dialogfelds „Mit Server verbinden“ in SQL Server Management Studio mit den Einstellungen für Servertyp, Servername und Authentifizierung](./media/authentication-mfa-ssms-overview/1mfa-universal-connect.png)

## <a name="authentication-options"></a>Authentifizierungsoptionen

Es gibt zwei nicht interaktive Authentifizierungsmodelle für Azure AD, die in zahlreichen verschiedenen Anwendungen (ADO.NET, JDCB, ODC usw.) verwendet werden können. Bei diesen beiden Methoden werden niemals Popupdialogfelder angezeigt:

- `Azure Active Directory - Password`
- `Azure Active Directory - Integrated`

Die folgende interaktive Methode unterstützt auch Azure AD Multi-Factor Authentication (MFA): 

- `Azure Active Directory - Universal with MFA`

Azure AD MFA bietet Schutz für den Zugriff auf Daten sowie Anwendungen und erfüllt gleichzeitig Benutzeranforderungen nach einem einfachen Anmeldevorgang. Diese Lösung ermöglicht eine sichere Authentifizierung über eine Reihe einfacher Überprüfungsoptionen: Telefonanruf, SMS, Smartcards mit PIN oder Benachrichtigung in einer mobilen App. Benutzer können ihre bevorzugte Methode wählen. Bei der interaktiven MFA mit Azure AD kann ein Popupdialogfeld zur Überprüfung geöffnet werden.

Eine Beschreibung von Azure AD Multi-Factor Authentication finden Sie unter [Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md).
Konfigurationsschritte finden Sie unter [Konfigurieren der Multi-Factor Authentication in Azure SQL-Datenbank für SQL Server Management Studio](authentication-mfa-ssms-configure.md).

### <a name="azure-ad-domain-name-or-tenant-id-parameter"></a>Name der Azure AD-Domäne oder Mandanten-ID-Parameter

Ab [SSMS Version 17](/sql/ssms/download-sql-server-management-studio-ssms) können Benutzer, die aus anderen Azure Active Directory-Instanzen als Gastbenutzer in Active AD importiert wurden, beim Herstellen einer Verbindung den Namen der Azure AD-Domäne oder die Mandanten-ID angeben. Zu den Gastbenutzern zählen Benutzer, die von anderen Azure ADs eingeladen wurden, Microsoft-Konten wie outlook.com, hotmail.com, live.com oder andere Konten wie gmail.com. Durch diese Informationen kann bei der Authentifizierungsmethode `Azure Active Directory - Universal with MFA` die richtige Authentifizierungsstelle identifiziert werden. Diese Option ist für die Unterstützung von Microsoft-Konten (MSA) wie outlook.com, hotmail.com oder live.com oder Nicht-MSA-Konten erforderlich. 

Alle diese Gastbenutzer, die mithilfe der universellen Authentifizierung authentifiziert werden sollen, müssen den Namen ihrer Azure AD-Domäne oder ihre Mandanten-ID eingeben. Dieser Parameter stellt den Namen der aktuellen Azure AD-Domäne oder die aktuelle Mandanten-ID dar, mit der der logische Azure SQL-Server verknüpft ist. Wenn der logische SQL-Server beispielsweise mit der Azure AD-Domäne `contosotest.onmicrosoft.com` verbunden ist, in der der Benutzer `joe@contosodev.onmicrosoft.com` als importierter Benutzer aus der Azure AD-Domäne `contosodev.onmicrosoft.com` gehostet wird, lautet der erforderliche Domänenname für die Authentifizierung dieses Benutzers `contosotest.onmicrosoft.com`. Wenn der Benutzer ein nativer Benutzer des mit dem logischen SQL-Server verknüpften Azure AD ist und kein MSA-Konto darstellt, sind weder Domänenname noch Mandanten-ID erforderlich. So geben Sie den Parameter ein (ab SSMS Version 17.2)


1. Stellen Sie in SSMS eine Verbindung her. Geben Sie den Servernamen ein, und wählen Sie die Authentifizierung **Azure Active Directory: universell mit MFA** aus. Fügen Sie den **Benutzernamen** hinzu, mit dem Sie sich anmelden möchten.
1. Aktivieren Sie das Kontrollkästchen **Optionen**, und wechseln Sie zur Registerkarte **Verbindungseigenschaften**. Füllen Sie das Dialogfeld **Verbindung mit Datenbank herstellen** für Ihre Datenbank aus. Aktivieren Sie das Kontrollkästchen **AD-Domänenname oder Mandanten-ID**, und geben Sie die Authentifizierungsstelle an, z.B. den Domänennamen (**contosotest.onmicrosoft.com**) oder die GUID der Mandanten-ID. 

   ![Screenshot der Registerkarte „Verbindungseigenschaften“ mit hervorgehobenen Einstellungen für die Verbindung mit der Datenbank und dem AD-Domänennamen oder der Mandanten-ID](./media/authentication-mfa-ssms-overview/mfa-tenant-ssms.png)

Wenn Sie SSMS 18.x oder höher ausführen, wird der AD-Domänenname oder die Mandanten-ID nicht mehr für Gastbenutzer benötigt, weil diese Angaben von Version 18.x oder höher automatisch erkannt werden.

   ![Screenshot der Registerkarte „Verbindungseigenschaften“ im Dialogfeld „Mit Server verbinden“ in SSMS. Im Feld „Mit Datenbank verbinden“ ist „MyDatabase“ ausgewählt.](./media/authentication-mfa-ssms-overview/mfa-no-tenant-ssms.png)

### <a name="azure-ad-business-to-business-support"></a>Azure AD-Business-to-Business-Unterstützung

> [!IMPORTANT]
> Die Unterstützung für Gastbenutzer für das Herstellen einer Verbindung mit Azure SQL-Datenbank, SQL Managed Instance und Azure Synapse, ohne dass diese Mitglied einer Gruppe sein müssen, befindet sich derzeit in der **Public Preview**. Weitere Informationen finden Sie unter [Erstellen von Azure AD-Gastbenutzern und Festlegen als Azure AD-Administrator](authentication-aad-guest-users.md).

Azure AD-Benutzer, die für Azure AD B2B-Szenarien als Gastbenutzer unterstützt werden (siehe [Was ist Azure B2B Collaboration?](../../active-directory/external-identities/what-is-b2b.md)), können nur als Mitglieder einer Gruppe im zugehörigen Azure AD eine Verbindung mit SQL-Datenbank und Azure Synapse herstellen und müssen dann mit der Transact-SQL-Anweisung [CREATE USER](/sql/t-sql/statements/create-user-transact-sql) in einer bestimmten Datenbank manuell zugeordnet werden. Wenn beispielsweise `steve@gmail.com` zu Azure AD `contosotest` (mit der Azure AD-Domäne `contosotest.onmicrosoft.com`) eingeladen wird, muss eine Azure AD-Gruppe, z. B. `usergroup` in dem Azure AD erstellt werden, das `steve@gmail.com` als Mitglied enthält. Diese Gruppe muss dann von einem Azure AD-SQL-Administrator oder Azure AD-DBO durch Ausführen der Transact-SQL-Anweisung `MyDatabase` für eine bestimmte Datenbank (z. B. `CREATE USER [usergroup] FROM EXTERNAL PROVIDER`) erstellt werden. 

Nach dem Erstellen des Datenbankbenutzers kann der Benutzer `steve@gmail.com` sich mithilfe der SSMS-Authentifizierungsoption `Azure Active Directory – Universal with MFA` bei `MyDatabase` anmelden. Standardmäßig verfügt die `usergroup` nur über die Berechtigung für Verbindungen. Jeder weitere Datenzugriff muss von einem Benutzer mit ausreichenden Berechtigungen in der Datenbank [gewährt](/sql/t-sql/statements/grant-transact-sql) werden. 

> [!NOTE]
> Wenn Sie für SSMS 17.x `steve@gmail.com` als Gastbenutzer verwenden möchten, müssen Sie das Feld **AD-Domänennamen oder Mandanten-ID** aktivieren und den AD-Domänennamen `contosotest.onmicrosoft.com` im Dialogfeld **Verbindungseigenschaft** hinzufügen. Die Option **AD-Domänenname oder Mandanten-ID** wird nur für die Authentifizierung **Azure Active Directory: universell mit MFA** unterstützt. Andernfalls ist das Kontrollkästchen abgeblendet.

## <a name="universal-authentication-limitations"></a>Einschränkungen der universellen Authentifizierung

- SSMS und SqlPackage.exe sind die einzigen Tools, die derzeit für MFA über die universelle Active Directory-Authentifizierung aktiviert sind.
- SSMS Version 17.2 unterstützt den gleichzeitigen Zugriff durch mehrere Benutzer mithilfe der universellen Authentifizierung mit mehrstufiger Authentifizierung. In Version 17.0 und 17.1 von SSMS konnte sich nur ein einziges Azure Active Directory-Konto über die universelle Authentifizierung bei einer SSMS-Instanz anmelden. Für die Anmeldung mit einem anderen Azure AD-Konto müssen Sie eine andere SSMS-Instanz verwenden. Diese Einschränkung ist auf die universelle Active Directory-Authentifizierung beschränkt. Sie können sich mit den Authentifizierungen `Azure Active Directory - Password`, `Azure Active Directory - Integrated` oder `SQL Server Authentication` bei einem anderen Server anmelden.
- SSMS unterstützt die universelle Active Directory-Authentifizierung für den Objekt-Explorer, den Abfrage-Editor und die Abfragespeichervisualisierung.
- SSMS Version 17.2 bietet Unterstützung von DacFx Wizard zum Exportieren/Extrahieren/Bereitstellen von Daten aus Datenbanken. Sobald ein bestimmter Benutzer über das erste Authentifizierungsdialogfeld mithilfe der universellen Authentifizierung authentifiziert wurde, funktioniert DacFx Wizard genau wie bei allen anderen Authentifizierungsmethoden.
- Die SSMS-Tabellendesigner unterstützt die universelle Authentifizierung nicht.
- Es bestehen keine weiteren Softwareanforderungen für die universelle Active Directory-Authentifizierung – mit einer Ausnahme: Sie müssen eine unterstützte SSMS-Version verwenden.  
- Die aktuelle Version der Active Directory-Authentifizierungsbibliothek (ADAL) für die universelle Authentifizierung finden Sie unter folgendem Link: [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).  

## <a name="next-steps"></a>Nächste Schritte

- Konfigurationsschritte finden Sie unter [Konfigurieren der Multi-Factor Authentication in Azure SQL-Datenbank für SQL Server Management Studio](authentication-mfa-ssms-configure.md).
- Gewähren Sie anderen Benutzern Zugriff auf Ihre Datenbank: [SQL-Datenbankauthentifizierung und -Autorisierung: Gewähren von Zugriff](logins-create-manage.md)  
- Stellen Sie sicher, dass andere Benutzer über die Firewall eine Verbindung herstellen können: [Konfigurieren einer Firewallregel auf Serverebene mit dem Azure-Portal](firewall-configure.md)  
- [Konfigurieren und Verwalten der Azure Active Directory-Authentifizierung mit SQL-Datenbank oder Azure Synapse](authentication-aad-configure.md)
- [Erstellen von Azure AD-Gastbenutzern und Festlegen als Azure AD-Administrator](authentication-aad-guest-users.md) 
- [Microsoft SQL Server Data-Tier Application Framework (17.0.0 GA)](https://www.microsoft.com/download/details.aspx?id=55088)  
- [SQLPackage.exe](/sql/tools/sqlpackage)  
- [Importieren einer BACPAC-Datei in eine neue Datenbank](database-import.md)  
- [Exportieren einer Datenbank in eine BACPAC-Datei](database-export.md)  
- C#-Schnittstelle [IUniversalAuthProvider-Schnittstelle](/dotnet/api/microsoft.sqlserver.dac.iuniversalauthprovider)  
- Bei Verwendung der Authentifizierung **Azure Active Directory: universell mit MFA** ist ab [SSMS 17.3](/sql/ssms/download-sql-server-management-studio-ssms) die ADAL-Ablaufverfolgung verfügbar. Die ADAL-Ablaufverfolgung ist standardmäßig deaktiviert. Sie können sie aktivieren, indem Sie unter **Azure-Dienste** > **Azure-Cloud** > **Ablaufverfolgungsebene für ADAL-Ausgabefenster** im Menü **Optionen** die **Tools** verwenden und im Menü **Ansicht** die Option **Ausgabe** aktivieren. Die Ablaufverfolgungen im Ausgabefenster sind verfügbar, wenn Sie die Option **Azure Active Directory** aktivieren.