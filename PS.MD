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

