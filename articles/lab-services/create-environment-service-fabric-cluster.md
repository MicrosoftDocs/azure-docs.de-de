---
title: Erstellen einer Umgebung mit einem Service Fabric-Cluster in Azure DevTest Labs | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie eine Umgebung mit einem eigenständigen Service Fabric-Cluster erstellen und den Cluster mithilfe von Zeitplänen starten und beenden.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: EMaher
manager: femila
editor: spelluru
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/01/2019
ms.author: enewman
ms.openlocfilehash: 9848f197800c391285c4065685b910685f0ac64b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60312076"
---
# <a name="create-an-environment-with-self-contained-service-fabric-cluster-in-azure-devtest-labs"></a>Erstellen einer Umgebung mit einem eigenständigen Service Fabric-Cluster in Azure DevTest Labs
Dieser Artikel enthält Informationen zum Erstellen einer Umgebung mit einem eigenständigen Service Fabric-Cluster in Azure DevTest Labs. 

## <a name="overview"></a>Übersicht
Mit DevTest Labs können Sie eigenständige Testumgebungen erstellen, wie sie in Azure Resource Manager-Vorlagen definiert sind. Diese Umgebungen enthalten sowohl IaaS-Ressourcen wie virtuelle Computer als auch PaaS-Ressourcen wie Service Fabric. Mit DevTest Labs können Sie virtuelle Computer in einer Umgebung verwalten und mit Befehlen steuern. Mit diesen Befehlen können Sie einen virtuellen Computer nach einem Zeitplan starten oder beenden. Auf vergleichbare Weise unterstützt DevTest Labs Sie auch bei der Verwaltung von Service Fabric-Clustern in einer Umgebung. Service Fabric-Cluster in einer Umgebung können Sie entweder manuell oder über einen Zeitplan starten und beenden.

## <a name="create-a-service-fabric-cluster"></a>Erstellen von Service Fabric-Clustern
Service Fabric-Cluster werden in DevTest Labs mithilfe von Umgebungen erstellt. Jede Umgebung wird von einer Azure Resource Manager-Vorlage in einem Git-Repository definiert. Das [öffentliche Git-Repository](https://github.com/Azure/azure-devtestlab/tree/master/Environments/) für DevTest Labs enthält im Ordner [ServiceFabric-Cluster](https://github.com/Azure/azure-devtestlab/tree/master/Environments/ServiceFabric-LabCluster) die Resource Manager-Vorlage zum Erstellen eines Service Fabric-Clusters. 

1. Erstellen Sie zunächst mithilfe der Anweisungen im folgenden Artikel ein Lab in Azure DevTest Labs: [Erstellen eines Labs](devtest-lab-create-lab.md). Beachten Sie, dass die Option **Öffentliche Umgebungen** standardmäßig auf **Ein** eingestellt ist. 
2. Vergewissern Sie sich, dass der Service Fabric-Anbieter für Ihr Abonnement registriert ist. Führen Sie dazu die folgenden Schritte aus:
    1. Wählen Sie **Abonnements** im linken Navigationsmenü und dann Ihr **Abonnement** aus.
    2. Wählen Sie auf der Seite **Abonnement** im Abschnitt **Einstellungen** im linken Menü die Option **Ressourcenanbieter** aus. 
    3. Wenn **Microsoft.ServiceFabric** nicht registriert ist, wählen Sie **Registrieren** aus. 
3. Wählen Sie auf der Seite **DevTest Lab** für das Lab auf der Symbolleiste die Option **+ Hinzufügen** aus. 
    
    ![Schaltfläche „Hinzufügen“ auf der Symbolleiste](./media/create-environment-service-fabric-cluster/add-button.png)
3. Wählen Sie auf der Seite **Basis auswählen** in der Liste die Option **Service Fabric-Lab-Cluster** aus. 

    ![Auswählen von „Service Fabric-Lab-Cluster“ in der Liste](./media/create-environment-service-fabric-cluster/select-service-fabric-cluster.png)
4. Führen Sie auf der Seite **Einstellungen konfigurieren** die folgenden Schritte aus: 
    1. Geben Sie einen **Namen** für Ihre Cluster-**Umgebung** an. Dies ist der Name der Ressourcengruppe in Azure, in welcher der Service Fabric-Cluster erstellt werden soll. 
    2. Wählen Sie das **Betriebssystem** für die virtuellen Computer im Cluster aus. Der Standardwert lautet: **Windows**.
    3. Geben Sie einen Namen für den **Administrator** des Clusters an. 
    4. Geben Sie ein **Kennwort** für den Administrator an. 
    5. Geben Sie als **Zertifikat** Ihre Zertifikatinformationen als Base64-codierte Zeichenfolge ein. Führen Sie zum Erstellen eines Zertifikats die folgenden Schritte aus:
        1. Laden Sie die Datei **Create-ClusterCertificate.ps1** aus dem [Git-Repository](https://github.com/Azure/azure-devtestlab/tree/master/Environments/ServiceFabric-LabCluster) herunter. Klonen Sie alternativ das Repository auf Ihrem Computer. 
        2. Starten Sie **PowerShell**. 
        3. Führen Sie die **PS1**-Datei mit dem Befehl `.\Create-ClusterCertificate.ps1` aus. Daraufhin wird eine Textdatei im Editor mit den Informationen, die Sie zum Ausfüllen der zertifikatspezifischen Felder auf dieser Seite benötigen, geöffnet. . 
    6. Geben Sie das **Kennwort für das Zertifikat** ein.
    7. Geben Sie den **Fingerabdruck** für Ihr Zertifikat an.
    8. Wählen Sie auf der Seite **Einstellungen konfigurieren** die Option **Hinzufügen** aus. 

        ![Konfigurieren des Clustereinstellungen](./media/create-environment-service-fabric-cluster/configure-settings.png)
5. Nachdem der Cluster erstellt wurde, wird eine Ressourcengruppe mit dem Namen der im vorherigen Schritt bereitgestellten Umgebung angezeigt. Wenn Sie die Gruppe erweitern, wird der darin enthaltene Service Fabric-Cluster angezeigt. Wenn der Status der Ressourcengruppe bei **Wird erstellt** hängenbleibt, wählen Sie auf der Symbolleiste **Aktualisieren** aus. Mit der **Service Fabric-Cluster**-Umgebung wird ein Cluster mit einem Knotentyp und fünf Knoten unter Linux oder Windows erstellt.

    Im folgenden Beispiel ist **mysfabricclusterrg** der Name der speziell für den Service Fabric-Cluster erstellten Ressourcengruppe. Es ist wichtig zu wissen, dass Lab-Umgebungen innerhalb der Ressourcengruppe, in der sie erstellt werden, eigenständig sind. Das bedeutet, dass die Vorlage die Umgebung definiert, in der Sie nur auf Ressourcen innerhalb der neu erstellten Ressourcengruppe oder auf [für das Lab konfigurierte virtuelle Netzwerke](devtest-lab-configure-vnet.md) zugreifen können. Im obigen Beispiel werden alle erforderlichen Ressourcen in derselben Ressourcengruppe erstellt.

    ![Erstellter Cluster](./media/create-environment-service-fabric-cluster/cluster-created.png)

## <a name="start-or-stop-the-cluster"></a>Starten oder Beenden des Clusters
Sie können den Cluster entweder direkt auf der Seite „DevTest Lab“ oder auf der Seite „Service Fabric-Cluster“, die von DevTest-Labs bereitgestellt wird, starten oder beenden. 

### <a name="from-the-devtest-lab-page"></a>Seite „DevTest Lab“
Auf der Seite „DevTest Lab“ für Ihr Lab können Sie den Cluster starten oder beenden. 

1. Wählen Sie die **drei Punkte (...)**  für den Service Fabric-Cluster aus, wie in der folgenden Abbildung gezeigt: 

    ![Befehle zum Starten und Beenden des Clusters](./media/create-environment-service-fabric-cluster/start-stop-on-devtest-lab-page.png)

2. Im Kontextmenü werden zwei Befehle zum **Starten** und **Beenden** des Clusters angezeigt. Mit dem Befehl „Starten“ werden alle Knoten im Cluster gestartet. Mit dem Befehl „Beenden“ werden alle Knoten im Cluster beendet. Wenn ein Cluster beendet wird, bleibt der Service Fabric-Cluster selbst in einem betriebsbereiten Zustand. Es sind aber keine Knoten verfügbar, bis Sie im Lab erneut den Befehl „Starten“ für den Cluster ausgeben.

    Beim Verwenden eines Service Fabric-Clusters in einer Testumgebung sind einige Dinge zu beachten. Es kann einige Zeit dauern, bis der Service Fabric-Cluster nach dem Neustart der Knoten vollständig aktiviert ist. Um die Daten vom Herunterfahren bis zum Neustartvorgang aufzubewahren, müssen sie auf einem verwalteten Datenträger gespeichert werden, der an den virtuellen Computer angeschlossen ist. Bei Verwendung eines verwalteten Datenträgers treten Leistungseinbußen auf. Daher wird dieser nur für Testumgebungen empfohlen. Wenn der Datenträger für die Datenspeicherung nicht gesichert wird, gehen Daten verloren, wenn der Befehl zum Beenden des Clusters ausgegeben wird.

### <a name="from-the-service-fabric-cluster-page"></a>Seite „Service Fabric-Cluster“ 
Diese Seite bietet eine weitere Möglichkeit zum Starten oder Beenden des Clusters. 

1. Wählen Sie Ihren Service Fabric-Cluster auf der Seite „DevTest Lab“ in der Strukturansicht aus. 

    ![Auswählen Ihres Clusters](./media/create-environment-service-fabric-cluster/select-cluster.png)

2. Auf der Seite **Service Fabric-Cluster** für den Cluster werden auf der Symbolleiste Befehle zum Starten oder Beenden des Clusters angezeigt. 

    ![Befehle zum Starten oder Beenden auf der Seite „Service Fabric-Cluster“](./media/create-environment-service-fabric-cluster/start-stop-on-cluster-page.png)

## <a name="configure-auto-startup-and-auto-shutdown-schedule"></a>Konfigurieren eines Zeitplans zum automatischen Starten und Herunterfahren
Service Fabric-Cluster können auch nach einem Zeitplan gestartet oder beendet werden. Diese Benutzeroberfläche ähnelt der für virtuelle Computer in einer Lab-Umgebung. Aus Sparsamkeitsgründen wird standardmäßig jeder in einem Lab erstellte Cluster automatisch zu dem Zeitpunkt heruntergefahren, der in den [Richtlinien zum automatischen Herunterfahren](devtest-lab-get-started-with-lab-policies.md#set-auto-shutdown) für das Lab definiert wurde. Sie können die Standardeinstellung überschreiben, indem Sie angeben, ob und zu welchem Zeitpunkt der Cluster heruntergefahren werden soll. 

![Vorhandene Zeitpläne zum automatischen Starten und Herunterfahren](./media/create-environment-service-fabric-cluster/existing-schedules.png)

### <a name="opt-in-to-the-auto-start-schedule"></a>Aktivieren der Planung des automatischen Startens
Um den Startzeitplan zu aktivieren, führen Sie die folgenden Schritte aus:

1. Wählen Sie im linken Menü **Automatisch starten** aus.
2. Wählen Sie unter **Planung des automatischen Starts für diesen Service Fabric-Cluster zulassen** die Option **Ein** aus. Diese Seite ist nur aktiviert, wenn der Lab-Besitzer den Benutzern das automatische Starten von virtuellen Computern oder Service Fabric-Clustern erlaubt hat.
3. Wählen Sie auf der Symbolleiste **Speichern** aus. 

    ![Seite „Automatisch starten“](./media/create-environment-service-fabric-cluster/set-auto-start-settings.png)

### <a name="configure-auto-shutdown-settings"></a>Konfigurieren der Einstellungen für das automatische Herunterfahren 
Führen Sie zum Ändern der Einstellungen für das automatische Herunterfahren die folgenden Schritte aus:

1. Wählen Sie im linken Menü **Automatisch herunterfahren** aus. 
2. Auf dieser Seite können Sie das automatische Herunterfahren deaktivieren, indem Sie unter **Aktiviert** die Option **Aus** auswählen. 
3. Wenn Sie unter **Aktiviert** die Option **Ein** auswählen, führen Sie die folgenden Schritte aus:
    1. Geben Sie die **Zeit** für das Herunterfahren an.
    2. Geben Sie die **Zeitzone** für die Zeit an. 
    3. Geben Sie an, ob DevTest Labs vor dem automatischen Herunterfahren **Benachrichtigungen** senden soll. 
    4. Wenn Sie bei der Option „Benachrichtigungen“ **Ja** ausgewählt haben, geben Sie die zum Senden von Benachrichtigungen erforderliche **Webhook-URL** und/oder die **E-Mail-Adresse** an. 
    5. Wählen Sie auf der Symbolleiste **Speichern** aus.

        ![Seite „Automatisch herunterfahren“](./media/create-environment-service-fabric-cluster/auto-shutdown-settings.png)

## <a name="to-view-nodes-in-the-service-fabric-cluster"></a>Anzeigen von Knoten im Service Fabric-Cluster
Die Seite „Service Fabric-Cluster“, die in den vorherigen Schritten gezeigt wurde, bezieht sich auf die Seite „DevTest Labs“. Die Knoten im Cluster werden nicht angezeigt. Führen Sie die folgenden Schritte aus, um weitere Informationen zum Cluster anzuzeigen:

1. Wählen Sie auf der Seite **DevTest Lab** für Ihr Lab im Abschnitt **Meine virtuellen Computer** in der Strukturansicht die **Ressourcengruppe** aus.

    ![Auswählen der Ressourcengruppe](./media/create-environment-service-fabric-cluster/select-resource-group.png)
2. Auf der Seite **Ressourcengruppe** werden alle Ressourcen in der Ressourcengruppe in einer Liste angezeigt. Wählen Sie **Service Fabric-Cluster** in der Liste aus. 

    ![Auswählen des Clusters in der Liste](./media/create-environment-service-fabric-cluster/select-cluster-resource-group-page.png)
3. Die Seite **Service Fabric-Cluster** wird für Ihren Cluster angezeigt. Diese Seite wird von Service Fabric bereitstellt. Angezeigt werden alle Informationen zu den Clustern (z.B. Knoten, Knotentypen usw.).

    ![Startseite des Service Fabric-Clusters](./media/create-environment-service-fabric-cluster/service-fabric-cluster-page.png)

## <a name="next-steps"></a>Nächste Schritte
Die folgenden Artikel enthalten ausführliche Informationen zu Umgebungen: 

- [Erstellen von Umgebungen mit mehreren virtuellen Computern und PaaS-Ressourcen mit Azure Resource Manager-Vorlagen](devtest-lab-create-environment-from-arm.md)
- [Konfigurieren und Verwenden von öffentlichen Umgebungen in Azure DevTest Labs](devtest-lab-configure-use-public-environments.md)
