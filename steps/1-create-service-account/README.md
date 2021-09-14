# Service Account

Если вы ранее создавали сервисный аккаунт с именем `service-account-for-cf`, добавляли вновь созданному сервисному аккаунту роли `editor` и  `storage.editor` и создавали ключ доступа то вам остается только добавить роль `serverless.mdbProxies.user` для подключения к управляемым БД из функции.

## Создание аккаунта

Создайте сервисный аккаунт с именем `service-account-for-cf`: 

    export SERVICE_ACCOUNT=$(yc iam service-account create --name service-account-for-cf \
    --description "service account for cloud functions" \
    --format json | jq -r .)

Проверьте текущий список сервисных аккаунтов:
     
    yc iam service-account list
    echo $SERVICE_ACCOUNT

После проверки запишите ID, созданного сервисного аккаунта, в переменную `SERVICE_ACCOUNT_ID`:

    echo "export SERVICE_ACCOUNT_ID=<ID>" >> ~/.bashrc && . ~/.bashrc 
    echo $SERVICE_ACCOUNT_ID

## Назначение роли сервисному аккаунту

Добавим вновь созданному сервисному аккаунту роль `editor`,  `storage.editor` и `serverless.mdbProxies.user` :

    echo "export FOLDER_ID=$(yc config get folder-id)" >> ~/.bashrc && . ~/.bashrc 
    echo $FOLDER_ID

    yc resource-manager folder add-access-binding $FOLDER_ID \
    --subject serviceAccount:$SERVICE_ACCOUNT_ID \
    --role editor

    yc resource-manager folder add-access-binding $FOLDER_ID \
    --role storage.editor \
    --subject serviceAccount:$SERVICE_ACCOUNT_ID

    yc resource-manager folder add-access-binding $FOLDER_ID \
    --role serverless.mdbProxies.user \
    --subject serviceAccount:$SERVICE_ACCOUNT_ID

## Создание ключа доступа для сервисного аккаунта

Этот этап нужен для получения идентификатора ключа доступа и секретного ключа, которые будут использованы для загрузки файлов в Object Storage, работы с Yandex Message Queue и в том случае если на следующем шаге вы планируете использовать утилиту `terraform` для создания Object Storage.  

Для создания ключа доступа необходимо вызвать следующую команду:

    yc iam access-key create --service-account-name service-account-for-cf

В результате вы получите примерно следующее

    access_key:
        id: ajefraollq5puj2tir1o
        service_account_id: ajetdv28pl0a1a8r41f0
        created_at: "2021-08-23T21:13:05.677319393Z"
        key_id: BTPNvWthv0ZX2xVmlPIU
    secret: cWLQ0HrTM0k_qAac43cwMNJA8VV_rfTg_kd4xVPi

Тут `key_id` — это идентификатор ключа доступа `ACCESS_KEY`. А `secret` — это секретный ключ `SECRET_KEY`. Переменные `ACCESS_KEY` и `SECRET_KEY` могут быть использованы для задания соответствующих значений `aws_access_key_id` и `aws_secret_access_key` при использовании библиотеки boto3 или при использовании команды `terraform init`:

    terraform init -backend-config="access_key=<ACCESS_KEY>" -backend-config="secret_key=<SECRET_KEY>"



