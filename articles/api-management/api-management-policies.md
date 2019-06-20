---
title: Richtlinien in Azure API Management | Microsoft Docs
description: Erfahren Sie mehr über die Richtlinien, die für die Verwendung in Azure API Management verfügbar sind.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: e27829fe5ebf57552ef4e97a2bfc7b6aefd81dc8
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/28/2019
ms.locfileid: "66254399"
---
# <a name="api-management-policies"></a>Richtlinien für die API-Verwaltung
Dieser Abschnitt enthält eine Referenz für die folgenden API Management-Richtlinien. Weitere Informationen zum Hinzufügen und Konfigurieren von Richtlinien finden Sie unter [Richtlinien in API Management](api-management-howto-policies.md).  
  
 Richtlinien sind eine leistungsfähige Funktion des Systems, mit der Herausgeber das Verhalten der API über eine Konfiguration ändern können. Richtlinien sind eine Sammlung von Anweisungen, die sequenziell bei Anfragen oder Antworten einer API ausgeführt werden. Häufig verwendete Anweisungen sind z. B. Formatumwandlungen von XML nach JSON und Aufrufbeschränkungen, um die Anzahl eingehender Aufrufe von einem Entwickler zu beschränken. Viele weitere Richtlinien sind vorkonfiguriert verfügbar.  
  
 Richtlinienausdrücke können als Attributwerte oder Textwerte in einer beliebigen API Management-Richtlinie verwendet werden, sofern in der Richtlinie nicht anders angegeben. Einige Richtlinien, beispielsweise [Ablaufsteuerung](api-management-advanced-policies.md#choose) und [Variable festlegen](api-management-advanced-policies.md#set-variable), basieren auf Richtlinienausdrücken. Weitere Informationen finden Sie unter [Erweiterte Richtlinien](api-management-advanced-policies.md#AdvancedPolicies) und [Richtlinienausdrücke](api-management-policy-expressions.md).  
  
##  <a name="ProxyPolicies"></a> Richtlinien  
  
-   [Richtlinien für die Zugriffsbeschränkung](api-management-access-restriction-policies.md#AccessRestrictionPolicies)  
    -   [HTTP-Header überprüfen](api-management-access-restriction-policies.md#CheckHTTPHeader) – Erfordert das Vorhandensein und/oder einen Wert eines HTTP-Headers.  
    -   [Limit call rate (Aufrufrate begrenzen)](api-management-access-restriction-policies.md#LimitCallRate) – verhindert API-Lastspitzen, indem die Aufrufrate jeweils pro Abonnement beschränkt wird.  
    -   [Limit call rate (Aufrufrate nach Schlüssel begrenzen)](api-management-access-restriction-policies.md#LimitCallRateByKey) – verhindert API-Lastspitzen, indem die Aufrufrate jeweils pro Schlüssel beschränkt wird.  
    -   [Beschränkung für Aufrufer-IP](api-management-access-restriction-policies.md#RestrictCallerIPs) – Filtert (erlaubt/blockiert) Aufrufe von bestimmten IP-Adressen und/oder Adressbereichen.  
    -   [Set usage quota by subscription (Nutzungskontingent nach Abonnement festlegen)](api-management-access-restriction-policies.md#SetUsageQuota) – ermöglicht die Durchsetzung eines erneuerbaren oder für die Lebensdauer gültigen Kontingents für Aufrufe und/oder Bandbreite auf Grundlage des Abonnements.  
    -   [Set usage quota by key (Nutzungskontingent nach Schlüssel festlegen)](api-management-access-restriction-policies.md#SetUsageQuotaByKey) – ermöglicht die Durchsetzung eines erneuerbaren oder für die Lebensdauer gültigen Kontingents für Aufrufe und/oder Bandbreite auf Grundlage des Schlüssels.  
    -   [JWT überprüfen](api-management-access-restriction-policies.md#ValidateJWT) – Erzwingt das Vorhandensein und die Gültigkeit eines JWT, das entweder aus einem angegebenen HTTP-Header oder aus einem angegebenen Abfrageparameter extrahiert wurde.  
-   [Erweiterte Richtlinien](api-management-advanced-policies.md#AdvancedPolicies)  
    -   [Ablaufsteuerung](api-management-advanced-policies.md#choose) – bedingte Anwendung von Richtlinienanweisungen basierend auf der Auswertung von booleschen Ausdrücken.  
    -   [Anforderung weiterleiten](api-management-advanced-policies.md#ForwardRequest) – leitet die Anforderung an den Back-End-Dienst.
    -   [Parallelität einschränken:](api-management-advanced-policies.md#LimitConcurrency) verhindert die Ausführung der eingeschlossenen Richtlinien durch mehr als die angegebene Anzahl von Anforderungen gleichzeitig.
    -   [Protokoll an Event Hub](api-management-advanced-policies.md#log-to-eventhub) – sendet Nachrichten im angegebenen Format an ein von einem Protokollierungstool definiertes Nachrichtenziel.
    -   [Modellantwort](api-management-advanced-policies.md#mock-response) – bricht die Pipelineausführung ab und gibt die Modellantwort unmittelbar an den Aufrufer zurück.
    -   [Wiederholen](api-management-advanced-policies.md#Retry) – Wiederholt die Ausführung der eingeschlossenen Richtlinienanweisungen, falls und bis die Bedingung erfüllt ist. Die Ausführung wird mit den angegebenen Zeitintervallen und bis zur angegebenen Anzahl der Wiederholungsversuche wiederholt.  
    -   [Zurückgegebene Antwort](api-management-advanced-policies.md#ReturnResponse) – bricht die Pipeline-Ausführung ab und gibt die angegebene Antwort unmittelbar an den Aufrufer zurück.  
    -   [Unidirektionale Anforderung senden](api-management-advanced-policies.md#SendOneWayRequest) – sendet eine Anforderung an die angegebene URL, ohne auf eine Antwort zu warten.  
    -   [Sendeanforderung](api-management-advanced-policies.md#SendRequest) – sendet eine Anforderung an die angegebene URL.
    -   [HTTP-Proxy festlegen](api-management-advanced-policies.md#SetHttpProxy): Sie können weitergeleitete Anforderungen über einen HTTP-Proxy leiten.
    -   [Variable festlegen](api-management-advanced-policies.md#set-variable) – speichert einen Wert in einer benannten Kontextvariablen, um später darauf zugreifen zu können.  
    -   [Anforderungsmethode festlegen](api-management-advanced-policies.md#SetRequestMethod) – dient der Vornahme von Änderungen der HTTP-Anforderungsmethode.  
    -   [Statuscode festlegen](api-management-advanced-policies.md#SetStatus) – Ändert den HTTP-Statuscode in den angegebenen Wert.  
    -   [Ablaufverfolgung](api-management-advanced-policies.md#Trace) – Fügt eine Zeichenfolge in der Ausgabe für den [API-Inspektor](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) hinzu.  
    -   [Warten](api-management-advanced-policies.md#Wait) – wartet darauf, dass eingeschlossene Richtlinien für [Send request](api-management-advanced-policies.md#SendRequest) (Sendeanforderung), [Get value from cache](api-management-caching-policies.md#GetFromCacheByKey) (Wert aus dem Cache abrufen) oder [Control flow](api-management-advanced-policies.md#choose) (Ablaufsteuerung) abgeschlossen werden, bevor der Vorgang fortgesetzt wird.  
-   [Authentifizierungsrichtlinien](api-management-authentication-policies.md#AuthenticationPolicies)  
    -   [Standardauthentifizierung](api-management-authentication-policies.md#Basic) – Authentifizierung mit einem Back-End-Dienst unter Verwendung der Standardauthentifizierung.  
    -   [Authentifizierung mit Clientzertifikat](api-management-authentication-policies.md#ClientCertificate) – Authentifizierung mit einem Back-End-Dienst unter Verwendung von Clientzertifikaten.  
    -   [Authentifizierung mit einer verwalteten Identität](api-management-authentication-policies.md#ManagedIdentity) – Authentifizierung mit einem Back-End-Dienst unter Verwendung einer [verwalteten Identität](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).  
-   [Cachingrichtlinien](api-management-caching-policies.md#CachingPolicies)  
    -   [Aus Cache abrufen](api-management-caching-policies.md#GetFromCache) – Führt eine Cachesuche aus und gibt ggf. eine gültige Antwort aus dem Cache zurück.  
    -   [In Cache ablegen](api-management-caching-policies.md#StoreToCache) – Cacheantwort gemäß der angegebenen Konfiguration für die Cachesteuerung.  
    -   [Get value from cache (Wert aus dem Cache abrufen)](api-management-caching-policies.md#GetFromCacheByKey) – ruft ein zwischengespeichertes Element nach Schlüssel ab.  
    -   [Store value in cache (Wert im Cache speichern)](api-management-caching-policies.md#StoreToCacheByKey) – speichert ein Element im Cache auf Basis des Schlüssels.  
    -   [Wert aus dem Cache entfernen](api-management-caching-policies.md#RemoveCacheByKey) – Entfernt ein Element im Cache nach Schlüssel.  
-   [Domänenübergreifende Richtlinien](api-management-cross-domain-policies.md#CrossDomainPolicies)  
    -   [Domänenübergreifende Aufrufe zulassen](api-management-cross-domain-policies.md#AllowCrossDomainCalls) – Erlaubt API-Aufrufe aus browserbasierten Clients, die Adobe Flash und Microsoft Silverlight verwenden.  
    -   [CORS](api-management-cross-domain-policies.md#CORS) – Fügt Unterstützung für Cross-Origin Resource Sharing (CORS) zu einer Operation oder einer API hinzu, um domänenübergreifende Aufrufe aus browserbasierten Clients zu ermöglichen.  
    -   [JSONP](api-management-cross-domain-policies.md#JSONP) – Fügt Unterstützung für JSON mit Padding (JSONP) zu einer Operation oder einer API hinzu, um domänenübergreifende Aufrufe aus browserbasierten Clients mit JavaScript zu ermöglichen.  
-   [Transformationsrichtlinien](api-management-transformation-policies.md#TransformationPolicies)  
    -   [JSON in XML konvertieren](api-management-transformation-policies.md#ConvertJSONtoXML) – Konvertiert den Anforderungs- oder Antworttext von JSON in XML.  
    -   [XML in JSON konvertieren](api-management-transformation-policies.md#ConvertXMLtoJSON) – Konvertiert den Anforderungs- oder Antworttext von XML in JSON.  
    -   [Zeichenfolge in Text ersetzen](api-management-transformation-policies.md#Findandreplacestringinbody) – Sucht nach einer Zeichenfolge in Antwort oder Anforderung und ersetzt diese durch eine andere Teilzeichenfolge.  
    -   [URLs in Inhalt maskieren](api-management-transformation-policies.md#MaskURLSContent) – Ändert (maskiert) Links im Antworttext, sodass diese über das Gateway auf den äquivalenten Link zeigen.  
    -   [Back-End-Dienst festlegen](api-management-transformation-policies.md#SetBackendService) – Ändert den Back-End-Dienst für eine eingehende Anforderung.  
    -   [Text festlegen](api-management-transformation-policies.md#SetBody) – Legt den Nachrichtentext für eingehende und ausgehende Anforderungen fest.  
    -   [HTTP-Header setzen](api-management-transformation-policies.md#SetHTTPheader) – Weist einem vorhandenen Antwort- und/oder Anforderungsheader einen Wert zu oder fügt einen neuen Antwort- und/oder Anforderungsheader hinzu.  
    -   [Abfrageparameter setzen](api-management-transformation-policies.md#SetQueryStringParameter) – Fügt Abfrageparameter hinzu, löscht diese oder ersetzt deren Werte.  
    -   [URL umschreiben](api-management-transformation-policies.md#RewriteURL) – Konvertiert eine Anforderung-URL von der öffentlichen Form in die vom Webdienst erwartete Form.  
    -   [XML mithilfe von XSLT transformieren](api-management-transformation-policies.md#XSLTransform) – wendet eine XSL-Transformation auf XML im Anforderungs- oder Antworttext an.  



## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zur Verwendung von Richtlinien finden Sie unter:

+ [Richtlinien in Azure API Management](api-management-howto-policies.md)
+ [Transform and protect your API](transform-api.md) (Transformieren und Schützen von APIs)
+ [API Management-Richtlinienbeispiele](policy-samples.md)   
