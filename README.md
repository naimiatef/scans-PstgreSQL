# scans-PstgreSQL



Types de Scans dans PostgreSQL
1. Sequential Scans (seq scan) :
Un seq scan parcourt toute la table sur le disque, indépendamment de sa taille, de son schéma, de ses contraintes ou de la présence d’index.
Caractéristiques :

Démarrage rapide (l'I/O séquentiel est plus rapide que l'accès aléatoire).
Chaque bloc est lu une seule fois.
Produit une sortie non triée.
Exemple de requête :

sql
Copier le code
explain analyze select * from pgbench_tellers;
explain analyze select * from pgbench_tellers where bid=30;
2. Index Scans :
Un Index Scan parcourt un index B-tree, explore les nœuds feuilles pour trouver les entrées correspondantes, et récupère les données de la table associée.
Caractéristiques :

L'accès aléatoire est beaucoup plus lent que l'I/O séquentiel.
Nécessite des I/O supplémentaires pour accéder à l'index.
Peut lire plusieurs fois le même bloc.
Produit une sortie triée.
Exemple de requête :

sql
Copier le code
explain analyze select * from pgbench_tellers where tid=204;
3. Bitmap Index/Heap Scans :
Contrairement à un Index Scan simple, un bitmap scan récupère tous les pointeurs de tuples en une seule fois, les trie via une structure "bitmap" en mémoire, puis accède aux tuples de la table dans l'ordre physique.
Caractéristiques :

Combine l'I/O séquentiel avec la sélectivité des index.
Lent à démarrer (lecture et tri de tous les tuples de l'index).
Fréquemment utilisé pour les opérateurs IN et =ANY(array), ou pour les scans d'index peu sélectifs.
Permet de combiner plusieurs index.
Produit une sortie non triée.
Exemple de requête :

sql
Copier le code
explain analyze select * from pgbench_tellers where tid>0 and tid<100;
4. Index Only Scans :
Un Index Only Scan parcourt uniquement l'index B-tree pour trouver les entrées correspondantes. Aucun accès à la table n'est nécessaire, car l'index contient toutes les colonnes nécessaires à la requête.
Exemple de requête :

sql
Copier le code
explain analyze select count(*) from pgbench_tellers where tid<300;

