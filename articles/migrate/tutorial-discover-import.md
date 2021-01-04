---
title: Bewerten lokaler Server mithilfe einer importierten CSV-Datei mit der Azure Migrate-Serverbewertung
description: Hier wird beschrieben, wie Sie lokale Server für die Migration zu Azure ermitteln, indem Sie eine importierte CSV-Datei in der Azure Migrate-Serverbewertung verwenden.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 09/14/2020
ms.openlocfilehash: dfa7ee941e2c373b02fe5fb2f2a648a60a677670
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96753108"
---
# <a name="tutorial-assess-servers-using-an-imported-csv-file"></a>Tutorial: Bewerten von Servern mithilfe einer importierten CSV-Datei

Im Rahmen der Migration zu Azure ermitteln Sie den lokalen Bestand und die lokalen Workloads. 

In diesem Tutorial wird gezeigt, wie Sie lokale Computer mit dem Azure Migrate- Serverbewertungstool und einer importierten CSV-Datei (Comma-Separated Value, durch Trennzeichen getrennt) bewerten. 

Wenn Sie eine CSV-Datei verwenden, müssen Sie nicht die Azure Migrate-Appliance zum Ermitteln und Bewerten von Servern einrichten. Sie können steuern, welche Daten Sie in der Datei freigeben, und ein Großteil der Daten ist optional. Diese Methode ist in folgenden Fällen sinnvoll:

- Sie möchten schnell eine anfängliche Bewertung erstellen, bevor Sie die Appliance bereitstellen.
- Sie können die Azure Migrate-Appliance nicht in Ihrer Organisation bereitstellen.
- Sie können keine Anmeldeinformationen freigeben, die den Zugriff auf lokale Server ermöglichen.
- Aufgrund von Sicherheitsbeschränkungen wird verhindert, dass Sie von der Appliance erfasste Daten sammeln und an Azure senden.

> [!NOTE]
> Sie können mithilfe einer CSV-Datei importierte Server nicht migrieren.

In diesem Tutorial lernen Sie Folgendes:
> [!div class="checklist"]
> * Einrichten eines Azure-Kontos
> * Einrichten eines Azure Migrate-Projekts
> * Vorbereiten einer CSV-Datei
> * Importieren der Datei
> * Bewerten von Servern

> [!NOTE]
> In Tutorials wird der schnellste Weg zum Ausprobieren eines Szenarios beschrieben, und nach Möglichkeit werden dabei die Standardoptionen verwendet. 

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

- In einer einzelnen CSV-Datei und einem Azure Migrate-Projekt können bis zu 20.000 Server hinzugefügt werden. 
- Die in der CSV-Datei angegebenen Betriebssystemnamen müssen [unterstützte Namen](#supported-operating-system-names) enthalten und mit diesen identisch sein.


## <a name="prepare-an-azure-user-account"></a>Vorbereiten eines Azure-Benutzerkontos

Zum Erstellen eines Azure Migrate-Projekts benötigen Sie ein Konto mit folgenden Berechtigungen:
- Berechtigungen vom Typ „Mitwirkender“ oder „Besitzer“ für ein Azure-Abonnement
- Berechtigungen zum Registrieren von Azure Active Directory-Apps

Wenn Sie gerade erst ein kostenloses Azure-Konto erstellt haben, sind Sie der Besitzer Ihres Abonnements. Wenn Sie nicht der Besitzer des Abonnements sind, müssen Sie mit dem Besitzer zusammenarbeiten, um die Berechtigungen wie folgt zuzuweisen:

1. Suchen Sie im Azure-Portal nach „Abonnements“, und wählen Sie unter **Dienste** die Option **Abonnements** aus.

    ![Suchfeld, um nach dem Azure-Abonnement zu suchen](./media/tutorial-discover-import/search-subscription.png)

2. Wählen Sie auf der Seite **Abonnements** das Abonnement aus, in dem Sie ein Azure Migrate-Projekt erstellen möchten. 
3. Wählen Sie im Abonnement die Option **Zugriffssteuerung (IAM)**  > **Zugriff überprüfen** aus.
4. Suchen Sie unter **Zugriff überprüfen** nach dem entsprechenden Benutzerkonto.
5. Klicken Sie unter **Rollenzuweisung hinzufügen** auf **Hinzufügen**.

    ![Suchen nach einem Benutzerkonto, um den Zugriff zu überprüfen und eine Rolle zuzuweisen](./media/tutorial-discover-import/azure-account-access.png)

6. Wählen Sie unter **Rollenzuweisung hinzufügen** die Rolle „Mitwirkender“ oder „Besitzer“ und das Konto aus (in unserem Beispiel „azmigrateuser“). Klicken Sie anschließend auf **Speichern**.

    ![Die Seite „Rollenzuweisung“ wird geöffnet, auf der Sie dem Konto eine Rolle zuweisen können.](./media/tutorial-discover-import/assign-role.png)

7. Suchen Sie im Portal nach Benutzern, und wählen Sie unter **Dienste** die Option **Benutzer** aus.
8. Vergewissern Sie sich unter **Benutzereinstellungen**, dass Azure AD-Benutzer Anwendungen registrieren können (standardmäßig auf **Ja** festgelegt).

    ![Überprüfen unter „Benutzereinstellungen“, ob Benutzer Active Directory-Apps registrieren können](./media/tutorial-discover-import/register-apps.png)



## <a name="set-up-a-project"></a>Einrichten eines Projekts

Richten Sie ein neues Azure Migrate-Projekt ein, sofern Sie noch keins besitzen.

1. Wählen Sie im Azure-Portal **Alle Dienste** aus, und suchen Sie nach **Azure Migrate**.
2. Wählen Sie unter **Dienste** die Option **Azure Migrate** aus.
3. Wählen Sie unter **Übersicht** die Option **Create project** (Projekt erstellen) aus.
5. Wählen Sie unter **Create project** (Projekt erstellen) Ihr Azure-Abonnement und die Ressourcengruppe aus. Falls noch keine vorhanden ist, erstellen Sie eine Ressourcengruppe.
6. Geben Sie unter **Projektdetails** den Projektnamen und die geografische Region an, in der Sie das Projekt erstellen möchten. Beachten Sie die unterstützten geografischen Regionen für [öffentliche Clouds](migrate-support-matrix.md#supported-geographies-public-cloud) und [Azure Government-Clouds](migrate-support-matrix.md#supported-geographies-azure-government).

   ![Felder für Projektname und Region](./media/tutorial-discover-import/new-project.png)

7. Klicken Sie auf **Erstellen**.
8. Warten Sie einige Minuten, bis das Azure Migrate-Projekt bereitgestellt wurde.

Das Tool **Azure Migrate- Serverbewertung** wird dem neuen Projekt standardmäßig hinzugefügt.

![Seite mit dem standardmäßig hinzugefügten Serverbewertungstool](./media/tutorial-discover-import/added-tool.png)

## <a name="prepare-the-csv"></a>Vorbereiten der CSV-Datei

Laden Sie die CSV-Vorlage herunter, und fügen Sie ihr Serverinformationen hinzu.

### <a name="download-the-template"></a>Herunterladen der Vorlage

1. Klicken Sie unter **Migrationsziele** > **Server** > **Azure Migrate: Server Assessment** (Azure Migrate-Serverbewertung) auf **Ermitteln**.
2. Wählen Sie unter **Computer ermitteln** die Option **Über CSV importieren** aus.
3. Wählen Sie **Herunterladen** aus, um die CSV-Vorlage herunterzuladen. Alternativ können Sie den [Download direkt durchführen](https://go.microsoft.com/fwlink/?linkid=2109031).

    ![Herunterladen der CSV-Vorlage](./media/tutorial-discover-import/download-template.png)

### <a name="add-server-information"></a>Hinzufügen von Serverinformationen

Sammeln Sie Serverdaten, und fügen Sie sie der CSV-Datei hinzu.

- Zum Sammeln von Daten können Sie diese aus Tools exportieren, die Sie für die lokale Serververwaltung nutzen. Beispiele wären etwa VMware vSphere oder Ihre Konfigurationsverwaltungsdatenbank (CMDB).
- Laden Sie unsere [Beispieldatei](https://go.microsoft.com/fwlink/?linkid=2108405) herunter, um sich die Beispieldaten anzusehen.

In der folgenden Tabelle sind die auszufüllenden Felder zusammengefasst:

**Feldname** | **Obligatorisch** | **Details**
--- | --- | ---
**Servername** | Ja | Wir empfehlen, den vollqualifizierten Domänennamen anzugeben.
**IP-Adresse** | Nein | Serveradresse
**Kerne** | Ja | Anzahl von Prozessorkernen, die dem Server zugeordnet sind
**Memory** | Ja | Gesamter Arbeitsspeicher, der dem Server zugeordnet ist (in MB)
**OS name** (Betriebssystemname) | Ja | Serverbetriebssystem <br/> Betriebssystemnamen, die den Namen in [dieser Liste](#supported-operating-system-names) entsprechen oder diese Namen enthalten, werden von der Bewertung erkannt.
**Betriebssystemversion** | Nein | Betriebssystemversion des Servers
**Betriebssystemarchitektur** | Nein | Architektur des Serverbetriebssystems <br/> Gültige Werte: x64, x86, amd64, 32-Bit oder 64-Bit
**Anzahl der Datenträger** | Nein | Nicht erforderlich, wenn Details zu den einzelnen Datenträgern angegeben werden.
**Disk 1 size** (Größe von Datenträger 1)  | Nein | Die maximale Größe des Datenträgers (in GB).<br/>Sie können Details zu weiteren Datenträgern hinzufügen, indem Sie in der Vorlage [Spalten hinzufügen](#add-multiple-disks). Es ist möglich, bis zu acht Datenträger hinzuzufügen.
**Disk 1 read ops** (Lesevorgänge für Datenträger 1) | Nein | Datenträger-Lesevorgänge pro Sekunde
**Disk 1 write ops** (Schreibvorgänge für Datenträger 1) | Nein | Datenträger-Schreibvorgänge pro Sekunde
**Disk 1 read throughput** (Lesedurchsatz für Datenträger 1) | Nein | Vom Datenträger gelesenen Daten pro Sekunde (in MB pro Sekunde)
**Disk 1 write throughput** (Schreibdurchsatz für Datenträger 1) | Nein | Daten, die pro Sekunde auf den Datenträger geschrieben werden (in MB pro Sekunde)
**Prozentsatz der CPU-Auslastung** | Nein | CPU-Auslastung in Prozent
**Memory utilization percentage** (Prozentuale Auslastung des Arbeitsspeichers) | Nein | Auslastung des Arbeitsspeichers in Prozent
**Total disks read ops** (Gesamte Lesevorgänge für Datenträger) | Nein | Datenträger-Lesevorgänge pro Sekunde
**Total disks write ops** (Gesamte Schreibvorgänge für Datenträger) | Nein | Datenträger-Schreibvorgänge pro Sekunde
**Total disks read throughput** (Gesamter Lesedurchsatz für Datenträger) | Nein | Vom Datenträger gelesene Daten (in MB pro Sekunde)
**Total disks write throughput** (Gesamter Schreibdurchsatz für Datenträger) | Nein | Auf den Datenträger geschriebene Daten (in MB pro Sekunden)
**Network In throughput** (Eingehender Netzwerkdurchsatz) | Nein | Vom Server empfangene Daten (in MB pro Sekunde)
**Network Out throughput** (Ausgehender Netzwerkdurchsatz) | Nein | Vom Server übertragene Daten (in MB pro Sekunde)
**Firmware type** (Firmwaretyp) | Nein | Serverfirmware. Mögliche Werte: „BIOS“ und „UEFI“.
**MAC address** (MAC-Adresse)| Nein | MAC-Adresse des Servers


### <a name="add-operating-systems"></a>Hinzufügen von Betriebssystemen

Bei der Bewertung werden bestimmte Betriebssystemnamen erkannt. Alle von Ihnen angegebenen Namen müssen exakt einer der Zeichenfolgen aus der [Liste mit den unterstützten Namen](#supported-operating-system-names) entsprechen.

### <a name="add-multiple-disks"></a>Hinzufügen mehrerer Datenträger

Die Vorlage enthält Standardfelder für den ersten Datenträger. Sie können ähnliche Spalten für bis zu acht Datenträger hinzufügen.

Fügen Sie beispielsweise die folgenden Spalten hinzu, um alle Felder für einen zweiten Datenträger anzugeben:

- „Disk 2 size“ (Größe von Datenträger 2)
- „Disk 2 read ops“ (Lesevorgänge für Datenträger 2)
- „Disk 2 write ops“ (Schreibvorgänge für Datenträger 2)
- „Disk 2 read throughput“ (Lesedurchsatz für Datenträger 2)
- „Disk 2 write throughput“ (Schreibdurchsatz für Datenträger 2)


## <a name="import-the-server-information"></a>Importieren der Serverinformationen

Importieren Sie die CSV-Datei in die Serverbewertung, nachdem Sie der CSV-Vorlage Informationen hinzugefügt haben.

1. Navigieren Sie in Azure Migrate unter **Computer ermitteln** zu der fertigen Vorlage.
2. Wählen Sie **Importieren** aus.
3. Der Importstatus wird angezeigt.
    - Sollten Warnungen vorliegen, können Sie diese entweder beheben oder ignorieren.
    - Optimieren Sie die Serverinformationen gemäß den Vorschlägen in den Warnungen, um die Genauigkeit von Bewertungen zu verbessern.
    - Wählen Sie zum Anzeigen und Beheben von Warnungen die Option **Download warning details .CSV** (CSV mit Warnungsdetails herunterladen) aus. Dadurch wird die CSV-Datei mit Warnungen heruntergeladen. Überprüfen Sie die Warnungen, und beheben Sie sie bei Bedarf.
    - Sollten Fehler vorliegen (Importstatus: **Fehler**), müssen Sie diese beheben, um den Importvorgang fortsetzen zu können.
        1. Laden Sie die CSV-Datei herunter. Diese enthält nun Fehlerdetails.
        1. Überprüfen und beheben Sie die Fehler nach Bedarf. 
        1. Laden Sie die geänderte Datei erneut hoch.
4. Wenn der Importstatus **Abgeschlossen** lautet, wurden die Serverinformationen importiert. Aktualisieren Sie die Seite, wenn der Import anscheinend noch nicht abgeschlossen ist.

## <a name="update-server-information"></a>Aktualisieren von Serverinformationen

Sie können die Informationen für einen Server aktualisieren, indem Sie die Daten für den Server mit dem gleichen Wert für **Servername** erneut hochladen. Das Feld **Servername** kann nicht geändert werden. Das Löschen von Servern wird derzeit nicht unterstützt.

## <a name="verify-servers-in-the-portal"></a>Überprüfen von Servern im Portal

Gehen Sie wie folgt vor, um sich zu vergewissern, dass die Server nach der Ermittlung im Azure-Portal angezeigt werden:

1. Öffnen Sie das Azure Migrate-Dashboard.
2. Wählen Sie auf der Seite **Azure Migrate – Server** > **Azure Migrate: Server Assessment** (Azure Migrate-Serverbewertung) das Symbol mit der Anzahl für **Ermittelte Server** aus.
3. Wählen Sie die Registerkarte **Importbasiert** aus.



## <a name="supported-operating-system-names"></a>Namen von unterstützten Betriebssystemen

Die in der CSV-Datei angegebenen Betriebssystemnamen müssen mit den Namen in dieser Liste identisch sein oder diese enthalten. Andernfalls können sie nicht bewertet werden. 

**A - H** | **I - R** | **S - T** | **U - Z**
--- | --- | --- | ---
Apple Mac OS X 10<br/>Asianux 3<br/>Asianux 4<br/>Asianux 5<br/>CentOS<br/>CentOS 4/5<br/>CoreOS Linux<br/>Debian GNU/Linux 4<br/>Debian GNU/Linux 5<br/>Debian GNU/Linux 6<br/>Debian GNU/Linux 7<br/>Debian GNU/Linux 8<br/>FreeBSD | IBM OS/2<br/>MS-DOS<br/>Novell NetWare 5<br/>Novell NetWare 6<br/>Oracle Linux<br/>Oracle Linux 4/5<br/>Oracle Solaris 10<br/>Oracle Solaris 11<br/>Red Hat Enterprise Linux 2<br/>Red Hat Enterprise Linux 3<br/>Red Hat Enterprise Linux 4<br/>Red Hat Enterprise Linux 5<br/>Red Hat Enterprise Linux 6<br/>Red Hat Enterprise Linux 7<br/>Red Hat Fedora | SCO OpenServer 5<br/>SCO OpenServer 6<br/>SCO UnixWare 7<br/> Serenity Systems eComStation 1<br/>Serenity Systems eComStation <br/>Sun Microsystems Solaris 8<br/>Sun Microsystems Solaris 9<br/><br/>SUSE Linux Enterprise 10<br/>SUSE Linux Enterprise 11<br/>SUSE Linux Enterprise 12<br/>SUSE Linux Enterprise 8/9<br/>SUSE Linux Enterprise 11<br/>SUSE openSUSE | Ubuntu Linux<br/>VMware ESXi 4<br/>VMware ESXi 5<br/>VMware ESXi 6<br/>Windows 10<br/>Windows 2000<br/>Windows 3<br/>Windows 7<br/>Windows 8<br/>Windows 95<br/>Windows 98<br/>Windows NT<br/>Windows Server (R) 2008<br/>Windows Server 2003<br/>Windows Server 2008<br/>Windows Server 2008 R2<br/>Windows Server 2012<br/>Windows Server 2012 R2<br/>Windows Server 2016<br/>Windows Server 2019<br/>Windows Server Threshold<br/>Windows Vista<br/>Windows Web Server 2008 R2<br/>Windows XP Professional

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial führen Sie Folgendes durch:

> [!div class="checklist"]
> * Sie haben ein Azure Migrate-Projekt erstellt. 
> * Sie haben Server mithilfe einer importierten CSV-Datei ermittelt. Führen Sie nun eine Bewertung für die [Migration von virtuellen VMware-Computern zu virtuellen Azure-Computern](./tutorial-assess-vmware-azure-vm.md) aus.