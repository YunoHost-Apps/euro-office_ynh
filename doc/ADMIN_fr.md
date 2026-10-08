## Intégrez votre serveur Euro-Office dans une application

Pour intégrer les éditeurs dans votre application (nextcloud, seafile, etc.) :
1. Installez l'extension dédiée Euro-Office connector dans vos applications
2. Effectuez les réglages suivants dans la configuration de cette extension et enregistrez (ou redémarrez le service de l'application s'il s'agit d'un fichier de configuration) :
  - Adresse du serveur de documents : « https://__DOMAIN____PATH__ »
  - Clé secrète : « __JWT_SECRET__ »
3. Testez la modification ou la création d'un document sur votre application

### Nextcloud
Pour configurer Euro-Office avec Nextcloud :

- Dans Nextcloud, installez l'application Nextcloud Office
- Allez dans les paramètres sous « Administration > Office > Paramètres du serveur »
- Adresse du serveur de documents : « https://__DOMAIN____PATH__ »
- Clé secrète : « __JWT_SECRET__ »

### Seafile
Ajouter les paramètres de configuration suivants à `seahub_settings.py`
```
# Enable Euro-Office
ENABLE_Euro-Office = True
VERIFY_Euro-Office_CERTIFICATE = True
Euro-Office_APIJS_URL = 'https://__DOMAIN____PATH__/web-apps/apps/api/documents/api.js'
Euro-Office_FILE_EXTENSION = ('doc', 'docx', 'ppt', 'pptx', 'xls', 'xlsx', 'odt', 'fodt', 'odp', 'fodp', 'ods', 'fods')
Euro-Office_EDIT_FILE_EXTENSION = ('docx', 'pptx', 'xlsx')

# Enable force save to let user can save file when he/she press the save button on Euro-Office file edit page.
Euro-Office_FORCE_SAVE = True

# if JWT enabled
Euro-Office_JWT_SECRET = '__JWT_SECRET__'
```

Restart seafile
```
systemctl restart seafile
systemctl restart seahub
```

### Yeswiki
Une extension existe.
Soyez libre de contribuer si vous trouvez comment l'installer.
Cf: https://github.com/YesWiki/yeswiki-extension-documents/blob/main/services/Euro-OfficeDocumentProvider.php

### Moodle 
Si on installe Moodle sur le même YNH qu'Euro-Office, il faut penser à enlever 127.0.0.0/8 de la curlsecurityblockedhosts (in Site Administration / Security / HTTP Security).

### Autres

Pour Owncloud, Opencloud, Mattermost, Odoo, Moodle, Redmine, Wordpress, Humhub, vous pouvez trouver les infos d'intégration ici: https://www.Euro-Office.com/all-connectors.aspx

## Questions et Réponses
### Mes applications ne peuvent pas avoir de certificat valide (adresse IP locale), puis-je les connecter à Euro-Office ?
Oui, vous pouvez le faire en désactivant l'option « Empêcher les requêtes non authentifiées ».

Important : cela pourrait constituer une faille de sécurité si vous ne pouvez pas faire confiance à votre routeur local et à vos utilisateurs locaux...

### Je ne parviens pas à connecter mon application à ce serveur Euro-Office
 - Vérifiez que vous disposez d'un certificat Let's Encrypt (https) valide pour votre application et ce serveur Euro-Office
 - Vérifiez que votre application et l'application Euro-Office sont publiques (afin que le groupe de visiteurs puisse accéder à l'application ou au moins à l'API...).
 - Vérifiez que votre application ne contient pas de mécanisme interdisant les requêtes locales sur 127.0.0.1. Si c'est le cas, vous pouvez essayer de supprimer `rm /etc/dsnmasq.d/__DOMAIN__` et redémarrer le service dnsmasq `systemctl restart dnsmasq`.

### Certaines modifications apportées aux documents sont perdues
Si vous utilisez la synchronisation du client Nextcloud et les applications Web Euro-Office sur le même fichier, vous pouvez parfois être confronté à des situations où Euro-Office réécrit une modification effectuée à partir d'un appareil local et la synchronise avec le client Nextcloud. Vous devriez probablement adopter une pratique avec votre équipe pour éviter cette situation. Par exemple, en adoptant une convention de nommage pour les fichiers collaboratifs à ouvrir uniquement via le Web Euro-Office.
