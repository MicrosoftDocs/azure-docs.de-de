---
title: 'Generieren und Übertragen von HSM-geschützten Schlüsseln für Azure Key Vault: Azure Key Vault'
description: Verwenden Sie diesen Artikel zum Planen, Generieren und anschließenden Übertragen Ihrer eigenen HSM-geschützten Schlüssel für die Nutzung mit dem Azure-Schlüsseltresor. Wird auch als Bring Your Own Key (BYOK) bezeichnet.
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: tutorial
ms.date: 05/29/2020
ms.author: ambapat
ms.openlocfilehash: 8a1f3b5e80152fb0fb9458aef0d3524dd2d6f5eb
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97092328"
---
# <a name="import-hsm-protected-keys-for-key-vault-ncipher"></a>Importieren von HSM-geschützten Schlüsseln für Key Vault (nCipher)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Zur Steigerung der Sicherheit können Sie bei Verwendung des Azure-Schlüsseltresors Schlüssel in HSMs (Hardwaresicherheitsmodule) importieren oder darin generieren. Diese Schlüssel verbleiben immer innerhalb der HSM-Grenzen. Dieses Szenario wird häufig als *Bring Your Own Key* (BYOK) bezeichnet. Azure Key Vault verwendet die nCipher nShield-HSM-Produktfamilie (validiert für FIPS 140-2 Level 2) zum Schützen der Schlüssel.

> [!NOTE]
> Die in diesem Dokument beschriebene Methode für den HSM-Schlüsselimport funktioniert nur mit der nCipher nShield-Familie der HSMs. Informationen zum Importieren von HSM-Schlüsseln aus anderen HSMs [finden Sie hier](hsm-protected-keys-byok.md).

Verwenden Sie die Informationen in diesem Thema zum Planen, Generieren und anschließenden Übertragen Ihrer eigenen HSM-geschützten Schlüssel für die Nutzung mit Azure Key Vault. 

Diese Funktion steht für Azure China 21ViaNet nicht zur Verfügung.

> [!NOTE]
> Weitere Informationen zum Azure-Schlüsseltresor finden Sie unter [Was ist der Azure-Schlüsseltresor?](../general/overview.md)  
> Ein Tutorial zu den ersten Schritten, z.B. zum Erstellen eines Schlüsseltresors für HSM-geschützte Schlüssel, finden Sie unter [Was ist Azure Key Vault?](../general/overview.md).

Weitere Informationen zum Generieren und Übertragen eines HSM-geschützten Schlüssels über das Internet:

* Sie generieren den Schlüssel auf einer Offlinearbeitsstation, um die Angriffsfläche zu reduzieren.
* Der Schlüssel wird mit einem Schlüsselaustauschschlüssel (Key Exchange Key, KEK) verschlüsselt. Die Verschlüsselung bleibt bis zur Übertragung an die HSMs des Azure-Schlüsseltresors bestehen. Nur die verschlüsselte Version Ihres Schlüssels verlässt die ursprüngliche Arbeitsstation.
* Mit dem Toolset werden Eigenschaften für Ihren Mandantenschlüssel festgelegt, mit dem Ihr Schlüssel an die Sicherheitsumgebung des Azure-Schlüsseltresors gebunden wird. Nachdem die HSMs des Azure-Schlüsseltresors Ihren Schlüssel empfangen und entschlüsselt haben, kann er nur von diesen HSMs verwendet werden. Der Schlüssel kann nicht exportiert werden. Diese Bindung wird mit den nCipher-HSMs erzwungen.
* Der Schlüsselaustauschschlüssel (Key Exchange Key, KEK), der zum Verschlüsseln Ihres Schlüssels verwendet wird, wird innerhalb der HSMs des Azure-Schlüsseltresors generiert und kann nicht exportiert werden. Die HSMs erzwingen, dass außerhalb der HSMs keine unverschlüsselte Version des Schlüsselaustauschschlüssels vorhanden sein kann. Darüber hinaus verfügt das Toolset über den Nachweis von nCipher, dass der Schlüsselaustauschschlüssel nicht exportiert werden kann und innerhalb eines Original-HSM von nCipher generiert wurde.
* Das Toolset enthält einen Nachweis von nCipher, dass die Azure Key Vault-Sicherheitsumgebung ebenfalls in einem von nCipher hergestellten Original-HSM generiert wurde. Dieser Nachweis ist ein Beleg dafür, dass Microsoft Originalhardware verwendet.
* Microsoft verwendet separate Schlüsselaustauschschlüssel (KEK) und getrennte Sicherheitsumgebungen (Security Worlds) in jeder geografischen Region. Durch diese Trennung wird sichergestellt, dass Ihr Schlüssel nur in Rechenzentren in der Region verwendet werden kann, in der Sie die Verschlüsselung durchgeführt haben. Ein Schlüssel eines Kunden aus Europa kann beispielsweise nicht in Rechenzentren in Nordamerika oder Asien verwendet werden.

## <a name="more-information-about-ncipher-hsms-and-microsoft-services"></a>Weitere Informationen zu nCipher-HSMs und Microsoft-Diensten

nCipher Security, ein Unternehmen von Entrust Datacard, ist ein führender Anbieter im universellen HSM-Markt und unterstützt weltweit führende Unternehmen, indem es Vertrauen, Integrität und Kontrolle für ihre geschäftskritischen Informationen und Anwendungen bietet. Die kryptographischen Lösungen von nCipher schützen neu entstehende Technologien – Cloud, IoT, Blockchain, digitale Zahlungen – und helfen bei der Erfüllung neuer Compliance-Anforderungen, indem sie dieselbe bewährte Technologie verwenden, auf die globale Unternehmen heute angewiesen sind, um sich vor Bedrohungen für ihre vertraulichen Daten, die Netzwerkkommunikation und die Unternehmensinfrastruktur zu schützen. nCipher bietet Vertrauen für geschäftskritische Anwendungen, stellt die Integrität der Daten sicher und gibt Kunden die volle Kontrolle – heute, morgen und in Zukunft.

Microsoft hat mit nCipher Security zusammengearbeitet, um den Entwicklungsstand der HSMs zu verbessern. Dank dieser Verbesserungen können Sie die typischen Vorteile von gehosteten Diensten nutzen, ohne die Kontrolle über Ihre Schlüssel abzugeben. Vor allem ist es vorteilhaft, dass die HSMs aufgrund dieser Verbesserungen von Microsoft verwaltet werden können, damit Sie dies nicht übernehmen müssen. Als Clouddienst kann der Azure-Schlüsseltresor schnell zentral hochskaliert werden, um die Auslastungsspitzen Ihrer Organisation zu bewältigen. Gleichzeitig ist der Schlüssel in den HSMs von Microsoft geschützt: Sie behalten die Kontrolle über den Lebenszyklus des Schlüssels, da Sie ihn generieren und in die HSMs von Microsoft übertragen.

## <a name="implementing-bring-your-own-key-byok-for-azure-key-vault"></a>Implementieren von „Bring Your Own Key“ (BYOK) für den Azure-Schlüsseltresor

Verwenden Sie die folgenden Informationen und Verfahren, wenn Sie Ihren eigenen HSM-geschützten Schlüssel generieren und dann an den Azure-Schlüsseltresor übertragen möchten. Dies ist das BYOK-Szenario (Bring Your Own Key).

## <a name="prerequisites-for-byok"></a>Voraussetzungen für BYOK

Die folgende Tabelle enthält eine Liste mit Voraussetzungen, die beim Azure-Schlüsseltresor für Bring Your Own Key (BYOK) erfüllt sein müssen.

| Anforderung | Weitere Informationen |
| --- | --- |
| Azure-Abonnement |Um eine Azure Key Vault-Instanz erstellen zu können, benötigen Sie ein Azure-Abonnement: [Für kostenlose Testversion registrieren](https://azure.microsoft.com/pricing/free-trial/) |
| Azure Key Vault-Dienstebene „Premium“ zur Unterstützung von HSM-geschützten Schlüsseln |Weitere Informationen zu den Dienstebenen und Funktionen für Azure Key Vault finden Sie auf der Website [Key Vault – Preise](https://azure.microsoft.com/pricing/details/key-vault/). |
| nCipher nShield-HSMs, Smartcards und Supportsoftware |Sie benötigen Zugriff auf ein nCipher-Hardwaresicherheitsmodul sowie grundlegende Kenntnisse zum Betrieb von nCipher nShield-HSMs. Eine Liste mit kompatiblen Modellen bzw. Informationen zum Kauf eines HSM, für den Fall, dass Sie noch keins besitzen, finden Sie unter [nCipher nShield-Hardwaresicherheitsmodul](https://go.ncipher.com/rs/104-QOX-775/images/nCipher_nShield_Family_Brochure.pdf?_ga=2.106120835.1607422418.1590478092-577009923.1587131206). |
| Folgende Hardware und Software:<ol><li>Eine x64-Arbeitsstation im Offlinemodus mit einem Windows-Betriebssystem der Mindestversion Windows 7 und nCipher nShield-Software der Mindestversion 11.50.<br/><br/>Wenn auf dieser Arbeitsstation Windows 7 ausgeführt wird, müssen Sie [Microsoft .NET Framework 4.5](https://download.microsoft.com/download/b/a/4/ba4a7e71-2906-4b2d-a0e1-80cf16844f5f/dotnetfx45_full_x86_x64.exe) installieren.</li><li>Eine Arbeitsstation, die mit dem Internet verbunden ist und auf der mindestens Windows 7 und [Azure PowerShell](/powershell/azure/?view=azps-1.2.0) (**Mindestversion 1.1.0**) installiert ist.</li><li>Ein USB-Laufwerk oder ein anderes tragbares Speichergerät mit mindestens 16 MB freiem Speicherplatz.</li></ol> |Aus Sicherheitsgründen wird empfohlen, die erste Arbeitsstation nicht mit einem Netzwerk zu verbinden. Diese Empfehlung wird jedoch nicht programmgesteuert erzwungen.<br/><br/>Diese Arbeitsstation wird in den folgenden Anleitungen als verbindungslose Arbeitsstation bezeichnet.</p></blockquote><br/>Falls Ihr Mandantenschlüssel für ein Produktionsnetzwerk gilt, empfehlen wir außerdem, eine zweite separate Arbeitsstation zu verwenden, um das Toolset herunterzuladen und den Mandantenschlüssel hochzuladen. Zu Testzwecken können Sie aber auch ein und dieselbe Arbeitsstation verwenden.<br/><br/>Die zweite Arbeitsstation in den folgenden Anleitungen wird als Arbeitsstation mit Internetverbindung bezeichnet.</p></blockquote><br/> |

## <a name="generate-and-transfer-your-key-to-azure-key-vault-hsm"></a>Generieren und Übertragen des Schlüssels an das HSM des Azure-Schlüsseltresors

Sie verwenden die folgenden fünf Schritte zum Generieren und Übertragen Ihres Schlüssels an das HSM von Azure Key Vault:

* [Schritt 1: Vorbereiten der Arbeitsstation mit Internetverbindung](#step-1-prepare-your-internet-connected-workstation)
* [Schritt 2: Vorbereiten der verbindungslosen Arbeitsstation](#step-2-prepare-your-disconnected-workstation)
* [Schritt 3: Generieren des Schlüssels](#step-3-generate-your-key)
* [Schritt 4: Vorbereiten des Schlüssels für die Übertragung](#step-4-prepare-your-key-for-transfer)
* [Schritt 5: Übertragen des Schlüssels an Azure Key Vault](#step-5-transfer-your-key-to-azure-key-vault)

## <a name="step-1-prepare-your-internet-connected-workstation"></a>Schritt 1: Vorbereiten der Arbeitsstation mit Internetverbindung

Führen Sie für diesen ersten Schritt die folgenden Verfahren auf der Arbeitsstation mit Internetverbindung aus.

### <a name="step-11-install-azure-powershell"></a>Schritt 1.1: Installieren von Azure Powershell

Laden Sie auf der Arbeitsstation mit Internetverbindung das Azure PowerShell-Modul herunter, das die Cmdlets zum Verwalten des Azure-Schlüsseltresors enthält, und installieren Sie es. Installationsanweisungen finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/).

### <a name="step-12-get-your-azure-subscription-id"></a>Schritt 1.2: Abrufen der Azure-Abonnement-ID

Starten Sie eine Azure PowerShell-Sitzung, und melden Sie sich mit dem folgenden Befehl bei Ihrem Azure-Konto an:

```Powershell
   Connect-AzAccount
```
Geben Sie im Popup-Browserfenster den Benutzernamen und das Kennwort Ihres Azure-Kontos ein. Verwenden Sie anschließend den Befehl [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription):

```powershell
   Get-AzSubscription
```
Suchen Sie in der Ausgabe die ID für das Abonnement, das Sie für den Azure-Schlüsseltresor verwenden möchten. Sie benötigen diese Abonnement-ID später noch.

Lassen Sie das Azure PowerShell-Fenster geöffnet.

### <a name="step-13-download-the-byok-toolset-for-azure-key-vault"></a>Schritt 1.3: Herunterladen des BYOK-Toolsets für Azure Key Vault

Wechseln Sie zum Microsoft Download Center, und [laden Sie das Azure-Schlüsseltresor-BYOK-Toolset für Ihre geografische Region oder Instanz von Azure herunter](https://www.microsoft.com/download/details.aspx?id=45345) . Ermitteln Sie anhand der folgenden Informationen den Namen des herunterzuladenden Pakets und seinen entsprechenden SHA-256-Pakethash:

---
**USA:**

KeyVault-BYOK-Tools-UnitedStates.zip

2E8C00320400430106366A4E8C67B79015524E4EC24A2D3A6DC513CA1823B0D4

---
**Europa:**

KeyVault-BYOK-Tools-Europe.zip

9AAA63E2E7F20CF9BB62485868754203721D2F88D300910634A32DFA1FB19E4A

---
**Asien:**

KeyVault-BYOK-Tools-AsiaPacific.zip

4BC14059BF0FEC562CA927AF621DF665328F8A13616F44C977388EC7121EF6B5

---
**Lateinamerika:**

KeyVault-BYOK-Tools-LatinAmerica.zip

E7DFAFF579AFE1B9732C30D6FD80C4D03756642F25A538922DD1B01A4FACB619

---
**Japan:**

KeyVault-BYOK-Tools-Japan.zip

3933C13CC6DC06651295ADC482B027AF923A76F1F6BF98B4D4B8E94632DEC7DF

---
**Korea:**

KeyVault-BYOK-Tools-Korea.zip

71AB6BCFE06950097C8C18D532A9184BEF52A74BB944B8610DDDA05344ED136F

---
**Südafrika:**

KeyVault-BYOK-Tools-SouthAfrica.zip

C41060C5C0170AAAAD896DA732E31433D14CB9FC83AC3C67766F46D98620784A

---
**Vereinigte Arabische Emirate:**

KeyVault-BYOK-Tools-UAE.zip

FADE80210B06962AA0913EA411DAB977929248C65F365FD953BB9F241D5FC0D3

---
**Australien:**

KeyVault-BYOK-Tools-Australia.zip

CD0FB7365053DEF8C35116D7C92D203C64A3D3EE2452A025223EEB166901C40A

---
[**Azure Government:**](https://azure.microsoft.com/features/gov/)

KeyVault-BYOK-Tools-USGovCloud.zip

F8DB2FC914A7360650922391D9AA79FF030FD3048B5795EC83ADC59DB018621A

---
**US Government (US-Verteidigungsministerium):**

KeyVault-BYOK-Tools-USGovernmentDoD.zip

A79DD8C6DFFF1B00B91D1812280207A205442B3DDF861B79B8B991BB55C35263

---
**Kanada:**

KeyVault-BYOK-Tools-Canada.zip

61BE1A1F80AC79912A42DEBBCC42CF87C88C2CE249E271934630885799717C7B

---
**Deutschland:**

KeyVault-BYOK-Tools-Germany.zip

5385E615880AAFC02AFD9841F7BADD025D7EE819894AA29ED3C71C3F844C45D6

---
**Deutschland (öffentlich):**

KeyVault-BYOK-Tools-Germany-Public.zip

54534936D0A0C99C8117DB724C34A5E50FD204CFCBD75C78972B785865364A29

---
**Indien:**

KeyVault-BYOK-Tools-India.zip

49EDCEB3091CF1DF7B156D5B495A4ADE1CFBA77641134F61B0E0940121C436C8

---
**Frankreich:**

KeyVault-BYOK-Tools-France.zip

5C9D1F3E4125B0C09E9F60897C9AE3A8B4CB0E7D13A14F3EDBD280128F8FE7DF

---
**Großbritannien:**

KeyVault-BYOK-Tools-UnitedKingdom.zip

432746BD0D3176B708672CCFF19D6144FCAA9E5EB29BB056489D3782B3B80849

---
**Schweiz:**

KeyVault-BYOK-Tools-Switzerland.zip

88CF8D39899E26D456D4E0BC57E5C94913ABF1D73A89013FCE3BBD9599AD2FE9

---


Verwenden Sie in der Azure PowerShell-Sitzung das [Get-FileHash](/powershell/module/microsoft.powershell.utility/get-filehash) -Cmdlet, um die Integrität des heruntergeladenen BYOK-Toolsets zu überprüfen.

   ```powershell
   Get-FileHash KeyVault-BYOK-Tools-*.zip
   ```

Das Toolset enthält Folgendes:

* Ein Schlüsselaustauschschlüssel-Paket (Key Exchange Key, KEK), dessen Name mit **BYOK-KEK-pkg-.**
* Ein Security World-Paket, dessen Name mit **BYOK-SecurityWorld-pkg-.**
* Ein Python-Skript mit dem Namen **verifykeypackage.py**.
* Eine ausführbare Befehlszeilendatei namens **KeyTransferRemote.exe** und die zugehörigen DLLs.
* Ein Visual C++ Redistributable Package namens **vcredist_x64.exe**.

Kopieren Sie das Paket auf ein USB-Laufwerk oder ein anderes tragbares Speichergerät.

## <a name="step-2-prepare-your-disconnected-workstation"></a>Schritt 2: Vorbereiten der verbindungslosen Arbeitsstation

Führen Sie für diesen zweiten Schritt die folgenden Verfahren auf der Arbeitsstation durch, die nicht mit einem Netzwerk (dem Internet oder Ihrem internen Netzwerk) verbunden ist.

### <a name="step-21-prepare-the-disconnected-workstation-with-ncipher-nshield-hsm"></a>Schritt 2.1: Vorbereiten der verbindungslosen Arbeitsstation per nCipher nShield-HSM

Installieren Sie die Supportsoftware nCipher auf einem Windows-Computer, und schließen Sie dann ein nCipher nShield-HSM an diesen Computer an.

Stellen Sie sicher, dass sich die nCipher-Tools in Ihrem Pfad befinden ( **%nfast_home%\bin**). Geben Sie beispielsweise Folgendes ein:

  ```cmd
  set PATH=%PATH%;"%nfast_home%\bin"
  ```

Weitere Informationen finden Sie im Benutzerhandbuch zum nShield-HSM.

### <a name="step-22-install-the-byok-toolset-on-the-disconnected-workstation"></a>Schritt 2.2: Installieren des BYOK-Toolsets auf der verbindungslosen Arbeitsstation

Kopieren Sie das BYOK-Toolsetpaket vom USB-Laufwerk bzw. vom tragbaren Speichergerät, und führen Sie Folgendes aus:

1. Extrahieren Sie die Dateien aus dem heruntergeladenen Paket in einen beliebigen Ordner.
2. Führen Sie in diesem Ordner die Datei „vcredist_x64.exe“ aus.
3. Folgen Sie den Anweisungen zum Installieren der Visual C++-Laufzeitkomponenten für Visual Studio 2013.

## <a name="step-3-generate-your-key"></a>Schritt 3: Generieren des Schlüssels

Führen Sie für diesen dritten Schritt die folgenden Verfahren auf der verbindungslosen Arbeitsstation durch. Um diesen Schritt ausführen zu können, muss sich das HSM im Initialisierungsmodus befinden. 

### <a name="step-31-change-the-hsm-mode-to-i"></a>Schritt 3.1: Ändern des HSM-Modus in „I“

Wenn Sie nCipher nShield Edge verwenden, ändern Sie den Modus wie folgt: 1. Verwenden Sie die Taste „Mode“, um den erforderlichen Modus hervorzuheben. 2. Halten Sie innerhalb weniger Sekunden die Taste „Clear“ ein paar Sekunden lang gedrückt. Wenn der Modus geändert wurde, hört die LED des neuen Modus auf zu blinken und leuchtet dauerhaft. Möglicherweise blinkt die Status-LED erst ein paar Sekunden lang unregelmäßig und dann regelmäßig, wenn das Gerät bereit ist. Andernfalls verbleibt das Gerät im aktuellen Modus, wobei die LED des entsprechenden Modus leuchtet.

### <a name="step-32-create-a-security-world"></a>Schritt 3.2: Erstellen einer Sicherheitsumgebung (Security World)

Starten Sie eine Eingabeaufforderung, und führen Sie das nCipher-new-world-Programm aus.

   ```cmd
    new-world.exe --initialize --cipher-suite=DLf3072s256mRijndael --module=1 --acs-quorum=2/3
   ```

Dieses Programm erstellt eine Datei für die **Security World** unter „%NFAST_KMDATA%\local\world“ entsprechend dem Ordner „C:\ProgramData\nCipher\Key Management Data\local“. Sie können verschiedene Werte für das Quorum verwenden, aber in unserem Beispiel werden Sie aufgefordert, jeweils drei leere Karten und PINs einzugeben. Sie erhalten dann mit zwei beliebigen Karten vollständigen Zugriff auf die Security World. Diese Karten werden zur **Administratorkartengruppe** für die neue Security World.

> [!NOTE]
> Wenn Ihr HSM die neuere Cypher Suite DLf3072s256mRijndael nicht unterstützt, können Sie „--cipher-suite= DLf3072s256mRijndael“ durch „--cipher-suite=DLf1024s160mRijndael“ ersetzen.
> 
> Die mit new-world.exe erstellte Security World, die mit der nCipher-Software (Version 12.50) ausgeliefert wird, ist mit diesem BYOK-Verfahren nicht kompatibel. Es stehen zwei Optionen zur Verfügung:
> 1) Downgrade der nCipher-Software auf Version 12.40.2, um eine neue Security World zu erstellen.
> 2) Wenden Sie sich an den nCipher-Support und fordern Sie ihn auf, einen Hotfix für die Softwareversion 12.50 bereitzustellen, mit dem Sie die Version 12.40.2 von new-world.exe verwenden können, die mit diesem BYOK-Verfahren kompatibel ist.

Gehen Sie wie folgt vor:

* Sichern Sie die World-Datei. Bewahren Sie die World-Datei, die Administratorkarten und die dazugehörigen PINs an einem geschützten Ort auf, und stellen Sie sicher, dass Personen jeweils nur Zugang zu einer Karte haben.

### <a name="step-33-change-the-hsm-mode-to-o"></a>Schritt 3.3: Ändern des HSM-Modus in „O“

Wenn Sie nCipher nShield Edge verwenden, ändern Sie den Modus wie folgt: 1. Verwenden Sie die Taste „Mode“, um den erforderlichen Modus hervorzuheben. 2. Halten Sie innerhalb weniger Sekunden die Taste „Clear“ ein paar Sekunden lang gedrückt. Wenn der Modus geändert wurde, hört die LED des neuen Modus auf zu blinken und leuchtet dauerhaft. Möglicherweise blinkt die Status-LED erst ein paar Sekunden lang unregelmäßig und dann regelmäßig, wenn das Gerät bereit ist. Andernfalls verbleibt das Gerät im aktuellen Modus, wobei die LED des entsprechenden Modus leuchtet.

### <a name="step-34-validate-the-downloaded-package"></a>Schritt 3.4: Überprüfen des heruntergeladenen Pakets

Dieser Schritt ist optional, wird aber empfohlen, damit Sie Folgendes überprüfen können:

* Der Schlüsselaustauschschlüssel, der im Toolset enthalten ist, wurde mit einem Original-HSM von nCipher nShield generiert.
* Der Hash der Security World, der im Toolset enthalten ist, wurde mit einem Original-HSM von nCipher nShield generiert.
* Der Schlüsselaustauschschlüssel kann nicht exportiert werden.

> [!NOTE]
> Um das heruntergeladene Paket überprüfen zu können, muss das HSM verbunden sein, eingeschaltet sein und über eine Security World verfügen (z. B. die eben erstellte).

So überprüfen Sie das heruntergeladene Paket

1. Führen Sie das Skript „verifykeypackage.py“ aus, indem Sie je nach Region oder Instanz von Azure Folgendes eingeben:

   * Für Nordamerika:

      ```azurepowershell
      "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-NA-1 -w BYOK-SecurityWorld-pkg-NA-1
      ```
   * Für Europa:

      ```azurepowershell
      "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-EU-1 -w BYOK-SecurityWorld-pkg-EU-1
      ```
   * Für Asien:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-AP-1 -w BYOK-SecurityWorld-pkg-AP-1
        ```
   * Für Lateinamerika:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-LATAM-1 -w BYOK-SecurityWorld-pkg-LATAM-1
        ```
   * Für Japan:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-JPN-1 -w BYOK-SecurityWorld-pkg-JPN-1
        ```
   * Für Korea:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-KOREA-1 -w BYOK-SecurityWorld-pkg-KOREA-1
        ```
   * Für Südafrika:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-SA-1 -w BYOK-SecurityWorld-pkg-SA-1
        ```
   * Für Vereinigte Arabische Emirate:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-UAE-1 -w BYOK-SecurityWorld-pkg-UAE-1
        ```
   * Für Australien:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-AUS-1 -w BYOK-SecurityWorld-pkg-AUS-1
        ```
   * Für [Azure Government](https://azure.microsoft.com/features/gov/)(arbeitet mit der Instanz der US-Regierung von Azure):

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-USGOV-1 -w BYOK-SecurityWorld-pkg-USGOV-1
        ```
   * Für US Government (US-Verteidigungsministerium):

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-USDOD-1 -w BYOK-SecurityWorld-pkg-USDOD-1
        ```
   * Für Kanada:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-CANADA-1 -w BYOK-SecurityWorld-pkg-CANADA-1
        ```
   * Für Deutschland:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-GERMANY-1 -w BYOK-SecurityWorld-pkg-GERMANY-1
        ```
   * Für Deutschland (öffentlich):

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-GERMANY-1 -w BYOK-SecurityWorld-pkg-GERMANY-1
        ```
   * Für Indien:

      ```azurepowershell
      "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-INDIA-1 -w BYOK-SecurityWorld-pkg-INDIA-1
      ```
   * Für Frankreich:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-FRANCE-1 -w BYOK-SecurityWorld-pkg-FRANCE-1
        ```
   * Für das Vereinigte Königreich:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-UK-1 -w BYOK-SecurityWorld-pkg-UK-1
        ```
   * Für die Schweiz:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-SUI-1 -w BYOK-SecurityWorld-pkg-SUI-1
        ```

     > [!TIP]
     > Die nCipher nShield-Software enthält Python unter „%NFAST_HOME%\python\bin“.
     >
     >
2. Vergewissern Sie sich, dass Folgendes angezeigt wird, um eine erfolgreiche Überprüfung zu melden: **Result: SUCCESS**

Mit diesem Skript wird die Signaturgeberkette bis zum nShield-Stammschlüssel überprüft. Der Hash dieses Stammschlüssels ist in das Skript eingebettet, und sein Wert sollte **59178a47 de508c3f 291277ee 184f46c4 f1d9c639** lauten. Sie können diesen Wert auch separat bestätigen, indem Sie die [nCipher-Website](https://www.ncipher.com)besuchen.

Sie können nun einen neuen Schlüssel erstellen.

### <a name="step-35-create-a-new-key"></a>Schritt 3.5: Erstellen eines neuen Schlüssels

Generieren Sie einen Schlüssel, indem Sie das nCipher nShield-Programm **generatekey** verwenden.

Führen Sie den folgenden Befehl aus, um den Schlüssel zu generieren:

```azurepowershell
generatekey --generate simple type=RSA size=2048 protect=module ident=contosokey plainname=contosokey nvram=no pubexp=
```

Gehen Sie wie folgt vor, wenn Sie diesen Befehl ausführen:

* Der Parameter *protect* muss wie gezeigt auf den Wert **module** festgelegt werden. So wird ein modulgeschützter Schlüssel erstellt. Das BYOK-Toolset unterstützt OCS-geschützte Schlüssel nicht.
* Ersetzen Sie den Wert von *contosokey* für **ident** und **plainname** durch einen beliebigen Zeichenfolgenwert. Um den Verwaltungsaufwand zu minimieren und das Fehlerrisiko zu senken, empfehlen wir, jeweils den gleichen Wert zu verwenden. Der Wert **Ident** darf nur Zahlen, Bindestriche und Kleinbuchstaben enthalten.
* „pubexp“ wird in diesem Beispiel leer gelassen (Standard), aber Sie können bestimmte Werte angeben. Weitere Informationen finden Sie in der [nCipher-Dokumentation](https://www.entrust.com/-/media/documentation/brochures/entrust-nshield-general-purpose-hsms-br-a4.pdf).

Mit diesem Befehl wird im Ordner „%NFAST_KMDATA%\local“ eine Tokenschlüsseldatei erstellt, deren Name mit **key_simple_** beginnt. Danach folgt der Wert von **ident**, der im Befehl angegeben wurde. Beispiel: **key_simple_contosokey**. Diese Datei enthält einen verschlüsselten Schlüssel.

Sichern Sie diese Tokenschlüsseldatei an einem sicheren Ort.

> [!IMPORTANT]
> Wenn Sie Ihren Schlüssel später in den Azure-Schlüsseltresor übertragen, kann Microsoft diesen Schlüssel nicht für Sie zurück exportieren. Daher ist es sehr wichtig, dass Sie Ihren Schlüssel und die Security World sorgfältig sichern. Informationen und bewährte Methoden zum Sichern des Schlüssels erhalten Sie bei [nCipher](https://www.ncipher.com/about-us/contact-us).
>


Sie können Ihren Schlüssel jetzt an den Azure-Schlüsseltresor übertragen.

## <a name="step-4-prepare-your-key-for-transfer"></a>Schritt 4: Vorbereiten des Schlüssels für die Übertragung

Führen Sie für diesen vierten Schritt die folgenden Verfahren auf der verbindungslosen Arbeitsstation durch.

### <a name="step-41-create-a-copy-of-your-key-with-reduced-permissions"></a>Schritt 4.1: Erstellen einer Kopie des Schlüssels mit reduzierten Berechtigungen

Öffnen Sie eine neue Eingabeaufforderung, und ändern Sie das aktuelle Verzeichnis in den Speicherort, an dem Sie die BYOK-ZIP-Datei entpackt haben. Um die Berechtigungen für den Schlüssel zu reduzieren, führen Sie an einer Eingabeaufforderung je nach Region oder Instanz von Azure Folgendes aus:

* Für Nordamerika:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-
   ```
* Für Europa:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1
   ```
* Für Asien:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1
   ```
* Für Lateinamerika:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1
   ```
* Für Japan:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1
   ```
* Für Korea:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-KOREA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-KOREA-1
   ```
* Für Südafrika:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SA-1
   ```
* Für Vereinigte Arabische Emirate:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UAE-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UAE-1
   ```
* Für Australien:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1
   ```
* Für [Azure Government](https://azure.microsoft.com/features/gov/)(arbeitet mit der Instanz der US-Regierung von Azure):

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1
   ```
* Für US Government (US-Verteidigungsministerium):

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USDOD-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USDOD-1
   ```
* Für Kanada:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1
   ```
* Für Deutschland:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1
   ```
* Für Deutschland (öffentlich):

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1
   ```
* Für Indien:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1
   ```
* Für Frankreich:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-FRANCE-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-FRANCE-1
   ```
* Für das Vereinigte Königreich:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UK-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UK-1
   ```
* Für die Schweiz:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SUI-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SUI-1
   ```

Ersetzen Sie beim Ausführen dieses Befehls *contosokey* durch den gleichen Wert, den Sie in **Schritt 3.5: Erstellen eines neuen Schlüssels** unter [Generieren des Schlüssels](#step-3-generate-your-key) angegeben haben.

Sie werden aufgefordert, Ihre Security World-Administratorkarten einzuführen.

Wenn der Befehl abgeschlossen ist, wird **Result: SUCCESS** angezeigt, und die Kopie Ihres Schlüssels mit reduzierten Berechtigungen ist in der Datei „key_xferacId_\<contosokey>“ enthalten.

Sie können die ACLs mithilfe der nCipher nShield-Hilfsprogramme mit den folgenden Befehlen überprüfen:

* aclprint.py:

   ```cmd
   "%nfast_home%\bin\preload.exe" -m 1 -A xferacld -K contosokey "%nfast_home%\python\bin\python" "%nfast_home%\python\examples\aclprint.py"
   ```
* kmfile-dump.exe:

   ```cmd
   "%nfast_home%\bin\kmfile-dump.exe" "%NFAST_KMDATA%\local\key_xferacld_contosokey"
   ```
  Ersetzen Sie beim Ausführen dieser Befehle „contosokey“ durch den gleichen Wert, den Sie in **Schritt 3.5: Erstellen eines neuen Schlüssels** unter [Generieren des Schlüssels](#step-3-generate-your-key) angegeben haben.

### <a name="step-42-encrypt-your-key-by-using-microsofts-key-exchange-key"></a>Schritt 4.2: Verschlüsseln des Schlüssels mit dem Schlüsselaustauschschlüssel von Microsoft

Führen Sie je nach geografischer Region oder Instanz von Azure einen der folgenden Befehle aus:

* Für Nordamerika:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Für Europa:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Für Asien:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Für Lateinamerika:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Für Japan:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Für Korea:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-KOREA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-KOREA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Für Südafrika:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Für Vereinigte Arabische Emirate:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UAE-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UAE-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Für Australien:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Für [Azure Government](https://azure.microsoft.com/features/gov/)(arbeitet mit der Instanz der US-Regierung von Azure):

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Für US Government (US-Verteidigungsministerium):

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USDOD-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USDOD-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Für Kanada:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Für Deutschland:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Für Deutschland (öffentlich):

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Für Indien:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Für Frankreich:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-France-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-France-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Für das Vereinigte Königreich:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UK-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UK-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Für die Schweiz:

  ```azurepowershell
  KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SUI-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SUI-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
  ```


Gehen Sie wie folgt vor, wenn Sie diesen Befehl ausführen:

* Ersetzen Sie *contosokey* durch den Bezeichner, den Sie in **Schritt 3.5: Erstellen eines neuen Schlüssels** unter [Generieren des Schlüssels](#step-3-generate-your-key) angegeben haben.
* Ersetzen Sie *SubscriptionID* durch die ID des Azure-Abonnements, das Ihren Schlüsseltresor enthält. Sie haben diesen Wert zuvor unter **Schritt 1.2: Abrufen der Azure-Abonnement-ID** im Schritt [Vorbereiten der Arbeitsstation mit Internetverbindung](#step-1-prepare-your-internet-connected-workstation) abgerufen.
* Ersetzen Sie *ContosoFirstHSMKey* durch eine Bezeichnung, die als Name Ihrer Ausgabedatei verwendet wird.

Wenn dieser Vorgang erfolgreich ist, wird **Result: SUCCESS** angezeigt, und im aktuellen Ordner ist eine neue Datei mit dem folgenden Namen enthalten: KeyTransferPackage-*ContosoFirstHSMkey*.byok

### <a name="step-43-copy-your-key-transfer-package-to-the-internet-connected-workstation"></a>Schritt 4.3: Kopieren des Schlüsselübertragungspakets auf die Arbeitsstation mit Internetverbindung

Verwenden Sie ein USB-Laufwerk oder anderes tragbares Speichergerät, um die Ausgabedatei aus dem vorherigen Schritt (KeyTransferPackage-ContosoFirstHSMkey.byok) auf die Arbeitsstation mit Internetverbindung zu kopieren.

## <a name="step-5-transfer-your-key-to-azure-key-vault"></a>Schritt 5: Übertragen des Schlüssels an Azure Key Vault

Verwenden Sie für diesen letzten Schritt auf der Arbeitsstation mit Internetverbindung das Cmdlet [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey), um das Schlüsselübertragungspaket hochzuladen, das Sie von der verbindungslosen Arbeitsstation in das Azure Key Vault-HSM kopiert haben:

   ```powershell
        Add-AzKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMkey' -KeyFilePath 'c:\KeyTransferPackage-ContosoFirstHSMkey.byok' -Destination 'HSM'
   ```

Wenn der Upload erfolgreich ist, werden die Eigenschaften des gerade hinzugefügten Schlüssels angezeigt.

## <a name="next-steps"></a>Nächste Schritte

Sie können diesen HSM-geschützten Schlüssel jetzt in Ihrem Schlüsseltresor verwenden. Weitere Informationen finden Sie in dieser [Gegenüberstellung](https://azure.microsoft.com/pricing/details/key-vault/) von Preisen und Funktionen.