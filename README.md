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
from machine import Pin, I2C # Controla os pinos físicos / Gerencia o protocolo.
from time import sleep # Controla o tempo e as pausas.
import ssd1306 # Driver específico

# Configura a comunicação I2C
i2c = I2C(0, scl=Pin(22), sda=Pin(21)) # Inicialização Física
tela = ssd1306.SSD1306_I2C(128, 64, i2c) # Cria o objeto chamado tela.

# --- APRESENTAÇÃO ---
tela.fill(0) # Limpa a memória interna da tela.
tela.text('Ola!', 48, 20) # Desenha um texto na memória da tela.
tela.text('Sou JARVAN', 24, 35) # Desenha um texto na memória da tela.
tela.text('FLAMENGUISTA', 20, 50) # Desenha um texto na memória da tela.
tela.show() # comando para exibir algo na tela.
sleep(3)  # Fica na tela por 3 segundos antes de começar as carinhas.

# --- LOOP DAS CARINHAS (Roda para sempre) ---
while True:
    # --- 1. CARINHA BRAVA ---
    tela.fill(0) # Limpa a memória interna da tela.
    tela.fill_rect(20, 22, 30, 26, 1) # Desenha um retangulo preenchido ex: (x, y, largura, altura, cor)
    tela.fill_rect(25, 27, 20, 16, 0) # Desenha um retangulo na cor preta.
    tela.fill_rect(32, 31, 6, 8, 1)
    tela.fill_rect(65, 22, 30, 26, 1)
    tela.fill_rect(70, 27, 20, 16, 0)
    tela.fill_rect(77, 31, 6, 8, 1)
    tela.line(20, 18, 50, 24, 1) # Desenha uma linha reta ligando um ponto inicial. a um ponto final.
    tela.line(65, 24, 95, 18, 1)
    tela.text('NAO SOU VASCO', 20, 52) # Desenha um texto na memória da tela.
    tela.show() # comando para exibir algo na tela.
    sleep(2)  # Fica na tela por 2 segundos antes de começar as carinhas.
    
    # --- 2. CARINHA TRISTE ---
    tela.fill(0)
    tela.line(20, 25, 50, 35, 1)#Desenha uma linha reta ligando um ponto inicial(x1, y1) a um ponto final(x2, y2)
    tela.line(65, 35, 95, 25, 1)
    tela.line(20, 26, 50, 36, 1)
    tela.line(65, 36, 95, 26, 1)
    tela.text('MUAAAH :(', 32, 52)
    tela.show()
    sleep(2)
    
    # --- 3. CARINHA FELIZ ---
    tela.fill(0)
    tela.line(20, 30, 35, 22, 1)
    tela.line(35, 22, 50, 30, 1)
    tela.line(65, 30, 80, 22, 1)
    tela.line(80, 22, 95, 30, 1)
    tela.text('SOU FLAMENGO! ^_^', 20, 52) # Desenha um texto na memória da tela.
    tela.show() # comando para exibir algo na tela.
    sleep(2) # Fica na tela por 2 segundos antes de começar as carinhas.
