# -*- coding : utf8 -*-
# NadHat_MK2.py
# https://www.python-exemplarisch.ch/index_en.php?inhalt_links=navigation_en.inc.php&inhalt_mitte=raspi/en/gsm.inc.php
# NadHAT MK2 sur boitier Pironman
# Attent un prénom puis une couleur
# Affiche la couleur dans le Pironman

import serial
import io
import time
import os
import subprocess
import sys
import shlex
from gpiozero import CPUTemperature

liste_couleurs = ['rouge', 'vert', 'bleu', 'jaune', 'mauve', 'blanc']
liste_hexa     = ['FF000', '00FF00', '0000FF', 'FFFF00', 'FF00FF', 'FFFFFF']

tel_carte = "+33656737913"

SERIAL_PORT = "/dev/ttyUSB2"  

signal = serial.Serial(
    port=SERIAL_PORT,
    baudrate=9600,
    bytesize=8,
    parity='N',
    timeout=1,
    stopbits=1,
    rtscts=False,
    dsrdtr=False
)
signal_text = io.TextIOWrapper(signal, newline='\r\n')

# Configurer la carte pour les SMS
# Passer en mode ECHO OFF
print("\r\nMode ECHO OFF")
signal.write(str.encode('ATE0\r'))
rep = signal.read(signal.inWaiting())
print(rep)

# Stocker les SMS en mémoire flash
print("\r\nStocker les SMS dans la mémoire flash")
signal.write(str.encode('AT+CPMS="ME", "ME", "ME"\r'))
rep = signal.read(signal.inWaiting())
print(rep)

signal.write('AT+CSCS="IRA"\r'.encode()) # mode IRA
time.sleep(1)
rep = signal.read(signal.inWaiting())
print(rep)

signal.write("AT+CMGF=1\r\n".encode()) # set to text mode
time.sleep(1)
rep = signal.read(signal.inWaiting())
print(rep)

signal.write('AT+CMGD=1,4\r\n'.encode()) # delete all SMS
time.sleep(1)
rep = signal.read(signal.inWaiting())
print(rep)

# =======================================================================
# On envoie un SMS sur la carte elle même pour récupérer l'heure GSM
# et mettre le Raspberry Pi à l'heure s'il n'est pas connecté à un réseau
# Envoyer SMS au N° de la carte
print ('AT+CMGS="'+ tel_carte + '"')
signal.write(('AT+CMGS="' + tel_carte +'"\r\n').encode())
time.sleep(3)
rep = signal.read(signal.inWaiting())
print("Apres CGMS  : \r\n",rep.decode())
time.sleep(3)
sms = "SMS\r\n"
signal.write(sms.encode())
time.sleep(1)
rep = signal.read(signal.inWaiting())
print("SMS Carte envoyé  : \r\n",rep.decode())
signal.write(chr(26).encode())
time.sleep(1)
rep = signal.read(signal.inWaiting())
print("Fin SMS et OK  : \r\n",rep.decode())
time.sleep(1)
# On lit le SMS
signal.write("AT+CMGR=1\r".encode()) 
time.sleep(1)
reply = signal.read(signal.inWaiting())
print ("SMS sur la carte reçu : \n\r", reply)
time.sleep(2)
# Extraire les données du message
data = reply.decode()
# Séparer en fonction de CR LF
decoupe=(data.split("\r\n"))
#print ("Decoupe : ", decoupe)
message = decoupe[2]
print("Message : ", message)
print("Message Type : ",type(message))
#Telephone de l'appelant
tel = data[23:35]
#print(type(tel))
print ("Téléphone : ", tel)
# Date fournie par le réseau
date = data[41:58]
print("Date : ", date)
#Créer chaine date_heure
an= data[41:43]
print(type(an))
mois=data[44:46]
jour=data[47:49]
heure=data[50:52]
minute=data[53:55]
seconde=data[56:58]
# Traitement de la date et de l'heure
date_sms = jour + " " + mois + " 20" + an
heure_sms = heure + ":" + minute + ":" + seconde
print("Date du SMS : ", date_sms)
print("Heure du SMS : ", heure_sms)
# Effacer tous les SMS
signal.write('AT+CMGD=1,4\r\n'.encode()) # delete all SMS
time.sleep(1)
rep = signal.read(signal.inWaiting())
print("Efface les SMS  : \r\n",rep.decode())
# Mettre l'OS à l'heure
# Créer la chaine date Heure
date_heure = "'20"+an+"-"+mois+"-"+jour+" "+heure+":"+minute+":"+seconde+"'"
# Créer la chaine date Heure
#date_heure = "'2023-01-17 11:00:00'"
chaine = "sudo date -s " + date_heure
print ("Chaine : ", chaine)
# Arrêter la mise à l'heure NTP
print("Arret NTP")
subprocess.run(["sudo", "timedatectl", "set-ntp", "0"])
time.sleep(1)
print("Mofifier heure système ")
#subprocess.call(shlex.split("sudo date -s '2023-01-17 19:00:00'"))
subprocess.call(shlex.split(chaine))
print ("Heure modifiée ")
time.sleep(1)


# On se met en attente du SMS qui va déclencher le programme
reply = signal.read(signal.inWaiting()) # Clean bu
print ("\r\n=============== EN ATTENTE DE SMS... ==============================")

while True:

    reply = signal.read(signal.inWaiting())
    #print(reply, len(reply))
    # Est-ce qu'on a reçu un SMS ?
    if len(reply) != 0:
        signal.write("AT+CMGR=1\r".encode()) 
        time.sleep(1)
        reply = signal.read(signal.inWaiting())
        print ("SMS reçu : \n\r", reply)
        time.sleep(2)
        
        # Extraire les données du message
        data = reply.decode()
        # Séparer en fonction de CR LF
        decoupe=(data.split("\r\n"))
        #print ("Decoupe : ", decoupe)
        message = decoupe[2]
        print("Message : ", message)
        print("Message Type : ",type(message))
        #Telephone de l'appelant
        tel = data[23:35]
        #print(type(tel))
        print ("Téléphone : ", tel)
        # Date fournie par le réseau
        date = data[41:58]
        print("Date : ", date)
        #Créer chaine date_heure
        an= data[41:43]
        print(type(an))
        mois=data[44:46]
        jour=data[47:49]
        heure=data[50:52]
        minute=data[53:55]
        seconde=data[56:58]
        # Now create new time string in the form MMDDhhmmYYYY for the date program
        date_sms = jour + " " + mois + " 20" + an
        heure_sms = heure + ":" + minute + ":" + seconde
        print("Date du SMS : ", date_sms)
        print("Heure du SMS : ", heure_sms)

        # Effacer tous les SMS
        signal.write('AT+CMGD=1,4\r\n'.encode()) # delete all SMS
        time.sleep(1)
        rep = signal.read(signal.inWaiting())
        print("Efface les SMS  : \r\n",rep.decode())
        
        # Enlever les espaces avant/après message
        message = message.strip()
        #print(type(message))
        # Envoyer la réponse au N° appelant
        print ('AT+CMGS="'+ tel + '"')
        signal.write(('AT+CMGS="' + tel +'"\r\n').encode())
        time.sleep(3)
        rep = signal.read(signal.inWaiting())
        print("Apres CGMS  : \r\n",rep.decode())
        time.sleep(3)
        choix_couleur = "\r\nChoisis ta couleur : \r\n Rouge, vert, bleu, jaune, mauve, blanc et envoie la par SMS"
        retour = "Bonjour " + message + choix_couleur +"\r\n"
        print ("Retour : ", retour)
        signal.write(retour.encode())
        time.sleep(1)
        rep = signal.read(signal.inWaiting())
        print("Texte retour envoyé  : \r\n",rep.decode())
        
        signal.write(chr(26).encode())
        time.sleep(1)
        rep = signal.read(signal.inWaiting())
        print("Fin SMS et OK  : \r\n",rep.decode())
        time.sleep(1)
        
        # Attendre la réponse +CMGR
        # Effacer tous les SMS
        signal.write('AT+CMGD=1,4\r\n'.encode()) # delete all SMS
        time.sleep(1)

        
        # Attendre la réponse du participant
        #print(reply, len(reply))
        # Est-ce qu'on a reçu un SMS ?
        reply = signal.read(signal.inWaiting())
        print(type(reply))
        print("Reply avant le while : ",reply)
        while bytes("+CMGR", 'utf_8') not in reply :
            signal.write("AT+CMGR=1\r".encode()) 
            time.sleep(1)
            reply = signal.read(signal.inWaiting())
            data = reply.decode()
            decoupe=(data.split("\r\n"))
            time.sleep(1)

        print ("Decoupe : ", decoupe, "\r\nLongueur de Découpe : ", len(decoupe))
        for morceau in decoupe :
            print("Decoupe  : ", morceau)
        # Si la ligne 2 contient CMTI lire ligne 4
        # Sinon lire ligne 2
        """if len(decoupe) == 8 :
            couleur = decoupe[4]
        if len(decoupe) == 6 :
            couleur = decoupe[2]
        if len(decoupe) == 7 :
            couleur = decoupe[3]"""
        if "CMTI" in decoupe[1] :
            couleur = decoupe[4]
        else :
            couleur = decoupe[2]
        print("Couleur reçue  : \r\n",couleur)
        # Vérifier si la couleur est présente
        rep_couleur = couleur.strip()  # Supprimer les espaces avant/après
        rep_couleur = rep_couleur.lower()  # mettre la réponse en minuscules
        
        # Effacer tous les SMS
        signal.write('AT+CMGD=1,4\r\n'.encode()) # delete all SMS
        time.sleep(1)

        
        if rep_couleur in liste_couleurs :
            # Lire la température CPU
            cpu = CPUTemperature()
            print("Température : ", cpu.temperature)
            
            # La couleur existe dans la liste
            print("La couleur existe : ", rep_couleur)
            # Répondre que la couleur est OK
            # Préparer la date
            date_sms =  jour + "/" + mois + "/" + "20" + an
            heure_sms = heure + ":" + minute
            date = date_sms + heure_sms
            degre = "C"

            chaine = date_sms + "\r\n" + heure_sms + "\r\n" +  "OK, je passe en couleur " + rep_couleur + "\r\n" + "La température du CPU est : " + "\r\n" + str(int(cpu.temperature)) + degre
            
            # Envoyer la réponse au N° appelant
            print ('AT+CMGS="'+ tel + '"')
            signal.write(('AT+CMGS="' + tel +'"\r\n').encode())
            time.sleep(3)
            rep = signal.read(signal.inWaiting())
            print("Apres CGMS  : \r\n",rep.decode())
            time.sleep(3)
            signal.write(chaine.encode())
            time.sleep(1)
            rep = signal.read(signal.inWaiting())
            print("Texte confirmation envoyé  : \r\n",rep.decode())
            signal.write(chr(26).encode())
            time.sleep(1)
            rep = signal.read(signal.inWaiting())
            print("Fin confirmation  : \r\n",rep.decode())
            time.sleep(1)

            
            # Récupérer l'indice de la couleur dans la liste
            position = liste_couleurs.index(rep_couleur)
            print("Position de la couleur : ", position)
            # Récupérer le code hexa correspondant
            hexa = liste_hexa[position]
            print("Code hexa : ", hexa)
            # Modifier la couleur des LED
            subprocess.run(["pironman", "-rc", hexa])
            
            # Vider le buffer
            rep = signal.read(signal.inWaiting())
            time.sleep(3)
            rep = signal.read(signal.inWaiting())
            print("Contenu à la fin des op : ", rep)
            # Recommencer
            print ("\r\n======================= ENVOYEZ VOTRE PRENOM ==============================\n\r" )
        else:
            # La couleur n'existe pas
            # On envoie un SMS pour dire que ce n'est pas bon !
            print ('AT+CMGS="'+ tel + '"')
            signal.write(('AT+CMGS="' + tel +'"\r\n').encode())
            time.sleep(1)
            rep = signal.read(signal.inWaiting())
            print("Apres CGMS  : \r\n",rep.decode())
            erreur_couleur = "\r\nCette couleur n'existe pas dans la liste !"
            retour = "Désolé " + message + ", il faut recommencer à zéro en envoyant ton prénom !" +"\r\n"
            print ("Retour erreur : ", retour)
            signal.write(retour.encode())
            time.sleep(1)
            rep = signal.read(signal.inWaiting())
            print("Texte erreur envoyé  : \r\n",rep.decode())
            
            signal.write(chr(26).encode())
            time.sleep(1)
            rep = signal.read(signal.inWaiting())
            print("Fin SMS et OK  : \r\n",rep.decode())
            time.sleep(1)
        
        
        


