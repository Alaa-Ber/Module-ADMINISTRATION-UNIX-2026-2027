# TP n° 4 — Gestion des paquetages sous Linux (RHEL 9) : Atelier 1 (`rpm`)





Compte rendu pratique de l'**Atelier 1** du **TP n° 4 : Gestion des paquetages sous Linux**, consacré à l'utilisation du gestionnaire de paquetages de bas niveau **RPM** (*Red Hat Package Manager*) sur **Red Hat Enterprise Linux 9**.

## Informations académiques

| Champ | Détail |
| :--- | :--- |
| **Établissement** | École Marocaine des Sciences de l'Ingénieur (EMSI) |
| **Filière / Niveau** | 4IIR — Ingénierie Informatique et Réseaux |
| **Module** | Administration Unix |
| **Professeur** | Mr. Noreddine Abghour |
| **Étudiant** | ALAA BERGUIGUA |
| **Environnement** | Red Hat Enterprise Linux 9 (`x86_64`) |
| **Outils utilisés** | `rpm`, `wget`, `curl`, `wc` |

## Objectifs de l'atelier

- Recenser et compter l'ensemble des paquetages installés sur un système RHEL 9.
- Interroger les métadonnées, la liste des fichiers et les fichiers de configuration d'un paquetage installé ou d'une archive `.rpm`.
- Identifier le paquetage d'origine d'un fichier système ou d'une commande.
- Vérifier l'intégrité des fichiers installés (`rpm -V`) et la signature électronique GPG d'une archive (`rpm -K`).
- Installer un paquetage en ignorant les dépendances (`--nodeps`), vérifier son installation et le désinstaller proprement (`rpm -e`).
- Mettre en évidence les limites de `rpm` face à la résolution automatique des dépendances (cas de `wireshark`).

## Récapitulatif des manipulations réalisées

| Quest. | Objectif | Commande utilisée | Résultat observé |
| :--- | :--- | :--- | :--- |
| **Q1** | Lister et compter les paquetages installés | `rpm -qa \| wc -l` | `1202` paquetages installés |
| **Q2** | Vérifier `binutils` et afficher ses informations | `rpm -q binutils` & `rpm -qi binutils` | `binutils-2.35.2-72.el9.x86_64` (GPLv3+, 25,2 Mo) |
| **Q3** | Lister les fichiers du paquetage `binutils` | `rpm -ql binutils` | Binaires `/usr/bin/*` et `/usr/lib/.build-id/*` |
| **Q4** | Identifier le paquetage de `/etc/services` | `rpm -qf /etc/services` | `setup-2.13.7-10.el9.noarch` |
| **Q5** | Vérifier l'intégrité de `nfs-utils` et `pam` | `rpm -V nfs-utils` & `rpm -V pam` | `nfs-utils` non installé ; 5 fichiers de config `pam` modifiés (`....L....`) |
| **Q6** | Télécharger `zsh` et lister ses fichiers de config | `wget ...zsh-5.9-14.el10.x86_64.rpm` & `rpm -qcp ...` | 7 fichiers de config (`/etc/z*`, `/etc/skel/.z*`) |
| **Q7** | Vérifier la signature électronique de `zsh` | `rpm -K zsh-5.9-14.el10.x86_64.rpm` | `digests SIGNATURES PAS OK` (clé GPG `8483c65d` absente) |
| **Q8** | Installer `zsh` sans vérification de dépendances | `rpm -ivh --nodeps zsh-5.9-14.el10.x86_64.rpm` | Installation terminée à 100 % |
| **Q9** | Vérifier l'installation de `zsh` | `rpm -q zsh` | `zsh-5.9-14.el10.x86_64` |
| **Q10** | Lister les fichiers installés par `zsh` | `rpm -ql zsh` | `/usr/bin/zsh`, configs `/etc/` et `.build-id` |
| **Q11** | Désinstaller le paquetage `zsh` | `rpm -e zsh` | Suppression effectuée sans erreur |
| **Q12** | Identifier le paquetage de `/bin/ls` | `rpm -qf /bin/ls` | `coreutils-8.32-41.el9_8.1.x86_64` |
| **Q13** | Télécharger et tenter d'installer `wireshark` | `sudo rpm -ivh wireshark-*.rpm` | Échec : 15 dépendances requises (`Qt5`, `wireshark-cli`, etc.) |

## Points clés à retenir

- **Vérification d'intégrité (`rpm -V`)** : Le drapeau `L` sur les fichiers `/etc/pam.d/*` signale une différence de cible de lien symbolique (*readLink(2) path mismatch*) par rapport à l'installation d'origine, tandis que le marqueur `c` désigne des fichiers de configuration.
- **Signature GPG (`rpm -K`)** : Le statut `digests SIGNATURES PAS OK` confirme que l'archive n'est pas corrompue (`digests` valides), mais que la clé publique du dépôt CentOS Stream 10 n'est pas importée dans le trousseau RPM local de RHEL 9 (`NOKEY`).
- **Limites de `rpm` vs `dnf`/`yum`** : Lors de l'installation manuelle de `wireshark-3.4.10-6.el9.x86_64.rpm`, `rpm` détecte les 15 dépendances manquantes et bloque l'opération car il ne gère pas le téléchargement automatique depuis des dépôts distants (rôle assuré par `dnf` dans l'Atelier 2).

## Structure du dépôt

```text
.
├── README.md
├── Compte_Rendu_TP4_Atelier1_RPM.pdf   # Compte rendu complet illustré
├── TP4-Gestion des paquetages.pdf      # Énoncé officiel du TP n° 4
└── screenshots/                        # Captures d'écran terminal (Q1 à Q13)
```

## Consultation du compte rendu

Ouvrez directement le fichier **`Compte_Rendu_TP4_Atelier1_RPM.pdf`** à la racine du dépôt pour consulter les explications détaillées, les commandes exécutées, les 12 captures d'écran annotées et le tableau récapitulatif final.
