# Portswigger Writeup

Thực hành các bài labs trên Portswigger.

## Content

- [API Testing](https://github.com/DucThinh47/PortSwigger/tree/main#api-testing)
- [Clickjacking (UI redrressing)](https://github.com/DucThinh47/PortSwigger/tree/main?tab=readme-ov-file#clickjackingui-redressing)
- [Cross-site request forgery](https://github.com/DucThinh47/PortSwigger/tree/main?tab=readme-ov-file#cross-site-request-forgery-csrf)
- [Web LLM attacks](https://github.com/DucThinh47/PortSwigger/tree/main?tab=readme-ov-file#web-llm-attacks)
- [Path traversal](https://github.com/DucThinh47/PortSwigger/tree/main#path-traversal)
- [SQL injection](https://github.com/DucThinh47/PortSwigger#sql-injection)
- [Cross-origin resource sharing (CORS)](https://github.com/DucThinh47/PortSwigger#cross-origin-resource-sharing-cors)
- [NoSQL Injection](https://github.com/DucThinh47/PortSwigger/tree/main#nosql-injection)
- [Server-side request forgery (SSRF) attacks](https://github.com/DucThinh47/PortSwigger#server-side-request-forgery-ssrf-attacks)
- [Server-side vulnerabilities](https://github.com/DucThinh47/PortSwigger/tree/main#server-side-vulnerabilities)
- [Race conditions](https://github.com/DucThinh47/PortSwigger/tree/main#race-conditions)
- [Authentication](https://github.com/DucThinh47/PortSwigger/tree/main#authentication)
- [Server-side template injection](https://github.com/DucThinh47/PortSwigger#server-side-template-injection)
- [HTTP Host header attacks](https://github.com/DucThinh47/PortSwigger#http-host-header-attacks)
- [JWT attacks](https://github.com/DucThinh47/PortSwigger/tree/main#jwt-attacks)
- [HTTP request smuggling](https://github.com/DucThinh47/PortSwigger/tree/main#http-request-smuggling)

### API Testing

- [Lab: Exploiting an API endpoint using documentation](https://github.com/DucThinh47/PortSwigger/blob/main/API_testing/API_documentation.md#lab-exploiting-an-api-endpoint-using-documentation)
- [Lab: Finding and exploiting an unused API endpoint](https://github.com/DucThinh47/PortSwigger/blob/main/API_testing/Identifying_API_endpoints.md#lab-finding-and-exploiting-an-unused-api-endpoint)
- [Lab: Exploiting a mass assignment vulnerability](https://github.com/DucThinh47/PortSwigger/blob/main/API_testing/Mass_assignment_vulnerabilities.md#lab-exploiting-a-mass-assignment-vulnerability)
- [Lab: Exploiting server-side parameter pollution in a query string](https://github.com/DucThinh47/PortSwigger/blob/main/API_testing/Testing_for_server-side_parameter_pollution_in_the_query_string.md#lab-exploiting-server-side-parameter-pollution-in-a-query-string)

### Clickjacking (UI redressing)

- [Lab: Basic clickjacking with CSRF token protection](https://github.com/DucThinh47/PortSwigger/blob/main/Clickjacking(UI_redressing)/How_to_construct_a_basic_clickjacking_attack.md#lab-basic-clickjacking-with-csrf-token-protection)

### Clickjacking(UI redressing)

- [Lab: Clickjacking with form input data prefilled from a URL parameter](https://github.com/DucThinh47/PortSwigger/blob/main/Clickjacking(UI_redressing)/Clickjacking_with_prefilled_form_input.md#lab-clickjacking-with-form-input-data-prefilled-from-a-url-parameter)
- [Lab: Clickjacking with a frame buster script](https://github.com/DucThinh47/PortSwigger/blob/main/Clickjacking(UI_redressing)/Frame_busting_scripts.md#lab-clickjacking-with-a-frame-buster-script)
- [Lab: Exploiting clickjacking vulnerability to trigger DOM-based XSS](https://github.com/DucThinh47/PortSwigger/blob/main/Clickjacking(UI_redressing)/Combining_clickjacking_with_a_DOM_XSS_attack.md#lab-exploiting-clickjacking-vulnerability-to-trigger-dom-based-xss)
- [Lab: Multistep clickjacking](https://github.com/DucThinh47/PortSwigger/blob/main/Clickjacking(UI_redressing)/Multistep_clickjacking.md#lab-multistep-clickjacking)

### Cross-site request forgery (CSRF)

- [Lab: CSRF vulnerability with no defenses](https://github.com/DucThinh47/PortSwigger/blob/main/Cross-site_request_forgery_(CSRF)/How_to_construct_a_CSRF_attack.md#lab-csrf-vulnerability-with-no-defenses)
- [Lab: CSRF where token validation depends on request method](https://github.com/DucThinh47/PortSwigger/blob/main/Cross-site_request_forgery_(CSRF)/Common_flaws_in_CSRF_token_validation.md#lab-csrf-where-token-validation-depends-on-request-method)
- [Lab: CSRF where token validation depends on token being present](https://github.com/DucThinh47/PortSwigger/blob/main/Cross-site_request_forgery_(CSRF)/Common_flaws_in_CSRF_token_validation.md#lab-csrf-where-token-validation-depends-on-token-being-present)
- [Lab: CSRF where token is not tied to user session](https://github.com/DucThinh47/PortSwigger/blob/main/Cross-site_request_forgery_(CSRF)/Common_flaws_in_CSRF_token_validation.md#lab-csrf-where-token-is-not-tied-to-user-session)
- [Lab: CSRF where token is tied to non-session cookie](https://github.com/DucThinh47/PortSwigger/blob/main/Cross-site_request_forgery_(CSRF)/Common_flaws_in_CSRF_token_validation.md#lab-csrf-where-token-is-tied-to-non-session-cookie)
- [Lab: CSRF where token is duplicated in cookie](https://github.com/DucThinh47/PortSwigger/blob/main/Cross-site_request_forgery_(CSRF)/Common_flaws_in_CSRF_token_validation.md#lab-csrf-where-token-is-duplicated-in-cookie)
- [Lab: SameSite Lax bypass via method override](https://github.com/DucThinh47/PortSwigger/blob/main/Cross-site_request_forgery_(CSRF)/Bypassing_SameSite_Lax_restrictions_using_GET_requests.md#lab-samesite-lax-bypass-via-method-override)
- [Lab: SameSite Strict bypass via client-side redirect](https://github.com/DucThinh47/PortSwigger/blob/main/Cross-site_request_forgery_(CSRF)/Bypassing_SameSite_restrictions_using_on-site_gadgets.md#lab-samesite-strict-bypass-via-client-side-redirect)
- [Lab: SameSite Strict bypass via sibling domain](https://github.com/DucThinh47/PortSwigger/blob/main/Cross-site_request_forgery_(CSRF)/Bypassing_SameSite_restrictions_via_vulnerable_sibling_domains.md#lab-samesite-strict-bypass-via-sibling-domain)
- [Lab: SameSite Lax bypass via cookie refresh](https://github.com/DucThinh47/PortSwigger/blob/main/Cross-site_request_forgery_(CSRF)/Bypassing_SameSite_Lax_restrictions_with_newly_issued_cookies.md#lab-samesite-lax-bypass-via-cookie-refresh)
- [Lab: CSRF where Referer validation depends on header being present](https://github.com/DucThinh47/PortSwigger/blob/main/Cross-site_request_forgery_(CSRF)/Validation_of_Referer_can_be_circumvented.md#lab-csrf-with-broken-referer-validation)

### Web LLM attacks

- [Lab: Exploiting LLM APIs with excessive agency](https://github.com/DucThinh47/PortSwigger/blob/main/Web_LLM_attacks/Exploiting_LLM_APIs_functions_and_plugins.md#lab-exploiting-llm-apis-with-excessive-agency)
- [Lab: Exploiting vulnerabilities in LLM APIs](https://github.com/DucThinh47/PortSwigger/blob/main/Web_LLM_attacks/Exploiting_LLM_APIs_functions_and_plugins.md#lab-exploiting-vulnerabilities-in-llm-apis)
- [Lab: Indirect prompt injection](https://github.com/DucThinh47/PortSwigger/blob/main/Web_LLM_attacks/Indirect_prompt_injection.md#lab-indirect-prompt-injection)

### Path traversal 

- [Lab: File path traversal, simple case](https://github.com/DucThinh47/PortSwigger/blob/main/Path-Traversal/Reading_arbitrary_files_via_path_traversal.md#lab-file-path-traversal-simple-case)
- [Lab: File path traversal, traversal sequences blocked with absolute path bypass](https://github.com/DucThinh47/PortSwigger/blob/main/Path-Traversal/Common_obstacles_to_exploiting_path_traversal_vulnerabilities.md#lab-file-path-traversal-traversal-sequences-blocked-with-absolute-path-bypass)
- [Lab: File path traversal, traversal sequences stripped non-recursively](https://github.com/DucThinh47/PortSwigger/blob/main/Path-Traversal/Common_obstacles_to_exploiting_path_traversal_vulnerabilities.md#lab-file-path-traversal-traversal-sequences-stripped-non-recursively)
- [Lab: File path traversal, traversal sequences stripped with superfluous URL-decode](https://github.com/DucThinh47/PortSwigger/blob/main/Path-Traversal/Common_obstacles_to_exploiting_path_traversal_vulnerabilities.md#lab-file-path-traversal-traversal-sequences-stripped-with-superfluous-url-decode)
- [Lab: File path traversal, validation of start of path](https://github.com/DucThinh47/PortSwigger/blob/main/Path-Traversal/Common_obstacles_to_exploiting_path_traversal_vulnerabilities.md#lab-file-path-traversal-validation-of-start-of-path)
- [Lab: File path traversal, validation of file extension with null byte bypass](https://github.com/DucThinh47/PortSwigger/blob/main/Path-Traversal/Common_obstacles_to_exploiting_path_traversal_vulnerabilities.md#lab-file-path-traversal-validation-of-file-extension-with-null-byte-bypass)

### SQL injection

- [Lab: SQL injection vulnerability in WHERE clause allowing retrieval of hidden data](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/Retrieving_hidden_data.md#lab-sql-injection-vulnerability-in-where-clause-allowing-retrieval-of-hidden-data)
- [Lab: SQL injection vulnerability allowing login bypass](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/Subverting_application_logic.md#lab-sql-injection-vulnerability-allowing-login-bypass)
- [Lab: SQL injection UNION attack, determining the number of columns returned by the query](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/Determining_the_number_of_columns_required.md#lab-sql-injection-union-attack-determining-the-number-of-columns-returned-by-the-query)
- [Lab: SQL injection UNION attack, finding a column containing text](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/Finding_columns_with_a_useful_data_type.md#lab-sql-injection-union-attack-finding-a-column-containing-text)
- [Lab: SQL injection UNION attack, retrieving data from other tables](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/Using_a_SQL_injection_UNION_attack_to_retrieve_interesting_data.md#lab-sql-injection-union-attack-retrieving-data-from-other-tables)
- [Lab: SQL injection UNION attack, retrieving multiple values in a single column](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/Retrieving_multiple_values_within_a_single_column.md#lab-sql-injection-union-attack-retrieving-multiple-values-in-a-single-column)
- [Lab: SQL injection attack, querying the database type and version on MySQL and Microsoft](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/Examining_the_database_in_SQL_injection_attacks.md#lab-sql-injection-attack-querying-the-database-type-and-version-on-mysql-and-microsoft)
- [Lab: SQL injection attack, listing the database contents on non-Oracle databases](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/Examining_the_database_in_SQL_injection_attacks.md#lab-sql-injection-attack-listing-the-database-contents-on-non-oracle-databases)
- [Lab: Blind SQL injection with conditional responses](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/Exploiting_blind_SQL_injection_by_triggering_conditional_responses.md#lab-blind-sql-injection-with-conditional-responses)
- [Lab: Blind SQL injection with conditional errors](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/Error-based_SQL_injection.md#lab-blind-sql-injection-with-conditional-errors)
- [Lab: Visible error-based SQL injection](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/Error-based_SQL_injection.md#lab-visible-error-based-sql-injection)
- [Lab: Blind SQL injection with time delays and information retrieval](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/Exploiting_blind_SQL_injection_by_triggering_time_delays.md#lab-blind-sql-injection-with-time-delays-and-information-retrieval)
- [Lab: SQL injection with filter bypass via XML encoding](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/SQL_injection_in_different_contexts.md#lab-sql-injection-with-filter-bypass-via-xml-encoding)

### Cross-origin resource sharing (CORS) 

- [Lab: CORS vulnerability with basic origin reflection](https://github.com/DucThinh47/PortSwigger/blob/main/Cross-origin-resource-sharing-(CORS)/Vulnerabilities_arising_from_CORS_configuration_issues.md#lab-cors-vulnerability-with-basic-origin-reflection)
- [Lab: CORS vulnerability with trusted null origin](https://github.com/DucThinh47/PortSwigger/blob/main/Cross-origin-resource-sharing-(CORS)/Vulnerabilities_arising_from_CORS_configuration_issues.md#lab-cors-vulnerability-with-trusted-null-origin)
- [Lab: CORS vulnerability with trusted insecure protocols](https://github.com/DucThinh47/PortSwigger/blob/main/Cross-origin-resource-sharing-(CORS)/Vulnerabilities_arising_from_CORS_configuration_issues.md#lab-cors-vulnerability-with-trusted-insecure-protocols)

### NoSQL Injection

- [Lab: Detecting NoSQL injection](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/NoSQL_Injection.md#lab-detecting-nosql-injection)
- [Lab: Exploiting NoSQL operator injection to bypass authentication](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/NoSQL_Injection.md#lab-exploiting-nosql-operator-injection-to-bypass-authentication)
- [Lab: Exploiting NoSQL injection to extract data](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/NoSQL_Injection.md#lab-exploiting-nosql-injection-to-extract-data)
- [Lab: Exploiting NoSQL operator injection to extract unknown fields](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/NoSQL_Injection.md#lab-exploiting-nosql-operator-injection-to-extract-unknown-fields)

### Server-side request forgery (SSRF) attacks 

- [Lab: Basic SSRF against the local server](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-request-forgery-(SSRF)-attacks/SSRF.md#lab-basic-ssrf-against-the-local-server)
- [Lab: Basic SSRF against another back-end system](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-request-forgery-(SSRF)-attacks/SSRF.md#lab-basic-ssrf-against-another-back-end-system)
- [Lab: SSRF with blacklist-based input filter](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-request-forgery-(SSRF)-attacks/SSRF.md#lab-ssrf-with-blacklist-based-input-filter)
- [Lab: SSRF with filter bypass via open redirection vulnerability](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-request-forgery-(SSRF)-attacks/SSRF.md#lab-ssrf-with-filter-bypass-via-open-redirection-vulnerability)

### Server-side vulnerabilities

- [Lab: File path traversal, simple case](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/Server_side_vulnerabilities.md#lab-file-path-traversal-simple-case)
- [Lab: Unprotected admin functionality](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/Server_side_vulnerabilities.md#lab-unprotected-admin-functionality)
- [Lab: Unprotected admin functionality with unpredictable URL](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/Server_side_vulnerabilities.md#lab-unprotected-admin-functionality-with-unpredictable-url)
- [Lab: User role controlled by request parameter](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/Server_side_vulnerabilities.md#lab-user-role-controlled-by-request-parameter)
- [Lab: User ID controlled by request parameter, with unpredictable user IDs](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/Server_side_vulnerabilities.md#lab-user-id-controlled-by-request-parameter-with-unpredictable-user-ids)
- [Lab: User ID controlled by request parameter with password disclosure](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/Server_side_vulnerabilities.md#lab-user-id-controlled-by-request-parameter-with-password-disclosure)
- [Lab: Username enumeration via different responses](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/Server_side_vulnerabilities.md#lab-username-enumeration-via-different-responses)
- [Lab: 2FA simple bypass](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/Server_side_vulnerabilities.md#lab-2fa-simple-bypass)
- [Lab: Basic SSRF against the local server](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/Server_side_vulnerabilities.md#lab-basic-ssrf-against-the-local-server)
- [Lab: Basic SSRF against another back-end system](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/Server_side_vulnerabilities.md#lab-basic-ssrf-against-another-back-end-system)
- [Lab: Remote code execution via web shell upload](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/Server_side_vulnerabilities.md#lab-remote-code-execution-via-web-shell-upload)
- [Lab: Web shell upload via Content-Type restriction bypass](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/Server_side_vulnerabilities.md#lab-web-shell-upload-via-content-type-restriction-bypass)
- [Lab: OS command injection, simple case](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/Server_side_vulnerabilities.md#lab-os-command-injection-simple-case)
- [Lab: SQL injection vulnerability in WHERE clause allowing retrieval of hidden data](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/Server_side_vulnerabilities.md#lab-sql-injection-vulnerability-in-where-clause-allowing-retrieval-of-hidden-data)
- [Lab: SQL injection vulnerability allowing login bypass](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/Server_side_vulnerabilities.md#lab-sql-injection-vulnerability-allowing-login-bypass)

### Race conditions

- [Lab: Limit overrun race conditions](https://github.com/DucThinh47/PortSwigger/blob/main/Race-conditions/Race_conditions.md#lab-limit-overrun-race-conditions)
- [Lab: Bypassing rate limits via race conditions](https://github.com/DucThinh47/PortSwigger/blob/main/Race-conditions/Race_conditions.md#lab-bypassing-rate-limits-via-race-conditions)
- [Lab: Multi-endpoint race conditions](https://github.com/DucThinh47/PortSwigger/blob/main/Race-conditions/Race_conditions.md#lab-multi-endpoint-race-conditions)
- [Lab: Single-endpoint race conditions](https://github.com/DucThinh47/PortSwigger/blob/main/Race-conditions/Race_conditions.md#lab-single-endpoint-race-conditions)

### Authentication

- [Lab: Username enumeration via different responses](https://github.com/DucThinh47/PortSwigger/blob/main/Authentication/Contents.md#lab-username-enumeration-via-different-responses)
- [Lab: 2FA simple bypass](https://github.com/DucThinh47/PortSwigger/blob/main/Authentication/Contents.md#lab-2fa-simple-bypass)
- [Lab: Password reset broken logic](https://github.com/DucThinh47/PortSwigger/blob/main/Authentication/Contents.md#lab-password-reset-broken-logic)
- [Lab: Username enumeration via subtly different responses](https://github.com/DucThinh47/PortSwigger/blob/main/Authentication/Contents.md#lab-username-enumeration-via-subtly-different-responses)
- [Lab: Username enumeration via response timing](https://github.com/DucThinh47/PortSwigger/blob/main/Authentication/Contents.md#lab-username-enumeration-via-response-timing)
- [Lab: Broken brute-force protection, IP block](https://github.com/DucThinh47/PortSwigger/blob/main/Authentication/Contents.md#lab-broken-brute-force-protection-ip-block)
- [Lab: Username enumeration via account lock](https://github.com/DucThinh47/PortSwigger/blob/main/Authentication/Contents.md#lab-username-enumeration-via-account-lock)
- [Lab: 2FA broken logic](https://github.com/DucThinh47/PortSwigger/blob/main/Authentication/Contents.md#lab-2fa-broken-logic)

### Server-side template injection

- [Lab: Basic server-side template injection](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side_template_injection/Contents.md#lab-basic-server-side-template-injection)
- [Lab: Basic server-side template injection (code context)](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side_template_injection/Contents.md#lab-basic-server-side-template-injection-code-context)
- [Lab: Server-side template injection using documentation](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side_template_injection/Contents.md#lab-server-side-template-injection-using-documentation)
- [Lab: Server-side template injection in an unknown language with a documented exploit](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side_template_injection/Contents.md#lab-server-side-template-injection-in-an-unknown-language-with-a-documented-exploit)
- [Lab: Server-side template injection with information disclosure via user-supplied objects](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side_template_injection/Contents.md#lab-server-side-template-injection-with-information-disclosure-via-user-supplied-objects)

### HTTP Host header attacks
- [Lab: Basic password reset poisoning](https://github.com/DucThinh47/PortSwigger/blob/main/HTTP_Host_header_attacks/Contents.md#lab-basic-password-reset-poisoning)
- [Lab: Host header authentication bypass](https://github.com/DucThinh47/PortSwigger/blob/main/HTTP_Host_header_attacks/Contents.md#lab-host-header-authentication-bypass)
- [Lab: Web cache poisoning via ambiguous requests](https://github.com/DucThinh47/PortSwigger/blob/main/HTTP_Host_header_attacks/Contents.md#lab-web-cache-poisoning-via-ambiguous-requests)

### JWT attacks
- [Lab: JWT authentication bypass via unverified signature](https://github.com/DucThinh47/PortSwigger/blob/main/JWT_Attacks/Contents.md#lab-jwt-authentication-bypass-via-unverified-signature)
- [Lab: JWT authentication bypass via flawed signature verification](https://github.com/DucThinh47/PortSwigger/blob/main/JWT_Attacks/Contents.md#lab-jwt-authentication-bypass-via-flawed-signature-verification)
- [Lab: JWT authentication bypass via weak signing key](https://github.com/DucThinh47/PortSwigger/blob/main/JWT_Attacks/Contents.md#lab-jwt-authentication-bypass-via-weak-signing-key)
- [Lab: JWT authentication bypass via jwk header injection](https://github.com/DucThinh47/PortSwigger/blob/main/JWT_Attacks/Contents.md#lab-jwt-authentication-bypass-via-jwk-header-injection)
- [Lab: JWT authentication bypass via jku header injection](https://github.com/DucThinh47/PortSwigger/blob/main/JWT_Attacks/Contents.md#lab-jwt-authentication-bypass-via-jku-header-injection)
- [Lab: JWT authentication bypass via kid header path traversal](https://github.com/DucThinh47/PortSwigger/blob/main/JWT_Attacks/Contents.md#lab-jwt-authentication-bypass-via-kid-header-path-traversal)

### HTTP request smuggling
- [Lab: HTTP request smuggling, confirming a CL.TE vulnerability via differential responses](https://github.com/DucThinh47/PortSwigger/blob/main/HTTP_request_smuggling/Contents.md#lab-http-request-smuggling-confirming-a-clte-vulnerability-via-differential-responses)
- [Lab: HTTP request smuggling, confirming a TE.CL vulnerability via differential responses]()