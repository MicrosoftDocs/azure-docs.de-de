---
title: Problembehandlung für Azure Monitor für Container | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Sie Probleme mit Azure Monitor für Container behandeln und lösen können.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/27/2018
ms.author: magoedte
ms.openlocfilehash: 2e3e39ef24d82393d981c0ce276b3338419e0b2d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65521778"
---
# <a name="troubleshooting-azure-monitor-for-containers"></a>Problembehandlung für Azure Monitor für Container

Wenn Sie die Überwachung Ihres AKS-Clusters (Azure Kubernetes Service) mit Azure Monitor für Container konfigurieren, treffen Sie möglicherweise auf ein Problem, das die Sammlung von Daten oder die Meldung des Status verhindert. In diesem Artikel werden einige allgemeine Probleme und Schritte zur Problembehandlung ausführlich erläutert.

## <a name="authorization-error-during-onboarding-or-update-operation"></a>Autorisierungsfehler bei Onboarding- oder Updatevorgängen
Wenn Sie Azure Monitor für Container aktivieren oder einen Cluster aktualisieren, damit dieser das Sammeln von Metriken unterstützt, tritt möglicherweise ein Fehler wie der folgende auf: *Client "<Identität des Benutzers>" mit der Objekt-ID "<objectId des Benutzers>" hat keine Berechtigung zum Ausführen der Aktion "Microsoft.Authorization/roleAssignments/write" über Bereich*.

Während des Onboarding- oder Updatevorgangs wird versucht, die Rollenzuweisung **Überwachungsmetriken veröffentlichen** in der Clusterressource zu gewähren. Der Benutzer, der den Prozess zur Aktivierung von Azure Monitor für Container oder den Aktualisierungsprozess initiiert, damit das Sammeln von Metriken unterstützt wird, muss Zugriff auf die Berechtigung **Microsoft.Authorization/roleAssignments/write** im Bereich der AKS-Clusterressource haben. Nur Mitglieder der Rollen **Besitzer** und **Benutzerzugriffsadministrator** verfügen über Zugriff auf diese Berechtigung. Wenn Ihre Sicherheitsrichtlinien das Zuweisen von differenzierten Berechtigungen erfordern, empfiehlt es sich, [benutzerdefinierte Rollen](../../role-based-access-control/custom-roles.md) anzuzeigen und den Benutzern, die diese Berechtigung benötigen, die entsprechende Rolle zuzuweisen. 

Sie können diese Rolle auch manuell über das Azure-Portal zuweisen. Führen Sie dazu die folgenden Schritte aus:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. 
2. Klicken Sie links oben im Azure-Portal auf **Alle Dienste**. Geben Sie in der Liste mit den Ressourcen **Kubernetes** ein. Sobald Sie mit der Eingabe beginnen, wird die Liste auf der Grundlage Ihrer Eingabe gefiltert. Wählen Sie **Azure Kubernetes** aus.
3. Wählen Sie in der Liste der Kubernetes-Cluster einen Cluster aus.
2. Klicken Sie im linken Menü auf **Zugriffssteuerung (IAM)** .
3. Klicken Sie auf **+ Hinzufügen**, um eine Rollenzuweisung hinzuzufügen. Wählen Sie die Rolle **Überwachungsmetriken veröffentlichen** aus, und geben Sie unterhalb des Felds **Auswählen** die Zeichenfolge **AKS** ein, um die Ergebnisse nur nach denjenigen Clusterdienstprinzipalen zu filtern, die im Abonnement definiert sind. Wählen Sie den Dienstprinzipal für diesen Cluster aus.
4. Wählen Sie **Speichern** aus, um das Zuweisen der Rolle abzuschließen. 

## <a name="azure-monitor-for-containers-is-enabled-but-not-reporting-any-information"></a>Azure Monitor für Container ist aktiviert, meldet aber keinerlei Informationen
Wenn Azure Monitor für Container erfolgreich aktiviert und konfiguriert wurde, Sie aber keine Statusinformationen anzeigen können oder eine Protokollabfrage keine Ergebnisse zurückgibt, diagnostizieren Sie das Problem, indem Sie die folgenden Schritte ausführen: 

1. Überprüfen Sie den Zustand des Agents, indem Sie den folgenden Befehl ausführen: 

    `kubectl get ds omsagent --namespace=kube-system`

    Die Ausgabe sollte wie folgt aussehen, was auf eine ordnungsgemäße Bereitstellung hinweist:

    ```
    User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
    NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
    omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
    ```  
2. Überprüfen Sie den Bereitstellungsstatus in Agent-Version *06072018* oder höher mit dem Befehl:

    `kubectl get deployment omsagent-rs -n=kube-system`

    Die Ausgabe sollte wie im folgenden Beispiel aussehen, was auf eine ordnungsgemäße Bereitstellung hinweist:

    ```
    User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
    NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
    omsagent   1         1         1            1            3h
    ```

3. Überprüfen Sie den Status des Pods, um festzustellen, ob er ausgeführt wird, mit dem folgenden Befehl: `kubectl get pods --namespace=kube-system`

    Die Ausgabe sollte etwa dem folgenden Beispiel mit dem Status *Ausgeführt* für „omsagent“ ähneln:

    ```
    User@aksuser:~$ kubectl get pods --namespace=kube-system 
    NAME                                READY     STATUS    RESTARTS   AGE 
    aks-ssh-139866255-5n7k5             1/1       Running   0          8d 
    azure-vote-back-4149398501-7skz0    1/1       Running   0          22d 
    azure-vote-front-3826909965-30n62   1/1       Running   0          22d 
    omsagent-484hw                      1/1       Running   0          1d 
    omsagent-fkq7g                      1/1       Running   0          1d 
    ```

4. Überprüfen Sie die Agent-Protokolle. Beim Bereitstellen des Container-Agents wird durch OMI-Befehle eine Schnellüberprüfung ausgeführt und die Agent- sowie Anbieterversion angezeigt. 

5. Um zu überprüfen, ob der Agent erfolgreich bereitgestellt wurde, führen Sie diesen Befehl aus: `kubectl logs omsagent-484hw --namespace=kube-system`

    Der Status sollte wie im folgenden Beispiel aussehen:

    ```
    User@aksuser:~$ kubectl logs omsagent-484hw --namespace=kube-system
    :
    :
    instance of Container_HostInventory
    {
        [Key] InstanceID=3a4407a5-d840-4c59-b2f0-8d42e07298c2
        Computer=aks-nodepool1-39773055-0
        DockerVersion=1.13.1
        OperatingSystem=Ubuntu 16.04.3 LTS
        Volume=local
        Network=bridge host macvlan null overlay
        NodeRole=Not Orchestrated
        OrchestratorType=Kubernetes
    }
    Primary Workspace: b438b4f6-912a-46d5-9cb1-b44069212abc    Status: Onboarded(OMSAgent Running)
    omi 1.4.2.2
    omsagent 1.6.0.23
    docker-cimprov 1.0.0.31
    ```

## <a name="error-messages"></a>Fehlermeldungen

In der unten stehenden Tabelle sind bekannte Fehler aufgeführt, die bei der Verwendung von Azure Monitor für Container auftreten können.

| Fehlermeldungen  | Aktion |  
| ---- | --- |  
| Fehlermeldung `No data for selected filters`  | Die Einrichtung der Überwachung des Datenflusses für neu erstellte Cluster kann einige Zeit in Anspruch nehmen. Es dauert mindestens 10 bis 15 Minuten, bis Daten für Ihren Cluster angezeigt werden. |   
| Fehlermeldung `Error retrieving data` | Während der Einrichtung eines Azure Kubernetes-Dienstclusters für die Überwachung von Integrität und Leistung wird zwischen dem Cluster und dem Azure Log Analytics-Arbeitsbereich eine Verbindung hergestellt. Zum Speichern sämtlicher Überwachungsdaten für Ihren Cluster wird ein Log Analytics-Arbeitsbereich verwendet. Dieser Fehler kann auftreten, wenn ein Log Analytics-Arbeitsbereich gelöscht wurde oder verloren gegangen ist. Überprüfen Sie anhand der Informationen unter [Verwalten des Zugriffs](../platform/manage-access.md#view-workspace-details), ob Ihr Arbeitsbereich verfügbar ist. Wenn der Arbeitsbereich fehlt, müssen Sie die Überwachung Ihres Clusters mit Azure Monitor für Container erneut aktivieren. Zum erneuten Aktivieren müssen Sie die Überwachung für den Cluster [deaktivieren](container-insights-optout.md) und dann mit Azure Monitor für Container [erneut aktivieren](container-insights-enable-new-cluster.md). |  
| `Error retrieving data` nach dem Hinzufügen von Azure Monitor für Container mithilfe von „az aks cli“ | Beim Aktivieren der Überwachung mithilfe von `az aks cli` wird Azure Monitor für Container möglicherweise nicht ordnungsgemäß bereitgestellt. Überprüfen Sie, ob die Lösung bereitgestellt ist. Wechseln Sie zu diesem Zweck zu Ihrem Log Analytics-Arbeitsbereich, und wählen Sie im linken Bereich **Lösungen** aus, um zu überprüfen, ob die Lösung verfügbar ist. Um dieses Problem zu lösen, müssen Sie die Lösung erneut bereitstellen. Befolgen Sie dazu die Anweisungen zum [Bereitstellen von Azure Monitor für Container](container-insights-onboard.md). |  

Um Sie bei der Diagnose zu unterstützen, haben wir [hier](https://github.com/Microsoft/OMS-docker/tree/ci_feature_prod/Troubleshoot#troubleshooting-script) ein Skript zur Problembehandlung bereitgestellt.  

## <a name="next-steps"></a>Nächste Schritte
Bei aktivierter Überwachung zum Erfassen der Integritätsmetriken für AKS-Clusterknoten wie auch für Pods stehen diese Integritätsmetriken im Azure-Portal zur Verfügung. Informationen zum Erlernen der Verwendung von Azure Monitor für Container finden Sie unter [Anzeigen der Integrität von Azure Kubernetes Service](container-insights-analyze.md).