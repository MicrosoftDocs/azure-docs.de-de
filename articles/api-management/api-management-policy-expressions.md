---
title: Richtlinienausdrücke in Azure API Management | Microsoft-Dokumentation
description: Erfahren Sie mehr über Richtlinienausdrücke in Azure API Management. Sehen Sie sich Beispiele an, und zeigen Sie zusätzliche verfügbare Ressourcen an.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: ea160028-fc04-4782-aa26-4b8329df3448
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/22/2019
ms.author: apimpm
ms.openlocfilehash: aec1967f0652e18c4a24ca258c14a103355b22af
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/31/2021
ms.locfileid: "99219314"
---
# <a name="api-management-policy-expressions"></a>Richtlinienausdrücke in API Management
In diesem Artikel wird die Syntax für Richtlinienausdrücke in C# 7 erläutert. Jeder Ausdruck besitzt Zugriff auf die implizit bereitgestellte [Kontextvariable](api-management-policy-expressions.md#ContextVariables) und eine zulässige [Teilmenge](api-management-policy-expressions.md#CLRTypes) von .NET Framework-Typen.

Weitere Informationen finden Sie unter:

- Erfahren Sie, wie Kontextinformationen für Ihren Back-End-Dienst bereitgestellt werden. Verwenden Sie die Richtlinien [Abfragezeichenfolgenparameter festlegen](api-management-transformation-policies.md#SetQueryStringParameter) und [HTTP-Header festlegen](api-management-transformation-policies.md#SetHTTPheader), um diese Informationen bereitzustellen.
- Erfahren Sie, wie die Richtlinie [JWT überprüfen](api-management-access-restriction-policies.md#ValidateJWT) verwendet wird, um den Zugriff auf Vorgänge basierend auf Tokenansprüchen vorab zu autorisieren.
- Erfahren Sie, wie eine [API-Inspektor](./api-management-howto-api-inspector.md)-Ablaufverfolgung verwendet wird, um die Auswertung von Richtlinien und die Ergebnisse dieser Auswertungen anzuzeigen.
- Erfahren Sie, wie Ausdrücke mit den Richtlinien [Aus Cache abrufen](api-management-caching-policies.md#GetFromCache) und [In Cache speichern](api-management-caching-policies.md#StoreToCache) verwendet werden, um das Zwischenspeichern von Antworten für API Management zu konfigurieren. Legen Sie eine Dauer fest, die dem Zwischenspeichern von Antworten des Back-End-Diensts entspricht, wie in der `Cache-Control`-Anweisung des Back-End-Diensts angegeben.
- Erfahren Sie, wie Inhalte gefiltert werden. Entfernen Sie Datenelemente mit den Richtlinien [Ablaufsteuerung](api-management-advanced-policies.md#choose) und [Text festlegen](api-management-transformation-policies.md#SetBody) aus der vom Back-End empfangenen Antwort.
- Informationen zum Herunterladen der Richtlinienanweisungen finden Sie im GitHub-Repository unter [api-management-samples/policies](https://github.com/Azure/api-management-samples/tree/master/policies).


## <a name="syntax"></a><a name="Syntax"></a> Syntax
Ausdrücke mit einer einzelnen Anweisung werden in `@(expression)` eingeschlossen, wobei `expression` eine wohlgeformte C#-Ausdrucksanweisung ist.

Ausdrücke mit mehreren Anweisungen werden in `@{expression}` eingeschlossen. Alle Codepfade in Ausdrücken mit mehreren Anweisungen müssen mit einer `return`-Anweisung enden.

## <a name="examples"></a><a name="PolicyExpressionsExamples"></a> Beispiele

```
@(true)

@((1+1).ToString())

@("Hi There".Length)

@(Regex.Match(context.Response.Headers.GetValueOrDefault("Cache-Control",""), @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value)

@(context.Variables.ContainsKey("maxAge") ? int.Parse((string)context.Variables["maxAge"]) : 3600)

@{
  string[] value;
  if (context.Request.Headers.TryGetValue("Authorization", out value))
  {
      if(value != null && value.Length > 0)
      {
          return Encoding.UTF8.GetString(Convert.FromBase64String(value[0]));
      }
  }
  return null;

}
```

## <a name="usage"></a><a name="PolicyExpressionsUsage"></a>Verwendung
Ausdrücke können als Attributwerte oder Textwerte in beliebigen API Management-[Richtlinien](api-management-policies.md) verwendet werden (sofern in der Richtlinienreferenz nicht anders angegeben).

> [!IMPORTANT]
> Bei Verwendung von Richtlinienausdrücken erfolgt nur eine begrenzte Überprüfung der Richtlinienausdrücke beim Definieren der Richtlinie. Ausdrücke werden vom Gateway zur Laufzeit ausgeführt, und durch Richtlinienausdrücke generierte Ausnahmen führen zu einem Laufzeitfehler.

## <a name="net-framework-types-allowed-in-policy-expressions"></a><a name="CLRTypes"></a> In Richtlinienausdrücken zulässige .NET Framework-Typen
Die folgende Tabelle enthält die .NET Framework-Typen und die zugehörigen Mitglieder, die in Richtlinienausdrücken zulässig sind.

|type|Unterstützte Member|
|--------------|-----------------------|
|Newtonsoft.Json.Formatting|All|
|Newtonsoft.Json.JsonConvert|SerializeObject, DeserializeObject|
|Newtonsoft.Json.Linq.Extensions|All|
|Newtonsoft.Json.Linq.JArray|All|
|Newtonsoft.Json.Linq.JConstructor|All|
|Newtonsoft.Json.Linq.JContainer|All|
|Newtonsoft.Json.Linq.JObject|All|
|Newtonsoft.Json.Linq.JProperty|All|
|Newtonsoft.Json.Linq.JRaw|All|
|Newtonsoft.Json.Linq.JToken|All|
|Newtonsoft.Json.Linq.JTokenType|All|
|Newtonsoft.Json.Linq.JValue|All|
|System.Array|All|
|System.BitConverter|All|
|System.Boolean|All|
|System.Byte|All|
|System.Char|All|
|System.Collections.Generic.Dictionary<TKey, TValue>|All|
|System.Collections.Generic.HashSet\<T>|All|
|System.Collections.Generic.ICollection\<T>|All|
|System.Collections.Generic.IDictionary<TKey, TValue>|All|
|System.Collections.Generic.IEnumerable\<T>|All|
|System.Collections.Generic.IEnumerator\<T>|All|
|System.Collections.Generic.IList\<T>|All|
|System.Collections.Generic.IReadOnlyCollection\<T>|All|
|System.Collections.Generic.IReadOnlyDictionary<TKey, TValue>|All|
|System.Collections.Generic.ISet\<T>|All|
|System.Collections.Generic.KeyValuePair<TKey, TValue>|All|
|System.Collections.Generic.List\<T>|All|
|System.Collections.Generic.Queue\<T>|All|
|System.Collections.Generic.Stack\<T>|All|
|System.Convert|All|
|System.DateTime|(Konstruktor), Add, AddDays, AddHours, AddMilliseconds, AddMinutes, AddMonths, AddSeconds, AddTicks, AddYears, Date, Day, DayOfWeek, DayOfYear, DaysInMonth, Hour, IsDaylightSavingTime, IsLeapYear, MaxValue, Millisecond, Minute, MinValue, Month, Now, Parse, Second, Subtract, Ticks, TimeOfDay, Today, ToString, UtcNow, Year|
|System.DateTimeKind|UTC|
|System.DateTimeOffset|All|
|System.Decimal|All|
|System.Double|All|
|System.Exception|All|
|System.Guid|All|
|System.Int16|All|
|System. Int32|All|
|System.Int64|All|
|System.IO.StringReader|All|
|System.IO.StringWriter|All|
|System.Linq.Enumerable|All|
|System.Math|All|
|System.MidpointRounding|All|
|System.Net.WebUtility|All|
|System.Nullable|All|
|System.Random|All|
|System.SByte|All|
|System.Security.Cryptography.AsymmetricAlgorithm|All|
|System.Security.Cryptography.CipherMode|All|
|System.Security.Cryptography.HashAlgorithm|All|
|System.Security.Cryptography.HashAlgorithmName|All|
|System.Security.Cryptography.HMAC|All|
|System.Security.Cryptography.HMACMD5|All|
|System.Security.Cryptography.HMACSHA1|All|
|System.Security.Cryptography.HMACSHA256|All|
|System.Security.Cryptography.HMACSHA384|All|
|System.Security.Cryptography.HMACSHA512|All|
|System.Security.Cryptography.KeyedHashAlgorithm|All|
|System.Security.Cryptography.MD5|All|
|System.Security.Cryptography.Oid|All|
|System.Security.Cryptography.PaddingMode|All|
|System.Security.Cryptography.RNGCryptoServiceProvider|All|
|System.Security.Cryptography.RSA|All|
|System.Security.Cryptography.RSAEncryptionPadding|All|
|System.Security.Cryptography.RSASignaturePadding|All|
|System.Security.Cryptography.SHA1|All|
|System.Security.Cryptography.SHA1Managed|All|
|System.Security.Cryptography.SHA256|All|
|System.Security.Cryptography.SHA256Managed|All|
|System.Security.Cryptography.SHA384|All|
|System.Security.Cryptography.SHA384Managed|All|
|System.Security.Cryptography.SHA512|All|
|System.Security.Cryptography.SHA512Managed|All|
|System.Security.Cryptography.SymmetricAlgorithm|All|
|System.Security.Cryptography.X509Certificates.PublicKey|All|
|System.Security.Cryptography.X509Certificates.RSACertificateExtensions|All|
|System.Security.Cryptography.X509Certificates.X500DistinguishedName|Name|
|System.Security.Cryptography.X509Certificates.X509Certificate|All|
|System.Security.Cryptography.X509Certificates.X509Certificate2|All|
|System.Security.Cryptography.X509Certificates.X509ContentType|All|
|System.Security.Cryptography.X509Certificates.X509NameType|All|
|System.Single|All|
|System.String|All|
|System.StringComparer|All|
|System.StringComparison|All|
|System.StringSplitOptions|All|
|System.Text.Encoding|All|
|System.Text.RegularExpressions.Capture|Index, Length, Value|
|System.Text.RegularExpressions.CaptureCollection|Count, Item|
|System.Text.RegularExpressions.Group|Captures, Success|
|System.Text.RegularExpressions.GroupCollection|Count, Item|
|System.Text.RegularExpressions.Match|Empty, Groups, Result|
|System.Text.RegularExpressions.Regex|(Konstruktor), IsMatch, Match, Matches, Replace, Unescape, Split|
|System.Text.RegularExpressions.RegexOptions|All|
|System.Text.StringBuilder|All|
|System.TimeSpan|All|
|System.TimeZone|All|
|System.TimeZoneInfo.AdjustmentRule|All|
|System.TimeZoneInfo.TransitionTime|All|
|System.TimeZoneInfo|All|
|System.Tuple|All|
|System.UInt16|All|
|System.UInt32|All|
|System.UInt64|All|
|System.Uri|All|
|System.UriPartial|All|
|System.Xml.Linq.Extensions|All|
|System.Xml.Linq.XAttribute|All|
|System.Xml.Linq.XCData|All|
|System.Xml.Linq.XComment|All|
|System.Xml.Linq.XContainer|All|
|System.Xml.Linq.XDeclaration|All|
|System.Xml.Linq.XDocument|Alle außer: Laden|
|System.Xml.Linq.XDocumentType|All|
|System.Xml.Linq.XElement|All|
|System.Xml.Linq.XName|All|
|System.Xml.Linq.XNamespace|All|
|System.Xml.Linq.XNode|All|
|System.Xml.Linq.XNodeDocumentOrderComparer|All|
|System.Xml.Linq.XNodeEqualityComparer|All|
|System.Xml.Linq.XObject|All|
|System.Xml.Linq.XProcessingInstruction|All|
|System.Xml.Linq.XText|All|
|System.Xml.XmlNodeType|All|

## <a name="context-variable"></a><a name="ContextVariables"></a> Kontextvariable
Eine Variable namens `context` steht implizit in jedem [Richtlinienausdruck](api-management-policy-expressions.md#Syntax) zur Verfügung. Ihre Mitglieder bieten Informationen zu `\request`. Alle `context`-Mitglieder sind schreibgeschützt.

|Kontextvariable|Zulässige Methoden, Eigenschaften und Parameterwerte|
|----------------------|-------------------------------------------------------|
|context|[Api](#ref-context-api): [IApi](#ref-iapi)<br /><br /> [Bereitstellung](#ref-context-deployment)<br /><br /> Elapsed: TimeSpan – Zeitintervall zwischen dem Wert des Zeitstempels und der aktuellen Uhrzeit<br /><br /> [LastError](#ref-context-lasterror)<br /><br /> [Vorgang](#ref-context-operation)<br /><br /> [Produkt](#ref-context-product)<br /><br /> [Anforderung](#ref-context-request)<br /><br /> RequestId: Guid – eindeutiger Bezeichner der Anforderung<br /><br /> [Antwort](#ref-context-response)<br /><br /> [Abonnement](#ref-context-subscription)<br /><br /> Timestamp: DateTime – Zeitpunkt des Empfangs der Anforderung<br /><br /> Tracing: bool – gibt an, ob die Ablaufverfolgung aktiviert oder deaktiviert ist <br /><br /> [Benutzer](#ref-context-user)<br /><br /> [Variablen:](#ref-context-variables) IReadOnlyDictionary<string, object><br /><br /> void Trace(message: string)|
|<a id="ref-context-api"></a>context.Api|Id: string<br /><br /> IsCurrentRevision: bool<br /><br />  Name: string<br /><br /> Path: string<br /><br /> Revision: string<br /><br /> ServiceUrl: [IUrl](#ref-iurl)<br /><br /> Version: string |
|<a id="ref-context-deployment"></a>context.Deployment|Region: string<br /><br /> ServiceName: string<br /><br /> Certificates: IReadOnlyDictionary<string, X509Certificate2>|
|<a id="ref-context-lasterror"></a>context.LastError|Source: string<br /><br /> Reason: string<br /><br /> Message: string<br /><br /> Scope: string<br /><br /> Section: string<br /><br /> Path: string<br /><br /> PolicyId: string<br /><br /> Weitere Informationen zu context.LastError finden Sie unter [Fehlerbehandlung](api-management-error-handling-policies.md).|
|<a id="ref-context-operation"></a>context.Operation|Id: string<br /><br /> Method: string<br /><br /> Name: string<br /><br /> UrlTemplate: string|
|<a id="ref-context-product"></a>context.Product|Apis: IEnumerable<[IApi](#ref-iapi)\><br /><br /> ApprovalRequired: bool<br /><br /> Groups: IEnumerable<[IGroup](#ref-igroup)\><br /><br /> Id: string<br /><br /> Name: string<br /><br /> State: enum ProductState {NotPublished, Published}<br /><br /> SubscriptionLimit: int?<br /><br /> SubscriptionRequired: bool|
|<a id="ref-context-request"></a>context.Request|Hauptteil: [IMessageBody](#ref-imessagebody) oder `null`, wenn die Anforderung keinen Text hat.<br /><br /> Certificate: System.Security.Cryptography.X509Certificates.X509Certificate2<br /><br /> [Headers](#ref-context-request-headers): IReadOnlyDictionary<string, string[]><br /><br /> IpAddress: string<br /><br /> MatchedParameters: IReadOnlyDictionary<string, string><br /><br /> Method: string<br /><br /> OriginalUrl: [IUrl](#ref-iurl)<br /><br /> Url: [IUrl](#ref-iurl)|
|<a id="ref-context-request-headers"></a>string context.Request.Headers.GetValueOrDefault(headerName: string, defaultValue: string)|headerName: string<br /><br /> defaultValue: string<br /><br /> Gibt durch Trennzeichen getrennte Anforderungsheaderwerte oder `defaultValue` zurück, wenn der Header nicht gefunden wurde.|
|<a id="ref-context-response"></a>context.Response|Hauptteil: [IMessageBody](#ref-imessagebody)<br /><br /> [Headers](#ref-context-response-headers): IReadOnlyDictionary<string, string[]><br /><br /> StatusCode: int<br /><br /> StatusReason: string|
|<a id="ref-context-response-headers"></a>string context.Response.Headers.GetValueOrDefault(headerName: string, defaultValue: string)|headerName: string<br /><br /> defaultValue: string<br /><br /> Gibt durch Trennzeichen getrennte Antwortheaderwerte oder `defaultValue` zurück, wenn der Header nicht gefunden wurde.|
|<a id="ref-context-subscription"></a>context.Subscription|CreatedDate: Datetime<br /><br /> EndDate: DateTime?<br /><br /> Id: string<br /><br /> Key: string<br /><br /> Name: string<br /><br /> PrimaryKey: string<br /><br /> SecondaryKey: string<br /><br /> StartDate: DateTime?|
|<a id="ref-context-user"></a>context.User|Email: string<br /><br /> FirstName: string<br /><br /> Groups: IEnumerable<[IGroup](#ref-igroup)\><br /><br /> Id: string<br /><br /> Identities: IEnumerable<[IUserIdentity](#ref-iuseridentity)\><br /><br /> LastName: string<br /><br /> Note: string<br /><br /> RegistrationDate: Datetime|
|<a id="ref-iapi"></a>IApi|Id: string<br /><br /> Name: string<br /><br /> Path: string<br /><br /> Protocols: IEnumerable<string\><br /><br /> ServiceUrl: [IUrl](#ref-iurl)<br /><br /> SubscriptionKeyParameterNames: [ISubscriptionKeyParameterNames](#ref-isubscriptionkeyparameternames)|
|<a id="ref-igroup"></a>IGroup|Id: string<br /><br /> Name: string|
|<a id="ref-imessagebody"></a>IMessageBody|As<T\>(preserveContent: bool = false): Where T: string, byte[],JObject, JToken, JArray, XNode, XElement, XDocument<br /><br /> Mit der `context.Request.Body.As<T>`-Methode und der `context.Response.Body.As<T>`-Methode werden ein Anforderungs- und ein Antwortnachrichtentext in einem angegebenen Typ `T` gelesen. Standardmäßig verwendet die Methode den Datenstrom des Originalnachrichtentexts und sorgt dafür, dass er nach seiner Rückgabe nicht mehr verfügbar ist. Wenn dies vermieden werden und die Methode eine Kopie des Textdatenstroms verarbeiten soll, legen Sie den `preserveContent`-Parameter auf `true` fest. Ein Beispiel finden Sie [hier](api-management-transformation-policies.md#SetBody).|
|<a id="ref-iurl"></a>IUrl|Host: string<br /><br /> Path: string<br /><br /> Port: int<br /><br /> [Query](#ref-iurl-query) (Abfrage): IReadOnlyDictionary<string, string[]><br /><br /> QueryString: string<br /><br /> Scheme: string|
|<a id="ref-iuseridentity"></a>IUserIdentity|Id: string<br /><br /> Provider: string|
|<a id="ref-isubscriptionkeyparameternames"></a>ISubscriptionKeyParameterNames|Header: string<br /><br /> Query: string|
|<a id="ref-iurl-query"></a>string IUrl.Query.GetValueOrDefault(queryParameterName: string, defaultValue: string)|queryParameterName: string<br /><br /> defaultValue: string<br /><br /> Gibt durch Trennzeichen getrennte Abfrageparameterwerte oder `defaultValue` zurück, wenn der Parameter nicht gefunden wurde.|
|<a id="ref-context-variables"></a>T context.Variables.GetValueOrDefault<T\>(variableName: string, defaultValue: T)|variableName: string<br /><br /> defaultValue: T<br /><br /> Gibt eine Variablenwertumwandlung in den Typ `T` oder `defaultValue` zurück, wenn die Variable nicht gefunden wird.<br /><br /> Diese Methode löst eine Ausnahme aus, wenn der angegebene Typ nicht mit dem tatsächlichen Typ der zurückgegebenen Variablen übereinstimmt.|
|BasicAuthCredentials AsBasic(input: this string)|input: string<br /><br /> Wenn der Eingabeparameter einen gültigen Anforderungsheaderwert für die Autorisierung der HTTP-Standardauthentifizierung enthält, gibt die Methode ein Objekt des Typs `BasicAuthCredentials` zurück; andernfalls gibt die Methode NULL zurück.|
|bool TryParseBasic(input: this string, result: out BasicAuthCredentials)|input: string<br /><br /> result: out BasicAuthCredentials<br /><br /> Wenn der Eingabeparameter einen gültigen Wert für die HTTP-Standardauthentifizierungsautorisierung im Anforderungsheader enthält, gibt die Methode `true` zurück, und der Ergebnisparameter enthält einen Wert des Typs `BasicAuthCredentials`. Andernfalls gibt die Methode `false` zurück.|
|BasicAuthCredentials|Password: string<br /><br /> UserId: string|
|Jwt AsJwt(input: this string)|input: string<br /><br /> Wenn der Eingabeparameter einen gültigen JWT-Tokenwert enthält, gibt die Methode ein Objekt des Typs `Jwt` zurück; andernfalls gibt die Methode `null` zurück.|
|bool TryParseJwt(input: this string, result: out Jwt)|input: string<br /><br /> result: out Jwt<br /><br /> Wenn der Eingabeparameter einen gültigen JWT-Tokenwert enthält, gibt die Methode `true` zurück, und der Ergebnisparameter enthält einen Wert des Typs `Jwt`; andernfalls gibt die Methode `false` zurück.|
|Jwt|Algorithm: string<br /><br /> Zielgruppen: IEnumerable<string\><br /><br /> Claims: IReadOnlyDictionary<string, string[]><br /><br /> ExpirationTime: DateTime?<br /><br /> Id: string<br /><br /> Issuer: string<br /><br /> IssuedAt: (Ausgestellt um) DateTime?<br /><br /> NotBefore: DateTime?<br /><br /> Subject: string<br /><br /> Type: string|
|string Jwt.Claims.GetValueOrDefault(claimName: string, defaultValue: string)|claimName: string<br /><br /> defaultValue: string<br /><br /> Gibt durch Trennzeichen getrennte Anspruchswerte oder `defaultValue` zurück, wenn der Header nicht gefunden wurde.|
|byte[] Encrypt(input: this byte[], alg: string, key:byte[], iv:byte[])|input - zu verschlüsselnder Klartext<br /><br />alg - Name eines symmetrischen Verschlüsselungsalgorithmus<br /><br />key - Verschlüsselungsschlüssel<br /><br />iv - Initialisierungsvektor<br /><br />Gibt verschlüsselten Klartext zurück.|
|byte[] Encrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm)|input - zu verschlüsselnder Klartext<br /><br />alg - Verschlüsselungsalgorithmus<br /><br />Gibt verschlüsselten Klartext zurück.|
|byte[] Encrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm, key:byte[], iv:byte[])|input - zu verschlüsselnder Klartext<br /><br />alg - Verschlüsselungsalgorithmus<br /><br />key - Verschlüsselungsschlüssel<br /><br />iv - Initialisierungsvektor<br /><br />Gibt verschlüsselten Klartext zurück.|
|byte[] Decrypt(input: this byte[], alg: string, key:byte[], iv:byte[])|input - zu entschlüsselnder Verschlüsselungstext<br /><br />alg - Name eines symmetrischen Verschlüsselungsalgorithmus<br /><br />key - Verschlüsselungsschlüssel<br /><br />iv - Initialisierungsvektor<br /><br />Gibt Klartext zurück.|
|byte[] Decrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm)|input - zu entschlüsselnder Verschlüsselungstext<br /><br />alg - Verschlüsselungsalgorithmus<br /><br />Gibt Klartext zurück.|
|byte[] Decrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm, key:byte[], iv:byte[])|input - zu entschlüsselnder Verschlüsselungstext<br /><br />alg - Verschlüsselungsalgorithmus<br /><br />key - Verschlüsselungsschlüssel<br /><br />iv - Initialisierungsvektor<br /><br />Gibt Klartext zurück.|
|bool VerifyNoRevocation(input: this System.Security.Cryptography.X509Certificates.X509Certificate2)|Führt eine X.509-Kettenüberprüfung ohne Überprüfung des Zertifikatsperrungsstatus aus.<br /><br />Eingabe: Zertifikatobjekt<br /><br />Gibt `true` zurück, wenn die Überprüfung erfolgreich ist, `false`, wenn die Validierung fehlschlägt.|


## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Verwendung von Richtlinien finden Sie unter:

+ [Richtlinien in Azure API Management](api-management-howto-policies.md)
+ [Transform and protect your API](transform-api.md) (Transformieren und Schützen von APIs)
+ Unter [Richtlinien für die API-Verwaltung](./api-management-policies.md) finden Sie eine komplette Liste der Richtlinienanweisungen und der zugehörigen Einstellungen.
+ [API Management-Richtlinienbeispiele](./policy-reference.md)
