---
author: manish-shukla01
ms.author: manshuk
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 08-03-2018
ms.openlocfilehash: 41216fe12e10f72f76043f1a8bc361b538259ac1
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2018
ms.locfileid: "39721105"
---
# <a name="virtual-machine-size-flexibility-with-reserved-vm-instances"></a>Гибкость размеров виртуальных машин при использовании зарезервированных экземпляров виртуальных машин

При использовании зарезервированного экземпляра виртуальной машины, оптимизированного для гибкости размера экземпляра, резервирование, которое приобретается, может применяться к размерам виртуальных машин той же группы серий размеров. Например, если приобретается резервирование для размера виртуальной машины, указанного в таблице серии DSv2, такого как Standard_DS5_v2, скидка на резервирование может применяться к остальным четырем размерам, указанным в этой же таблице:

- Standard_DS1_v2
- Standard_DS2_v2
- Standard_DS3_v2
- Standard_DS4_v2

Но эта скидка не применяется к размерам виртуальных машин, которые перечислены в разных таблицах, например в таблице верхней памяти серии DSv2: Standard_DS11_v2, Standard_DS12_v2 и т. д.

В группе серий размеров количество виртуальных машин, на которые распространяется скидка на резервирование, зависит от размера виртуальной машины, которая выбирается, когда приобретается резервирование. Также оно зависит от размеров используемых виртуальных машин. В столбце коэффициента, который имеется в следующих таблицах, показан результат сравнения относительной нагрузки для каждого размера виртуальной машины этой группы. Используйте значение коэффициента, чтобы вычислить, как скидка на резервирование применяется к используемым виртуальным машинам.

## <a name="examples"></a>Примеры

В следующих примерах используются размеры и коэффициенты таблицы для серии DSv2.

 Вы можете приобрести зарезервированный экземпляр виртуальной машины размера Standard_DS4_v2, в котором коэффициент или относительная нагрузка, в сравнении с другими размерами этого ряда, составляет 8.

- Сценарий 1. Запуск восьми виртуальных машин размера Standard_DS1_v2 с коэффициентом 1. Скидка на резервирование применяется ко всем восьми виртуальным машинам.
- Сценарий 2. Запуск двух виртуальных машин размера Standard_DS2_v2 р с коэффициентом 2 для каждой. Также запускается виртуальная машина размера Standard_DS3_v2 с коэффициентом 4. Общая нагрузка составляет 2+2+4=8. Таким образом, скидка на резервирование применяется ко всем трем виртуальным машинам.
- Сценарий 3. Запуск виртуальной машины размера Standard_DS5_v2 с коэффициентом 16. Скидка на резервирование применяется к половине стоимости вычислений виртуальной машины.

В следующих разделах показано, какие размеры находятся в той же группе серий размеров при покупке зарезервированного экземпляра виртуальной машины, оптимизированного для гибкости размера экземпляра.

## <a name="b-series"></a>Серия B

| Размер | Коэффициент|
|---|---|
| Standard_B1s | 1 |
|Standard_B2s|4.|

Дополнительные сведения см. в статье [Размеры виртуальных машин Azure серии B с накапливаемыми ресурсами](../articles/virtual-machines/windows/b-series-burstable.md).

## <a name="b-series-high-memory"></a>Серия B с максимальной памятью

| Размер | Коэффициент|
|---|---|
| Standard_B1ms |1|
|Standard_B2ms|4.|
|Standard_B4ms|8|
|Standard_B8ms|16|

Дополнительные сведения см. в статье [Размеры виртуальных машин Azure серии B с накапливаемыми ресурсами](../articles/virtual-machines/windows/b-series-burstable.md).

## <a name="d-series"></a>Серия D

| Размер | Коэффициент|
|---|---|
| Standard_D1|1|
|Standard_D2|2|
|Standard_D3|4.|
|Standard_D4|8|

Дополнительные сведения см. в статье [Размеры виртуальных машин предыдущих поколений](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="d-series-high-memory"></a>Серия D с максимальной памятью

| Размер | Коэффициент|
|---|---|
| Standard_D11|1|
|Standard_D12|2|
|Standard_D13|4.|
|Standard_D14|8|

Дополнительные сведения см. в статье [Размеры виртуальных машин предыдущих поколений](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="ds-series"></a>Серия Ds

| Размер | Коэффициент|
|---|---|
|Standard_DS1|1|
|Standard_DS2|2|
|Standard_DS3|4.|
|Standard_DS4|8|

Дополнительные сведения см. в статье [Размеры виртуальных машин предыдущих поколений](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="ds-series-high-memory"></a>Серия Ds с максимальной памятью

| Размер | Коэффициент|
|---|---|
|Standard_DS11|1|
|Standard_DS12|2|
|Standard_DS13|4.|
|Standard_DS14|8|

Дополнительные сведения см. в статье [Размеры виртуальных машин предыдущих поколений](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="dsv2-series"></a>Серия DSv2

| Размер | Коэффициент|
|---|---|
|Standard_DS1_v2|1|
|Standard_DS2_v2|2|
|Standard_DS3_v2|4.|
|Standard_DS4_v2|8|
|Standard_DS5_v2|16|

Дополнительные сведения см. в статье [Размеры виртуальных машин общего назначения](../articles/virtual-machines/windows/sizes-general.md#dv2-series).

## <a name="dsv2-series-high-memory"></a>Серия DSv2 с максимальной памятью

| Размер | Коэффициент|
|---|---|
|Standard_DS11_v2|1|
|Standard_DS12_v2|2|
|Standard_DS13_v2|4.|
|Standard_DS14_v2|8|
|Standard_DS15_v2|10|

Дополнительные сведения см. в статье [Размеры виртуальных машин, оптимизированных для операций в памяти](../articles/virtual-machines/windows/sizes-memory.md#dsv2-series-11-15).

## <a name="dsv3-series"></a>Серия DSv3

| Размер | Коэффициент|
|---|---|
|Standard_D2s_v3|1|
|Standard_D4s_v3|2|
|Standard_D8s_v3|4.|
|Standard_D16s_v3|8|
|Standard_D32s_v3|16|
|Standard_D64s_v3|32|

Дополнительные сведения см. в статье [Размеры виртуальных машин общего назначения](../articles/virtual-machines/windows/sizes-general.md#dsv3-series-sup1sup).

## <a name="dv2-series"></a>Серия Dv2

| Размер | Коэффициент|
|---|---|
|Standard_D1_v2|1|
|Standard_D2_v2|2|
|Standard_D3_v2|4.|
|Standard_D4_v2|8|
|Standard_D5_v2|16|

Дополнительные сведения см. в статье [Размеры виртуальных машин общего назначения](../articles/virtual-machines/windows/sizes-general.md#dv2-series).

## <a name="dv2-series-high-memory"></a>Серия Dv2 с максимальной памятью

| Размер | Коэффициент|
|---|---|
|Standard_D11_v2|1|
|Standard_D12_v2|2|
|Standard_D13_v2|4.|
|Standard_D14_v2|8|
|Standard_D15_v2|10|

Дополнительные сведения см. в статье [Размеры виртуальных машин, оптимизированных для операций в памяти](../articles/virtual-machines/windows/sizes-memory.md#dv2-series-11-15).

## <a name="dv3-series"></a>Серия Dv3

| Размер | Коэффициент|
|---|---|
| Standard_D2_v3|1|
|Standard_D4_v3|2|
|Standard_D8_v3|4.|
|Standard_D16_v3|8|
|Standard_D32_v3|16|
|Standard_D64_v3|32|

Дополнительные сведения см. в статье [Размеры виртуальных машин общего назначения](../articles/virtual-machines/windows/sizes-general.md#dv3-series-sup1sup).

## <a name="esv3-series"></a>Серия ESv3

| Размер | Коэффициент|
|---|---|
| Standard_E2s_v3|1|
|Standard_E4s_v3|2|
|Standard_E8s_v3|4.|
|Standard_E16s_v3|8|
|Standard_E32s_v3|16|
|Standard_E64s_v3|32|

Дополнительные сведения см. в статье [Размеры виртуальных машин, оптимизированных для операций в памяти](../articles/virtual-machines/windows/sizes-memory.md#esv3-series).

## <a name="ev3-series"></a>Серия Ev3

| Размер | Коэффициент|
|---|---|
| Standard_E2_v3|1|
|Standard_E4_v3|2|
|Standard_E8_v3|4.|
|Standard_E16_v3|8|
|Standard_E32_v3|16|
|Standard_E64_v3|32|

Дополнительные сведения см. в статье [Размеры виртуальных машин, оптимизированных для операций в памяти](../articles/virtual-machines/windows/sizes-memory.md#ev3-series).

## <a name="f-series"></a>Серия F

| Размер | Коэффициент|
|---|---|
| Standard_F1|1|
|Standard_F2|2|
|Standard_F4|4.|
|Standard_F8|8|
Standard_F16|16|

Дополнительные сведения см. в статье [Размеры виртуальных машин, оптимизированных для вычислений](../articles/virtual-machines/windows/sizes-compute.md#f-series).

## <a name="fs-series"></a>Серия FS

| Размер | Коэффициент|
|---|---|
| Standard_F1s|1|
|Standard_F2s|2|
|Standard_F4s|4.|
|Standard_F8s|8|
|Standard_F16s|16|

Дополнительные сведения см. в статье [Размеры виртуальных машин, оптимизированных для вычислений](../articles/virtual-machines/windows/sizes-compute.md#fs-series-sup1sup).

## <a name="fsv2-series"></a>Серия Fsv2

| Размер | Коэффициент|
|---|---|
|Standard_F2s_v2|1|
|Standard_F4s_v2|2|
|Standard_F8s_v2|4.|
|Standard_F16s_v2|8|
|Standard_F32s_v2|16|
|Standard_F64s_v2|32|
|Standard_F72s_v2|36|

Дополнительные сведения см. в статье [Размеры виртуальных машин, оптимизированных для вычислений](../articles/virtual-machines/windows/sizes-compute.md#fsv2-series-sup1sup).

## <a name="h-series"></a>Серия H

| Размер | Коэффициент|
|---|---|
| Standard_H8|1|
|Standard_H16|2|

Дополнительные сведения см. в статье [Размеры виртуальных машин Linux, оптимизированных для HPC](../articles/virtual-machines/windows/sizes-hpc.md).

## <a name="h-series-high-memory"></a>Серия H с максимальной памятью

| Размер | Коэффициент|
|---|---|
| Standard_H8m|1|
|Standard_H16m|2|

Дополнительные сведения см. в статье [Размеры виртуальных машин Linux, оптимизированных для HPC](../articles/virtual-machines/windows/sizes-hpc.md).

## <a name="ls-series"></a>Серия Ls

| Размер | Коэффициент|
|---|---|
| Standard_L4s|1|
|Standard_L8s|2|
|Standard_L16s|4.|
|Standard_L32s|8|

Дополнительные сведения см. в статье [Размеры виртуальных машин, оптимизированных для операций в хранилище](../articles/virtual-machines/windows/sizes-storage.md).

## <a name="m-series"></a>Серия M

| Размер | Коэффициент|
|---|---|
| Standard_M64s|1|
|Standard_M128s|2|

Дополнительные сведения см. в статье [Размеры виртуальных машин, оптимизированных для операций в памяти](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="m-series-fractional"></a>Дробная серия M

| Размер | Коэффициент|
|---|---|
| Standard_M16s|1|
|Standard_M32s|2|

Дополнительные сведения см. в статье [Размеры виртуальных машин, оптимизированных для операций в памяти](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="m-series-fractional-high-memory"></a>Дробная серия M с максимальной памятью

| Размер | Коэффициент|
|---|---|
| Standard_M8ms|1|
|Standard_M16ms|2|
|Standard_M32ms|4.|

Дополнительные сведения см. в статье [Размеры виртуальных машин, оптимизированных для операций в памяти](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="m-series-fractional-large"></a>Долгосрочная дробная серия M

| Размер | Коэффициент|
|---|---|
| Standard_M32ls|1|
|Standard_M64ls|2|

Дополнительные сведения см. в статье [Размеры виртуальных машин, оптимизированных для операций в памяти](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="m-series-high-memory"></a>Серия M с максимальной памятью

| Размер | Коэффициент|
|---|---|
| Standard_M64ms|1|
|Standard_M128ms|2|

Дополнительные сведения см. в статье [Размеры виртуальных машин, оптимизированных для операций в памяти](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="nc-series"></a>Серия NC

| Размер | Коэффициент|
|---|---|
| Standard_NC6|1|
|Standard_NC12|2|
|Standard_NC24|4.|

Дополнительные сведения см. в статье [Размеры виртуальных машин, оптимизированных для GPU](../articles/virtual-machines/windows/sizes-gpu.md).

## <a name="ncv2-series"></a>Серия NCv2

| Размер | Коэффициент|
|---|---|
| Standard_NC6s_v2|1|
|Standard_NC12s_v2|2|
|Standard_NC24s_v2|4.|

Дополнительные сведения см. в статье [Размеры виртуальных машин, оптимизированных для GPU](../articles/virtual-machines/windows//sizes-gpu.md#ncv2-series).

## <a name="ncv3-series"></a>Серия NCv3

| Размер | Коэффициент|
|---|---|
| Standard_NC6s_v3|1|
|Standard_NC12s_v3|2|
|Standard_NC24s_v3|4.|

Дополнительные сведения см. в статье [Размеры виртуальных машин, оптимизированных для GPU](../articles/virtual-machines/windows//sizes-gpu.md#ncv3-series).

## <a name="nd-series"></a>Серия ND

| Размер | Коэффициент|
|---|---|
| Standard_ND6s|1|
|Standard_ND12s|2|
|Standard_ND24s|4.|

Дополнительные сведения см. в статье [Размеры виртуальных машин, оптимизированных для GPU](../articles/virtual-machines/windows//sizes-gpu.md#nd-series).

## <a name="nv-series"></a>Серия NV

| Размер | Коэффициент|
|---|---|
| Standard_NV6|1|
|Standard_NV12|2|
|Standard_NV24|4.|

Дополнительные сведения см. в статье [Размеры виртуальных машин, оптимизированных для GPU](../articles/virtual-machines/windows//sizes-gpu.md#nv-series).


