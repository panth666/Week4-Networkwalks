# Week4-Networkwalks
#🏥Mediroza General Hospital — Black-Box Penetration Test
The last week of this internship program. Main target Mediroza General Hospital [](https://medirozahospital.com/) 📌 This is a training which is carried out against the purpose-built lab. 
<img width="977" height="524" alt="image" src="https://github.com/user-attachments/assets/75850cfc-3995-4e4d-9210-80cdc801e7ee" />
# Project Brief
Type : Penetration Testing & Vulnerability Assessment on the Live Website
Client : Mediroza General Hospital
Target : [](https://medirozahospital.com/)
Goal : Full black-box penetration test

# What to Do?
|#|Task|Goal|
|-|----|----|
|1|Early Access| Attacking the website and retrieve 3 Confidential patient LAB report|
|2|Data Extraction|Crack the Encryption on the retrieved file|
|3|Cracking |Uncovering the Staff and the Shareholders data|
|4|Final Report|Delivering the final professional penetration testing report|

# Methodology
The testing was performed according to a standard black-box methodology, generally conforming to the PTES and the OWASP Testing Guide:

Reconnaissance▶️ Mapping ▶️ Analysis ▶️ Exploitation ▶️ Data ▶️ Recovery ▶️ Reporting

Tools: curl (rapid cli recon), Burp Community (proxy, Repeater, Intruder), a proxied chromium browser (to Burp), and a tool for hash extraction + dictionary attack on the encrypted PDFs.

# Full Walkthrough 
M1 - Early Access
Step 1 - Command-line reconnaissance Before opening a browser, we fingerprinted the stack with a minimal footprint using:

<img width="468" height="157" alt="image" src="https://github.com/user-attachments/assets/4300c165-dc40-47c4-a5dc-097b12af589b" />

Next up was a robots.txt - which gave us way more than we bargained for: /patient/, /staff/, and /old/ were all explicitly banned:

<img width="468" height="87" alt="image" src="https://github.com/user-attachments/assets/390bb762-7357-46e5-bdb1-cb549f84de90" />

This is an effectively authored map of the site's most sensitive areas, revealed before any manual page browsing was performed. Even more (or less, depending on your perspective) reassuring was the sitemap.xml at the bottom of robots.txt, which was also verified, to find it contained the publicly visible marketing page (index, about, doctors, contact) - really proved sensitive areas intentionally omitted from the "official" map, rather than overlooked:

<img width="468" height="138" alt="image" src="https://github.com/user-attachments/assets/c0ab1b4f-ec4f-49b2-8874-cb251bef83f5" />

Step 2 - Browser reconnaissance. Access path /patient/login.php detected by robots.txt was directly opened in a Burp-proxied browser:

<img width="468" height="138" alt="image" src="https://github.com/user-attachments/assets/4edbda79-fb13-4686-9a6c-7b7c6f6572d3" />

Step 3 - Login test (username enumeration present) We entered a very real-sounding but non-existent username to determine if the application behaved normally - it returned an explicit message indicating the username was unknown (this is a second finding, since it checks whether the username exists, then verifies the password:

<img width="468" height="309" alt="image" src="https://github.com/user-attachments/assets/0b015fe2-546e-40c7-8f55-3786499b6fdf" />

Step 4 - Manual confirmation & exploitation. The reproduced a SQL syntax anomaly, confirming that unsanitized input makes it to the database layer. The standard payload to always bypass authentication was used as the username, with any value as the password:

<img width="612" height="218" alt="image" src="https://github.com/user-attachments/assets/d39f28db-516d-450e-a71d-9ba8cb2be6e8" />

Step 5-Impact: unauthorized access to data. Bypassed and went to "My Reports" - three password protected reports of pathology:

<img width="468" height="309" alt="image" src="https://github.com/user-attachments/assets/ec0e67c0-5c33-4e96-b07f-7c1611a61c17" />

# Milestone2- Cracking the Encryption
In this phase each of the 3 downloaded file were opened with the password prompt, exactly as advertised on the portal and on the week 3 . In this phase all the three files were treated as can independent target. Each evidence are below and all the hash valve were done as per the week 3.

patient_report_1.pdf: Sipho Dlamini (Password:123456)

<img width="468" height="309" alt="image" src="https://github.com/user-attachments/assets/10b2d6b1-204f-4bc3-b23b-f86282dfc30e" />

<img width="468" height="309" alt="image" src="https://github.com/user-attachments/assets/24b070dc-9c4d-43ed-b8f1-9f242b8238b4" />

patient_report_2.pdf: Priya Reddy (Password:password)

<img width="468" height="309" alt="image" src="https://github.com/user-attachments/assets/df187807-a85b-4ee4-823b-5de5b0316f23" />

<img width="468" height="309" alt="image" src="https://github.com/user-attachments/assets/f8da2ae1-c5b4-4b05-ae10-3cbd53966403" />

patient_report_3.pdf: Emily Thompson (!@#$%^&)

<img width="468" height="309" alt="image" src="https://github.com/user-attachments/assets/ec663349-be1a-4416-8c90-bb129e90dd11" /> 

<img width="468" height="309" alt="image" src="https://github.com/user-attachments/assets/d0f0e9b7-f2a5-4ae2-84a3-922bc253d848" />

# Critical Data Exposure
   Following M3 The hidden directories discovered earlier by robots.txt, /staff/ and /old/, were then located directly, after the M3 brief to look for content.

/www/ - directory listing present. Rather than a 403/404, the server rather responded with a full directory listing exposing staff/login.php by name:

<img width="325" height="257" alt="image" src="https://github.com/user-attachments/assets/73692a8a-9c0d-4a6a-b358-6a57d4ff10fd" />

/old/ — a far more serious exposure. The same misconfiguration on /old/ revealed a publicly downloadable historical database backup, mediroza_db_backup_2019.sql:

<img width="325" height="257" alt="image" src="https://github.com/user-attachments/assets/80fa9258-7daa-4bc8-acab-1618b966bbde" />
<img width="468" height="285" alt="image" src="https://github.com/user-attachments/assets/b9775b66-ae34-4d32-8bbb-bf2256bcedfd" />

The dump included full staff records — names, job titles, departments, contact details, national ID numbers, and monthly salaries for all 30 hospital employees:

<img width="468" height="285" alt="image" src="https://github.com/user-attachments/assets/ff4f4e1a-c444-46a0-96e0-e3d8626ad617" />

<img width="468" height="285" alt="image" src="https://github.com/user-attachments/assets/22544740-7571-4934-89fb-f97135f2c446" />

.and a separate shareholders table listing ownership stakes in the hospital:

<img width="468" height="285" alt="image" src="https://github.com/user-attachments/assets/3318740d-f559-46d3-944f-58f659e18919" />

# Extra Evidence which were worth 

<img width="468" height="190" alt="image" src="https://github.com/user-attachments/assets/70d194a5-2ff2-4612-a513-61f3cae69d5f" />

<img width="468" height="190" alt="image" src="https://github.com/user-attachments/assets/fe84d299-65ae-441b-8347-c91adb1bfc4c" />

# Author
Bibek Panth Cybersecurity Student B083’E’ Linkedln: www.linkedin.com/in/bibek-panth-033019274
