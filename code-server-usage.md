# 非官方code-server安装及使用指南
# 安装篇
+ 正常情况下推荐命令行输入`curl -fsSL https://code-server.dev/install.sh | sh`无脑一键安装
+ 特殊情况参考官网指南: [https://coder.com/docs/code-server/latest/install](https://coder.com/docs/code-server/latest/install)
# 使用篇
+ 使用caddy反代与nginx共存, 畅享http3
  + 安装caddy(参考官网教程)
  + 修改caddy配置文件`Caddyfile` (默认保存在`\etc\caddy`)
```

{
        admin off
        auto_https off
        servers {
                protocol {
                        experimental_http3 //开启http3
                }
        }
}
https://domain:444 { //444为我们将要走http3的端口, 可自定义. 防火墙注意放开444端口的TCP和UDP
        tls /path/to/your/cert /path/to/your/key //你的证书路径, 参考官网对Caddyfile的说明(//及后面的内容删掉, 下同)
        // 以下参考code-server的官方说明
        reverse_proxy 127.0.0.1:8100 { 
                header_up X-Forwarded-For {remote_host}
                header_up Host {upstream_hostport}
                header_up X-Real-IP {remote}
        }
}

```
  + nginx按常规(官网有教程)配置好反代后(浏览器或者网络环境不支持http3就走443端口的nginx, websocket也走nginx)
    + 在配置文件里的server项加上`add_header Alt-Svc 'h3=":444"; ma=86400,h3-29=":444"; ma=86400';`, 444就是前面设置的端口, 如:
```
......
server
{
	listen 443 ssl http2;
	add_header Alt-Svc 'h3=":444"; ma=86400,h3-29=":444"; ma=86400';
    server_name your.domain;
    index index.php index.html index.htm default.php default.htm default.html;
    root /www/wwwroot/your.domain;
    ......

}
......
```
   + 到这里就OK啦, http3确实比http2快一点
 + code-server使用VSCode的插件市场(open-vsx.org太慢了而且缺少一些插件)
   + 打开目录`/usr/lib/code-server/lib/vscode`
   + 修改`product.json`, 在添加下面的内容(记得根据json格式要求补上逗号)
 ```
"extensionsGallery": {
      "serviceUrl": "https://marketplace.visualstudio.com/_apis/public/gallery",
      "cacheUrl": "https://vscode.blob.core.windows.net/gallery/index",
      "itemUrl": "https://marketplace.visualstudio.com/items"
}
 ```
  + 配置完后长这样, 仅供参考
 ```json
 {
  "nameShort": "code-server",
  "nameLong": "code-server",
  "applicationName": "code-server",
  "dataFolderName": ".code-server",
  "win32MutexName": "codeserver",
  "licenseName": "MIT",
  "licenseUrl": "https://github.com/coder/code-server/blob/main/LICENSE",
  "serverGreeting": [],
  "serverLicense": [],
  "serverLicensePrompt": "",
  "serverApplicationName": "code-server-oss",
  "serverDataFolderName": ".vscode-server-oss",
  "win32DirName": "code-server",
  "win32NameVersion": "code-server",
  "win32RegValueName": "CodeOSS",
  "win32AppId": "{{E34003BB-9E10-4501-8C11-BE3FAA83F23F}",
  "win32x64AppId": "{{D77B7E06-80BA-4137-BCF4-654B95CCEBC5}",
  "win32arm64AppId": "{{D1ACE434-89C5-48D1-88D3-E2991DF85475}",
  "win32UserAppId": "{{C6065F05-9603-4FC4-8101-B9781A25D88E}",
  "win32x64UserAppId": "{{CC6B787D-37A0-49E8-AE24-8559A032BE0C}",
  "win32arm64UserAppId": "{{3AEBF0C8-F733-4AD4-BADE-FDB816D53D7B}",
  "win32AppUserModelId": "coder.code-server",
  "win32ShellNameShort": "c&ode-server",
  "darwinBundleIdentifier": "com.coder.code.server",
  "linuxIconName": "com.coder.code.server",
  "licenseFileName": "LICENSE.txt",
  "reportIssueUrl": "https://github.com/coder/code-server/issues/new",
  "urlProtocol": "code-oss",
  "webviewContentExternalBaseUrlTemplate": "https://{{uuid}}.vscode-webview.net/insider/93a2a2fa12dd3ae0629eec01c05a28cb60ac1c4b/out/vs/workbench/contrib/webview/browser/pre/",
  "extensionAllowedProposedApi": [
    "ms-vscode.vscode-js-profile-flame",
    "ms-vscode.vscode-js-profile-table",
    "GitHub.remotehub",
    "GitHub.remotehub-insiders"
  ],
  "extensionsGallery": {
      "serviceUrl": "https://marketplace.visualstudio.com/_apis/public/gallery",
      "cacheUrl": "https://vscode.blob.core.windows.net/gallery/index",
      "itemUrl": "https://marketplace.visualstudio.com/items"
  },
  "builtInExtensions": [
    {
      "name": "ms-vscode.references-view",
      "version": "0.0.85",
      "repo": "https://github.com/microsoft/vscode-references-view",
      "metadata": {
        "id": "dc489f46-520d-4556-ae85-1f9eab3c412d",
        "publisherId": {
          "publisherId": "5f5636e7-69ed-4afe-b5d6-8d231fb3d3ee",
          "publisherName": "ms-vscode",
          "displayName": "Microsoft",
          "flags": "verified"
        },
        "publisherDisplayName": "Microsoft"
      }
    },
    {
      "name": "ms-vscode.js-debug-companion",
      "version": "1.0.15",
      "repo": "https://github.com/microsoft/vscode-js-debug-companion",
      "metadata": {
        "id": "99cb0b7f-7354-4278-b8da-6cc79972169d",
        "publisherId": {
          "publisherId": "5f5636e7-69ed-4afe-b5d6-8d231fb3d3ee",
          "publisherName": "ms-vscode",
          "displayName": "Microsoft",
          "flags": "verified"
        },
        "publisherDisplayName": "Microsoft"
      }
    },
    {
      "name": "ms-vscode.js-debug",
      "version": "1.65.0",
      "repo": "https://github.com/microsoft/vscode-js-debug",
      "metadata": {
        "id": "25629058-ddac-4e17-abba-74678e126c5d",
        "publisherId": {
          "publisherId": "5f5636e7-69ed-4afe-b5d6-8d231fb3d3ee",
          "publisherName": "ms-vscode",
          "displayName": "Microsoft",
          "flags": "verified"
        },
        "publisherDisplayName": "Microsoft"
      }
    },
    {
      "name": "ms-vscode.vscode-js-profile-table",
      "version": "0.0.18",
      "repo": "https://github.com/microsoft/vscode-js-profile-visualizer",
      "metadata": {
        "id": "7e52b41b-71ad-457b-ab7e-0620f1fc4feb",
        "publisherId": {
          "publisherId": "5f5636e7-69ed-4afe-b5d6-8d231fb3d3ee",
          "publisherName": "ms-vscode",
          "displayName": "Microsoft",
          "flags": "verified"
        },
        "publisherDisplayName": "Microsoft"
      }
    }
  ],
  "enableTelemetry": false,
  "commit": "c722ca6c7eed3d7987c0d5c3df5c45f6b15e77d1",
  "quality": "stable",
  "date": "2022-04-14T21:18:54Z",
  "codeServerVersion": "4.3.0",
  "documentationUrl": "https://go.microsoft.com/fwlink/?LinkID=533484#vscode",
  "keyboardShortcutsUrlMac": "https://go.microsoft.com/fwlink/?linkid=832143",
  "keyboardShortcutsUrlLinux": "https://go.microsoft.com/fwlink/?linkid=832144",
  "keyboardShortcutsUrlWin": "https://go.microsoft.com/fwlink/?linkid=832145",
  "introductoryVideosUrl": "https://go.microsoft.com/fwlink/?linkid=832146",
  "tipsAndTricksUrl": "https://go.microsoft.com/fwlink/?linkid=852118",
  "newsletterSignupUrl": "https://www.research.net/r/vsc-newsletter",
  "linkProtectionTrustedDomains": [
    "https://open-vsx.org",
    "https://vscode.blob.core.windows.net",
    "https://marketplace.visualstudio.com"
  ]
}
 ```
  + 最后那个`linkProtectionTrustedDomains`我也改了, 好像没什么变化
  + 然后重启code-server吧~ 参考: `systemctl restart code-server@root`(我是root用户), 去插件市场看看吧~
