Yep its PowerShell

Hunt for a description of passw in active directory user accounts.
```
function Invoke-Description {
    param(
        [string]$domain
    )

    $ldapPath = "LDAP://$domain"
    $searchBase = "DC=$($domain -replace '\.', ',DC=')"
    $filter = "(description=*passw*)"

    $directorySearcher = New-Object DirectoryServices.DirectorySearcher([ADSI]$ldapPath)
    $directorySearcher.Filter = $filter
    $directorySearcher.SearchRoot = [ADSI]"LDAP://$searchBase"

    $results = $directorySearcher.FindAll()

    $results | ForEach-Object { 
        Write-Host "User: $($_.Properties['sAMAccountName']) - Description: $($_.Properties['description'] -join ', ')"
    }
}

# Invoke-Description -d "hacklab.local"

```

Execution of the function.

```
PS C:\Users\g.white> Invoke-Description -d "hacklab.local"
User: Allowed RODC Password Replication Group - Description: Members in this group can have their passwords replicated to all read-only domain controllers in the domain
User: Denied RODC Password Replication Group - Description: Members in this group cannot have their passwords replicated to any read-only domain controllers in the domain
User: b.jackson - Description: Changed the users password to Iloveyou1
User: a.martinuez - Description: Test this up password
User: y.wilison - Description: Changed the users password to Morgan1
User: s.marttinez - Description: Changed the users password to Yellow1
User: u.thomhhas - Description: Changed the users password to Adidas1
User: d.atkinson - Description: User Password Summer123

PS C:\Users\g.white> 
PS C:\Users\g.white> 
PS C:\Users\g.white> Invoke-Description -d hacklab.local
User: Allowed RODC Password Replication Group - Description: Members in this group can have their passwords replicated to all read-only domain controllers in the domain
User: Denied RODC Password Replication Group - Description: Members in this group cannot have their passwords replicated to any read-only domain controllers in the domain
User: b.jackson - Description: Changed the users password to Iloveyou1
User: a.martinuez - Description: Test this up password
User: y.wilison - Description: Changed the users password to Morgan1
User: s.marttinez - Description: Changed the users password to Yellow1
User: u.thomhhas - Description: Changed the users password to Adidas1
User: d.atkinson - Description: User Password Summer123


```


Added keyword selection so you can define any word you want to hunt for.

```

function Invoke-Description {
    param(
        [string]$domain,
        [string]$keyword
    )

    $ldapPath = "LDAP://$domain"
    $searchBase = "DC=$($domain -replace '\.', ',DC=')"
    
    $filter = if ($keyword) { "(description=*${keyword}*)" } else { "(description=*passw*)" }

    $directorySearcher = New-Object DirectoryServices.DirectorySearcher([ADSI]$ldapPath)
    $directorySearcher.Filter = $filter
    $directorySearcher.SearchRoot = [ADSI]"LDAP://$searchBase"

    $results = $directorySearcher.FindAll()

    if ($results.Count -gt 0) {
        $results | ForEach-Object { 
            Write-Host "User: $($_.Properties['sAMAccountName']) - Description: $($_.Properties['description'] -join ', ')"
        }
    } else {
        Write-Host "No users found matching the keyword '$keyword' in their description."
    }
}

# Invoke-Description -d "hacklab.local" -k "admin"

```

Execution of the function.

```

PS C:\Users\g.white> Invoke-Description -d "hacklab.local" -k "golf"
User: b.wilswon - Description: I like to hit balls in golf on sunny day

PS C:\Users\g.white> 
PS C:\Users\g.white> 
PS C:\Users\g.white> Invoke-Description -d "hacklab.local" -k "passwo"
User: Allowed RODC Password Replication Group - Description: Members in this group can have their passwords replicated to all read-only domain controllers in the domain
User: Denied RODC Password Replication Group - Description: Members in this group cannot have their passwords replicated to any read-only domain controllers in the domain
User: b.jackson - Description: Changed the users password to Iloveyou1
User: a.martinuez - Description: Test this up password
User: y.wilison - Description: Changed the users password to Morgan1
User: s.marttinez - Description: Changed the users password to Yellow1
User: u.thomhhas - Description: Changed the users password to Adidas1
User: d.atkinson - Description: User Password Summer123

PS C:\Users\g.white> 
PS C:\Users\g.white> 
PS C:\Users\g.white> Invoke-Description -d "hacklab.local" -k "admin"
User: Administrator - Description: Built-in account for administering the computer/domain
User: Administrators - Description: Administrators have complete and unrestricted access to the computer/domain
User: Print Operators - Description: Members can administer printers installed on domain controllers
User: Network Configuration Operators - Description: Members in this group can have some administrative privileges to manage configuration of networking features
User: RDS Management Servers - Description: Servers in this group can perform routine administrative actions on servers running Remote Desktop Services. This group needs to be populated on al
l servers in a Remote Desktop Services deployment. The servers running the RDS Central Management service must be included in this group.
User: Schema Admins - Description: Designated administrators of the schema
User: Enterprise Admins - Description: Designated administrators of the enterprise
User: Domain Admins - Description: Designated administrators of the domain
User: Server Operators - Description: Members can administer domain servers
User: Account Operators - Description: Members can administer domain user and group accounts
User: Key Admins - Description: Members of this group can perform administrative actions on key objects within the domain.
User: Enterprise Key Admins - Description: Members of this group can perform administrative actions on key objects within the forest.
User: DnsAdmins - Description: DNS Administrators Group
User: WSS_RESTRICTED_WPG_V4 - Description: Group for the Microsoft SharePoint Foundation farm administrator. This group is required for the Microsoft SharePoint Foundation Administration Serv
ice to function.

PS C:\Users\g.white> 

```
