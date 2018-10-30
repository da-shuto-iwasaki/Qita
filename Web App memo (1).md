
<h1>【Flask+uWSGI+NginxでWebアプリケーション作成】</h1>

***
***

<h2>1. Flask（フラスク）</h2>

<b>　FlaskはWebフレームワークの一種である。<br>
　WebフレームワークというとRubyのRailsやPythonのDjangoなどが他にも挙げられるが、Flaskの特徴は、『軽量で機能がそこまで備わっていない』ということである。<br>
　大規模なWebアプリケーションを作る際には、Flaskでは不十分ということもあるが、簡単に習得することができ学習コストは低いため、Webアプリケーション初心者の人にとってはもってこいのフレームワークである。</b>

<h2>2. Nginx（エンジンエックス）</h2>

<b>　Nginxは、Apache（アパッチ）の代替となるWebサーバーのことであり、最近注目されている。注目されている理由としては、Apacheが抱えていた「クライアント一万台問題」（一度に大量のアクセスがあった際に処理できず、レスポンスが悪くなってしまう問題のこと）を解決したことが挙げられる。<br>
　ApacheとNginxの性能をまとめると、以下のようになる。

| Appache | Nginx |
|:---:|:---:|
| httpに特化 | 同時リクエストの処理に特化 |
| Webサーバーとしての機能が豊富 | 初めから多くの機能はついていない |
| ある程度のメモリが必要 | メモリ使用量が少ない |

<h2>3. uWSGI（ウエスギ）</h2>

<b>　PythonにはDjango（ジャンゴ）やFlask（フラスク）などの様々なWebフレームワークがあるが、選ぶWebフレームワークによって機能や使えるWebサーバーアプリケーションが制限されることがあった。これを解決したのがWSGI（ウィズギー）と呼ばれるインターフェイスである。<br>
　WSGIは Web Server Gateway Interface の略で、その名の通りWebフレームワークとWebサーバーアプリケーションをつなぐ役割をしている。<br>
 　今回使用するuWSGIはNginxとFlaskをつなぐ役割をするもので、これはDjangoとNginxをつなぐこともできる。使用するWebフレームワークとWebサーバーアプリケーションによってWSGIの種類も異なるため、注意が必要である。

<h2>4. ajax（エイジャックス）</h2>

<b>　ウェブブラウザ内で非同期通信とインターフェイスの構築などを行う技術の総称。よくGoogleマップが例として挙げられる。<br>
 　ページ遷移をすることなくページの内容を動的に変更することができる。

***

<b>　以上でそれぞれがどのような機能を果たしているのかはわかったので、ここからはWebアプリケーションを作る際に最低限必要なものについてまとめていく。<br>
　以下が、サンプルとなるWebアプリケーションの全体像である。</b>

```
├── etc
│   ├── nginx
│   │   ├── conf.d
│   │   │   ├── default.conf
│   │   │   └── uwsgi.conf
│   │   ├── nginx.conf
│   │   └── uwsgi_params
│   └── systemd
│       └── system
│           └── uwsgi.service
└── usr
    └── local
        └── app
            ├── main.py
            ├── templates
            │   └── index.html
            ├── tmp
            └── uwsgi.ini
```

<b>それでは、これら２つのディレクトリがどのような機能を果たしているのかを見ていく。<br>なお、詳しい説明はNginxは[ここ](http://nginx.org/en/docs/http/ngx_http_uwsgi_module.html)（[日本語](http://mogile.web.fc2.com/nginx/http/ngx_http_uwsgi_module.html#uwsgi_param)）、uwsgiは[ここ](https://uwsgi-docs.readthedocs.io/en/latest/)を参照！</b>

***
***

<h2>◯/etc/</h2>
<b>・サーバー同士の連携など、様々な設定を行う。</b>

```
etc
├── nginx
│   ├── conf.d
│   │   ├── default.conf
│   │   └── uwsgi.conf
│   ├── nginx.conf
│   └── uwsgi_params
└── systemd
    └── system
        └── uwsgi.service
```

<h3>/etc/nginx/conf.d/default.conf</h3>
<b>・バーチャルサーバーの設定を記述する。</b>

```
server {
    listen       80; #デフォルト値。全てのインターフェイスアドレスを表す。

    location / {
        include uwsgi_params;
	# Unixドメインソケットに流す。
        uwsgi_pass unix:///usr/local/app/tmp/uwsgi.sock;
    }
}
```

***

<h3>/etc/nginx/conf.d/uwsgi.conf</h3>
<b>・エラーログを残す。</b>

```
server {
  listen 5050;
  # warnレベル以上のものが書き込まれる
  error_log  /var/log/nginx/error.log warn;

  location / {
    include uwsgi_params;
    uwsgi_pass unix:///usr/local/app/tmp/uwsgi.sock;
  }
}
```

***

<h3>/etc/nginx/nginx.conf</h3>
<b>・アクセス時のログの出力先などを決める。</b>

```
user www-data; # 実行ユーザーの設定
pid /run/nginx.pid; # プロセスIDを保存するファイル

http {
	access_log /var/log/nginx/access.log;
	error_log /var/log/nginx/error.log;

	include /etc/nginx/mime.types;
	include /etc/nginx/conf.d/*.conf;
}
```

***

<h3>/etc/nginx/uwsgi_params</h3>
<b>・よくわかってない。</b>

```
uwsgi_param  QUERY_STRING       $query_string;
uwsgi_param  REQUEST_METHOD     $request_method;
uwsgi_param  CONTENT_TYPE       $content_type;
uwsgi_param  CONTENT_LENGTH     $content_length;

uwsgi_param  REQUEST_URI        $request_uri;
uwsgi_param  PATH_INFO          $document_uri;
uwsgi_param  DOCUMENT_ROOT      $document_root;
uwsgi_param  SERVER_PROTOCOL    $server_protocol;
uwsgi_param  REQUEST_SCHEME     $scheme;
uwsgi_param  HTTPS              $https if_not_empty;

uwsgi_param  REMOTE_ADDR        $remote_addr;
uwsgi_param  REMOTE_PORT        $remote_port;
uwsgi_param  SERVER_PORT        $server_port;
uwsgi_param  SERVER_NAME        $server_name;
```

***

<h3>/etc/systemd/system/uwsgi.service</h3>
<b>・uwsgiを使う上での環境などを設定する。</b>

```
[Unit]
Description=uWSGI instance to serve uwsgi
After=network.target

[Service]
User=root
Group=root
WorkingDirectory=/usr/local/app
Environment="LD_LIBRARY_PATH=/home/ubuntu/.local/lib"
ExecStart=/home/ubuntu/.local/bin/uwsgi --ini uwsgi.ini

[Install]
WantedBy=multi-user.target
```

***
***

<h2>◯/usr/

```
usr
└── local
    └── app
        ├── main.py
        ├── templates
        │   └── index.html
        ├── tmp
        └── uwsgi.ini
```

***

<h3>/usr/local/api/sample_api.py</h3>
<b>・アプリケーションの機能をまとめたもの。</b>

```
# -*- coding: utf-8 -*-
from flask import Flask,render_template

# 初期化
app = Flask(__name__)

# ルートアクセス時の処理
@app.route('/')
def index():
    return render_template('index.html')

if __name__ == "__main__":
    app.run()
```

***

<h3>/usr/local/api/templates/hello.html</h3>
<b>・ルートアクセスした時に表示されるhtml（メイン）</b>

```
<!doctype html>
<html>
  <head>
    <title>sample</title>
    <body>
      <h1>Hello world.</h1>
      <!-- メインコンテンツ -->
    </body>
  </head>
</html>
```

***

<h3>/usr/local/api/uwsgi.ini</h3>
<b>・uWSGI Emperor用？</b>

```
[uwsgi]
#application's base folder
base = /usr/local/app

#python module to import
app = main_api
module = %(app)

#socket file's location
socket = /usr/local/app/tmp/uwsgi.sock


#permissions for the socket file
chmod-socket = 666

#the variable that holds a flask application inside the module imported at line #6
callable = app

#location of log files
logto = /var/log/uwsgi/%n.log

master = true
processes = 5
vacuum = true
die-on-term = true
```
***
***
[ここ]()に、実際にAWSでアプリケーションを作成したときのメモをまとめてある。
