# Request-Smuggling-Journey-From-Payloads-to-patched
 Welcome to my interactive case study repo.
 This project documents my hands on exploitation of HTTP request smuggling, from crafting payloads,testing endpoints ,analyzing all the way to discover the target system was patched and not exploitable
 
**Tools used**
-Burpsuite repeatr for sending crafted requests 
-firefox for endpoint testing
-zaproxy for scanning vulnerabilities in the site 
**Endpoints Tested**
-/index.php(baseline test)
-/admin/users

 ## RECONNAISSANCE WITH ZAP
 i started by scanning Zetech.ac.ke using OWASP ZAP to find potential vulnerabilities.
 -ZAP flagged the server type and version
 -This gave me a lead to check if the server has known vulnerabilities.
 
 ##CVE RESEARCH 
 I then checked for CVE advisories
 -Found reference to vulnerabilities in Apache versions.
 -Decided to craft payloads to test if the site can be affected
 
 ## PAYLOAD CRAFTING 
 i built a payload with conflicting headers and secondary embedded requests

 TESTING WITH BURPSUITE 
 i then sent the rafted payloads to burpsuite repeater after intercepting the site using burpbrowser and edited them raw
 .The server consistently returned 200 OK when the first  part of the payload is true not knowing there is a second embedded part to it 
 .the first part of the payload i used index.php with my secondary payload down to it Normaly it would have refused to receive the payload cause of the embedded part but since thats its vulnrbility well it didnt refuse 
 
 .and so i tried changing the raw traffick using different endpoints and sending them back to the server but then i noticed only the first part index.php was the only one it brought back no matter the endpint i used(i searched for keywords from the burp traffick i received for keywords based on the endpoints i used) so then seeint  this i descovered wow the vulnerability must have been patched

 ANALYSIS 
 the server accepts malformed requests(indicating potential vulnerability)
 however it only processess the first request line
 embedded payloads are ignored 
 no data leakage,no exploitable behaviour

 CONCLUSION 
 vulnerability suspected but patched hence not exploitable
 No usernames,passwords or admin data exposed
 LEARNING OUTCOMES 
 i ended up learning how to use ZAP for reconaissance which i use to date 
 i had to go back to the daring board and learn how too craft payloads and how they actually work 
 and i understood the difference between "accepts malformed input vs actually exploitable 
