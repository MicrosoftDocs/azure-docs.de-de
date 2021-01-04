---
title: Anpassen der Schaltfläche des plastischen Readers
titleSuffix: Azure Cognitive Services
description: In diesem Artikel wird gezeigt, wie Sie die Schaltfläche anpassen, mit der der plastische Reader gestartet wird.
services: cognitive-services
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: metang
ms.openlocfilehash: b24a9dcb7a369708b1374f367186dc4d9a6ce475
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/01/2020
ms.locfileid: "93146874"
---
# <a name="how-to-customize-the-immersive-reader-button"></a>Vorgehensweise: Anpassen der Schaltfläche des plastischen Readers

In diesem Artikel wird veranschaulicht, wie Sie die Schaltfläche anpassen, die den plastischen Reader startet, um die Anforderungen Ihrer Anwendung zu erfüllen.

## <a name="add-the-immersive-reader-button"></a>Hinzufügen der Plastischer Reader-Schaltfläche

Das SDK für den plastischen Reader stellt einen Standardstil für die Schaltfläche zum Starten des plastischen Readers bereit. Verwenden Sie das Klassenattribut `immersive-reader-button` zum Aktivieren dieses Stils.

```html
<div class='immersive-reader-button'></div>
```

## <a name="customize-the-button-style"></a>Anpassen des Schaltflächenstils

Verwenden Sie das `data-button-style`-Attribut, um den Stil der Schaltfläche festzulegen. Zulässige Werte sind `icon`, `text` und `iconAndText`. Standardwert: `icon`.

### <a name="icon-button"></a>Symbolschaltfläche

```html
<div class='immersive-reader-button' data-button-style='icon'></div>
```

Dies rendert Folgendes:

![Dies ist die gerenderte Textschaltfläche.](./media/button-icon.png)

### <a name="text-button"></a>Textschaltfläche

```html
<div class='immersive-reader-button' data-button-style='text'></div>
```

Dies rendert Folgendes:

![Dies ist die gerenderte Schaltfläche „Plastischer Reader“.](./media/button-text.png)

### <a name="icon-and-text-button"></a>Symbol- und Textschaltfläche

```html
<div class='immersive-reader-button' data-button-style='iconAndText'></div>
```

Dies rendert Folgendes:

![Symbolschaltfläche](./media/button-icon-and-text.png)

## <a name="customize-the-button-text"></a>Anpassen des Schaltflächentexts

Konfigurieren Sie die Sprache und den Alternativtext für die Schaltfläche mit dem `data-locale`-Attribut. Die Standardsprache ist Deutsch.

```html
<div class='immersive-reader-button' data-locale='fr-FR'></div>
```

## <a name="customize-the-size-of-the-icon"></a>Anpassen der Größe des Symbols

Die Größe des Plastischer Reader-Symbols kann mit dem `data-icon-px-size`-Attribut konfiguriert werden. Dies legt die Größe des Symbols in Pixeln fest. Die Standardgröße beträgt 20 Pixel.

```html
<div class='immersive-reader-button' data-icon-px-size='50'></div>
```

## <a name="next-steps"></a>Nächste Schritte

* Lesen Sie die [Referenz zum SDK für den plastischen Reader](./reference.md).