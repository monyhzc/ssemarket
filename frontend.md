# 前端部署与初步性能分析
2024.4 monyhzc sse@sysu

## Part 0: 检查环境
笔者使用以下环境。请根据自己的环境对文中步骤作出适当调整。标注 *推荐* 的是目前部署服务器的版本。

- WorkStation Pro 17 （当然也可以原生linux 或者 wsl）

- Ubuntu 22.04.4 LTS （推荐 Ubuntu 22.04 LTS）

- CPUs: 2 （推荐 2 核）

- Disk Storage: 至少16G，当然多多益善

- Mem: 8G （推荐 8G）

在过程中会使用到下列工具

- git

- docker

## Part 1: 前端仓库部署

- 拉取仓库分支，此处以 mobile 的 master 分支为例
  ```bash
  ~/workdir$ git clone -b master https://gitee.com/yang-peiyue/sse_market_mobile.git
  ```

- 进入仓库，并新建 `nginx.conf`。你也可以从 [这里](https://gitee.com/yang-peiyue/sse_market_mobile/blob/online/nginx.conf) 找到它。
  ```bash
  worker_processes  1;
  events {
    worker_connections  1024;
  }
  http {
    include       mime.types;
    default_type  application/octet-stream;
    sendfile        on;
    keepalive_timeout  65;
    server {
        listen       81 ssl;
        error_page 497 https://$host:81$request_uri;
        listen 80;
        server_name  mobile;
        ssl_certificate /etc/letsencrypt/1_ssemarket.cn_bundle.crt;
        ssl_certificate_key /etc/letsencrypt/2_ssemarket.cn.key;
        ssl_session_timeout 5m;
        ssl_protocols TLSv1.2 TLSv1.3;
        ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:HIGH:!aNULL:!MD5:!RC4:!DHE;
        ssl_prefer_server_ciphers on;
        location / {
            root   /usr/share/nginx/html;
            try_files $uri $uri/ /index.html last; 
            index  index.html index.htm;
        }
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }
  }
  ```

- 找一个自己喜欢的文件夹，比如我选择了 `~/test/Nginx`。在其中新建`1_ssemarket.cn_bundle.crt` 和 `2_ssemarket.cn.key` 分别如下
```
-----BEGIN CERTIFICATE-----
MIIGTjCCBLagAwIBAgIQHrl4nO9DDg0oaU5i02npvzANBgkqhkiG9w0BAQwFADA8
MQswCQYDVQQGEwJDTjEVMBMGA1UEChMMRE5TUG9kLCBJbmMuMRYwFAYDVQQDEw1E
TlNQb2QgUlNBIERWMB4XDTIzMDkwNTAwMDAwMFoXDTI0MDkwNDIzNTk1OVowFzEV
MBMGA1UEAxMMc3NlbWFya2V0LmNuMIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIB
CgKCAQEAt4iGL9tNKtZn8qDghSl0HTn2c8a7uAFucbNYEZ2YV3n+f1NXkF+ceG1p
sg38XMnqdXrONGHl1/uOsTKt1OxSaMDvje+yO+otcJYS8LFW9ZbHMmzEvCtW8qCE
Zo2PJ9iyByMcLGGUtzN7nuowpQ8Y6sQbCWv2JHiuCc2avW7yelxMi6OyQAQmMEGN
8i788jkG2itgFrSXQNXyyRvR7KBpVtyUll8ABrmTJF4mnUg/BJ8ooj4IR1XbkwLb
D2BkiVRzEHDc2oq6AChKiJDdz1bnntFEkAuicYsPdtV8qY6EpHonnN4GFONO2DOS
2pkvcJlojWLFO86JKHZQgO/LCb7kRQIDAQABo4IC7zCCAuswHwYDVR0jBBgwFoAU
AkBnmwfbCcXTISBgMuu8V/PYiBQwHQYDVR0OBBYEFCkxNE2oXauf7VkOsQKtj78B
v3PoMA4GA1UdDwEB/wQEAwIFoDAMBgNVHRMBAf8EAjAAMB0GA1UdJQQWMBQGCCsG
AQUFBwMBBggrBgEFBQcDAjBJBgNVHSAEQjBAMDQGCysGAQQBsjEBAgJfMCUwIwYI
KwYBBQUHAgEWF2h0dHBzOi8vc2VjdGlnby5jb20vQ1BTMAgGBmeBDAECATBzBggr
BgEFBQcBAQRnMGUwOAYIKwYBBQUHMAKGLGh0dHA6Ly9jcnQudHJ1c3QtcHJvdmlk
ZXIuY24vRE5TUG9kUlNBRFYuY3J0MCkGCCsGAQUFBzABhh1odHRwOi8vb2NzcC50
cnVzdC1wcm92aWRlci5jbjApBgNVHREEIjAgggxzc2VtYXJrZXQuY26CEHd3dy5z
c2VtYXJrZXQuY24wggF/BgorBgEEAdZ5AgQCBIIBbwSCAWsBaQB2AHb/iD8KtvuV
UcJhzPWHujS0pM27KdxoQgqf5mdMWjp0AAABimaS4OkAAAQDAEcwRQIgT225JN1Z
hwhbmz5/PJabAfOLATXFuD9ZVeM2TQVUwpECIQDh4g1GYIes11h5zqzgEiEcw+Dg
W5xE1DD4PRfYxO4NSAB2ANq2v2s/tbYin5vCu1xr6HCRcWy7UYSFNL2kPTBI1/ur
AAABimaS4VgAAAQDAEcwRQIhAPOhauh7NdkPfPOPHuScc3TCwYsGF6vQ3qN3jHwa
Uv7LAiBdL1Wmtm70meVYCb+F2WOKplMSLoKeBC87kuQI3UM6tgB3AO7N0GTV2xrO
xVy3nbTNE6Iyh0Z8vOzew1FIWUZxH7WbAAABimaS4V8AAAQDAEgwRgIhAOiGs6O5
zvBVG2t4BdTyZB8e+pDP3zKWoYEdgXT//TdAAiEA4H5ulWVKM/kk/Wt0z7MUshQ+
hZlHmFpEoPTYCiHBUW8wDQYJKoZIhvcNAQEMBQADggGBADgXxTD/cUcelGFUhCOE
q2JNFitfL/4GGWONj1x9PF1VCevDG4ajQwNbB6J4y91URsCVvxEAJwi1bpJb5pcI
6yi6GFZmdBkBoPFa/RGE4LXcwF+ZLNVeUVbW0U1JBM/PbtZ2Vgu5kBPbvvXQrEvc
PiACRMpJ69N/JkN1yI3Pa+Y4xBQVm8homw7maqfXW1DxIQByTFV1/9EEgM7Vadel
XXXDpFkRZwpXVELYwSJiLA8dCL5BG5CVVk8OfDS5EJN+OERwPdI8RMU5/s32Ud9d
ITN7R/SbRRhDLu48JwnVqF19lON/LgLs7h6phfKeFVEMJMvv7s80FuJWfzSeUJlZ
i100UU7rSpn7xh22hDu+fsH37Ombsj6PhEfi3d6wAB/lUgw7QoNeCAtHj5lAyj10
2cNWjnpZTI4L0Ou1XK2KNF4ADKPqXdSeDhusT1IlPd93NwH5n0P9FC6elAufYTMX
3qMT3pJulm4fWuj/HjFh0wyoSHaJT7tITfKVq60Lb7pB1w==
-----END CERTIFICATE-----
-----BEGIN CERTIFICATE-----
MIIGQjCCBCqgAwIBAgIRALzt9DJo0V96bBkz/Ta511MwDQYJKoZIhvcNAQEMBQAw
gYgxCzAJBgNVBAYTAlVTMRMwEQYDVQQIEwpOZXcgSmVyc2V5MRQwEgYDVQQHEwtK
ZXJzZXkgQ2l0eTEeMBwGA1UEChMVVGhlIFVTRVJUUlVTVCBOZXR3b3JrMS4wLAYD
VQQDEyVVU0VSVHJ1c3QgUlNBIENlcnRpZmljYXRpb24gQXV0aG9yaXR5MB4XDTIy
MDQxNDAwMDAwMFoXDTMyMDQxMzIzNTk1OVowPDELMAkGA1UEBhMCQ04xFTATBgNV
BAoTDEROU1BvZCwgSW5jLjEWMBQGA1UEAxMNRE5TUG9kIFJTQSBEVjCCAaIwDQYJ
KoZIhvcNAQEBBQADggGPADCCAYoCggGBAK3O1fkuf69z+GVthojdYX7ek9E4G6x8
4gFnWzBG10WgRcaOLXsgf9zntMiUIdP57lkw6iaRbmPAMEEk7fWqsX1YKxhwDMuw
GxwRMBeunSDFa7eJrfKB28gm86VRUc2YDwxXhfuVlxPNsHla2S4pPZKR5CFFXCW8
AaAmPSXjZo7O81FpidnyD+78fy06bByD7RIAzDDoeBz39ZX1hBZJks0aTLzbNoBz
F0eJa0RCP6SnzJhZUsFzfX89ddhon9i1EtLu1fATgUmFRu72XGHVBXFDWapSft29
ksNOOuL2gHA+ED5/P9A7ZtObb4aW7iHKOG1X0p8doQLOPNHVOr5sOvy7kmtBZTw+
/Pf04SOiT6Jmqa6oDj4dS21M+obBAh7GN4a8IfG7/4hq7PfelvOD29uAmtaCCd/Q
zZqxje6hCrr1LzNRkHBhGrvpOMmvxdD0v6okJawGk0nZI2TKaPlLRUGdnkirOWjL
glKYYl09+uTGMdEco0ixedm+5dMRa8hiQQIDAQABo4IBcDCCAWwwHwYDVR0jBBgw
FoAUU3m/WqorSs9UgOHYm8Cd8rIDZsswHQYDVR0OBBYEFAJAZ5sH2wnF0yEgYDLr
vFfz2IgUMA4GA1UdDwEB/wQEAwIBhjASBgNVHRMBAf8ECDAGAQH/AgEAMB0GA1Ud
JQQWMBQGCCsGAQUFBwMBBggrBgEFBQcDAjAiBgNVHSAEGzAZMA0GCysGAQQBsjEB
AgJfMAgGBmeBDAECATBQBgNVHR8ESTBHMEWgQ6BBhj9odHRwOi8vY3JsLnVzZXJ0
cnVzdC5jb20vVVNFUlRydXN0UlNBQ2VydGlmaWNhdGlvbkF1dGhvcml0eS5jcmww
cQYIKwYBBQUHAQEEZTBjMDoGCCsGAQUFBzAChi5odHRwOi8vY3J0LnVzZXJ0cnVz
dC5jb20vVVNFUlRydXN0UlNBQUFBQ0EuY3J0MCUGCCsGAQUFBzABhhlodHRwOi8v
b2NzcC51c2VydHJ1c3QuY29tMA0GCSqGSIb3DQEBDAUAA4ICAQAE0dip1UKw9HHo
vD++VLzM0PPi1rkJ1bQNRwMWPBFzezQhMezFoThN1kpWdtxvFjQrAxLoFCHPOUav
kGxXE4eyJpPWt0Rizx90OwDqAoZN87i0MgBVo+om1/8EIMdU137+TZotPBQMmGvK
qfRS2VZVC9RrDX8H9V1cIFyaphJfBgx7YaJRNgdVbjcZUw0tNA8SF/YGzQRPq9bX
pvNXWRA1PRK8r55HoNsJRNnP5nkO5RMCXIb7kjjHME42g4ZMRHIJlOKCYraqEzEk
JNUk0ovPaC6W69BUl2MgW7tuBX0YlpDjZHU03eEgmIYDtXhwLCYavXH8ujvvkuWa
LFDQADsNAIS1YNzH1kUVcsqSLB0egbUxIs/xrkuuVEjcnh9Qe6Jn/+Meef1jG2xX
9hB4hgYGtov7gjiUgLRZurtD2pCTnoEBiTRsdZ9B7xgvIHcCbdrhmBj2jbzP0dr2
2qVu0TytQS5NUidZGDxkpqsg3FHbe5VbcLqcU+emqp6rvjudFTE4NkcENe2OTD0l
TrQmPZ57KoOtFaPNwaOEHPlHeZrpqc8mWjUcijUIfuGqroHlG/9Bbfsk/7cF3k/N
DrUEPO0TZJ5xheJeqQBGvnBGDaqJcf7ree7DHs8nkdLtx5sctB2Zw5xa/pcUem55
fmNqfTy4zYl9zgEm1vvSfphOfU365w==
-----END CERTIFICATE-----
-----BEGIN CERTIFICATE-----
MIIFgTCCBGmgAwIBAgIQOXJEOvkit1HX02wQ3TE1lTANBgkqhkiG9w0BAQwFADB7
MQswCQYDVQQGEwJHQjEbMBkGA1UECAwSR3JlYXRlciBNYW5jaGVzdGVyMRAwDgYD
VQQHDAdTYWxmb3JkMRowGAYDVQQKDBFDb21vZG8gQ0EgTGltaXRlZDEhMB8GA1UE
AwwYQUFBIENlcnRpZmljYXRlIFNlcnZpY2VzMB4XDTE5MDMxMjAwMDAwMFoXDTI4
MTIzMTIzNTk1OVowgYgxCzAJBgNVBAYTAlVTMRMwEQYDVQQIEwpOZXcgSmVyc2V5
MRQwEgYDVQQHEwtKZXJzZXkgQ2l0eTEeMBwGA1UEChMVVGhlIFVTRVJUUlVTVCBO
ZXR3b3JrMS4wLAYDVQQDEyVVU0VSVHJ1c3QgUlNBIENlcnRpZmljYXRpb24gQXV0
aG9yaXR5MIICIjANBgkqhkiG9w0BAQEFAAOCAg8AMIICCgKCAgEAgBJlFzYOw9sI
s9CsVw127c0n00ytUINh4qogTQktZAnczomfzD2p7PbPwdzx07HWezcoEStH2jnG
vDoZtF+mvX2do2NCtnbyqTsrkfjib9DsFiCQCT7i6HTJGLSR1GJk23+jBvGIGGqQ
Ijy8/hPwhxR79uQfjtTkUcYRZ0YIUcuGFFQ/vDP+fmyc/xadGL1RjjWmp2bIcmfb
IWax1Jt4A8BQOujM8Ny8nkz+rwWWNR9XWrf/zvk9tyy29lTdyOcSOk2uTIq3XJq0
tyA9yn8iNK5+O2hmAUTnAU5GU5szYPeUvlM3kHND8zLDU+/bqv50TmnHa4xgk97E
xwzf4TKuzJM7UXiVZ4vuPVb+DNBpDxsP8yUmazNt925H+nND5X4OpWaxKXwyhGNV
icQNwZNUMBkTrNN9N6frXTpsNVzbQdcS2qlJC9/YgIoJk2KOtWbPJYjNhLixP6Q5
D9kCnusSTJV882sFqV4Wg8y4Z+LoE53MW4LTTLPtW//e5XOsIzstAL81VXQJSdhJ
WBp/kjbmUZIO8yZ9HE0XvMnsQybQv0FfQKlERPSZ51eHnlAfV1SoPv10Yy+xUGUJ
5lhCLkMaTLTwJUdZ+gQek9QmRkpQgbLevni3/GcV4clXhB4PY9bpYrrWX1Uu6lzG
KAgEJTm4Diup8kyXHAc/DVL17e8vgg8CAwEAAaOB8jCB7zAfBgNVHSMEGDAWgBSg
EQojPpbxB+zirynvgqV/0DCktDAdBgNVHQ4EFgQUU3m/WqorSs9UgOHYm8Cd8rID
ZsswDgYDVR0PAQH/BAQDAgGGMA8GA1UdEwEB/wQFMAMBAf8wEQYDVR0gBAowCDAG
BgRVHSAAMEMGA1UdHwQ8MDowOKA2oDSGMmh0dHA6Ly9jcmwuY29tb2RvY2EuY29t
L0FBQUNlcnRpZmljYXRlU2VydmljZXMuY3JsMDQGCCsGAQUFBwEBBCgwJjAkBggr
BgEFBQcwAYYYaHR0cDovL29jc3AuY29tb2RvY2EuY29tMA0GCSqGSIb3DQEBDAUA
A4IBAQAYh1HcdCE9nIrgJ7cz0C7M7PDmy14R3iJvm3WOnnL+5Nb+qh+cli3vA0p+
rvSNb3I8QzvAP+u431yqqcau8vzY7qN7Q/aGNnwU4M309z/+3ri0ivCRlv79Q2R+
/czSAaF9ffgZGclCKxO/WIu6pKJmBHaIkU4MiRTOok3JMrO66BQavHHxW/BBC5gA
CiIDEOUMsfnNkjcZ7Tvx5Dq2+UUTJnWvu6rvP3t3O9LEApE9GQDTF1w52z97GA1F
zZOFli9d31kWTz9RvdVFGD/tSo7oBmF0Ixa1DVBzJ0RHfxBdiSprhTEUxOipakyA
vGp4z7h/jnZymQyd/teRCBaho1+V
-----END CERTIFICATE-----
```
---
```
-----BEGIN RSA PRIVATE KEY-----
MIIEowIBAAKCAQEAt4iGL9tNKtZn8qDghSl0HTn2c8a7uAFucbNYEZ2YV3n+f1NX
kF+ceG1psg38XMnqdXrONGHl1/uOsTKt1OxSaMDvje+yO+otcJYS8LFW9ZbHMmzE
vCtW8qCEZo2PJ9iyByMcLGGUtzN7nuowpQ8Y6sQbCWv2JHiuCc2avW7yelxMi6Oy
QAQmMEGN8i788jkG2itgFrSXQNXyyRvR7KBpVtyUll8ABrmTJF4mnUg/BJ8ooj4I
R1XbkwLbD2BkiVRzEHDc2oq6AChKiJDdz1bnntFEkAuicYsPdtV8qY6EpHonnN4G
FONO2DOS2pkvcJlojWLFO86JKHZQgO/LCb7kRQIDAQABAoIBAARjlivqT2ndFz7I
5J2inGV/h5qEr+QKWrkK3CoNrgE3iGPEmzU3aB9EJJEuvvB8HqiBD5HWnn/AAw1P
RDNxlFXveTsYdpdytfP5jsnlP8pfOPPGgJzsX2eteYUXUQtXpghAKXIRQlRwPX5T
JOcpmYOxvWDIDWqZXTYcTd1GXDObW5EUqxS1tWT3gkhe9WX98WQjqRZECBATzDeL
U1+zBG/was+j3yhfWgSqqdRQP1AvL6MVj+L0fOm5jbRKyA50bIq18tLfQWr0dhy9
uJX6P8yQNaiYsVA9VXiUXugstJUmiAcdAbQxY9UNOUEn4RonTUH7BrigzgwnqLai
CRNJGIECgYEA5rAuw0XcR/dSzu8LZeuBOkqMYpjxwnUjReKRXUIDSKHUdXyxQpTP
ElBYijrr4by3X2iMeNKjlY5JDjPAd+iY9nqB5x0NPT/VncYC6Wp/3euGmEpwnIIb
7snQPOlIxNTlRNrYPQRVQDHVm8IzR83gg+eHfo/6LYUDh+BbCsC1CoECgYEAy6vN
xBQe8uXECCs9wzf97u5u+JpeHf8iEYFNQZOkFqC4H76LREkzfqkyPsBaOGBdv8n5
mX+pMEzEBVF6tO4UJFE/xLnC3GUyccKKeJaF+W/+JrBLCltMYfyNw/hc0gGgsvTR
GJ8EEZl+n+4xWQgNO9PAIMqIqRHgKeQ0lugwT8UCgYEApDXHo9MH4eaI+Kqqh2tj
utW0Jr9OYKi3ahjEdwj1g29G6DbRi16YJCYzUHpuzGpkcbLfQ2ZVcbEuCcEHAZP9
xn4gGB7S/MWCzuNT0UmxKYxta9yZWp83OPxJkgJRfWM6rI/Hm2+JK8ANmOldTYCc
j+hwx8O/R0XG5KLsy7Ova4ECgYAyFyDWFnS+HFAoCip4dq7XMoDMV3rKOdMxJvHP
LPZwMjrWY9BwjOhDQG2BDWGi9+BWqN/gJot/u4GCJVXeZadCf1EFmwIXnjPX0pan
ra1peWeyMC1kcKvKdYiQJ+b8JzZUjO1X9tPTWELSJoywbtCCZEOeTPFrU2AkLWHZ
s2UeWQKBgHGjNFMImGLKW9oymowpQ3845+qSUVIo9Gb2Yl1AhOBGBeS0989RpwCA
Dnqvwp4EWLR68/Hn/eI68dgLVYn+vLVsl1DgV33lhu/VEq2puGdr27g4MzP+i+4R
bNMpD1ludNZt4WLKV6F3eCrJyAT5CU9HfP4bMdi3J/oJPdL61qPN
-----END RSA PRIVATE KEY-----
```
也可以尝试自行生成公私钥。

- 重新回到仓库中。新建 `compose.yml`

  ```
  services:
    sse_market_client_mobile:
      image: sse_market_client-mobile:latest
      container_name: cilent_mobile
      restart: always
      build: ../sse_market_mobile
      ports:
        - 81:81
      environment:
        - TZ=Asia/Shanghai
      volumes:
        - ~/test/Nginx:/etc/letsencrypt/
  volumes:
    my-vol:
  ```
  其中 `~/test/Nginx` 替换成刚才你选择的那个文件夹的路径

- 进入 `src/router` 目录，修改 `index.js`。你会看到长成这样的东西。
  ```js
  {
    path: '/',
    name: 'home',
    meta: {
      auth: true,
      keepAlive: true,
    },
    component: () => import('../views/home/HomeView.vue'),
  },
  ```
  将 `auth: true,` 注释掉即可开启一个部分的游客模式。
  ```js
  router.beforeEach((to, from, next) => {
    if (to.meta.auth) {
      if (store.state.userModule.token) {
        store.commit('getNoticesNum');
        next();
      } else {
        Toast.fail('请先登录');
        if (from.name !== 'logintest') router.push({ name: 'logintest' });
      }
    } else {
      next();
    }
  });
  ```
  这个部分是检验登录状态并强制跳转登录页，反正我是深受其害。将 `store.state.userModule.token` 改为 `1` 即可。

- 部署，如果一切顺利你会看到类似下面的输出。反正我感觉这一步挺慢的，可以去干点别的
  ```bash
  $ docker compose up

  [+] Running 1/0
   ✔ Container cilent_mobile  Created                                                                                                                    0.0s
  Attaching to cilent_mobile
  cilent_mobile  | /docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
  cilent_mobile  | /docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
  cilent_mobile  | /docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
  cilent_mobile  | 10-listen-on-ipv6-by-default.sh: info: IPv6 listen already enabled
  cilent_mobile  | /docker-entrypoint.sh: Sourcing /docker-entrypoint.d/15-local-resolvers.envsh
  cilent_mobile  | /docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
  cilent_mobile  | /docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
  cilent_mobile  | /docker-entrypoint.sh: Configuration complete; ready for start up
  ```

  然后在浏览器中访问 `localhost:81`，可能会报危险，因为这是自签的证书。忽略危险继续访问，应该可以看到主页。

- 也可以在宿主机上访问（推荐）。
  ```bash
  $ ifconfig
  ens33: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.***.***  netmask 255.255.255.0  broadcast 192.168.***.255
  ```
  在宿主机的浏览器上输入 inet 的网址加端口。至于网卡怎么看，应该大部分长的都和 ens33 差不多，反正一定不是 `lo` 或者带 `docker` 的。有用 `ufw` 的记得放行端口。

- 至于接入到软工集市主站的api，我尝试过在仓库根目录下修改 `.env.development` 的内容为
  ```
  VUE_APP_BASE_URL = https://ssemarket.cn:8080/api/
  ```
  但是似乎并没有成功。·

## Part 2: 使用 lighthouse 进行性能分析

推荐使用 chromium 内核的浏览器。比如 chrome 自带 lighthouse

- 按 F12 或者 右键检查元素 弹出开发者界面，找到 lighthouse。大概长这样。

  ![lighthouse](./lighthouse.jpg)

- 生成报告。比如我以主站为对象生成的报告在 [这里](./lighthouse_mb.html)