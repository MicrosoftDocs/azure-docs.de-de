---
title: 'Azure-Ressourcen: QnA Maker'
description: In QnA Maker werden verschiedene Azure-Quellen verwendet, von denen jede einen anderen Zweck erfüllt. Wenn Sie wissen, wie sie einzeln verwendet werden, können Sie den richtigen Tarif planen und auswählen oder bestimmen, wann ein Tarifwechsel sinnvoll ist. Das Verständnis dafür, wie sie kombiniert verwendet werden, ermöglicht es Ihnen, auftretende Probleme zu finden und zu beheben.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: f46a0938ebb8d9fe7e032162120056dca96b9567
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "99979761"
---
# <a name="azure-resources-for-qna-maker"></a>Azure-Ressourcen für QnA Maker

In QnA Maker werden verschiedene Azure-Quellen verwendet, von denen jede einen anderen Zweck erfüllt. Wenn Sie wissen, wie sie einzeln verwendet werden, können Sie den richtigen Tarif planen und auswählen oder bestimmen, wann ein Tarifwechsel sinnvoll ist. Das Verständnis dafür, wie sie _kombiniert_ verwendet werden, ermöglicht es Ihnen, auftretende Probleme zu finden und zu beheben.

## <a name="resource-planning"></a>Ressourcenplanung

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker, allgemeine Verfügbarkeit (stabile Version)](#tab/v1)

Wenn Sie zum ersten Mal eine QnA Maker Knowledge Base entwickeln, ist es in der Prototypenphase üblich, sowohl für Tests als auch für die Produktion eine einzelne QnA Maker-Ressource zu verwenden.

Beim Einstieg in die Entwicklungsphase des Projekts sollten Sie die folgenden Punkte berücksichtigen:

* Wie viele Sprachen soll Ihr Wissensdatenbank-System enthalten?
* In wie vielen Regionen muss Ihre Wissensdatenbank verfügbar sein, bzw. aus wie vielen Regionen wird sie zur Verfügung gestellt?
* Wie viele Dokumente werden die einzelnen Domänen in Ihrem System enthalten?

Sie sollten so planen, dass eine einzelne QnA Maker-Ressource alle Wissensdatenbanken enthält, die die gleiche Sprache, die gleiche Region und die gleiche Kombination aus Thema und Domäne aufweisen.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker verwaltet (Vorschauversion)](#tab/v2)

Wenn Sie zum ersten Mal eine von QnA Maker verwaltete Wissensdatenbank entwickeln, ist es in der Prototypenphase üblich, sowohl für Tests als auch für die Produktion eine einzelne von QnA Maker verwaltete Ressource zu verwenden.

Beim Einstieg in die Entwicklungsphase des Projekts sollten Sie die folgenden Punkte berücksichtigen:

* Wie viele Sprachen soll Ihr Wissensdatenbank-System enthalten?
* In wie vielen Regionen muss Ihre Wissensdatenbank verfügbar sein, bzw. aus wie vielen Regionen wird sie zur Verfügung gestellt?
* Wie viele Dokumente werden die einzelnen Domänen in Ihrem System enthalten?

---

## <a name="pricing-tier-considerations"></a>Tarifstufeninformationen

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker, allgemeine Verfügbarkeit (stabile Version)](#tab/v1)

Es gibt in der Regel drei Parameter, die Sie berücksichtigen müssen:

* **Den Durchsatz, den Sie vom Dienst benötigen:**
    * Wählen Sie den passenden [App-Plan](https://azure.microsoft.com/pricing/details/app-service/plans/) für Ihren App Service auf der Grundlage Ihrer Anforderungen aus. Sie können die App [hochskalieren](../../../app-service/manage-scale-up.md) oder herunterskalieren.
    * Dies sollte auch Einfluss auf Ihre Wahl der Azure [Cognitive Search](../../../search/search-sku-tier.md)-SKU haben. Weitere Details dazu finden Sie **hier**. Darüber hinaus müssen Sie unter Umständen die Cognitive Search-[Kapazität](../../../search/search-capacity-planning.md) mit Replikaten anpassen.

* **Größe und Anzahl von Wissensdatenbanken:** Wählen Sie eine geeignete [Azure Search-SKU](https://azure.microsoft.com/pricing/details/search/) für Ihr Szenario aus. Normalerweise entscheiden Sie über die Anzahl der erforderlichen Wissensdatenbanken anhand der Anzahl der verschiedenen Themendomänen. Eine Themendomäne (für eine einzelne Sprache) sollte in einer Wissensdatenbank vorliegen.

    Sie können N-1 Wissensdatenbanken in einem bestimmten Tarif veröffentlichen, wobei N die Anzahl der im Tarif maximal zulässigen Indizes ist. Überprüfen Sie außerdem die maximale Größe und Anzahl der in den einzelnen Tarifen zulässigen Dokumente.

    Wenn Ihr Tarif beispielsweise 15 zulässige Indizes aufweist, können Sie 14 Wissensdatenbanken veröffentlichen (1 Index pro veröffentlichter Wissensdatenbank). Der fünfzehnte Index wird für alle Wissensdatenbanken zum Erstellen und Testen verwendet.

* **Anzahl von Dokumenten als Quellen:** Die kostenlose SKU des QnA Maker-Verwaltungsdiensts begrenzt die Anzahl der Dokumente, die über das Portal und die APIs verwaltet werden können, auf 3 (bei einer Größe von jeweils 1 MB). Die Standard-SKU ist in der Anzahl der Dokumente, die Sie verwalten können, nicht begrenzt. Ausführlichere Informationen finden Sie [hier](https://aka.ms/qnamaker-pricing).

Die folgende Tabelle gibt Ihnen einige allgemeine Richtlinien.

|                            | QnA Maker Management | App Service | Azure Cognitive Search | Einschränkungen                      |
| -------------------------- | -------------------- | ----------- | ------------ | -------------------------------- |
| **Experimentieren**        | Kostenlose SKU             | Free-Tarif   | Free-Tarif    | Veröffentlichen von bis zu 2 KB bei einer Größe von 50 MB  |
| **Entwicklungs-/Testumgebung**   | Standard-SKU         | Shared      | Basic        | Veröffentlichen von bis zu 14 KB bei einer Größe von 2 GB    |
| **Produktionsumgebung** | Standard-SKU         | Basic       | Standard     | Veröffentlichen von bis zu 49 KB bei einer Größe von 25 GB |

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker verwaltet (Vorschauversion)](#tab/v2)

Es gibt in der Regel drei Parameter, die Sie berücksichtigen müssen:

* **Den Durchsatz, den Sie vom Dienst benötigen:**
    * QnA Maker verwaltet (Vorschau) ist ein kostenloser Dienst, und der Durchsatz ist derzeit auf 10 TPS sowohl für Verwaltungs-APIs als auch für Vorhersage-APIs begrenzt.
    * Dies sollte auch Einfluss auf Ihre Wahl der Azure [Cognitive Search](../../../search/search-sku-tier.md)-SKU haben. Weitere Details dazu finden Sie **hier**. Darüber hinaus müssen Sie unter Umständen die Cognitive Search-[Kapazität](../../../search/search-capacity-planning.md) mit Replikaten anpassen.

* **Größe und Anzahl von Wissensdatenbanken:** Wählen Sie eine geeignete [Azure Search-SKU](https://azure.microsoft.com/pricing/details/search/) für Ihr Szenario aus. Normalerweise entscheiden Sie über die Anzahl der erforderlichen Wissensdatenbanken anhand der Anzahl der verschiedenen Themendomänen. Eine Themendomäne (für eine einzelne Sprache) sollte in einer Wissensdatenbank vorliegen.

    Mit QnA Maker verwaltet (Vorschau) haben Sie die Wahl, Ihren QnA Maker-Dienst für Wissensdatenbanken in einer einzelnen oder in mehreren Sprachen einzurichten. Sie können diese Auswahl treffen, wenn Sie die erste Wissensdatenbank in Ihrem vom QnA Maker verwalteten Dienst (Vorschau) erstellen.

    ![QnA Maker verwaltet (Vorschau) mehrsprachige Wissensdatenbankauswahl](../media/concept-plan-your-knowledge-base/qnamaker-v2-select-multilanguage-knowledge-base.png)

    Sie können N-1 Wissensdatenbanken einer einzelnen Sprache oder N/2 Wissensdatenbanken verschiedener Sprachen einer bestimmten Ebene veröffentlichen, wobei N die maximal zulässigen Indizes auf der Ebene angibt. Überprüfen Sie außerdem die maximale Größe und Anzahl der in den einzelnen Tarifen zulässigen Dokumente.

    Wenn Ihr Tarif z. B. 15 zulässige Indizes aufweist, können Sie 14 Wissensdatenbanken derselben Sprache veröffentlichen (1 Index pro veröffentlichter Wissensdatenbank). Der fünfzehnte Index wird für alle Wissensdatenbanken zum Erstellen und Testen verwendet. Wenn Sie sich für Wissensdatenbanken in verschiedenen Sprachen entscheiden, können Sie nur sieben Wissensdatenbanken veröffentlichen.

* **Anzahl von Dokumenten als Quellen:** QnA Maker verwaltete (Vorschau) ist ein kostenloser Dienst, und es gibt keine Beschränkungen für die Anzahl der Dokumente, die Sie als Quellen hinzufügen können. Ausführlichere Informationen finden Sie [hier](https://aka.ms/qnamaker-pricing).

Die folgende Tabelle gibt Ihnen einige allgemeine Richtlinien.

|                            |Azure Cognitive Search | Einschränkungen                      |
| -------------------------- |------------ | -------------------------------- |
| **Experimentieren**        |Free-Tarif    | Veröffentlichen von bis zu 2 KB bei einer Größe von 50 MB  |
| **Entwicklungs-/Testumgebung**   |Basic        | Veröffentlichen von bis zu 14 KB bei einer Größe von 2 GB    |
| **Produktionsumgebung** |Standard     | Veröffentlichen von bis zu 49 KB bei einer Größe von 25 GB |

---

## <a name="recommended-settings"></a>Empfohlene Einstellungen

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker, allgemeine Verfügbarkeit (stabile Version)](#tab/v1)

|Ziel-QPS | App Service | Azure Cognitive Search |
| -------------------- | ----------- | ------------ |
| 3             | S1, 1 Replikat   | S1, 1 Replikat    |
| 50         | S3, 10 Replikate       | S1, 12 Replikate         |
| 80         | S3, 10 Replikate      |  S3, 12 Replikate  |
| 100         | P3V2, 10 Replikate  | S3, 12 Replikate, 3 Partitionen   |
| 200 bis 250         | P3V2, 20 Replikate | S3, 12 Replikate, 3 Partitionen    |

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker verwaltet (Vorschauversion)](#tab/v2)

„QnA Maker verwaltet“ ist ein kostenloser Dienst, und der Durchsatz ist derzeit auf 10 Transaktionen pro Sekunde sowohl für Verwaltungs-APIs als auch für Vorhersage-APIs begrenzt. Um 10 Transaktionen pro Sekunde für Ihren Dienst zu erreichen, empfehlen wir die SKU S1 (1 Instanz) von Azure Cognitive Search.

---

## <a name="when-to-change-a-pricing-tier"></a>Anlässe zum Ändern eines Tarifs

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker, allgemeine Verfügbarkeit (stabile Version)](#tab/v1)

|Aktualisieren|`Reason`|
|--|--|
|[Upgrade](../How-to/set-up-qnamaker-service-azure.md#upgrade-qna-maker-sku) der QnA Maker Management-SKU|Sie möchten mehr QnA-Paare oder Dokumentquellen in Ihre Wissensdatenbank aufnehmen.|
|[Upgrade](../How-to/set-up-qnamaker-service-azure.md#upgrade-app-service) der App Service-SKU, Überprüfen der Cognitive Search-Dienstebene und [Erstellen von Cognitive Search-Replikaten](../../../search/search-capacity-planning.md)|Ihre Wissensdatenbank muss mehr Anforderungen aus Ihrer Client-App verarbeiten, z.B. einen Chatbot.|
|[Upgrade](../How-to/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service) des Azure Cognitive Search-Diensts|Sie benötigen voraussichtlich eine Vielzahl von Wissensdatenbanken.|

Rufen Sie die aktuellsten Runtime-Updates ab, indem Sie [Ihren App Service im Azure-Portal aktualisieren](../how-to/set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates).

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker verwaltet (Vorschauversion)](#tab/v2)

Führen Sie ein [Upgrade](../How-to/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service) für den Azure Cognitive Search-Dienst durch, wenn Sie planen, mehrere Wissensdatenbanken zu verwenden.

---

## <a name="keys-in-qna-maker"></a>Schlüssel in QnA Maker

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker, allgemeine Verfügbarkeit (stabile Version)](#tab/v1)

Ihr QnA Maker-Dienst befasst sich mit zwei Arten von Schlüsseln: **Erstellungsschlüsseln** und **Anfrageendpunktschlüsseln**, die zusammen mit der im App Service gehosteten Runtime verwendet werden.

Verwenden Sie diese Schlüssel, wenn Sie Anforderungen an den Dienst über APIs senden.

![Schlüsselverwaltung](../media/qnamaker-how-to-key-management/key-management.png)

|Name|Standort|Zweck|
|--|--|--|
|Erstellungs-/Abonnementschlüssel|[Azure portal](https://azure.microsoft.com/free/cognitive-services/)|Diese Schlüssel werden verwendet, um auf die [QnA Maker-Verwaltungsdienst-APIs](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase) zuzugreifen. Mit diesen APIs können Sie die Fragen und Antworten in Ihrer Wissensdatenbank bearbeiten und Ihre Wissensdatenbank veröffentlichen. Diese Schlüssel werden erstellt, wenn Sie einen neuen QnA Maker-Dienst erstellen.<br><br>Sie finden diese Schlüssel in der Ressource **Cognitive Services** auf der Seite **Schlüssel**.|
|Abfrageendpunktschlüssel|[QnA Maker-Portal](https://www.qnamaker.ai)|Diese Schlüssel werden zum Abfragen des Endpunkts der veröffentlichten Wissensdatenbank verwendet, um eine Antwort auf eine Benutzerfrage abzurufen. In der Regel verwenden Sie diesen Abfrageendpunkt in Ihrem Chatbot oder im Clientanwendungscode, der eine Verbindung mit dem QnA Maker-Dienst herstellt. Diese Schlüssel werden erstellt, wenn Sie Ihre QnA Maker-Wissensdatenbank veröffentlichen.<br><br>Sie finden diese Schlüssel auf der Seite **Diensteinstellungen**. Suchen Sie diese Seite im Menü des Benutzers in der oberen rechten Ecke der Seite im Dropdownmenü.|

### <a name="find-authoring-keys-in-the-azure-portal"></a>Suchen von Erstellungsschlüsseln im Azure-Portal

Sie können Ihre Erstellungsschlüssel in dem Azure-Portal anzeigen und zurücksetzen, in dem Sie die QnA Maker-Ressource erstellt haben. Diese Schlüssel können auch als Abonnementschlüssel bezeichnet werden.

1. Wechseln Sie im Azure-Portal zur QnA Maker-Ressource, und wählen Sie die Ressource mit dem _Cognitive Services_-Typ aus:

    ![QnA Maker-Ressourcenliste](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. Wechseln Sie zu **Schlüssel und Endpunkt**:

    ![QnA Maker verwaltet (Vorschau)-Abonnementschlüssel](../media/qnamaker-how-to-key-management/subscription-key-v2.png)

### <a name="find-query-endpoint-keys-in-the-qna-maker-portal"></a>Suchen von Abfrageendpunktschlüsseln im QnA Maker-Portal

Der Endpunkt befindet sich in demselben Bereich wie die Ressource, da die Endpunktschlüssel verwendet werden, um einen Aufruf an die Wissensdatenbank zu senden.

Endpunktschlüssel können im [QnA Maker-Portal](https://qnamaker.ai) verwaltet werden.

1. Melden Sie sich beim [QnA Maker-Portal](https://qnamaker.ai) an, wechseln Sie zu Ihrem Profil, und wählen Sie **Diensteinstellungen** aus:

    ![Endpunktschlüssel](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. Zeigen Sie Ihre Schlüssel an, oder setzen Sie die Schlüssel zurück:

    > [!div class="mx-imgBorder"]
    > ![Endpunktschlüssel-Manager](../media/qnamaker-how-to-key-management/Endpoint-keys1.png)

    >[!NOTE]
    >Aktualisieren Sie Ihre Schlüssel, wenn Sie denken, dass sie gefährdet sind. Dazu müssen möglicherweise entsprechende Änderungen an Ihrem Clientanwendungs- oder Botcode vorgenommen werden.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker verwaltet (Vorschauversion)](#tab/v2)

Ihr QnA Maker verwaltet-Dienst (Vorschau) befasst sich mit zwei Arten von Schlüsseln: **Erstellungsschlüssel** und **Azure Cognitive Search-Schlüssel**, die für den Zugriff auf den Dienst im Abonnement des Kunden verwendet werden.

Verwenden Sie diese Schlüssel, wenn Sie Anforderungen an den Dienst über APIs senden.

![Schlüsselverwaltung: Verwaltete Vorschau](../media/qnamaker-how-to-key-management/qnamaker-v2-key-management.png)

|Name|Standort|Zweck|
|--|--|--|
|Erstellungs-/Abonnementschlüssel|[Azure portal](https://azure.microsoft.com/free/cognitive-services/)|Diese Schlüssel werden verwendet, um auf die [QnA Maker-Verwaltungsdienst-APIs](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase) zuzugreifen. Mit diesen APIs können Sie die Fragen und Antworten in Ihrer Wissensdatenbank bearbeiten und Ihre Wissensdatenbank veröffentlichen. Diese Schlüssel werden erstellt, wenn Sie einen neuen QnA Maker-Dienst erstellen.<br><br>Sie finden diese Schlüssel in der Ressource **Cognitive Services** auf der Seite **Schlüssel**.|
|Administratorschlüssel von Azure Cognitive Search|[Azure portal](../../../search/search-security-api-keys.md)|Diese Schlüssel werden verwendet, um mit dem Azure Cognitive Search-Dienst zu kommunizieren, der im Azure-Abonnement des Benutzers bereitgestellt wird. Wenn Sie Azure Cognitive Search mit dem QnA Maker verwaltet-Dienst (Vorschau) verknüpfen, wird der Administratorschlüssel automatisch an den QnA Maker-Dienst weitergeleitet. <br><br>Sie finden diese Schlüssel in der **Azure Cognitive Search**-Ressource auf der Seite **Schlüssel**.|

### <a name="find-authoring-keys-in-the-azure-portal"></a>Suchen von Erstellungsschlüsseln im Azure-Portal

Sie können Ihre Erstellungsschlüssel in dem Azure-Portal anzeigen und zurücksetzen, in dem Sie die QnA Maker verwaltet (Vorschau)-Ressource erstellt haben. Diese Schlüssel können auch als Abonnementschlüssel bezeichnet werden.

1. Wechseln Sie im Azure-Portal zur QnA Maker verwaltet (Vorschau)-Ressource, und wählen Sie die Ressource mit dem *Cognitive Services*-Typ aus:

    ![QnA Maker verwaltet (Vorschau)-Ressourcenliste](../media/qnamaker-how-to-key-management/qnamaker-v2-resource-list.png)

2. Wechseln Sie zu **Schlüssel und Endpunkt**:

    ![QnA Maker verwaltet (Vorschau)-Abonnementschlüssel](../media/qnamaker-how-to-key-management/subscription-key-v2.png)

### <a name="update-the-resources"></a>Aktualisieren der Ressourcen

Erfahren Sie, wie Sie die von Ihrer Wissensdatenbank genutzten Ressourcen aktualisieren können. QnA Maker verwaltet (Vorschau) ist während der Vorschauphase **kostenlos**. 

---

## <a name="management-service-region"></a>Verwaltungsdienstregion

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker, allgemeine Verfügbarkeit (stabile Version)](#tab/v1)

Der Verwaltungsdienst von QnA Maker wird nur für das QnA Maker-Portal und für die anfängliche Datenverarbeitung genutzt. Dieser Dienst ist nur in der Region **USA, Westen** verfügbar. In diesem Dienst (USA, Westen) werden keine Kundendaten gespeichert.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker verwaltet (Vorschauversion)](#tab/v2)

Bei QnA Maker verwaltet (Vorschau) befinden sich sowohl die Verwaltungs- als auch die Vorhersage-Dienste in derselben Region. Derzeit ist QnA Maker verwaltet (Vorschau) in **USA (Süden-Mitte), Europa (Norden) und Australien (Osten)** verfügbar.

---

## <a name="resource-naming-considerations"></a>Überlegungen zu Ressourcennamen

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker, allgemeine Verfügbarkeit (stabile Version)](#tab/v1)

Der Ressourcenname für die QnA Maker-Ressource, wie etwa `qna-westus-f0-b`, wird auch zum Benennen der weiteren Ressourcen verwendet.

Im Erstellen-Fenster des Azure-Portals können Sie eine QnA Maker-Ressource erstellen und die Tarife für die weiteren Ressourcen auswählen.

> [!div class="mx-imgBorder"]
> ![Screenshot des Azure-Portals für die Erstellung von QnA Maker-Ressourcen](../media/concept-azure-resource/create-blade.png)

Nach dem Erstellen haben die Ressourcen den gleichen Namen, mit Ausnahme der optionalen Application Insights-Ressource, die dem Namen Zeichen nachstellt.

> [!div class="mx-imgBorder"]
> ![Screenshot der Ressourcenauflistung im Azure-Portal](../media/concept-azure-resource/all-azure-resources-created-qnamaker.png)

> [!TIP]
> Erstellen Sie eine neue Ressourcengruppe, wenn Sie eine QnA Maker Ressource erstellen. Auf diese Weise können Sie beim Suchen nach der Ressourcengruppe alle Ressourcen anzeigen, die der QnA Maker Ressource zugeordnet sind.

> [!TIP]
> Verwenden Sie eine Benennungskonvention, die Tarifstufen im Namen der Ressource oder Ressourcengruppe anzeigt. Wenn beim Erstellen einer neuen Wissensdatenbank oder beim Hinzufügen neuer Dokumente Fehler angezeigt werden, ist der Grenzwert des Cognitive Search-Tarifs ein häufiges Problem.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker verwaltet (Vorschauversion)](#tab/v2)

Der Ressourcenname für die QnA Maker verwaltet-Ressource (Vorschau), wie etwa `qna-westus-f0-b`, wird auch zum Benennen der weiteren Ressourcen verwendet.

Im Erstellen-Fenster des Azure-Portals können Sie eine QnA Maker verwaltet-Ressource (Vorschau) erstellen und die Tarife für die weiteren Ressourcen auswählen.

> [!div class="mx-imgBorder"]
> ![Screenshot: Azure-Portal für die Erstellung der QnA Maker verwaltet-Ressource (Vorschau)](../media/qnamaker-how-to-setup-service/enter-qnamaker-v2-info.png) Nachdem die Ressourcen erstellt wurden, weisen sie denselben Namen auf.

> [!div class="mx-imgBorder"]
> ![Screenshot: Azure-Portalressource, die QnA Maker verwaltet (Vorschau) auflistet](../media/qnamaker-how-to-setup-service/resources-created-v2.png)

> [!TIP]
> Erstellen Sie eine neue Ressourcengruppe, wenn Sie eine QnA Maker Ressource erstellen. Auf diese Weise können Sie beim Suchen nach der Ressourcengruppe alle Ressourcen anzeigen, die der QnA Maker verwaltet-Ressource (Vorschau) zugeordnet sind.

> [!TIP]
> Verwenden Sie eine Benennungskonvention, die Tarifstufen im Namen der Ressource oder Ressourcengruppe anzeigt. Wenn beim Erstellen einer neuen Wissensdatenbank oder beim Hinzufügen neuer Dokumente Fehler angezeigt werden, ist der Grenzwert des Cognitive Search-Tarifs ein häufiges Problem.

---

## <a name="resource-purposes"></a>Zwecke von Ressourcen

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker, allgemeine Verfügbarkeit (stabile Version)](#tab/v1)

Jede Azure-Ressource, die mit QnA Maker erstellt wird, hat einen bestimmten Zweck:

* QnA Maker-Ressource
* Cognitive Search-Ressource
* App Service
* App-Plandienst
* Application Insights-Dienst

### <a name="qna-maker-resource"></a>QnA Maker-Ressource

Die QnA Maker-Ressource bietet zur Laufzeit Zugriff auf die Erstellungs- und Veröffentlichungs-APIs sowie auf die zweite Bewertungsschicht (Bewerter Nr. 2) der QnA-Paare, die auf natürlichsprachlicher Verarbeitung (Natural Language Processing, NLP) beruht.

Die zweite Bewertung wendet intelligente Filter an, die Metadaten und Folgeaufforderungen beinhalten können.

#### <a name="qna-maker-resource-configuration-settings"></a>Ressourcenkonfigurationseinstellungen für QnA Maker

Wenn Sie eine neue Wissensdatenbank im [QnA Maker-Portal](https://qnamaker.ai) erstellen, ist die Einstellung für **Sprache** die einzige Einstellung, die auf der Ressourcenebene angewendet wird. Sie wählen die Sprache aus, wenn Sie die erste Wissensdatenbank für die Ressource erstellen.

### <a name="cognitive-search-resource"></a>Cognitive Search-Ressource

Die [Cognitive Search](../../../search/index.yml)-Ressource wird für diese Zwecke verwendet:

* Speichern der QnA-Paare
* Angeben der anfänglichen Rangfolge (Bewerter Nr. 1) der QnA-Paare zur Laufzeit

#### <a name="index-usage"></a>Indexnutzung

Die Ressource pflegt einen Index, der als Testindex fungiert, und die verbleibenden Indizes korrelieren jeweils zu einer veröffentlichten Wissensdatenbank.

Eine Ressource, die nach ihrem Preis 15 Indizes unterhalten kann, enthält 14 veröffentlichte Wissensdatenbanken, während ein Index zum Testen aller Wissensdatenbanken verwendet wird. Dieser Testindex ist nach Wissensdatenbanken partitioniert, sodass eine Abfrage, die den interaktiven Testbereich verwendet, den Testindex verwendet, jedoch nur Ergebnisse aus der spezifischen Partition zurückgibt, die der spezifischen Wissensdatenbank zugeordnet ist.

#### <a name="language-usage"></a>Sprachverwendung

Die erste Wissensdatenbank, die in der QnA Maker-Ressource erstellt wird, wird verwendet, um den _einzelnen_ Sprachsatz zu bestimmen, der für die Cognitive Search-Ressource und alle zugehörigen Indizes festgelegt ist. Es kann nur _ein Sprachsatz_ für einen QnA Maker-Dienst festgelegt werden.

#### <a name="using-a-single-cognitive-search-service"></a>Verwenden eines einzelnen Cognitive Search-Diensts

Wenn Sie einen QnA-Dienst und dessen Abhängigkeiten (z.B. Search) über das Portal erstellen, wird automatisch ein Suchdienst erstellt und mit dem QnA Maker-Dienst verknüpft. Nachdem diese Ressourcen erstellt wurden, können Sie die App Service-Einstellung aktualisieren, um einen bereits vorhandenen Suchdienst zu nutzen und den soeben erstellten Suchdienst zu entfernen.

Erfahren Sie, wie Sie QnA Maker [so konfigurieren](../How-To/set-up-qnamaker-service-azure.md#configure-qna-maker-to-use-different-cognitive-search-resource), dass er eine andere Cognitive Service-Ressource als die verwendet, die im Rahmen des Ressourcenerstellungsvorgangs für QnA Maker erstellt wurde.

### <a name="app-service-and-app-service-plan"></a>App Service und App Service-Plan

Der [App Service](../../../app-service/index.yml) wird von Ihrer Clientanwendung verwendet, um über den Laufzeitendpunkt auf die veröffentlichten Wissensdatenbanken zuzugreifen.

Um die veröffentlichte Wissensdatenbank abzufragen, verwenden alle veröffentlichten Wissensdatenbanken denselben URL-Endpunkt, geben aber die **Wissensdatenbank-ID** in der Route an.

`{RuntimeEndpoint}/qnamaker/knowledgebases/{kbId}/generateAnswer`

### <a name="application-insights"></a>Application Insights

[Application Insights](../../../azure-monitor/app/app-insights-overview.md) wird verwendet, um Chatprotokolle und Telemetriedaten zu erfassen. Verwenden Sie die allgemeinen [Kusto-Abfragen](../how-to/get-analytics-knowledge-base.md), um Informationen zu Ihrem Dienst zu erhalten.

### <a name="share-services-with-qna-maker"></a>Freigeben von Diensten mit QnA Maker

QnA Maker erstellt verschiedene Azure-Ressourcen. Wenn Sie den Verwaltungsaufwand reduzieren und von der Kostenteilung profitieren möchten, informieren Sie sich anhand der folgenden Tabelle, was Sie freigeben können und was nicht:

|Dienst|Freigabe|`Reason`|
|--|--|--|
|Cognitive Services|X|Programmbedingt nicht möglich.|
|App Service-Plan|✔|Der für einen App Service-Plan zugewiesene Speicherplatz auf dem Datenträger. Wenn andere Apps, die denselben App Service-Plan gemeinsam nutzen, eine erhebliche Menge an Speicherplatz belegen, treten bei der QnAMaker App Service-Instanz Probleme auf.|
|App Service|X|Programmbedingt nicht möglich.|
|Application Insights|✔|Gemeinsame Nutzung möglich.|
|Suchdienst|✔|1. `testkb` ist ein reservierter Name für den QnAMaker-Dienst, der von anderen Benutzern nicht verwendet werden kann.<br>2. Eine Synonymzuordnung nach dem Namen `synonym-map` ist für den QnAMaker-Dienst reserviert.<br>3. Die Anzahl von veröffentlichten Wissensdatenbanken wird durch den Suchdiensttarif begrenzt. Wenn freie Indizes verfügbar sind, können sie von anderen Diensten genutzt werden.|

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker verwaltet (Vorschauversion)](#tab/v2)

Jede Azure-Ressource, die mit QnA Maker verwaltet (Vorschau) erstellt wird, hat einen bestimmten Zweck.

* QnA Maker-Ressource
* Cognitive Search-Ressource

### <a name="qna-maker-resource"></a>QnA Maker-Ressource

Die QnA Maker verwaltet-Ressource (Vorschau) bietet Zugriff auf die Erstellungs- und Veröffentlichungs-APIs, hostet die Rangfolgeruntime und stellt die Telemetrie bereit.

### <a name="azure-cognitive-search-resource"></a>Azure Cognitive Search-Ressource

Die [Cognitive Search](../../../search/index.yml)-Ressource wird für diese Zwecke verwendet:

* Speichern der QnA-Paare
* Angeben der anfänglichen Rangfolge (Bewerter Nr. 1) der QnA-Paare zur Laufzeit

#### <a name="index-usage"></a>Indexnutzung

Sie können N-1 Wissensdatenbanken einer einzelnen Sprache oder N/2 Wissensdatenbanken verschiedener Sprachen einer bestimmten Ebene veröffentlichen, wobei N die maximal zulässigen Indizes auf der Azure Cognitive Search-Ebene angibt. Überprüfen Sie außerdem die maximale Größe und Anzahl der in den einzelnen Tarifen zulässigen Dokumente.

Wenn Ihr Tarif z. B. 15 zulässige Indizes aufweist, können Sie 14 Wissensdatenbanken derselben Sprache veröffentlichen (1 Index pro veröffentlichter Wissensdatenbank). Der fünfzehnte Index wird für alle Wissensdatenbanken zum Erstellen und Testen verwendet. Wenn Sie sich für Wissensdatenbanken in verschiedenen Sprachen entscheiden, können Sie nur sieben Wissensdatenbanken veröffentlichen.

#### <a name="language-usage"></a>Sprachverwendung

Mit QnA Maker verwaltet (Vorschau) haben Sie die Wahl, Ihren QnA Maker-Dienst für Wissensdatenbanken in einer einzelnen oder in mehreren Sprachen einzurichten. Sie treffen diese Wahl bei der Erstellung der ersten Wissensdatenbank in Ihrem QnA Maker-Dienst. Weitere Informationen zum Aktivieren der Spracheinstellung pro Wissensdatenbank finden Sie [hier](#pricing-tier-considerations).

---

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über die QnA Maker-[Wissensdatenbank](../index.yml)
* Grundlegendes zum [Lebenszyklus von Wissensdatenbanken](development-lifecycle-knowledge-base.md)
* [Grenzwerte](../limits.md) von Überprüfungsdienst und Wissensdatenbanken
