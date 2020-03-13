BATAILLION Alice  
MOREAU Marianne  
4 ETI  

# TP 4 - Utilisateurs, groupes et permissions

## Exercice 1. Gestion des utilisateurs et des groupes

**1. Commencez par créer deux groupes groupe1 et groupe2**  

`sudo addgroup groupe1`
Pour vérifier que le groupe a bien été créé :  
`cut -d: -f1 /etc/group`

**2. Créez ensuite 4 utilisateurs u1, u2, u3, u4 avec la commande useradd, en demandant la création de
leur dossier personnel et avec bash pour shell.**
`sudo useradd -m u1`  
L'option -m permet de créer un dossier personnel à l'utilisateur.  
Pour vérifier que l'utilisateur a bien été créé :  
`cut -d: -f1 /etc/passwd`

**3. Ajoutez les utilisateurs dans les groupes créés :**  
**- u1, u2, u4 dans groupe1**  
**- u2, u3, u4 dans groupe2**  

`sudo gpasswd -a u1 groupe1` ajoute u1 au groupe1  
Pour consulter les groupes auxquels appartient un utilisateur : `groups u1`

**4. Donnez deux moyens d’afficher les membres de groupe2**  

`sudo grep groupe2 /etc/group | cut -d: -f4` : va chercher la ligne du groupe recherché dans le fichier des groupes, et sélectionne la colonne qui correspond aux noms des utilisateurs appartenant à ce groupe.  
`sudo grep groupe2 /etc/gshadow | cut -d: -f4` : de même dans le fichier gshadow qui contient également ces informations.  

**5. Faites de groupe1 le groupe propriétaire de /home/u1 et /home/u2 et de groupe2 le groupe propriétaire
de /home/u3 et /home/u4**  

`sudo chown -R u1:groupe1 /home/u1`
Dans le dossier /home, faire `ls -l` pour vérifier que le groupe propriétaire de chaque dossier personnel d'utilisateur est bien celui attribué.  

**6. Remplacez le groupe primaire des utilisateurs :**  
**- groupe1 pour u1 et u2**  
**- groupe2 pour u3 et u4**  

`sudo usermod -g groupe1 u1` remplace le groupe primaire de l'utilisateur u1 (qui était par défaut le groupe u1) en groupe1.
On peut vérifier que le groupe primaire a bien été modifié en faisant : `groups u1` où groupe1 ets le premier groupe à apparaître. 

**7. Créez deux répertoires /home/groupe1 et /home/groupe2 pour le contenu commun aux groupes, et
mettez en place les permissions permettant aux membres de chaque groupe d’écrire dans le dossier
associé.**  
`sudo mkdir /home/groupe1`  
`sudo chown -R root:groupe1 /home/groupe1`On indique quels utilisateurs ont droit sur le dossier groupe1 : il s'agit de l'utilisateur root et du groupe d'utilisateurs groupe1.  
`sudo chmod 775 /home/groupe1` On change les droits des différents utilisateurs : le groupe groupe1 peut maintenant écrire dans le dossier.  

**8. Comment faire pour que, dans ces dossiers, seul le propriétaire d’un fichier ait le droit de renommer
ou supprimer ce fichier ?**  

`chmod +t /home/groupe1` permet d'activer le sticky bit. Ce bit indique que dans ce dossier, seuls le propriétaire d’un fichier, le propriétaire du dossier ou root ont le droit de renommer ou supprimer ce fichier.  

**9. Pouvez-vous vous connecter en tant que u1 ? Pourquoi ?**  

Non, nous n'avons pas attribué de mot de passe au compte de l'utilisateur u1. Le compte est donc inactif jusqu'à attribution d'un mot de passe.

**10. Activez le compte de l’utilisateur u1 et vérifiez que vous pouvez désormais vous connecter avec son
compte.**  

`sudo passwd u1` permet d'attribuer un mot de passe au compte de l'utilisateur u1. Nous avons attribué le mot de passe : u1.  
Avec `su u1` on se connecte en tant que u1.

**11. Quels sont l’uid et le gid de u1 ?** 

Avec `id` on obtient uid=1001 et gidup=1001.  

**12. Quel utilisateur a pour uid 1003 ?** 

`sudo cut -d: -f1-3 /etc/passwd | grep 1003 | cut -d: -f1` : permet de récupérer la ligne qui comprend un uid de 1003 et renvoie la première colonne de cette ligne (qui correspond au nom d'utilisateur).  
up
L'utilisateur u3 a pour uid 1003.
up
**13. Quel est l’id du groupe groupe1 ?**

`sudo grep groupe1 /etc/group | cut -d: -f3` : on sélectionne la ligne qui correspond au groupe1 et on affiche la colonne qui correspond au gid.

Le groupe1 a pour gid 1001.

**14. Quel groupe a pour gid 1002 ? ( Rien n’empêche d’avoir un groupe dont le nom serait 1002...)**  

`awk -F":" '$3=="1002"' /etc/group | cut -d: -f1` : awk permet de trouver la ligne où la 3ème colonne (celle du gid) est égale à 1002; on sélectionne ensuite la première colonne de cette ligne qui est le nom du groupe correspondant à ce gid.

Le groupe2 a pour gid 1002.

**15. Retirez l’utilisateur u3 du groupe groupe2. Que se passe-t-il ? Expliquez.**  

`sudo gpasswd -d u3 groupe2` retire u3 du groupe groupe2.
Lorsque l'on regarde à quels groupes appartient u3, groupe2 reste son groupe primaire. Pourtant lorsque l'on vérifie les membres du groupe2, u3 a bien été supprimé. 


**16. Modifiez le compte de u4 de sorte que :**  
**— il expire au 1 er juin 2020  : -E**
**— il faut changer de mot de passe avant 90 jours  : -M**
**— il faut attendre 5 jours pour modifier un mot de passe  : -m**
**— l’utilisateur est averti 14 jours avant l’expiration de son mot de passe : -W** 
**— le compte sera bloqué 30 jours après expiration du mot de passe  : -I**

- `chage -E 2020-06-01 u4` permet de changer la date d'expiration du compte
- `chage -M 90 u4`
- `chage -m 5 u4`
- `chage -W 14 u4`
- `chage -I 30 u4`

**17. Quel est l’interpréteur de commandes (Shell) de l’utilisateur root ?**  

Avec `grep root /etc/passwd | cut -d: -f7`, on obtient l'interprêteur de commande utilisé par l'utilisateur root : /bin/bash.  

**18. à quoi correspond l’utilisateur nobody ?**  

L'utilisateur nobody est un utilisateur qui ne possède aucun fichier, qui n'est dans aucun groupe et qui possède les droits de base réservés aux utilisateurs, sans aucun privilège accessible via des groupes.

**19. Par défaut, combien de temps la commande sudo conserve-t-elle votre mot de passe en mémoire ?**

Le mots de passe est mémorisé par défaut pour une durée de 15 minutes.  

**Quelle commande permet de forcer sudo à oublier votre mot de passe ?** 

`sudo -k`

## Exercice 2. Gestion des permissions

**1. Dans votre $HOME, créez un dossier test, et dans ce dossier un fichier fichier contenant quelques
lignes de texte. Quels sont les droits sur test et fichier ?**  

**2. Retirez tous les droits sur ce fichier (même pour vous), puis essayez de le modifier et de l’afficher en
tant que root. Conclusion ?**  

**3. Redonnez vous les droits en écriture et exécution sur fichier puis exécutez la commande echo "echo
Hello" > fichier. On a vu lors des TP précédents que cette commande remplace le contenu d’un
fichier s’il existe déjà. Que peut-on dire au sujet des droits ?**  

**4. Essayez d’exécuter le fichier. Est-ce que cela fonctionne ? Et avec sudo ? Expliquez.**  

**5. Placez-vous dans le répertoire test, et retirez-vous le droit en lecture pour ce répertoire. Listez le
contenu du répertoire, puis exécutez ou affichez le contenu du fichier fichier. Qu’en déduisez-vous ?
Rétablissez le droit en lecture sur test**  

**6. Créez dans test un fichier nouveau ainsi qu’un répertoire sstest. Retirez au fichier nouveau et au
répertoire test le droit en écriture. Tentez de modifier le fichier nouveau. Rétablissez ensuite le droit
en écriture au répertoire test. Tentez de modifier le fichier nouveau, puis de le supprimer. Que pouvezvous
déduire de toutes ces manipulations ?**  

**7. Positionnez vous dans votre répertoire personnel, puis retirez le droit en exécution du répertoire test.
Tentez de créer, supprimer, ou modifier un fichier dans le répertoire test, de vous y déplacer, d’en
lister le contenu, etc…Qu’en déduisez vous quant au sens du droit en exécution pour les répertoires ?**  

**8. Rétablissez le droit en exécution du répertoire test. Positionnez vous dans ce répertoire et retirez lui
à nouveau le droit d’exécution. Essayez de créer, supprimer et modifier un fichier dans le répertoire
test, de vous déplacer dans ssrep, de lister son contenu. Qu’en concluez-vous quant à l’influence des
droits que l’on possède sur le répertoire courant ? Peut-on retourner dans le répertoire parent avec ”cd
..” ? Pouvez-vous donner une explication ?**  

**9. Rétablissez le droit en exécution du répertoire test. Attribuez au fichier fichier les droits suffisants
pour qu’une autre personne de votre groupe puisse y accéder en lecture, mais pas en écriture.**  

**10. Définissez un umask très restrictif qui interdit à quiconque à part vous l’accès en lecture ou en écriture,
ainsi que la traversée de vos répertoires. Testez sur un nouveau fichier et un nouveau répertoire.**  

**11. Définissez un umask très permissif qui autorise tout le monde à lire vos fichiers et traverser vos répertoires,
mais n’autorise que vous à écrire. Testez sur un nouveau fichier et un nouveau répertoire.**  

**12. Définissez un umask équilibré qui vous autorise un accès complet et autorise un accès en lecture aux
membres de votre groupe. Testez sur un nouveau fichier et un nouveau répertoire.**  

**13. Transcrivez les commandes suivantes de la notation classique à la notation octale ou vice-versa (vous
pourrez vous aider de la commande stat pour valider vos réponses) :**  
- chmod u=rx,g=wx,o=r fic  
- chmod uo+w,g-rx fic en sachant que les droits initiaux de fic sont r--r-x---  
- chmod 653 fic en sachant que les droits initiaux de fic sont 711  
- chmod u+x,g=w,o-r fic en sachant que les droits initiaux de fic sont r--r-x---  

**14. Affichez les droits sur le programme passwd. Que remarquez-vous ? En affichant les droits du fichier
/etc/passwd, pouvez-vous justifier les permissions sur le programme passwd ?**  



