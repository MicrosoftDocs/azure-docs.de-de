---
title: Grenzwerte und Konfiguration – Azure Logic Apps | Microsoft-Dokumentation
description: Dienstlimits und Konfigurationswerte für Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 05/23/2019
ms.openlocfilehash: e824ac81f1336644fa70cc24539284feacee3199
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244531"
---
# <a name="limits-and-configuration-information-for-azure-logic-apps"></a>Grenzwert- und Konfigurationsinformationen für Azure Logic Apps

In diesem Artikel sind die Einschränkungen und Konfigurationsdetails beschrieben, die beim Erstellen und Ausführen von automatisierten Workflows mit Azure Logic Apps zu beachten sind. Informationen zu Microsoft Flow finden Sie unter [Grenzwerte und Konfiguration in Microsoft Flow](https://docs.microsoft.com/flow/limits-and-config).

<a name="definition-limits"></a>

## <a name="definition-limits"></a>Definitionslimits

Dies sind die Grenzwerte für eine einzelne Logik-App-Definition:

| NAME | Begrenzung | Notizen |
| ---- | ----- | ----- |
| Aktionen pro Workflow | 500 | Zur Erhöhung dieses Grenzwerts können Sie bei Bedarf geschachtelte Workflows hinzufügen. |
| Zulässige Schachtelungstiefe für Aktionen | 8 | Zur Erhöhung dieses Grenzwerts können Sie bei Bedarf geschachtelte Workflows hinzufügen. |
| Workflows pro Region und Abonnement | 1.000 | |
| Trigger pro Workflow | 10 | Wenn in der Codeansicht gearbeitet wird, nicht im Designer |
| Bereichsumschaltbegrenzung | 25 | |
| Variablen pro Workflow | 250 | |
| Zeichen pro Ausdruck | 8.192 | |
| Maximale Größe für `trackedProperties` | 16.000 Zeichen |
| Name für `action` oder `trigger` | 80 Zeichen | |
| Länge von `description` | 256 Zeichen | |
| Größter Wert für `parameters` | 50 | |
| Größter Wert für `outputs` | 10 | |
||||

<a name="run-duration-retention-limits"></a>

## <a name="run-duration-and-retention-limits"></a>Grenzwerte für Ausführungsdauer und Aufbewahrung

Dies sind die Grenzwerte für eine einzelne Ausführung der Logik-App:

| NAME | Grenzwert bei mehreren Mandanten | Grenzwert für Integrationsdienstumgebung | Notizen |
|------|--------------------|---------------------------------------|-------|
| Ausführungsdauer | 90 Tage | 365 Tage | Informationen dazu, wie der Standardgrenzwert geändert wird, finden Sie unter [Ändern der Ausführungsdauer](#change-duration). |
| Aufbewahrungsdauer im Speicher | 90 Tage ab der Startzeit der Ausführung | 365 Tage | Informationen dazu, wie der Standardgrenzwert geändert wird, finden Sie unter [Ändern der Aufbewahrungsdauer im Speicher](#change-retention). |
| Kürzestes Wiederholungsintervall | 1 Sekunde | 1 Sekunde ||
| Längstes Wiederholungsintervall | 500 Tage | 500 Tage ||
|||||

<a name="change-duration"></a>
<a name="change-retention"></a>

### <a name="change-run-duration-and-storage-retention"></a>Ändern von Ausführungsdauer und Aufbewahrungsdauer im Speicher

Gehen Sie folgendermaßen vor, um den Standardgrenzwert für die Ausführungsdauer und die Aufbewahrungsdauer im Speicher zu ändern. Wenn Sie den maximalen Grenzwert überschreiten müssen, [wenden Sie sich an das Logic Apps-Team ](mailto://logicappsemail@microsoft.com), um Hilfestellung zu Ihren Anforderungen zu erhalten.

1. Wählen Sie im Azure-Portal im Menü Ihrer Logik-App den Befehl **Workfloweinstellungen** aus.

2. Wählen Sie unter **Laufzeitoptionen** in der Liste **Aufbewahrung des Ausführungsverlaufs in Tagen** die Option **Benutzerdefiniert** aus.

3. Geben Sie die gewünschte Anzahl von Tagen ein, oder ziehen Sie den Schieberegler bis zur gewünschten Anzahl.

<a name="looping-debatching-limits"></a>

## <a name="concurrency-looping-and-debatching-limits"></a>Grenzwerte für Parallelität, Schleifen und Auflösen von Batches

Dies sind die Grenzwerte für eine einzelne Ausführung der Logik-App:

| NAME | Begrenzung | Notizen |
| ---- | ----- | ----- |
| Triggerparallelität | * Unbegrenzt, wenn die Parallelitätssteuerung deaktiviert ist <p><p>* Wenn die Parallelitätssteuerung aktiviert ist, beträgt der standardmäßige Grenzwert 25. Der Wert kann nach der Aktivierung der Parallelitätssteuerung nicht mehr rückgängig gemacht werden. Der Standardwert kann in einen Wert von 1 bis 50 (einschließlich) geändert werden. | Dieser Grenzwert beschreibt die maximale Anzahl von Logik-App-Instanzen, die gleichzeitig bzw. parallel ausgeführt werden können. <p><p>Informationen zum Ändern des Standardlimits auf einen Wert zwischen 1 und 50 (einschließlich) finden Sie unter [Ändern des Triggerparallelitäts-Grenzwerts](../logic-apps/logic-apps-workflow-actions-triggers.md#change-trigger-concurrency) und [Sequenzielles Auslösen von Instanzen](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-trigger). |
| Maximale Anzahl von wartenden Ausführungen | Bei aktivierter Parallelitätssteuerung beträgt die Mindestanzahl wartender Ausführungen 10 zuzüglich der Anzahl paralleler Ausführungen (Triggerparallelität). Sie können die maximale Anzahl bis auf 100 (einschließlich) heraufsetzen. | Dieser Grenzwert beschreibt die maximale Anzahl von Logik-App-Instanzen, die auf die Ausführung warten können, wenn für Ihre Logik-App bereits die maximale Anzahl paralleler Instanzen ausgeführt wird. <p><p>Informationen zum Ändern des Standardlimits finden Sie unter [Ändern des Limits für wartende Ausführungen](../logic-apps/logic-apps-workflow-actions-triggers.md#change-waiting-runs). |
| Foreach-Arrayelemente | 100.000 | Dieser Grenzwert beschreibt die maximale Anzahl von Arrayelementen, die eine Foreach-Schleife verarbeiten kann. <p><p>Sie können die [Abfrageaktion](../connectors/connectors-native-query.md) verwenden, um größere Arrays zu filtern. |
| Foreach-Parallelität | Wenn die Parallelitätssteuerung deaktiviert ist, beträgt der standardmäßige Grenzwert 20. Der Standardwert kann in einen Wert von 1 bis 50 (einschließlich) geändert werden. | Dieser Grenzwert entspricht der maximalen Anzahl von Foreach-Schleifeniterationen, die gleichzeitig bzw. parallel ausgeführt werden können. <p><p>Informationen zum Ändern des Standardlimits auf einen Wert zwischen 1 und 50 (einschließlich) finden Sie unter [Ändern des Foreach-Parallelitätsgrenzwerts](../logic-apps/logic-apps-workflow-actions-triggers.md#change-for-each-concurrency) und [Sequenzielles Ausführen von Foreach-Schleifen](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-for-each). |
| SplitOn-Elemente | 100.000 | Für Trigger, die ein Array zurückgeben, können Sie einen Ausdruck angeben, der eine SplitOn-Eigenschaft verwendet, um [Arrayelemente für die Verarbeitung in mehrere Workflowinstanzen aufzuteilen bzw. aufzulösen](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch), anstatt eine Foreach-Schleife zu verwenden. Dieser Ausdruck verweist auf das Array, das zum Erstellen und Ausführen einer Workflowinstanz für jedes Arrayelement verwendet werden soll. |
| Until-Iterationen | 5.000 | |
||||

<a name="throughput-limits"></a>

## <a name="throughput-limits"></a>Durchsatzlimits

Dies sind die Grenzwerte für eine einzelne Ausführung der Logik-App:

### <a name="multi-tenant-logic-apps-service"></a>Logic Apps-Dienst mit mehreren Mandanten

| NAME | Begrenzung | Notizen |
| ---- | ----- | ----- |
| Aktion: Ausführungen pro 5 Minuten | 100.000 ist der Standardgrenzwert, aber 300.000 ist der maximale Grenzwert. | Weitere Informationen zum Ändern des Standardgrenzwerts finden Sie unter [Ausführen Ihre Logik-App im Modus „Hoher Durchsatz“](../logic-apps/logic-apps-workflow-actions-triggers.md#run-high-throughput-mode) (Vorschauversion). Sie können auch die Workload nach Bedarf auf mehrere Logik-Apps verteilen. |
| Aktion: Gleichzeitig ausgehende Aufrufe | ca. 2.500 | Sie können die Anzahl gleichzeitiger Anforderungen oder die Dauer nach Bedarf verringern. |
| Endpunkt zur Laufzeit: Gleichzeitig eingehende Aufrufe | ca. 1.000 | Sie können die Anzahl gleichzeitiger Anforderungen oder die Dauer nach Bedarf verringern. |
| Endpunkt zur Laufzeit: Leseaufrufe pro 5 Minuten  | 60.000 | Bei Bedarf können Sie eine Workload auch auf mehrere Apps verteilen. |
| Endpunkt zur Laufzeit: Aufrufe pro 5 Minuten | 45.000 | Bei Bedarf können Sie eine Workload auch auf mehrere Apps verteilen. |
| Inhaltsdurchsatz pro 5 Minuten | 600 MB | Bei Bedarf können Sie eine Workload auch auf mehrere Apps verteilen. |
||||

### <a name="integration-service-environment-ise"></a>Integrationsdienstumgebung (Integration Service Environment, ISE)

| NAME | Begrenzung | Notizen |
|------|-------|-------|
| Ausführungsgrenzwert für eine Basiseinheit | System gedrosselt, wenn die Infrastrukturkapazität 80 % erreicht | Bietet ca. 4.000 Aktionsausführungen pro Minute, was rund 160 Mio. Aktionsausführungen pro Monat entspricht. | |
| Ausführungsgrenzwert für eine Skalierungseinheit | System gedrosselt, wenn die Infrastrukturkapazität 80 % erreicht | Jede Skalierungseinheit kann ca. 2.000 zusätzliche Aktionsausführungen pro Minute bereitstellen, was etwa 80 Mio. zusätzlichen Aktionsausführungen pro Monat entspricht. | |
| Maximale Skalierungseinheiten, die Sie hinzufügen können | 10 | |
||||

Wenn diese Grenzwerte bei der normalen Verarbeitung überschritten oder Auslastungstests ausgeführt werden sollen, bei denen diese Grenzwerte möglicherweise überschritten werden, [bitten Sie das Logic Apps-Team](mailto://logicappsemail@microsoft.com) um Unterstützung im Hinblick auf Ihre Anforderungen.

<a name="request-limits"></a>

## <a name="http-limits"></a>HTTP-Grenzwerte

Diese Grenzwerte gelten für eine einzelne HTTP-Anforderung oder einen einzelnen synchronen Connectoraufruf:

#### <a name="timeout"></a>Timeout

Einige Connectorvorgänge führen asynchrone Aufrufe aus oder lauschen auf Webhookanforderungen, sodass das Timeout für diese Vorgänge länger sein kann, als diese Grenzwerte angeben. Weitere Informationen hierzu finden Sie in den technischen Details für den bestimmten Connector sowie unter [Workflowtrigger und -aktionen](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action).

| NAME | Grenzwert bei mehreren Mandanten | Grenzwert für Integrationsdienstumgebung | Notizen |
|------|--------------------|---------------------------------------|-------|
| Ausgehende Anforderung | 120 Sekunden | 240 Sekunden | Verwenden Sie für Vorgänge, die länger ausgeführt werden, ein [asynchrones Abrufmuster](../logic-apps/logic-apps-create-api-app.md#async-pattern) oder eine [Until-Schleife](../logic-apps/logic-apps-workflow-actions-triggers.md#until-action). |
| Synchrone Antwort | 120 Sekunden | 240 Sekunden | Damit die ursprüngliche Anforderung die Antwort erhält, müssen alle Schritte in der Antwort innerhalb des Grenzwerts abgeschlossen werden, es sei denn, Sie rufen eine andere Logik-App als geschachtelten Workflow auf. Weitere Informationen hierzu finden Sie unter [Aufrufen, Auslösen oder Schachteln von Logik-Apps](../logic-apps/logic-apps-http-endpoint.md). |
|||||

#### <a name="message-size"></a>Nachrichtengröße

| NAME | Grenzwert bei mehreren Mandanten | Grenzwert für Integrationsdienstumgebung | Notizen |
|------|--------------------|---------------------------------------|-------|
| Nachrichtengröße | 100 MB | 200 MB | Informationen, wie Sie diese Beschränkung umgehen können, finden Sie unter [Verarbeiten von großen Nachrichten durch Blockerstellung in Logic Apps](../logic-apps/logic-apps-handle-large-messages.md). Es kann aber sein, dass einige Connectors und APIs Blockerstellung (Segmentierung) oder sogar den Standardgrenzwert nicht unterstützen. |
| Nachrichtengröße mit Blockerstellung (Segmentierung) | 1 GB | 5 GB | Dieser Grenzwert gilt für Aktionen, die Blockerstellung automatisch unterstützen, oder für die Sie die Blockerstellung in der Laufzeitkonfiguration aktivieren können. <p>Für die Integrationsdienstumgebung unterstützt die Logic Apps-Engine diesen Grenzwert, Connectors verfügen jedoch über eigene Blockerstellungsgrenzwerte bis zum Grenzwert der Engine. Beachten Sie hierzu z. B. die Informationen unter [Azure Blob Storage-Connector](/connectors/azureblob/). Weitere Informationen zur Blockerstellung finden Sie unter [Verarbeiten von großen Nachrichten durch Blockerstellung](../logic-apps/logic-apps-handle-large-messages.md). |
| Grenzwert für die Auswertung von Ausdrücken | 131.072 Zeichen | 131.072 Zeichen | Keiner der Ausdrücke `@concat()`, `@base64()` und `@string()` darf länger sein, als dieser Grenzwert angibt. |
|||||

#### <a name="retry-policy"></a>Wiederholungsrichtlinie

| NAME | Begrenzung | Notizen |
| ---- | ----- | ----- |
| Wiederholungsversuche | 90 | Der Standardwert ist 4. Um den Standardwert zu ändern, verwenden Sie den [Parameter für Richtlinienwiederholung](../logic-apps/logic-apps-workflow-actions-triggers.md) (retryPolicy). |
| Maximale Wiederholungsverzögerung | 1 Tag | Um den Standardwert zu ändern, verwenden Sie den [Parameter für Richtlinienwiederholung](../logic-apps/logic-apps-workflow-actions-triggers.md) (retryPolicy). |
| Minimale Wiederholungsverzögerung | 5 Sekunden | Um den Standardwert zu ändern, verwenden Sie den [Parameter für Richtlinienwiederholung](../logic-apps/logic-apps-workflow-actions-triggers.md) (retryPolicy). |
||||

<a name="custom-connector-limits"></a>

## <a name="custom-connector-limits"></a>Grenzwerte für einen benutzerdefinierten Connector

Die folgenden Grenzwerte gelten für benutzerdefinierte Connectors, die Sie über Web-APIs erstellen können.

| NAME | Grenzwert bei mehreren Mandanten | Grenzwert für Integrationsdienstumgebung | Notizen |
|------|--------------------|---------------------------------------|-------|
| Anzahl von benutzerdefinierten Connectors | 1.000 pro Azure-Abonnement | 1.000 pro Azure-Abonnement ||
| Anzahl von Anforderungen pro Minute für einen benutzerdefinierten Connector | 500 Anforderungen pro Minute und Verbindung | 2.000 Anforderungen pro Minute und *benutzerdefiniertem Connector* ||
|||

<a name="managed-identity"></a>

## <a name="managed-identities"></a>Verwaltete Identitäten

| NAME | Begrenzung |
| ---- | ----- |
| Anzahl von Logik-Apps mit vom System zugewiesenen verwalteten Identitäten pro Azure-Abonnement | 100 |
|||

<a name="integration-account-limits"></a>

## <a name="integration-account-limits"></a>Grenzwerte für Integrationskonten

<a name="artifact-number-limits"></a>

### <a name="artifact-limits-per-integration-account"></a>Artefaktgrenzwerte pro Integrationskonto

Die folgenden Grenzwerte gelten für die Anzahl von Artefakten für jedes Integrationskonto. Weitere Informationen hierzu finden Sie unter [Logic Apps – Preise](https://azure.microsoft.com/pricing/details/logic-apps/).

> [!NOTE] 
> Verwenden Sie den Free-Tarif nur für Versuchsszenarios und nicht für Produktionsszenarios. Dieser Tarif beschränkt Durchsatz und Nutzung und ist nicht mit einer Vereinbarung zum Servicelevel (Service-Level Agreement, SLA) verbunden.

| Artefakt | Kostenlos | Basic | Standard |
|----------|------|-------|----------|
| EDI-Handelsverträge | 10 | 1 | 1.000 |
| EDI-Handelspartner | 25 | 2 | 1.000 |
| Karten | 25 | 500 | 1.000 |
| Schemas | 25 | 500 | 1.000 |
| Assemblys | 10 | 25 | 1.000 |
| Zertifikate | 25 | 2 | 500 |
| Batchkonfigurationen | 5 | 1 | 50 |
||||

<a name="artifact-capacity-limits"></a>

### <a name="artifact-capacity-limits"></a>Artefaktkapazitätsgrenzen

| Artefakt | Begrenzung | Notizen |
| -------- | ----- | ----- |
| Assembly | 8 MB | Verwenden Sie zum Hochladen von Dateien über 2 MB ein [Azure Storage-Konto und einen Blobcontainer](../logic-apps/logic-apps-enterprise-integration-schemas.md). |
| Zuordnung (XSLT-Datei) | 8 MB | Verwenden Sie zum Hochladen von Dateien über 2 MB die [Zuordnungen der REST-API für Azure Logic Apps](https://docs.microsoft.com/rest/api/logic/maps/createorupdate). |
| Schema | 8 MB | Verwenden Sie zum Hochladen von Dateien über 2 MB ein [Azure Storage-Konto und einen Blobcontainer](../logic-apps/logic-apps-enterprise-integration-schemas.md). |
||||

| Endpunkt zur Laufzeit | Begrenzung | Notizen |
|------------------|-------|-------|
| Leseaufrufe pro 5 Minuten | 60.000 | Sie können die Workload nach Bedarf auf mehrere Konten verteilen. |
| Aufrufe pro 5 Minuten | 45.000 | Sie können die Workload nach Bedarf auf mehrere Konten verteilen. |
| Nachverfolgungsaufrufe pro 5 Minuten | 45.000 | Sie können die Workload nach Bedarf auf mehrere Konten verteilen. |
| Gleichzeitige Blockierungsaufrufe | ca. 1.000 | Sie können die Anzahl gleichzeitiger Anforderungen oder die Dauer nach Bedarf verringern. |
||||

<a name="b2b-protocol-limits"></a>

### <a name="b2b-protocol-as2-x12-edifact-message-size"></a>Nachrichtengröße für B2B-Protokoll (AS2, X12, EDIFACT)

Folgende Grenzwerte für die Nachrichtengröße gelten für B2B-Protokolle:

| NAME | Grenzwert bei mehreren Mandanten | Grenzwert für Integrationsdienstumgebung | Notizen |
|------|--------------------|---------------------------------------|-------|
| AS2 | v2 – 100 MB<br>v1 – 50 MB | v2 – 200 MB <br>v1 – 50 MB | Gilt für das Decodieren und das Codieren |
| X12 | 50 MB | 50 MB | Gilt für das Decodieren und das Codieren |
| EDIFACT | 50 MB | 50 MB | Gilt für das Decodieren und das Codieren |
||||

<a name="disable-delete"></a>

## <a name="disabling-or-deleting-logic-apps"></a>Deaktivieren oder Löschen von Logik-Apps

Wenn Sie eine Logik-App deaktivieren, werden keine neuen Ausführungen instanziiert. Alle in Bearbeitung befindlichen und ausstehenden Ausführungen werden bis zum Ende fortgesetzt, was einige Zeit in Anspruch nehmen kann.

Wenn Sie eine Logik-App löschen, werden keine neuen Ausführungen instanziiert. Alle in Bearbeitung befindlichen und ausstehenden Ausführungen werden abgebrochen. Bei Tausenden von Ausführungen kann der Abbruch möglicherweise erhebliche Zeit in Anspruch nehmen.

<a name="configuration"></a>

## <a name="firewall-configuration-ip-addresses"></a>Firewallkonfiguration: IP-Adressen

Für alle Logik-Apps in derselben Region werden dieselben IP-Adressbereiche verwendet. Um die Aufrufe zu unterstützen, die Ihre Logik-Apps direkt mit [HTTP](../connectors/connectors-native-http.md), [HTTP + Swagger](../connectors/connectors-native-http-swagger.md) und anderen HTTP-Anforderungen durchführen, richten Sie Ihre Firewalls mit *allen* [eingehenden](#inbound) *und* [ausgehenden](#outbound) IP-Adressen ein, die vom Logic Apps-Dienst verwendet werden, basierend auf den Regionen, in denen Ihre Logik-Apps vorhanden sind. Diese Adressen werden unter den Überschriften **Eingehend** und **Ausgehend** in diesem Abschnitt angezeigt, wobei sie nach Region sortiert sind.

Um die Aufrufe zu unterstützen, die [von Microsoft verwalteten Connectors](../connectors/apis-list.md) ausführen, richten Sie Ihre Firewall mit *allen* [ausgehenden](#outbound) IP-Adressen ein, die von diesen Connectors verwendet werden, basierend auf den Regionen, in denen Ihre Logik-Apps vorhanden sind. Diese Adressen werden unter der Überschrift **Ausgehend** in diesem Abschnitt angezeigt, wobei sie nach Region sortiert sind.

Für [Azure Government](../azure-government/documentation-government-overview.md) und [Azure China 21Vianet](https://docs.microsoft.com/azure/china/) stehen derzeit keine reservierten IP-Adressen für Connectors zur Verfügung.

> [!IMPORTANT]
>
> Aktualisieren Sie ggf. vorhandene Konfigurationen **so bald wie möglich vor dem 1. September 2018**, damit sie die IP-Adressen in diesen Listen für die Regionen, in denen sich Ihre Logik-Apps befinden, enthalten und mit ihnen übereinstimmen.

Logic Apps unterstützt keine direkte Verbindung mit Azure-Speicherkonten über Firewalls. Um auf diese Speicherkonten zuzugreifen, nutzen Sie eine der folgenden Optionen:

* Erstellen Sie eine [Integrationsdienstumgebung](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), die eine Verbindung mit Ressourcen in einem virtuellen Azure-Netzwerk herstellen kann.

* Wenn Sie API Management bereits verwenden, können Sie diesen Dienst für das Szenario nutzen. Weitere Informationen finden Sie unter [Einfache Unternehmensintegrationsarchitektur](https://aka.ms/aisarch).

<a name="inbound"></a>

### <a name="inbound-ip-addresses---logic-apps-service-only"></a>Eingehende IP-Adressen – nur für Logic Apps-Dienst

| Region | IP |
|--------|----|
| Australien (Osten) | 13.75.153.66, 52.187.231.161, 104.210.89.222, 104.210.89.244 |
| Australien, Südosten | 13.73.115.153, 40.115.78.70, 40.115.78.237, 52.189.216.28 |
| Brasilien Süd | 191.234.166.198, 191.235.86.199, 191.235.94.220, 191.235.95.229 |
| Kanada, Mitte | 13.88.249.209, 40.85.241.105, 52.233.29.79, 52.233.30.218 |
| Kanada, Osten | 40.86.202.42, 52.229.125.57, 52.232.129.143, 52.232.133.109 |
| Indien, Mitte | 52.172.157.194, 52.172.184.192, 52.172.191.194, 104.211.73.195 |
| USA (Mitte) | 13.67.236.76, 40.77.31.87, 40.77.111.254, 104.43.243.39 |
| Asien, Osten | 13.75.89.159, 23.97.68.172, 40.83.98.194, 168.63.200.173 |
| USA (Ost) | 40.117.99.79, 40.117.100.228, 137.116.126.165, 137.135.106.54 |
| USA (Ost) 2 | 40.70.27.253, 40.79.44.7, 40.84.25.234, 40.84.59.136 |
| Japan, Osten | 13.71.146.140, 13.78.43.164, 13.78.62.130, 13.78.84.187 |
| Japan, Westen | 40.74.68.85, 40.74.81.13, 40.74.85.215, 40.74.140.173 |
| USA Nord Mitte | 65.52.9.64, 65.52.211.164, 168.62.249.81, 157.56.12.202 |
| Nordeuropa | 13.79.173.49, 40.112.90.39, 52.169.218.253, 52.169.220.174 |
| USA Süd Mitte | 13.65.98.39, 13.84.41.46, 13.84.43.45, 40.84.138.132 |
| Indien (Süden) | 52.172.9.47, 52.172.49.43, 52.172.51.140, 104.211.225.152 |
| Asien, Südosten | 52.163.93.214, 52.187.65.81, 52.187.65.155, 104.215.181.6 |
| USA, Westen-Mitte | 13.78.137.247, 52.161.8.128, 52.161.19.82, 52.161.26.172 |
| Europa, Westen | 13.95.155.53, 51.144.176.185, 52.174.49.6, 52.174.54.218 |
| Indien, Westen | 104.211.157.237, 104.211.164.25, 104.211.164.112, 104.211.165.81 |
| USA (Westen) | 13.91.252.184, 52.160.90.237, 138.91.188.137, 157.56.160.212 |
| USA, Westen 2 | 13.66.128.68, 13.66.224.169, 52.183.30.10, 52.183.39.67 |
| UK, Süden | 51.140.78.71, 51.140.79.109, 51.140.84.39, 51.140.155.81 |
| UK, Westen | 51.141.48.98, 51.141.51.145, 51.141.53.164, 51.141.119.150 |
| | |

<a name="outbound"></a>

### <a name="outbound-ip-addresses---logic-apps-service--managed-connectors"></a>Ausgehende IP-Adressen – Logic Apps-Dienst und verwaltete Connectors

| Region | IP der Logik-Apps | IP der verwalteten Connectors |
|--------|---------------|-----------------------|
| Australien (Osten) | 13.75.149.4, 52.187.226.96, 52.187.226.139, 52.187.227.245, 52.187.229.130, 52.187.231.184, 104.210.90.241, 104.210.91.55 | 13.70.72.192 - 13.70.72.207, 13.72.243.10, 40.126.251.213 |
| Australien, Südosten | 13.70.159.205, 13.73.114.207, 13.77.3.139, 13.77.56.167, 13.77.58.136, 52.189.214.42, 52.189.220.75, 52.189.222.77 | 13.70.136.174, 13.77.50.240–13.77.50.255, 40.127.80.34 |
| Brasilien Süd | 191.234.161.28, 191.234.161.168, 191.234.162.131, 191.234.162.178, 191.234.182.26, 191.235.82.221, 191.235.91.7, 191.237.255.116 | 104.41.59.51, 191.232.38.129, 191.233.203.192–191.233.203.207 | 
| Kanada, Mitte | 13.71.184.150, 13.71.186.1, 40.85.250.135, 40.85.250.212, 40.85.252.47, 52.233.29.92, 52.228.39.241, 52.228.39.244 | 13.71.170.208–13.71.170.223, 13.71.170.224–13.71.170.239, 52.228.33.76, 52.228.34.13, 52.228.42.205, 52.233.26.83, 52.233.31.197, 52.237.24.126 |
| Kanada, Osten | 40.86.203.228, 40.86.216.241, 40.86.217.241, 40.86.226.149, 40.86.228.93, 52.229.120.45, 52.229.126.25, 52.232.128.155 | 40.69.106.240–40.69.106.255, 52.229.120.52, 52.229.120.131, 52.229.120.178, 52.229.123.98, 52.229.126.202, 52.242.35.152 |
| Indien, Mitte | 52.172.154.168, 52.172.185.79, 52.172.186.159, 104.211.74.145, 104.211.90.162, 104.211.90.169, 104.211.101.108, 104.211.102.62 | 52.172.211.12, 104.211.81.192–104.211.81.207, 104.211.98.164 |
| USA (Mitte) | 13.67.236.125, 23.100.82.16, 23.100.86.139, 23.100.87.24, 23.100.87.56, 40.113.218.230, 40.122.170.198, 104.208.25.27 | 13.89.171.80–13.89.171.95, 40.122.49.51, 52.173.245.164 |
| Asien, Osten | 13.75.94.173, 40.83.73.39, 40.83.75.165, 40.83.77.208, 40.83.100.69, 40.83.127.19, 52.175.33.254, 65.52.175.34 | 13.75.36.64–13.75.36.79, 23.99.116.181, 52.175.23.169 |
| USA (Ost) | 13.92.98.111, 23.100.29.190, 23.101.132.208, 23.101.136.201, 23.101.139.153, 40.114.82.191, 40.121.91.41, 104.45.153.81 | 40.71.11.80 - 40.71.11.95, 40.71.249.205, 191.237.41.52 |
| USA (Ost) 2 | 40.70.26.154, 40.70.27.236, 40.70.29.214, 40.70.131.151, 40.84.30.147, 104.208.140.40, 104.208.155.200, 104.208.158.174 | 40.70.146.208 - 40.70.146.223, 52.232.188.154, 104.208.233.100 |
| Japan, Osten | 13.71.158.3, 13.71.158.120, 13.73.4.207, 13.78.18.168, 13.78.20.232, 13.78.21.155, 13.78.35.229, 13.78.42.223 | 13.71.153.19, 13.78.108.0–13.78.108.15, 40.115.186.96 |
| Japan, Westen | 40.74.64.207, 40.74.68.85, 40.74.74.21, 40.74.76.213, 40.74.77.205, 40.74.140.4, 104.214.137.243, 138.91.26.45 | 40.74.100.224–40.74.100.239, 40.74.130.77, 104.215.61.248 |
| USA Nord Mitte | 52.162.208.216, 52.162.213.231, 65.52.8.225, 65.52.9.96, 65.52.10.183, 157.55.210.61, 157.55.212.238, 168.62.248.37 | 52.162.107.160 - 52.162.107.175, 52.162.242.161, 65.52.218.230 |
| Nordeuropa | 40.112.92.104, 40.112.95.216, 40.113.1.181, 40.113.3.202, 40.113.4.18, 40.113.12.95, 52.178.165.215, 52.178.166.21 | 13.69.227.208 - 13.69.227.223, 52.178.150.68, 104.45.93.9 |
| USA Süd Mitte | 13.65.82.17, 13.66.52.232, 23.100.124.84, 23.100.127.172, 23.101.183.225, 70.37.54.122, 70.37.50.6, 104.210.144.48 | 13.65.86.57, 104.214.19.48–104.214.19.63, 104.214.70.191 |
| Indien (Süden) | 52.172.50.24, 52.172.52.0, 52.172.55.231, 104.211.227.229, 104.211.229.115, 104.211.230.126, 104.211.230.129, 104.211.231.39 | 13.71.125.22, 40.78.194.240–40.78.194.255, 104.211.227.225 |
| Asien, Südosten | 13.67.91.135, 13.67.107.128, 13.67.110.109, 13.76.4.194, 13.76.5.96, 13.76.133.155, 52.163.228.93, 52.163.230.166 | 13.67.8.240–13.67.8.255, 13.76.231.68, 52.187.68.19 |
| USA, Westen-Mitte | 13.78.129.20, 13.78.137.179, 13.78.141.75, 13.78.148.140, 13.78.151.161, 52.161.18.218, 52.161.9.108, 52.161.27.190 | 13.71.195.32–13.71.195.47, 52.161.24.128, 52.161.26.212, 52.161.27.108, 52.161.29.35, 52.161.30.5, 52.161.102.22 |
| Europa, Westen | 13.95.147.65, 23.97.210.126, 23.97.211.179, 23.97.218.130, 40.68.209.23, 40.68.222.65, 51.144.182.201, 104.45.9.52 | 13.69.64.208–13.69.64.223, 40.115.50.13, 52.174.88.118 |
| Indien, Westen | 104.211.154.7, 104.211.154.59, 104.211.156.153, 104.211.158.123, 104.211.158.127, 104.211.162.205, 104.211.164.80, 104.211.164.136 | 104.211.146.224–104.211.146.239, 104.211.161.203, 104.211.189.218 |
| USA (Westen) | 40.83.164.80, 40.118.244.241, 40.118.241.243, 52.160.92.112, 104.42.38.32, 104.42.49.145, 157.56.162.53, 157.56.167.147 |40.112.243.160–40.112.243.175, 104.40.51.248, 104.42.122.49 |
| USA, Westen 2 | 13.66.201.169, 13.66.210.167, 13.66.246.219, 13.77.149.159, 52.175.198.132, 52.183.29.132, 52.183.30.169 | 13.66.140.128–13.66.140.143, 13.66.218.78, 13.66.219.14, 13.66.220.135, 13.66.221.19, 13.66.225.219, 52.183.78.157 |
| UK, Süden | 51.140.28.225, 51.140.73.85, 51.140.74.14, 51.140.78.44, 51.140.137.190, 51.140.142.28, 51.140.153.135, 51.140.158.24 | 51.140.80.51, 51.140.148.0–51.140.148.15 |
| UK, Westen | 51.141.45.238, 51.141.47.136, 51.141.54.185, 51.141.112.112, 51.141.113.36, 51.141.114.77, 51.141.118.119, 51.141.119.63 | 51.140.211.0 - 51.140.211.15, 51.141.47.105 |
||||

## <a name="next-steps"></a>Nächste Schritte  

* Erfahren Sie, wie Sie [Ihre erste Logik-App erstellen](../logic-apps/quickstart-create-first-logic-app-workflow.md).  
* Lernen Sie [allgemeine Beispiele und Szenarien](../logic-apps/logic-apps-examples-and-scenarios.md) kennen.
