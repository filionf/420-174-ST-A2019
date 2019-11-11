---
title: Paiements en ligne
description: Stripe
---


##	Introduction

Les étapes du paiement en ligne sont relativement simples. Nous sommes tous de plus en plus habitués à l’utiliser. Pour effectuer une transaction, il suffit d’échanger les informations sur le client, son mode de paiement pour ensuite se faire payer.

À notre niveau, toutefois, l’élément le plus important d’avoir en tête lorsqu’on fait des paiements en ligne est la sécurité. Toutes les personnes impliquées dans le traitement des transactions doivent être certifiées PCI DSS (Payment Card Industry Security Data Security Standard). Cette certification est gérée par le "Payment Card Industry Security Standard Concil" : [https://www.pcisecuritystandards.org/](https://www.pcisecuritystandards.org/){:target="_blank"}.

Des questionnaires peuvent être remplis pour valider votre conformité par vous-même:
[https://www.pcisecuritystandards.org/document_library?category=saqs#results](https://www.pcisecuritystandards.org/document_library?category=saqs#results){:target="_blank"}

## Stripe
### Introduction
Utiliser un API externe tel que Stripe simplifie beaucoup le niveau de conformité que vous avez besoin d’obtenir parce que vos serveurs ne recevront jamais de données sur les cartes. Ce sont des tokens qui sont échangés entre les parties.
L’API Stripe utilise un token "public", qui peut être utilisé en JavaScript puis un token privé, qui ne doit être utilisé que sur notre serveur et jamais partagé.

### Avantages d’utiliser des API externes
-	Gestion des cartes
-	Génération de factures
-	Support des "portefeuilles"
    - Apple Pay
    - Google Pay
    - Microsoft Pay
    - ...
-	Conversion de devises
-	Transactions programmées

### Désavantages des API externes
-	Frais de transactions 

### Processus de paiement
1. Obtenir les informations sur la carte
2. Transformer ces informations en un token avec l’API Stripe
3. Envoyer le token à notre serveur pour traiter la transaction.

Démarrage rapide : [https://stripe.com/docs/payments/accept-a-payment-charges#dotnet](https://stripe.com/docs/payments/accept-a-payment-charges#dotnet){:target="_blank"}  
Personnaliser le formulaire : [https://stripe.com/docs/stripe-js/reference#element-options](https://stripe.com/docs/stripe-js/reference#element-options){:target="_blank"}
