---
title: Suchen nach einem Ort mit dem Suchdienst von Azure Maps
description: Erfahren Sie mehr über den Azure Maps-Suchdienst. Sehen Sie, wie Sie diese Gruppe von APIs für die Geocodierung, inverse Geocodierung, Fuzzysuchen und umgekehrte Querstraßensuchen verwenden.
author: anastasia-ms
ms.author: v-stharr
ms.date: 10/05/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 00ddb53276c052d538d658f2c40384e86cf72aee
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2020
ms.locfileid: "92910983"
---
# <a name="search-for-a-location-using-azure-maps-search-services"></a>Suchen nach einem Ort mit dem Suchdienst von Azure Maps

Beim [Azure Maps-Suchdienst](/rest/api/maps/search) handelt es sich um eine Gruppe von RESTful-APIs, die Entwickler bei der Suche nach Adressen, Orten, Brancheneinträgen (nach Name oder Kategorie) und weiteren geografischen Informationen unterstützen. Zusätzlich zur Unterstützung der traditionellen Geocodierung kann mit den Diensten eine inverse Geocodierung für Adressen und Querstraßen basierend auf Breiten- und Längengraden durchgeführt werden. Die bei der Suche zurückgegebenen Werte für den Breiten- und Längengrad können als Parameter in anderen Azure Maps-Diensten verwendet werden, beispielsweise in den Diensten für [Routen](/rest/api/maps/route) und [Wetter](/rest/api/maps/weather).


In diesem Artikel lernen Sie Folgendes:

* Anfordern von Breiten- und Längenkoordinaten für eine Adresse (Geocode-Adresse) mithilfe der [API für die Adresssuche]( https://docs.microsoft.com/rest/api/maps/search/getsearchaddress)
* Suchen nach einer Adresse oder einem Point of Interest (POI) mithilfe der [API für die Fuzzysuche](/rest/api/maps/search/getsearchfuzzy)
* Ausführen einer [inversen Adresssuche](/rest/api/maps/search/getsearchaddressreverse), um den Koordinatenort in eine Straßenadresse zu übersetzen
* Übersetzen der Koordinaten in ein von Menschen lesbares Format nach Querstraßen mithilfe der [API für die inverse Adresssuche nach Querstraßen](/rest/api/maps/search/getsearchaddressreversecrossstreet)  Diese Funktion wird meistens in Trackinganwendungen benötigt, die einen GPS-Feed von einem Gerät oder einer Ressource empfangen und Adressen an bestimmten Koordinatenpunkten ermitteln sollen.

## <a name="prerequisites"></a>Voraussetzungen

1. [Erstellen eines Azure Maps-Kontos](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Abrufen eines Primärschlüssels](quick-demo-map-app.md#get-the-primary-key-for-your-account) (auch primärer Schlüssel oder Abonnementschlüssel genannt)

In diesem Tutorial wird die Anwendung [Postman](https://www.postman.com/) verwendet. Sie können aber auch eine andere API-Entwicklungsumgebung verwenden.

## <a name="request-latitude-and-longitude-for-an-address-geocoding"></a>Anfordern des Breiten- und Längengrads für eine Adresse (Geocodierung)

In diesem Beispiel wird die Azure Maps-[API zum Abrufen der Suchadresse](/rest/api/maps/search/getsearchaddress) verwendet, um eine Adresse in Breiten- und Längenkoordinaten zu konvertieren. Dieser Vorgang wird auch als *Geocodierung* bezeichnet. Zusätzlich zu den Koordinaten werden in der Antwort auch detaillierte Adressinformationen zurückgegeben, darunter beispielsweise Straße, Postleitzahl, Gemeinde und Land/Region.

>[!TIP]
>Wenn Sie über eine Gruppe von Adressen für die Geocodierung verfügen, können Sie die [API zur Batchübermittlung von Suchadressen](/rest/api/maps/search/postsearchaddressbatch) verwenden, um einen Batch von Abfragen in einem einzigen API-Aufruf zu senden.

1. Öffnen Sie die Postman-App. Wählen Sie oben in der Postman-App **New** (Neu) aus. Wählen Sie im Fenster **Create New** (Neu erstellen) die Option **Collection** (Sammlung) aus.  Geben Sie einen Namen für die Sammlung ein, und klicken Sie dann auf **Create** (Erstellen). Sie verwenden diese Sammlung für alle weiteren Beispiele in diesem Dokument.

2. Klicken Sie erneut auf **New** (Neu), um die Anforderung zu erstellen. Wählen Sie im Fenster **Create New** (Neu erstellen) die Option **Request** (Anforderung) aus. Geben Sie einen Anforderungsnamen ( **Request name** ) ein. Wählen Sie die im vorherigen Schritt erstellte Sammlung und anschließend **Save** (Speichern) aus.

3. Wählen Sie auf der Registerkarte „Builder“ (Generator) die HTTP-Methode **GET** aus, und geben Sie die folgende URL ein. In dieser Anforderung suchen wir nach einer bestimmten Adresse: `400 Braod St, Seattle, WA 98109`. Ersetzen Sie bei dieser Anforderung sowie bei den anderen in diesem Artikel angegebenen Anforderungen jeweils `{Azure-Maps-Primary-Subscription-key}` durch Ihren primären Abonnementschlüssel.

    ```http
    https://atlas.microsoft.com/search/address/json?&subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&language=en-US&query=400 Broad St, Seattle, WA 98109
    ```

4. Klicken Sie auf die Schaltfläche **Send** (Senden). Der Antworttext enthält Daten für einen einzelnen Standort.

5. Jetzt suchen wir nach einer Adresse mit mehr als einem möglichen Standort. Ändern Sie im Abschnitt **Params** den Schlüssel `query` in `400 Broad, Seattle`. Klicken Sie auf die Schaltfläche **Send** (Senden).

    :::image type="content" source="./media/how-to-search-for-address/search-address.png" alt-text="Suchen nach einer Adresse":::

6. Legen Sie als Nächstes den Schlüssel `query`auf `400 Broa` fest.

7. Klicken Sie auf die Schaltfläche **Senden**. Sie sehen, dass die Antwort Daten aus mehreren Ländern umfasst. Fügen Sie der Anforderung immer so viele Standortangaben wie möglich hinzu, damit die Ergebnisse dem für Ihren Benutzer relevanten geografischen Bereich entsprechen.

## <a name="using-fuzzy-search-api"></a>Verwenden der API für die Fuzzysuche

Die Azure Maps-[API für die Fuzzysuche](/rest/api/maps/search/getsearchfuzzy) unterstützt einzeilige Standardsuchen und Freiformsuchen. Es wird empfohlen, die Azure Maps-API für die Fuzzysuche zu verwenden, wenn Sie den Benutzereingabetyp für eine Suchabfrage nicht kennen.  Die Abfrageeingabe kann eine vollständige oder eine unvollständige Adresse sein. Es kann sich auch um ein POI-Token (Point of Interest) handeln, z. B. einen POI-Namen, eine POI-Kategorie oder einen Markennamen. Darüber hinaus können die Abfrageergebnisse durch eine Koordinatenposition und einen Radius oder durch das Definieren eines Begrenzungsrahmens eingeschränkt werden, um die Relevanz Ihrer Suchergebnisse zu verbessern.

>[!TIP]
>Die meisten Suchabfragen sind zur Leistungsverbesserung und zur Verringerung ungewöhnlicher Ergebnisse standardmäßig auf „maxFuzzyLevel=1“ festgelegt. Sie können die Fuzzyebene mithilfe der Parameter `maxFuzzyLevel` oder `minFuzzyLevel` anpassen. Weitere Informationen zu `maxFuzzyLevel` und eine vollständige Liste aller optionalen Parameter finden Sie unter [URI-Parameter](/rest/api/maps/search/getsearchfuzzy#uri-parameters) im Artikel zur Fuzzysuche.

### <a name="search-for-an-address-using-fuzzy-search"></a>Suchen nach einer Adresse mithilfe der Fuzzysuche

In diesem Beispiel verwenden wir die Fuzzysuche, um eine weltweite Suche nach `pizza` durchzuführen.  Anschließend wird gezeigt, wie die Suche nur für ein bestimmtes Land/eine bestimmte Region durchgeführt wird. Abschließend wird beschrieben, wie Sie eine Koordinatenposition und einen Radius verwenden, um eine Suche auf einen bestimmten Bereich einzuschränken und die Anzahl der zurückgegebenen Ergebnisse zu begrenzen.

>[!IMPORTANT]
>Fügen Sie immer so viele Standortangaben wie möglich hinzu, damit die Ergebnisse dem für Ihren Benutzer relevanten geografischen Bereich entsprechen. Weitere Informationen finden Sie unter [Bewährte Methoden für den Suchdienst](how-to-use-best-practices-for-search.md#geobiased-search-results).

1. Öffnen Sie die Postman-App, klicken Sie auf **New** „Neu“, und wählen Sie **Request** (Anforderung) aus. Geben Sie einen Anforderungsnamen ( **Request name** ) ein. Wählen Sie die im vorherigen Abschnitt oder eine neu erstellte Sammlung aus, und klicken Sie dann auf **Save** (Speichern).

2. Wählen Sie auf der Registerkarte „Builder“ (Generator) die HTTP-Methode **GET** aus, und geben Sie die folgende URL ein. Ersetzen Sie bei dieser Anforderung sowie bei den anderen in diesem Artikel angegebenen Anforderungen jeweils `{Azure-Maps-Primary-Subscription-key}` durch Ihren primären Abonnementschlüssel.

    ```http
   https://atlas.microsoft.com/search/fuzzy/json?&api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}&language=en-US&query=pizza
    ```

    >[!NOTE]
    >Das Attribut _json_ im URL-Pfad legt das Antwortformat fest. In diesem Artikel wird JSON verwendet, um die Benutzerfreundlichkeit und Lesbarkeit zu verbessern. Informationen zu weiteren unterstützten Antwortformaten finden Sie in der Definition des `format`-Parameters im Abschnitt [URI-Parameter](/rest/api/maps/search/getsearchfuzzy#uri-parameters).

3. Klicken Sie auf **Send** (Senden), und sehen Sie sich den Antworttext an.

    Die mehrdeutige Abfragezeichenfolge „pizza“ hat 10 [POI-Ergebnisse](/rest/api/maps/search/getsearchpoi#searchpoiresponse) sowohl in der Kategorie „Pizza“ als auch in der Kategorie „Restaurant“ zurückgegeben. Jedes Ergebnis enthält eine Adresse, Werte für den Breiten- und Längengrad, ein Bildausschnitt und Einstiegspunkte für den Standort. Die Ergebnisse für diese Abfrage variieren und sind mit keinem bestimmten Referenzstandort verbunden.
  
    Im nächsten Schritt verwenden wir den Parameter `countrySet`, um nur die Länder/Regionen anzugeben, die von Ihrer Anwendung abgedeckt werden sollen. Eine vollständige Liste der unterstützten Länder/Regionen finden Sie im Artikel zur [Suchabdeckung](./geocoding-coverage.md).

4. Das Standardverhalten besteht darin, die ganze Welt zu durchsuchen, wodurch unnötige Ergebnisse zurückgegeben werden können. Als Nächstes führen wir eine Suche nach „pizza“ nur in den USA durch. Fügen Sie dem Abschnitt `countrySet`Params **den Schlüssel** hinzu, und legen Sie seinen Wert auf `US` fest. Durch das Festlegen des Schlüssels `countrySet` auf `US` werden die Ergebnisse auf die USA beschränkt.

    :::image type="content" source="./media/how-to-search-for-address/search-fuzzy-country.png" alt-text="Suche nach „pizza“ in den USA":::

    Die Ergebnisse werden nun durch den Ländercode begrenzt, und die Abfrage gibt Pizzerien in den USA zurück.

5. Um eine noch gezieltere Suche durchzuführen, können Sie als Suchbereich ein Koordinatenpaar aus Längen- und Breitengrad angeben. In diesem Beispiel verwenden wir den Längen- und Breitengrad der Space Needle in Seattle. Da wir nur die Ergebnisse innerhalb eines Radius von 400 Metern zurückgeben möchten, fügen wir den Parameter `radius` hinzu. Außerdem verwenden wir den Parameter `limit`, um die Ergebnisse auf die fünf nächstgelegenen Pizzerien zu beschränken.

    Fügen Sie dem Abschnitt **Params** die folgenden Schlüssel-Wert-Paare hinzu:

     | Schlüssel | Wert |
    |-----|------------|
    | lat | 47.620525 |
    | lon | -122.349274 |
    | Radius | 400 |
    | limit | 5|

6. Klicken Sie auf **Send**. Die Antwort enthält die Ergebnisse für Pizzerien in der Nähe der Space Needle in Seattle.

## <a name="search-for-a-street-address-using-reverse-address-search"></a>Suchen nach einer Adresse mithilfe der inversen Adresssuche

Die Azure Maps-[API für die inverse Adresssuche]( https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) übersetzt Koordinaten in lesbare Anschriften. Diese API wird häufig für Anwendungen verwendet, die einen GPS-Feed empfangen und Adressen an bestimmten Koordinatenpunkten ermitteln sollen.

>[!IMPORTANT]
>Fügen Sie immer so viele Standortangaben wie möglich hinzu, damit die Ergebnisse dem für Ihren Benutzer relevanten geografischen Bereich entsprechen. Weitere Informationen finden Sie unter [Bewährte Methoden für den Suchdienst](how-to-use-best-practices-for-search.md#geobiased-search-results).

>[!TIP]
>Wenn Sie über eine Gruppe von Koordinatenpositionen für die inverse Geocodierung verfügen, können Sie [API „Post Search Address Reverse Batch“](/rest/api/maps/search/postsearchaddressreversebatch) verwenden, um einen Batch von Abfragen in einem einzigen API-Aufruf zu senden.

In diesem Beispiel führen wir mithilfe einiger der verfügbaren optionalen Parameter eine umgekehrte Suche durch. Eine vollständige Liste der optionalen Parameter finden Sie im Abschnitt zu den [Parametern für die umgekehrte Suche](/rest/api/maps/search/getsearchaddressreverse#uri-parameters).

1. Klicken Sie in der Postman-App auf **New** „Neu“, und wählen Sie **Request** (Anforderung) aus. Geben Sie einen Anforderungsnamen ( **Request name** ) ein. Wählen Sie die im ersten Abschnitt oder eine neu erstellte Sammlung aus, und klicken Sie dann auf **Save** (Speichern).

2. Wählen Sie auf der Registerkarte „Builder“ (Generator) die HTTP-Methode **GET** aus, und geben Sie die folgende URL ein. Ersetzen Sie bei dieser Anforderung sowie bei den anderen in diesem Artikel angegebenen Anforderungen jeweils `{Azure-Maps-Primary-Subscription-key}` durch Ihren primären Abonnementschlüssel. Die Anforderung sollte wie die folgende URL aussehen:

    ```http
    https://atlas.microsoft.com/search/address/reverse/json?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}&language=en-US&query=47.591180,-122.332700&number=1
    ```

3. Klicken Sie auf **Send** (Senden), und sehen Sie sich den Antworttext an. Es sollte nur ein Abfrageergebnis angezeigt werden. Die Antwort enthält Schlüsseladressinformationen über Safeco Field.
  
4. Fügen Sie dem Abschnitt **Params** nun die folgenden Schlüssel-Wert-Paare hinzu:

    | Schlüssel | Wert | Rückgabe
    |-----|------------|------|
    | number | 1 |Die Antwort enthält möglicherweise die Straßenseite (links/rechts) und außerdem eine Versatzposition für die Hausnummer.|
    | returnSpeedLimit | true | Gibt das Tempolimit an der Adresse zurück.|
    | returnRoadUse | true | Gibt den Straßentyp an der Adresse zurück. Eine Übersicht der möglichen Straßentypen finden Sie im Abschnitt [URI-Parameter](/rest/api/maps/search/getsearchaddressreverse#uri-parameters).|
    | returnMatchType | true| Gibt den Typ der Übereinstimmung zurück. Eine Übersicht der möglichen Werte finden Sie unter [SearchAddressReverseResult](/rest/api/maps/search/getsearchaddressreverse#searchaddressreverseresult).

   :::image type="content" source="./media/how-to-search-for-address/search-reverse.png" alt-text="Umgekehrte Suche":::

5. Klicken Sie auf **Send** (Senden), und sehen Sie sich den Antworttext an.

6. Als Nächstes fügen wir den Schlüssel `entityType` hinzu und legen seinen Wert auf `Municipality` fest. Der Schlüssel `entityType` überschreibt den Schlüssel `returnMatchType` im vorherigen Schritt. Wir müssen außerdem `returnSpeedLimit` und `returnRoadUse` entfernen, weil wir Informationen zur Gemeinde anfordern.  Eine Liste aller möglichen Typen finden Sie unter [EntityType](/rest/api/maps/search/getsearchaddressreverse#entitytype).

    :::image type="content" source="./media/how-to-search-for-address/search-reverse-entity-type.png" alt-text="Umgekehrte Suche nach „entityType“":::

7. Klicken Sie auf **Send**. Vergleichen Sie die Ergebnisse mit denen in Schritt 5.  Der angeforderte Entitätstyp lautet jetzt `municipality`, deshalb enthält die Antwort keine Informationen zur Anschrift. Der zurückgegebene `geometryId`-Wert kann darüber hinaus zum Anfordern eines Begrenzungspolygons über die Azure Maps-[API zum Abrufen von Suchpolygonen](/rest/api/maps/search/getsearchpolygon) verwendet werden.

>[!TIP]
>Weitere Informationen zu diesen und weiteren Parametern finden Sie im Abschnitt zu den [Parametern für die umgekehrte Suche](/rest/api/maps/search/getsearchaddressreverse#uri-parameters).

## <a name="search-for-cross-street-using-reverse-address-cross-street-search"></a>Suchen nach der Querstraße mithilfe der inversen Adresssuche für Querstraßen

In diesem Beispiel suchen wir basierend auf den Koordinaten einer Adresse nach einer Querstraße.

1. Klicken Sie in der Postman-App auf **New** „Neu“, und wählen Sie **Request** (Anforderung) aus. Geben Sie einen Anforderungsnamen ( **Request name** ) ein. Wählen Sie die im ersten Abschnitt oder eine neu erstellte Sammlung aus, und klicken Sie dann auf **Save** (Speichern).

2. Wählen Sie auf der Registerkarte „Builder“ (Generator) die HTTP-Methode **GET** aus, und geben Sie die folgende URL ein. Ersetzen Sie bei dieser Anforderung sowie bei den anderen in diesem Artikel angegebenen Anforderungen jeweils `{Azure-Maps-Primary-Subscription-key}` durch Ihren primären Abonnementschlüssel. Die Anforderung sollte wie die folgende URL aussehen:
  
    ```http
   https://atlas.microsoft.com/search/address/reverse/crossstreet/json?&api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}&language=en-US&query=47.591180,-122.332700
    ```

    :::image type="content" source="./media/how-to-search-for-address/search-address-cross.png" alt-text="Suche nach einer Querstraße":::
  
3. Klicken Sie auf **Send** (Senden), und sehen Sie sich den Antworttext an. Beachten Sie, dass die Antwort den Wert `Occidental Avenue South` für `crossStreet` enthält.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Azure Maps-Suchdienst-REST-API](/rest/api/maps/search)

> [!div class="nextstepaction"]
> [Bewährte Methoden für den Suchdienst von Azure Maps](how-to-use-best-practices-for-search.md)