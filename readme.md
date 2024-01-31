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
  > 
