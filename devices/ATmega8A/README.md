# `ATmega8A`

`ATmega8A` --- микроконтроллер фирмы Atmel на базе архитектуры AVR.

## Некоторые характеристики устройства

* 8-битная архитектура AVR
* Тактовая частота до 16МГц
    * Калиброванный RC-осциллятор на 1, 2, 4, 8МГц
    * Возможность подключения внешнего осциллятора частотой до 16МГц
* 8Кб энергонезависимой Flash-памяти с возможностью самопрограммирования
* 512б энергонезависимой EEPROM-памяти
* 1Кб энергозависимой SRAM-памяти
* Два 8-битных и один 16-битный таймер
* Генератор функций, способный производить ШИМ-сигналы и др. виды сигналов
* Аналоговый компаратор
* 6-канальный АЦП
* Реализованные интерфейсы: `TWI`, `USART`, `SPI`

## Принципиальная схема устройства и конфигурация пинов

![Конфигурация пинов `ATmega8A`](/img/ATmega8A-overview.png)

![Принципиальная схема `ATmega8A`](/img/ATmega8A-schema.png)

## Замечания

1. Здесь и далее рассматривается только устройство в PDIP-корпусе.
2. Все изображения по прибору взяты из даташитов на прибор.
3. Весь код, рассматриваемый в этом разделе, приводится для среды разработки `IAR Embedded Workbench` от `IAR Systems`.