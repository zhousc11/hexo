---
title: nginx小技巧——直接提供文件下载
tags:
  - 小技巧
  - Linux
  - nginx
excerpt: >-
  >
  有的时候，我们需要去为他人提供一些文件下载的服务，或者比如我们使用租的vultr服务器下载完巨大的数据集后，需要将数据集传回本地，建立稳定连接的时候，都需要使用nginx提供简单的http下载服务。

  nginx默认的文档根目录是在/var/www/ht...
date: 2024-01-26 21:31:12
categories:
---

\> 有的时候，我们需要去为他人提供一些文件下载的服务，或者比如我们使用租的vultr服务器下载完巨大的数据集后，需要将数据集传回本地，建立稳定连接的时候，都需要使用nginx提供简单的http下载服务。 nginx默认的文档根目录是在/var/www/ht...
<!-- more -->
\> 有的时候，我们需要去为他人提供一些文件下载的服务，或者比如我们使用租的vultr服务器下载完巨大的数据集后，需要将数据集传回本地，建立稳定连接的时候，都需要使用nginx提供简单的http下载服务。

nginx默认的文档根目录是在`/var/www/html`中，我们在安装了nginx后，可以将文件放到这个默认文档目录，然后直接使用`http://<serveraddress>/<filename>`的方式去访问。