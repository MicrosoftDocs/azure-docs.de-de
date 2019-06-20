---
title: Bereitstellen von Azure Databricks in Ihrem virtuellen Netzwerk (Vorschauversion)
description: Dieser Artikel beschreibt, wie Sie Azure Databricks in Ihrem virtuellen Netzwerk bereitstellen, auch bekannt als VNet-Einschleusung.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 03/18/2019
ms.openlocfilehash: 2db588a0cf67d7826408139e8facb43a2e897951
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62126680"
---
# <a name="deploy-azure-databricks-in-your-virtual-network-preview"></a>Bereitstellen von Azure Databricks in Ihrem virtuellen Netzwerk (Vorschauversion)

Die standardmäßige Bereitstellung von Azure Databricks ist ein vollständig verwalteter Dienst auf Azure: alle Ressourcen der Datenebene, einschließlich eines virtuellen Netzwerks (VNet), werden in einer gesperrten Ressourcengruppe bereitgestellt. Wenn Sie jedoch eine Netzwerkanpassung benötigen, können Sie die Ressourcen von Azure Databricks in Ihrem eigenen virtuellen Netzwerk bereitstellen (auch VNet-Einschleusung genannt). Damit haben Sie dann folgende Möglichkeiten:

* Sicheres Verbinden von Azure Databricks mit anderen Azure-Diensten (z.B. Azure Storage) über Dienstendpunkte.
* Verbinden mit lokalen Datenquellen zur Verwendung mit Azure Databricks unter Verwendung von benutzerdefinierten Routen.
* Verbinden von Azure Databricks mit einem virtuellen Netzwerkgerät, um den ausgehenden Datenverkehr zu untersuchen und anhand von Zulassungs- und Ablehnungsregeln Maßnahmen zu ergreifen.
* Konfigurieren von Azure Databricks zur Verwendung eines benutzerdefinierten DNS.
* Konfigurieren von Regeln für Netzwerksicherheitsgruppen (NSG), um Einschränkungen des ausgehenden Datenverkehrs zu definieren.
* Bereitstellen von Azure Databricks-Clustern in Ihrem vorhandenen virtuellen Netzwerk.

Durch die Bereitstellung von Azure Databricks-Ressourcen in Ihrem eigenen virtuellen Netzwerk können Sie auch die Vorteile flexibler CIDR-Bereiche nutzen (an einer beliebigen Stelle zwischen /16-/24 für das virtuelle Netzwerk und zwischen /18-/26 für die Subnetze).

  > [!NOTE]
  > Sie können das virtuelle Netzwerk nicht für einen bestehenden Arbeitsbereich ersetzen. Wenn Ihr aktueller Arbeitsbereich nicht die erforderliche Anzahl aktiver Clusterknoten aufnehmen kann, erstellen Sie einen weiteren Arbeitsbereich in einem größeren virtuellen Netzwerk. Folgen Sie [diesen detaillierten Migrationsschritten](howto-regional-disaster-recovery.md#detailed-migration-steps), um Ressourcen (Notebooks, Clusterkonfigurationen, Aufträge) vom alten in den neuen Arbeitsbereich zu kopieren.

## <a name="virtual-network-requirements"></a>Anforderungen für virtuelle Netzwerke

Sie können die Schnittstelle für die Bereitstellung des Azure Databricks-Arbeitsbereichs im Azure Portal verwenden, um ein bestehendes virtuelles Netzwerk automatisch mit den erforderlichen Subnetzen, Netzwerksicherheitsgruppen- und Whitelist-Einstellungen zu konfigurieren, oder Sie können Azure Resource Manager-Vorlagen verwenden, um Ihr virtuelles Netzwerk zu konfigurieren und Ihren Arbeitsbereich bereitzustellen.

Das virtuelle Netzwerk, für das Sie Ihren Azure Databricks-Arbeitsbereich bereitstellen, muss die folgenden Anforderungen erfüllen:

### <a name="location"></a>Location

Das virtuelle Netzwerk muss sich am selben Ort wie der Arbeitsbereich von Azure Databricks befinden.

### <a name="subnets"></a>Subnetze

Das virtuelle Netzwerk muss zwei Subnetze enthalten, die für Azure Databricks bestimmt sind:

   1. Ein privates Subnetz mit einer konfigurierten Netzwerksicherheitsgruppe, die eine clusterinterne Kommunikation ermöglicht.

   2. Ein öffentliches Subnetz mit einer konfigurierten Netzwerksicherheitsgruppe, die die Kommunikation mit der Azure Databricks-Steuerungsebene ermöglicht.

### <a name="address-space"></a>Adressraum

Ein CIDR-Block zwischen /16 - /24 für das virtuelle Netzwerk und ein CIDR-Block zwischen /18 - /26 für die privaten und öffentlichen Subnetze.

### <a name="whitelisting"></a>Whitelisting

Der gesamte ausgehende und eingehende Datenverkehr zwischen den Subnetzen und der Azure Databricks-Steuerungsebene muss auf der Whitelist stehen.

## <a name="create-an-azure-databricks-workspace"></a>Erstellen eines Azure Databricks-Arbeitsbereichs

Dieser Abschnitt beschreibt, wie Sie einen Azure Databricks-Arbeitsbereich im Azure-Portal erstellen und in Ihrem eigenen bestehenden virtuellen Netzwerk bereitstellen. Azure Databricks aktualisiert das virtuelle Netzwerk mit zwei neuen Subnetzen und Netzwerksicherheitsgruppen unter Verwendung der von Ihnen bereitgestellten CIDR-Bereiche, Whitelists für den eingehenden und ausgehenden Subnetzverkehr, und stellt den Arbeitsbereich für das aktualisierte virtuelle Netzwerk bereit.

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen ein virtuelles Netzwerk, in dem Sie den Azure Databricks-Arbeitsbereich bereitstellen. Sie können ein bestehendes virtuelles Netzwerk verwenden oder ein neues erstellen, aber das virtuelle Netzwerk muss sich in der gleichen Region befinden wie der Azure Databricks-Arbeitsbereich, den Sie erstellen möchten. Für das virtuelle Netzwerk ist ein CIDR-Bereich zwischen /16 - /24 erforderlich.

  > [!Warning]
  > Bei einem Arbeitsbereich mit einem kleineren virtuellen Netzwerk – also einem niedrigeren CIDR-Bereich – können schneller keine IP-Adressen (Netzwerkraum) mehr verfügbar sein, als bei einem Arbeitsbereich mit einem größeren virtuellen Netzwerk. So können beispielsweise in einem Arbeitsbereich mit einem /24 virtuellen Netzwerk und /26 Subnetzen maximal 64 Knoten gleichzeitig aktiv sein, während ein Arbeitsbereich mit einem /20 virtuellen Netzwerk und /22 Subnetzen maximal 1.024 Knoten aufnehmen kann.

  Ihre Subnetze werden automatisch erstellt, wenn Sie Ihren Arbeitsbereich konfigurieren, und Sie haben die Möglichkeit, den CIDR-Bereich für die Subnetze während der Konfiguration anzugeben.

## <a name="configure-the-virtual-network"></a>Konfigurieren des virtuellen Netzwerks

1. Wählen Sie im Azure-Portal **+ Eine Ressource erstellen > Analytics > Azure Databricks**, um das Dialogfeld für den Azure Databricks-Dienst zu öffnen.

2. Führen Sie die in Schritt 2 beschriebenen Konfigurationsschritte aus: Erstellen Sie einen Azure Databricks-Arbeitsbereich in den ersten Schritten und wählen Sie in den Optionen für Ihr virtuelles Netzwerk „Azure Databricks-Arbeitsbereich bereitstellen“.

   ![Erstellen eines Azure Databricks-Diensts](./media/vnet-injection/create-databricks-service.png)

3. Wählen Sie das virtuelle Netzwerk aus, das Sie verwenden möchten.

   ![Optionen für das virtuelle Netzwerk](./media/vnet-injection/select-vnet.png)

4. Stellen Sie CIDR-Bereiche in einem Block zwischen /18 - /26 für zwei Subnetze bereit, die für Azure Databricks bestimmt sind:

   * Ein öffentliches Subnetz wird mit einer dazugehörigen Netzwerksicherheitsgruppe erstellt, die die Kommunikation mit der Azure Databricks-Steuerungsebene ermöglicht.
   * Ein privates Subnetz wird mit einer dazugehörigen Netzwerksicherheitsgruppe erstellt, die eine clusterinterne Kommunikation ermöglicht.

5. Klicken Sie auf **Erstellen**, um den Azure Databricks-Arbeitsbereich für das virtuelle Netzwerk bereitzustellen.

## <a name="advanced-resource-manager-configurations"></a>Erweiterte Resource Manager-Konfigurationen

Wenn Sie mehr Kontrolle über die Konfiguration des virtuellen Netzwerks haben möchten– z.B. wenn Sie bestehende Subnetze verwenden, bestehende Netzwerksicherheitsgruppen verwenden oder eigene Sicherheitsregeln erstellen möchten – können Sie die folgenden Azure Resource Manager-Vorlagen anstelle der Konfiguration und Bereitstellung des virtuellen Netzwerks und des Arbeitsbereichs im Portal verwenden.

### <a name="all-in-one"></a>All-in-One

Um ein virtuelles Netzwerk, Netzwerksicherheitsgruppen und den  Azure Databricks-Arbeitsbereich in einer Komplettlösung zu erstellen, verwenden Sie die [All-in-One-Vorlage für Arbeitsbereiche mit eingeschleusten Databricks-VNet](https://azure.microsoft.com/resources/templates/101-databricks-all-in-one-template-for-vnet-injection/).

Wenn Sie diese Vorlage verwenden, müssen Sie keine manuelle Whitelist des Subnetzverkehrs erstellen.

### <a name="network-security-groups"></a>Netzwerksicherheitsgruppen

Um Netzwerksicherheitsgruppen mit den erforderlichen Regeln für ein bestehendes virtuelles Netzwerk zu erstellen, verwenden Sie die [Vorlage für Netzwerksicherheitsgruppen für Databricks-VNet-Einschleusung](https://azure.microsoft.com/resources/templates/101-databricks-nsg-for-vnet-injection).

Wenn Sie diese Vorlage verwenden, müssen Sie keine manuelle Whitelist des Subnetzverkehrs erstellen.

### <a name="virtual-network"></a>Virtuelles Netzwerk

Um ein virtuelles Netzwerk mit den richtigen öffentlichen und privaten Subnetzen zu erstellen, verwenden Sie die [Vorlage für virtuelle Netzwerk für die Databricks-VNet-Einschleusung](https://azure.microsoft.com/resources/templates/101-databricks-vnet-for-vnet-injection).

Wenn Sie diese Vorlage verwenden, ohne auch die Vorlage für Netzwerksicherheitsgruppen zu verwenden, müssen Sie den Netzwerksicherheitsgruppen, die Sie im virtuellen Netzwerk verwenden, manuell Whitelistregeln hinzufügen.

### <a name="azure-databricks-workspace"></a>Azure Databricks-Arbeitsbereich

Um einen Azure Databricks-Arbeitsbereich in einem bestehenden virtuellen Netzwerk bereitzustellen, in dem bereits öffentliche und private Subnetze und ordnungsgemäß konfigurierte Netzwerksicherheitsgruppen eingerichtet sind, verwenden Sie die [Arbeitsbereichsvorlage für die Databricks-VNet-Einschleusung](https://azure.microsoft.com/resources/templates/101-databricks-workspace-with-vnet-injection).

Wenn Sie diese Vorlage verwenden, ohne auch die Vorlage für Netzwerksicherheitsgruppen zu verwenden, müssen Sie den Netzwerksicherheitsgruppen, die Sie im virtuellen Netzwerk verwenden, manuell Whitelistregeln hinzufügen.

## <a name="whitelisting-subnet-traffic"></a>Erstellen von Whitelists für den Subnetzdatenverkehr

Wenn Sie zur Erstellung Ihrer Netzwerksicherheitsgruppen nicht das [Azure-Portal](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html#vnet-inject-portal) oder die [Azure Resource Manager-Vorlagen](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html#vnet-inject-advanced) verwenden, müssen Sie den folgenden Datenverkehr in Ihren Subnetzen manuell auf die Whitelist setzen.

|Direction|Protocol|`Source`|Quellport|Ziel|Zielport|
|---------|--------|------|-----------|-----------|----------------|
|Eingehend|\*|VirtualNetwork|\*|\*|\*|
|Eingehend|\*|NAT-API der Steuerungsebene|\*|\*|22|
|Eingehend|\*|NAT-API der Steuerungsebene|\*|\*|5557|
|Ausgehend|\*|\*|\*|Webapp-IP|\*|
|Ausgehend|\*|\*|\*|SQL (Diensttag)|\*|
|Ausgehend|\*|\*|\*|Speicher (Diensttag)|\*|
|Ausgehend|\*|\*|\*|VirtualNetwork|\*|

Setzen Sie den Subnetzdatenverkehr, der folgenden IP-Adressen verwendet, auf die Whitelist. Für SQL (Metastore) und Storage (Artefakt- und Protokollspeicher) sollten Sie die [Diensttags](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) für SQL und Storage verwenden.

|Azure Databricks-Region|Dienst|Öffentliche IP-Adresse|
|-----------------------|-------|---------|
|USA (Ost)|NAT der Steuerungsebene </br></br>Webapp|23.101.152.95/32 </br></br>40.70.58.221/32|
|USA (Ost) 2|NAT der Steuerungsebene </br></br>Webapp|23.101.152.95/32 </br></br>40.70.58.221/32|
|USA Nord Mitte|NAT der Steuerungsebene </br></br>Webapp|23.101.152.95/32 </br></br>40.70.58.221/32|
|USA (Mitte)|NAT der Steuerungsebene </br></br>Webapp|23.101.152.95/32 </br></br>40.70.58.221/32|
|USA Süd Mitte|NAT der Steuerungsebene </br></br>Webapp|40.83.178.242/32 </br></br>40.118.174.12/32|
|USA (Westen)|NAT der Steuerungsebene </br></br>Webapp|40.83.178.242/32 </br></br>40.118.174.12/32|
|USA, Westen 2|NAT der Steuerungsebene </br></br>Webapp|40.83.178.242/32 </br></br>40.118.174.12/32|
|Kanada, Mitte|NAT der Steuerungsebene </br></br>Webapp|40.85.223.25/32 </br></br>13.71.184.74/32|
|Kanada, Osten|NAT der Steuerungsebene </br></br>Webapp|40.85.223.25/32 </br></br>13.71.184.74/32|
|UK, Westen|NAT der Steuerungsebene </br></br>Webapp|51.140.203.27/32 </br></br>51.140.204.4/32|
|UK, Süden|NAT der Steuerungsebene </br></br>Webapp|51.140.203.27/32 </br></br>51.140.204.4/32|
|Europa, Westen|NAT der Steuerungsebene </br></br>Webapp|23.100.0.135/32 </br></br>52.232.19.246/32|
|Nordeuropa|NAT der Steuerungsebene </br></br>Webapp|23.100.0.135/32 </br></br>52.232.19.246/32|
|Indien, Mitte|NAT der Steuerungsebene </br></br>Webapp|104.211.89.81/32 </br></br>104.211.101.14/32|
|Indien (Süden)|NAT der Steuerungsebene </br></br>Webapp|104.211.89.81/32 </br></br>104.211.101.14/32|
|Indien, Westen|NAT der Steuerungsebene </br></br>Webapp|104.211.89.81/32 </br></br>104.211.101.14/32|
|Südostasien|NAT der Steuerungsebene </br></br>Webapp|52.187.0.85/32 </br></br>52.187.145.107/32|
|Asien, Osten|NAT der Steuerungsebene </br></br>Webapp|52.187.0.85/32 </br></br>52.187.145.107/32|
|Australien (Osten)|NAT der Steuerungsebene </br></br>Webapp|13.70.105.50/32 </br></br>13.75.218.172/32|
|Australien, Südosten|NAT der Steuerungsebene </br></br>Webapp|13.70.105.50/32 </br></br>13.75.218.172/32|
|Australien, Mitte|NAT der Steuerungsebene </br></br>Webapp|13.70.105.50/32 </br></br>13.75.218.172/32|
|Australien, Mitte 2|NAT der Steuerungsebene </br></br>Webapp|13.70.105.50/32 </br></br>13.75.218.172/32|
|Japan, Osten|NAT der Steuerungsebene </br></br>Webapp|13.78.19.235/32 </br></br>52.246.160.72/32|
|Japan, Westen|NAT der Steuerungsebene </br></br>Webapp|13.78.19.235/32 </br></br>52.246.160.72/32|

## <a name="troubleshooting"></a>Problembehandlung

### <a name="workspace-launch-errors"></a>Fehler beim Starten des Arbeitsbereichs

Das Starten eines Arbeitsbereichs in einem benutzerdefinierten virtuellen Netzwerk schlägt fehl und im Anmeldebildschirm von Azure Databricks wird der folgende Fehler angezeigt: **„Beim Erstellen Ihres Arbeitsbereichs ist ein Fehler aufgetreten. Stellen Sie sicher, dass die benutzerdefinierte Konfiguration Ihres Netzwerks richtig ist, und versuchen Sie es erneut.“**

Dieser Fehler wird durch eine Netzwerkkonfiguration verursacht, die nicht den Anforderungen entspricht. Vergewissern Sie sich, dass Sie bei der Erstellung des Arbeitsbereichs die Anweisungen in diesem Thema befolgt haben.

### <a name="cluster-creation-errors"></a>Fehler bei der Clustererstellung

**Die Instanzen ist nicht erreichbar: Ressourcen sind über SSH nicht erreichbar.**

Mögliche Ursache: Datenverkehr von der Steuerungsebene zum Worker wird blockiert. Sie können das Problem beheben, indem Sie sicherstellen, dass die Eingangssicherheitsregeln den Anforderungen entsprechen. Wenn Sie die Bereitstellung in einem bestehenden virtuellen Netzwerk durchführen, das mit Ihrem lokalen Netzwerk verbunden ist, überprüfen Sie das Setup anhand der Informationen in „Verbinden Ihres Azure Databricks-Arbeitsbereichs mit Ihrem lokalen Netzwerk“.

**Unerwarteter Startfehler: Beim Einrichten des Clusters ist ein unerwarteter Fehler aufgetreten. Versuchen Sie es erneut, und kontaktieren Sie Azure Databricks, wenn das Problem weiterhin besteht. Meldung zu internem Fehler: Timeout beim Platzieren von Knoten.**

Mögliche Ursache: Datenverkehr vom Worker zu Azure Storage-Endpunkten wird blockiert. Sie können das Problem beheben, indem Sie sicherstellen, dass die Ausgangssicherheitsregeln den Anforderungen entsprechen. Wenn Sie benutzerdefinierte DNS-Server verwenden, überprüfen Sie auch den Status der DNS-Server in Ihrem virtuellen Netzwerk.

**Startfehler des Cloudanbieters: Beim Einrichten des Clusters ist ein unerwarteter Cloudanbieterfehler aufgetreten. Weitere Informationen finden Sie im Azure Databricks-Handbuch. Azure-Fehlercode: AuthorizationFailed/InvalidResourceReference.**

Mögliche Ursache: das virtuelle Netzwerk oder Subnetze sind nicht mehr vorhanden. Stellen Sie sicher, dass das virtuelle Netzwerk und die Subnetze existieren.

**Cluster wurde beendet. Ursache: Fehler beim Starten von Spark: Spark konnte nicht rechtzeitig starten. Dieses Problem kann durch einen fehlerhaften Hive-Metastore, ungültige Spark-Konfigurationen oder fehlerhafte Init-Skripte verursacht werden. Lesen Sie die Spark-Treiberprotokolle, um dieses Problem zu beheben, und wenden Sie sich an Databricks, wenn das Problem weiterhin besteht. Meldung zu internem Fehler: Fehler beim Starten von Spark: Treiber konnte nicht rechtzeitig gestartet werden.**

Mögliche Ursache: Der Container kann nicht mit der Hosting-Instanz oder dem DBFS-Speicherkonto kommunizieren. Beheben Sie das Problem, indem Sie eine benutzerdefinierte Route zu den Subnetzen für das DBFS-Speicherkonto hinzufügen, wobei der nächste Hop das Internet ist.

### <a name="notebook-command-errors"></a>Fehler bei Notebook-Befehl

**Command is not responding (Der Befehl reagiert nicht)**

Mögliche Ursache: Die Kommunikation zwischen den Workern wird blockiert. Sie können das Problem beheben, indem Sie sicherstellen, dass die Eingangssicherheitsregeln den Anforderungen entsprechen.

**Notebook-Workflow schlägt fehl mit der Ausnahme: com.databricks.WorkflowException: org.apache.http.conn.ConnectTimeoutException**

Mögliche Ursache: Datenverkehr vom Worker zur Azure Databricks-Webapp wird blockiert. Sie können das Problem beheben, indem Sie sicherstellen, dass die Ausgangssicherheitsregeln den Anforderungen entsprechen.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Extrahieren, Transformieren und Laden von Daten mithilfe von Azure Databricks](databricks-extract-load-sql-data-warehouse.md)
