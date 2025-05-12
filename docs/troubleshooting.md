# Troubleshooting TGI Deployment

## Vérification de l'état du déploiement

### 1. Vérifier les pods et PVC
```bash
kubectl get pvc,pod -n tgi
```

### 2. Vérifier les détails d'un pod
```bash
# Remplacer <pod-name> par le nom du pod
kubectl describe pod -n tgi <pod-name>
```

### 3. Vérifier les événements du pod
```bash
# Remplacer <pod-name> par le nom du pod
kubectl describe pod -n tgi <pod-name> | grep -A 5 "Events:"
```

### 4. Vérifier les conditions du pod
```bash
# Remplacer <pod-name> par le nom du pod
kubectl describe pod -n tgi <pod-name> | grep -A 10 "Conditions:"
```

## Monitoring en temps réel

### 1. Surveiller les logs d'un pod
```bash
# Remplacer <pod-name> par le nom du pod
kubectl logs -n tgi <pod-name> -f
```

### 2. Vérifier l'état détaillé d'un pod
```bash
# Remplacer <pod-name> par le nom du pod
kubectl get pod -n tgi <pod-name> -o wide
```

### 3. Vérifier l'état d'un node
```bash
# Remplacer <node-name> par le nom du node
kubectl describe node <node-name>
```

### 4. Vérifier les ressources utilisées sur un node
```bash
# Remplacer <node-name> par le nom du node
kubectl describe node <node-name> | grep -A 10 "Allocated resources"
```

## Vérification des nœuds GPU

### 1. Lister tous les nœuds
```bash
kubectl get nodes
```

### 2. Vérifier les GPUs sur un nœud spécifique
```bash
# Remplacer <node-name> par le nom du nœud
kubectl describe node <node-name> | grep -i gpu
```

## Interprétation des résultats

### État du plugin GPU
Dans la sortie de `kubectl describe node`, cherchez la ligne avec `nvidia-gpu-device-plugin` :
```
kube-system    nvidia-gpu-device-plugin-small-cos-xxxxx    150m (3%)     1 (25%)     80Mi (0%)    80Mi (0%)    XXmXXs
```

Les indicateurs importants sont :
- `150m (3%)` : Utilisation CPU du plugin (doit être stable)
- `1 (25%)` : GPUs alloués (doit correspondre à vos besoins)
- `XXmXXs` : Âge du plugin (doit être > 5 minutes pour une initialisation complète)

### États possibles du pod
- `Pending` : En attente de ressources (GPU, PVC, etc.)
- `ContainerCreating` : En cours de création
- `Running` : En cours d'exécution
- `Error` : Erreur lors du démarrage

### États possibles du PVC
- `Pending` : En attente de provisionnement
- `Bound` : Lié à un PersistentVolume
- `Failed` : Échec du provisionnement

## Commandes utiles pour le nettoyage

### Supprimer un pod
```bash
kubectl delete pod -n tgi <pod-name>
```

### Supprimer un PVC
```bash
kubectl delete pvc -n tgi model-cache-pvc
```

### Redéployer avec Helm
```bash
helm upgrade tgi . -f values.prod.yaml --namespace tgi --create-namespace
``` 