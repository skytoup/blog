---
title: pip install pycurl 小问题
hugo.Data: 2018-08-31
date: 2018-08-31
draft: false
tags:
categories:
  - python
---

`pip install pycurl`后, 使用`pycurl`时, 出现了些错误:

		PycURL: ImportError: pycurl: libcurl link-time ssl backend (nss) is different from compile-time ssl
		
解决方法:

	pip uninstall pycurl
	export PYCURL_SSL_LIBRARY=nss # 错误提示nss, 所有使用nss, 所有选项 openssl, gnutls, nss
	pip install pycurl
	
