# Model Card - fraude (version servie par l'alias production)

## Modele
Nom et version dans le registre MLflow : fraude, version 1 (alias production)
Famille et reglages : RandomForestClassifier, n_estimators = 500, max_depth = 16
Run d'origine (MLflow) : <<e0211d0ec60d4bae94e3b7c335bf6103>>

## Objectif et usage prevu
Estimer la probabilite qu'une transaction de paiement soit une fraude, a partir de 4 variables : montant, anciennete du client, nombre de transactions sur 24 h, paiement de nuit. Sert d'aide a la decision pour signaler les transactions suspectes.

## Usages exclus
Ne pas bloquer definitivement un compte ou un paiement sans revue humaine. Ne pas utiliser le modele sur d'autres donnees que celles d'entrainement sans nouvelle evaluation. Ne pas presenter une explication SHAP ou LIME comme la cause reelle de la fraude.

## Donnees
Fichier data/transactions.csv, 5 000 lignes, 568 fraudes (environ 11 %), empreinte (tag donnees_md5) : <<cf9f6f86c141e7df32c61715c493483f>>
Origine des donnees (reelles ou synthetiques) : synthetiques, generees avec une graine fixe.

## Performances (jeu de test, 1 250 transactions)
F1 : 0.8669   precision : 0.8837   rappel : 0.8507   AUC : 0.9872
Le test contient 134 fraudes : 114 detectees, 20 manquees, 15 fausses alertes.

## Methodes d'explicabilite
Globale : importance par permutation (10 melanges) et SHAP TreeExplainer sur 400 transactions de test.
Locale : SHAP (valeurs exactes, base + contributions = prediction) et LIME (2 000 voisins, graine 0).
Variables les plus influentes : montant en tete (permutation 0,593 ; SHAP 0,1157), puis anciennete, nuit et freq_24h, proches entre elles.

## Comparaison avec un modele interpretable
Regression logistique avec log10(montant) : F1 0,8593 et 113 / 134 fraudes detectees contre 0,8669 et 114 pour la foret. Ecart d'une fraude, dans le bruit du test. Arbre de profondeur 3 : F1 0,5737, trop faible.

## Limites et risques connus
Donnees synthetiques : les performances ne se transferent pas telles quelles a des paiements reels. Seulement 134 fraudes en test : les scores sont bruites. LIME est instable sur les petits effets (freq_24h change de signe selon la graine). SHAP decrit le modele, pas la cause reelle. La variable nuit et l'anciennete (un client recent est juge plus suspect) sont a surveiller pour des effets injustes.

## Recommandations d'utilisation et de surveillance
Faire relire par un humain tout blocage. Preferer SHAP a LIME pour expliquer une decision a un client. Surveiller le F1 et le taux de fausses alertes apres chaque reentrainement, et relancer les explications a chaque changement d'alias.

## Tracabilite
Run des explications (experience tp7-explicabilite) : xai-fraude-v1, identifiant <<23a5a6e0f4a1489da450af6802f31f6b>>
Auteurs : <<BENNANI Rania - 5IADATA>>   Date : 12 novembre 2026   Prochaine revue : apres le prochain reentrainement