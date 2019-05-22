## 苹果CMS中player.js文件解密
> 源文件经过加密,实际是需要eval输出的JS代码.顺手放上来
另外苹果cms默认模板有个很恶心人的地方就是在播放页要访问官方地址的一个js文件,而文件又不存在.白白放慢加载时间.用这个Js文件替换就能解决.

- 2019.05.20 这个作者竟然在加密js中隐藏了广告植入,挨千刀

- 2019.05.20 有些群友反馈 

- 整理现有的，删掉了一个重复的（代码完全一样），另把代码全都标注V10 or V8

``` js
var killErrors=function(value){return true};window.onerror=null; 
```
这个变量声明的地方必须加.


> 这里找到了网上流传的几份js文件 分为群内共享版和网站来源版本

### 群内共享版

来源于QQ群 390381653

#### V10解密js 

路径地址:

``` js
var killErrors=function(value){return true};window.onerror=null;window.onerror=killErrors;
var base64EncodeChars = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/";
var base64DecodeChars = new Array(-1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, 62, -1, -1, -1, 63, 52, 53, 54, 55, 56, 57, 58, 59, 60, 61, -1, -1, -1, -1, -1, -1, -1, 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, -1, -1, -1, -1, -1, -1, 26, 27, 28, 29, 30, 31, 32, 33, 34, 35, 36, 37, 38, 39, 40, 41, 42, 43, 44, 45, 46, 47, 48, 49, 50, 51, -1, -1, -1, -1, -1);

function base64encode(str) {
	var out, i, len;
	var c1, c2, c3;
	len = str.length;
	i = 0;
	out = "";
	while (i < len) {
		c1 = str.charCodeAt(i++) & 0xff;
		if (i == len) {
			out += base64EncodeChars.charAt(c1 >> 2);
			out += base64EncodeChars.charAt((c1 & 0x3) << 4);
			out += "==";
			break
		}
		c2 = str.charCodeAt(i++);
		if (i == len) {
			out += base64EncodeChars.charAt(c1 >> 2);
			out += base64EncodeChars.charAt(((c1 & 0x3) << 4) | ((c2 & 0xF0) >> 4));
			out += base64EncodeChars.charAt((c2 & 0xF) << 2);
			out += "=";
			break
		}
		c3 = str.charCodeAt(i++);
		out += base64EncodeChars.charAt(c1 >> 2);
		out += base64EncodeChars.charAt(((c1 & 0x3) << 4) | ((c2 & 0xF0) >> 4));
		out += base64EncodeChars.charAt(((c2 & 0xF) << 2) | ((c3 & 0xC0) >> 6));
		out += base64EncodeChars.charAt(c3 & 0x3F)
	}
	return out
}
function base64decode(str) {
	var c1, c2, c3, c4;
	var i, len, out;
	len = str.length;
	i = 0;
	out = "";
	while (i < len) {
		do {
			c1 = base64DecodeChars[str.charCodeAt(i++) & 0xff]
		} while (i < len && c1 == -1);
		if (c1 == -1) break;
		do {
			c2 = base64DecodeChars[str.charCodeAt(i++) & 0xff]
		} while (i < len && c2 == -1);
		if (c2 == -1) break;
		out += String.fromCharCode((c1 << 2) | ((c2 & 0x30) >> 4));
		do {
			c3 = str.charCodeAt(i++) & 0xff;
			if (c3 == 61) return out;
			c3 = base64DecodeChars[c3]
		} while (i < len && c3 == -1);
		if (c3 == -1) break;
		out += String.fromCharCode(((c2 & 0XF) << 4) | ((c3 & 0x3C) >> 2));
		do {
			c4 = str.charCodeAt(i++) & 0xff;
			if (c4 == 61) return out;
			c4 = base64DecodeChars[c4]
		} while (i < len && c4 == -1);
		if (c4 == -1) break;
		out += String.fromCharCode(((c3 & 0x03) << 6) | c4)
	}
	return out
}
function utf16to8(str) {
	var out, i, len, c;
	out = "";
	len = str.length;
	for (i = 0; i < len; i++) {
		c = str.charCodeAt(i);
		if ((c >= 0x0001) && (c <= 0x007F)) {
			out += str.charAt(i)
		} else if (c > 0x07FF) {
			out += String.fromCharCode(0xE0 | ((c >> 12) & 0x0F));
			out += String.fromCharCode(0x80 | ((c >> 6) & 0x3F));
			out += String.fromCharCode(0x80 | ((c >> 0) & 0x3F))
		} else {
			out += String.fromCharCode(0xC0 | ((c >> 6) & 0x1F));
			out += String.fromCharCode(0x80 | ((c >> 0) & 0x3F))
		}
	}
	return out
}
function utf8to16(str) {
	var out, i, len, c;
	var char2, char3;
	out = "";
	len = str.length;
	i = 0;
	while (i < len) {
		c = str.charCodeAt(i++);
		switch (c >> 4) {
		case 0:
		case 1:
		case 2:
		case 3:
		case 4:
		case 5:
		case 6:
		case 7:
			out += str.charAt(i - 1);
			break;
		case 12:
		case 13:
			char2 = str.charCodeAt(i++);
			out += String.fromCharCode(((c & 0x1F) << 6) | (char2 & 0x3F));
			break;
		case 14:
			char2 = str.charCodeAt(i++);
			char3 = str.charCodeAt(i++);
			out += String.fromCharCode(((c & 0x0F) << 12) | ((char2 & 0x3F) << 6) | ((char3 & 0x3F) << 0));
			break
		}
	}
	return out
}
var MacPlayer = {
	'GetUrl': function(s, n) {
		return this.Link.replace('{sid}', s).replace('{sid}', s).replace('{nid}', n).replace('{nid}', n)
	},
	'Go': function(s, n) {
		location.href = this.GetUrl(s, n)
	},
	'Show': function() {
		$('#buffer').attr('src', this.Prestrain);
		setTimeout(function() {
			MacPlayer.AdsEnd()
		}, this.Second * 1000);
		$("#playleft").get(0).innerHTML = this.Html + '';
//		var a = document.createElement('script');
//		a.type = 'text/javascript';
//		a.async = true;
//		a.charset = 'utf-8';
//		a.src = '//union.maccms.com/html/top10.js';
//		var b = document.getElementsByTagName('script')[0];
//		b.parentNode.insertBefore(a, b)
	},
	'AdsStart': function() {
		if ($("#buffer").attr('src') != this.Buffer) {
			$("#buffer").attr('src', this.Buffer)
		}
		$("#buffer").show()
	},
	'AdsEnd': function() {
		$('#buffer').hide()
	},
	'Install': function() {
		this.Status = false;
		$('#install').show()
	},
	'Play': function() {
		document.write('<style>.MacPlayer{background: #000000;font-size:14px;color:#F6F6F6;margin:0px;padding:0px;position:relative;overflow:hidden;width:' + this.Width + ';height:' + this.Height + ';min-height:100px;}.MacPlayer table{width:100%;height:100%;}.MacPlayer #playleft{position:inherit;!important;width:100%;height:100%;}</style><div class="MacPlayer">' + '<iframe id="buffer" src="" frameBorder="0" scrolling="no" width="100%" height="100%" style="position:absolute;z-index:99998;"></iframe><iframe id="install" src="" frameBorder="0" scrolling="no" width="100%" height="100%" style="position:absolute;z-index:99998;display:none;"></iframe>' + '<table border="0" cellpadding="0" cellspacing="0"><tr><td id="playleft" valign="top" style="">&nbsp;</td></table></div>');
		this.offsetHeight = $('.MacPlayer').get(0).offsetHeight;
		this.offsetWidth = $('.MacPlayer').get(0).offsetWidth;
		document.write('<scr' + 'ipt src="' + this.Path + this.PlayFrom + '.js"></scr' + 'ipt>')
	},
	'Down': function() {},
	'Init': function() {
		this.Status = true;
		this.Parse = '';
		if (player_data.encrypt == '1') {
			player_data.url = unescape(player_data.url);
			player_data.url_next = unescape(player_data.url_next)
		} else if (player_data.encrypt == '2') {
			player_data.url = unescape(base64decode(player_data.url));
			player_data.url_next = unescape(base64decode(player_data.url_next))
		}
		this.Agent = navigator.userAgent.toLowerCase();
		this.Width = MacPlayerConfig.width;
		this.Height = MacPlayerConfig.height;
		if (this.Agent.indexOf("android") > 0 || this.Agent.indexOf("mobile") > 0 || this.Agent.indexOf("ipod") > 0 || this.Agent.indexOf("ios") > 0 || this.Agent.indexOf("iphone") > 0 || this.Agent.indexOf("ipad") > 0) {
			this.Width = MacPlayerConfig.widthmob;
			this.Height = MacPlayerConfig.heightmob
		}
		if (this.Width.indexOf("px") == -1 && this.Width.indexOf("%") == -1) {
			this.Width = '100%'
		}
		if (this.Height.indexOf("px") == -1 && this.Height.indexOf("%") == -1) {
			this.Height = '100%'
		}
		this.Prestrain = MacPlayerConfig.prestrain;
		this.Buffer = MacPlayerConfig.buffer;
		this.Second = MacPlayerConfig.second;
		this.Flag = player_data.flag;
		this.Trysee = player_data.trysee;
		this.Points = player_data.points;
		this.Link = decodeURIComponent(player_data.link);
		this.PlayFrom = player_data.from;
		this.PlayNote = player_data.note;
		this.PlayServer = player_data.server == 'no' ? '' : player_data.server;
		this.PlayUrl = player_data.url;
		this.PlayUrlNext = player_data.url_next;
		this.PlayLinkNext = player_data.link_next;
		this.PlayLinkPre = player_data.link_pre;
		if (MacPlayerConfig.server_list[this.PlayServer] != undefined) {
			this.PlayServer = MacPlayerConfig.server_list[this.PlayServer].des
		}
		if (MacPlayerConfig.player_list[this.PlayFrom] != undefined) {
			if (MacPlayerConfig.player_list[this.PlayFrom].ps == "1") {
				this.Parse = MacPlayerConfig.player_list[this.PlayFrom].parse == '' ? MacPlayerConfig.parse : MacPlayerConfig.player_list[this.PlayFrom].parse;
				this.PlayFrom = 'parse'
			}
		}
		this.Path = maccms.path + '/static/player/';
		if (this.Flag == "down") {
			MacPlayer.Down()
		} else {
			MacPlayer.Play()
		}
	}
};
MacPlayer.Init();

```


#### 群友提供的另一方v10 解密js
``` js
var killErrors = function(value) {
    return true
};
window.onerror = null;
window.onerror = killErrors;
var base64EncodeChars = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/";
var base64DecodeChars = new Array( - 1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, 62, -1, -1, -1, 63, 52, 53, 54, 55, 56, 57, 58, 59, 60, 61, -1, -1, -1, -1, -1, -1, -1, 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, -1, -1, -1, -1, -1, -1, 26, 27, 28, 29, 30, 31, 32, 33, 34, 35, 36, 37, 38, 39, 40, 41, 42, 43, 44, 45, 46, 47, 48, 49, 50, 51, -1, -1, -1, -1, -1);
function base64encode(str) {
    var out, i, len;
    var c1, c2, c3;
    len = str.length;
    i = 0;
    out = "";
    while (i < len) {
        c1 = str.charCodeAt(i++) & 0xff;
        if (i == len) {
            out += base64EncodeChars.charAt(c1 >> 2);
            out += base64EncodeChars.charAt((c1 & 0x3) << 4);
            out += "==";
            break
        }
        c2 = str.charCodeAt(i++);
        if (i == len) {
            out += base64EncodeChars.charAt(c1 >> 2);
            out += base64EncodeChars.charAt(((c1 & 0x3) << 4) | ((c2 & 0xF0) >> 4));
            out += base64EncodeChars.charAt((c2 & 0xF) << 2);
            out += "=";
            break
        }
        c3 = str.charCodeAt(i++);
        out += base64EncodeChars.charAt(c1 >> 2);
        out += base64EncodeChars.charAt(((c1 & 0x3) << 4) | ((c2 & 0xF0) >> 4));
        out += base64EncodeChars.charAt(((c2 & 0xF) << 2) | ((c3 & 0xC0) >> 6));
        out += base64EncodeChars.charAt(c3 & 0x3F)
    }
    return out
}
function base64decode(str) {
    var c1, c2, c3, c4;
    var i, len, out;
    len = str.length;
    i = 0;
    out = "";
    while (i < len) {
        do {
            c1 = base64DecodeChars[str.charCodeAt(i++) & 0xff]
        } while ( i < len && c1 == - 1 );
        if (c1 == -1) break;
        do {
            c2 = base64DecodeChars[str.charCodeAt(i++) & 0xff]
        } while ( i < len && c2 == - 1 );
        if (c2 == -1) break;
        out += String.fromCharCode((c1 << 2) | ((c2 & 0x30) >> 4));
        do {
            c3 = str.charCodeAt(i++) & 0xff;
            if (c3 == 61) return out;
            c3 = base64DecodeChars[c3]
        } while ( i < len && c3 == - 1 );
        if (c3 == -1) break;
        out += String.fromCharCode(((c2 & 0XF) << 4) | ((c3 & 0x3C) >> 2));
        do {
            c4 = str.charCodeAt(i++) & 0xff;
            if (c4 == 61) return out;
            c4 = base64DecodeChars[c4]
        } while ( i < len && c4 == - 1 );
        if (c4 == -1) break;
        out += String.fromCharCode(((c3 & 0x03) << 6) | c4)
    }
    return out
}
function utf16to8(str) {
    var out, i, len, c;
    out = "";
    len = str.length;
    for (i = 0; i < len; i++) {
        c = str.charCodeAt(i);
        if ((c >= 0x0001) && (c <= 0x007F)) {
            out += str.charAt(i)
        } else if (c > 0x07FF) {
            out += String.fromCharCode(0xE0 | ((c >> 12) & 0x0F));
            out += String.fromCharCode(0x80 | ((c >> 6) & 0x3F));
            out += String.fromCharCode(0x80 | ((c >> 0) & 0x3F))
        } else {
            out += String.fromCharCode(0xC0 | ((c >> 6) & 0x1F));
            out += String.fromCharCode(0x80 | ((c >> 0) & 0x3F))
        }
    }
    return out
}
function utf8to16(str) {
    var out, i, len, c;
    var char2, char3;
    out = "";
    len = str.length;
    i = 0;
    while (i < len) {
        c = str.charCodeAt(i++);
        switch (c >> 4) {
        case 0:
        case 1:
        case 2:
        case 3:
        case 4:
        case 5:
        case 6:
        case 7:
            out += str.charAt(i - 1);
            break;
        case 12:
        case 13:
            char2 = str.charCodeAt(i++);
            out += String.fromCharCode(((c & 0x1F) << 6) | (char2 & 0x3F));
            break;
        case 14:
            char2 = str.charCodeAt(i++);
            char3 = str.charCodeAt(i++);
            out += String.fromCharCode(((c & 0x0F) << 12) | ((char2 & 0x3F) << 6) | ((char3 & 0x3F) << 0));
            break
        }
    }
    return out
}
var MacPlayer = {
    'GetUrl': function(s, n) {
        return this.Link.replace('{sid}', s).replace('{sid}', s).replace('{nid}', n).replace('{nid}', n)
    },
    'Go': function(s, n) {
        location.href = this.GetUrl(s, n)
    },
    'Show': function() {
        $('#buffer').attr('src', this.Prestrain);
        setTimeout(function() {
            MacPlayer.AdsEnd()
        },
        this.Second * 1000);
        $("#playleft").get(0).innerHTML = this.Html + '';
        var a = document.createElement('script');
        a.type = 'text/javascript';
        a.async = true;
        a.charset = 'utf-8';
        a.src = '';
        var b = document.getElementsByTagName('script')[0];
        b.parentNode.insertBefore(a, b)
    },
    'AdsStart': function() {
        if ($("#buffer").attr('src') != this.Buffer) {
            $("#buffer").attr('src', this.Buffer)
        }
        $("#buffer").show()
    },
    'AdsEnd': function() {
        $('#buffer').hide()
    },
    'Install': function() {
        this.Status = false;
        $('#install').show()
    },
    'Play': function() {
        document.write('<style>.MacPlayer{background: #000000;font-size:14px;color:#F6F6F6;margin:0px;padding:0px;position:relative;overflow:hidden;width:' + this.Width + ';height:' + this.Height + ';min-height:100px;}.MacPlayer table{width:100%;height:100%;}.MacPlayer #playleft{position:inherit;!important;width:100%;height:100%;}</style><div class="MacPlayer">' + '<iframe id="buffer" src="" frameBorder="0" scrolling="no" width="100%" height="100%" style="position:absolute;z-index:99998;"></iframe><iframe id="install" src="" frameBorder="0" scrolling="no" width="100%" height="100%" style="position:absolute;z-index:99998;display:none;"></iframe>' + '<table border="0" cellpadding="0" cellspacing="0"><tr><td id="playleft" valign="top" style="">&nbsp;</td></table></div>');
        this.offsetHeight = $('.MacPlayer').get(0).offsetHeight;
        this.offsetWidth = $('.MacPlayer').get(0).offsetWidth;
        document.write('<scr' + 'ipt src="' + this.Path + this.PlayFrom + '.js"></scr' + 'ipt>')
    },
    'Down': function() {},
    'Init': function() {
        this.Status = true;
        this.Parse = '';
        if (player_data.encrypt == '1') {
            player_data.url = unescape(player_data.url);
            player_data.url_next = unescape(player_data.url_next)
        } else if (player_data.encrypt == '2') {
            player_data.url = unescape(base64decode(player_data.url));
            player_data.url_next = unescape(base64decode(player_data.url_next))
        }
        this.Agent = navigator.userAgent.toLowerCase();
        this.Width = MacPlayerConfig.width;
        this.Height = MacPlayerConfig.height;
        if (this.Agent.indexOf("android") > 0 || this.Agent.indexOf("mobile") > 0 || this.Agent.indexOf("ipod") > 0 || this.Agent.indexOf("ios") > 0 || this.Agent.indexOf("iphone") > 0 || this.Agent.indexOf("ipad") > 0) {
            this.Width = MacPlayerConfig.widthmob;
            this.Height = MacPlayerConfig.heightmob
        }
        if (this.Width.indexOf("px") == -1 && this.Width.indexOf("%") == -1) {
            this.Width = '100%'
        }
        if (this.Height.indexOf("px") == -1 && this.Height.indexOf("%") == -1) {
            this.Height = '100%'
        }
        this.Prestrain = MacPlayerConfig.prestrain;
        this.Buffer = MacPlayerConfig.buffer;
        this.Second = MacPlayerConfig.second;
        this.Flag = player_data.flag;
        this.Trysee = player_data.trysee;
        this.Points = player_data.points;
        this.Link = decodeURIComponent(player_data.link);
        this.PlayFrom = player_data.from;
        this.PlayNote = player_data.note;
        this.PlayServer = player_data.server == 'no' ? '': player_data.server;
        this.PlayUrl = player_data.url;
        this.PlayUrlNext = player_data.url_next;
        this.PlayLinkNext = player_data.link_next;
        this.PlayLinkPre = player_data.link_pre;
        if (MacPlayerConfig.server_list[this.PlayServer] != undefined) {
            this.PlayServer = MacPlayerConfig.server_list[this.PlayServer].des
        }
        if (MacPlayerConfig.player_list[this.PlayFrom] != undefined) {
            if (MacPlayerConfig.player_list[this.PlayFrom].ps == "1") {
                this.Parse = MacPlayerConfig.player_list[this.PlayFrom].parse == '' ? MacPlayerConfig.parse: MacPlayerConfig.player_list[this.PlayFrom].parse;
                this.PlayFrom = 'parse'
            }
        }
        this.Path = maccms.path + '/static/player/';
        if (this.Flag == "down") {
            MacPlayer.Down()
        } else {
            MacPlayer.Play()
        }
    }
};
MacPlayer.Init();

```

#### V8解密js 

路径地址:

``` js
var killErrors=function(value){return true};window.onerror=null;window.onerror=killErrors;
var base64EncodeChars="ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/";var base64DecodeChars=new Array(-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,62,-1,-1,-1,63,52,53,54,55,56,57,58,59,60,61,-1,-1,-1,-1,-1,-1,-1,0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,-1,-1,-1,-1,-1,-1,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,-1,-1,-1,-1,-1);function base64encode(str){var out,i,len;var c1,c2,c3;len=str.length;i=0;out="";while(i<len){c1=str.charCodeAt(i++)&0xff;if(i==len){out+=base64EncodeChars.charAt(c1>>2);out+=base64EncodeChars.charAt((c1&0x3)<<4);out+="==";break}c2=str.charCodeAt(i++);if(i==len){out+=base64EncodeChars.charAt(c1>>2);out+=base64EncodeChars.charAt(((c1&0x3)<<4)|((c2&0xF0)>>4));out+=base64EncodeChars.charAt((c2&0xF)<<2);out+="=";break}c3=str.charCodeAt(i++);out+=base64EncodeChars.charAt(c1>>2);out+=base64EncodeChars.charAt(((c1&0x3)<<4)|((c2&0xF0)>>4));out+=base64EncodeChars.charAt(((c2&0xF)<<2)|((c3&0xC0)>>6));out+=base64EncodeChars.charAt(c3&0x3F)}return out}function base64decode(str){var c1,c2,c3,c4;var i,len,out;len=str.length;i=0;out="";while(i<len){do{c1=base64DecodeChars[str.charCodeAt(i++)&0xff]}while(i<len&&c1==-1);if(c1==-1)break;do{c2=base64DecodeChars[str.charCodeAt(i++)&0xff]}while(i<len&&c2==-1);if(c2==-1)break;out+=String.fromCharCode((c1<<2)|((c2&0x30)>>4));do{c3=str.charCodeAt(i++)&0xff;if(c3==61)return out;c3=base64DecodeChars[c3]}while(i<len&&c3==-1);if(c3==-1)break;out+=String.fromCharCode(((c2&0XF)<<4)|((c3&0x3C)>>2));do{c4=str.charCodeAt(i++)&0xff;if(c4==61)return out;c4=base64DecodeChars[c4]}while(i<len&&c4==-1);if(c4==-1)break;out+=String.fromCharCode(((c3&0x03)<<6)|c4)}return out}function utf16to8(str){var out,i,len,c;out="";len=str.length;for(i=0;i<len;i++){c=str.charCodeAt(i);if((c>=0x0001)&&(c<=0x007F)){out+=str.charAt(i)}else if(c>0x07FF){out+=String.fromCharCode(0xE0|((c>>12)&0x0F));out+=String.fromCharCode(0x80|((c>>6)&0x3F));out+=String.fromCharCode(0x80|((c>>0)&0x3F))}else{out+=String.fromCharCode(0xC0|((c>>6)&0x1F));out+=String.fromCharCode(0x80|((c>>0)&0x3F))}}return out}function utf8to16(str){var out,i,len,c;var char2,char3;out="";len=str.length;i=0;while(i<len){c=str.charCodeAt(i++);switch(c>>4){case 0:case 1:case 2:case 3:case 4:case 5:case 6:case 7:out+=str.charAt(i-1);break;case 12:case 13:char2=str.charCodeAt(i++);out+=String.fromCharCode(((c&0x1F)<<6)|(char2&0x3F));break;case 14:char2=str.charCodeAt(i++);char3=str.charCodeAt(i++);out+=String.fromCharCode(((c&0x0F)<<12)|((char2&0x3F)<<6)|((char3&0x3F)<<0));break}}return out}

window.onresize = function() {
    if (window.name == "macopen1") {
        MacPlayer.Width = $(window).width() - $(".MacPlayer").offset().left - 15;
        MacPlayer.HeightAll = $(window).height() - $(".MacPlayer").offset().top - 15;
        MacPlayer.Height = MacPlayer.HeightAll;
        if (mac_showtop == 1) {
            MacPlayer.Height -= 20
        }
        $(".MacPlayer").width(MacPlayer.Width);
        $(".MacPlayer").height(MacPlayer.HeightAll);
        $("#buffer").width(MacPlayer.Width);
        $("#buffer").height(MacPlayer.HeightAll);
        $("#Player").width(MacPlayer.Width);
        $("#Player").height(MacPlayer.Height)
    }
}
;
var MacPlayer = {
    'GoPreUrl': function() {
        if (this.Num > 0) {
            this.Go(this.Src + 1, this.Num)
        }
    },
    'GetPreUrl': function() {
        return this.Num > 0 ? this.GetUrl(this.Src + 1, this.Num) : ''
    },
    'GoNextUrl': function() {
        if (this.Num + 1 != this.PlayUrlLen) {
            this.Go(this.Src + 1, this.Num + 2)
        }
    },
    'GetNextUrl': function() {
        return this.Num + 1 <= this.PlayUrlLen ? this.GetUrl(this.Src + 1, this.Num + 2) : ''
    },
    'GetUrl': function(s, n) {
        return mac_link.replace('{src}', s).replace('{src}', s).replace('{num}', n).replace('{num}', n)
    },
    'Go': function(s, n) {
        location.href = this.GetUrl(s, n)
    },
    'GetList': function() {
        this.RightList = '';
        for (i = 0; i < this.Data.from.length; i++) {
            from = this.Data.from[i];
            url = this.Data.url[i];
            listr = "";
            sid_on = 'h2';
            sub_on = 'none';
            urlarr = url.split('#');
            for (j = 0; j < urlarr.length; j++) {
                urlinfo = urlarr[j].split('$');
                name = '';
                url = '';
                list_on = '';
                from1 = '';
                if (urlinfo.length > 1) {
                    name = urlinfo[0];
                    url = urlinfo[1];
                    if (urlinfo.length > 2) {
                        from1 = urlinfo[2]
                    }
                } else {
                    name = "第" + (j + 1) + "集";
                    url = urlinfo[0]
                }
                if (this.Src == i && this.Num == j) {
                    sid_on = 'h2_on';
                    sub_on = 'block';
                    list_on = "list_on";
                    this.PlayUrlLen = urlarr.length;
                    this.PlayUrl = url;
                    this.PlayName = name;
                    if (from1 != '') {
                        this.PlayFrom = from1
                    }
                    if (j < urlarr.length - 1) {
                        urlinfo = urlarr[j + 1].split('$');
                        if (urlinfo.length > 1) {
                            name1 = urlinfo[0];
                            url1 = urlinfo[1]
                        } else {
                            name1 = "第" + (j + 1) + "集";
                            url1 = urlinfo[0]
                        }
                        this.PlayUrl1 = url1;
                        this.PalyName1 = name1
                    }
                }
                listr += '<li><a class="' + list_on + '" href="javascript:void(0)" onclick="MacPlayer.Go(' + (i + 1) + ',' + (j + 1) + ');return false;" >' + name + '</a></li>'
            }
            this.RightList += '<div id="main' + i + '" class="' + sid_on + '"><h2 onclick="MacPlayer.Tabs(' + i + ',' + (this.Data.from.length - 1) + ')">' + mac_show[from] + '</h2>' + '<ul id="sub' + i + '" style="display:' + sub_on + '">' + listr + '</ul></div>'
        }
    },
    'ShowList': function() {
        $('#playright').toggle()
    },
    'Tabs': function(a, n) {
        var b = $('#sub' + a).css('display');
        for (var i = 0; i <= n; i++) {
            $('#main' + i).attr('className', 'h2');
            $('#sub' + i).hide()
        }
        if (b == 'none') {
            $('#sub' + a).show();
            $('#main' + a).attr('className', 'h2_on')
        } else {
            $('#sub' + a).hide()
        }
    },
    'Show': function() {
        if (mac_showtop == 0) {
            $("#playtop").hide()
        }
        if (mac_showlist == 0) {
            $("#playright").hide()
        }
        setTimeout(function() {
            MacPlayer.AdsEnd()
        }, this.Second * 1000);
        $("#topdes").get(0).innerHTML = '' + '正在播放：' + this.PlayName + '';
        $("#playright").get(0).innerHTML = '<div class="rightlist" id="rightlist" style="height:' + this.Height + 'px;">' + this.RightList + '</div>';
        $("#playleft").get(0).innerHTML = '<iframe id="buffer" src="' + this.Prestrain + '" frameBorder="0" scrolling="no" width="100%" height="' + this.Height + '" style="position:absolute;z-index:99998;"></iframe>' + this.Html + '';
    },
    'ShowBuffer': function() {
        var w = this.Width - 100;
        var h = this.Height - 100;
        var l = (this.Width - w) / 2;
        var t = (this.Height - h) / 2 + 20;
        $(".MacBuffer").css({
            'width': w,
            'height': h,
            'left': l,
            'top': t
        });
        $(".MacBuffer").toggle()
    },
    'AdsEnd': function() {
        $('#buffer').hide()
    },
    'Install': function() {
        this.Status = false;
        $('#install').parent().show();
        $('#install').show()
    },
    'Play': function() {
        var a = mac_colors.split(',');
        document.write('<style>.MacPlayer{background: #' + a[0] + ';font-size:14px;color:#' + a[1] + ';margin:0px;padding:0px;position:relative;overflow:hidden;width:' + this.Width + 'px;height:' + this.HeightAll + 'px;}.MacPlayer a{color:#' + a[2] + ';text-decoration:none}a:hover{text-decoration: underline;}.MacPlayer a:active{text-decoration: none;}.MacPlayer table{width:100%;height:100%;}.MacPlayer ul,li,h2{ margin:0px; padding:0px; list-style:none}.MacPlayer #playtop{text-align:center;height:20px; line-height:21px;font-size:12px;}.MacPlayer #topleft{width:150px;}.MacPlayer #topright{width:100px;} .MacPlayer #topleft{text-align:left;padding-left:5px}.MacPlayer #topright{text-align:right;padding-right:5px}.MacPlayer #playleft{width:100%;height:100%;overflow:hidden;}.MacPlayer #playright{height:100%;overflow-y:auto;}.MacPlayer #rightlist{width:120px;overflow:auto;scrollbar-face-color:#' + a[7] + ';scrollbar-arrow-color:#' + a[8] + ';scrollbar-track-color: #' + a[9] + ';scrollbar-highlight-color:#' + a[10] + ';scrollbar-shadow-color: #' + a[11] + ';scrollbar-3dlight-color:#' + a[12] + ';scrollbar-darkshadow-color:#' + a[13] + ';scrollbar-base-color:#' + a[14] + ';}.MacPlayer #rightlist ul{ clear:both; margin:5px 0px}.MacPlayer #rightlist li{ height:21px; line-height:21px;overflow: hidden; text-overflow: ellipsis; white-space: nowrap;}.MacPlayer #rightlist li a{padding-left:15px; display:block; font-size:12px}.MacPlayer #rightlist h2{ cursor:pointer;font-size:13px;font-family: "宋体";font-weight:normal;height:25px;line-height:25px;background:#' + a[3] + ';padding-left:5px; margin-bottom:1px}.MacPlayer #rightlist .h2{color:#' + a[4] + '}.MacPlayer #rightlist .h2_on{color:#' + a[5] + '}.MacPlayer #rightlist .ul_on{display:block}.MacPlayer #rightlist .list_on{color:#' + a[6] + '} </style><div class="MacPlayer"><table border="0" cellpadding="0" cellspacing="0"><tr><td colspan="2"><table border="0" cellpadding="0" cellspacing="0" id="playtop"><tr><td width="100" id="topleft"><a target="_self" href="javascript:void(0)" onclick="MacPlayer.GoPreUrl();return false;">上一集</a> <a target="_self" href="javascript:void(0)" onclick="MacPlayer.GoNextUrl();return false;">下一集</a></td><td id="topcc"><div id="topdes" style="height:26px;line-height:26px;overflow:hidden"></div></td><td width="100" id="topright"><a target="_self" href="javascript:void(0)" onClick="MacPlayer.ShowList();return false;">开/关列表</a></td></tr></table></td></tr><tr style="display:none"><td colspan="2" id="install" style="display:none"></td></tr><tr><td id="playleft" valign="top">&nbsp;</td><td id="playright" valign="top">&nbsp;</td></tr></table></div>');
        document.write('<scr' + 'ipt src="' + this.Path + this.PlayFrom + '.js"></scr' + 'ipt>')
    },
    'Down': function() {},
    'Init': function() {
        this.Status = true;
        this.Url = location.href;
        this.Par = location.search;
        this.Data = {
            'from': mac_from.split('$$$'),
            'server': mac_server.split('$$$'),
            'note': mac_note.split('$$$'),
            'url': mac_url.split('$$$')
        };
        this.Width = window.name == 'macopen1' ? mac_widthpop : mac_width;
        this.HeightAll = window.name == 'macopen1' ? mac_heightpop : mac_height;
        this.Height = this.HeightAll;
        if (mac_showtop == 1) {
            this.Height -= 20
        }
        if (this.Url.indexOf('#') > -1) {
            this.Url = this.Url.substr(0, this.Url.indexOf('#'))
        }
        this.Prestrain = mac_prestrain;
        this.Buffer = mac_buffer;
        this.Second = mac_second;
        this.Flag = mac_flag;
        var a = this.Url.match(/\d+.*/g)[0].match(/\d+/g);
        var b = a.length;
        this.Id = a[(b - 3)] * 1;
        this.Src = a[(b - 2)] * 1 - 1;
        this.Num = a[(b - 1)] * 1 - 1;
        this.PlayFrom = this.Data.from[this.Src];
        this.PlayServer = this.Data.server[this.Src] == 'no' ? '' : mac_show_server[this.Data.server[this.Src]];
        this.PlayNote = this.Data.note[this.Src];
        this.GetList();
        this.NextUrl = this.GetNextUrl();
        this.PreUrl = this.GetPreUrl();
        this.Path = SitePath + 'player/';
        if (this.Flag == "down") {
            MacPlayer.Down()
        } else {
            MacPlayer.Play()
        }
    }
};

MacPlayer.Init();
```

### 网站流传版  V8版本

来源网站:https://leoi.info/blog/43.html#cmt656

``` js
var killErrors=function(value){return true};window.onerror=null;window.onerror=killErrors;
var base64EncodeChars="ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/";var base64DecodeChars=new Array(-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,62,-1,-1,-1,63,52,53,54,55,56,57,58,59,60,61,-1,-1,-1,-1,-1,-1,-1,0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,-1,-1,-1,-1,-1,-1,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,-1,-1,-1,-1,-1);function base64encode(str){var out,i,len;var c1,c2,c3;len=str.length;i=0;out="";while(i<len){c1=str.charCodeAt(i++)&0xff;if(i==len){out+=base64EncodeChars.charAt(c1>>2);out+=base64EncodeChars.charAt((c1&0x3)<<4);out+="==";break}c2=str.charCodeAt(i++);if(i==len){out+=base64EncodeChars.charAt(c1>>2);out+=base64EncodeChars.charAt(((c1&0x3)<<4)|((c2&0xF0)>>4));out+=base64EncodeChars.charAt((c2&0xF)<<2);out+="=";break}c3=str.charCodeAt(i++);out+=base64EncodeChars.charAt(c1>>2);out+=base64EncodeChars.charAt(((c1&0x3)<<4)|((c2&0xF0)>>4));out+=base64EncodeChars.charAt(((c2&0xF)<<2)|((c3&0xC0)>>6));out+=base64EncodeChars.charAt(c3&0x3F)}return out}function base64decode(str){var c1,c2,c3,c4;var i,len,out;len=str.length;i=0;out="";while(i<len){do{c1=base64DecodeChars[str.charCodeAt(i++)&0xff]}while(i<len&&c1==-1);if(c1==-1)break;do{c2=base64DecodeChars[str.charCodeAt(i++)&0xff]}while(i<len&&c2==-1);if(c2==-1)break;out+=String.fromCharCode((c1<<2)|((c2&0x30)>>4));do{c3=str.charCodeAt(i++)&0xff;if(c3==61)return out;c3=base64DecodeChars[c3]}while(i<len&&c3==-1);if(c3==-1)break;out+=String.fromCharCode(((c2&0XF)<<4)|((c3&0x3C)>>2));do{c4=str.charCodeAt(i++)&0xff;if(c4==61)return out;c4=base64DecodeChars[c4]}while(i<len&&c4==-1);if(c4==-1)break;out+=String.fromCharCode(((c3&0x03)<<6)|c4)}return out}function utf16to8(str){var out,i,len,c;out="";len=str.length;for(i=0;i<len;i++){c=str.charCodeAt(i);if((c>=0x0001)&&(c<=0x007F)){out+=str.charAt(i)}else if(c>0x07FF){out+=String.fromCharCode(0xE0|((c>>12)&0x0F));out+=String.fromCharCode(0x80|((c>>6)&0x3F));out+=String.fromCharCode(0x80|((c>>0)&0x3F))}else{out+=String.fromCharCode(0xC0|((c>>6)&0x1F));out+=String.fromCharCode(0x80|((c>>0)&0x3F))}}return out}function utf8to16(str){var out,i,len,c;var char2,char3;out="";len=str.length;i=0;while(i<len){c=str.charCodeAt(i++);switch(c>>4){case 0:case 1:case 2:case 3:case 4:case 5:case 6:case 7:out+=str.charAt(i-1);break;case 12:case 13:char2=str.charCodeAt(i++);out+=String.fromCharCode(((c&0x1F)<<6)|(char2&0x3F));break;case 14:char2=str.charCodeAt(i++);char3=str.charCodeAt(i++);out+=String.fromCharCode(((c&0x0F)<<12)|((char2&0x3F)<<6)|((char3&0x3F)<<0));break}}return out}

window.onresize = function() {
    if (window.name == "macopen1") {
        MacPlayer.Width = $(window).width() - $(".MacPlayer").offset().left - 15;
        MacPlayer.HeightAll = $(window).height() - $(".MacPlayer").offset().top - 15;
        MacPlayer.Height = MacPlayer.HeightAll;
        if (mac_showtop == 1) {
            MacPlayer.Height -= 20
        }
        $(".MacPlayer").width(MacPlayer.Width);
        $(".MacPlayer").height(MacPlayer.HeightAll);
        $("#buffer").width(MacPlayer.Width);
        $("#buffer").height(MacPlayer.HeightAll);
        $("#Player").width(MacPlayer.Width);
        $("#Player").height(MacPlayer.Height)
    }
}
;
var MacPlayer = {
    'GoPreUrl': function() {
        if (this.Num > 0) {
            this.Go(this.Src + 1, this.Num)
        }
    },
    'GetPreUrl': function() {
        return this.Num > 0 ? this.GetUrl(this.Src + 1, this.Num) : ''
    },
    'GoNextUrl': function() {
        if (this.Num + 1 != this.PlayUrlLen) {
            this.Go(this.Src + 1, this.Num + 2)
        }
    },
    'GetNextUrl': function() {
        return this.Num + 1 <= this.PlayUrlLen ? this.GetUrl(this.Src + 1, this.Num + 2) : ''
    },
    'GetUrl': function(s, n) {
        return mac_link.replace('{src}', s).replace('{src}', s).replace('{num}', n).replace('{num}', n)
    },
    'Go': function(s, n) {
        location.href = this.GetUrl(s, n)
    },
    'GetList': function() {
        this.RightList = '';
        for (i = 0; i < this.Data.from.length; i++) {
            from = this.Data.from[i];
            url = this.Data.url[i];
            listr = "";
            sid_on = 'h2';
            sub_on = 'none';
            urlarr = url.split('#');
            for (j = 0; j < urlarr.length; j++) {
                urlinfo = urlarr[j].split('$');
                name = '';
                url = '';
                list_on = '';
                from1 = '';
                if (urlinfo.length > 1) {
                    name = urlinfo[0];
                    url = urlinfo[1];
                    if (urlinfo.length > 2) {
                        from1 = urlinfo[2]
                    }
                } else {
                    name = "第" + (j + 1) + "集";
                    url = urlinfo[0]
                }
                if (this.Src == i && this.Num == j) {
                    sid_on = 'h2_on';
                    sub_on = 'block';
                    list_on = "list_on";
                    this.PlayUrlLen = urlarr.length;
                    this.PlayUrl = url;
                    this.PlayName = name;
                    if (from1 != '') {
                        this.PlayFrom = from1
                    }
                    if (j < urlarr.length - 1) {
                        urlinfo = urlarr[j + 1].split('$');
                        if (urlinfo.length > 1) {
                            name1 = urlinfo[0];
                            url1 = urlinfo[1]
                        } else {
                            name1 = "第" + (j + 1) + "集";
                            url1 = urlinfo[0]
                        }
                        this.PlayUrl1 = url1;
                        this.PalyName1 = name1
                    }
                }
                listr += '<li><a class="' + list_on + '" href="javascript:void(0)" onclick="MacPlayer.Go(' + (i + 1) + ',' + (j + 1) + ');return false;" >' + name + '</a></li>'
            }
            this.RightList += '<div id="main' + i + '" class="' + sid_on + '"><h2 onclick="MacPlayer.Tabs(' + i + ',' + (this.Data.from.length - 1) + ')">' + mac_show[from] + '</h2>' + '<ul id="sub' + i + '" style="display:' + sub_on + '">' + listr + '</ul></div>'
        }
    },
    'ShowList': function() {
        $('#playright').toggle()
    },
    'Tabs': function(a, n) {
        var b = $('#sub' + a).css('display');
        for (var i = 0; i <= n; i++) {
            $('#main' + i).attr('className', 'h2');
            $('#sub' + i).hide()
        }
        if (b == 'none') {
            $('#sub' + a).show();
            $('#main' + a).attr('className', 'h2_on')
        } else {
            $('#sub' + a).hide()
        }
    },
    'Show': function() {
        if (mac_showtop == 0) {
            $("#playtop").hide()
        }
        if (mac_showlist == 0) {
            $("#playright").hide()
        }
        setTimeout(function() {
            MacPlayer.AdsEnd()
        }, this.Second * 1000);
        $("#topdes").get(0).innerHTML = '' + '正在播放：' + this.PlayName + '';
        $("#playright").get(0).innerHTML = '<div class="rightlist" id="rightlist" style="height:' + this.Height + 'px;">' + this.RightList + '</div>';
        $("#playleft").get(0).innerHTML = '<iframe id="buffer" src="' + this.Prestrain + '" frameBorder="0" scrolling="no" width="100%" height="' + this.Height + '" style="position:absolute;z-index:99998;"></iframe>' + this.Html + '';
    },
    'ShowBuffer': function() {
        var w = this.Width - 100;
        var h = this.Height - 100;
        var l = (this.Width - w) / 2;
        var t = (this.Height - h) / 2 + 20;
        $(".MacBuffer").css({
            'width': w,
            'height': h,
            'left': l,
            'top': t
        });
        $(".MacBuffer").toggle()
    },
    'AdsEnd': function() {
        $('#buffer').hide()
    },
    'Install': function() {
        this.Status = false;
        $('#install').parent().show();
        $('#install').show()
    },
    'Play': function() {
        var a = mac_colors.split(',');
        document.write('<style>.MacPlayer{background: #' + a[0] + ';font-size:14px;color:#' + a[1] + ';margin:0px;padding:0px;position:relative;overflow:hidden;width:' + this.Width + 'px;height:' + this.HeightAll + 'px;}.MacPlayer a{color:#' + a[2] + ';text-decoration:none}a:hover{text-decoration: underline;}.MacPlayer a:active{text-decoration: none;}.MacPlayer table{width:100%;height:100%;}.MacPlayer ul,li,h2{ margin:0px; padding:0px; list-style:none}.MacPlayer #playtop{text-align:center;height:20px; line-height:21px;font-size:12px;}.MacPlayer #topleft{width:150px;}.MacPlayer #topright{width:100px;} .MacPlayer #topleft{text-align:left;padding-left:5px}.MacPlayer #topright{text-align:right;padding-right:5px}.MacPlayer #playleft{width:100%;height:100%;overflow:hidden;}.MacPlayer #playright{height:100%;overflow-y:auto;}.MacPlayer #rightlist{width:120px;overflow:auto;scrollbar-face-color:#' + a[7] + ';scrollbar-arrow-color:#' + a[8] + ';scrollbar-track-color: #' + a[9] + ';scrollbar-highlight-color:#' + a[10] + ';scrollbar-shadow-color: #' + a[11] + ';scrollbar-3dlight-color:#' + a[12] + ';scrollbar-darkshadow-color:#' + a[13] + ';scrollbar-base-color:#' + a[14] + ';}.MacPlayer #rightlist ul{ clear:both; margin:5px 0px}.MacPlayer #rightlist li{ height:21px; line-height:21px;overflow: hidden; text-overflow: ellipsis; white-space: nowrap;}.MacPlayer #rightlist li a{padding-left:15px; display:block; font-size:12px}.MacPlayer #rightlist h2{ cursor:pointer;font-size:13px;font-family: "宋体";font-weight:normal;height:25px;line-height:25px;background:#' + a[3] + ';padding-left:5px; margin-bottom:1px}.MacPlayer #rightlist .h2{color:#' + a[4] + '}.MacPlayer #rightlist .h2_on{color:#' + a[5] + '}.MacPlayer #rightlist .ul_on{display:block}.MacPlayer #rightlist .list_on{color:#' + a[6] + '} </style><div class="MacPlayer"><table border="0" cellpadding="0" cellspacing="0"><tr><td colspan="2"><table border="0" cellpadding="0" cellspacing="0" id="playtop"><tr><td width="100" id="topleft"><a target="_self" href="javascript:void(0)" onclick="MacPlayer.GoPreUrl();return false;">上一集</a> <a target="_self" href="javascript:void(0)" onclick="MacPlayer.GoNextUrl();return false;">下一集</a></td><td id="topcc"><div id="topdes" style="height:26px;line-height:26px;overflow:hidden"></div></td><td width="100" id="topright"><a target="_self" href="javascript:void(0)" onClick="MacPlayer.ShowList();return false;">开/关列表</a></td></tr></table></td></tr><tr style="display:none"><td colspan="2" id="install" style="display:none"></td></tr><tr><td id="playleft" valign="top">&nbsp;</td><td id="playright" valign="top">&nbsp;</td></tr></table></div>');
        document.write('<scr' + 'ipt src="' + this.Path + this.PlayFrom + '.js"></scr' + 'ipt>')
    },
    'Down': function() {},
    'Init': function() {
        this.Status = true;
        this.Url = location.href;
        this.Par = location.search;
        this.Data = {
            'from': mac_from.split('$$$'),
            'server': mac_server.split('$$$'),
            'note': mac_note.split('$$$'),
            'url': mac_url.split('$$$')
        };
        this.Width = window.name == 'macopen1' ? mac_widthpop : mac_width;
        this.HeightAll = window.name == 'macopen1' ? mac_heightpop : mac_height;
        this.Height = this.HeightAll;
        if (mac_showtop == 1) {
            this.Height -= 20
        }
        if (this.Url.indexOf('#') > -1) {
            this.Url = this.Url.substr(0, this.Url.indexOf('#'))
        }
        this.Prestrain = mac_prestrain;
        this.Buffer = mac_buffer;
        this.Second = mac_second;
        this.Flag = mac_flag;
        var a = this.Url.match(/\d+.*/g)[0].match(/\d+/g);
        var b = a.length;
        this.Id = a[(b - 3)] * 1;
        this.Src = a[(b - 2)] * 1 - 1;
        this.Num = a[(b - 1)] * 1 - 1;
        this.PlayFrom = this.Data.from[this.Src];
        this.PlayServer = this.Data.server[this.Src] == 'no' ? '' : mac_show_server[this.Data.server[this.Src]];
        this.PlayNote = this.Data.note[this.Src];
        this.GetList();
        this.NextUrl = this.GetNextUrl();
        this.PreUrl = this.GetPreUrl();
        this.Path = SitePath + 'player/';
        if (this.Flag == "down") {
            MacPlayer.Down()
        } else {
            MacPlayer.Play()
        }
    }
};

MacPlayer.Init();
//应该是v8版本
```


