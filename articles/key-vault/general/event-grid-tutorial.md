---
title: Empfangen von und Reagieren auf Key Vault-Benachrichtigungen mit Azure Event Grid
description: Hier erfahren Sie, wie Sie Key Vault in Azure Event Grid integrieren.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/25/2019
ms.author: mbaldwin
ms.openlocfilehash: d7bb697879f40b45c886cd90bbb1e34906d35f66
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96187373"
---
# <a name="receive-and-respond-to-key-vault-notifications-with-azure-event-grid"></a>Empfangen von und Reagieren auf Key Vault-Benachrichtigungen mit Azure Event Grid

Die Azure Key Vault-Integration in Azure Event Grid ermöglicht die Benutzerbenachrichtigung, wenn sich der Status eines in einem Schlüsseltresor gespeicherten Geheimnisses geändert hat. Eine Übersicht über dieses Feature finden Sie unter [Überwachen von Key Vault mit Event Grid](event-grid-overview.md).

In dieser Anleitung ist beschrieben, wie Sie Key Vault-Benachrichtigungen über Event Grid erhalten und wie Sie über Azure Automation auf Statusänderungen reagieren.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Abonnement. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.
- Ein Schlüsseltresor in Ihrem Azure-Abonnement. Sie können ganz schnell einen neuen Schlüsseltresor erstellen. Eine entsprechende Anleitung finden Sie unter [Schnellstart: Festlegen eines Geheimnisses und Abrufen des Geheimnisses aus Azure Key Vault mithilfe der Azure CLI](../secrets/quick-create-cli.md).

## <a name="concepts"></a>Konzepte

Event Grid ist ein Ereignisdienst für die Cloud. Durch Ausführen der Schritte in diesem Leitfaden abonnieren Sie Ereignisse für Key Vault und leiten Sie Ereignisse an Automation weiter. Wenn eines der Geheimnisse im Schlüsseltresor in Kürze abläuft, wird Event Grid über die Statusänderung informiert und sendet eine HTTP POST-Anforderung an den Endpunkt. Daraufhin löst ein Webhook eine Automation-Ausführung eines PowerShell-Skripts aus.

![HTTP POST-Flussdiagramm](../media/event-grid-tutorial-1.png)

## <a name="create-an-automation-account"></a>Erstellen eines Automation-Kontos

Erstellen Sie ein Automation-Konto über das [Azure-Portal](https://portal.azure.com):

1.  Navigieren Sie zu „portal.azure.com“, und melden Sie sich bei Ihrem Abonnement an.

1.  Geben Sie **Automation-Konten** in das Suchfeld ein.

1.  Wählen Sie auf der Suchleiste im **Dienste**-Abschnitt des Dropdownmenüs die Option **Automation-Konten** aus.

1.  Wählen Sie **Hinzufügen**.

    ![Bereich „Automatisierungskonten“ (Automation-Konten)](../media/event-grid-tutorial-2.png)

1.  Geben Sie im Bereich **Automation-Konto hinzufügen** die erforderlichen Informationen ein, und wählen Sie anschließend **Erstellen** aus.

## <a name="create-a-runbook"></a>Erstellen eines Runbooks

Wenn Ihr Automation-Konto vorbereitet ist, erstellen Sie ein Runbook.

![Benutzeroberfläche für Erstellen eines Runbooks](../media/event-grid-tutorial-3.png)

1.  Wählen Sie das soeben erstellte Automation-Konto aus.

1.  Wählen Sie **Runbooks** unter Option **Prozessautomatisierung** aus.

1.  Wählen Sie **Runbook erstellen** aus.

1.  Benennen Sie Ihr Runbook, und wählen Sie als Runbooktyp **PowerShell** aus.

1.  Wählen Sie das von Ihnen erstellte Runbook aus, und wählen Sie dann die Schaltfläche **Bearbeiten** aus.

1.  Geben Sie (zu Testzwecken) den folgenden Code ein, und wählen Sie anschließend die Schaltfläche **Veröffentlichen** aus. Durch diese Aktion wird das Ergebnis der empfangenen POST-Anforderung zurückgegeben.

```azurepowershell
param
(
[Parameter (Mandatory = $false)]
[object] $WebhookData
)

#If runbook was called from Webhook, WebhookData will not be null.
if ($WebhookData) {

#rotate secret:
#generate new secret version in key vault
#update db/service with generated secret

#Write-Output "WebhookData <$WebhookData>"
Write-Output $WebhookData.RequestBody
}
else
{
# Error
write-Error "No input data found." 
}
```

![Benutzeroberfläche für Veröffentlichen eines Runbooks](../media/event-grid-tutorial-4.png)

## <a name="create-a-webhook"></a>Erstellen eines Webhooks

Erstellen Sie einen Webhook, um Ihr neu erstelltes Runbook auszulösen.

1.  Wählen Sie im **Ressourcen**-Abschnitt des soeben von Ihnen veröffentlichten Runbooks die Option **Webhooks** aus.

1.  Wählen Sie **Webhook hinzufügen** aus.

    ![Schaltfläche „Webhook hinzufügen“](../media/event-grid-tutorial-5.png)

1.  Wählen Sie **Neuen Webhook erstellen** aus.

1. Benennen Sie den Webhook, legen Sie ein Ablaufdatum fest, und kopieren Sie die URL.

    > [!IMPORTANT] 
    > Nach ihrer Erstellung kann die URL nicht mehr angezeigt werden. Speichern Sie sie an einem sicheren Ort, auf den Sie im weiteren Verlauf dieses Leitfadens problemlos zugreifen können.

1. Wählen Sie **Parameter und Ausführungseinstellungen** aus, und wählen Sie dann **OK** aus. Geben Sie keine Parameter ein. Dadurch wird die Schaltfläche **Erstellen** aktiviert.

1. Wählen Sie **OK** und anschließend **Erstellen** aus.

    ![Benutzeroberfläche für Erstellen eines neuen Webhooks](../media/event-grid-tutorial-6.png)

## <a name="create-an-event-grid-subscription"></a>Erstellen eines Event Grid-Abonnements

Erstellen Sie ein Event Grid-Abonnement über das [Azure-Portal](https://portal.azure.com).

1.  Navigieren Sie zu Ihrem Schlüsseltresor, und wählen Sie die Registerkarte **Ereignisse** aus.

    ![Registerkarte „Ereignisse“ im Azure-Portal](../media/event-grid-tutorial-7.png)

1.  Wählen Sie die Schaltfläche **Ereignisabonnement** aus.

1.  Erstellen Sie einen aussagekräftigen Namen für das Abonnement.

1.  Wählen Sie **Event Grid-Schema** aus.

1.  **Themenressource** muss der Schlüsseltresor sein, den Sie auf Statusänderungen überwachen möchten.

1.  Belassen Sie für **Nach Ereignistypen filtern** alle Optionen ausgewählt (**9 ausgewählt**).

1.  Wählen Sie unter **Endpunkttyp** die Option **Webhook** aus.

1.  Wählen Sie **Endpunkt auswählen** aus. Fügen Sie im neuen Kontextbereich die Webhook-URL aus dem Schritt [Erstellen eines Webhooks](#create-a-webhook) in das Feld **Abonnentenendpunkt** ein.

1.  Wählen Sie im Kontextbereich die Option **Auswahl bestätigen** aus.

1.  Klicken Sie auf **Erstellen**.

    ![Erstellen eines Ereignisabonnements](../media/event-grid-tutorial-8.png)

## <a name="test-and-verify"></a>Testen und Überprüfen

Vergewissern Sie sich, dass Ihr Event Grid-Abonnement ordnungsgemäß konfiguriert ist. Bei diesem Test wird davon ausgegangen, dass Sie im Schritt [Erstellen eines Event Grid-Abonnements](#create-an-event-grid-subscription) die Benachrichtigung für die Erstellung einer neuen Geheimnisversion (Secret New Version Created) abonniert haben und über die nötigen Berechtigungen verfügen, um in einem Schlüsseltresor eine neue Version eines Geheimnisses zu erstellen.

![Testen der Konfiguration des Event Grid-Abonnements](../media/event-grid-tutorial-9.png)

![Bereich „Geheimnis erstellen“](../media/event-grid-tutorial-10.png)

1.  Navigieren Sie im Azure-Portal zu Ihrem Schlüsseltresor.

1.  Erstellen Sie ein neues Geheimnis. Legen Sie zu Testzwecken das Ablaufdatum auf den nächsten Tag fest.

1.  Wählen Sie auf der Registerkarte **Ereignisse** in Ihrem Schlüsseltresor das Event Grid-Abonnement aus, das Sie erstellt haben.

1.  Überprüfen Sie unter **Metriken**, ob ein Ereignis erfasst wurde. Zwei Ereignisse werden erwartet: „SecretNewVersion“ und „SecretNearExpiry“. Mit diesen Ereignissen wird überprüft, ob die Statusänderung des Geheimnisses in Ihrem Schlüsseltresor von Event Grid erfolgreich erfasst wurde.

    ![Bereich „Metriken“: Überprüfen auf erfasste Ereignisse](../media/event-grid-tutorial-11.png)

1.  Navigieren Sie zu Ihrem Automation-Konto.

1.  Wählen Sie die Registerkarte **Runbooks** und anschließend das von Ihnen erstellte Runbook aus.

1.  Wählen Sie die Registerkarte **Webhooks** aus, und vergewissern Sie sich, dass der „Zuletzt ausgelöst“-Zeitstempel innerhalb von 60 Sekunden nach der Erstellung des neuen Geheimnisses liegt. Mit diesem Ergebnis wird bestätigt, dass Event Grid eine POST-Anforderung mit den Ereignisdetails der Statusänderung in Ihrem Schlüsseltresor an den Webhook gesendet hat und dass der Webhook ausgelöst wurde.

    ![Registerkarte „Webhooks“, „Zuletzt ausgelöst“-Zeitstempel](../media/event-grid-tutorial-12.png)

1. Kehren Sie zu Ihrem Runbook zurück, und wählen Sie die Registerkarte **Übersicht** aus.

1. Sehen Sie sich die Liste **Kürzlich ausgeführte Aufträge** an. Darin sollte ein erstellter und abgeschlossener Auftrag enthalten sein. Ist dies der Fall, hat der Webhook das Runbook ausgelöst, um die Skriptausführung zu starten.

    ![Webhook-Liste „Kürzlich ausgeführte Aufträge“](../media/event-grid-tutorial-13.png)

1. Wählen Sie den aktuellen Auftrag aus, und sehen Sie sich die POST-Anforderung an, die von Event Grid an den Webhook gesendet wurde. Überprüfen Sie den JSON-Code, und vergewissern Sie sich, dass die Parameter für den Schlüsseltresor und den Ereignistyp korrekt sind. Wenn der Parameter „event type“ (Ereignistyp) im JSON-Objekt dem Ereignis entspricht, das im Schlüsseltresor aufgetreten ist (in diesem Beispiel: Microsoft.KeyVault.SecretNearExpiry), war der Test erfolgreich.

## <a name="troubleshooting"></a>Problembehandlung

### <a name="you-cant-create-an-event-subscription"></a>Sie können kein Ereignisabonnement erstellen.

Wiederholen Sie die Registrierung von Event Grid und des Key Vault-Anbieters in den Ressourcenanbietern Ihres Azure-Abonnements. Weitere Informationen finden Sie unter [Azure-Ressourcenanbieter und -typen](../../azure-resource-manager/management/resource-providers-and-types.md).

## <a name="next-steps"></a>Nächste Schritte

Glückwunsch! Wenn Sie alle aufgeführten Schritte ordnungsgemäß ausgeführt haben, können Sie nun programmgesteuert auf Statusänderungen von Geheimnissen reagieren, die in Ihrem Schlüsseltresor gespeichert sind.

Falls Sie bislang ein abrufbasiertes System verwendet haben, um nach Statusänderungen von Geheimnissen in Ihren Schlüsseltresors zu suchen, können Sie nun beginnen, dieses Benachrichtigungsfeature zu verwenden. Sie können auch das Testskript in Ihrem Runbook durch Code ersetzen, um Ihre in Kürze ablaufenden Geheimnisse programmgesteuert zu erneuern.

Weitere Informationen:


- Übersicht: [Überwachen von Key Vault mit Azure Event Grid](event-grid-overview.md)
- Gewusst wie: [Verwenden von Logic Apps zum Empfangen einer E-Mail bei Statusänderungen von Key Vault-Geheimnissen](event-grid-logicapps.md)
- [Azure Event Grid-Ereignisschema für Azure Key Vault](../../event-grid/event-schema-key-vault.md)
- [Was ist der Azure-Schlüsseltresor?](overview.md)
- [Was ist Azure Event Grid?](../../event-grid/overview.md)
- [Azure Automation – Übersicht](../../automation/index.yml)
