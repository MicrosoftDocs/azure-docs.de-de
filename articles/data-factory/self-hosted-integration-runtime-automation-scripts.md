---
title: Automatisieren der Installation von selbstgehosteter Integration Runtime mithilfe von lokalen PowerShell-Skripts
description: Informationen zum Automatisieren der Installation von selbstgehosteter Integration Runtime auf lokalen Computern
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: anandsub
ms.custom: seo-lt-2019
ms.date: 05/09/2020
ms.openlocfilehash: 36414c975e97dbaa7d8747da98c31eeb12fbc206
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92636968"
---
# <a name="automating-self-hosted-integration-runtime-installation-using-local-powershell-scripts"></a>Automatisieren der Installation von selbstgehosteter Integration Runtime mithilfe von lokalen PowerShell-Skripts
Sie können mithilfe von lokalen PowerShell-Skripts die Installation von selbstgehosteter Integration Runtime auf lokalen Computern automatisieren (mit Ausnahme von Azure VMs, bei denen Sie stattdessen die Resource Manager-Vorlage nutzen können). In diesem Artikel werden zwei Skripts vorgestellt, die Sie dafür verwenden können.

## <a name="prerequisites"></a>Voraussetzungen

* Starten Sie PowerShell auf Ihrem lokalen Computer. Zum Ausführen der Skripts müssen Sie **Als Administrator ausführen** auswählen.
* [Laden Sie](https://www.microsoft.com/download/details.aspx?id=39717) die selbstgehostete Integration Runtime-Software herunter. Kopieren Sie den Pfad der heruntergeladenen Datei. 
* Außerdem benötigen Sie einen **Authentifizierungsschlüssel** zum Registrieren der selbstgehosteten Integration Runtime.
* Zum Automatisieren manueller Updates müssen Sie eine vorkonfigurierte selbstgehostete Integration Runtime haben.

## <a name="scripts-introduction"></a>Einführung in Skripts 

> [!NOTE]
> Diese Skripts werden mithilfe des [dokumentierten Befehlszeilen-Hilfsprogramms](./create-self-hosted-integration-runtime.md#set-up-an-existing-self-hosted-ir-via-local-powershell) in der selbstgehosteten Integration Runtime erstellt. Bei Bedarf können die Skripts entsprechend angepasst werden, um deren Automatisierungsanforderungen zu erfüllen.
> Die Skripts müssen pro Knoten angewendet werden. Stellen Sie deshalb sicher, dass sie bei einem Setup mit Hochverfügbarkeit (2 oder mehr Knoten) auf allen Knoten ausgeführt werden.

* Zur Automatisierung des Setups: Installieren und registrieren Sie einen neuen selbstgehosteten Integration Runtime-Knoten mithilfe von **[InstallGatewayOnLocalMachine.ps1](https://github.com/nabhishek/SelfHosted-IntegrationRuntime_AutomationScripts/blob/master/InstallGatewayOnLocalMachine.ps1)** – Das Skript kann zum Installieren des selbstgehosteten Integration Runtime-Knotens und zu dessen Registrierung mit einem Authentifizierungsschlüssel verwendet werden. Es akzeptiert zwei Argumente: das **erste** zur Angabe des Speicherorts für die [selbstgehostete Integration Runtime](https://www.microsoft.com/download/details.aspx?id=39717) auf einem lokalen Datenträger, das **zweite** zur Angabe des **Authentifizierungsschlüssels** (für die Registrierung eines selbstgehosteten IR-Knotens).

* Zur Automatisierung manueller Updates: Aktualisieren Sie den selbstgehosteten IR-Knoten mit einer bestimmten Version oder auf die neueste Version **[script-update-gateway.ps1](https://github.com/nabhishek/SelfHosted-IntegrationRuntime_AutomationScripts/blob/master/script-update-gateway.ps1)** . Dies wird auch für den Fall unterstützt, dass Sie das automatische Update deaktiviert haben oder mehr Kontrolle über Updates haben möchten. Das Skript kann zum Aktualisieren des selbstgehosteten Integration Runtime-Knotens auf die neueste Version oder eine angegebene höhere Version verwendet werden (ein Downgrade funktioniert nicht). Es akzeptiert ein Argument zur Angabe der Versionsnummer (Beispiel: „-Version 3.13.6942.1“). Wenn keine Version angegeben wurde, wird die selbstgehostete IR immer auf die neueste Version aus den [Downloads](https://www.microsoft.com/download/details.aspx?id=39717) aktualisiert.
    > [!NOTE]
    > Es können nur die letzten 3 Versionen angegeben werden. Im Idealfall wird damit ein vorhandener Knoten auf die neueste Version aktualisiert. **DABEI WIRD DAVON AUSGEGANGEN, DASS SIE EINE REGISTRIERTE SELBSTGEHOSTETE IR HABEN** . 

## <a name="usage-examples"></a>Anwendungsbeispiele

### <a name="for-automating-setup"></a>Zur Automatisierung des Setups
1. Laden Sie die selbstgehostete IR von [hier](https://www.microsoft.com/download/details.aspx?id=39717) herunter. 
1. Geben Sie den Pfad für die oben heruntergeladene SHIR-MSI (Installationsdatei) an. Wenn der Pfad beispielsweise *C:\Benutzer\benutzername\Downloads\IntegrationRuntime_4.7.7368.1.msi* lautet, können Sie das folgende PowerShell-Befehlszeilenbeispiel für diese Aufgabe verwenden:

   ```powershell
   PS C:\windows\system32> C:\Users\username\Desktop\InstallGatewayOnLocalMachine.ps1 -path "C:\Users\username\Downloads\IntegrationRuntime_4.7.7368.1.msi" -authKey "[key]"
   ```

    > [!NOTE]
    > Ersetzen Sie „[key]“ durch den Authentifizierungsschlüssel zum Registrieren Ihrer IR.
    > Ersetzen Sie „benutzername“ durch Ihren Benutzernamen.
    > Geben Sie bei der Ausführung des Skripts den Speicherort der Datei „InstallGatewayOnLocalMachine.ps1“ an. In diesem Beispiel wurde sie auf dem Desktop gespeichert.

1. Wenn es auf Ihrem Computer eine vorinstallierte selbstgehostete IR gibt, wird sie vom Skript automatisch deinstalliert und dann eine neue konfiguriert. Das folgende Fenster wird eingeblendet: ![configure integration runtime](media/self-hosted-integration-runtime-automation-scripts/integration-runtime-configure.png) (Integration Runtime konfigurieren).

1. Nach Abschluss der Installation und Schlüsselregistrierung werden in Ihrer lokalen PowerShell die Ergebnisse *Succeed to install gateway* (Gateway wurde erfolgreich installiert) und *Succeed to register gateway* (Gateway wurde erfolgreich registriert) angezeigt.
        [![Skript 1, Ausführungsergebnis](media/self-hosted-integration-runtime-automation-scripts/script-1-run-result.png)](media/self-hosted-integration-runtime-automation-scripts/script-1-run-result.png#lightbox)

### <a name="for-automating-manual-updates"></a>Zur Automatisierung manueller Updates
Dieses Skript wird zum Aktualisieren/Installieren + Registrieren der neuesten selbstgehosteten Integration Runtime verwendet. Mit dem Skript werden die folgenden Schritte ausgeführt:
1. Überprüfung der aktuellen selbstgehosteten IR-Version
2. Abrufen der neuesten Version oder der angegebenen Version aus dem Argument
3. Wenn es eine neuere als die aktuelle Version gibt:
    * Herunterladen einer selbstgehosteten IR MSI
    * Ausführen des Upgrades

Zur Verwendung dieses Skripts können Sie dem nachstehenden Befehlszeilenbeispiel folgen:
* Herunterladen und Installieren des neuesten Gateways:

   ```powershell
   PS C:\windows\system32> C:\Users\username\Desktop\script-update-gateway.ps1
   ```    
* Herunterladen und Installieren des Gateways der angegebenen Version:
   ```powershell
   PS C:\windows\system32> C:\Users\username\Desktop\script-update-gateway.ps1 -version 3.13.6942.1
   ``` 
   Wenn Ihre aktuelle Version bereits die neueste ist, wird das folgende Ergebnis angezeigt und Ihnen mitgeteilt, dass kein Update erforderlich ist.   
    [![Skript 2, Ausführungsergebnis](media/self-hosted-integration-runtime-automation-scripts/script-2-run-result.png)](media/self-hosted-integration-runtime-automation-scripts/script-2-run-result.png#lightbox)