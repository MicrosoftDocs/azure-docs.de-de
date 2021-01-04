---
title: Hochverfügbarkeit mit Datenverwaltungsgateway in Azure Data Factory
description: In diesem Artikel wird beschrieben, wie Sie ein Datenverwaltungsgateway aufskalieren können, indem Sie mehr Knoten hinzufügen. Außerdem erfahren Sie, wie Sie das Hochskalieren durchführen, indem Sie die Anzahl von gleichzeitigen Aufträgen erhöhen, die für einen Knoten ausgeführt werden können.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: anandsub
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: b8d05293359cff16bb6d8c9a629a1fbf68104365
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96003615"
---
# <a name="data-management-gateway---high-availability-and-scalability-preview"></a>Datenverwaltungsgateway – Hochverfügbarkeit und Skalierbarkeit (Vorschauversion)
> [!NOTE]
> Dieser Artikel gilt für Version 1 von Data Factory. Wenn Sie die aktuelle Version des Data Factory-Diensts verwenden, finden Sie weitere Informationen unter [Selbstgehostete Integration Runtime](../create-self-hosted-integration-runtime.md). 


Dieser Artikel enthält hilfreiche Informationen zum Konfigurieren einer Lösung für Hochverfügbarkeit und Skalierbarkeit mit dem Datenverwaltungsgateway/Integration.    

> [!NOTE]
> In diesem Artikel wird davon ausgegangen, dass Sie bereits mit den Grundlagen von Integration Runtime (ehemals Datenverwaltungsgateway) vertraut sind. Falls nicht, helfen Ihnen die Informationen unter [Datenverwaltungsgateway](data-factory-data-management-gateway.md) weiter.
> 
> **Dieses Vorschaufeature wird offiziell in Datenverwaltungsgateway Version 2.12.xxxx.x und höher unterstützt**. Stellen Sie sicher, dass Sie Version 2.12.xxxx.x oder höher verwenden. Laden Sie die aktuelle Version von Datenverwaltungsgateway [hier](https://www.microsoft.com/download/details.aspx?id=39717) herunter.

## <a name="overview"></a>Übersicht
Sie können Datenverwaltungsgateways, die auf mehreren lokalen Computern installiert sind, einem einzelnen logischen Gateway über das Portal zuordnen. Diese Computer werden als **Knoten** bezeichnet. Sie können bis zu **vier Knoten** verwenden, die einem logischen Gateway zugeordnet sind. Die Vorteile der Nutzung mehrerer Knoten (lokale Computer mit installiertem Gateway) für ein logisches Gateway sind:  

- Verbesserung der Leistung bei der Datenverschiebung zwischen lokalen und Clouddatenspeichern.  
- Wenn einer der Knoten aus irgendeinem Grund ausfällt, sind andere Knoten weiterhin zum Verschieben der Daten verfügbar. 
- Falls einer der Knoten zu Wartungszwecken in den Offlinezustand versetzt werden muss, sind andere Knoten weiterhin zum Verschieben der Daten verfügbar.

Sie können auch die Anzahl von **gleichzeitigen Datenverschiebungsaufträgen** konfigurieren, die auf einem Knoten ausgeführt werden können, um die Voraussetzungen zum Verschieben von Daten zwischen lokalen und Clouddatenspeichern zu verbessern. 

Mit dem Azure-Portal können Sie den Status dieser Knoten überwachen, um leichter entscheiden zu können, ob ein Knoten dem logischen Gateway hinzugefügt oder daraus entfernt werden soll. 

## <a name="architecture"></a>Aufbau 
Das folgende Diagramm enthält die Architekturübersicht zur Skalierbarkeits- und Verfügbarkeitsfunktion des Datenverwaltungsgateways: 

![Datenverwaltungsgateway – Hochverfügbarkeit und Skalierbarkeit](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-high-availability-and-scalability.png)

Ein **logisches Gateway** ist das Gateway, das Sie einer Data Factory im Azure-Portal hinzufügen. Bisher konnten Sie nur einen lokalen Windows-Computer mit installiertem Datenverwaltungsgateway einem logischen Gateway zuordnen. Dieser lokale Gatewaycomputer wird als Knoten bezeichnet. Jetzt können Sie einem logischen Gateway bis zu **vier physische Knoten** zuordnen. Ein logisches Gateway, das über mehrere Knoten verfügt, wird als **Gateway mit mehreren Knoten** bezeichnet.  

Hierbei sind alle Knoten **aktiv**. Alle Knoten können Datenverschiebungsaufträge verarbeiten, um Daten zwischen lokalen und Cloudatenspeichern zu verschieben. Einer der Knoten fungiert sowohl als Verteiler als auch als Worker. Die anderen Knoten in den Gruppen sind Workerknoten. Ein **Verteiler** knoten führt für Datenverschiebungsaufgaben/-aufträge den Pullvorgang aus dem Clouddienst durch und verteilt sie an Workerknoten (auch sich selbst). Ein **Worker** knoten führt Datenverschiebungsaufträge aus, um Daten zwischen lokalen und Clouddatenspeichern zu verschieben. Alle Knoten sind Worker. Nur ein Knoten kann sowohl Verteiler als auch Worker sein.    

Normalerweise beginnen Sie mit einem Knoten und führen dann das **Aufskalieren** durch, um weitere Knoten hinzuzufügen, wenn die Belastung der Datenverschiebung von dem bzw. den vorhandenen Knoten nicht mehr bewältigt werden kann. Sie können die Datenverschiebungsfunktion eines Gatewayknotens auch **hochskalieren**, indem Sie die Anzahl von gleichzeitigen Aufträgen erhöhen, die auf dem Knoten ausgeführt werden können. Diese Funktion ist auch für ein Gateway mit nur einem Knoten verfügbar (auch wenn die Skalierbarkeits- und Verfügbarkeitsfunktion nicht aktiviert ist). 

Ein Gateway mit mehreren Knoten erhält die Synchronisierung der Datenspeicher-Anmeldeinformationen für alle Knoten aufrecht. Falls ein Problem mit der Konnektivität von Knoten zu Knoten auftritt, sind die Anmeldeinformationen ggf. nicht mehr synchron. Wenn Sie Anmeldeinformationen für einen lokalen Datenspeicher festlegen, der ein Gateway nutzt, werden die Anmeldeinformationen auf dem Verteiler-/Workerknoten gespeichert. Der Verteilerknoten wird mit anderen Workerknoten synchronisiert. Dieser Prozess wird als **Synchronisierung von Anmeldeinformationen** bezeichnet. Der Kommunikationskanal zwischen Knoten kann mit einem öffentlichen SSL/TLS-Zertifikat **verschlüsselt** werden. 

## <a name="set-up-a-multi-node-gateway"></a>Einrichten eines Gateways mit mehreren Knoten
In diesem Abschnitt wird davon ausgegangen, dass Sie die beiden folgenden Artikel gelesen haben bzw. mit den Konzepten in diesen Artikeln vertraut sind: 

- [Datenverwaltungsgateway](data-factory-data-management-gateway.md): Liefert eine ausführliche Übersicht über das Gateway.
- [Verschieben von Daten zwischen lokalen Quellen und der Cloud mit dem Datenverwaltungsgateway](data-factory-move-data-between-onprem-and-cloud.md): Enthält eine exemplarische Vorgehensweise mit einer Schritt-für-Schritt-Anleitung für die Verwendung eines Gateways mit einem einzelnen Knoten.  

> [!NOTE]
> Bevor Sie ein Datenverwaltungsgateway auf einem lokalen Windows-Computer installieren, sollten Sie sich mit den Voraussetzungen im [Hauptartikel](data-factory-data-management-gateway.md#prerequisites) vertraut machen.

1. Aktivieren Sie in der [exemplarischen Vorgehensweise](data-factory-move-data-between-onprem-and-cloud.md#create-gateway) beim Erstellen eines logischen Gateways das Feature **Hochverfügbarkeit und Skalierbarkeit**. 

    ![Datenverwaltungsgateway – Aktivieren der Hochverfügbarkeit und Skalierbarkeit](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-enable-high-availability-scalability.png)
2. Verwenden Sie auf der Seite **Konfigurieren** entweder den Link **Express-Setup** oder **Manuelles Setup**, um auf dem ersten Knoten (lokaler Windows-Computer) ein Gateway zu installieren.

    ![Datenverwaltungsgateway – Express-Setup oder manuelles Setup](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-express-manual-setup.png)

    > [!NOTE]
    > Bei Verwendung der Option „Express-Setup“ wird die Kommunikation von Knoten zu Knoten ohne Verschlüsselung durchgeführt. Der Knotenname entspricht dem Computernamen. Verwenden Sie das manuelle Setup, wenn die Kommunikation von Knoten zu Knoten verschlüsselt werden muss oder wenn Sie einen Knotennamen Ihrer Wahl angeben möchten. Knotennamen können später nicht mehr geändert werden.
3. Bei Auswahl von **Express-Setup**:
    1. Nach der erfolgreichen Installation des Gateways wird die folgende Meldung angezeigt:

        ![Datenverwaltungsgateway – Express-Setup erfolgreich](media/data-factory-data-management-gateway-high-availability-scalability/express-setup-success.png)
    2. Starten Sie für das Gateway den Konfigurations-Manager für die Datenverwaltung, indem Sie [diese Anleitung](data-factory-data-management-gateway.md#configuration-manager) befolgen. Folgendes wird angezeigt: Gatewayname, Knotenname, Status usw.

        ![Screenshot: Benutzeroberfläche mit dem Gatewaynamen, dem Namen des Knoten und dem Status](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-installation-success.png)
4. Bei Auswahl von **Manuelles Setup**:
    1. Laden Sie das Installationspaket über das Microsoft Download Center herunter, und führen Sie es aus, um das Gateway auf Ihrem Computer zu installieren.
    2. Verwenden Sie den **Authentifizierungsschlüssel** von der Seite **Konfigurieren**, um das Gateway zu registrieren.
    
        ![Screenshot: Benutzeroberfläche, in der der Authentifizierungsschlüssel verwendet wird](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-authentication-key.png)
    3. Auf der Seite **Neuer Gatewayknoten** können Sie einen benutzerdefinierten **Namen** für den Gatewayknoten angeben. Standardmäßig entspricht der Knotenname dem Computernamen.    

        ![Datenverwaltungsgateway – Name angeben](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-name.png)
    4. Auf der nächsten Seite können Sie auswählen, **ob Sie die Verschlüsselung für die Kommunikation von Knoten zu Knoten aktivieren möchten**. Klicken Sie auf **Überspringen**, um die Verschlüsselung zu deaktivieren (Standardeinstellung).

        ![Datenverwaltungsgateway – Verschlüsselung aktivieren](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-node-encryption.png)  
    
        > [!NOTE]
        > Das Ändern des Verschlüsselungsmodus wird nur unterstützt, wenn Sie auf dem logischen Gateway über einen einzelnen Gatewayknoten verfügen. Führen Sie die folgenden Schritte aus, um den Verschlüsselungsmodus zu ändern, wenn ein Gateway mehrere Knoten hat: Löschen Sie alle Knoten bis auf einen, ändern Sie den Verschlüsselungsmodus, und fügen Sie die Knoten dann wieder hinzu.
        > 
        > Der Abschnitt [TLS/SSL-Zertifikatanforderungen](#tlsssl-certificate-requirements) enthält eine Liste mit Anforderungen zur Verwendung eines TLS/SSL-Zertifikats. 
    5. Klicken Sie auf „Konfigurations-Manager starten“, nachdem das Gateway erfolgreich installiert wurde:
    
        ![Manuelles Setup – Konfigurations-Manager starten](media/data-factory-data-management-gateway-high-availability-scalability/manual-setup-launch-configuration-manager.png)   
    6. Der Konfigurations-Manager für das Datenverwaltungsgateway auf dem Knoten (lokaler Windows-Computer) mit dem Verbindungsstatus, **Gatewaynamen** und **Knotennamen** wird angezeigt.  

        ![Datenverwaltungsgateway – Installation erfolgreich](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-installation-success.png)

        > [!NOTE]
        > Wenn Sie das Gateway auf einer Azure-VM bereitstellen, können Sie [diese Azure Resource Manager-Vorlage](https://github.com/Azure/azure-quickstart-templates/tree/master/101-mutiple-vms-with-data-management-gateway) verwenden. Mit diesem Skript wird ein logisches Gateway erstellt, und es werden VMs mit installierter Datenverwaltungsgateway-Software eingerichtet und für das logische Gateway registriert. 
6. Starten Sie im Azure-Portal die Seite **Gateway**: 
    1. Klicken Sie auf der Data Factory-Startseite im Portal auf **Verknüpfte Dienste**.
    
        ![Screenshot: Kachel „Verknüpfte Dienste“ hervorgehoben](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-home-page.png)
    2. Wählen Sie das **Gateway** aus, um die Seite **Gateway** anzuzeigen:
    
        ![Data Factory-Startseite](media/data-factory-data-management-gateway-high-availability-scalability/linked-services-gateway.png)
    4. Die Seite **Gateway** wird angezeigt:   

        ![Gateway mit Ansicht für einzelnen Knoten](media/data-factory-data-management-gateway-high-availability-scalability/gateway-first-node-portal-view.png) 
7. Klicken Sie in der Symbolleiste auf **Knoten hinzufügen**, um dem logischen Gateway einen Knoten hinzuzufügen. Wenn Sie die Verwendung des Express-Setups planen, sollten Sie diesen Schritt auf dem lokalen Computer ausführen, der dem Gateway als Knoten hinzugefügt werden soll. 

    ![Datenverwaltungsgateway – Menü „Knoten hinzufügen“](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-add-node-menu.png)
8. Die Schritte ähneln hierbei der Einrichtung des ersten Knotens. Auf der Benutzeroberfläche des Konfigurations-Managers können Sie den Knotennamen einrichten, wenn Sie die Option für die manuelle Installation wählen: 

    ![Konfigurations-Manager – Installation des zweiten Gateways](media/data-factory-data-management-gateway-high-availability-scalability/install-second-gateway.png)
9. Nachdem die Installation des Gateways auf dem Knoten erfolgreich abgeschlossen wurde, wird im Konfigurations-Manager-Tool Folgendes angezeigt:  

    ![Konfigurations-Manager – Installation des zweiten Gateways erfolgreich](media/data-factory-data-management-gateway-high-availability-scalability/second-gateway-installation-successful.png)
10. Wenn Sie die Seite **Gateway** im Portal öffnen, werden jetzt zwei Gatewayknoten angezeigt: 

    ![Gateway mit zwei Knoten im Portal](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring.png)
11. Klicken Sie zum Löschen eines Gatewayknotens in der Symbolleiste auf **Knoten löschen**, wählen Sie den zu löschenden Knoten aus, und klicken Sie dann in der Symbolleiste auf **Löschen**. Mit dieser Aktion wird der ausgewählte Knoten aus der Gruppe gelöscht. Beachten Sie Folgendes: Mit dieser Aktion wird die Datenverwaltungsgateway-Software nicht vom Knoten (lokaler Windows-Computer) deinstalliert. Verwenden Sie auf dem lokalen Computer in der Systemsteuerung die Option **Programme hinzufügen oder entfernen**, um das Gateway zu deinstallieren. Wenn Sie das Gateway vom Knoten deinstallieren, wird es im Portal automatisch gelöscht.   

## <a name="upgrade-an-existing-gateway"></a>Aktualisieren eines vorhandenen Gateways
Sie können ein vorhandenes Gateway aktualisieren, um das Feature für Hochverfügbarkeit und Skalierbarkeit zu verwenden. Dieses Feature funktioniert nur mit Knoten, die über das Datenverwaltungsgateway der Version >= 2.12.xxxx verfügen. Im Datenverwaltungsgateway-Konfigurations-Manager auf der Registerkarte **Hilfe** wird die Version des Datenverwaltungsgateways angezeigt, die auf einem Computer installiert ist. 

1. Aktualisieren Sie das Gateway auf dem lokalen Computer auf die aktuelle Version, indem Sie über das [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=39717) ein MSI-Setuppaket herunterladen und ausführen. Der Abschnitt [Installation](data-factory-data-management-gateway.md#installation) enthält entsprechende Details hierzu.  
2. Navigieren Sie zum Azure-Portal. Starten Sie die **Data Factory-Seite** für Ihre Data Factory. Klicken Sie auf die Kachel „Verknüpfte Dienste“, um die **Seite „Verknüpfte Dienste“** zu starten. Wählen Sie das Gateway, um die **Seite „Gateway“** zu starten. Klicken Sie wie hier gezeigt auf **Vorschaufeature**, und aktivieren Sie die Option: 

    ![Datenverwaltungsgateway – Vorschaufeature aktivieren](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-existing-gateway-enable-high-availability.png)   
2. Schließen Sie alle Seiten, nachdem Sie das Vorschaufeature im Portal aktiviert haben. Öffnen Sie die **Seite „Gateway“** erneut, um die neue Benutzeroberfläche der Vorschauversion anzuzeigen.
 
    ![Datenverwaltungsgateway – Vorschaufeature aktivieren – Erfolgreich](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-preview-success.png)

    ![Datenverwaltungsgateway – Benutzeroberfläche der Vorschauversion](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-preview.png)

    > [!NOTE]
    > Beim Upgradevorgang ist der Name des ersten Knotens der Name des Computers. 
3. Fügen Sie nun einen Knoten hinzu. Klicken Sie auf der Seite **Gateway** auf **Knoten hinzufügen**.  

    ![Datenverwaltungsgateway – Menü „Knoten hinzufügen“](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-add-node-menu.png)

    Befolgen Sie die Anleitung aus dem vorherigen Abschnitt, um den Knoten einzurichten. 

### <a name="installation-best-practices"></a>Bewährte Methoden für die Installation

- Konfigurieren Sie den Energiesparplan auf dem Hostcomputer für das Gateway, sodass der Computer nicht in den Ruhezustand wechselt. Wenn sich der Hostcomputer im Ruhezustand befindet, reagiert das Gateway nicht auf Datenanforderungen.
- Sichern Sie das Zertifikat, das dem Gateway zugeordnet ist.
- Stellen Sie sicher, dass alle Knoten eine ähnliche Konfiguration aufweisen (empfohlen), um die bestmögliche Leistung zu erzielen. 
- Fügen Sie mindestens zwei Knoten hinzu, um Hochverfügbarkeit sicherzustellen.  

### <a name="tlsssl-certificate-requirements"></a>TLS/SSL-Zertifikatanforderungen
Hier sind die Anforderungen für das TLS/SSL-Zertifikat angegeben, das zum Schützen der Kommunikation zwischen Integration Runtime-Knoten verwendet wird:

- Das Zertifikat muss ein öffentlich vertrauenswürdiges Zertifikat vom Typ „X509 v3“ sein. Wir empfehlen Ihnen die Verwendung von Zertifikaten, die von einer öffentlichen Zertifizierungsstelle (Drittanbieter) ausgestellt werden.
- Jeder Integration Runtime-Knoten muss diesem Zertifikat und dem Clientcomputer, der die Anwendung für die Anmeldeinformationsverwaltung ausführt, vertrauen. 
  > [!NOTE]
  > Die Anwendung für die Anmeldeinformationsverwaltung wird beim sicheren Festlegen von Anmeldeinformationen über den Kopier-Assistenten/das Azure-Portal verwendet. Dies kann auf jedem Computer im gleichen Netzwerk wie der lokale/private Datenspeicher ausgelöst werden.
- Platzhalterzertifikate werden unterstützt. Wenn der FQDN **node1.domain.contoso.com** lautet, können Sie *_.domain.contoso.com_* als Antragstellernamen des Zertifikats verwenden.
- SAN-Zertifikate sind nicht empfehlenswert, da nur das letzte Element des alternativen Antragstellernamens verwendet wird und alle anderen aufgrund von aktuellen Einschränkungen ignoriert werden. Beispiel: Wenn Sie ein SAN-Zertifikat mit den SANs **node1.domain.contoso.com** und **node2.domain.contoso.com** haben, können Sie dieses Zertifikat nur auf dem Computer mit dem FQDN **node2.domain.contoso.com** verwenden.
- Unterstützt alle Schlüsselgrößen, die von Windows Server 2012 R2 für TLS/SSL-Zertifikate unterstützt werden.
- Zertifikate mit CNG-Schlüsseln werden nicht unterstützt.

#### <a name="faq-when-would-i-not-enable-this-encryption"></a>Häufig gestellte Fragen: Wann sollte ich diese Verschlüsselung nicht aktivieren?
Das Aktivieren der Verschlüsselung kann einen gewissen Aufwand für Ihre Infrastruktur (die das öffentliche Zertifikat besitzt) bedeuten, daher sollten Sie in den folgenden Fällen auf das Aktivieren der Verschlüsselung verzichten:
- Wenn Integration Runtime in einem vertrauenswürdigen Netzwerk oder einem Netzwerk mit transparenter Verschlüsselung wie IP/SEC ausgeführt wird. Da diese Kanalkommunikation auf Ihr vertrauenswürdiges Netzwerk beschränkt ist, ist eventuell keine zusätzliche Verschlüsselung erforderlich.
- Wenn Integration Runtime nicht in einer Produktionsumgebung ausgeführt wird. Dies hilft bei der Reduzierung des Aufwands für TLS/SSL-Zertifikate.


## <a name="monitor-a-multi-node-gateway"></a>Überwachen eines Gateways mit mehreren Knoten
### <a name="multi-node-gateway-monitoring"></a>Überwachung eines Gateways mit mehreren Knoten
Im Azure-Portal können Sie auf jedem Knoten nahezu in Echtzeit Momentaufnahmen der Ressourcenverwendung (CPU, Arbeitsspeicher, Netzwerk (Eingang/Ausgang) usw.) sowie die Status der Gatewayknoten anzeigen. 

![Datenverwaltungsgateway – Überwachung mehrerer Knoten](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring.png)

Sie können auf der Seite **Gateway** die Option **Erweiterte Einstellungen** aktivieren, um beispielsweise die folgenden erweiterten Metriken anzuzeigen: **Netzwerk** (Eingang/Ausgang), **Status von Rollen und Anmeldeinformationen**, die beim Debuggen von Gatewayproblemen hilfreich ist, und **Gleichzeitige Aufträge** (ausgeführt/Limit), die bei der Leistungsoptimierung modifiziert bzw. geändert werden kann. Die folgende Tabelle enthält Beschreibungen von Spalten in der Liste **Gatewayknoten**:  

Überwachungseigenschaft | BESCHREIBUNG
:------------------ | :---------- 
Name | Name des logischen Gateways und der Knoten, die dem Gateway zugeordnet sind.  
Status | Status des logischen Gateways und der Gatewayknoten. Beispiel: Online/Offline/Eingeschränkt usw. Informationen zu diesen Status finden Sie im Abschnitt [Gatewaystatus](#gateway-status). 
Version | Zeigt die Version des logischen Gateways und jedes Gatewayknotens an. Die Version des logischen Gateways wird basierend auf der Version bestimmt, die die meisten Knoten der Gruppe aufweisen. Wenn Knoten mit unterschiedlichen Versionen am Setup des logischen Gateways beteiligt sind, funktionieren nur die Knoten mit der gleichen Versionsnummer wie beim logischen Gateway richtig. Andere Knoten befinden sich im eingeschränkten Modus und müssen manuell aktualisiert werden (nur für den Fall, dass die automatische Aktualisierung fehlschlägt). 
Verfügbarer Arbeitsspeicher | Verfügbarer Arbeitsspeicher auf einem Gatewayknoten. Dieser Wert steht für eine Momentaufnahme nahezu in Echtzeit. 
CPU-Auslastung | CPU-Auslastung eines Gatewayknotens. Dieser Wert steht für eine Momentaufnahme nahezu in Echtzeit. 
Netzwerk (Eingang/Ausgang) | Netzwerkauslastung eines Gatewayknotens. Dieser Wert steht für eine Momentaufnahme nahezu in Echtzeit. 
Gleichzeitige Aufträge (ausgeführt/Limit) | Anzahl von Aufträgen oder Aufgaben, die auf den einzelnen Knoten ausgeführt werden. Dieser Wert steht für eine Momentaufnahme nahezu in Echtzeit. Mit „Limit“ wird angegeben, wie viele Aufträge für einen Knoten jeweils gleichzeitig ausgeführt werden können. Dieser Wert wird basierend auf der Größe des Computers definiert. Sie können das Limit erhöhen, um die Ausführung von gleichzeitigen Aufträgen in erweiterten Szenarien hochzuskalieren, in denen CPU, Arbeitsspeicher und Netzwerk nicht voll ausgelastet sind, aber Zeitüberschreitungen für Aktivitäten auftreten. Diese Funktion ist auch für ein Gateway mit nur einem Knoten verfügbar (auch wenn die Skalierbarkeits- und Verfügbarkeitsfunktion nicht aktiviert ist). Weitere Informationen finden Sie im Abschnitt [Aspekte der Skalierung](#scale-considerations). 
Role | Es gibt zwei Arten von Rollen: „Verteiler“ und „Worker“. Alle Knoten sind Worker. Dies bedeutet, dass alle Knoten zum Ausführen von Aufträgen verwendet werden können. Es ist nur ein Verteilerknoten vorhanden, der zum Durchführen der Pullvorgänge für Aufgaben bzw. Aufträge von Clouddiensten und Verteilen an die einzelnen Workerknoten (einschließlich sich selbst) genutzt wird. 

![Datenverwaltungsgateway – Erweiterte Überwachung mehrerer Knoten](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring-advanced.png)

### <a name="gateway-status"></a>Gatewaystatus

Die folgende Tabelle enthält die möglichen Status eines **Gatewayknotens**: 

Status  | Kommentare/Szenarien
:------- | :------------------
Online | Knoten, der mit dem Data Factory-Dienst verbunden ist.
Offline | Knoten ist offline.
Wird aktualisiert | Der Knoten wird automatisch aktualisiert.
Eingeschränkt | Es besteht ein Konnektivitätsproblem. Dies kann ein Problem mit HTTP-Port 8050, mit der Service Bus-Konnektivität oder mit der Synchronisierung von Anmeldeinformationen sein. 
Inaktiv | Die Konfiguration des Knotens unterscheidet sich von der Konfiguration anderer Mehrheitsknoten.<br/><br/> Ein Knoten kann inaktiv sein, wenn er keine Verbindungen mit anderen Knoten herstellen kann. 


Die folgende Tabelle enthält die möglichen Status eines **logischen Gateways**. Der Gatewaystatus hängt von den Status der Gatewayknoten ab. 

Status | Kommentare
:----- | :-------
Registrierung erforderlich | Für dieses logische Gateway ist noch kein Knoten registriert.
Online | Gatewayknoten sind online.
Offline | Kein Knoten befindet sich im Onlinestatus.
Eingeschränkt | Nicht alle Knoten dieses Gateways befinden sich in einem fehlerfreien Zustand. Dieser Status ist eine Warnung, dass ein Knoten unter Umständen ausgefallen ist. <br/><br/>Die Ursache kann ein Problem mit der Synchronisierung von Anmeldeinformationen auf einem Verteiler- oder Workerknoten sein. 

### <a name="pipeline-activities-monitoring"></a>Pipeline-/Aktivitätsüberwachung
Das Azure-Portal enthält einen Bereich für die Pipelineüberwachung mit präzisen Knotenebenendetails. Es wird beispielsweise angezeigt, welche Aktivitäten auf welchem Knoten ausgeführt wurden. Diese Informationen können für das Verständnis von Leistungsproblemen, z.B. aufgrund der Netzwerkdrosselung, auf einem bestimmten Knoten hilfreich sein. 

![Datenverwaltungsgateway – Überwachung mehrerer Knoten für Pipelines](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring-pipelines.png)

![Datenverwaltungsgateway – Pipelinedetails](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-pipeline-details.png)

## <a name="scale-considerations"></a>Aspekte der Skalierung

### <a name="scale-out"></a>Aufskalieren
Wenn **nur wenig Arbeitsspeicher verfügbar** und die **CPU-Auslastung hoch ist**, ist das Hinzufügen eines neuen Knotens hilfreich, um die Last per Aufskalierung auf die Computer zu verteilen. Falls für Aktivitäten Fehler auftreten, weil es zu Zeitüberschreitungen kommt oder Gatewayknoten offline sind, ist es ratsam, dem Gateway einen Knoten hinzuzufügen.
 
### <a name="scale-up"></a>Hochskalieren
Wenn die Auslastung des verfügbaren Arbeitsspeichers und der CPU nicht hoch ist, aber die Kapazität im Leerlauf den Wert „0“ hat, sollten Sie hochskalieren. Erhöhen Sie hierfür die Anzahl von gleichzeitigen Aufträgen, die auf einem Knoten ausgeführt werden können. Es kann auch hilfreich sein, das Hochskalieren durchzuführen, wenn für Aktivitäten eine Zeitüberschreitung auftritt, weil das Gateway überlastet ist. Wie in der folgenden Abbildung gezeigt, können Sie die maximale Kapazität für einen Knoten erhöhen. Es wird empfohlen, den Wert erst einmal zu verdoppeln.  

![Datenverwaltungsgateway – Aspekte der Skalierung](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-scale-considerations.png)


## <a name="known-issuesbreaking-changes"></a>Bekannte Probleme/Wichtige Änderungen

- Derzeit können Sie bis zu vier physische Gatewayknoten für ein einzelnes logisches Gateway verwenden. Falls Sie aus Leistungsgründen mehr als vier Knoten benötigen, können Sie eine E-Mail an [DMGHelp@microsoft.com](mailto:DMGHelp@microsoft.com) senden.
- Es ist nicht möglich, einen Gatewayknoten mit dem Authentifizierungsschlüssel eines anderen logischen Gateways erneut zu registrieren, um einen Wechsel vom aktuellen logischen Gateway durchzuführen. Gehen Sie wie folgt vor, um die erneute Registrierung vorzunehmen: Deinstallieren Sie das Gateway vom Knoten, und installieren Sie es dann erneut. Registrieren Sie es anschließend mit dem Authentifizierungsschlüssel für das andere logische Gateway. 
- Wenn für alle Gatewayknoten ein HTTP-Proxy erforderlich ist, können Sie den Proxy in „diahost.exe.config“ und „diawp.exe.config“ festlegen. Verwenden Sie den Server-Manager, um sicherzustellen, dass für alle Knoten „diahost.exe.config“ und „diawip.exe.config“ jeweils gleich sind. Der Abschnitt [Konfigurieren von Proxyeinstellungen](data-factory-data-management-gateway.md#configure-proxy-server-settings) enthält Details hierzu. 
- Um den Verschlüsselungsmodus für die Kommunikation von Knoten zu Knoten im Gatewaykonfigurations-Manager zu ändern, löschen Sie im Portal alle Knoten bis auf einen. Fügen Sie die Knoten dann wieder hinzu, nachdem Sie den Verschlüsselungsmodus geändert haben.
- Verwenden Sie ein offizielles TLS-Zertifikat, wenn Sie sich dafür entscheiden, den Kanal für die Kommunikation von Knoten zu Knoten zu verschlüsseln. Ein selbstsigniertes Zertifikat kann zu Konnektivitätsproblemen führen, da dasselbe Zertifikat in der Zertifizierungsstellenliste auf anderen Computern ggf. nicht als vertrauenswürdig angesehen wird. 
- Sie können ein Gatewayknoten nicht auf einem logischen Gateway registrieren, wenn die Knotenversion niedriger als die Version des logischen Gateways ist. Löschen Sie alle Knoten des logischen Gateways aus dem Portal, damit Sie einen Knoten mit einer niedrigeren Version registrieren können (Downgrade). Wenn Sie alle Knoten eines logischen Gateways löschen, müssen Sie auf diesem logischen Gateway neue Knoten manuell installieren und registrieren. Das Express-Setup wird in diesem Fall nicht unterstützt.
- Sie können das Express-Setup nicht nutzen, um Knoten auf einem vorhandenen logischen Gateway zu installieren, für das noch Cloudanmeldeinformationen verwendet werden. Im Gatewaykonfigurations-Manager auf der Registerkarte „Einstellungen“ können Sie prüfen, wo die Anmeldeinformationen gespeichert sind.
- Es ist nicht möglich, das Express-Setup zum Installieren eines vorhandenen logischen Gateways zu verwenden, für das die Verschlüsselung von Knoten zu Knoten aktiviert ist. Da das Festlegen des Verschlüsselungsmodus das manuelle Hinzufügen von Zertifikaten umfasst, ist die Expressinstallation keine Option mehr. 
- Für einen Dateikopiervorgang aus der lokalen Umgebung sollten Sie „\\localhost“ oder „C:\files“ nicht mehr verwenden, da „localhost“ oder das lokale Laufwerk unter Umständen nicht über alle Knoten zugänglich ist. Verwenden Sie stattdessen „\\ServerName\files“, um den Speicherort der Dateien anzugeben.


## <a name="rolling-back-from-the-preview"></a>Rollback der Vorschauversion 
Löschen Sie alle Knoten außer einem, um für die Vorschauversion ein Rollback auszuführen. Es spielt keine Rolle, welche Knoten Sie löschen. Stellen Sie aber sicher, dass das logische Gateway mindestens einen Knoten enthält. Sie können einen Knoten löschen, indem Sie entweder das Gateway auf dem Computer deinstallieren oder das Azure-Portal verwenden. Klicken Sie im Azure-Portal auf der Seite **Data Factory** auf „Verknüpfte Dienste“, um die Seite **Verknüpfte Dienste** zu starten. Wählen Sie das Gateway, um die **Seite „Gateway“** zu starten. Auf der Seite „Gateway“ werden die Knoten angezeigt, die dem Gateway zugeordnet sind. Über die Seite können Sie einen Knoten vom Gateway löschen.
 
Klicken Sie nach dem Löschen auf derselben Seite des Azure-Portals auf **Vorschaufeatures**, und deaktivieren Sie das Vorschaufeature. Sie haben Ihr Gateway auf ein Gateway mit einem Knoten im Zustand „Allgemeine Verfügbarkeit“ (General Availability, GA) zurückgesetzt.


## <a name="next-steps"></a>Nächste Schritte
Lesen Sie die folgenden Artikel:
- [Datenverwaltungsgateway](data-factory-data-management-gateway.md): Liefert eine ausführliche Übersicht über das Gateway.
- [Verschieben von Daten zwischen lokalen Quellen und der Cloud mit dem Datenverwaltungsgateway](data-factory-move-data-between-onprem-and-cloud.md): Enthält eine exemplarische Vorgehensweise mit einer Schritt-für-Schritt-Anleitung für die Verwendung eines Gateways mit einem einzelnen Knoten. 
