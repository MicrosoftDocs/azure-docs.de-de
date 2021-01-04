---
title: Schützen von Azure CDN-Assets mit Tokenauthentifizierung | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie die Tokenauthentifizierung zum Schützen des Zugriffs auf Ihre Azure CDN-Assets verwenden.
services: cdn
documentationcenter: .net
author: zhangmanling
manager: zhangmanling
editor: ''
ms.assetid: 837018e3-03e6-4f9c-a23e-4b63d5707a64
ms.service: azure-cdn
ms.devlang: multiple
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 11/17/2017
ms.author: mazha
ms.openlocfilehash: 21ef06f37e6840df08b1477f9c0ff24f6e15d1a3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95978035"
---
# <a name="securing-azure-cdn-assets-with-token-authentication"></a>Schützen von Azure CDN-Assets mit Tokenauthentifizierung

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Übersicht

Die Tokenauthentifizierung ist ein Mechanismus, mit dem Sie verhindern können, dass Assets im Azure Content Delivery Network (CDN) für nicht autorisierte Clients bereitgestellt werden. Die Tokenauthentifizierung wird normalerweise genutzt, um das *Hotlinking* von Inhalten zu verhindern. Dabei verwendet eine andere Website (z.B. ein Diskussionsforum) Ihre Assets ohne Erlaubnis. Hotlinking kann sich auf Ihre Kosten für die Inhaltsbereitstellung auswirken. Durch Aktivieren der Tokenauthentifizierung im CDN werden Anforderungen von CDN-Edgeserver authentifiziert, bevor das CDN den Inhalt übermittelt. 

## <a name="how-it-works"></a>Funktionsweise

Bei der Tokenauthentifizierung wird überprüft, ob Anforderungen von einer vertrauenswürdigen Website generiert werden. Dazu müssen die Anforderungen einen Tokenwert mit codierten Informationen zur anfordernden Person enthalten. Inhalte werden der anfordernden Person nur bereitgestellt, wenn die codierten Informationen die erforderlichen Voraussetzungen erfüllen. Andernfalls werden die Anforderungen abgelehnt. Sie können die Anforderungen einrichten, indem Sie einen oder mehrere der folgenden Parameter verwenden:

- Land/Region: Lassen Sie Anforderungen zu, die aus den Ländern/Regionen stammen, die durch den [Länder-/Regionscode](/previous-versions/azure/mt761717(v=azure.100)) angegeben sind, oder verweigern Sie solche Anforderungen.
- URL: Lassen Sie nur Anforderungen zu, die der angegebenen Ressource oder dem Pfad entsprechen.
- Host: Lassen Sie Anforderungen zu, die die im Anforderungsheader angegebenen Hosts verwenden, oder verweigern Sie solche Anforderungen.
- Verweiser: Lassen Sie Anforderungen vom angegebenen Verweiser zu, oder verweigern Sie solche Anforderungen.
- IP-Adresse: Lassen Sie nur Anforderungen zu, die von einer bestimmten IP-Adresse oder aus einem bestimmten IP-Subnetz stammen.
- Protokoll: Lassen Sie Anforderungen basierend auf dem Protokoll zu, das zum Anfordern des Inhalts verwendet wird, oder verweigern Sie solche Anforderungen.
- Ablaufzeit: Weisen Sie einen Datums- und Zeitbereich zu, um sicherzustellen, dass ein Link nur für eine begrenzte Zeit gültig bleibt.

Weitere Informationen finden Sie in den ausführlichen Konfigurationsbeispielen für jeden Parameter unter [Einrichten der Tokenauthentifizierung](#setting-up-token-authentication).

>[!IMPORTANT] 
> Wenn die Tokenberechtigung für einen beliebigen Pfad für dieses Konto aktiviert ist, ist der Standardcachemodus der einzige Modus, der für die Zwischenspeicherung von Abfragezeichenfolgen verwendet werden kann. Weitere Informationen finden Sie unter [Steuern des Azure CDN-Zwischenspeicherverhaltens mit Abfragezeichenfolgen](cdn-query-string-premium.md).

## <a name="reference-architecture"></a>Referenzarchitektur

Im folgenden Workflowdiagramm wird beschrieben, wie das CDN die Tokenauthentifizierung verwendet, um mit der Web-App zu arbeiten.

![Worflow der CDN-Tokenauthentifizierung](./media/cdn-token-auth/cdn-token-auth-workflow2.png)

## <a name="token-validation-logic-on-cdn-endpoint"></a>Tokenüberprüfungslogik auf dem CDN-Endpunkt
    
Im folgenden Flussdiagramm wird veranschaulicht, wie Azure CDN eine Clientanforderung überprüft, wenn die Tokenauthentifizierung auf dem CDN-Endpunkt konfiguriert ist.

![Logik der CDN-Tokenüberprüfung](./media/cdn-token-auth/cdn-token-auth-validation-logic.png)

## <a name="setting-up-token-authentication"></a>Einrichten der Tokenauthentifizierung

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrem CDN-Profil, und klicken Sie dann auf **Verwalten**, um das zusätzliche Portal zu starten.

    ![Schaltfläche „Verwalten“ für CDN-Profile](./media/cdn-token-auth/cdn-manage-btn.png)

2. Zeigen Sie mit der Maus auf **HTTP Large**, und klicken Sie im Flyout dann auf **Token Auth**. Sie können dann den Verschlüsselungsschlüssel und Verschlüsselungsparameter wie folgt einrichten:

   1. Erstellen Sie einen oder mehrere Verschlüsselungsschlüssel. Bei einem Verschlüsselungsschlüssel muss die Groß-/Kleinschreibung beachtet werden, und er kann eine beliebige Kombination aus alphanumerischen Zeichen enthalten. Alle anderen Arten von Zeichen, einschließlich Leerzeichen, sind nicht zulässig. Die maximale Länge beträgt 250 Zeichen. Um sicherzustellen, dass die Verschlüsselungsschlüssel auf Zufallsbasis generiert werden, sollten sie mit dem [OpenSSL-Tool](https://www.openssl.org/) erstellt werden. 

      Das OpenSSL-Tool hat die folgende Syntax:

      ```rand -hex <key length>```

      Beispiel:

      ```OpenSSL> rand -hex 32``` 

      Erstellen Sie sowohl einen Primär- als auch einen Sicherungsschlüssel, um Downtime zu vermeiden. Ein Sicherungsschlüssel ermöglicht während der Aktualisierung des Primärschlüssels unterbrechungsfreien Zugriff auf Ihre Inhalte.
    
   2. Geben Sie einen eindeutigen Verschlüsselungsschlüssel in das Feld **Primärschlüssel** ein, und geben Sie optional einen Sicherungsschlüssel in das Feld **Sicherungsschlüssel** ein.

   3. Wählen Sie in der Liste **Minimum Encryption Version** (Verschlüsselungsmindestversion) die Verschlüsselungsmindestversion aus, und klicken Sie dann auf **Aktualisieren**:
      - **V2**: Gibt an, dass mit dem Schlüssel Token der Versionen 2.0 und 3.0 generiert werden können. Verwenden Sie diese Option nur bei der Umstellung von einem älteren Verschlüsselungsschlüssel der Version 2.0 auf einen Schlüssel der Version 3.0.
      - **V3**: (Empfohlen) Gibt an, dass mit dem Schlüssel nur Token der Version 3.0 generiert werden können.

      ![CDN-Tokenauthentifizierung – Einrichten des Schlüssels](./media/cdn-token-auth/cdn-token-auth-setupkey.png)
    
   4. Verwenden Sie das Verschlüsselungstool, um Verschlüsselungsparameter einzurichten und ein Token zu generieren. Mit dem Verschlüsselungstool können Sie Anforderungen basierend auf Ablaufzeit, Land/Region, Verweiser, Protokoll und Client-IP (in beliebigen Kombinationen) zulassen oder ablehnen. Anzahl und Art der Parameter, die zur Bildung eines Tokens kombiniert werden können, sind zwar nicht beschränkt, die Gesamtlänge des Tokens darf jedoch maximal 512 Zeichen betragen. 

      ![CDN-Verschlüsselungstool](./media/cdn-token-auth/cdn-token-auth-encrypttool.png)

      Geben Sie Werte für einen oder mehrere der folgenden Verschlüsselungsparameter im Abschnitt für das **Verschlüsselungstool** ein: 

      > [!div class="mx-tdCol2BreakAll"] 
      > <table>
      > <tr>
      >   <th>Parametername</th> 
      >   <th>BESCHREIBUNG</th>
      > </tr>
      > <tr>
      >    <td><b>ec_expire</b></td>
      >    <td>Dient dem Zuweisen einer Ablaufzeit zu einem Token. Nach Verstreichen dieser Zeit wird das Token ungültig. Anforderungen, die nach der Ablaufzeit übermittelt werden, werden abgelehnt. Dieser Parameter verwendet einen UNIX-Zeitstempel, der auf der Anzahl der Sekunden seit Beginn der UNIX-Standardepoche `1/1/1970 00:00:00 GMT` basiert. (Sie können Onlinetools für die Konvertierung zwischen der Standardzeit und der UNIX-Zeit verwenden.)> 
      >    Wenn das Token beispielsweise am `12/31/2016 12:00:00 GMT` ablaufen soll, geben Sie den UNIX-Zeitstempelwert `1483185600` ein. 
      > </tr>
      > <tr>
      >    <td><b>ec_url_allow</b></td> 
      >    <td>Dient zum Anpassen von Token an ein bestimmtes Asset oder einen Pfad. Der Zugriff wird auf Anforderungen beschränkt, deren URL mit einem bestimmten relativen Pfad beginnt. Bei URLs wird die Groß-/Kleinschreibung berücksichtigt. Geben Sie mehrere Pfade ein, indem Sie als Trennzeichen jeweils ein Komma verwenden. Fügen Sie außerdem keine Leerzeichen hinzu. Je nach Ihren Anforderungen können Sie andere Werte angeben, um unterschiedliche Zugriffsebenen bereitzustellen.> 
      >    Für die URL `http://www.mydomain.com/pictures/city/strasbourg.png` sind diese Anforderungen z.B. für die folgenden Eingabewerte zulässig: 
      >    <ul>
      >       <li>Eingabewert `/`: Alle Anforderungen sind zulässig.</li>
      >       <li>Eingabewert `/pictures`: Die folgenden Anforderungen sind zulässig: <ul>
      >          <li>`http://www.mydomain.com/pictures.png`</li>
      >          <li>`http://www.mydomain.com/pictures/city/strasbourg.png`</li>
      >          <li>`http://www.mydomain.com/picturesnew/city/strasbourgh.png`</li>
      >       </ul></li>
      >       <li>Eingabewert `/pictures/`: Nur Anforderungen mit dem Pfad `/pictures/` sind zulässig. Beispiel: `http://www.mydomain.com/pictures/city/strasbourg.png`.</li>
      >       <li>Eingabewert `/pictures/city/strasbourg.png`: Nur Anforderungen für diesen speziellen Pfad und dieses Asset sind zulässig.</li>
      >    </ul>
      > </tr>
      > <tr>
      >    <td><b>ec_country_allow</b></td> 
      >    <td>Es sind nur Anforderungen zulässig, die aus einem oder mehreren der angegebenen Länder/Regionen stammen. Anforderungen aus allen anderen Ländern/Regionen werden abgelehnt. Verwenden Sie für jedes Land/jede Region einen aus zwei Buchstaben bestehenden [Ländercode nach ISO 3166](/previous-versions/azure/mt761717(v=azure.100)). Trennen Sie alle Länder/Regionen jeweils mit einem Komma voneinander, und fügen Sie keine Leerzeichen ein. Beispiel: Wenn Sie den Zugriff nur aus den USA und aus Frankreich zulassen möchten, geben Sie `US,FR` ein.</td>
      > </tr>
      > <tr>
      >    <td><b>ec_country_deny</b></td> 
      >    <td>Dient zum Ablehnen von Anforderungen, die aus einem oder mehreren angegebenen Ländern/Regionen stammen. Anforderungen aus allen anderen Ländern/Regionen werden zugelassen. Die Implementierung ist identisch mit dem <b>ec_country_allow</b>-Parameter. Ist ein Länder-/Regionscode sowohl im <b>ec_country_allow</b>-Parameter als auch im <b>ec_country_deny</b>-Parameter vorhanden, hat der <b>ec_country_allow</b>-Parameter Vorrang.</td>
      > </tr>
      > <tr>
      >    <td><b>ec_ref_allow</b></td>
      >    <td>Es werden nur Anforderungen vom angegebenen Verweiser zugelassen. Mit einem Verweiser wird die URL der Webseite identifiziert, die als Link zur angeforderten Ressource dient. Nehmen Sie das Protokoll nicht in den Parameterwert auf.> 
      >    Die folgenden Eingabetypen sind zulässig:
      >    <ul>
      >       <li>Ein Hostname oder ein Hostname und ein Pfad.</li>
      >       <li>Mehrere Verweiser. Um mehrere Verweiser hinzuzufügen, trennen Sie sie durch Kommas und fügen Sie kein Leerzeichen hinzu. Wenn Sie einen Verweiserwert angeben, die entsprechenden Informationen aufgrund der Browserkonfiguration aber nicht mit der Anforderung gesendet werden, wird die Anforderung standardmäßig abgelehnt.</li> 
      >       <li>Anforderungen mit fehlenden oder leeren Verweiserinformationen. Standardmäßig blockiert der Parameter <b>ec_ref_allow</b> diese Anforderungstypen. Um solche Anforderungen zuzulassen, geben Sie entweder den Text „missing“ oder einen leeren Wert (unter Verwendung eines nachgestellten Kommas) ein.</li> 
      >       <li>Unterdomänen. Um Unterdomänen zuzulassen, geben Sie ein Sternchen (\*) ein. Um beispielsweise alle Unterdomänen von `contoso.com` zuzulassen, geben Sie `*.contoso.com` ein.</li>
      >    </ul> 
      >    Beispielsweise geben Sie `www.contoso.com,*.contoso.com,missing` ein, um Zugriff für Anforderungen von `www.contoso.com`, allen Unterdomänen unter `contoso2.com` und Anforderungen mit leeren oder fehlenden Verweisern zuzulassen.</td>
      > </tr>
      > <tr> 
      >    <td><b>ec_ref_deny</b></td>
      >    <td>Dient zum Ablehnen von Anforderungen über den angegebenen Verweiser. Die Implementierung ist identisch mit dem <b>ec_ref_allow</b>-Parameter. Ist ein Verweiser sowohl im <b>ec_ref_allow</b>- als auch im <b>ec_ref_deny</b>-Parameter vorhanden, dann hat der Parameter <b>ec_ref_allow</b> Vorrang.</td>
      > </tr>
      > <tr> 
      >    <td><b>ec_proto_allow</b></td> 
      >    <td>Es werden nur Anforderungen vom angegebenen Protokoll zugelassen. Gültige Werte sind `http`, `https` oder `http,https`.</td>
      > </tr>
      > <tr>
      >    <td><b>ec_proto_deny</b></td>
      >    <td>Dient zum Ablehnen von Anforderungen vom angegebenen Protokoll. Die Implementierung ist identisch mit dem <b>ec_proto_allow</b>-Parameter. Ist ein Protokoll sowohl im <b>ec_proto_allow</b>- als auch im <b>ec_proto_deny</b>-Parameter vorhanden, dann hat der Parameter <b>ec_proto_allow</b> Vorrang.</td>
      > </tr>
      > <tr>
      >    <td><b>ec_clientip</b></td>
      >    <td>Beschränkt den Zugriff auf die IP-Adresse der angegebenen anfordernden Person. IPv4 und IPv6 werden unterstützt. Sie können entweder eine einzelne Anforderungs-IP-Adresse oder einem bestimmten Subnetz zugeordnete IP-Adressen angeben. Beispielsweise gestattet `11.22.33.0/22` Anforderungen der IP-Adressen 11.22.32.1 bis 11.22.35.254.</td>
      > </tr>
      > </table>

   5. Nach der Eingabe von Werten für die Verschlüsselungsparameter wählen Sie den Schlüssel zum Verschlüsseln (wenn Sie einen Primär- und einen Sicherungsschlüssel erstellt haben) aus der Liste **Schlüssel zum Verschlüsseln** aus.
    
   6. Wählen Sie in der Liste **Verschlüsselungsversion** eine Verschlüsselungsversion aus: **V2** für Version 2 oder **V3** für Version 3 (empfohlen). 

   7. Klicken Sie auf **Verschlüsseln**, um das Token zu generieren.

      Nachdem das Token generiert wurde, wird es im Feld **Generated Token** (Generiertes Token) angezeigt. Zur Verwendung des Tokens fügen Sie es als Abfragezeichenfolge am Ende der Datei im URL-Pfad ein. Beispiel: `http://www.domain.com/content.mov?a4fbc3710fd3449a7c99986b`.
        
   8. Testen Sie Ihr Token optional mit dem Entschlüsselungstool, um die Parameter Ihres Tokens anzuzeigen. Fügen Sie den Tokenwert in das Feld **Token zum Entschlüsseln** ein. Wählen Sie den zu verwendenden Verschlüsselungsschlüssel aus der Liste **Schlüssel zum Entschlüsseln** aus, und klicken Sie auf **Entschlüsseln**.

      Nach der Entschlüsselung des Tokens werden seine Parameter im Feld **Ursprüngliche Parameter** angezeigt.

   9. Passen Sie optional den Typ des Antwortcodes an, der zurückgegeben wird, wenn eine Anforderung abgelehnt wird. Wählen Sie **Aktiviert** aus, und wählen Sie dann den Antwortcode aus der Liste **Antwortcode** aus. **Headername** wird automatisch auf **Speicherort** festgelegt. Klicken Sie auf **Speichern**, um den neuen Antwortcode zu implementieren. Für bestimmte Antwortcodes müssen Sie auch die URL Ihrer Fehlerseite in das Feld **Headerwert** eingeben. Der Antwortcode **403** („Unzulässig“) ist standardmäßig aktiviert. 

3. Klicken Sie unter **HTTP Large** auf **Regel-Engine**. Sie verwenden die Regel-Engine, um Pfade zum Anwenden der Funktion zu definieren und die Tokenauthentifizierung sowie weitere Funktionen zur Tokenauthentifizierung zu aktivieren. Weitere Informationen finden Sie unter [Azure CDN-Regel-Engine](./cdn-verizon-premium-rules-engine-reference.md).

   1. Wählen Sie eine vorhandene Regel aus, oder erstellen Sie eine neue Regel, um das Asset oder den Pfad zu definieren, auf das bzw. den Sie die Tokenauthentifizierung anwenden möchten. 
   2. Zum Aktivieren der Tokenauthentifizierung für eine Regel wählen Sie **[Token Auth](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth.htm)** aus der Liste **Features** und dann **Aktiviert** aus. Klicken Sie auf **Aktualisieren**, wenn Sie eine Regel aktualisieren, oder auf **Hinzufügen**, wenn Sie eine Regel erstellen.
        
      ![Beispiel für die CDN-Regel-Engine: Tokenauthentifizierung aktiviert](./media/cdn-token-auth/cdn-rules-engine-enable2.png)

4. In der Regel-Engine können Sie auch weitere Features im Zusammenhang mit der Tokenauthentifizierung aktivieren. Um die folgenden Features zu aktivieren, wählen Sie sie in der Liste **Features** aus, und wählen Sie dann **Aktiviert** aus.
    
   - **[Token Auth Denial Code:](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth-Denial-Code.htm)** Gibt den Typ der Antwort an, die an einen Benutzer zurückgegeben wird, wenn eine Anforderung abgelehnt wird. Hier festgelegte Regeln setzen den Antwortcode außer Kraft, der im Abschnitt **Custom Denial Handling** auf der Seite für die tokenbasierte Authentifizierung festgelegt wurde.

   - **[Token Auth Ignore URL Case:](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth-Ignore-URL-Case.htm)** Legt fest, ob für die zum Überprüfen des Tokens verwendete URL die Groß-/Kleinschreibung berücksichtigt wird.

   - **[Token Auth Parameter:](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth-Parameter.htm)** Benennt den Parameter der Abfragezeichenfolge für die Tokenauthentifizierung, der in der angeforderten URL angezeigt wird, um. 
        
     ![Beispiel für die CDN-Regel-Engine: Einstellungen der Tokenauthentifizierung](./media/cdn-token-auth/cdn-rules-engine2.png)

5. Sie können Ihr Token anpassen, indem Sie auf Quellcode in [GitHub](https://github.com/VerizonDigital/ectoken) zugreifen.
   Verfügbare Sprachen:
    
   - C
   - C#
   - PHP
   - Perl
   - Java
   - Python    

## <a name="azure-cdn-features-and-provider-pricing"></a>Preise für Azure CDN-Funktionen und -Anbieter

Weitere Informationen zu Funktionen finden Sie unter [Azure CDN-Produktfeatures](cdn-features.md). Weitere Informationen zur Preisgestaltung finden Sie unter [Azure Content Delivery Network – Preise ](https://azure.microsoft.com/pricing/details/cdn/).