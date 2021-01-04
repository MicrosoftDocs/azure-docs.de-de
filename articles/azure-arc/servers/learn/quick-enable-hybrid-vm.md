---
title: Verbinden eines Hybridcomputers mit Azure Arc-fähigen Servern
description: Hier erfahren Sie, wie Sie Ihren Hybridcomputer mit Azure Arc-fähigen Servern verbinden und registrieren.
ms.topic: quickstart
ms.date: 11/12/2020
ms.openlocfilehash: 3779d95ac138e83b1d953f744e07ae553890a5d7
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/13/2020
ms.locfileid: "94576843"
---
# <a name="quickstart-connect-hybrid-machine-with-azure-arc-enabled-servers"></a>Schnellstart: Verbinden eines Hybridcomputers mit Azure Arc-fähigen Servern

Mit [Azure Arc-fähigen Servern](../overview.md) können Sie Ihre Windows- und Linux-Computer, die in lokalen Umgebungen, in Edgeumgebungen und in Multicloudumgebungen gehostet werden, verwalten und steuern. In dieser Schnellstartanleitung wird der Connected Machine-Agent auf Ihrem außerhalb von Azure gehosteten Windows- oder Linux-Computer bereitgestellt und für die Verwaltung durch Azure Arc-fähige Server konfiguriert.

## <a name="prerequisites"></a>Voraussetzungen

* Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

* Zur Bereitstellung des hybriden Connected Machine-Agents für Azure Arc-fähige Server müssen Sie auf dem Computer über Administratorberechtigungen verfügen, um den Agent installieren und konfigurieren zu können. Verwenden Sie dazu unter Linux das root-Konto und unter Windows ein Konto, das der Gruppe „Lokale Administratoren“ angehört.

* Machen Sie sich zunächst mit den [Voraussetzungen](../agent-overview.md#prerequisites) für den Agent vertraut, und vergewissern Sie sich, dass Folgendes erfüllt ist:

    * Auf Ihrem Zielcomputer wird ein unterstütztes [Betriebssystem](../agent-overview.md#supported-operating-systems) ausgeführt.

    * Ihrem Konto sind die [erforderlichen Azure-Rollen](../agent-overview.md#required-permissions) zugewiesen.

    * Sollte der Computer für die Kommunikation über das Internet eine Verbindung über eine Firewall oder über einen Proxyserver herstellen, sorgen Sie dafür, dass die [aufgeführten](../agent-overview.md#networking-configuration) URLs nicht blockiert werden.

    * Von Azure Arc-fähigen Servern werden nur die [hier](../overview.md#supported-regions) angegebenen Regionen unterstützt.

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

## <a name="register-azure-resource-providers"></a>Registrieren von Azure-Ressourcenanbietern

Azure Arc-fähige Server benötigen folgende Azure-Ressourcenanbieter in Ihrem Abonnement, um diesen Dienst nutzen zu können:

* Microsoft.HybridCompute
* Microsoft.GuestConfiguration

Registrieren Sie sie mithilfe folgender Befehle:

```azurecli-interactive
az account set --subscription "{Your Subscription Name}"
az provider register --namespace 'Microsoft.HybridCompute'
az provider register --namespace 'Microsoft.GuestConfiguration'
```

## <a name="generate-installation-script"></a>Generieren des Installationsskripts

Das Skript zum Automatisieren des Download- und Installationsvorgangs sowie zum Herstellen der Verbindung mit Azure Arc ist über das Azure-Portal verfügbar. Gehen Sie wie folgt vor:

1. Starten Sie den Azure Arc-Dienst im Azure-Portal, indem Sie auf **Alle Dienste** klicken und dann nach **Server – Azure Arc** suchen und diese Option auswählen.

    :::image type="content" source="./media/quick-enable-hybrid-vm/search-machines.png" alt-text="Suchen nach Servern mit Arc-Unterstützung unter „Alle Dienste“" border="false":::

1. Wählen Sie auf der Seite **Server - Azure Arc** oben links **Hinzufügen** aus.

1. Wählen Sie auf der Seite **Methode auswählen** die Kachel **Server über ein interaktives Skript hinzufügen** und dann **Skript generieren** aus.

1. Wählen Sie auf der Seite **Skript generieren** das Abonnement und die Ressourcengruppe für die Verwaltung des Computers in Azure aus. Wählen Sie einen Azure-Standort zum Speichern der Computermetadaten aus. Bei diesem Standort kann es sich um denselben Standort wie bei der Ressourcengruppe oder um einen anderen Standort handeln.

1. Lesen Sie sich die Informationen auf der Seite **Voraussetzungen** durch, und wählen Sie dann **Weiter: Ressourcendetails** aus.

1. Geben Sie auf der Seite **Ressourcendetails** Folgendes an:

    1. Wählen Sie in der Dropdownliste **Ressourcengruppe** die Ressourcengruppe aus, über die der Computer verwaltet wird.
    1. Wählen Sie in der Dropdownliste **Regionen** die Azure-Region aus, in der die Metadaten des Servers gespeichert werden sollen.
    1. Wählen Sie in der Dropdownliste **Betriebssystem** das Betriebssystem aus, unter dem die Ausführung des Skripts konfiguriert werden soll.
    1. Wenn der Computer über einen Proxyserver kommuniziert, um eine Verbindung mit dem Internet herzustellen, geben Sie die IP-Adresse des Proxyservers oder den Namen und die Portnummer an, die der Computer für die Kommunikation mit dem Proxyserver verwenden wird. Geben Sie den Wert im Format `http://<proxyURL>:<proxyport>` ein.
    1. Klicken Sie auf **Weiter: Tags**.

1. Überprüfen Sie auf der Seite **Tags** die vorgeschlagenen standardmäßigen **physischen Speicherorttags**, und geben Sie einen Wert ein, oder geben Sie mindestens ein **benutzerdefiniertes Tag** an, um Ihre Standards zu unterstützen.

1. Klicken Sie auf **Weiter: Skript herunterladen und ausführen**.

1. Überprüfen Sie die auf der Seite **Skript herunterladen und ausführen** die Zusammenfassungsinformationen, und wählen Sie dann **Herunterladen** aus. Sollten noch Änderungen erforderlich sein, wählen Sie **Zurück** aus.

## <a name="install-the-agent-using-the-script"></a>Installieren des Agents mithilfe des Skripts

### <a name="windows-agent"></a>Windows-Agent

1. Melden Sie sich beim Server an.

1. Öffnen Sie eine 64-Bit-PowerShell-Eingabeaufforderung mit erhöhten Rechten.

1. Navigieren Sie zu dem Ordner bzw. zu der Freigabe, in den bzw. in die Sie das Skript kopiert haben, und führen Sie es auf dem Server aus, indem Sie das Skript `./OnboardingScript.ps1` ausführen.

### <a name="linux-agent"></a>Linux-Agent

1. Führen Sie den folgenden Befehl aus, um den Linux-Agent auf dem Zielcomputer zu installieren, der direkt mit Azure kommunizieren kann:

    ```bash
    bash ~/Install_linux_azcmagent.sh
    ```

    * Wenn der Zielcomputer über einen Proxyserver kommuniziert, führen Sie den folgenden Befehl aus:

        ```bash
        bash ~/Install_linux_azcmagent.sh --proxy "{proxy-url}:{proxy-port}"
        ```

## <a name="verify-the-connection-with-azure-arc"></a>Überprüfen der Verbindung mit Azure Arc

Vergewissern Sie sich im Azure-Portal, dass die Serververbindung erfolgreich hergestellt wurde, nachdem Sie den Agent installiert und für die Verbindungsherstellung mit Azure Arc-fähigen Servern konfiguriert haben. Sehen Sie sich Ihre Computer im [Azure-Portal](https://aka.ms/hybridmachineportal) an.

:::image type="content" source="./media/quick-enable-hybrid-vm/enabled-machine.png" alt-text="Erfolgreiche Computerverbindung" border="false":::

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie Ihren Linux- oder Windows-Hybridcomputer aktiviert und erfolgreich mit dem Dienst verbunden haben, können Sie Azure Policy aktivieren, um sich mit der Compliance in Azure zu beschäftigen.

Im nächsten Tutorial erfahren Sie, wie Sie Azure Arc-fähige Server identifizieren, auf denen der Log Analytics-Agent nicht installiert ist:

> [!div class="nextstepaction"]
> [Erstellen einer Richtlinienzuweisung zum Identifizieren nicht konformer Ressourcen](tutorial-assign-policy-portal.md)
