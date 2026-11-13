<div align="center">

<a href="https://hexlet.io/?utm_source=github&utm_medium=link&utm_campaign=kafka-cli-stand">
    <picture>
        <source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/Hexlet/brand-assets/master/images/svg/hexlet_wordmark_white_rus.svg">
        <img src="https://raw.githubusercontent.com/Hexlet/brand-assets/master/images/svg/hexlet_wordmark_primary_rus.svg" alt="Хекслет" height="64">
    </picture>
</a>

# Стенд Apache Kafka для курса Хекслета

[![CI](https://github.com/hexlet-components/kafka-cli-stand/actions/workflows/main.yml/badge.svg)](https://github.com/hexlet-components/kafka-cli-stand/actions/workflows/main.yml)
[![Check](https://github.com/hexlet-components/kafka-cli-stand/actions/workflows/check.yml/badge.svg)](https://github.com/hexlet-components/kafka-cli-stand/actions/workflows/check.yml)

</div>

Компонент создаёт и поддерживает команда [Хекслета](https://hexlet.io/?utm_source=github&utm_medium=link&utm_campaign=kafka-cli-stand), образовательного проекта.

Стенд курса Хекслета «Apache Kafka» с практикой в командной строке. Уроки курса описывают, какая Kafka нужна для опыта, а способ установки студент выбирает сам. Стенд один из таких способов, он поднимает кластер, на котором выполняются все команды курса, от первого топика до отказа брокера.

Внутри один контроллер KRaft и три брокера с номерами `1`, `2` и `3`. Брокеры принимают клиентов по адресам `localhost:9092`, `localhost:9093` и `localhost:9094`, причём эти адреса одинаково работают с компьютера и из контейнера `cli`. Проверки удаления по сроку хранения и уплотнения ускорены до одной секунды, поэтому опыты с очисткой истории не ждут по пять минут. Изменение адресов, номеров брокеров или этих настроек расходится с выводом команд в уроках.

## Запуск

Нужен Docker с Compose. Команда в каталоге репозитория поднимает кластер в фоне:

```sh
docker compose up --detach
```

Консольные утилиты Kafka лежат в контейнере `cli`, его рабочий каталог _/opt/kafka_ совпадает с каталогом установки из уроков. Каждый новый терминал открывается так:

```sh
docker compose exec cli bash
```

Дальше команды из уроков выполняются как написаны, например `bin/kafka-topics.sh --bootstrap-server localhost:9092 --list`. Если утилиты Kafka стоят на компьютере, они подключаются к тем же адресам напрямую.

В уроках про репликацию брокер останавливается и возвращается. На стенде это делается так:

```sh
docker compose stop broker-3
docker compose start broker-3
```

Стенд с данными останавливается командой `docker compose down`. Флаг `--volumes` к ней не нужен, потому что данные живут внутри контейнеров и пропадают вместе с ними.

## Проверка стенда

```sh
mise run setup
mise run test
```

`mise run test` поднимает стенд, ждёт три брокера, пишет и читает запись, останавливает брокер `3` и проверяет, что он выпадает из ISR и возвращается в него. После проверки стенд гасится. `make <задача>` вызывает ту же задачу mise.

## Проверки

`mise run check` гоняет тот же набор, что CI: разметку, опечатки, ссылки, workflows и синтаксис `compose.yml`.
