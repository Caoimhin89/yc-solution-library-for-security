# ☑️ Yandex-Cloud-Security-Checklist

**Yandex-Cloud-Security-Checklist** — это 

- 🔵 **Сетевая безопасность**:
  - ☑️ **Сегментация**: разделите ресурсы по группам и поместите их в разные каталоги либо разные VPC (в случае необходимости наиболее строгой изоляции), посмотрите вебинар 📹[Как работает сеть в Облаке](https://www.youtube.com/watch?v=g3cZ0o50qH0). PS: внутри VPC траффик по умолчанию разрешен, между VPC нет (только через routing vm(2 ports), vpn, Cloud interconnect)
  - ☑️ **Ограничение сетевого доступа - "Группы безопасности"**: ограничьте сетевой доступ между ресурсами с помощью встроенного межсетевого экрана ["Группы безопасности"](https://cloud.yandex.ru/docs/vpc/operations/security-group-create), используйте 🔐[Пример настройки Security Groups (dev/stage/prod): Terraform](https://github.com/yandex-cloud/yc-solution-library-for-security/tree/master/network-sec/segmentation)
  - ☑️ **NGFW из marketplace**: если требуется продвинутая сетевая защита используйте [NGFW из marketplace](https://cloud.yandex.ru/marketplace?categories=network), 🔐[[Пример установки в Яндекс Облако ВМ-Межсетевой экран (NGFW): Checkpoint](https://github.com/yandex-cloud/yc-solution-library-for-security/tree/master/network-sec/checkpoint-1VM)
  - ☑️ **Безопасный удаленный доступ из удаленных площадок (VPN)**: если требуется удаленный доступ до ресурсов облака, то настройте site-to-site VPN [Инструкция по созданию site-to-site VPN соединения с Yandex Cloud (strongSwan): UI](https://cloud.yandex.ru/docs/solutions/routing/ipsec-vpn), 🔐 [Пример создания site-to-site VPN соединения с Yandex Cloud: Terraform](https://github.com/yandex-cloud/yc-solution-library-for-security/tree/master/network-sec/vpn) либо воспользуйтесь услугой [Cloud Interconnect](https://cloud.yandex.ru/docs/vpc/interconnect/)(также доступна услуга ГОСТ VPN)
  - ☑️ **Безопасный удаленный доступ администраторов (VPN)**: сlient VPN между удаленными устройствами и Yandex Cloud ([ссылка на варианты в Marketplace](https://cloud.yandex.ru/marketplace?categories=network)), 📖[Инструкция по настройке ClientVPN на базе  OpenVPN](https://cloud.yandex.ru/docs/solutions/routing/openvpn)
  - ☑️ **Bastion host**: для доступа в инфраструктуру по управляющим протоколам (например ssh, rdp) рекомендуется создать бастионную виртуальную машину 
  - ☑️ **Исходящий доступ (NAT)**: используйте один из безопасных вариантов исходящего доступа в интернет:	[Egress NAT](https://cloud.yandex.ru/docs/vpc/operations/enable-nat)(встроенный сервис NAR), [NAT instance](https://cloud.yandex.ru/docs/solutions/routing/nat-instance#create-nat-instance)(отдельная NAT ВМ)
  - ☑️ **Защита от DDoS**: при назначении публичных IP адресов на ресурсы облака, используйте встроенный [DDoS-Protection](https://cloud.yandex.ru/docs/vpc/ddos-protection/) по кнопке (услуга L7 защиты от DDoS по запросу)

- 🔵  **Аутентификация и управление доступом**:
  - ☑️ **Централизованное управление и федерации удостоверений**: создайте организацию в сервисе [Yandex Cloud Organization](https://nmotina-feature-overview-access.farm2.cloud.yandex.ru/docs/organization/) и настройте федерацию удостоверений (Single Sign-On аутентификацию в Yandex.Cloud через свой сервер IdP). 📖[Пример настройки для ADFS](https://nmotina-feature-overview-access.farm2.cloud.yandex.ru/docs/organization/operations/federations/integration-adfs), 📖[Пример настройки для Keycloak](https://www.youtube.com/watch?v=m-oe7V9PvC4), 📖[Пример настройки для Google Workspace](https://nmotina-feature-overview-access.farm2.cloud.yandex.ru/docs/organization/operations/federations/integration-gworkspace)
  - ☑️ **Используйте федеративные аккаунты вместо аккаунтов Яндекс.Паспорта, где это возможно**
  - ☑️ **Принцип минимальных привелегий**: назначайте сервисные роли (например compute.images.user) вместо примитивных (viewer, editor, admin), [список всех ролей] (https://cloud.yandex.ru/docs/iam/concepts/access-control/roles), 📖 [пример назначения ролей](https://nmotina-feature-overview-access.farm2.cloud.yandex.ru/docs/iam/operations/roles/grant), посмотрите вебинар 📹 [Управление доступами в облаке](https://www.youtube.com/watch?v=7VwSfPZ6eRM&t=3s)
  - ☑️ **Используйте Terraform Yandex Cloud IAM module**: позволяет организовать группы доступов для пользователей облака и имеет ряд других удобных функций. [IAM модуль (с примерами использования)](https://github.com/yandex-cloud/yc-solution-library-for-security/tree/master/auth_and_access/iam#identity-and-access-management-iam-terraform-module-for-yandexcloud)
  - ☑️ **Работа с сервисными аккаунтами**: применяйте механизм [назначения сервисного аккаунта виртуальной машине](https://nmotina-feature-overview-access.farm2.cloud.yandex.ru/docs/compute/operations/vm-connect/auth-inside-vm) и получения токена через сервис метаданных. Настройте локальный файрвол на виртуальной машине так, чтобы только необходимые процессы и пользователи системы имели доступ к сервису метаданных (IP-адрес: 169.254.169.254), ссылка на решение (tbd)
  - ☑️ **Используйте 2FA**: настройке 2FA на стороне вашего IDP (при использовании федерации удостоверений), для паспортного аккаунта настройте 2FA согласно [инструкции](https://yandex.ru/support/id/authorization/twofa.html).
  - ☑️ **Защитите billing.accounts.owner**: выполните первоначальные операции под данным аккаунтом и после этого не используйте его. Для управления платежным аккаунтом назначьте роль admin или editor на платежный аккаунт выделенному сотруднику организации с федеративным аккаунтом. Для просмотра данных биллинга назначьте роль viewer на платежный аккаунт выделенному сотруднику организации с федеративным аккаунтом. 
  - ☑️ **Защитите resource-manager.clouds.owner**: назначьте роль resource-manager.clouds.owner сотрудникам организации с федеративным аккаунтом. Аккаунту Яндекс.Паспорта, с которым создано облако, назначьте сложный пароль и используйте только в случае крайней необходимости. Назначьте менее привелигированные доступы администраторам и используйте resource-manager.clouds.owner в случае крайней необходимости
  - ☑️ **Ресурсная модель**: Все критичные ресурсы, которые входят в область соответствия стандартам, поместите в отдельное облако, Группы ресурсов, разделите по каталогам, Общие ресурсы (например, сеть и группы безопасности) поместите в отдельный каталог для разделяемых ресурсов.

- 🔵 **Шифрование данных и управление ключами/секретами**:
  - ☑️ **Включиите server-side шифрование Yandex Object Storage**: [ссылка на инстуркцию](https://cloud.yandex.ru/docs/storage/operations/buckets/encrypt)
  - ☑️ **Используйте Шифрование диска ВМ в Облаке с помощью YC KMS (если требуется)**: в случае необходимости шифрования диска ВМ в облаке используйте 🔐[Шифрование диска ВМ в Облаке с помощью YC KMS](https://github.com/yandex-cloud/yc-solution-library-for-security/tree/master/encrypt_and_keys/encrypt_disk_VM)
  - ☑️ **Шифруйте данные с помощью client-side encryption (если требуется)**: возможно использования client-side encryption при помощи KMS [следующими библиотеками](https://cloud.yandex.ru/docs/kms/solutions/encrypt/) (KMS использует схему шифрования AES-GCM. Клиент может выбрать длину ключа: 128/192/256 — и настроить период ротации ключей в зависимости своих потребностей)
  - ☑️ **Защита ключей KMS**: выдавайте только точечные права на KMS ключ -  kms.keys.encrypterDecrypter, используйте [ротацию ключей](https://cloud.yandex.ru/docs/kms/concepts/version)
  - ☑️ **Управление секретами**: используйте SecretManager для работы с секретами: [Yandex Lockbox](https://cloud.yandex.ru/docs/lockbox/) либо [HashiCorp Vault c поддержкой KMS](https://cloud.yandex.ru/marketplace/products/f2eokige6vtlf94uvgs2) (из marketplace)

- 🔵 **Безопасная конфигурация**:
  - ☑️ **Следите за default паролями в ПО внутри ВМ**: Организационно и технически с помощью различных сканнеров уязвимостей
  - ☑️ **Конфигурируйте ОС и ПО в соответствии с baseline и стандартами**: для автоматизации соответствия различным стандартам и baseline (CIS, PCI DSS, др), например [OpenSCAP](https://www.open-scap.org/getting-started/)
  - ☑️ **Старайтесь не использовать серифнйю консоль**: если необходимо то [оценивайте указанные риски](https://cloud.yandex.ru/docs/compute/operations/serial-console/) и отклюайте по итогу работы
  - ☑️ **Безопасное использование Terraform**: используйте terraform remote state на базе Yandex Cloud Object Storage с функцией блокировки в Yandex Database [Ссылка на решение](https://github.com/yandex-cloud/examples/tree/master/terraform-ydb-state). Где необходимо: используйте параметр [“sensitive = true”](https://www.terraform.io/docs/language/values/outputs.html#sensitive-suppressing-values-in-cli-output)
  - ☑️ **Используйте контроль целостности на гостевых ОС**: возможно имплементировать с помощью беплатных host-based решений: Wazuh, Osquery либо с помощью платных решений в marketplace (Kaspersky security).
  - ☑️ **Безопасная конфигурация Object Storage**: шифрование (описано выше) + [Bucket Policy](https://cloud.yandex.ru/docs/storage/concepts/policy)/ACL + [версионирование(защита от удаления)](https://cloud.yandex.ru/docs/storage/concepts/versioning) +  [включить встроенный аудит доступа](https://cloud.yandex.ru/docs/storage/operations/buckets/enable-logging) + настройки CORS (при необходимости). 🔐[Пример безопасной конфигурации Yandex Cloud Object Storage: Terraform](https://github.com/yandex-cloud/yc-solution-library-for-security/tree/master/configuration/hardering_bucket)
  - ☑️ **Безопасная конфигурация Cloud Functionse**: предоставлять токен сервисного аккаунта через [нативный механизм](https://nmotina-feature-overview-access.farm2.cloud.yandex.ru/docs/compute/operations/vm-connect/auth-inside-vm) с помощью назначенного сервисного аккаунта и метадаты. Старайтесь использовать [приватные функции](https://cloud.yandex.ru/docs/functions/operations/function-public)
  - ☑️ **Безопасная конфигурация Yandex Container Registry**: не рекомендуется использовать привилегированные контейнеры для запуска нагрузок. Используйте встроенный в сервис сканер уязвиомстей в образах (tbd ссылка на инстуркцию)
  - ☑️ **Используйте Yandex Certificate Manager**: [Yandex Certificate Manager](https://cloud.yandex.ru/docs/certificate-manager/) сервис для хранения, получения и обновления TLS-сертификатов от Let's Encrypt®, а также для загрузки собственных сертификатов. Интоегрирован с: Yandex Object Storage, Yandex API Gateway, Application LoadBalancer

- 🔵 **Защита от вредоносного кода**:
  - ☑️ **Защитите от вредоносного кода уровень ОС**: антивирусные решения [представлены в marketplace](https://cloud.yandex.ru/marketplace?categories=security), которые возможно устанавливать на ВМ в рамках сервиса Compute Cloud. 🔐[Развертывание Kaspersky Antivirus в Yandex.Cloud (Compute Instance, COI)](https://github.com/yandex-cloud/yc-solution-library-for-security/tree/master/malware-defense/kaspersy-install-in-yc)
  - ☑️ **Защитите от вредоносного кода уровень Сети**: NGFW/IDS/IPS (некоторые со встроенными Sandbox) [представлены в marketplace](https://cloud.yandex.ru/marketplace?categories=security)
  - ☑️ **Защитите от вредоносного кода уровень Образрв контейнеров**:  Используйте встроенный в сервис Yandex Container registry сканер уязвиомстей в образах (tbd ссылка на инстуркцию)

- 🔵 **Управление уязвимостями**:
  - ☑️ **Автоматизированное сканирование уязвимостей**: Примеры бесплатных сетевых сканеров: nmap, OpenVas, OWASP ZAP. Пример бесплатного сканера в виде агента на хостах: Wazuh, Tripwire
  - ☑️ **Проводите внешние сканирования безопасности**: [Правила проведения Внешних сканирований безопасности](https://cloud.yandex.ru/docs/overview/compliance/pentest)
  - ☑️ **Самостоятельно выполняйте обновления ПО, ОС**: Используйте автоматизированные инструменты обновлений.
  - ☑️ **Используйте WAF**: [WAF из marketplace](https://cloud.yandex.ru/marketplace?categories=security), 🔐[Cluster PT Application Firewall на базе Yandex.Cloud](https://github.com/yandex-cloud/yc-solution-library-for-security/tree/master/vuln-mgmt/unmng-waf-ptaf-cluster). Managed WAF (партнерство с Qrator) по запросу, [Установка уязвимого веб приложения (dvwa) в Яндекс Облаке (с помощью terraform) для тестирования managed WAF](https://github.com/yandex-cloud/yc-solution-library-for-security/tree/master/vuln-mgmt/vulnerable-web-app-waf-test)

- 🔵 **Сбор, мониторинг и анализ Аудит логов**:
  - ☑️ **Включите Audit trails**: [Yandex Audit Trails](https://cloud.yandex.ru/docs/audit-trails/quickstart) для всех облаков/каталогов
  - ☑️ **Собирайте события с уровня гостевых ОС и приложений**: например с помщоью [Yandex Managed Service for Elasticsearch](https://cloud.yandex.ru/docs/managed-elasticsearch/), или с помощью бесплатных решений: osquery, wazzuh и др.
  - ☑️ **При необходимости собирайте Flow logs**: например с помощью NGFW из marketplace либо бесплатное ПО (в планах встроенные возможности)
  - ☑️ **Настройке экспорт событий Audit Trails в SIEM**: 🔐[Экспорт в Yandex Managed Service for Elasticsearch (ELK)](https://github.com/yandex-cloud/yc-solution-library-for-security/tree/master/auditlogs/export-auditlogs-to-ELK_main), 🔐[Экспорт в ArcSight](https://github.com/yandex-cloud/yc-solution-library-for-security/tree/master/auditlogs/export-auditlogs-to-ArcSight), 🔐[Экспорт в Splunk](https://github.com/yandex-cloud/yc-solution-library-for-security/tree/master/auditlogs/export-auditlogs-to-Splunk), в любой другой SIEM с [помощью s3fs](https://cloud.yandex.ru/docs/storage/tools/s3fs)
  - ☑️ **Используйте заготовленные Use cases**: [Use cases и важные события безопасности в аудит логах](https://github.com/yandex-cloud/yc-solution-library-for-security/tree/master/auditlogs/_use_cases_and_searches)
  - ☑️ **(Альтернатива) Реагирование на Audit Trails с Cloud Functions**: Оповещения и реагирование на события ИБ Audit trails с помощью Cloud Logging/Cloud Functions + Telegram (tbd ссылка на решение)
  - ☑️ **Регулярный аудит state**: используйте [yc client](https://cloud.yandex.ru/docs/cli/) для запросов к текущему состоянию облачной инфраструктуры, либо партнерское решение [Cloud Advisor](https://cloud.yandex.ru/blog/posts/2021/03/cloud-advisor-review)

- 🔵 **Физическая безопасность**:
  - ☑️ **Ознакомится с мерами физической безопасности**: [Ссылка на подробное описание мер физической безопасности облака](https://cloud.yandex.ru/docs/overview/security/standarts#physic-sec)

- 🔵 **Резервное копирование**:
  - ☑️ **Выполнять регулярные резервные копии**: [Создание снимков дисков по расписанию с Yandex Cloud Functions](https://cloud.yandex.ru/blog/posts/2020/01/snapshot-triggers)

- 🔵 **Управление бюджетами**:
  - ☑️ **Настройте уведомления для контроля расходов в билинге**: [Установка уведомлений на пороги бюджета](https://cloud.yandex.ru/docs/billing/operations/budgets)

- 🔵 **Реагирование на инциденты**:
  - ☑️ **Выстроить процедуру реагирования на инциденты**: в Yandex Cloud возможно оформлять [запрос на дополнительных логов](https://cloud.yandex.ru/docs/support/request)

- 🔵 **Безопасность Managed Service for Kubernetes**:
  - **Шифрование данных и управление ключами/секретами Managed Kubernetes**:
    - ☑️ **Включиите server-side шифрование в k8s**: включение шифрования secrets в etcd [ссылка на инстуркцию](https://cloud.yandex.ru/docs/managed-kubernetes/operations/encrypting-secrets)
    - ☑️ **Управление секретами**: используйте SecretManager для работы с секретами: [Yandex Lockbox](https://cloud.yandex.ru/docs/lockbox/) либо [HashiCorp Vault c поддержкой KMS](https://cloud.yandex.ru/marketplace/products/f2eokige6vtlf94uvgs2) (из marketplace). [Управление секретами c SecretManager(Lockbox,Vault)](https://github.com/yandex-cloud/yc-solution-library-for-security/tree/master/kubernetes-security/encrypt_and_keys/secret-management)
  - **Сетевая безопасность**:
    - ☑️ **Настройте группы безопасности для k8s**: [подробная инструкция](https://cloud.yandex.ru/docs/managed-kubernetes/operations/security-groups). Не рекомендуется давать публичный доступ и публичные адреса компонентам k8s
    - ☑️ **Используйте Ingress контроллер**: для доступа извне к сервисам k8s используйте Ingress контроллер (https) с типом LoadBalancer (внешний либо внутренний)[Инструкция по настройке](https://cloud.yandex.ru/docs/managed-kubernetes/solutions/ingress-cert-manager)
    - ☑️ **Сетевые политики (Network Policy)**: ограничьте доступ внутри k8s с помощью [network policy calico](https://cloud.yandex.ru/docs/managed-kubernetes/operations/calico) либо продвинутых [network policy cilium](https://cloud.yandex.ru/docs/managed-kubernetes/operations/cilium)
  - **Аутентификация и управление доступом**:
    - ☑️ **Правильно построить ролевую модель в k8s**: [Детальное описание ролевого доступа в k8s](https://cloud.yandex.ru/docs/managed-kubernetes/security/#sa-annotation). 🔐[Пример настройки ролевых моделей и политик в Managed Service for Kubernetes](https://github.com/yandex-cloud/yc-solution-library-for-security/tree/master/kubernetes-security/auth_and_access/role-model-example). Контролируйте права доступа сервисного аккаунта группы узлов (как правило достаточно: container-registry.images.puller)
  - **Безопасная конфигурация**:
    - ☑️ **Конфигурация группы узлов в соответствии с baseline и стандартами**: настройке группы узлов в соответствии со стандартами и baseline: NIST, CIS, др. Возможно использовать автоматизированные инструменты: kubebench, kubescape
    - ☑️ **Используйте runtime security и Policy Engine**: пример runtime security: Falco, примеры Policy Engine: OPA Gatekeeper, Kyverno. 🔐[Пример установки Falco+Policy Engine с отправкой alerts в managed ELK](https://github.com/yandex-cloud/yc-solution-library-for-security/tree/master/auditlogs/export-auditlogs-to-ELK_k8s)
    - ☑️ **Обновления безопасности**: выберите подходящий [канал обновления](https://cloud.yandex.ru/docs/managed-kubernetes/concepts/release-channels-and-updates) и настройте автоматическое применение обновлений либо применяйте их вручную сразу после публикации в выбранном канале. Также выполняйте своевременное обновление собственного ПО на группах узлов
    - ☑️ **Распределяйте поды на разные группы узлов**: с помощью node taints and tolerations + node affinity
  - **Cбор, мониторинг и анализ Аудит логов**:
    - ☑️ **Собирайте, анализируйте аудит логи k8s и инструментов защиты**: 🔐[Анализ логов безопасности k8s в Managed ELK: аудит-логи, policy engine, falco](https://github.com/yandex-cloud/yc-solution-library-for-security/tree/master/auditlogs/export-auditlogs-to-ELK_k8s)
    - ☑️ **Собирайте, анализируйте аудит логи workloads и группы узлов**: например, с помощью открытых инструментов
    - ☑️ **Мониторьте аномальную нагрузку**: с помощью [Yandex Cloud Monitoring](https://cloud.yandex.ru/docs/monitoring/)
  - **Резервное копирование**:
    - ☑️ **Выполняйте резервное копирование**: например в object storage [по гайду](https://cloud.yandex.ru/docs/managed-kubernetes/solutions/backup), необходимо следовать рекомендациям из раздела «Безопасная конфигурация Yandex Object Storage» 




