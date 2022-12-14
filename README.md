# harbor-on-k8s [TODO]
Tutorial for Harbor on k8s

## Prerequisite

- [Minikube](https://minikube.sigs.k8s.io/docs/start/)
- Helm v3.2.0+

## Setup

### Create Cluster
Create minikube cluster

```
make cluster
```

```
minikube start driver=docker --profile harbor
π  [harbor] Darwin 12.1 (arm64) μ minikube v1.26.0
π  minikube 1.27.1 μ΄ μ¬μ©κ°λ₯ν©λλ€! λ€μ κ²½λ‘μμ λ€μ΄λ°μΌμΈμ: https://github.com/kubernetes/minikube/releases/tag/v1.27.1
π‘  ν΄λΉ μλ¦Όμ λΉνμ±ννλ €λ©΄ λ€μ λͺλ Ήμ΄λ₯Ό μ€ννμΈμ. 'minikube config set WantUpdateNotification false'
β¨  μλμ μΌλ‘ docker λλΌμ΄λ²κ° μ νλμμ΅λλ€
π  Using Docker Desktop driver with root privileges
π  harbor ν΄λ¬μ€ν°μ harbor μ»¨νΈλ‘€ νλ μΈ λΈλλ₯Ό μμνλ μ€
π  λ² μ΄μ€ μ΄λ―Έμ§λ₯Ό λ€μ΄λ°λ μ€ ...
π₯  Creating docker container (CPUs=2, Memory=7803MB) ...

π§―  Docker is nearly out of disk space, which may cause deployments to fail! (88% of capacity). You can pass '--force'
 to skip this check.
π‘  κΆμ₯:

    Try one or more of the following to free up space on the device:

    1. Run "docker system prune" to remove unused Docker data (optionally with "-a")
    2. Increase the storage allocated to Docker for Desktop by clicking on:
    Docker icon > Preferences > Resources > Disk Image Size
    3. Run "minikube ssh -- docker system prune" if using the Docker container runtime
πΏ  κ΄λ ¨ μ΄μ: https://github.com/kubernetes/minikube/issues/9024

π³  μΏ λ²λ€ν°μ€ v1.24.1 μ Docker 20.10.17 λ°νμμΌλ‘ μ€μΉνλ μ€
    βͺ μΈμ¦μ λ° ν€λ₯Ό μμ±νλ μ€ ...
    βͺ μ»¨νΈλ‘€ νλ μΈμ΄ λΆν...
    βͺ RBAC κ·μΉμ κ΅¬μ±νλ μ€ ...
π  Kubernetes κ΅¬μ± μμλ₯Ό νμΈ...
    βͺ Using image gcr.io/k8s-minikube/storage-provisioner:v5
π  μ λμ¨ νμ±ν : storage-provisioner, default-storageclass
π  λλ¬μ΅λλ€! kubectlμ΄ "harbor" ν΄λ¬μ€ν°μ "default" λ€μμ€νμ΄μ€λ₯Ό κΈ°λ³Έμ μΌλ‘ μ¬μ©νλλ‘ κ΅¬μ±λμμ΅λλ€.
```

### Deploy Harbor with helm

Deploy Harbor

```
make harbor
```

Check pod

```
kubectl get pod
```

```
NAME                                    READY   STATUS    RESTARTS        AGE
harbor-chartmuseum-5796b58557-q5tj2     1/1     Running   0               4m17s
harbor-core-7b7f645d5b-sq8j7            1/1     Running   1 (2m41s ago)   4m17s
harbor-database-0                       1/1     Running   0               4m17s
harbor-jobservice-7dd6577b55-cr7dc      1/1     Running   4 (2m31s ago)   4m17s
harbor-notary-server-8546c954b4-g7cc5   1/1     Running   1 (3m22s ago)   4m17s
harbor-notary-signer-76d5f7f6bf-bb4fc   1/1     Running   2 (3m6s ago)    4m17s
harbor-portal-7d99b4fdc6-bvvck          1/1     Running   0               4m17s
harbor-redis-0                          1/1     Running   0               4m17s
harbor-registry-6876588c9d-f8kd9        2/2     Running   0               4m17s
harbor-trivy-0                          1/1     Running   0               4m17s
```

Check PVC and PV. Harbor use persistence volume. Check [the values of a harbor chart](https://github.com/goharbor/harbor-helm/blob/2e2d2f2c4f2a971cbe89dd58d6ddc88527f5b0c6/values.yaml#L211).


```
kubectl get PVC
```

```
NAME                              STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
data-harbor-redis-0               Bound    pvc-4cf7d8c5-7dae-4341-b94b-d03cc399fa88   1Gi        RWO            standard       5m42s
data-harbor-trivy-0               Bound    pvc-3f32ffb2-a041-43a2-a80e-c5da8c58024a   5Gi        RWO            standard       5m42s
database-data-harbor-database-0   Bound    pvc-a95687dd-8bcb-45ad-877b-3942500646ff   1Gi        RWO            standard       5m42s
harbor-chartmuseum                Bound    pvc-85db53f5-a7e4-4912-80a8-a00657cd6963   5Gi        RWO            standard       5m42s
harbor-jobservice                 Bound    pvc-1a4432d0-f892-4133-b637-9e21179fb041   1Gi        RWO            standard       5m42s
harbor-jobservice-scandata        Bound    pvc-9a37fae1-f05f-4e78-91bc-4a49036d0692   1Gi        RWO            standard       5m42s
harbor-registry                   Bound    pvc-e170e61f-aaea-4a7b-b7c7-997e55c2a58e   5Gi        RWO            standard       5m42s
```

kubectl get PV

```
kubectl get pv
```

```
NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                                     STORAGECLASS   REASON   AGE
pvc-1a4432d0-f892-4133-b637-9e21179fb041   1Gi        RWO            Delete           Bound    default/harbor-jobservice                 standard                6m24s
pvc-3f32ffb2-a041-43a2-a80e-c5da8c58024a   5Gi        RWO            Delete           Bound    default/data-harbor-trivy-0               standard                6m24s
pvc-4cf7d8c5-7dae-4341-b94b-d03cc399fa88   1Gi        RWO            Delete           Bound    default/data-harbor-redis-0               standard                6m23s
pvc-85db53f5-a7e4-4912-80a8-a00657cd6963   5Gi        RWO            Delete           Bound    default/harbor-chartmuseum                standard                6m24s
pvc-9a37fae1-f05f-4e78-91bc-4a49036d0692   1Gi        RWO            Delete           Bound    default/harbor-jobservice-scandata        standard                6m24s
pvc-a95687dd-8bcb-45ad-877b-3942500646ff   1Gi        RWO            Delete           Bound    default/database-data-harbor-database-0   standard                6m23s
pvc-e170e61f-aaea-4a7b-b7c7-997e55c2a58e   5Gi        RWO            Delete           Bound    default/harbor-registry                   standard                6m24s
```


