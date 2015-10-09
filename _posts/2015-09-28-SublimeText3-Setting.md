---
layout:     post
title:      SublimeText 3
date:       2015-09-28 17:51:18
summary:    SublimeText 3 setting
author:     Tneciv
categories: tools
thumbnail: rocket
tags:
 - SublimeText
 - setting
---



###Sublimetext个人设置

````json

{
	"color_scheme": "Packages/Theme - itg.flat/itg.Monokai.tmTheme",
	"default_encoding": "UTF-8",
	"fold_buttons": true,
	"font_size": 13,
	"highlight_line": true,
	"ignored_packages":
	[
		"Vintage"
	],
	"show_encoding": true,
	"sublimelinter": "save-only",
	"tab_size": 4,
	"theme": "Brogrammer.sublime-theme",
	"word_wrap": true,
	"wrap_width": 0
}

````

###按键绑定

````json

[
    {
        "keys": ["ctrl+shift+f"], "command": "js_format",
        "keys": ["ctrl+shift+f"], "command": "html_beautify",
        "context": [{"key": "selector", "operator": "equal", "operand": "source.js,source.json"}]
    }
]

````

###Sublime Text Build 3083 x64 Setup.exe
##激活码(全部粘贴)：

	----- BEGIN LICENSE -----
	Andrew Weber
	Single User License
	EA7E-855605
	813A03DD 5E4AD9E6 6C0EEB94 BC99798F
	942194A6 02396E98 E62C9979 4BB979FE
	91424C9D A45400BF F6747D88 2FB88078
	90F5CC94 1CDC92DC 8457107A F151657B
	1D22E383 A997F016 42397640 33F41CFC
	E1D0AE85 A0BBD039 0E9C8D55 E1B89D5D
	5CDB7036 E56DE1C0 EFCC0840 650CD3A6
	B98FC99C 8FAC73EE D2B95564 DF450523
	------ END LICENSE ------


简单的方法安装Package Control:

使用Ctrl+`快捷键或者通过View->Show Console菜单打开命令行，粘贴如下代码：

````

import urllib.request,os; pf = 'Package Control.sublime-package'; ipp = sublime.installed_packages_path(); urllib.request.install_opener( urllib.request.build_opener( urllib.request.ProxyHandler()) ); open(os.path.join(ipp, pf), 'wb').write(urllib.request.urlopen( 'http://sublime.wbond.net/' + pf.replace(' ','%20')).read())

````

###插件：
	EncodingHelper
	ConvertToUTF8
	Chinese​Localization 
	IMESupport
	HTML-CSS-JS Prettify

主题：
[brogrammer-theme](https://github.com/kenwheeler/brogrammer-theme "brogrammer-theme")

