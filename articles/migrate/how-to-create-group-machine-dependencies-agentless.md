---
title: Einrichten der Abhängigkeitsanalyse ohne Agent in der Azure Migrate-Serverbewertung
description: Hier erfahren Sie, wie Sie die Abhängigkeitsanalyse ohne Agent in der Azure Migrate-Serverbewertung einrichten.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 6/08/2020
ms.openlocfilehash: d84c85326c6f5d87189a2c24a3b13654f157cb05
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96754282"
---
# <a name="analyze-machine-dependencies-agentless"></a>Analysieren von Computerabhängigkeiten (ohne Agent)

In diesem Artikel wird das Einrichten der Abhängigkeitsanalyse ohne Agent in der Azure Migrate-Serverbewertung beschrieben. Mit der [Abhängigkeitsanalyse](concepts-dependency-visualization.md) können Sie Abhängigkeiten zwischen Computern im Hinblick auf die Bewertung und Migration zu Azure besser identifizieren und verstehen.


> [!IMPORTANT]
> Die Abhängigkeitsvisualisierung ohne Agent ist derzeit als Vorschauversion für VMware-VMs verfügbar, die mithilfe des Azure Migrate-Serverbewertungstools ermittelt wurden.
> Die Funktionen sind möglicherweise eingeschränkt oder unvollständig.
> Diese Vorschau wird durch den Kundensupport abgedeckt und kann für Produktionsworkloads verwendet werden.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="current-limitations"></a>Aktuelle Einschränkungen

- In der Abhängigkeitsanalyseansicht können Sie zurzeit keine Server zu einer Gruppe hinzufügen oder daraus entfernen.
- Eine Abhängigkeitszuordnung für eine Gruppe von Servern ist derzeit nicht verfügbar.
- In einem Azure Migrate-Projekt kann die Sammlung von Abhängigkeitsdaten gleichzeitig für 1.000 Server eingerichtet werden. Sie können eine höhere Anzahl von Servern analysieren, indem Sie eine Reihe von Batches mit jeweils 1.000 Servern erstellen.

## <a name="before-you-start"></a>Vorbereitung

- [Lesen](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) Sie die Informationen zu unterstützten Betriebssystemen und erforderlichen Berechtigungen.
- Stellen Sie Folgendes sicher:
    - Sie verfügen über ein Azure Migrate-Projekt. Wenn dies nicht der Fall ist, [erstellen](./create-manage-projects.md) Sie jetzt ein entsprechendes Projekt.
    - Vergewissern Sie sich, dass Sie das Azure Migrate-Serverbewertungstool zum Projekt [hinzugefügt](how-to-assess.md) haben.
    - Richten Sie eine [Azure Migrate-Appliance](migrate-appliance.md) ein, um lokale Computer zu ermitteln. [Richten Sie eine Appliance für VMware-VMs ein](how-to-set-up-appliance-vmware.md). Die Appliance ermittelt lokale Computer und sendet Metadaten und Leistungsdaten an die Azure Migrate-Serverbewertung.
- Überprüfen Sie, ob auf jeder VM, die Sie analysieren möchten, VMware Tools (höhere Version als 10.2) installiert ist.


## <a name="create-a-user-account-for-discovery"></a>Erstellen eines Benutzerkontos für die Ermittlung

Richten Sie ein Benutzerkonto ein, damit die Serverbewertung zur Ermittlung von Abhängigkeiten auf den virtuellen Computer zugreifen kann. [Informieren](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) Sie sich über die Kontoanforderungen für Windows- und Linux-VMs.


## <a name="add-the-user-account-to-the-appliance"></a>Hinzufügen des Benutzerkontos zur Appliance

Fügen Sie das Benutzerkonto zur Appliance hinzu.

1. Öffnen Sie die Verwaltungs-App für die Appliance. 
2. Navigieren Sie zum Bereich **vCenter-Details angeben**.
3. Klicken Sie unter **Anwendungen und Abhängigkeiten auf VMs ermitteln** auf **Anmeldeinformationen hinzufügen**.
3. Wählen Sie das **Betriebssystem** aus, geben Sie einen benutzerfreundlichen Namen für das Konto an, und geben Sie dann **Benutzername**/**Kennwort** an.
6. Klicken Sie auf **Speichern**.
7. Klicken Sie auf **Speichern und Ermittlung starten**.

    ![VM-Benutzerkonto hinzufügen](./media/how-to-create-group-machine-dependencies-agentless/add-vm-credential.png)

## <a name="start-dependency-discovery"></a>Starten der Abhängigkeitsermittlung

Wählen Sie die Computer aus, auf denen Sie die Abhängigkeitsermittlung aktivieren möchten. 

1. Klicken Sie in **Azure Migrate: Serverbewertung** auf **Ermittelte Server**.
2. Klicken Sie auf das Symbol **Abhängigkeitsanalyse**.
3. Klicken Sie auf **Server hinzufügen**.
4. Wählen Sie auf der Seite **Server hinzufügen** die Appliance aus, die die relevanten Computer ermittelt.
5. Wählen Sie in der Liste der Computer die Computer aus.
6. Klicken Sie auf **Server hinzufügen**.

    ![Starten der Abhängigkeitsermittlung](./media/how-to-create-group-machine-dependencies-agentless/start-dependency-discovery.png)

Ungefähr sechs Stunden nach dem Start der Abhängigkeitsermittlung können Sie die Abhängigkeiten visualisieren. Falls Sie die Aktivierung für mehrere Computer durchführen möchten, können Sie [PowerShell](#start-or-stop-dependency-discovery-using-powershell) verwenden.

## <a name="visualize-dependencies"></a>Visualisieren von Abhängigkeiten

1. Klicken Sie in **Azure Migrate: Serverbewertung** auf **Ermittelte Server**.
2. Suchen Sie nach dem Computer, den Sie anzeigen möchten.
3. Klicken Sie in der Spalte **Abhängigkeiten** auf **Abhängigkeiten anzeigen**.
4. Ändern Sie den Zeitraum, für den Sie die Zuordnung anzeigen möchten, über die Dropdown-Liste **Zeitdauer**.
5. Erweitern Sie die Gruppe **Client**, um die Computer aufzulisten, die eine Abhängigkeit zum ausgewählten Computer aufweisen.
6. Erweitern Sie die Gruppe **Port**, um die Computer aufzulisten, die eine Abhängigkeit vom ausgewählten Computer aufweisen.
7. Wenn Sie zur Zuordnungsansicht eines abhängigen Computers navigieren möchten, klicken Sie auf den Computernamen und dann auf **Serverzuordnung laden**.

    ![Erweitern der Portgruppe des Servers und Laden der Serverzuordnung](./media/how-to-create-group-machine-dependencies-agentless/load-server-map.png)

    ![Erweitern der Clientgruppe ](./media/how-to-create-group-machine-dependencies-agentless/expand-client-group.png)

8. Erweitern Sie den ausgewählten Computer, um Details auf Prozessebene für die einzelnen Abhängigkeiten anzuzeigen.

    ![Erweitern des Servers zum Anzeigen von Prozessen](./media/how-to-create-group-machine-dependencies-agentless/expand-server-processes.png)

> [!NOTE]
> Prozessinformationen für eine Abhängigkeit sind nicht immer verfügbar. Wenn keine Informationen verfügbar sind, wird die Abhängigkeit mit „Unbekannter Prozess“ als Prozess dargestellt.

## <a name="export-dependency-data"></a>Exportieren der Abhängigkeitsdaten

1. Klicken Sie in **Azure Migrate: Serverbewertung** auf **Ermittelte Server**.
2. Klicken Sie auf das Symbol **Abhängigkeitsanalyse**.
3. Klicken Sie auf **Anwendungsabhängigkeiten exportieren**.
4. Wählen Sie auf der Seite **Anwendungsabhängigkeiten exportieren** die Appliance aus, die die relevanten Computer ermittelt.
5. Wählen Sie die Start- und Endzeit aus. Beachten Sie, dass Sie nur Daten für die letzten 30 Tage herunterladen können.
6. Klicken Sie auf **Abhängigkeit exportieren**.

Die Abhängigkeitsdaten werden im CSV-Format exportiert und heruntergeladen. Die heruntergeladene Datei enthält die Abhängigkeitsdaten für alle Computer, auf denen die Abhängigkeitsanalyse aktiviert war. 

![Exportieren von Abhängigkeiten](./media/how-to-create-group-machine-dependencies-agentless/export.png)

### <a name="dependency-information"></a>Abhängigkeitsinformationen

Jede Zeile in der exportierten CSV-Datei entspricht einer Abhängigkeit, die im angegebenen Zeitfenster beobachtet wurde. 

In der folgenden Tabelle sind die Felder der exportierten CSV-Datei zusammengefasst. Beachten Sie, dass die Felder für den Servernamen, die Anwendung und den Prozess nur für Server aufgefüllt werden, auf denen die Abhängigkeitsanalyse ohne Agent aktiviert ist.

**Feldname** | **Details**
--- | --- 
Zeitfenster | Das Zeitfenster, in dem die Abhängigkeit beobachtet wurde. <br/> Abhängigkeitsdaten werden derzeit in Zeitfenstern von 6 Stunden erfasst.
Name des Quellservers | Der Name des Quellcomputers. 
Quellanwendung | Der Name der Anwendung auf dem Quellcomputer. 
Quellprozess | Der Name des Prozesses auf dem Quellcomputer. 
Name des Zielservers | Der Name des Zielcomputers.
Ziel-IP | Die IP-Adresse des Zielcomputers.
Zielanwendung | Der Name der Anwendung auf dem Zielcomputer.
Zielprozess | Der Name des Prozesses auf dem Zielcomputer. 
Zielport | Die Portnummer auf dem Zielcomputer.


## <a name="stop-dependency-discovery"></a>Beenden der Abhängigkeitsermittlung

Wählen Sie die Computer aus, auf denen Sie die Abhängigkeitsermittlung beenden möchten. 

1. Klicken Sie in **Azure Migrate: Serverbewertung** auf **Ermittelte Server**.
2. Klicken Sie auf das Symbol **Abhängigkeitsanalyse**.
3. Klicken Sie auf **Server entfernen**.
3. Wählen Sie auf der Seite **Server entfernen** die **Appliance** aus, die die virtuellen Computer ermittelt, auf denen die Abhängigkeitsermittlung beendet werden soll.
4. Wählen Sie in der Liste der Computer die Computer aus.
5. Klicken Sie auf **Server entfernen**.

Falls Sie die Abhängigkeitsermittlung auf mehreren Computern beenden möchten, können Sie [PowerShell](#start-or-stop-dependency-discovery-using-powershell) verwenden.


## <a name="start-or-stop-dependency-discovery-using-powershell"></a>Starten oder Beenden der Abhängigkeitsermittlung mit PowerShell

Laden Sie das PowerShell-Modul aus dem GitHub-Repository mit den [Azure PowerShell-Beispielen](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/dependencies-at-scale) herunter.


### <a name="log-in-to-azure"></a>Anmelden an Azure

1. Melden Sie sich mit dem Cmdlet „Connect-AzAccount“ bei Ihrem Azure-Abonnement an.

    ```PowerShell
    Connect-AzAccount
    ```
    Verwenden Sie den folgenden Befehl, falls Sie Azure Government nutzen.
    ```PowerShell
    Connect-AzAccount -EnvironmentName AzureUSGovernment
    ```

2. Wählen Sie das Abonnement aus, unter dem Sie das Azure Migrate-Projekt erstellt haben. 

    ```PowerShell
    select-azsubscription -subscription "Fabrikam Demo Subscription"
    ```

3. Importieren Sie das heruntergeladene PowerShell-Modul „AzMig_Dependencies“.

    ```PowerShell
    Import-Module .\AzMig_Dependencies.psm1
    ```

### <a name="enable-or-disable-dependency-data-collection"></a>Aktivieren oder Deaktivieren der Sammlung von Abhängigkeitsdaten

1. Verwenden Sie die folgenden Befehle, um die Liste mit den ermittelten VMware-VMs in Ihrem Azure Migrate-Projekt abzurufen. Im Beispiel unten lautet der Projektname „FabrikamDemoProject“ und die zugehörige Ressourcengruppe „FabrikamDemoRG“. Die Liste mit den Computern wird in der Datei „FabrikamDemo_VMs.csv“ gespeichert.

    ```PowerShell
    Get-AzMigDiscoveredVMwareVMs -ResourceGroupName "FabrikamDemoRG" -ProjectName "FabrikamDemoProject" -OutputCsvFile "FabrikamDemo_VMs.csv"
    ```

    Der Datei können Sie den Anzeigenamen der VM, den aktuellen Status der Abhängigkeitssammlung und die ARM-ID aller ermittelten VMs entnehmen. 

2. Erstellen Sie eine CSV-Eingabedatei, um Abhängigkeiten zu aktivieren oder zu deaktivieren. Die Datei muss eine Spalte mit der Überschrift „ARM ID“ enthalten. Alle zusätzlichen Überschriften in der CSV-Datei werden ignoriert. Sie können die CSV-Datei erstellen, indem Sie die im vorherigen Schritt generierte Datei verwenden. Erstellen Sie eine Kopie der Datei, und behalten Sie die VMs bei, für die Sie Abhängigkeiten aktivieren oder deaktivieren möchten. 

    Im folgenden Beispiel wird die Abhängigkeitsanalyse in der Liste mit den VMs in der Eingabedatei „FabrikamDemo_VMs_Enable.csv“ aktiviert.

    ```PowerShell
    Set-AzMigDependencyMappingAgentless -InputCsvFile .\FabrikamDemo_VMs_Enable.csv -Enable
    ```

    Im folgenden Beispiel wird die Abhängigkeitsanalyse in der Liste mit den VMs in der Eingabedatei „FabrikamDemo_VMs_Disable.csv“ deaktiviert.

    ```PowerShell
    Set-AzMigDependencyMappingAgentless -InputCsvFile .\FabrikamDemo_VMs_Disable.csv -Disable
    ```

## <a name="visualize-network-connections-in-power-bi"></a>Visualisieren von Netzwerkverbindungen in Power BI

Azure Migrate verfügt über eine Power BI-Vorlage, die Sie zum gleichzeitigen Visualisieren der Netzwerkverbindungen vieler Server und zum Filtern nach Prozess und Server verwenden können. Laden Sie Power BI zum Visualisieren mit Abhängigkeitsdaten, indem Sie die unten angegebene Anleitung verwenden.

1. Laden Sie das PowerShell-Modul und die Power BI-Vorlage aus dem GitHub-Repository mit den [Azure PowerShell-Beispielen](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/dependencies-at-scale) herunter.

2. Melden Sie sich bei Azure an, indem Sie die folgende Anleitung befolgen: 
- Melden Sie sich mit dem Cmdlet „Connect-AzAccount“ bei Ihrem Azure-Abonnement an.

    ```PowerShell
    Connect-AzAccount
    ```

- Verwenden Sie den folgenden Befehl, falls Sie Azure Government nutzen.

    ```PowerShell
    Connect-AzAccount -EnvironmentName AzureUSGovernment
    ```

- Wählen Sie das Abonnement aus, unter dem Sie das Azure Migrate-Projekt erstellt haben. 

    ```PowerShell
    select-azsubscription -subscription "Fabrikam Demo Subscription"
    ```

3. Importieren Sie das heruntergeladene PowerShell-Modul „AzMig_Dependencies“.

    ```PowerShell
    Import-Module .\AzMig_Dependencies.psm1
    ```

4. Führen Sie den folgenden Befehl aus. Mit diesem Befehl werden die Abhängigkeitsdaten in eine CSV-Datei heruntergeladen und verarbeitet, um eine Liste mit eindeutigen Abhängigkeiten zu generieren, die für die Visualisierung in Power BI genutzt werden können. Im Beispiel unten lautet der Projektname „FabrikamDemoProject“ und die zugehörige Ressourcengruppe „FabrikamDemoRG“. Die Abhängigkeiten werden für Computer heruntergeladen, die über FabrikamAppliance ermittelt werden. Die eindeutigen Abhängigkeiten werden in der Datei „FabrikamDemo_Dependencies.csv“ gespeichert.

    ```PowerShell
    Get-AzMigDependenciesAgentless -ResourceGroup FabrikamDemoRG -Appliance FabrikamAppliance -ProjectName FabrikamDemoProject -OutputCsvFile "FabrikamDemo_Dependencies.csv"
    ```

5. Öffnen Sie die heruntergeladene Power BI-Vorlage.

6. Laden Sie die heruntergeladenen Abhängigkeitsdaten in Power BI.
    - Öffnen Sie die Vorlage in Power BI.
    - Klicken Sie in der Symbolleiste auf **Daten abrufen**. 
    - Wählen Sie in der Liste mit den häufig verwendeten Datenquellen die Option **Text/CSV** aus.
    - Wählen Sie die heruntergeladene Datei mit den Abhängigkeiten aus.
    - Klicken Sie auf **Laden**.
    - Sie sehen, dass eine Tabelle mit dem Namen der CSV-Datei importiert wird. Die Tabelle wird rechts in der Felderleiste angezeigt. Benennen Sie sie in „AzMig_Dependencies“ um.
    - Klicken Sie in der Symbolleiste auf „Aktualisieren“.

    Die importierten Daten sollten nun im Diagramm mit den Netzwerkverbindungen und unter den Slicern für den Namen des Quellservers, Zielservers, Quellprozesses und Zielprozesses widergespiegelt werden.

7. Visualisieren Sie die Karte mit den Netzwerkverbindungen, die nach Servern und Prozessen gefiltert wurde. Speichern Sie die Datei.


## <a name="next-steps"></a>Nächste Schritte

[Gruppieren von Computern](how-to-create-a-group.md) für die Bewertung