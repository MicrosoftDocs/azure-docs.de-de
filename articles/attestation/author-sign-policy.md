---
title: Erstellen einer Azure Attestation-Richtlinie
description: Hier wird erläutert, wie Sie eine Nachweisrichtlinie erstellen.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 3e36de62b79788e2efdc3e9abf711924c4fba0c4
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93341806"
---
# <a name="how-to-author-an-attestation-policy"></a>Erstellen einer Nachweisrichtlinie

Bei einer Nachweisrichtlinie handelt es sich um eine Datei, die in Microsoft Azure Attestation hochgeladen wird. Mit Azure Attestation kann eine Richtlinie in einem nachweisspezifischen Richtlinienformat hochgeladen werden. Alternativ kann auch eine mit JSON Web Signature codierte Version der Richtlinie hochgeladen werden. Der Richtlinienadministrator ist für das Schreiben der Nachweisrichtlinie verantwortlich. In den meisten Nachweisszenarien fungiert die vertrauende Seite als Richtlinienadministrator. Der Client, der den Nachweisaufruf durchführt, sendet einen Nachweisbeweis. Dieser wird vom Dienst analysiert und in eingehende Ansprüche konvertiert (verschiedene Eigenschaften, Wert). Anschließend verarbeitet der Dienst die Ansprüche basierend auf der Definition in der Richtlinie und gibt das berechnete Ergebnis zurück.

Die Richtlinie enthält Regeln, die die Autorisierungskriterien, Eigenschaften und den Inhalt des Nachweistokens bestimmen. Eine Beispielrichtliniendatei sieht wie folgt aus:

```
version=1.0;
authorizationrules
{
   c:[type="secureBootEnables", issuer=="AttestationService"]=> permit()
};

issuancerules
{
  c:[type="secureBootEnables", issuer=="AttestationService"]=> issue(claim=c)
  c:[type="notSafeMode", issuer=="AttestationService"]=> issue(claim=c)
};
```
 
Eine Richtliniendatei enthält drei Segmente, wie oben gezeigt:

- **version** :  Die Version ist die Versionsnummer der befolgten Grammatik. 

    ```
    version=MajorVersion.MinorVersion   
    ```

    Die einzige derzeit unterstützte Version lautet „1.0“.

- **authorizationrules** : Eine Sammlung von Anspruchsregeln, die zuerst geprüft werden, um zu bestimmen, ob Azure Attestation mit **issuancerules** fortfahren soll. Die Anspruchsregeln werden in der Reihenfolge angewendet, in der sie definiert wurden.

- **issuancerules** : Eine Sammlung von Anspruchsregeln, die ausgewertet werden, um dem Nachweisergebnis gemäß der Definition in der Richtlinie weitere Informationen hinzuzufügen. Die Anspruchsregeln werden in der Reihenfolge angewendet, in der sie definiert wurden, und sind ebenfalls optional.

Weitere Informationen finden Sie unter [Anspruch und Anspruchsregeln](claim-rule-grammar.md).
   
## <a name="drafting-the-policy-file"></a>Entwerfen der Richtliniendatei

1. Erstellen Sie eine neue Datei.
1. Fügen Sie der Datei eine Version hinzu.
1. Fügen Sie Abschnitte für **authorizationrules** und **issuancerules** hinzu.

  ```
  version=1.0;
  authorizationrules
  {
  =>deny();
  };
  
  issuancerules
  {
  };
  ```

  Die Autorisierungsregeln enthalten die Aktion „deny()“ ohne jegliche Bedingung. So wird sichergestellt, dass keine Ausstellungsregeln verarbeitet werden. Alternativ kann die Autorisierungsregel auch die Aktion „permit()“ enthalten, um die Verarbeitung von Ausstellungsregeln zuzulassen.
  
4. Hinzufügen von Anspruchsregeln zu den Autorisierungsregeln

  ```
  version=1.0;
  authorizationrules
  {
  [type=="secureBootEnabled", value==true, issuer=="AttestationService"]=>permit();
  };
  
  issuancerules
  {
  };
  ```

  Wenn der eingehende Anspruchssatz einen Anspruch enthält, der mit Typ, Wert und Aussteller übereinstimmt, weist die Aktion „permit()“ die Richtlinien-Engine an, **issuancerules** zu verarbeiten.
  
5. Fügen Sie **issuancerules** Anspruchsregeln hinzu.

  ```
  version=1.0;
  authorizationrules
  {
  [type=="secureBootEnabled", value==true, issuer=="AttestationService"]=>permit();
  };
  
  issuancerules
  {
  => issue(type="SecurityLevelValue", value=100);
  };
  ```
  
  Der ausgehende Anspruchssatz enthält einen Anspruch mit folgenden Angaben:

  ```
  [type="SecurityLevelValue", value=100, valueType="Integer", issuer="AttestationPolicy"]
  ```

  Komplexe Richtlinien können auf ähnliche Weise erstellt werden. Weitere Informationen finden Sie unter [Beispiele für eine Nachweisrichtlinie](policy-examples.md).
  
6. Speichern Sie die Datei .

## <a name="creating-the-policy-file-in-json-web-signature-format"></a>Erstellen der Richtliniendatei im JSON Web Signature-Format

Führen Sie nach der Erstellung einer Richtliniendatei die folgenden Schritte aus, um eine Richtlinie im JWS-Format hochzuladen:

1. Generieren Sie die JWS-Datei (RFC 7515) mit der Richtlinie (UTF-8-codiert) als Nutzlast.
     - Die Nutzlast-ID für die Base64Url-codierte Richtlinie muss „AttestationPolicy“ lauten.
     
     JWT-Beispiel:
     ```
     Header: {"alg":"none"}
     Payload: {"AttestationPolicy":" Base64Url (policy)"}
     Signature: {}

     JWS format: eyJhbGciOiJub25lIn0.XXXXXXXXX.
     ```

2. (Optional) Signieren Sie die Richtlinie. Azure Attestation unterstützt die folgenden Algorithmen:
     - **Keine:** Signieren Sie die Richtliniennutzlast nicht.
     - **RS256** : Unterstützter Algorithmus zum Signieren der Richtliniennutzlast

3. Laden Sie die JWS-Datei hoch, und überprüfen Sie die Richtlinie.
     - Enthält die Richtliniendatei keine Syntaxfehler, wird sie vom Dienst akzeptiert.
     - Enthält die Richtliniendatei Syntaxfehler, wird sie vom Dienst abgelehnt.

## <a name="next-steps"></a>Nächste Schritte
- [Einrichten von Azure Attestation mithilfe von PowerShell](quickstart-powershell.md)
- [Erbringen eines Nachweises für eine SGX-Enclave mithilfe von Codebeispielen](/samples/browse/?expanded=azure&terms=attestation)