# `IAR Embedded Workbench`

_!
`IAR Embedded Workbench` рассматривается на примере разработки под `ATmega8A`.
!_

`IAR Embedded Workbench` -- среда разработки приложений для микроконтроллеров. Не имеет средств столь богатого синтаксического анализа и автозавершения кода, как другие более продвинутые IDE. Однако позволяет компилировать и отлаживать код под `ATmega8A` в том числе.

В `IAR EW` позволено использовать среды исполнения `clib` и `dlib`. Однако первая является морально устаревшей (судя по документации; `dlib` подключается по умолчанию; настраивается в свойствах проекта). Также имеется несколько диалектов `C/C++` и возможность писать код / делать вставки на `Assembler`. К использованию рекомендуется диалект `Embedded C++` (подключается по умолчанию; настраивается в свойствах проекта) ввиду его "легковесности" - он не утяжелен шаблонами, `STL` (`std::vector` и ему подобные), виртуальными функциями и классами, множественным наследованием и т.д. и содержит минимальный набор библиотек, достаточных для полноценной разработки и полноценного функционирования приложений.

`IAR EW` реализует несколько моделей памяти: от `tiny` до `large` (и выше). Отличаются они количеством адресуемой памяти. По умолчанию выставлена `tiny` (256 bytes). Поскольку `ATmega8A` имеет SRAM размером 1 Kbyte, есть смысл использовать модель памяти `small` для адресации на стеке не только 256, а 1020 bytes.

Для долго работающих приложений не рекомендуется использовать динамическое выделение памяти. Оно способствует "фрагментации" памяти, что может повлечь невозможность выделения требуемого количества памяти (не найдется линейного участка памяти нужной длины) даже при достаточно большом общем количестве свободной памяти (расположенной ввиду ее фрагментации маленькими порциями по всему адресному пространству).

Переменные, функции, определения типов могут располагаться в разных типах памяти. А конкретно, в `SRAM`, `flash`, `eeprom`.






# Типы памяти

`ATmega8A` имеет три типа памяти:

* энергонезависимая `flash`-память (64 Kbytes), используемая для хранения исходного кода программы и констант _(имеется возможность самопрограммирования памяти во время исполнения программы)_
* энергозависимая `SRAM`-память (1 Kbyte; в русской литературе -- ОЗУ) для хранения стека (переменных на стеке, адресов возврата из функций и т.д.)
* энергонезависимая `EEPROM`-память (512 bytes) для долговременного хранения переменных (в отличие от `flash` (?), является программируемой пользователем)

Архитектура `AVR` стандартизирует работату со всеми этими типами памяти.

В `IAR EW` по умолчанию все переменные хранятся на стеке (или в регистрах процессора), т.е. в `SRAM`. Имеется возможность (см. ниже) адресовать переменные и в других двух типах памяти.

При работе с `EEPROM` приходится учитывать тот факт, что эта память программируется отдельно от `flash`. Так, при использовании `EEPROM` (при соответствующих настройках `IAR EW`, см. [здесь](http://chipenable.ru/index.php/programming-avr/item/158-avr-rabota-s-eeprom-na-si.html)) будет сгенерировано два выходных файла: один загружается в `flash`, другой -- с умолчательными значениями `EEPROM`-переменных -- в `EEPROM`.






# Код под `ATmega8A` в `IAR EW IDE`

Пока-что содержит разрозненные данные по написанию кода.



## Включаемые файлы

Для управления состоянием микроконтроллера через код требуется иметь определения регистров. Эти определения уже имеются во включаемых файлах, соответствующих конкретным микроконтроллерам. Для `ATmega8A` используется заголовочный файл `iom8a.h`. Вместо него может быть подключен более "общий" файл `ioavr.h`, который автоматически подключает требуемый файл в зависимости от типа микроконтроллера.



### `iom8a.h`

Содержит описание векторов прерываний и стандартных регистров управления.

Например, описание регистра `DDRB` (направление ввода-вывода порта `B`). Изменить направление ввода-вывода можно путем записи `1` в соответствующий бит регистра. Например, `DDRB |= (1 << 5)` или `DDRB_Bit5 = 1` устанавливает (согласно даташитам) `PB5` (порт `B`, 5-й пин) портом вывода. И т.д.



### `bitdefinitions/iom8a.inc`

Подключается автоматически через файл `iom8a.h` при определении макроса `ENABLE_BIT_DEFINITIONS` (или опцией `General Options / System / Enable Bit Definitions in IO-include Files` среды `IAR EW`).

Содержит описание битов регистров. Например, `DDB0`..`DDB7`.

Предыдущий пример теперь можно переписать в виде: `DDRB |= (1 << DDB5)`.



### `intrinsics.h`

Этот заголовочный файл сожержит набор определений специальных функций и макросов (т.н. интринзиков), которые позволяют вставлять в код специальные инструкции. Например, разрешать и запрещать прерывания (`__enable_interrupt(void)`, `__disable_interrupt(void)`), приостанавливать работу программы на некотое время (__delay_cycles(unsigned long)) и т.д.



## Хранение переменных в разных типах памяти

Для объявления переменной с хранением в `EEPROM` используется ключевое слово `__eeprom`, с хранением во `flash` (только константы) -- `__flash`. Для объявления указателя на переменную в `EEPROM`-памяти или `flash`-памяти используются те же ключевые слова. Можно также указать место хранения самого указателя при помощи все тех же ключевых слов.

```c++
// переменная в SRAM
int v1;
// переменная в EEPROM
__eeprom int v2;
// указатель на переменную в SRAM, сам хранящийся в SRAM
int *p1;
// указатель на переменную в EEPROM, сам хранящийся в SRAM
int __eeprom *p2;
// указатель на переменную в EEPROM, сам хранящийся в EEPROM
__eeprom int __eeprom *p3;
```

Если переменные не изменяются в процессе работы программы (константы; например таблицы, массивы, строковые и числовые константы и т.д.), их можно хранить в `flash` (`EEPROM`). В противном случае (переменные) -- в `SRAM` (`EEPROM`). Очевидно, что `SRAM` обладает самым быстрым доступом к памяти. Однако `SRAM` ценна ввиду ее малого размера.

`IAR EW` позволяет указать точный адрес переменной посредством добавления `@ <address>` к объявлению. При этом переменная должна также быть помечена одним из квалификаторов `__no_init` или `const`. Первый означает, что инициализация переменной компилятором производиться не будет.

```c++
__no_init char alpha @ 0x2000; // инициализация отсутствует
const char beta @ 0x2002 = 5; // необходима инициализация
```

Указание точного адреса переменной полезно, если у него (адреса) есть какое-то специальное значение. Например, в `ATmega8A` существует ряд настроечных регистров, расположение которых строго фиксировано. Пример -- уже известный `DDRB`. Для его определения в файле `iom8a.h` как раз используется точная адресация. Кроме этого переферийные устройства могут иметь специальные адреса в одной из памятей микроконтроллера.



## Работа с прерываниями

**Прерывание** (англ. interrupt) — сигнал от программного или аппаратного обеспечения, сообщающий процессору о наступлении какого-либо события, требующего немедленного внимания. Прерывание извещает процессор о наступлении высокоприоритетного события, требующего прерывания текущего кода, выполняемого процессором. Процессор отвечает приостановкой своей текущей активности, сохраняя свое состояние, и выполняя функцию, называемую обработчиком прерывания (или программой обработки прерывания), который реагирует на событие и обслуживает его, после чего возвращает управление в прерванный код. (*Wikipedia*)

Прерывания могут генерироваться внешними / внутренними устройствами, например таймерами, интерфейсами приема-передачи данных (`USART`, `TWI`, `SPI`), программно.

Для определения того, какой код будет выполняться при наступлении конкретного прерывания, служит т.н. таблица векторов прерываний, в которую записываются адреса функций-обработчиков.

Для того, чтобы разные типы прерываний могли быть сгенерированы, требуется, чтобы был установлен флаг глобального разрешения прерываний. В `IAR EW` за это отвечает функция `__enable_interrupt(void)`. Она эквивалентна ассемблерной команде `sei`.

Как правило, конкретные типы прерываний требуется также разрешать отдельно. Например, при работе с `USART` для разрешения прерывания `RX Complete`, сигнализирующего о завершении приема фрейма данных, помимо глобального разрешения прерываний в регистре `USART Control and Status Register` (`UCSRE`) должен быть установлен бит `RXCIE`.

Для того, чтобы назначить какую-либо функцию обработчиком прерывания, ее объявляют следующим образом (приведен пример для `USART RX Complete Interrupt`):

```c
#pragma vector=USART_RXC_vect
__interrupt void usart_rxc_interrupt_handler()
{
    // TODO: handler body...
}
```

Определения векторов прерываний содержатся в подключаемых файлах, соответствующих модели микроконтроллера (`ATmega8A` → `iom8a.h`).





# Установка `IAR EW IDE`

_!
Рассматривается установка `IAR EWAVR` из нелицензионного дистрибутива, полученного на безвозмездной основе от третьих лиц.
!_

1. Запустить `ewavr5501_full/ewavr/setup.exe` (\<= Windows 7)
2. Запустить `ewavr5501_full/keygen/!RUNME`
3. В сгенерированном файле найти строки `Installserial` и `Key`, соответствующие архитектуре `EWAVR`
4. Ввести их в программу установки
5. Завершить установку
6. (?) Запустить программу `Пуск → IAR Systems → IAR Systems License Activation`
7. (?) Выбрать `Locking Criteria = My Computer`
8. (?) Выбрать `Register later by starting...`

После установки размер шрифта редактора кода устанавливается равным `9pt`, что очень мало. Рекомендуется его увеличить, поскольку `Ctrl + колесико мыши` здесь не работает.

Документация по компилятору (и, соответственно, разрешенным диалектам `Assembler/C/C++`) расположена в папке установки в директории `avr/doc`. Это в основном файлы, начинающиеся с `EWAVR`.

# Настройка `EWAVR` для работы с `ATmega8A`

Для работы с `ATmega8A` требуется произвести операции, изображенные на рисунках (ниже).

![Настройка сборки под конкретный процессор](/img/ewavr/target.png)

![Настройка используемой библиотеки (`CLIB` / `DLIB`)](/img/ewavr/lib.png)

![Подключение констант-наименований портов и т.д.](/img/ewavr/system.png)

![Включение генерации отладочных ассемблерных листингов](/img/ewavr/compiler-listing.png)

![Прописывание дополнительных путей к включаемым файлам](/img/ewavr/compiler-preprocessor.png)

![Настройка формата файла прошивки](/img/ewavr/linker-output.png)

Флаг `Enable Bit Definitions in IO-include Files` на вкладке `General Options / System` необходим для подключения констант-наименований портов, элементов регистров и т.д. при включении в код заголовочных файлов-описаний регистров (`iom8a.h`).

Для просмотра ассемблерных листингов следует включить опции вкладки `C/C++ Compiler / List`.

На вкладке `C/C++ Compiler / Preprocessor` можно указать дополнительные директории для поиска включаемых файлов, а также другие полезные опции.

На вкладке `Linker / Output` следует установить формат файла прошивки, как представлено на скриншоте. Полезно также изменить расширение файла на `.hex` -- стандартное расширение для прошивок микроконтроллеров.