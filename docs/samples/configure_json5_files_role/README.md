# configure_json5_files

This Ansible role writes JSON5 configuration files to a specified directory and can trigger a restart of selected applications to apply the new configuration.

## Role Overview

This role is designed to:

* Write JSON5 formatted configuration files based on Jinja2 templates.
* Set appropriate file ownership and permissions.
* Optionally restart specified applications that rely on PM2 process management to pick up the updated configuration.

## Table of Contents

* [Sample Playbook Usage](#sample-playbook-usage)
* [Role Variables](#role-variables)
    * [Core Role Variables](#core-role-variables)
    * [Shared Variables](#shared-variables)
        * [Shared Handler Variables](#shared-handler-variables)
        * [Shared Template Variables](#shared-template-variables)
* [Handler Variables](#handler-variables)
* [Template Variables](#template-variables)
    * [att-canvas-api/config.j2](#att-canvas-apiconfigj2)
    * [att-cdl-api/config.j2](#att-cdl-apiconfigj2)
    * [att-ldap-wrapper/config.j2](#att-ldap-wrapperconfigj2)
    * [att-mysql-data/config.js](#att-mysql-dataconfigjs)
    * [bulk-user-updates/config.j2](#bulk-user-updatesconfigj2)
    * [course-hall-of-fame-data/config.j2](#course-hall-of-fame-dataconfigj2)
    * [course-hall-of-fame-lti/config.j2](#course-hall-of-fame-lticonfigj2)
    * [course-request-data/config.j2](#course-request-dataconfigj2)
    * [course-request-lti/config.j2](#course-request-lticonfigj2)
    * [data-basement-data/config.j2](#data-basement-dataconfigj2)
    * [data-basement-lti/config.j2](#data-basement-lticonfigj2)
    * [dors-api/config.j2](#dors-apiconfigj2)
    * [dors-enrollments-api/config.j2](#dors-enrollments-apiconfigj2)
    * [dors-filter/config.j2](#dors-filterconfigj2)
    * [dors-grades-consumer/config.j2](#dors-grades-consumerconfigj2)
    * [dors-grades-data/config.j2](#dors-grades-dataconfigj2)
    * [dors-lti/config.j2](#dors-lticonfigj2)
    * [kafka-monitor/config.j2](#kafka-monitorconfigj2)
    * [manual-section-enrollment/config.js](#manual-section-enrollmentconfigjs)
    * [oracle-connection/config.j2](#oracle-connectionconfigj2)
    * [send-grades-lti/config.j2](#send-grades-lticonfigj2)
    * [send-grades-to-ppsft/config.j2](#send-grades-to-ppsftconfigj2)
    * [sync/config.j2](#syncconfigj2)
    * [ua-eligibility/config.j2](#ua-eligibilityconfigj2)
    * [ua-eligibility-api/config.j2](#ua-eligibility-apiconfigj2)
    * [att-kaltura-api/config.j2](#att-kaltura-apiconfigj2)
    * [zoom-license/config.j2](#zoom-licenseconfigj2)
    * [zoom-license-controller/config.j2](#zoom-license-controllerconfigj2)
* [Handlers](#handlers)
* [Templates](#templates)
* [Dependencies](#dependencies)
* [Author](#author)

## Sample Playbook Usage

<details>
  <summary>Click to expand: Updating configuration and restarting applications</summary>

```yaml
- hosts: servers
  roles:
    - role: configure_json5_files
      json5_main_dir: 'mainDir'
      json5_config_dir: 'configDir'
      json5_file_name: 'json5ConfigFileName'
      json5_restart_applications:
        - dors-lti
        - course-request
```

</details>

<details>
  <summary>Click to expand: Updating configuration without restarting applications</summary>

```yaml
- hosts: servers
  roles:
    - role: configure_json5_files
      json5_main_dir: 'mainDir'
      json5_config_dir: 'configDir'
      json5_file_name: 'json5ConfigFileName'
      json5_restart_applications:
        - none
```

</details>

## Role Variables

<details>
  <summary>Click to expand: Core Role Variables</summary>

### Core Role Variables

These variables are specific to the `configure_json5_files` role.

  * `file_access_permissions`: The file's user permissions.
      * Default: `u=rwx,g=r,o=r`
  * `json5_config_dir`: The path to the directory where the configuration is to be written.
      * Default: `configurations`
  * `json5_main_dir`: The project's root directory.
      * Default: `monorepo`
  * `json5_file_name`: The configuration file's name.
      * Default: `oracleConnectionConfig`
  * `json5_restart_applications`: A list of application directory names containing `pm2` directories that should be restarted to pick up the updated configuration file.
      * Default:
        ```yaml
        - 'sync'
        ```
          * Note: The default value will not cause any application to be restarted. See the [Handlers](https://www.google.com/search?q=%23handlers) section for more details.
  * `masquerade_user`: The user who will own the file.
      * Default: Varies by environment
  * `masquerade_user_group`: The group that will own the file.
      * Default: Varies by environment

</details>

<details>
  <summary>Click to expand: Shared Variables</summary>

### Shared Variables

These variables are used by handlers and/or templates associated with this role.

#### <a name="shared-handler-variables"></a>Shared Handler Variables: Pickup json5 configuration change

  * `masquerade_user`: The user who will own the file.
      * Default: Varies by environment
  * `json5_main_dir`: The directory or path to the `applications` directory containing `pm2_app_dir`.
      * Default: `monorepo`
  * `json5_restart_app_dir`: The name of the directory containing the pm2 directory for the application to be updated.
      * Default: N/A; This is set equal to the current list item being evaluated from `json5_restart_applications`.

#### <a name="shared-template-variables"></a>Shared Template Variables:

##### General

  * `mysql_user`: A MySQL user name.
      * Default: `attcanvaslti`
  * `mysql_password`: A MySQL password.
      * Default: `secret`
  * `mysql_host`: The MySQL server.
      * Default: `mysql-dev5.oit.umn.edu`
  * `mysql_port`: The MySQL port.
      * Default: `3306`

##### All LTI applications

  * `canvas_lti_client_url`: The Canvas LTI launch url.
      * Default: `https://canvas.instructure.com`
      * Needs to be overridden per environment.
  * `canvas_lti_authentication_endpoint`: The Canvas LTI Authentication Redirect url.
      * Default: `https://sso.canvaslms.com/api/lti/authorize_redirect`
      * Needs to be overridden per environment.
  * `canvas_lti_auth_config_method`: The authentication configuration algorithm.
      * Default: `JWK_SET`
      * Probably will not be overridden unless and until we migrate away from json webtoken.
  * `canvas_lti_auth_config_key`: The Canvas LTI Authentication Key access url.
      * Default: `https://sso.canvaslms.com/api/lti/security/jwks`
      * Needs to be overridden per environment.
  * [Canvas LMS docs for these variables](https://canvas.instructure.com/doc/api/file.lti_dev_key_config.html)
  * `canvas_vanity_url`: The vanity URL, if any.
      * Default: `https://umn.test.instructure.com`
      * Needs to be overridden per environment.
  * `lti_db_name`: The database name for LTI metadata
      * Default: `att_canvas_lti_dev`
      * Needs to be overridden in production.
  * `ssl_cert`: The canvas api URL to be used.
      * Default: `/etc/httpd/conf/ssl/server.cer`
  * `ssl_key`: The canvas api URL to be used.
      * Default: `/etc/httpd/conf/ssl/server.key`
  * `lti_log_suppress`: Don't output LTIJS standard logs
      * Default: `false`

##### att-canvas-api, course-request-lti, dors-lti

  * `canvas_api_url`: The canvas api URL to be used.
      * Default: `https://scratch.canvas.umn.edu`

##### course-request-lti, dors-lti

  * `secure`: Configure with secure cookie
      * Default: `true`
  * `dev_mode`: Whether to run the application in dev mode.
      * Default: `false`
  * `lti_port`: The server port number on which the LTI listens for traffic.
      * Default: 0

##### dors-filter, dors-grades-consumer

  * `kafka_connect_config_broker_list`: A list of brokers with port 9093 appended, created by another role.
      * Default: None required. This value is always correct.
  * `ssl_ca_cert`: The location and file name of the Certificate Authority's self-signed certificate.
      * Default: `/home/{{ masquerade_user }}/kafka-management/certificates/cert.ca.pem`
  * `ssl_server_cert`: The location and file name of the server's self-signed certificate.
      * Default: `/home/{{ masquerade_user }}/kafka-management/certificates/cert.server.pem`
  * `ssl_server_key`: The location and file name of the server's self-signed private key.
      * Default: `/home/{{ masquerade_user }}/kafka-management/certificates/key.server.pem`

</details>

<details>
  <summary>Click to expand: Handler Variables</summary>

### Handler Variables

These variables are specific to the handlers defined in this role.

  * `ui_env`: The server environment.
      * Default: `production`
      * Possible values:
          * `beta`
          * `dev`
          * `production`
          * `scratch`
          * `test`
  * [See Shared Handler Variables](#shared-handler-variables)

</details>

<details>
  <summary>Click to expand: Template Variables</summary>

### Template Variables

These variables are specific to the Jinja2 templates used by this role.

<details>
  <summary>Click to expand: att-canvas-api/config.j2</summary>

#### <a name="att-canvas-apiconfigj2"></a>att-canvas-api/config.j2

  * `canvas_api_tokens`: The tokens needed to access canvas.
      * Default: `secret`

</details>

<details>
  <summary>Click to expand: att-cdl-api/config.j2</summary>

#### <a name="att-cdl-apiconfigj2"></a>att-cdl-api/config.j2

  * `cdl_user`: Basic auth username for CDL APIs
      * Default: `attcanvaslti`
  * `cdl_pass`: Basic auth password for CDL APIs
      * Default: `secret`
  * `cdl_api_url`: URL for CDL APIs
      * Default: `https://umn-test-apigw.boomi.cloud:8077/test/ws/rest`
  * `cdl_employee_info_api_key`: API Key for Employee Information API
      * Default: `secret`

</details>

<details>
  <summary>Click to expand: att-ldap-wrapper/config.j2</summary>

#### <a name="att-ldap-wrapperconfigj2"></a>att-ldap-wrapper/config.j2

  * `att_ldap_dn`: The LDAP "distinguished name," or username.
      * Default: `ldapdn`
  * `att_ldap_pw`: The LDAP password.
      * Default: `ldappw`

</details>

<details>
  <summary>Click to expand: att-mysql-data/config.js</summary>

#### <a name="att-mysql-dataconfigjs"></a>att-mysql-data/config.js

  * `ssl_ca`: The location and file name of a valid CA cert to authenticate to mysql hotel
      * Default: `/etc/ssl/certs/ca-bundle.crt`

</details>

<details>
  <summary>Click to expand: bulk-user-updates/config.j2</summary>

#### <a name="bulk-user-updatesconfigj2"></a>bulk-user-updates/config.j2

  * `bulk_user_lti_client_id`: The Canvas client id.
      * Default: `secret`
  * `bulk_user_lti_dev_key`: The Canvas developer key.
      * Default: `secret`

</details>

<details>
  <summary>Click to expand: course-hall-of-fame-data/config.j2</summary>

#### <a name="course-hall-of-fame-dataconfigj2"></a>course-hall-of-fame-data/config.j2

  * `course_hall_of_fame_data_db_name`: The name of the database.
      * Default: `override me`

</details>

<details>
  <summary>Click to expand: course-hall-of-fame-lti/config.j2</summary>

#### <a name="course-hall-of-fame-lticonfigj2"></a>course-hall-of-fame-lti/config.j2

  * `course_hall_of_fame_lti_client_id`: The Canvas client id.
      * Default: `secret`
  * `course_hall_of_fame_lti_dev_key`: The Canvas developer key.
      * Default: `secret`

</details>

<details>
  <summary>Click to expand: course-request-data/config.j2</summary>

#### <a name="course-request-dataconfigj2"></a>course-request-data/config.j2

  * `course_request_data_db_name`: The name of the database.
      * Default: `override me`

</details>

<details>
  <summary>Click to expand: course-request-lti/config.j2</summary>

#### <a name="course-request-lticonfigj2"></a>course-request-lti/config.j2

  * `course_request_lti_client_id`: The Canvas client id.
      * Default: `secret`
  * `course_request_lti_dev_key`: The Canvas developer key.
      * Default: `secret`
  * `dors_lti_client_id`: The current DORS LTI client id.
      * Default: `secret`
  * `old_dors_lti_version`: The version of DORS LTI to remove.
      * Default: `1.1`

</details>

<details>
  <summary>Click to expand: data-basement-data/config.j2</summary>

#### <a name="data-basement-dataconfigj2"></a>data-basement-data/config.j2

  * `data_basement_data_db_name`: The name of the database.
      * Default: `override me`

</details>

<details>
  <summary>Click to expand: data-basement-lti/config.j2</summary>

#### <a name="data-basement-lticonfigj2"></a>data-basement-lti/config.j2

  * `data_basement_lti_client_id`: The Canvas client id.
      * Default: `secret`
  * `data_basement_lti_dev_key`: The Canvas developer key.
      * Default: `secret`

</details>

<details>
  <summary>Click to expand: dors-api/config.j2</summary>

#### <a name="dors-apiconfigj2"></a>dors-api/config.j2

  * `dors_api_url`: The DestinyOne remote url.
      * Default: `https://uminnws.destinysolutions.com/webservice/InternalViewRESTV2`
  * `dors_api_password`: The password used to authenticate to DestinyOne.
      * Default: `secret`
  * `dors_api_user`: The username used to authenticate to DestinyOne.
      * Default: `secret`

</details>

<details>
  <summary>Click to expand: dors-enrollments-api/config.j2</summary>

#### <a name="dors-enrollments-apiconfigj2"></a>dors-enrollments-api/config.j2

  * `dors_enrollments_api_token`: The DORS token for authentication.
      * Default: `secret`
  * `dors_enrollment_port`: The port for the DORS enrollment application.
      * Default: `10446`
  * `dors_enrollment_use_https`: Start the server listening in HTTPS mode
      * Default: `true`
  * [See "Shared"](#shared-template-variables)

</details>

<details>
  <summary>Click to expand: dors-filter/config.j2</summary>

#### <a name="dors-filterconfigj2"></a>dors-filter/config.j2

  * `dors_filter_client_id`: The client id for the Kafka consumer.
      * Default: `dors-filter-client`
  * `dors_filter_group_id`: The client id for the Kafka consumer.
      * Default: `dors-filter-group`
  * `dors_filter_destination_topic`: The kafka topic to write to
      * Default: `dors-grades-filtered`
  * `dors_filter_source_topic`: The kafka topic to read from
      * Default: `dors-grades-firehose`
  * `canvas_instance_id`: The Canvas instance id. These are the digits that precede the tilde in the token.
      * Default: `4356`
  * [See "Shared"](#shared-template-variables)

</details>

<details>
  <summary>Click to expand: dors-grades-consumer/config.j2</summary>

#### <a name="dors-grades-consumerconfigj2"></a>dors-grades-consumer/config.j2

  * `dors_grades_consumer_client_id`: The client id for the Kafka consumer.
      * Default: `dors-grades-client`
  * `dors_grades_consumer_group_id`: The client id for the Kafka consumer.
      * Default: `dors-grades-group`
  * `dors_grades_consumer_destination_topic`: The kafka topic to write to
      * Default: ``
  * `dors_grades_consumer_source_topic`: The kafka topic to read from
      * Default: `dors-grades-filtered`
  * [See "Shared"](#shared-template-variables)

</details>

<details>
  <summary>Click to expand: dors-grades-data/config.j2</summary>

#### <a name="dors-grades-dataconfigj2"></a>dors-grades-data/config.j2

  * `dors_mysql_database_name`: The name of the database.
      * Default: `override me`

</details>

<details>
  <summary>Click to expand: dors-lti/config.j2</summary>

#### <a name="dors-lticonfigj2"></a>dors-lti/config.j2

  * `dors_lti_client_id`: The application's Canvas LTI client id.
      * Default: `secret`
  * `dors_lti_dev_key`: The application's Canvas LTI developer key.
      * Default: `secret`

</details>

<details>
  <summary>Click to expand: kafka-monitor/config.j2</summary>

#### <a name="kafka-monitorconfigj2"></a>kafka-monitor/config.j2

  * `connector_types`: Connector name substrings for monitor
      * Default: `['canvas-firehose', 'grade-audit-firehost', 'masquerade-audit-firehose']`

</details>

<details>
  <summary>Click to expand: manual-section-enrollment/config.js</summary>

#### <a name="manual-section-enrollmentconfigjs"></a>manual-section-enrollment/config.js

  * `manual_section_lti_client_id`: The application's Canvas LTI client id.
      * Default: `secret`
  * `manual_section_lti_dev_key`: The application's Canvas LTI developer key.
      * Default: `secret`

</details>

<details>
  <summary>Click to expand: oracle-connection/config.j2</summary>

#### <a name="oracle-connectionconfigj2"></a>oracle-connection/config.j2

  * `oracle_connection`: The oracle connection string.
      * Default: Uses the long ps connection string.
      * Note: Changes to connection string (short/long version or ps/hotel infix) can be made in tower.
  * `oracle_env`: The oracle server environment.
      * Default: `prd`
      * Possible values:
          * `prd`
          * `qat`
          * `tst`
  * `oracle_password`: The Oracle account password.
      * Default: `secret`
  * `oracle_port`: The port the Oracle host listens on.
      * Default: `1521`
  * `oracle_user`: The Oracle account username.
      * Default: `at-lms`

</details>

<details>
  <summary>Click to expand: send-grades-lti/config.j2</summary>

#### <a name="send-grades-lticonfigj2"></a>send-grades-lti/config.j2

  * `send_grades_lti_client_id`: The application's Canvas LTI client id.
      * Default: `secret`
  * `send_grades_lti_dev_key`: The application's Canvas LTI developer key.
      * Default: `secret`
  * `send_grades_lti_allowed_roles`: An array of roles allowed to launch the LTI.
      * Not currently used because the default value is already part of the configuration.

</details>

<details>
  <summary>Click to expand: send-grades-to-ppsft/config.j2</summary>

#### <a name="send-grades-to-ppsftconfigj2"></a>send-grades-to-ppsft/config.j2

  * `send_grades_to_ppsft_admin_roles`: An array of Canvas administrative roles.
      * Not currently used because the default value is already part of the configuration.
  * `send_grades_to_ppsft_client_world`: The portion of the SOAP url that specifies the environment.
      * Default: `https://csi.qat.psoft.umn.edu`
  * `send_grades_to_ppsft_client_endpoint`: The portion of the SOAP url that specifies the WSDL.
      * Default: `/PSIGW/PeopleSoftServiceListeningConnector/UM_CANVAS_SENDGRADE.1.wsdl`
      * This value doesn't change between environments.
  * `send_grades_to_ppsft_password`: The PeopleSoft SOAP user's password.
      * Default: `secret`
  * `send_grades_to_ppsft_username`: The PeopleSoft SOAP user's username.
      * Default: `secret`

</details>

<details>
  <summary>Click to expand: sync/config.j2</summary>

#### <a name="syncconfigj2"></a>sync/config.j2

  * `sync_report_course_id`: The Canvas course id that contains the sync report.
      * Default: `47497`
  * `sync_terms`: List of term ids to sync.
      * Default: `1239`, `1243`
      * Note: Term ids are calculated using `$current_year - 1900`, then appending semester value.
      * Semester values: `3` (Spring), `5` (Summer), or `9` (Fall/Autumn)
  * `sync_type`: The specific "thing" being synced.
      * Default: `Class`
      * Possible values:
          * `Class`
          * `Instructor`
          * `Student`

</details>

<details>
  <summary>Click to expand: ua-eligibility/config.j2</summary>

#### <a name="ua-eligibilityconfigj2"></a>ua-eligibility/config.j2

  * `ua_sftp_host`: Hostname for SFTP server
      * Default: `aux400.auxs.umn.edu`
  * `ua_sftp_user`: Username for SFTP server
      * Default: `ftpcrslms`
  * `ua_sftp_pass`: Password for SFTP server
      * Default: `secret`
  * `ua_sftp_path`: Path to files on SFTP server
      * Default: `/ftp/crs/pos/in`
  * `ua_file_prefixes`: Filename markers to identify eligiblity files per campus
      * Default: `["CR", "DL", "MO", "RO", "TC"]`

</details>

<details>
  <summary>Click to expand: att-kaltura-api/config.j2</summary>

#### <a name="att-kaltura-apiconfigj2"></a>att-kaltura-api/config.j2

  * `kaltura_api_partner_id`
      * Default: `542131`
  * `kaltura_api_secret`
      * Default: `secret`
  * `kaltura_api_userId`
      * Default: `secret`
  * `kaltura_api_profile_ids`
      * Default: `[3181, 51302, 51292]`

</details>

<details>
  <summary>Click to expand: zoom-license/config.j2</summary>

#### <a name="zoom-licenseconfigj2"></a>zoom-license/config.j2
  * `zoom_account_id`
      * Default: `0`
  * `zoom_client_id`
      * Default: `0`
  * `zoom_secret`
      * Default: `secret`

</details>

<details>
  <summary>Click to expand: zoom-license-controller/config.j2</summary>

#### <a name="zoom-license-controllerconfigj2"></a>zoom-license-controller/config.j2
  * `zoom_exempt_roles`
      * Default: `[]`
  * `zoom_exempt_users`
      * Default: `[]`
  * `zoom_ldap_dn`
      * Default: `secret`
  * `zoom_ldap_pw`
      * Default: `secret`

</details>

</details>

## Handlers

<details>
  <summary>Click to expand: Handlers</summary>

  * `Pickup json5 configuration change`
      * Checks each entry in the `json5_restart_application` list for the presence of the string `sync`. When found, the handler will not execute.

</details>

## Templates

<details>
  <summary>Click to expand: Templates</summary>

  * File: `config.j2`
    * Directories:
      * `att-canvas-api`
      * `att-ldap-wrapper`
      * `att-lti-configuration`
      * `course-request-data`
      * `course-request-lti`
      * `data-basement-data`
      * `data-basement-load`
      * `data-basement-lti`
      * `dors-api`
      * `dors-enrollments-api`
      * `dors-filter`
      * `dors-grades-consumer`
      * `dors-grades-data`
      * `dors-lti`
      * `oracle-connection`
      * `peoplesoft-send-grades-data`
      * `send-grades-lti`
      * `sync`

</details>

## Dependencies

<details>
  <summary>Click to expand: Dependencies</summary>

  * `manage_app_pm2_configuration`
    * We believe you need to have called the `manage_app_pm2_configuration` role prior to calling this role.
    * It may be that order doesn't matter. Unit testing would help.

</details>

## Author

<details>
  <summary>Click to expand: Author</summary>

  Developed by the AT Tools team. For questions or feedback, email attdevs@umn.edu.

</details>

[Back to main README](../../README.md)
