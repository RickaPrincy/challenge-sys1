# **Challenge Réseau de Root Me**

## **1. FTP - Authentification**

Pour obtenir le mot de passe, on peut utiliser Wireshark pour analyser les données du fichier `ch1.pcap`. Pour cela, on va simplement filtrer les paquets par FTP. 

Ensuite, nous pouvons analyser la partie "information" des paquets. Il est très facile de le reconnaître avec :

```
    11	7.639420	10.20.144.150	10.20.144.151	FTP	81	Request: PASS *******
```

Nous pouvons également cliquer sur l'un des paquets de protocole FTP et le suivre.

> Le mot de passe est donc : ********

<hr>

## **2. TELNET - Authentification**

Pour ce challenge, nous allons faire la même chose :
- Ouvrir le fichier `ch2.pcap` avec Wireshark
- Filtrer par **telnet**
- Suivre un des paquets de protocole **telnet**
- C'est tout, le mot de passe sera affiché

## **3. Ethernet Trame**

Pour ce challenge, on nous donne une suite de code hexadécimal. Pour trouver le mot de passe, nous allons convertir le code hexadécimal en texte. Pour cela, nous avons deux méthodes :
- La première consiste à trouver un site qui le fait pour nous
- La deuxième consiste à utiliser des commandes Linux (donc c'est seulement pour ceux qui sont sur Linux)

**Comment faire avec la deuxième option ?**

C'est très simple, nous allons simplement utiliser la commande `xxd`.

Donc, nous pouvons créer un fichier et y mettre tout le code hexadécimal (supposant que ce fichier sera nommé `myCode.txt`), et nous allons simplement exécuter la ligne de commande suivante :

```sh
    xxd -r -p myCode.txt
```
- **-r** : pour faire le revert car la première fonctionnalité de xxd est de convertir un texte en hexadécimal.
- **-p** : pour dire à xxd de considérer le code hexadécimal comme un code hexadécimal sans espace.

Et c'est tout ! Nous avons une vue textuelle du code hexadécimal en texte. Cependant, le mot de passe affiché en texte est encore encodé en `base64`.

**Note** : Vous obtiendrez le même résultat si vous utilisez des sites pour convertir le code hexadécimal en texte.

**Comment avons-nous pu savoir que c'est encodé en base64 et comment le décoder ?**

- Nous avons pu savoir que c'est encodé en base64 grâce à la présence de "==" à la fin du mot de passe.
- Pour le décoder, nous pouvons utiliser à nouveau les deux options précédentes : trouver un site ou taper des lignes de commande.

La commande qui peut le décoder est **base64**.

Nous pouvons donc taper, par exemple :

```sh
    echo LeMotDePasseEnBase64Ici== | base64 -d
```

- **-d** : on utilise -d pour décoder. Sans cette option, nous encoderions le mot de passe à nouveau.

**Après avoir décoder le mot de passe avec des sites ou avec cette méthode, vous devriez avoir le mot de passe.**.


## **4. Twitter- Authentification**

Cette épreuve est un peu comme la combinaison des épreuves précédentes.
On nous donne un fichier `ch3.pcap` qui contient qu'il seul paquet.

Donc, il n'est pas nécessaire de le suivre car il est unique (on peut le faire, mais cela modifierait un peu le timing). 

- Nous pouvons directement cliquer dessus pour voir les détails de ce paquet en bas, et nous allons nous concentrer sur l'option **Hypertext Transfer Protocol**.

- En cliquant sur cette option, nous pouvons voir tous les détails de celle-ci, et ce qui nous intéresse est l'`Authorization`, qui ressemble à quelque chose comme : `Basis ...... =\r\n`.

Donc pour nous, c'est clair, c'est encodé en base64, ce qui nous amène à taper la ligne de commande suivante pour le décoder (ou bien, à utiliser un site qui le décryptera pour nous) :

```sh
    echo MotDePasseIci= | base64 -d
```

Après cela, il nous affichera le mot de passe.

## **5. Bluetooth - Fichier inconnu**
Voyons un peut le sujet d'abord : 

```sh
#Votre ami travaillant à l’ANSSI a récupéré un fichier illisible dans l’ordi d’un hacker. Tout ce qu’il sait est que cela provient d’un échange entre un ordinateur et un téléphone. A vous d’en apprendre le plus possible sur ce téléphone.

"La réponse est le hash SHA1 de la concaténation de l’adresse MAC (en majuscules) et du nom du téléphone."

#Exemple :
#AB:CD:EF:12:34:56monTelephone -> 836eca0d42f34291c5fefe91010873008b53c129
```

Là on a présque notre réponse ( XD ), le sujet dit : `La réponse est le hash SHA1 de la concaténation de l’adresse MAC (en majuscules) et du nom du téléphone.`

Ce qui signifie que la réponse est simplement la **hachage cryptographique** de  `AB:CD:EF:12:34:56monTelephone` (par exemple).

- Ouvrons à nouveau wireshark pour analyser ce qu'il y a dans `ch18.pcap` et cliquant un des paquets. 

Sur wireshark, on peut constater un onglet **wireless**, donc cliquant dessus et puis l'option **Equipements bluetooth**, nous avons accès à l'adresse MAC et au nom du téléphone.

Utilisons la ligne de commande suivante ou un site pour créer un hachage cryptographique de l'adresse MAC avec le nom du téléphone

```sh
echo -n 0C:B3:19:B9:4F:C6GT-S7390G | sha1sum 
```
- **-n** est utilisé pour dire à echo de ne pas mettre un retour à la ligne, sinon ce retour à la ligne sera également haché par sha1sum.

Et c'est déjà fini, nous avons notre mot de passe.

## **6. SIP - Authentification**

Pour cette challenge, il suffit d'analyser ce qui est écrit
`...."PLAIN"****`.

Quoi ? oui... vous avez raison, le mot de passe est ce fameux chiffre derrière le "PLAIN".

## **7. DNS - Tranfèrt de zone**

Voici le sujet : 
```sh
#Un administrateur peu consciencieux a mis en place un service DNS pour le domaine "ch11.
#challenge01.root-me.org"...

Paramètres de connexion au challenge
Hôte	challenge01.root-me.org
Protocole	DNS
Port	54011
```
Pour cette challenge, il suffit d'utiliser la commande `dig` pour comme suite : 
```sh
    dig @challenge01.root-me.org -p 54011 ch11.challenge01.root-me.org txt
```.
