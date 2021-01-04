---
title: Benutzerdefinierter Skill „AML“ in Skillsets
titleSuffix: Azure Cognitive Search
description: Erweitern Sie die Funktionen von Azure Cognitive Search-Skillsets durch Azure Machine Learning-Modelle.
manager: nitinme
author: mattmsft
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/12/2020
ms.openlocfilehash: d1e6f4e16e3eda8519913a9e2ae14f7cc909bf61
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94445454"
---
# <a name="aml-skill-in-an-azure-cognitive-search-enrichment-pipeline"></a>AML-Skill in einer Anreicherungspipeline von Azure Cognitive Search

> [!IMPORTANT] 
> Diese Qualifikation ist zurzeit als öffentliche Vorschauversion verfügbar. Die Vorschaufunktion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Derzeit wird das .NET SDK nicht unterstützt.

Mit dem **AML** -Skill können Sie die KI-Anreicherung durch ein benutzerdefiniertes AML-Modell ( [Azure Machine Learning](../machine-learning/overview-what-is-azure-ml.md)) erweitern. Nachdem ein AML-Modell [trainiert und bereitgestellt](../machine-learning/concept-azure-machine-learning-architecture.md#workspace) wurde, integriert ein **AML** -Skill dieses in die KI-Anreicherung.

Wie integrierte Skills weist ein **AML** -Skill Eingaben und Ausgaben auf. Die Eingaben werden als JSON-Objekt an den bereitgestellten AML-Dienst gesendet, das eine JSON-Nutzlast als Antwort zusammen mit einem Erfolgsstatuscode ausgibt. Es wird erwartet, dass die Antwort die von Ihrem Skill **AML** angegebenen Ergebnisse aufweist. Jede andere Antwort gilt als Fehler und es werden keine Anreicherung durchgeführt.

> [!NOTE]
> Der Indexer versucht zwei Mal, bestimmte Standard-HTTP-Statuscodes, die vom AML-Dienst zurückgegeben werden, erneut zu ermitteln. Diese HTTP-Statuscodes lauten:
> * `503 Service Unavailable`
> * `429 Too Many Requests`

## <a name="prerequisites"></a>Voraussetzungen

* Ein [AML-Arbeitsbereich](../machine-learning/concept-workspace.md)
* Eine [Azure Kubernetes Service-AML-Computeziel](../machine-learning/concept-compute-target.md) in diesem Arbeitsbereich mit einem [bereitgestellten Modell](../machine-learning/how-to-deploy-azure-kubernetes-service.md)
  * Für das [Computeziel sollte SSL aktiviert sein](../machine-learning/how-to-secure-web-service.md#deploy-on-aks-and-field-programmable-gate-array-fpga). Azure Cognitive Search ermöglicht nur Zugriff auf **HTTPS** -Endpunkte.
  * Selbstsignierte Zertifikate können nicht verwendet werden.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Custom.AmlSkill

## <a name="skill-parameters"></a>Skillparameter

Bei den Parametern wird zwischen Groß- und Kleinschreibung unterschieden. Welche Parameter Sie verwenden, hängt davon ab, welche [Authentifizierung der AML-Dienst ggf. erfordert](#WhatSkillParametersToUse).

| Parametername | BESCHREIBUNG |
|--------------------|-------------|
| `uri` | (Erforderlich für [keine Authentifizierung oder Schlüsselauthentifizierung](#WhatSkillParametersToUse)) Der [Bewertungs-URI des AML-Diensts](../machine-learning/how-to-consume-web-service.md), an den die _JSON_ -Nutzlast gesendet wird. Nur das **HTTPS** -URI-Schema ist zulässig. |
| `key` | (Erforderlich für [Schlüsselauthentifizierung](#WhatSkillParametersToUse)) Der [Schlüssel für den AML-Dienst](../machine-learning/how-to-consume-web-service.md#authentication-with-keys). |
| `resourceId` | (Erforderlich für [Tokenauthentifizierung](#WhatSkillParametersToUse)). Die Azure Resource Manager-Ressourcen-ID des AML-Diensts. Er sollte im Format „subscriptions/{GUID}/resourceGroups/{Ressourcengruppenname}/Microsoft.MachineLearningServices/workspaces/{Workspacename}/services/{Dienstname} vorliegen. |
| `region` | (Optional für [Tokenauthentifizierung](#WhatSkillParametersToUse)). Die [Region](https://azure.microsoft.com/global-infrastructure/regions/), in der der AML-Dienst bereitgestellt wird. |
| `timeout` | (Optional) Wenn angegeben, wird damit das Zeitlimit für den HTTP-Client angegeben, der den API-Aufruf durchführt. Es muss als XSD-Wert „dayTimeDuration“ formatiert sein (eine eingeschränkte Teilmenge eines [ISO 8601-Zeitwerts](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration)). Zum Beispiel `PT60S` für 60 Sekunden. Wenn kein Wert festgelegt ist, wird ein Standardwert von 30 Sekunden ausgewählt. Das Zeitlimit kann auf maximal 230 Sekunden und mindestens 1 Sekunde festgelegt werden. |
| `degreeOfParallelism` | (Optional) Wenn angegeben, wird die Anzahl der Aufrufe angezeigt, die der Indexer parallel zum von Ihnen bereitgestellten Endpunkt vornimmt. Sie können diesen Wert herab setzen, wenn der Endpunkt bei einer zu hohen Anforderungslast ausfällt, oder herauf setzen, wenn der Endpunkt mehr Anforderungen verarbeiten kann und Sie die Leistung des Indexers erhöhen möchten.  Wenn kein Wert festgelegt ist, wird ein Standardwert von 5 verwendet. „degreeOfParallelism“ kann auf maximal 10 und mindestens 1 festgelegt werden.

<a name="WhatSkillParametersToUse"></a>

## <a name="what-skill-parameters-to-use"></a>Zu verwendende Skillparameter

Welche AML-Skillparameter erforderlich sind, hängt davon ab, welche Authentifizierung der AML-Dienst ggf. erfordert. AML-Dienste bieten drei Authentifizierungsoptionen:

* [Schlüsselbasierte Authentifizierung](../machine-learning/how-to-authenticate-web-service.md#key-based-authentication). Ein statischer Schlüssel wird bereitgestellt, um Bewertungsanforderungen von AML-Skills zu authentifizieren.
  * Verwenden Sie die Parameter _uri_ und _key_.
* [Tokenbasierte Authentifizierung](../machine-learning/how-to-authenticate-web-service.md#token-based-authentication). Der AML-Dienst wird [mithilfe von tokenbasierter Authentifizierung](../machine-learning/how-to-authenticate-web-service.md#token-based-authentication) bereitgestellt. Der [verwalteten Identität](../active-directory/managed-identities-azure-resources/overview.md) des Azure Cognitive Search-Diensts wird die [Leserrolle](../machine-learning/how-to-assign-roles.md) im Arbeitsbereich des AML-Diensts erteilt. Der AML-Skill verwendet dann die verwaltete Identität des Azure Cognitive Search-Diensts für die Authentifizierung mit dem AML-Dienst, ohne dass statische Schlüssel erforderlich sind.
  * Verwenden Sie den _resourceId_ -Parameter.
  * Wenn sich der Azure Cognitive Search-Dienst in einer anderen Region als der AML-Arbeitsbereich befindet, verwenden Sie den Parameter _region_ , um die Region festzulegen, in der der AML-Dienst bereitgestellt wurde.
* Keine Authentifizierung. Es ist keine Authentifizierung erforderlich, um den AML-Dienst zu verwenden.
  * Verwenden Sie den _uri_ -Parameter.

## <a name="skill-inputs"></a>Skilleingaben

Es gibt keine „vordefinierten“ Eingaben für diese Qualifikation. Sie können mindestens ein Feld auswählen, das zum Zeitpunkt der Ausführung dieses Skills bereits verfügbar ist, da Eingaben und die an den AML-Dienst gesendete _JSON_ -Nutzlast unterschiedliche Felder aufweisen.

## <a name="skill-outputs"></a>Skillausgaben

Es gibt keine „vordefinierten“ Ausgaben für diese Qualifikation. Abhängig von der Antwort, die Ihr AML-Dienst zurückgibt, fügen Sie Ausgabefelder hinzu, damit sie von der _JSON_ -Antwort übernommen werden können.

## <a name="sample-definition"></a>Beispieldefinition

```json
  {
    "@odata.type": "#Microsoft.Skills.Custom.AmlSkill",
    "description": "A sample model that detects the language of sentence",
    "uri": "https://contoso.count-things.com/score",
    "context": "/document",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "detected_language_code"
      }
    ]
  }
```

## <a name="sample-input-json-structure"></a>Beispiel für eine Eingabe-JSON-Struktur

Diese _JSON_ -Struktur stellt die Nutzlast dar, die an Ihren AML-Dienst gesendet wird. Die Felder der Struktur auf oberster Ebene entsprechen den im Abschnitt `inputs` der Skilldefinition angegebenen „Namen“. Der Wert dieser Felder ergibt sich aus der `source` dieser Felder (die aus einem Feld im Dokument oder möglicherweise aus einer anderen Qualifikation stammen kann).

```json
{
  "text": "Este es un contrato en Inglés"
}
```

## <a name="sample-output-json-structure"></a>Beispiel für eine Ausgabe-JSON-Struktur

Die „Ausgabe“ entspricht der Antwort, die von Ihrem AML-Dienst zurückgegebenen wird. Der AML-Dienst sollte nur eine _JSON_ -Nutzlast zurückgeben (verifiziert durch Untersuchen des `Content-Type`-Antwortheaders) und sollte ein Objekt sein, bei dem die Felder Anreicherungen sind, die mit den „Namen“ in `output` übereinstimmen und deren Wert als die Anreicherung angesehen wird.

```json
{
    "detected_language_code": "es"
}
```

## <a name="inline-shaping-sample-definition"></a>Definition des Inlinestrukturierungsbeispiels

```json
  {
    "@odata.type": "#Microsoft.Skills.Custom.AmlSkill",
    "description": "A sample model that detects the language of sentence",
    "uri": "https://contoso.count-things.com/score",
    "context": "/document",
    "inputs": [
      {
        "name": "shapedText",
        "sourceContext": "/document",
        "inputs": [
            {
              "name": "content",
              "source": "/document/content"
            }
        ]
      }
    ],
    "outputs": [
      {
        "name": "detected_language_code"
      }
    ]
  }
```

## <a name="inline-shaping-input-json-structure"></a>JSON-Eingabestruktur der Inlinestrukturierung

```json
{
  "shapedText": { "content": "Este es un contrato en Inglés" }
}
```

## <a name="inline-shaping-sample-output-json-structure"></a>JSON-Ausgabestruktur des Inlinestrukturierungsbeispiels

```json
{
    "detected_language_code": "es"
}
```

## <a name="error-cases"></a>Auftretende Fehler
Neben der Tatsache, dass AML nicht verfügbar ist oder keine erfolgreichen Statuscodes gesendet werden, gelten die folgenden Fälle als Fehlerfälle:

* Wenn der AML-Dienst einen Statuscode für den Erfolg zurückgibt, die Antwort aber anzeigt, dass er nicht `application/json` ist, dann wird die Antwort als ungültig betrachtet, und es werden keine Anreicherungen durchgeführt.
* Wenn der AML-Dienst ungültiges JSON zurückgibt.

In Fällen, in denen der AML-Dienst nicht verfügbar ist oder einen HTTP-Fehler zurückgibt, wird eine Fehlermeldung mit allen verfügbaren Details zum HTTP-Fehler in den Ausführungsverlauf des Indexers aufgenommen.

## <a name="see-also"></a>Weitere Informationen

+ [Definieren eines Skillsets](cognitive-search-defining-skillset.md)
+ [Problembehandlung des AML-Diensts](../machine-learning/how-to-troubleshoot-deployment.md)