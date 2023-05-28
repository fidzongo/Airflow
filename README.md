# Collecte de données et entraînement de modèles + sélection du plus performant
DAG qui permet :
- De récupérer des informations depuis une API de données météo disponible en ligne
- Les stocke dans un dataset
- Les transforme et entraîne un algorithme dessus

# Installation de Airflow
```
# creating directories
mkdir clean_data
mkdir raw_files

echo -e "AIRFLOW_UID=$(id -u)\nAIRFLOW_GID=0" > .env

# initialisation
docker-compose up airflow-init

# copie du fichier data.csv
cp data.csv clean_data/data.csv
echo '[]' >> raw_files/null_file.json

# lancement des conteneurs airflow
docker-compose up -d
```

# Paramétrage du DAG
Pour exécuter/tester ce DAG il faut effectuer le paramétrage ci-dessous:
*	Optionnel : Modifier les variables ci-dessous dans le fichier et le remplacer par les vôtres
  `* Api_url=" "`
  `* Api_key=" "`
  `* cities=['paris', 'london', 'washington']`

- NB : si le dag est chargée avec ces variables a vide, elles seront automatiquement créées dans airflow avec des valeurs par defaut; vous pouvez les modifier après dans airflow pour mettre vos propres valeurs et/ou ajouter une ville ou des villes supplémentaires sans modifier le fichier DAG pour la récolte des données (explications de ce choix dans « choix et paramétrage »)
-	Créer un pool nommé « evaluation_pool » avec un slot de 100 par exemple (il faut un minimum de 3 puisque les tâches 4’, 4’’ et 4’’’ s’exécutent en parallèle)
-	Créer les deux connections suivantes de type File pour vérifier la présence des fichiers crées
`* raw_files_fs : pour surveiller « /app/raw_files/ »`
`* clean_data_fs : pour surveiller « /app/clean_data/ »`
-	Pour initialiser le workflow il faut copier les deux fichiers data.csv et fulldata.csv dans le répertoire « clean_data » de votre airflow.
-	Copier enfin le fichier « openweathermap_dag.py » dans le répertoire dags de votre installation airflow
-	Les premières exécutions seront en erreurs pour les tâches 4’, 4’’ et 4’’’ et 5. Il faut attendre 15 minutes (15 récoltes de fichiers) pour avoir un minimum de données pour l’entrainement des modèles.

- Une puce
- Une autre puce
  - Une puce imbriqué
  - Une autre puce imbriqué
