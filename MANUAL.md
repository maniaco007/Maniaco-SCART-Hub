# Manual do Maniaco SCART Hub

**[English version](MANUAL.en.md)**

O Maniaco SCART Hub faz a **GBS-Control** carregar sozinha o perfil do console escolhido no **switch SCART automático**. Um ESP32-C3 SuperMini instalado dentro do switch lê qual porta está ativa e avisa a GBS pelo Wi-Fi da própria GBS. Este manual cobre as medições, a montagem, a gravação do firmware e a página de configuração.

Um projeto do blog **[Maniaco Game Room](https://www.maniacogameroom.com.br/)**.

---

## Sumário

1. [Antes de começar](#1-antes-de-começar)
2. [Medir o switch](#2-medir-o-switch)
3. [Montar a placa](#3-montar-a-placa)
4. [Ligar no switch](#4-ligar-no-switch)
5. [Gravar o firmware](#5-gravar-o-firmware)
6. [Primeira configuração](#6-primeira-configuração)
7. [A página de configuração](#7-a-página-de-configuração)
8. [LED do ESP32-C3](#8-led-do-esp32-c3)
9. [Alimentação: USB e 5 V](#9-alimentação-usb-e-5-v)
10. [Atualizar o firmware](#10-atualizar-o-firmware)
11. [Problemas e soluções](#11-problemas-e-soluções)
12. [Créditos](#12-créditos)

---

## 1. Antes de começar

**Você precisa de:**

- **ESP32-C3 SuperMini**;
- **switch SCART automático de 10 portas** com 2× ULN2003 acionando os relés;
- **GBS-Control**, usando a rede própria (`gbscontrol`);
- 10 resistores de **10 kΩ** (1/4 W), capacitor eletrolítico de **470 µF / 10 V**, **conector de 2 pinos** (header macho/fêmea), placa perfurada pequena (~12 × 9 furos) e fio fino (26 a 30 AWG);
- multímetro, ferro de solda com ponta fina e um cabo USB-C de dados.

**Firmware:** baixe na página de [Releases](../../releases). Há dois arquivos por versão:

| Arquivo | Quando usar |
|---|---|
| `maniaco-scart-hub-X.Y.Z-instalacao.bin` | Primeira gravação (apaga o mapa salvo) |
| `maniaco-scart-hub-X.Y.Z-atualizacao.bin` | Atualização (mantém o mapa salvo) |

Confira os arquivos com o `SHA256SUMS.txt` do mesmo release: `Get-FileHash arquivo.bin` no PowerShell, ou `sha256sum arquivo.bin` no Linux/Mac. O código tem que ser igual; se não for, não grave.

> **Faça toda a montagem com o switch desligado da tomada.** A montagem é por sua conta e risco.

---

## 2. Medir o switch

Só multímetro, **antes de soldar qualquer coisa**.

> ⚠️ **Não encoste a ponta do multímetro nos pinos dos ULN2003.** O passo é de 1,27 mm: uma ponta que escorrega faz curto. Meça nos **pads dos resistores** ligados a eles.

**Com o switch desligado, ache os pontos:**

1. Localize os dois **ULN2003** (chips de 16 pinos perto dos relés). O pino 1 fica do lado da bolinha gravada no chip; os pinos 1 a 7 são as entradas.
2. Siga a trilha de cada entrada até o **resistor SMD** ligado a ela (geralmente marcado "472").
3. Em continuidade, descubra qual **pad** desse resistor tem **0 Ω até o pino do ULN**: é o pad do lado do ULN, onde você vai soldar.
4. Ache o **capacitor de saída do conversor** de 5 V (perto da entrada DC): o + e o − dele serão o 5 V e o GND.

**Com o switch ligado e um console ligado** (espere o relé clicar), ponta preta no − do capacitor, multímetro em DC:

| Onde | Esperado |
|---|---|
| + do capacitor do conversor | ~4,5 a 5 V |
| Pad do resistor da porta ativa (lado do ULN) | ~3,3 a 3,8 V |
| Pads das outras portas | ~0 V |

Repita para cada porta. O que fazer com o resultado:

| Se você mediu... | Então |
|---|---|
| ~3,3 a 3,8 V na porta ativa, ~0 V nas outras | Siga o manual como está (só o resistor de 10 kΩ) |
| ~5 V na porta ativa | Acrescente um resistor de **18 kΩ** do GPIO para o GND em cada linha (divisor), além do 10 kΩ |
| Porta ativa **baixa** e as outras altas, ou tensão que oscila | **Este firmware não serve** para o seu switch. Não ligue |
| Mais de um pad alto com um só console | Switch não suportado. Não ligue |

---

## 3. Montar a placa

> **Desenho furo por furo:** a [página interativa de montagem](https://www.maniacogameroom.com.br/montagem/maniaco-scart-hub/) mostra a placa de 12 × 9 furos vista de cima e por baixo. Também há o [tutorial de montagem](https://www.maniacogameroom.com.br/2026/10/maniaco-scart-hub-montagem-da-placa) no blog.

1. **Solde o ESP32-C3 na placa perfurada** com a barra de pinos macho, com o **USB-C na borda** da placa (para plugar o cabo depois de instalado).
2. **Resistores de 10 kΩ em pé**, um ao lado de cada GPIO usado: corpo no furo de fora, perna dobrada no furo vizinho ao pino. Por baixo, faça a ponte entre o pino e essa perna. Ponha espaguete (termo-retrátil fino) nas pernas dobradas, se ficarem perto umas das outras.
3. **GPIOs usados** (a ordem não importa, a página identifica depois):

   | Linha | L1 | L2 | L3 | L4 | L5 | L6 | L7 | L8 | L9 | L10 |
   |---|---|---|---|---|---|---|---|---|---|---|
   | GPIO | 0 | 1 | 3 | 4 | 5 | 6 | 7 | 10 | 20 | 21 |

   **Não use** os GPIO 2, 8 e 9: o 2 e o 9 decidem o modo de boot, e o 8 é o LED. Os pinos 3,3, 2, 8 e 9 não se ligam em nada.
4. **Capacitor de 470 µF** no canto dos pinos 5V e G: perna **+** no 5V, perna **−** (a da faixa) no G.
5. **Teste com o multímetro** antes de ligar qualquer coisa:

| Pontas em | Deve medir |
|---|---|
| Pino GPIO e a perna de fora do resistor dele | ~10 kΩ |
| Pernas de fora de dois resistores vizinhos | Nunca zero |
| 5V e G | Sobe devagar (o capacitor carregando), nunca zero |

![Lado da solda: onde vai cada fio](imagens/placa-fios.png)

---

## 4. Ligar no switch

Switch **desligado da tomada**. O 5 V fica por último.

As fotos abaixo são de um switch **AUTO EUR-SCART 10IN1OUT** (placa 2024-03-17-DJF). Em outro modelo, use os pontos que você achou na seção 2.

![Onde ligar as 10 linhas e o GND](imagens/switch-linhas-e-gnd.png)

![Onde tirar o 5 V](imagens/switch-5v.png)

1. **GND:** fio do pino **G** do ESP32-C3 até o **−** do capacitor do conversor, ou uma ilha larga de GND. Nunca no pino 8 do ULN.
2. **Linhas:** um fio da perna de fora de cada resistor até o **pad do resistor do lado do ULN** de uma porta (seção 2). Passe os fios dos GPIO 20 e 21 longe da antena do ESP32-C3.
3. **5 V:** fio do **+** do capacitor do conversor até o **conector de 2 pinos**, e do conector até o pino **5V**. **Deixe o conector aberto** até o fim da seção 6.
4. Etiquete o conector: **"5V: desligar antes do USB"**.
5. Confira com o multímetro, entre cada solda nova e as vizinhas: nunca zero.

---

## 5. Gravar o firmware

> ⚠️ **Conector de 5 V aberto** durante toda a gravação ([por quê](#9-alimentação-usb-e-5-v)).

**Pelo navegador (o mais fácil):** abra o [instalador do Maniaco SCART Hub](https://www.maniacogameroom.com.br/instalar/maniaco-scart-hub/) com o **Chrome** ou o **Edge** num computador, conecte o ESP32-C3 no USB, clique em **Instalar**, escolha a porta e, na primeira instalação, aceite **apagar** o aparelho.

**Pelo `esptool`:**

```bash
esptool --chip esp32c3 write-flash 0x0 maniaco-scart-hub-X.Y.Z-instalacao.bin
```

**Depois de gravar, desconecte e reconecte o USB** (o ESP32-C3 fica em modo de gravação até isso).

> A gravação não começa? Segure **BOOT**, aperte e solte **RST**, solte **BOOT** e tente de novo.

---

## 6. Primeira configuração

1. **Confira a rede da GBS:** ligue a GBS. A rede `gbscontrol` deve aparecer em poucos segundos. Se a GBS estiver conectada na rede da sua casa, a `gbscontrol` não aparece: use **Reset → Reset WiFi** no menu do OLED da GBS.
2. Com o ESP32-C3 ainda no USB, **ligue o switch**. Isso é permitido porque o conector de 5 V está aberto.
3. No celular, entre na rede **`gbscontrol`** e abra **`http://192.168.4.20`**. Salve nos favoritos: o endereço é sempre o mesmo.
4. Clique em **Identificar portas** e siga os passos (seção 7.2). Toda porta com console deve acender uma linha; se alguma não acender, confira a solda dela.
5. Em cada linha, escolha o **perfil da GBS**, dê um nome ao console e clique em **Salvar**.
6. Use **Testar** em cada linha para conferir o perfil na TV.

**Passe para a alimentação do switch:**

1. **Tire o cabo USB primeiro.**
2. Feche o conector de 5 V e ligue o switch. Em até ~30 s o ESP32-C3 conecta na GBS e a página volta a abrir.
3. Feche a caixa e veja o **sinal** no topo da página. Se ficar muito fraco (abaixo de −75 dBm), mude a placa de lugar, longe de partes metálicas.

---

## 7. A página de configuração

![Página de configuração](imagens/pagina-configuracao.png)

### 7.1 A tela

| Parte | O que mostra |
|---|---|
| **Topo** | A conexão com a GBS (com o sinal) e a linha **Ativo**, por exemplo "Ativo: Porta 3 · Pc Engine → D · Turbo Duo (L3)" |
| **Linhas L1 a L10** | Uma por fio ligado ao switch: **Porta** do switch, **nome do console**, **perfil** da GBS e **Testar**. A linha do console ligado fica acesa |
| **Espera antes de carregar o preset** | Ajuste fino do tempo de troca (seção 7.4) |
| **Botões** | **Salvar**, **Identificar portas** e **Reler nomes da GBS** |
| **Rodapé** | A versão do firmware |

- **Porta:** só identifica qual porta do switch é aquela linha. A mesma porta não pode estar em duas linhas.
- **Perfil:** o slot da GBS carregado quando a linha fica ativa. A lista mostra os nomes salvos na GBS: primeiro os perfis com nome, depois os slots vazios.
- **Não trocar o perfil:** quando a porta fica ativa, nada é enviado à GBS. Use para consoles sem perfil próprio ou portas sem uso.
- Depois de mudar qualquer coisa, clique em **Salvar**. O mapa fica gravado no ESP32-C3.

### 7.2 Identificar portas

1. Clique em **Identificar portas**. A tela pede: "Porta 1 de 10: ligue só o console da porta 1 do switch".
2. Ligue esse console. Quando a linha dele acender e ficar estável, aparece "Detectado: L6". Clique em **Confirmar**.
3. Sem console naquela porta? Clique em **Pular esta porta**.
4. No fim, clique em **Salvar e concluir**.

**Cancelar** devolve as portas como estavam. Se a mesma linha aparecer para duas portas, o assistente avisa e não deixa confirmar. Enquanto isso, a GBS continua recebendo o perfil de cada linha: é normal a imagem ir trocando.

### 7.3 Testar

Salva o mapa e faz a GBS carregar o perfil daquela linha na hora. Aparece um aviso amarelo "Simulando a linha…": durante esse tempo o switch é ignorado. Clique em **Voltar às linhas** quando terminar; se esquecer, ele volta sozinho em 2 minutos.

### 7.4 Espera antes de carregar o preset

Quando a porta muda, o ESP32-C3 escolhe o perfil na GBS na hora e, depois dessa espera, manda carregar. A espera dá tempo de o console novo estabilizar o sinal: carregar sem sinal faz a GBS cair num preset de reserva (480p).

- **Padrão:** 1500 ms.
- **O perfil muda, mas a imagem fica errada?** Aumente, por exemplo para 2500 ms.
- **0:** nunca manda carregar. Só serve se a sua GBS sempre recarregar sozinha.

### 7.5 Quando ele envia

- **A porta mudou:** escolhe o perfil na hora e manda carregar depois da espera. Se a porta mudar de novo antes, vale a mais nova.
- **Nenhum console, ou "Não trocar o perfil":** não envia nada.
- **A GBS reiniciou ou o Wi-Fi caiu:** ao reconectar, reenvia o perfil da porta atual.
- **Fora isso, nada.** Cada troca grava na memória da GBS, por isso não há envio repetido.

---

## 8. LED do ESP32-C3

| LED | Significa |
|---|---|
| Pisca devagar (1 vez por segundo) | Procurando a rede da GBS |
| Pisca rápido | A GBS não respondeu; tentando de novo |
| Pisca médio | Mais de uma linha ativa, ou sinal instável nas linhas |
| Aceso | Conectado, com uma porta ativa |
| Apagado | Conectado, nenhuma porta ativa |

---

## 9. Alimentação: USB e 5 V

> **Regra:** com o fio de 5 V do switch ligado no pino 5V do ESP32-C3, **não plugue o cabo USB** (PC, carregador ou power bank). Para usar o USB, **abra antes o conector de 5 V**.

**Por quê:** no ESP32-C3 SuperMini, o pino 5V é o próprio VBUS do USB, sem proteção entre eles. Com os dois ligados:

- **o PC passa a alimentar o switch** pela porta USB (o USB fica em ~5,0 V, acima do trilho do switch), e a porta pode desligar ou se danificar;
- **com o PC desligado, o switch alimenta o PC** pelo cabo;
- **as duas fontes brigam** pelo mesmo fio;
- **o terra do PC se liga ao do switch, da GBS e da TV**, o que pode criar ruído na imagem.

**Para usar o USB com tudo instalado:** abra o conector de 5 V (o GND pode continuar), plugue o USB e pode deixar o switch ligado. Ao terminar, **tire o USB primeiro** e só depois feche o conector.

| Situação | Pode? |
|---|---|
| Conector de 5 V fechado, sem USB (uso normal) | **Sim** |
| USB plugado, conector de 5 V aberto (gravar, depurar) | **Sim** |
| USB plugado **e** conector de 5 V fechado | **Não** |

---

## 10. Atualizar o firmware

1. **Abra o conector de 5 V.** Depois conecte o USB.
2. Grave o arquivo de **atualização**, que mantém o mapa:
   - **pelo navegador:** responda **não** quando o instalador perguntar se deve apagar;
   - **pelo `esptool`:**
     ```bash
     esptool --chip esp32c3 write-flash 0x10000 maniaco-scart-hub-X.Y.Z-atualizacao.bin
     ```
3. Desconecte o USB e **só então** feche o conector de 5 V.
4. Confira a versão no rodapé da página.

Para **voltar a uma versão anterior**, grave o arquivo de atualização dela do mesmo jeito.

---

## 11. Problemas e soluções

| Problema | O que fazer |
|---|---|
| A porta do ESP32-C3 não aparece no computador | Troque o cabo (precisa ser de dados). Segure BOOT, aperte e solte RST, solte BOOT |
| Gravou, mas nada acontece | Desconecte e reconecte o USB: depois de gravar, o ESP32-C3 fica em modo de gravação |
| A página `192.168.4.20` não abre | O celular está na rede `gbscontrol` (e não na de casa)? O switch está ligado? Espere ~30 s depois de ligar a GBS |
| A rede `gbscontrol` some de tempos em tempos | A GBS tem a rede de casa salva: **Reset → Reset WiFi** no menu do OLED da GBS |
| A lista de perfis mostra só letras | A GBS estava com pouca memória livre. Clique em **Reler nomes da GBS** |
| Uma porta não acende no Identificar portas | Solda do fio daquela porta, ou o pad errado do resistor. Meça de novo (seção 2) |
| Duas linhas acendem juntas | Ponte de solda entre fios vizinhos. Desligue e confira com o multímetro |
| O perfil muda, mas a imagem não fica certa | Aumente a **Espera antes de carregar o preset** (seção 7.4) |
| Apaguei um perfil na GBS e as portas ficaram com o perfil errado | Apagar um perfil desloca os seguintes. Escolha de novo o perfil de cada linha |
| O ESP32-C3 reinicia quando um console liga | A fonte do switch está no limite: capacitor maior (1000 µF) junto do ESP32-C3, ou fonte do switch melhor |

---

## 12. Créditos

- gbs-control: **[ramapcsx2/gbs-control](https://github.com/ramapcsx2/gbs-control)** e colaboradores
- GBS-Control PT-BR: **[Maniaco Game Room](https://github.com/maniaco007/Projeto-GBSC---BR-by-Maniaco)**
- Arduino core para ESP32 e ESP-IDF: **[Espressif](https://github.com/espressif/arduino-esp32)**
- Instalador pelo navegador: [ESP Web Tools](https://esphome.github.io/esp-web-tools/)

Licença: **Freeware — uso gratuito. © 2026 Maniaco Game Room. Todos os direitos reservados.** Veja a [licença](LICENCA.md) e os [componentes de terceiros](TERCEIROS.md).
