!SLIDE bullets
# Comment utiliser une API Rest ?

* lire la doc
* utiliser une implémentation existante dans votre langage préféré

!SLIDE center

![FAIL](fail.jpg)

!SLIDE
# Comment décrire une API Rest (et faciliter l'écriture de client) ?

!SLIDE bullets
# Specifications for a POrtable Rest Environment

* Description de l'API
* Implémentation du client

!SLIDE bullets
# Description de l'API

    @@@ Javascript
    {
      "base_url" : "http://api.twitter.com/1",
      "version" : "0.1",
      "methods" : {
      "public_timeline" : {
        "optional_params" : [
          "trim_user",
          "include_entities"
         ],
         "required_params" : [
           "format"
          ],
          "path" : "/statuses/public_timeline.:format",
          "method" : "GET"
       }
    }

!SLIDE bullets
# Pour chaque méthode, on décrit

* son uri
* le verbe HTTP
* les paramètres optionnels et obligatoires
* les entêtes http
* ...

!SLIDE bullets
# Implémentation des clients

* Tous les mêmes !
* Pas besoin de mettre à jour les clients

!SLIDE
# Code

    @@@ Javascript
    var client = spore.createClient(
        __dirname +'/statusnet.json');

    client.public_timeline({format: 'json'},
                           function(err, res) {

        console.log(err, res);
    });

!SLIDE bullets
# Middlewares

* Ils sont partout !
* Avant que vous fassiez la requête
* A la réponse de l'API

!SLIDE bullets
# Middlewares

* Rajouter les informations d'authentification
* Mettre en cache les réponses
* Déserialiser la réponse
* Réparer l'API ?

!SLIDE
# Exemple

    @@@ Javascript
    var oauth2 = require('spore/middlewares').oauth2;

    client.enable(oauth2('access_token'));

    client.home_timeline({format: 'json'}, function(err, res) {
        console.log(err, res);
    });

!SLIDE
# enable/enable_if/disable

Ajout de middlewares au démarrage

    @@@ Javascript
    spore.createClient(middleware, __dirname+'twitter.json');

Plus tard

    @@@ Javascript
    client.enable(middleware);

Seulement dans certains cas

    @@@ Javascript
    client.enable_if(middleware);

!SLIDE
# Implémentation (qui modifie les paramètres)

    @@@ Javascript
    function myMiddleware() {
        return function(method, request, next) {
            request.params["format"] = "xml";
            next();
        }
    }

!SLIDE
# Implémentation (qui fait une chose asynchrone)

    @@@ Javascript
    function myMiddleware() {
        return function(method, request, next) {
            fs.stat(path, function(err, stats) {
                next();
            }) ;
        }
    }

!SLIDE
# Appel a la réponse

    @@@ Javascript
    function myMiddleware() {
        return function(method, request, next) {
            next(function(response, next) {
                response.body = 'plop';
                next();
            });
        }
    }

!SLIDE bullets
# Liens

* https://github.com/SPORE/specifications/
* https://github.com/francois2metz/node-spore
