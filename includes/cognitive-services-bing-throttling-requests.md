Служба и тип подписки определяют число запросов, которые можно выполнить за секунду (QPS). Убедитесь, что приложение содержит логику, необходимую, чтобы оставаться в пределах вашей квоты. Если ограничение QPS достигнуто или превышено, запрос завершится ошибкой с возвратом кода состояния HTTP 429. Ответ включает заголовок `Retry-After`, который указывает, сколько необходимо ждать перед отправкой другого запроса.

## <a name="denial-of-service-versus-throttling"></a>Отказ в обслуживании и регулирование

Служба различает атаку типа "отказ в обслуживании" (DoS) и нарушение QPS. Если служба подозревает, что совершается атака DoS, запрос будет успешно завершен (код состояния HTTP: 200 OK). Но текст ответа будет пустым.