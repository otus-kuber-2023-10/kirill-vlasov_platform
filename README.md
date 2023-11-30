# kirill-vlasov_platform
kirill-vlasov Platform repository

--- kubernetes-intro ---
В этой домашней работе я выполнил следующие шаги:
1. Развернул и запустил minikube (linux)
2. Собрал и запушил docker образ [quickrestodocker/web:1.0](kubernetes-intro/web/Dockerfile)
3. Создал конфигурационный файл [web-pod.yaml](kubernetes-intro/web-pod.yaml) для запуска этого проекта в minikube 
4. После запуска все собралось, проинициализировалось (initContainers), пробросил порты и проверил работу - отобразился 
index.html c космическим кораблем 42 :)
5. Скачал и собрал образ frontend для проекта hipster stop, запушил в докерхаб.
6. Запустил через kubectl run, поймал ошибку старта pod
7. Сформировал [frontend-pod-healthy.yaml](kubernetes-intro/frontend-pod-healthy.yaml) для корректного запуска.
По логам пода ясно, что приложение крешится из-за отсутствия необходимых переменных окружения. Соответственно в 
новом конфигурационном файле они добавлены. 
8. Применил (kubectl apply) файл frontend-pod-healthy.yaml, pod стартовал.


# Выполнено ДЗ №4

- [V] Основное ДЗ kubernetes-volumes
- [V] Задание со *

## В процессе сделано:
- Запустил кластер инструментом kind
- Применил конфигурацию StatefulSet из файла [minio-statefulset.yaml](kubernetes-volumes%2Fminio-statefulset.yaml)
- Применил конфигурацию для создания сервиса [minio-headlessservice.yaml](kubernetes-volumes%2Fminio-headlessservice.yaml)
- В результате запустился statefulset `minio`:
```
❯ kubectl get statefulsets
NAME    READY   AGE
minio   1/1     3h10m
```
сервис:
```
❯ kubectl get pods
NAME      READY   STATUS    RESTARTS   AGE
minio-0   1/1     Running   0          7m21s
```

persistent volume claim:
```
❯ kubectl get pvc
NAME           STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
data-minio-0   Bound    pvc-517361d2-387f-4045-90c0-f4c1f5fc1dd8   10Gi       RWO            standard       3h11m
```
и persistent volume:
```
❯ kubectl get pv
NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                  STORAGECLASS   REASON   AGE
pvc-517361d2-387f-4045-90c0-f4c1f5fc1dd8   10Gi       RWO            Delete           Bound    default/data-minio-0   standard                3h11m
```
- Создал файл конфигурации секретов для stateful set minio [minio-secrets.yaml](kubernetes-volumes%2Fminio-secrets.yaml) 
- Обновил файл [minio-statefulset.yaml](kubernetes-volumes%2Fminio-statefulset.yaml) добавив изменения по заданию
- Применил файл секретов и файл для stateful set
- Проверил, что секреты стали видны в переменных окружения сервиса:
```
  ❯ kubectl exec -it minio-0 bash -- /bin/sh -c 'echo $MINIO_ACCESS_KEY'
  minio
  ❯ kubectl exec -it minio-0 bash -- /bin/sh -c 'echo $MINIO_SECRET_KEY'
  minio123
```


# Выполнено ДЗ №7

- [V] Основное ДЗ kubernetes-security

## В процессе сделано:
- Запустил кластер инструментом kind и minikube
- Создал файлы конфигурации согласно заданию в директориях taskXX
- Проверил работу заданий, через `kubectl describe rolebindings`, например для task03:

```bash
❯ kubectl apply -f 01-namespace.yml
namespace/dev created
❯ kubectl apply -f 02-user-jane.yml
serviceaccount/jane created
❯ kubectl apply -f ./03-role-admin.yml
role.rbac.authorization.k8s.io/admin created
❯ kubectl apply -f 04-role-admin-binding.yml
rolebinding.rbac.authorization.k8s.io/admin-role-binding created
❯ kubectl apply -f 05-user-ken.yml
serviceaccount/ken created
❯ kubectl apply -f 06-role-view.yml
role.rbac.authorization.k8s.io/view created
❯ kubectl apply -f 07-role-view-binding.yml
rolebinding.rbac.authorization.k8s.io/view-role-binding created

❯ kubectl get roles -n dev
NAME    CREATED AT
admin   2023-11-26T17:57:58Z
view    2023-11-26T17:59:21Z
❯ kubectl describe rolebindings.rbac.authorization.k8s.io admin -n dev
Name:         admin-role-binding
Labels:       <none>
Annotations:  <none>
Role:
  Kind:  Role
  Name:  admin
Subjects:
  Kind  Name  Namespace
  ----  ----  ---------
  User  jane  dev

❯ kubectl describe rolebindings.rbac.authorization.k8s.io view -n dev
Name:         view-role-binding
Labels:       <none>
Annotations:  <none>
Role:
  Kind:  Role
  Name:  view
Subjects:
  Kind  Name  Namespace
  ----  ----  ---------
  User  ken   dev
```
