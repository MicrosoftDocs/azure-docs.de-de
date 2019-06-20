---
title: Verwalten des Avere vFXT-Clusters – Azure
description: Verwalten des Avere-Clusters – Hinzufügen oder Entfernen von Knoten, Neustarten, Beenden oder Zerstören des vFXT-Clusters
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: v-erkell
ms.openlocfilehash: be9205fdf7fec0661d7382ed0d1bedf47487b15e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60409737"
---
# <a name="manage-the-avere-vfxt-cluster"></a>Verwalten des Avere vFXT-Clusters

Nach der Erstellung des Clusters müssen Sie möglicherweise Clusterknoten hinzufügen oder den Cluster beenden oder neu starten. Wenn Ihr Projekt abgeschlossen ist, müssen Sie zudem wissen, wie Sie den Cluster beenden und dauerhaft entfernen können. 

Abhängig von der Aufgabe der Clusterverwaltung müssen Sie möglicherweise die Avere-Systemsteuerung, das vfxt.py-Befehlszeilenskript zur Erstellung von Clustern oder das Azure-Portal verwenden. 

Diese Tabelle bietet eine Übersicht darüber, welche Tools für die einzelnen Aufgaben verwendet werden können. 

| Aktion | Avere-Systemsteuerung | vfxt.py  | Azure-Portal |
| --- | --- | --- | --- |
| Hinzufügen von Clusterknoten | no | Ja | no |
| Entfernen von Clusterknoten | Ja | no | no |
| Beenden eines Clusterknotens | Ja (kann auch Dienste oder das System neu starten) | no | Das Herunterfahren einer Knoten-VM über das Portal wird als Knotenfehler interpretiert |
| Starten eines beendeten Knotens | no | no | Ja |
| Zerstören eines einzelnen Clusterknotens | no | no | Ja |
| Neustarten des Clusters |  |  |  |
| Sicheres Herunterfahren oder Beenden des Clusters | Ja | Ja | no |
| Zerstören des Clusters  | no | Ja | Ja, aber die Datenintegrität wird nicht garantiert |

Detaillierte Anweisungen für die einzelnen Tools finden Sie unten.

## <a name="about-stopped-instances-in-azure"></a>Informationen zu beendeten Instanzen in Azure

Wenn Sie einen virtuellen Azure-Computer herunterfahren oder beenden, fallen keine Computegebühren mehr an, aber Sie müssen dennoch für den Speicher bezahlen. Wenn Sie einen vFXT-Knoten oder den gesamten vFXT-Cluster herunterfahren und nicht beabsichtigen, ihn neu zu starten, sollten Sie das Azure-Portal verwenden, um die zugehörigen virtuellen Computer zu löschen. 

Im Azur-Portal zeigt ein *beendeter* Knoten (der neu gestartet werden kann) den Status **Beendet** im Azur-Portal an. Ein *gelöschter* Knoten zeigt den Status **Beendet (Zuordnung aufgehoben)** an und es fallen keine Compute- oder Speicherkosten mehr an.

Bevor Sie den virtuellen Computer löschen, stellen Sie sicher, dass alle geänderten Daten aus dem Cache in den Back-End-Speicher geschrieben wurden, indem Sie die Optionen „Avere-Systemsteuerung“ oder „vfxt.py“ verwenden, um den Cluster zu beenden oder herunterzufahren.

## <a name="manage-the-cluster-with-avere-control-panel"></a>Verwaltung des Clusters über die Avere-Systemsteuerung 

Für diese Aufgaben kann die Avere-Systemsteuerung verwendet werden: 

* Einzelne Knoten beenden oder neu starten
* Einen Knoten aus dem Cluster entfernen
* Den gesamten Cluster beenden oder neu starten

Die Avere-Systemsteuerung priorisiert die Datenintegrität, daher versucht sie, alle geänderten Daten vor einem möglicherweise destruktiven Vorgang in den Back-End-Speicher zu schreiben. Dadurch ist sie eine sicherere Option als das Avery-Portal. 

Greifen Sie über einen Webbrowser auf die Avere-Systemsteuerung zu. Befolgen Sie die Anweisungen unter [Zugreifen auf den vFXT-Cluster](avere-vfxt-cluster-gui.md), wenn Sie Hilfe benötigen.

### <a name="manage-nodes-with-avere-control-panel"></a>Verwalten von Knoten mit der Avere-Systemsteuerung

Die Einstellungsseite **FXT-Knoten** enthält Steuerelemente für die Verwaltung einzelner Knoten.

Um einen Knoten herunterzufahren, neu zu starten oder zu entfernen, suchen Sie den Knoten in der Liste auf der Seite **FXT-Knoten**, und klicken Sie auf die entsprechende Schaltfläche in der Spalte **Aktionen**.

> [!NOTE] 
> IP-Adressen können zwischen Clusterknoten verschoben werden, wenn sich die Anzahl der aktiven Knoten ändert.

Weitere Informationen finden Sie unter [Cluster > FXT-Knoten](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_fxt_nodes.html#gui-fxt-nodes>) in der Anleitung zu den Clustereinstellungen von Avere.

### <a name="stop-or-reboot-the-cluster-with-avere-control-panel"></a>Beenden oder Neustarten des Clusters über die Avere-Systemsteuerung

Auf der Einstellungsseite **Systemwartung** finden Sie Befehle zum Neustarten von Clusterdiensten, zum Neustarten des Clusters oder zum sicheren Herunterfahren des Clusters. Weitere Informationen finden Sie unter [Verwaltung > Systemwartung](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_system_maintenance.html#gui-system-maintenance>) (in der Anleitung zu den Clustereinstellungen von Avere).

Wenn ein Cluster heruntergefahren wird, veröffentlicht er zunächst Statusmeldungen auf der Registerkarte **Dashboard**. Nach einigen Augenblicken reagiert die Avere-Systemsteuerungssitzung nicht mehr, was darauf hinweist, dass der Cluster heruntergefahren ist.

## <a name="manage-the-cluster-with-vfxtpy"></a>Verwalten des Clusters mit vfxt.py

vfxt.py ist ein Befehlszeilentool für die Erstellung und Verwaltung des Clusters. 

vfxt.py ist auf der Clustercontroller-VM vorinstalliert. Wenn Sie es auf einem anderen System installieren möchten, lesen Sie die Dokumentation unter <https://github.com/Azure/AvereSDK>.

Das Skript vfxt.py kann für folgende Aufgaben der Clusterverwaltung verwendet werden:

* Neue Knoten zu einem Cluster hinzufügen
* Einen Cluster beenden oder starten  
* Einen Cluster zerstören

Wie bei der Avere-Systemsteuerung versuchen die vfxt.py-Operationen sicherzustellen, dass geänderte Daten dauerhaft im Back-End-Speicher gespeichert werden, bevor der Cluster oder Knoten heruntergefahren oder zerstört wird. Dadurch ist sie eine sicherere Option als das Avery-Portal.

Ein vollständiges Benutzerhandbuch zu „vfxt.py“ ist auf GitHub verfügbar: [Cloud cluster management with vfxt.py (Cloudclusterverwaltung mit „vfxt.py“)](https://github.com/azure/averesdk/blob/master/docs/README.md)

### <a name="add-cluster-nodes-with-vfxtpy"></a>Hinzufügen von Clusterknoten mit vfxt.py

Ein Beispielbefehlsskript zum Hinzufügen von Clusterknoten ist auf dem Clustercontroller enthalten. Suchen Sie ``./add-nodes`` auf dem Controller und öffnen Sie es in einem Editor, um es mit Ihren Clusterinformationen anzupassen. 

Der Cluster muss ausgeführt werden, um diesen Befehl zu verwenden. 

Geben Sie die folgenden Werte an: 

* Name der Ressourcengruppe für den Cluster sowie für Netzwerk- und Speicherressourcen, wenn sie nicht mit dem Cluster übereinstimmen
* Clusterstandort
* Clusternetzwerk und Subnetz 
* Zugriffsrolle für Clusterknoten (verwenden Sie die integrierte Rolle [Avere Operator](../role-based-access-control/built-in-roles.md#avere-operator))
* IP-Adresse und Administratorkennwort der Clusterverwaltung 
* Anzahl der hinzuzufügenden Knoten (1, 2 oder 3)
* Werte für Knoteninstanztyp und Cachegröße 

Wenn Sie den Prototyp nicht verwenden, müssen Sie einen Befehl wie den folgenden erstellen, der alle oben beschriebenen Informationen enthält. 

```bash
   vfxt.py --cloud-type azure --from-environment \
   --resource-group GROUP_NAME \ 
   [--network-resource-group GROUP_NAME --storage-resource-group GROUP_NAME]  \
   --location LOCATION --azure-network NETWORK_NAME --azure-subnet SUBNET_NAME \
   --add-nodes --nodes NODE_COUNT \
   --management-address CLUSTER_IP --admin-password ADMIN_PASSWORD \
   --instance-type TYPE --node-cache-size SIZE \
   --azure-role "Avere Operator" \
   --log ~/vfxt.log
```

Weitere Informationen finden Sie unter [Hinzufügen von Knoten zu einem Cluster](https://github.com/Azure/AvereSDK/blob/master/docs/using_vfxt_py.md#add-nodes-to-a-cluster) im Benutzerhandbuch zu vfxt.py.

### <a name="stop-a-cluster-with-vfxtpy"></a>Beenden eines Clusters mit vfxt.py

```bash
vfxt.py --cloud-type azure --from-environment --stop --resource-group GROUPNAME --admin-password PASSWORD --management-address ADMIN_IP --location LOCATION --azure-network NETWORK --azure-subnet SUBNET
```

### <a name="start-a-stopped-cluster-with-vfxtpy"></a>Starten eines beendeten Clusters mit vfxt.py

```bash
vfxt.py --cloud-type azure --from-environment --start --resource-group GROUPNAME --admin-password PASSWORD --management-address ADMIN_IP --location LOCATION --azure-network NETWORK --azure-subnet SUBNET --instances INSTANCE1_ID INSTANCE2_ID INSTANCE3_ID ...
```    

Da der Cluster beendet wird, müssen Sie Instanzbezeichner übergeben, um die Clusterknoten anzugeben. Weitere Informationen finden Sie im Benutzerhandbuch zu vfxt.py unter [Angeben des zu ändernden Clusters](https://github.com/Azure/AvereSDK/blob/master/docs/using_vfxt_py.md#specifying-which-cluster-to-modify).

### <a name="destroy-a-cluster-with-vfxtpy"></a>Zerstören eines Clusters mit vfxt.py

```bash
vfxt.py --cloud-type azure --from-environment --destroy --resource-group GROUPNAME --admin-password PASSWORD --management-address ADMIN_IP --location LOCATION --azure-network NETWORK --azure-subnet SUBNET --management-address ADMIN_IP
```

Die Option ``--quick-destroy`` kann verwendet werden, wenn Sie geänderte Daten nicht aus dem Clustercache schreiben möchten.

Weitere Informationen finden Sie im [Benutzerhandbuch zu vfxt.py](<https://github.com/Azure/AvereSDK/blob/master/docs/README.md>).  

## <a name="manage-cluster-vms-from-the-azure-portal"></a>Verwalten von virtuellen Clustercomputern über das Azure-Portal 

Über das Azure-Portal können virtuelle Clustercomputer einzeln zerstört werden, aber die Datenintegrität ist nicht gewährleistet, wenn der Cluster nicht zuerst ordnungsgemäß heruntergefahren wird. 

Das Azure-Portal kann für folgende Aufgaben der Clusterverwaltung verwendet werden: 

* Starten eines beendeten vFXT-Knotens
* Beenden eines einzelnen vFXT-Knotens (der Cluster interpretiert dies als Knotenfehler)
* Zerstören eines vFXT-Clusters, *wenn* Sie nicht sicherstellen müssen, dass geänderte Daten im Clustercache in die Kernspeichereinheit geschrieben werden
* Dauerhaftes Entfernen von vFXT-Knoten und anderen Clusterressourcen, nachdem sie sicher heruntergefahren wurden

### <a name="restart-vfxt-instances-from-the-azure-portal"></a>Neustarten von vFXT-Instanzen über das Azure-Portal

Für den Neustart eines beendeten Knotens müssen Sie das Azure-Portal verwenden. Wählen Sie **Virtuelle Computer** im linken Menü aus, und klicken Sie dann auf den VM-Namen in der Liste, um die zugehörige Übersichtsseite zu öffnen.

Klicken Sie oben auf der Übersichtsseite auf die Schaltfläche **Starten**, um den virtuellen Computer erneut zu aktivieren.

![Azur-Portalbildschirm mit der Option zum Starten eines beendeten virtuellen Computers](media/avere-vfxt-start-stopped-incurring-annot.png)

### <a name="delete-cluster-nodes"></a>Löschen von Clusterknoten

Wenn Sie einen Knoten aus dem vFXT-Cluster löschen möchten, aber den Rest des Clusters behalten, sollten Sie zuerst mit der Avere-Systemsteuerung [den Knoten aus dem Cluster](#manage-nodes-with-avere-control-panel) entfernen.

> [!CAUTION]
> Wenn Sie einen Knoten löschen, ohne ihn zuvor aus dem vFXT-Cluster zu entfernen, können Daten verloren gehen.

Um eine oder mehrere Instanzen, die als vFXT-Knoten verwendet werden, dauerhaft zu zerstören, verwenden Sie das Azure-Portal.
Wählen Sie **Virtuelle Computer** im linken Menü aus, und klicken Sie dann auf den VM-Namen in der Liste, um die zugehörige Übersichtsseite zu öffnen.

Klicken Sie oben auf der Übersichtsseite auf die Schaltfläche **Löschen**, um den virtuellen Computer dauerhaft zu zerstören.

Mit dieser Methode können Sie Clusterknoten dauerhaft entfernen, nachdem sie sicher heruntergefahren wurden. 

### <a name="destroy-the-cluster-from-the-azure-portal"></a>Zerstören des Clusters über das Azure-Portal

> [!NOTE] 
> Wenn alle verbleibenden Clientänderungen im Cache in den Back-End-Speicher geschrieben werden sollen, verwenden Sie entweder die vfxt.py-Option `--destroy` oder die Avere-Systemsteuerung, um den Cluster ordnungsgemäß herunterzufahren, bevor Sie die Knoteninstanzen im Azure-Portal entfernen.

Sie können Knoteninstanzen dauerhaft zerstören, indem Sie sie im Azure-Portal löschen. Sie können sie wie oben beschrieben einzeln löschen oder die Seite **Virtuelle Computer** verwenden, um alle virtuellen Clustercomputer zu finden, sie über die Kontrollkästchen auszuwählen und auf die Schaltfläche **Löschen** zu klicken, um sie alle in einer Aktion zu entfernen.

![Liste der virtuellen Computer im Portal, gefiltert nach dem Begriff „Cluster“, wobei drei der vier ausgewählt und hervorgehoben sind](media/avere-vfxt-multi-vm-delete.png)

### <a name="delete-additional-cluster-resources-from-the-azure-portal"></a>Löschen zusätzlicher Clusterressourcen über das Azure-Portal

Wenn Sie zusätzliche Ressourcen speziell für den vFXT-Cluster erstellt haben, sollten Sie diese möglicherweise entfernen, um den Cluster aufzuheben. Zerstören Sie keine Elemente, die erforderliche Daten enthalten, oder Elemente, die mit anderen Projekten geteilt werden.

Zusätzlich zum Löschen der Clusterknoten sollten Sie erwägen, die folgenden Komponenten zu entfernen: 

* Die Clustercontroller-VM
* Datenträger, die Clusterknoten zugeordnet sind
* Netzwerkschnittstellen und öffentliche IP-Adressen, die Clusterkomponenten zugeordnet sind
* Virtuelle Netzwerke
* Speicherkonten (**nur**, wenn sie keine wichtigen Daten enthalten)
* Verfügbarkeitsgruppe 

![Liste „aller Ressourcen“ des Azure-Portals mit den für einen Testcluster erstellten Ressourcen](media/avere-vfxt-all-resources-list.png)

### <a name="delete-a-clusters-resource-group-from-the-azure-portal"></a>Löschen der Ressourcengruppe eines Clusters über das Azure-Portal

Wenn Sie eine Ressourcengruppe speziell zur Aufnahme des Clusters erstellt haben, können Sie alle zugehörigen Ressourcen für den Cluster zerstören, indem Sie die Ressourcengruppe zerstören. 

> [!Caution] 
> Zerstören Sie die Ressourcengruppe nur, wenn Sie sicher sind, dass in der Gruppe keine wichtigen Komponenten vorhanden sind. Stellen Sie z. B. sicher, dass Sie alle erforderlichen Daten aus allen Speichercontainern innerhalb der Ressourcengruppe verschoben haben.  

Um eine Ressourcengruppe zu löschen, klicken Sie im linken Menü des Portals auf **Ressourcengruppen**, und filtern Sie die Liste der Ressourcengruppen, um diejenige zu finden, die Sie für den vFXT-Cluster erstellt haben. Wählen Sie die Ressourcengruppe aus, und klicken Sie auf die drei Punkte rechts im Fenster. Klicken Sie auf **Ressourcengruppe löschen**. Sie werden vom Portal aufgefordert, den unwiderruflichen Löschvorgang zu bestätigen.  

![Ressourcengruppe mit der Aktion „Ressourcengruppe löschen“](media/avere-vfxt-delete-resource-group.png)
