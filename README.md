Corynebacterium Diphtheriae Pipeline
Description

Ce pipeline permet d'effectuer une annotation génomique, de rechercher des gènes de virulence, de rechercher des gènes de résistance aux antibiotiques, et de réaliser une analyse phylogénétique des génomes de Corynebacterium diphtheriae. Le pipeline utilise Snakemake pour l'automatisation du workflow et plusieurs outils bioinformatiques comme Prokka, ABRicate, ainsi que des outils pour l'alignement et la construction d'arbres phylogénétiques.
Objectifs du pipeline :

    Annotation du génome : Identification et annotation des caractéristiques génomiques du Corynebacterium diphtheriae à l'aide de Prokka.
    Recherche des gènes de virulence : Identification des gènes de virulence présents dans le génome à l'aide de l'outil ABRicate et de la base de données vfdb.
    Recherche des gènes de résistance aux antibiotiques : Identification des gènes associés à la résistance aux antibiotiques via ABRicate et la base de données CARD.
    Analyse phylogénétique : Construction d'un arbre phylogénétique basé sur l'alignement des séquences génomiques de plusieurs souches de Corynebacterium diphtheriae.

Structure du projet

Le projet se compose de plusieurs répertoires et fichiers :

    Dockerfile : Contient l'image Docker pour l'exécution du pipeline dans un environnement contrôlé.
    Snakefile : Le fichier principal de Snakemake où toutes les étapes du pipeline sont définies.
    genome.fasta : Le fichier d'entrée contenant le génome à analyser.
    results/ : Dossier contenant les résultats des différentes étapes, tels que les annotations, les gènes de virulence, les gènes de résistance et l'arbre phylogénétique.

Dockerfile

Le Dockerfile installe une image de base Ubuntu 22.04 et y ajoute les dépendances nécessaires pour exécuter le pipeline. Voici les étapes principales :

    Installation des dépendances :
        Mise à jour des paquets et installation des dépendances nécessaires comme Python 3, Perl, et les bibliothèques de développement.

    Installation de Snakemake :
        Snakemake est installé via pip pour l'exécution des workflows de traitement de données.

    Installation de Prokka :
        Prokka est cloné depuis GitHub et installé pour annoter les génomes.

    Installation d'ABRicate :
        ABRicate est cloné depuis GitHub pour l'identification des gènes de virulence et de résistance.

    Définition du répertoire de travail :
        Le répertoire de travail /workspace est défini, et le conteneur utilise Snakemake par défaut.

Snakefile

Le Snakefile définit les règles du pipeline. Il exécute plusieurs étapes sur le génome en entrée, telles que l'annotation, la recherche de gènes de virulence, la recherche de gènes de résistance aux antibiotiques et la génération d'un arbre phylogénétique.
Snakefile (extrait)

# Fichier génome
GENOME = "Genome.fasta"

# Règle pour annoter le génome
rule annotate_genome:
    input:
        GENOME
    output:
        "results/annotations/annotated.gff"
    shell:
        """
        set -x
        mkdir -p results/annotations
        prokka --outdir results/annotations --prefix annotated --force {input}
        """

# Règle pour identifier les gènes de virulence
rule find_virulence_genes:
    input:
        GENOME
    output:
        "results/virulence/virulence_genes.tsv"
    shell:
        """
        mkdir -p results/virulence
        abricate --db vfdb {input} > {output}
        """

# Règle pour identifier les gènes de résistance aux antibiotiques
rule find_resistance_genes:
    input:
        GENOME
    output:
        "results/resistance/resistance_genes.tsv"
    shell:
        """
        mkdir -p results/resistance
        abricate --db card {input} > {output}
        """

# Règle pour générer l'arbre phylogénétique
rule phylogenetic_tree:
    input:
        "aligned_sequences.fasta"
    output:
        "results/phylogeny/phylogenetic_tree.nwk"
    shell:
        """
        mkdir -p results/phylogeny
        fasttree -nt {input} > {output}
        """

# Règle par défaut
rule all:
    input:
        "results/annotations/annotated.gff",
        "results/virulence/virulence_genes.tsv",
        "results/resistance/resistance_genes.tsv",
        "results/phylogeny/phylogenetic_tree.nwk"

Conclusion

Ce pipeline fournit une méthode complète et automatisée pour analyser le génome de Corynebacterium diphtheriae, identifier les gènes de virulence et de résistance, et construire un arbre phylogénétique. Il peut être exécuté de manière transparente dans un environnement Docker, assurant ainsi une reproductibilité des analyses sur différentes plateformes.

TALL Mamadou Lamine Ph.D - Bioinformatique
