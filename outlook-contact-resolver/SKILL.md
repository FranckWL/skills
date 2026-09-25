---
name: outlook-contact-resolver
description: >-
  Résout automatiquement l'adresse email d'un destinataire depuis l'historique Outlook, demande confirmation (avec possibilité de modifier), puis enchaîne sur la rédaction de l'email. Utiliser dès que l'utilisateur mentionne l'envoi d'un email ou d'un message à une personne nommée. Phrases déclencheurs : "envoie un email à", "écris un message à", "je veux contacter", "envoyer un mail à", "send an email to", "write to", "message à", "contacter [prénom nom]", "mail pour", "email pour".
allowed-tools: search_emails list_emails get_email
metadata:
  version: 1.0.0
  tags: email outlook contacts composition communication
  author: Franck LAY
---

# Outlook Contact Resolver

Active dès que l'utilisateur mentionne l'envoi d'un email ou d'un message à une personne nommée.

Exemples de déclencheurs :
- "Envoie un email à Jean Dupont"
- "Écris un message à Marie Martin"
- "Je veux contacter Thomas Leroy"
- "Send an email to Anna Schmidt"
- "Mail pour Pierre Bernard"

---

## Étape 1 — Extraire le nom du destinataire

Extrait le nom de la personne depuis le message de l'utilisateur. Si plusieurs noms sont mentionnés, traite-les un par un. Si le nom est ambigu ou incomplet, demande une clarification avant de continuer.

---

## Étape 2 — Rechercher l'adresse dans l'historique Outlook

Appelle `search_emails` avec le nom complet de la personne comme requête :

```
search_emails(query="[Prénom Nom]", top=20)
```

Parcours les résultats et extrait toutes les adresses email **uniques** associées à ce nom, en cherchant dans les champs expéditeur (`from`) et destinataires (`to`, `cc`).

Si le premier résultat est insuffisant, essaie également :
```
search_emails(query="from:[Prénom Nom]", top=10)
```

---

## Étape 3 — Présenter l'adresse et demander confirmation

### Cas 1 — Une seule adresse trouvée

Présente-la clairement et demande confirmation ou modification :

> J'ai trouvé l'adresse suivante pour **[Nom]** :
> `adresse@domaine.com`
>
> C'est la bonne ? Confirmez, ou saisissez une adresse différente.

### Cas 2 — Plusieurs adresses trouvées

Liste toutes les adresses trouvées et demande à l'utilisateur de choisir ou d'en saisir une autre :

> J'ai trouvé plusieurs adresses pour **[Nom]** :
> 1. `adresse1@domaine.com`
> 2. `adresse2@domaine.com`
>
> Laquelle souhaitez-vous utiliser ? Vous pouvez aussi saisir une adresse différente.

### Cas 3 — Aucune adresse trouvée

> Je n'ai pas trouvé d'adresse email pour **[Nom]** dans votre historique Outlook.
> Pouvez-vous me fournir l'adresse directement ?

---

## Étape 4 — Traiter la réponse de confirmation

- L'utilisateur **confirme** → utilise l'adresse telle quelle
- L'utilisateur **choisit** parmi les options proposées → utilise celle sélectionnée
- L'utilisateur **saisit ou corrige** une adresse → utilise l'adresse fournie par l'utilisateur
- L'utilisateur **dit "non"** ou indique que c'est incorrect → demande l'adresse correcte

Ne pas continuer tant que l'adresse n'est pas explicitement confirmée ou fournie.

---

## Étape 5 — Rédiger l'email

Une fois l'adresse confirmée :

1. Si l'objet et le contenu ne sont pas encore connus, demande :
   > Quel est l'objet du message, et quels sont les points clés à inclure ?

2. Rédige l'email de manière professionnelle et concise, dans la langue de l'utilisateur (français par défaut).

3. Génère un lien `mailto:` cliquable qui s'ouvre directement dans Outlook :
   ```
   mailto:[adresse-confirmée]?subject=[objet encodé]&body=[corps encodé]
   ```
   Encode les espaces et caractères spéciaux (remplace les espaces par `%20`, les sauts de ligne par `%0A`).

4. Présente le brouillon à l'utilisateur avec le lien mailto: pour qu'il puisse l'ouvrir en un clic dans Outlook.

---

## Contrainte importante

Il n'existe pas d'outil de recherche directe dans le GAL Outlook. La recherche repose sur l'**historique des emails** — elle fonctionne bien pour les contacts avec qui l'utilisateur a déjà échangé. Si la personne est totalement nouvelle, demande l'adresse directement à l'étape 3 (cas 3).
