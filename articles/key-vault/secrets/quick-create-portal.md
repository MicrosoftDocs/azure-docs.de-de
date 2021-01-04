---
title: 'Azure-Schnellstart: Festlegen eines Geheimnisses und Abrufen des Geheimnisses aus Key Vault mithilfe des Azure-Portals | Microsoft-Dokumentation'
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie mithilfe des Azure-Portals ein Geheimnis in Azure Key Vault festlegen und das Geheimnis daraus abrufen.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/03/2019
ms.author: mbaldwin
ms.openlocfilehash: 3d7e6357fd8f1091509cbf27875c028d3af310cb
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96445919"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-the-azure-portal"></a>Schnellstart: Festlegen eines Geheimnisses und Abrufen des Geheimnisses aus Azure Key Vault mithilfe des Azure-Portals

Azure Key Vault ist ein Clouddienst, der als sicherer Geheimnisspeicher fungiert. Dadurch können Schlüssel, Kennwörter, Zertifikate und andere Geheimnisse sicher gespeichert werden. Azure Key Vault-Instanzen können über das Azure-Portal erstellt und verwaltet werden. In dieser Schnellstartanleitung erstellen Sie einen Schlüsseltresor und verwenden diesen dann, um ein Geheimnis zu speichern. 

Weitere Informationen finden Sie hier: 
- [Übersicht über Key Vault](../general/overview.md)
- [Informationen zu Azure Key Vault-Geheimnissen](about-secrets.md)

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen ein Azure-Abonnement, um auf Azure Key Vault zuzugreifen. Wenn Sie noch kein Abonnement haben, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

Der gesamte Zugriff auf Geheimnisse erfolgt über Azure Key Vault. Für diese Schnellstartanleitung können Sie einen Schlüsseltresor im [Azure-Portal](../general/quick-create-portal.md), mit der [Azure-Befehlszeilenschnittstelle](../general/quick-create-cli.md) oder mit [Azure PowerShell](../general/quick-create-powershell.md) erstellen.

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich unter https://portal.azure.com beim Azure-Portal an.

## <a name="add-a-secret-to-key-vault"></a>Hinzufügen eines Geheimnisses zu Key Vault

Führen Sie zum Hinzufügen eines Geheimnisses zum Tresor die folgenden Schritte aus:

1. Navigieren Sie im Azure-Portal zu Ihrem neuen Schlüsseltresor.
1. Wählen Sie auf den Seiten mit den Key Vault-Einstellungen **Geheimnisse** aus.
1. Klicken Sie auf **Generieren/Importieren**.
1. Wählen Sie auf dem Bildschirm **Geheimnis erstellen** folgende Werte aus:
    - **Uploadoptionen**: Manuell
    - **Name**: Geben Sie einen Namen für das Geheimnis ein. Der Geheimnisname muss innerhalb einer Key Vault-Instanz eindeutig sein. Der Name muss eine Zeichenfolge mit 1 bis 127 Zeichen sein und mit einem Buchstaben beginnen und darf nur die Zeichen „0 - 9“, „a - z“, „A - Z“ und „-“ enthalten. Weitere Informationen zur Benennung finden Sie unter [Objekte, Bezeichner und Versionsverwaltung](../general/about-keys-secrets-certificates.md#objects-identifiers-and-versioning).
    - **Value**: Geben Sie einen Wert für das Geheimnis ein. Key Vault-APIs akzeptieren geheime Werte als Zeichenfolge und geben sie auch in dieser Form zurück. 
    - Behalten Sie bei den anderen Optionen die Standardwerte bei. Klicken Sie auf **Erstellen**.

Nachdem Sie die Meldung erhalten haben, dass das Geheimnis erfolgreich erstellt wurde, können Sie in der Liste auf das Geheimnis klicken. 

Weitere Informationen zu Geheimnisattributen finden Sie unter [Informationen zu Azure Key Vault-Geheimnissen](./about-secrets.md).

## <a name="retrieve-a-secret-from-key-vault"></a>Abrufen eines Geheimnisses aus Key Vault

Wenn Sie auf die aktuelle Version klicken, sehen Sie den Wert, den Sie im vorherigen Schritt angegeben haben.

![Geheimniseigenschaften](../media/quick-create-portal/current-version-hidden.png)

Wenn Sie im rechten Bereich auf die Schaltfläche „Geheimniswert anzeigen“ klicken, wird der ausgeblendete Wert angezeigt. 

![Angezeigter geheimer Wert](../media/quick-create-portal/current-version-shown.png)

Sie können auch die [Azure CLI]() oder [Azure PowerShell]() verwenden, um das zuvor erstellte Geheimnis abzurufen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Andere Schnellstartanleitungen und Tutorials für Key Vault bauen auf dieser Schnellstartanleitung auf. Falls Sie mit weiteren Schnellstartanleitungen und Tutorials fortfahren möchten, sollten Sie die Ressourcen nicht bereinigen.
Wenn Sie die Ressourcen nicht mehr benötigen, löschen Sie die Ressourcengruppe. Dadurch werden die Key Vault-Instanz und die dazugehörigen Ressourcen gelöscht. So löschen Sie die Ressourcengruppe über das Portal:

1. Geben Sie den Namen Ihrer Ressourcengruppe in das Suchfeld am oberen Rand des Portals ein. Klicken Sie in den Suchergebnissen auf die Ressourcengruppe aus dieser Schnellstartanleitung.
2. Wählen Sie die Option **Ressourcengruppe löschen**.
3. Geben Sie im Feld **GEBEN SIE DEN RESSOURCENGRUPPENNAMEN EIN:** den Namen der Ressourcengruppe ein, und klicken Sie anschließend auf **Löschen**.

> [!NOTE]
> Wichtig ist hierbei Folgendes: Sobald Geheimnisse, Schlüssel, Zertifikate oder Schlüsseltresore gelöscht wurden, können sie wiederhergestellt werden. Dies ist je nach Ihrer Konfiguration für einen Zeitraum von sieben bis 90 Kalendertagen möglich. Wenn keine Konfiguration angegeben wird, wird der Standardwiederherstellungszeitraum von 90 Tagen festgelegt. So haben Benutzer ausreichend Zeit, ein versehentliches Löschen eines Geheimnisses zu erkennen und darauf zu reagieren. Weitere Informationen zum Löschen und Wiederherstellen von Schlüsseltresoren und Schlüsseltresorobjekten finden Sie unter [Übersicht über die Azure Key Vault-Funktion für vorläufiges Löschen](../general/soft-delete-overview.md).

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie eine Key Vault-Instanz erstellt und ein Geheimnis darin gespeichert. Weitere Informationen zu Key Vault und zur Integration in Ihre Anwendungen finden Sie in den folgenden Artikeln:

- [Was ist der Azure-Schlüsseltresor?](../general/overview.md)
- Lesen Sie [Sicherer Zugriff auf einen Schlüsseltresor](../general/secure-your-key-vault.md).
- Weitere Informationen finden Sie unter [Verwenden von Key Vault mit App Service-Web-App](../general/tutorial-net-create-vault-azure-web-app.md).
- Weitere Informationen finden Sie unter [Verwenden von Key Vault mit einer auf einer VM bereitgestellten Anwendung](../general/tutorial-net-virtual-machine.md).
- [Entwicklerhandbuch zu Azure-Schlüsseltresor](../general/developers-guide.md)
- [Bewährte Methoden zum Verwenden von Key Vault](../general/best-practices.md)