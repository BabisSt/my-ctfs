#CTF 

_Boss wanted me to create a prototype, so here it is! We even used something called "version control" that made deploying this really easy!_

## Recon

```
PORT   STATE SERVICE VERSION
80/tcp open  http    nginx 1.14.0 (Ubuntu)
|_http-server-header: nginx/1.14.0 (Ubuntu)
|_http-title: Super Awesome Site!
| http-git: 
|   10.10.250.206:80/.git/
|     Git repository found!
|_    Repository description: Unnamed repository; edit this file 'description' to name the...
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

From Debugger of firefox dev tools I find this

```
        const _0x4368=['+(\x20+[^','471197','value','RegExp','functi','test','CbRnH','passwo','userna','TML','tml','a865c5','+[^\x20]}','a5f298','cookie','admin','3a71fd','getEle','login-','^([^\x20]','TEhxP','href','f64cb3','51a151','d84319','D\x20USER','digest','R\x20PASS','oard.h','error','\x20]+)+)','19a3c0','f80f67','/dashb','bea070','3ec9cb','padSta','from','4004c2','WORD!','map','NAME\x20O','encode','INVALI','a5106e','baf89f','6a7c7c','elemen','9a88db','log','join','innerH','SaltyB','apply','ned','442a9d','mentBy'];(function(_0x1ef2d8,_0x436806){const _0x2c2818=function(_0x302738){while(--_0x302738){_0x1ef2d8['push'](_0x1ef2d8['shift']());}},_0x6f8b4a=function(){const _0x2e9681={'data':{'key':'cookie','value':'timeout'},'setCookie':function(_0x329b53,_0x28dc3d,_0x22f4a3,_0x6012c1){_0x6012c1=_0x6012c1||{};let _0x3d8f23=_0x28dc3d+'='+_0x22f4a3,_0x18026e=0x0;for(let _0x4175c9=0x0,_0x25d1be=_0x329b53['length'];_0x4175c9<_0x25d1be;_0x4175c9++){const _0x109e81=_0x329b53[_0x4175c9];_0x3d8f23+=';\x20'+_0x109e81;const _0x1e9a27=_0x329b53[_0x109e81];_0x329b53['push'](_0x1e9a27),_0x25d1be=_0x329b53['length'],_0x1e9a27!==!![]&&(_0x3d8f23+='='+_0x1e9a27);}_0x6012c1['cookie']=_0x3d8f23;},'removeCookie':function(){return'dev';},'getCookie':function(_0x3e797a,_0x2a5b7d){_0x3e797a=_0x3e797a||function(_0x242cdf){return _0x242cdf;};const _0x996bc1=_0x3e797a(new RegExp('(?:^|;\x20)'+_0x2a5b7d['replace'](/([.$?*|{}()[]\/+^])/g,'$1')+'=([^;]*)')),_0x51d0ee=function(_0x439650,_0x52fa41){_0x439650(++_0x52fa41);};return _0x51d0ee(_0x2c2818,_0x436806),_0x996bc1?decodeURIComponent(_0x996bc1[0x1]):undefined;}},_0x17997b=function(){const _0x383e88=new RegExp('\x5cw+\x20*\x5c(\x5c)\x20*{\x5cw+\x20*[\x27|\x22].+[\x27|\x22];?\x20*}');return _0x383e88['test'](_0x2e9681['removeCookie']['toString']());};_0x2e9681['updateCookie']=_0x17997b;let _0x39ee22='';const _0xad377=_0x2e9681['updateCookie']();if(!_0xad377)_0x2e9681['setCookie'](['*'],'counter',0x1);else _0xad377?_0x39ee22=_0x2e9681['getCookie'](null,'counter'):_0x2e9681['removeCookie']();};_0x6f8b4a();}(_0x4368,0xe6));const _0x2c28=function(_0x1ef2d8,_0x436806){_0x1ef2d8=_0x1ef2d8-0x0;let _0x2c2818=_0x4368[_0x1ef2d8];return _0x2c2818;};const _0x22f4a3=function(){let _0x36b504=!![];return function(_0x1087c7,_0x108f32){if(_0x2c28('0x4')===_0x2c28('0x4')){const _0x52d1da=_0x36b504?function(){if(_0x2c28('0x12')!==_0x2c28('0x12')){function _0x382a78(){document[_0x2c28('0xf')+_0x2c28('0x36')+'Id'](_0x2c28('0x1b'))['innerH'+_0x2c28('0x7')]=_0x2c28('0x29')+_0x2c28('0x17')+'NAME\x20O'+_0x2c28('0x19')+_0x2c28('0x25');}}else{if(_0x108f32){const _0x725292=_0x108f32[_0x2c28('0x33')](_0x1087c7,arguments);return _0x108f32=null,_0x725292;}}}:function(){};return _0x36b504=![],_0x52d1da;}else{function _0x323170(){const _0x2ed5f9=_0x36b504?function(){if(_0x108f32){const _0x407994=_0x108f32[_0x2c28('0x33')](_0x1087c7,arguments);return _0x108f32=null,_0x407994;}}:function(){};return _0x36b504=![],_0x2ed5f9;}}};}(),_0x28dc3d=_0x22f4a3(this,function(){const _0x5b8de6=typeof window!=='undefi'+_0x2c28('0x34')?window:typeof process==='object'&&typeof require===_0x2c28('0x2')+'on'&&typeof global==='object'?global:this,_0x4d9f75=function(){const _0x1eee2f=new _0x5b8de6[(_0x2c28('0x1'))](_0x2c28('0x11')+_0x2c28('0x37')+_0x2c28('0x1c')+_0x2c28('0xa'));return!_0x1eee2f[_0x2c28('0x3')](_0x28dc3d);};return _0x4d9f75();});_0x28dc3d();async function login(){let _0x110afb=document[_0x2c28('0xf')+_0x2c28('0x36')+'Id'](_0x2c28('0x10')+'form');console[_0x2c28('0x2f')](_0x110afb[_0x2c28('0x2d')+'ts']);let _0x383cb8=_0x110afb[_0x2c28('0x2d')+'ts'][_0x2c28('0x6')+'me'][_0x2c28('0x0')],_0x5b6063=await digest(_0x110afb[_0x2c28('0x2d')+'ts'][_0x2c28('0x5')+'rd'][_0x2c28('0x0')]);_0x383cb8===_0x2c28('0xd')&&_0x5b6063===_0x2c28('0x24')+_0x2c28('0xe')+'6ba9b0'+_0x2c28('0x21')+'7eed08'+_0x2c28('0x38')+_0x2c28('0x16')+_0x2c28('0x9')+_0x2c28('0x35')+_0x2c28('0x2c')+_0x2c28('0x20')+'f3cb6a'+_0x2c28('0x2a')+_0x2c28('0x1e')+_0x2c28('0x2e')+_0x2c28('0x2b')+_0x2c28('0x14')+_0x2c28('0x15')+_0x2c28('0xb')+_0x2c28('0x1d')+'94eceb'+'bb'?(document[_0x2c28('0xc')]='login='+'1',window['locati'+'on'][_0x2c28('0x13')]=_0x2c28('0x1f')+_0x2c28('0x1a')+_0x2c28('0x8')):document['getEle'+_0x2c28('0x36')+'Id'](_0x2c28('0x1b'))[_0x2c28('0x31')+_0x2c28('0x7')]=_0x2c28('0x29')+_0x2c28('0x17')+_0x2c28('0x27')+_0x2c28('0x19')+_0x2c28('0x25');}async function digest(_0x35521d){const _0x179c00=new TextEncoder(),_0x713734=_0x179c00[_0x2c28('0x28')](_0x35521d+(_0x2c28('0x32')+'ob')),_0x39b76f=await crypto['subtle'][_0x2c28('0x18')]('SHA-51'+'2',_0x713734),_0x558ac0=Array[_0x2c28('0x23')](new Uint8Array(_0x39b76f)),_0x34e00e=_0x558ac0[_0x2c28('0x26')](_0x468ec7=>_0x468ec7['toStri'+'ng'](0x10)[_0x2c28('0x22')+'rt'](0x2,'0'))[_0x2c28('0x30')]('');return _0x34e00e;}
```

On /.git I get this

```
[../](http://10.10.250.206/)
[branches/](http://10.10.250.206/.git/branches/)  23-Jul-2020 22:39              -
[hooks/](http://10.10.250.206/.git/hooks/)   23-Jul-2020 22:39                   -
[info/](http://10.10.250.206/.git/info/)     23-Jul-2020 22:39                   -
[logs/](http://10.10.250.206/.git/logs/)     23-Jul-2020 22:39                   -
[objects/](http://10.10.250.206/.git/objects/)     23-Jul-2020 22:39                   -
[refs/](http://10.10.250.206/.git/refs/)      23-Jul-2020 22:39                   -
[HEAD](http://10.10.250.206/.git/HEAD)        23-Jul-2020 22:39                  23
[config](http://10.10.250.206/.git/config)    24-Jul-2020 06:25                 110
[description](http://10.10.250.206/.git/description)  23-Jul-2020 22:39         73
[index](http://10.10.250.206/.git/index)      23-Jul-2020 22:39                 645
[packed-refs](http://10.10.250.206/.git/packed-refs)  24-Jul-2020 06:25                 102
```

Under `# /.git/logs/` I find HEAD that contains this information
```
0000000000000000000000000000000000000000 d0b3578a628889f38c0affb1b75457146a4678e5 root <root@ubuntu.(none)> 1595543975 +0200	clone: from https://hydragyrum:kMhJnM42EHdTN7MXNWeD@gitlab.com/cfe-atc/seccom/git-fail.git
```

I notice these creds hydragyrum:kMhJnM42EHdTN7MXNWeD

I try to login with these creds but I cant. So next I try to clone the project on my machine.

First I download GitTools
`git clone https://github.com/internetwache/GitTools.git/`
and the I cd into `/GitTools/Dumper` and execute
`./gitdumper.sh http://10.10.51.76/.git/ clone`, and the project is downloaded.

In the folder I type git status and I get this
```
        deleted:    .gitlab-ci.yml
        deleted:    Dockerfile
        deleted:    README.md
        deleted:    css/style.css
        deleted:    dashboard.html
        deleted:    default.conf
        deleted:    index.html
```

Also with git log I get
```
commit d0b3578a628889f38c0affb1b75457146a4678e5 (HEAD -> master, tag: v1.0)
Author: Adam Bertrand <hydragyrum@gmail.com>
Date:   Thu Jul 23 22:22:16 2020 +0000

    Update .gitlab-ci.yml

commit 77aab78e2624ec9400f9ed3f43a6f0c942eeb82d
Author: Hydragyrum <hydragyrum@gmail.com>
Date:   Fri Jul 24 00:21:25 2020 +0200

    add gitlab-ci config to build docker file.

commit 2eb93ac3534155069a8ef59cb25b9c1971d5d199
Author: Hydragyrum <hydragyrum@gmail.com>
Date:   Fri Jul 24 00:08:38 2020 +0200

    setup dockerfile and setup defaults.

commit d6df4000639981d032f628af2b4d03b8eff31213
Author: Hydragyrum <hydragyrum@gmail.com>
:...skipping...
commit d0b3578a628889f38c0affb1b75457146a4678e5 (HEAD -> master, tag: v1.0)                                                                                                                                                                 
Author: Adam Bertrand <hydragyrum@gmail.com>                                                                                                                                                                                                
Date:   Thu Jul 23 22:22:16 2020 +0000                                                                                                                                          
    Update .gitlab-ci.yml      

commit 77aab78e2624ec9400f9ed3f43a6f0c942eeb82d                                                                                                                                                                                             
Author: Hydragyrum <hydragyrum@gmail.com>                                                                                                                                                                                                   
Date:   Fri Jul 24 00:21:25 2020 +0200                                                  

    add gitlab-ci config to build docker file.                                          
    
commit 2eb93ac3534155069a8ef59cb25b9c1971d5d199                                                                                                                                                                                             
Author: Hydragyrum <hydragyrum@gmail.com>                                                                                                                                                                                                   
Date:   Fri Jul 24 00:08:38 2020 +0200      

    setup dockerfile and setup defaults.    
                                                
commit d6df4000639981d032f628af2b4d03b8eff31213                                                                                                                                                                                             
Author: Hydragyrum <hydragyrum@gmail.com>                                                                                                                                                                                                   
Date:   Thu Jul 23 23:42:30 2020 +0200                                                  

    Make sure the css is standard-ish!                                                  
    
commit d954a99b96ff11c37a558a5d93ce52d0f3702a7d                                                                                                                                                                                             
Author: Hydragyrum <hydragyrum@gmail.com>                                                                                                                                                                                                   
Date:   Thu Jul 23 23:41:12 2020 +0200                                                                                                                                          
    re-obfuscating the code to be really secure!                                                                                                                                
commit bc8054d9d95854d278359a432b6d97c27e24061d                                                                                                                                                                                             
Author: Hydragyrum <hydragyrum@gmail.com>                                                                                                                                                                                                   
Date:   Thu Jul 23 23:37:32 2020 +0200

    Security says obfuscation isn't enough.
    
    They want me to use something called 'SHA-512'

commit e56eaa8e29b589976f33d76bc58a0c4dfb9315b1
Author: Hydragyrum <hydragyrum@gmail.com>
Date:   Thu Jul 23 23:25:52 2020 +0200

    Obfuscated the source code.
    
    Hopefully security will be happy!

commit 395e087334d613d5e423cdf8f7be27196a360459
Author: Hydragyrum <hydragyrum@gmail.com>
Date:   Thu Jul 23 23:17:43 2020 +0200

    Made the login page, boss!

commit 2f423697bf81fe5956684f66fb6fc6596a1903cc
Author: Adam Bertrand <hydragyrum@gmail.com>
Date:   Mon Jul 20 20:46:28 2020 +0000

    Initial commit
```


I take a look the the commits and on the 395e087334d613d5e423cdf8f7be27196a360459
I get the login page. Scrolling down I stable upon this
```
+    <script>
+      function login() {
+        let form = document.getElementById("login-form");
+        console.log(form.elements);
+        let username = form.elements["username"].value;
+        let password = form.elements["password"].value;
+        if (
+          username === "admin" &&
+          password === "Th1s_1s_4_L0ng_4nd_S3cur3_P4ssw0rd!"
+        ) {
+          document.cookie = "login=1";
+          window.location.href = "/dashboard.html";
+        } else {
+          document.getElementById("error").innerHTML =
+            "INVALID USERNAME OR PASSWORD!";
+        }
+      }
+    </script>
```

Which is the obfuscated script I found earlier on the browser, and it has hardcoded the creds
And we are done!
`Th1s_1s_4_L0ng_4nd_S3cur3_P4ssw0rd!`