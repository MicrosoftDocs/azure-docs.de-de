---
title: Einrichten der Notfallwiederherstellung für eine IIS-basierte Webanwendung mit mehreren Ebenen mithilfe von Azure Site Recovery | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie virtuelle Computer einer IIS-Webfarm mit Azure Site Recovery replizieren.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: mayg
ms.openlocfilehash: 66b9342f1a67c4c9d35fda447a297cc64d048c1e
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66480297"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-iis-based-web-application"></a>Einrichten der Notfallwiederherstellung für eine IIS-basierte Webanwendung mit mehreren Ebenen

Die Anwendungssoftware ist der Motor der geschäftlichen Produktivität in einer Organisation. Verschiedene Webanwendungen können in einer Organisation unterschiedliche Zwecke erfüllen. Einige Anwendungen, z.B. Anwendungen für Gehaltsabrechnungen, Finanzanwendungen und Kunden zugängliche Websites sind für ein Unternehmen möglicherweise von entscheidender Bedeutung. Um einen Produktivitätsverlust zu verhindern, ist es für die Organisation wichtig, dass diese Anwendungen ständig verfügbar sind und ausgeführt werden. Vor allem kann eine ständige Verfügbarkeit dieser Anwendungen Schaden an der Marke oder dem Image der Organisation verhindern.

Wichtige Webanwendungen werden normalerweise als Anwendungen mit mehreren Ebenen eingerichtet, wobei Web-, Datenbank- und Anwendungsbereich auf unterschiedlichen Ebenen angeordnet werden. Die Anwendungen sind nicht nur auf unterschiedliche Ebenen verteilt, sondern auf jeder Ebene können dafür auch mehrere Server eingesetzt werden, um für den Datenverkehr einen Lastenausgleich vorzunehmen. Außerdem können die Zuordnungen zwischen verschiedenen Ebenen und auf dem Webserver auf statischen IP-Adressen basieren. Bei einem Failover müssen einige dieser Zuordnungen aktualisiert werden. Dies gilt vor allem, wenn auf dem Webserver mehrere Websites konfiguriert wurden. Bei Webanwendungen, die SSL verwenden, müssen Zertifikatbindungen aktualisiert werden.

Herkömmliche Wiederherstellungsmethoden ohne Replikation umfassen das Sichern von verschiedenen Konfigurationsdateien, Registrierungseinstellungen, Bindungen, benutzerdefinierten Komponenten (COM oder .NET), Inhalten und Zertifikaten. Die Dateien werden über eine Reihe von manuellen Schritte wiederhergestellt. Die traditionellen Wiederherstellungsmethoden der Sicherung und manuellen Wiederherstellung von Dateien sind aufwendig, fehleranfällig und nicht skalierbar. Beispielsweise könnten Sie vergessen, die Zertifikate zu sichern. Nach einem Failover haben Sie keine andere Möglichkeit, als neue Zertifikate für den Server zu erwerben.

Eine gute Lösung zur Notfallwiederherstellung sollte die Modellierung von Wiederherstellungsplänen für komplexe Anwendungsarchitekturen ermöglichen. Sie sollte außerdem die Möglichkeit aufweisen, dem Wiederherstellungsplan benutzerdefinierte Schritte zur Durchführung von Anwendungszuordnungen zwischen unterschiedlichen Ebenen hinzuzufügen. Bei einem Notfall bieten Anwendungszuordnungen eine einfache und sichere Möglichkeit mit nur einem Klick, die für ein niedrigeres RTO sorgt.

In diesem Artikel wird beschrieben, wie Sie mit [Azure Site Recovery](site-recovery-overview.md) eine Webanwendung schützen, die auf Internet Information Services (IIS) basiert. Wir stellen die bewährten Methoden zum Replizieren einer IIS-basierten Webanwendung mit drei Ebenen in Azure, zum Durchführen einer Übung für die Notfallwiederherstellung und zum Durchführen eines Failovers für die Anwendung in Azure vor.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie beginnen, stellen Sie sicher, dass Sie wissen, wie Sie die folgenden Aufgaben ausführen:

* [Replizieren eines virtuellen Computers in Azure](vmware-azure-tutorial.md)
* [Entwerfen eines Netzwerks für die Wiederherstellung](site-recovery-network-design.md)
* [Ausführen eines Testfailovers in Azure](site-recovery-test-failover-to-azure.md)
* [Ausführen eines Failovers in Azure](site-recovery-failover.md)
* [Replizieren eines Domänencontrollers](site-recovery-active-directory.md)
* [Replizieren von SQL Server](site-recovery-sql.md)

## <a name="deployment-patterns"></a>Bereitstellungsmuster
Für eine IIS-basierte Webanwendung wird in der Regel eines der folgenden Bereitstellungsmuster verwendet:

**Bereitstellungsmuster 1**

Eine IIS-basierte Webfarm mit Routing von Anwendungsanforderungen (Application Request Routing, ARR), einem IIS-Server und SQL Server

![Diagramm einer IIS-basierten Webfarm mit drei Ebenen](./media/site-recovery-iis/deployment-pattern1.png)

**Bereitstellungsmuster 2**

Eine IIS-basierte Webfarm mit ARR, einem IIS-Server, einem Anwendungsserver und SQL Server

![Diagramm einer IIS-basierten Webfarm mit vier Ebenen](./media/site-recovery-iis/deployment-pattern2.png)

## <a name="site-recovery-support"></a>Site Recovery-Unterstützung

Für die Beispiele in diesem Artikel wurden virtuelle VMware-Computer mit IIS 7.5 unter Windows Server 2012 R2 Enterprise verwendet. Da die Site Recovery-Replikation nicht anwendungsspezifisch ist, gelten die Empfehlungen in diesem Artikel für die in der folgenden Tabelle aufgeführten Szenarien und für verschiedene Versionen von IIS.

### <a name="source-and-target"></a>Quelle und Ziel

Szenario | Sekundärer Standort | Azure
--- | --- | ---
Hyper-V | Ja | Ja
VMware | Ja | Ja
Physischer Server | Nein | Ja
Azure|Nicht verfügbar|Ja

## <a name="replicate-virtual-machines"></a>Replizieren von virtuellen Computern

Um mit der Replikation aller virtuellen Computer einer IIS Webfarm in Azure zu beginnen, befolgen Sie die Anleitung unter [Testfailover nach Azure in Site Recovery](site-recovery-test-failover-to-azure.md).

Wenn Sie eine statische IP-Adresse verwenden, können Sie die IP-Adresse angeben, die Sie dem virtuellen Computer zuordnen möchten. Um die IP-Adresse festzulegen, wechseln Sie zu **Einstellungen für Compute und Netzwerk** > **ZIEL-IP**.

![Screenshot, der zeigt, wie die Ziel-IP-Adresse im Site Recovery-Bereich „Compute und Netzwerk“ festgelegt wird](./media/site-recovery-active-directory/dns-target-ip.png)

## <a name="create-a-recovery-plan"></a>Erstellen eines Wiederherstellungsplans
Ein Wiederherstellungsplan unterstützt die Sequenzierung der verschiedenen Ebenen in einer Anwendung mit mehreren Ebenen während eines Failovers. Die Sequenzierung trägt zur Wahrung der Anwendungskonsistenz bei. Beim Erstellen eines Wiederherstellungsplans für eine Webanwendung mit mehreren Ebenen führen Sie die unter [Erstellen eines Wiederherstellungsplans mit Site Recovery](site-recovery-create-recovery-plans.md) beschriebenen Schritte aus.

### <a name="add-virtual-machines-to-failover-groups"></a>Hinzufügen von virtuellen Computern zu Failovergruppen
Eine typische IIS-Webanwendung mit mehreren Ebenen besteht aus den folgenden Komponenten:
* Eine Datenbankebene mit virtuellen SQL-Computern
* Die Webebene mit einem IIS-Server und einer Anwendungsebene 

Hinzufügen von virtuellen Computern zu den einzelnen Gruppen basierend auf der Ebene:

1. Erstellen Sie einen Wiederherstellungsplan. Fügen Sie die virtuellen Computer der Datenbankebene unter Gruppe 1 hinzu. Dadurch wird sichergestellt, dass die virtuellen Computer der Datenbankebene zuletzt heruntergefahren und zuerst hochgefahren werden.
1. Fügen Sie die virtuellen Computer der Anwendungsebene unter Gruppe 2 hinzu. Damit stellen Sie sicher, dass die virtuellen Computer der Anwendungsebene hochgefahren werden, nachdem die Datenbankebene hochgefahren wurde.
1. Fügen Sie die virtuellen Computer der Webebene unter Gruppe 3 hinzu. Damit stellen Sie sicher, dass die virtuellen Computer der Webebene hochgefahren werden, nachdem die Anwendungsebene hochgefahren wurde.
1. Fügen Sie die virtuellen Computer für den Lastenausgleich unter Gruppe 4 hinzu. Damit stellen Sie sicher, dass die virtuellen Computer für den Lastenausgleich hochgefahren werden, nachdem die Webebene hochgefahren wurde.

Weitere Informationen finden Sie unter [Anpassen des Wiederherstellungsplans](site-recovery-runbook-automation.md#customize-the-recovery-plan).


### <a name="add-a-script-to-the-recovery-plan"></a>Hinzufügen eines Skripts zum Wiederherstellungsplan
Für die korrekte Funktionsweise der IIS-Webfarm kann es erforderlich sein, nach dem Failover bzw. während eines Testfailovers einige Vorgänge auf den virtuellen Azure-Computern durchzuführen. Sie können einige Vorgänge nach einem Failover automatisieren. Durch Hinzufügen entsprechender Skripts zum Wiederherstellungsplan können Sie beispielsweise den DNS-Eintrag aktualisieren sowie Bindungen und Verbindungszeichenfolgen ändern. Unter [Hinzufügen eines VMM-Skripts zu einem Wiederherstellungsplan](site-recovery-how-to-add-vmmscript.md) wird beschrieben, wie Sie automatisierte Aufgaben mithilfe eines Skripts einrichten.

#### <a name="dns-update"></a>DNS-Update
Wenn das DNS für das dynamische DNS-Update konfiguriert ist, führen virtuelle Computer nach dem Starten normalerweise ein Update des DNS mit der neuen IP-Adresse durch. Falls Sie einen expliziten Schritt für das Update des DNS mit den neuen IP-Adressen der virtuellen Computer hinzufügen möchten, fügen Sie ein [Skript zum Aktualisieren der IP-Adresse im DNS](https://aka.ms/asr-dns-update) als Aktion nach dem Failover den Wiederherstellungsplangruppen hinzu.  

#### <a name="connection-string-in-an-applications-webconfig"></a>Verbindungszeichenfolge in der „web.config“ einer Anwendung
Die Verbindungszeichenfolge gibt die Datenbank an, mit der die Website kommuniziert. Wenn die Verbindungszeichenfolge den Namen des virtuellen Datenbankcomputers hat, sind nach dem Failover keine weiteren Schritte erforderlich. Die Anwendung kann automatisch mit der Datenbank kommunizieren. Wenn die IP-Adresse für den virtuellen Datenbankcomputer beibehalten wird, wird sie für die Aktualisierung der Verbindungszeichenfolge nicht benötigt. 

Falls die Verbindungszeichenfolge auf den virtuellen Datenbankcomputer mit einer IP-Adresse verweist, muss sie nach dem Failover aktualisiert werden. Die unten angegebene Verbindungszeichenfolge verweist beispielsweise auf die Datenbank mit der IP-Adresse 127.0.1.2.

        <?xml version="1.0" encoding="utf-8"?>
        <configuration>
        <connectionStrings>
        <add name="ConnStringDb1" connectionString="Data Source= 127.0.1.2\SqlExpress; Initial Catalog=TestDB1;Integrated Security=False;" />
        </connectionStrings>
        </configuration>

Zum Aktualisieren der Verbindungszeichenfolge auf der Webebene fügen Sie das [Skript für das IIS-Verbindungsupdate](https://gallery.technet.microsoft.com/Update-IIS-connection-2579aadc) nach Gruppe 3 im Wiederherstellungsplan hinzu.

#### <a name="site-bindings-for-the-application"></a>Websitebindungen für die Anwendung
Jede Website besteht aus Bindungsinformationen. Die Bindungsinformationen enthalten den Typ der Bindung, die IP-Adresse, unter der der IIS-Server auf Anforderungen für die Website lauscht, die Portnummer und den Hostnamen für die Website. Bei einem Failover müssen diese Bindungen unter Umständen aktualisiert werden, wenn sich die zugeordneten IP-Adressen ändern.

> [!NOTE]
>
> Wenn Sie die Websitebindungen auf **Alle nicht zugewiesen** festlegen, müssen Sie diese Bindung nach einem Failover nicht aktualisieren. Wenn die IP-Adresse, die einer Website zugeordnet ist, nach dem Failover nicht geändert wird, müssen Sie auch die Websitebindung nicht aktualisieren. (Die Beibehaltung der IP-Adresse hängt von der Netzwerkarchitektur und den Subnetzen ab, die den primären und den Wiederherstellungswebsites zugewiesen sind. Eine Aktualisierung ist für Ihre Organisation möglicherweise nicht möglich.)

![Screenshot des Festlegens der SSL-Bindung](./media/site-recovery-iis/sslbinding.png)

Wenn Sie die IP-Adresse einer Website zugeordnet haben, müssen Sie alle Websitebindungen mit der neuen IP-Adresse aktualisieren. Zum Ändern der Websitebindungen fügen Sie ein [Skript zum Aktualisieren der IIS-Webebene](https://aka.ms/asr-web-tier-update-runbook-classic) nach Gruppe 3 im Wiederherstellungsplan hinzu.

#### <a name="update-the-load-balancer-ip-address"></a>Aktualisieren der IP-Adresse des Lastenausgleichs
Wenn Sie über einen virtuellen ARR-Computer (Application Request Routing, Routing von Anwendungsanforderungen) verfügen, fügen Sie ein [IIS-ARR-Failoverskript](https://aka.ms/asr-iis-arrtier-failover-script-classic) nach Gruppe 4 hinzu.

#### <a name="ssl-certificate-binding-for-an-https-connection"></a>SSL-Zertifikatbindung für eine HTTPS-Verbindung
Eine Website kann über ein zugeordnetes SSL-Zertifikat verfügen, mit dem sichergestellt werden kann, dass zwischen dem Webserver und dem Browser des Benutzers eine sichere Kommunikation erfolgt. Falls für die Website eine HTTPS-Verbindung und eine zugeordnete HTTPS-Websitebindung mit der IP-Adresse des IIS-Servers mit einer SSL-Zertifikatbindung verwendet werden, muss für das Zertifikat mit der IP-Adresse des virtuellen IIS-Computers nach dem Failover eine neue Websitebindung hinzugefügt werden.

Das SSL-Zertifikat kann für diese Komponenten ausgegeben werden:

* Vollqualifizierter Domänenname der Website
* Name des Servers
* Platzhalterzertifikat für den Domänennamen  
* Eine IP-Adresse Wenn das SSL-Zertifikat für die IP-Adresse des IIS-Servers ausgestellt wird, muss ein weiteres SSL-Zertifikat für die IP-Adresse des IIS-Servers am Azure-Standort ausgestellt werden. Es muss eine zusätzliche SSL-Bindung für dieses Zertifikat erstellt werden. Aus diesem Grund wird empfohlen, kein SSL-Zertifikat zu verwenden, das für die IP-Adresse ausgestellt wurde. Diese Option wird weniger häufig verwendet und bald nicht mehr unterstützt, um den neuen Änderungen für Zertifizierungsstellen/Browserforen zu entsprechen.

#### <a name="update-the-dependency-between-the-web-tier-and-the-application-tier"></a>Aktualisieren der Abhängigkeit zwischen Web- und Anwendungsebene
Wenn Sie über eine anwendungsspezifische Abhängigkeit verfügen, die auf der IP-Adresse der virtuellen Computer basiert, müssen Sie diese Abhängigkeit nach dem Failover aktualisieren.

## <a name="run-a-test-failover"></a>Ausführen eines Testfailovers

1. Wählen Sie im Azure-Portal Ihren Recovery Services-Tresor aus.
2. Wählen Sie den Wiederherstellungsplan aus, den Sie für die IIS-Webfarm erstellt haben.
3. Wählen Sie **Testfailover** aus.
4. Wählen Sie den Wiederherstellungspunkt und das virtuelle Azure-Netzwerk aus, um den Prozess für das Testfailover zu starten.
5. Wenn die sekundäre Umgebung aktiv ist, können Sie Ihre Überprüfungen durchführen.
6. Klicken Sie nach Abschluss der Überprüfungen auf **Überprüfungen abgeschlossen**, um die Testfailoverumgebung zu bereinigen.

Weitere Informationen finden Sie unter [Testfailover nach Azure in Site Recovery](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Ausführen eines Failovers

1. Wählen Sie im Azure-Portal Ihren Recovery Services-Tresor aus.
1. Wählen Sie den Wiederherstellungsplan aus, den Sie für die IIS-Webfarm erstellt haben.
1. Wählen Sie **Failover** aus.
1. Wählen Sie den Wiederherstellungspunkt aus, um den Failoverprozess zu starten.

Weitere Informationen finden Sie unter [Failover in Site Recovery](site-recovery-failover.md).

## <a name="next-steps"></a>Nächste Schritte
* Erfahren Sie mehr über das [Replizieren anderer Anwendungen](site-recovery-workload.md) mit Site Recovery.
