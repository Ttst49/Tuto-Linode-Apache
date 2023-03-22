Aller sur le site https://cloud.linode.com et après vous être connecté/créer un compte
aller sur les options de création et choisissez linode
Choisir Ubuntu 22.04,l'option désirée ainsi qu’un mot de passe à conserver précieusement
Vous pouvez renommer votre Linode pour mieux vous repérer

Lancer le Terminal et écrire la commande:
« ssh root@{adresseIpDuServeur} »
puis entrez votre mot de passe

Une fois connecté, Vous devez d’abord lancer la commande:
« sudo apt update »
puis
« sudo apt upgrade »


Une fois cela fait, vous devez en priorité installer neovim ou tout autre logiciel pour écrire du texte pour plus de facilité d’utilisation avec la commande:
« sudo apt install {logicielVoulu} »

Nous voulons utiliser un serveur Apache, nous devons l’installer avec la commande:
« sudo apt install apache2 »

Nous devons maintenant autoriser les serveurs apache par notre firewall, pour cela nous utilisons la commande:
« ufw app list », cette commande nous renvoie les applications qui sont disponibles pour notre serveur. Il devrait y avoir:
-Apache
-Apache Full
-Apache Secure
-OpenSSH
Nous devons autoriser les applications Apache, Apache Full ainsi que OpenSSH(Ne surtout pas oublier OpenSSH car il permet la connexion à notre serveur; si vous l’oubliez, vous devrez recommencer du début)
Pour autoriser des applications nous faisons la commande:
« ufw allow {nomApplication} », dans le cas d’une application en deux mots comme Apache Full il faut mettre le nom de l’application entre guillemets comme suit:
« ufw allow ‘Apache Full’ »
Finissez ces manipulations en démarrant votre firewall avec cette commande:
« ufw enable »


Cela étant fait, nous devons vérifier l’existant, si vous avez un nom de domaine vous devez vous souvenir son nom exacte extension incluse ainsi que l’adresse mail associée à ce nom de domaine, allez sur le compte linode associé à votre serveur et entrez dans la section « Domains ».
À partir d’ici, vous devez créer un domaine, entrez votre nom de domaine extension incluse et votre adresse mail liée a ce nom de domaine. Depuis cet endroit, remplissez les NS records si ils ne le sont pas, privilégiez ceux de linode(ns1.linode.com,ns2.linode.com…) Continuez plus bas vers les A/AAAA Record et ajoutez en un ne remplissez pas le premier input et indiquez l’adresse ip du serveur dans le second, une fois cela fait: créez en un nouveau et cette fois-ci écrivez www dans le premier input, l’adresse ip du serveur dans le second et validez. Cette partie permet d’obtenir un certificat et d'obtenir un site en https.


Une fois cela effectué, nous devons savoir où sont nos fichiers de configurations de Apache ainsi que les dossiers où placer nos fichiers tels que HTML,PHP,CSS… 

Pour les fichiers qui seront lus par notre serveur ils se situent à:
« /var/www/html/ » pour y accéder, la commande est:
« cd /var/www/html »

Par défaut, cet emplacement possède un simple fichier « index.html » que nous pouvons supprimer via la commande:
« rm index.html », suite à cela nous allons regarder comment associer un compte github et copier un dépôt déjà existant.


*Si vous désirez partir de rien vous devez faire la commande:*
*« touch {nomDuFichier} », cette commande permet de créer un fichier si vous souhaitez partir de zéro, dans le cas d’un fichier html vous pouvez écrire: « touch index.html » puis « nvim index.html » pour pouvoir modifier ce fichier.*


Pour associer un compte github, vous devez copier ces lignes de commande dans votre terminal:
« type -p curl >/dev/null || sudo apt install curl -y
curl -fsSL https://cli.github.com/packages/githubcli-archive-keyring.gpg | sudo dd of=/usr/share/keyrings/githubcli-archive-keyring.gpg \
&& sudo chmod go+r /usr/share/keyrings/githubcli-archive-keyring.gpg \
&& echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" | sudo tee /etc/apt/sources.list.d/github-cli.list > /dev/null \
&& sudo apt update \
&& sudo apt install gh -y »

Continuez avec la commande:
« gh auth login », cette commande vous demandera plusieurs informations, choisissez dans l’ordre:
-Github.com
-HTTPS
-Yes
-Login with a browser
Une fois validé, vous devrez ouvrir le lien:
« https://github.com/login/device »
puis rentrez le code présent dans votre terminal pour terminer la connexion

À partir de là, allez sur le dépôt que vous voulez copier et appuyez sur le bouton ‘Code’ et copiez la ligne de commande qui vous est proposé, une fois collée dans votre terminal, votre dépôt va être copié à l’emplacement « /var/www/html/ »
n’hésitez pas à déplacez les fichiers dans des dossiers à la racine de « /var/www/html » avec des commandes tels que:
« mv {nomFichier/nomDossier} /var/www/html»

Une fois tous vos fichiers au bon endroit vous pouvez avancer à l’étape suivante


Pour les fichiers d’apache, ils se situent à:
« /etc/apache2/ » pour y accéder, la commande est:
« cd /etc/apache2/ »


Nous allons devoir toucher aux fichiers contenus dans le dossier sites-available qui est un dossier présent dans /etc/apache2/
à l’intérieur du dossier sites-available nous trouvons 
—> 000-default.conf
—> default-ssl.conf

Nous commençons par modifier 000-default.conf:
utilisez la commande liée à votre éditeur de texte, ici neovim:
« nvim 000-default.conf » vous devriez voir deux lignes nommées respectivement:
—>ServerAdmin
—>DocumentRoot

Ne touchez pas à DocumentRoot, passez en mode insertion en appuyant sur la touche ‘insert’ ou bien sur la touche ‘i’ Changez la valeur ServerAdmin de webmaster@localhost en votre adresse mail liée à votre nom de domaine, ajoutez par ailleurs une ligne en dessous de ServerAdmin que vous nommerez « ServerName » et dont la valeur sera votre nom de domaine extension inclus, ajoutez ensuite une dernière ligne en dessous, nommée « ServerAlias » et dont la valeur sera 
« www.{votreNomdeDomaine} » extension inclus.


Vous pouvez partir de ce fichiers, appuyez sur la touche echap puis écrivez « :w » et pour finir « :exit » cela sauvegardera et quittera le fichier

Ouvrez le second fichier nommé default-ssl.conf en utilisant la même méthode:
« nvim default-ssl.conf »
Vous devriez voir les deux même lignes que précédemment, vous devez faire les même étapes que pour le fichier précédent, répétez la manoeuvre une seconde fois pour passer à la suite de la démarche


Nous considérons ici que vous avez modifié les deux fichiers et que vous êtes de retour sur le terminal à l’adresse « root@localhost:/etc/apache2/ »

Lancez les commandes ci-dessous dans le même ordre précis:
—> « a2dissite 000-default.conf »
—> « systemctl reload apache2 »
—> « snap install certbot --classic »
—> « a2ensite 000-default.conf »
—> « a2ensite default-ssl.conf »
—> « systemctl reload apache2 »


À partir d’ici, nous allons installer php,mysql ainsi que phpmyadmin:

Pour installer php, lancez la commande:
« sudo apt install php »
normalement, cela devrait installer la dernière version de php


Pour installer mysql, lancez la commande:
« sudo apt install mysql-server »
Une fois cela fait, vous devez écrire une commande qui va nous lancer un terminal SQL:
« mysql -u root »
Nous allons créer un compte dans la base de donnée avec la commande:
« ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY {votremdpSQL}; »
N’oubliez pas le point virgule car il est obligatoire pour amorcer une commande, choisissez un mot de passe et retenez le/notez le

une fois cela fait, quittez le terminal SQL en écrivant « \q » par la suite, vous devez lancer l’installation de la base de donnée en utilisant la commande: « mysql_secure_installation »
Validez ou refusez les options par rapport à votre besoin, vos envies ou la demande qu’on vous à faite


Pour installer phpmyadmin, lancez la commande:
« sudo apt install phpmyadmin »
validez l’option apache2 avec espace et validez ensuite, renseignez votre mot de passe et confirmez le


Pour finir, utilisez la commande:
« certbot --apache »
Écrivez votre adresse mail avec laquelle vous avez votre nom de domaine puis, validez la première demande concernant l’acceptation des conditions d’utilisation et refusez la seconde concernant les utilisation de votre adresse mail, choisissez ensuite le numéro 1 qui correspond à votre ServerName

Pour finir, lancez la commande:
« sudo apt install libapache2-mod-php »
