# `COM`-порт

**После́довательный порт** (англ. serial port, **COM-порт**, англ. communications port) — сленговое название интерфейса стандарта `RS-232`, которым массово оснащались персональные компьютеры. (*Wikipedia*)

Здесь будет рассмотрен пример настройки и работы с `COM`-портом в `Windows`.

## Открытие порта

Для открытия порта можно использовать следующий код, основанный на `Windows API` (*MSDN*).

```c
#define BUFFER_SIZE 1000   /* Port R/W Buffers Size (e.g. 1s)      */
#define TIMEOUT 1000       /* Port R/W Timeouts     (e.g. 1s)      */
#define BAUDRATE 4800      /* Baud Rate             (e.g. 4.8kbps) */
#define PORT_NAME "COM3"   /* Port Name             (e.g. COM3)    */

/* Open port */

HANDLE m_Handle = CreateFile(
    _T(PORT_NAME),
    GENERIC_READ | GENERIC_WRITE,
    0,
    NULL,
    OPEN_EXISTING,
    FILE_ATTRIBUTE_NORMAL,
    NULL
);
if (m_Handle == INVALID_HANDLE_VALUE)
{
    std::cerr << "Cannot open port.";
    return;
}

/* Setup buffers and timeouts */

SetCommMask(m_Handle, EV_RXCHAR);
SetupComm(m_Handle, BUFFER_SIZE, BUFFER_SIZE);

COMMTIMEOUTS CommTimeOuts;
CommTimeOuts.ReadIntervalTimeout = 0xFFFFFFFF;
CommTimeOuts.ReadTotalTimeoutMultiplier = 0;
CommTimeOuts.ReadTotalTimeoutConstant = TIMEOUT;
CommTimeOuts.WriteTotalTimeoutMultiplier = 0;
CommTimeOuts.WriteTotalTimeoutConstant = TIMEOUT;

if (!SetCommTimeouts(m_Handle, &CommTimeOuts))
{
    CloseHandle(m_Handle);
    std::cerr << "Cannot setup port timeouts.";
    return;
}

/* Setup Baud Rate and Frame Format */

DCB ComDCM;

memset(&ComDCM, 0, sizeof(ComDCM));
ComDCM.DCBlength = sizeof(DCB);
GetCommState(m_Handle, &ComDCM);
ComDCM.BaudRate = DWORD(BAUDRATE);
ComDCM.ByteSize = 8;
ComDCM.Parity = ODDPARITY;
ComDCM.StopBits = ONESTOPBIT;
ComDCM.fAbortOnError = TRUE;
ComDCM.fDtrControl = DTR_CONTROL_DISABLE;
ComDCM.fRtsControl = RTS_CONTROL_DISABLE;
ComDCM.fBinary = TRUE;
ComDCM.fParity = TRUE;
ComDCM.fInX = FALSE;
ComDCM.fOutX = FALSE;
ComDCM.XonChar = 0;
ComDCM.XoffChar = (unsigned char) 0xFF;
ComDCM.fErrorChar = FALSE;
ComDCM.fNull = FALSE;
ComDCM.fOutxCtsFlow = FALSE;
ComDCM.fOutxDsrFlow = FALSE;
ComDCM.XonLim = 128;
ComDCM.XoffLim = 128;

if (!SetCommState(m_Handle, &ComDCM))
{
    CloseHandle(m_Handle);
    std::cerr << "Cannot setup port configuration.";
    return;
}
```

Для настройки параметров порта также может быть вызвано стандартное окно `Windows` (см. MSDN).

## Чтение и запись

Чтение осуществляется следующим образом:

```c
char *bytes = /* whatever data */;
size_t length = /* byte array length */;
DWORD read;
if (!ReadFile(m_Handle, bytes, length, &read, 0))
{
    CloseHandle(m_Handle);
    std::cerr << "Cannot read data.";
    return;
}
```

Запись производится не сильно иначе:

```c
char *bytes = /* whatever data */;
size_t length = /* byte array length */;
DWORD written;
if (!WriteFile(m_Handle, bytes, length, &written, 0))
{
    CloseHandle(m_Handle);
    std::cerr << "Cannot send data.";
    return;
}
```

Обе функции возвращают через параметр количество записанных/прочитанных байт, которое в принципе может быть меньше, чем `length`. Чтение и запись могут быть зациклены таким образом, чтобы обеспечить передачу/прием ровно `length` байт.

## Закрытие порта

Производится функцией `CloseHandle(m_Handle)`.