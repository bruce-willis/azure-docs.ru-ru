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
### [Совместное использование учетных записей](active-directory-sharing-accounts.md)
### [Назначение пользователям ролей администратора](fundamentals/active-directory-users-assign-role-azure-portal.md)
### [Восстановление удаленного пользователя](fundamentals/active-directory-users-restore.md)
### [Добавление гостевых пользователей из другого каталога (B2B)](b2b/what-is-b2b.md)
#### [Добавление пользователей B2B администраторами](b2b/add-users-administrator.md)
#### [Добавление пользователей B2B информационными работниками](b2b/add-users-information-worker.md)
#### [API и настройка](b2b/customize-invitation-api.md)
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
### Управление группами
#### [портал Azure](fundamentals/active-directory-groups-create-azure-portal.md)
#### [Azure AD PowerShell для Graph (версия 2)](users-groups-roles/groups-settings-v2-cmdlets.md)
#### [Azure AD PowerShell MSOnline](users-groups-roles/groups-settings-cmdlets.md)
### [Управление членами группы](fundamentals/active-directory-groups-members-azure-portal.md)
### [Управление владельцами групп](fundamentals/active-directory-accessmanagement-managing-group-owners.md)
### [Управление членством в группах](fundamentals/active-directory-groups-membership-azure-portal.md)
### [Назначение лицензий с помощью групп](fundamentals/active-directory-licensing-whatis-azure-portal.md)
#### [Назначение лицензий группе](users-groups-roles/licensing-groups-assign.md)
#### [Поиск и устранение проблем с лицензированием групп](users-groups-roles/licensing-groups-resolve-problems.md)
#### [Перевод отдельных лицензированных пользователей на групповое лицензирование](users-groups-roles/licensing-groups-migrate-users.md)
#### [Перенос пользователей между лицензиями на продукт](users-groups-roles/licensing-groups-change-licenses.md)
#### [Дополнительные сценарии группового лицензирования](users-groups-roles/licensing-group-advanced.md)
#### [Примеры Azure PowerShell для группового лицензирования](users-groups-roles/licensing-ps-examples.md)
#### [Справочник по продуктам и планам службы в Azure AD](users-groups-roles/licensing-service-plan-reference.md)
### [Настройка срока действия групп Office 365](users-groups-roles/groups-lifecycle.md)
### [Принудительное применение политики именования для групп](users-groups-roles/groups-naming-policy.md)
### [Просмотр всех групп](fundamentals/active-directory-groups-view-azure-portal.md)
### [Включение доступа для групп в приложениях SaaS](users-groups-roles/groups-saasapps.md)
### [Восстановление удаленной группы Office 365](fundamentals/active-directory-groups-restore-azure-portal.md)
### [Управление параметрами группы](fundamentals/active-directory-groups-settings-azure-portal.md) 
### Создание дополнительных правил
#### [портал Azure](users-groups-roles/groups-dynamic-membership.md)
### [Настройка групп самообслуживания](users-groups-roles/groups-self-service-management.md)
### [Устранение неполадок](users-groups-roles/groups-troubleshooting.md)

## [Управление отчетами](active-directory-reporting-azure-portal.md)
### [Действие входа](active-directory-reporting-activity-sign-ins.md)
### [Действие аудита](active-directory-reporting-activity-audit-logs.md)
### [Пользователи под угрозой](active-directory-reporting-security-user-at-risk.md)
### [Вход, представляющий риск](active-directory-reporting-security-risky-sign-ins.md)
### [События риска](active-directory-reporting-risk-events.md)
### [Часто задаваемые вопросы](active-directory-reporting-faq.md)
### Задачи
#### [Настройка именованных расположений](active-directory-named-locations.md)
#### [Поиск отчетов об активности](active-directory-reporting-migration.md)
#### [Использование пакета содержимого Azure Active Directory Power BI](active-directory-reporting-power-bi-content-pack-how-to.md)
#### [Применение защитных мер к пользователям, находящимся в группе риска](active-directory-report-security-user-at-risk-remediation.md)
### Справочные материалы
#### [Сохранение](active-directory-reporting-retention.md)
#### [Задержки](active-directory-reporting-latencies-azure-portal.md)
#### [Справочные материалы по действиям аудита](active-directory-reporting-activity-audit-reference.md)
#### [Коды ошибок входа в систему](active-directory-reporting-activity-sign-ins-errors.md)
#### [Многофакторная проверка подлинности](active-directory-reporting-activity-sign-ins-mfa.md)


### Устранение неполадок
#### [Отсутствующие данные аудита](active-directory-reporting-troubleshoot-missing-audit-data.md)
#### [Отсутствующие данные в файлах для скачивания](active-directory-reporting-troubleshoot-missing-data-download.md)
#### [Ошибки пакета содержимого журналов действий Azure Active Directory](active-directory-reporting-troubleshoot-content-pack.md)
#### [Ошибки в API отчетов Azure Active Directory](active-directory-reporting-troubleshoot-graph-api.md)


### [Программный доступ](active-directory-reporting-api-getting-started-azure-portal.md)
#### [Предварительные требования](active-directory-reporting-api-prerequisites-azure-portal.md)
#### [Примеры аудита](active-directory-reporting-api-audit-samples.md)
#### [Примеры входа](active-directory-reporting-api-sign-in-activity-samples.md)
#### [Использование сертификатов](active-directory-reporting-api-with-certificates.md)

## Управление паролями
### [Общие сведения о паролях](authentication/active-directory-passwords-overview.md)
### Документы пользователя
#### [Сброс или изменение пароля](user-help/active-directory-passwords-update-your-own-password.md)
#### [Рекомендации по паролям](active-directory-secure-passwords.md)
#### [Регистрация для самостоятельного сброса пароля](user-help/active-directory-passwords-reset-register.md)
### [Принципы работы SSPR](authentication/concept-sspr-howitworks.md)
### [Руководство по развертыванию SSPR](authentication/howto-sspr-deployment.md)
### [SSPR и Windows 10](authentication/tutorial-sspr-windows.md)
### [Политики SSPR](authentication/concept-sspr-policy.md)
### [Настройка SSPR](authentication/concept-sspr-customization.md)
### [Требования к данным SSPR](authentication/howto-sspr-authenticationdata.md)
### [Отчеты SSPR](authentication/howto-sspr-reporting.md)
### [Смарт-блокировка](authentication/howto-password-smart-lockout.md)
### [Исключение простых паролей](authentication/concept-password-ban-bad.md)
### [Настройка списка запрещенных паролей](authentication/howto-password-ban-bad.md)
### [Интеграция с локальной средой](authentication/concept-password-ban-bad-on-premises.md)
### [Развертывание защиты паролем Azure AD](authentication/howto-password-ban-bad-on-premises.md)
### [Настройка защиты паролем Azure AD](authentication/howto-password-ban-bad-on-premises-operations.md)
### [Мониторинг защиты паролем Azure AD](authentication/howto-password-ban-bad-on-premises-troubleshoot.md)
### ИТ-администрирование. Сброс паролей
#### [портал Azure](fundamentals/active-directory-users-reset-password-azure-portal.md)
### [Лицензия SSPR](authentication/concept-sspr-licensing.md)
### [Обратная запись паролей](authentication/howto-sspr-writeback.md)
### [Устранение неполадок](authentication/active-directory-passwords-troubleshoot.md)
### [Часто задаваемые вопросы](authentication/active-directory-passwords-faq.md)


## Управление устройствами
### [Введение](device-management-introduction.md)
### [Использование портала Azure](device-management-azure-portal.md)
### [Планирование присоединения к Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
### [Часто задаваемые вопросы](device-management-faq.md)
### Задачи
#### [Настройка устройств Windows 10, зарегистрированных в Azure AD](user-help/device-management-azuread-registered-devices-windows10-setup.md)
#### [Настройка устройств, присоединенных к Azure AD](user-help/device-management-azuread-joined-devices-setup.md)
#### [Настройка гибридных устройств, присоединенных к Azure AD](device-management-hybrid-azuread-joined-devices-setup.md)
#### [Локальное развертывание](active-directory-device-registration-on-premises-setup.md)
#### [Присоединение к Azure AD во время первого запуска Windows 10](device-management-azuread-joined-devices-frx.md)
### Устранение неполадок
#### [Устройства под управлением Windows 10 и Windows Server 2016, присоединенные к гибридному облаку Azure AD](device-management-troubleshoot-hybrid-join-windows-current.md)
#### [Устройства под управлением прежних версий Windows, присоединенные к гибридному облаку Azure AD](device-management-troubleshoot-hybrid-join-windows-legacy.md)

## Управление приложениями
### [Обзор](manage-apps/what-is-application-management.md)
### [Приступая к работе](manage-apps/plan-an-application-integration.md)
### [Руководства по интеграции приложений SaaS](saas-apps/tutorial-list.md)

### [Подготовка пользователей и отзыв подготовки для приложений SaaS](active-directory-saas-app-provisioning.md) 
#### [Руководства по интеграции приложений](saas-apps/tutorial-list.md) 
#### [Автоматизация подготовки для приложений с поддержкой SCIM](manage-apps/use-scim-to-provision-users-and-groups.md) 
#### [Настройка сопоставлений атрибутов](active-directory-saas-customizing-attribute-mappings.md) 
#### [Запись выражений для сопоставления атрибутов в Azure Active Directory](active-directory-saas-writing-expressions-for-attribute-mappings.md) 
#### [Использование фильтров области](active-directory-saas-scoping-filters.md) 
#### [Отчеты об автоматической подготовке пользователей](active-directory-saas-provisioning-reporting.md) 
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
##### [Подстановочные знаки](active-directory-application-proxy-wildcard.md)
##### [Удаление персональных данных](manage-apps/application-proxy-remove-personal-data.md)


#### Пошаговые руководства по публикации
##### [Удаленный рабочий стол](manage-apps/application-proxy-integrate-with-remote-desktop-services.md)
##### [SharePoint](manage-apps/application-proxy-integrate-with-sharepoint-server.md)
##### [Microsoft Teams](manage-apps/application-proxy-integrate-with-teams.md)
##### [Tableau](manage-apps/application-proxy-integrate-with-tableau.md)
##### [Qlik](active-directory-application-proxy-qlik.md)
#### [PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_application_management) 

#### [Устранение неполадок](manage-apps/application-proxy-troubleshoot.md)

### Управление корпоративными приложениями
#### [Назначение пользователей](manage-apps/assign-user-or-group-access-portal.md)
#### [Настройка фирменной символики](manage-apps/change-name-or-logo-portal.md)
#### [Отключение входа пользователей](manage-apps/disable-user-sign-in-portal.md)
#### [Удаление пользователей](manage-apps/remove-user-or-group-access-portal.md)
#### [Просмотр всех приложений](manage-apps/view-applications-portal.md)
#### [Управление подготовкой учетной записи пользователя](manage-apps/configure-automatic-user-provisioning-portal.md)
#### [Управление параметрами единого входа для корпоративных приложений](manage-apps/configure-single-sign-on-portal.md)
#### [Расширенные параметры подписи сертификатов для приложений SAML](manage-apps/certificate-signing-options.md)
#### [Скрытие приложения в интерфейсе пользователя](manage-apps/hide-application-from-user-portal.md)
### [Настройка автоматического ускорения входа с помощью политики обнаружения домашней области](manage-apps/configure-authentication-for-federated-users-portal.md)
### [Перенос приложений AD FS в Azure AD](manage-apps/migrate-adfs-apps-to-azure.md) 
### [Управление доступом к приложениям](manage-apps/what-is-access-management.md)
#### [Доступ с единым входом](manage-apps/what-is-single-sign-on.md)
#### [Сертификаты для единого входа](manage-apps/manage-certificates-for-federated-single-sign-on.md)
#### [Ограничения клиента](manage-apps/tenant-restrictions.md)
#### [Использование SCIM подготовки пользователей](manage-apps/use-scim-to-provision-users-and-groups.md)


### Устранение неполадок



#### Панель доступа
##### [Приложение не отображается](application-access-panel-unexpected-application-not-appearing.md)
##### [Непредусмотренное отображение приложения](application-access-panel-unexpected-application-appears.md)
##### [Не удается войти](application-access-panel-web-sign-in-problem.md)
##### [Ошибка при установке расширения браузера](application-access-panel-extension-problem-installing.md)
##### [Использование самостоятельного доступа к приложению](application-access-panel-self-service-applications-how-to.md)
##### [Ошибка при использовании самостоятельного доступа к приложению](application-access-panel-self-service-applications-problem.md)

#### Добавление приложения
##### [Выбор типа приложения](application-config-add-app-problem-how-to-choose-application-type.md)
##### [Распространенные проблемы — приложения из коллекции](application-config-add-app-problem-problem-adding-gallery-app.md)
##### [Распространенные проблемы — приложения не из коллекции](application-config-add-app-problem-problem-adding-non-gallery-app.md)

#### Прокси приложения
##### [Проблемы при отображении страницы приложения](application-proxy-page-appearance-broken-problem.md)
##### [Слишком долгая загрузка приложения](application-proxy-page-load-speed-problem.md)
##### [Ссылки на странице приложения не работают](application-proxy-page-links-broken-problem.md)
##### [Порты, которые необходимо открыть для приложения](application-proxy-connectivity-ports-how-to.md)
##### [Не удается найти рабочий соединитель в группе соединителей для приложения](application-proxy-connectivity-no-working-connector.md)
##### [Настройка на портале администрирования](application-proxy-config-how-to.md)
##### [Настройка единого входа в приложение](application-proxy-config-sso-how-to.md)
##### [Проблема при создании приложения на портале администрирования](application-proxy-config-problem.md)
##### [Настройка ограниченного делегирования Kerberos](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
##### [Настройка для использования PingAccess](application-proxy-back-end-ping-access-how-to.md)
##### [Ошибка "Can't Access this Corporate Application" (Нет доступа к этому корпоративному приложению)](application-proxy-sign-in-bad-gateway-timeout-error.md)
##### [Проблема при установке соединителя агента Application Proxy](application-proxy-connector-installation-problem.md)


#### Регистрация приложения
##### [Заполнение полей для объекта приложения](application-dev-registration-config-specific-application-property-how-to.md)
##### [Изменение значений по умолчанию для времени существования маркера](application-dev-registration-config-change-token-lifetime-how-to.md)

#### Authentication
##### [Настройка конечных точек](application-dev-registration-config-how-to.md)

#### условный доступ;
##### [Клиент не соответствует предварительным требованиям для регистрации устройства](active-directory-conditional-access.md)
##### [Клиент блокируется из-за неправильной настройки политик условного доступа](active-directory-conditional-access-device-remediation.md)
##### [Как и когда перестают действовать правила корпоративной сети?](https://aka.ms/calocation)
##### [Как увеличить количество устройств, которые пользователь может зарегистрироваться в Azure AD?](active-directory-azureadjoin-setup.md)
##### [Как настроить политику условного доступа для Exchange Online?](https://aka.ms/csforexchange)
##### [Как настроить условный доступ для устройств Windows 7?](active-directory-conditional-access.md#device-based-conditional-access)
##### [Какие приложения поддерживают условный доступ?](active-directory-conditional-access-supported-apps.md)

#### Поиск API
##### [Поиск API](application-dev-api-find-an-api-how-to.md)

#### Управление доступом
##### [Назначение приложения пользователям и группам](application-access-assignment-how-to-add-assignment.md)
##### [Запрет доступа пользователей к приложению](application-access-assignment-how-to-remove-assignment.md)
##### [Настройка самостоятельного назначения приложения](application-access-self-service-how-to.md)
##### [Непредусмотренное назначение пользователю](application-access-unexpected-user-assignment.md)
##### [Непредусмотренное приложение в списке приложений](application-access-unexpected-application.md)

#### Мультитенантные приложения
##### [Настройка нового приложения](application-dev-setup-multi-tenant-app.md)
##### [Добавление приложения в коллекцию](application-dev-registration-config-multi-tenant-application-add-to-gallery-how-to.md)

#### Разрешения
##### [Выбор разрешений для API](application-dev-perms-for-given-api.md)
##### [Предоставление разрешений приложению](application-dev-registration-config-grant-permissions-how-to.md)
##### [Делегированные разрешения и разрешения, предоставляемые приложениям](application-dev-delegated-and-app-perms.md)
##### [Согласие для приложения](application-dev-consent-framework.md)

#### Подготовка
##### [Сколько времени это занимает](application-provisioning-when-will-provisioning-finish-specific-user.md)
##### [Подготовка в течение нескольких часов для приложения из коллекции](application-provisioning-when-will-provisioning-finish.md)
##### [Настройка подготовки пользователей для приложения из коллекции](application-provisioning-config-how-to.md)
##### [Проблема при настройке подготовки пользователей для приложения из коллекции](application-provisioning-config-problem.md)
##### [Проблема при сохранении учетных данных администратора во время настройки подготовки пользователей для приложения из коллекции](application-provisioning-config-problem-storage-limit.md)
##### [Не удается подготовить пользователей для приложения из коллекции](application-provisioning-config-problem-no-users-provisioned.md)
##### [Для приложения из коллекции подготовлены не те пользователи](application-provisioning-config-problem-wrong-users-provisioned.md)

#### Единый вход
##### [Выбор метода](application-config-sso-how-to-choose-sign-on-method.md)
##### [Настройка](application-dev-registration-config-sso-how-to.md)
##### [Настройка федеративного единого входа в приложения из коллекции](application-config-sso-how-to-configure-federated-sso-gallery.md)
##### [Настройка федеративного единого входа в приложения из коллекции. Распространенные проблемы](application-config-sso-problem-configure-federated-sso-gallery.md)
##### [Настройка федеративного единого входа в приложения не из коллекции](application-config-sso-how-to-configure-federated-sso-non-gallery.md)
##### [Настройка федеративного единого входа в приложения не из коллекции. Распространенные проблемы](application-config-sso-problem-configure-federated-sso-non-gallery.md)
##### [Настройка входа по паролю для приложений из коллекции](application-config-sso-how-to-configure-password-sso-gallery.md)
##### [Настройка входа по паролю для приложений из коллекции. Распространенные проблемы](application-config-sso-problem-configure-password-sso-gallery.md)
##### [Настройка входа по паролю для приложений не из коллекции](application-config-sso-how-to-configure-password-sso-non-gallery.md)
##### [Настройка входа по паролю для приложений не из коллекции. Распространенные проблемы](application-config-sso-problem-configure-password-sso-non-gallery.md)

#### Проблемы при входе пользователей
##### [Непредусмотренный запрос на продолжение](application-sign-in-unexpected-user-consent-prompt.md)
##### [Ошибка при предоставлении согласия пользователем](application-sign-in-unexpected-user-consent-error.md)
##### [Проблемы при входе с пользовательского портала](application-sign-in-other-problem-deeplink.md)
##### [Проблемы при входе с панели доступа](application-sign-in-other-problem-access-panel.md)
##### [Ошибка на странице входа в приложение](application-sign-in-problem-application-error.md)
##### [Проблема при едином входе с помощью пароля — приложение не из коллекции](application-sign-in-problem-password-sso-non-gallery.md)
##### [Проблема при едином входе с помощью пароля — приложение из коллекции](application-sign-in-problem-password-sso-gallery.md)
##### [Проблема при входе в приложение Майкрософт](application-sign-in-problem-first-party-microsoft.md)
##### [Проблема при федеративном едином входе в приложение не из коллекции](application-sign-in-problem-federated-sso-non-gallery.md)
##### [Проблема при федеративном едином входе в приложение из коллекции](application-sign-in-problem-federated-sso-gallery.md)
##### [Проблемы со специально разработанным приложением](application-sign-in-problem-custom-dev.md)
##### [Проблема с локальным приложением — Application Proxy](application-sign-in-problem-on-premises-application-proxy.md)

### [Разработка приложений](active-directory-applications-guiding-developers-for-lob-applications.md)
### [Библиотека документов](active-directory-apps-index.md)

## Управление каталогом
### [Azure AD Connect](./connect/active-directory-aadconnect.md)
### Имена пользовательских доменов
#### [Краткое руководство](fundamentals/add-custom-domain.md)
#### [Добавление имен личных доменов](users-groups-roles/domains-manage.md)
### [Администрирование каталога](fundamentals/active-directory-administer.md)
### [Удаление каталога](users-groups-roles/directory-delete-howto.md)
### [Несколько каталогов](users-groups-roles/licensing-directory-independence.md)
### [Самостоятельная регистрация](users-groups-roles/directory-self-service-signup.md)
### [Взятие под контроль неуправляемого каталога](users-groups-roles/domains-admin-takeover.md)
### [Enterprise State Roaming.](active-directory-windows-enterprise-state-roaming-overview.md)
#### [Включение](active-directory-windows-enterprise-state-roaming-enable.md)
#### [Параметры групповой политики](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
#### [Параметры Windows 10](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
#### [Часто задаваемые вопросы](active-directory-windows-enterprise-state-roaming-faqs.md)
#### [Устранение неполадок](active-directory-windows-enterprise-state-roaming-troubleshooting.md)


### [Интеграция локальных удостоверений с помощью Azure AD Connect](./connect/active-directory-aadconnect.md)

## Делегирование доступа к ресурсам
### [Роли администратора](users-groups-roles/directory-assign-admin-roles.md)
#### [Просмотр членов роли администратора](users-groups-roles//directory-manage-roles-portal.md)
#### [Назначение роли администратора пользователю](fundamentals/active-directory-users-assign-role-azure-portal.md)
#### [Сравнение пользовательских разрешений члена и гостя](fundamentals/users-default-permissions.md)
### [Защита роли администратора](users-groups-roles/directory-admin-roles-secure.md)  
#### [Создание учетных записей администраторов для аварийного доступа](users-groups-roles/directory-emergency-access.md)
### [Административные единицы](users-groups-roles/directory-administrative-units.md)
### [Настройка времени существования маркеров](active-directory-configurable-token-lifetimes.md)

## Проверки доступа
### [Обзор проверки доступа](active-directory-azure-ad-controls-access-reviews-overview.md)
### [Выполнение проверки доступа](active-directory-azure-ad-controls-complete-access-review.md)
### [Создание проверки доступа](active-directory-azure-ad-controls-create-access-review.md)
### [Как выполнить проверку доступа](active-directory-azure-ad-controls-perform-access-review.md)
### [Как проверить доступ](active-directory-azure-ad-controls-how-to-review-your-access.md)
### [Гостевой доступ с использованием проверок доступа](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md)
### [Управление доступом пользователей с помощью проверок](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md)
### [Управление программами и элементами управления](active-directory-azure-ad-controls-manage-programs-controls.md)
### [Получение результатов проверок доступа](active-directory-azure-ad-controls-retrieve-access-review.md)

## Защита удостоверений
### [Условный доступ](active-directory-conditional-access-azure-portal.md)
#### [Приступая к работе с условным доступом](active-directory-conditional-access-azure-portal-get-started.md)
#### Быстрое начало работы
##### [Настройка отдельных облачных приложений MFA](active-directory-conditional-access-app-based-mfa.md)
##### [Обеспечение принятия условий использования](active-directory-conditional-access-tou.md)
##### [Блокировка доступа при обнаружении риска безопасности сеанса](active-directory-conditional-access-app-sign-in-risk.md)
#### Учебники
##### [Перенос классической политики MFA](active-directory-conditional-access-migration-mfa.md)
#### Основные понятия
##### [Базовая защита](active-directory-conditional-access-baseline-protection.md)
##### [Условия](active-directory-conditional-access-conditions.md)
##### [Условия расположения](active-directory-conditional-access-locations.md)
##### [Элементы управления](active-directory-conditional-access-controls.md)
##### [Средство"Что если"](active-directory-conditional-access-whatif.md)
##### [Политики условного доступа к службам Office 365 с устройств](active-directory-conditional-access-device-policies.md)
#### Практические руководства
##### [Рекомендации по использованию хранилища данных SQL Azure](active-directory-conditional-access-best-practices.md)
##### [Настройка политик условного доступа для попыток доступа из ненадежных сетей](active-directory-conditional-access-untrusted-networks.md)
##### [Настройка условного доступа на основе устройств](active-directory-conditional-access-policy-connected-applications.md)
##### [Настройка условного доступа на основе приложений](active-directory-conditional-access-mam.md)
##### [Условия использования для пользователей и приложений](active-directory-tou.md)
##### [Миграция классических политик](active-directory-conditional-access-migration.md)
##### [Настройка VPN-подключения](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/always-on-vpn/deploy/always-on-vpn-deploy)
##### [Настройка SharePoint и Exchange Online](active-directory-conditional-access-no-modern-authentication.md)
##### [Исправление](active-directory-conditional-access-device-remediation.md)
#### [Технический справочник: условный доступ к приложениям Azure AD](active-directory-conditional-access-technical-reference.md)
#### [Часто задаваемые вопросы](active-directory-conditional-faqs.md)

### Аутентификация на основе сертификата
#### [Android](active-directory-certificate-based-authentication-android.md)
#### [iOS](active-directory-certificate-based-authentication-ios.md)
#### [Приступая к работе с условным доступом](active-directory-certificate-based-authentication-get-started.md)

### [Защита идентификации Azure AD](active-directory-identityprotection.md)
#### [Включение](active-directory-identityprotection-enable.md)
#### [Обнаружение уязвимостей](active-directory-identityprotection-vulnerabilities.md)
#### [События риска](active-directory-identity-protection-risk-events.md)
#### [Уведомления](active-directory-identityprotection-notifications.md)
#### [Процедура входа](active-directory-identityprotection-flows.md)
#### [Моделирование событий риска](active-directory-identityprotection-playbook.md)
#### [Разблокирование пользователей](active-directory-identityprotection-unblock-howto.md)
#### [Часто задаваемые вопросы](active-directory-identity-protection-faqs.md)
#### [Глоссарий](active-directory-identityprotection-glossary.md)
#### [Microsoft Graph](active-directory-identityprotection-graph-getting-started.md)
### [управление привилегированными пользователями;](privileged-identity-management/pim-configure.md?toc=%2fazure%2factive-directory%2ftoc.json)

## Интеграция других служб с Azure AD 
### [Интеграция LinkedIn с Azure AD](users-groups-roles/linkedin-integration.md)

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
## [Ограничения службы](users-groups-roles/directory-service-limits-restrictions.md)

# Сопутствующие материалы
## [Многофакторная идентификация](/azure/multi-factor-authentication/)
## [Azure AD Connect](./connect/active-directory-aadconnect.md)
## [Azure AD Connect Health](./connect-health/active-directory-aadconnect-health.md)
## [Информация для разработчиков об Azure AD](./develop/active-directory-how-to-integrate.md)
## [Управление привилегированными пользователями Azure AD](./privileged-identity-management/pim-configure.md)

# Ресурсы
## [Форум отзывов и предложений по Azure](https://feedback.azure.com/forums/169401-azure-active-directory)
## [Стратегия развития Azure](https://azure.microsoft.com/roadmap/?category=security-identity)
## [Форум MSDN](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)
## [Цены](https://azure.microsoft.com/pricing/details/active-directory/)
## [Калькулятор цен](https://azure.microsoft.com/pricing/calculator/)
## [Обновления службы](https://azure.microsoft.com/updates/?product=active-directory)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-active-directory)
## [Видеоролики](https://azure.microsoft.com/documentation/videos/index/?services=active-directory)
