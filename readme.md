### Un peu de contexte
je maintiens 2 inventaires dynamique pour mes playbooks ansible:  
1 inventaire que jextrais directement des hyperviseurs en faisant appel aux API de Nutanix et Vmware  
1 autre inventaire que j'extrais de Red hat satellite   
Et justement, en regardant les logs, je vois que le premier cité ne tourne plus depuis un certain temps à cause du mot de passe expiré du compte utilisé dans le script  
Mais pire, je re-réalise, avec horreur, que jutilise un compte user dans le script !!!     
Illico, je me refuse donc a juste mettre le nouveau pass, mais me résout à utiliser un **SA** , d'autant plus que ledit SA na besoin que d'un access RO.  

### Là où ca ma fait ch***  
* je commence par AHV, et je fais usage du compte *rho* avec access *Viewer*; je le teste dans le browser: ca marche, **apres plusieurs tentatives**.
  * en effet par defaut les PC authentifient against un IDP (ici AD ldap), et donc il faut choisir *connect with local accounts* pour sauthentifier avec un user local  
* le script utilise HTTP basic authentication; jai donc entrepris dencoder en base64:
  `echo -n ro:xxxx | base64`  
  PS:
  > - nul besoin dentourer de quotes, ca donne exactement le meme encodage  
  > - on est souvent amené a croire que lorskon encode avec base64, il faut que le string se termine par = ou ==. Non. pas forcement, = ou == c juste le padding pour que le strings soit tjrs un multiple de 4  
  > - -n du echo est important, autrement il considere que le \n final fait partie du mdp  
  > - ATTN ATTN ATTN base64 nest en rien safe, c de lencodage, pas de l'encryption, il sert a generer de l'ascii c tout
  > - pkoi lutiliser alors ? c du transport, par exp: transporter facilement des caracteres "bizarres", on peut justement par exp transporter des secrets encryptés
  > - pour securiser ce genre de choses, fo se tourner vers une solution de vault: Hashicorp vault, mais ...   
  > - en absolu, on peut se contenter de bien proteger le fichier en local, car vu quavec AHV, on est sur du https, rien ne transite en clair de tte facon
* par contre, mes tentatives sont restées vaines: je recois un 401 systematiquement durant mes appels:
  `curl -X POST -H 'content-type:application/json' -H 'Authorization:basic bHgfsteralkjs==' https://server_ahv01:9440/api/nutanix/v3/vms/list -d '{"kind":"vm"}'`  
  PS:
  > `-d "{'kind':'vm'}"` a la place de `-d '{"kind":"vm"}'` generera une err ==> JSON is a fucking asshole: evitez den ecrire bbeaucoup, vs ferez forcement une erreur de syntaxe, qui plus est sera difficile a capturer  

### TENTER UNE NOUVELLE EXPERIENCE AVEC la V4
  **un peu dhistorique**  
  avec AHV pilotant l'hypervision de toute l'infra. (cest a dire vmware est subordonné aux PCs), le souci majeur que jai retenu en adressant linfra par code est la necessité dutiliser plusieurs APIs:
  * une API pour le cluster mgt, les VMs et une partie du network (v3) -- liée aux PC
  * une API pour le storage et une autre partie du network (v2) -- liée aux PE
  * une API encore pour attaquer ESX en natif
  la promesse de la v4 est entre autres d' unifier tout cela.
  Donc, pourquoi pas ???

  
  
  




