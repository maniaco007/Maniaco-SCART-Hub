# Licença de uso — Maniaco SCART Hub

**Copyright © 2026 Maniaco Game Room. Todos os direitos reservados.**

## Português

1. **Uso gratuito.** Você pode baixar e usar o firmware do Maniaco SCART Hub ("o Firmware") de graça, para uso pessoal, gravando-o em quantas placas suas quiser.
2. **Propriedade.** O Firmware, o seu código, a página de configuração embutida nele, a documentação e as imagens pertencem à Maniaco Game Room. Esta licença não transfere nenhum direito de propriedade.
3. **É proibido:** vender o Firmware ou cobrar por ele; vender placas, kits ou aparelhos com o Firmware gravado sem autorização por escrito; distribuir versões modificadas; descompilar, fazer engenharia reversa ou extrair o código do Firmware, exceto quando a lei ou as licenças de terceiros (item 6) permitirem expressamente; remover os créditos e avisos de direitos autorais.
4. **Compartilhar:** você pode compartilhar o link oficial deste repositório. Para redistribuir os arquivos `.bin` em outro lugar, peça autorização.
5. **gbs-control.** O Firmware não contém nem modifica o firmware da GBS: ele usa os comandos web que o gbs-control já oferece. O gbs-control é um projeto independente, de seus autores. Este projeto não é afiliado ao gbs-control nem aos fabricantes da GBS-8200, do ESP32-C3 ou do switch SCART.
6. **Componentes de terceiros.** O Firmware inclui bibliotecas de terceiros, listadas abaixo e em [TERCEIROS.md](TERCEIROS.md), cada uma com a própria licença. Nada nesta licença restringe os direitos que elas dão a você. Em particular, para as bibliotecas sob a GNU LGPL 2.1, você pode modificar o Firmware para uso próprio e fazer engenharia reversa para depurar essas modificações, na medida em que a LGPL 2.1 exige; a oferta dos arquivos objeto está em [TERCEIROS.md](TERCEIROS.md).
7. **Sem garantia.** O Firmware e a documentação são fornecidos "como estão", sem garantia de qualquer tipo. A montagem envolve solda dentro de aparelhos ligados à tomada e a outros equipamentos: o uso é por sua conta e risco. A Maniaco Game Room não se responsabiliza por danos ao switch, à GBS, aos consoles, à TV, ao computador ou a qualquer outro equipamento.

## English

1. **Free use.** You may download and use the Maniaco SCART Hub firmware ("the Firmware") free of charge, for personal use, on any number of boards you own.
2. **Ownership.** The Firmware, its code, the configuration page built into it, the documentation and the images belong to Maniaco Game Room. This license transfers no ownership rights.
3. **You may not:** sell the Firmware or charge for it; sell boards, kits or devices with the Firmware installed without written permission; distribute modified versions; decompile, reverse engineer or extract the Firmware's code, except where the law or the third-party licenses (item 6) expressly allow it; remove credits and copyright notices.
4. **Sharing:** you may share the official link to this repository. To redistribute the `.bin` files elsewhere, ask for permission.
5. **gbs-control.** The Firmware neither contains nor modifies the GBS firmware: it uses the web commands gbs-control already provides. gbs-control is an independent project by its own authors. This project is not affiliated with gbs-control or with the makers of the GBS-8200, the ESP32-C3 or the SCART switch.
6. **Third-party components.** The Firmware includes third-party libraries, listed below and in [TERCEIROS.md](TERCEIROS.md), each under its own license. Nothing in this license restricts the rights they grant you. In particular, for the libraries under the GNU LGPL 2.1, you may modify the Firmware for your own use and reverse engineer it to debug those modifications, to the extent the LGPL 2.1 requires; the object file offer is in [TERCEIROS.md](TERCEIROS.md).
7. **No warranty.** The Firmware and the documentation are provided "as is", without warranty of any kind. Installation involves soldering inside devices connected to mains power and to other equipment: use it at your own risk. Maniaco Game Room is not liable for damage to the switch, the GBS, consoles, TV, computer or any other equipment.

## Componentes de terceiros / Third-party components

O firmware inclui / The firmware includes:

| Componente | Licença |
|---|---|
| Arduino core para ESP32 3.3.12 (núcleo, Wi-Fi, rede, servidor web, cliente HTTP, FS) | GNU LGPL 2.1 |
| Arduino core para ESP32: `Preferences`, `Hash`; ESP-IDF 5.5; Mbed TLS; bibliotecas Wi-Fi da Espressif | Apache 2.0 |
| Arduino core para ESP32: `NetworkUdp`; FreeRTOS Kernel | MIT |
| lwIP; wpa_supplicant | BSD 3-Clause |
| Newlib | Licenças da Newlib / Newlib licenses |
| libgcc, libstdc++ | GPL 3 com a GCC Runtime Library Exception 3.1 |

Os textos completos dessas licenças estão em [TERCEIROS.md](TERCEIROS.md). / The full texts are in [TERCEIROS.md](TERCEIROS.md).
