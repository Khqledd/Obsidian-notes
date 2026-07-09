<span style="color:rgb(0, 176, 240)">Email Anatomy</span>: `khaled@gmail.com`
- username: khaled
- domain: gmail.com
*Open .eml files in Thunderbird for better header analysis.*
--------------------------
In the email source page:
- **Content-Type** indicates the file type `application/pdf`
- **Content-Disposition** specifies that the file is an attachment and includes its filename
- **Content-Transfer-Encoding** shows that the file is `base64` encoded
---------------------------
https://wheregoes.com/ : Investigate shortened URLs

https://toolbox.googleapps.com/apps/messageheader/analyzeheader : Paste the full email header to extract key details for analysis

https://talosintelligence.com/reputation_center/ : asses IP / domains / networks / hash value of a file

https://www.convertcsv.com/url-extractor.htm : Extract URLs from email body

https://app.any.run/ : Safely execute files / URLs in a sandbox

--------------------

<span style="color:rgb(0, 176, 240)">To analyze .lnk files:</span> `lnkparse path/to/file.lnk`

<span style="color:rgb(0, 176, 240)">To filter JSON files</span>: `cat data.json | jq`
     ![[Pasted image 20260710015245.png]]