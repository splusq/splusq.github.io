---
title: 'Option Groups'
date: Fri, 25 Feb 2005 01:50:47 +0000
draft: false
tags: ['Uncategorized']
---

Not many people know about this. In a HTML dropdown (select) there is a option group tag that groups your options together. For instance:

```html
<select name="cars">
	<optgroup label="Honda">
		<option>Honda Accord DX</option>
		<option>Honda Accord LX</option>
		<option>Honda Accord EX</option>
	</optgroup>
	
	<optgroup label="BMW">
		<option>BMW 530iL</option>
		<option>BMW 640iL</option>
		<option>BMW 740i</option>
	</optgroup>
</select> 
```

If you [run the code above](/sources/optiongroup.php) you will [notice](/sources/optiongroup.php) users can't even select the option group (which is perfectly valid). And the automatic indendation within the sub-groups is very useful also. This feature is supported by Opera and IE browsers. The actual version details you will have to find out.