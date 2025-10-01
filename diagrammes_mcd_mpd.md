# Diagrammes MCD et MPD

## MCD (Modèle Conceptuel de Données)
- **Entités** : Enfant, Profil Élève
- **Relations** : chaque Enfant possède un Profil Élève.

## MPD (Modèle Physique de Données)
- **Table Enfant** :  
  - ID (clé primaire)  
  - Nom  
  - Prénom  
  - Date de naissance  

- **Table Profil Élève** :  
  - ID (clé primaire)  
  - Enfant_ID (clé étrangère vers Enfant)  
  - Classe  
  - Date d'inscription  

### Relation entre Enfant et Profil Élève  
- Un Enfant peut avoir un ou plusieurs Profils Élève.