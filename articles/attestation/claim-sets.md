---
title: Azure Attestation-Anspruchsätze
description: Die Anspruchsätze von Azure Attestation
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: e82e9fc93bf8c816fcbfd5869156745dea630313
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517554"
---
# <a name="claim-sets"></a>Anspruchsätze

Ansprüche, die während der Nachweisgenerierung für Enclaves mit Microsoft Azure Attestation generiert werden, können in die folgenden Kategorien unterteilt werden:

- **Eingehende Ansprüche**: Dies sind Ansprüche, die von Microsoft Azure Attestation nach dem Analysieren der Nachweisdaten generiert werden. Sie können von Richtlinienerstellern genutzt werden, um in einer benutzerdefinierten Richtlinie Autorisierungsregeln zu definieren.

- **Ausgehende Ansprüche**: Von Azure Attestation generierte und im Nachweistoken enthaltene Ansprüche.

- **Eigenschaftsansprüche**: Ansprüche, die von Azure Attestation als Ausgabe erstellt werden. Dies enthält alle Ansprüche, die Eigenschaften des Nachweistokens darstellen, wie z. B. die Codierung des Berichts, die Gültigkeitsdauer des Berichts usw.

## <a name="incoming-claims"></a>Eingehende Ansprüche 

### <a name="sgx-attestation"></a>SGX-Nachweis

Ansprüche, die von Richtlinienerstellern verwendet werden, um Autorisierungsregeln in einer SGX-Nachweisrichtlinie zu definieren:

- **x-ms-sgx-is-debuggable**: Ein boolescher Wert, der angibt, ob Debugging für die Enclave aktiviert ist.
  
  SGX-Enclaves können mit deaktiviertem oder aktiviertem Debuggen geladen werden. Wenn das Flag in der Enclave auf „true“ festgelegt ist, werden Debugfeatures für den Enclave-Code aktiviert. Dies schließt auch die Möglichkeit mit ein, auf den Arbeitsspeicher der Enclave zuzugreifen. Daher sollte das Flag nur zu Entwicklungszwecken auf „true“ festgelegt werden. Wenn diese Option in einer Produktionsumgebung aktiviert wird, erlöschen die SGX-Sicherheitsgarantien.
  
  Azure Attestation-Benutzer können mithilfe der Nachweisrichtlinie überprüfen, ob das Debuggen für die SGX-Enclave deaktiviert ist. Nach dem Hinzufügen der Richtlinienregel tritt beim Nachweis ein Fehler auf, wenn ein böswilliger Benutzer die Debugunterstützung aktiviert, um Zugriff auf den Enclave-Inhalt zu erlangen.

- **x-ms-sgx-product-id**: Ein ganzzahliger Wert, der die Produkt-ID der SGX-Enclave angibt.

  Vom Enclave-Autor wird jeder Enclave eine Produkt-ID zugewiesen. Die Produkt-ID ermöglicht es dem Enclave-Autor, Enclaves zu segmentieren, die mit dem gleichen MRSIGNER-Wert signiert wurden. Durch Hinzufügen einer Validierungsregel zur Nachweisrichtlinie können Kunden überprüfen, ob sie die beabsichtigten Enclaves verwenden. Beim Nachweis tritt ein Fehler auf, wenn die Produkt-ID der Enclave nicht dem vom Enclave-Autor veröffentlichten Wert entspricht.

- **x-ms-sgx-mrsigner**: Ein Zeichenfolgenwert, der den Autor der SGX-Enclave identifiziert.

  MRSIGNER ist der Hash des öffentlichen Schlüssels des Enclave-Autors, der zum Signieren der Binärdatei der Enclave verwendet wird. Durch die Überprüfung des MRSIGNER-Werts über eine Nachweisrichtlinie können Kunden prüfen, ob innerhalb einer Enclave vertrauenswürdige Binärdateien ausgeführt werden. Wenn der Richtlinienanspruch nicht dem MRSIGNER-Wert des Enclave-Autors entspricht, deutet dies darauf hin, dass die Binärdatei der Enclave nicht von einer vertrauenswürdigen Quelle signiert wurde, und bei dem Nachweis tritt ein Fehler auf.
  
  Wenn ein Enclave-Autor MRSIGNER aus Sicherheitsgründen rotieren möchte, muss die Azure Attestation-Richtlinie aktualisiert werden, um die neuen und alten MRSIGNER-Werte zu unterstützen, bevor die Binärdateien aktualisiert werden. Andernfalls tritt bei Autorisierungsprüfungen und somit bei Nachweisen ein Fehler auf.
  
  Die Nachweisrichtlinie muss im folgenden Format aktualisiert werden: 
 
  #### <a name="before-key-rotation"></a>Vor der Schlüsselrotation
 
   ```
    version= 1.0;
    authorizationrules 
    {
    [ type=="x-ms-sgx-is-debuggable", value==false]&&
    [ type=="x-ms-sgx-mrsigner", value=="mrsigner1"] => permit(); 
    };
  ```

   #### <a name="during-key-rotation"></a>Während der Schlüsselrotation

    ```
      version= 1.0;
      authorizationrules 
      {
      [ type=="x-ms-sgx-is-debuggable", value==false]&&
      [ type=="x-ms-sgx-mrsigner", value=="mrsigner1"] => permit(); 
      [ type=="x-ms-sgx-is-debuggable", value==false ]&& 
      [ type=="x-ms-sgx-mrsigner", value=="mrsigner2"] => permit(); 
      };
    ```

   #### <a name="after-key-rotation"></a>Nach der Schlüsselrotation

    ```
      version= 1.0;
      authorizationrules 
      { 
      [ type=="x-ms-sgx-is-debuggable", value==false]&& 
      [ type=="x-ms-sgx-mrsigner", value=="mrsigner2"] => permit(); 
      };
    ```

- **x-ms-sgx-mrenclave**: Ein Zeichenfolgenwert, der den Code und die Daten identifiziert, die in den Enclave-Arbeitsspeicher geladen wurden. 

  MRENCLAVE ist eine der Enclave-Messungen, die zum Überprüfen der Enclave-Binärdateien verwendet werden können. Hierbei handelt es sich um den Hash des Codes, der innerhalb der Enclave ausgeführt wird. Die Messung ändert sich mit jeder Änderung am Binärcode der Enclave. Durch die Überprüfung des MRENCLAVE-Werts über eine Nachweisrichtlinie können Kunden prüfen, ob innerhalb einer Enclave die beabsichtigten Binärdateien ausgeführt werden. Da sich MRENCLAVE jedoch bei jeder noch so kleinen Änderung des vorhandenen Codes ändert, empfiehlt es sich, Enclave-Binärdateien mithilfe der MRSIGNER-Überprüfung in einer Nachweisrichtlinie zu überprüfen.

- **x-ms-sgx-svn**: Ein ganzzahliger Wert, der die Sicherheitsversionsnummer der SGX-Enclave angibt.

  Der Enclave-Autor weist jeder Version der SGX-Enclave eine Sicherheitsversionsnummer (SVN) zu. Wird im Enclave-Code ein Sicherheitsproblem erkannt, erhöht der Enclave-Autor den SVN-Wert nach der Beseitigung des Sicherheitsrisikos. Um die Interaktion mit unsicherem Enclave-Code zu verhindern, können Kunden der Nachweisrichtlinie eine entsprechende Validierungsregel hinzufügen. Wenn die SVN des Enclave-Codes nicht der vom Enclave-Autor empfohlenen Version entspricht, tritt bei dem Nachweis ein Fehler auf.

Die unten angegebenen Ansprüche werden als veraltet eingestuft, aber sie werden vollständig unterstützt und sind auch in Zukunft noch vorhanden. Wir empfehlen Ihnen, die nicht als veraltet eingestuften Anspruchsnamen zu verwenden.

Veralteter Anspruch | Empfohlener Anspruch
--- | --- 
$is-debuggable | x-ms-sgx-is-debuggable
$product-id | x-ms-sgx-product-id
$sgx-mrsigner | x-ms-sgx-mrsigner
$sgx-mrenclave | x-ms-sgx-mrenclave
$svn | x-ms-sgx-svn

### <a name="tpm-attestation"></a>TPM-Nachweis

Ansprüche, die von Richtlinienerstellern verwendet werden, um Autorisierungsregeln in einer TPM-Nachweisrichtlinie zu definieren:

- **aikValidated**: Ein boolescher Wert, der Informationen dazu enthält, ob das Zertifikat des Nachweisidentitätsschlüssels (Attestation Identity Key, AIK) überprüft wurde
- **aikPubHash**:  Eine Zeichenfolge mit base64 (SHA256 (öffentlicher AIK-Schlüssel im DER-Format))
- **tpmVersion**:   Ein ganzzahliger Wert, der die Hauptversion des Trusted Platform Module (TPM) enthält
- **secureBootEnabled**: Ein boolescher Wert, der angibt, ob der sichere Start aktiviert ist
- **iommuEnabled**:  Ein boolescher Wert, der angibt, ob IOMMU (Input-Output Memory Management Unit) aktiviert ist
- **bootDebuggingDisabled**: Ein boolescher Wert, der angibt, ob das Startdebugging deaktiviert ist
- **notSafeMode**:  Ein boolescher Wert, der angibt, wenn Windows nicht im abgesicherten Modus ausgeführt wird
- **notWinPE**:  Ein boolescher Wert, der angibt, wenn Windows nicht im WinPE-Modus ausgeführt wird
- **vbsEnabled**:  Ein boolescher Wert, der angibt, ob VBS aktiviert ist
- **vbsReportPresent**:  Ein boolescher Wert, der angibt, ob der VBS-Enclavebericht verfügbar ist

### <a name="vbs-attestation"></a>VBS-Nachweis

Zusätzlich zu den TPM-Nachweisrichtlinienansprüchen können Richtlinienersteller auch die folgenden Ansprüche verwenden, um Autorisierungsregeln in einer VBS-Nachweisrichtlinie zu definieren:

- **enclaveAuthorId**:  Ein Zeichenfolgenwert, der den Base64Url-codierten Wert der Enclave-Autor-ID enthält. Der Autorenbezeichner des primären Moduls für die Enclave
- **enclaveImageId**:  Ein Zeichenfolgenwert, der den Base64Url-codierten Wert der Enclave-Image-ID enthält. Der Imagebezeichner des primären Moduls für die Enclave
- **enclaveOwnerId**:  Ein Zeichenfolgenwert, der den Base64Url-codierten Wert der Enclave-Besitzer-ID enthält. Der Bezeichner des Besitzers des primären Moduls für die Enclave
- **enclaveFamilyId**:  Ein Zeichenfolgenwert, der den Base64Url-codierten Wert der Enclave-Familien-ID enthält – Der Familienbezeichner des primären Moduls für die Enclave
- **enclaveSvn**:  Ein ganzzahliger Wert, der die Sicherheitsversionsnummer des primären Moduls für die Enclave enthält
- **enclavePlatformSvn**:  Ein ganzzahliger Wert, der die Sicherheitsversionsnummer der Plattform enthält, die die Enclave hostet
- **enclaveFlags**:  Der enclaveFlags-Anspruch ist ein ganzzahliger Wert, der Flags enthält, die die Runtimerichtlinie für die Enclave beschreiben

## <a name="outgoing-claims"></a>Ausgehende Ansprüche 

### <a name="common-for-all-attestation-types"></a>Für alle Nachweistypen

Von Azure Attestation werden in das Nachweistoken folgende Ansprüche für alle Nachweistypen eingeschlossen: 

- **x-ms-ver**: JWT-Schemaversion („1.0“ wird erwartet)
- **x-ms-attestation-type**: Zeichenfolgenwert für Nachweistyp 
- **x-ms-policy-hash**: Hash der Azure Attestation-Evaluierungsrichtlinie, Berechnung als BASE64URL(SHA256(UTF8(BASE64URL(UTF8(Richtlinientext)))))
- **x-ms-policy-signer**: JSON-Objekt mit Member „jwk“, das den Schlüssel darstellt, den ein Kunde zum Signieren seiner Richtlinie verwendet hat. Relevant, wenn der Kunde eine signierte Richtlinie hochlädt.

Die folgenden Anspruchsnamen stammen aus der [IETF-JWT-Spezifikation](https://tools.ietf.org/html/rfc7519):

- Anspruch **„jti“ (JWT ID)** (JWT-ID): Eindeutiger Bezeichner für das JWT.
- Anspruch **„iss“ (Issuer)** (Aussteller): Der Prinzipal, der das JWT ausgestellt hat. 
- Anspruch **„iat“ (Issued At)** (Ausgestellt um): Der Zeitpunkt, zu dem das JWT ausgestellt wurde. 
- Anspruch **„exp“ (Expiration Time)** (Ablaufzeitpunkt): Der Ablaufzeitpunkt, nach dem das JWT nicht mehr für die Verarbeitung akzeptiert werden darf.
- Anspruch **„nbf“ (Not Before)** (Nicht vor): Die Zeit, vor der das JWT nicht für die Verarbeitung akzeptiert werden darf. 

Die folgenden Anspruchsnamen stammen aus der [IETF-EAT-Entwurfsspezifikation](https://tools.ietf.org/html/draft-ietf-rats-eat-03#page-9):

- **Nonce-Anspruch (Nonce)** : Eine nicht transformierte direkte Kopie eines optionalen, von einem Client bereitgestellten Nonce-Werts. 

Die unten angegebenen Ansprüche werden als veraltet eingestuft, aber sie werden vollständig unterstützt und sind auch in Zukunft noch vorhanden. Wir empfehlen Ihnen, die nicht als veraltet eingestuften Anspruchsnamen zu verwenden.

Veralteter Anspruch | Empfohlener Anspruch
--- | --- 
ver | x-ms-ver
tee | x-ms-attestation-type
policy_hash | x-ms-policy-hash
maa-policyHash | x-ms-policy-hash
policy_signer  | x-ms-policy-signer

### <a name="sgx-attestation"></a>SGX-Nachweis 

Die folgenden Ansprüche werden vom Dienst generiert und für den SGX-Nachweis in das Nachweistoken eingefügt.

- **x-ms-sgx-is-debuggable**: ein boolescher Wert, der angibt, ob Debugging für die Enclave aktiviert ist oder nicht
- **x-ms-sgx-product-id**: Produkt-ID-Wert der SGX-Enclave 
- **x-ms-sgx-mrsigner**: Hexadezimal codierter Wert des Felds „mrsigner“ des Angebots
- **x-ms-sgx-mrenclave**: Hexadezimal codierter Wert des Felds „mrenclave“ des Angebots
- **x-ms-sgx-svn**: Im Angebot codierte Sicherheitsversionsnummer 
- **x-ms-sgx-ehd**: In Enklave enthaltene Daten im Format „BASE64URL(Enclave Held Data)“
- **x-ms-sgx-collateral**: JSON-Objekt mit der Beschreibung der Begleitdaten für die Durchführung des Nachweisvorgangs. Der Wert für den Anspruch „x-ms-sgx-collateral“ ist ein geschachteltes JSON-Objekt mit den folgenden Schlüssel-Wert-Paaren:
    - **qeidcertshash**: SHA256-Wert der QE-Identität (Quoting Enclave), von der Zertifikate ausgestellt werden
    - **qeidcrlhash**: SHA256-Wert der QE-Identität, von der Zertifikate (Zertifikatssperrliste) ausgestellt werden
    - **qeidhash**: SHA256-Wert der Begleitdaten der QE-Identität
    - **quotehash**: SHA256-Wert des ausgewerteten Angebots
    - **tcbinfocertshash**: SHA256-Wert der TCB-Informationen zum Ausstellen von Zertifikaten
    - **tcbinfocrlhash**: SHA256-Wert der TCB-Informationen zum Ausstellen von Zertifikaten (Zertifikatssperrliste)
    - **tcbinfohash**: SHA256-Wert der Begleitdaten für TCB-Informationen

Die unten angegebenen Ansprüche werden als veraltet eingestuft, aber sie werden vollständig unterstützt und sind auch in Zukunft noch vorhanden. Wir empfehlen Ihnen, die nicht als veraltet eingestuften Anspruchsnamen zu verwenden.

Veralteter Anspruch | Empfohlener Anspruch
--- | --- 
$is-debuggable | x-ms-sgx-is-debuggable
$product-id | x-ms-sgx-product-id
$sgx-mrsigner | x-ms-sgx-mrsigner
$sgx-mrenclave | x-ms-sgx-mrenclave
$svn | x-ms-sgx-svn
$maa-ehd | x-ms-sgx-ehd
$aas-ehd | x-ms-sgx-ehd
$maa-attestationcollateral | x-ms-sgx-collateral

### <a name="tpm-and-vbs-attestation"></a>TPM- und VBS-Nachweis

- **cnf (Bestätigung):** Der Anspruch „cnf“ wird verwendet, um den Eigentumsnachweisschlüssel zu identifizieren. Bestätigungsanspruch wie in RFC 7800 definiert, enthält den öffentlichen Teil des nachgewiesenen Enclaveschlüssel, der als JSON Web Key-Objekt (JWK) dargestellt ist (RFC 7517)
- **rp_data (Daten der vertrauenden Seite)** : Die Daten der vertrauenden Seite, sofern in der Anforderung angegeben, die von der vertrauenden Seite als Nonce verwendet wird, um sicherzustellen, dass der Bericht aktuell ist rp_data wird nur hinzugefügt, wenn rp_data vorhanden ist.

## <a name="property-claims"></a>Eigenschaftsansprüche

### <a name="tpm-and-vbs-attestation"></a>TPM- und VBS-Nachweis

- **report_validity_in_minutes**: Ein ganzzahliger Anspruch zur Angabe der Gültigkeitsdauer des Tokens
  - **Default value(time)** : Ein Tag in Minuten
  - **Maximum value(time)** : Ein Jahr in Minuten
- **omit_x5c**: Boolescher Wert, der angibt, ob Azure Attestation das Zertifikat auslassen soll, mit dem die Dienstauthentizität nachgewiesen werden soll. Wenn dieser Wert wahr ist, wird x5t zum Nachweistoken hinzugefügt. Wenn dieser Wert falsch ist (Standard), wird x5t zum Nachweistoken hinzugefügt.

## <a name="next-steps"></a>Nächste Schritte
- [Erstellen und Signieren einer Nachweisrichtlinie](author-sign-policy.md)
- [Einrichten von Azure Attestation mithilfe von PowerShell](quickstart-powershell.md)
