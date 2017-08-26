# `act-photo`

До некоторого момента -- единая кодовая база. Сейчас -- кодовая база одноименного проекта.

Содержит:

1. Код регулятора освещенности под микроконтроллер `ATmega8a` (среда разработки -- `IAR Embedded Workbench for AVR`)
1. Схемы (`Micro-Cap 9`) и разводка (`Sprint Layout 5`) платы
1. Утилита просмотра данных, отладки и настройки программы под микроконтроллер (`Visual Studio`, тулчейн `v120_xp`, совместима с `Windows XPI SP3`)
1. Отчет (`MS Word`)

Зависит от `act-common`.

В основном кодовая база не привносит ничего нового/ценного для обсуждения и (пере)использования. Хотя, код регулятора содержит реализацию ПИД-алгоритма (довольно корявую) в целых числах, а также не менее корявый цикл обработки сообщений.