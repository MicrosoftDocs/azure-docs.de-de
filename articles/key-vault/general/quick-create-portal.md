---
title: Schnellstart – Erstellen eines Azure Key Vault über das Azure-Portal
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie eine Azure Key Vault-Instanz über das Azure-Portal erstellen.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: quickstart
ms.custom: mvc
ms.date: 12/08/2020
ms.author: mbaldwin
ms.openlocfilehash: b5f4f3d2b3eda9f00049cee26ae95850d65257d6
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96778868"
---
# <a name="quickstart-create-a-key-vault-using-the-azure-portal"></a>Schnellstart: Erstellen eines Schlüsseltresors über das Azure-Portal

Azure Key Vault ist ein Clouddienst, der einen sicheren Speicher für [Schlüssel](../keys/index.yml), [Geheimnisse](../secrets/index.yml) und [Zertifikate](../certificates/index.yml) bereitstellt. Weitere Informationen zu Key Vault finden Sie unter [Informationen zu Azure Key Vault](overview.md). Weitere Informationen zu den Elementen, die in einem Schlüsseltresor gespeichert werden können, finden Sie unter [Informationen zu Schlüsseln, Geheimnissen und Zertifikaten](about-keys-secrets-certificates.md).

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

In dieser Schnellstartanleitung erstellen Sie einen Schlüsseltresor über das [Azure-Portal](https://portal.azure.com). 

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich unter https://portal.azure.com beim Azure-Portal an.

## <a name="create-a-vault"></a>Erstellen eines Tresors

1. Wählen Sie im Menü des Azure-Portals oder auf der **Startseite** die Option **Ressource erstellen** aus.
2. Geben Sie **Key Vault** in das Suchfeld ein.
3. Wählen Sie in der Ergebnisliste **Key Vault** aus.
4. Klicken Sie im Abschnitt „Key Vault“ auf **Erstellen**.
5. Geben Sie im Abschnitt **Schlüsseltresor erstellen** folgende Informationen ein:
    - **Name**: Es ist ein eindeutiger Name erforderlich. Für diesen Schnellstart verwenden wir **Contoso-vault2**. 
    - **Abonnement**: Wählen Sie ein Abonnement aus.
    - Klicken Sie unter **Ressourcengruppe** auf **Neu erstellen**, und geben Sie einen Ressourcengruppennamen ein.
    - Wählen Sie im Pulldownmenü **Speicherort** einen Speicherort aus.
    - Behalten Sie bei den anderen Optionen die Standardeinstellungen bei.
6. Klicken Sie nach der Angabe der obigen Informationen auf **Erstellen**.

Beachten Sie die beiden folgenden Eigenschaften:

* **Tresorname**: In diesem Beispiel wird **Contoso-Vault2** verwendet. Dieser Name wird noch für andere Schritte benötigt.
* **Tresor-URI**: In diesem Beispiel https://contoso-vault2.vault.azure.net/. Anwendungen, die Ihren Tresor über die zugehörige REST-API nutzen, müssen diesen URI verwenden.

An diesem Punkt ist nur Ihr Azure-Konto zum Ausführen von Vorgängen für den neuen Tresor autorisiert.

![Ausgabe nach Erstellung der Key Vault-Instanz](../media/quick-create-portal/vault-properties.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Andere Schnellstartanleitungen und Tutorials für Key Vault bauen auf dieser Schnellstartanleitung auf. Falls Sie mit weiteren Schnellstartanleitungen und Tutorials fortfahren möchten, sollten Sie die Ressourcen nicht bereinigen.
Wenn Sie die Ressourcen nicht mehr benötigen, löschen Sie die Ressourcengruppe. Dadurch werden die Key Vault-Instanz und die dazugehörigen Ressourcen gelöscht. So löschen Sie die Ressourcengruppe über das Portal:

1. Geben Sie den Namen Ihrer Ressourcengruppe in das Suchfeld am oberen Rand des Portals ein. Klicken Sie in den Suchergebnissen auf die Ressourcengruppe aus dieser Schnellstartanleitung.
2. Wählen Sie die Option **Ressourcengruppe löschen**.
3. Geben Sie im Feld **GEBEN SIE DEN RESSOURCENGRUPPENNAMEN EIN:** den Namen der Ressourcengruppe ein, und klicken Sie anschließend auf **Löschen**.


## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie eine Key Vault-Instanz über das Azure-Portal erstellt. Weitere Informationen zu Key Vault und zur Integration in Ihre Anwendungen finden Sie in den folgenden Artikeln:

- [Was ist der Azure-Schlüsseltresor?](overview.md)
- [Entwicklerhandbuch zu Azure-Schlüsseltresor](developers-guide.md)
- [Bewährte Methoden zum Verwenden von Key Vault](best-practices.md)
