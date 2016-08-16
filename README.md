# Intel-Edison-AutoStart
Procedimento para auto start de scripts em Python

# Torne seus scripst auto-executaveis
Com Intel Edison você pode desenvolver um
projeto e querer que ele seja executado automaticamente
assim que a placa for ligada, com nos Arduinos.

Existem diversas maneira de tornar um Script auto-executavel
em placas Linux embarcado, mas aqui vou
demonstrar de maneira bem simplificada como tornar
um Script Python auto executavel.

A maneira mais comum é através do systemd criando um .serviceque irá executar
o scripst.

Primeiro precisamos criar um script que usaremos no exemplo
crie uma pasta no diretorio /home/hoot

$mkdir python_examples
~# cd python_examples/

vamos criar nosso script de teste
~# vi hello_lcd.py


#! /usr/bin/python
import pyupm_i2clcd as grove_lcd
import mraa
import time

lcd = grove_lcd.Jhd1313m1(0,0x3E, 0x62)

lcd.setCursor(0,0)
lcd.setColor(255,0,0)
lcd.write('#IntelMaker')

lcd.setCursor(1,0)
lcd.write('Hello World!')

while True:
	lcd.setColor(0,255,0)
	time.sleep(1)
	lcd.setColor(0,0,255)
	time.sleep(1)
	lcd.setColor(255,0,0)
	time.sleep(1) 
	
para sair e salvar o arquivo:
'Esc', ':', 'w', 'q', 'Enter'	
	
Exemplo bem simples, facil de entender o que faz.

agora vamos dar permissão de execução do script
~# chmod +x hello_lcd.py

para testar:
~# ./hello_lcd.py

para encerrar o programa:
~# 'Ctrl' + 'c'

Criando o serviço:

Mude para o diretório /lib/systemd/system
~# cd /lib/systemd/system

crie o arquivo start-lcd.service
~# vi start-lcd.service

[Unit]
Description=Iniciar exemplo LCD
After=xdk-daemon.service

[Service]
ExecStart=/home/hoot/python_examples/hello_lcd.py
Restart=always
RestartSec=10s

[Install]
Wanted=multi-user.target 

Salve o arquivo, 
conceda permissão:
~# chmod +x start-lcd.service

Atualize o systemctl para que ele reconheça seu serviço:
~# systemctl daemon-reload

Ative seu serviço, para ele ser executado durante o boot
~# systemctl enable start-lcd.service
Verificar se o serviço está ativo:
~# systemctl --no-pager list-units-files start-lcd.service

Desativar o serviço:
~# systemctl disable start-lcd.service

Parar o serviço, mas sem desativar o auto-start
~# systemctl stop start-lcd.service

Iniciar o serviço:
~# systemctl start start-lcd.service

Remover o serviço por completo
~#systemctl stop start-lcd.service
~#systemctl disable start-lcd.service
~# rm /lib/systemd/system/start_lcd.service
