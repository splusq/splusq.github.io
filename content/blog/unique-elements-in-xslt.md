---
title: 'Unique Elements (in XSLT)'
date: Wed, 05 May 2004 23:58:32 +0000
draft: false
tags: ['Web Development']
---

At work I have this XML document that lists all the employees along with their responsiblities. The general format looks something like this (staff.xml):

```xml
 <ga-staff>
  <department title="Administration">

    <employee>
      <responsibility>Director, Corporate Accounting</responsibility> 
      <name>...</name> 
      <phone>...</phone> 
      <email>...</email> 
    </employee>
  
    <employee>
      <responsibility>Manager</responsibility> 
      <name>...</name> 
      <phone>...</phone> 
      <email>...</email> 
    </employee>
  
  </department>
  
  <department title="">
    ...
  </department>
</ga-staff> 
```

I was asked to generate a flat (meaning only their name, phone and e-mail) output (sorted by name) of all the staffs in our department. The only problem with using (existing) staff.xml is that there are certain employees who have multiple responsiblities, so their name appears more than once. Of course I cannot display their name more than once in the flat output. That makes no sense. The output would be something like this:

```bash
PersonA		1234	persona@finance.ucla.edu
PersonA		1234	persona@finance.ucla.edu 
```

To solve this problem I could have very well have used the Microsoft.DOMDocument, but I decided to use XSLT.

The first step was to generate an XML document (dynamically using XSLT) that would transform the staff.xml into an XML document that looked like this (sorted by name):

```xml
<ga-staff>
  <employee>
    <name> ... </name>
    <phone> ... </phone>
    <email> ... </phone>
  </employee>
</ga-staff> 
```

This is done using the following XSLT document (staff.xsl):

```xslt
<xsl:stylesheet xmlns:xsl="http://www.w3.org/1999/XSL/Transform" version="1.0">
<xsl:template match="/">
  <ga-staff>

    <xsl:for-each select="//employee">
      <xsl:sort select="name" order="ascending" />
        <employee>
          <name><xsl:value-of select="name" /></name>
          <phone><xsl:value-of select="phone" /></phone>
          <email><xsl:value-of select="email" /></email>
        </employee>
       </xsl:for-each>
  </ga-staff>
</xsl:template>
</xsl:stylesheet> 
```

Now that the data is sorted I can remove all the duplicate elements and generate a unique employee list. This is done using the following XSLT document (unique.xsl):

```xslt
<xsl:stylesheet xmlns:xsl="http://www.w3.org/1999/XSL/Transform" version="1.0" >
<xsl:template match="/">
  <ga-staff>
    <xsl:for-each select="//employee[not (name = preceding-sibling::employee/name)]">
      <xsl:if test="not(contains(name,'- Open -')) and not(contains(name,'/'))">
        <xsl:copy-of select="."/>
      </xsl:if>
    </xsl:for-each>	
  </ga-staff>
</xsl:template>
</xsl:stylesheet> 
```

That's it! Finally I used Microsoft.DOMDocument to apply these transformations to staff.xml. That is done using the following code:

```basic
Dim source, styles, unique, tmp, xdocument

' ** Load source XML Data
set source =   CreateObject("MSXML2.DOMDocument")
source.async = false
source.load(Server.MapPath("xml/staff.xml"))


' ** Load styles
set styles = CreateObject("MSXML2.DOMDocument")
styles.async = false
styles.load(Server.MapPath("xml/xslt/staff.xsl"))
	
set unique = CreateObject("MSXML2.DOMDocument")
unique.async = false
unique.load(Server.MapPath("xml/xslt/unique.xsl"))

' ** Resulting transformation
set tmp = CreateObject("MSXML2.DOMDocument")
tmp.async = false
tmp.validateOnParse = true

set xdocument = CreateObject("MSXML2.DOMDocument")
xdocument.async = false
xdocument.validateOnParse = true

source.transformNodeToObject styles, tmp
tmp.transFormNodeToObject unique, xdocument 
```