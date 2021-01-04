---
title: include file
description: include file
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: cog-serv-seo-aug-2020
ms.date: 08/25/2020
ms.openlocfilehash: 791e46e73ad7292528f0197f8424d44486ea9795
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2020
ms.locfileid: "94371929"
---
[Referenzdokumentation](/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer?view=azure-python) | [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-personalizer) | [Paket (pypi)](https://pypi.org/project/azure-cognitiveservices-personalizer/) | [Beispiele](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/Personalizer)

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/cognitive-services)
* [Python 3.x](https://www.python.org/)
* Sobald Sie über Ihr Azure-Abonnement verfügen, sollten Sie über <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer"  title="Erstellen einer Personalisierungsressource"  target="_blank"> im Azure-Portal eine Personalisierungsressource <span class="docon docon-navigate-external x-hidden-focus"></span></a> erstellen, um Ihren Schlüssel und Endpunkt abzurufen. Klicken Sie nach Abschluss der Bereitstellung auf **Zu Ressource wechseln**.
    * Sie benötigen den Schlüssel und Endpunkt der von Ihnen erstellten Ressource, um Ihre Anwendung mit der Personalisierungs-API zu verbinden. Der Schlüssel und der Endpunkt werden weiter unten in der Schnellstartanleitung in den Code eingefügt.
    * Sie können den kostenlosen Tarif (`F0`) verwenden, um den Dienst zu testen, und später für die Produktion auf einen kostenpflichtigen Tarif upgraden.

## <a name="setting-up"></a>Einrichten

[!INCLUDE [Change model frequency](change-model-frequency.md)]

### <a name="install-the-client-library"></a>Installieren der Clientbibliothek

Nach der Installation von Python, können Sie die Clientbibliothek mit Folgendem installieren:

```console
pip install azure-cognitiveservices-personalizer
```

### <a name="create-a-new-python-application"></a>Erstellen einer neuen Python-Anwendung

Erstellen Sie eine neue Python-Datei sowie Variablen für den Endpunkt und den Abonnementschlüssel Ihrer Ressource.

[!INCLUDE [Personalizer find resource info](find-azure-resource-info.md)]

```python
from azure.cognitiveservices.personalizer import PersonalizerClient
from azure.cognitiveservices.personalizer.models import RankableAction, RewardRequest, RankRequest
from msrest.authentication import CognitiveServicesCredentials

import datetime, json, os, time, uuid

key = "<paste-your-personalizer-key-here>"
endpoint = "<paste-your-personalizer-endpoint-here>"
```

## <a name="object-model"></a>Objektmodell

Der Personalisierungsclient ist ein Objekt vom Typ [PersonalizerClient](/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer.personalizer_client.personalizerclient?view=azure-python), das mithilfe der Klasse „Microsoft.Rest.ServiceClientCredentials“, die Ihren Schlüssel enthält, bei Azure authentifiziert wird.

Erstellen Sie zum Anfordern des besten Inhaltselements eine Rangfolgeanforderung ([RankRequest](/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer.models.rankrequest?view=azure-python)), und übergeben Sie sie an die Methode „client.Rank“. Die Methode „Rank“ gibt eine Rangfolgeantwort (RankResponse) zurück.

Legen Sie die Ereignis-ID und die Relevanzbewertung (Wert) fest, die an die Methode [Reward](/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer.operations.events_operations.eventsoperations?view=azure-python#reward-event-id--value--custom-headers-none--raw-false----operation-config-) der Klasse „EventOperations“ gesendet werden sollen, um eine Relevanzbewertung an die Personalisierung zu senden.

Im Rahmen dieser Schnellstartanleitung ist die Bestimmung der Relevanz ganz einfach. In einem Produktionssystem kann die Bestimmung der Einflussfaktoren für die [Relevanzbewertung](../concept-rewards.md) sowie deren jeweilige Gewichtung allerdings eine komplexe Angelegenheit sein und muss unter Umständen im Laufe der Zeit überarbeitet werden. Hierbei handelt es sich um eine der wichtigsten Entwurfsentscheidungen im Zusammenhang mit Ihrer Personalisierungsarchitektur.

## <a name="code-examples"></a>Codebeispiele

In diesen Codeausschnitten werden folgende Vorgänge mit der Personalisierungsclientbibliothek für Python veranschaulicht:

* [Authentifizieren des Clients](#authenticate-the-client)
* [Rangfolge-API](#request-the-best-action)
* [Relevanz-API](#send-a-reward)

## <a name="authenticate-the-client"></a>Authentifizieren des Clients

Instanziieren Sie `PersonalizerClient` mit den zuvor erstellten Elementen `key` und `endpoint`.

```python
# Instantiate a Personalizer client
client = PersonalizerClient(endpoint, CognitiveServicesCredentials(key))
```

## <a name="get-content-choices-represented-as-actions"></a>Abrufen von Inhaltsoptionen, dargestellt als Aktionen

Aktionen stellen die Inhaltsoptionen dar, aus denen die Personalisierung das beste Inhaltselement auswählen soll. Fügen Sie der Klasse „Program“ die folgenden Methoden hinzu, um die Gruppe der Aktionen und deren Features darzustellen.

```python
def get_actions():
    action1 = RankableAction(id='pasta', features=[{"taste":"salty", "spice_level":"medium"},{"nutrition_level":5,"cuisine":"italian"}])
    action2 = RankableAction(id='ice cream', features=[{"taste":"sweet", "spice_level":"none"}, { "nutritional_level": 2 }])
    action3 = RankableAction(id='juice', features=[{"taste":"sweet", 'spice_level':'none'}, {'nutritional_level': 5}, {'drink':True}])
    action4 = RankableAction(id='salad', features=[{'taste':'salty', 'spice_level':'none'},{'nutritional_level': 2}])
    return [action1, action2, action3, action4]
```

```python
def get_user_timeofday():
    res={}
    time_features = ["morning", "afternoon", "evening", "night"]
    time = input("What time of day is it (enter number)? 1. morning 2. afternoon 3. evening 4. night\n")
    try:
        ptime = int(time)
        if(ptime<=0 or ptime>len(time_features)):
            raise IndexError
        res['time_of_day'] = time_features[ptime-1]
    except (ValueError, IndexError):
        print("Entered value is invalid. Setting feature value to", time_features[0] + ".")
        res['time_of_day'] = time_features[0]
    return res
```

```python
def get_user_preference():
    res = {}
    taste_features = ['salty','sweet']
    pref = input("What type of food would you prefer? Enter number 1.salty 2.sweet\n")
    
    try:
        ppref = int(pref)
        if(ppref<=0 or ppref>len(taste_features)):
            raise IndexError
        res['taste_preference'] = taste_features[ppref-1]
    except (ValueError, IndexError):
        print("Entered value is invalid. Setting feature value to", taste_features[0]+ ".")
        res['taste_preference'] = taste_features[0]
    return res
```

## <a name="create-the-learning-loop"></a>Erstellen der Lernschleife

Die Lernschleife der Personalisierung ist ein Zyklus aus [Rangfolge-](#request-the-best-action) und [Relevanzaufrufen](#send-a-reward). In dieser Schnellstartanleitung folgt auf jeden Rangfolgeaufruf zur Personalisierung des Inhalts ein Relevanzaufruf, um der Personalisierung mitzuteilen, wie gut der Dienst abgeschnitten hat.

Der folgende Code durchläuft einen Zyklus in Form einer Schleife: Der Benutzer wird an der Befehlszeile nach seinen Präferenzen befragt, die Angaben werden zur Wahl der besten Aktion an die Personalisierung gesendet, die Auswahl wird dem Kunden in einer Auswahlliste angezeigt, und anschließend wird eine Relevanz an die Personalisierung gesendet, die angibt, wie gut die Wahl des Diensts war.

```python
keep_going = True
while keep_going:

    eventid = str(uuid.uuid4())

    context = [get_user_preference(), get_user_timeofday()]
    actions = get_actions()

    rank_request = RankRequest( actions=actions, context_features=context, excluded_actions=['juice'], event_id=eventid)
    response = client.rank(rank_request=rank_request)
    
    print("Personalizer service ranked the actions with the probabilities listed below:")
    
    rankedList = response.ranking
    for ranked in rankedList:
        print(ranked.id, ':',ranked.probability)

    print("Personalizer thinks you would like to have", response.reward_action_id+".")
    answer = input("Is this correct?(y/n)\n")[0]

    reward_val = "0.0"
    if(answer.lower()=='y'):
        reward_val = "1.0"
    elif(answer.lower()=='n'):
        reward_val = "0.0"
    else:
        print("Entered choice is invalid. Service assumes that you didn't like the recommended food choice.")

    client.events.reward(event_id=eventid, value=reward_val)

    br = input("Press Q to exit, any other key to continue: ")
    if(br.lower()=='q'):
        keep_going = False
```

Fügen Sie vor dem Ausführen der Codedatei die folgenden Methoden hinzu, die die [Inhaltsoptionen abrufen](#get-content-choices-represented-as-actions):

* `get_user_preference`
* `get_user_timeofday`
* `get_actions`

## <a name="request-the-best-action"></a>Anfordern der besten Aktion

Für die Rangfolgeanforderung erfragt das Programm die Präferenzen des Benutzers, um für die Inhaltsoptionen ein Element vom Typ `currentContent` zu erstellen. Der Prozess kann Inhalte erstellen, die von den Aktionen ausgeschlossen werden sollen (angezeigt als `excludeActions`). Die Rangfolgeanforderung muss die Aktionen und ihre Features, die Features des aktuellen Kontexts (currentContext), die auszuschließenden Aktionen (excludeActions) und eine eindeutige Ereignis-ID enthalten, um die Antwort zu erhalten.

In dieser Schnellstartanleitung werden die einfachen Kontextmerkmale „Tageszeit“ und „Essenswunsch des Benutzers“ verwendet. In Produktionssystemen kann die Bestimmung und [Auswertung](../concept-feature-evaluation.md) von [Aktionen und Merkmalen](../concepts-features.md) allerdings eine komplexe Angelegenheit sein.

```python
rank_request = RankRequest( actions=actions, context_features=context, excluded_actions=['juice'], event_id=eventid)
response = client.rank(rank_request=rank_request)
```

## <a name="send-a-reward"></a>Senden einer Relevanz

Um die Relevanzbewertung zu erhalten, die in der Relevanzanforderung gesendet wird, ruft das Programm die Auswahl des Benutzers aus der Befehlszeile ab, weist der Auswahl einen numerischen Wert zu und sendet dann die eindeutige Ereignis-ID und die Relevanzbewertung als numerischen Wert an die Relevanz-API.

In dieser Schnellstartanleitung wird als Relevanzbewertung eine einfache Zahl (0 oder 1) zugewiesen. In Produktionssystemen ist die Entscheidung, was wann an den [Relevanzaufruf](../concept-rewards.md) gesendet werden soll (abhängig von Ihren spezifischen Anforderungen), unter Umständen etwas komplizierter.

```python
reward_val = "0.0"
if(answer.lower()=='y'):
    reward_val = "1.0"
elif(answer.lower()=='n'):
    reward_val = "0.0"
else:
    print("Entered choice is invalid. Service assumes that you didn't like the recommended food choice.")

client.events.reward(event_id=eventid, value=reward_val)
```

## <a name="run-the-program"></a>Ausführen des Programms

Führen Sie die Anwendung mit dem Python-Beispiel aus dem Anwendungsverzeichnis aus.

```console
python sample.py
```

![Das Schnellstartprogramm stellt ein paar Fragen zum Sammeln von Benutzereinstellungen – auch bekannt als Features – und stellt dann die am besten bewertete Aktion bereit.](../media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)