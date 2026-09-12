# Jarvan
# 🤖 JARVAN - Robô Expressivo com ESP32 e MicroPython

Projeto desenvolvido para praticar sistemas embarcados e automação utilizando Python em um microcontrolador ESP32 conectado a uma tela OLED 0.96". O projeto dá "vida" a um robô chamado JARVAN, que exibe diferentes estados de humor (Bravo, Triste e Feliz) com animações visuais e frases personalizadas.

## 🛠️ Tecnologias e Ferramentas Utilizadas
* **Hardware:**
  * Placa ESP32 WROOM-32
  * Módulo Display OLED 0.96" I2C
  * Protoboard e Jumpers Macho-Macho
* **Software:**
  * Thonny IDE
  * MicroPython
* **Linguagem:** Python

## 🚀 Como Funciona
1. **Inicialização:** Ao ligar o sistema, o ESP32 inicializa a comunicação I2C e exibe uma tela de apresentação com o nome "JARVAN".
2. **Loop de Estados (Animação):** O sistema entra em um loop infinito (`while True`) alternando entre três expressões faciais dinâmicas desenhadas através de formas geométricas e linhas na tela OLED:
   * 😡 **Bravo:** Olhos com pupilas, sobrancelhas inclinadas e a frase *"GRRR..."*
   * 😢 **Triste:** Olhos caídos e a frase *"MUUUH :("*
   * 😄 **Feliz:** Olhos em formato de arco e a frase *"IUPI! ^_^*"

## 🤖 GIF
<p align="center">
  <img width="808" height="814" alt="jarvan2" src="https://github.com/user-attachments/assets/1ad362b1-8168-4665-87db-52e04ad12673" />







</p>

## 💻 Código Principal (`main.py`)
```python
import network                # Importa a biblioteca para controlar o rádio Wi-Fi do ESP32
import urequests              # Biblioteca que permite ao ESP32 acessar a internet (abrir sites, baixar dados de APIs em JSON).
import ntptime                # Ferramenta nativa do MicroPython que busca a hora oficial na internet
import time                   # Importa comandos de tempo (como pausas e leitura de relógio)
from machine import Pin, I2C  # Importa o controle dos pinos físicos da placa
import ssd1306                # Importa o "driver" (tradutor) que entende como acender a tela OLED

# --- CONFIGURAÇÕES INICIAIS ---
                      # Variáveis que guardam o nome e a senha da sua rede de casa
ssid = "CRIS.VIVO"    # onde você guarda as chaves da sua rede de casa.
password = "84792289" # onde você guarda as chaves da sua rede de casa.

# Link RAW do arquivo JSON que você vai criar no GitHub
url_jogos = "https://raw.githubusercontent.com/Ohlipeh/Jarvan/refs/heads/main/jogos.json" 


i2c = I2C(0, scl=Pin(22), sda=Pin(21))   # Configura a comunicação I2C nos pinos 22 (SCL/Clock) e 21 (SDA/Dados).
tela = ssd1306.SSD1306_I2C(128, 64, i2c) # Cria o objeto 'tela' com a resolução de 128x64 pixels e liga na comunicação I2C.

# --- 1. CONEXÃO WI-FI ---
station = network.WLAN(network.STA_IF) # Coloca o ESP32 em modo "Estação". Significa que ele vai se conectar a um roteador existente.
station.active(False)                  # O truque de reiniciar o módulo Wi-Fi.
time.sleep(1)                          # Pausa de 1 segundo.
station.active(True)
station.connect(ssid, password)        # Envia o comando real ao roteador pedindo acesso à internet.

# Feedback visual na tela enquanto tenta conectar.
tela.fill(0)                          # Apaga a tela inteira (0 = preto).
tela.text('WIFI...', 36, 4, 1)        # Escreve na faixa amarela (y = 4).
tela.text('Conectando...', 16, 32, 1) # Escreve na faixa azul (y = 32).
tela.show()                           # Envia as informações para a tela acender.

while not station.isconnected():      # Fica rodando em círculos aqui (esperando 1 seg) até o roteador liberar o acesso.
    time.sleep(1)

# --- 2. CONFIGURANDO DATA E CHECANDO A WEB ---
tela.fill(0)
tela.text('WIFI OK!', 32, 4, 1)      
tela.text('Consultando API', 4, 32, 1)
tela.show()

# Sincroniza o relógio
try:                 
    ntptime.settime() # Tenta puxar a hora oficial dos servidores mundiais (vem no formato UTC, que é a hora de Londres)
except:
    pass              # Se der erro (ex: servidor bloqueado), ignora e segue o código com a hora zerada

t_atual = time.time() - 10800                                                      # Pega o tempo recebido da internet e subtrai 10800 segundos (3 horas) para ficar no fuso do Brasil (UTC-3)
data_hora = time.localtime(t_atual)
hoje_str = "{:04d}-{:02d}-{:02d}".format(data_hora[0], data_hora[1], data_hora[2]) # Formata a data de hoje no padrão AAAA-MM-DD

dia_de_jogo = False
try:
    resposta = urequests.get(url_jogos) # Baixa o seu arquivo JSON da internet
    dados = resposta.json()
    
    if hoje_str in dados["jogos"]:      # Verifica se a string de hoje existe dentro da lista "jogos"
        dia_de_jogo = True
        
    resposta.close()
except:
    print("Erro ao buscar calendário.")

# --- 3. LOOP PRINCIPAL ---
while True:
    t_atual = time.time() - 10800                                   # Pega o tempo recebido da internet e subtrai 10800 segundos (3 horas) para ficar no fuso do Brasil (UTC-3)
    hora_atual = time.localtime(t_atual) 
    hora_str = "{:02d}:{:02d}".format(hora_atual[3], hora_atual[4]) # Formata a data de hoje no padrão AAAA-MM-DD

    if dia_de_jogo:
        tela.fill(0)                         # MODO FLAMENGO
        tela.text('FLAMENGO HOJE', 12, 4, 1) # Texto estático na faixa amarela
        
        # Desenha o escudo na parte azul (coordenadas Y maiores que 16)
        tela.rect(34, 20, 60, 38, 1)         # Desenha só a borda de fora do escudo
        tela.fill_rect(38, 24, 52, 30, 0)    # Preenche o miolo de preto
        tela.text('CRF', 52, 34, 1)          # Escreve a sigla dentro do miolo
        tela.show()                          # Acende a tela
        time.sleep(3)                        # Mantém essa tela por 3 segundos
        
        # Desenha a segunda tela de torcida
        tela.fill(0)
        tela.text('DIA DE JOGO!', 16, 4, 1)   
        tela.rect(10, 20, 108, 38, 1)        # Desenha uma moldura grande       
        tela.text('MENGAO', 40, 28, 1)
        tela.text('ACIMA DE TUDO', 12, 42, 1)
        tela.show()
        time.sleep(3)
        
    else:
                     # MODO DEV COM RELÓGIO
        tela.fill(0)  # Frame 1: JARVAN DE OLHOS ABERTOS
        tela.text('DEV MODE {}'.format(hora_str), 0, 4, 1) # Usa o 'format' para injetar a variável hora_str no meio do texto da faixa amarela
        
        # Desenha o Olho Esquerdo (Borda, miolo e pupila)
        tela.fill_rect(20, 22, 30, 26, 1)   # Borda branca do olho esquerdo
        tela.fill_rect(25, 27, 20, 16, 0)   # Miolo preto do olho esquerdo
        tela.fill_rect(32, 31, 6, 8, 1)     # Pupila branca no meio
        
        # Desenha o Olho Direito (Borda, miolo e pupila)
        tela.fill_rect(65, 22, 30, 26, 1)
        tela.fill_rect(70, 27, 20, 16, 0)
        tela.fill_rect(77, 31, 6, 8, 1)
        tela.text('BORA CODAR!', 20, 54, 1)
        tela.show()
        time.sleep(2) # Mantém os olhos abertos por 2 segundos
        
        # Frame 2: JARVAN PISCANDO (OLHOS FECHADOS)
        tela.fill(0)
        tela.text('DEV MODE {}'.format(hora_str), 0, 4, 1)
        
        # Em vez de retângulos, desenha apenas duas linhas finas horizontais para simular olhos fechados
        tela.line(20, 35, 50, 35, 1)        # Linha do olho esquerdo
        tela.line(65, 35, 95, 35, 1)        # Linha do olho direito
        tela.text('BORA CODAR!', 20, 54, 1)
        tela.show()
        time.sleep(0.3)                     # Mantém a piscada por apenas 0.3 segundos (bem rápido)
