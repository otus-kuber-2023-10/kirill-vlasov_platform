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
