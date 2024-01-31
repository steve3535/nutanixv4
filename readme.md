### Un peu de contexte
je maintiens 2 inventaires dynamique pour mes playbooks ansible:  
1 inventaire que jextrais directement des hyperviseurs en faisant appel aux API de Nutanix et Vmware  
1 autre inventaire que j'extrais de Red hat satellite   
Et justement, en regardant les logs, je vois que le premier cité ne tourne plus depuis un certain temps à cause du mot de passe expiré du compte utilisé dans le script  
Mais pire, je re-réalise, avec horreur, que jutilise un compte user dans le script !!!     
Illico, je me refuse donc a juste mettre le nouveau pass, mais me résout à utiliser un **SA** , d'autant plus que ledit SA na besoin que d'un access RO.  


