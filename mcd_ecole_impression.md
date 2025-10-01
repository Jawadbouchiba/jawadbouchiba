# Diagramme MCD et MPD - École/Impression

## Énoncé
**Dans une école, chaque professeur a un bureau. Dans chaque bureau est installée une imprimante reliée au serveur de l'école. À chaque travail d'impression, des statistiques sont conservées. L'école veut connaître le nombre d'impression de ses professeurs.**

## MCD (Modèle Conceptuel de Données)

```
┌─────────────┐    ┌─────────────┐    ┌─────────────┐    ┌─────────────┐
│ PROFESSEUR  │    │   BUREAU    │    │ IMPRIMANTE  │    │ IMPRESSION  │
├─────────────┤    ├─────────────┤    ├─────────────┤    ├─────────────┤
│ nom         │    │ numero      │    │ marque      │    │ date_heure  │
│ prenom      │    │ etage       │    │ modele      │    │ nb_pages    │
│ specialite  │    │ batiment    │    │ ip_address  │    │ format      │
│ email       │    │             │    │ statut      │    │ couleur     │
└─────────────┘    └─────────────┘    └─────────────┘    └─────────────┘
       │                  │                  │                  │
       │    OCCUPE        │    CONTIENT      │    GENERE        │
   (1,1)──────(1,1)   (1,1)──────(1,1)   (1,N)──────(0,N)
```

## MPD (Modèle Physique de Données)

```
┌─────────────────────────────────┐
│          PROFESSEUR             │
├─────────────────────────────────┤
│ id_professeur  INTEGER PK       │
│ nom            VARCHAR(50)      │
│ prenom         VARCHAR(50)      │
│ specialite     VARCHAR(30)      │
│ email          VARCHAR(100)     │
│ id_bureau      INTEGER FK UNIQUE│
└─────────────────────────────────┘
                 │
                 │ 1:1
                 ▼
┌─────────────────────────────────┐
│            BUREAU               │
├─────────────────────────────────┤
│ id_bureau      INTEGER PK       │
│ numero         VARCHAR(10)      │
│ etage          INTEGER          │
│ batiment       VARCHAR(20)      │
│ id_imprimante  INTEGER FK UNIQUE│
└─────────────────────────────────┘
                 │
                 │ 1:1
                 ▼
┌───────────────────────────────���─┐
│          IMPRIMANTE             │
├─────────────────────────────────┤
│ id_imprimante  INTEGER PK       │
│ marque         VARCHAR(30)      │
│ modele         VARCHAR(50)      │
│ ip_address     VARCHAR(15)      │
│ statut         VARCHAR(20)      │
└─────────────────────────────────┘
                 │
                 │ 1:N
                 ▼
┌─────────────────────────────────┐
│          IMPRESSION             │
├─────────────────────────────────┤
│ id_impression  INTEGER PK       │
│ id_imprimante  INTEGER FK       │
│ id_professeur  INTEGER FK       │
│ date_heure     DATETIME         │
│ nb_pages       INTEGER          │
│ format         VARCHAR(10)      │
│ couleur        BOOLEAN          │
│ taille_fichier INTEGER          │
└─────────────────────────────────┘
```

## Explications

### MCD
- **PROFESSEUR OCCUPE BUREAU** : (1,1) - (1,1) → Chaque professeur a exactement un bureau
- **BUREAU CONTIENT IMPRIMANTE** : (1,1) - (1,1) → Chaque bureau a exactement une imprimante
- **IMPRIMANTE GENERE IMPRESSION** : (1,1) - (0,N) → Une imprimante peut générer 0 ou plusieurs impressions

### MPD
- Relation 1:1 entre PROFESSEUR et BUREAU (FK id_bureau dans PROFESSEUR)
- Relation 1:1 entre BUREAU et IMPRIMANTE (FK id_imprimante dans BUREAU)
- Relation 1:N entre IMPRIMANTE et IMPRESSION (FK id_imprimante dans IMPRESSION)
- FK id_professeur dans IMPRESSION pour tracer qui a imprimé

### Requêtes utiles
```sql
-- Nombre total d'impressions par professeur
SELECT p.nom, p.prenom, COUNT(i.id_impression) as nb_impressions
FROM PROFESSEUR p
LEFT JOIN BUREAU b ON p.id_bureau = b.id_bureau
LEFT JOIN IMPRIMANTE imp ON b.id_imprimante = imp.id_imprimante
LEFT JOIN IMPRESSION i ON imp.id_imprimante = i.id_imprimante
GROUP BY p.id_professeur;

-- Statistiques d'impression par bureau
SELECT b.numero, COUNT(i.id_impression) as total_impressions, 
       SUM(i.nb_pages) as total_pages
FROM BUREAU b
JOIN IMPRIMANTE imp ON b.id_imprimante = imp.id_imprimante
LEFT JOIN IMPRESSION i ON imp.id_imprimante = i.id_imprimante
GROUP BY b.id_bureau;
```
