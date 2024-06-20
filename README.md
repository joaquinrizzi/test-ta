# README
Ce test s’agit de réaliser un live code pour la création d’une application MVP qui impliquera des restaurants (Restaurant) et des avis (Review) de leurs clients.

Nous allons commencer par énoncer les informations dont nous aurons besoin ainsi comme les principales fonctionnalités pour faire de ce projet un vrai MVP.

**Models, relations et validations**

**Models**

- Un restaurant a un nom (`name`), une adresse (`address`), un numéro de téléphone (`phone_number`) et une catégorie (`category`), et peut avoir de nombreux avis.
- Un avis a une note (`rating`) et un contenu (`content`), et appartient à un restaurant.

**Relations**

D’après ces précisions, nous pouvons déterminer que chaque Restaurant peut avoir un ou plusieurs Reviews, et que chaque Review appartient à un seul Restaurant, ce qui fait une relation de 1 à N.

Voici le lien de l’exemple du schema DB

https://kitt.lewagon.com/db/129213


En réfléchissant aux types de données de nos modeles, voici les détails :

- **varchar** / string pour name, phone_number et category
- **text** pour address et content
- **integer** pour rating

**Validations et Associations**

Par rapport aux validations et associations de nos modeles, nous préciserons que :

- Un restaurant a plusieurs reviews - en model Restaurant -> has_many :reviews
- Un restaurant doit avoir un nom, une adresse et une catégorie - en Migration -> null: false / en model Restaurant -> presence: true
- La catégorie du restaurant doit appartenir à cette liste fixe : `["chinese", "italian", "japanese", "french", "belgian"] -
  En model Restaurant -> inclusion: { in: CATEGORY, message: "type de cuisine invalide" }
- Quand un restaurant est supprimé, tous ses avis doivent aussi être supprimés - en model Restaurant -> has_many :reviews, dependent: :destroy
- Un avis doit appartenir à un restaurant - en model Review -> belongs_to :restaurant
- Un avis doit avoir un contenu (content) - en Review Migration - > null: false / en model Review -> presence: true
- Un avis doit avoir une note (rating) - en Review Migration -> null: false / en model Review -> presence: true
- La note d’un avis doit être un entier entre 0 et 5 - en Review Migration -> null: false / en model Review -> presence: true et numericality: { only_integer: true, greater_than_or_equal_to: 0, less_than_or_equal_to: 5}

Note : pour la création de nos Models, nous utiliserons les generators de Rails

rails generate model (en précisant les types de données)

**Migrations**

Une fois nous aurons créé nos Models avec ses associations et validations, nous serons en mesure de terminer la migration et créations de nos tables en DB en utilisant la commande suivante :

rails db:migrate

**Testing et Seeds**

Même si c’est toujours recommandé de réaliser des tests dans la console pour vérifier qu’on n’a rien oublié et que tout fonctionne comme prévu, c’est aussi très pratique d’avoir des seeds dans notre projet qui nous faciliterons nos tâches par la suite.

Commençons par quelques tests unitaires dans la console :

```
rails c
> bristol= Restaurant.new(name: "Epicure", category: "french")> bristol.valid?# Should return false> bristol.address= "75008 Paris"
> bristol.valid?# Should return true> bristol.save# Insert into DB and set id> yummy= Review.new(content: "yummy yummy", rating: 4)> yummy.restaurant= bristol# Set foreign key restaurant_id> yummy.save
> bristol.reviews# Should contain the yummy review> yummy.restaurant
```

Pour les seeds, on va s’amuser avec la gem Faker en l’utilisant notamment pour les noms de restaurants, leurs addresses et les commentaires de chaque avis:

On doit d’abord ajouter la gem Faker dans le fichier Gemfile :
`gem 'faker'`

Et ensuite on n’oublie pas d’installer cette gem avec le commande :

`bundle install`

On va utiliser la gem plus précisément dans le fichier seeds.rb en rajoutant la ligne de code suivante :
`require 'faker'`

Pour plus d’info sur le setup de la gem Faker, voici le lien sur Github :

https://github.com/faker-ruby/faker/blob/main/doc/default/restaurant.md

On est prêt pour commencer avec la création de nos seeds, et pour ça on va se servir en particulier de deux Faker Generators :

- Restaurant’s name and Review’s content

https://github.com/faker-ruby/faker/blob/main/doc/default/restaurant.md

- Restaurant’s address

https://github.com/faker-ruby/faker/blob/main/doc/default/address.md

Passons alors à la création de 10 restaurants, chaqu’un avec 5 reviews :

ex : 10.times Restaurant.create en utilissant les Fakers et au même temps 5.times pour la création de Reviews
