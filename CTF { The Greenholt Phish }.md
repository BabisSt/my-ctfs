#CTF 

A Sales Executive at Greenholt PLC received an email that he didn't expect to receive from a customer. He claims that the customer never uses generic greetings such as "Good day" and didn't expect any amount of money to be transferred to his account. The email also contains an attachment that he never requested. He forwarded the email to the SOC (Security Operations Center) department for further investigation.

Just searching into the challenge.eml file

- What date was the email received? (answer format: M/DD/YY) 
`6/10/20`

-  Who is the email from?
`Mr. James Jackson`

- What is his email address?
`info@mutawamarine.com`

- What email address will receive a reply to this email?
`info.mutawamarine@mail.com`

- What is the Originating IP?
`192.119.71.157`

- Who is the owner of the Originating IP? (Do not include the "." in your answer.)
`Hostwinds LLC` -> search in the internet

- What is the SPF record for the Return-Path domain?
`v=spf1 include:spf.protection.outlook.com -all`-> go to [**https://mxtoolbox.com/spf.aspx**](https://mxtoolbox.com/spf.aspx)
search with return path of Return-Path: <info@mutawamarine.com>

- What is the DMARC record for the Return-Path domain?
`v=DMARC1; p=quarantine; fo=1` -> go to [**https://mxtoolbox.com/dmarc.aspx**](https://mxtoolbox.com/dmarc.aspx)
search with the same return path

- What is the name of the attachment?
`SWT_#09674321____PDF__.CAB`

- What is the SHA256 hash of the file attachment?
`2e91c533615a9bb8929ac4bb76707b2444597ce063d84a4b33525e25074fff3f`-> Open challenge.eml with thunderbird from console. Download attachment.
use sha256sum on the attachment

- What is the attachments file size? (Don't forget to add "KB" to your answer, **NUM KB**)
`400.26 KB` -> go to virustotal and search with the sha256

- What is the actual file extension of the attachment?
`rar`