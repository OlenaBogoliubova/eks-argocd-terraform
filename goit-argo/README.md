GoIT ArgoCD EKS GitOps Project
Цей проєкт використовує Terraform для розгортання інфраструктури (AWS EKS, VPC) та ArgoCD для автоматичного розгортання Kubernetes-додатків з Git-репозиторію.

1. Запуск інфраструктури за допомогою Terraform
Для розгортання кластера EKS та встановлення ArgoCD.

Перехід до робочої директорії Terraform
cd terraform/argocd
1.1 Ініціалізація (Init)
Ініціалізувати Terraform, щоб завантажити необхідні провайдери та налаштувати бекенд стану.

terraform init
1.2 Розгортання (Apply)
Застосувати конфігурації, щоб створити всі ресурси AWS (VPC, EKS Cluster, Worker Nodes) та встановити ArgoCD.

terraform apply

2. Перевірка роботи ArgoCD
Після успішного terraform apply або встановлення ArgoCD (за допомогою маніфестів), переконатися, що всі компоненти працюють у просторі імен argocd.

2.1 Перевірка Pods
Переконатися, що всі поди ArgoCD знаходяться у стані Running та READY.

kubectl get pods -n argocd
2.2 Перевірка Сервісу
Переконатися, що сервіс веб-інтерфейсу ArgoCD існує:

kubectl get svc -n argocd

3. Відкрити UI ArgoCD та виконати вхід
3.1 Тимчасовий доступ (Port-Forward)
Для безпечного та тимчасового доступу з локального комп'ютера використати kubectl port-forward.

kubectl port-forward svc/argocd-server 8080:443 -n argocd
Відкрити у браузері: https://localhost:8080

3.2 Логін (Initial Login)
Користувач: admin

Пароль: Початковий пароль адміністратора генерується автоматично і зберігається у секреті. Отримати його можна за допомогою цієї команди:

kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
Використати отриманий пароль для першого входу.

4. Перевірка деплою аплікації
Якщо ArgoCD налаштовано для автоматичного моніторингу цього Git-репозиторію, він повинен створити ресурси в Kubernetes.

4.1 Перевірка статусу ArgoCD Application
Перевірити статус ArgoCD Application:

kubectl get application <APP_NAME> -n argocd

4.2 Перевірка Kubernetes-ресурсів
Перевірити, чи був створений простір імен application-ns та чи працює Nginx:

kubectl get ns application-ns

kubectl get deploy -n application-ns

Якщо є Nginx Deployment зі статусом READY, це означає, що деплой відбувся успішно.

5. Посилання на Git-репозиторій
ArgoCD використовує файли з цього репозиторію для розгортання додатків.

Посилання на кореневий каталог маніфестів:
[Git-посилання]/tree/main/goit-argo/

Посилання на маніфест аплікації Nginx:
[Git-посилання]/blob/main/goit-argo/application.yaml 