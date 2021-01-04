---
title: 'Beispiel für eine mehrinstanzenfähige App: Wingtip SaaS'
description: Dieser Artikel enthält Anweisungen zum Installieren und Ausführen der mehrinstanzenfähigen SaaS-Beispielanwendung, die Azure SQL-Datenbank verwendet – in diesem Fall Wingtip-Tickets.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/18/2018
ms.openlocfilehash: aa9215fa001fb117000eb6a68867ddd46fac9b92
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92780324"
---
# <a name="general-guidance-for-working-with-wingtip-tickets-sample-saas-apps"></a>Allgemeine Anweisungen zum Arbeiten mit den Wingtip-Tickets-Beispielanwendungen für SaaS
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Dieser Artikel enthält allgemeine Anweisungen zum Ausführen der Wingtip-Tickets-SaaS-Beispielanwendungen, die Azure SQL-Datenbank verwenden.

## <a name="download-and-unblock-the-wingtip-tickets-saas-scripts"></a>Herunterladen und Entsperren der Wingtip-Tickets-SaaS-Skripts

Ausführbare Inhalte (Skripts, DLLs) können durch Windows blockiert werden, wenn ZIP-Dateien aus einer externen Quelle heruntergeladen und extrahiert werden. Führen Sie bei der Extraktion der Skripts aus einer ZIP-Datei **die nachfolgenden Schritte durch, um die Blockierung der ZIP-Datei vor der Extraktion aufzuheben** . Hierdurch wird sichergestellt, dass die Skripts ausgeführt werden dürfen.

1. Navigieren Sie zum Wingtip-Tickets-SaaS-GitHub-Repository für das Datenbank-Mandantenmuster, das Sie kennenlernen möchten:
    - [WingtipTicketsSaaS-StandaloneApp](https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp)
    - [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant)
    - [WingtipTicketsSaaS-MultiTenantDb](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb)
2. Klicken Sie auf **Klonen oder herunterladen** .
3. Klicken Sie auf **ZIP herunterladen** , und speichern Sie die Datei.
4. Klicken Sie mit der rechten Maustaste auf die Zip-Datei, und wählen Sie **Eigenschaften** aus. Der Name der Zip-Datei entspricht dabei dem Namen des Repositorys. (Beispiel: _WingtipTicketsSaaS-DbPerTenant-master.zip_ )
5. Wählen Sie auf der Registerkarte **Allgemein** die Option **Blockierung aufheben** .
6. Klicken Sie auf **OK** .
7. Extrahieren Sie die Dateien.

Skripts befinden sich im Ordner *..\\Learning Modules* (Lernmodule).


## <a name="working-with-the-wingtip-tickets-powershell-scripts"></a>Arbeiten mit den Wingtip-Tickets-PowerShell-Skripts

Um das Beispiel optimal nutzen zu können, müssen Sie sich mit den bereitgestellten Skripts auseinandersetzen. Verwenden Sie Haltepunkte, gehen Sie die Skripts schrittweise ausführend durch, und untersuchen Sie die Implementierung der verschiedenen SaaS-Muster. Um mühelos die bereitgestellten Skripts und Module durchzugehen und auf diese Weise bestmöglich zu verstehen, wird die Verwendung der [PowerShell ISE](/powershell/scripting/components/ise/introducing-the-windows-powershell-ise) empfohlen.

### <a name="update-the-configuration-file-for-your-deployment"></a>Aktualisieren der Konfigurationsdatei für Ihre Bereitstellung

Bearbeiten Sie die Datei **UserConfig.psm1** mit dem Ressourcengruppen- und Benutzerwert, den Sie bei der Bereitstellung festlegen:

1. Öffnen Sie die *PowerShell ISE* , und laden Sie ...\\Learning Modules\\*UserConfig.psm1* .
2. Aktualisieren Sie *ResourceGroupName* und *Name* mit den jeweiligen Werten für Ihre Bereitstellung (nur in den Zeilen 10 und 11).
3. Speichern Sie die Änderungen!

Indem Sie diese Werte hier festlegen, wird es Ihnen erspart, diese bereitstellungsspezifischen Werte in jedem Skript aktualisieren zu müssen.

### <a name="execute-the-scripts-by-pressing-f5"></a>Führen Sie die Skripts aus, indem Sie F5 drücken.

Zur Navigation in Ordnern verwenden die verschiedenen Skripts *$PSScriptRoot* . *$PSScriptRoot* wird nur ausgewertet, wenn die Skripts durch Drücken der Taste **F5** ausgeführt werden.    Das Markieren und Ausführen einer Auswahl ( **F8** ) kann zu Fehlern führen. Drücken Sie daher zum Ausführen von Skripts **F5** .

### <a name="step-through-the-scripts-to-examine-the-implementation"></a>Schrittweises Durcharbeiten der Skripts zum Überprüfen der Implementierung

Die beste Möglichkeit, die Skripts zu verstehen, besteht darin, die jeweiligen Funktionen durchzugehen. Sehen Sie sich die enthaltenen **Demo-** -Skripts an, die einen einfachen allgemeinen Workflow darstellen. Die **Demo -** -Skripts veranschaulichen die erforderlichen Schritte zur Durchführung der einzelnen Aufgaben. Legen Sie daher die Haltepunkte fest, und führen Sie einen Drilldown auf die einzelnen Aufrufe durch, um Implementierungsdetails für die verschiedenen SaaS-Muster anzuzeigen.

Tipps zum Prüfen und schrittweisen Ausführen der PowerShell-Skripts:

- Öffnen Sie die **Demo-** -Skripts in der PowerShell ISE.
- Drücken Sie zum Ausführen oder Fortfahren die Taste **F5** . (Es wird davon abgeraten, **F8** zu drücken, da *$PSScriptRoot* beim Ausführen einer Auswahl aus einem Skript nicht ausgewertet wird.)
- Platzieren Sie Haltepunkte, indem Sie auf eine Zeile klicken bzw. diese auswählen und **F9** drücken.
- Überspringen Sie eine Funktion oder einen Skriptaufruf durch Drücken von **F10** .
- Führen Sie eine Funktion oder einen Skriptaufruf als Einzelschritt aus, indem Sie **F11** drücken.
- Führen Sie die aktuelle Funktion bzw. den aktuellen Skriptaufruf bis zum Rücksprung aus, indem Sie **UMSCHALT+F11** drücken.


## <a name="explore-database-schema-and-execute-sql-queries-using-ssms"></a>Untersuchen des Datenbankschemas und Ausführen von SQL-Abfragen mit SSMS

Verwenden Sie [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms), um eine Verbindung mit den Anwendungsservern und -datenbanken herzustellen und diese zu durchsuchen.

Die Bereitstellung verfügt anfänglich über Mandanten und Katalogserver, mit denen Verbindungen hergestellt werden können. Die Benennung der Server hängt vom Mandantenmuster der Datenbank ab (Einzelheiten nachfolgend).

   - **Eigenständige Anwendung:** Server für jeden Mandanten (Beispiel: *contosoconcerthall-&lt;Benutzer&gt;* -Server) und *catalog-sa-&lt;Benutzer&gt;*
   - **Datenbank pro Mandant:** *tenants1-dpt-&lt;Benutzer&gt;* und *catalog-dpt-&lt;Benutzer&gt;* -Server
   - **Mehrinstanzenfähige Datenbank:** *tenants1-mt-&lt;Benutzer&gt;* - und *catalog-mt-&lt;Benutzer&gt;* -Server

Um sicherzustellen, dass erfolgreich eine Verbindung mit der Demo hergestellt wird, wird allen Servern eine [Firewallregel](firewall-configure.md) zugewiesen, die alle IP-Adressen zulässt.


1. Öffnen Sie *SSMS* , und stellen Sie eine Verbindung mit den Mandanten her. Der Servername hängt vom gewählten Mandantenmuster der Datenbank ab (Einzelheiten nachfolgend):
    - **Eigenständige Anwendung:** Server der einzelnen Mandanten (Beispiel: *contosoconcerthall-&lt;Benutzer&gt;.database.windows.net* )
    - **Datenbank pro Mandant:** *tenants1-dpt-&lt;Benutzer&gt;.database.windows.net*
    - **Mehrinstanzenfähige Datenbank:** *tenants1-mt-&lt;Benutzer&gt;.database.windows.net*
2. Klicken Sie auf **Verbinden** > **Datenbank-Engine...** :

   ![Katalogserver](./media/saas-tenancy-wingtip-app-guidance-tips/connect.png)

3. Die folgenden Anmeldeinformationen gelten für die Demo: Anmeldung = *developer* , Kennwort = *P\@ssword1*

    Die Abbildung unten stellt die Anmeldung für das Muster *Datenbank pro Mandant* dar.
    ![connection](./media/saas-tenancy-wingtip-app-guidance-tips/tenants1-connect.png)



4. Wiederholen Sie die Schritte 2 und 3, und stellen Sie die Verbindung zum Katalogserver her (die spezifischen Servernamen, die auf dem gewählten Datenbank-Mandantenmuster basieren, finden Sie unten)
    - **Eigenständige Anwendung:** *catalog-sa-&lt;Benutzer&gt;.database.windows.net*
    - **Datenbank pro Mandant:** *catalog-dpt-&lt;Benutzer&gt;.database.windows.net*
    - **Mehrinstanzenfähige Datenbank:** *catalog-mt-&lt;Benutzer&gt;.database.windows.net*


Nachdem die Verbindung erfolgreich hergestellt wurde, werden alle Server angezeigt. Ihre Liste der Datenbanken kann variieren, je nachdem, welche Mandanten Sie bereitgestellt haben.

Die Abbildung unten stellt die Anmeldung für das Muster *Datenbank pro Mandant* dar.

![Objekt-Explorer](./media/saas-tenancy-wingtip-app-guidance-tips/object-explorer.png)



## <a name="next-steps"></a>Nächste Schritte
- [Bereitstellen der eigenständigen SaaS-Anwendung Wingtip Tickets](./saas-standaloneapp-get-started-deploy.md)
- [Bereitstellen der SaaS-Anwendung Wingtips Tickets mit einer Datenbank pro Mandant](./saas-dbpertenant-get-started-deploy.md)
- [Bereitstellen der mehrinstanzenfähigen Wingtip Tickets-SaaS-Datenbankanwendung](./saas-multitenantdb-get-started-deploy.md)