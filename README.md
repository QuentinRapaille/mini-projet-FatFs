# Mini-projet

Le projet initial consistait à concevoir un lecteur MP3. Toutefois, j’ai rencontré plusieurs difficultés lors de l’utilisation des bibliothèques disponibles sur GitHub, ainsi que dans l’adaptation des projets de démonstration proposés par MCUXpresso. Cependant, ces pistes restent très intéressantes pour celles et ceux qui souhaiteraient approfondir ce sujet par la suite. Par manque de temps, je me suis finalement orienté vers la lecture de fichiers audio au format .wav. Le mini-projet s’appuie en grande partie sur le travail réalisé dans le cadre du TP FatFs du module UE442.


# Lecture de fichier .wav

La première étape consiste à lire un fichier audio stocké sur une carte SD. Cette dernière, formatée en FAT32, est accessible via la bibliothèque FatFs. Pour l'utiliser, il faut l'activer dans l'IOC (Middleware --> FatFs) puis activer le contrôleur de la carte SD (Connectivity --> SDMMCx). Il faut bien faire attention à ce que le mode de communication choisi reste cohérent tout au long de la configuration (on utilise ici le mode 4bits). \
Un fichier .wav est séparé en deux sections : en-tête + données audio. Les 44 premiers octets forment l'en-tête décrit de manière détaillée sur ce document : https://ericecmorlaix.github.io/pdf/FormatWAV.pdf . On y trouve entre-autre le format, la fréquence d'échantillonage ou encore la taille des données audio. Ce qui suit sont les données audio à lire. 


# Lecture de la musique

Pour assurer une lecture continue et fluide des données audio, un double buffer a été mis en place, alimenté par deux canaux DMA. Cette technique permet de lire les blocs de données en arrière-plan, pendant que l’autre buffer est en cours de lecture, réduisant ainsi la charge sur le processeur et évitant les interruptions audibles. En effet, les transferts grâce aux DMA sont déclenchés automatiquement et permettent de remplir les buffers sans intervention explicite du CPU, ce qui optimise les performances du système. \
Lorsque ces données sont lues, le périphérique SAI permet de les transmettre par I2S vers le codec audio WM8994. Ce dernier les convertit en signal analogique pour pouvoir finalement écouter la musique à l'aide du haut-parleur. Pour cela, le SAI doit être activé en tant que maître. Le codec, lui, est configuré en activant le périphérique I2C3. 
