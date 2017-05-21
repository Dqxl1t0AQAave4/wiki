# Регулятор освещенности

> Разработать регулятор освещенности на базе микроконтроллера `ATmega8(A)`. Задача -- поддерживать заданный уровень освещенности в условиях переменной освещенности.

---

## Инвентарь (технические средства)

- Макет *Регулятор освещенности*
- Микроконтроллер `ATmega8A`
- Драйвер RS-232 `ST232` / `MAX232`
- Набор усилителей `TL084`
- Резисторы, конденсаторы, диоды...

## Инвентарь (программные средства)

- Схемотехнический редактор `Micro Cap 9`
- Разводчик `Layout 5.0`
- Среда разработки `IAR Embedded Workbench for Atmel AVR 5.50.1` со встроенным компилятором
- Программатор `avreal`

---

## Требования к реализации

Вся работа по созданию регулятора освещенности состоит из следующих частей:

1. Схемотехническое моделирование (моделирование, разводка)
2. Монтаж платы
3. Программирование микроконтроллера
4. Проведение испытаний

### Моделирование

Производится исходя из имеющегося инвентаря.

1. Необходимо разработать питающие каскады фотодиода, принять с него сигнал, отфильтровать и усилить его для подачи на АЦП. Фильтрация производится для устранения наводок сети (колебания интенсивности света с частотой, кратной 50Гц, должны быть отсеяны). Усиление (ослабление) должно завести сигнал в нормальный диапазон работы АЦП.

    Кроме сигнала с фотодиода, на АЦП должен поступать (тоже через фильтр и усилитель) некоторый (регулируемый) опорный уровень, определяющий уровень освещенности, относительно которого будет производиться регулирование.

2. Разработка питающих каскадов светодиода. Питание светодиода производится при помощи широтно-импульсной модуляции, которая рассчитывается и производится микроконтроллером.

3. Разработка схем подключения интерфейсных выводов (`RS-232`, `SPI`, `I2C`).

### Монтаж

Состоит из производства непосредственно печатной платы.

### Программирование

Должно решать задачу управления светодиодом в зависимости от поступающих опорного и текущего сигналов интенсивности света, а именно задачу формирования необходимой ширины импульсов, поступающих на светодиод. Предлагается использовать ПИД-регулятор в качестве алгоритма управления.

Кроме того, необходимо разработать механизмы отладки через последовательный интерфейс `RS-232`.

---

## Описание макета

Изображение лабораторного макета представлено на рис.

![Изображение макета](/img/act-photo-model.jpg)

Макет состоит из планки с двумя парами светодиодов (пара, управляемая с макета и дающая свет в ИК и видимом красном диапазоне волн, и аналогичная пара с внешним управлением) и управляющей ручкой и плоскости с установленным на ней фотодиодом, способной передвигаться по рельсам вперед и назад. Ручка позволяет настраивать яркость первой пары светодиодов. Светодиоды в парах связаны между собой и управляются общим входным сигналом. Фотодиод имеет выходы для включения во внешнюю управляющую схему.

Задачей управления является построение оптимального алгоритма поддержания постоянной интенсивности света на светодиоде путем управления парой внешних светодиодов.