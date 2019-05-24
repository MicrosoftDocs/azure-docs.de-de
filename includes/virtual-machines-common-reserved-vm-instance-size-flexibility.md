---
author: yashar
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 11-20-2018
ms.openlocfilehash: 05820cc5f7b7d61d83f73ea5b62b05f8712e0997
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66156220"
---
# <a name="virtual-machine-size-flexibility-with-reserved-vm-instances"></a>Flexibilität bei der VM-Größe mit reservierten VM-Instanzen

Bei einer für Flexibilität bei der Instanzgröße optimierten reservierten VM-Instanz kann die von Ihnen gekaufte Reservierung für die VM-Größen in derselben Gruppe der Größenserie gelten. Wenn Sie beispielsweise eine Reservierung für eine VM-Größe kaufen, die in der Tabelle der DSv2-Serie aufgeführt ist (z.B. Standard_DS5_v2), kann der Reservierungsrabatt für die anderen vier Größen in dieser Tabelle gelten:

- Standard_DS1_v2
- Standard_DS2_v2
- Standard_DS3_v2
- Standard_DS4_v2

Dieser Reservierungsrabatt gilt jedoch nicht für VM-Größen in anderen Tabellen, beispielsweise die in der Tabelle „DSv2-Serie, oberer Speicher“ aufgeführten Größen: Standard_DS11_v2, Standard_DS12_v2 usw.

Innerhalb der Gruppe der Größenserie hängt die Anzahl von VMs, für die der Reservierungsrabatt gilt, von der VM-Größe ab, die Sie beim Kauf einer Reservierung auswählen. Sie ist auch von den Größen der VMs abhängig, die Sie ausführen. Die Spalte „Verhältnis“ in den folgenden Tabellen stellt einen Vergleich des relativen Speicherbedarfs der einzelnen VM-Größen in der jeweiligen Gruppe dar. Anhand des Verhältniswerts können Sie berechnen, inwieweit der Reservierungsrabatt für die von Ihnen ausgeführten VMs gilt.

## <a name="examples"></a>Beispiele

In den folgenden Beispielen werden die Größen und Verhältnisse in der Tabelle der DSv2-Serie verwendet.

 Sie kaufen eine reservierte VM-Instanz der Größe Standard_DS4_v2, für die das Verhältnis bzw. der relative Speicherbedarf im Vergleich zu den anderen Größen in der Serie 8 ist.

- Szenario 1: Sie führen acht VMs der Größe Standard_DS1_v2 mit einem Verhältnis von 1 aus. Ihr Reservierungsrabatt gilt für alle acht VMs.
- Szenario 2: Sie führen zwei VMs der Größe Standard_DS2_v2 mit einem Verhältnis von jeweils 2 aus. Außerdem führen Sie eine VM der Größe Standard_DS3_v2 mit einem Verhältnis von 4 aus. Der gesamte Speicherbedarf ist 2 + 2 + 4 = 8. Ihr Reservierungsrabatt gilt somit für alle drei VMs.
- Szenario 3: Sie führen eine VM der Größe Standard_DS5_v2 mit einem Verhältnis von 16 aus. Ihr Reservierungsrabatt gilt für die Hälfte der Computekosten dieser VM.

Den folgenden Abschnitten können Sie entnehmen, welche Größen in derselben Gruppe einer Größenserie enthalten sind, wenn Sie eine für Flexibilität bei der Instanzgröße optimierte reservierte VM-Instanz kaufen.

## <a name="b-series"></a>B-Serie

| Größe | Verhältnis|
|---|---|
| Standard_B1s | 1 |
|Standard_B2s|4|

Weitere Informationen finden Sie unter [Größen von Burstable-VMs der B-Serie](../articles/virtual-machines/windows/b-series-burstable.md).

## <a name="b-series-high-memory"></a>B-Serie, oberer Speicher

| Größe | Verhältnis|
|---|---|
| Standard_B1ms |1|
|Standard_B2ms|4|
|Standard_B4ms|8|
|Standard_B8ms|16|

Weitere Informationen finden Sie unter [Größen von Burstable-VMs der B-Serie](../articles/virtual-machines/windows/b-series-burstable.md).

## <a name="d-series"></a>D-Serie

| Größe | Verhältnis|
|---|---|
| Standard_D1|1|
|Standard_D2|2|
|Standard_D3|4|
|Standard_D4|8|

Weitere Informationen finden Sie unter [Vorherige Generationen von VM-Größen](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="d-series-high-memory"></a>D-Serie, oberer Speicher

| Größe | Verhältnis|
|---|---|
| Standard_D11|1|
|Standard_D12|2|
|Standard_D13|4|
|Standard_D14|8|

Weitere Informationen finden Sie unter [Vorherige Generationen von VM-Größen](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="ds-series"></a>Ds-Serie

| Größe | Verhältnis|
|---|---|
|Standard_DS1|1|
|Standard_DS2|2|
|Standard_DS3|4|
|Standard_DS4|8|

Weitere Informationen finden Sie unter [Vorherige Generationen von VM-Größen](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="ds-series-high-memory"></a>Ds-Serie, oberer Speicher

| Größe | Verhältnis|
|---|---|
|Standard_DS11|1|
|Standard_DS12|2|
|Standard_DS13|4|
|Standard_DS14|8|

Weitere Informationen finden Sie unter [Vorherige Generationen von VM-Größen](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="dsv2-series"></a>DSv2-Serie

| Größe | Verhältnis|
|---|---|
|Standard_DS1_v2|1|
|Standard_DS2_v2|2|
|Standard_DS3_v2|4|
|Standard_DS4_v2|8|
|Standard_DS5_v2|16|

Weitere Informationen finden Sie unter [Vorherige Generationen von VM-Größen](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="dsv2-series-high-memory"></a>DSv2-Serie, oberer Speicher

| Größe | Verhältnis|
|---|---|
|Standard_DS11_v2|1|
|Standard_DS11-1_v2|1|
|Standard_DS12_v2|2|
|Standard_DS12-1_v2|2|
|Standard_DS12-2_v2|2|
|Standard_DS13_v2|4|
|Standard_DS13-2_v2|4|
|Standard_DS13-4_v2|4|
|Standard_DS14_v2|8|
|Standard_DS14-4_v2|8|
|Standard_DS14-8_v2|8|
|Standard_DS15_v2|10|

Weitere Informationen finden Sie unter [Vorherige Generationen von VM-Größen](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="dsv3-series"></a>DSv3-Serie

| Größe | Verhältnis|
|---|---|
|Standard_D2s_v3|1|
|Standard_D4s_v3|2|
|Standard_D8s_v3|4|
|Standard_D16s_v3|8|
|Standard_D32s_v3|16|
|Standard_D64s_v3|32|

Weitere Informationen finden Sie unter [Universelle VM-Größen](../articles/virtual-machines/windows/sizes-general.md#dsv3-series-1).

## <a name="dv2-series"></a>Dv2-Serie

| Größe | Verhältnis|
|---|---|
|Standard_D1_v2|1|
|Standard_D2_v2|2|
|Standard_D3_v2|4|
|Standard_D4_v2|8|
|Standard_D5_v2|16|

Weitere Informationen finden Sie unter [Vorherige Generationen von VM-Größen](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="dv2-series-high-memory"></a>Dv2-Serie, oberer Speicher

| Größe | Verhältnis|
|---|---|
|Standard_D11_v2|1|
|Standard_D12_v2|2|
|Standard_D13_v2|4|
|Standard_D14_v2|8|
|Standard_D15_v2|10|

Weitere Informationen finden Sie unter [Vorherige Generationen von VM-Größen](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="dv3-series"></a>Dv3-Serie

| Größe | Verhältnis|
|---|---|
| Standard_D2_v3|1|
|Standard_D4_v3|2|
|Standard_D8_v3|4|
|Standard_D16_v3|8|
|Standard_D32_v3|16|
|Standard_D64_v3|32|

Weitere Informationen finden Sie unter [Universelle VM-Größen](../articles/virtual-machines/windows/sizes-general.md#dv3-series-1).

## <a name="esv3-series"></a>ESv3-Serie

| Größe | Verhältnis|
|---|---|
|Standard_E2s_v3|1|
|Standard_E4s_v3|2|
|Standard_E4-2s_v3|2|
|Standard_E8s_v3|4|
|Standard_E8-2s_v3|4|
|Standard_E8-4s_v3|4|
|Standard_E16s_v3|8|
|Standard_E16-4s_v3|8|
|Standard_E16-8s_v3|8|
|Standard_E20s_v3|10|
|Standard_E32s_v3|16|
|Standard_E32-8s_v3|16|
|Standard_E32-16s_v3|16|
|Standard_E64s_v3|28.8|
|Standard_E64-16s_v3|28.8|
|Standard_E64-32s_v3|28.8|

Weitere Informationen finden Sie unter [Arbeitsspeicheroptimierte VM-Größen](../articles/virtual-machines/windows/sizes-memory.md#esv3-series).

## <a name="ev3-series"></a>Ev3-Serie

| Größe | Verhältnis|
|---|---|
| Standard_E2_v3|1|
|Standard_E4_v3|2|
|Standard_E8_v3|4|
|Standard_E16_v3|8|
|Standard_E20_v3|10|
|Standard_E32_v3|16|
|Standard_E64_v3|32|

Weitere Informationen finden Sie unter [Arbeitsspeicheroptimierte VM-Größen](../articles/virtual-machines/windows/sizes-memory.md#ev3-series).

## <a name="f-series"></a>F-Serie

| Größe | Verhältnis|
|---|---|
| Standard_F1|1|
|Standard_F2|2|
|Standard_F4|4|
|Standard_F8|8|
Standard_F16|16|

Weitere Informationen finden Sie unter [Vorherige Generationen von VM-Größen](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="fs-series"></a>FS-Serie

| Größe | Verhältnis|
|---|---|
| Standard_F1s|1|
|Standard_F2s|2|
|Standard_F4s|4|
|Standard_F8s|8|
|Standard_F16s|16|

Weitere Informationen finden Sie unter [Vorherige Generationen von VM-Größen](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="fsv2-series"></a>Fsv2-Serie

| Größe | Verhältnis|
|---|---|
|Standard_F2s_v2|1|
|Standard_F4s_v2|2|
|Standard_F8s_v2|4|
|Standard_F16s_v2|8|
|Standard_F32s_v2|16|
|Standard_F64s_v2|32|
|Standard_F72s_v2|36|

Weitere Informationen finden Sie unter [Für Compute optimierte VM-Größen](../articles/virtual-machines/windows/sizes-compute.md#fsv2-series-1).

## <a name="h-series"></a>H-Reihe

| Größe | Verhältnis|
|---|---|
| Standard_H8|1|
|Standard_H16|2|

Weitere Informationen finden Sie unter [Größen von virtuellen HPC-Computern (High Performance Computing)](../articles/virtual-machines/windows/sizes-hpc.md).

## <a name="h-series-high-memory"></a>H-Serie, oberer Speicher

| Größe | Verhältnis|
|---|---|
| Standard_H8m|1|
|Standard_H16m|2|

Weitere Informationen finden Sie unter [Größen von virtuellen HPC-Computern (High Performance Computing)](../articles/virtual-machines/windows/sizes-hpc.md).

## <a name="ls-series"></a>Ls-Serie

| Größe | Verhältnis|
|---|---|
| Standard_L4s|1|
|Standard_L8s|2|
|Standard_L16s|4|
|Standard_L32s|8|

Weitere Informationen finden Sie unter [Datenspeicheroptimierte VM-Größen](../articles/virtual-machines/windows/sizes-storage.md).

## <a name="m-series"></a>M-Serie

| Größe | Verhältnis|
|---|---|
| Standard_M64s|1|
|Standard_M128s|2|

Weitere Informationen finden Sie unter [Arbeitsspeicheroptimierte VM-Größen](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="m-series-fractional"></a>M-Serie, partiell

| Größe | Verhältnis|
|---|---|
| Standard_M16s|1|
|Standard_M32s|2|

Weitere Informationen finden Sie unter [Arbeitsspeicheroptimierte VM-Größen](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="m-series-fractional-high-memory"></a>M-Serie, partiell, oberer Speicher

| Größe | Verhältnis|
|---|---|
|Standard_M8ms|1|
|Standard_M8-2 ms|1|
|Standard_M8-4ms|1|
|Standard_M16ms|2|
|Standard_M16-4ms|2|
|Standard_M16-8ms|2|
|Standard_M32ms|4|
|Standard_M32-8ms|4|
|Standard_M32-16ms|4|

Weitere Informationen finden Sie unter [Arbeitsspeicheroptimierte VM-Größen](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="m-series-fractional-large"></a>M-Serie, partiell, groß

| Größe | Verhältnis|
|---|---|
| Standard_M32ls|1|
|Standard_M64ls|2|

Weitere Informationen finden Sie unter [Arbeitsspeicheroptimierte VM-Größen](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="m-series-high-memory"></a>M-Serie, oberer Speicher

| Größe | Verhältnis|
|---|---|
| Standard_M64ms|1|
|Standard_M64-16ms|1|
|Standard_M64-32ms|1|
|Standard_M128ms|2|
|Standard_M128-32ms|2|
|Standard_M128-64ms|2|

Weitere Informationen finden Sie unter [Arbeitsspeicheroptimierte VM-Größen](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="nc-series"></a>NC-Serie

| Größe | Verhältnis|
|---|---|
| Standard_NC6|1|
|Standard_NC12|2|
|Standard_NC24|4|

Weitere Informationen finden Sie unter [Für GPU optimierte VM-Größen](../articles/virtual-machines/windows/sizes-gpu.md).

## <a name="ncv2-series"></a>NCv2-Serie

| Größe | Verhältnis|
|---|---|
| Standard_NC6s_v2|1|
|Standard_NC12s_v2|2|
|Standard_NC24s_v2|4|

Weitere Informationen finden Sie unter [Für GPU optimierte VM-Größen](../articles/virtual-machines/windows//sizes-gpu.md#ncv2-series).

## <a name="ncv3-series"></a>NCv3-Serie

| Größe | Verhältnis|
|---|---|
| Standard_NC6s_v3|1|
|Standard_NC12s_v3|2|
|Standard_NC24s_v3|4|

Weitere Informationen finden Sie unter [Für GPU optimierte VM-Größen](../articles/virtual-machines/windows//sizes-gpu.md#ncv3-series).

## <a name="nd-series"></a>ND-Serie

| Größe | Verhältnis|
|---|---|
| Standard_ND6s|1|
|Standard_ND12s|2|
|Standard_ND24s|4|

Weitere Informationen finden Sie unter [Für GPU optimierte VM-Größen](../articles/virtual-machines/windows//sizes-gpu.md#nd-series).

## <a name="nv-series"></a>NV-Serie

| Größe | Verhältnis|
|---|---|
| Standard_NV6|1|
|Standard_NV12|2|
|Standard_NV24|4|

Weitere Informationen finden Sie unter [Für GPU optimierte VM-Größen](../articles/virtual-machines/windows//sizes-gpu.md#nv-series).


