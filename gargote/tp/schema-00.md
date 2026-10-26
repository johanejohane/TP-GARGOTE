1--schema relationnel des 4 tables :
    categorie (**id**, libelle)
    plat (**id**, nom, prix, #id_categorie)
    commande (**id**, date_commande, numero_table)
    ligne_commande (**id**, #id_commande, #id_plat, quantite,prix_unitaire)

2-le prix_unitaire dans ligne_commande est le prix du plat au moment de la commande, 
et celle  dans la table plat est le prix du plat actuelle .
exemple si aujourd'hui le prix est a 500 donc quand on fait la commande le plats est a 500
mais si demain le prix est 1000 la commande d'hier restera toujour 500 parce que cela a ete le prix au moment de la commande


3--ce qui ce passe c'est que la commande est disparait 
l'instruction responsable est la deletion:
delete From commande table where id = ?;

4-les 3 donnee absurdes sont 
-les lignes de commande qui n'ont pas de commande associée, c'est a dire que la commande a ete supprimee mais les lignes de commande sont restes dans la table ligne_commande
-le prix peut etre negatif dans la table plat
-le prix peut etre negatif dans la table ligne_commande
-les quantites peuvent etre negatifs dans la table ligne_commande

# 0.3 interroger api  a la main 
     5--pour b on obtient 422 parce que c'est erreur de validation du prix voila pourquoi c'est  pas 200
     6--PHP bloque la suppression 
     7--commande curl :
     curl -i -X POST "http://localhost:8080/api/commandes" \
    -H "Content-Type: application/json" \
    -d '{
        "numero_table": 6,
        "lignes": [
            {
                "id_plat": 1,
                "quantite": 2
            },
            {
                "id_plat": 2,
                "quantite": 1
            }
        ]
    }'
# 0.4 suivre une requete de bout en bout 

| # | Étape | Fichier | Fonction / ligne |
|---|---|---|---|
| 1 | Le formulaire est soumis | `components/PlatForm.vue` |enregistrer() |
| 2 | La requête HTTP est construite |src/api.js | `requete()` |
| 3 | Vite transmet `/api/...` au port 8080 | frontend/vite.conf.js|defineconfig|
| 4 | CodeIgniter choisit le contrôleur|backend/app/config/routes.php|$routes->post(plat','Plats::create'| 
| 5 | Le contrôleur lit le JSON |backend/app/controlleurs/api/plats.php|$this->donnesJson() |
| 6 | Les règles de validation sont vérifiées |backend/app/Models/PlatModel.php|  protected $validationRules|
| 7 | La ligne est insérée |backend/app/Controllers/api/Plats.php|return $this->respondCreated($this->plats->find($id) |
| 8 | Le formulaire prévient l’écran parent | components/PlatForm.vue| `emit(…)` |
| 9 | L’écran recharge la liste | `views/CarteView.vue` | charger()|



08- le metier qui fais la creation est dans le service parce que le controlleur recoit la requette http
# 0.5 Repérer les notions de Vue
09-parce que elle s'occupe uniquement de ffiche le panier et signale les actions ; le composant parent gère les quantités.
10-Dans PlatForm.vue, le watch sert à surveiller les changements de props.plat et à mettre à jour automatiquement le formulaire.
- Avec watch : lorsqu’on clique sur Modifier pour un autre plat, les champs du formulaire sont remplis avec les informations de ce nouveau plat. Les anciens messages d’erreur sont également effacés.
- Sans watch : le formulaire ne se mettrait pas automatiquement à jour lorsque props.plat change. Il pourrait donc conserver les informations de l’ancien plat au lieu d’afficher celles du nouveau.
# 0.6 
**a)Chez Grill le capricorne, Ampandrozognana fichier modifier APP.VUE
**b)ajout du nouvel categorie INSERT INTO categorie (libelle) VALUES ('Goûter');
11-parce que 