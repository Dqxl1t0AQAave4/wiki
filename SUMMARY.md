# Содержание

* [Введение](README.md)

## Кодовая база

* [`act-common`](codebase/act-common.md)
    * [`src-logger`](codebase/act-common.md#act-common-src-logger)
    * [`src-com-port`](codebase/act-common.md#act-common-src-com-port)
    * [`mc-common`](codebase/act-common.md#act-common-mc-common)
* [`act-photo`](codebase/act-photo.md)

## Задачи

* [Регулятор освещенности](problems/act-photo/problem.md)
    * [Постановка](problems/act-photo/problem.md#регулятор-освещенности)
    * [Решение](problems/act-photo/solution.md)
        * [Выходной каскад](problems/act-photo/solution.md#выходной-каскад)
        * [Входные каскады](problems/act-photo/solution.md#входные-каскады)
        * [Производство печатной платы](problems/act-photo/solution.md#производство-печатной-платы)
        * [Код](problems/act-photo/solution.md#код)
        * [Итог](problems/act-photo/solution.md#итог)
        * [Замечания](problems/act-photo/solution.md#замечания)
* [Интерфейсы](problems/interfaces/problem.md)


## Цифровые устройства

* [`ATmega8A`](devices/ATmega8A/README.md)
    * [Таймеры и ШИМ](devices/ATmega8A/timers-and-pwm.md)
    * [АЦП](devices/ATmega8A/adc.md)
    * [Интерфейсы](devices/ATmega8A/interfaces.md)

## Схемотехника

## Теория

* [ПИД-регулятор](theory/pid-controller.md)

## Написание кода

* [Ведение репозиториев команды](code/repos.md)
    * [Структура репозиториев](code/repos.md#структура-репозиториев)
    * [Оформление задач](code/repos.md#соглашение-об-оформлении-задач)
    * [Оформление запроса не внесение изменений](code/repos.md#соглашение-о-внесении-изменений)
* [Работа с `COM`-портом](code/com-port.md)

## Программные инструменты

* [Git](apps/git.md)
    * [Введение в Git](apps/git.md#введение-в-git)
    * [Установка](apps/git.md#установка)
    * [Основы работы](apps/git.md#основы-работы)
        * [Клонирование репозитория](apps/git.md#клонирование-репозитория)
        * [Выкачивание изменений](apps/git.md#выкачивание-изменений-из-удаленного-репозитория)
        * [Фиксация локальных изменений](apps/git.md#создание-локальных-изменений)
        * [Фиксация удаленных изменений](apps/git.md#внесение-изменений-в-удаленном-репозитории)
        * [Откат изменений](apps/git.md#откат-к-предшествующим-состояниям)
        * [Работа с ветками](apps/git.md#работа-с-ветками)
        * [Слияние веток](apps/git.md#слияние-веток)
        * [Еще раз о `git push` и `git pull`](apps/git.md#еще-раз-о-git-push-и-git-pull)
    * [Подмодули](apps/git.md#подмодули)
        * [Добавление подмодуля](apps/git.md#добавление-репозитория-как-подмодуля)
        * [Обновление подмодуля](apps/git.md#выкачивание-изменений-подмодуля)
        * [Клонирование репозиториев с подмодулями](apps/git.md#клонирование-репозиториев-с-подмодулями)
        * [Внесение изменений в подмодуль](apps/git.md#внесение-изменений-в-подмодули)
    * [`Git LFS`](apps/git.md#git-lfs)
        * [Настройка](apps/git.md#настройка-git-lfs)
    * [Правила хорошего тона](apps/git.md#правила-хорошего-тона-при-работе-с-репозиториями)
* [`IAR Embedded Workbench`](apps/iar-ew.md)
    * [Типы памяти](apps/iar-ew.md#типы-памяти)
    * [Включаемые файлы](apps/iar-ew.md#включаемые-файлы)
    * [Хранение переменных в разных типах памяти](apps/iar-ew.md#хранение-переменных-в-разных-типах-памяти)
    * [Работа с прерываниями](apps/iar-ew.md#работа-с-прерываниями)
    * [Установка `IAR EW IDE`](apps/iar-ew.md#установка-iar-ew-ide)
    * [Настройка `IAR EW IDE`](apps/iar-ew.md#настройка-ewavr-для-работы-с-atmega8a)

---

* [Ссылки](links/README.md)