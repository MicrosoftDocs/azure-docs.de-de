---
title: (VERALTET) Überwachen von Azure-DC/OS-Clustern – Operations Management
description: Überwachen eines Azure Container Service-DC/OS-Clusters mit Log Analytics.
services: container-service
author: keikhara
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 11/17/2016
ms.author: keikhara
ms.custom: mvc
ms.openlocfilehash: 290141136672729060f5156d645c47ac303fa0c3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60809956"
---
# <a name="deprecated-monitor-an-azure-container-service-dcos-cluster-with-log-analytics"></a>(VERALTET) Überwachen eines Azure Container Service-DC/OS-Clusters mit Log Analytics

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Log Analytics ist die cloudbasierte IT-Verwaltungslösung von Microsoft, die Ihnen das Verwalten und Schützen Ihrer lokalen und cloudbasierten Infrastruktur erleichtert. Die Containerlösung ist eine Lösung in Log Analytics, mit der Sie das Inventar, die Leistung und die Protokolle von Containern an einer zentralen Stelle anzeigen können. Sie können Container überwachen und Problembehebungen durchführen, indem Sie die Protokolle an einer zentralen Stelle anzeigen und Container mit zu hoher Aktivität und zu hohem Ressourcenverbrauch auf einem Host suchen.

![](media/container-service-monitoring-oms/image1.png)

Weitere Informationen zur Containerlösung finden Sie unter [Log Analytics-Containerlösung](../../azure-monitor/insights/containers.md).

## <a name="setting-up-log-analytics-from-the-dcos-universe"></a>Einrichten von Log Analytics in DC/OS-Umgebungen


In diesem Artikel wird vorausgesetzt, dass Sie DC/OS eingerichtet und einfache Webcontaineranwendungen im Cluster bereitgestellt haben.

### <a name="pre-requisite"></a>Voraussetzung
- [Microsoft Azure-Abonnement](https://azure.microsoft.com/free/) – hierfür steht Ihnen ein kostenloses Abonnement zur Verfügung.  
- Einrichtung des Log Analytics-Arbeitsbereichs – siehe „Schritt 3“ weiter unten.
- [DC/OS-Befehlszeilenschnittstelle](https://docs.mesosphere.com/1.12/cli) installiert.

1. Klicken Sie auf dem DC/OS-Dashboard auf „Universe“, und suchen Sie nach „OMS“ – wie unten dargestellt.

   >[!NOTE]
   >OMS wird jetzt als Log Analytics bezeichnet.

   ![](media/container-service-monitoring-oms/image2.png)

2. Klicken Sie auf **Installieren**. Es werden ein Popupfenster mit den Versionsinformationen sowie die Schaltflächen **Paket installieren** oder **Erweiterte Installation** angezeigt. Wenn Sie auf **Advanced Installation** klicken, werden Sie zur Seite **OMS specific configuration properties** weitergeleitet.

   ![](media/container-service-monitoring-oms/image3.png)

   ![](media/container-service-monitoring-oms/image4.png)

3. Hier werden Sie dazu aufgefordert, die Werte für `wsid` (ID des Log Analytics-Arbeitsbereichs) und `wskey` (der primäre Schlüssel für die Arbeitsbereichs-ID) einzugeben. Um sowohl `wsid` als auch `wskey` abzurufen, müssen Sie ein Konto unter <https://mms.microsoft.com> erstellen.
   Führen Sie die entsprechenden Schritte aus, um ein Konto zu erstellen. Nachdem Sie das Konto erstellt haben, müssen Sie Ihre `wsid` und `wskey` abrufen, indem Sie auf **Einstellungen**, **Verbundene Quellen** und **Linux-Server** klicken, wie unten dargestellt.

   ![](media/container-service-monitoring-oms/image5.png)

4. Wählen Sie die Anzahl der gewünschten Instanzen aus, und klicken Sie auf die Schaltfläche „Überprüfen und Installieren“. In der Regel sollte die Anzahl von Instanzen der Anzahl von virtuellen Computern in Ihrem Agent-Cluster entsprechen. Der Log Analytics-Agent für Linux wird auf jedem virtuellen Computer, für den Überwachungs- und Anmeldeinformationen gesammelt werden sollen, als individueller Container installiert.

   [!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

## <a name="setting-up-a-simple-log-analytics-dashboard"></a>Einrichten eines einfachen Log Analytics-Dashboards

Nachdem Sie den Log Analytics-Agent für Linux auf den virtuellen Computern installiert haben, richten Sie im nächsten Schritt das Log Analytics-Dashboard ein. Sie können das Dashboard über das Azure-Portal einrichten.

### <a name="azure-portal"></a>Azure-Portal 

Melden Sie sich beim Azure-Portal unter <https://portal.microsoft.com/> an. Wechseln Sie zu **Marketplace**, wählen Sie **Überwachung und Verwaltung**, und klicken Sie auf **Alle anzeigen**. Geben Sie „`containers`container“ in das Suchfeld ein. Der Begriff „Container“ wird in den Suchergebnissen angezeigt. Wählen Sie **Container**, und klicken Sie auf **Erstellen**.

![](media/container-service-monitoring-oms/image9.png)

Nachdem Sie auf **Erstellen** geklickt haben, werden Sie nach Ihrem Arbeitsbereich gefragt. Wählen Sie Ihren Arbeitsbereich aus, oder erstellen Sie einen neuen, wenn Sie noch keinen besitzen.

![](media/container-service-monitoring-oms/image10.PNG)

Nach dem Auswählen Ihres Arbeitsbereichs klicken Sie auf **Erstellen**.

![](media/container-service-monitoring-oms/image11.png)

Weitere Informationen zur Log Analytics-Containerlösung finden Sie unter [Log Analytics-Containerlösung](../../azure-monitor/insights/containers.md).

### <a name="how-to-scale-log-analytics-agent-with-acs-dcos"></a>Skalieren des Log Analytics-Agents mit ACS DC/OS 

Wenn der Log Analytics-Agent nicht auf der richtigen Anzahl von Knoten bereitgestellt wurde oder wenn Sie VM-Skalierungsgruppe durch Hinzufügen weiterer virtueller Computer hochskalieren möchten, können Sie dies durch Skalieren des `msoms`-Diensts erreichen.

Sie können dazu entweder zur Registerkarte „Marathon“ oder „DC/OS UI-Dienste“ wechseln und Ihre Knotenanzahl zentral hochskalieren.

![](media/container-service-monitoring-oms/image12.PNG)

Dadurch erfolgt die Bereitstellung auf anderen Knoten, auf denen der Log Analytics-Agent noch nicht bereitgestellt wurde.

## <a name="uninstall-ms-oms"></a>Deinstallieren der MS OMS

Geben Sie den folgenden Befehl ein, um die MS OMS zu deinstallieren:

```bash
$ dcos package uninstall msoms
```

## <a name="let-us-know"></a>Informieren Sie uns darüber!
Was funktioniert? Was fehlt? Was benötigen Sie noch, was nützlich für Sie sein könnte? Informieren Sie uns: <a href="mailto:OMSContainers@microsoft.com">OMSContainers</a>.

## <a name="next-steps"></a>Nächste Schritte

 Nachdem Sie Log Analytics für die Überwachung Ihrer Container eingerichtet haben, [sehen Sie sich Ihr Container-Dashboard an](../../azure-monitor/insights/containers.md).
