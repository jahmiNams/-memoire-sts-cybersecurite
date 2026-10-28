# Évaluation reproductible des modèles de langage pour l'appariement sémantique des bases de connaissances en cybersécurité

Code et résultats du mémoire de Master de **NAMWISI ATUYEKULA Jahmi** (Université de Kinshasa,
Faculté des Sciences et Technologies, Département de Mathématiques, Statistique et Informatique),
réalisé sous la direction du Prof. Dr. Jordan Felicien MASAKUNA, année académique 2025-2026.

Le travail reproduit, à partir des seules sources publiques, le banc d'essai présenté dans :

> A. Soltani, D. Kanda Nkashama, J. F. Masakuna, M. Frappier, P.-M. Tardif, F. Kabanza.
> *Extended Abstract: Assessing Language Models for Semantic Textual Similarity in Cybersecurity.*
> DIMVA 2024.

Il complète ensuite l'évaluation par des méthodes de référence, un recall normalisé, des intervalles de
confiance par bootstrap, des tests de permutation appariés avec correction de Holm, une expérience sur
la longueur des textes et un affinage de MPNet sans fuite de données.

## Contenu du dépôt

| Fichier | Contenu |
|---|---|
| `memoire_sts.ipynb` | notebook complet des expériences, étape par étape, avec ses sorties |
| `requirements.txt` | bibliothèques Python utilisées |
| `PQ_par_requete.json` | LRAP, recall@5 et recall normalisé de chaque requête, pour chaque méthode |
| `troncature.json` | LRAP par requête pour chaque configuration de longueur lue |
| `affinage_MPNet.json` | scores par CVE de MPNet avant et après affinage, et composition des cinq parties |
| `figure4.png` | recall@k par sous-tâche, avec le plafond atteignable |

## Données

Les données sont téléchargées par le notebook depuis les dépôts officiels :

| Donnée | Version | Source |
|---|---|---|
| ATT&CK (Enterprise) | v13.1 | https://github.com/mitre-attack/attack-stix-data |
| CAPEC | 3.9 | https://github.com/mitre/cti |
| Liens CVE → ATT&CK | CTID, *Mapping ATT&CK to CVE for Impact* | https://github.com/center-for-threat-informed-defense/attack_to_cve |
| Fiches CVE | dépôt consulté en octobre 2026 | https://github.com/CVEProject/cvelistV5 |
| D3FEND | 0.13.0-BETA-1 | https://github.com/d3fend/d3fend-ontology |

## Modèles évalués

| Modèle | Identifiant Hugging Face | Jetons lus |
|---|---|---|
| MiniLM | `sentence-transformers/all-MiniLM-L12-v2` | 128 |
| MPNet | `sentence-transformers/all-mpnet-base-v2` | 384 |
| RoBERTa | `sentence-transformers/all-roberta-large-v1` | 256 |
| BGE | `BAAI/bge-large-en-v1.5` | 512 |
| GTE | `thenlper/gte-large` | 512 |
| ATTACK-BERT | `basel/ATTACK-BERT` | 384 |

L'ensemble E-SLMs+AB fait la moyenne des similarités de BGE, GTE et ATTACK-BERT. Les références sont
un classement au hasard, TF-IDF et, pour les CVE, la popularité des étiquettes.

## Exécution

1. Ouvrir `memoire_sts.ipynb` dans Google Colab et choisir un environnement avec GPU (une T4 suffit).
2. Exécuter les cellules dans l'ordre. Les résultats intermédiaires sont enregistrés dans le dossier
   Google Drive `memoire_sts/resultats`, qui est monté au début du notebook.
3. Après une déconnexion, la cellule de reprise rapide (étape B) recharge les douze sous-tâches
   sauvegardées et permet de reprendre à l'étape 9 ou aux suivantes. Les étapes longues (12 et 13)
   reprennent là où elles se sont arrêtées.

| Étapes | Rôle |
|---|---|
| 1 à 8 | chargement des quatre bases, construction des douze sous-tâches, métriques, références et six modèles |
| A, B | sauvegarde des sous-tâches et reprise rapide |
| 9 | ensemble E-SLMs+AB, comparaison avec l'étude de référence, coefficient de Kendall, figure 4 |
| 10, 11 | scores par requête, recall normalisé, bootstrap, tests de permutation et correction de Holm |
| 12 | longueur des textes et expérience de troncature |
| 13 | affinage de MPNet en validation croisée et référence de popularité |

Toutes les opérations aléatoires utilisent une graine fixée à 0. Seul l'encodage sur GPU peut
introduire des écarts de l'ordre de 10⁻³.
