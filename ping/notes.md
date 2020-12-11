# Ping: SP-initiated SSO to Salesforce.com and SAML 2.0
How to configure PF in IdP role to enable SP-initiated SSO to Salesforce.com via SAML 2.0 (instead of Delegated Authentication)?
 
Solution
 
Prerequisite: SSO settings in Salesforce.com are configured properly, including enabling SAML for SSO.
 
Steps:
- Under My IdP Configuration, setup a SAML 2.0 SP connection to Salesforce.com
- Enter https://saml.salesforce.com as the Partner's Entity ID
- Enable IdP-Initiated SSO and SP-Initiated SSO
- In Attribute Contract, add an attribute called, ssoStartPage, select ...unspecified as the Attribute Name Format
    - Enter http[s]://<PF external address>:<PF external port>/idp/SSO.saml2 as the value
- In Assertion Consumer Service URL, enter 0 for Index, POST for Binding, and https://login.salesforce.com as Endpoint
- In Allowable SAML Bindings, select only POST
- In Signature Policy, select Always sign the SAML Assertion
- For each browser, once the user has completed a successful IdP-initiated SSO to Salesforce.com, Salesforce.com would have sent back the content and many cookies; the browser should be saving the persistent cookies.
 
- Of all the persistent cookies, ssostartpage and saml_request_id are the cookies for SP-init SSO requests.  These two cookies are set for the specific <host>.salesforce.com in the IdP-init SSO request.  Their expiry is about 2 months.
 
- As long as these two cookies are saved by the browser, then subsequent SP-init SSO requests to the same <host>.salesforce.com (with these two cookies) would trigger Salesforce.com to send an AuthnRequest to http[s]://<PF external address>:<PF external port>/idp/SSO.saml2 - the value in the ssoStartPage attribute in the SAML contract and the ssostartpage cookie.
 
- If either is missing, then any subsequent SP-init SSO requests would fail.
 
- If the subsequent SP-init SSO request is sent to <another host>.salesforce.com, then it would also fail, because the browser will not send the ssostartpage and saml_request_id which were set for <host>.salesforce.com.
 
- About ssoStartPage
    - SP-initiated SAML SSO in Salesforce now uses the 'My Domain' feature to remove the need for the persistent cookie. Set up 'My Domain', then, when users go to http://your_cust_name.my.salesforce.com, Salesforce will use the hostname to figure out the correct identity provider (IdP) to which it will redirect the user.

## References
- [SP-initiated SSO to Salesforce.com and SAML 2.0](https://support.pingidentity.com/s/article/SP-initiated-SSO-to-Salesforce-com-and-SAML-2-0)
- [Pingfederate 8.0+ - HTTP Request Header Size To Large](https://support.pingidentity.com/s/article/Pingfederate-8-HTTP-Request-Header-Size-To-Large)
 -[akamai error codes](https://learn.akamai.com/en-us/webhelp/enterprise-application-access/enterprise-application-access/GUID-34A1894E-48A3-4B5A-A9D5-2FE1196CB54E.html)
