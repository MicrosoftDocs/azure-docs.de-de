---
author: rothja
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 10/26/2018
ms.author: jroth
ms.openlocfilehash: 297317ff33d88d6390220980ef35f2538579e310
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178032"
---
### <a name="open-tcp-ports-in-the-windows-firewall-for-the-default-instance-of-the-database-engine"></a>Öffnen der TCP-Ports in der Windows-Firewall für die Standardinstanz der Datenbank-Engine
1. Herstellen der Verbindung mit dem virtuellen Computer über Remotedesktop Weitere Informationen zum Herstellen einer Verbindung mit dem virtuellen Computer finden Sie unter [Öffnen der VM mit Remotedesktop](../articles/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision.md#remotedesktop).
2. Geben Sie nach der Anmeldung auf dem Startbildschirm **WF.msc** ein, und drücken Sie dann die EINGABETASTE.
   
    ![Starten des Firewall-Programms](./media/virtual-machines-sql-server-connection-steps/12Open-WF.png)
3. Klicken Sie in **Windows-Firewall mit erweiterter Sicherheit** im linken Bereich mit der rechten Maustaste auf **Eingehende Regeln**, und klicken Sie dann im Aktionsbereich auf **Neue Regel**.
   
    ![Neue Regel](./media/virtual-machines-sql-server-connection-steps/13New-FW-Rule.png)
4. Wählen Sie im Dialogfeld **Assistent für neue eingehende Regel** unter **Regeltyp** die Option **Port** aus, und klicken Sie dann auf **Weiter**.
5. Verwenden Sie im Dialogfeld **Protokolle und Ports** die Standardeinstellung **TCP**. Geben Sie im Feld **Bestimmte lokale Ports** die Portnummer der Instanz der Datenbank-Engine ein (**1433** für die Standardinstanz oder die von Ihnen im Endpunktschritt ausgewählte Nummer für den privaten Port).
   
    ![TCP-Port 1433](./media/virtual-machines-sql-server-connection-steps/14Port-1433.png)
6. Klicken Sie auf **Weiter**.
7. Klicken Sie im Dialogfeld **Aktion** auf **Verbindung zulassen** und anschließend auf **Weiter**.
   
    **Sicherheitshinweis:** Die Auswahl von **Verbindung zulassen, wenn sie sicher ist** kann für zusätzliche Sicherheit sorgen. Wählen Sie diese Option, wenn Sie zusätzliche Sicherheitsoptionen in Ihrer Umgebung konfigurieren möchten.
   
    ![Verbindungen zulassen](./media/virtual-machines-sql-server-connection-steps/15Allow-Connection.png)
8. Klicken Sie im Dialogfeld **Profil** auf **Öffentlich**, **Privat** und **Domäne**. Klicken Sie auf **Weiter**.
   
    **Sicherheitshinweis:**  Die Auswahl von **Öffentlich** ermöglicht den Zugriff über das Internet. Sie sollten möglichst ein restriktiveres Profil auswählen.
   
    ![Öffentliches Profil](./media/virtual-machines-sql-server-connection-steps/16Public-Private-Domain-Profile.png)
9. Geben Sie im Dialogfeld **Name** einen Namen und eine Beschreibung für diese Regel ein, und klicken Sie dann auf **Fertig stellen**.
   
    ![Regelname](./media/virtual-machines-sql-server-connection-steps/17Rule-Name.png)

Öffnen Sie gegebenenfalls zusätzliche Ports für andere Komponenten. Weitere Informationen finden Sie unter [Konfigurieren der Windows-Firewall für den SQL Server-Zugriff](https://msdn.microsoft.com/library/cc646023.aspx).

### <a name="configure-sql-server-to-listen-on-the-tcp-protocol"></a>Konfigurieren von SQL Server für das Lauschen des TCP-Protokolls

[!INCLUDE [Enable TCP](virtual-machines-sql-server-connection-tcp-protocol.md)]

### <a name="configure-sql-server-for-mixed-mode-authentication"></a>Konfigurieren von SQL Server für die Authentifizierung mit gemischtem Modus
Die SQL Server-Datenbank-Engine kann ohne Domänenumgebung keine Windows-Authentifizierung nutzen. Um von einem anderen Computer aus eine Verbindung mit der Datenbank-Engine herzustellen, konfigurieren Sie SQL Server für die Authentifizierung im gemischtem Modus. Die Authentifizierung mit gemischtem Modus ermöglicht sowohl die SQL Server-Authentifizierung als auch die Windows-Authentifizierung.

> [!NOTE]
> Die Konfiguration der Authentifizierung mit gemischtem Modus ist möglicherweise nicht notwendig, wenn Sie ein Azure Virtual Network mit einer konfigurierten Domänenumgebung eingerichtet haben.
> 
> 

1. Geben Sie während der Verbindung mit dem virtuellen Computer auf der Startseite **SQL Server Management Studio** ein, und klicken Sie auf das ausgewählte Symbol.
   
    Wenn Sie Management Studio erstmalig öffnen, muss dieses eine Management Studio-Benutzerumgebung erstellen. Dies kann einige Zeit dauern.
2. Management Studio zeigt das Dialogfeld **Verbindung mit Server herstellen** an. Geben Sie im Feld **Servername** den Namen des virtuellen Computers ein, der über den Objekt-Explorer mit der Datenbank-Engine verbunden werden soll. (Statt des Namens des virtuellen Computers können Sie auch **(lokal)** oder einen einzelnen Punkt als **Servername** verwenden.) Wählen Sie **Windows-Authentifizierung**, und lassen Sie ***Ihr_VM-Name\Ihr_lokaler_Administrator*** im Feld **Benutzername** unverändert. Klicken Sie auf **Verbinden**.
   
    ![Verbindung mit Server herstellen](./media/virtual-machines-sql-server-connection-steps/19Connect-to-Server.png)
3. Klicken Sie im Objekt-Explorer von SQL Server Management Studio mit der rechten Maustaste auf die Instanz von SQL Server (den Namen des virtuellen Computers), und klicken Sie dann auf **Eigenschaften**.
   
    ![Servereigenschaften](./media/virtual-machines-sql-server-connection-steps/20Server-Properties.png)
4. Wählen Sie auf der Seite **Sicherheit** unter **Serverauthentifizierung** die Option **SQL Server- und Windows-Authentifizierungsmodus**, und klicken Sie dann auf **OK**.
   
    ![Authentifizierungsmodus auswählen](./media/virtual-machines-sql-server-connection-steps/21Mixed-Mode.png)
5. Klicken Sie im Dialogfeld von SQL Server Management Studio auf **OK** , um zu bestätigen, dass es erforderlich ist, SQL Server neu zu starten.
6. Klicken Sie im Objekt-Explorer mit der rechten Maustaste auf den Server, und klicken Sie dann auf **Neu starten**. (Wenn SQL Server-Agent ausgeführt wird, muss dieser ebenfalls neu gestartet werden.)
   
    ![Neu starten](./media/virtual-machines-sql-server-connection-steps/22Restart2.png)
7. Klicken Sie im Dialogfeld von SQL Server Management Studio auf **Ja** , um zu bestätigen, dass SQL Server neu gestartet werden soll.

### <a name="create-sql-server-authentication-logins"></a>Erstellen von Anmeldenamen für die SQL Server-Authentifizierung
Um von einem anderen Computer aus eine Verbindung mit der Datenbank-Engine herzustellen, müssen Sie mindestens einen Anmeldenamen für die SQL Server-Authentifizierung erstellen.

1. Erweitern Sie in SQL Server Management Studio-Objekt-Explorer den Ordner der Serverinstanz, in der der neue Anmeldename erstellt werden soll.
2. Klicken Sie mit der rechten Maustaste auf den Ordner **Sicherheit**, zeigen Sie auf **Neu**, und wählen Sie **Anmeldung** aus.
   
    ![Neue Anmeldung](./media/virtual-machines-sql-server-connection-steps/23New-Login.png)
3. Geben Sie im Dialogfeld **Anmeldung – Neu** auf der Seite **Allgemein** im Feld **Anmeldename** den Namen des neuen Benutzers ein.
4. Wählen Sie **SQL Server-Authentifizierung**.
5. Geben Sie im Feld **Kennwort** ein Kennwort für den neuen Benutzer ein. Geben Sie dieses Kennwort erneut im Feld **Kennwort bestätigen** ein.
6. Wählen Sie die erforderlichen Optionen aus, die im Zusammenhang mit dem Kennwort erzwungen werden sollen (**Kennwortrichtlinie erzwingen**, **Ablauf des Kennworts erzwingen** und **Benutzer muss das Kennwort bei der nächsten Anmeldung ändern**). Wenn Sie diese Anmeldung für sich selbst verwenden, ist eine Änderung des Kennworts bei der nächsten Anmeldung nicht erforderlich.
7. Wählen Sie aus der Liste **Standarddatenbank** eine Standarddatenbank für den Anmeldenamen aus. **master** ist die Standardoption. Falls Sie noch keine Benutzerdatenbank erstellt haben, lassen Sie diese Einstellung auf **master**.
   
    ![Anmeldenameneigenschaften](./media/virtual-machines-sql-server-connection-steps/24Test-Login.png)
8. Falls es sich um den ersten Anmeldenamen handelt, den Sie erstellen, werden Sie diesen Anmeldenamen wahrscheinlich als SQL Server-Administrator bezeichnen. Prüfen Sie in diesem Fall **sysadmin** auf der Seite **Serverrollen**.
   
   > [!NOTE]
   > Mitglieder der festen Serverrolle sysadmin haben vollständige Kontrolle über die Datenbank-Engine. Sie sollten die Mitgliedschaft zu dieser Rolle sorgfältig beschränken.
   > 
   > 
   
   ![Serverrollen](./media/virtual-machines-sql-server-connection-steps/25sysadmin.png)
9. Klicken Sie auf OK.

Weitere Informationen zu Anmeldenamen für SQL Server finden Sie unter [Erstellen eines Anmeldenamens](https://msdn.microsoft.com/library/aa337562.aspx).

