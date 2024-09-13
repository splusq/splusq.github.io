---
title: 'Using ASP.NET to authenticate against AD'
date: Sun, 18 Sep 2005 02:58:44 +0000
draft: false
tags: ['Uncategorized']
---

The company I work for reliies heavily on active-directory to organize, authenticate and integrate. In order to login to the intranet from our site, we currently request the integrated windows authentication box and ask the user to login. For some people this was a little flattering because it didn't really say why the login was taking place -- where are they loging into and so on. So to remedy this issue, I am working on a ASP.NET product that will authenticate against AD. This can be done using DirectoryServices provided within the .NET framework.

**Create secure connection to Active Directory**

```csharp
 public static DirectoryEntry GetDirectoryEntry() {
	DirectoryEntry de = new DirectoryEntry();
	de.Path = "LDAP://192.168.1.1/CN=Users;DC=Yourdomain";
	de.AuthenticationType = AuthenticationTypes.Secure;
	return de;
} 
```

**Set the culture and identity**

```csharp
 public static void SetCultureAndIdentity() {
	AppDomain.CurrentDomain.SetPrincipalPolicy(PrincipalPolicy.WindowsPrincipal);
	WindowsPrincipal principal = (WindowsPrincipal)Thread.CurrentPrincipal;
	WindowsIdentity identity = (WindowsIdentity)principal.Identity;
	System.Threading.Thread.CurrentThread.CurrentCulture = new CultureInfo("en-US");
} 
```

**Validate if user exists**

```csharp
 public bool UserExists(string UserName) {
	DirectoryEntry de = ADHelper.GetDirectoryEntry();
	DirectorySearcher deSearch = new DirectorySearcher();
	deSearch.SearchRoot =de;
	deSearch.Filter = "(&(objectClass=user) (cn=" + UserName +"))"; 
	SearchResultCollection results = deSearch.FindAll();
	if(results.Count == 0) {
		return false;
	}
	else {
		return true;
	}
} 
```

Code provided from [C-Sharp corner](http://www.c-sharpcorner.com/Code/2005/June/ADand.NET.asp).