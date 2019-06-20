---
title: 'Azure AD Connect: PowerShell-Referenz zu ADConnectivityTools | Microsoft-Dokumentation'
description: Dieses Dokument enthält Referenzinformationen für das PowerShell-Modul „ADConnectivityTools.psm1“.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.date: 05/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.topic: reference
ms.collection: M365-identity-device-management
ms.openlocfilehash: d6b90ff82601acca1249c7d8c353944e39e89f95
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66473783"
---
# <a name="azure-ad-connect--adconnectivitytools-powershell-reference"></a>Azure AD Connect:  PowerShell-Referenz zu ADConnectivityTools

Die folgende Dokumentation enthält Referenzinformationen für das PowerShell-Modul „ADConnectivityTools.psm1“, das in Azure AD Connect enthalten ist.

## <a name="confirm-dnsconnectivity"></a>Confirm-DnsConnectivity

### <a name="synopsis"></a>ZUSAMMENFASSUNG

Erkennt lokale DNS-Probleme.

### <a name="syntax"></a>SYNTAX

```
Confirm-DnsConnectivity [-Forest] <String> [-DCs] <Array> [-ReturnResultAsPSObject] [<CommonParameters>]
```

### <a name="description"></a>Beschreibung

Führt lokale DNS-Konnektivitätstests aus.
Um den Active Directory Connector zu konfigurieren, muss der Benutzer über Namensauflösung verfügen und zwar sowohl in der Gesamtstruktur, mit der eine Verbindung hergestellt werden soll, als auch in den Domänencontrollern, die dieser Gesamtstruktur zugeordnet sind.

### <a name="examples"></a>BEISPIELE

#### <a name="example-1"></a>BEISPIEL 1

```powershell
Confirm-DnsConnectivity -Forest "TEST.CONTOSO.COM" -DCs "MYDC1.CONTOSO.COM","MYDC2.CONTOSO.COM"
```

#### <a name="example-2"></a>BEISPIEL 2

```powershell
Confirm-DnsConnectivity -Forest "TEST.CONTOSO.COM"
```

### <a name="parameters"></a>PARAMETER

#### <a name="-forest"></a>-Forest

Gibt den Namen der zu testenden Gesamtstruktur an.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-dcs"></a>-DCs

Zu testende DCs eingeben.

```yml
Type: Array
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-returnresultaspsobject"></a>-ReturnResultAsPSObject

Gibt das Ergebnis dieser Diagnose in Form eines „PSObject“ zurück.
Während manueller Interaktion mit diesem Tool nicht erforderlich.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters

Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable.
Weitere Informationen finden Sie unter "about_CommonParameters" (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="confirm-forestexists"></a>Confirm-ForestExists

### <a name="synopsis"></a>ZUSAMMENFASSUNG

Bestimmt, ob eine angegebene Gesamtstruktur vorhanden ist.

### <a name="syntax"></a>SYNTAX

```
Confirm-ForestExists [-Forest] <String> [<CommonParameters>]
```

### <a name="description"></a>Beschreibung

Fragt bei einem DNS-Server die IP-Adressen ab, die einer Gesamtstruktur zugeordnet sind.

### <a name="examples"></a>BEISPIELE

#### <a name="example-1"></a>BEISPIEL 1

```powershell
Confirm-TargetsAreReachable -Forest "TEST.CONTOSO.COM"
```

### <a name="parameters"></a>PARAMETER

#### <a name="-forest"></a>-Forest

Gibt den Namen der zu testenden Gesamtstruktur an.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters

Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable.
Weitere Informationen finden Sie unter "about_CommonParameters" (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="confirm-functionallevel"></a>Confirm-FunctionalLevel

### <a name="synopsis"></a>ZUSAMMENFASSUNG

Überprüft die Funktionsebene der Active Directory-Gesamtstruktur.

### <a name="syntax"></a>SYNTAX

#### <a name="samaccount"></a>SamAccount

```
Confirm-FunctionalLevel -Forest <String> [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

#### <a name="forestfqdn"></a>ForestFQDN

```
Confirm-FunctionalLevel -ForestFQDN <Forest> [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

### <a name="description"></a>Beschreibung

Überprüft, ob die Funktionsebene der AD-Gesamtstruktur gleich oder höher als eine angegebene „MinAdForestVersion“ (WindowsServer2003) ist.
Konto (Domäne\Benutzername) und Kennwort werden möglicherweise angefordert.

### <a name="examples"></a>BEISPIELE

#### <a name="example-1"></a>BEISPIEL 1

```powershell
Confirm-FunctionalLevel -Forest "test.contoso.com"
```

#### <a name="example-2"></a>BEISPIEL 2

```powershell
Confirm-FunctionalLevel -Forest "test.contoso.com" -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

#### <a name="example-3"></a>BEISPIEL 3

```powershell
Confirm-FunctionalLevel -ForestFQDN $ForestFQDN -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

### <a name="parameters"></a>PARAMETER

#### <a name="-forest"></a>-Forest

Zielgesamtstruktur.
Standardwert ist die Gesamtstruktur des aktuell angemeldeten Benutzers.

```yml
Type: String
Parameter Sets: SamAccount
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-forestfqdn"></a>-ForestFQDN

ForestFQDN-Zielobjekt.

```yml
Type: Forest
Parameter Sets: ForestFQDN
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-runwithcurrentlyloggedinusercredentials"></a>-RunWithCurrentlyLoggedInUserCredentials

Die Funktion verwendet die Anmeldeinformationen des Benutzers, der derzeit bei dem Computer angemeldet ist statt benutzerdefinierte Anmeldeinformationen vom Benutzer anzufordern.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters

Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable.
Weitere Informationen finden Sie unter "about_CommonParameters" (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="confirm-networkconnectivity"></a>Confirm-NetworkConnectivity

### <a name="synopsis"></a>ZUSAMMENFASSUNG

Erkennt lokale Netzwerkkonnektivitätsprobleme.

### <a name="syntax"></a>SYNTAX

```
Confirm-NetworkConnectivity [-DCs] <Array> [-SkipDnsPort] [-ReturnResultAsPSObject] [<CommonParameters>]
```

### <a name="description"></a>Beschreibung

Führt lokale Netzwerkkonnektivitätstests aus.

Für die lokalen Netzwerktests muss AAD Connect in der Lage sein, mit den benannten Domänencontrollern an den Ports 53 (DNS), 88 (Kerberos) und 389 (LDAP) zu kommunizieren. Die meisten Organisationen führen DNS auf ihren DCs aus, weshalb dieser Test derzeit integriert ist.
Port 53 sollte übersprungen werden, wenn ein anderer DNS-Server angegeben wurde.

### <a name="examples"></a>BEISPIELE

#### <a name="example-1"></a>BEISPIEL 1

```powershell
Confirm-NetworkConnectivity -SkipDnsPort -DCs "MYDC1.CONTOSO.COM","MYDC2.CONTOSO.COM"
```

#### <a name="example-2"></a>BEISPIEL 2

```powershell
Confirm-NetworkConnectivity -DCs "MYDC1.CONTOSO.COM","MYDC2.CONTOSO.COM" -Verbose
```

### <a name="parameters"></a>PARAMETER

#### <a name="-dcs"></a>-DCs

Zu testende DCs eingeben.

```yml
Type: Array
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipdnsport"></a>-SkipDnsPort

Wenn der Benutzer keine vom AD-Standort/Anmelde-DC bereitgestellten DNS-Dienste verwendet, sollte er eventuell die Überprüfung von Port 53 überspringen.
Der Benutzer muss weiterhin in der Lage sein, „_.ldap._tcp.\<forestfqdn\>“ aufzulösen, damit die Active Directory Connector-Konfiguration erfolgreich erfolgen kann.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-returnresultaspsobject"></a>-ReturnResultAsPSObject

Gibt das Ergebnis dieser Diagnose in Form eines „PSObject“ zurück.
Während manueller Interaktion mit diesem Tool nicht erforderlich.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters

Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable.
Weitere Informationen finden Sie unter "about_CommonParameters" (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="confirm-targetsarereachable"></a>Confirm-TargetsAreReachable

### <a name="synopsis"></a>ZUSAMMENFASSUNG

Bestimmt, ob eine angegebene Gesamtstruktur und ihre zugeordneten Domänencontroller erreichbar sind.

### <a name="syntax"></a>SYNTAX

```
Confirm-TargetsAreReachable [-Forest] <String> [-DCs] <Array> [<CommonParameters>]
```

### <a name="description"></a>Beschreibung

Führt Pingtests aus (ob ein Computer einen Zielcomputer über das Netzwerk und/oder das Internet erreichen kann).

### <a name="examples"></a>BEISPIELE

#### <a name="example-1"></a>BEISPIEL 1

```powershell
Confirm-TargetsAreReachable -Forest "TEST.CONTOSO.COM" -DCs "MYDC1.CONTOSO.COM","MYDC2.CONTOSO.COM"
```

#### <a name="example-2"></a>BEISPIEL 2

```powershell
Confirm-TargetsAreReachable -Forest "TEST.CONTOSO.COM"
```

### <a name="parameters"></a>PARAMETER

#### <a name="-forest"></a>-Forest

Gibt den Namen der zu testenden Gesamtstruktur an.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-dcs"></a>-DCs

Zu testende DCs eingeben.

```yml
Type: Array
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters

Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable.
Weitere Informationen finden Sie unter "about_CommonParameters" (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="confirm-validdomains"></a>Confirm-ValidDomains

### <a name="synopsis"></a>ZUSAMMENFASSUNG

Überprüfen Sie, ob die Domänen im abgerufenen Gesamtstruktur-FQDN erreichbar sind.

### <a name="syntax"></a>SYNTAX

#### <a name="samaccount"></a>SamAccount

```
Confirm-ValidDomains [-Forest <String>] [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

#### <a name="forestfqdn"></a>ForestFQDN

```
Confirm-ValidDomains -ForestFQDN <Forest> [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

### <a name="description"></a>Beschreibung

Überprüfen Sie, ob alle Domänen im abgerufenen Gesamtstruktur-FQDN erreichbar sind, indem Sie versuchen, „DomainGuid“ und „DomainDN“ abzurufen.
Konto (Domäne\Benutzername) und Kennwort werden möglicherweise angefordert.

### <a name="examples"></a>BEISPIELE

#### <a name="example-1"></a>BEISPIEL 1

```powershell
Confirm-ValidDomains -Forest "test.contoso.com" -Verbose
```

#### <a name="example-2"></a>BEISPIEL 2

```powershell
Confirm-ValidDomains -Forest "test.contoso.com" -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

#### <a name="example-3"></a>BEISPIEL 3

```powershell
Confirm-ValidDomains -ForestFQDN $ForestFQDN -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

### <a name="parameters"></a>PARAMETER

#### <a name="-forest"></a>-Forest

Zielgesamtstruktur.

```yml
Type: String
Parameter Sets: SamAccount
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-forestfqdn"></a>-ForestFQDN

ForestFQDN-Zielobjekt.

```yml
Type: Forest
Parameter Sets: ForestFQDN
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-runwithcurrentlyloggedinusercredentials"></a>-RunWithCurrentlyLoggedInUserCredentials

Die Funktion verwendet die Anmeldeinformationen des Benutzers, der derzeit bei dem Computer angemeldet ist statt benutzerdefinierte Anmeldeinformationen vom Benutzer anzufordern.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters

Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable.
Weitere Informationen finden Sie unter "about_CommonParameters" (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="confirm-validenterpriseadmincredentials"></a>Confirm-ValidEnterpriseAdminCredentials

### <a name="synopsis"></a>ZUSAMMENFASSUNG

Überprüft, ob ein Benutzer Unternehmensadministrator-Anmeldeinformationen besitzt.

### <a name="syntax"></a>SYNTAX

```
Confirm-ValidEnterpriseAdminCredentials [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

### <a name="description"></a>Beschreibung

Sucht, ob der angegebene Benutzer Unternehmensadministrator-Anmeldeinformationen besitzt.
Konto (Domäne\Benutzername) und Kennwort werden möglicherweise angefordert.

### <a name="examples"></a>BEISPIELE

#### <a name="example-1"></a>BEISPIEL 1

```powershell
Confirm-ValidEnterpriseAdminCredentials -DomainName test.contoso.com -Verbose
```

#### <a name="example-2"></a>BEISPIEL 2

```powershell
Confirm-ValidEnterpriseAdminCredentials -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

### <a name="parameters"></a>PARAMETER

#### <a name="-runwithcurrentlyloggedinusercredentials"></a>-RunWithCurrentlyLoggedInUserCredentials

Die Funktion verwendet die Anmeldeinformationen des Benutzers, der derzeit bei dem Computer angemeldet ist statt benutzerdefinierte Anmeldeinformationen vom Benutzer anzufordern.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters

Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable.
Weitere Informationen finden Sie unter "about_CommonParameters" (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="get-domainfqdndata"></a>Get-DomainFQDNData

### <a name="synopsis"></a>ZUSAMMENFASSUNG

Ruft einen DomainFQDN aus einer Konto- und Kennwort-Kombination ab.

### <a name="syntax"></a>SYNTAX

```
Get-DomainFQDNData [[-DomainFQDNDataType] <String>] [-RunWithCurrentlyLoggedInUserCredentials]
 [-ReturnExceptionOnError] [<CommonParameters>]
```

### <a name="description"></a>Beschreibung

Versucht, ein domainFQDN-Objekt aus angegebenen Anmeldeinformationen abzurufen.
Wenn der DomainFQDN gültig ist, wird ein DomainFQDNName oder RootDomainName zurückgegeben, je nach Auswahl des Benutzers.
Konto (Domäne\Benutzername) und Kennwort werden möglicherweise angefordert.

### <a name="examples"></a>BEISPIELE

#### <a name="example-1"></a>BEISPIEL 1

```powershell
Get-DomainFQDNData -DomainFQDNDataType DomainFQDNName -Verbose
```

#### <a name="example-2"></a>BEISPIEL 2

```powershell
Get-DomainFQDNData -DomainFQDNDataType RootDomainName -RunWithCurrentlyLoggedInUserCredentials
```

### <a name="parameters"></a>PARAMETER

#### <a name="-domainfqdndatatype"></a>-DomainFQDNDataType

Die gewünschte Art von Daten, die abgerufen werden.
Aktuell beschränkt auf „DomainFQDNName“ oder „RootDomainName“.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-runwithcurrentlyloggedinusercredentials"></a>-RunWithCurrentlyLoggedInUserCredentials

Die Funktion verwendet die Anmeldeinformationen des Benutzers, der derzeit bei dem Computer angemeldet ist statt benutzerdefinierte Anmeldeinformationen vom Benutzer anzufordern.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-returnexceptiononerror"></a>-ReturnExceptionOnError

Zusätzlicher Parameter, der von der Funktion „Start-NetworkConnectivityDiagnosisTools“ verwendet wird.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters

Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable.
Weitere Informationen finden Sie unter "about_CommonParameters" (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="get-forestfqdn"></a>Get-ForestFQDN

### <a name="synopsis"></a>ZUSAMMENFASSUNG

Ruft einen ForestFQDN aus einer Konto- und Kennwort-Kombination ab.

### <a name="syntax"></a>SYNTAX

```
Get-ForestFQDN [-Forest] <String> [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

### <a name="description"></a>Beschreibung

Versucht, einen ForestFQDN aus angegebenen Anmeldeinformationen abzurufen.
Konto (Domäne\Benutzername) und Kennwort werden möglicherweise angefordert.

### <a name="examples"></a>BEISPIELE

#### <a name="example-1"></a>BEISPIEL 1

```powershell
Get-ForestFQDN -Forest CONTOSO.MICROSOFT.COM -Verbose
```

#### <a name="example-2"></a>BEISPIEL 2

```powershell
Get-ForestFQDN -Forest CONTOSO.MICROSOFT.COM -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

### <a name="parameters"></a>PARAMETER

#### <a name="-forest"></a>-Forest

Standardgesamtstruktur. Standardwert ist die Domäne des aktuell angemeldeten Benutzers.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-runwithcurrentlyloggedinusercredentials"></a>-RunWithCurrentlyLoggedInUserCredentials

Die Funktion verwendet die Anmeldeinformationen des Benutzers, der derzeit bei dem Computer angemeldet ist statt benutzerdefinierte Anmeldeinformationen vom Benutzer anzufordern.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters

Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable.
Weitere Informationen finden Sie unter "about_CommonParameters" (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="start-connectivityvalidation"></a>Start-ConnectivityValidation

### <a name="synopsis"></a>ZUSAMMENFASSUNG

Hauptfunktion.

### <a name="syntax"></a>SYNTAX

```
Start-ConnectivityValidation [-Forest] <String> [-AutoCreateConnectorAccount] <Boolean> [[-UserName] <String>]
 [<CommonParameters>]
```

### <a name="description"></a>Beschreibung

Führt alle verfügbaren Mechanismen aus, die überprüfen, ob AD-Anmeldeinformationen gültig sind.

### <a name="examples"></a>BEISPIELE

#### <a name="example-1"></a>BEISPIEL 1

```powershell
Start-ConnectivityValidation -Forest "test.contoso.com" -AutoCreateConnectorAccount $True -Verbose
```

### <a name="parameters"></a>PARAMETER

#### <a name="-forest"></a>-Forest

Zielgesamtstruktur.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-autocreateconnectoraccount"></a>-AutoCreateConnectorAccount

Für benutzerdefinierte Installationen: Das Flag lautet „$true“, wenn der Benutzer im AD-Gesamtstrukturkonto-Fenster des AAD Connect-Assistenten die Option „Neues AD-Konto erstellen“ gewählt hat.
„$False“, wenn der Benutzer „Vorhandenes AD-Konto verwenden“ ausgewählt hat.
Für Expressinstallationen: Der Wert dieser Variablen muss für Expressinstallationen „$True“ lauten.

```yml
Type: Boolean
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-username"></a>-UserName

Der Parameter, der das Feld „Username“ im Voraus auffüllt, wenn die Anmeldeinformationen des Benutzers angefordert werden.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters

Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable.
Weitere Informationen finden Sie unter "about_CommonParameters" (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="start-networkconnectivitydiagnosistools"></a>Start-NetworkConnectivityDiagnosisTools

### <a name="synopsis"></a>ZUSAMMENFASSUNG

Hauptfunktion für Netzwerkkonnektivitätstests.

### <a name="syntax"></a>SYNTAX

```
Start-NetworkConnectivityDiagnosisTools [[-Forest] <String>] [-Credentials] <PSCredential>
 [[-LogFileLocation] <String>] [[-DCs] <Array>] [-DisplayInformativeMessage] [-ReturnResultAsPSObject]
 [-ValidCredentials] [<CommonParameters>]
```

### <a name="description"></a>Beschreibung

Führt lokale Netzwerkkonnektivitätstests aus.

### <a name="examples"></a>BEISPIELE

#### <a name="example-1"></a>BEISPIEL 1

```powershell
Start-NetworkConnectivityDiagnosisTools -Forest "TEST.CONTOSO.COM"
```

#### <a name="example-2"></a>BEISPIEL 2

```powershell
Start-NetworkConnectivityDiagnosisTools -Forest "TEST.CONTOSO.COM" -DCs "DC1.TEST.CONTOSO.COM", "DC2.TEST.CONTOSO.COM"
```

### <a name="parameters"></a>PARAMETER

#### <a name="-forest"></a>-Forest

Gibt den Namen der zu testenden Gesamtstruktur an.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-credentials"></a>-Credentials

Der Benutzernamen und das Kennwort des Benutzers, der den Test ausführt.
Erfordert dieselbe Berechtigungsstufe, die zur Ausführung des Azure AD Connect-Assistenten erforderlich ist.

```yml
Type: PSCredential
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-logfilelocation"></a>-LogFileLocation

Gibt den Speicherort einer Protokolldatei an, die die Ausgabe dieser Funktion enthält.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-dcs"></a>-DCs

Zu testende DCs eingeben.

```yml
Type: Array
Parameter Sets: (All)
Aliases:

Required: False
Position: 4
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-displayinformativemessage"></a>-DisplayInformativeMessage

Flag, das die Anzeige einer Meldung zum Zweck dieser Funktion ermöglicht.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-returnresultaspsobject"></a>-ReturnResultAsPSObject

Gibt das Ergebnis dieser Diagnose in Form eines „PSObject“ zurück.
Muss während der manuellen Interaktion mit diesem Tool nicht angegeben werden.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-validcredentials"></a>-ValidCredentials

Zeigt an, ob die vom Benutzer eingegebenen Anmeldeinformationen gültig sind.
Muss während der manuellen Interaktion mit diesem Tool nicht angegeben werden.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters

Dieses Cmdlet unterstützt diese gängigen Parameter: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction und -WarningVariable.
Weitere Informationen finden Sie unter "about_CommonParameters" (https://go.microsoft.com/fwlink/?LinkID=113216).
