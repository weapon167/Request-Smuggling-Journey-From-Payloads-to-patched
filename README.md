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

## TESTING WITH BURPSUITE 
 i then sent the rafted payloads to burpsuite repeater after intercepting the site using burpbrowser and edited them raw
 .The server consistently returned 200 OK when the first  part of the payload is true not knowing there is a second embedded part to it 
 .the first part of the payload i used index.php with my secondary payload down to it Normaly it would have refused to receive the payload cause of the embedded part but since thats its vulnrbility well it didnt refuse 
 
 .and so i tried changing the raw traffick using different endpoints and sending them back to the server but then i noticed only the first part index.php was the only one it brought back no matter the endpint i used(i searched for keywords from the burp traffick i received for keywords based on the endpoints i used) so then seeint  this i descovered wow the vulnerability must have been patched

 ## ANALYSIS 
 the server accepts malformed requests(indicating potential vulnerability)
 however it only processess the first request line
 embedded payloads are ignored 
 no data leakage,no exploitable behaviour

## CONCLUSION 
 vulnerability suspected but patched hence not exploitable
 No usernames,passwords or admin data exposed

## UPDATE -february 2026
Technical Research Update: The "Never Mind" Chronicles
Project: Security Audit of [zetech.ac.ke]

Status: Confirmed Vulnerable (CL.TE Request Smuggling)

Last Updated: February 26, 2026

 Executive Correction: Reopening the Case
In my previous report, I noted that the site appeared to be patched. I was wrong. While the obvious error messages were silenced, the underlying logic flaw—the "desynchronization"—remained live. This update documents the journey from a suspected patch back into the heart of a high-severity vulnerability.

 Phase 1: The EOL Red Flag
The journey began with simple recon. nmap scans and HTTP headers revealed the server is running Apache 2.4.52/2.4.58.
### Nmap Scan Evidence
![Nmap Scan](evidence/nmap-scan-2026-02-22-02-18-00.jpeg)

 Insight: These versions are widely known to be End-of-Life (EOL) or lacking critical security backports in certain configurations.

  Infrastructure this brittle rarely gets "patched" with a single click. It usually requires a total architecture changes  that hadn't happened yet.

 Phase 2: Proving the Desync 
To prove the vulnerability wasn't fixed, I moved from simple requests to Timing Payloads and CL.TE Desynchronization techniques.

I achieved the the 303 Redirect.

By sending a single, specifically crafted chunked POST request, I forced the back-end to process a second hidden request. The server responded with a 303 See Other redirecting to the login page.

### Payload with Session Cookie
![Payload with Session cookie](images/payload-session-cookie.jpeg)

- In a standard HTTP environment, one request equals one response. Forcing the server to respond to a "smuggled" request proves the front-end and back-end are desynchronized.

-as at now i have successfully broken the server’s request-processing logic.

 Phase 3: The "Wall" of Session Hijacking
Currently, I am working on the final 10%: Capturing the Session. This is where the difficulty spikes.

I successfully implemented a "Profile Sink," attempting to smuggle my own MoodleSession cookie to update my bio with a victim's data. However, I hit a sophisticated "Shield":

Connection Termination: The server is responding with Connection: close.

The Impact: The server is essentially "hanging up the phone" after the first request, which prevents the victim’s data from being "glued" to my smuggled payload.

 Lessons in Persistence
I’ll admit: I didn't know much about the deep mechanics of Session Hijacking when I started this. I thought finding the bug was the end of the road.

Current Status: Heading back to the lab to master TCP connection persistence and Moodle-specific session handling. It turns out that breaking a server’s brain is easy; making it hand over the keys is the real challenge.

Next Steps:

Refine the Content-Length math to bypass connection resets.

Experiment with Proxy-Connection headers to force keep-alive.

Locate a "Session Hijacking Master" for a peer review of the payload timing.



##  LEARNING OUTCOMES 
 -I ended up learning how to use ZAP for reconaissance in start of this project 
 -i had to go back to the daring board and learn how too craft payloads and how they actually and i understood the difference between "accepts malformed input vs actually explLearning    
 outcome,Technical Mastery Achieved
-Infrastructure Fingerprinting,Learned to identify End-of-Life (EOL) Apache versions (2.4.52/58) and link them to known desynchronization vulnerabilities.
-Desynchronization Verification,"Successfully proved CL.TE Desynchronization by triggering a 303 See Other response—proving the back-end is processing requests the front-end didn't 
-Identifying Connection Shields,Learned to diagnose Connection Termination (Connection: close) as a primary defense mechanism against session capture.
-come to think of it this projct has also helped me a lot in mastering patience and persistence 
 
