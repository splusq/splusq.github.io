---
title: 'Date Format (using XSLT)'
date: Fri, 23 Jul 2004 22:31:59 +0000
draft: false
tags: ['Web Development']
---

It was sort of surprising to find out that XSLT does not have any predefined date formatting function. So after spending sometime on my own I came up with this solution:

```xslt
<xsl:template name="format-date">
	<xsl:param name="date" />

	<xsl:variable name="monthName" select="substring-before($date, '/')" />
	<xsl:variable name="day" select="substring-before(substring-after($date, '/'), '/')" />
	<xsl:variable name="year" select="substring-after(substring-after($date, '/'), '/')" />
	<xsl:variable name="month" select="substring(substring-after('01Jan02Feb03Mar04Apr05May06Jun07Jul08Aug09Sep10Oct11Nov12Dec', $monthName), 1, 3)" />
	
	<xsl:value-of select="concat($month, ', ', $year)" />
</xsl:template> 
```

You can call this function like this:

```xslt
<xsl:call-template name="format-date">
	<xsl:with-param name="date" select="@date" />
</xsl:call-template> 
```

Where `@date` is the date string (in this case the date attribute) you want to format. For instance this XSLT function rewrites dates written in this format: 06/23/2004 to. Jan. 2004. If you want to include the day in the resulting date, just modify the `xsl:value-of` concat line and concat the day also.