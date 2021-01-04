---
title: Dienstkonfiguration – QnA Maker
description: Erfahren Sie, wie und wo Ressourcen konfiguriert werden.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 11/9/2020
ms.openlocfilehash: e7dfc6db50432b19d515671fabe26edf9cfb45ef
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96345667"
---
# <a name="service-configuration"></a>Dienstkonfiguration

Jede Version von QnA Maker verwendet einen anderen Satz von Azure-Ressourcen (Diensten). In diesem Artikel werden die unterstützten Anpassungen für diese Dienste beschrieben. 

## <a name="app-service"></a>App Service

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker, allgemeine Verfügbarkeit (stabile Version)](#tab/v1)

QnA Maker verwendet den App Service, um die von der [generateAnswer-API](/rest/api/cognitiveservices/qnamaker4.0/runtime/generateanswer) verwendete Abfragelaufzeit bereitzustellen.

Diese Einstellungen sind im Azure-Portal für den App Service verfügbar. Die Einstellungen sind durch Auswahl von **Einstellungen** und dann **Konfiguration** verfügbar.

Sie können eine einzelne Einstellung entweder über die Liste der Anwendungseinstellungen festlegen oder mehrere Einstellungen durch Auswahl von **Erweiterte Bearbeitung** ändern.

|Resource|Einstellung|
|--|--|
|AzureSearchAdminKey|Cognitive Search: wird für QnA-Paarspeicher und Bewerter 1 verwendet|
|AzureSearchName|Cognitive Search: wird für QnA-Paarspeicher und Bewerter 1 verwendet|
|DefaultAnswer|Text der Antwort, wenn kein Treffer gefunden wird|
|UserAppInsightsAppId|Chatprotokoll und Telemetrie|
|UserAppInsightsKey|Chatprotokoll und Telemetrie|
|UserAppInsightsName|Chatprotokoll und Telemetrie|

Sie müssen den Dienst über die Seite **Übersicht** des Azure-Portals **neu starten**, sobald Sie mit den Änderungen fertig sind.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker verwaltet (Vorschauversion)](#tab/v2)

App Service-Anpassungen gelten nicht für QnA Maker verwaltet (Vorschau).

---

## <a name="qna-maker-service"></a>QnA Maker Service

Der QnA Maker-Dienst stellt die Konfiguration für die folgenden Benutzer bereit, die an einem einzelnen QnA Maker-Dienst und all seinen Wissensdatenbanken zusammenarbeiten können.

Erfahren Sie, [wie Sie Projektmitarbeiter](./reference-role-based-access-control.md) zu Ihrem Dienst hinzufügen können.

## <a name="change-azure-cognitive-search"></a>Ändern von Azure Cognitive Search

Erfahren Sie, [wie Sie den Cognitive Search-Dienst ändern](./how-to/set-up-qnamaker-service-azure.md#configure-qna-maker-to-use-different-cognitive-search-resource), der mit Ihrem QnA Maker-Dienst verknüpft ist.

## <a name="change-default-answer"></a>Ändern der Standardantwort

Erfahren Sie, [wie Sie den Text Ihrer Standardantworten ändern](How-To/change-default-answer.md) können. 

## <a name="telemetry"></a>Telemetrie

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker, allgemeine Verfügbarkeit (stabile Version)](#tab/v1)

Anwendung Insights wird zur Überwachung der Telemetrie mit QnA Maker GA verwendet. Es gibt keine speziellen Konfigurationseinstellungen für QnA Maker.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker verwaltet (Vorschauversion)](#tab/v2)

Erfahren Sie, [wie Sie QnA Maker verwaltet (Vorschau) Telemetriedaten hinzufügen](How-To/get-analytics-knowledge-base.md). 

---

## <a name="app-service-plan"></a>App Service-Plan

# <a name="qnamaker-ga-stable-release"></a>[QnA Maker, allgemeine Verfügbarkeit (stabile Version)](#tab/v1)

App Service-Plan verfügt über keine speziellen Konfigurationseinstellungen für QnA Maker.

# <a name="qnamaker-managed-preview-release"></a>[QnA Maker verwaltet (Vorschauversion)](#tab/v2)

App Service-Plan wird nicht mit QnA Maker verwaltet (Vorschau) verwendet.

---

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über [Formate](reference-document-format-guidelines.md) für Dokumente und URLs, die Sie in eine Wissensdatenbank importieren möchten.