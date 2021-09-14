# Yandex Message Queue
## Создание очереди
Для создания очереди Yandex Message Queue вы можете использовать три разных способа:
* UI;
* консольную утилиту aws;
* terraform.

## Создание очереди с помощью UI

1. Откройте раздел Message Queue. 
2. Нажмите кнопку `Создать очередь`. 
3. Укажите имя очереди `my-first-queue`. 
4. Выберите тип очереди `Стандартная`. 
5. Нажмите кнопку `Создать`.

## Создание очереди с помощью утилиты aws

Для альтернативного способа создания очереди можете воспользоваться  AWS CLI. Для начала, задайте конфигурацию с помощью команды `aws configure`. При этом от вас потребуется ввести:
* `AWS Access Key ID` — это идентификатор ключа доступа `key_id` сервисного аккаунта, полученный на предыдущем шаге.
* `AWS Secret Access Key` — это секретный ключ `secret` сервисного аккаунта, полученный на предыдущем шаге.
* `Default region name` — используйте значение `ru-central1`.

По завершению конфигурации вы сможете создать очередь: 

    aws configure
    aws sqs create-queue --queue-name my-first-queue-2 --endpoint https://message-queue.api.cloud.yandex.net/

В результате успешного выполнения предыдущей команды в ответ вы получите URL: 
    
    {
        "QueueUrl": "https://message-queue.api.cloud.yandex.net/b1ga4gj7agij03ln6aov/dj6000000003kv2t02b3/my-first-queue-2"
    }

