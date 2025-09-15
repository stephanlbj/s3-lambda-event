# Next.js Deployment Pipeline with AWS Lambda & SNS Notifications

## Objectif

Ce projet automatise le déploiement d'une application **Next.js** sur un bucket **S3** via **GitHub Actions** et déclenche une **Lambda** après chaque déploiement pour :

- Logger le statut dans **CloudWatch**
- Envoyer une **notification par email** via **SNS**

---

## GitHub Actions Workflow

Le workflow se déclenche sur un push sur la branche `main` et comprend les étapes suivantes :

1. **Checkout du repository**
2. **Setup Node.js 18+**
3. **Installation des dépendances** (`npm ci`)
4. **Build Next.js** (`npm run build`)
5. **Sync avec S3** (`jakejarvis/s3-sync-action`)
6. **Configuration des credentials AWS** pour l'invocation Lambda
7. **Invocation de la Lambda** avec un payload JSON :

8. **Affichage de la réponse de la Lambda (response.json)**

**Bucket S3**

Contient la version exportée statique de Next.js (out/)
Synchronisation avec l'option --delete pour ne garder que les fichiers à jour

**AWS Lambda (NotifyDeployLambda)**

Runtime : Node.js 22.x

Fichier principal : index.mjs

Fonctionnalités :

Log du payload dans CloudWatch

Envoi d'un email via SNS si source = "github-actions"

Dépendances : @aws-sdk/client-sns

Package de déploiement : index.mjs + node_modules + package.json + package-lock.json

Déploiement : via console Lambda ou AWS CLI

**Permissions IAM**

Rôle Lambda (LambdaDeployRole) :

AWSLambdaBasicExecutionRole → logs CloudWatch

Policy LambdaPublishSNS → sns:Publish sur le topic DeployNotifications

GitHub Actions : AWS credentials via aws-actions/configure-aws-credentials

**SNS Topic**

Nom : DeployNotifications

Contient un abonnement email pour recevoir les notifications de déploiement

ARN configuré dans la Lambda

**Tests et Validation**

Test direct de la Lambda via la console AWS avec payload JSON

Vérification des logs dans CloudWatch

Vérification de la réception du mail SNS

Pipeline GitHub Actions testé et fonctionnel : déploiement S3 + invocation Lambda + notification
