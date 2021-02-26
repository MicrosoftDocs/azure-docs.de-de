---
title: Abrechnungs- und Preismodelle
description: Übersicht über die Funktionsweise von Preis- und Abrechnungsmodellen in Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 01/29/2021
ms.openlocfilehash: 0de0c5d53bd3195a24f75f4a2e65c19602e2a2b3
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/30/2021
ms.locfileid: "99088920"
---
# <a name="pricing-and-billing-models-for-azure-logic-apps"></a>Preis- und Abrechnungsmodelle für Azure Logic Apps

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) unterstützt Sie dabei, automatisierte Integrationsworkflows zu erstellen und auszuführen, die in der Cloud abskaliert werden können. In diesem Artikel werden die Abrechnungs- und Preismodelle für den Logic Apps-Dienst und die zugehörigen Ressourcen beschrieben. Eine Preisübersicht finden Sie unter [Logic Apps – Preise](https://azure.microsoft.com/pricing/details/logic-apps). Informationen zum Planen, Verwalten und Überwachen von Kosten finden Sie unter [Planen und Verwalten von Kosten für Azure Logic Apps](plan-manage-costs.md).

<a name="consumption-pricing"></a>

## <a name="multi-tenant-pricing"></a>Preismodell für mehrere Mandanten

Für Logik-Apps, die im öffentlichen, „globalen“, mehrinstanzenfähigen Dienst Azure Logic Apps ausgeführt werden, gilt ein nutzungsabhängiges Preismodell. Alle erfolgreichen und fehlgeschlagenen Ausführungen werden gezählt und abgerechnet.

So wird z. B. eine Anforderung, die ein Abfragetrigger stellt, auch dann als Ausführung gezählt, wenn dieser Trigger übersprungen und keine Workflowinstanz der Logik-App erstellt wird.

| Elemente | Beschreibung |
|-------|-------------|
| [Integrierte](../connectors/apis-list.md#built-in) Trigger und Aktionen | Werden im Dienst Azure Logic Apps nativ ausgeführt und mit dem Preis von [**Aktionen** gemessen](https://azure.microsoft.com/pricing/details/logic-apps/). <p><p>Beispielsweise sind die Trigger „HTTP“ und „Anforderung“ integrierte Trigger, während die Aktionen „HTTP“ und „Antwort“ integrierte Aktionen sind. Daten-, Batch- und Variablenvorgänge sowie [Aktionen zum Steuern von Workflows](../connectors/apis-list.md#control-workflow) wie Schleifen, Bedingungen, Schalter, parallele Verzweigungen usw. sind ebenfalls integrierte Aktionen. |
| Trigger und Aktionen des Typs [Standardconnector](../connectors/apis-list.md#managed-connectors) <p><p>Trigger und Aktionen des Typs [Benutzerdefinierter Connector](../connectors/apis-list.md#custom) | Gemessen mit dem Preis für [Standardconnector](https://azure.microsoft.com/pricing/details/logic-apps/). |
| Trigger und Aktionen des Typs [Unternehmensconnector](../connectors/apis-list.md#managed-connectors) | Gemessen mit dem Preis für [Unternehmensconnector](https://azure.microsoft.com/pricing/details/logic-apps/). In der öffentlichen Vorschauphase werden Unternehmensconnectors jedoch mit dem Preis für [*Standardconnectors*](https://azure.microsoft.com/pricing/details/logic-apps/)abgerechnet. |
| Aktionen innerhalb von [Schleifen](logic-apps-control-flow-loops.md) | Jede Aktion, die in einer Schleife erfolgt, wird für jeden ausgeführten Schleifenzyklus gemessen. <p><p>Angenommen, Sie haben eine FOR EACH-Schleife mit Aktionen zum Verarbeiten einer Liste. Der Dienst Azure Logic Apps erfasst jede Aktion in dieser Schleife, indem die Anzahl der Listenelemente mit der Anzahl der Aktionen in der Schleife multipliziert wird. Anschließend wird die Aktion zum Starten der Schleife hinzugefügt. Daher lautet die Berechnung für eine Liste mit zehn Elementen (10 * 1) + 1, sodass sich 11 Aktionsausführungen ergeben. |
| Wiederholungsversuche | Um die grundlegendsten Ausnahmen und Fehler zu behandeln, können Sie, sofern unterstützt, eine [Wiederholungsrichtlinie](logic-apps-exception-handling.md#retry-policies) für Trigger und Aktionen einrichten. Diese Wiederholungen werden zusammen mit der ursprünglichen Anforderung zu Tarifen in Rechnung gestellt, die darauf basieren, ob der Trigger oder die Aktion den Typ „Integriert“, „Standard“ oder „Unternehmen“ hat. Beispielsweise werden für eine Aktion, die mit zwei Wiederholungen ausgeführt wird, drei Aktionsausführungen berechnet. |
| [Datenaufbewahrung und Speichernutzung](#data-retention) | Wird mit dem Preis für Datenaufbewahrung gemessen, den Sie auf der [Preisseite für Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/) unter der Tabelle **Preisdetails** finden. |
|||

Weitere Informationen finden Sie unter

* [Anzeigen von Metriken für Ausführungen und Speichernutzung](plan-manage-costs.md#monitor-billing-metrics)
* [Grenzwerte in Azure Logic Apps](logic-apps-limits-and-config.md)

### <a name="not-metered"></a>Nicht gemessen

* Aktionen, die aufgrund nicht erfüllter Bedingungen übersprungen werden
* Aktionen, die nicht ausgeführt werden, weil die Logik-App vor Abschluss beendet wurde
* [Deaktivierte Logik-Apps](#disabled-apps)

### <a name="other-related-resources"></a>Andere zugehörige Ressourcen

Logik-Apps arbeiten mit anderen zugehörigen Ressourcen zusammen, z. B. mit Integrationskonten, lokalen Datengateways und Integrationsdienstumgebungen (ISEs). Weitere Informationen zu den Preisen dieser Ressourcen finden Sie in diesen Abschnitten weiter unten in diesem Thema:

* [Lokales Datengateway](#data-gateway)
* [Preismodell für Integrationskonten](#integration-accounts)
* [ISE-Preismodell](#fixed-pricing)

### <a name="tips-for-estimating-consumption-costs"></a>Tipps zum Schätzen der Nutzungskosten

Diese Tipps helfen Ihnen beim Schätzen genauerer Nutzungskosten:

* Berücksichtigen Sie die mögliche Anzahl von Nachrichten oder Ereignissen, die an einem beliebigen Tag eingehen, statt Ihre Berechnungen nur auf das Abrufintervall zu stützen.

* Wenn ein Ereignis oder eine Nachricht die Auslösekriterien erfüllt, versuchen viele Trigger sofort, alle anderen wartenden Ereignisse oder Nachrichten, die die Kriterien erfüllen, zu lesen. Dieses Verhalten bedeutet, dass auch bei Auswahl eines längeren Abrufintervalls der Trigger aufgrund der Anzahl der wartenden Ereignisse oder Nachrichten, die für das Starten von Workflows qualifiziert sind, ausgelöst wird. Zu den Triggern, die diesem Verhalten folgen, zählen Azure Service Bus und Azure Event Hub.

  Angenommen, Sie haben einen Trigger eingerichtet, der täglich einen Endpunkt überprüft. Wenn der Trigger den Endpunkt überprüft und 15 Ereignisse findet, die die Kriterien erfüllen, wird der Trigger ausgelöst und führt den entsprechenden Workflow 15 mal aus. Der Dienst Azure Logic Apps misst alle Aktionen, die diese 15 Workflows ausführen, einschließlich der Triggeranforderungen.

<a name="fixed-pricing"></a>

## <a name="ise-pricing"></a>ISE-Preise

Für Logik-Apps, die in einer [*Integrationsdienstumgebung* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) ausgeführt werden, gilt ein Festpreismodell. Eine ISE wird mit dem [Preis für Integrationsdienstumgebungen](https://azure.microsoft.com/pricing/details/logic-apps) abgerechnet, der von der von Ihnen erstellten [ISE-Stufe oder *SKU*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) abhängt. Diese Preise unterscheiden sich von den Preisen für mehrere Mandanten, da Sie für reservierte Kapazität und dedizierte Ressourcen unabhängig davon zahlen, ob Sie diese nutzen oder nicht.

| ISE SKU | BESCHREIBUNG |
|---------|-------------|
| **Premium** | Die Basiseinheit bietet eine feste Kapazität und wird für [die SKU „Premium“ mit einem Stundensatz abgerechnet](https://azure.microsoft.com/pricing/details/logic-apps). Falls Sie mehr Durchsatz benötigen, können Sie bei der Erstellung Ihrer ISE oder nachträglich [weitere Skalierungseinheiten hinzufügen](../logic-apps/ise-manage-integration-service-environment.md#add-capacity). Jede Skalierungseinheit wird mit einem [Stundensatz abgerechnet, der etwa der Hälfte des Satzes der Basiseinheit entspricht](https://azure.microsoft.com/pricing/details/logic-apps). <p><p>Informationen zu Grenzwerten finden Sie unter [ISE-Grenzwerte in Azure Logic Apps](logic-apps-limits-and-config.md#integration-service-environment-ise). |
| **Developer** | Die Basiseinheit bietet eine feste Kapazität und wird für [die SKU „Developer“ mit einem Stundensatz abgerechnet](https://azure.microsoft.com/pricing/details/logic-apps). Diese SKU bietet nicht die Möglichkeit des Hochskalierens, keine Vereinbarung zum Servicelevel (SLA) oder veröffentlichte Grenzwerte. Diese SKU eignet sich nur für Erkundung, Experimente, Entwicklung und Tests, nicht jedoch für die Produktion oder Leistungstests. |
|||

### <a name="included-at-no-extra-cost"></a>Ohne zusätzliche Kosten inbegriffen

| Elemente | Beschreibung |
|-------|-------------|
| [Integrierte](../connectors/apis-list.md#built-in) Trigger und Aktionen | Werden mit der Beschriftung **Core** angezeigt und in derselben ISE ausgeführt wie Ihre Logik-Apps. |
| [Standardconnectors](../connectors/apis-list.md#managed-connectors) <p><p>[Unternehmensconnector](../connectors/apis-list.md#enterprise-connectors) | Verwaltete Connectors, die mit der Beschriftung **ISE** versehen sind, wurden speziell für den Betrieb ohne das lokale Datengateways entwickelt und werden in derselben ISE wie Ihre Logik-Apps ausgeführt. Die ISE-Preise decken so viele Unternehmensverbindungen wie gewünscht ab. <p><p>Connectors ohne die Beschriftung „ISE“ werden im mehrinstanzenfähigen Dienst Azure Logic Apps ausgeführt. Die ISE-Preise decken diese Ausführungen jedoch für in einer ISE ausgeführte Logik-Apps ab. |
| Aktionen innerhalb von [Schleifen](logic-apps-control-flow-loops.md) | Die ISE-Preise decken jede in einer Schleife ausgeführte Aktion für jeden ausgeführten Schleifenzyklus ab. <p><p>Angenommen, Sie haben eine FOR EACH-Schleife mit Aktionen zum Verarbeiten einer Liste. Zur Ermittlung der Gesamtanzahl der Ausführungen von Aktionen multiplizieren Sie die Anzahl der Listenelemente mit der Anzahl der Aktionen in der Schleife und addieren die Aktion zum Starten der Schleife. Daher lautet die Berechnung für eine Liste mit zehn Elementen (10 * 1) + 1, sodass sich 11 Aktionsausführungen ergeben. |
| Wiederholungsversuche | Um die grundlegendsten Ausnahmen und Fehler zu behandeln, können Sie, sofern unterstützt, eine [Wiederholungsrichtlinie](logic-apps-exception-handling.md#retry-policies) für Trigger und Aktionen einrichten. Die ISE-Preise berücksichtigen Wiederholungen ebenso wie die ursprüngliche Anforderung. |
| [Datenaufbewahrung und Speichernutzung](#data-retention) | Für Logik-Apps in einer ISE fallen keine Aufbewahrungs- und Speicherkosten an. |
| [Integrationskonten](#integration-accounts) | Schließt die Nutzung eines Einzeltarifs für Integrationskonten auf Basis der ISE SKU ohne zusätzliche Kosten ein. |
|||

Informationen zu Grenzwerten finden Sie unter [ISE-Grenzwerte in Azure Logic Apps](logic-apps-limits-and-config.md#integration-service-environment-ise).

<a name="integration-accounts"></a>

## <a name="integration-accounts"></a>Integrationskonten

Ein [Integrationskonto](../logic-apps/logic-apps-pricing.md#integration-accounts) ist eine gesonderte Ressource, die Sie erstellen und mit Logik-Apps verknüpfen. Es dient zum Erkunden, Erstellen und Testen von B2B-Integrationslösungen, die [EDI](logic-apps-enterprise-integration-b2b.md)- und [XML-Verarbeitungsfunktionen](logic-apps-enterprise-integration-xml.md) nutzen. Azure Logic Apps bietet diese Ebenen bzw. Tarife für Integrationskonten:

| Tarif | BESCHREIBUNG |
|------|-------------|
| **Basic** | Für Szenarien, in denen Sie nur die Verarbeitung von Nachrichten nutzen oder als Partner eines kleines Unternehmens fungieren möchten, das eine Handelspartnerbeziehung mit einem größeren Unternehmen eingegangen ist. <p><p>Ist von der SLA für Logic Apps abgedeckt. |
| **Standard** | Für Szenarien mit komplexeren B2B-Beziehungen und einer größeren Anzahl zu verwaltender Entitäten. <p><p>Ist von der SLA für Logic Apps abgedeckt. |
| **Free** | Nur für Erkundungs- und nicht für Produktionsszenarien. Bei diesem Tarif gelten Einschränkungen für Region, Durchsatz und Nutzung. Der Free-Tarif ist beispielsweise nur für öffentliche Regionen in Azure verfügbar, z. B. USA, Westen oder Asien, Südosten, aber nicht für [Azure China 21ViaNet](/azure/china/overview-operations) oder [Azure Government](../azure-government/documentation-government-welcome.md). <p><p>**Hinweis**: Ist von der SLA für Logic Apps nicht abgedeckt. |
|||

Informationen zu den Grenzwerten für Integrationskonten finden Sie unter [Grenzwerte und Konfiguration für Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits) wie z. B.:

* [Grenzwerte für Integrationskonten pro Azure-Abonnement](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)

* [Grenzwerte für verschiedene Artefakte pro Integrationskonto](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits). Artefakte umfassen Handelspartner, Vereinbarungen, Zuordnungen, Schemas, Assemblys, Zertifikate, Batchkonfigurationen usw.

### <a name="integration-accounts-for-consumption-based-logic-apps"></a>Integrationskonten für nutzungsbasierte Logik-Apps

Integrationskonten werden mit einem [Festpreis für Integrationskonten](https://azure.microsoft.com/pricing/details/logic-apps/) abgerechnet, der sich nach dem von Ihnen genutzten Kontotarif richtet.

### <a name="ise-based-logic-apps"></a>ISE-basierte Logik-Apps

Ohne Mehrkosten gehört zu Ihrer ISE ein einzelnes Integrationskonto, das auf Ihrer ISE SKU basiert. Gegen Mehrkosten können Sie weitere Integrationskonten für Ihre ISE anlegen, die Sie bis zur [ISE-Obergrenze](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits) nutzen können. Weitere Informationen zum [ISE-Preismodell](#fixed-pricing) finden Sie weiter oben in diesem Thema.

| ISE SKU | Integrationskonto inbegriffen | Mehrkosten |
|---------|------------------------------|-----------------|
| **Premium** | Einzelnes Integrationskonto im Tarif [Standard](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) | Bis zu 19 weitere Standard-Konten. Es sind keine Free- oder Basic-Konten zulässig. |
| **Developer** | Einzelnes Integrationskonto im Tarif [Free](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) | Bis zu 19 weitere Standard-Konten, wenn Sie bereits über ein Free-Konto verfügen, oder 20 Standardkonten, wenn Sie kein Free-Konto besitzen. Es sind keine Basic-Konten zulässig. |
||||

<a name="data-retention"></a>

## <a name="data-retention-and-storage-consumption"></a>Datenaufbewahrung und Speichernutzung

Alle Ein- und Ausgaben im Ausführungsverlauf Ihrer Logik-App werden auf Grundlage der [Ausführungsdauer und Aufbewahrungsdauer des Verlaufs](logic-apps-limits-and-config.md#run-duration-retention-limits) dieser App gespeichert und gemessen.

* Für Logik-Apps im mehrinstanzenfähigen Dienst Azure Logic Apps wird die Speichernutzung zu einem Festpreis abgerechnet, den Sie auf der [Preisseite für Logik-Apps](https://azure.microsoft.com/pricing/details/logic-apps) unter der Tabelle **Preisdetails** finden.

* Für Logik-Apps in ISEs entstehen durch die Speichernutzung keine Datenaufbewahrungskosten.

Informationen zur Überwachung der Speichernutzung finden Sie unter [Anzeigen von Metriken zu Ausführungen und Speichernutzung](plan-manage-costs.md#monitor-billing-metrics).

<a name="data-gateway"></a>

## <a name="on-premises-data-gateway"></a>Lokales Datengateway

Ein [lokales Datengateways](../logic-apps/logic-apps-gateway-install.md) ist eine gesonderte Ressource, die Sie erstellen, damit Ihre Logik-Apps mithilfe bestimmter vom Gateway unterstützter Connectors auf lokale Daten zugreifen können. Für über das Gateway erfolgende Connectorvorgänge fallen Gebühren an, für das Gateway selbst jedoch nicht.

<a name="disabled-apps"></a>

## <a name="disabled-logic-apps"></a>Deaktivierte Logik-Apps

Deaktivierte Logik-Apps werden nicht berechnet, da für sie keine neuen Instanzen erstellt werden können. Nachdem Sie eine Logik-App deaktiviert haben, kann es einige Zeit dauern, bis die derzeit ausgeführten Instanzen vollständig beendet werden.

## <a name="next-steps"></a>Nächste Schritte

* [Planen und Verwalten von Kosten für Azure Logic Apps](plan-manage-costs.md)
