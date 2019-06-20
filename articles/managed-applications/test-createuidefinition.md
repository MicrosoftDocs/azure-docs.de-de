---
title: Testen der Benutzeroberflächendefinition für Azure Managed Applications | Microsoft-Dokumentation
description: Hier wird beschrieben, wie Sie die Benutzeroberfläche zum Erstellen Ihrer Azure Managed Applications-Instanz über das Portal testen.
author: tfitzmac
ms.service: managed-applications
ms.topic: conceptual
ms.date: 05/26/2019
ms.author: tomfitz
ms.openlocfilehash: 99ca319910be2cb20214172826eb40361abe72f0
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/28/2019
ms.locfileid: "66257602"
---
# <a name="test-your-portal-interface-for-azure-managed-applications"></a>Testen Ihrer Portalschnittstelle für Azure Managed Applications

Nachdem Sie [die Datei „createUiDefinition.json“](create-uidefinition-overview.md) für Ihre verwaltete Anwendung erstellt haben, müssen Sie die Benutzeroberfläche testen. Um das Testen zu vereinfachen, verwenden Sie eine Sandbox-Umgebung, die Ihre Datei in das Portal lädt. Sie müssen Ihre verwaltete Anwendung nicht tatsächlich bereitstellen. Die Sandbox bietet Ihre Benutzeroberfläche in der aktuellen, Vollbildportalerfahrung dar. Alternativ können Sie ein PowerShell-Skript zum Testen der Schnittstelle verwenden, aber dieses verwendet eine Legacyansicht des Portals. In diesem Artikel werden beide Ansätze beschrieben. Die Sandbox ist die empfohlene Methode, um eine Vorschau der Schnittstelle anzuzeigen.

## <a name="prerequisites"></a>Voraussetzungen

* Die Datei **createUiDefinition.json**. Falls Sie nicht über diese Datei verfügen, kopieren Sie die [Beispieldatei](https://github.com/Azure/azure-quickstart-templates/blob/master/100-marketplace-sample/createUiDefinition.json).

* Ein Azure-Abonnement. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="use-sandbox"></a>Verwenden der Sandbox

1. Öffnen Sie die [Sandboc für das Erstellen von Benutzeroberflächendefinitionen](https://portal.azure.com/?feature.customPortal=false&#blade/Microsoft_Azure_CreateUIDef/SandboxBlade).

   ![Anzeigen der Sandbox](./media/test-createuidefinition/show-sandbox.png)

1. Ersetzen Sie die leere Definition durch den Inhalt Ihrer Datei „createUiDefinition.json“. Wählen Sie **Vorschau** aus.

   ![Auswählen von „Vorschau“](./media/test-createuidefinition/select-preview.png)

1. Das von Ihnen erstellte Formular wird angezeigt. Sie können die Benutzererfahrung schrittweise durchlaufen und die Werte eingeben.

   ![Anzeigen des Formulars](./media/test-createuidefinition/show-ui-form.png)

### <a name="troubleshooting"></a>Problembehandlung

Wenn Ihr Formular nach Auswahl von **Vorschau** nicht angezeigt wird, liegt vielleicht ein Syntaxfehler vor. Suchen Sie nach dem rote Kennzeichen in der rechten Scrollleiste, und navigieren Sie zu ihm.

![Anzeigen eines Syntaxfehlers](./media/test-createuidefinition/show-syntax-error.png)

Wenn Ihr Formular nicht angezeigt und stattdessen das Symbol einer Wolke mit Tränen angezeigt wird, enthält Ihr Formular einen Fehler, z. B. eine fehlende Eigenschaft. Öffnen Sie die Web Developer Tools in Ihrem Browser. In der **Konsole** werden wichtige Meldungen zur Benutzeroberfläche angezeigt.

![Anzeigen des Fehlers](./media/test-createuidefinition/show-error.png)

## <a name="use-test-script"></a>Verwenden des Testskripts

Kopieren Sie zum Testen der Oberfläche im Portal eins der folgenden Skripts auf den lokalen Computer:

* [PowerShell-Skript zum Querladen](https://github.com/Azure/azure-quickstart-templates/blob/master/SideLoad-CreateUIDefinition.ps1)
* [Azure CLI-Skript zum Querladen](https://github.com/Azure/azure-quickstart-templates/blob/master/sideload-createuidef.sh)

Führen Sie zum Anzeigen Ihrer Benutzeroberflächendatei im Portal das heruntergeladene Skript aus. Mit dem Skript wird ein Speicherkonto in Ihrem Azure-Abonnement erstellt und anschließend die Datei „createUiDefinition.json“ in das Speicherkonto hochgeladen. Das Speicherkonto wird erstellt, wenn das Skript erstmals ausgeführt wird oder das Speicherkonto gelöscht wurde. Wenn das Speicherkonto in Ihrem Azure-Abonnement bereits vorhanden ist, wird das Skript erneut verwendet. Anschließend wird das Portal durch das Skript geöffnet und die Datei aus dem Speicherkonto heruntergeladen.

Geben Sie einen Ort für das Speicherkonto und den Ordner mit der Datei „createUiDefinition.json“ an.

Verwenden Sie für PowerShell Folgendes:

```powershell
.\SideLoad-CreateUIDefinition.ps1 `
  -StorageResourceGroupLocation southcentralus `
  -ArtifactsStagingDirectory .\100-Marketplace-Sample
```

Verwenden Sie für die Azure-Befehlszeilenschnittstelle den folgenden Befehl:

```azurecli
./sideload-createuidef.sh \
  -l southcentralus \
  -a .\100-Marketplace-Sample
```

Wenn sich Ihre createUiDefinition.json-Datei im selben Ordner wie das Skript befindet, und Sie bereits das Speicherkonto erstellt haben, müssen Sie diese Parameter nicht angeben.

Verwenden Sie für PowerShell Folgendes:

```powershell
.\SideLoad-CreateUIDefinition.ps1
```

Verwenden Sie für die Azure-Befehlszeilenschnittstelle den folgenden Befehl:

```azurecli
./sideload-createuidef.sh
```

Das Skript öffnet eine neue Registerkarte im Browser. Das Portal mit der Schnittstelle zum Erstellen der verwalteten Anwendung wird angezeigt.

![Anzeigen des Portals](./media/test-createuidefinition/view-portal.png)

Geben Sie Werte für die Felder an. Anschließend werden die Werte angezeigt, die an die Vorlage übergeben werden.

![Anzeigen der Werte](./media/test-createuidefinition/show-json.png)

Sie können diese Werte als Parameterdatei für das Testen Ihrer Bereitstellungsvorlage verwenden.

Wenn das Portal bei der Zusammenfassungsseite hängen bleibt, kann ein Fehler im Ausgabeabschnitt vorliegen. Beispielsweise haben wie möglicherweise auf ein nicht vorhandenes Steuerelement verwiesen. Wenn ein Parameter in der Ausgabe leer ist, verweist er möglicherweise auf eine nicht vorhandene Eigenschaft. Beispielsweise ist der Verweis auf das Steuerelement gültig, aber der Eigenschaftenverweis ist es nicht.

## <a name="test-your-solution-files"></a>Testen Ihrer Lösungsdateien

Sie haben überprüft, ob die Portalbenutzeroberfläche wie erwartet funktioniert. Vergewissern Sie sich nun, dass die Datei „createUiDefinition“ ordnungsgemäß in die Datei „mainTemplate.json“ integriert wurde. Sie können einen Überprüfungsskripttest ausführen, um den Inhalt der Lösungsdateien, einschließlich der Datei „createUiDefinition“, zu testen. Das Skript überprüft die JSON-Syntax, sucht in den Textfeldern nach regulären Ausdrücken und stellt sicher, dass die Ausgabewerte der Portalbenutzeroberfläche mit den Parametern der Vorlage übereinstimmen. Informationen zum Ausführen dieses Skripts finden Sie unter [Run static validation checks for templates](https://github.com/Azure/azure-quickstart-templates/tree/master/test/template-validation-tests) (Ausführen von statischen Überprüfungen für Vorlagen).

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie die Portalbenutzeroberfläche überprüft haben, lesen Sie mehr darüber, wie Sie Ihre [verwaltete Azure-Anwendung im Marketplace verfügbar machen](publish-marketplace-app.md).
