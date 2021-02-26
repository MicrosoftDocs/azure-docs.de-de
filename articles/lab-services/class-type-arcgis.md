---
title: Einrichten eines Labs für ArcMap\ArcGIS Desktop mit Azure Lab Services | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie ein Lab für Klassen mit ArcGIS einrichten.
author: nicolela
ms.topic: article
ms.date: 02/04/2021
ms.author: nicolela
ms.openlocfilehash: 8d5356f7fd2661d9743d1058a147a0b20f62850e
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100373707"
---
# <a name="set-up-a-lab-for-arcmaparcgis-desktop"></a>Einrichten eines Labs für ArcMap\ArcGIS Desktop

[ArcGIS](https://www.esri.com/en-us/arcgis/products/arcgis-solutions/overview) ist eine Art von geografischem Informationssystem (GIS).  ArcGIS wird verwendet, um Karten zu erstellen bzw. zu analysieren und mit geografischen Daten zu arbeiten, die vom [Environmental Systems Research Institute](https://www.esri.com/en-us/home) (ESRI) bereitgestellt werden.  Obwohl ArcGIS Desktop mehrere Anwendungen umfasst, wird in diesem Artikel gezeigt, wie Sie Labs zur Verwendung von ArcMap einrichten.  [ArcMap](https://desktop.arcgis.com/en/arcmap/latest/map/main/what-is-arcmap-.htm) wird zum Erstellen, Bearbeiten und Analysieren von 2D-Karten verwendet.

## <a name="lab-configuration"></a>Labkonfiguration

Zum Einrichten eines Labs mit ArcMap benötigen Sie zunächst ein Azure-Abonnement und ein Lab-Konto.  Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

Sobald Sie ein Azure-Abonnement haben, können Sie ein neues Lab-Konto in Azure Lab Services erstellen.  Weitere Informationen zum Erstellen eines neuen Lab-Kontos finden Sie unter [Einrichten eines Lab-Kontos](tutorial-setup-lab-account.md).  Sie können auch ein vorhandenes Lab-Konto verwenden.

### <a name="lab-account-settings"></a>Lab-Kontoeinstellungen

Aktivieren Sie die Einstellungen Ihres Lab-Kontos wie in der folgenden Tabelle beschrieben.  Weitere Informationen zum Aktivieren von Azure Marketplace-Images finden Sie unter [Angeben der für Lab-Ersteller verfügbaren Azure Marketplace-Images](https://docs.microsoft.com/azure/lab-services/specify-marketplace-images).

| Lab-Kontoeinstellungen | Instructions |
| ------------------- | ------------ |
|Marketplace-Image| Aktivieren Sie das Windows 10 Pro- oder Windows 10 Pro N-Image zur Verwendung in Ihrem Lab-Konto.|

### <a name="licensing-server"></a>Lizenzierungsserver

Eine Art der Lizenzierung, die ArcGIS Desktop bietet, sind [Lizenzen zur gleichzeitigen Verwendung](https://desktop.arcgis.com/en/license-manager/latest/license-manager-basics.htm).  Dies setzt voraus, dass Sie ArcGIS License Manager auf Ihrem Lizenzserver installieren.  License Manager überwacht die Anzahl der Kopien der Software, die gleichzeitig ausgeführt werden können.  Weitere Informationen zum Einrichten von License Manager auf Ihrem Server finden Sie im [Leitfaden zu License Manager](https://desktop.arcgis.com/en/license-manager/latest/welcome.htm).

Der Lizenzserver wird in der Regel in Ihrem lokalen Netzwerk platziert oder auf einem virtuellen Azure-Computer in einem virtuellen Azure-Netzwerk gehostet.  Nach dem Einrichten des Lizenzservers müssen Sie ein [Peering des virtuellen Netzwerks](https://docs.microsoft.com/azure/lab-services/how-to-connect-peer-virtual-network) mit Ihrem [Lab-Konto](https://docs.microsoft.com/azure/lab-services/tutorial-setup-lab-account) erstellen.  Das Netzwerkpeering muss vor dem Erstellen des Labs durchgeführt werden, damit Ihre Lab-VMs auf den Lizenzserver zugreifen können und umgekehrt.

Weitere Informationen finden Sie unter [Einrichten eines Lizenzservers als freigegebene Ressource](how-to-create-a-lab-with-shared-resource.md).

### <a name="lab-settings"></a>Lab-Einstellungen

Die Größe des virtuellen Computers (VM), den wir für ArcGIS Desktop empfehlen, hängt von den Anwendungen, Erweiterungen und den spezifischen Versionen ab, die die Kursteilnehmer verwenden werden.  Die VM-Größe hängt auch von den Workloads ab, die die Kursteilnehmer ausführen sollen.  Informationen zur Identifizierung der VM-Größe finden Sie unter den [Systemanforderungen für ArcGIS Desktop](https://desktop.arcgis.com/en/system-requirements/latest/arcgis-desktop-system-requirements.htm).  Nachdem Sie die potenzielle VM-Größe ermittelt haben, empfehlen wir Ihnen, die Workloads Ihrer Kursteilnehmer zu testen, um eine angemessene Leistung sicherzustellen.

In diesem Artikel wird empfohlen, die [VM-Größe **Mittel**](administrator-guide.md#vm-sizing) für Version [10.7.1 von ArcMap](https://desktop.arcgis.com/en/system-requirements/10.7/arcgis-desktop-system-requirements.htm) zu verwenden, vorausgesetzt, dass keine anderen ArcGIS Desktop-Erweiterungen verwendet werden.  Je nach den Anforderungen Ihrer Klasse benötigen Sie jedoch möglicherweise die VM-Größe **Groß** oder sogar **Kleine\Mittlere GPU (Visualisierung)** .  Beispielsweise unterstützt die [Spatial Analyst-Erweiterung](https://desktop.arcgis.com/en/arcmap/latest/tools/spatial-analyst-toolbox/gpu-processing-with-spatial-analyst.htm), die in ArcGIS Desktop enthalten ist, eine GPU für eine verbesserte Leistung, erfordert aber nicht die Verwendung einer GPU.

| Lab-Einrichtung | Wert und Beschreibung |
| ------------ | ------------------ |
|VM-Größe| **Mittel**  Am besten geeignet für relationale Datenbanken, speicherinternes Zwischenspeichern und Analysen.|  

### <a name="template-machine"></a>Vorlagencomputer

Die Schritte in diesem Abschnitt zeigen, wie Sie die Vorlage für virtuelle Computer einrichten:

1.  Starten Sie die Vorlage für virtuelle Computer, und stellen Sie über RDP eine Verbindung mit dem Computer her.

2.  Laden Sie die ArcGIS Desktop-Komponenten herunter, und installieren Sie sie mithilfe der Anweisungen des ESRI.  Diese Schritte umfassen die Zuweisung des Lizenz-Managers für die Lizenzierung der gleichzeitigen Verwendung: 
    - [Einführung in das Installieren und Konfigurieren von ArcGIS Desktop](https://desktop.arcgis.com/en/arcmap/latest/get-started/installation-guide/introduction.htm)

3.  Richten Sie einen externen Sicherungsspeicher für Kursteilnehmer ein.  Die Kursteilnehmer können Dateien direkt auf der ihnen zugewiesenen VM speichern, da alle Änderungen, die sie vornehmen, sitzungsübergreifend gespeichert werden.  Wir empfehlen jedoch, dass die Kursteilnehmer ihre Arbeit in einem Speicher sichern, der sich außerhalb ihrer VM befindet, und zwar aus mehreren Gründen:
    - Damit die Kursteilnehmer nach dem Ende der Klasse und des Labs auf ihre Arbeit zugreifen können.  
    - Für den Fall, dass die VM des Kursteilnehmers einen schlechten Zustand aufweist und das Image [zurückgesetzt](how-to-set-virtual-machine-passwords.md#reset-vms) werden muss.

    Mit ArcGIS sollte jeder Kursteilnehmer am Ende jeder Arbeitssitzung die folgenden Dateien sichern:

    - MXD-Datei, in der die Layoutinformationen für ein Projekt gespeichert werden.
    - Dateigeodatenbanken, in denen alle Daten gespeichert werden, die von ArcGIS erzeugt werden.
    - Alle anderen Daten, die der Kursteilnehmer möglicherweise verwendet, z. B. Rasterdateien, Shape-Dateien, GeoTIFF, usw.

    Wir empfehlen, OneDrive für den Sicherungsspeicher zu verwenden.  Um OneDrive auf der Vorlage für virtuelle Computer einzurichten, folgen Sie den Schritten im Artikel [Installieren und Konfigurieren von OneDrive](how-to-prepare-windows-template.md#install-and-configure-onedrive). 

4.  [Veröffentlichen](how-to-create-manage-template.md#publish-the-template-vm) Sie zum Schluss die Vorlage für virtuelle Computer, um die VM der Kursteilnehmer zu erstellen.

### <a name="auto-shutdown-and-disconnect-settings"></a>Einstellungen für automatisches Herunterfahren und Trennen

Die [Einstellungen für das automatische Herunterfahren und Trennen](cost-management-guide.md#automatic-shutdown-settings-for-cost-control) eines Labs helfen dabei, sicherzustellen, dass der virtuelle Computer eines Kursteilnehmers heruntergefahren wird, wenn er nicht verwendet wird.  Diese Einstellungen sollten entsprechend der Arten von Workloads festgelegt werden, die Ihre Kursteilnehmer ausführen werden, sodass ihr virtueller Computer nicht während der Arbeit heruntergefahren wird.  Die Einstellung **Benutzerverbindung bei Leerlauf der VMs trennen** trennt z. B. die Verbindung des Kursteilnehmers mit seiner RDP-Sitzung, nachdem für eine bestimmte Zeit keine Maus- oder Tastatureingaben erkannt wurden.  Diese Einstellung muss ausreichend Zeit für Workloads zulassen, bei denen der Kursteilnehmer nicht aktiv die Maus oder Tastatur verwendet, z. B. um lange Abfragen auszuführen oder auf das Rendering zu warten.

Für ArcGIS empfehlen wir die folgenden Werte für diese Einstellungen:
- Benutzerverbindung bei Leerlauf der VMs trennen
    - 30 Minuten nach Feststellung des Leerlaufzustands
- VMs bei Trennen der Benutzerverbindung herunterfahren
    - 15 Minuten nach Trennen der Verbindung durch den Benutzer

## <a name="cost"></a>Kosten

Betrachten wir eine mögliche Kostenschätzung für diesen Kurs. Diese Schätzung enthält nicht die Kosten für das Ausführen des Lizenzservers. Wir setzen einen Kurs mit 25 Kursteilnehmern an. Es ist eine Kursdauer von 20 Stunden geplant. Zudem erhält jeder Kursteilnehmer ein Kontingent von 10 Stunden für Hausaufgaben und Aufgaben außerhalb der regulären Kurszeiten. Als Größe des virtuellen Computers wurde **Mittel** ausgewählt, was 42 Lab-Einheiten entspricht.

25 Kursteilnehmer \* (20 geplante Stunden + 10 Kontingentstunden) \* 42 Lab-Einheiten * 0,01 USD pro Stunde = 315,00 USD

>[!IMPORTANT]
> Diese Kostenschätzung dient ausschließlich zu Beispielzwecken.  Weitere Informationen zu den aktuellen Preisen finden Sie unter [Azure Lab Services – Preise](https://azure.microsoft.com/pricing/details/lab-services/).  

## <a name="next-steps"></a>Nächste Schritte

Die nächsten Einrichtungsschritte sind für alle Labs gleich.

- [Erstellen und Verwalten einer Vorlage](how-to-create-manage-template.md)
- [Hinzufügen von Benutzern](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Kontingent festlegen](how-to-configure-student-usage.md#set-quotas-for-users)
- [Zeitplan festlegen](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Registrierungslinks per E-Mail an Kursteilnehmer senden](how-to-configure-student-usage.md#send-invitations-to-users)