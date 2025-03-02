# Portswigger Writeup

Thực hành các bài labs trên Portswigger.

# Content

- [API Testing](https://github.com/DucThinh47/PortSwigger/tree/main#api-testing)

- [Clickjacking (UI redrressing)](https://github.com/DucThinh47/PortSwigger/tree/main?tab=readme-ov-file#clickjackingui-redressing)

- [Cross-site request forgery](https://github.com/DucThinh47/PortSwigger/tree/main?tab=readme-ov-file#cross-site-request-forgery-csrf)

- [Web LLM attacks](https://github.com/DucThinh47/PortSwigger/tree/main?tab=readme-ov-file#web-llm-attacks)

- [Path traversal](https://github.com/DucThinh47/PortSwigger/tree/main#path-traversal)

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


