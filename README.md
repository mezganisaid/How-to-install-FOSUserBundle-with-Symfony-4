# Comment installer FOSUserBundle avec Symfony 4

<img src="https://www.mezganisaid.com/blog/images/img_article/Symfony2.svg_.png" class="img-responsive">

<strong>FOSUserBundle est certainement le bundle le plus populaire pour gérer les utilisateurs de Symfony.</strong><br><br>

Avec la dernière version majeure de Symfony (4), l’organisation du code du framework est un peu différente. La documentation officielle du processus d’installation de cet ensemble n’est pas claire.<br><br>

Vous trouverez ci-dessous les étapes nécessaires à l’utilisation de FOSUserBundle. Vous pouvez obtenir toutes les sources de cette installation sur <a href="https://github.com/mezganisaid/How-to-install-FOSUserBundle-with-Symfony-4" class="btn btn-md btn-info" target="_blank"> github</a>.<br><br>

<h2 class="text-primary" title="Installation du squelette Symfony 4 et des composants requis">Étape 1: Installation du squelette Symfony 4 et des composants obligatoires</h2>
<br><br>
<h3 class="text-blog" title="Installation de Symfony 4 squelette">Installation de Symfony 4 squelette</h3>
<br>
La première étape est l’installation du squelette de symfony pour partir d’une base propre.
<br>
<pre><code>	
  composer create-project symfony/skeleton fosUserBundleProject
</code></pre>
<br><br>
<h3 class="text-blog" title="Doctrine">Doctrine</h3>
<br>
Object-Relational-Mapper pour gérer la base de données
<br><br>
Vous devez pointer sur le project pour instaler l'ORM
<br><br>
<pre><code>	
  cd fosUserBundleProject/
</code></pre>
<br><br>
<pre><code>	
  composer require doctrine
</code></pre>
<br><br>
Ou
<br><br>
<pre><code>	
  composer require orm
</code></pre>
<br><br>
A cette étape, vous devez modifier votre fichier de configuration .env et éventuellement config/packages/doctrine.yaml.<br>
En .env, la chaîne à modifier est la suivante :
<br><br>
<pre><code>	
  DATABASE_URL=mysql://userbd:passbd@localhost:3306/dbname
</code></pre>
<br><br>
<h3 class="text-blog" title="Annotations">Annotations</h3>
<br>
Docblock Annotations Parser utilisé avec Doctrine
<br><br>
<pre><code>	
  composer require annotations
</code></pre>
<br><br>
<h3 class="text-blog" title="SwiftMailerBundle">SwiftMailerBundle</h3>
<br>
Swiftmailer, PHP messagerie gratuite et riche en fonctionnalités
<br><br>
<pre><code>	
  composer require swiftmailer-bundle
</code></pre>
<br><br>
<h3 class="text-blog" title="Twig">Twig</h3>
<br>
Twig, le langage de template flexible, rapide et sécurisé pour PHP
<br><br>
<pre><code>	
  composer require twig
</code></pre>
<br><br>
<h3 class="text-blog" title="Web Server">Web Server</h3>
<br>
Symfony WebServerBundle
<br><br>
<pre><code>	
  composer require server --dev
</code></pre>
<br><br>

<h2 class="text-primary" title="Étape 2: Téléchargez FOSUserBundle à l'aide de composer">Étape 2: Téléchargez FOSUserBundle à l'aide de composer</h2>
<br><br>
<pre><code>	
  composer require friendsofsymfony/user-bundle "~2.0"
</code></pre>
<br><br>
A la fin de l'installation, vous aurez le message d'erreur suivant:
<br><br>
<div class="alert alert-danger">
  <strong>The child node "db_driver" at path "fos_user" must be configured.</strong>
</div>
<br><br>
C'est normal, vous devez créer votre classe d'utilisateurs et configurer FOSUserBundle.
<br><br>

<h2 class="text-primary" title="Étape 3: Créez votre classe d'utilisateurs">Étape 3: Créez votre classe d'utilisateurs</h2>
<br><br>
Créez src/Entity/User.php qui étend de la classe FOSUserBundle BaseUser.
<br><br>
<pre>
<code>
&lt;?php
// src/Entity/User.php

namespace App\Entity;

use FOS\UserBundle\Model\User as BaseUser;
use Doctrine\ORM\Mapping as ORM;

##/**
 ##* @ORM\Entity
 ##* @ORM\Table(name="fos_user")
 ##*/
class User extends BaseUser
{
    ##/**
     ##* @ORM\Id
     ##* @ORM\Column(type="integer")
     ##* @ORM\GeneratedValue(strategy="AUTO")
     ##*/
    protected $id;

    public function __construct()
    {
        parent::__construct();
        // your own logic
    }
}
</code></pre>
N'oublie pas de supprimer les "#"
<br><br>

<h2 class="text-primary" title="Étape 4: configurez le fichier security.yml de votre application">Étape 4: configurez le fichier security.yml de votre application</h2>
<br><br>
Modifier le fichier security.yml sur le chemin config/packages/security.yaml (Configuration de symfony FOSUserBundle security)
<br><br>
<pre><code>	
security:
    encoders:
        FOS\UserBundle\Model\UserInterface: bcrypt

    role_hierarchy:
        ROLE_ADMIN:       ROLE_USER
        ROLE_SUPER_ADMIN: ROLE_ADMIN

    # https://symfony.com/doc/current/security.html#where-do-users-come-from-user-providers
    providers:
        fos_userbundle:
            id: fos_user.user_provider.username

    firewalls:
        dev:
            pattern: ^/(_(profiler|wdt)|css|images|js)/
            security: false
        main:
            pattern: ^/
            form_login:
                provider: fos_userbundle
                csrf_token_generator: security.csrf.token_manager

            logout:       true
            anonymous:    true

    # Easy way to control access for large sections of your site
    # Note: Only the *first* access control that matches will be used
    access_control:
        - { path: ^/login$, role: IS_AUTHENTICATED_ANONYMOUSLY }
        - { path: ^/register, role: IS_AUTHENTICATED_ANONYMOUSLY }
        - { path: ^/resetting, role: IS_AUTHENTICATED_ANONYMOUSLY }
        - { path: ^/admin/, role: ROLE_USER }
</code></pre>
<br><br>

<h2 class="text-primary" title="Étape 5: Configurez le FOSUserBundle">Étape 5: Configurez le FOSUserBundle</h2>
<br><br>
Créer un fichier config/packages/fos_user.yaml
<br><br>
<pre><code>	
fos_user:
    db_driver: orm # other valid values are 'mongodb' and 'couchdb'
    firewall_name: main
    user_class: App\Entity\User
    from_email:
        address: "mezgani.said@gmail.com"
        sender_name: "mezgani.said@gmail.com"
</code></pre>
<br><br>
Mettez à jour config/packages/framework.yaml pour ajouter une configuration de modèle
<br><br>
<pre><code>	
framework:
    templating:
        engines: ['twig', 'php']
</code></pre>
<br><br>

<h2 class="text-primary" title="Étape 6: Importer le routage FOSUserBundle">Étape 6: Importer le routage FOSUserBundle</h2>
<br><br>
Créer config/routes/fos_user.yaml
<br><br>
<pre><code>	
fos_user:
    resource: "@FOSUserBundle/Resources/config/routing/all.xml"
</code></pre>
<br><br>

<h2 class="text-primary" title="Étape 7: mettez à jour votre schéma de base de données">Étape 7: mettez à jour votre schéma de base de données</h2>
<br><br>
<textarea cols="10" rows="10" class="text-left textareacode">	
### Créer la base de donnée 
php bin/console doctrine:database:create
### Voir les modifications sans rien faire
php bin/console doctrine:schema:update --dump-sql
### Forcer les modifications 
php bin/console doctrine:schema:update --force
</code></pre>
<br><br>
À ce stade, tout est installé et configuré pour utiliser FOSUserBundle dans Symfony 4. Exécutez la commande suivante pour vérifier si tout va bien
<br><br>
<pre><code>	
  composer update
</code></pre>
<br><br>
<pre><code>	
  php bin/console server:run
</code></pre>
<br><br>
<img src="https://www.mezganisaid.com/blog/images/img_article/fosuserbundle.png" class="img-responsive">


