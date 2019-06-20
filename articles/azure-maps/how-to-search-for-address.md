---
title: Suchen nach einer Adresse mit dem Suchdienst von Azure Maps | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mit dem Suchdienst von Azure Maps nach einer Adresse suchen.
author: walsehgal
ms.author: v-musehg
ms.date: 04/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 497ffb5acf6262dfb42f490efe68e1ea26c777cb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64572370"
---
# <a name="find-an-address-using-the-azure-maps-search-service"></a>Suchen nach einer Adresse mit dem Suchdienst von Azure Maps

Bei dem Maps-Suchdienst handelt es sich um eine für Entwickler konzipierte Gruppe von RESTful-APIs für die Suche nach Adressen, Orten, interessanten Orten, Brancheneinträgen und anderen geografischen Informationen. Der Dienst weist einer bestimmten Adresse, einer Querstraße, einem geografischen Objekt oder einem interessanten Ort (POI) einen Breiten- und Längengrad zu. Die von der Suche zurückgegebenen Werte für den Breiten- und Längengrad können als Parameter in anderen Maps-Diensten verwendet werden, z.B. für Routen oder Verkehrsfluss.

In diesem Artikel wird Folgendes behandelt:

* Suchen nach einer Adresse mithilfe der [API für die Fuzzysuche](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)
* Suchen nach einer Adresse mit deren Eigenschaften und Koordinaten
* Ausführen einer [inversen Adresssuche](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse), um nach einer Anschrift zu suchen
* Suchen nach der Querstraße mithilfe der [API für die inverse Adresssuche nach Querstraßen](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreversecrossstreet)

## <a name="prerequisites"></a>Voraussetzungen

Um die Maps-Dienst-APIs aufrufen zu können, benötigen Sie ein Maps-Konto mit zugehörigem Schlüssel. Informationen zum Erstellen eines Kontos und Abrufen eines Schlüssels finden Sie unter [Verwalten Ihres Azure Maps-Kontos und der dazugehörigen Schlüssel](how-to-manage-account-keys.md).

In diesem Artikel wird die [Postman-App](https://www.getpostman.com/apps) zum Erstellen von REST-Aufrufen verwendet. Sie können jedoch auch Ihre bevorzugte API-Entwicklungsumgebung verwenden.

## <a name="using-fuzzy-search"></a>Verwenden der Fuzzysuche

Die Standard-API für den Suchdienst ist die [Fuzzysuche](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) und ist nützlich, wenn Sie nicht wissen, was Ihr Benutzer als Suchabfrage eingibt. Die API kombiniert POI-Suche und Geocodierung zu einer kanonischen „einzeiligen Suche“. So kann die API beispielsweise Eingaben beliebiger Adress- oder POI-Token-Kombinationen verarbeiten. Sie kann auch mit einer kontextabhängigen Position (Breitengrad-Längengrad-Paar) gewichtet werden, die durch eine Koordinate oder einen Radius vollständig eingeschränkt ist. Oder sie kann allgemeiner ohne jeglichen geografiebezogenen Ankerpunkt ausgeführt werden.

Die meisten Suchabfragen sind zur Leistungssteigerung und Verringerung ungewöhnlicher Ergebnisse standardmäßig auf `maxFuzzyLevel=1` festgelegt. Diese Standardeinstellung kann bei Bedarf pro Anforderung durch Übergeben des Abfrageparameters `maxFuzzyLevel=2` oder `3` überschrieben werden.

### <a name="search-for-an-address-using-fuzzy-search"></a>Suchen nach einer Adresse mithilfe der Fuzzysuche

1. Öffnen Sie die Postman-App, klicken Sie auf „New“ (Neu) und „Create New“ (Neu erstellen), und wählen Sie **GET request** (GET-Anforderung) aus. Geben Sie **Fuzzy search** (Fuzzysuche) als Name der Anforderung ein, wählen Sie eine Sammlung oder einen Ordner aus, in der bzw. dem die Anforderung gespeichert wird, und klicken Sie auf **Save** (Speichern).

2. Wählen Sie auf der Registerkarte „Builder“ die HTTP-Methode **GET** aus, und geben Sie die Anforderungs-URL für Ihren API-Endpunkt ein.

    ![Fuzzysuche](./media/how-to-search-for-address/fuzzy_search_url.png)

    | Parameter | Empfohlener Wert |
    |---------------|------------------------------------------------|
    | HTTP-Methode | GET |
    | Anfrage-URL | [https://atlas.microsoft.com/search/fuzzy/json?](https://atlas.microsoft.com/search/fuzzy/json?) |
    | Autorisierung | No Auth |

    Das Attribut **json** im URL-Pfad legt das Antwortformat fest. In diesem Artikel wird zur einfachen Handhabung und besseren Lesbarkeit durchgängig JSON verwendet. Sie finden die verfügbaren Antwortformate in der Definition für **Get Search Fuzzy** der [Referenz zur funktionalen API von Maps](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy).

3. Klicken Sie auf **Params** (Parameter), und geben Sie die folgenden Schlüssel-Wert-Paare zur Verwendung als Abfrage- oder Pfadparameter in der Anforderungs-URL ein:

    ![Fuzzysuche](./media/how-to-search-for-address/fuzzy_search_params.png)

    | Schlüssel | Wert |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | subscription-key | \<Ihr Azure Maps-Schlüssel\> |
    | query | pizza |

4. Klicken Sie auf **Send** (Senden), und sehen Sie sich den Antworttext an.

    Die mehrdeutige Abfragezeichenfolge „pizza“ hat 10 [POI-Ergebnisse](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi#searchpoiresponse) mit Kategorien zurückgegeben, die unter „Pizza“ und „Restaurant“ fallen. In jedem Ergebnis werden eine Adresse, Breitengrad-Längengrad-Werte, ein Bildausschnitt und Einstiegspunkte für den Standort zurückgegeben.
  
    Die Ergebnisse für diese Abfrage variieren und sind mit keinem bestimmten Referenzstandort verbunden. Mit dem **countrySet**-Parameter können Sie nur die Länder angeben, die in der Anwendung abgedeckt werden sollen. Beim Standardverhalten wird auf der ganzen Welt gesucht, sodass potenziell unnötige Ergebnisse zurückgegeben werden.

5. Fügen Sie das folgende Schlüssel-Wert-Paar zum Abschnitt **Params** (Parameter) hinzu, und klicken Sie auf **Senden**:

    | Schlüssel | Wert |
    |------------------|-------------------------|
    | countrySet | US |
  
    Die Ergebnisse werden nun durch den Ländercode begrenzt, und die Abfrage gibt Pizzerien in den USA zurück.
  
    Für Ergebnisse, die sich auf einen Standort beziehen, können Sie einen interessanten Ort abfragen und die zurückgegebenen Werte für den Breiten- und Längengrad im Aufruf des Diensts für die Fuzzysuche verwenden. In diesem Fall verwenden Sie den Suchdienst für die Rückgabe des Standorts der Space Needle in Seattle sowie die Werte für den Breiten- und Längengrad zur Eingrenzung der Suche.
  
6. Geben Sie unter „Params“ (Parameter) die folgenden Schlüssel-Wert-Paare ein, und klicken Sie auf **Send** (Senden):

    ![Fuzzysuche](./media/how-to-search-for-address/fuzzy_search_latlon.png)
  
    | Schlüssel | Wert |
    |-----|------------|
    | lat | 47.620525 |
    | lon | -122.349274 |

## <a name="search-for-address-properties-and-coordinates"></a>Suchen nach Adresseigenschaften und -koordinaten

Sie können eine vollständige oder unvollständige Adresse an die API für die Adresssuche übergeben. Sie erhalten dann eine Antwort, die detaillierte Adresseigenschaften, z.B. die Gemeinde oder Wohnsiedlung, sowie Positionswerte mit Breiten- und Längengraden umfasst.

1. Klicken Sie in Postman auf **New Request (Neue Anforderung)**  | **GET request (GET-Anforderung)** , und nennen Sie sie **Address Search** (Adresssuche).
2. Wählen Sie auf der Registerkarte „Builder“ die HTTP-Methode **GET** aus, geben Sie die Anforderungs-URL für Ihren API-Endpunkt ein, und wählen Sie ggf. ein Autorisierungsprotokoll aus.

    ![Adresssuche](./media/how-to-search-for-address/address_search_url.png)
  
    | Parameter | Empfohlener Wert |
    |---------------|------------------------------------------------|
    | HTTP-Methode | GET |
    | Anfrage-URL | [https://atlas.microsoft.com/search/address/json?](https://atlas.microsoft.com/search/address/json?) |
    | Autorisierung | No Auth |

3. Klicken Sie auf **Params** (Parameter), und geben Sie die folgenden Schlüssel-Wert-Paare zur Verwendung als Abfrage- oder Pfadparameter in der Anforderungs-URL ein:
  
    ![Adresssuche](./media/how-to-search-for-address/address_search_params.png)
  
    | Schlüssel | Wert |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | subscription-key | \<Ihr Azure Maps-Schlüssel\> |
    | query | 400 Broad St, Seattle, WA 98109 |
  
4. Klicken Sie auf **Send** (Senden), und sehen Sie sich den Antworttext an.
  
    In diesem Fall haben Sie eine Abfrage für eine vollständige Adresse angegeben und erhalten im Antworttext ein einzelnes Ergebnis.
  
5. Ändern Sie in „Params“ (Parameter) die Abfragezeichenfolge in den folgenden Wert:
    ```plaintext
        400 Broad, Seattle
    ```

6. Fügen Sie das folgende Schlüssel-Wert-Paar zum Abschnitt **Params** (Parameter) hinzu, und klicken Sie auf **Senden**:

    | Schlüssel | Wert |
    |-----|------------|
    | typeahead | true |

    Das Flag **typeahead** weist die API für die Adresssuche an, die Abfrage als unvollständige Eingabe zu verarbeiten und ein Array von Vorhersagewerten zurückzugeben.

## <a name="search-for-a-street-address-using-reverse-address-search"></a>Suchen nach einer Adresse mithilfe der inversen Adresssuche

1. Klicken Sie in Postman auf **New Request (Neue Anforderung)**  | **GET request (GET-Anforderung)** , und nennen Sie sie **Reverse Address Search** (Inverse Adresssuche).

2. Wählen Sie auf der Registerkarte „Builder“ die HTTP-Methode **GET** aus, und geben Sie die Anforderungs-URL für Ihren API-Endpunkt ein.
  
    ![URL für inverse Adresssuche](./media/how-to-search-for-address/reverse_address_search_url.png)
  
    | Parameter | Empfohlener Wert |
    |---------------|------------------------------------------------|
    | HTTP-Methode | GET |
    | Anfrage-URL | [https://atlas.microsoft.com/search/address/reverse/json?](https://atlas.microsoft.com/search/address/reverse/json?) |
    | Autorisierung | No Auth |
  
3. Klicken Sie auf **Params** (Parameter), und geben Sie die folgenden Schlüssel-Wert-Paare zur Verwendung als Abfrage- oder Pfadparameter in der Anforderungs-URL ein:
  
    ![Parameter für inverse Adresssuche](./media/how-to-search-for-address/reverse_address_search_params.png)
  
    | Schlüssel | Wert |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | subscription-key | \<Ihr Azure Maps-Schlüssel\> |
    | query | 47.591180,-122.332700 |
  
4. Klicken Sie auf **Send** (Senden), und sehen Sie sich den Antworttext an.

    Die Antwort enthält Schlüsseladressinformationen über Safeco Field.
  
5. Fügen Sie das folgende Schlüssel-Wert-Paar zum Abschnitt **Params** (Parameter) hinzu, und klicken Sie auf **Senden**:

    | Schlüssel | Wert |
    |-----|------------|
    | number | true |

    Wenn der Abfrageparameter [number](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) mit der Anforderung gesendet wird, enthält die Antwort möglicherweise die Straßenseite (links/rechts) und auch eine versetzte Position für diese Hausnummer.
  
6. Fügen Sie das folgende Schlüssel-Wert-Paar zum Abschnitt **Params** (Parameter) hinzu, und klicken Sie auf **Senden**:

    | Schlüssel | Wert |
    |-----|------------|
    | returnSpeedLimit | true |
  
    Wenn der Abfrageparameter [returnSpeedLimit](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) festgelegt wird, wird in der Antwort die angegebene Geschwindigkeitsbegrenzung zurückgegeben.

7. Fügen Sie das folgende Schlüssel-Wert-Paar zum Abschnitt **Params** (Parameter) hinzu, und klicken Sie auf **Senden**:

    | Schlüssel | Wert |
    |-----|------------|
    | returnRoadUse | true |

    Wenn der Abfrageparameter [returnRoadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) festgelegt wird, wird in der Antwort die Straßennutzung für die inverse Geocodierung von Straßen zurückgegeben.

8. Fügen Sie das folgende Schlüssel-Wert-Paar zum Abschnitt **Params** (Parameter) hinzu, und klicken Sie auf **Senden**:

    | Schlüssel | Wert |
    |-----|------------|
    | roadUse | true |

    Mithilfe des Abfrageparameters [roadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) können Sie die Abfrage der inversen Geocodierung auf eine bestimmte Art der Straßennutzung beschränken.
  
## <a name="search-for-the-cross-street-using-reverse-address-cross-street-search"></a>Suchen nach der Querstraße mithilfe der inversen Adresssuche für Querstraßen

1. Klicken Sie in Postman auf **New Request (Neue Anforderung)**  | **GET request (GET-Anforderung)** , und nennen Sie sie **Reverse Address Cross Street Search** (Inverse Adresssuche für Querstraßen).

2. Wählen Sie auf der Registerkarte „Builder“ die HTTP-Methode **GET** aus, und geben Sie die Anforderungs-URL für Ihren API-Endpunkt ein.
  
    ![Inverse Adresssuche für Querstraßen](./media/how-to-search-for-address/reverse_address_search_url.png)
  
    | Parameter | Empfohlener Wert |
    |---------------|------------------------------------------------|
    | HTTP-Methode | GET |
    | Anfrage-URL | [https://atlas.microsoft.com/search/address/reverse/crossstreet/json?](https://atlas.microsoft.com/search/address/reverse/crossstreet/json?) |
    | Autorisierung | No Auth |
  
3. Klicken Sie auf **Params** (Parameter), und geben Sie die folgenden Schlüssel-Wert-Paare zur Verwendung als Abfrage- oder Pfadparameter in der Anforderungs-URL ein:
  
    | Schlüssel | Wert |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | subscription-key | \<Ihr Azure Maps-Schlüssel\> |
    | query | 47.591180,-122.332700 |
  
4. Klicken Sie auf **Send** (Senden), und sehen Sie sich den Antworttext an.

## <a name="next-steps"></a>Nächste Schritte

- Machen Sie sich mit der API-Dokumentation zum [Suchdienst von Azure Maps](https://docs.microsoft.com/rest/api/maps/search) vertraut.
