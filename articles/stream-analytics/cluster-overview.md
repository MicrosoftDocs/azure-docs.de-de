---
title: Übersicht über Azure Stream Analytics-Cluster (Vorschau)
description: In diesem Artikel erfahren Sie mehr über das dedizierte Angebot für Stream Analytics-Cluster mit einem einzelnen Mandanten.
author: sidramadoss
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc
ms.date: 09/22/2020
ms.openlocfilehash: f67df45b6b663584e848983251031146cdf8df60
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/22/2020
ms.locfileid: "95247692"
---
# <a name="overview-of-azure-stream-analytics-cluster-preview"></a>Übersicht über Azure Stream Analytics-Cluster (Vorschau)

Azure Stream Analytics-Cluster bieten eine Bereitstellung mit einem einzelnen Mandanten für komplexe und anspruchsvolle Streamingszenarios. Im vollen Umfang können Stream Analytics-Cluster mehr als 200 MB pro Sekunde in Echtzeit verarbeiten. Stream Analytics-Aufträge, die auf dedizierten Clustern ausgeführt werden, können alle Features des Tarifs „Standard“ nutzen und umfassen Unterstützung für Private Link-Konnektivität mit Ihren Eingaben und Ausgaben.

Stream Analytics-Cluster werden nach Streamingeinheiten berechnet, die die Menge an CPU- und Arbeitsspeicherressourcen darstellen, die Ihrem Cluster zugeordnet sind. Eine Streamingeinheit ist in den Tarifen „Standard“ und „Dedicated“ (dediziert) identisch. Sie können für jeden Cluster 36, 72, 108, 144, 180 oder 216 Streamingeinheiten erwerben. Ein Stream Analytics-Cluster kann als Streamingplattform für Ihre Organisation fungieren und von verschiedenen Teams gemeinsam genutzt werden, die an verschiedenen Anwendungsfällen arbeiten.

## <a name="what-are-stream-analytics-clusters"></a>Was sind Stream Analytics-Cluster?

Stream Analytics-Cluster werden von derselben Engine betrieben, die Stream Analytics-Aufträge betreibt, die in einer Umgebung mit mehreren Mandanten ausgeführt werden. Der dedizierte Cluster mit einem einzelnen Mandant verfügt über die folgenden Features:

* Hosting eines einzelnen Mandanten ohne Störungen durch andere Mandanten Ihre Ressourcen sind tatsächlich „isoliert“ und erzielen eine bessere Leistung bei Datenverkehrsspitzen.

* Skalieren Sie Ihre Cluster zwischen 36 bis 216 Streamingeinheiten, wenn Ihre Streamingnutzung mit dem Lauf der Zeit steigt.

* Die VNET-Unterstützung ermöglicht sichere Verbindungen zwischen Ihren Stream Analytics-Aufträgen mit anderen Ressourcen mithilfe privater Endpunkte.

* Schreiben von benutzerdefinierten C#-Funktionen und benutzerdefinierten Deserialisierern in beliebigen Regionen

* Es entstehen keine Wartungskosten, sodass Sie sich auf das Erstellen von Echtzeitanalyselösungen konzentrieren können.

## <a name="how-to-get-started"></a>Erste Schritte

Über das [Azure-Portal](https://aka.ms/asaclustercreateportal) können Sie [einen Stream Analytics-Cluster erstellen](create-cluster.md). Wenn Sie Fragen haben oder Hilfe beim Onboarding benötigen, können Sie das [Stream Analytics-Team](mailto:askasa@microsoft.com) kontaktieren.

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

### <a name="how-do-i-choose-between-a-stream-analytics-cluster-and-a-stream-analytics-job"></a>Wie wähle ich zwischen einem Stream Analytics-Cluster und einem Stream Analytics-Auftrag aus?

Die einfachste Einstiegsmöglichkeit besteht darin, einen Stream Analytics-Auftrag zu erstellen und zu entwickeln, um sich mit dem Dienst vertraut zu machen und zu erfahren, wie er Ihre Analyseanforderungen erfüllen kann.

Stream Analytics-Aufträge unterstützen VNETs nicht eigenständig. Wenn Ihre Eingaben oder Ausgaben durch eine Firewall oder ein virtuelles Azure-Netzwerk geschützt werden, stehen Ihnen die folgenden zwei Optionen zur Auswahl:

* Wenn Ihr lokaler Computer über Zugriff auf die Eingabe- und Ausgaberessourcen verfügt, die durch ein VNET geschützt werden (z. B. Azure Event Hubs oder Azure SQL-Datenbank), können Sie [Azure Stream Analytics-Tools für Visual Studio auf Ihrem lokalen Computer installieren](stream-analytics-tools-for-visual-studio-install.md). Auf Ihrem Gerät können Sie [Stream Analytics-Aufträge lokal testen](stream-analytics-live-data-local-testing.md) und entwickeln, ohne dass Kosten entstehen. Sobald Sie bereit sind, Stream Analytics in Ihrer Architektur zu verwenden, können Sie einen Stream Analytics-Cluster erstellen, private Endpunkte konfigurieren und Ihre Aufträge nach Maß ausführen.

* Sie können einen Stream Analytics-Cluster erstellen, den Cluster mit den erforderlichen privaten Endpunkten für Ihre Pipeline konfigurieren und Ihre Stream Analytics-Aufträge auf dem Cluster ausführen.

### <a name="what-performance-can-i-expect"></a>Welche Leistung kann ich erwarten?

Eine Streamingeinheit ist in den Tarifen „Standard“ und „Dedicated“ (dediziert) identisch. Ein einzelner Auftrag, der einen vollständigen Cluster mit 36 Streamingeinheiten nutzt, kann einen Durchsatz von 36 MB pro Sekunde mit Latenzen im Millisekundenbereich erzielen. Die genauen Zahlen hängen von dem Format der Ereignisse und der Art von Analyse ab. Da es sich um einen dedizierten Stream Analytics-Cluster handelt, bietet dieser zuverlässigere Leistungsgarantien. Alle auf Ihrem Cluster ausgeführten Aufträge gehören nur Ihnen.

### <a name="can-i-scale-my-cluster"></a>Kann ich meinen Cluster skalieren?

Ja. Sie können die Kapazität Ihres Clusters problemlos konfigurieren, wodurch Sie nach Bedarf [hoch- oder herunterskalieren](scale-cluster.md) können, um den sich ändernden Bedarf zu erfüllen.

### <a name="can-i-run-my-existing-jobs-on-these-new-clusters-ive-created"></a>Kann ich meine vorhandenen Aufträge auf neu erstellten Clustern ausführen?

Ja. Sie können Ihre vorhandenen Aufträge mit Ihrem neu erstellten Stream Analytics-Cluster verknüpfen und wie gewohnt ausführen. Sie müssen Ihre vorhandenen Stream Analytics-Aufträge nicht von Grund auf neu erstellen.

### <a name="how-much-will-these-clusters-cost-me"></a>Wie viel kosten diese Cluster?

Ihre Stream Analytics-Cluster werden basierend auf der ausgewählten Kapazität an Streamingeinheiten in Rechnung gestellt. Cluster werden stündlich abgerechnet, und es fallen keine zusätzlichen Gebühren pro Auftrag an, die auf diesen Clustern ausgeführt werden. Updates zu den Gebühren für private Endpunkte finden Sie auf der [Seite mit den Preisen für den Private Link-Dienst](https://azure.microsoft.com/pricing/details/private-link/).

### <a name="which-inputs-and-outputs-can-i-privately-connect-to-from-my-stream-analytics-cluster"></a>Mit welchen Ein- und Ausgaben kann ich über meinen Stream Analytics-Cluster eine private Verbindung herstellen?

Stream Analytics unterstützt verschiedene Eingabe- und Ausgabetypen. In Ihrem Cluster können Sie [private Endpunkte erstellen](private-endpoints.md), die Aufträgen den Zugriff auf Eingabe- und Ausgaberessourcen ermöglichen. Derzeit werden Azure SQL-Datenbank, Azure Storage, Azure Data Lake Storage Gen2, Azure Event Hub und Azure Service Bus unterstützt, und weitere Typen werden in Kürze hinzugefügt. 

## <a name="next-steps"></a>Nächste Schritte

Sie haben einen Überblick über Azure Stream Analytics-Cluster erhalten. Als Nächstes können Sie Ihren Cluster erstellen und Ihren Stream Analytics-Auftrag ausführen: 

* [Erstellen eines Stream Analytics-Clusters](create-cluster.md)
* [Verwalten privater Endpunkte in einem Azure Stream Analytics-Cluster](private-endpoints.md)
* [Verwalten von Stream Analytics-Aufträgen in einem Stream Analytics-Cluster](manage-jobs-cluster.md)
* [Erstellen eines Stream Analytics-Auftrags](stream-analytics-quick-create-portal.md)
