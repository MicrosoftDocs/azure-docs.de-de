---
title: 'Azure Attestation: Leitfaden zur Problembehandlung'
description: Leitfaden zur Behandlung von häufig auftretenden Problemen
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: reference
ms.date: 07/20/2020
ms.author: mbaldwin
ms.openlocfilehash: 72d7a40c38f3629a70260f223074b456dff9ce38
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96182834"
---
# <a name="microsoft-azure-attestation-troubleshooting-guide"></a>Microsoft Azure Attestation: Leitfaden zur Problembehandlung

Die Fehlerbehandlung in Azure Attestation wird gemäß den [Richtlinien zur Microsoft-REST-API](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#7102-error-condition-responses) implementiert. Die Fehlerantwort, die von Azure Attestation-APIs zurückgegeben wird, enthält den HTTP-Statuscode und Name-Wert-Paare mit den Namen „code“ und „message“. Der Wert von „code“ ist für Menschen lesbar und ein Indikator für den Fehlertyp. Der Wert von „message“ dient dem Benutzer als Hilfe und enthält Details zum Fehler.

Wenn Ihr Problem in diesem Artikel nicht behandelt wird, können Sie auf der [Azure-Supportseite](https://azure.microsoft.com/support/options/) auch eine Azure-Supportanfrage einreichen.

Unten sind einige Beispiele für die Fehler angegeben, die von Azure Attestation zurückgegeben werden:

## <a name="1-http401--unauthorized-exception"></a>1. HTTP 401: Ausnahme „Nicht autorisiert“

### <a name="http-status-code"></a>HTTP-Statuscode
401

**Fehlercode**: Nicht autorisiert

**Szenariobeispiele**
  - Nachweisfehler, wenn dem Benutzer die Attestation-Rolle „Leser“ nicht zugewiesen ist
  - Nachweisrichtlinien können nicht verwaltet werden, da dem Benutzer nicht die richtigen Rollen zugewiesen sind
  - Nachweisrichtlinien-Signaturgeber können nicht verwaltet werden, da dem Benutzer nicht die richtigen Rollen zugewiesen sind

Ein Benutzer mit der Rolle „Leser“ versucht, in PowerShell eine Nachweisrichtlinie zu bearbeiten: 

  ```powershell
  Set-AzAttestationPolicy : Operation returned HTTP Status Code 401
At line:1 char:1
+ Set-AzAttestationPolicy -Name $attestationProvider -ResourceGroupName ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzAttestationPolicy], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.SetAzureAttestationPolicy
  ```

**Schritte zur Problembehandlung**

Zum Anzeigen von Nachweisrichtlinien bzw. Richtliniensignaturgebern benötigt ein Azure AD-Benutzer die Berechtigung für „Aktionen“:
- Microsoft.Attestation/attestationProviders/attestation/read

  Diese Berechtigung kann einem AD-Benutzer über eine Rolle wie „Besitzer“ (Platzhalterberechtigungen), „Leser“ (Platzhalterberechtigungen) oder „Nachweisleser“ (spezifische Berechtigungen nur für Azure Attestation) zugewiesen werden.

Zum Hinzufügen/Löschen von Richtliniensignaturgebern oder Konfigurieren von Richtlinien benötigt ein Azure AD-Benutzer die folgenden Berechtigungen für „Aktionen“:
- Microsoft.Attestation/attestationProviders/attestation/write
- Microsoft.Attestation/attestationProviders/attestation/delete

  Diese Berechtigungen können einem AD-Benutzer über eine Rolle wie „Besitzer“ (Platzhalterberechtigungen), „Mitwirkender“ (Platzhalterberechtigungen) oder „Mitwirkender an Nachweis“ (spezifische Berechtigungen nur für Azure Attestation) zugewiesen werden.

Kunden können den Standardanbieter für den Nachweis verwenden oder eigene Anbieter mit benutzerdefinierten Richtlinien erstellen. Zum Senden von Nachweisanforderungen an benutzerdefinierte Nachweisanbieter benötigt der Benutzer die Rolle „Besitzer“ (Platzhalterberechtigungen), „Leser“ (Platzhalterberechtigungen) oder „Nachweisleser“. Alle Azure AD-Benutzer können auf die Standardanbieter zugreifen.

Führen Sie die folgenden Schritte aus, um die Rollen in PowerShell zu überprüfen:

a. Starten Sie PowerShell, und melden Sie sich über das Cmdlet „Connect-AzAccount“ bei Azure an.

b. Überprüfen Sie Ihre Einstellungen für die Azure-Rollenzuweisung.


  ```powershell
  $c = Get-AzContext
  Get-AzRoleAssignment -ResourceGroupName $attestationResourceGroup -ResourceName $attestationProvider -ResourceType Microsoft.Attestation/attestationProviders -SignInName $c.Account.Id
  ```

  Die Ausgabe sollte in etwa wie folgt aussehen:

  ```
  RoleAssignmentId   :/subscriptions/subscriptionId/providers/Microsoft.Authorization/roleAssignments/roleAssignmentId
  
  Scope              : /subscriptions/subscriptionId
  
  DisplayName        : displayName
  
  SignInName         : signInName
  
  RoleDefinitionName : Reader
  
  RoleDefinitionId   : roleDefinitionId
  
  ObjectId           : objectid
  
  ObjectType         : User
  
  CanDelegate        : False
 
  ```

c. Befolgen Sie [diese Anleitung](../role-based-access-control/role-assignments-powershell.md), falls Sie in der Liste keine passende Rollenzuweisung finden.

## <a name="2-http--400-errors"></a>2. HTTP 400-Fehler

### <a name="http-status-code"></a>HTTP-Statuscode
400

Es gibt verschiedene Gründe, warum für eine Anforderung der Fehler 400 zurückgegeben werden kann. Unten sind einige Beispiele für Fehler aufgeführt, die von Azure Attestation-APIs zurückgegeben werden:

### <a name="21-attestation-failure-due-to-policy-evaluation-errors"></a>2.1. Nachweisfehler aufgrund von Fehlern bei der Richtlinienauswertung

Eine Nachweisrichtlinie umfasst Autorisierungs- und Ausstellungsregeln. Die Enklavenbeweise werden basierend auf den Autorisierungsregeln ausgewertet. Mit Ausstellungsregeln werden die Ansprüche definiert, die in das Nachweistoken eingeschlossen werden sollen. Wenn Ansprüche in den Enklavenbeweisen nicht mit den Autorisierungsregeln konform sind, wird bei Nachweisaufrufen für die Richtlinienauswertung ein Fehler zurückgegeben. 

**Fehlercode**: PolicyEvaluationError

**Szenariobeispiele** Wenn Ansprüche im Enklavenangebot nicht mit den Autorisierungsregeln der Nachweisrichtlinie übereinstimmen

```
Native operation failed with 65518: G:\Az\security\Attestation\src\AttestationServices\Instance\NativePolicyWrapper\NativePolicyEngine.cpp(168)\(null)!00007FF801762308: (caller: 00007FF80143DCC8) Exception(0) 83FFFFEE Policy Evaluation Error has occurred Msg:[Policy Engine Exception: A Deny claim was issued, authorization failed.]

G:\Az\security\Attestation\src\AttestationServices\Instance\Enclave\api.cpp(840)\(null)!00007FF801739FF3: (caller: 00007FF801232801) LogHr(0) 83FFFFEE Policy Evaluation Error has occurred Msg:[Unhandled Enclave Exception: "Policy Evaluation Error has occurred"]

```

**Schritte zur Problembehandlung** Benutzer können die Enklavenbeweise anhand einer SGX-Nachweisrichtlinie auswerten, bevor sie die Konfiguration dafür durchführen.

Senden Sie eine Anforderung für einen API-Nachweis, indem Sie Richtlinientext im Parameter „draftPolicyForAttestation“ angeben. Von der AttestSgxEnclave-API wird dieses Richtliniendokument während des Nachweisaufrufs verwendet. Es kann genutzt werden, um Nachweisrichtlinien vor der Verwendung zu testen. Ein Nachweistoken, das bei Vorhandensein dieses Felds generiert wird, ist nicht geschützt.

Sehen Sie sich die [Beispiele für Nachweisrichtlinien](./policy-examples.md) an.

### <a name="22-attestation-failure-due-to-invalid-input"></a>2.2. Nachweisfehler aufgrund einer ungültigen Eingabe

**Fehlercode**: InvalidParameter

**Szenariobeispiele** SGX-Nachweisfehler aufgrund einer ungültigen Eingabe. Hier sind einige Beispiele für Fehlermeldungen angegeben:
- Das angegebene Angebot war aufgrund eines Fehlers im Begleitmaterial des Angebots ungültig. 
- Das angegebene Angebot war ungültig, weil das Gerät, auf dem das Angebot generiert wurde, die Azure-Baselineanforderungen nicht erfüllt.
- Das angegebene Angebot war ungültig, weil die vom PCK-Cachedienst bereitgestellten TCBInfo- oder QEID-Daten ungültig waren.

**Schritte zur Problembehandlung**

Microsoft Azure Attestation unterstützt den Nachweisvorgang für SGX-Angebote, die vom Intel SDK und Open Enclave SDK generiert werden.

Sehen Sie sich die [Codebeispiele](/samples/browse/?expanded=azure&terms=attestation) zur Durchführung des Nachweisvorgangs mit dem Open Enclave SDK/Intel SDK an.

### <a name="23-invalid-certificate-chain-error-while-uploading-policypolicy-signer"></a>2.3. Fehler aufgrund einer ungültigen Zertifikatkette beim Hochladen der Richtlinie bzw. des Richtliniensignaturgebers

**Fehlercode**: InvalidParameter

**Szenariobeispiele** Konfigurieren einer signierten Richtlinie oder Hinzufügen/Löschen eines Richtliniensignaturgebers, wenn zum Signieren eine ungültige Zertifikatkette verwendet wurde (z. B. wenn die Erweiterung für Basiseinschränkungen des Stammzertifikats nicht auf „Subject Type = CA“ festgelegt ist)

```
Native operation failed with 65529: C:\source\src\AttestationServices\Instance\SgxPal\sgxcert.cpp(1074)\(null)!00007FFA285CDAED: (caller: 00007FFA285C36E8) Exception(0) 83FFFFF9 The requested item is not found    Msg:[Unable to find issuer certificate CN=attestationsigningcert]
C:\source\src\AttestationServices\Instance\Enclave\api.cpp(618)\(null)!00007FFA286DCBF8: (caller: 00007FFA285860D3) LogHr(0) 83FFFFF9 The requested item is not found    Msg:[Unhandled Enclave Exception: "The requested item is not found"]
At line:1 char:1
+ Set-AzAttestationPolicy -Name "testpolicy1" -ResourceGroupName "BugBa ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzAttestationPolicy], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.SetAzureAttestationPolicy

```

**Schritte zur Problembehandlung** Das Stammzertifikat muss über eine Kennzeichnung verfügen, dass es von einer Zertifizierungsstelle (CA) ausgestellt wurde (X.509-Basiseinschränkungen), weil es sonst nicht als gültiges Zertifikat angesehen wird. 

Stellen Sie sicher, dass die Erweiterung für Basiseinschränkungen des Stammzertifikats auf „Subject Type = CA“ festgelegt ist.

Andernfalls wird die Zertifikatkette als ungültig angesehen.

Sehen Sie sich die Beispiele für [Richtliniensignaturgeber](./policy-signer-examples.md) und [Richtlinien](./policy-examples.md) an. 

### <a name="24-adddelete-policy-signer-failure"></a>2.4. Fehler beim Hinzufügen/Löschen des Richtliniensignaturgebers

**Fehlercode**: InvalidOperation

**Szenariobeispiele**

Benutzer lädt JWS ohne „maa-policyCertificate“-Anspruch hoch

```
Add-AzAttestationPolicySigner : Operation returned HTTP Status Code 400
Code: InvalidOperation
Message: Native operation failed with 74: ..\Enclave\enclave.cpp(2213)\(null)!: (caller: ) Exception(0) 83FF004A Bad
message    Msg:[Could not find "maa-policyCertificate" claim in policy token]
..\Enclave\api.cpp(496)\(null)!: (caller: ) LogHr(0) 83FF004A Bad message    Msg:[Unhandled Enclave Exception: "Bad
message"]
At line:1 char:1
+ Add-AzAttestationPolicySigner -Name $attestationProvider -ResourceGro ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Add-AzAttestationPolicySigner], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.AddAzureAttestationPolicySigner

```

Benutzer lädt kein Zertifikat im JWS-Format hoch

```
Add-AzAttestationPolicySigner : Operation returned HTTP Status Code 400
Code: InvalidOperation
Message: Native operation failed with 74: ..\JsonWebToken\jsonwebtoken.cpp(375)\(null)!: (caller: ) LogHr(0) 83FF004A
Bad message    Msg:[RETURN_IF_TRUE('(firstPeriod == std::string::npos)') failed with 0x4a: Malformed JWT: Could not
find first period in the token.]
..\Enclave\enclave.cpp(2106)\(null)!: (caller: ) LogHr(0) 83FF004A Bad message
Msg:[THROW_IF_ERROR('DecomposeJsonWebSignature(&policyJws, encodedJoseHeader, encodedJwsBody, jwsSignature)') failed
with 0x4a: 'Bad message']
..\Enclave\enclave.cpp(2106)\(null)!: (caller: ) Exception(0) 83FF004A Bad message
..\Enclave\api.cpp(496)\(null)!: (caller: ) LogHr(0) 83FF004A Bad message    Msg:[Unhandled Enclave Exception: "Bad
message"]
At line:1 char:1
+ Add-AzAttestationPolicySigner -Name $attestationProvider -ResourceGro ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Add-AzAttestationPolicySigner], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.AddAzureAttestationPolicySigner
```

**Schritte zur Problembehandlung** Verwenden Sie zum Hinzufügen/Löschen ein neues Richtlinien-Signaturgeberzertifikats das RFC7519 JSON Web Token (JWT) mit dem Anspruch „x-ms-policyCertificate“. Der Wert des Anspruchs ist ein RFC7517 JSON Web Key, der das hinzuzufügende Zertifikat enthält. Das JWT muss mit dem privaten Schlüssel eines beliebigen gültigen Richtlinien-Signaturgeberzertifikats signiert werden, das dem Anbieter zugeordnet ist. Weitere Informationen finden Sie unter [Beispiele für Richtliniensignaturgeber](./policy-signer-examples.md).

### <a name="25-attestation-policy-configuration-failure"></a>2.5. Fehler bei der Konfiguration der Nachweisrichtlinie

**Fehlercode**: PolicyParsingError

**Szenariobeispiele** Bereitstellung der Richtlinie mit fehlerhafter Syntax (z. B. Semikolon/gültige JWT-Richtlinie fehlt)

```
Native operation failed with 65526: ..\NativePolicyWrapper\NativePolicyEngine.cpp(31)\(null)!: (caller: ) Exception(0) 83FFFFF6 Invalid policy was specified    Msg:[Policy Parser Exception Thrown: Offending
symbol: '['
Line: '2', Column: '1'
Failure message: 'mismatched input '[' expecting ';''
Failing rule: 'policy > versionInfo']
..\Enclave\api.cpp(618)\(null)!: (caller: ) LogHr(0) 83FFFFF6 Invalid policy was specified    Msg:[Unhandled Enclave Exception: "Invalid policy was specified"]
At line:1 char:1
+ set-AzAttestationPolicy -Name $attestationProvider -ResourceGroupName ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzAttestationPolicy], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.SetAzureAttestationPolicy
```

**Fehlercode**: InvalidOperation

**Szenariobeispiele** Bereitstellung ungültiger Inhalte (z. B. Richtlinienupload/unsignierte Richtlinie, obwohl erforderlich)

```
Native operation failed with 74: ..\Shared\base64url.h(226)\(null)!: (caller: ) Exception(0) 83FF004A Bad message    Msg:[Unknown base64 character: 41 (')')]
..\Enclave\api.cpp(618)\(null)!: (caller: ) LogHr(0) 83FF004A Bad message    Msg:[Unhandled Enclave Exception: "Bad message"]
At line:1 char:1
+ set-AzAttestationPolicy -Name $attestationProvider -ResourceGroupName ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzAttestationPolicy], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.SetAzureAttestationPolicy
```

**Schritte zur Problembehandlung** Stellen Sie sicher, dass die Richtlinie im Textformat UTF-8-codiert ist.

Wenn die Richtlinie signiert sein muss, darf die Nachweisrichtlinie nur im RFC7519 JWT-Format (JSON Web Token) konfiguriert sein. Falls keine Richtliniensignaturen erforderlich sind, kann die Richtlinie im Text- oder JWT-Format konfiguriert werden.

Verwenden Sie das JWT mit dem Anspruch „AttestationPolicy“, um eine Richtlinie im JWT-Format zu konfigurieren. Der Wert des Anspruchs ist die Base64URL-codierte Version des Richtlinientexts. Wenn der Nachweisanbieter mit Richtlinien-Signaturgeberzertifikaten konfiguriert wird, muss das JWT mit dem privaten Schlüssel eines beliebigen gültigen Richtlinien-Signaturgeberzertifikats signiert werden, das dem Anbieter zugeordnet ist. 

Geben Sie Richtlinientext direkt an, um eine Richtlinie im Textformat zu konfigurieren.

Geben Sie in PowerShell für „PolicyFormat“ die Option „JWT“ an, um die Richtlinie im JWT-Format zu konfigurieren. Das Standardformat für Richtlinien ist „Text“.

Weitere Informationen finden Sie unter [Beispiele für eine Nachweisrichtlinie](./policy-examples.md) und [Erstellen und Signieren einer Nachweisrichtlinie](./author-sign-policy.md). 

## <a name="3-azattestation-installation-issues-in-powershell"></a>3. Probleme bei der Az.Attestation-Installation in PowerShell

Az- oder Az.Attestation-Module können in PowerShell nicht installiert werden.

### <a name="error"></a>Fehler

WARNUNG: Unable to resolve package source 'https://www.powershellgallery.com/api/v2 ' (Paketquelle kann nicht aufgelöst werden). PackageManagement\Install-Package : No match was found for the specified search criteria and module name (Für die angegebenen Suchkriterien und den angegebenen Modulnamen wurde keine Übereinstimmung gefunden.)

### <a name="troubleshooting-steps"></a>Schritte zur Problembehandlung

Im PowerShell-Katalog wurden die TLS-Versionen (Transport Layer Security) 1.0 und 1.1 als veraltet gekennzeichnet. 

Empfohlen wird TLS 1.2 oder eine höhere Version. 

Führen Sie den folgenden Befehl vor den Befehlen vom Typ „Install-Module“ aus, um weiterhin mit dem PowerShell-Katalog zu interagieren.

**[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12**

## <a name="4-policy-accessconfiguration-issues-in-powershell"></a>4. Richtlinienzugriff/Konfigurationsprobleme in PowerShell

Dem Benutzer wurden die richtigen Rollen zugewiesen. Beim Verwalten der Nachweisrichtlinien mit PowerShell treten aber Autorisierungsprobleme auf.

### <a name="error"></a>Fehler
Der Client mit der Objekt-ID <object Id> ist nicht zur Durchführung der Aktion „Microsoft.Authorization/roleassignments/write“ für den Bereich „subscriptions/<subscriptionId>resourcegroups/secure_enclave_poc/providers/Microsoft.Authorization/roleassignments/<role assignmentId>“ berechtigt, oder der Bereich ist ungültig. Wenn der Zugriff erst vor Kurzem gewährt wurde, sollten Sie Ihre Anmeldeinformationen aktualisieren.

### <a name="troubleshooting-steps"></a>Schritte zur Problembehandlung

Hier sind die mindestens erforderlichen Versionen der Az-Module zur Unterstützung von Nachweisvorgängen angegeben: 

 **Az 4.5.0** 
 
 **Az.Accounts 1.9.2**
 
 **Az.Attestation 0.1.8** 

Führen Sie den folgenden Befehl aus, um die installierte Version aller Az-Module zu überprüfen: 

```powershell
Get-InstalledModule 
```

Führen Sie „Update-Module“-Befehle aus, wenn die Versionen nicht die Mindestanforderungen erfüllen.

Beispiel: Update-Module -Name Az.Attestation