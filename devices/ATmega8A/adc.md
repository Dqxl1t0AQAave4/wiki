# Аналого-цифровой преобразователь

Для преобразования аналоговых сигналов в цифровые используется устройство, называемое аналого-цифровым преобазователем (АЦП).

В микроконтроллер `ATmega8A` встроен 6-канальный 10-разрядный АЦП. Рассмотрим его настройку.

## Некоторые характеристики АЦП

* 6-канальный АЦП
* Разрешение -- 10 бит
    * Возможна установка левого выравнивания данных для упрощенного получения старших 8 бит
* Оцифровка сигналов от 0 до 2.56В или до напряжения питания
* Работа в непрерывном режиме или в режиме одной конверсии
* Время одной конверсии -- от 13 до 260мкс
    * Частота тактового сигнала АЦП -- от 50 до 200кГц
    * Время первой (после включения) конверсии -- 25 тактовых интервалов
    * Время последующих конверсий -- 13 тактовых интервалов
* Прерывание по завершении конверсии

## АЦП в режиме одной конверсии

Для включения и установки частоты тактового сигнала АЦП можно использовать следующий код:

```c
#define FOSC     1000000 /* Oscillator Frequency     (e.g. 1MHz)     */
#define PRESCALE 3       /* Clock Frequency Prescale (e.g. 1/8 FOSC) */

/* Enable ADC, setup prescaling */
ADCSR = (1<<ADEN)|(PRESCALE<<ADPS0);
```

Делитель частоты (`prescale`) выбирается из ряда допустимых значений (см. даташит) из необходимости обеспечить на АЦП частоту тактового сигнала 50..200кГц.

Рассмотрим работу в режиме ожидания результата.

Для произведения конверсии требуется выбрать необходимый канал, опорный уровень (2.56В, напряжение питания) и инициализировать конверсию. Для простоты будем рассматривать левое выравнивание данных.

```c
/**
 * Select `c` channel,
 * setup internal reference level (2.56V) as reference level,
 * setup left data alignment.
 */
#define REMUX(c) ADMUX = (3<<REFS0)|(1<<ADLAR)|(c<<MUX0)

/* Instantiate a Single-Ended conversion at 2-nd ADC channel */

unsigned char adc2;

REMUX(1);                       /* 2nd ADC channel                    */
ADCSR |= (1 << ADSC);           /* Initialize single-ended conversion */
while(!(ADCSR & (1 << ADIF)))   /* Wait for the end of the conversion */
    ;
adc2 = ADCH;                    /* Read the 8-bit conversion result   */
ADCSR &= ~(1 << ADIF);          /* Clear Conversion Complete flag     */
```