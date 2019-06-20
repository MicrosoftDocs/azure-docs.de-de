---
title: Richtlinienausdrücke in Azure API Management | Microsoft-Dokumentation
description: Erfahren Sie mehr über Richtlinienausdrücke in Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: ea160028-fc04-4782-aa26-4b8329df3448
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2019
ms.author: apimpm
ms.openlocfilehash: 9a19165f9ac15f7a40aea0501f960b06efbd63a3
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66304360"
---
# <a name="api-management-policy-expressions"></a>Richtlinienausdrücke in API Management
Die in diesem Artikel erörterte Syntax für Richtlinienausdrücke entspricht C# 7. Jeder Ausdruck besitzt Zugriff auf die implizit bereitgestellte [Kontextvariable](api-management-policy-expressions.md#ContextVariables) und eine zulässige [Teilmenge](api-management-policy-expressions.md#CLRTypes) von .NET Framework-Typen.

Weitere Informationen finden Sie unter:

- Erfahren Sie, wie Kontextinformationen für Ihren Back-End-Dienst bereitgestellt werden. Verwenden Sie die Richtlinien [Abfragezeichenfolgenparameter festlegen](api-management-transformation-policies.md#SetQueryStringParameter) und [HTTP-Header festlegen](api-management-transformation-policies.md#SetHTTPheader), um diese Informationen bereitzustellen.
- Erfahren Sie, wie die Richtlinie [JWT überprüfen](api-management-access-restriction-policies.md#ValidateJWT) verwendet wird, um den Zugriff auf Vorgänge basierend auf Tokenansprüchen vorab zu autorisieren.
- Erfahren Sie, wie eine [API-Inspektor](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/)-Ablaufverfolgung verwendet wird, um die Auswertung von Richtlinien und die Ergebnisse dieser Auswertungen anzuzeigen.
- Erfahren Sie, wie Ausdrücke mit den Richtlinien [Aus Cache abrufen](api-management-caching-policies.md#GetFromCache) und [In Cache speichern](api-management-caching-policies.md#StoreToCache) verwendet werden, um das Zwischenspeichern von Antworten für API Management zu konfigurieren. Legen Sie eine Dauer fest, die dem Zwischenspeichern von Antworten des Back-End-Diensts entspricht, wie in der `Cache-Control`-Anweisung des Back-End-Diensts angegeben.
- Erfahren Sie, wie Inhalte gefiltert werden. Entfernen Sie Datenelemente mit den Richtlinien [Ablaufsteuerung](api-management-advanced-policies.md#choose) und [Text festlegen](api-management-transformation-policies.md#SetBody) aus der vom Back-End empfangenen Antwort.
- Informationen zum Herunterladen der Richtlinienanweisungen finden Sie im GitHub-Repository unter [api-management-samples/policies](https://github.com/Azure/api-management-samples/tree/master/policies).


## <a name="Syntax"></a> Syntax
Ausdrücke mit einer einzelnen Anweisung werden in `@(expression)` eingeschlossen, wobei `expression` eine wohlgeformte C#-Ausdrucksanweisung ist.

Ausdrücke mit mehreren Anweisungen werden in `@{expression}` eingeschlossen. Alle Codepfade in Ausdrücken mit mehreren Anweisungen müssen mit einer `return`-Anweisung enden.

## <a name="PolicyExpressionsExamples"></a> Beispiele

```
@(true)

@((1+1).ToString())

@("Hi There".Length)

@(Regex.Match(context.Response.Headers.GetValueOrDefault("Cache-Control",""), @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value)

@(context.Variables.ContainsKey("maxAge") ? int.Parse((string)context.Variables["maxAge"]) : 3600)

@{
  string value;
  if (context.Request.Headers.TryGetValue("Authorization", out value))
  {
    return Encoding.UTF8.GetString(Convert.FromBase64String(value));
  }
  else
  {
    return null;
  }
}
```

## <a name="PolicyExpressionsUsage"></a>Verwendung
Ausdrücke können als Attributwerte oder Textwerte in beliebigen API Management-[Richtlinien](api-management-policies.md) verwendet werden (sofern in der Richtlinienreferenz nicht anders angegeben).

> [!IMPORTANT]
> Bei Verwendung von Richtlinienausdrücken erfolgt nur eine begrenzte Überprüfung der Richtlinienausdrücke beim Definieren der Richtlinie. Ausdrücke werden vom Gateway zur Laufzeit ausgeführt, und durch Richtlinienausdrücke generierte Ausnahmen führen zu einem Laufzeitfehler.

## <a name="CLRTypes"></a> In Richtlinienausdrücken zulässige .NET Framework-Typen
Die folgende Tabelle enthält die .NET Framework-Typen und die zugehörigen Mitglieder, die in Richtlinienausdrücken zulässig sind.

|Type|Unterstützte Member|
|--------------|-----------------------|
|Newtonsoft.Json.Formatting|Alle|
|Newtonsoft.Json.JsonConvert|SerializeObject, DeserializeObject|
|Newtonsoft.Json.Linq.Extensions|Alle|
|Newtonsoft.Json.Linq.JArray|Alle|
|Newtonsoft.Json.Linq.JConstructor|Alle|
|Newtonsoft.Json.Linq.JContainer|Alle|
|Newtonsoft.Json.Linq.JObject|Alle|
|Newtonsoft.Json.Linq.JProperty|Alle|
|Newtonsoft.Json.Linq.JRaw|Alle|
|Newtonsoft.Json.Linq.JToken|Alle|
|Newtonsoft.Json.Linq.JTokenType|Alle|
|Newtonsoft.Json.Linq.JValue|Alle|
|System.Array|Alle|
|System.BitConverter|Alle|
|System.Boolean|Alle|
|System.Byte|Alle|
|System.Char|Alle|
|System.Collections.Generic.Dictionary<TKey, TValue>|Alle|
|System.Collections.Generic.HashSet<T>|Alle|
|System.Collections.Generic.ICollection<T>|Alle|
|System.Collections.Generic.IDictionary<TKey, TValue>|Alle|
|System.Collections.Generic.IEnumerable<T>|Alle|
|System.Collections.Generic.IEnumerator<T>|Alle|
|System.Collections.Generic.IList<T>|Alle|
|System.Collections.Generic.IReadOnlyCollection<T>|Alle|
|System.Collections.Generic.IReadOnlyDictionary<TKey, TValue>|Alle|
|System.Collections.Generic.ISet<T>|Alle|
|System.Collections.Generic.KeyValuePair<TKey, TValue>|Alle|
|System.Collections.Generic.List<T>|Alle|
|System.Collections.Generic.Queue<T>|Alle|
|System.Collections.Generic.Stack<T>|Alle|
|System.Convert|Alle|
|System.DateTime|(Konstruktor), Add, AddDays, AddHours, AddMilliseconds, AddMinutes, AddMonths, AddSeconds, AddTicks, AddYears, Date, Day, DayOfWeek, DayOfYear, DaysInMonth, Hour, IsDaylightSavingTime, IsLeapYear, MaxValue, Millisecond, Minute, MinValue, Month, Now, Parse, Second, Subtract, Ticks, TimeOfDay, Today, ToString, UtcNow, Year|
|System.DateTimeKind|UTC|
|System.DateTimeOffset|Alle|
|System.Decimal|Alle|
|System.Double|Alle|
|System.Exception|Alle|
|System.Guid|Alle|
|System.Int16|Alle|
|System. Int32|Alle|
|System.Int64|Alle|
|System.IO.StringReader|Alle|
|System.IO.StringWriter|Alle|
|System.Linq.Enumerable|Alle|
|System.Math|Alle|
|System.MidpointRounding|Alle|
|System.Net.WebUtility|Alle|
|System.Nullable|Alle|
|System.Random|Alle|
|System.SByte|Alle|
|System.Security.Cryptography.AsymmetricAlgorithm|Alle|
|System.Security.Cryptography.CipherMode|Alle|
|System.Security.Cryptography.HashAlgorithm|Alle|
|System.Security.Cryptography.HashAlgorithmName|Alle|
|System.Security.Cryptography.HMAC|Alle|
|System.Security.Cryptography.HMACMD5|Alle|
|System.Security.Cryptography.HMACSHA1|Alle|
|System.Security.Cryptography.HMACSHA256|Alle|
|System.Security.Cryptography.HMACSHA384|Alle|
|System.Security.Cryptography.HMACSHA512|Alle|
|System.Security.Cryptography.KeyedHashAlgorithm|Alle|
|System.Security.Cryptography.MD5|Alle|
|System.Security.Cryptography.Oid|Alle|
|System.Security.Cryptography.PaddingMode|Alle|
|System.Security.Cryptography.RNGCryptoServiceProvider|Alle|
|System.Security.Cryptography.RSA|Alle|
|System.Security.Cryptography.RSAEncryptionPadding|Alle|
|System.Security.Cryptography.RSASignaturePadding|Alle|
|System.Security.Cryptography.SHA1|Alle|
|System.Security.Cryptography.SHA1Managed|Alle|
|System.Security.Cryptography.SHA256|Alle|
|System.Security.Cryptography.SHA256Managed|Alle|
|System.Security.Cryptography.SHA384|Alle|
|System.Security.Cryptography.SHA384Managed|Alle|
|System.Security.Cryptography.SHA512|Alle|
|System.Security.Cryptography.SHA512Managed|Alle|
|System.Security.Cryptography.SymmetricAlgorithm|Alle|
|System.Security.Cryptography.X509Certificates.PublicKey|Alle|
|System.Security.Cryptography.X509Certificates.RSACertificateExtensions|Alle|
|System.Security.Cryptography.X509Certificates.X500DistinguishedName|NAME|
|System.Security.Cryptography.X509Certificates.X509Certificate|Alle|
|System.Security.Cryptography.X509Certificates.X509Certificate2|Alle|
|System.Security.Cryptography.X509Certificates.X509ContentType|Alle|
|System.Security.Cryptography.X509Certificates.X509NameType|Alle|
|System.Single|Alle|
|System.String|Alle|
|System.StringComparer|Alle|
|System.StringComparison|Alle|
|System.StringSplitOptions|Alle|
|System.Text.Encoding|Alle|
|System.Text.RegularExpressions.Capture|Index, Length, Value|
|System.Text.RegularExpressions.CaptureCollection|Count, Item|
|System.Text.RegularExpressions.Group|Captures, Success|
|System.Text.RegularExpressions.GroupCollection|Count, Item|
|System.Text.RegularExpressions.Match|Empty, Groups, Result|
|System.Text.RegularExpressions.Regex|(Konstruktor), IsMatch, Match, Matches, Replace, Unescape, Split|
|System.Text.RegularExpressions.RegexOptions|Alle|
|System.Text.StringBuilder|Alle|
|System.TimeSpan|Alle|
|System.TimeZone|Alle|
|System.TimeZoneInfo.AdjustmentRule|Alle|
|System.TimeZoneInfo.TransitionTime|Alle|
|System.TimeZoneInfo|Alle|
|System.Tuple|Alle|
|System.UInt16|Alle|
|System.UInt32|Alle|
|System.UInt64|Alle|
|System.Uri|Alle|
|System.UriPartial|Alle|
|System.Xml.Linq.Extensions|Alle|
|System.Xml.Linq.XAttribute|Alle|
|System.Xml.Linq.XCData|Alle|
|System.Xml.Linq.XComment|Alle|
|System.Xml.Linq.XContainer|Alle|
|System.Xml.Linq.XDeclaration|Alle|
|System.Xml.Linq.XDocument|Alle außer: Laden|
|System.Xml.Linq.XDocumentType|Alle|
|System.Xml.Linq.XElement|Alle|
|System.Xml.Linq.XName|Alle|
|System.Xml.Linq.XNamespace|Alle|
|System.Xml.Linq.XNode|Alle|
|System.Xml.Linq.XNodeDocumentOrderComparer|Alle|
|System.Xml.Linq.XNodeEqualityComparer|Alle|
|System.Xml.Linq.XObject|Alle|
|System.Xml.Linq.XProcessingInstruction|Alle|
|System.Xml.Linq.XText|Alle|
|System.Xml.XmlNodeType|Alle|

## <a name="ContextVariables"></a> Kontextvariable
Eine Variable namens `context` steht implizit in jedem [Richtlinienausdruck](api-management-policy-expressions.md#Syntax) zur Verfügung. Ihre Mitglieder bieten Informationen zu `\request`. Alle `context`-Mitglieder sind schreibgeschützt.

|Kontextvariable|Zulässige Methoden, Eigenschaften und Parameterwerte|
|----------------------|-------------------------------------------------------|
|context|[Api](#ref-context-api): [IApi](#ref-iapi)<br /><br /> [Bereitstellung](#ref-context-deployment)<br /><br /> Elapsed: TimeSpan – Zeitintervall zwischen dem Wert des Zeitstempels und der aktuellen Uhrzeit<br /><br /> [LastError](#ref-context-lasterror)<br /><br /> [Vorgang](#ref-context-operation)<br /><br /> [Produkt](#ref-context-product)<br /><br /> [Anforderung](#ref-context-request)<br /><br /> RequestId: Guid – eindeutiger Bezeichner der Anforderung<br /><br /> [Antwort](#ref-context-response)<br /><br /> [Abonnement](#ref-context-subscription)<br /><br /> Timestamp: DateTime – Zeitpunkt des Empfangs der Anforderung<br /><br /> Tracing: bool – gibt an, ob die Ablaufverfolgung aktiviert oder deaktiviert ist <br /><br /> [Benutzer](#ref-context-user)<br /><br /> [Variablen:](#ref-context-variables) IReadOnlyDictionary<string, object><br /><br /> void Trace(message: string)|
|<a id="ref-context-api"></a>context.Api|Id: string<br /><br /> IsCurrentRevision: bool<br /><br />  Name: string<br /><br /> Path: string<br /><br /> Revision: string<br /><br /> ServiceUrl: [IUrl](#ref-iurl)<br /><br /> Version: string |
|<a id="ref-context-deployment"></a>context.Deployment|Region: string<br /><br /> ServiceName: string<br /><br /> Certificates: IReadOnlyDictionary<string, X509Certificate2>|
|<a id="ref-context-lasterror"></a>context.LastError|Source: string<br /><br /> Reason: string<br /><br /> Message: string<br /><br /> Scope: string<br /><br /> Section: string<br /><br /> Path: string<br /><br /> PolicyId: string<br /><br /> Weitere Informationen zu context.LastError finden Sie unter [Fehlerbehandlung](api-management-error-handling-policies.md).|
|<a id="ref-context-operation"></a>context.Operation|Id: string<br /><br /> Method: string<br /><br /> Name: string<br /><br /> UrlTemplate: string|
|<a id="ref-context-product"></a>context.Product|Apis: IEnumerable<[IApi](#ref-iapi)\><br /><br /> ApprovalRequired: bool<br /><br /> Groups: IEnumerable<[IGroup](#ref-igroup)\><br /><br /> Id: string<br /><br /> Name: string<br /><br /> State: enum ProductState {NotPublished, Published}<br /><br /> SubscriptionLimit: int?<br /><br /> SubscriptionRequired: bool|
|<a id="ref-context-request"></a>context.Request|Hauptteil: [IMessageBody](#ref-imessagebody)<br /><br /> Certificate: System.Security.Cryptography.X509Certificates.X509Certificate2<br /><br /> [Headers](#ref-context-request-headers): IReadOnlyDictionary<string, string[]><br /><br /> IpAddress: string<br /><br /> MatchedParameters: IReadOnlyDictionary<string, string><br /><br /> Method: string<br /><br /> OriginalUrl: [IUrl](#ref-iurl)<br /><br /> Url: [IUrl](#ref-iurl)|
|<a id="ref-context-request-headers"></a>string context.Request.Headers.GetValueOrDefault(headerName: string, defaultValue: string)|headerName: string<br /><br /> defaultValue: string<br /><br /> Gibt durch Trennzeichen getrennte Anforderungsheaderwerte oder `defaultValue` zurück, wenn der Header nicht gefunden wurde.|
|<a id="ref-context-response"></a>context.Response|Hauptteil: [IMessageBody](#ref-imessagebody)<br /><br /> [Headers](#ref-context-response-headers): IReadOnlyDictionary<string, string[]><br /><br /> StatusCode: int<br /><br /> StatusReason: string|
|<a id="ref-context-response-headers"></a>string context.Response.Headers.GetValueOrDefault(headerName: string, defaultValue: string)|headerName: string<br /><br /> defaultValue: string<br /><br /> Gibt durch Trennzeichen getrennte Antwortheaderwerte oder `defaultValue` zurück, wenn der Header nicht gefunden wurde.|
|<a id="ref-context-subscription"></a>context.Subscription|CreatedTime: DateTime<br /><br /> EndDate: DateTime?<br /><br /> Id: string<br /><br /> Key: string<br /><br /> Name: string<br /><br /> PrimaryKey: string<br /><br /> SecondaryKey: string<br /><br /> StartDate: DateTime?|
|<a id="ref-context-user"></a>context.User|Email: string<br /><br /> FirstName: string<br /><br /> Groups: IEnumerable<[IGroup](#ref-igroup)\><br /><br /> Id: string<br /><br /> Identities: IEnumerable<[IUserIdentity](#ref-iuseridentity)\><br /><br /> LastName: string<br /><br /> Note: string<br /><br /> RegistrationDate: DateTime|
|<a id="ref-iapi"></a>IApi|Id: string<br /><br /> Name: string<br /><br /> Path: string<br /><br /> Protocols: IEnumerable<string\><br /><br /> ServiceUrl: [IUrl](#ref-iurl)<br /><br /> SubscriptionKeyParameterNames: [ISubscriptionKeyParameterNames](#ref-isubscriptionkeyparameternames)|
|<a id="ref-igroup"></a>IGroup|Id: string<br /><br /> Name: string|
|<a id="ref-imessagebody"></a>IMessageBody|As<T\>(preserveContent: bool = false): Where T: string, JObject, JToken, JArray, XNode, XElement, XDocument<br /><br /> Mit der `context.Request.Body.As<T>`-Methode und der `context.Response.Body.As<T>`-Methode werden ein Anforderungs- und ein Antwortnachrichtentext in einem angegebenen Typ `T` gelesen. Standardmäßig verwendet die Methode den Datenstrom des Originalnachrichtentexts und sorgt dafür, dass er nach seiner Rückgabe nicht mehr verfügbar ist. Wenn dies vermieden werden und die Methode eine Kopie des Textdatenstroms verarbeiten soll, legen Sie den `preserveContent`-Parameter auf `true` fest. Ein Beispiel finden Sie [hier](api-management-transformation-policies.md#SetBody).|
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
|Jwt|Algorithm: string<br /><br /> Audience: IEnumerable<string\><br /><br /> Claims: IReadOnlyDictionary<string, string[]><br /><br /> ExpirationTime: DateTime?<br /><br /> Id: string<br /><br /> Issuer: string<br /><br /> IssuedAt: (Ausgestellt um) DateTime?<br /><br /> NotBefore: DateTime?<br /><br /> Subject: string<br /><br /> Type: string|
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
+ Unter [Richtlinien für die API-Verwaltung](api-management-policy-reference.md) finden Sie eine komplette Liste der Richtlinienanweisungen und der zugehörigen Einstellungen.
+ [API Management policy samples](policy-samples.md) (API Management-Richtlinienbeispiele)
