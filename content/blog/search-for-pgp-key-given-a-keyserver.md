---
title: 'Search for PGP key given a keyserver'
date: Tue, 20 Jul 2004 17:37:24 +0000
draft: false
tags: ['Security']
---

In Linux just do this:

```bash
gpg --keyserver "ldap://certserver.pgp.com" --search-keys "<e-mail address>"
```

Note the option: --keyserver can be other Keyservers like the [MIT Key Server](http://pgp.mit.edu/) (pgp.mit.edu).