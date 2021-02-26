---
title: Authentifizieren des Zugriffs auf Azure Event Hubs mit Shared Access Signatures
description: In diesem Artikel erfahren Sie, wie Sie den Zugriff auf Event Hubs-Ressourcen mit Shared Access Signatures authentifizieren.
ms.topic: conceptual
ms.date: 06/23/2020
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: ff141fc1bb681e2356a4471dfdc808d622fd76b5
ms.sourcegitcommit: 04297f0706b200af15d6d97bc6fc47788785950f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98986494"
---
# <a name="authenticate-access-to-event-hubs-resources-using-shared-access-signatures-sas"></a>Authentifizieren des Zugriffs auf Event Hubs-Ressourcen mit Shared Access Signatures (SAS)
Die Shared Access Signature (SAS) ermöglicht Ihnen die präzise Steuerung des Zugriffstyps, den Sie den Clients mit der Shared Access Signature gewähren. Folgende Kontrollen können Sie beispielsweise in einer SAS festlegen: 

- Das Intervall, in dem die SAS gültig ist (einschließlich Startzeit und Ablaufzeit).
- Berechtigungen, die von der SAS gewährt werden. Beispielsweise kann eine SAS für einen Event Hubs-Namespace die Berechtigung „Lauschen“, jedoch nicht die Berechtigung „Senden“ gewähren.
- Nur Clients, die gültige Anmeldeinformationen bereitstellen, können Daten an einen Event Hub senden.
- Ein Client kann nicht die Identität eines anderen Clients annehmen.
- Ein nicht autorisierter Client kann daran gehindert werden, Daten an einen Event Hub zu senden.

In diesem Artikel wird die Authentifizierung des Zugriffs auf Event Hubs-Ressourcen mit SAS behandelt. Informationen zum **Autorisieren** des Zugriffs auf Event Hubs-Ressourcen mit SAS finden Sie in [diesem Artikel](authorize-access-shared-access-signature.md). 

> [!NOTE]
> Microsoft empfiehlt als bewährte Methode, nach Möglichkeit Azure AD-Anmeldeinformationen anstelle der Shared Access Signatures zu verwenden, da diese leichter kompromittiert werden können. Sie können weiterhin Shared Access Signatures (SAS) verwenden, um differenzierte Zugriffsrechte für Ihre Event Hubs-Ressourcen zu gewähren. Azure AD bietet jedoch ähnliche Funktionen ohne die Notwendigkeit, SAS-Token verwalten oder sich um das Widerrufen einer kompromittierten SAS kümmern zu müssen.
> 
> Weitere Informationen zur Azure AD-Integration in Azure Event Hubs finden Sie unter [Autorisieren des Zugriffs auf Event Hubs-Ressourcen mit Azure Active Directory](authorize-access-azure-active-directory.md). 


## <a name="configuring-for-sas-authentication"></a>Konfigurieren der SAS-Authentifizierung
Sie können die EventHubs-SAS-Autorisierungsregel für einen Event Hubs-Namespace oder eine Entität (Event Hub-Instanz oder Kafka-Thema in einer Event Hub-Instanz) konfigurieren. Das Konfigurieren einer SAS-Autorisierungsregel für eine Consumergruppe wird zurzeit nicht unterstützt, Sie können den Zugriff auf Consumergruppen jedoch mit den für einen Namespace oder eine Entität konfigurierten Regeln sichern. 

Die folgende Abbildung zeigt, wie die Autorisierungsregeln auf Beispielentitäten angewendet werden. 

![Konfigurieren einer Autorisierungsregel](./media/authenticate-shared-access-signature/configure-sas-authorization-rule.png)

In diesem Beispiel enthält der Event Hubs-Beispielnamespace (ExampleNamespace) zwei Entitäten: „eh1“ und „topic1“. Die Autorisierungsregeln werden sowohl auf Entitätsebene als auch auf Namespaceebene definiert.  

Die Autorisierungsregeln „manageRuleNS“, „sendRuleNS“ und „listenRuleNS“ gelten sowohl für die Event Hub-Instanz „eh1“ als auch für das Thema „t1“. Die Autorisierungsregeln „listenRule-eh“ und „sendRule-eh“ gelten nur für die Event Hub-Instanz „eh1“, und die Autorisierungsregel „sendRuleT“ gilt nur für das Thema „topic1“. 

Bei Verwendung der Autorisierungsregel „sendRuleNS“ können Clientanwendungen sowohl an „eh1“ als auch an „topic1“ Daten senden. Bei Verwendung der Autorisierungsregel „sendRuleT“ wird nur der differenzierte Zugriff auf „topic1“ erzwungen. Daher können Clientanwendungen, die diese Regel für den Zugriff verwenden, keine Daten an „eh1“ senden, sondern nur an „topic1“.

## <a name="generate-a-shared-access-signature-token"></a>Generieren eines SAS-Tokens 
Jeder Client mit Zugriff auf den Namen einer Autorisierungsregel und einen von deren Signaturschlüsseln kann ein SAS-Token generieren. Zur Tokengenerierung wird eine Zeichenfolge im folgenden Format erstellt:

- `se`: Tokenablaufwert. Eine ganze Zahl, die die Anzahl von Sekunden seit der Epoche 00:00:00 Uhr UTC am 1. Januar 1970 (UNIX-Epoche) beim Ablauf des Tokens darstellt.
- `skn`: Name der Autorisierungsregel, d. h. der Name des SAS-Schlüssels.
- `sr`: URI der Ressource, auf die zugegriffen wird.
- `sig`: Signatur.

Die Signaturzeichenfolge ist der SHA-256-Hash, der über den Ressourcen-URI berechnet wird (der im vorherigen Abschnitt beschriebene Bereich), und eine Zeichenfolgendarstellung des Tokenablaufwerts, getrennt durch CR-LF.

Die Berechnung des Hashs ähnelt dem folgenden Pseudocode und gibt einen 256-Bit-/32-Byte-Hashwert zurück. 

```
SHA-256('https://<yournamespace>.servicebus.windows.net/'+'\n'+ 1438205742)
```

Das Token enthält die Werte ohne Hash, sodass der Empfänger den Hash mit den gleichen Parametern neu berechnen und überprüfen kann, ob der Aussteller im Besitz eines gültigen Signaturschlüssels ist.

Der Ressourcen-URI ist der vollständige URI der Service Bus-Ressource, auf die der Zugriff beansprucht wird. Beispiel: http://<namespace>.servicebus.windows.net/<entityPath> oder `sb://<namespace>.servicebus.windows.net/<entityPath>;`, d. h. `http://contoso.servicebus.windows.net/eventhubs/eh1`.

Der URI muss als Prozentwert codiert sein.

Die zum Signieren verwendete SAS-Autorisierungsregel muss für die durch diesen URI angegebene Entität oder eines seiner hierarchisch übergeordneten Elemente konfiguriert werden. Beispiel: `http://contoso.servicebus.windows.net/eventhubs/eh1` oder `http://contoso.servicebus.windows.net` im vorherigen Beispiel.

Ein SAS-Token ist für alle Ressourcen mit dem Präfix <resourceURI> in der Signaturzeichenfolge gültig.

> [!NOTE]
> Sie generieren mithilfe der SAS-Richtlinie ein Zugriffstoken für Event Hubs. Weitere Informationen finden Sie unter [Shared access authorization policies](authorize-access-shared-access-signature.md#shared-access-authorization-policies) (SAS-Autorisierungsrichtlinien).

### <a name="generating-a-signaturetoken-from-a-policy"></a>Generieren einer Signatur (eines Tokens) anhand einer Richtlinie 
Im folgenden Abschnitt wird gezeigt, wie Sie ein SAS-Token mithilfe von SAS-Richtlinien erstellen:

#### <a name="nodejs"></a>NodeJS

```javascript
function createSharedAccessToken(uri, saName, saKey) { 
    if (!uri || !saName || !saKey) { 
            throw "Missing required parameter"; 
        } 
    var encoded = encodeURIComponent(uri); 
    var now = new Date(); 
    var week = 60*60*24*7;
    var ttl = Math.round(now.getTime() / 1000) + week;
    var signature = encoded + '\n' + ttl; 
    var signatureUTF8 = utf8.encode(signature); 
    var hash = crypto.createHmac('sha256', saKey).update(signatureUTF8).digest('base64'); 
    return 'SharedAccessSignature sr=' + encoded + '&sig=' +  
        encodeURIComponent(hash) + '&se=' + ttl + '&skn=' + saName; 
```

#### <a name="java"></a>Java

```java
private static String GetSASToken(String resourceUri, String keyName, String key)
  {
      long epoch = System.currentTimeMillis()/1000L;
      int week = 60*60*24*7;
      String expiry = Long.toString(epoch + week);

      String sasToken = null;
      try {
          String stringToSign = URLEncoder.encode(resourceUri, "UTF-8") + "\n" + expiry;
          String signature = getHMAC256(key, stringToSign);
          sasToken = "SharedAccessSignature sr=" + URLEncoder.encode(resourceUri, "UTF-8") +"&sig=" +
                  URLEncoder.encode(signature, "UTF-8") + "&se=" + expiry + "&skn=" + keyName;
      } catch (UnsupportedEncodingException e) {

          e.printStackTrace();
      }

      return sasToken;
  }


public static String getHMAC256(String key, String input) {
    Mac sha256_HMAC = null;
    String hash = null;
    try {
        sha256_HMAC = Mac.getInstance("HmacSHA256");
        SecretKeySpec secret_key = new SecretKeySpec(key.getBytes(), "HmacSHA256");
        sha256_HMAC.init(secret_key);
        Encoder encoder = Base64.getEncoder();

        hash = new String(encoder.encode(sha256_HMAC.doFinal(input.getBytes("UTF-8"))));

    } catch (InvalidKeyException e) {
        e.printStackTrace();
    } catch (NoSuchAlgorithmException e) {
        e.printStackTrace();
   } catch (IllegalStateException e) {
        e.printStackTrace();
    } catch (UnsupportedEncodingException e) {
        e.printStackTrace();
    }

    return hash;
}
```
#### <a name="php"></a>PHP

```php
function generateSasToken($uri, $sasKeyName, $sasKeyValue) 
{ 
    $targetUri = strtolower(rawurlencode(strtolower($uri))); 
    $expires = time();  
    $expiresInMins = 60; 
    $week = 60*60*24*7;
    $expires = $expires + $week; 
    $toSign = $targetUri . "\n" . $expires; 
    $signature = rawurlencode(base64_encode(hash_hmac('sha256',             
     $toSign, $sasKeyValue, TRUE))); 
    
    $token = "SharedAccessSignature sr=" . $targetUri . "&sig=" . $signature . "&se=" . $expires .      "&skn=" . $sasKeyName; 
    return $token; 
}
```

#### <a name="c"></a>C#

```csharp
private static string createToken(string resourceUri, string keyName, string key)
{
    TimeSpan sinceEpoch = DateTime.UtcNow - new DateTime(1970, 1, 1);
    var week = 60 * 60 * 24 * 7;
    var expiry = Convert.ToString((int)sinceEpoch.TotalSeconds + week);
    string stringToSign = HttpUtility.UrlEncode(resourceUri) + "\n" + expiry;
    HMACSHA256 hmac = new HMACSHA256(Encoding.UTF8.GetBytes(key));
    var signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));
    var sasToken = String.Format(CultureInfo.InvariantCulture, "SharedAccessSignature sr={0}&sig={1}&se={2}&skn={3}", HttpUtility.UrlEncode(resourceUri), HttpUtility.UrlEncode(signature), expiry, keyName);
    return sasToken;
}
```

## <a name="authenticating-event-hubs-publishers-with-sas"></a>Authentifizieren von Event Hubs-Herausgebern mit SAS 
Ein Ereignisherausgeber definiert einen virtuellen Endpunkt für einen Event Hub. Der Herausgeber kann nur zum Senden von Nachrichten an einen Event Hub verwendet werden, nicht zum Empfangen von Nachrichten.

In der Regel setzt ein Event Hub einen Herausgeber pro Client ein. Alle Nachrichten, die an einen der Herausgeber eines Event Hubs gesendet werden, werden in die Warteschlange innerhalb dieses Event Hubs eingereiht. Herausgeber ermöglichen eine präzise Zugriffssteuerung.

Jedem Event Hubs-Client wird ein eindeutiges Token zugewiesen, das auf den Client hochgeladen wird. Token werden so erzeugt, dass jedes eindeutige Token Zugriff auf einen anderen eindeutigen Herausgeber gewährt. Ein Client, der über ein Token verfügt, kann nur an einen Herausgeber Daten senden und an keinen anderen. Wenn mehrere Clients dasselbe Token gemeinsam nutzen, verwendet jeder von ihnen denselben Herausgeber.

Alle Token werden mit SAS-Schlüsseln zugewiesen. Alle Token werden in der Regel mit demselben Schlüssel signiert. Clients kennen den Schlüssel nicht. Clients können daher keine Token generieren. Clients verwenden die gleichen Token, bis diese ablaufen.

Zum Definieren von Autorisierungsregeln, die nur für das Senden/Veröffentlichen von Daten an bzw. in Event Hubs gelten, müssen Sie beispielsweise eine Autorisierungsregel für das Senden definieren. Sie können die Regel auf der Namespaceebene definieren oder einen differenzierteren Bereich für eine bestimmte Entität (eine Event Hubs-Instanz oder ein Thema) festlegen. Ein Client oder eine Anwendung, für den bzw. die ein solch präziser Zugriff festgelegt ist, wird als Event Hubs-Herausgeber bezeichnet. Gehen Sie dazu folgendermaßen vor:

1. Erstellen Sie einen SAS-Schlüssel für die Entität, die Sie veröffentlichen möchten, um ihr den Bereich **Senden** zuzuweisen. Weitere Informationen finden Sie unter [Shared access authorization policies](authorize-access-shared-access-signature.md#shared-access-authorization-policies) (SAS-Autorisierungsrichtlinien).
2. Generieren Sie mit dem in Schritt 1 generierten Schlüssel ein SAS-Token mit einer Ablaufzeit für einen bestimmten Herausgeber.

    ```csharp
    var sasToken = SharedAccessSignatureTokenProvider.GetPublisherSharedAccessSignature(
                new Uri("Service-Bus-URI"),
                "eventub-name",
                "publisher-name",
                "sas-key-name",
                "sas-key",
                TimeSpan.FromMinutes(30));
    ```
3. Stellen Sie das Token für den Herausgeberclient bereit, der nur an die Entität und den Herausgeber Daten senden kann, auf die das Token Zugriff gewährt.

    Nach dem Ablauf des Tokens verliert der Client seinen Sende-/Veröffentlichungszugriff auf die Entität. 


> [!NOTE]
> Obwohl dies nicht empfohlen wird, ist es möglich, Geräte mit Token auszustatten, die Zugriff auf einen Event Hub oder Namespace gewähren. Alle Geräte mit diesem Token können Nachrichten direkt an den Event Hub senden. Darüber hinaus kann das Gerät nicht davon gesperrt werden, an den betreffenden Event Hub zu senden.
> 
> Es empfiehlt sich immer, spezifische und präzise Bereiche festzulegen.

> [!IMPORTANT]
> Sobald die Token erstellt wurden, wird jedem Client sein eigenes eindeutiges Token bereitgestellt.
>
> Wenn der Client Daten an einen Event Hub sendet, taggt er seine Anforderung mit dem Token. Um Lauschangriffe und Diebstahl von Token von einem Angreifer zu verhindern, muss die Kommunikation zwischen dem Client und dem Event Hub über einen verschlüsselten Kanal erfolgen.
> 
> Wenn ein Token von einem Angreifer gestohlen wurde, kann der Angreifer den Client imitieren, dessen Token gestohlen wurde. Wird ein Herausgeber in eine Blockliste eingetragen, wird dieser Client als nicht verwendbar gekennzeichnet, bis er ein neues Token mit einem anderen Herausgeber erhält.


## <a name="authenticating-event-hubs-consumers-with-sas"></a>Authentifizieren von Event Hubs-Consumern mit SAS 
Zum Authentifizieren von Back-End-Anwendungen, die von Event Hubs-Producern generierte Daten nutzen, erfordert die Event Hubs-Tokenauthentifizierung, dass den Clients entweder Rechte zum **Verwalten** oder die Berechtigung **Lauschen** für den Event Hubs-Namespace, die Event Hub-Instanz oder das Thema zugewiesen sind. Daten werden von Event Hubs mithilfe von Consumergruppen genutzt. Mit einer SAS-Richtlinie können Sie zwar einen präzisen Bereich festlegen, dieser Bereich wird aber nur auf der Entitätsebene definiert und nicht auf der Consumerebene. Die auf der Ebene des Namespaces, der Event Hub-Instanz oder des Themas definierten Berechtigungen werden folglich auf die Consumergruppen dieser Entität angewendet.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie in folgenden Artikeln:

- [Authorize using SAS](authenticate-shared-access-signature.md) (Autorisieren mit SAS)
- [Autorisieren mit rollenbasierter Zugriffssteuerung in Azure (Azure RBAC)](authenticate-shared-access-signature.md)
- [Weitere Informationen zu Event Hubs](event-hubs-about.md)

Weitere Informationen finden Sie in den folgenden verwandten Artikeln:

- [Authentifizieren von Anforderungen an Event Hubs über eine Anwendung mithilfe von Azure Active Directory](authenticate-application.md)
- [Authentifizieren einer verwalteten Identität mit Azure Active Directory für den Zugriff auf Event Hubs-Ressourcen](authenticate-managed-identity.md)
- [Autorisieren des Zugriffs auf Event Hubs-Ressourcen mit Azure Active Directory](authorize-access-azure-active-directory.md)
- [Autorisieren des Zugriffs auf Event Hubs-Ressourcen mit Shared Access Signature](authorize-access-shared-access-signature.md)
