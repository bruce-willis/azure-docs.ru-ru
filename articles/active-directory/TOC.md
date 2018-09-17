# [Документация по Azure Active Directory](index.md)

# Обзор
## [Что такое Microsoft Azure Active Directory](fundamentals/active-directory-whatis.md)
## [Об управлении удостоверениями Azure](fundamentals/identity-fundamentals.md)
## [Основные сведения о решениях для идентификации в Azure](fundamentals/understand-azure-identity-solutions.md)
## [Выбор решения для гибридной идентификации](choose-hybrid-identity-solution.md)
## [Сопоставление подписок Azure](fundamentals/active-directory-how-subscriptions-associated-directory.md)
## [Рекомендации по хранению данных и работе с данными](fundamentals/active-directory-data-storage-eu.md)
## [Часто задаваемые вопросы](fundamentals/active-directory-faq.md)
## [Новые возможности](fundamentals/whats-new.md)


# Начало работы
## [Приступая к работе с Azure AD](fundamentals/get-started-azure-ad.md)
## [Регистрация для использования Azure AD Premium](fundamentals/active-directory-get-started-premium.md)
## [Добавление имени личного домена](fundamentals/add-custom-domain.md)
## [Настройка фирменной символики компании](fundamentals/customize-branding.md)
## [Добавление пользователей в Azure AD](fundamentals/add-users-azure-active-directory.md)
## [Назначение лицензий пользователям](fundamentals/license-users-groups.md)
## [Настройка самостоятельного сброса пароля](authentication/quickstart-sspr.md)
## [Добавление конфиденциальных данных организации в Azure Active Directory](active-directory-properties-area.md)


# Практическое руководство
## Планирование и проектирование
### [Основы архитектуры Azure AD](fundamentals/active-directory-architecture.md)
### [Сопоставление утверждений в Azure Active Directory](active-directory-claims-mapping.md)
### [Развертывание решения для гибридной идентификации](active-directory-hybrid-identity-design-considerations-overview.md)
#### Определение требований
##### [Удостоверение](active-directory-hybrid-identity-design-considerations-business-needs.md)
##### [Синхронизация каталогов](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md)
##### [Многофакторная проверка подлинности](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md)
##### [Стратегия жизненного цикла удостоверений](active-directory-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md)
#### [Планирование безопасности данных](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md)
##### [Защита данных](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md)
##### [Управление содержимым](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md)
##### [Контроль доступа](active-directory-hybrid-identity-design-considerations-accesscontrol-requirements.md)
##### [Реагирование на инциденты](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md)
#### Планирование жизненного цикла удостоверений
##### [Задачи](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)
##### [Стратегия внедрения](active-directory-hybrid-identity-design-considerations-identity-adoption-strategy.md)
#### [Дальнейшие действия](active-directory-hybrid-identity-design-considerations-nextsteps.md)
#### [Сравнение инструментов](active-directory-hybrid-identity-design-considerations-tools-comparison.md)

## Управление пользователями
### [Добавление новых пользователей в Azure AD](fundamentals/add-users-azure-active-directory.md)
### [Управление профилями пользователей](fundamentals/active-directory-users-profile-azure-portal.md)
### [Сброс паролей пользователей](fundamentals/active-directory-users-reset-password-azure-portal.md)
### [Совместное использование учетных записей](active-directory-sharing-accounts.md)
### [Назначение пользователям ролей администратора](fundamentals/active-directory-users-assign-role-azure-portal.md)
### [Добавление гостевых пользователей из другого каталога (B2B)](b2b/what-is-b2b.md)
#### [Добавление пользователей B2B администраторами](b2b/add-users-administrator.md)
#### [Добавление пользователей B2B информационными работниками](b2b/add-users-information-worker.md)
#### [API и настройка](b2b/customize-invitation-api.md)
#### [Федерация Google](b2b/google-federation.md)
#### [Примеры кода и сценариев Azure PowerShell](b2b/code-samples.md)
#### [Пример самостоятельной регистрации на портале](b2b/self-service-portal.md)
#### [Сообщение электронной почты с приглашением](b2b/invitation-email-elements.md)
#### [Активация приглашения](b2b/redemption-experience.md)
#### [Добавление пользователей B2B без приглашения](b2b/add-user-without-invite.md)
#### [Предоставление или отзыв приглашений](b2b/allow-deny-list.md)
#### [Условный доступ для B2B](b2b/conditional-access.md)
#### [Политики общего доступа к B2B](b2b/delegate-invitations.md)
#### [Добавление B2B-пользователя к роли](b2b/add-guest-to-role.md)
#### [Динамические группы и B2B-пользователи](b2b/use-dynamic-groups.md)
#### [Выход из организации](b2b/leave-the-organization.md)
#### [Аудит и отчеты](b2b/auditing-and-reporting.md)
#### [B2B для гибридных организаций](b2b/hybrid-organizations.md)
##### [Предоставление пользователям B2B доступа к локальным приложениям](b2b/hybrid-cloud-to-on-premises.md)
##### [Предоставление локальным пользователям доступа к облачным приложениям](b2b/hybrid-on-premises-to-cloud.md)
#### [Доступ внешних пользователей к B2B и Office 365](b2b/o365-external-user.md)
#### [Лицензирование B2B](b2b/licensing-guidance.md)
#### [Текущие ограничения](b2b/current-limitations.md)
#### [Часто задаваемые вопросы](b2b/faq.md)
#### [Устранение неполадок B2B](b2b/troubleshoot.md)
#### [Общие сведения о B2B-пользователе](b2b/user-properties.md)
#### [Маркер B2B-пользователя](b2b/user-token.md)
#### [B2B для интегрированных приложений Azure AD](b2b/configure-saas-apps.md)
#### [Сопоставление утверждений пользователя B2B](b2b/claims-mapping.md)
#### [Сравнение совместной работы B2B и B2C](b2b/compare-with-b2c.md)
#### [Получение технической поддержки для B2B](b2b/get-support.md)

## [Управление группами и членами](fundamentals/active-directory-manage-groups.md)
### [Управление группами](fundamentals/active-directory-groups-create-azure-portal.md)
### [Удаление группы и ее участников](fundamentals/active-directory-groups-delete-group.md)
### [Управление параметрами группы](fundamentals/active-directory-groups-settings-azure-portal.md)
## [Управление отчетами](reports-monitoring/overview-reports.md)
### [Действие входа](reports-monitoring/concept-sign-ins.md)
### [Действие аудита](reports-monitoring/concept-audit-logs.md)
### [Пользователи под угрозой](reports-monitoring/concept-user-at-risk.md)
### [Вход, представляющий риск](reports-monitoring/concept-risky-sign-ins.md)
### [События риска](reports-monitoring/concept-risk-events.md)
### [Мониторинг журналов с помощью Azure Monitor](reports-monitoring/overview-activity-logs-in-azure-monitor.md)
### [Часто задаваемые вопросы](reports-monitoring/reports-faq.md)

### Задачи
#### [Настройка именованных расположений](active-directory-named-locations.md)
#### [Поиск отчетов об активности](reports-monitoring/howto-find-activity-reports.md)
#### [Использование пакета содержимого Power BI для Azure AD](reports-monitoring/howto-power-bi-content-pack.md)
#### [Применение защитных мер к пользователям, находящимся в группе риска](reports-monitoring/howto-remediate-users-flagged-for-risk.md)
#### [Перенаправление журналов действий в концентратор событий Azure](reports-monitoring/quickstart-azure-monitor-stream-logs-to-event-hub.md)
#### [Архивация журналов действий в учетной записи хранения Azure](reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md)
#### [Интеграция журналов действий со Splunk с помощью Azure Monitor](reports-monitoring/tutorial-integrate-activity-logs-with-splunk.md)
#### [Интеграция журналов действий с SumoLogic с помощью Azure Monitor](reports-monitoring/howto-integrate-activity-logs-with-sumologic.md)

### Справочные материалы
#### [Сохранение](reports-monitoring/reference-reports-data-retention.md)
#### [Задержки](reports-monitoring/reference-reports-latencies.md)
#### [Справочные материалы по действиям аудита](reports-monitoring/reference-audit-activities.md)
#### [Коды ошибок входа в систему](reports-monitoring/reference-sign-ins-error-codes.md)
#### [Интерпретация схемы журнала аудита в Azure Monitor](reports-monitoring/reference-azure-monitor-audit-log-schema.md)
#### [Интерпретация схемы журнала аудита событий входа в систему в Azure Monitor](reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md)

### Устранение неполадок
#### [Отсутствующие данные в журналах действий Azure AD](reports-monitoring/troubleshoot-missing-audit-data.md)
#### [Отсутствующие данные в файлах для скачивания](reports-monitoring/troubleshoot-missing-data-download.md)
#### [Ошибки пакетов содержимого журналов действий Azure AD](reports-monitoring/troubleshoot-content-pack.md)
#### [Ошибки в API отчетов Azure AD](reports-monitoring/troubleshoot-graph-api.md)

### [Программный доступ](reports-monitoring/concept-reporting-api.md)
#### [Предварительные требования](reports-monitoring/howto-configure-prerequisites-for-reporting-api.md)
#### [Использование сертификатов](reports-monitoring/tutorial-access-api-with-certificates.md)

## [Управление паролями](authentication/concept-sspr-howitworks.md)

## Управление приложениями
### [Обзор](manage-apps/what-is-application-management.md)
### [Приступая к работе](manage-apps/plan-an-application-integration.md)
### [Руководства по интеграции приложений SaaS](saas-apps/tutorial-list.md)

### [Подготовка пользователей и отзыв подготовки для приложений SaaS](manage-apps/user-provisioning.md) 
#### [Руководства по интеграции приложений](saas-apps/tutorial-list.md) 
#### [Автоматизация подготовки для приложений с поддержкой SCIM](manage-apps/use-scim-to-provision-users-and-groups.md) 
#### [Настройка сопоставлений атрибутов](manage-apps/customize-application-attributes.md) 
#### [Запись выражений для сопоставления атрибутов в Azure Active Directory](manage-apps/functions-for-customizing-application-data.md) 
#### [Использование фильтров области](manage-apps/define-conditional-rules-for-provisioning-user-accounts.md) 
#### [Отчеты об автоматической подготовке пользователей](manage-apps/check-status-user-account-provisioning.md) 
#### [Устранение неполадок с подготовкой пользователей](active-directory-application-provisioning-content-map.md) 

### [Удаленный доступ к приложениям с помощью прокси приложения](manage-apps/application-proxy.md)
#### Начало работы
##### [Использование прокси приложения](manage-apps/application-proxy-enable.md)
##### [Публикация приложений](manage-apps/application-proxy-publish-azure-portal.md)
##### [Пользовательские домены](manage-apps/application-proxy-configure-custom-domain.md)
#### [Единый вход](manage-apps/application-proxy-single-sign-on.md)
##### [Единый вход с применением ограниченного делегирования Kerberos](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)
##### [Единый вход с применением заголовков](manage-apps/application-proxy-configure-single-sign-on-with-ping-access.md)
##### [Единый вход с хранением паролей](manage-apps/application-proxy-configure-single-sign-on-password-vaulting.md)
#### Основные понятия
##### [Соединители](manage-apps/application-proxy-connectors.md)
##### [Безопасность](manage-apps/application-proxy-security.md)
##### [Сети](manage-apps/application-proxy-network-topology.md)


##### [Обновление с TMG или UAG](manage-apps/application-proxy-migration.md)

#### Расширенные конфигурации
##### [Публикация в отдельных сетях](manage-apps/application-proxy-connector-groups.md)
##### [Прокси-серверы](manage-apps/application-proxy-configure-connectors-with-proxy-servers.md)
##### [Приложения с поддержкой утверждений](manage-apps/application-proxy-configure-for-claims-aware-applications.md)
##### [Приложения Native Client](manage-apps/application-proxy-configure-native-client-application.md)
##### [Автоматическая установка](manage-apps/application-proxy-register-connector-powershell.md)
##### [Пользовательская домашняя страница](manage-apps/application-proxy-configure-custom-home-page.md)
##### [Преобразование встроенных ссылок](manage-apps/application-proxy-configure-hard-coded-link-translation.md)
##### [Подстановочные знаки](manage-apps/application-proxy-wildcard.md)
##### [Удаление персональных данных](manage-apps/application-proxy-remove-personal-data.md)


#### Пошаговые руководства по публикации
##### [Удаленный рабочий стол](manage-apps/application-proxy-integrate-with-remote-desktop-services.md)
##### [SharePoint](manage-apps/application-proxy-integrate-with-sharepoint-server.md)
##### [Microsoft Teams](manage-apps/application-proxy-integrate-with-teams.md)
##### [Tableau](manage-apps/application-proxy-integrate-with-tableau.md)
##### [Qlik](manage-apps/application-proxy-qlik.md)
#### [PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_application_management) 

#### [Устранение неполадок](manage-apps/application-proxy-troubleshoot.md)

### Управление корпоративными приложениями
#### [Добавление приложения](manage-apps/add-application-portal.md)
#### [Просмотр приложений клиента](manage-apps/view-applications-portal.md)
#### [Настройка единого входа](manage-apps/configure-single-sign-on-portal.md)
#### [Назначение пользователей](manage-apps/assign-user-or-group-access-portal.md)
#### [Настройка фирменной символики](manage-apps/change-name-or-logo-portal.md)
#### [Отключение входа пользователей](manage-apps/disable-user-sign-in-portal.md)
#### [Удаление пользователей](manage-apps/remove-user-or-group-access-portal.md)

#### [Управление подготовкой учетной записи пользователя](manage-apps/configure-automatic-user-provisioning-portal.md)

#### [Расширенные параметры подписи сертификатов для приложений SAML](manage-apps/certificate-signing-options.md)
#### [Скрытие приложения в интерфейсе пользователя](manage-apps/hide-application-from-user-portal.md)
### [Настройка автоматического ускорения входа с помощью политики обнаружения домашней области](manage-apps/configure-authentication-for-federated-users-portal.md)
### [Перенос приложений AD FS в Azure AD](manage-apps/migrate-adfs-apps-to-azure.md) 
### [Управление доступом к приложениям](manage-apps/what-is-access-management.md)
#### [Доступ с единым входом](manage-apps/what-is-single-sign-on.md)
#### [Сертификаты для единого входа](manage-apps/manage-certificates-for-federated-single-sign-on.md)
#### [Ограничения клиента](manage-apps/tenant-restrictions.md)
#### [Использование SCIM подготовки пользователей](manage-apps/use-scim-to-provision-users-and-groups.md)

### [Основные сведения о процедуре предоставления согласия для приложений Azure AD](application-consent-experience.md)

### Устранение неполадок



#### Панель доступа
##### [Приложение не отображается](manage-apps/access-panel-troubleshoot-application-not-appearing.md)
##### [Непредусмотренное отображение приложения](manage-apps/access-panel-troubleshoot-unexpected-application.md)
##### [Не удается войти](manage-apps/access-panel-troubleshoot-web-sign-in-problem.md)
##### [Ошибка при установке расширения браузера](manage-apps/access-panel-extension-problem-installing.md)
##### [Использование самостоятельного доступа к приложению](manage-apps/access-panel-manage-self-service-access.md)
##### [Ошибка при использовании самостоятельного доступа к приложению](manage-apps/access-panel-troubleshoot-self-service-access.md)

#### Добавление приложения
##### [Выбор типа приложения](manage-apps/choose-application-type.md)
##### [Распространенные проблемы — приложения из коллекции](manage-apps/adding-gallery-app-common-problems.md)
##### [Распространенные проблемы — приложения не из коллекции](manage-apps/adding-non-gallery-app-common-problems.md)

#### Прокси приложения
##### [Проблемы при отображении страницы приложения](manage-apps/application-proxy-page-appearance-broken-problem.md)
##### [Слишком долгая загрузка приложения](manage-apps/application-proxy-page-load-speed-problem.md)
##### [Ссылки на странице приложения не работают](manage-apps/application-proxy-page-links-broken-problem.md)
##### [Порты, которые необходимо открыть для приложения](manage-apps/application-proxy-connectivity-ports-how-to.md)
##### [Не удается найти рабочий соединитель в группе соединителей для приложения](manage-apps/application-proxy-connectivity-no-working-connector.md)
##### [Настройка на портале администрирования](manage-apps/application-proxy-config-how-to.md)
##### [Настройка единого входа в приложение](manage-apps/application-proxy-config-sso-how-to.md)
##### [Проблема при создании приложения на портале администрирования](manage-apps/application-proxy-config-problem.md)
##### [Настройка ограниченного делегирования Kerberos](manage-apps/application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
##### [Настройка для использования PingAccess](manage-apps/application-proxy-back-end-ping-access-how-to.md)
##### [Ошибка "Can't Access this Corporate Application" (Нет доступа к этому корпоративному приложению)](manage-apps/application-proxy-sign-in-bad-gateway-timeout-error.md)
##### [Проблема при установке соединителя агента Application Proxy](manage-apps/application-proxy-connector-installation-problem.md)


#### Регистрация приложения
##### [Заполнение полей для объекта приложения](develop/registration-config-specific-application-property-how-to.md)
##### [Изменение значений по умолчанию для времени существования маркера](develop/registration-config-change-token-lifetime-how-to.md)

#### Authentication
##### [Настройка конечных точек](develop/registration-config-how-to.md)

#### условный доступ;
##### [Клиент не соответствует предварительным требованиям для регистрации устройства](active-directory-conditional-access.md)
##### [Клиент блокируется из-за неправильной настройки политик условного доступа](active-directory-conditional-access-device-remediation.md)
##### [Как и когда перестают действовать правила корпоративной сети?](https://aka.ms/calocation)
##### [Как увеличить количество устройств, которые пользователь может зарегистрироваться в Azure AD?](active-directory-azureadjoin-setup.md)
##### [Как настроить политику условного доступа для Exchange Online?](https://aka.ms/csforexchange)
##### [Как настроить условный доступ для устройств Windows 7?](active-directory-conditional-access.md#device-based-conditional-access)
##### [Какие приложения поддерживают условный доступ?](active-directory-conditional-access-supported-apps.md)

#### Поиск API
##### [Поиск API](develop/api-find-an-api-how-to.md)

#### Управление доступом
##### [Назначение приложения пользователям и группам](manage-apps/methods-for-assigning-users-and-groups.md)
##### [Запрет доступа пользователей к приложению](manage-apps/methods-for-removing-user-access.md)
##### [Настройка самостоятельного назначения приложения](manage-apps/manage-self-service-access.md)
##### [Непредусмотренное назначение пользователю](manage-apps/ways-users-get-assigned-to-applications.md)
##### [Непредусмотренное приложение в списке приложений](manage-apps/application-types.md)

#### Мультитенантные приложения
##### [Настройка нового приложения](develop/setup-multi-tenant-app.md)
##### [Добавление приложения в коллекцию](develop/registration-config-multi-tenant-application-add-to-gallery-how-to.md)

#### Разрешения
##### [Выбор разрешений для API](develop/perms-for-given-api.md)
##### [Предоставление разрешений приложению](develop/registration-config-grant-permissions-how-to.md)
##### [Делегированные разрешения и разрешения, предоставляемые приложениям](develop/delegated-and-app-perms.md)
##### [Согласие для приложения](develop/consent-framework.md)

#### Подготовка
##### [Сколько времени это занимает](manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md)
##### [Подготовка в течение нескольких часов для приложения из коллекции](manage-apps/application-provisioning-when-will-provisioning-finish.md)
##### [Настройка подготовки пользователей для приложения из коллекции](manage-apps/application-provisioning-config-how-to.md)
##### [Проблема при настройке подготовки пользователей для приложения из коллекции](manage-apps/application-provisioning-config-problem.md)
##### [Проблема при сохранении учетных данных администратора во время настройки подготовки пользователей для приложения из коллекции](manage-apps/application-provisioning-config-problem-storage-limit.md)
##### [Не удается подготовить пользователей для приложения из коллекции](manage-apps/application-provisioning-config-problem-no-users-provisioned.md)
##### [Для приложения из коллекции подготовлены не те пользователи](manage-apps/application-provisioning-config-problem-wrong-users-provisioned.md)

#### Единый вход
##### [Выбор метода](manage-apps/single-sign-on-modes.md)
##### [Настройка](develop/registration-config-sso-how-to.md)
##### [Настройка федеративного единого входа в приложения из коллекции](manage-apps/configure-federated-single-sign-on-gallery-applications.md)
##### [Настройка федеративного единого входа в приложения из коллекции. Распространенные проблемы](manage-apps/configure-federated-single-sign-on-gallery-applications-problems.md)
##### [Настройка федеративного единого входа в приложения не из коллекции](manage-apps/configure-federated-single-sign-on-non-gallery-applications.md)
##### [Настройка федеративного единого входа в приложения не из коллекции. Распространенные проблемы](manage-apps/configure-federated-single-sign-on-non-gallery-applications-problems.md)
##### [Настройка входа по паролю для приложений из коллекции](manage-apps/configure-password-single-sign-on-gallery-applications.md)
##### [Настройка входа по паролю для приложений из коллекции. Распространенные проблемы](manage-apps/configure-password-single-sign-on-gallery-applications-problems.md)
##### [Настройка входа по паролю для приложений не из коллекции](manage-apps/configure-password-single-sign-on-non-gallery-applications.md)
##### [Настройка входа по паролю для приложений не из коллекции. Распространенные проблемы](manage-apps/configure-password-single-sign-on-non-gallery-applications-problems.md)

#### Проблемы при входе пользователей
##### [Непредусмотренный запрос на продолжение](manage-apps/application-sign-in-unexpected-user-consent-prompt.md)
##### [Ошибка при предоставлении согласия пользователем](manage-apps/application-sign-in-unexpected-user-consent-error.md)
##### [Проблемы при входе с пользовательского портала](manage-apps/application-sign-in-other-problem-deeplink.md)
##### [Проблемы при входе с панели доступа](manage-apps/application-sign-in-other-problem-access-panel.md)
##### [Ошибка на странице входа в приложение](manage-apps/application-sign-in-problem-application-error.md)
##### [Проблема при едином входе с помощью пароля — приложение не из коллекции](manage-apps/application-sign-in-problem-password-sso-non-gallery.md)
##### [Проблема при едином входе с помощью пароля — приложение из коллекции](manage-apps/application-sign-in-problem-password-sso-gallery.md)
##### [Проблема при входе в приложение Майкрософт](manage-apps/application-sign-in-problem-first-party-microsoft.md)
##### [Проблема при федеративном едином входе в приложение не из коллекции](manage-apps/application-sign-in-problem-federated-sso-non-gallery.md)
##### [Проблема при федеративном едином входе в приложение из коллекции](manage-apps/application-sign-in-problem-federated-sso-gallery.md)
##### [Проблемы со специально разработанным приложением](manage-apps/application-sign-in-problem-custom-dev.md)
##### [Проблема с локальным приложением — Application Proxy](manage-apps/application-sign-in-problem-on-premises-application-proxy.md)

### [Разработка приложений](active-directory-applications-guiding-developers-for-lob-applications.md)


## Управление каталогом
### [Azure AD Connect](./connect/active-directory-aadconnect.md)
### Имена пользовательских доменов
#### [Краткое руководство](fundamentals/add-custom-domain.md)
### [Администрирование каталога](fundamentals/active-directory-administer.md)
### [Enterprise State Roaming.](active-directory-windows-enterprise-state-roaming-overview.md)
#### [Включение](active-directory-windows-enterprise-state-roaming-enable.md)
#### [Параметры групповой политики](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
#### [Параметры Windows 10](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
#### [Часто задаваемые вопросы](active-directory-windows-enterprise-state-roaming-faqs.md)
#### [Устранение неполадок](active-directory-windows-enterprise-state-roaming-troubleshooting.md)


### [Интеграция локальных удостоверений с помощью Azure AD Connect](./connect/active-directory-aadconnect.md)

### [Настройка времени существования маркеров](active-directory-configurable-token-lifetimes.md)

## Защита удостоверений

### [управление привилегированными пользователями;](privileged-identity-management/pim-configure.md?toc=%2fazure%2factive-directory%2ftoc.json)

## [Развертывание AD FS в Azure](active-directory-aadconnect-azure-adfs.md)
### [Высокая доступность](active-directory-adfs-in-azure-with-azure-traffic-manager.md)
### [Изменение хэш-алгоритма подписи](active-directory-federation-sha256-guidance.md)

## [Устранение неполадок](fundamentals/active-directory-troubleshooting-support-howto.md)

## Развертывание подтверждения концепции Azure AD (PoC)
### [Сборник тренировочных заданий по PoC: введение](active-directory-playbook-intro.md)
### [Сборник тренировочных заданий по PoC: ингредиенты](active-directory-playbook-ingredients.md)
### [Сборник тренировочных заданий по PoC: реализация](active-directory-playbook-implementation.md)
### [Сборник тренировочных заданий по PoC: стандартные блоки](active-directory-playbook-building-blocks.md)

# Справочные материалы
## [Примеры кода](https://azure.microsoft.com/resources/samples/?service=active-directory)
## [Командлеты Azure PowerShell](/powershell/azure/overview)
## [Справочник по API Java](/java/api)
## [API для .NET](/active-directory/adal/microsoft.identitymodel.clients.activedirectory)

# Сопутствующие материалы
## [Многофакторная идентификация](/azure/multi-factor-authentication/)
## [Azure AD Connect](./connect/active-directory-aadconnect.md)
## [Azure AD Connect Health](./connect-health/active-directory-aadconnect-health.md)
## [Информация для разработчиков об Azure AD](./develop/active-directory-how-to-integrate.md)
## [Управление привилегированными пользователями Azure AD](./privileged-identity-management/pim-configure.md)

# Ресурсы
## [Планы развертывания Azure Active Directory](./fundamentals/active-directory-deployment-plans.md)
## [Форум отзывов и предложений по Azure](https://feedback.azure.com/forums/169401-azure-active-directory)
## [Стратегия развития Azure](https://azure.microsoft.com/roadmap/?category=security-identity)
## [Форум MSDN](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)
## [Цены](https://azure.microsoft.com/pricing/details/active-directory/)
## [Калькулятор цен](https://azure.microsoft.com/pricing/calculator/)
## [Обновления службы](https://azure.microsoft.com/updates/?product=active-directory)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-active-directory)
## [Видеоролики](https://azure.microsoft.com/documentation/videos/index/?services=active-directory)
