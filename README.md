# udp_echo_server
enc28j60 + FreeRTOS + stm32f407-discovery + lwip 

Сделано для новой станции GNS. Так как на stm32f407 выводы интерфейса RMII/MII сидят на тех же ногах, что и 
выводы HS-USB. Скорость большая не нужна, а для посылки данных в seismic unix spi ethernet карты будет достаточно

платка enc28j60 подключена по SPI2 на stm32

RESET-PB0

INT - PB2

SCK-PB10

MISO-PB14

MOSI-PB15

CS-PB12

натянут стек lwip. Платка получает адрес по DHCP, на исходящие пакеты не ставилась контрольная сумма, так как дефиниции в файле lwipopts.h IAR не обрабатывал правильно - пришлось ковырятся с настройками lwip и править те места, где она (CRC) формируется: icmp.c ip.c в lwip

программа после получения адреса работает как UDP echo сервер.
Необходимо на следующем шаге установить прерывание платки по приему пакета и посылать семафор из обработчика прерываний,
как это сделано в других программах. сейчас просто запускается задача, которая крутится в бесконечном цикле

Изменил работу платки-теперь задача получает семафор из прерывания при наличие необработанных пакетов.
драйвер eth-карты /library/lwip/port/enc28j60.c.
Прерывание на ноге PB2. Настройка платки и прерываний в драйвере в функции exti2_irq_config()
